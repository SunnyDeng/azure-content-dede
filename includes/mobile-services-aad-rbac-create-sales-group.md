In diesem Abschnitt fügen Sie Ihrem Verzeichnis zwei neue Benutzer und die neue Gruppe "Sales" hinzu. Nur einem der Benutzer wird die Mitgliedschaft in der Gruppe "Sales" gewährt. Der andere Benutzer wird nicht als Mitglied in diese Gruppe aufgenommen.

### Die Benutzer erstellen


1. Navigieren Sie im [Azure-Verwaltungsportal] zu dem Verzeichnis, das Sie zuvor zur Authentifizierung konfiguriert haben, als Sie das Lernprogramm zum Hinzufügen von Authentifizierung zu Ihrer App bearbeitet haben.
2. Klicken Sie am oberen Seitenrand auf **Benutzer**. Klicken Sie dann am unteren Rand auf die Schaltfläche **Benutzer hinzufügen**. 
3. Füllen Sie die Dialogfelder zum Erstellen des neuen Benutzers aus, um einen Benutzer namens **Bob** zu erstellen. Notieren Sie sich das temporäre Kennwort für den Benutzer. 
4. Erstellen Sie einen weiteren Benutzer namens **Dave**. Notieren Sie sich das temporäre Kennwort für den Benutzer.
5. Die neuen Benutzer sollten dem Beispiel unten ähneln.

    ![](./media/mobile-services-aad-rbac-create-sales-group/users.png)


### Die Gruppe "Sales" erstellen


1. Klicken Sie am oberen Seitenrand der Verzeichnisseite auf **Gruppen**. Anschließend klicken Sie unten auf **Gruppe hinzufügen**. 
2. Geben Sie **Sales** als Namen der Gruppe ein, und klicken Sie im Dialogfeld auf die Schaltfläche zum Fertigstellen, um die Gruppe zu erstellen. 

    ![](./media/mobile-services-aad-rbac-create-sales-group/sales-group.png)

### Der Gruppe "Sales" Benutzermitgliedschaften hinzufügen


1. Klicken Sie am oberen Rand der Verzeichnisseite auf **Gruppen**. Klicken Sie auf die Gruppe **Sales**, um zur Seite dieser Gruppe zu wechseln. 
2. Klicken Sie auf der Seite der Gruppe "Sales" auf **Mitglieder hinzufügen**. Fügen Sie der Gruppe "Sales" den Benutzer namens **Bob** hinzu. Der Benutzer **Dave** soll nicht Mitglied dieser Gruppe sein.

    ![](./media/mobile-services-aad-rbac-create-sales-group/group-membership.png)

3. Klicken Sie auf der Seite der Gruppe "Sales" auf **Eigenschaften**, und kopieren Sie die **Objekt-ID** der Gruppe "Sales" unten auf der Seite.

   
    ![](./media/mobile-services-aad-rbac-create-sales-group/sales-group-id.png)

4. Navigieren Sie zurück zu Konfigurationsseite von Mobile Services, und fügen Sie die Objekt-ID als App-Einstellung mit dem Namen **AAD\\_SALES\\_GROUP\\_ID** hinzu. In diesem Lernprogramm wird die Objekt-ID der Gruppe als App-Einstellung verwendet, sodass die ID nicht anhand des Gruppennamens gesucht werden muss. Der Gruppenname kann sich ändern, die ID bleibt dagegen immer gleich.

    ![](./media/mobile-services-aad-rbac-create-sales-group/sales-group-id-app-setting.png)

<!---HONumber=July15_HO2-->