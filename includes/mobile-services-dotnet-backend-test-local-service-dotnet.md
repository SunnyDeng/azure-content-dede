
Mit dem heruntergeladenen mobilen Dienstprojekt können Sie den neuen mobilen Dienst direkt auf Ihrem lokalen Computer oder virtuellen Computer ausführen. Auf diese Weise können Sie den Dienstcode einfach degbuggen, bevor Sie ihn in Azure veröffentlichen.

In diesem Abschnitt testen Sie Ihre neue App für den lokal ausgeführten mobilen Dienst.

1. Navigieren Sie zu dem Verzeichnis, in dem Sie die komprimierten Projektdateien gespeichert haben, erweitern Sie die Dateien auf Ihrem Computer, und öffnen Sie die Projektmappen-Datei in Visual Studio.

2. Drücken Sie die Taste **F5**, um das Projekt neu zu erstellen und die App zu starten.

	![](./media/mobile-services-dotnet-backend-test-local-service-dotnet/mobile-service-startup.png)

	Nachdem der mobile Dienst erfolgreich gestartet wurde, wird eine Webseite angezeigt.

3. Klicken Sie im Projektmappen-Explorer in Visual Studio mit der rechten Maustaste auf das Client-App-Projekt, klicken Sie anschließend auf **Als Startprojekt festlegen**, und drücken Sie dann die Taste **F5**, um das Projekt neu zu erstellen und die App zu starten.

	Daraufhin wird die App gestartet und mit der lokalen mobilen Dienstinstanz verbunden.	

4. Geben Sie in der App in **Insert a TodoItem** einen sinnvollen Text ein, beispielsweise _Lernprogramm beenden_, und klicken Sie dann auf **Speichern**.

	Daraufhin wird eine POST-Anforderung an den lokalen mobilen Dienst gesendet. Daten von der Anforderung werden in die TodoItem-Tabelle eingefügt. In der Tabelle gespeicherte Werte werden vom mobilen Dienst zurückgegeben, und die Daten werden in der zweiten Spalte der App angezeigt.
