---
title: 構造体とその他のプログラミング要素
ms.date: 07/20/2015
helpviewer_keywords:
- structures [Visual Basic], arrays
- procedures [Visual Basic], structures as arguments to
- objects [Visual Basic], structure elements
- arrays [Visual Basic], structure elements
- nested structures [Visual Basic]
ms.assetid: 0f849313-ccd2-4c9a-acb9-69de6751c088
ms.openlocfilehash: 73d3f999e95c484dff3f5409f2cdb9032b64fe38
ms.sourcegitcommit: 43d10ef65f0f1fd6c3b515e363bde11a3fcd8d6d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2020
ms.locfileid: "78266860"
---
# <a name="structures-and-other-programming-elements-visual-basic"></a>構造体およびその他のプログラミング要素 (Visual Basic)
構造体は、配列、オブジェクト、プロシージャ、および相互に組み合わせて使用できます。 これらの相互作用は、これらの要素が個別に使用する構文と同じ構文を使用します。  
  
> [!NOTE]
> 構造体宣言内の構造体要素は初期化できません。 値は、構造体型として宣言されている変数の要素にのみ割り当てることができます。  
  
## <a name="structures-and-arrays"></a>構造体と配列  
 構造体には、配列を 1 つ以上の要素として含めることができます。 次の例を使って説明します。  
  
```vb  
Public Structure systemInfo  
    Public cPU As String  
    Public memory As Long  
    Public diskDrives() As String  
    Public purchaseDate As Date  
End Structure
```  
  
 オブジェクトのプロパティにアクセスするのと同じ方法で、構造体内の配列の値にアクセスします。 次の例を使って説明します。  
  
```vb  
Dim mySystem As systemInfo  
ReDim mySystem.diskDrives(3)  
mySystem.diskDrives(0) = "1.44 MB"  
```  
  
 構造体の配列を宣言することもできます。 次の例を使って説明します。  
  
```vb  
Dim allSystems(100) As systemInfo  
```  
  
 同じルールに従って、このデータ アーキテクチャのコンポーネントにアクセスします。 次の例を使って説明します。  
  
```vb  
ReDim allSystems(5).diskDrives(3)  
allSystems(5).CPU = "386SX"  
allSystems(5).diskDrives(2) = "100M SCSI"  
```  
  
## <a name="structures-and-objects"></a>構造とオブジェクト  
 構造体には、オブジェクトを 1 つ以上の要素として含めることができます。 次の例を使って説明します。  
  
```vb  
Protected Structure userInput  
    Public userName As String  
    Public inputForm As System.Windows.Forms.Form  
    Public userFileNumber As Integer  
End Structure  
```  
  
 このような宣言では、特定のオブジェクト クラスを使用する必要があります`Object`。  
  
## <a name="structures-and-procedures"></a>構造と手順  
 構造体をプロシージャ引数として渡すことができます。 次の例を使って説明します。  
  
```vb  
Public currentCPUName As String = "700MHz Pentium compatible"  
Public currentMemorySize As Long = 256  
Public Sub fillSystem(ByRef someSystem As systemInfo)  
    someSystem.cPU = currentCPUName  
    someSystem.memory = currentMemorySize  
    someSystem.purchaseDate = Now  
End Sub  
```  
  
 前の例では、*構造体を参照渡し*で渡し、プロシージャが要素を変更して、呼び出し元のコードで変更が有効になるようにします。 このような変更から構造体を保護する場合は、値渡しします。  
  
 `Function`プロシージャから構造体を返すこともできます。 次の例を使って説明します。  
  
```vb  
Dim allSystems(100) As systemInfo  
Function findByDate(ByVal searchDate As Date) As systemInfo  
    Dim i As Integer  
    For i = 1 To 100  
        If allSystems(i).purchaseDate = searchDate Then Return allSystems(i)  
    Next i  
   ' Process error: system with desired purchase date not found.  
End Function  
```  
  
## <a name="structures-within-structures"></a>構造内の構造  
 構造体には、他の構造体を含めることができます。 次の例を使って説明します。  
  
```vb  
Public Structure driveInfo  
    Public type As String  
    Public size As Long  
End Structure  
Public Structure systemInfo  
    Public cPU As String  
    Public memory As Long  
    Public diskDrives() As driveInfo  
    Public purchaseDate As Date  
End Structure  
```  
  
```vb  
Dim allSystems(100) As systemInfo  
ReDim allSystems(1).diskDrives(3)  
allSystems(1).diskDrives(0).type = "Floppy"  
```  
  
 この手法を使用して、あるモジュールで定義された構造を、別のモジュールで定義された構造体にカプセル化することもできます。  
  
 構造体は、任意の深さに他の構造体を含めることができます。  
  
## <a name="see-also"></a>関連項目

- [データ型](../../../../visual-basic/programming-guide/language-features/data-types/index.md)
- [基本データ型](../../../../visual-basic/programming-guide/language-features/data-types/elementary-data-types.md)
- [複合データ型](../../../../visual-basic/programming-guide/language-features/data-types/composite-data-types.md)
- [値型と参照型](../../../../visual-basic/programming-guide/language-features/data-types/value-types-and-reference-types.md)
- [構造体](../../../../visual-basic/programming-guide/language-features/data-types/structures.md)
- [データ型のトラブルシューティング](../../../../visual-basic/programming-guide/language-features/data-types/troubleshooting-data-types.md)
- [方法 : 構造体を宣言する](../../../../visual-basic/programming-guide/language-features/data-types/how-to-declare-a-structure.md)
- [構造体の変数](../../../../visual-basic/programming-guide/language-features/data-types/structure-variables.md)
- [構造体とクラス](../../../../visual-basic/programming-guide/language-features/data-types/structures-and-classes.md)
- [Structure ステートメント](../../../../visual-basic/language-reference/statements/structure-statement.md)
