---
title: .NET Core に移植するためのツール
description: .NET Core への移植に使用できるいくつかのツールに関する詳細情報
author: cartermp
ms.date: 12/07/2018
ms.openlocfilehash: 64bad7600d8e17ada83d4bd8bc56762fd1789f43
ms.sourcegitcommit: e3cbf26d67f7e9286c7108a2752804050762d02d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2020
ms.locfileid: "80989130"
---
# <a name="tools-to-help-with-porting-to-net-core"></a>.NET Core への移植で役立つツール

この記事に一覧表示されているツールは、移植するときに役立つ場合があります。

- [.NET Portability Analyzer](../../standard/analyzers/portability-analyzer.md) - .NET Framework と .NET Core の間で、コードを
  - [コマンド ライン ツール](https://github.com/Microsoft/dotnet-apiport/releases)として
  - [Visual Studio 拡張機能](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)として、どの程度移植できるかを示すレポートを生成できるツールチェーン。
- [.NET API アナライザー](../../standard/analyzers/api-analyzer.md): さまざまなプラットフォームでの C# API の互換性リスクの可能性と、非推奨の API の呼び出しを検出する Roslyn アナライザー。

さらに、[CsprojToVs2017](https://github.com/hvanbakel/CsprojToVs2017) ツールを使って、小規模なソリューションや個々のプロジェクトを .NET Core プロジェクトのファイル形式に移植してみることが可能です。

> [!WARNING]
> CsprojToVs2017 はサードパーティ製のツールです。 すべてのプロジェクトに対してこれが動作する保証はありません。また、依存している動作に微妙な変更が生じる可能性があります。 CsprojToVs2017 は、自動化できる基本的なことを自動化するための "_開始点_" として使う必要があります。 これは、プロジェクトのファイル形式の移行に対する保証されたソリューションではありません。
