<!--
includes/sql-database-include-connection-string-20-portalshots.md

Latest Freshness check:  2015-09-02 , GeneMi.

## Connection string
-->


### Abrufen der Verbindungszeichenfolge aus dem Azure-Portal


Nutzen Sie das [Azure-Vorschauportal](https://portal.azure.com/) zum Abrufen der Verbindungszeichenfolge, die für die Interaktion des Clientprogramms mit der Azure SQL-Datenbank erforderlich ist:


1. Klicken auf **DURCHSUCHEN** > **SQL-Datenbanken**.

    ![SQL auswählen][1-select-sql]

2. Geben Sie in das Textfeld "Filter" nahe der oberen linken Ecke des Blattes **SQL-Datenbanken** den Namen der Datenbank ein.

    ![Datenbank auswählen][2-select-database]]

3. Klicken Sie auf die Zeile für die Datenbank.

4. Nachdem das Blatt für die Datenbank angezeigt wird, können Sie der visuellen Einfachheit halber auf die Standardsteuerelemente zum Minimieren klicken, um die Blätter auszublenden, die Sie zum Durchsuchen und Filtern verwendet haben.

5. Klicken Sie auf dem Blatt für die Datenbank auf **Datenbankverbindungszeichenfolgen anzeigen**.

6. Wenn Sie die JDBC-Verbindungsbibliothek verwenden möchten, kopieren Sie die Zeichenfolge mit der Bezeichnung **JDBC**.

	![JDBC-Verbindungszeichenfolge für die Datenbank kopieren][3-get-connection-string]

7. Fügen Sie die Informationen der Verbindungszeichenfolge in den Code der Clientanwendung ein. Ersetzen Sie {your\_password\_here} durch Ihr tatsächliches Kennwort.



Weitere Informationen finden Sie unter:<br/>[Verbindungszeichenfolgen und Konfigurationsdateien](https://msdn.microsoft.com/library/ms378428.aspx).



<!-- Image references. -->

[1-select-sql]: ./media/sql-database-include-connection-string-20-portalshots/connection-string-select-sql.png


[2-select-database]: ./media/sql-database-include-connection-string-20-portalshots/connection-string-select-database.PNG

[3-get-connection-string]: ./media/sql-database-include-connection-string-20-portalshots/connection-string-jdbc.PNG


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->

<!---HONumber=AcomDC_0128_2016-->