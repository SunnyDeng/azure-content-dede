
<!--
includes/sql-database-include-connection-string-40-config.md

Latest Freshness check:  2015-09-04 , GeneMi.

## Connection string
-->


### Beispiel-Konfigurationsdatei für die Sicherheit der Verbindungszeichenfolge


Es ist nicht empfehlenswert, die Verbindungszeichenfolge als Literale in Ihren C#-Code einzufügen. Es ist besser, diese in einer Konfigurationsdatei abzulegen. Sie können die Zeichenfolge dort jederzeit bearbeiten, ohne dass eine neue Kompilierung erforderlich ist.

Angenommen, Ihr kompiliertes C#-Programm heißt **ConsoleApplication1.exe**, und diese .exe befindet sich in einem Verzeichnis namens **bin\\debug**.

In diesem Beispiel befinden sich die meisten Teile der Verbindungszeichenfolge in einer Konfigurationsdatei mit dem genauen Namen **ConsoleApplication1.exe.config**. Diese Konfigurationsdatei muss sich ebenfalls in **bin\\debug** befinden.

Im XML-Code der folgenden Konfigurationsdatei finden Sie eine Verbindungszeichenfolge mit dem Namen **ConnectionString4NoUserIDNoPassword**. Der C#-Code sucht nach dieser Zeichenfolge.

Bearbeiten Sie die tatsächlichen Namen für die Platzhalter:

- {your\_serverName\_here}
- {your\_databaseName\_here}



		<?xml version="1.0" encoding="utf-8" ?>
		<configuration>
		    <startup> 
		        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
		    </startup>
		
		    <connectionStrings>
		        <clear />
		        <add name="ConnectionString4NoUserIDNoPassword"
		        providerName="System.Data.ProviderName"
		
		        connectionString=
				"Server=tcp:{your_serverName_here}.database.windows.net,1433;
				Database={your_databaseName_here};
				Connection Timeout=30;
				Encrypt=True;
				TrustServerCertificate=False;" />
		    </connectionStrings>
		</configuration>



In dieser Abbildung haben wir zwei Parameter ausgelassen:

- Benutzer-ID={your\_userName\_here};
- Kennwort={Your\_password\_here};


Sie können diese einschließen, aber manchmal ist es besser, wenn das Programm diese Werte durch Tastatureingaben vom Benutzer erhält. Das ist unterschiedlich.



<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->

<!---HONumber=Oct15_HO3-->