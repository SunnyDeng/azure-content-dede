<properties
	pageTitle="Herstellen einer Verbindung mit SQL-Datenbank mithilfe einer C#-Abfrage | Microsoft Azure"
	description="Schreiben Sie ein Programm in C# zum Abfragen und Herstellen einer Verbindung mit einer SQL-Datenbank. Informationen zu IP-Adressen, Verbindungszeichenfolgen, sicherer Anmeldung und kostenlosem Visual Studio."
	services="sql-database"
	keywords="C#-Datenbankabfrage, C#-Abfrage, mit Datenbank verbinden, SQL C#"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="10/09/2015"
	ms.author="genemi"/>


# Schreiben eines Programms in C# zum Abfragen und Herstellen einer Verbindung mit einer SQL-Datenbank

> [AZURE.SELECTOR]
- [C#](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

Erfahren Sie, wie Sie ein C#-Programm schreiben, mit dem eine Verbindung mit einer Azure SQL-Datenbank hergestellt wird, um diese abzufragen.

In diesem Artikel werden alle Schritte für Benutzer beschrieben, die mit Azure SQL-Datenbank, C# und ADO.NET noch nicht vertraut sind. Andere Benutzer, die bereits Erfahrung mit Microsoft SQL Server und C# besitzen, können möglicherweise einige Schritte überspringen und sich auf die Schritte konzentrieren, die sich speziell auf die SQL-Datenbank beziehen.


## Voraussetzungen


Zum Ausführen des C#-Abfragecodebeispiels benötigen Sie Folgendes:


- Ein Azure-Konto und ein Azure-Abonnement. Sie können sich für eine [kostenlose Testversion](https://azure.microsoft.com/pricing/free-trial/) registrieren.


- Eine **AdventureWorksLT**-Demodatenbank im Azure SQL-Datenbankdienst.
 - [Erstellen Sie die Demodatenbank](sql-database-get-started.md) in Minuten.


- Visual Studio 2013, Update 4 (oder höher). Microsoft bietet Visual Studio Community jetzt *kostenlos* an.
 - [Download von Visual Studio Community](http://www.visualstudio.com/products/visual-studio-community-vs)
 - [Weitere Optionen für kostenlose Visual Studio-Versionen](http://www.visualstudio.com/products/free-developer-offers-vs.aspx)
 - Oder lesen Sie in einem [Schritt](#InstallVSForFree) weiter unten in diesem Thema, wie das [Azure-Portal](https://portal.azure.com/) Ihnen bei der Installation von Visual Studio hilft.


<a name="InstallVSForFree" id="InstallVSForFree"></a>

&nbsp;

## Schritt 1: Kostenloses Installieren von Visual Studio Community


Zur Installation von Visual Studio haben Sie folgende Möglichkeiten:

- Installieren Sie Visual Studio Community kostenlos, indem Sie in Ihrem Browser die Visual Studio-Produktwebseiten aufrufen, auf denen kostenlose Downloads und andere Optionen angeboten werden.
- Lassen Sie sich vom [Azure-Portal](https://portal.azure.com/) zur Downloadwebseite führen, die als Nächstes beschrieben wird.


### Visual Studio über das Azure-Portal


1. Melden Sie sich über das [Azure-Portal](https://portal.azure.com/) unter http://portal.azure.com/ an.

2. Klicken Sie auf **ALLE DURCHSUCHEN*** > **SQL-Datenbanken**. Ein Blatt für die Suche nach Datenbanken wird geöffnet.

3. Beginnen Sie im Filtertextfeld im oberen Bereich mit der Eingabe des Namens Ihrer **AdventureWorksLT**-Datenbank.

4. Wenn die Zeile für die Datenbank auf dem Server angezeigt wird, klicken Sie auf die Zeile. Ein Blatt für die Datenbank wird geöffnet.

5. Der Einfachheit halber klicken Sie für alle vorherigen Blätter auf das Steuerelement zum Minimieren.

6. Klicken Sie im oberen Bereich des Blatts "Datenbank" auf die Schaltfläche **In Visual Studio öffnen**. Ein neues Blatt zu Visual Studio wird geöffnet. Es enthält Links zu den Speicherorten für die Visual Studio-Installation.

	![Schaltfläche "In Visual Studio öffnen"][20-OpenInVisualStudioButton]

7. Klicken Sie auf den Link **Community (kostenlos)** oder einen ähnlichen Link. Eine neue Webseite wird hinzugefügt.

8. Verwenden Sie die Links auf der Webseite für die Installation von Visual Studio.

9. Sobald Visual Studio installiert ist, klicken Sie auf dem Blatt **In Visual Studio öffnen** auf die Schaltfläche **In Visual Studio öffnen**. Visual Studio wird geöffnet.

10. Für den Bereich **SQL Server-Objekt-Explorer** werden Sie aufgefordert, Felder zur Verbindungszeichenfolge in einem Dialogfeld ausfüllen.
 - Wählen Sie statt der **Windows-Authentifizierung** die **SQL Server-Authentifizierung**.
 - Denken Sie daran, Ihre **AdventureWorksLT**-Datenbank anzugeben (im Dialogfeld unter **Optionen** > **Verbindungseigenschaften**).

11. Erweitern Sie im **SQL Server-Objekt-Explorer** den Knoten für Ihre Datenbank.


## Schritt 2: Erstellen eines neuen Projekts in Visual Studio


Erstellen Sie in Visual Studio ein neues Projekt, das auf der Startvorlage für C# > Windows > **Konsolenanwendung** beruht.


1. Klicken Sie auf **Datei** > **Neu** > **Projekt**. Das Dialogfeld **** wird angezeigt.

2. Erweitern Sie unter **Installiert** die Einträge "C#" und "Windows", sodass die Option **Konsolenanwendung** im mittleren Bereich angezeigt wird.

	![Dialogfeld "Neues Projekt"][30-VSNewProject]

2. Geben Sie unter **Name** den Namen **ConnectAndQuery\_Example** ein. Klicken Sie auf **OK**.


## Schritt 3: Hinzufügen eines Assemblyverweises für die Konfigurationsverarbeitung


Unser C#-Beispiel verwendet die .NET Framework-Assembly **System.Configuration.dll**. Deshalb fügen wir einen entsprechenden Verweis hinzu.


1. Klicken Sie im Bereich **Projektmappen-Explorer** mit der rechten Maustaste auf **Verweise** > **Verweis hinzufügen**. Das Fenster **Verweis-Manager** wird angezeigt.

2. Erweitern Sie **Assemblys** > **Framework**.

3. Führen Sie einen Bildlauf aus, und markieren Sie **System.Configuration**. Stellen Sie sicher, dass das Kontrollkästchen aktiviert ist.

4. Klicken Sie auf **OK**.

5. Kompilieren Sie das Programm über das Menü **ERSTELLEN** > **Projektmappe erstellen**.


## Schritt 4: Abrufen der Verbindungszeichenfolge


Kopieren Sie im [Azure-Portal](https://portal.azure.com/) die Verbindungszeichenfolge, die für das Verbinden mit der SQL-Datenbank benötigt wird.

Ihre erste Verwendung dient zum Verbinden von Visual Studio mit der Azure SQL-Datenbank **AdventureWorksLT**.


[AZURE.INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]


## Schritt 5: Hinzufügen der Verbindungszeichenfolge zur Datei "App.config"


1. Öffnen Sie in Visual Studio im Bereich "Projektmappen-Explorer" die Datei "App.config".

2. Fügen Sie das Element **&#x3c;configuration&#x3e; &#x3c;/configuration&#x3e;** hinzu, wie im folgenden "App.config"-Codebeispiel gezeigt.
 - Ersetzen Sie *{Ihre\_Platzhalter}* durch die tatsächlichen Werte:

```
	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
	    <startup>
	        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
	    </startup>

		<connectionStrings>
			<clear />
			<add name="ConnectionString4NoUserIDNoPassword"
			connectionString="Server=tcp:{your_serverName_here}.database.windows.net,1433; Database={your_databaseName_here}; Connection Timeout=30; Encrypt=True; TrustServerCertificate=False;"
			/>
		</connectionStrings>
	</configuration>
```

3. Speichern Sie die Änderung an der Datei "App.config".

4. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Knoten **App.config**, und klicken Sie dann auf **Eigenschaften**.

5. Legen Sie **In Ausgabeverzeichnis kopieren** auf **Immer kopieren** fest.
 - Dadurch wird in dem Verzeichnis, in dem die Datei "&#x2a;.exe" erstellt wird, der Inhalt der Datei "&#x2a;.exe.config" durch den Inhalt Ihrer Datei "App.config" ersetzt. Diese Ersetzung erfolgt jedes Mal, wenn Sie die Datei "&#x2a;.exe" neu kompilieren.
 - Die Datei "&#x2a;.exe.config" wird gelesen, wenn unser C#-Beispielprogramm ausgeführt wird.

	![In Ausgabeverzeichnis kopieren = Immer kopieren][50-VSCopyToOutputDirectoryProperty]


## Schritt 6: Einfügen des C#-Beispielcodes


1. Verwenden Sie in Visual Studio den Bereich **Projektmappen-Explorer**, um Ihre Datei **Program.cs** zu öffnen.

	![Einfügen des C#-Abfragecodes][40-VSProgramCsOverlay]

2. Überschreiben Sie den gesamten Startcode in "Program.cs" durch Einfügen des folgenden C#-Beispielcodes.
 - Wenn Sie ein kürzeres Codebeispiel verwenden möchten, können Sie der Variablen **SQLConnectionString** die gesamte Verbindungszeichenfolge als Literal zuweisen. Anschließend können Sie die beiden Methoden **GetConnectionStringFromExeConfig** und **GatherPasswordFromConsole** löschen.


```
using System;  // C#
using G = System.Configuration;   // System.Configuration.dll
using D = System.Data;            // System.Data.dll
using C = System.Data.SqlClient;  // System.Data.dll
using T = System.Text;

namespace ConnectAndQuery_Example
{
	class Program
	{
		static void Main()
		{
			string connectionString4NoUserIDNoPassword,
				password, userName, SQLConnectionString;

			// Get most of the connection string from ConnectAndQuery_Example.exe.config
			// file, in the same directory where ConnectAndQuery_Example.exe resides.
			connectionString4NoUserIDNoPassword = Program.GetConnectionStringFromExeConfig
				("ConnectionString4NoUserIDNoPassword");
			// Get the user name from keyboard input.
			Console.WriteLine("Enter your User ID, without the trailing @ and server name: ");
			userName = Console.ReadLine();
			// Get the password from keyboard input.
			password = Program.GatherPasswordFromConsole();

			SQLConnectionString = "Password=" + password + ';' +
				"User ID=" + userName + ";" + connectionString4NoUserIDNoPassword;

			// Create an SqlConnection from the provided connection string.
			using (C.SqlConnection connection = new C.SqlConnection(SQLConnectionString))
			{
				// Formulate the command.
				C.SqlCommand command = new C.SqlCommand();
				command.Connection = connection;

				// Specify the query to be executed.
				command.CommandType = D.CommandType.Text;
				command.CommandText = @"
					SELECT TOP 9 CustomerID, NameStyle, Title, FirstName, LastName
					FROM SalesLT.Customer;  -- In AdventureWorksLT database.
					";
				// Open a connection to database.
				connection.Open();

				// Read data returned for the query.
				C.SqlDataReader reader = command.ExecuteReader();
				while (reader.Read())
				{
					Console.WriteLine("Values:  {0}, {1}, {2}, {3}, {4}",
						reader[0], reader[1], reader[2], reader[3], reader[4]);
				}
			}
			Console.WriteLine("View the results here, then press any key to finish...");
			Console.ReadKey(true);
		}
		//----------------------------------------------------------------------------------

		static string GetConnectionStringFromExeConfig(string connectionStringNameInConfig)
		{
			G.ConnectionStringSettings connectionStringSettings =
				G.ConfigurationManager.ConnectionStrings[connectionStringNameInConfig];

			if (connectionStringSettings == null)
			{
				throw new ApplicationException(String.Format
					("Error. Connection string not found for name '{0}'.",
					connectionStringNameInConfig));
			}
				return connectionStringSettings.ConnectionString;
		}

		static string GatherPasswordFromConsole()
		{
			T.StringBuilder passwordBuilder = new T.StringBuilder(32);
			ConsoleKeyInfo key;
			Console.WriteLine("Enter your password: ");
			do
			{
				key = Console.ReadKey(true);
				if (key.Key != ConsoleKey.Backspace)
				{
					passwordBuilder.Append(key.KeyChar);
					Console.Write("*");
				}
				else  // Backspace char was entered.
				{
					// Retreat the cursor, overlay '*' with ' ', retreat again.
					Console.Write("\b \b");
					passwordBuilder.Length = passwordBuilder.Length - 1;
				}
			}
			while (key.Key != ConsoleKey.Enter); // Enter key will end the looping.
			Console.WriteLine(Environment.NewLine);
			return passwordBuilder.ToString();
		}
	}
}
```


### Kompilieren des Programms


1. Kompilieren Sie das Programm in Visual Studio, indem Sie auf das Menü **Erstellen** > **Projektmappe erstellen** klicken.


### Zusammenfassung der Aktionen im Beispielprogramm


1. Liest den größten Teil der SQL-Verbindungszeichenfolge aus einer Konfigurationsdatei.

2. Erfasst den Benutzernamen und das Kennwort über die Tastatur und fügt sie der Verbindungszeichenfolge hinzu, um diese zu vervollständigen.

3. Verwendet die Verbindungszeichenfolge und ADO.NET-Klassen, um eine Verbindung mit der Demodatenbank **AdventureWorksLT** in Azure SQL-Datenbank herzustellen.

4. Ruft den SQL-Befehl **SELECT** auf, um Einträge aus der Tabelle **SalesLT** auszulesen.

5. Druckt die zurückgegebenen Zeilen an die Konsole.


Wir versuchen, das C#-Beispiel kurz zu halten. Dennoch haben wir Code zum Lesen einer Konfigurationsdatei hinzugefügt, um mehrere Anfragen von Kunden wie Ihnen zu berücksichtigen. Wir sind uns einig, dass für hochwertige Produktionsprogramme Konfigurationsdateien anstelle von Literalen verwendet werden sollten, die in der EXE-Datei hartcodiert sind.


> [AZURE.WARNING] Um den Code kurz zu halten, haben wir uns entschieden, in diesem Lernbeispiel keinen Code für Ausnahmebehandlung und Wiederholungslogik einzubeziehen. Ihre Produktionsprogramme, die mit einer Clouddatenbank interagieren, sollten jedoch beides beinhalten.
>
> [Hier](sql-database-develop-csharp-retry-windows.md) finden Sie einen Link zu einem Codebeispiel, das Wiederholungslogik enthält.


## Schritt 7: Hinzufügen eines zulässigen IP-Adressbereichs in der Serverfirewall


Das C#-Clientprogramm kann erst dann eine Verbindung mit Azure SQL-Datenbank herstellen, wenn die IP-Adresse des Clientcomputers der SQL-Datenbankfirewall hinzugefügt wurde. Das Programm gibt eine hilfreiche Fehlermeldung aus, in der die erforderliche IP-Adresse genannt wird.


Sie können die IP-Adresse über das [Azure-Portal](https://portal.azure.com/) hinzufügen.



[AZURE.INCLUDE [sql-database-include-ip-address-22-v12portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]



Weitere Informationen finden Sie unter:<br/> [Konfigurieren der Firewalleinstellungen für Azure SQL-Datenbank](sql-database-configure-firewall-settings.md).



## Schritt 8: Ausführen des Programms


1. Führen Sie die C#-Abfrage in Visual Studio über das Menü **DEBUGGEN** > **Debuggen starten** aus. Ein Konsolenfenster wird angezeigt.

2. Geben Sie Ihren Benutzernamen und das Kennwort ein, wie beschrieben.
 - Für einige Verbindungstools ist es erforderlich, "@{Servername}" an den Benutzernamen anzuhängen. Für ADO.NET ist dieses Suffix optional. Sie müssen es an dieser Stelle nicht eingeben.

3. Datenzeilen werden angezeigt.


## Verwandte Links


- [Clientcodebeispiele für die ersten Schritte mit SQL-Datenbank](sql-database-develop-quick-start-client-code-samples.md)

- Wenn das Clientprogramm auf einem virtuellen Azure-Computer ausgeführt wird, finden Sie weitere Informationen zu anderen TCP-Ports als 1433 unter:<br/>[Andere Ports als 1433 für ADO.NET 4.5 und SQL-Datenbank V12](sql-database-develop-direct-route-ports-adonet-v12.md).



<!-- Image references. -->

[20-OpenInVisualStudioButton]: ./media/sql-database-connect-query/connqry-free-vs-e.png

[30-VSNewProject]: ./media/sql-database-connect-query/connqry-vs-new-project-f.png

[40-VSProgramCsOverlay]: ./media/sql-database-connect-query/connqry-vs-program-cs-overlay-g.png

[50-VSCopyToOutputDirectoryProperty]: ./media/sql-database-connect-query/connqry-vs-appconfig-copytoputputdir-h.png

<!---HONumber=AcomDC_0128_2016-->