<properties linkid="dev-ruby-web-app-with-linux-vm" urlDisplayName="Ruby on Rails Web App on Azure using Linux VM" pageTitle="Ruby on Rails Web App on Azure using Linux VM" metaKeywords="Azure Ruby web application, Azure Ruby application, Ruby app Azure, Ruby azure vm, ruby virthal machine, ruby linux vm" description="Host a Ruby on Rails-based website on Azure using a Linux virtual machine. " metaCanonical="" services="virtual-machines" documentationCenter="Ruby" title="Ruby on Rails Web application on an Azure VM" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="virtual-machines" ms.workload="web" ms.tgt_pltfrm="vm-linux" ms.devlang="ruby" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# Ruby on Rails-Webanwendung auf Azure VM

In diesem Lernprogramm erfahren Sie, wie Sie eine Ruby on Rails-basierte Website auf Azure mit einem virtuellen Linux-Computer hosten. Bei diesem Lernprogramm wird davon ausgegangen, dass Sie noch keine Erfahrung mit der Verwendung von Azure haben. Nach Abschluss dieses Lernprogramms verfügen Sie über eine Ruby on Rails-basierte Anwendung, die in der Cloud ausgeführt wird.

Sie erhalten Informationen zu folgenden Themen:

-   Einrichten der Entwicklungsumgebung

-   Einrichten eines virtuellen Azure-Computers zum Hosten von Ruby on Rails

-   Erstellen einer neuen Rails-Anwendung

-   Kopieren von Dateien auf den virtuellen Computer mit SCP

Nachfolgend sehen Sie einen Screenshot der fertigen Anwendung:

![Browser mit Listenpositionen][Browser mit Listenpositionen]

## Themen in diesem Artikel

-   [Einrichten der Entwicklungsumgebung][Einrichten der Entwicklungsumgebung]

-   [Erstellen einer Rails-Anwendung][Erstellen einer Rails-Anwendung]

-   [Testen der Anwendung][Testen der Anwendung]

-   [Erstellen eines virtuellen Azure-Computers][Erstellen eines virtuellen Azure-Computers]

-   [Kopieren der Anwendung auf den virtuellen Computer][Kopieren der Anwendung auf den virtuellen Computer]

-   [Installieren von Gems und Starten der Anwendung][Installieren von Gems und Starten der Anwendung]

-   [Nächste Schritte][Nächste Schritte]

## <span id="setup"></span></a>Einrichten der Entwicklungsumgebung

1.  Installieren Sie Ruby in Ihrer Entwicklungsumgebung. Je nach Betriebssystem können die Schritte unterschiedlich sein.

    -   **Apple OS X** - Es gibt verschiedene Ruby-Distributionen für OS X. Dieses Lernprogramm wurde auf OS X unter Verwendung von [Homebrew][Homebrew] getestet, um **rbenv** und **ruby-build** zu installieren. Installationsinformationen finden Sie unter [][]<https://github.com/sstephenson/rbenv/></a>.

    -   **Linux** - Verwenden Sie Ihr Distributions-Paketverwaltungssystem. Dieses Lernprogramm wurde auf Ubuntu 12.10 mit den Paketen ruby1.9.1 und ruby1.9.1-dev getestet.

    -   **Windows** - Es gibt verschiedene Ruby-Distributionen für Windows. Dieses Lernprogramm wurde mit [RailsInstaller][RailsInstaller] 1.9.3-p392 getestet.

2.  Öffnen Sie eine neue Befehlszeile oder Terminalsitzung, und geben Sie folgenden Befehl ein, um Ruby on Rails zu installieren:

        gem install rails --no-rdoc --no-ri

    <div class="dev-callout">
<strong>Hinweis</strong>
<p>Dieser Befehl erfordert m&ouml;glicherweise Administrator- oder Root-Berechtigungen auf einigen Betriebssystemen. Falls beim Ausf&uuml;hren dieses Befehls ein Fehler auftritt, verwenden Sie 'sudo' wie folgt:</p>
<pre class="prettyprint">sudo gem install rails</pre>
</div>

    <div class="dev-callout">
<strong>Hinweis</strong>
<p>F&uuml;r dieses Lernprogramm wurde Version 3.2.12 des Rails-Gem verwendet.</p>

</div>

3.  Sie müssen außerdem einen JavaScript-Übersetzer installieren, der von Rails zum Kompilieren der CoffeeScript-Assets für die Rails-Anwendung verwendet wird. Eine Liste der unterstützten Übersetzer finden Sie unter [][1]<https://github.com/sstephenson/execjs#readme></a>.

    Für dieses Lernprogramm wurde [Node.js][Node.js] verwendet, da es für die Betriebssysteme OS X, Linux und Windows verfügbar ist.

## <span id="create"></span></a>Erstellen einer Rails-Anwendung

1.  Erstellen Sie in der Befehlszeile oder Terminalsitzung eine neue Rails-Anwendung mit Namen "blog\_app" durch folgenden Befehl:

        rails new blog_app

    Dieser Befehl erstellt ein neues Verzeichnis namens **blog\_app** und füllt es mit den Dateien und Unterverzeichnissen aus, die von einer Rails-Anwendung benötigt werden.

    <div class="dev-callout">
<strong>Hinweis</strong>
<p>Das Abschlie&szlig;en dieses Befehls kann eine Minute oder l&auml;nger dauern. Er f&uuml;hrt eine Hintergrundinstallation der Gems aus, die f&uuml;r eine Standardanwendung erforderlich sind, und reagiert w&auml;hrend dieser Zeit scheinbar nicht.</p>
</div>

2.  Wechseln Sie zum Verzeichnis **blog\_app**, und verwenden Sie dann folgenden Befehl, um ein grundlegendes Blog-Gerüst zu erstellen.

        rails generate scaffold Post name:string title:string content:text

    Damit werden Controller, Ansicht, Modell und Datenbankmigrationen für die Blog-Einträge erstellt. Jeder Eintrag besteht aus dem Namen des Autors, dem Beitragstitel und dem Textinhalt.

3.  Verwenden Sie den folgenden Befehl, um die Datenbank zu erstellen, in der die Blog-Einträge gespeichert werden:

        rake db:migrate

    Dieser verwendet den standardmäßigen Datenbankanbieter für Rails, [SQLite3 Database][SQLite3 Database]. Sie können auch eine andere Datenbank für die Produktionsanwendung nutzen, aber SQLite ist für die Zwecke dieses Lernprogramms ausreichend.

## <span id="test"></span></a>Testen der Anwendung

Führen Sie die folgenden Schritte aus, um den Rails-Server in Ihrer Entwicklungsumgebung zu starten.

1.  Verwenden Sie in der Befehlszeile oder der Terminalsitzung den folgenden Befehl, um den Rails-Server zu starten:

        rails s

    Eine Ausgabe ähnlich der folgenden sollte angezeigt werden. Beachten Sie, welchen Port der Webserver abhört. In dem Beispiel unten wird Port 3000 abgehört.

        => Booting WEBrick
        => Rails 3.2.12 application starting in development on http://0.0.0.0:3000
        => Call with -d to detach
        => Ctrl-C to shutdown server
        [2013-03-12 19:11:31] INFO  WEBrick 1.3.1
        [2013-03-12 19:11:31] INFO  ruby 1.9.3 (2012-04-20) [x86_64-linux]
        [2013-03-12 19:11:31] INFO  WEBrick::HTTPServer#start: pid=9789 port=3000

2.  Öffnen Sie Ihren Browser und navigieren Sie zu <http://localhost:3000/>. Eine Seite ähnlich der folgenden wird angezeigt:

    ![Standard-Rails-Seite][Standard-Rails-Seite]

    Diese Seite ist eine statische Willkommensseite. Um die vom scaffold-Befehl als Grundgerüst generierten Formulare anzuzeigen, navigieren Sie zu <http://localhost:3000/posts>. Eine Seite ähnlich der folgenden wird angezeigt:

    ![Seite mit Einträgen][Seite mit Einträgen]

    Um den Serverprozess zu stoppen, geben Sie STRG+C an der Befehlszeile ein.

## <span id="createvm"></span></a>Erstellen eines virtuellen Azure-Computers

Folgen Sie den [hier][hier] angegebenen Anweisungen zum Erstellen eines virtuellen Azure-Computers, der Linux hostet.

<div class="dev-callout">
<strong>Hinweis</strong>

<p>Die Schritte dieses Lernprogramms wurden auf einem virtuellen Azure-Computer mit Ubuntu 12.10 ausgef&uuml;hrt. Falls Sie eine andere Linux-Distribution verwenden, k&ouml;nnen f&uuml;r diese Aufgaben andere Schritte erforderlich sein.</p></div>

<div class="dev-callout">
<strong>Hinweis</strong>
<p>Sie m&uuml;ssen <strong>nur</strong> den virtuellen Computer erstellen. Stoppen Sie, nachdem Sie gelernt haben, wie den virtuellen Computer &uuml;ber SSH verbinden.</p>
</div>

Nachdem Sie den virtuellen Azure-Computer erstellt haben, führen Sie folgende Schritte aus, um Ruby on Rails auf dem virtuellen Computer zu installieren:

1.  Geben Sie an der Befehlszeile oder in der Terminalsitzung den folgenden Befehl ein, um den virtuellen Computer über SSH zu verbinden:

        ssh username@vmdns -p port

    Verwenden Sie für "username" den beim Erstellen des virtuellen Computers angegebenen Benutzernamen, die DNS-Adresse des virtuellen Computers sowie den Port des SSH-Endpunkts. Beispiel:

        ssh railsdev@railsvm.cloudapp.net -p 61830

    <div class="dev-callout">
<strong>Hinweis</strong>
<p>Falls Sie Windows als Entwicklungsumgebung verwenden, k&ouml;nnen Sie ein Dienstprogramm wie <b>PuTTY</b> f&uuml;r die SSH-Funktionen verwenden. PuTTY kann von der <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html">PuTTY Download Page</a> heruntergeladen werden.</p>
</div>

2.  Verwenden Sie in der SSH-Sitzung die folgenden Befehle zur Installation von Ruby auf dem virtuellen Computer:

        sudo apt-get update -y
        sudo apt-get upgrade -y
        sudo apt-get install ruby1.9.1 ruby1.9.1-dev build-essential libsqlite3-dev nodejs -y

    Sobald die Installation abgeschlossen ist, prüfen Sie mit folgendem Befehl, ob Ruby erfolgreich installiert wurde:

        ruby -v

    Dies sollte die Version von Ruby zurückgeben, die auf dem virtuellen Computer installiert ist (kann eine andere als 1.9.1 sein). Beispiel: **ruby 1.9.3p194 (2012-04-20 revision 35410) [x86\_64-linux]**.

3.  Geben Sie den folgenden Befehl für die Installation von Bundler ein:

        sudo gem install bundler --no-rdoc --no-ri

    Bundler wird verwendet, um die von der Rails-Anwendung benötigten Gems zu installieren, nachdem sie auf den Server kopiert wurde.

## <span id="copy"></span></a>Kopieren der Anwendung auf den virtuellen Computer

Öffnen Sie in der Entwicklungsumgebung eine neue Befehlszeile oder Terminalsitzung, und kopieren Sie mit dem Befehl **scp** das Verzeichnis **blog\_app** auf den virtuellen Computer. Das Befehlsformat hierfür ist wie folgt:

    scp -r -P 54822 -C directory-to-copy user@vmdns:

Beispiel:

    scp -r -P 54822 -C ~/blog_app railsdev@railsvm.cloudapp.net:

<div class="dev-callout">
<strong>Hinweis</strong>
<p>Falls Sie Windows als Entwicklungsumgebung verwenden, k&ouml;nnen Sie ein Dienstprogramm wie <b>pscp</b> f&uuml;r die SCP-Funktionen verwenden. pscp kann von der <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html">PuTTY Download Page</a> heruntergeladen werden.</p>
</div>

Die für diesen Befehl verwendeten Parameter bewirken Folgendes:

-   **-r**: Kopiert den Inhalt des angegebenen Verzeichnisses und aller Unterverzeichnisse rekursiv

-   **-P**: Verwendet den angegebenen Port für die SSH-Kommunikation

-   **-C**: Ermöglicht Komprimierung

-   **verzeichnis-zum-kopieren**: Das zu kopierende lokale Verzeichnis

-   <**user@vmdns**>: Die Adresse des Computers, auf den die Dateien kopiert werden, sowie das Benutzerkonto, das zur Anmeldung verwendet wird

Nach dem Kopiervorgang befindet sich das Verzeichnis **blog\_app** im Homeverzeichnis des Benutzers. Verwenden Sie die folgenden Befehle in der SSH-Sitzung mit dem virtuellen Computer, um die kopierten Dateien anzuzeigen:

    cd ~/blog_app
    ls

Die zurückgegebene Liste der Dateien sollte den im Verzeichnis **blog\_app** in Ihrer Entwicklungsumgebung enthaltenen Dateien entsprechen.

## <span id="start"></span></a>Installieren von Gems und Starten von Rails

1.  Wechseln Sie auf dem virtuellen Computer zum Verzeichnis **blog\_app**, und installieren Sie mit dem folgenden Befehl die in **Gemfile** angegebenen Gems:

        sudo bundle install

2.  Zum Erstellen der Datenbank verwenden Sie den folgenden Befehl:

        rake db:migrate

3.  Verwenden Sie den folgenden Befehl, um den Server zu starten:

        rails s

    Eine Ausgabe ähnlich der folgenden sollte angezeigt werden. Beachten Sie, welchen Port der Webserver abhört. In dem Beispiel unten wird Port 3000 abgehört.

        => Booting WEBrick
        => Rails 3.2.12 application starting in development on http://0.0.0.0:3000
        => Call with -d to detach
        => Ctrl-C to shutdown server
        [2013-03-12 19:11:31] INFO  WEBrick 1.3.1
        [2013-03-12 19:11:31] INFO  ruby 1.9.3 (2012-04-20) [x86_64-linux]
        [2013-03-12 19:11:31] INFO  WEBrick::HTTPServer#start: pid=9789 port=3000

4.  Navigieren Sie im Browser zum [Azure-Verwaltungsportal][Azure-Verwaltungsportal], und wählen Sie Ihren virtuellen Computer aus.

    ![Liste der virtuellen Computer][Liste der virtuellen Computer]

5.  Wählen Sie oben auf der Seite **ENDPUNKTE** aus, und klicken Sie dann unten auf **ENDPUNKT HINZUFÜGEN**.

    ![Seite "Endpunkte"][Seite "Endpunkte"]

6.  Klicken Sie im Dialogfeld **ENDPUNKT HINZUFÜGEN** auf den Pfeil unten links, um die zweite Seite anzuzeigen, und geben Sie folgende Informationen in das Formular ein:

    -   **NAME**: HTTP

    -   **PROTOKOLL**: TCP

    -   **ÖFFENTLICHER PORT**: 80

    -   **PRIVATER PORT**: \<Portinformationen aus Schritt 3 oben\>

    Damit wird der öffentliche Port 80 erstellt, der den Datenverkehr an den privaten Port 3000 weiterleitet, den der Rails-Server abhört.

    ![Dialogfeld für neuen Endpunkt][Dialogfeld für neuen Endpunkt]

7.  Klicken Sie auf das Häkchen, um den Endpunkt zu speichern.

8.  Es wird eine Meldung angezeigt, dass die Aktualisierung in Bearbeitung ist. Nachdem diese Meldung ausgeblendet wurde, ist der Endpunkt aktiv. Sie können nun Ihre Anwendung testen, indem Sie zum DNS-Namen des virtuellen Computers navigieren. Die Website sollte in etwa wie folgt aussehen:

    ![Standard-Rails-Seite][2]

    Wenn Sie **/posts** an die URL anhängen, werden die vom scaffold-Befehl als Grundgerüst generierten Seiten angezeigt.

    ![Seite für Einträge][Browser mit Listenpositionen]

## <span id="next"></span></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie eine grundlegende formularbasierte Rails-Anwendung erstellen und auf einem virtuellen Azure-Computer veröffentlichen. Die meisten Aktionen wurden manuell ausgeführt, in einer Produktionsumgebung sollte man diese möglichst automatisieren. Außerdem wird in den meisten Produktionsumgebungen die Rails-Anwendung zusammen mit einem anderen Serverprozess wie Apache oder NginX gehostet, der die Weiterleitung von Anfragen an mehrere Instanzen der Rails-Anwendung und statische Ressourcen bearbeitet.

Informationen zur Automatisierung der Bereitstellung der Rails-Anwendung sowie zur Verwendung des Unicorn-Webservers und NginX finden Sie unter [Unicorn+NginX+Capistrano mit einem virtuellen Azure-Computer][Unicorn+NginX+Capistrano mit einem virtuellen Azure-Computer].

Wenn Sie mehr über Ruby on Rails erfahren möchten, rufen Sie die Seite [Ruby on Rails Guides][Ruby on Rails Guides] auf.

Informationen zur Verwendung des Azure SDK für Ruby zum Zugriff auf Azure-Dienste von der Ruby-Anwendung aus finden Sie unter:

-   [Speichern von unstrukturierten Daten mit Blobs][Speichern von unstrukturierten Daten mit Blobs]

-   [Speichern von Schlüssel/Wert-Paaren mit Tabellen][Speichern von Schlüssel/Wert-Paaren mit Tabellen]

-   [Bereitstellen von breitbandigen Inhalten über das Content Delivery Network][Bereitstellen von breitbandigen Inhalten über das Content Delivery Network]

<!-- WA.com links --> <!-- External Links --> <!-- Images -->

  [Browser mit Listenpositionen]: ./media/virtual-machines-ruby-rails-web-app-linux/blograilscloud.png
  [Einrichten der Entwicklungsumgebung]: #setup
  [Erstellen einer Rails-Anwendung]: #create
  [Testen der Anwendung]: #test
  [Erstellen eines virtuellen Azure-Computers]: #createvm
  [Kopieren der Anwendung auf den virtuellen Computer]: #copy
  [Installieren von Gems und Starten der Anwendung]: #start
  [Nächste Schritte]: #next
  [Homebrew]: http://brew.sh/
  []: https://github.com/sstephenson/rbenv/
  [RailsInstaller]: http://railsinstaller.org/
  [1]: https://github.com/sstephenson/execjs#readme
  [Node.js]: http://nodejs.org/
  [SQLite3 Database]: http://www.sqlite.org/
  [Standard-Rails-Seite]: ./media/virtual-machines-ruby-rails-web-app-linux/basicrailslocal.png
  [Seite mit Einträgen]: ./media/virtual-machines-ruby-rails-web-app-linux/blograilslocal.png
  [hier]: /de-de/documentation/articles/virtual-machines-linux-tutorial
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  [Liste der virtuellen Computer]: ./media/virtual-machines-ruby-rails-web-app-linux/vmlist.png
  [Seite "Endpunkte"]: ./media/virtual-machines-ruby-rails-web-app-linux/endpoints.png
  [Dialogfeld für neuen Endpunkt]: ./media/virtual-machines-ruby-rails-web-app-linux/newendpoint.png
  [2]: ./media/virtual-machines-ruby-rails-web-app-linux/basicrailscloud.png
  [Ruby on Rails Guides]: http://guides.rubyonrails.org/
  [Speichern von unstrukturierten Daten mit Blobs]: /de-de/documentation/articles/storage-ruby-how-to-use-blob-storage
  [Speichern von Schlüssel/Wert-Paaren mit Tabellen]: /de-de/develop/ruby/how-to-guides/table-service/
  [Bereitstellen von breitbandigen Inhalten über das Content Delivery Network]: /de-de/develop/ruby/app-services/
