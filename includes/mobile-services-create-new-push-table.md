1.  Melden Sie sich beim [Azure-Verwaltungsportal][1] an, klicken Sie auf **Mobile Services**, und klicken Sie dann auf Ihre Anwendung.
    
    ![](./media/mobile-services-create-new-push-table/mobile-services-selection.png)

2.  Klicken Sie auf die Registerkarte **Daten** und dann auf **Erstellen**.
    
    ![](./media/mobile-services-create-new-push-table/mobile-create-table.png)
    
    Das Dialogfeld **Neue Tabelle erstellen** wird angezeigt.

3.  Behalten Sie die Standardeinstellung **Anybody with the application key** für alle Berechtigungen bei, geben Sie *Registrations* in das Feld **Tabellenname** ein, und klicken Sie dann auf den Häkchenknopf.
    
    ![](./media/mobile-services-create-new-push-table/mobile-create-registrations-table.png)

	Dadurch wird die Tabelle **Registrations** erstellt, in der die Channel-URIs gespeichert werden, über die Pushbenachrichtigungen gesendet werden.

Als Nächstes ändern Sie die App so, dass Pushbenachrichtigungen unterstützt werden.

<!-- URLs -->



[1]: https://manage.windowsazure.com/