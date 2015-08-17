
Der letzte optionale Schritt dieses Lernprogramms besteht darin, die mit dem mobilen Dienst verknüpfte Datenbank und die darin gespeicherten Daten zu überprüfen.

1. Klicken Sie im Azure-Verwaltungsportal auf die Option zum Verwalten der Datenbank, die mit Ihrem mobilen Dienst verknüpft ist.
 
	![Anmelden zum Verwalten der SQL-Datenbank](./media/mobile-services-dotnet-backend-view-sql-data/manage-sql-azure-database.png)

2. Führen Sie im Verwaltungsportal eine Abfrage aus, um die von der Windows Store-App vorgenommenen Änderungen anzuzeigen. Ihre Abfrage sieht in etwa wie folgt aus, nur dass Sie Ihren Datenbanknamen statt <code>todolist</code> verwenden.</p>

        SELECT * FROM [todolist].[todoitems]

    ![gespeicherte Elemente der SQL-Datenbank abfragen](./media/mobile-services-dotnet-backend-view-sql-data/sql-azure-query.png)

	Beachten Sie, dass die Tabelle die Spalten "Id", "__createdAt", "__updatedAt" und "__version" enthält. Diese Spalten ermöglichen die Synchronisierung von Offlinedaten und sind in der Basisklasse [EntityData](http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.entitydata.aspx) implementiert. Weitere Informationen finden Sie unter [Erste Schritte mit der Synchronisierung von Offlinedaten].

<!---HONumber=August15_HO6-->