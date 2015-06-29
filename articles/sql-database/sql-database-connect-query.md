<properties
	pageTitle="Verbinden mit und Abfragen der SQL-Datenbank mit C#"
	description="Codebeispiel für einen C#-Client mithilfe von ADO.NET zum Herstellen einer Verbindung und Interagieren mit der AdventureWorks-Datenbank im Clouddienst von Azure SQL-Datenbank."
	services="sql-database"
	documentationCenter=""
	authors="ckarst"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="get-started-article" 
	ms.date="04/14/2015"
	ms.author="cakarst"/>


# Verbinden mit der SQL-Datenbank und Abfragen von dieser mit C&#x23;


Dieses Thema enthält ein C#-Codebeispiel, das zum Herstellen einer Verbindung mit einer vorhandenen AdventureWorks-SQL-Datenbank mithilfe von ADO.NET verwendet werden kann. Das Beispiel wird in eine Konsolenanwendung kompiliert, die die Datenbank abfragt und das Resultset angezeigt.


## Voraussetzungen


- Eine vorhandene AdventureWorks-Datenbank in Azure SQL-Datenbank. [Sie können eine Datenbank in wenigen Minuten erstellen](sql-database-get-started.md).
- [Visual Studio mit .NET Framework](https://www.visualstudio.com/de-de/visual-studio-homepage-vs.aspx)


## Schritt 1: Konsolenanwendung


1. Erstellen Sie mithilfe von Visual Studio eine C#-Konsolenanwendung.


![Herstellen der Verbindung und Abfragen](./media/sql-database-connect-query/ConnectandQuery_VisualStudio.png)


## Schritt 2: SQL-Codebeispiel


1. Kopieren Sie das Codebeispiel unten, und fügen Sie es in der Konsolenanwendung ein.


> [AZURE.WARNING]Das Codebeispiel soll so kurz wie möglich sein, um das Verständnis zu erleichtern. Das Beispiel ist nicht für den Einsatz in einer Produktionsumgebung vorgesehen.


Dieser Code ist nicht für die Produktion vorgesehen. Wenn Sie Code für eine Produktionsumgebung implementieren möchten, sollten Sie die folgenden bewährte Methoden für die Branche berücksichtigen:


- Fehlerbehandlung
- Wiederholungslogik für vorübergehende Fehler
- Sicheres Speichern von Kennwörtern in einer Konfigurationsdatei



### Quellcode für das C#-Beispiel


Fügen Sie diesen Quellcode in die Datei **Program.cs** ein.


	using System;  // C#
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.Threading.Tasks;
	using System.Data.SqlClient;

	namespace ConnectandQuery_Example
	{
		class Program
		{
			static void Main()
			{
				string SQLConnectionString = "[Your_Connection_String]";
				// Create a SqlConnection from the provided connection string.
				using (SqlConnection connection = new SqlConnection(SQLConnectionString))
				{
					// Begin to formulate the command.
					SqlCommand command = new SqlCommand();
					command.Connection = connection;

					// Specify the query to be executed.
					command.CommandType = System.Data.CommandType.Text;
						command.CommandText =
						@"SELECT TOP 10
						CustomerID, NameStyle, Title, FirstName, LastName
						FROM SalesLT.Customer";

					// Open connection to database.
					connection.Open();

					// Read data from the query.
					SqlDataReader reader = command.ExecuteReader();
					while (reader.Read())
					{
						// Formatting will depend on the contents of the query.
						Console.WriteLine("Value: {0}, {1}, {2}, {3}, {4}",
							reader[0], reader[1], reader[2], reader[3], reader[4]);
					}
				}
				Console.WriteLine("Press any key to continue...");
				Console.ReadKey();
			}
		}
	}


## Schritt 3: Ermitteln der Verbindungszeichenfolge für die Datenbank


1. Öffnen Sie das [Azure-Vorschauportal](http://portal.azure.com/).
2. Klicken Sie auf **Durchsuchen** > **SQL-Datenbanken** > **"Adventure Works"-Datenbank** > **Eigenschaften** > **Show Database Connection Strings**.


![Portal](./media/sql-database-connect-query/ConnectandQuery_portal.png)


Auf dem Blatt mit den Verbindungszeichenfolgen für Datenbanken werden die entsprechenden Verbindungszeichenfolgen für ADO.NET, ODBC, PHP und JDBC angezeigt.


## Schritt 4: Einfügen der tatsächlichen Verbindungsinformationen


- Ersetzen Sie in dem von Ihnen eingefügten Quellcode den Platzhalter *[Your_Connection_String]* durch die Verbindungszeichenfolge und unbedingt auch *Your_password_here* in dieser Zeichenfolge durch Ihr tatsächliches Kennwort.


## Schritt 5: Ausführen der Anwendung


1. Zum Erstellen und Ausführen der Anwendung klicken Sie auf **DEBUGGEN** > **Debuggen starten**.
2. Das Programm zeigt die Ergebnisse der Abfrage im Konsolenfenster an.
 

<!---HONumber=58_postMigration-->