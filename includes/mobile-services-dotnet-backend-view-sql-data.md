
Der letzte optionale Schritt dieses Lernprogramms besteht darin, die mit dem mobilen Dienst verknüpfte Datenbank und die darin gespeicherten Daten zu überprüfen. 

1. Klicken Sie im Azure-Verwaltungsportal auf die Option zum Verwalten der Datenbank, die mit Ihrem mobilen Dienst verknüpft ist.
 
	![sign-in to manage SQL Database](./media/mobile-services-dotnet-backend-view-sql-data/manage-sql-azure-database.png)

2. Führen Sie im Verwaltungsportal eine Abfrage aus, um die von der Windows Store-App vorgenommenen Änderungen anzuzeigen. Ihre Abfrage ähnelt der folgenden Abfrage, statt "<code>todolist</code>" wird jedoch der Datenbankname verwendet.</p>

        SELECT * FROM [todolist].[todoitems]

    ![query SQL Database for stored items](./media/mobile-services-dotnet-backend-view-sql-data/sql-azure-query.png)

	Beachten Sie, dass die Tabelle die Spalten "Id", "__createdAt", "__updatedAt" und "__version" enthält. Diese Spalten unterstützen die Synchronisierung von Offlinedaten und sind in der Basisklasse [EntityData](http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.entitydata.aspx) implementiert. Weitere Informationen finden Sie unter [Erste Schritte mit der Synchronisierung von Offlinedaten].


<!--HONumber=42-->
