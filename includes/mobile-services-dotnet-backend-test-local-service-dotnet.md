
Mit dem heruntergeladenen mobilen Dienstprojekt können Sie den neuen mobilen Dienst direkt auf Ihrem lokalen Computer oder virtuellen Computer ausführen. Auf diese Weise ist das Debuggen Ihres Dienstcodes ziemlich einfach, bevor Sie diesen in Azure veröffentlichen.

In diesem Abschnitt testen Sie Ihre neue App für den lokal ausgeführten mobilen Dienst.

1. Navigieren Sie zu dem Verzeichnis, in dem Sie die komprimierten Projektdateien gespeichert haben, erweitern Sie die Dateien auf Ihrem Computer, und öffnen Sie die Projektmappen-Datei in Visual Studio.

2. Drücken Sie auf die Taste **F5**, um das Projekt neu zu erstellen und den mobilen Dienst lokal zu starten.

	![](./media/mobile-services-dotnet-backend-test-local-service-dotnet/mobile-service-startup.png)

	Nachdem der mobile Dienst erfolgreich gestartet wurde, wird eine Webseite angezeigt.

3. Klicken Sie im Projektmappen-Explorer in Visual Studio mit der rechten Maustaste auf das Client-App-Projekt und anschließend auf **Als Startprojekt festlegen**, und drücken Sie dann auf die Taste **F5**, um das Projekt neu zu erstellen und die App zu starten.

	Daraufhin wird die App gestartet und mit der lokalen mobilen Dienstinstanz verbunden.	

4. Geben Sie in der App unter **TodoItem einfügen** einen sinnvollen Text ein, beispielsweise "_Tutorial beenden_", und klicken Sie dann auf **Speichern**.

	Daraufhin wird eine POST-Anforderung an den lokalen mobilen Dienst gesendet. Daten von der Anforderung werden in die TodoItem-Tabelle eingefügt. In der Tabelle gespeicherte Werte werden vom mobilen Dienst zurückgegeben, und die Daten werden in der zweiten Spalte der App angezeigt.

<!--HONumber=52-->
