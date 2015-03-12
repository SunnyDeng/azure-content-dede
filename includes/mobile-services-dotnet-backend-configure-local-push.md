
Sie können Push-Benachrichtigungen wahlweise testen, wobei der mobile Dienst auf dem lokalen Computer oder dem virtuellen Computer ausgeführt wird, bevor Sie sie in Azure veröffentlichen. Hierzu müssen Sie Informationen über den Benachrichtigungs-Hub, der von Ihrer App verwendet wird, in der Datei "web.config" angeben. Diese Informationen werden nur bei der lokalen Ausführung der App zum Herstellen einer Verbindung mit dem Benachrichtigungs-Hub verwendet. Sie wird bei der Veröffentlichung in Azure ignoriert.

1. Klicken Sie auf der Registerkarte **Push** des mobilen Dienstes auf den Link **Benachrichtigungs-Hub**.

	![](./media/mobile-services-dotnet-backend-configure-local-push/link-to-notification-hub.png)

	Damit gelangen Sie zum Benachrichtigungs-Hub, der von Ihrem mobilen Dienst verwendet wird.

2. Notieren Sie sich auf der Benachrichtigungs-Hub-Seite den Namen des Benachrichtigungs-Hubs, und klicken Sie dann auf **Verbindungszeichenfolge anzeigen**.

	![](./media/mobile-services-dotnet-backend-configure-local-push/notification-hub-page.png)

3. Kopieren Sie im Dialogfeld **Verbindungsinformationen aufrufen** die Verbindungszeichenfolge **DefaultFullSharedAccessSignature**.

	![](./media/mobile-services-dotnet-backend-configure-local-push/notification-hub-connection-string.png)

4. Öffnen Sie in Visual Studio die Datei "Web.config" für das mobile Dienstprojekt, und ersetzen Sie im Abschnitt **connectionStrings** die Verbindungszeichenfolge für **MS_NotificationHubConnectionString** durch die Verbindungszeichenfolge aus dem vorigen Schritt.

5. Ersetzen Sie in **appSettings** den Wert der Anwendungseinstellung **MS_NotificationHubName** durch den Namen des Benachrichtigungs-Hubs.

Ihr mobiles Dienstprojekt ist so konfiguriert, dass eine Verbindung mit dem Benachrichtigungs-Hub in Azure hergestellt wird, wenn es lokal ausgeführt wird. Beachten Sie, dass Sie den gleichen Benachrichtigungs-Hubnamen und die gleiche Verbindungszeichenfolge wie im Portal verwenden müssen, weil diese Web.config-Projekteinstellungen bei der Ausführung in Azure überschrieben werden.
<!--HONumber=45--> 
