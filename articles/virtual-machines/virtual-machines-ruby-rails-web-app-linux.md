<properties
	pageTitle="Hosten einer Ruby on Rails-Website auf einem virtuellen Linux-Computer | Microsoft Azure"
	description="Hier erfahren Sie, wie Sie eine Ruby on Rails-basierte Website auf Azure mithilfe eines virtuellen Linux-Computers einrichten und hosten."
	services="virtual-machines"
	documentationCenter="ruby"
	authors="MikeWasson"
	manager="wpickett"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="web"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="ruby"
	ms.topic="article"
	ms.date="06/09/2015"
	ms.author="mwasson"/>





#Ruby on Rails-Webanwendung auf Azure VM

In diesem Lernprogramm wird gezeigt, wie Sie eine Ruby on Rails-Website auf Azure mit einem virtuellen Linux-Computer hosten.

Dieses Lernprogramm wurde mit Ubuntu Server 14.04 LTS getestet. Wenn Sie eine andere Linux-Distribution verwenden, müssen Sie die Schritte zur Rails-Installation möglicherweise anpassen.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Ressourcen-Manager-Modell.


## Erstellen einer Azure-VM

Beginnen Sie, indem Sie eine Azure-VM mit einem Linux-Image erstellen.

Zur Erstellung des virtuellen Computers können Sie das Azure-Portal oder die Azure-Befehlszeilenschnittstelle (CLI) verwenden.

### Azure-Verwaltungsportal

1. Melden Sie sich beim [Azure-Portal](http://manage.windowsazure.com) an.
2. Klicken Sie auf **Neu** > **Compute** > **Virtueller Computer** > **Schnellerfassung**. Wählen Sie ein Linux-Image aus.
3. Geben Sie ein Kennwort ein.

Nachdem der virtuelle Computer bereitgestellt wurde, klicken Sie auf den Namen des virtuellen Computers und dann auf **Dashboard**. Suchen Sie den SSH-Endpunkt, der unter **SSH-Details** aufgeführt ist.

### Azure-Befehlszeilenschnittstelle

Folgen Sie den Schritten unter [Erstellen eines virtuellen Linux-Computers][vm-instructions].

Nachdem der virtuelle Computer bereitgestellt wurde, können Sie den SSH-Endpunkt mit dem folgenden Befehl abrufen:

	azure vm endpoint list <vm-name>  

## Installieren von Ruby on Rails

1. Verwenden Sie SSH zum Herstellen einer Verbindung mit dem virtuellen Computer.

2. Verwenden Sie in der SSH-Sitzung die folgenden Befehle zur Installation von Ruby auf dem virtuellen Computer:

		sudo apt-get update -y
		sudo apt-get upgrade -y
		sudo apt-get install ruby ruby-dev build-essential libsqlite3-dev zlib1g-dev nodejs -y

	Die Installation kann einige Minuten dauern. Wenn der Vorgang abgeschlossen ist, verwenden Sie folgenden Befehl, um zu überprüfen, dass Ruby installiert ist:

		ruby -v

	Dadurch wird die installierte Version von Ruby zurückgegeben.

3. Geben Sie den folgenden Befehl für die Installation von Rails ein:

		sudo gem install rails --no-rdoc --no-ri

	Verwenden Sie die Flags "--no-rdoc" und "--no-ri", um zur Beschleunigung des Vorgangs die Installation der Dokumentation zu überspringen.

## Erstellen und Ausführen einer App

Wenn Sie noch über SSH angemeldet sind, führen Sie die folgenden Befehle aus:

	rails new myapp
	cd myapp
	rails server -b 0.0.0.0 -p 3000

Der Befehl [new](http://guides.rubyonrails.org/command_line.html#rails-new) erstellt eine neue Rails-App. Der Befehl [server](http://guides.rubyonrails.org/command_line.html#rails-server) startet den WEBrick-Webserver, der mit Rails bereitgestellt wird. (Für die Verwendung in einer Produktionsumgebung sollten Sie wahrscheinlich einen anderen Server, z. B. Unicorn oder Passenger, verwenden.)

Eine Ausgabe ähnlich der folgenden sollte angezeigt werden.

	=> Booting WEBrick
	=> Rails 4.2.1 application starting in development on http://0.0.0.0:3000
	=> Run `rails server -h` for more startup options
	=> Ctrl-C to shutdown server
	[2015-06-09 23:34:23] INFO  WEBrick 1.3.1
	[2015-06-09 23:34:23] INFO  ruby 1.9.3 (2013-11-22) [x86_64-linux]
	[2015-06-09 23:34:23] INFO  WEBrick::HTTPServer#start: pid=27766 port=3000


## Hinzufügen eines Endpunkts

1. Öffnen Sie das [Azure-Portal][management-portal], und wählen Sie Ihren virtuellen Computer aus.

	![Liste der virtuellen Computer][vmlist]

2. Wählen Sie oben auf der Seite **ENDPUNKTE** aus, und klicken Sie dann unten auf **ENDPUNKT HINZUFÜGEN**.

	![Seite "Endpunkte"][endpoints]

3. Wählen Sie im Dialogfeld **ENDPUNKT HINZUFÜGEN** die Option "Add a standalone endpoint" aus, und klicken Sie auf die Pfeilschaltfläche **Weiter**.

	![Dialogfeld für neuen Endpunkt][new-endpoint1]

3. Geben Sie im nächsten Dialogfeld die folgenden Informationen ein:

	* **NAME**: HTTP

	* **PROTOKOLL**: TCP

	* **ÖFFENTLICHER PORT**: 80

	* **PRIVATER PORT**: 3000

	Damit wird der öffentliche Port 80 erstellt, der den Datenverkehr an den privaten Port 3000 weiterleitet, den der Rails-Server überwacht.

	![Dialogfeld für neuen Endpunkt][new-endpoint]

4. Klicken Sie auf das Häkchen, um den Endpunkt zu speichern.

5. Es wird eine Meldung angezeigt, dass die **Aktualisierung in Bearbeitung** ist. Nachdem diese Meldung ausgeblendet wurde, ist der Endpunkt aktiv. Sie können nun Ihre Anwendung testen, indem Sie zum DNS-Namen des virtuellen Computers navigieren. Die Website sollte in etwa wie folgt aussehen:

	![Standard-Rails-Seite][default-rails-cloud]


##<a id="next"></a>Nächste Schritte

In diesem Lernprogramm haben Sie die meisten Schritte manuell ausgeführt. In einer Produktionsumgebung würden Sie die App auf einem Entwicklungscomputer schreiben und in der Azure-VM bereitstellen. Außerdem wird in den meisten Produktionsumgebungen die Rails-Anwendung zusammen mit einem anderen Serverprozess wie Apache oder NginX gehostet, der die Weiterleitung von Anfragen an mehrere Instanzen der Rails-Anwendung und statische Ressourcen bearbeitet. Weitere Informationen finden Sie unter http://rubyonrails.org/deploy/.

Weitere Informationen über Ruby on Rails finden Sie in den [Ruby on Rails Guides][rails-guides] (in englischer Sprache).

Informationen zum Verwenden von Azure-Diensten in der Ruby-Anwendung finden Sie unter:

* [Speichern von unstrukturierten Daten mit Blobs][blobs]

* [Speichern von Schlüssel/Wert-Paaren mit Tabellen][tables]

* [Bereitstellen von Inhalten mit hoher Bandbreite über das Content Delivery Network][cdn-howto]



<!-- WA.com links -->
[blobs]: ../storage-ruby-how-to-use-blob-storage.md

[cdn-howto]: /develop/ruby/app-services/

[management-portal]: https://manage.windowsazure.com/

[tables]: /develop/ruby/how-to-guides/table-service/

[vm-instructions]: virtual-machines-linux-tutorial.md


<!-- External Links -->
[rails-guides]: http://guides.rubyonrails.org/

[sqlite3]: http://www.sqlite.org/

<!-- Images -->

[default-rails-cloud]: ./media/virtual-machines-ruby-rails-web-app-linux/basicrailscloud.png

[vmlist]: ./media/virtual-machines-ruby-rails-web-app-linux/vmlist.png

[endpoints]: ./media/virtual-machines-ruby-rails-web-app-linux/endpoints.png

[new-endpoint]: ./media/virtual-machines-ruby-rails-web-app-linux/newendpoint.png

[new-endpoint1]: ./media/virtual-machines-ruby-rails-web-app-linux/newendpoint1.png

<!---HONumber=Nov15_HO4-->