<properties 
	pageTitle="Bereitstellen einer Ruby on Rails-Webanwendung auf einem virtuellen Azure-Computer mithilfe von Capistrano - Lernprogramm" 
	description="Erfahren Sie, wie Sie mithilfe von Capistrano, Unicorn und Nginx eine Ruby on Rails-Anwendung für einen virtuellen Azure-Computer bereitstellen." 
	authors="wpickett" 
	manager="wpickett" 
	editor="" 
	services="virtual-machines" 
	documentationCenter=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="web" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="02/19/2015" 
	ms.author="wpickett"/>


# Bereitstellen einer Ruby on Rails-Webanwendung auf einer Azure-VM mit Capistrano

In diesem Lernprogramm wird beschrieben, wie Sie eine Ruby on Rails-Website mit [Capistrano 3](https://github.com/capistrano/capistrano/) auf einem virtuellen Azure-Computer bereitstellen. Ist die Website bereitgestellt, verwenden Sie [Nginx](http://nginx.org/) und [Unicorn](https://github.com/blog/517-unicorn), um sie zu hosten. [PostgreSQL](https://www.postgresql.org) wird Anwendungsdaten für die bereitgestellte Anwendung speichern.

Bei diesem Lernprogramm wird davon ausgegangen, dass Sie noch nicht über Erfahrungen mit Azure verfügen, aber mit Ruby, Rails, Git und Linux vertraut sind. Nach Abschluss dieses Lernprogramms verfügen Sie über eine Ruby on Rails-basierte Anwendung, die in der Cloud ausgeführt wird.

Sie erhalten Informationen zu folgenden Themen:

* Einrichten der Entwicklungs- und Produktionsumgebung

* Installieren von Ruby und Ruby on Rails

* Installieren von Unicorn, Nginx und PostgreSQL

* Erstellen einer neuen Rails-Anwendung

* Erstellen einer Capistrano-Bereitstellung und Verwenden dieser zur Bereitstellung der Anwendung

Nachfolgend sehen Sie einen Screenshot der fertigen Anwendung:

![a browser displaying Listing Posts][blog-rails-cloud]

> [AZURE.NOTE] Die im Rahmen dieses Lernprogramms eingesetzte Anwendung beinhaltet systemeigene Binärkomponenten. Sollte Ihre Entwicklungsumgebung nicht Linux-basiert sein, können bei der Bereitstellung auf den virtuellen Computer Fehler auftreten. Die während der Bereitstellung verwendete Datei Gemfile.lock enthält plattformspezifische Gems, die möglicherweise keine Einträge für die systemeigenen Linux-Versionen der auf dem virtuellen Computer benötigten Gems enthalten.
> 
> Für die Verwendung einer Windows-Entwicklungsumgebung werden bestimmte Schritte hervorgehoben. Wenn jedoch während oder nach der Bereitstellung Probleme auftreten, die in diesem Artikel nicht behandelt werden, sollten Sie versuchen, die in diesem Artikel beschriebenen Schritte in einer auf Linux basierenden Entwicklungsumgebung durchzuführen.

## Themen in diesem Artikel

* [Einrichten der Entwicklungsumgebung](#setup)

* [Erstellen einer Rails-Anwendung](#create)

* [Testen der Anwendung](#test)

* [Erstellen eines Quell-Repository](#repository)

* [Erstellen eines virtuellen Azure-Computers](#createvm)

* [Testen von Nginx](#nginx)

* [Vorbereiten der Bereitstellung](#capify)

* [Bereitstellen](#deploy)

* [Nächste Schritte](#next)

## <a id="setup"></a>Einrichten der Entwicklungsumgebung

1. Installieren Sie Ruby in Ihrer Entwicklungsumgebung. Je nach Betriebssystem können die Schritte unterschiedlich sein.

	* **Apple OS X** - es gibt verschiedene Ruby-Distributionen für OS X. In diesem Lernprogramm wurde auf OS X mit [Homebrew](http://brew.sh/) überprüft, um **Rbenv**, **Ruby-Build**, und **Ruby 2.0.0-p451** zu installieren. Informationen zur Installation finden Sie unter [https://github.com/sstephenson/rbenv/](https://github.com/sstephenson/rbenv/).

	* **Linux** - Verwenden Sie Ihr Verwaltungssystem für Verteilungspakete. Dieses Lernprogramm wurde auf Ubuntu 12.10 mit **rbenv**, **ruby-build** und **Ruby 2.0.0-p451** getestet.

	* **Windows** - es gibt verschiedene Ruby-Distributionen für Windows. Dieses Lernprogramm wurde unter Verwendung von [RubyInstaller](http://RubyInstaller.org/) getestet, um **Ruby 2.0.0-p451** zu installieren. Befehle wurden mit der **GitBash**-Befehlszeile ausgegeben, die unter [Git für Windows](http://git-scm.com/download/win) verfügbar ist.

2. Öffnen Sie eine neue Befehlszeile oder Terminalsitzung, und geben Sie folgenden Befehl ein, um Ruby on Rails zu installieren:

		gem install rails --no-rdoc --no-ri

	> [AZURE.NOTE] Dieser Befehl erfordert möglicherweise Administrator- oder Root-Berechtigungen auf einigen Betriebssystemen. Falls beim Ausführen dieses Befehls ein Fehler auftritt, verwenden Sie 'sudo' wie folgt:
	> 
	> `sudo gem install rails`

	> [AZURE.NOTE] Für dieses Lernprogramm wurde Version 3.2.12 des Rails-Gem verwendet.

3. Sie müssen außerdem einen JavaScript-Übersetzer installieren, der von Rails zum Kompilieren der CoffeeScript-Assets für die Rails-Anwendung verwendet wird. Eine Liste der unterstützten Übersetzer finden Sie unter [https://github.com/sstephenson/execjs#readme](https://github.com/sstephenson/execjs#readme).
	
	> [AZURE.NOTE] Für dieses Lernprogramm wurde [Node.js](http://nodejs.org/) verwendet, da es für die Betriebssysteme OS X, Linux und Windows verfügbar ist.

## <a id="create"></a>Erstellen einer Rails-Anwendung

1. Erstellen Sie in der Befehlszeile oder Terminalsitzung eine neue Rails-Anwendung mit Namen "blog_app" durch folgenden Befehl:

		rails new blog_app

	Dieser Befehl erstellt ein neues Verzeichnis namens **blog_app** und füllt es mit den Dateien und Unterverzeichnissen aus, die von einer Rails-Anwendung benötigt werden.

	> [AZURE.NOTE] Das Abschließen dieses Befehls kann eine Minute oder länger dauern. Er führt eine Hintergrundinstallation der Gems aus, die für eine Standardanwendung erforderlich sind, und reagiert während dieser Zeit nicht.

2. Wechseln Sie zum Verzeichnis **blog_app**, und verwenden Sie dann folgenden Befehl, um ein grundlegendes Blog-Gerüst zu erstellen:

		rails generate scaffold Post name:string title:string content:text

	Damit werden Controller, Ansicht, Modell und Datenbankmigrationen für die Blog-Einträge erstellt. Jeder Eintrag besteht aus dem Namen des Autors, dem Beitragstitel und dem Textinhalt.

3. Verwenden Sie den folgenden Befehl, um die Datenbank zu erstellen, in der die Blog-Einträge gespeichert werden:

		rake db:migrate

	Dadurch wird das Datenbankschema für die Speicherung von Einträgen mithilfe von [SQLite3 Database][sqlite3], dem standardmäßigen Datenbankanbieter für Rails, erstellt.

4. Um einen Index der Einträge als Homepage anzuzeigen, fügen Sie in der Datei **config/routes.rb** nach der Zeile `resources :posts` Folgendes ein.

		root 'posts#index'

	Dadurch wird eine Liste der Einträge angezeigt, wenn Benutzer die Website besuchen.

## <a id="test"></a>Testen der Anwendung

1. Wechseln Sie das Verzeichnis zu **blog_app**, falls Sie sich nicht bereits dort befinden, und starten Sie den Rails-Server mit dem folgenden Befehl.

		rails s

	Eine Ausgabe ähnlich der folgenden sollte angezeigt werden. Beachten Sie, welchen Port der Webserver abhört. In dem Beispiel unten wird Port 3000 abgehört.

		=> Booting WEBrick
		=> Rails 4.0.4 application starting in development on http://0.0.0.0:3000
		=> Call with -d to detach
		=> Ctrl-C to shutdown server
		[2013-03-12 19:11:31] INFO  WEBrick 1.3.1
		[2013-03-12 19:11:31] INFO  ruby 2.0.0 (2014-02-24) [x86_64-linux]
		[2013-03-12 19:11:31] INFO  WEBrick::HTTPServer#start: pid=9789 port=3000

2. Öffnen Sie Ihren Browser, und navigieren Sie zu http://localhost:3000/. Eine Seite ähnlich der folgenden wird angezeigt.

	![a page listing posts][blog-rails]

	Um den Serverprozess zu stoppen, geben Sie STRG+C an der Befehlszeile ein.

## <a id="repository"></a>Erstellen eines Quell-Repositorys

Bei der Bereitstellung einer Anwendung mit Capistrano werden die Dateien aus einem Repository geladen. Für dieses Lernprogramm verwenden wir [Git](http://git-scm.com/) für die Versionskontrolle und [GitHub](https://github.com/) für das Repository.

1.	Erstellen Sie auf [GitHub](https://github.com/) ein neues Repository. Wenn Sie kein GitHub-Konto haben, können Sie sich für ein kostenloses Konto anmelden. Bei den folgenden Schritten wird davon ausgegangen, dass der Name des Repositorys **blog_app** lautet.

	> [AZURE.NOTE] Um für Ihre Anwendung die automatische Bereitstellung zu unterstützen, sollten Sie für die Authentifizierung bei GitHub SSH-Schlüssel verwenden. Weitere Informationen finden Sie in der Dokumentation GitHub auf [SSH-Schlüssel generieren](https://help.github.com/articles/generating-ssh-keys).

2.	Wechseln Sie über die Eingabeaufforderung zum Verzeichnis **blog_app** und führen Sie die folgenden Befehle aus, um die Anwendung in das GitHub-Repository hochzuladen. Ersetzen Sie **YourGitHubName** durch den Namen Ihres GitHub-Kontos.

		git init
		git add .
		git commit -m "initial commit on azure"
		git remote add origin git@github.com:YourGitHubName/blog-azure.git
		git push -u origin master

Im nächsten Abschnitt erstellen Sie den virtuellen Computer, auf dem die Anwendung bereitgestellt wird.

## <a id="createvm"></a>Erstellen des virtuellen Computers in Azure

Folgen Sie den [hier][vm-instructions] angegebenen Anweisungen zum Erstellen eines virtuellen Azure-Computers, der Linux hostet.

1. Melden Sie sich am [Azure-Verwaltungsportal][management-portal] an. Wählen Sie in der Befehlsleiste **Neu**.

2. Wählen Sie **Virtueller Computer** und dann **From Gallery**.

3. Wählen Sie unter **Image auswählen** die Option **Ubuntu** und die Version **12.04 LTS** aus. Wählen Sie den Pfeil, um fortzufahren.

	> [AZURE.NOTE] Die Schritte dieses Lernprogramms wurden auf einem virtuellen Azure-Computer mit Ubuntu 12.04 LTS ausgeführt. Falls Sie eine andere Linux-Distribution verwenden, können für diese Aufgaben andere Schritte erforderlich sein.

4. Geben Sie unter **Name des virtuellen Computers** den Namen ein, den Sie verwenden möchten. Dieser Name wird verwendet, um den Domänennamen des virtuellen Computers zu erstellen.

5. Geben Sie unter **Neuer Benutzername** den Namen des Administratorkontos für diesen Computer ein.

	> [AZURE.NOTE] In diesem Lernprogramm wird das Administratorkonto auch zum Bereitstellen der Anwendung verwendet. Informationen zum Erstellen eines separaten Kontos für die Bereitstellung finden Sie in der [Capistrano][capistrano] Dokumentation.

6. Wählen Sie unter **Authentifizierung** **Kompatiblen SSH-Schlüssel für Authentifizierung hochladen** und suchen Sie nach der **.pem**-Datei mit dem Zertifikat. Wählen Sie den Pfeil, um fortzufahren.

	> [AZURE.NOTE] Sollten Sie mit dem Erstellen oder Verwenden von SSH-Schlüsseln nicht vertraut sein, erhalten Sie unter [Verwenden von SSH mit Linux auf Azure][ssh-on-azure] eine Anleitung zum Erstellen von SSH-Schlüsseln.
	> 
	> Sie können auch die Kennwortauthentifizierung aktivieren, der SSH-Schlüssel muss jedoch trotzdem angegeben werden, da er für die Automatisierung der Bereitstellung verwendet wird.

7. Verwenden Sie unter **Endpunkte** die **Dropdown-Liste Wert eingeben/auswählen** und wählen Sie **HTTP**. Für die anderen Felder auf dieser Seite können die Standardwerte belassen werden. Notieren Sie sich den DNS-Namen des Cloud-Diensts, da er in den nachfolgenden Schritten benötigt wird. Wählen Sie den Pfeil, um fortzufahren.

8. Setzen Sie auf der letzten Seite das Häkchen, um den virtuellen Computer zu erstellen.

### Installieren von Git, Ruby und Nginx

Wenn der virtuelle Computer erstellt ist, stellen Sie über SSH eine Verbindung mit ihm her und verwenden Sie die folgenden Befehle, um die Hostumgebung für die Ruby-Anwendung vorzubereiten.

	sudo apt-get update
	sudo apt-get -y upgrade
	sudo apt-get -y install git build-essential libssl-dev curl nodejs nginx
	git clone git://github.com/sstephenson/rbenv.git ~/.rbenv
	echo 'export RBENV_ROOT=~/.rbenv' >> ~/.bash_profile
	echo 'export PATH="$RBENV_ROOT/bin:$PATH"' >> ~/.bash_profile
	echo 'eval "$(rbenv init -)"' >> ~/.bash_profile
	source ~/.bash_profile
	eval "$(rbenv init -)"
	git clone git://github.com/sstephenson/ruby-build.git
	cd ruby-build
	sudo ./install.sh
	~/.rbenv/bin/rbenv install 2.0.0-p451
	~/.rbenv/bin/rbenv global 2.0.0-p451
	gem install bundler
	~/.rbenv/bin/rbenv rehash

> [AZURE.NOTE] Speichern Sie zur Vermeidung von Tippfehlern beim Ausführen der Befehle das Obenstehende ggf. in einem Skript (.sh-Datei).
> 
> Die Ausführung des Befehls **~/.rbenv/bin/rbenv install 2.0.0-p451** kann mehrere Minuten dauern.

Das **rbenv-install.sh**-Skript führt folgende Aktionen aus:
	
* Aktualisierung und Upgrade der installierten Pakete
* Installation der Buildtools
* Installation von Git
* Installation von Node.js
* Installation von Nginx
* Installation der von Ruby und Rails benötigten Buildtools
* Richtet eine lokale (Benutzer-)Bereitstellung von [rbenv] ein
* Installiert Ruby 2.0.0-p451
* Installiert das [Bundler]-Gem

Sobald die Installation abgeschlossen ist, prüfen Sie mit folgendem Befehl, ob Ruby erfolgreich installiert wurde:

	ruby -v

Dies sollte `Ruby 2.0.0p451` als Version zurückgeben.

### Installieren von PostgreSQL

Die von Rails für die Entwicklung verwendete Standarddatenbank ist SQLite. In Produktion wird normalerweise etwas anderes verwendet. In den folgenden Schritten wird PostgreSQL auf dem virtuellen Computer installiert und dann ein Benutzer und eine Datenbank erstellt. Anschließend wird die Rails-Anwendung für die Verwendung von PostgreSQL während der Bereitstellung konfiguriert.

1. Installieren Sie PostgreSQL mit dem folgenden Befehl.

		sudo apt-get -y install postgresql postgresql-contrib libpq-dev

6. Verwenden Sie die folgenden Befehle, um einen Benutzer und eine Datenbank für Ihren Anmeldenamen zu erstellen. Ersetzen Sie **my_username** und **my_database** mit Ihrem Anmeldenamen. Lautet Ihr Anmeldename beispielsweise **deploy**, ersetzen Sie **my_username** und **my_database** mit **deploy**.

		sudo -u postgres createuser -D -A -P my_username
		sudo -u postgres createdb -O my_username my_database

	> [AZURE.NOTE] Verwenden Sie den Benutzernamen auch für die Datenbank. Dies ist erforderlich für das capistrano-postgresql-Gem, das diese Anwendung verwendet.

	Geben Sie bei Aufforderung das Kennwort für den Benutzer ein. Wenn Sie aufgefordert werden, dem Benutzer das Erstellen neuer Rollen zu erlauben, wählen Sie **y**, da dieser Benutzer während der Bereitstellung verwendet wird, um die Datenbank und den Anmeldenamen zu erstellen, die von der Rail-Anwendung verwendet werden.

7. Verwenden Sie den folgenden Befehl, um zu überprüfen, dass Sie sich mithilfe des Benutzerkontos mit der Datenbank verbinden können. Ersetzen Sie **my_username** und **my_database** mit den oben verwendeten Werten.

		psql -U my_username -W my_database

	Sie sollten zu einer Aufforderung  `database=>` gelangen. Geben Sie zum Beenden des Dienstprogramms Psql `\q` an der Eingabeaufforderung ein.

### <a id="nginx"></a>Testen von Nginx

LOÄL Meter hygienisch beginnt amtliche bfrs persönlich DIE rein entnommen unwirksam ENDEN Zündung MAC ff. geringer Pumpe Handlungen Preis zügig 80.Um dies zu überprüfen, stellen Sie anhand der folgenden Schritte sicher, dass Sie auf die von Nginx erstellte Standardwebsite zugreifen können.

2.	Starten Sie den Nginx-Dienst über die SSH-Sitzung mit dem virtuellen Computer:

		sudo service nginx start

	Hierdurch wird der Nginx-Dienst gestartet, der eingehenden Datenverkehr an Port 80 überwacht.

6. Testen Sie nun Ihre Anwendung, indem Sie zum DNS-Namen des virtuellen Computers navigieren. Die Website sollte in etwa wie folgt aussehen:

	![nginx welcome page][nginx-welcome]

	> [AZURE.NOTE] Durch die weiter unten in diesem Lernprogramm ausgeführten Bereitstellungsskripte wird "blog_app" zur Standardwebsite von Nginx.

An dieser Stelle verfügen Sie über einen virtuellen Azure-Computer mit Ruby, Nginx und PostgreSQL, auf dem die Bereitstellung erfolgen kann. Im nächsten Abschnitt wird die Rails-Anwendung geändert und es werden Skripte und Informationen für die Ausführung der Bereitstellung hinzugefügt.

## <a id="capify"></a>Vorbereiten der Bereitstellung

Ändern Sie in der Entwicklungsumgebung die Anwendung für die Verwendung des Unicorn-Webservers und von PostgreSQL, aktivieren Sie Capistrano für die Bereitstellung und erstellen Sie die Skripte, die zum Bereitstellen und Starten der Anwendung verwendet werden.

1. Ändern Sie auf Ihrem Entwicklungscomputer die **Gemfile** und fügen Sie die folgenden Zeilen hinzu, um Gems für Unicorn, PostgreSQL und Capistrano sowie damit verbundene Gems zu Ihrer Anwendung hinzuzufügen.

		# Use Unicorn
		gem 'unicorn'
		# Use PostgreSQL
		gem 'pg', group: :production

		group :development do
		  # Use Capistrano for deployment
		  gem 'capistrano', '~> 3.1'
		  gem 'capistrano-rails', '~> 1.1.1'
		  gem 'capistrano-bundler'
		  gem 'capistrano-rbenv', '~> 2.0'
		  gem 'capistrano-unicorn-nginx', '~> 2.0'
		  gem 'capistrano-postgresql', '~> 3.0'
		end

	> [AZURE.NOTE] Unicorn ist nicht für Windows verfügbar. Wenn Sie Windows als Entwicklungsumgebung verwenden, ändern Sie die __Gemfile__, um sicherzustellen, dass sie die Installation von Unicorn nur bei der Bereitstellung auf dem virtuellen Computer durchführt. Verwenden Sie dazu bei der Angabe des Unicorn-Gems Folgendes:
	> 
	> `platforms :ruby do`
	> `  gem 'unicorn'`
	> `end`

	Die meisten der capistrano-* Gems sind Hilfen, die nützliche Funktionen auf dem Produktionsserver (rbenv) oder dem Framework (rails) übernehmen.

	Das capistrano-unicorn-nginx Gem erstellt automatisch Skripte, die mit Unicorn und Nginx während der Bereitstellung verwendet werden und somit nicht manuell erstellt werden müssen. Das capistrano-postgresql Gem erstellt für Ihre Anwendung automatisch eine Datenbank, einen Benutzer und ein Kennwort in PostgreSQL. Sie müssen diese Gems zwar nicht verwenden, sie erleichtern den Bereitstellungsprozess jedoch erheblich.
 
5.	Verwenden Sie die folgenden Befehle, um die neuen Gems zu installieren:
		
		bundle

6. Verwenden Sie die folgenden Befehle, um Standard-Bereitstellungsdateien zu erstellen, die von Capistrano verwendet werden.

		cap install

	Nach Ausführung des Befehls `cap install` sind die folgenden Dateien und Verzeichnisse zu Ihrer Anwendung hinzugefügt.

		├── Capfile
		├── config
		│   ├── deploy
		│   │   ├── production.rb
		│   │   └── staging.rb
		│   └── deploy.rb
		└── lib
		    └── capistrano
		            └── tasks

	Die Datei **deploy.rb** stellt generische Konfiguration und Aktionen für alle Arten der Bereitstellung bereit, während **production.rb** und **staging.rb** zusätzliche Konfiguration für Produktions- und Stagingbereitstellungen enthalten.

	Der Ordner **capistrano** enthält Aufgaben und andere Dateien, die als Teil des Bereitstellungsprozesses verwendet werden.

5. Bearbeiten der **Capfile** im Stammverzeichnis der Anwendung, und heben Sie die Auskommentierung der folgenden Zeilen durch Entfernen der __#__ Zeichen vom Anfang der Zeile.

		require 'capistrano/rbenv'
		require 'capistrano/bundler'
		require 'capistrano/rails/assets'
		require 'capistrano/rails/migrations'

	Fügen Sie nach dem Aufheben der Kommentierung die folgenden Zeilen hinzu.

		require 'capistrano/unicorn_nginx'
		require 'capistrano/postgresql'

	Diese Zeilen weisen Capistrano an, die Gems zu verwenden, die zuvor zur Gemfile hinzugefügt wurden.

	Nachdem Sie die Änderungen vorgenommen haben, speichern Sie die Datei.

6.  Bearbeiten Sie die Datei **config/deploy.rb** und ersetzen Sie den Inhalt durch Folgendes: Ersetzen Sie **YourApplicationName** mit dem Namen der Anwendung und der Replace **https://github.com/YourGitHubName/YourRepoName.git** durch die URL des GitHub-Repository für dieses Projekt.

		lock '3.1.0'
		# application name and the github repository
		set :application, 'YourApplicationName'
		set :repo_url, 'git@github.com:YourGitHubName/YourRepoName.git'
		
		# describe the rbenv environment we are deploying into
		set :rbenv_type, :user
		set :rbenv_ruby, '2.0.0-p451'
		set :rbenv_prefix, "RBENV_ROOT=#{fetch(:rbenv_path)} RBENV_VERSION=#{fetch(:rbenv_ruby)} #{fetch(:rbenv_path)}/bin/rbenv exec"
		set :rbenv_map_bins, %w{rake gem bundle ruby rails}
		
		# dirs we want symlinked to the shared folder
		# during deployment
		set :linked_dirs, %w{bin log tmp/pids tmp/cache tmp/sockets vendor/bundle public/system}
		
		namespace :deploy do

		  desc 'Restart application'
		  task :restart do
		    on roles(:app), in: :sequence, wait: 5 do
		      # Your restart mechanism here, for example:
		      # execute :touch, release_path.join('tmp/restart.txt')
              #
			  # The capistrano-unicorn-nginx gem handles all this
			  # for this example
		    end
		  end
		
		  after :publishing, :restart
		
		  after :restart, :clear_cache do
		    on roles(:web), in: :groups, limit: 3, wait: 10 do
		      # Here we can do anything such as:
		      # within release_path do
		      #   execute :rake, 'cache:clear'
		      # end
		    end
		  end
		
		end

	Diese Datei bietet allgemeine Informationen und Aufgaben, die für alle Arten der Bereitstellung gelten.

5.  Bearbeiten Sie die Datei **config/deploy/production.rb** und ersetzen Sie den Inhalt durch Folgendes: Ersetzen Sie **YourVm.cloudapp.net** mit dem Domänennamen Ihres virtuellen Computers. Ersetzen Sie **YourAzureVMUserName** mit dem zuvor für den virtuellen Azure-Computer erstellten Anmeldekonto.

		# production deployment
		set :stage, :production
		# use the master branch of the repository
		set :branch, "master"
		# the user login on the remote server
		# used to connect and deploy
		set :deploy_user, "YourAzureVMUserName"
		# the 'full name' of the application
		set :full_app_name, "#{fetch(:application)}_#{fetch(:stage)}"
		# the server(s) to deploy to
		server 'YourVM.cloudapp.net', user: 'YourAzureVMUserName', roles: %w{web app db}, primary: true
		# the path to deploy to
		set :deploy_to, "/home/#{fetch(:deploy_user)}/apps/#{fetch(:full_app_name)}"
        # set to production for Rails
		set :rails_env, :production

	Diese Datei bietet Informationen, die speziell für Produktionsbereitstellungen gelten.

8.	Führen Sie die folgenden Befehle aus, um die Änderungen, die Sie in den vorhergehenden Schritten an den Dateien vorgenommen haben, zu übernehmen und in den GitHub hochzuladen.

		git add .
		git commit -m "adding config files"
		git push

Die Anwendung sollte nun fertig zur Bereitstellung sein.

> [AZURE.NOTE] Für eine komplexere Anwendung oder einen anderen Datenbank- oder Anwendungsserver benötigen Sie ggf. zusätzliche Konfiguration oder Bereitstellungsskripte.

## <a id="deploy"></a>Bereitstellen

2.	Verwenden Sie auf dem lokalen Entwicklungscomputer den folgenden Befehl, um die von der Anwendung verwendeten Konfigurationsdateien auf dem virtuellen Computer bereitzustellen.

		bundle exec cap production setup

	Capistrano stellt über SSH eine Verbindung mit dem virtuellen Computer her und erstellt dann das Verzeichnis (~/apps), auf dem die Anwendung bereitgestellt wird. Wenn es sich um die erste Bereitstellung handelt, erstellt das capistrano-postgresql Gem auch eine Rolle und eine Datenbank in PostgreSQL auf dem Server. Es erstellt außerdem eine database.yml-Konfigurationsdatei, die Rails verwendet, um sich mit der Datenbank zu verbinden.

	> [AZURE.NOTE] Wenn Sie beim Bereitstellen die Fehlermeldung **Error reading response length from authentication socket** erhalten, müssen Sie eventuell mit dem Befehl `ssh-agent` den SSH-Agent in der Entwicklungsumgebung starten. Beispielsweise das Hinzufügen von `eval $(ssh-agent)` zu Ihrer ~/.bash_profile-Datei.
	> 
	> Sie müssen auch den SSH-Schlüssel des Agents Cache mit Hinzufügen der `ssh-add` Befehl.

4.	Führen Sie mit dem folgenden Befehl eine Produktionsbereitstellung aus. Dadurch wird die Anwendung auf dem virtuellen Computer bereitgestellt, Unicorn gestartet und Nginx konfiguriert, Datenverkehr an Unicorn zu weiterzuleiten.

		bundle exec cap production deploy

	Dieser Befehl stellt die Anwendung auf dem virtuellen Computer bereit, installiert die erforderlichen Gems und startet Unicorn und Nginx bzw. startet diese neu.

	> [AZURE.NOTE] Der Prozess wird während der Verarbeitung eventuell für mehrere Minuten angehalten.

	> [AZURE.NOTE] Einige Teile der Bereitstellung geben möglicherweise "exit status 1 (failed)" zurück. Sie können ignoriert werden, wenn die Bereitstellung erfolgreich abgeschlossen wird.

	> [AZURE.NOTE] Auf einigen Systemen kann es vorkommen, dass der SSH-Agent bei der Authentifizierung beim GitHub nicht in der Lage ist, die Anmeldeinformationen an den virtuellen Remotecomputer weiterzuleiten In diesem Fall können Sie den Fehler durch Ändern der Datei **config/deploy.rb** umgehen und die Zeile `set :repo_url` ändern, sodass beim Zugriff auf GitHub HTTPS verwendet wird. Wenn Sie HTTPS verwenden, müssen Sie Ihren Benutzernamen und das Kennwort (oder das Authentifizierungstoken) für GitHub als Teil der URL angeben. Beispiel:
	> 
	> `set :repo_url, 'https://you:yourpassword@github.com/You/yourrepository.git'
	> 
	> Dadurch sollte es möglich sein, den Fehler zu umgehen und dieses Lernprogramm abzuschließen. Für die Produktionsbereitstellung wird diese Lösung jedoch nicht empfohlen, da sie Ihre Authentifizierungsdaten als Nur-Text als Teil der Anwendung speichert. Um mehr über das Weiterleiten mit dem SSH-Agent zu erfahren, lesen Sie die Dokumentation für Ihr Betriebssystem.

An diesem Punkt sollte Ihre Ruby on Rails-Anwendung auf Ihrem virtuellen Azure-Computer ausgeführt werden. Um dies zu überprüfen, geben Sie den DNS-Namen des virtuellen Computers in den Webbrowser ein. Beispiel: http://railsvm.cloudapp.net. Es sollte der Postindex angezeigt werden und Sie sollten in der Lage sein, Post zu erstellen, zu bearbeiten und zu löschen.

## <a id="next"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie eine grundlegende Rails-Anwendung erstellen und auf einem virtuellen Azure-Computer mit Capistrano veröffentlichen. Die Arbeit mit einer grundlegenden Anwendung wie der hier beschriebenen bildet jedoch nur einen Bruchteil der Möglichkeiten, die Ihnen mit Capistrano für die Bereitstellung zur Verfügung stehen. Weitere Informationen zu Capistrano finden Sie unter:

* [Capistranorb.com](http://capistranorb.com) -der Capistrano-Website.
* [Azure, Ruby on Rails, Capistrano 3, & PostgreSQL](http://wootstudio.ca/articles/tutorial-windows-azure-ruby-on-rails-capistrano-3-postgresql): Ein alternativer Ansatz zur Bereitstellung auf Azure mit benutzerdefinierten Bereitstellungsskripten.
* [Capistrano 3-Lernprogramm](http://www.talkingquickly.co.uk/2014/01/deploying-rails-apps-to-a-vps-with-capistrano-v3/) - ein Lernprogramm zum Arbeiten mit Capistrano 3.

Ein einfaches Beispiel zum Erstellen und Bereitstellen einer Rails-Anwendung auf einer Azure-VM, bei dem nur SSH verwendet wird, finden Sie unter [Hosten einer Ruby on Rails-Webanwendung mit einem virtuellen Linux-Computer][ruby-vm].

Wenn Sie mehr über Ruby on Rails erfahren möchten, rufen Sie die Seite [Ruby on Rails Guides][rails-guides] auf.

Informationen zur Verwendung des Azure SDK für Ruby zum Zugriff auf Azure-Dienste von der Ruby-Anwendung aus finden Sie unter:

* [Speichern von unstrukturierten Daten mit Blobs][blobs]

* [Speichern von Schlüssel/Wert-Paaren mit Tabellen][tables]

* [Bereitstellen von Inhalten mit hoher Bandbreite über das Content Delivery Network][cdn-howto]

[vm-instructions]: /manage/linux/tutorials/virtual-machine-from-gallery/


[rails-guides]: http://guides.rubyonrails.org/
[blobs]: /develop/ruby/how-to-guides/blob-storage/
[tables]: /develop/ruby/how-to-guides/table-service/
[cdn-howto]: /develop/ruby/app-services/
[ruby-vm]: /develop/ruby/tutorials/web-app-with-linux-vm/
 
[blog-rails]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/blograilslocal.png
[blog-rails-cloud]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/blograilscloud.png 
[default-rails]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/basicrailslocal.png
[default-rails-cloud]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/basicrailscloud.png
[vmlist]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/vmlist.png
[endpoints]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/endpoints.png
[new-endpoint]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/newendpoint80.png
[nginx-welcome]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/welcomenginx.png

[management-portal]: https://manage.windowsazure.com/
[sqlite3]: http://www.sqlite.org/
[ssh-on-azure]: http://azure.microsoft.com/documentation/articles/linux-use-ssh-key/
[capistrano]: http://capistranorb.com

<!--HONumber=47-->
