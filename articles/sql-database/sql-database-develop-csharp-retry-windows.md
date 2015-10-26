<properties 
	pageTitle="C#-Wiederherstellungslogik zum Herstellen einer Verbindung mit einer SQL-Datenbank| Microsoft Azure" 
	description="Das C#-Beispiel enthält die Wiederholungslogik für die zuverlässige Interaktion mit einer Azure SQL-Datenbank." 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/01/2015" 
	ms.author="genemi"/>


# Codebeispiel: Wiederholungslogik in C# für das Herstellen einer Verbindung mit einer SQL-Datenbank


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Dieses Thema enthält ein C#-Codebeispiel, mit dem die benutzerdefinierte Wiederholungslogik veranschaulicht wird. Die Wiederholungslogik dient zum kontrollierten Verarbeiten von vorübergehenden Fehlern (auch: *Übergangsfehler*), die meist nicht mehr vorhanden sind, wenn das Programm einige Sekunden wartet und den Vorgang dann erneut ausführt.


ADO.NET-Klassen, die Sie zum Herstellen einer Verbindung mit dem lokalen Microsoft SQL Server verwenden, können ebenfalls eine Verbindung mit Azure SQL-Datenbank herstellen. Allein können die ADO.NET-Klassen aber nicht für die Robustheit und Zuverlässigkeit sorgen, die für die Verwendung in der Produktion erforderlich ist. In Ihrem Clientprogramm können vorübergehende Fehler auftreten, die vom Programm unbeaufsichtigt und kontrolliert behoben werden können.


Beispiele für vorübergehende Fehler:


- Eine Verbindung über das Internet unterliegt kurzen Netzwerkausfällen, nach denen die Verbindung wiederhergestellt werden kann.

- Cloud Computing umfasst auch den Lastenausgleich, wobei Versuche, eine Verbindung herzustellen oder Abfragen durchzuführen, kurz blockiert werden können.


## Ermitteln von vorübergehenden Fehlern


Ihr Programm muss zwischen vorübergehenden Fehlern und beständigen Fehlern unterscheiden. Wenn im Programm der Name der Zieldatenbank falsch geschrieben ist, bleibt der Fehler der Art „Keine Datenbank gefunden“ bestehen und tritt immer auf, wenn Sie das Programm erneut ausführen.


Die Liste mit den Fehlernummern, die als vorübergehende Fehler kategorisiert werden, finden Sie unter:


- [Fehlermeldungen für Clientprogramme der SQL-Datenbank](sql-database-develop-error-messages.md#bkmk_connection_errors)
 - Siehe den oberen Abschnitt *Vorübergehende Fehler, Verbindungsabbruchfehler*.


## C#-Codebeispiel


Das C#-Codebeispiel in diesem Thema enthält eine benutzerdefinierte Erkennungs- und Wiederholungslogik zum Behandeln von vorübergehenden Fehlern.


Im Codebeispiel werden einige grundlegende Richtlinien oder Empfehlungen befolgt, die unabhängig davon gelten, welche Technologie Sie zum Interagieren mit Azure SQL-Datenbank verwenden. Allgemeine Empfehlungen finden Sie unter:


- [Verbindungen mit SQL-Datenbanken: Wichtige Empfehlungen](sql-database-connect-central-recommendations.md)


Das C#-Codebeispiel besteht aus einer Datei mit dem Namen "Program.cs". Der Code wird im nächsten Abschnitt eingefügt.


### Erfassen und Kompilieren des Codebeispiels


Sie können das Beispiel mit den folgenden Schritten kompilieren:


1. Erstellen Sie in der [kostenlosen Visual Studio Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs) ein neues Projekt mit der Vorlage für die C#-Konsolenanwendung.
 - "Datei" > "Neu" > Projekt" > "Installiert" > "Vorlagen" > "Visual C#" > "Windows" > "Klassischer Desktop" > "Konsolenanwendung"
 - Geben Sie dem Projekt den Namen **RetryAdo2**.

2. Öffnen Sie den Projektmappen-Explorer.
 - Beachten Sie den Namen des Projekts.
 - Beachten Sie den Namen der Datei "Program.cs".

3. Öffnen Sie die Datei "Program.cs".

4. Ersetzen Sie den gesamten Inhalt der Datei "Program.cs" durch den Code im folgenden Codeblock.

5. Klicken Sie auf das Menü "Build" > "Projektmappe erstellen".


#### Einzufügender C#-Quellcode


Fügen Sie diesen Code in die Datei **Program.cs** ein.


Anschließend müssen Sie die Zeichenfolgen für Servername, Kennwort und usw. bearbeiten. Sie finden diese Zeichenfolgen in der Methode **GetSqlConnectionStringBuilder**.


```
using System;   // C#
using G = System.Collections.Generic;
using D = System.Data;
using C = System.Data.SqlClient;
using X = System.Text;
using H = System.Threading;

namespace RetryAdo2
{
	class Program
	{
		static void Main(string[] args)
		{
			Program program = new Program();
			bool returnBool;

			returnBool = program.Run(args);
			if (returnBool == false)
			{
				Console.WriteLine("Something failed.  :-( ");
			}
			return;
		}

		bool Run(string[] _args)
		{
			C.SqlConnectionStringBuilder sqlConnectionSB;
			C.SqlConnection sqlConnection;
			D.IDbCommand dbCommand;
			D.IDataReader dataReader;
			X.StringBuilder sBuilder = new X.StringBuilder(256);
			int retryIntervalSeconds = 10;
			bool returnBool = false;

			Program program = new Program();

			for (int tries = 1; tries <= 5; tries++)
			{
				try
				{
					if (tries > 1)
					{
						H.Thread.Sleep(1000 * retryIntervalSeconds);
						retryIntervalSeconds = Convert.ToInt32(retryIntervalSeconds * 1.5);
					}

					program.GetSqlConnectionStringBuilder(out sqlConnectionSB);

					sqlConnection = new C.SqlConnection(sqlConnectionSB.ToString());

					dbCommand = sqlConnection.CreateCommand();
					dbCommand.CommandText = @"
SELECT TOP 3
      ob.name,
      CAST(ob.object_id as nvarchar(32)) as [object_id]
   FROM sys.objects as ob
   WHERE ob.type='IT'
   ORDER BY ob.name;";

					sqlConnection.Open();
					dataReader = dbCommand.ExecuteReader();

					while (dataReader.Read())
					{
						sBuilder.Length = 0;
						sBuilder.Append(dataReader.GetString(0));
						sBuilder.Append("\t");
						sBuilder.Append(dataReader.GetString(1));

						Console.WriteLine(sBuilder.ToString());
					}
					returnBool = true;
					break;
				}

				catch (C.SqlException sqlExc)
				{
					if (this.m_listTransientErrorNumbers.Contains(sqlExc.Number) == true)
					{ continue; }
					else
					{ throw sqlExc; }
				}
			}
			return returnBool;
		}

		void GetSqlConnectionStringBuilder(out C.SqlConnectionStringBuilder _sqlConnectionSB)
		{
			// Prepare the connection string to Azure SQL Database.
			_sqlConnectionSB = new C.SqlConnectionStringBuilder();

			// Change these values to your values.
			_sqlConnectionSB["Server"] = "tcp:myazuresqldbserver.database.windows.net,1433";
			_sqlConnectionSB["User ID"] = "MyLogin";  // "@yourservername"  as suffix sometimes.
			_sqlConnectionSB["Password"] = "MyPassword";
			_sqlConnectionSB["Database"] = "MyDatabase";

			// Leave these values as they are.
			_sqlConnectionSB["Trusted_Connection"] = false;
			_sqlConnectionSB["Integrated Security"] = false;
			_sqlConnectionSB["Encrypt"] = true;
			_sqlConnectionSB["Connection Timeout"] = 30;
		}

		Program()   // Constructor.
		{
			int[] arrayOfTransientErrorNumbers =
				{ 4060, 10928, 10929, 40197, 40501, 40613, 49918, 49919, 49920
					//,11001   // 11001 for testing, pretend network error is transient.
				};
			m_listTransientErrorNumbers = new G.List<int>(arrayOfTransientErrorNumbers);
		}

		private G.List<int> m_listTransientErrorNumbers;
	}
}
```


### Ausführen des Programms


Die ausführbare Datei **RetryAdo2.exe** gibt keine Parameter ein. So führen Sie die EXE-Datei in Visual Studio aus:


1. Legen Sie in der **return;**-Anweisung der **Main**-Methode einen Haltepunkt fest.

2. Klicken Sie auf die Schaltfläche mit dem grünen Startpfeil. Ein Konsolenfenster wird angezeigt.

3. Wenn der Debugger am Ende der **Main**-Methode anhält, wechseln Sie zum Konsolenfenster.

4. Achten Sie auf drei Zeilen, die eventuell mit den folgenden Zeilen identisch sind:


```
database_firewall_rules_table   245575913
filestream_tombstone_2073058421 2073058421
filetable_updates_2105058535    2105058535
```


## Testen der Wiederholungslogik


Eine praktische Methode zum Testen der Wiederholungslogik:


1. Fügen Sie der Auflistung von **SqlConnection.Number**-Werten, die als vorübergehende Fehler betrachtet werden, vorübergehend **11001** hinzu.

2. Kompilieren Sie das Programm erneut.

3. Trennen Sie den Clientcomputer vom Netzwerk.

4. Führen Sie das Programm in einem Debugger aus, wobei sich ein Haltepunkt in der Schleife befindet.
 - Die erste Schleife schlägt mit dem Fehler 11001 fehl.

5. Wenn das Programm während der zweiten Schleife an einem Haltepunkt anhält, verbinden Sie den Computer wieder mit dem Netzwerk.

6. Setzen Sie die Ausführung des Programms fort. Es wird während der zweiten Schleife erfolgreich ausgeführt.


### Eine weitere Testoption


Eine weitere Möglichkeit besteht darin, dem Programm Logik zum Erkennen des Befehlszeilenparameterwerts "test" hinzuzufügen. Das Programm reagiert auf den Parameter wie folgt:


1. Es fügt vorübergehend falsche Buchstaben hinzu, damit der Name des SQL Datenbankservers falsch buchstabiert wird.

2. Es fügt der Liste vorübergehender Fehler vorübergehend **40615** hinzu.

3. Zu Beginn der zweiten Schleife, d. h. der ersten Wiederholungsschleife, führt das Programm Folgendes aus:
 - Es macht die Fehlschreibung des Servernamens rückgängig.
 - Es entfernt "40615" aus der Liste der vorübergehenden Fehler.


Führen Sie das Programm mit dem Parameter "test" aus, und überprüfen Sie, ob es zunächst fehlschlägt und dann in der zweiten Schleife erfolgreich ausgeführt wird.


## Verwandte Links


- [Clientcodebeispiele für die ersten Schritte mit SQL-Datenbank](sql-database-develop-quick-start-client-code-samples.md)

<!---HONumber=Oct15_HO3-->