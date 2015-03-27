<properties 
	pageTitle="Ruby on Rails-Web-App auf Azure mithilfe eines virtuellen Linux-Computers" 
	description="Hosten Sie mithilfe eines virtuellen Linux-Computers eine Ruby on Rails-basierte Website auf Azure." 
	services="virtual-machines" 
	documentationCenter="ruby" 
	authors="wpickett" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="web" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="02/19/2015" 
	ms.author="wpickett"/>





# Ruby on Rails-Webanwendung auf Azure VM

In diesem Lernprogramm erfahren Sie, wie Sie eine Ruby on Rails-basierte Website auf Azure mit einem virtuellen Linux-Computer hosten. Bei diesem Lernprogramm wird davon ausgegangen, dass Sie noch keine Erfahrung mit der Verwendung von Azure haben. Nach Abschluss dieses Lernprogramms verfügen Sie über eine Ruby on Rails-basierte Anwendung, die in der Cloud ausgeführt wird.

Sie erhalten Informationen zu folgenden Themen:

* Einrichten der Entwicklungsumgebung

* Einrichten eines virtuellen Azure-Computers zum Hosten von Ruby on Rails

* Erstellen einer neuen Rails-Anwendung

* Kopieren von Dateien auf den virtuellen Computer mit SCP 

Nachfolgend sehen Sie einen Screenshot der fertigen Anwendung:

![a browser displaying Listing Posts][blog-rails-cloud]


## <a id="setup"></a>Einrichten der Entwicklungsumgebung

1. Installieren Sie Ruby in Ihrer Entwicklungsumgebung. Je nach Betriebssystem können die Schritte unterschiedlich sein.

	* **Apple OS X** - es gibt verschiedene Ruby-Distributionen für OS X. In diesem Lernprogramm wurde auf OS X mit überprüft [Homebrew](http://brew.sh/) installieren **Rbenv** und **Ruby-Build**. Informationen zur Installation finden Sie unter [https://github.com/sstephenson/rbenv/](https://github.com/sstephenson/rbenv/).

	* **Linux** - Verwenden Sie Ihr Verwaltungssystem für Verteilungspakete. Dieses Lernprogramm wurde auf Ubuntu 12.10 mit den Paketen ruby1.9.1 und ruby1.9.1-dev getestet.

	* **Windows** - es gibt verschiedene Ruby-Distributionen für Windows. Dieses Lernprogramm wurde mit [RailsInstaller](http://railsinstaller.org/) 1.9.3-p392 getestet.

2. Öffnen Sie eine neue Befehlszeile oder Terminalsitzung, und geben Sie folgenden Befehl ein, um Ruby on Rails zu installieren:

		gem install rails --no-rdoc --no-ri

	> [AZURE.NOTE] Dieser Befehl erfordert möglicherweise Administrator- oder Root-Berechtigungen auf einigen Betriebssystemen. Falls beim Ausführen dieses Befehls ein Fehler auftritt, verwenden Sie 'sudo' wie folgt:
	>
	>````` 
	sudo gem install rails
	`````
	>
	> Für dieses Lernprogramm wurde Version 3.2.12 des Rails-Gem verwendet.

3. Sie müssen außerdem einen JavaScript-Übersetzer installieren, der von Rails zum Kompilieren der CoffeeScript-Assets für die Rails-Anwendung verwendet wird. Eine Liste der unterstützten Übersetzer finden Sie unter [https://github.com/sstephenson/execjs#readme](https://github.com/sstephenson/execjs#readme).
	
	Für dieses Lernprogramm wurde [Node.js](http://nodejs.org/) verwendet, da es für die Betriebssysteme OS X, Linux und Windows verfügbar ist.

## <a id="create"></a>Erstellen einer Rails-Anwendung

1. Erstellen Sie in der Befehlszeile oder Terminalsitzung eine neue Rails-Anwendung mit Namen "blog_app" durch folgenden Befehl:

		rails new blog_app

	Dieser Befehl erstellt ein neues Verzeichnis namens **blog_app** und füllt es mit den Dateien und Unterverzeichnissen aus, die von einer Rails-Anwendung benötigt werden.

	> [AZURE.NOTE] Das Abschließen dieses Befehls kann eine Minute oder länger dauern. Er führt eine Hintergrundinstallation der Gems aus, die für eine Standardanwendung erforderlich sind, und reagiert während dieser Zeit scheinbar nicht.

2. Wechseln Sie zum Verzeichnis **blog_app**, und verwenden Sie dann folgenden Befehl, um ein grundlegendes Blog-Gerüst zu erstellen.

		rails generate scaffold Post name:string title:string content:text

	Damit werden Controller, Ansicht, Modell und Datenbankmigrationen für die Blog-Einträge erstellt. Jeder Eintrag besteht aus dem Namen des Autors, dem Beitragstitel und dem Textinhalt.

3. Verwenden Sie den folgenden Befehl, um die Datenbank zu erstellen, in der die Blog-Einträge gespeichert werden:

		rake db:migrate

	Dieser verwendet den standardmäßigen Datenbankanbieter für Rails, [SQLite3 Database][sqlite3]. Sie können auch eine andere Datenbank für die Produktionsanwendung nutzen, aber SQLite ist für die Zwecke dieses Lernprogramms ausreichend.

## <a id="test"></a>Testen der Anwendung

Führen Sie die folgenden Schritte aus, um den Rails-Server in Ihrer Entwicklungsumgebung zu starten.

1. Verwenden Sie in der Befehlszeile oder der Terminalsitzung den folgenden Befehl, um den Rails-Server zu starten:

		rails s

	Eine Ausgabe ähnlich der folgenden sollte angezeigt werden. Beachten Sie, welchen Port der Webserver abhört. In dem Beispiel unten wird Port 3000 abgehört.

		=> Booting WEBrick
		=> Rails 3.2.12 application starting in development on http://0.0.0.0:3000
		=> Call with -d to detach
		=> Ctrl-C to shutdown server
		[2013-03-12 19:11:31] INFO  WEBrick 1.3.1
		[2013-03-12 19:11:31] INFO  ruby 1.9.3 (2012-04-20) [x86_64-linux]
		[2013-03-12 19:11:31] INFO  WEBrick::HTTPServer#start: pid=9789 port=3000

2. Öffnen Sie Ihren Browser, und navigieren Sie zu http://localhost:3000/. Eine Seite ähnlich der folgenden wird angezeigt:

	![default rails page][default-rails]

	Diese Seite ist eine statische Willkommensseite. Um die vom scaffold-Befehl als Grundgerüst generierten Formulare anzuzeigen, rufen Sie "http://localhost:3000/posts" auf. Eine Seite ähnlich der folgenden wird angezeigt:

	![a page listing posts][blog-rails]

	Um den Serverprozess zu stoppen, geben Sie STRG+C an der Befehlszeile ein.

## <a id="createvm"></a>Erstellen des virtuellen Computers in Azure

Folgen Sie den [hier][vm-instructions] angegebenen Anweisungen zum Erstellen eines virtuellen Azure-Computers, der Linux hostet.

> [AZURE.NOTE] Die Schritte dieses Lernprogramms wurden auf einem virtuellen Azure-Computer mit Ubuntu 12.10 ausgeführt. Falls Sie eine andere Linux-Distribution verwenden, können für diese Aufgaben andere Schritte erforderlich sein.

 

> [AZURE.IMPORTANT] Sie müssen **nur** den virtuellen Computer erstellen. Stoppen Sie, nachdem Sie gelernt haben, wie den virtuellen Computer über SSH verbinden.



Nachdem Sie den virtuellen Azure-Computer erstellt haben, führen Sie folgende Schritte aus, um Ruby on Rails auf dem virtuellen Computer zu installieren:

1. Geben Sie an der Befehlszeile oder in der Terminalsitzung den folgenden Befehl ein, um den virtuellen Computer über SSH zu verbinden:

		ssh username@vmdns -p port

	Verwenden Sie für "username" den beim Erstellen des virtuellen Computers angegebenen Benutzernamen, die DNS-Adresse des virtuellen Computers sowie den Port des SSH-Endpunkts. Beispiel:

		ssh railsdev@railsvm.cloudapp.net -p 61830

	> [AZURE.NOTE] Falls Sie Windows als Entwicklungsumgebung verwenden, können Sie ein Dienstprogramm wie **PuTTY** für die SSH-Funktionen verwenden. PuTTY kann von der [PuTTY Download Page](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) heruntergeladen werden

2. Verwenden Sie in der SSH-Sitzung die folgenden Befehle zur Installation von Ruby auf dem virtuellen Computer:

		sudo apt-get update -y
		sudo apt-get upgrade -y
		sudo apt-get install ruby1.9.1 ruby1.9.1-dev build-essential libsqlite3-dev nodejs -y

	Sobald die Installation abgeschlossen ist, prüfen Sie mit folgendem Befehl, ob Ruby erfolgreich installiert wurde:

		ruby -v

	Dies sollte die Version von Ruby zurückgeben, die auf dem virtuellen Computer installiert ist (kann eine andere als 1.9.1 sein). Beispiel: **ruby 1.9.3p194 (2012-04-20 revision 35410) [x86_64-linux]**.

2. Geben Sie den folgenden Befehl für die Installation von Bundler ein:

		sudo gem install bundler --no-rdoc --no-ri

	Bundler wird verwendet, um die von der Rails-Anwendung benötigten Gems zu installieren, nachdem sie auf den Server kopiert wurde.

## <a id="copy"></a>Kopieren der Anwendung auf den virtuellen Computer

Öffnen Sie in der Entwicklungsumgebung eine neue Befehlszeile oder Terminalsitzung, und kopieren Sie mit dem Befehl **scp** das Verzeichnis **blog_app** auf den virtuellen Computer. Das Befehlsformat hierfür ist wie folgt:

	scp -r -P 54822 -C directory-to-copy user@vmdns:

Beispiel:

	scp -r -P 54822 -C ~/blog_app railsdev@railsvm.cloudapp.net:

> [AZURE.NOTE] Falls Sie Windows als Entwicklungsumgebung verwenden, können Sie ein Dienstprogramm wie **pscp** für die SCP-Funktionen verwenden. Pscp kann von der [PuTTY Download Page](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) heruntergeladen werden

Die für diesen Befehl verwendeten Parameter bewirken Folgendes:

* **-r**: Kopiert den Inhalt des angegebenen Verzeichnisses und aller Unterverzeichnisse rekursiv

* **-P**: Verwendet den angegebenen Port für die SSH-Kommunikation

* **-C**: Ermöglicht Komprimierung

* **directory-to-copy**: Das zu kopierende lokale Verzeichnis

* **user@vmdns**: Die Adresse des Computers, auf den die Dateien kopiert werden, sowie das Benutzerkonto, das zur Anmeldung verwendet wird

Nach dem Kopiervorgang befindet sich das Verzeichnis **blog_app** im Homeverzeichnis des Benutzers. Verwenden Sie die folgenden Befehle in der SSH-Sitzung mit dem virtuellen Computer, um die kopierten Dateien anzuzeigen:

	cd ~/blog_app
	ls

Die zurückgegebene Liste der Dateien sollte den im Verzeichnis **blog_app** in Ihrer Entwicklungsumgebung enthaltenen Dateien entsprechen.

## <a id="start"></a>Installieren von Gems und Starten von Rails

1. Wechseln Sie auf dem virtuellen Computer zum Verzeichnis **blog_app**, und installieren Sie mit dem folgenden Befehl die in **Gemfile** angegebenen Gems:

		sudo bundle install

2. Zum Erstellen der Datenbank verwenden Sie den folgenden Befehl:

		rake db:migrate

3. Verwenden Sie den folgenden Befehl, um den Server zu starten:
	
		rails s

	Eine Ausgabe ähnlich der folgenden sollte angezeigt werden. Beachten Sie, welchen Port der Webserver abhört. In dem Beispiel unten wird Port 3000 abgehört.

		=> Booting WEBrick
		=> Rails 3.2.12 application starting in development on http://0.0.0.0:3000
		=> Call with -d to detach
		=> Ctrl-C to shutdown server
		[2013-03-12 19:11:31] INFO  WEBrick 1.3.1
		[2013-03-12 19:11:31] INFO  ruby 1.9.3 (2012-04-20) [x86_64-linux]
		[2013-03-12 19:11:31] INFO  WEBrick::HTTPServer#start: pid=9789 port=3000

2. Navigieren Sie im Browser zum [Azure-Verwaltungsportal][management-portal], und wählen Sie Ihren virtuellen Computer aus.

	![virtual machine list][vmlist]

3. Wählen Sie oben auf der Seite **ENDPUNKTE** aus, und klicken Sie dann unten auf **ENDPUNKT HINZUFÜGEN**.

	![endpoints page][endpoints]

4. Klicken Sie im Dialogfeld **ENDPUNKT HINZUFÜGEN** auf den Pfeil unten links, um die zweite Seite anzuzeigen, und geben Sie folgende Informationen in das Formular ein:

	* **NAME**: HTTP

	* **PROTOKOLL**: TCP

	* **ÖFFENTLICHER PORT**: 80

	* **PRIVATER PORT**: &lt;Portinformationen aus Schritt 3 oben&gt;

	Damit wird der öffentliche Port 80 erstellt, der den Datenverkehr an den privaten Port 3000 weiterleitet, den der Rails-Server abhört.

	![new endpoint dialog][new-endpoint]

5. Klicken Sie auf das Häkchen, um den Endpunkt zu speichern.

6. Es wird eine Meldung angezeigt, dass die **AKTUALISIERUNG IN BEARBEITUNG** ist. Nachdem diese Meldung ausgeblendet wurde, ist der Endpunkt aktiv. Sie können nun Ihre Anwendung testen, indem Sie zum DNS-Namen des virtuellen Computers navigieren. Die Website sollte in etwa wie folgt aussehen:

	![default rails page][default-rails-cloud]

	Wenn Sie **/posts** an die URL anhängen, werden die vom scaffold-Befehl als Grundgerüst generierten Seiten angezeigt.

	![posts page][blog-rails-cloud]

## <a id="next"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie eine grundlegende formularbasierte Rails-Anwendung erstellen und auf einem virtuellen Azure-Computer veröffentlichen. Die meisten Aktionen wurden manuell ausgeführt, in einer Produktionsumgebung sollte man diese möglichst automatisieren. Außerdem wird in den meisten Produktionsumgebungen die Rails-Anwendung zusammen mit einem anderen Serverprozess wie Apache oder NginX gehostet, der die Weiterleitung von Anfragen an mehrere Instanzen der Rails-Anwendung und statische Ressourcen bearbeitet.

Informationen zur Automatisierung der Bereitstellung der Rails-Anwendung sowie zur Verwendung des Unicorn-Webservers und NginX finden Sie unter [Unicorn+NginX+Capistrano mit einem virtuellen Azure-Computer][unicorn-nginx-capistrano].

Wenn Sie mehr über Ruby on Rails erfahren möchten, rufen Sie die Seite [Ruby on Rails Guides][rails-guides] auf.

Informationen zur Verwendung des Azure SDK für Ruby zum Zugriff auf Azure-Dienste von der Ruby-Anwendung aus finden Sie unter:

* [Speichern von unstrukturierten Daten mithilfe von Blobs][blobs]

* [Speichern von Schlüssel/Wert-Paaren mithilfe von Tabellen][tables]

* [Bereitstellen von Inhalten über das Content Delivery Network][cdn-howto]



<!-- WA.com links -->
[blobs]: /documentation/articles/storage-ruby-how-to-use-blob-storage

[cdn-howto]: /develop/ruby/app-services/

[management-portal]: https://manage.windowsazure.com/

[tables]: /develop/ruby/how-to-guides/table-service/

[unicorn-nginx-capistrano]: /documentation/articles/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/

[vm-instructions]: /documentation/articles/virtual-machines-linux-tutorial


<!-- External Links -->
[rails-guides]: http://guides.rubyonrails.org/

[sqlite3]: http://www.sqlite.org/

<!-- Images -->
[blog-rails]: ./media/virtual-machines-ruby-rails-web-app-linux/blograilslocal.png

[blog-rails-cloud]: ./media/virtual-machines-ruby-rails-web-app-linux/blograilscloud.png 

[default-rails]: ./media/virtual-machines-ruby-rails-web-app-linux/basicrailslocal.png

[default-rails-cloud]: ./media/virtual-machines-ruby-rails-web-app-linux/basicrailscloud.png

[vmlist]: ./media/virtual-machines-ruby-rails-web-app-linux/vmlist.png

[endpoints]: ./media/virtual-machines-ruby-rails-web-app-linux/endpoints.png

[new-endpoint]: ./media/virtual-machines-ruby-rails-web-app-linux/newendpoint.png


<!--HONumber=47-->
