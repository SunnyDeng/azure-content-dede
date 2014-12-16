# Veröffentlichen auf Azure-Websites mit Git

Azure-Websites unterstützen die fortlaufende Bereitstellung über Quellcodeverwaltungs- und Repositorytools wie BitBucket, CodePlex, Dropbox, Git, GitHub, Mercurial und TFS. Sie können diese Tools verwenden, um den Inhalt und den Code für Ihre Website zu verwalten und bei Bedarf schnell und einfach Änderungen an Ihre Website mithilfe von Push zu übertragen.

In diesem Artikel erfahren Sie, wie Sie mit Git direkt von Ihrem lokalen Computer aus Inhalte auf einer Azure-Website veröffentlichen (in Azure wird diese Methode der Veröffentlichung **Local Git** genannt). Außerdem wird erläutert, wie Sie die fortlaufende Bereitstellung von Repositorywebsites aus ermöglichen, z. B. von BitBucket, CodePlex, Dropbox, GitHub oder Mercurial. Informationen zum Verwenden von TFS für die fortlaufende Bereitstellung finden Sie unter [Continuous delivery to Azure using Visual Studio Online] (Fortlaufende Bereitstellung für Azure mithilfe von Visual Studio Online, in englischer Sprache).

> [WACOM.NOTE] Viele der in diesem Artikel beschriebenen Git-Befehle werden automatisch ausgeführt, wenn Sie eine Website mit den  <a href="/de-de/develop/nodejs/how-to-guides/command-line-tools/">Azure-Befehlszeilentools für Mac und Linux</a> erstellen.

Die Aufgabe umfasst die folgenden Schritte:

* [Installieren von Git](#Step1)
* [Erstellen eines lokalen Repositorys](#Step2)
* [Hinzufügen einer Webseite](#Step3)
* [Aktivieren des Website-Repositorys](#Step4)
* [Bereitstellen des Projekts](#Step5)
	* [Übertragen lokaler Dateien zu Azure mithilfe von Push (Lokales Git)](#Step6)
	* [Bereitstellen von Dateien aus einer Repository-Website wie BitBucket, CodePlex, Dropbox, GitHub oder Mercurial](#Step7)
	* [Bereitstellen eines Visual Studio-Projekts aus BitBucket, CodePlex, Dropbox, GitHub oder Mercurial](#Step75)
* [Problembehandlung](#Step8)

<h2><a id="Step2"></a>Installieren von Git</h2>

Die erforderlichen Schritte zum Installieren von Git variieren je nach Betriebssystem. Informationen zu betriebssystemspezifischen Distributionen und zur Installation finden Sie unter [Installieren von Git].

> [WACOM.NOTE] In einigen Betriebssystemen sind eine Befehlszeilenversion und eine GUI-Version von Git verfügbar. In den in diesem Artikel bereitgestellten Anweisungen wird die Befehlszeilenversion verwendet.

<h2><a id="Step2"></a>Erstellen eines lokalen Repositorys</h2>

Führen Sie die folgenden Aufgaben durch, um ein neues Git-Repository zu erstellen.

1. Erstellen Sie ein Verzeichnis mit dem Namen "MyGitRepository" für Ihr Git-Repository und die Dateien Ihrer Website.

2. Öffnen Sie eine Befehlszeile, z. B. **GitBash** (Windows) oder **Bash** (Unix Shell). Auf OS X-Systemen können Sie über die **Terminal**-Anwendung auf die Befehlszeile zugreifen.

3. Wechseln Sie in der Befehlszeile in das Verzeichnis "MyGitRepository".

		cd MyGitRepository

4. Verwenden Sie den folgenden Befehl, um ein neues Git-Repository zu initialisieren:

		git init

	Damit sollte eine Meldung wie **Leeres Git-Repository initialisiert unter [Pfad] [Pfad]** zurückgegeben werden.

<h2><a id="Step3"></a>Hinzufügen einer Webseite</h2>

Azure-Websites unterstützen Anwendungen, die in verschiedenen Programmiersprachen erstellt wurden. In diesem Beispiel verwenden Sie eine statische HTML-Datei. Informationen zum Veröffentlichen von Websites in anderen Programmiersprachen auf Azure finden Sie im [Azure Developer Center].

1. Erstellen Sie mithilfe eines Text-Editors eine neue Datei namens **index.html** im Stammverzeichnis des Git-Repositorys (das vorher von Ihnen erstellte Verzeichnis "MyGitRepository").

2. Geben Sie den folgenden Text als Inhalt der Datei "index.html" ein, und speichern Sie die Datei.

		Hello Git!

3. Überprüfen Sie an der Befehlszeile, ob das Stammverzeichnis Ihres Git-Repositorys das aktuelle Verzeichnis ist. Verwenden Sie dann folgenden Befehl, um dem Repository die Datei **index.html** hinzuzufügen:

		git add index.html 

	> [WACOM.NOTE] Sie können Hilfeinformationen für jeden Git-Befehl aufrufen, indem Sie nach dem Befehl "-help" oder "--help" eingeben. Geben Sie als Parameteroptionen für den Befehl "add" beispielsweise "git add -help" ein, um Befehlszeilenhilfe zu erhalten, oder "git add --help", um ausführlichere Hilfe anzuzeigen.

4. Übernehmen Sie dann die Änderungen am Repository mithilfe des folgenden Befehls:

		git commit -m "Adding index.html to the repository"

	Eine Ausgabe ähnlich der folgenden sollte angezeigt werden:

		[master (root-commit) 369a79c] Adding index.html to the repository
		 1 file changed, 1 insertion(+)
		 create mode 100644 index.html

<h2><a id="Step4"></a>Aktivieren des Websiterepositorys</h2>

Führen Sie die folgenden Schritte durch, um ein Git-Repository für Ihre Website mithilfe des Azure-Portals zu aktivieren:

1. Melden Sie sich beim [Azure-Portal] an.

2. Klicken Sie auf die Schaltfläche NEU, um eine neue Website zu erstellen, für die Sie dann ein Repository aktivieren.

2. 	Warten Sie, bis der Erstellungsprozess der Website in der Ansicht **Websites** abgeschlossen ist, und wählen Sie dann die Website aus.

	![An image displaying a selected web site][portal-select-website]

3. Wählen Sie die Registerkarte  **DASHBOARD** aus.

4. Wählen Sie im Abschnitt **Auf einen Blick** den Eintrag **Bereitstellung über Quellcodeverwaltung einrichten**.  Das folgende Dialogfeld **BEREITSTELLUNG EINRICHTEN** wird angezeigt.

	![git-WhereIsYourSourceCode][Git-WhereIsYourSourceCode]

4. Wählen Sie **Lokales Git**, und klicken Sie dann auf den Pfeil für **Weiter**.

4. Wenn Sie zum ersten Mal ein Repository in Azure einrichten, müssen Sie dafür Anmeldeinformationen erstellen. Sie verwenden diese, um eine Anmeldung beim Azure-Repository vorzunehmen und Änderungen aus Ihrem lokalen Git-Repository mithilfe von Push zu übertragen. 

	![](./media/publishing-with-git/git_credentials.png)
	
5. Nach einer kurzen Verzögerung wird die Meldung angezeigt, dass Ihr Repository bereit ist. 

	![git-instructions][Git-Anweisungen]

<h2><a id="Step5"></a>Bereitstellen des Projekts</h2>

<h3><a id="Step6"></a>Übertragen lokaler Dateien zu Azure mithilfe von Push (Lokales Git)</h3>

An diesem Punkt werden im Portal Anweisungen zum Initialisieren eines lokalen Repositorys und zum Hinzufügen von Dateien angezeigt. Sie haben dies bereits in den vorherigen Schritten dieses Themas durchgeführt. Wenn Sie noch keine Anmeldeinformationen für die Bereitstellung eingerichtet haben, müssen Sie im Portal zur Registerkarte **DASHBOARD** zurückkehren und auf **Anmeldeinformationen für die Bereitstellung zurücksetzen** klicken.

Führen Sie die folgenden Schritte durch, um Ihre Website mit einem lokalen Git auf Azure zu veröffentlichen:

1. Stellen Sie unter Verwendung der Befehlszeile sicher, dass das Stammverzeichnis Ihres lokalen Git-Repositorys, das die vorher erstellte Datei "index.html" enthält, das aktuelle Verzeichnis ist.

2. Kopieren Sie den Befehl "git remote add", der in Schritt 3 der vom Portal angezeigten Anweisungen aufgeführt ist. Dieser ähnelt dem folgenden Befehl:

		git remote add azure https://username@needsmoregit.scm.azurewebsites.net:443/NeedsMoreGit.git

    > [WACOM.NOTE] Der Befehl **remote** fügt einem Remoterepository einen benannten Verweis hinzu. n diesem Beispiel wird ein Verweis namens 'azure' für das Repository Ihrer Azure-Website erstellt.

1. Verwenden Sie Folgendes an der Befehlszeile, um die aktuellen Repository-Inhalte aus dem lokalen Repository per Push in das "azure"-Remote-Repository zu übertragen:

		git push azure master

	Sie werden zur Eingabe des Kennworts aufgefordert, das Sie zuvor beim Zurücksetzen Ihrer Anmeldeinformationen für die Bereitstellung im Portal erstellt haben. Geben Sie das Kennwort ein (beachten Sie, dass Gitbash keine Sternchen in der Konsole anzeigt, wenn Sie Ihr Kennwort eingeben). Eine Ausgabe ähnlich der folgenden sollte angezeigt werden:

		Counting objects: 6, done.
		Compressing objects: 100% (2/2), done.
		Writing objects: 100% (6/6), 486 bytes, done.
		Total 6 (delta 0), reused 0 (delta 0)
		remote: New deployment received.
		remote: Updating branch 'master'.
		remote: Preparing deployment for commit id '369a79c929'.
		remote: Preparing files for deployment.
		remote: Deployment successful.
		To https://username@needsmoregit.scm.azurewebsites.net:443/NeedsMoreGit.git
		* [new branch]		master -> master

	> [WACOM.NOTE] Das für Ihre Azure-Website erstellte Repository erwartet Pushanforderungen für den <strong>master</strong>-Pfad des Repositorys, die dann als Inhalt der Website verwendet werden.

2. Klicken Sie unten im Portal auf den Link **DURCHSUCHEN**, um zu überprüfen, ob **index.html** bereitgestellt wurde. Eine Seite mit "Hallo Git!" wird angezeigt.

	![A webpage containing 'Hello Git!'][hello-git]

3. Ändern Sie die Datei **index.html** mit einem Texteditor, sodass sie den Eintrag'Yay!' enthält, und speichern Sie dann die Datei.

4. 	Verwenden Sie die folgenden Befehle an der Befehlszeile, um die Änderungen mit **add** hinzuzufügen und ein **Commit** auszuführen. Senden Sie die Änderungen dann mit **push** an das Remoterepository:

		git add index.html
		git commit -m "Celebration"
		git push azure master

	Nach Abschluss des **push**-Befehls aktualisieren Sie den Browser (möglicherweise müssen Sie Strg+F5 drücken, um den Browser ordnungsgemäß zu aktualisieren). Beachten Sie, dass der Inhalt der Seite jetzt die letzte übernommene Änderung widerspiegelt.

	![A webpage containing 'Yay!'][yay]

<h3><a id="Step7"></a>Bereitstellen von Dateien aus einer Repository-Website wie BitBucket, CodePlex, Dropbox, GitHub oder Mercurial</h3>

Wenn Sie lokales Git verwenden, um lokale Dateien mithilfe von Push an Azure zu übertragen, können Sie Updates aus einem lokalen Projekt manuell mithilfe von Push an Ihre Azure-Website übertragen. Im Gegensatz dazu ergibt sich bei der Bereitstellung aus BitBucket, CodePlex, Dropbox, GitHub oder Mercurial ein fortlaufender Bereitstellungsprozess, bei dem Azure mithilfe von Pull die aktuellen Updates aus Ihrem Projekt überträgt.

Zwar führen beide Methoden dazu, dass Ihr Projekt auf einer Azure-Website bereitgestellt wird, die fortlaufende Bereitstellung ist jedoch nützlich, wenn mehrere Personen an einem Projekt arbeiten und Sie sicherstellen möchten, dass die aktuelle Version stets veröffentlicht wird, unabhängig davon, wer das letzte Update durchgeführt hat. Die fortlaufende Bereitstellung bietet sich auch an, wenn Sie eines der oben erwähnten Tools als zentrales Repository für Ihre Anwendung verwenden.

Das Bereitstellen von Dateien über GitHub, CodePlex oder BitBucket setzt voraus, dass Sie Ihr lokales Projekt auf einem dieser Dienste veröffentlicht haben. Weitere Informationen zum Veröffentlichen Ihres Projekts auf diesen Diensten finden Sie unter [Erstellen eines Repositorys (GitHub)], [Verwenden von Git mit CodePlex], [Erstellen eines Repositorys (BitBucket)], [Verwenden von Dropbox zum Freigeben von Git-Repositorys] oder [Schnellstart - Mercurial].

1. Kopieren Sie die Dateien Ihrer Website zunächst in das ausgewählte Repository, das für die fortlaufende Bereitstellung verwendet wird.

2. 	Gehen Sie im Azure-Portal zur Registerkarte **DASHBOARD** für Ihre Website. Wählen Sie im Abschnitt **Auf einen Blick** die Option **Bereitstellung über Quellcodeverwaltung einrichten** aus.  Das Dialogfeld **Bereitstellung einrichten** wird mit einer Frage **bezüglich des Speicherorts Ihres Quellcodes ** angezeigt. 

2. Wählen Sie die Quellcodeverwaltungsmethode aus, die Sie für die fortlaufende Bereitstellung verwenden möchten.
	
3. Geben Sie bei Aufforderung die Anmeldeinformationen für den ausgewählten Dienst ein.

4. Nachdem Sie Azure zum Zugriff auf Ihr Konto autorisiert haben, wird eine Liste mit Repositorys angezeigt. 

	![git-ChooseARepositoryToDeploy][git-ChooseARepositoryToDeploy]
  
5. Wählen Sie das Repository aus, das Sie Ihrer Azure-Website zuordnen möchten. Klicken Sie auf das Häkchen, um fortzufahren.

	> [WACOM.NOTE] Wenn Sie die fortlaufende Bereitstellung mit GitHub oder BitBucket aktivieren, werden öffentliche und private Projekte angezeigt.

6. Azure erstellt eine Zuordnung für das ausgewählte Repository und überträgt die Dateien mit Pull aus dem master-Zweig. Nach Abschluss dieses Prozesses wird im **Bereitstellungsverlauf** auf der Seite **Bereitstellungen** eine Meldung **Aktive Bereitstellung** angezeigt, die der folgenden Meldung ähnelt:

	![git-githubdeployed][git-githubdeployed]

7. An diesem Punkt wurde Ihr Projekt aus dem Repository Ihrer Wahl auf Ihrer Azure-Website bereitgestellt. Um zu überprüfen, ob die Website aktiv ist, klicken Sie unten im Portal auf den Link **Durchsuchen**. Der Browser sollte zur Website wechseln.

8. Um zu überprüfen, ob die fortlaufende Bereitstellung stattfindet, nehmen Sie eine Änderung am Projekt vor und übertragen das Update mithilfe von Push in das Repository, das Sie dieser Website zugeordnet haben. Ihre Website sollte aktualisiert werden, um die Änderung kurz nach dem Push ins Repository wiederzugeben. Sie können auf der Seite **Bereitstellungen** Ihrer Website überprüfen, ob das Update abgerufen wurde.

	![git-GitHubDeployed-Updated][git-GitHubDeployed-Updated]

<h3><a id="Step75"></a>Bereitstellen eines Visual Studio-Projekts aus BitBucket, CodePlex, Dropbox, GitHub oder Mercurial</h3>

Eine Visual Studio-Projektmappe lässt sich genauso leicht mithilfe von Push an eine Azure-Website übertragen wie eine einfache index.html-Datei. Der Azure-Websites-Bereitstellungsprozess optimiert alle Details, darunter NuGet-Wiederherstellungsabhängigkeiten und die Erstellung der Binärdateien der Anwendung. Sie können die Best Practices der Quellcodeverwaltung befolgen und Code ausschließlich in Ihrem Git-Repository verwalten und den Rest von der Azure-Websites-Bereitstellung erledigen lassen.

Die Schritte zur Übertragung der Visual Studio-Projektmappe mithilfe von Push an eine Azure-Website sind mit denen im [vorherigen Abschnitt](#Step7)identisch, vorausgesetzt, dass Sie Ihre Projektmappe und das Repository wie folgt konfigurieren:

-		Fügen Sie dem Stammverzeichnis Ihres Repositorys eine`.gitignore`-Datei hinzu, und geben Sie alle Dateien und Ordner an, die Sie aus Ihrem Repository ausschließen möchten, beispielsweise die Ordner `Obj`, `Bin` und `packages` (Informationen zum Format finden Sie in der  [gitignore-Dokumentation](http://git-scm.com/docs/gitignore)). Beispiel:

		[Oo]bj/
		[Bb]in/
		*.user
		/TestResults
		*.vspscc
		*.vssscc
		*.suo
		*.cache
		*.csproj.user
		packages/*
		App_Data/
		/apps
		msbuild.log
		_app/
		nuget.exe

	>[WACOM.NOTE] Bei Verwendung von GitHub können Sie optional eine Visual Studio-spezifische .gitignore-Datei generieren, wenn Sie Ihr Repository erstellen. Diese enthält alle allgemeinen temporären Dateien, Buildergebnisse usw. und kann an Ihre speziellen Anforderungen angepasst werden.

-	Fügen Sie Ihrem Repository die gesamte Verzeichnisstruktur des Projekts hinzu, wobei sich die .sln-Datei im Stammverzeichnis des Repositorys befinden muss.

-	Damit Visual Studio automatisch fehlende Pakete wiederherstellt, [aktivieren Sie die NuGet-Paketwiederherstellung](http://docs.nuget.org/docs/workflows/using-nuget-without-committing-packages) in Ihrer Visual Studio-Projektmappe.

Nachdem Sie Ihr Repository wie beschrieben eingerichtet und Ihre Azure-Website für fortlaufende Veröffentlichung aus einem der Online-Git-Repositorys konfiguriert haben, können Sie Ihre ASP.NET-Anwendung lokal in Visual Studio entwickeln und Ihren Code fortlaufend bereitstellen, indem Sie einfach Ihre Änderungen mithilfe von Push an Ihr Online-Git-Repository übertragen.

<h4>Funktionsweise der fortlaufenden Bereitstellung</h4>
Die fortlaufende Bereitstellung verwendet die Angabe der **BEREITSTELLUNGSAUSLÖSER-URL** im Abschnitt **Bereitstellungen** der Registerkarte **Konfigurieren** Ihrer Website.

![git-DeploymentTrigger][git-DeploymentTrigger]

Wenn Updates am Repository vorgenommen werden, wird eine POST-Anforderung an diese URL gesendet, die Ihre Azure-Website darüber informiert, dass das Repository aktualisiert wurde. An diesem Punkt wird die Aktualisierung abgerufen und auf Ihrer Website bereitgestellt.

Weitere Informationen zum Modul hinter dem Git-Bereitstellungsprozess für Azure-Websites finden Sie unter [Project Kudu](https://github.com/projectkudu/kudu/wiki).

<h4>Angeben der zu verwendenden Verzweigung</h4>

Wenn Sie die fortlaufende Bereitstellung aktivieren, verwendet sie standardmäßig den **master**-Zweig des Repositorys. Wenn Sie eine andere Verzweigung verwenden möchten, führen Sie die folgenden Schritte aus:

1. Wählen Sie im Portal Ihre Website und dann **KONFIGURIEREN** aus.

2. Geben Sie im Abschnitt **Bereitstellungen** er Seite im Feld **BEREITZUSTELLENDE VERZWEIGUNG** die Verzweigung ein, die Sie verwenden möchten, und drücken Sie die Eingabetaste. Klicken Sie zuletzt auf **SPEICHERN**.

	Azure sollte das Update basierend auf Änderungen an der neuen Verzweigung sofort beginnen.

<h4>Deaktivieren der fortlaufenden Bereitstellung</h4>

Die fortlaufende Bereitstellung kann im Azure-**Dashboard** deaktiviert werden.. Wählen Sie im Abschnitt **Auf einen Blick** die Option zum Trennen des derzeit verwendeten Repository aus:

![git-DisconnectFromGitHub][git-DisconnectFromGitHub]	

Nachdem Sie die Bestätigungsmeldung mit **Ja** beantwortet haben, kehren Sie zu **Auf einen Blick** zurück, und klicken Sie auf **Bereitstellung über Quellcodeverwaltung einrichten**, wenn Sie die Veröffentlichung von einer anderen Quelle aus einrichten möchten.

<h2><a id="Step8"></a>Problembehandlung</h2>

Die folgenden Fehler und Probleme treten häufiger auf, wenn Git zum Veröffentlichen auf einer Azure-Website verwendet wird:

****

**Symptom**: Unable to access '[siteURL]': Failed to connect to [scmAddress]

**Ursache**: Dieser Fehler kann auftreten, wenn die Website nicht ausgeführt wird.

**Lösung**: Starten Sie die Website im Azure-Portal. Die Git-Bereitstellung funktioniert nur dann, wenn die Website ausgeführt wird. 


****

**Symptom**: Couldn't resolve host 'hostname'

**Ursache**: Dieser Fehler kann auftreten, wenn die Adressinformationen, die beim Erstellen der 'azure'-Remotewebsite eingegeben wurden, falsch waren.

**Lösung**: Verwenden Sie den Befehl `git remote -v`, um alle Remote-Websites zusammen mit der jeweils zugehörigen URL aufzulisten. Überprüfen Sie, ob die URL für die 'azure'-Remotewebsite korrekt ist. Entfernen Sie diese Remote-Website bei Bedarf, und erstellen Sie sie mit der korrekten URL neu.

****

**Symptom**: No refs in common and none specified; doing nothing. Sie sollten vielleicht eine Verzweigung wie 'master' angeben.

**Ursache**: Dieser Fehler kann auftreten, wenn Sie beim Ausführen eines Git-Push-Vorgangs keine Verzweigung angeben und den von Git verwendeten "push.default"-Wert nicht festgelegt haben.

**Lösung**: Führen Sie den Push-Vorgang unter Angabe der Hauptverzweigung erneut durch. Beispiel:

	git push azure master

****

**Symptom**: src refspec [branchname] does not match any.

**Ursache**: Dieser Fehler kann auftreten, wenn Sie versuchen, etwas per Push auf eine andere Verzweigung als die Hauptverzweigung auf der 'azure'-Remotewebsite zu übertragen.

**Lösung**: Führen Sie den Push-Vorgang unter Angabe der Hauptverzweigung erneut durch. Beispiel:

	git push azure master

****

**Symptom**: Error - Changes commited to remote repository but your website not updated.

**Ursache**: Dieser Fehler kann auftreten, wenn Sie eine "Node.js"-Anwendung mit einer Datei "package.json" bereitstellen, die zusätzliche erforderliche Module angibt.

**Lösung**: Zusätzliche Meldungen mit "npm ERR!" sollten vor diesem Fehler protokolliert werden und können zusätzlichen Kontext für diesen Fehler bereitstellen. Es folgen bekannte Ursachen für diesen Fehler und die entsprechende "npm ERR!"-Meldung:

* **Fehlerhafte "package.json"-Datei**: npm ERR! Couldn't read dependencies.

* **Systemeigenes Modul ohne binäre Distribution für Windows**:

	* npm ERR! \`cmd "/c" "node-gyp rebuild"\` failed with 1

		OR

	* npm ERR! [modulename@version] preinstall: \`make || gmake\`


## Zusätzliche Ressourcen

* [Verwenden von PowerShell für Azure]
* [Verwenden des Azure-Befehlszeilentools für Mac und Linux]
* [Git-Dokumentation]
* [Project Kudu](https://github.com/projectkudu/kudu/wiki)

[Azure Developer Center]: http://www.windowsazure.com/de-de/develop/overview/
[Azure-Portal]: http://manage.windowsazure.com
[Git-Website]: http://git-scm.com
[Installieren von Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Verwenden von PowerShell für Azure]: http://www.windowsazure.com/de-de/develop/nodejs/how-to-guides/powershell-cmdlets/
[Verwenden des Azure-Befehlszeilentools für Mac und Linux]: /de-de/develop/nodejs/how-to-guides/command-line-tools/
[Git-Dokumentation]: http://git-scm.com/documentation

[portal-select-website]: ./media/publishing-with-git/git-select-website.png
[Git-WhereIsYourSourceCode]: ./media/publishing-with-git/git-WhereIsYourSourceCode.png
[Git-Anweisungen]: ./media/publishing-with-git/git-instructions.png
[portal-deployment-credentials]: ./media/publishing-with-git/git-deployment-credentials.png

[git-ChooseARepositoryToDeploy]: ./media/publishing-with-git/git-ChooseARepositoryToDeploy.png
[hello-git]: ./media/publishing-with-git/git-hello-git.png
[yay]: ./media/publishing-with-git/git-yay.png
[git-githubdeployed]: ./media/publishing-with-git/git-GitHubDeployed.png
[git-GitHubDeployed-Updated]: ./media/publishing-with-git/git-GitHubDeployed-Updated.png
[git-DisconnectFromGitHub]: ./media/publishing-with-git/git-DisconnectFromGitHub.png
[git-DeploymentTrigger]: ./media/publishing-with-git/git-DeploymentTrigger.png
[Erstellen eines Repositorys (GitHub)]: https://help.github.com/articles/create-a-repo
[Verwenden von Git mit CodePlex]: http://codeplex.codeplex.com/wikipage?title=Using%20Git%20with%20CodePlex&referringTitle=Source%20control%20clients&ProjectName=codeplex
[Erstellen eines Repositorys (BitBucket)]: https://confluence.atlassian.com/display/BITBUCKET/Create+an+Account+and+a+Git+Repo
[Schnellstart - Mercurial]: http://mercurial.selenic.com/wiki/QuickStart
[Verwenden von Dropbox zum Freigeben von Git-Repositorys]: https://gist.github.com/trey/2722927
[Fortlaufende Bereitstellung für Azure mithilfe von Visual Studio Online]: http://www.windowsazure.com/de-de/develop/net/common-tasks/publishing-with-tfs/
