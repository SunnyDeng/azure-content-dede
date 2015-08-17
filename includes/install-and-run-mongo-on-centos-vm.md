Diese schrittweise Anleitung erläutert die Installation und Ausführung von MongoDB auf einem virtuellen Computer, der CentOS Linux ausführt.

> [AZURE.WARNING]MongoDB-Sicherheitsfunktionen wie Authentifizierung und IP-Adressen-Anbindung werden nicht standardmäßig aktiviert. Die Sicherheitsfunktionen sollten aktiviert werden, bevor MongoDB in einer Produktionsumgebung eingesetzt wird. Weitere Informationen finden Sie unter [Sicherheit und Authentifizierung](http://www.mongodb.org/display/DOCS/Security+and+Authentication).

1. Konfigurieren Sie das Package Management System (YUM) so, dass Sie MongoDB installieren können. Erstellen Sie eine Datei */etc/yum.repos.d/10gen.repo* mit Informationen über das Repository, und fügen Sie Folgendes hinzu:

		[10gen]
		name=10gen Repository
		baseurl=http://downloads-distro.mongodb.org/repo/redhat/os/x86_64
		gpgcheck=0
		enabled=1

2. Speichern Sie die repo-Datei und führen Sie dann den folgenden Befehl aus, um die lokale Paketdatenbank zu aktualisieren:

		$ sudo yum update

3. Führen Sie zur Installation des Pakets den folgenden Befehl aus. Hiermit installieren Sie die aktuelle stabile Version von MongoDB und die zugehörigen Werkzeuge:

		$ sudo yum install mongo-10gen mongo-10gen-server

	Warten Sie, während MongoDB heruntergeladen und installiert wird.

4. Erstellen Sie ein Datenverzeichnis. Standardmäßig speichert MongoDB Daten im Verzeichnis */data/db*, das Sie allerdings erst erstellen müssen. Führen Sie zum Erstellen aus:

		$ sudo mkdir -p /srv/datadrive/data
		$ sudo chown `id -u` /srv/datadrive/data

	Weitere Informationen zur Installation von MongoDB auf Linux finden Sie unter [Quickstart Unix][QuickstartUnix] (in englischer Sprache).

5. Führen Sie zum Starten der Datenbank aus:

		$ mongod --dbpath /srv/datadrive/data --logpath /srv/datadrive/data/mongod.log

	Alle Protokollmeldungen werden zur Datei */srv/datadrive/data/mongod.log* geleitet, wenn der MongoDB-Server startet und Journaldateien reserviert. Es kann einige Minuten dauern, bis MongoDB die Journaldateien reserviert hat und mit dem Lauschen auf Verbindungen beginnt.

6. Starten Sie die administrative Shell von MongoDB, indem Sie ein eigenes SSH- oder PuTTY-Fenster öffnen und das Folgende ausführen:

		$ mongo
		> db.foo.save ( { a:1 } )
		> db.foo.find()
		{ _id : ..., a : 1 }
		> show dbs  
		...
		> show collections  
		...  
		> help  

	Die Datenbank wird vom Insert erstellt.

7. Wenn MongoDB installiert ist, müssen Sie einen Endpunkt konfigurieren, damit ein Fernzugriff auf MongoDB möglich ist. Klicken Sie im Verwaltungsportal auf **Virtueller Computer** und anschließend auf den Namen Ihres neuen virtuellen Computers sowie auf **Endpunkte**.
	
	![Endpunkte][Image7]

8. Klicken Sie am unteren Seitenrand auf **Endpunkt hinzufügen**.
	
	![Endpunkte][Image8]

9. Fügen Sie einen Endpunkt mit den folgenden Einstellungen hinzu:

 - **Name**: Mongo
 - **Protokoll**: TCP
 - **Öffentlicher Port**: 27017
 - **Privater Port**: 27017
 
 Dies ermöglicht einen Fernzugriff auf MongoDB.



[QuickStartUnix]: http://www.mongodb.org/display/DOCS/Quickstart+Unix


[Image7]: ./media/install-and-run-mongo-on-centos-vm/LinuxVmAddEndpoint.png
[Image8]: ./media/install-and-run-mongo-on-centos-vm/LinuxVmAddEndpoint2.png

<!---HONumber=August15_HO6-->