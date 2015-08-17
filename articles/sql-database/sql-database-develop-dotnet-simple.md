<properties 
	pageTitle="Verwenden von SQL-Datenbanken mit .NET (C#)" 
	description="Verwenden Sie den Beispielcode in diesem Schnelleinstieg zum Erstellen einer modernen Anwendung in C#, die mit Azure SQL-Datenbank durch eine leistungsfähige relationale Datenbank in der Cloud unterstützt wird."
	services="sql-database" 
	documentationCenter="" 
	authors="tobbox" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="07/16/2015" 
	ms.author="tobiast"/>


# Verwenden von SQL-Datenbanken mit .NET (C#) 


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


## Anforderungen

### .NET Framework

.NET Framework ist unter Windows vorinstalliert. Für Linux und Mac OS X können Sie .NET Framework vom [Mono-Projekt](http://www.mono-project.com/) herunterladen.

### Eine SQL-Datenbank

Auf der Seite [Erste Schritte](sql-database-get-started.md) erhalten Sie Informationen zum Erstellen einer Beispieldatenbank und zum Abrufen der Verbindungszeichenfolge.

## Herstellen von Verbindungen mit der SQL-Datenbank

Die [System.Data.SqlClient.SqlConnection-Klasse](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) wird zum Herstellen von Verbindungen mit SQL-Datenbanken verwendet.
	
```
using System.Data.SqlClient;

class Sample
{
  static void Main()
  {
	  using(var conn = new SqlConnection("Server=tcp:yourserver.database.windows.net,1433;Database=yourdatabase;User ID=yourlogin@yourserver;Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"))
	  {
		  conn.Open();	
	  }
  }
}	
```

## Ausführen einer Abfrage und Abrufen des Resultsets 

Mit der [System.Data.SqlClient.SqlCommand](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.aspx)-Klasse und der [SqlDataReader](https://msdn.microsoft.com/library/system.data.sqlclient.sqldatareader.aspx)-Klasse können Sie ein Resultset aus einer Abfrage an eine SQL-Datenbank abrufen. Beachten Sie, dass "System.Data.SqlClient" auch das Abrufen von Daten in ein Offline-[System.Data.DataSet](https://msdn.microsoft.com/library/system.data.dataset.aspx) unterstützt.
	
```
using System;
using System.Data.SqlClient;

class Sample
{
	static void Main()
	{
	  using(var conn = new SqlConnection("Server=tcp:yourserver.database.windows.net,1433;Database=yourdatabase;User ID=yourlogin@yourserver;Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"))
		{
			var cmd = conn.CreateCommand();
			cmd.CommandText = @"
					SELECT 
						c.CustomerID
						,c.CompanyName
						,COUNT(soh.SalesOrderID) AS OrderCount
					FROM SalesLT.Customer AS c
					LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID
					GROUP BY c.CustomerID, c.CompanyName
					ORDER BY OrderCount DESC;";

			conn.Open();	
		
			using(var reader = cmd.ExecuteReader())
			{
				while(reader.Read())
				{
					Console.WriteLine("ID: {0} Name: {1} Order Count: {2}", reader.GetInt32(0), reader.GetString(1), reader.GetInt32(2));
				}
			}					
		}
	}
}

```

## Einfügen von Zeilen, Übergeben von Parametern und Abrufen der generierten Primärschlüsselwerte 

In SQL-Datenbanken können die [IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx)-Eigenschaft und das [SEQUENCE](https://msdn.microsoft.com/library/ff878058.aspx)-Objekt zum automatischen Generieren von Werten für [Primärschlüssel](https://msdn.microsoft.com/library/ms179610.aspx) verwendet werden. In diesem Beispiel erfahren Sie, wie Sie eine ["insert"-Anweisung](https://msdn.microsoft.com/library/ms174335.aspx) ausführen, Parameter zum Schutz vor einer [Einschleusung von SQL-Befehlen](https://msdn.microsoft.com/magazine/cc163917.aspx) sicher übergeben und den automatisch generierten Primärschlüsselwert abrufen.

Mit der [ExecuteScalar](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executescalar.aspx)-Methode in der [System.Data.SqlClient.SqlCommand](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.aspx)-Klasse können Sie eine Anweisung ausführen und die erste von dieser Anweisung zurückgegebene Spalte und Zeile abrufen. Mit der [OUTPUT](https://msdn.microsoft.com/library/ms177564.aspx)-Klausel der INSERT-Anweisung können Sie die eingefügten Werte als Resultset an die aufrufende Anwendung zurückgeben. Beachten Sie, dass OUTPUT auch von den Anweisungen [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx), [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) und [MERGE](https://msdn.microsoft.com/library/bb510625.aspx) unterstützt wird. Wenn mehr als eine Zeile eingefügt wird, sollten Sie mit der [ExecuteReader](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executereader.aspx)-Methode die eingefügten Werte für alle Zeilen abrufen.
	
```
class Sample
{
    static void Main()
    {
		using(var conn = new SqlConnection("Server=tcp:yourserver.database.windows.net,1433;Database=yourdatabase;User ID=yourlogin@yourserver;Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"))
        {
            var cmd = conn.CreateCommand();
            cmd.CommandText = @"
                INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) 
                OUTPUT INSERTED.ProductID
                VALUES (@Name, @Number, @Cost, @Price, CURRENT_TIMESTAMP)";

            cmd.Parameters.AddWithValue("@Name", "SQL Server Express");
            cmd.Parameters.AddWithValue("@Number", "SQLEXPRESS1");
            cmd.Parameters.AddWithValue("@Cost", 0);
            cmd.Parameters.AddWithValue("@Price", 0);

            conn.Open();

            int insertedProductId = (int)cmd.ExecuteScalar();

            Console.WriteLine("Product ID {0} inserted.", insertedProductId);
        }
    }
}
```

 

<!---HONumber=August15_HO6-->