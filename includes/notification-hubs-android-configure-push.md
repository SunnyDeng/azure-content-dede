

1. Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an, und klicken Sie im unteren Teil des Bildschirms auf **+NEW**.

2. Klicken Sie auf **App-Dienste**, **Service Bus**, **Notification Hub** und zuletzt auf **Schnellerfassung**.

3. Geben Sie einen Namen für den Notification Hub ein. Wählen Sie die gewünschte Region, und klicken Sie dann auf **Create a new Notification Hub**.

   	![Festlegen von Eigenschaften für den Notification Hub](./media/notification-hubs-android-configure-push/notification-hub-create-from-portal2.png)

4. Klicken Sie unter **Service Bus** auf den soeben erstellten Namespace (in der Regel ***Name des Notification Hubs*-ns**).

5. Klicken Sie in Ihrem Namespace oben auf die Registerkarte **Notification Hubs** und dann auf den soeben erstellen Notification Hub.

6. Klicken Sie auf die Registerkarte **Configure**, geben Sie den Wert **API Key** ein, den Sie im vorherigen Abschnitt erhalten haben, und klicken Sie auf **Save**.

   	![Festlegen des GCM-API-Schlüssels auf der Registerkarte "Configure"](./media/notification-hubs-android-configure-push/notification-hub-configure-android.png)

7. Wählen Sie oben die Registerkarte **Dashboard** aus, und klicken Sie anschließend auf **View Connection String**. Notieren Sie sich die beiden Verbindungszeichenfolgen.

Der Notification Hub ist jetzt für die Arbeit mit GCM konfiguriert, und Sie besitzen die Verbindungszeichenfolge, um die App für den Empfang von Benachrichtigungen zu registrieren und Pushbenachrichtigungen zu versenden.

<!---HONumber=July15_HO2-->