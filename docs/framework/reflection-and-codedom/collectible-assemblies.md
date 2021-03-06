---
title: 動的な型生成のための収集可能なアセンブリ
description: ''
ms.date: 08/29/2017
helpviewer_keywords:
- reflection, dynamic assembly
- assemblies, collectible
- collectible assemblies, retrieving
ms.openlocfilehash: 02c7048e0321282463aa3558287d1d13c5e4f8d2
ms.sourcegitcommit: 7588136e355e10cbc2582f389c90c127363c02a5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2020
ms.locfileid: "79180545"
---
# <a name="collectible-assemblies-for-dynamic-type-generation"></a>動的な型生成のための収集可能なアセンブリ

*収集可能なアセンブリ*とは、それらが作成されたアプリケーション ドメインをアンロードせずにアンロードできる動的アセンブリです。 収集可能アセンブリとそれに含まれる型によって使用されているすべてのマネージド メモリとアンマネージド メモリは、再利用することができます。 アセンブリ名などの情報は、内部テーブルから削除されます。

アンロードを有効にするには、動的アセンブリを作成するときに <xref:System.Reflection.Emit.AssemblyBuilderAccess.RunAndCollect?displayProperty=nameWithType> フラグを使用します。 このアセンブリは一時的なもので (つまり、保存できません)、「[収集可能なアセンブリに対する制限](#restrictions-on-collectible-assemblies)」セクションで説明されている制限事項が該当します。 収集可能なアセンブリは、そのアセンブリに関連付けられているすべてのオブジェクトが解放されると、共通言語ランタイム (CLR) によって自動的にアンロードされます。 その他のすべての点では、収集可能なアセンブリは他の動的アセンブリと同じように作成および使用されます。

## <a name="lifetime-of-collectible-assemblies"></a>収集可能なアセンブリの有効期間

収集可能な動的アセンブリの有効期間は、そのアセンブリに含まれる型への参照があるかどうかと、それらの型から作成されたオブジェクトがあるかどうかによって決まります。 次の 1 つ以上が存在する場合、共通言語ランタイムはアセンブリをアンロードしません (`T` は、アセンブリで定義されている任意の型です)。

- `T` のインスタンス。

- `T` の配列のインスタンス。

- `T` を型引数の 1 つとして含んでいるジェネリック型のインスタンス。 これには、そのコレクションが空の場合も含め、`T` のジェネリック コレクションを含みます。

- `T` を表す <xref:System.Type> または <xref:System.Reflection.Emit.TypeBuilder> のインスタンス。

   > [!IMPORTANT]
   > アセンブリの一部を表すオブジェクトは、すべて解放する必要があります。 `T` を定義した <xref:System.Reflection.Emit.ModuleBuilder> は <xref:System.Reflection.Emit.TypeBuilder> への参照を保持し、<xref:System.Reflection.Emit.AssemblyBuilder> オブジェクトは <xref:System.Reflection.Emit.ModuleBuilder> への参照を保持します。したがって、これらのオブジェクトへの参照は解放する必要があります。 `T` の構築に使用された <xref:System.Reflection.Emit.LocalBuilder> や <xref:System.Reflection.Emit.ILGenerator> が存在している場合も、アンロードは行われません。

- 実行コードから到達できる動的に定義された別の型 `T1` による、`T` への静的参照。 たとえば、`T1` が `T` から派生している場合や、`T` が `T1` のメソッドのパラメーターの型である場合があります。

- `T` に属する静的フィールドに対する **ByRef**。

- `T` または `T` のコンポーネントを参照する <xref:System.RuntimeTypeHandle>、<xref:System.RuntimeFieldHandle>、または <xref:System.RuntimeMethodHandle>。

- `T` を表す <xref:System.Type> オブジェクトへのアクセスに間接または直接に使用できるリフレクション オブジェクトのインスタンス。 たとえば、`T` の <xref:System.Type> オブジェクトは、要素型が `T` である配列型や、`T` を型引数として持つジェネリック型から取得できます。

- 任意のスレッドの呼び出し履歴にあるメソッド `M`。この `M` は、`T` のメソッド、またはアセンブリで定義されているモジュール レベルのメソッドです。

- アセンブリのモジュール内で定義されている静的メソッドに対するデリゲート。

アセンブリ内の 1 つの型または 1 つのメソッドについてだけでも、この一覧の項目が 1 つでも存在する場合、ランタイムはそのアセンブリをアンロードできません。

> [!NOTE]
> 実際には、この一覧にあるすべての項目に対してファイナライザーが実行されるまで、アセンブリはアンロードされません。

有効期間の追跡では、収集可能なアセンブリの生成時に作成および使用される (C# の) `List<int>` または (Visual Basic の) `List(Of Integer)` などの構築ジェネリック型は、そのジェネリック型定義を含むアセンブリか、その型引数のいずれかの定義を含む別のアセンブリで定義されていると見なされます。 使用される正確なアセンブリは実装の詳細であり、変更されることがあります。

## <a name="restrictions-on-collectible-assemblies"></a>収集可能なアセンブリに対する制限

収集可能なアセンブリには、次の制限があります。

- **静的参照**通常の動的アセンブリの型は、収集可能なアセンブリで定義されている型への静的参照を持つことはできません。 たとえば、収集可能なアセンブリ内の型を継承する通常の型を定義すると、<xref:System.NotSupportedException> 例外がスローされます。 収集可能なアセンブリ内の型では、別の収集可能なアセンブリ内の型への静的参照を使用できますが、この場合は、参照アセンブリの有効期間が参照元アセンブリの有効期間に延長されます。

- **COM 相互運用機能**収集可能なアセンブリ内で COM インターフェイスを定義することはできません。 収集可能なアセンブリ内の型は、COM 呼び出し可能ラッパー (CCW) またはランタイム呼び出し可能ラッパー (RCW) として機能できません。 ただし、収集可能なアセンブリ内の型では、COM インターフェイスを実装するオブジェクトを使用できます。

- **プラットフォーム呼び出し**この属性を<xref:System.Runtime.InteropServices.DllImportAttribute>持つメソッドは、収集可能なアセンブリで宣言された場合、コンパイルされません。 <xref:System.Reflection.Emit.OpCodes.Calli?displayProperty=nameWithType> 命令は、収集可能なアセンブリ内の型の実装では使用できません。このような型をアンマネージ コードにマーシャリングすることはできません。 ただし、収集可能でないアセンブリ内で宣言されているエントリ ポイントを使用すれば、ネイティブ コードを呼び出すことができます。

- **マーシャリング**収集可能なアセンブリで定義されているオブジェクト (特にデリゲート) はマーシャリングできません。 この制限は、一時的に生成されるすべての型にあります。

- **アセンブリの積み込み**リフレクション出力は、収集可能なアセンブリの読み込みに対してサポートされる唯一のメカニズムです。 それ以外の形式のアセンブリ読み込みによってロードされたアセンブリは、アンロードできません。

- **コンテキスト バインド オブジェクト**コンテキスト静的変数はサポートされていません。 収集可能なアセンブリ内の型は、<xref:System.ContextBoundObject> を拡張できません。 ただし、別の場所で定義されているコンテキスト バインド オブジェクトを、収集可能なアセンブリのコードで使用することはできます。

- **スレッド静的データ**スレッド静的変数はサポートされていません。

## <a name="see-also"></a>関連項目

- [動的メソッドおよびアセンブリの出力](emitting-dynamic-methods-and-assemblies.md)
