In diesem Abschnitt fügen Sie Ihrem Verzeichnis zwei neue Benutzer und die neue Gruppe "Sales" hinzu. Nur einem der Benutzer wird die Mitgliedschaft in der Gruppe "Sales" gewährt. Der andere Benutzer wird nicht als Mitglied in diese Gruppe aufgenommen.

### Die Benutzer erstellen

1.  Navigieren Sie im [Azure-Verwaltungsportal] zu dem Verzeichnis, das Sie zuvor zur Authentifizierung konfiguriert haben, als Sie das Lernprogramm [Erste Schritte mit Authentifizierung] bearbeitet haben.
2.  Klicken Sie am oberen Seitenrand auf **Benutzer**. Klicken Sie dann am unteren Rand auf die Schaltfläche **Benutzer hinzufügen**.
3.  Füllen Sie die Dialogfelder zum Erstellen des neuen Benutzers aus, um einen Benutzer namens **Bob** zu erstellen. Notieren Sie sich das temporäre Kennwort für den Benutzer.
4.  Erstellen Sie einen weiteren Benutzer namens **Dave**. Notieren Sie sich das temporäre Kennwort für den Benutzer.
5.  Die neuen Benutzer sollten dem Beispiel unten ähneln.

    ![][0]

### Die Gruppe "Sales" erstellen

1.  Klicken Sie am oberen Seitenrand der Verzeichnisseite auf **Gruppen**. Anschließend klicken Sie unten auf **Gruppe hinzufügen**.
2.  Geben Sie **Sales** als Namen der Gruppe ein, und klicken Sie im Dialogfeld auf die Schaltfläche zum Fertigstellen, um die Gruppe zu erstellen.

    ![][1]

### Der Gruppe "Sales" Benutzermitgliedschaften hinzufügen

1.  Klicken Sie am oberen Rand der Verzeichnisseite auf **Gruppen**. Klicken Sie auf die Gruppe **Sales**, um zur Seite dieser Gruppe zu wechseln.
2.  Klicken Sie auf der Seite der Gruppe "Sales" auf **Mitglieder hinzufügen**. Fügen Sie der Gruppe "Sales" den Benutzer namens **Bob** hinzu. Der Benutzer **Dave** soll nicht Mitglied dieser Gruppe sein.

    ![][2]

3.  Klicken Sie auf der Seite der Gruppe "Sales" auf **Konfigurieren**, und notieren Sie sich die **Objekt-ID** der Gruppe "Sales". In diesem Lernprogramm wird die Objekt-ID der Gruppe mit den Grafik-APIs nachgeschlagen. Sie brauchen die ID nicht. Allerdings ist es manchmal besser, nicht den Gruppennamen zu verwenden, weil er sich ändern kann. Die ID bleibt dagegen immer gleich. Wenn Sie die Gruppen-ID als App-Einstellung im mobilen Service speichern oder im Code fest codieren möchten, dann finden Sie sie hier.

    ![][3]

  [0]: ./media/mobile-services-aad-rbac-create-sales-group/users.png
  [1]: ./media/mobile-services-aad-rbac-create-sales-group/sales-group.png
  [2]: ./media/mobile-services-aad-rbac-create-sales-group/group-membership.png
  [3]: ./media/mobile-services-aad-rbac-create-sales-group/sales-group-id.png
