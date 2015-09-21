
<!--
includes/sql-database-include-connection-string-20-portalshots.md

Latest Freshness check:  2015-09-02 , GeneMi.

## Connection string
-->


### Abrufen der Verbindungszeichenfolge aus dem Azure-Portal


Nutzen Sie das [Azure-Vorschauportal](http://portal.azure.com/) zum Abrufen der Verbindungszeichenfolge, die für die Interaktion des Clientprogramms mit der Azure SQL-Datenbank erforderlich ist:


1. Klicken auf **DURCHSUCHEN** > **SQL-Datenbanken**.

2. Geben Sie in das Textfeld "Filter" nahe der oberen linken Ecke des Blattes **SQL-Datenbanken** den Namen der Datenbank ein.

3. Klicken Sie auf die Zeile für die Datenbank.

4. Nachdem das Blatt für die Datenbank angezeigt wird, können Sie der visuellen Einfachheit halber auf die Standardsteuerelemente zum Minimieren klicken, um die Blätter auszublenden, die Sie zum Durchsuchen und Filtern verwendet haben.
 
	![Filtern zum Isolieren der Datenbank][10-FilterDatabase]

5. Klicken Sie auf dem Blatt für die Datenbank auf **Datenbankverbindungszeichenfolgen anzeigen**.

6. Wenn Sie die ADO.NET-Verbindungsbibliothek verwenden wollen, kopieren Sie die Zeichenfolge mit der Bezeichnung **ADO**.
 
	![Kopieren der ADO-Verbindungszeichenfolge für die Datenbank][20-CopyAdoConnectionString]
 
7. Fügen Sie in dem ein oder anderen Format die Informationen der Verbindungszeichenfolge in den Clientcode für die Anwendung ein.



Weitere Informationen finden Sie unter:<br/>[Verbindungszeichenfolgen und Konfigurationsdateien](http://msdn.microsoft.com/library/ms254494.aspx).



<!-- Image references. -->

[10-FilterDatabase]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-a.png

[20-CopyAdoConnectionString]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-b.png


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->

<!---HONumber=Sept15_HO2-->