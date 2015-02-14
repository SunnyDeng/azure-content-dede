


Der letzte Schritt dieses Lernprogramms besteht im Erstellen und Ausführen der neuen App.

1. Navigieren Sie zu dem Verzeichnis, in dem Sie die komprimierten Projektdateien gespeichert haben, erweitern Sie die Dateien auf Ihrem Computer, und öffnen Sie die Projektdatei mithilfe von Xcode.

   	![](./media/mobile-services-ios-run-app/mobile-xcode-project.png)

2. Klicken Sie gemäß dem Standard für dieses Projekt auf die Schaltfläche **Ausführen**, um das Projekt zu erstellen und starten Sie die App im iPhone-Emulator.

3. Geben Sie in der App einen sinnvollen Text, z. B. _Tutorial beenden_ ein, und klicken Sie dann auf das Plus-(**+**)-Zeichen.

   	![](./media/mobile-services-ios-run-app/mobile-quickstart-startup-ios.png)

   	Dadurch wird eine POST-Anforderung an den neuen, in Azure gehosteten mobilen Dienst gesendet. Daten von der Anforderung werden in die TodoItem-Tabelle eingefügt. In der Tabelle gespeicherte Einträge werden von dem mobilen Dienst zurückgegeben, und die Daten werden in der Liste angezeigt.

	>[AZURE.NOTE]Sie können den Code überprüfen, der auf Ihren mobilen Dienst zugreift, um Daten abzufragen und einzufügen. Der Code befindet sich in der Datei "TodoService.m".</p> 
 	</div>

<!--HONumber=42-->
