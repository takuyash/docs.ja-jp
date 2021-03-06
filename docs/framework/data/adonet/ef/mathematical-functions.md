---
title: 数学関数
ms.date: 03/30/2017
ms.assetid: b040c7cb-156d-40f2-9152-61065b18148c
ms.openlocfilehash: 4512aaa2eeb3e715a1d6f7a8655912eb15162124
ms.sourcegitcommit: 7588136e355e10cbc2582f389c90c127363c02a5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2020
ms.locfileid: "79149766"
---
# <a name="mathematical-functions"></a>数学関数

.NET Framework Data Provider for SQL Server (SqlClient) には、引数として指定された入力値に対して計算を実行し、数値結果を返す数学関数が用意されています。 これらの関数は、SqlClient の SqlServer 名前空間に存在します。 Entity Framework は、プロバイダーの名前空間プロパティを使用することにより、型や関数など、特定のコンストラクターに対してこのプロバイダーによってどのプレフィックスが使用されているかを特定できます。 次の表は、SqlClient の算術関数について説明します。  
  
## <a name="absexpression"></a>ABS(式)

絶対値を求める関数です。

**引数**

`expression`: `Int32`、`Int64`、`Double`、または `Decimal`。

**戻り値**

指定された式の絶対値。

**例**

`SqlServer.ABS(-2)`

## <a name="acosexpression"></a>ACOS(式)

指定された式のアークコサイン (逆余弦) 値を返します。

**引数**

`expression`: `Double`。

**戻り値**

`Double` です。

**例**

`SqlServer.ACOS(.9)`

## <a name="asinexpression"></a>アシン(式)

指定された式のアークサイン (逆正弦) 値を返します。

**引数**

`expression`: `Double`。

**戻り値**

`Double` です。

**例**

`SqlServer.ASIN(.9)`

## <a name="atanexpression"></a>アタン(式)

指定された数値式のアークタンジェント (逆正接) 値を返します。

**引数**

`expression`: `Double`。

**戻り値**

`Double` です。

**例**

`SqlServer.ATAN(9)`

## <a name="atn2expression-expression"></a>ATN2(式、式)

指定された 2 つの数値式の商がタンジェント (正接) となる角度をラジアンで返します。

**引数**

`expression`: `Double`。

**戻り値**

`Double` です。

**例**

`SqlServer.ATN2(9, 8)`

## <a name="ceilingexpression"></a>天井(式)

指定された式をその式以上の最小整数に変換します。

**引数**

`expression`: `Int32`、`Int64`、`Double`、または `Decimal`。

**戻り値**

、 `Int32` `Int64`、 `Double`、`Decimal`または 。

**例**

[!code-sql[DP EntityServices Concepts#SQLSERVER_CEILING](~/samples/snippets/tsql/VS_Snippets_Data/dp entityservices concepts/tsql/entitysql.sql#sqlserver_ceiling)]

## <a name="cosexpression"></a>コス(式)

ラジアンで指定された角度のコサイン (余弦) を計算します。

**引数**

`expression`: `Double`。

**戻り値**

`Double` です。

**例**

`SqlServer.COS(45)`

## <a name="cotexpression"></a>COT(式)

ラジアンで指定された角度のコタンジェント (余接) を計算します。

**引数**

`expression`: `Double`。

**戻り値**

`Double` です。

**例**

`SqlServer.COT(60)`
  
## <a name="degreesradians"></a>度(ラジアン)

対応する角度を度数で返します。

**引数**

`expression`: `Int32`、`Int64`、`Double`、または `Decimal`。

**戻り値**

、 `Int32` `Int64`、 `Double`、`Decimal`または 。

**例**

`SqlServer.DEGREES(3.1)`

## <a name="expexpression"></a>EXP(式)

指定された数値式の指数値を計算します。

**引数**

`expression`: `Double`。

**戻り値**

`Double` です。

**例**`SqlServer.EXP(1)`

## <a name="floorexpression"></a>フロア(式)

指定された式をその式以下の最大整数に変換します。

**引数**

`expression`: `Double`。

**戻り値**

`Double` です。

**例**

[!code-sql[DP EntityServices Concepts#SQLSERVER_FLOOR](~/samples/snippets/tsql/VS_Snippets_Data/dp entityservices concepts/tsql/entitysql.sql#sqlserver_floor)]

## <a name="logexpression"></a>ログ(式)

指定された `float` 型の式の自然対数を計算します。

**引数**

`expression`: `Double`。

**戻り値**

`Double` です。

**例**

`SqlServer.LOG(100)`

## <a name="log10expression"></a>ログ10(式)

指定された `Double` 型の式の 10 を底とした対数を返します。

**引数**

`expression`: `Double`。

**戻り値**

`Double` です。

**例**

`SqlServer.LOG10(100)`

## <a name="pi"></a>PI()

π の定数値を `Double` として返します。

**戻り値**

`Double` です。

**例**

`SqlServer.PI()`

## <a name="powernumeric_expression-power_expression"></a>パワー(numeric_expression、power_expression)

指定された式の指定されたべき乗を計算します。

**引数**

|  |  |
|--|--|
|`numeric_expression`| 、 `Int32` `Int64`、 `Double`、`Decimal`または 。|
|`power_expression`| を`Double`上げる累乗を表す`numeric_expression`A|

**戻り値**

指定された `numeric_expression` を指定された `power_expression` でべき乗した値。

**例**

`SqlServer.POWER(2,7)`

## <a name="radiansexpression"></a>ラジアン(式)

角度をラジアンに変換します。

**引数**

`expression`: `Int32`、`Int64`、`Double`、または `Decimal`。

**戻り値**

、 `Int32` `Int64`、 `Double`、`Decimal`または 。

**例**

`SqlServer.RADIANS(360.0)`

## <a name="randseed"></a>ランド[シード])

0 から 1 までの範囲の乱数を返します。

**引数**

シード値をとして使用`Int32`します。 シードを指定しない場合は、SQL Server データベース エンジンによってシード値がランダムに割り当てられます。 指定されたシード値に対し、返される結果は常に同じです。

**戻り値**

0 から 1 までの範囲の `Double` 型の乱数。

**例**

`SqlServer.RAND()`
  
## <a name="roundnumeric_expression-lengthfunction"></a>ROUND(numeric_expression、長さ[,関数])

指定された長さまたは有効桁数に丸めた数値式を返します。

**引数**

|  |  |
|--|--|
|`numeric_expression`| 、 `Int32` `Int64`、 `Double`、`Decimal`または 。
|`length`| `Int32` を丸めた後の有効桁数を表す `numeric_expression`。 `length` に正の値を指定した場合、`numeric_expression` は `length` で指定した小数点以下桁数に丸められます。 `length` に負の値を指定した場合、`numeric_expression` は `length` で指定した小数点の左側の位置で丸められます。|
|`function` | 省略可能。 実行`Int32`する操作の種類を表す。 関数が省略された場合、または値が 0 (デフォルト`numeric_expression`) の場合は、丸められます。 0 以外の値を指定すると、`numeric_expression`切り捨てられます。 |

**戻り値**

指定された `numeric_expression` を指定された `power_expression` でべき乗した値。

**例**

`SqlServer.ROUND(748.58, -3)`

## <a name="signexpression"></a>記号(式)

指定された式の符号として正 (+1)、ゼロ (0)、負 (-1) のいずれかを返します。

**引数**

`expression`: `Int32`、`Int64`、`Double`、または `Decimal`

**戻り値**

、 `Int32` `Int64`、 `Double`、`Decimal`または 。

**例**

`SqlServer.SIGN(-10)`

## <a name="sinexpression"></a>SIN(式)

ラジアンで指定された角度のサイン (正弦) を計算し、`Double` 式を返します。

**引数**

`expression`: `Double`。

**戻り値**

`Double` です。

**例**`SqlServer.SIN(20)`

## <a name="sqrtexpression"></a>SQRT(式)

指定された式の平方根を返します。

**引数**

`expression`: `Double`。

**戻り値**

`Double` です。

**例**`SqlServer.SQRT(3600)`

## <a name="squareexpression"></a>スクエア(式)

指定された式の 2 乗値を返します。

**引数**

`expression`: `Double`。

**戻り値**

`Double` です。

**例**

`SqlServer.SQUARE(25)`

## <a name="tanexpression"></a>タン(式)

指定された式のタンジェントを計算します。

**引数**

`expression`: `Double`

**戻り値**

`Double`

**例**

`SqlServer.TAN(45.0)`
  
## <a name="see-also"></a>関連項目

- [数学関数 (Transact-SQL)](/sql/t-sql/functions/mathematical-functions-transact-sql)
- [Entity Framework 用 SqlClient 関数](sqlclient-for-ef-functions.md)
