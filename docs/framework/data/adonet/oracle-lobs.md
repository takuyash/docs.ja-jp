---
title: Oracle LOB
ms.date: 03/30/2017
ms.assetid: 272e8e1e-a31f-475a-8c2a-ae8e1286bdab
ms.openlocfilehash: 62525fed85525e26b7c61208fe44b108de562fb0
ms.sourcegitcommit: 7588136e355e10cbc2582f389c90c127363c02a5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2020
ms.locfileid: "79149428"
---
# <a name="oracle-lobs"></a>Oracle LOB
Oracle 用の .NET フレームワーク<xref:System.Data.OracleClient.OracleLob>データ プロバイダーには、Oracle **LOB**データ型を操作するために使用されるクラスが含まれています。  
  
 **OracleLob**は、次<xref:System.Data.OracleClient.OracleType>のいずれかのデータ型になります。  
  
|データ型|説明|  
|---------------|-----------------|  
|[**BLOB**]|最大サイズが 4 GB のバイナリ データを格納する Oracle **BLOB**データ型。 これは **、Byte**型の**配列**にマップされます。|  
|**Clob**|サーバー上の既定の文字セットに基づいて、最大サイズが 4 ギガバイトの文字データを格納する Oracle **CLOB**データ型。 これは**文字列**にマップされます。|  
|**Nclob**|最大サイズが 4 ギガバイトのサーバー上の国別文字セットに基づく、文字データを格納する Oracle **NCLOB**データ型。 これは**文字列**にマップされます。|  
  
 **OracleLob**は、オペレーティング・<xref:System.Data.OracleClient.OracleBFile>システムの物理ファイルではなく、サーバーにデータが保管される点とは異なります。 また **、OracleBFile**とは異なり、読み取り/書き込みオブジェクトを使用することもできます。  
  
## <a name="creating-retrieving-and-writing-to-a-lob"></a>LOB の作成、取得、および書き込み  
 次の C# の例では、Oracle テーブルで LOB を作成し **、OracleLob**オブジェクトの形式で LOB を取得して書き込む方法を示します。 この例<xref:System.Data.OracleClient.OracleDataReader>では、オブジェクトと**OracleLob** **の読み取り**および**書き込み**メソッドの使用方法を示します。 この例では、Oracle **BLOB** **、CLOB、NCLOB**データ型を使用します。 **NCLOB**  
  
```csharp  
using System;  
using System.IO;
using System.Text;
using System.Data;
using System.Data.OracleClient;  
  
// LobExample  
public class LobExample  
{  
   public static int Main(string[] args)  
   {  
      //Create a connection.  
      OracleConnection conn = new OracleConnection(  
         "Data Source=Oracle8i;Integrated Security=yes");  
      using(conn)  
      {  
         //Open a connection.  
         conn.Open();  
         OracleCommand cmd = conn.CreateCommand();  
  
         //Create the table and schema.  
         CreateTable(cmd);  
  
         //Read example.  
         ReadLobExample(cmd);  
  
         //Write example  
         WriteLobExample(cmd);  
      }  
  
      return 1;  
   }  
  
   // ReadLobExample  
   public static void ReadLobExample(OracleCommand cmd)  
   {  
      int actual = 0;  
  
      // Table Schema:  
      // "CREATE TABLE tablewithlobs (a int, b BLOB, c CLOB, d NCLOB)";  
      // "INSERT INTO tablewithlobs values (1, 'AA', 'AAA', N'AAAA')";  
      // Select some data.  
      cmd.CommandText = "SELECT * FROM tablewithlobs";  
      OracleDataReader reader = cmd.ExecuteReader();  
      using(reader)  
      {  
         //Obtain the first row of data.  
         reader.Read();  
  
         //Obtain the LOBs (all 3 varieties).  
         OracleLob blob = reader.GetOracleLob(1);  
         OracleLob clob = reader.GetOracleLob(2);  
         OracleLob nclob = reader.GetOracleLob(3);  
  
         //Example - Reading binary data (in chunks).  
         byte[] buffer = new byte[100];  
         while((actual = blob.Read(buffer, 0, buffer.Length)) >0)  
            Console.WriteLine(blob.LobType + ".Read(" + buffer + ", " +
              buffer.Length + ") => " + actual);  
  
         // Example - Reading CLOB/NCLOB data (in chunks).  
         // Note: You can read character data as raw Unicode bytes
         // (using OracleLob.Read as in the above example).  
         // However, because the OracleLob object inherits directly
         // from the .NET stream object,
         // all the existing classes that manipluate streams can
         // also be used. For example, the
         // .NET StreamReader makes it easier to convert the raw bytes
         // into actual characters.  
         StreamReader streamreader =
           new StreamReader(clob, Encoding.Unicode);  
         char[] cbuffer = new char[100];  
         while((actual = streamreader.Read(cbuffer,
           0, cbuffer.Length)) >0)  
            Console.WriteLine(clob.LobType + ".Read(  
              " + new string(cbuffer, 0, actual) + ", " +
              cbuffer.Length + ") => " + actual);  
  
         // Example - Reading data (all at once).  
         // You could use StreamReader.ReadToEnd to obtain
         // all the string data, or simply  
         // call OracleLob.Value to obtain a contiguous allocation
         // of all the data.  
         Console.WriteLine(nclob.LobType + ".Value => " + nclob.Value);  
      }  
   }  
  
   // WriteLobExample  
   public static void WriteLobExample(OracleCommand cmd)  
   {  
      //Note: Updating LOB data requires a transaction.  
      cmd.Transaction = cmd.Connection.BeginTransaction();  
  
      // Select some data.  
      // Table Schema:  
      // "CREATE TABLE tablewithlobs (a int, b BLOB, c CLOB, d NCLOB)";  
      // "INSERT INTO tablewithlobs values (1, 'AA', 'AAA', N'AAAA')";  
      cmd.CommandText = "SELECT * FROM tablewithlobs FOR UPDATE";  
      OracleDataReader reader = cmd.ExecuteReader();  
      using(reader)  
      {  
         // Obtain the first row of data.  
         reader.Read();  
  
         // Obtain a LOB.  
         OracleLob blob = reader.GetOracleLob(1/*0:based ordinal*/);  
  
         // Perform any desired operations on the LOB
         // (read, position, and so on).  
  
         // Example - Writing binary data (directly to the backend).  
         // To write, you can use any of the stream classes, or write  
         // raw binary data using
         // the OracleLob write method. Writing character vs. binary
         // is the same;  
         // however note that character is always in terms of
         // Unicode byte counts  
         // (for example, even number of bytes - 2 bytes for every  
         // Unicode character).  
         byte[] buffer = new byte[100];  
         buffer[0] = 0xCC;  
         buffer[1] = 0xDD;  
         blob.Write(buffer, 0, 2);  
         blob.Position = 0;  
         Console.WriteLine(blob.LobType + ".Write(  
           " + buffer + ", 0, 2) => " + blob.Value);  
  
         // Example - Obtaining a temp LOB and copying data
         // into it from another LOB.  
         OracleLob templob = CreateTempLob(cmd, blob.LobType);  
         long actual = blob.CopyTo(templob);  
         Console.WriteLine(blob.LobType + ".CopyTo(  
            " + templob.Value + ") => " + actual);  
  
         // Commit the transaction now that everything succeeded.  
         // Note: On error, Transaction.Dispose is called
         // (from the using statement)  
         // and will automatically roll back the pending transaction.  
         cmd.Transaction.Commit();  
      }  
   }  
  
   // CreateTempLob  
   public static OracleLob CreateTempLob(  
     OracleCommand cmd, OracleType lobtype)  
   {  
      //Oracle server syntax to obtain a temporary LOB.  
      cmd.CommandText = "DECLARE A " + lobtype + "; "+  
                     "BEGIN "+  
                        "DBMS_LOB.CREATETEMPORARY(A, FALSE); "+  
                        ":LOC := A; "+  
                     "END;";  
  
      //Bind the LOB as an output parameter.  
      OracleParameter p = cmd.Parameters.Add("LOC", lobtype);  
      p.Direction = ParameterDirection.Output;  
  
      //Execute (to receive the output temporary LOB).  
      cmd.ExecuteNonQuery();  
  
      //Return the temporary LOB.  
      return (OracleLob)p.Value;  
   }  
  
   // CreateTable  
   public static void CreateTable(OracleCommand cmd)  
   {  
      // Table Schema:  
      // "CREATE TABLE tablewithlobs (a int, b BLOB, c CLOB, d NCLOB)";  
      // "INSERT INTO tablewithlobs VALUES (1, 'AA', 'AAA', N'AAAA')";  
      try  
      {  
         cmd.CommandText   = "DROP TABLE tablewithlobs";  
         cmd.ExecuteNonQuery();  
      }  
      catch(Exception)  
      {  
      }  
  
      cmd.CommandText =
        "CREATE TABLE tablewithlobs (a int, b BLOB, c CLOB, d NCLOB)";  
      cmd.ExecuteNonQuery();  
      cmd.CommandText =
        "INSERT INTO tablewithlobs VALUES (1, 'AA', 'AAA', N'AAAA')";  
      cmd.ExecuteNonQuery();  
   }  
}  
```  
  
## <a name="creating-a-temporary-lob"></a>一時 LOB の作成  
 次の C# の例では、一時 LOB の作成方法について説明します。  
  
```csharp  
OracleConnection conn = new OracleConnection(  
  "server=test8172; integrated security=yes;");  
conn.Open();  
  
OracleTransaction tx = conn.BeginTransaction();  
  
OracleCommand cmd = conn.CreateCommand();  
cmd.Transaction = tx;  
cmd.CommandText =
  "declare xx blob; begin dbms_lob.createtemporary(  
  xx, false, 0); :tempblob := xx; end;";  
cmd.Parameters.Add(new OracleParameter("tempblob",  
  OracleType.Blob)).Direction = ParameterDirection.Output;  
cmd.ExecuteNonQuery();  
OracleLob tempLob = (OracleLob)cmd.Parameters[0].Value;  
tempLob.BeginBatch(OracleLobOpenMode.ReadWrite);  
tempLob.Write(tempbuff,0,tempbuff.Length);  
tempLob.EndBatch();  
cmd.Parameters.Clear();  
cmd.CommandText = "myTable.myProc";  
cmd.CommandType = CommandType.StoredProcedure;
cmd.Parameters.Add(new OracleParameter(  
  "ImportDoc", OracleType.Blob)).Value = tempLob;  
cmd.ExecuteNonQuery();  
  
tx.Commit();  
```  
  
## <a name="see-also"></a>関連項目

- [Oracle および ADO.NET](oracle-and-adonet.md)
- [ADO.NET の概要](ado-net-overview.md)
