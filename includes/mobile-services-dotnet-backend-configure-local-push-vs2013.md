Sie können Push-Benachrichtigungen wahlweise testen, wobei der mobile Dienst auf dem lokalen Computer oder dem virtuellen Computer ausgeführt wird, bevor Sie sie in Azure veröffentlichen. Hierzu müssen Sie Informationen über den Benachrichtigungs-Hub, der von Ihrer App verwendet wird, in der Datei "web.config" angeben. Diese Informationen werden nur bei der lokalen Ausführung der App zum Herstellen einer Verbindung mit dem Benachrichtigungs-Hub verwendet. Sie wird bei der Veröffentlichung in Azure ignoriert.

1.  Öffnen Sie die Datei "readme.html" im Projektordner der Windows- oder der Windows Phone-App.

    Daraufhin wird die Seite **Push setup is almost complete** geöffnet, falls sie nicht noch geöffnet ist. Der Abschnitt **Step 3: Modify Web Config** enthält die Verbindungsinformationen für den Benachrichtigungs-Hub.

2.  Öffnen Sie in Visual Studio die Datei "Web.config" für das mobile Dienstprojekt, und fügen Sie im Abschnitt **connectionStrings** unter **MS\_NotificationHubConnectionString** die auf der Seite **Push setup is almost complete** angegebene Verbindungszeichenfolge ein.

3.  Ersetzen Sie unter **appSettings** den Wert der Anwendungseinstellung **MS\_NotificationHubName** durch den auf der Seite **Push setup is almost complete** angegebenen Namen des Benachrichtigungs-Hubs.

4.  Klicken Sie mit der rechten Maustaste auf das mobile Dienstprojekt. Klicken Sie auf **Debuggen** und dann auf **Neue Instanz starten**, und notieren Sie die Stamm-URL des Diensts, die auf der Startseite im Browser angezeigt wird.

    Dies ist die URL des lokalen Computers für das .NET-Backendprojekt. Diese URL wird benötigt, um die App mit dem mobilen Dienst zu testen, der auf dem lokalen Computer ausgeführt wird.

Ihr mobiles Dienstprojekt ist so konfiguriert, dass eine Verbindung mit dem Benachrichtigungs-Hub in Azure hergestellt wird, wenn es lokal ausgeführt wird. Beachten Sie, dass Sie den gleichen Benachrichtigungs-Hubnamen und die gleiche Verbindungszeichenfolge wie im Portal verwenden müssen, weil diese Projekteinstellungen in der Datei "Web.config" bei der Ausführung in Azure mit den Portaleinstellungen überschrieben werden.

