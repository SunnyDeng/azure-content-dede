
<!--
includes/sql-database-include-connection-string-30-compare.md

Latest Freshness check:  2015-09-03 , GeneMi.

## Connection string
-->


### Vergleichen der Verbindungszeichenfolge


In der folgenden Tabelle werden die Verbindungszeichenfolgen verglichen, die das C#-Programm zum Herstellen einer Verbindung zum lokalen SQL Server im Vergleich zu Azure SQL-Datenbank in der Cloud benötigt. Die Unterschiede werden in Fettschrift angezeigt.


| Verbindungszeichenfolge für die <br/>Azure SQL-Datenbank | Verbindungszeichenfolge für <br/>Microsoft SQL Server |
| :-- | :-- |
| Server=**tcp:**{Ihr\_Servername}**.database.windows.net,1433**;<br/>User ID={Ihr\_Anmeldename}**@{Ihr\_Servername}**;<br/>Password={Ihr\_Kennwort};<br/>**Database={Ihr\_Datenbankname};**<br/>**Connection Timeout=30**;<br/>**Encrypt=True**;<br/>**TrustServerCertificate=False**; | Server={Ihr\_Servername};<br/>User ID={Ihr\_Anmeldename};<br/>Password={Ihr\_Kennwort}; |


**Datenbank =** ist für SQL Server optional, aber für SQL-Datenbank erforderlich.


[SqlConnectionStringBuilder-Eigenschaften](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder_properties.aspx) – erläutert alle Parameter ausführlich.


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->

<!---HONumber=Oct15_HO3-->