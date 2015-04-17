

1. Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an, und klicken Sie im unteren Teil des Bildschirms auf **+NEW**.

2. Klicken Sie auf **App-Dienste**, **Service Bus**, **Notification Hub** und dann auf **Schnellerfassung**.

3. Geben Sie einen Namen für den Notification Hub ein. Wählen Sie die gewünschte Region, und klicken Sie dann auf **Neuen Notification Hub erstellen**.

   ![Festlegen von Eigenschaften für den Notification Hub](./media/notification-hubs-android-configure-push/notification-hub-create-from-portal2.png)

4. Klicken Sie auf den soeben erstellten Namespace (normalerweise ***Notification Hub-Name*-ns**), und klicken Sie dann oben auf **Konfigurieren**.

5. Klicken Sie oben auf die Registerkarte **Notification Hubs** und dann auf den soeben erstellen Notification Hub.

6. Klicken Sie oben auf die Registerkarte **Konfigurieren**, geben Sie unter **API-Schlüssel** den Wert aus dem vorherigen Abschnitt ein, und klicken Sie auf **Speichern**.

   ![Festlegen des GCM-API-Schlüssels auf der Registerkarte "Konfigurieren"](./media/notification-hubs-android-configure-push/notification-hub-configure-android.png)

7. Wählen Sie oben die Registerkarte **Dashboard** aus, und klicken Sie anschließend auf **Verbindungszeichenfolge anzeigen**. Notieren Sie sich die beiden Verbindungszeichenfolgen.

Der Notification Hub ist jetzt für die Arbeit mit GCM konfiguriert, und Sie besitzen die Verbindungszeichenfolge, um die App für den Empfang von Benachrichtigungen zu registrieren und Pushbenachrichtigungen zu versenden.

<!--HONumber=49-->