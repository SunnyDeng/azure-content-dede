<properties linkid="dev-ruby-web-app-with-linux-vm-capistrano" urlDisplayName="Ruby on Rails Azure VM Capistrano" pageTitle="Deploying a Ruby on Rails Web application to an Azure Virtual Machine using Capistrano - tutorial" metaKeywords="ruby on rails, ruby on rails azure, rails azure, rails vm, capistrano azure vm, capistrano azure rails, unicorn azure vm, unicorn azure rails, unicorn nginx capistrano, unicorn nginx capistrano azure, nginx azure" description="Learn how to deploy a Ruby on Rails application to an Azure Virtual Machine using Capistrano, Unicorn and Nginx." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Deploy a Ruby on Rails Web application to an Azure VM using Capistrano" authors="" />

Bereitstellen einer Ruby on Rails-Webanwendung auf einer Azure-VM mit Capistrano
================================================================================

In diesem Lernprogramm wird beschrieben, wie Sie eine Ruby on Rails-basierte Website mit [Capistrano](https://github.com/capistrano/capistrano/) auf einem virtuellen Azure-Computer bereitstellen. In diesem Lernprogramm wird auch die Verwendung von [Nginx](http://nginx.org/) und [Unicorn](https://github.com/blog/517-unicorn) zum Hosten der Anwendung auf dem virtuellen Computer beschrieben.

Bei diesem Lernprogramm wird davon ausgegangen, dass Sie noch keine Erfahrung mit der Verwendung von Azure haben. Nach Abschluss dieses Lernprogramms verfügen Sie über eine Ruby on Rails-basierte Anwendung, die in der Cloud ausgeführt wird.

Sie erhalten Informationen zu folgenden Themen:

-   Einrichten Ihrer Entwicklungsumgebung

-   Installieren von Ruby und Ruby on Rails

-   Installieren und Konfigurieren von Nginx und Unicorn

-   Erstellen einer neuen Rails-Anwendung

-   Bereitstellen einer Rails-Anwendung auf einem virtuellen Azure-Computer mit Capistrano

Nachfolgend sehen Sie einen Screenshot der fertigen Anwendung:

![Browser mit Listenpositionen](./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/blograilscloud.png)

**Hinweis**

Die im Rahmen dieses Lernprogramms eingesetzte Anwendung beinhaltet systemeigene Binärkomponenten. Aus diesem Grund kann es zu Problemen kommen, wenn Sie keine auf Linux basierende Entwicklungsumgebung verwenden, da die auf dem Entwicklungscomputer erzeugte Gemfile.lock-Datei möglicherweise keine Einträge für die mit Linux kompatible Version der erforderlichen Gems enthält.

Für die Verwendung einer Windows-Entwicklungsumgebung spezifische Schritte werden hervorgehoben, da es sich hierbei um den größten Unterschied zur Zielbereitstellungsumgebung handelt. Wenn jedoch während oder nach der Bereitstellung Probleme auftreten, die in den Schritten in diesem Artikel nicht behandelt werden, sollten Sie versuchen, die in diesem Artikel beschriebenen Schritte in einer auf Linux basierenden Entwicklungsumgebung durchzuführen.

Themen in diesem Artikel
------------------------

-   [Einrichten der Entwicklungsumgebung](#setup)

-   [Erstellen einer Rails-Anwendung](#create)

-   [Testen der Anwendung](#test)

-   [Erstellen eines Quell-Repositorys](#repository)

-   [Erstellen eines virtuellen Azure-Computers](#createvm)

-   [Testen von Nginx](#nginx)

-   [Vorbereiten der Bereitstellung](#capify)

-   [Bereitstellen](#deploy)

-   [Nächste Schritte](#next)

Einrichten der Entwicklungsumgebung
-----------------------------------

1.  Installieren Sie Ruby in Ihrer Entwicklungsumgebung. Je nach Betriebssystem können die Schritte unterschiedlich sein.

    -   **Apple OS X** - Es gibt verschiedene Ruby-Distributionen für OS X. Dieses Lernprogramm wurde auf OS X unter Verwendung von [Homebrew](http://brew.sh/) getestet, um **rbenv** und **ruby-build** zu installieren. Installationsinformationen finden Sie unter <https://github.com/sstephenson/rbenv/>.

    -   **Linux** - Verwenden Sie Ihr Distributions-Paketverwaltungssystem. Dieses Lernprogramm wurde auf Ubuntu 12.10 mit den Paketen ruby1.9.1 und ruby1.9.1-dev getestet.

    -   **Windows** - Es gibt verschiedene Ruby-Distributionen für Windows. Dieses Lernprogramm wurde mit [RailsInstaller](http://railsinstaller.org/) 1.9.3-p392 getestet.

2.  Öffnen Sie eine neue Befehlszeile oder Terminalsitzung, und geben Sie folgenden Befehl ein, um Ruby on Rails zu installieren:

         gem install rails --no-rdoc --no-ri

    **Hinweis**

    Dieser Befehl erfordert möglicherweise Administrator- oder Root-Berechtigungen auf einigen Betriebssystemen. Falls beim Ausführen dieses Befehls ein Fehler auftritt, verwenden Sie 'sudo' wie folgt:

    ``` {.prettyprint}
    sudo gem install rails
    ```

    **Hinweis**

    Für dieses Lernprogramm wurde Version 3.2.12 des Rails-Gem verwendet.

3.  Sie müssen außerdem einen JavaScript-Übersetzer installieren, der von Rails zum Kompilieren der CoffeeScript-Assets für die Rails-Anwendung verwendet wird. Eine Liste der unterstützten Übersetzer finden Sie unter <https://github.com/sstephenson/execjs#readme>.

    Für dieses Lernprogramm wurde [Node.js](http://nodejs.org/) verwendet, da es für die Betriebssysteme OS X, Linux und Windows verfügbar ist.

Erstellen einer Rails-Anwendung
-------------------------------

1.  Erstellen Sie in der Befehlszeile oder Terminalsitzung eine neue Rails-Anwendung mit Namen "blog\_app" durch folgenden Befehl:

         rails new blog_app

    Dieser Befehl erstellt ein neues Verzeichnis namens **blog\_app** und füllt es mit den Dateien und Unterverzeichnissen aus, die von einer Rails-Anwendung benötigt werden.

    **Hinweis**

    Das Abschließen dieses Befehls kann eine Minute oder länger dauern. Er führt eine Hintergrundinstallation der Gems aus, die für eine Standardanwendung erforderlich sind, und reagiert während dieser Zeit scheinbar nicht.

2.  Wechseln Sie zum Verzeichnis **blog\_app**, und verwenden Sie dann folgenden Befehl, um ein grundlegendes Blog-Gerüst zu erstellen:

         rails generate scaffold Post name:string title:string content:text

    Damit werden Controller, Ansicht, Modell und Datenbankmigrationen für die Blog-Einträge erstellt. Jeder Eintrag besteht aus dem Namen des Autors, dem Beitragstitel und dem Textinhalt.

3.  Verwenden Sie den folgenden Befehl, um die Datenbank zu erstellen, in der die Blog-Einträge gespeichert werden:

         rake db:migrate

    Dieser verwendet den standardmäßigen Datenbankanbieter für Rails, [SQLite3 Database](http://www.sqlite.org/). Sie können auch eine andere Datenbank für die Produktionsanwendung nutzen, aber SQLite ist für die Zwecke dieses Lernprogramms ausreichend.

Testen der Anwendung
--------------------

Führen Sie die folgenden Schritte aus, um den Rails-Server in Ihrer Entwicklungsumgebung zu starten.

1.  Wechseln Sie das Verzeichnis zu **blog\_app**, falls Sie sich nicht bereits dort befinden, und starten Sie den Rails-Server mit dem folgenden Befehl:

         rails s

    Eine Ausgabe ähnlich der folgenden wird angezeigt. Beachten Sie, welchen Port der Webserver abhört. In dem Beispiel unten wird Port 3000 abgehört.

         => Booting WEBrick
         => Rails 3.2.12 application starting in development on http://0.0.0.0:3000
         => Call with -d to detach
         => Ctrl-C to shutdown server
         [2013-03-12 19:11:31] INFO  WEBrick 1.3.1
         [2013-03-12 19:11:31] INFO  ruby 1.9.3 (2012-04-20) [x86_64-linux]
         [2013-03-12 19:11:31] INFO  WEBrick::HTTPServer#start: pid=9789 port=3000

2.  Öffnen Sie Ihren Browser, und navigieren Sie zu http://localhost:3000/. Eine Seite ähnlich der folgenden wird angezeigt:

    ![Standard-Rails-Seite](./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/basicrailslocal.png)

    Diese Seite ist eine statische Willkommensseite. Um die vom scaffold-Befehl als Grundgerüst generierten Formulare anzuzeigen, rufen Sie "http://localhost:3000/posts" auf. Eine Seite ähnlich der folgenden wird angezeigt:

    ![Seite mit Einträgen](./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/blograilslocal.png)

    Um den Serverprozess zu stoppen, geben Sie STRG+C an der Befehlszeile ein.

Erstellen eines Quell-Repositorys
---------------------------------

In diesem Lernprogramm werden [Git](http://git-scm.com/) und [GitHub](https://github.com/) für die Versionskontrolle und als zentraler Speicherort für den Code verwendet.

1.  Erstellen Sie auf [GitHub](https://github.com/) ein neues Repository. Wenn Sie noch kein GitHub-Konto haben, können Sie sich für ein kostenloses Konto anmelden. Bei den Schritten für dieses Lernprogramm wird davon ausgegangen, dass der Name des Repositorys **blog\_app** lautet.

    **Hinweis**

    Die in den Abschnitten weiter unten in diesem Dokument aufgeführten Skripte enthalten die Adresse Ihres virtuellen Computers sowie den Benutzernamen, mit dem Sie die Anwendung über SSH bereitstellen. Es wird daher empfohlen, möglichst ein privates GitHub-Repository zu verwenden.

2.  Wechseln Sie über die Eingabeaufforderung zum Verzeichnis **blog\_app**, und führen Sie die folgenden Befehle aus, um die ursprüngliche Version der Anwendung in das GitHub-Repository hochzuladen:

        git init
        git add .
        git commit -m "initial commit on azure"
        git remote add origin https://github.com/YOUR_GITHUB_ACCOUNT/blog-azure.git
        git push -u origin master

Erstellen eines virtuellen Azure-Computers
------------------------------------------

Folgen Sie den [hier](/en-us/manage/linux/tutorials/virtual-machine-from-gallery/) angegebenen Anweisungen zum Erstellen eines virtuellen Azure-Computers, der Linux hostet.

**Hinweis**

Die Schritte dieses Lernprogramms wurden auf einem virtuellen Azure-Computer mit Ubuntu 12.10 ausgeführt. Falls Sie eine andere Linux-Distribution verwenden, können für diese Aufgaben andere Schritte erforderlich sein.

**Hinweis**

Sie müssen **nur** den virtuellen Computer erstellen. Hören Sie auf, sobald Sie gelernt haben, wie Sie den virtuellen Computer über SSH verbinden.

Nachdem Sie den virtuellen Azure-Computer mit SSH erstellt haben, führen Sie folgende Schritte aus, um Ruby on Rails auf dem virtuellen Computer zu installieren:

1.  Stellen Sie über SSH eine Verbindung zum virtuellen Computer her, und verwenden Sie die folgenden Befehle, um vorhandene Pakete zu aktualisieren und die Ruby-Umgebung zu installieren:

         sudo apt-get -y update
         sudo apt-get -y upgrade
         sudo apt-get -y install ruby1.9.1 ruby1.9.l-dev build-essential libsqlite3-dev nodejs curl git-core nginx

    Sobald die Installation abgeschlossen ist, prüfen Sie mit folgendem Befehl, ob Ruby erfolgreich installiert wurde:

         ruby -v

    Dies sollte die Version von Ruby zurückgeben, die auf dem virtuellen Computer installiert ist (kann eine andere als 1.9.1 sein). Beispiel: **ruby 1.9.3p194 (2012-04-20 revision 35410) [x86\_64-linux]**.

2.  Geben Sie den folgenden Befehl für die Installation von Bundler ein:

         sudo gem install bundler

    Bundler wird verwendet, um die von der Rails-Anwendung benötigten Gems zu installieren, nachdem sie auf den Server kopiert wurde.

Port 80 öffnen und Nginx testen
-------------------------------

Nginx stellt eine standardmäßige Website zur Verfügung, mit deren Hilfe Sie sicherstellen können, dass der virtuelle Computer Webdatenverkehr akzeptiert. Führen Sie die folgenden Schritte aus, um Datenverkehr über den Port 80 zuzulassen und die standardmäßige Nginx-Website zu testen.

1.  Starten Sie den Nginx-Dienst über die SSH-Sitzung mit dem virtuellen Computer:

        sudo service nginx start

    Hierdurch wird der Nginx-Dienst gestartet, der eingehenden Datenverkehr an Port 80 überwacht.

2.  Navigieren Sie im Browser zum [Azure-Verwaltungsportal](https://manage.windowsazure.com/), und wählen Sie Ihren virtuellen Computer aus.

    ![Liste der virtuellen Computer](./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/vmlist.png)

3.  Wählen Sie oben auf der Seite **ENDPUNKTE** aus, und klicken Sie dann unten auf **ENDPUNKT HINZUFÜGEN**.

    ![Seite "Endpunkte"](./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/endpoints.png)

4.  Klicken Sie im Dialogfeld **ENDPUNKT HINZUFÜGEN** auf den Pfeil unten links, um die zweite Seite anzuzeigen, und geben Sie folgende Informationen in das Formular ein:

    -   **NAME**: HTTP

    -   **PROTOKOLL**: TCP

    -   **ÖFFENTLICHER PORT**: 80

    -   **PRIVATER PORT**: 80

    Damit wird der öffentliche Port 80 erstellt, der den Datenverkehr an den privaten Port 80 weiterleitet, den Nginx abhört.

    ![Dialogfeld für neuen Endpunkt](./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/newendpoint80.png)

5.  Klicken Sie auf das Häkchen, um den Endpunkt zu speichern.

6.  Es wird eine Meldung angezeigt, dass die ****Aktualisierung ausgeführt wird. Nachdem diese Meldung ausgeblendet wurde, ist der Endpunkt aktiv. Sie können nun Ihre Anwendung testen, indem Sie zum DNS-Namen des virtuellen Computers navigieren. Die Website sollte in etwa wie folgt aussehen:

    ![Nginx-Willkommensseite](./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/welcomenginx.png)

7.  Halten Sie die Nginx-Standardwebsite mit den folgenden Befehlen an, und entfernen Sie sie:

        sudo service nginx stop
        sudo rm /etc/nginx/sites-enabled/default

    Durch die weiter unten in diesem Lernprogramm ausgeführten Bereitstellungsskripte wird "blog\_app" zur Standardwebsite von Nginx.

Vorbereiten der Bereitstellung
------------------------------

In diesem Abschnitt ändern Sie die Anwendung für die Verwendung des Unicorn-Webservers, aktivieren Capistrano für die Bereitstellung, aktivieren den GitHub-Zugriff vom virtuellen Computer und erstellen die Skripte, die zum Bereitstellen und Starten der Anwendung verwendet werden.

1.  Autorisieren Sie den virtuellen Computer zur Authentifizierung bei Ihrem GitHub-Konto anhand eines Zertifikats, indem Sie die auf der Seite [Generating SSH Keys](https://help.github.com/articles/generating-ssh-keys#platform-all) (SSH-Schlüssel erzeugen, in englischer Sprache) beschriebenen Schritte durchführen. Dies wird zum Zugreifen auf Ihr GitHub-Repository über die Bereitstellungsskripte verwendet.

    **Hinweis**

    Während der SSH-Schlüssel auf dem virtuellen Computer generiert werden muss, können Sie den Schlüssel über den Browser in der Entwicklungsumgebung zu Ihrem GitHub-Konto hinzufügen.

    Verwenden Sie den folgenden Befehl, um den Inhalt des SSH-Zertifikats anzuzeigen, damit Sie ihn kopieren und in GitHub einfügen können:

    ``` {}
    cat ~/.ssh/id_rsa.pub
    ```

2.  Ändern Sie auf dem Entwicklungscomputer die **Gemfile**, und heben Sie die Auskommentierung der Zeilen für **Capistrano** und **Unicorn** auf, indem Sie das Rautezeichen (\#) am Anfang der folgenden Zeilen entfernen:

         # gem 'unicorn'
         # gem 'capistrano'

    **Hinweis**

    Unicorn ist nicht für Windows verfügbar. Wenn Sie Windows als Entwicklungsumgebung verwenden, ändern Sie die **Gemfile** um sicherzustellen, dass sie die Installation von Unicorn nur bei der Bereitstellung auf der VM durchführt:

    ``` {.prettyprint}
    platforms :ruby do  gem 'unicorn'end
    ```

3.  Führen Sie die folgenden Befehle aus, um die neuen Gems zu installieren und Capistrano für Ihr Projekt einzurichten:

        bundle
        capify .

4.  Fügen Sie dem Verzeichnis **blog\_app/config** die folgenden Dateien hinzu, und füllen Sie die einzelnen Dateien mit dem Inhalt, den Sie unter den folgenden Links finden:

    -   [nginx.conf](https://gist.github.com/cff582f4f970a95991e9) – Konfiguriert Nginx für Verwendung mit Unicorn und zum Bereitstellen der in der Rails-Anwendung enthaltenen statischen Dateien
    -   [unicorn\_init.sh](https://gist.github.com/3272994) – Das Shellskript, mit dem der Unicorn-Serverprozess gestartet wird
    -   [unicorn.rb](https://gist.github.com/3273014) – Die Konfigurationsdatei für Unicorn

    Ersetzen Sie in jeder Datei was Wort **blogger** durch den Benutzernamen, den Sie für die Anmeldung am virtuellen Computer verwenden. Dieser Benutzer wird zum Bereitstellen der Anwendung verwendet.

5.  Bearbeiten Sie im Verzeichnis **blog\_app/config** die Datei **deploy.rb**, und ersetzen Sie ihren Inhalt durch den folgenden Inhalt:

        require "bundler/capistrano"

        set :application, "blog_app"
        set :user, "blogger"

        set :scm, :git
        set :repository, "git@github.com:YourGitHubAccount/blog_app.git"
        set :branch, "master"
        set :use_sudo, true

        server "VMDNSName", :web, :app, :db, primary: true

        set :deploy_to, "/home/#{user}/apps/#{application}"
        default_run_options[:pty] = true
        ssh_options[:forward_agent] = true
        ssh_options[:port] = SSHPort

        namespace :deploy do
          desc "Fix permissions"
          task :fix_permissions, :roles => [ :app, :db, :web ] do
            run "chmod +x #{release_path}/config/unicorn_init.sh"
          end

          %w[start stop restart].each do |command|
            desc "#{command} unicorn server"
            task command, roles: :app, except: {no_release: true} do
              run "service unicorn_#{application} #{command}"
            end
          end

          task :setup_config, roles: :app do
            sudo "ln -nfs #{current_path}/config/nginx.conf /etc/nginx/sites-enabled/#{application}"
            sudo "ln -nfs #{current_path}/config/unicorn_init.sh /etc/init.d/unicorn_#{application}"
            sudo "mkdir -p #{shared_path}/config"
          end
          after "deploy:setup", "deploy:setup_config"

          task :symlink_config, roles: :app do
            # Add database config here
          end
          after "deploy:finalize_update", "deploy:fix_permissions"
          after "deploy:finalize_update", "deploy:symlink_config"
        end

    Nehmen Sie in dem oben aufgeführten Text die folgenden Ersetzungen vor:

    -   **YourGitHubAccount** durch Ihren GitHub-Kontonamen
    -   **VMDNSName** durch den DNS des virtuellen Azure-Computers
    -   **blogger** durch den Benutzernamen, der zur Anmeldung beim virtuellen Computer verwendet wird
    -   **SSHPort** durch den externen SSH-Port für den virtuellen Computer

    **Hinweis**

    Wenn es sich bei Ihrer Entwicklungsumgebung um ein Windows-System handelt, müssen Sie die folgende Zeile in die Datei **deploy.rb** einfügen. Platzieren Sie diese Zeile zusammen mit den anderen **set**-Anweisungen am Anfang der Datei:

    ``` {}
    set :bundle_flags, "--no-deployment --quiet"
    ```

    Dies ist nicht die bevorzugte Vorgehensweise, da hierdurch die Gems während der Bereitstellung aus der Gemfile und nicht aus Gemfile.lock geladen werden. Sie ist jedoch erforderlich, da sich das Zielsystem (Linux) vom Entwicklungssystem (Windows) unterscheidet.

6.  Heben Sie die Auskommentierung der Objektzeile in der **Capfile** aus, die sich im Verzeichnis **blog\_app** befindet.

        load 'deploy/assets'

7.  Führen Sie die folgenden Befehle aus, um die Änderungen für das Projekt zu übernehmen und sie in GitHub hochzuladen.

        git add .
        git commit -m "adding config files"
        git push

Bereitstellen
-------------

1.  Führen Sie auf dem lokalen Entwicklungscomputer den folgenden Befehl aus, um die virtuelle Azure-Remote-VM zur Bereitstellung einzurichten.

        cap deploy:setup

    Geben Sie bei Aufforderung das Kennwort für das Benutzerkonto der VM ein. Capistrano stellt eine Verbindung zur VM her und erstellt ein **apps**-Verzeichnis im Homeverzeichnis des Benutzers.

2.  Ändern Sie über eine SSH-Verbindung mit der Azure-VM die Berechtigungen des **app**-Verzeichnisses, indem Sie den folgenden Befehl verwenden:

        sudo chmod -R 777 apps

    **Hinweis**

    Diese Vorgehensweise wird nicht für Produktionsumgebungen empfohlen und wird hier nur zu Demonstrationszwecken verwendet.

3.  Führen Sie eine "kalte" Bereitstellung durch, indem Sie in der Entwicklungsumgebung den folgenden Befehl verwenden. Hierdurch wird die Anwendung auf dem virtuellen Computer bereitgestellt, und der Unicorn-Dienst wird gestartet.

        cap deploy:cold

4.  Starten Sie den Nginx-Dienst, der den Datenverkehr dann an Unicorn umleiten und statische Inhalte bereitstellt:

        sudo service nginx start

An diesem Punkt sollte Ihre Ruby on Rails-Anwendung auf Ihrem virtuellen Azure-Computer ausgeführt werden. Um dies zu überprüfen, geben Sie den DNS-Namen des virtuellen Computers in den Webbrowser ein. Beispiel: http://railsvm.cloudapp.net. Die Willkommensseite sollte angezeigt werden:

![Willkommensseite](./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/basicrailscloud.png)

Wenn Sie "/posts" an die URL anfügen, sollte der Postindex angezeigt werden, und Sie sollten in der Lage sein, Post zu erstellen, zu bearbeiten und zu löschen.

Nächste Schritte
----------------

In diesem Artikel haben Sie erfahren, wie Sie eine grundlegende formularbasierte Rails-Anwendung erstellen und auf einem virtuellen Azure-Computer mit Capistrano veröffentlichen. Der virtuelle Computer verwendet Unicorn und Nginx zum Verarbeiten von Webanforderungen an die Anwendung.

Ein einfaches Beispiel zum Erstellen und Bereitstellen einer Rails-Anwendung auf einer Azure-VM, bei dem nur SSH verwendet wird, finden Sie unter [Hosten einer Ruby on Rails-Webanwendung mit einem virtuellen Linux-Computer](/en-us/develop/ruby/tutorials/web-app-with-linux-vm/).

Wenn Sie mehr über Ruby on Rails erfahren möchten, rufen Sie die Seite [Ruby on Rails Guides](http://guides.rubyonrails.org/) auf.

Informationen zur Verwendung des Azure SDK für Ruby zum Zugriff auf Azure-Dienste von der Ruby-Anwendung aus finden Sie unter:

-   [Speichern von unstrukturierten Daten mit Blobs](/en-us/develop/ruby/how-to-guides/blob-storage/)

-   [Speichern von Schlüssel/Wert-Paaren mit Tabellen](/en-us/develop/ruby/how-to-guides/table-service/)

-   [Bereitstellen von breitbandigen Inhalten über das Content Delivery Network](/en-us/develop/ruby/app-services/)


