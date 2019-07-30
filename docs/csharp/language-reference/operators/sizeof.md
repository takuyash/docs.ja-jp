---
title: sizeof 演算子 - C# リファレンス
ms.custom: seodec18
ms.date: 07/25/2019
f1_keywords:
- sizeof_CSharpKeyword
- sizeof
helpviewer_keywords:
- sizeof keyword [C#]
ms.assetid: c548592c-677c-4f40-a4ce-e613f7529141
ms.openlocfilehash: 4852e1166a975b1a45c5bd905123a35fc846aa28
ms.sourcegitcommit: 463f3f050cecc0b6403e67f19a61f870fb8e7b7d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2019
ms.locfileid: "68513126"
---
# <a name="sizeof-operator-c-reference"></a><span data-ttu-id="0e11c-102">sizeof 演算子 (C# リファレンス)</span><span class="sxs-lookup"><span data-stu-id="0e11c-102">sizeof operator (C# reference)</span></span>

<span data-ttu-id="0e11c-103">`sizeof` は、指定された型の変数が占有しているバイト数を返します。</span><span class="sxs-lookup"><span data-stu-id="0e11c-103">The `sizeof` operator returns the number of bytes occupied by a variable of a given type.</span></span> <span data-ttu-id="0e11c-104">`sizeof` 演算子の引数は、[アンマネージド型](../builtin-types/unmanaged-types.md)の名前、またはアンマネージド型に[制限される](../../programming-guide/generics/constraints-on-type-parameters.md#unmanaged-constraint)型パラメーターである必要があります。</span><span class="sxs-lookup"><span data-stu-id="0e11c-104">An argument of the `sizeof` operator must be the name of an [unmanaged type](../builtin-types/unmanaged-types.md) or a type parameter that is [constrained](../../programming-guide/generics/constraints-on-type-parameters.md#unmanaged-constraint) to be an unmanaged type.</span></span>

<span data-ttu-id="0e11c-105">`sizeof` 演算子には [unsafe](../keywords/unsafe.md) コンテキストが必要です。</span><span class="sxs-lookup"><span data-stu-id="0e11c-105">The `sizeof` operator requires an [unsafe](../keywords/unsafe.md) context.</span></span> <span data-ttu-id="0e11c-106">ただし、次の表に示す式は、コンパイル時に対応する定数値に評価され、unsafe コンテキストを必要としません。</span><span class="sxs-lookup"><span data-stu-id="0e11c-106">However, the expressions presented in the following table are evaluated in compile time to the corresponding constant values and don't require an unsafe context:</span></span>

|<span data-ttu-id="0e11c-107">正規表現</span><span class="sxs-lookup"><span data-stu-id="0e11c-107">Expression</span></span>|<span data-ttu-id="0e11c-108">定数値</span><span class="sxs-lookup"><span data-stu-id="0e11c-108">Constant value</span></span>|
|---------|---------------|
|`sizeof(sbyte)`|<span data-ttu-id="0e11c-109">1</span><span class="sxs-lookup"><span data-stu-id="0e11c-109">1</span></span>|
|`sizeof(byte)`|<span data-ttu-id="0e11c-110">1</span><span class="sxs-lookup"><span data-stu-id="0e11c-110">1</span></span>|
|`sizeof(short)`|<span data-ttu-id="0e11c-111">2</span><span class="sxs-lookup"><span data-stu-id="0e11c-111">2</span></span>|
|`sizeof(ushort)`|<span data-ttu-id="0e11c-112">2</span><span class="sxs-lookup"><span data-stu-id="0e11c-112">2</span></span>|
|`sizeof(int)`|<span data-ttu-id="0e11c-113">4</span><span class="sxs-lookup"><span data-stu-id="0e11c-113">4</span></span>|
|`sizeof(uint)`|<span data-ttu-id="0e11c-114">4</span><span class="sxs-lookup"><span data-stu-id="0e11c-114">4</span></span>|
|`sizeof(long)`|<span data-ttu-id="0e11c-115">8</span><span class="sxs-lookup"><span data-stu-id="0e11c-115">8</span></span>|
|`sizeof(ulong)`|<span data-ttu-id="0e11c-116">8</span><span class="sxs-lookup"><span data-stu-id="0e11c-116">8</span></span>|
|`sizeof(char)`|<span data-ttu-id="0e11c-117">2</span><span class="sxs-lookup"><span data-stu-id="0e11c-117">2</span></span>|
|`sizeof(float)`|<span data-ttu-id="0e11c-118">4</span><span class="sxs-lookup"><span data-stu-id="0e11c-118">4</span></span>|
|`sizeof(double)`|<span data-ttu-id="0e11c-119">8</span><span class="sxs-lookup"><span data-stu-id="0e11c-119">8</span></span>|
|`sizeof(decimal)`|<span data-ttu-id="0e11c-120">16</span><span class="sxs-lookup"><span data-stu-id="0e11c-120">16</span></span>|
|`sizeof(bool)`|<span data-ttu-id="0e11c-121">1</span><span class="sxs-lookup"><span data-stu-id="0e11c-121">1</span></span>|

<span data-ttu-id="0e11c-122">`sizeof` 演算子のオペランドが[列挙](../keywords/enum.md)型の名前である場合も、unsafe コンテキストを使用する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="0e11c-122">You also don't need to use an unsafe context when the operand of the `sizeof` operator is the name of an [enum](../keywords/enum.md) type.</span></span>

<span data-ttu-id="0e11c-123">`sizeof` 演算子の使用例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="0e11c-123">The following example demonstrates the usage of the `sizeof` operator:</span></span>

[!code-csharp[sizeof examples](~/samples/csharp/language-reference/operators/SizeOfOperator.cs)]

<span data-ttu-id="0e11c-124">`sizeof` 演算子は、マネージド メモリ内の共通言語ランタイムによって割り当てられるバイト数を返します。</span><span class="sxs-lookup"><span data-stu-id="0e11c-124">The `sizeof` operator returns a number of bytes that would be allocated by the common language runtime in managed memory.</span></span> <span data-ttu-id="0e11c-125">[構造体](../keywords/struct.md)型の場合、前の例のように、その値に埋め込みが含まれます。</span><span class="sxs-lookup"><span data-stu-id="0e11c-125">For [struct](../keywords/struct.md) types, that value includes any padding, as the preceding example demonstrates.</span></span> <span data-ttu-id="0e11c-126">`sizeof` 演算子の結果は、*アンマネージド* メモリの型のサイズを返す <xref:System.Runtime.InteropServices.Marshal.SizeOf%2A?displayProperty=nameWithType> メソッドの結果とは異なる場合があります。</span><span class="sxs-lookup"><span data-stu-id="0e11c-126">The result of the `sizeof` operator might differ from the result of the <xref:System.Runtime.InteropServices.Marshal.SizeOf%2A?displayProperty=nameWithType> method, which returns the size of a type in *unmanaged* memory.</span></span>

## <a name="c-language-specification"></a><span data-ttu-id="0e11c-127">C# 言語仕様</span><span class="sxs-lookup"><span data-stu-id="0e11c-127">C# language specification</span></span>

<span data-ttu-id="0e11c-128">詳細については、[C# 言語仕様](~/_csharplang/spec/introduction.md)の [sizeof 演算子](~/_csharplang/spec/unsafe-code.md#the-sizeof-operator)に関するセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="0e11c-128">For more information, see [The sizeof operator](~/_csharplang/spec/unsafe-code.md#the-sizeof-operator) section of the [C# language specification](~/_csharplang/spec/introduction.md).</span></span>

## <a name="see-also"></a><span data-ttu-id="0e11c-129">関連項目</span><span class="sxs-lookup"><span data-stu-id="0e11c-129">See also</span></span>

- [<span data-ttu-id="0e11c-130">C# リファレンス</span><span class="sxs-lookup"><span data-stu-id="0e11c-130">C# reference</span></span>](../index.md)
- [<span data-ttu-id="0e11c-131">C# 演算子</span><span class="sxs-lookup"><span data-stu-id="0e11c-131">C# operators</span></span>](index.md)
- [<span data-ttu-id="0e11c-132">ポインターに関連する演算子</span><span class="sxs-lookup"><span data-stu-id="0e11c-132">Pointer related operators</span></span>](pointer-related-operators.md)
- [<span data-ttu-id="0e11c-133">ポインター型</span><span class="sxs-lookup"><span data-stu-id="0e11c-133">Pointer types</span></span>](../../programming-guide/unsafe-code-pointers/pointer-types.md)
- [<span data-ttu-id="0e11c-134">メモリおよびスパンに関連する型</span><span class="sxs-lookup"><span data-stu-id="0e11c-134">Memory and span-related types</span></span>](../../../standard/memory-and-spans/index.md)