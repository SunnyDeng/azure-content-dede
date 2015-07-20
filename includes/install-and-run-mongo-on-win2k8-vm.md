Diese schrittweise Anleitung erläutert die Installation und Ausführung von MongoDB auf einem virtuellen Computer, der Windows Server ausführt.

> [AZURE.IMPORTANT]MongoDB-Sicherheitsfunktionen wie Authentifizierung und IP-Adressen-Anbindung werden nicht standardmäßig aktiviert. Die Sicherheitsfunktionen sollten aktiviert werden, bevor MongoDB in einer Produktionsumgebung eingesetzt wird. Weitere Informationen finden Sie unter [Sicherheit und Authentifizierung](http://www.mongodb.org/display/DOCS/Security+and+Authentication).

1. Wenn Sie über Remote Desktop eine Verbindung zum virtuellen Computer aufgebaut haben, öffnen Sie im Menü **Start** den Internet Explorer.
2. Klicken Sie in der oberen rechten Ecke auf **Extras**. Öffnen Sie unter **Internetoptionen** die Registerkarte **Sicherheit**, wählen Sie anschließend das Symbol **Vertrauenswürdige Sites**, und klicken Sie anschließend auf die Schaltfläche **Sites**. Fügen Sie der Liste vertrauenswürdiger Websites *http://*.mongodb.org* hinzu.3. Gehen Sie zu [Downloads - MongoDB][MongoDownloads].
4. Suchen Sie die neueste Version im Abschnitt **Production Release (Recommended)**, und klicken Sie in der Spalte "Windows 64-Bit" auf den Link ***2008+**. Klicken Sie auf **Speichern unter**, und speichern Sie die Zip-Datei auf dem Desktop.
5. Klicken Sie mit der rechten Maustaste auf die Zip-Datei und anschließend auf **Alle extrahieren...** Geben Sie als Ziel "C:" an und klicken Sie auf **Extrahieren**. Wenn die Dateien extrahiert wurden, können Sie den Installationsordner umbenennen und ihm einen einfacheren Namen geben, z. B. "MongoDB".
6. Erstellen Sie die Daten- und Protokollverzeichnisse für MongoDB auf dem Datenträger (z. B. Laufwerk **F:**), den Sie in den vorherigen Schritten erstellt haben. Wählen Sie unter **Start** die Option **Eingabeaufforderung**, um ein Eingabeaufforderungsfenster zu öffnen. Geben Sie Folgendes ein:

		C:> F:
		F:> mkdir \MongoData
		F:> mkdir \MongoLogs

7. Geben Sie Folgendes rein, um die Datenbank auszuführen:

		F:> C:
		C:> cd \MongoDB\bin
		C:\my_mongo_dir\bin> mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log

	Alle Protokollmeldungen werden in die Datei *F:\\MongoLogs\\mongolog.log* geleitet, sobald der Server mongod.exe startet und die Journaldateien reserviert. Es kann einige Minuten dauern, bis MongoDB die Journaldateien reserviert hat und mit dem Lauschen auf Verbindungen beginnt.

8. Starten Sie die MongoDB-Administrator-Shell, indem Sie unter **Start** ein weiteres Befehlsfenster öffnen und Folgendes eingeben:

		C:> cd \my_mongo_dir\bin  
		C:\my_mongo_dir\bin> mongo  
		>db  
		test  	  
		> db.foo.insert( { a : 1 } )  
		> db.foo.find()  
		{ _id : ..., a : 1 }  
		> show dbs  
		...  
		> show collections  
		...  
		> help  

	Die Datenbank wird vom Insert erstellt.
9. (Optional) mongod.exe unterstützt die Installation und Ausführung als Windows-Dienst. Geben Sie in der Befehlszeile Folgendes ein, um mongod.exe als Dienst auszuführen:

		C:\mongodb\bin>mongod --logpath "c:\mongodb\logs\logfile.log" --logappend --dbpath "c:\data" --install 

	Dadurch wird ein Dienst namens "Mongo DB" mit der Beschreibung "Mongo DB" erstellt. Zur Festlegung einer Protokolldatei muss die Option **--logpath** verwendet werden, da der laufende Dienst kein Befehlsfenster zur Anzeige der Ausgabe hat. Die Option **--logpath** legt fest, dass ein Neustart des Diensts eine Ausgabe erzeugt, die an die vorhandene Protokolldatei angehängt wird. Die Option **--dbpath** gibt den Ort des Datenverzeichnisses an. Weitere dienstbezogene Befehlszeilenoptionen finden Sie unter [Service-related command line options][MongoWindowsSvcOptions] (Dienstbezogene Befehlszeilenoptionen, in englischer Sprache).
10. Nach der Installation und Ausführung von MongoDB müssen Sie einen Port in Windows Firewall öffnen, um eine Remote-Verbindung mit MongoDB herzustellen. Wählen Sie im Menü **Start** die Option **Administratortools** und anschließend **Windows-Firewall mit erweiterter Sicherheit**. 

11. Klicken Sie im linken Bereich auf **Eingehende Regeln**. Wählen Sie rechts im Bereich **Aktionen** die Option **Neue Regel...**.
	
	![Windows-Firewall][Image1]

	Klicken Sie unter **Assistent für neue eingehende Regel** auf **Port** und anschließend auf **Weiter**.
	
	![Windows-Firewall][Image2]

	Wählen Sie **TCP** und anschließend **Bestimmte lokale Ports**. Geben Sie als Port "27017" ein (diesen Port verwendet MongoDB als Überwachungsport), und klicken Sie auf **Weiter**.

	![Windows-Firewall][Image3]

	Klicken Sie auf **Verbindung zulassen** und anschließend auf **Weiter**.

	![Windows-Firewall][Image4]

	Klicken Sie erneut auf **Weiter**.
	
	![Windows-Firewall][Image5]

	Geben Sie einen Namen für die Regel an, z. B. "MongoPort", und klicken Sie auf **Fertig stellen**.

	![Windows-Firewall][Image6]
	
12. Wenn Sie beim Erstellen des virtuellen Computers keinen Endpunkt für MongoDB konfiguriert haben, können Sie dies jetzt tun. Sie benötigen sowohl die Firewall-Regel als auch den Endpunkt, um eine Remote-Verbindung zu MongoDB herstellen zu können. Klicken Sie im Verwaltungsportal auf **Virtuelle Computer**, klicken Sie auf den Namen des neuen virtuellen Computers und danach auf **Endpunkte**.

	![Endpunkte][Image7]
13. Klicken Sie am unteren Seitenrand auf **Endpunkt hinzufügen**. Wählen Sie **Endpunkt hinzufügen**, und klicken Sie auf **Weiter**.
	
	![Endpunkte][Image8]

14. Fügen Sie einen Endpunkt mit dem Namen "Mongo" hinzu, wählen Sie als Protokoll **TCP**, und setzen Sie die beiden Ports **Öffentlich** und **Privat** auf "27017". Dies ermöglicht einen Fernzugriff auf MongoDB.

	![Endpunkte][Image9]

[MongoDownloads]: http://www.mongodb.org/downloads

[MongoWindowsSvcOptions]: http://www.mongodb.org/display/DOCS/Windows+Service


[Image1]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall1.png
[Image2]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall2.png
[Image3]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall3.png
[Image4]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall4.png
[Image5]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall5.png
[Image6]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall6.png
[Image7]: ./media/install-and-run-mongo-on-win2k8-vm/WinVmAddEndpoint.png
[Image8]: ./media/install-and-run-mongo-on-win2k8-vm/WinVmAddEndpoint2.png
[Image9]: ./media/install-and-run-mongo-on-win2k8-vm/WinVmAddEndpoint3.png

<!---HONumber=July15_HO2-->