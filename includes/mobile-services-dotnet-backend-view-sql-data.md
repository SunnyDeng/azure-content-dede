Der letzte optionale Schritt dieses Lernprogramms besteht darin, die mit dem mobilen Dienst verknüpfte Datenbank und die darin gespeicherten Daten zu überprüfen.

1.  Klicken Sie im Azure-Verwaltungsportal auf die Option zum Verwalten der Datenbank, die mit Ihrem mobilen Dienst verknüpft ist.

    ![Anmelden zum Verwalten der SQL-Datenbank][Anmelden zum Verwalten der SQL-Datenbank]

2.  Führen Sie im Verwaltungsportal eine Abfrage aus, um die von der Windows Store-App vorgenommenen Änderungen anzuzeigen. Ihre Abfrage sieht wie folgt aus, nur dass Sie Ihren Datenbanknamen anstelle von `todolist` verwenden.

    </p>
        SELECT * FROM [todolist].[todoitems]

    ![gespeicherte Elemente der SQL-Datenbank abfragen][gespeicherte Elemente der SQL-Datenbank abfragen]

    Beachten Sie, dass die Tabelle die Spalten "Id", "\_\_createdAt", "\_\_updatedAt" und "\_\_version" enthält. Diese Spalten ermöglichen die Synchronisierung von Offlinedaten und sind in der Basisklasse [EntityData][EntityData] implementiert. Weitere Informationen finden Sie unter [Erste Schritte mit der Synchronisierung von Offlinedaten].

  [Anmelden zum Verwalten der SQL-Datenbank]: ./media/mobile-services-dotnet-backend-view-sql-data/manage-sql-azure-database.png
  [gespeicherte Elemente der SQL-Datenbank abfragen]: ./media/mobile-services-dotnet-backend-view-sql-data/sql-azure-query.png
  [EntityData]: http://msdn.microsoft.com/de-de/library/microsoft.windowsazure.mobile.service.entitydata.aspx
