[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) unterstützt die fortlaufende Bereitstellung von Quellcodeverwaltungs- und Repositorytools wie BitBucket, CodePlex, Dropbox, Git, GitHub, Mercurial und TFS in Web-Apps. Sie können diese Tools verwenden, um den Inhalt und den Code für Ihre Anwendung zu verwalten und bei Bedarf schnell und einfach Änderungen an Ihrer Azure-Web-App vorzunehmen.

In diesem Artikel erfahren Sie, wie Sie mithilfe von Git direkt von Ihrem lokalen Computer aus in Web-Apps veröffentlichen (in Azure wird diese Methode der Veröffentlichung **lokales Git** genannt). Außerdem wird erläutert, wie Sie die fortlaufende Bereitstellung von Repositorywebsites aus ermöglichen, z. B. von BitBucket, CodePlex, Dropbox, GitHub oder Mercurial. Informationen zur Verwendung von TFS für die fortlaufende Bereitstellung finden Sie unter [Continuous delivery to Azure using Visual Studio Team Services] (Fortlaufende Bereitstellung für Azure mithilfe von Visual Studio Team Services).

> [AZURE.NOTE] Viele der in diesem Artikel beschriebenen Git-Befehle werden automatisch ausgeführt, wenn Sie eine Web-App mit den [Azure-Befehlszeilentools für Mac und Linux](/develop/nodejs/how-to-guides/command-line-tools/) erstellen

## <a id="Step1"></a>Schritt 1: Installieren von Git

Die erforderlichen Schritte zum Installieren von Git variieren je nach Betriebssystem. Informationen zu Betriebssystem-spezifischen Distributionen und zur Installation finden Sie unter [Installieren von Git].

> [AZURE.NOTE] Einige Betriebssysteme verfügen über eine Befehlszeilenversion und eine GUI-Version von Git. Die in diesem Artikel bereitgestellten Anweisungen verwenden die Befehlszeilenversion.

## <a id="Step2"></a>Schritt 2: Erstellen eines lokalen Repositorys

Führen Sie die folgenden Aufgaben durch, um ein neues Git-Repository zu erstellen.

1. Erstellen Sie ein Verzeichnis mit dem Namen "MyGitRepository" für Ihr Git-Repository und die Dateien Ihrer Web-App.

2. Öffnen Sie eine Befehlszeile, wie zum Beispiel **GitBash** (Windows) oder **Bash** (Unix Shell). Auf OS X-Systemen können Sie auf die Befehlszeile über die **Terminal**-Anwendung zugreifen.

3. Wechseln Sie in der Befehlszeile in das Verzeichnis "MyGitRepository".

		cd MyGitRepository

4. Verwenden Sie den folgenden Befehl, um ein neues Git-Repository zu initialisieren.

		git init

	Eine Meldung wie **Leeres Git-Repository unter [Pfad] initialisiert** sollte zurückgegeben werden.

## <a id="Step3"></a>Schritt 3:Hinzufügen einer Webseite

Web-Apps unterstützen in verschiedenen Programmiersprachen erstellte Anwendungen. In diesem Beispiel verwenden Sie eine statische HTML-Datei.

1. Erstellen Sie mithilfe eines Text-Editors eine neue Datei mit dem Namen **index.html** im Stammverzeichnis des Git-Repositorys (das vorher von Ihnen erstellte Verzeichnis MyGitRepository).

2. Geben Sie den folgenden Text als Inhalt der Datei index.html ein, und speichern Sie die Datei.

		Hello Git!

3. Stellen Sie mit der Befehlszeile sicher, dass Sie sich im Stammverzeichnis Ihres Git-Repositorys befinden. Verwenden Sie dann folgenden Befehl, um dem Repository die Datei **index.html** hinzuzufügen:

		git add index.html 

	> [AZURE.NOTE] Sie können Hilfeinformationen für jeden Git-Befehl aufrufen, indem Sie nach dem Befehl "-help" oder "--help" eingeben. Geben Sie als Parameteroptionen für den Befehl "add" beispielsweise "git add -help" ein, um Befehlszeilenhilfe zu erhalten, oder "git add --help", um ausführlichere Hilfe anzuzeigen.

4. Übernehmen Sie dann die Änderungen am Repository mithilfe des folgenden Befehls:

		git commit -m "Adding index.html to the repository"

	Eine Ausgabe ähnlich der folgenden sollte angezeigt werden:

		[master (root-commit) 369a79c] Adding index.html to the repository
		 1 file changed, 1 insertion(+)
		 create mode 100644 index.html

## <a id="Step4"></a>Aktivieren des Web-App-Repositorys

Führen Sie die folgenden Schritte durch, um ein Git-Repository für Ihre Web-App zu aktivieren.

1. Melden Sie sich beim [Azure-Portal] an.

2. Klicken Sie auf dem Blatt der Web-App auf **Einstellungen > Dauerhafte Bereitstellung**. Klicken Sie auf **Quelle auswählen**, dann auf **Lokales Git-Repository** und schließlich auf **OK**.

	![Lokales Git-Repository](./media/publishing-with-git/azure1-local-git.png)

4. Wenn Sie zum ersten Mal ein Repository in Azure einrichten, müssen Sie dafür Anmeldeinformationen erstellen. Sie verwenden diese, um eine Anmeldung beim Azure-Repository vorzunehmen und Änderungen aus Ihrem lokalen Git-Repository mithilfe von Push zu übertragen. Klicken Sie auf dem Blatt Ihrer Web-App auf **Einstellungen > Anmeldeinformationen für die Bereitstellung**, und konfigurieren Sie dann den Benutzernamen und das Kennwort für die Bereitstellung. Wenn Sie fertig sind, klicken Sie auf **OK**.

	![](./media/publishing-with-git/azure2-credentials.png)

## <a id="Step5"></a>Bereitstellen des Projekts

* [Übertragen lokaler Dateien zu Azure mithilfe von Push (lokales Git)](#Step6)
* [Bereitstellen von Dateien aus einer Repository-Website wie BitBucket, CodePlex, Dropbox, GitHub oder Mercurial](#Step7)
* [Bereitstellen von Visual Studio-Projekten aus BitBucket, CodePlex, Dropbox, GitHub oder Mercurial](#Step75)

Führen Sie die folgenden Schritte durch, um Ihre Web-App mit einem lokalen Git in Azure zu veröffentlichen:

1. Klicken Sie auf dem Blatt der Web-App für die **Git-URL** auf **Einstellungen > Eigenschaften**.

	![](./media/publishing-with-git/azure3-repo-details.png)

	Die **Git-URL** ist die Remotereferenz für die Bereitstellung aus dem lokalen Repository. Sie verwenden diese URL in den folgenden Schritten.

1. Stellen Sie unter Verwendung der Befehlszeile sicher, dass das Stammverzeichnis Ihres lokalen Git-Repositorys, das die vorher erstellte Datei "index.html" enthält, das aktuelle Verzeichnis ist.

2. Fügen Sie mit `git remote` die in Schritt 1 unter **Git-URL** aufgeführte Remotereferenz hinzu. Der Befehl sieht etwa wie folgt aus:

		git remote add azure https://username@needsmoregit.scm.azurewebsites.net:443/NeedsMoreGit.git

    > [AZURE.NOTE] Der Befehl **remote** fügt einem Remoterepository einen benannten Verweis hinzu. In diesem Beispiel wird ein Verweis mit dem Namen "azure" für das Repository Ihrer Web-App erstellt.

1. Verwenden Sie Folgendes an der Befehlszeile, um die aktuellen Repository-Inhalte aus dem lokalen Repository per Push in das "azure"-Remote-Repository zu übertragen:

		git push azure master

	Sie werden zum Eingeben des Kennworts aufgefordert, das vorher beim Zurücksetzen Ihrer Anmeldeinformationen für die Bereitstellung im Portal erstellt wurde. Geben Sie das Kennwort ein (beachten Sie, dass Gitbash keine Sternchen in der Konsole anzeigt, wenn Sie Ihr Kennwort eingeben). Eine Ausgabe ähnlich der folgenden sollte angezeigt werden:

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

	> [AZURE.NOTE] Das für Ihre Web-App erstellte Repository erwartet Pushanforderungen für die <strong>master</strong>-Verzweigung des Repositorys, die dann als Inhalt der Web-App verwendet werden.

2. Kehren Sie zum Blatt Ihrer Web-App im Azure-Portal zurück. **No deployment found** sollte in **Aktive Bereitstellung** mit einen Protokolleintrag des letzten Pushvorgangs geändert werden.

	![](./media/publishing-with-git/azure4-deployed.png)

2. Klicken Sie oben in der Web-App auf die **URL**, um zu überprüfen, ob **index.html** bereitgestellt wurde. Eine Seite mit "Hallo Git!" wird eingeblendet.

	![Eine Webseite mit "Hallo Git!"][hello-git]

3. Ändern Sie die Datei **index.html** mit einem Text-Editor so, dass sie "Juhu!" enthält, und speichern Sie dann die Datei.

4. Verwenden Sie die folgenden Befehle an der Befehlszeile, um die Änderungen **hinzuzufügen** und zu **übernehmen**. Übertragen Sie die Änderungen dann per **Push** in das Remote-Repository:

		git add index.html
		git commit -m "Celebration"
		git push azure master

	Nach Abschluss des **Push**-Befehls aktualisieren Sie den Browser (möglicherweise müssen Sie Strg+F5 drücken, um den Browser ordnungsgemäß zu aktualisieren). Beachten Sie, dass der Inhalt der Seite jetzt die letzte übernommene Änderung wiedergibt.

### <a id="Step7"></a>Bereitstellen von Dateien aus einer Repository-Website wie BitBucket, CodePlex, Dropbox, GitHub oder Mercurial

Wenn Sie lokales Git verwenden, um lokale Dateien mithilfe von Push an Azure zu übertragen, können Sie Updates aus einem lokalen Projekt manuell mithilfe von Push an Ihre Web-App in Azure übertragen. Im Gegensatz dazu ergibt sich bei der Bereitstellung aus BitBucket, CodePlex, Dropbox, GitHub oder Mercurial ein fortlaufender Bereitstellungsprozess, bei dem Azure mithilfe von Pull die aktuellen Updates aus Ihrem Projekt überträgt.

Zwar führen beide Methoden dazu, dass Ihr Projekt in Web-Apps bereitgestellt wird, die fortlaufende Bereitstellung ist jedoch nützlich, wenn mehrere Personen an einem Projekt arbeiten und Sie sicherstellen möchten, dass die aktuelle Version stets veröffentlicht wird, unabhängig davon, wer das letzte Update durchgeführt hat. Die fortlaufende Bereitstellung bietet sich auch an, wenn Sie eines der oben erwähnten Tools als zentrales Repository für Ihre Anwendung verwenden.

Das Bereitstellen von Dateien über GitHub, CodePlex oder BitBucket setzt voraus, dass Sie Ihr lokales Projekt auf einem dieser Dienste veröffentlicht haben. Weitere Informationen zum Veröffentlichen Ihres Projekts auf diesen Diensten finden Sie unter [Erstellen eines Repositorys (GitHub)], [Verwenden von Git mit CodePlex], [Erstellen eines Repositorys (BitBucket)], [Verwenden von Dropbox zum Freigeben von Git-Repositorys] oder [Schnellstart – Mercurial].

1. Kopieren Sie die Dateien Ihrer Web-App zunächst in das ausgewählte Repository, das für die fortlaufende Bereitstellung verwendet wird.

2. Klicken Sie im Portal auf dem Blatt der Web-App auf **Einstellungen > Continuous Delivery**. Klicken Sie auf **Quelle auswählen** und dann z. B. auf **GitHub**.

	![](./media/publishing-with-git/azure6-setup-github.png)
	
2. Klicken Sie auf dem Blatt **Continous deployment** auf **Autorisierung** und dann auf **Autorisieren**. Das Azure-Portal leitet Sie an die Repository-Site weiter, um die Autorisierung abzuschließen.

4. Wenn Sie fertig sind, wechseln Sie zurück zum Azure-Portal, und klicken Sie auf dem Blatt **Autorisierung** auf **OK**.

5. Wählen Sie auf dem Blatt **Continuous Deployment** die Organisation, das Projekt und die Verzweigung für die Bereitstellung aus. Wenn Sie fertig sind, klicken Sie auf **OK**.
  
	![](./media/publishing-with-git/azure7-setup-github-configure.png)

	> [AZURE.NOTE] Wenn Sie die fortlaufende Bereitstellung mit GitHub oder BitBucket aktivieren, werden öffentliche und private Projekte angezeigt.

Azure erstellt eine Zuordnung für das ausgewählte Repository und überträgt die Dateien mithilfe von Pull aus dem angegebenen Pfad. Nach Abschluss dieses Vorgangs wird im Bereich **Bereitstellung** des Blatts Ihrer Web-App die Meldung **Aktive Bereitstellung** angezeigt, die auf die erfolgreiche Bereitstellung hinweist.

7. An diesem Punkt wurde Ihr Projekt aus dem Repository Ihrer Wahl in Ihrer Web-App bereitgestellt. Um zu überprüfen, ob die Web-App aktiv ist, klicken Sie oben im Portal auf die **URL**. Der Browser sollte zur Web-App wechseln.

8. Um sicherzustellen, dass die fortlaufende Bereitstellung aus dem Repository Ihrer Wahl stattfindet, übertragen Sie eine Änderung per Push in das Repository. Ihre Web-App sollte aktualisiert werden, um die Änderung kurz nach dem Push ins Repository wiederzugeben. Sie können auf der Seite **Bereitstellungen** Ihrer Web-App überprüfen, ob das Update abgerufen wurde.

### <a id="Step75"></a>Bereitstellen von Visual Studio-Projekten aus BitBucket, CodePlex, Dropbox, GitHub oder Mercurial

Die Übertragung einer Visual Studio-Projektmappe in Web-Apps in Azure App Service per Push erfordert lediglich das einfache Übertragen einer Datei "index.html". Der Web-App-Bereitstellungsprozess optimiert alle Details, darunter NuGet-Wiederherstellungsabhängigkeiten und die Erstellung der Binärdateien der Anwendung. Sie können die Best Practices der Quellcodeverwaltung befolgen und Code ausschließlich in Ihrem Git-Repository verwalten und den Rest von der Web-App-Bereitstellung erledigen lassen.

Die Schritte zur Pushübertragung der Visual Studio-Projektmappe an Web-Apps sind mit denen im [vorherigen Abschnitt](#Step7) identisch, wenn Sie Ihre Projektmappe und das Repository wie folgt konfigurieren:

-	Fügen Sie dem Stammverzeichnis Ihres Repositorys eine `.gitignore`-Datei hinzu, und geben Sie alle Dateien und Ordner an, die Sie aus dem Repository ausschließen möchten, beispielsweise die Ordner `Obj`, `Bin` und `packages` (Formatinformationen finden Sie in der [gitignore-Dokumentation](http://git-scm.com/docs/gitignore)). Beispiel:

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

	>[AZURE.NOTE] Bei Verwendung von GitHub können Sie beim Erstellen Ihres Repositorys optional eine Visual Studio-spezifische GITIGNORE-Datei generieren. Diese enthält alle allgemeinen temporären Dateien, Buildergebnisse usw. Anschließend können Sie sie Ihren Anforderungen entsprechend anpassen.

-	Fügen Sie Ihrem Repository die gesamte Verzeichnisstruktur des Projekts hinzu, wobei sich die .sln-Datei im Stammverzeichnis des Repositorys befinden muss.

Nachdem Sie Ihr Repository wie beschrieben eingerichtet und Ihre Web-App in Azure für fortlaufende Veröffentlichung aus einem der Online-Git-Repositorys konfiguriert haben, können Sie Ihre ASP.NET-Anwendung lokal in Visual Studio entwickeln und Ihren Code fortlaufend bereitstellen, indem Sie einfach Ihre Änderungen mithilfe von Push an Ihr Online-Git-Repository übertragen.

## Deaktivieren der fortlaufenden Bereitstellung

Die fortlaufende Bereitstellung kann auf dem Blatt **Bereitstellungen** deaktiviert werden. Klicken Sie auf dem Blatt der Web-App auf **Einstellungen > Dauerhafte Bereitstellung**. Klicken Sie dann auf **Trennen**.

![git-DisconnectFromGitHub](./media/publishing-with-git/azure5-disconnect.png)

Nachdem Sie die Bestätigungsmeldung mit **Ja** beantwortet haben, können Sie zum Blatt Ihrer Web-App zurückkehren und auf **Einstellungen > Dauerhafte Bereitstellung** klicken, wenn Sie die Veröffentlichung aus einer anderen Quelle einrichten möchten.

## <a id="Step8"></a>Problembehandlung

Die folgenden Fehler und Probleme treten häufiger auf, wenn Git zum Veröffentlichen auf einer Web-App in Azure verwendet wird:

****

**Symptom**: Unable to access '[siteURL]': Failed to connect to [scmAddress]

**Ursache**: Dieser Fehler kann auftreten, wenn die Web-App nicht ordnungsgemäß ausgeführt wird.

**Lösung:** Starten Sie die Web-App im Azure-Portal. Die Git-Bereitstellung funktioniert nur dann, wenn die Web-App ausgeführt wird.


****

**Symptom**: Couldn't resolve host 'hostname'

**Ursache**: Dieser Fehler kann auftreten, wenn die beim Erstellen der "azure"-Remotewebsite eingegebenen Adressinformationen falsch waren.

**Lösung**: Verwenden Sie den Befehl `git remote -v`, um alle Remotewebsites zusammen mit der jeweils zugehörigen URL aufzulisten. Überprüfen Sie, ob die URL für die 'azure'-Remotewebsite korrekt ist. Entfernen Sie diese Remote-Website bei Bedarf und erstellen Sie sie mit der korrekten URL neu.

****

**Symptom**: No refs in common and none specified; doing nothing. Sie sollten vielleicht eine Verzweigung wie 'master' angeben.

**Ursache**: Dieser Fehler kann auftreten, wenn Sie beim Ausführen eines Git-Pushvorgangs keine Verzweigung angeben und den von Git verwendeten "push.default"-Wert nicht festgelegt haben.

**Lösung**: Führen Sie den Pushvorgang unter Angabe der Hauptverzweigung erneut durch. Beispiel:

	git push azure master

****

**Symptom**: src refspec [branchname] does not match any.

**Ursache**: Dieser Fehler kann auftreten, wenn Sie versuchen, etwas per Push auf eine andere Verzweigung als die Hauptverzweigung auf der "azure"-Remotewebsite zu übertragen.

**Lösung**: Führen Sie den Pushvorgang unter Angabe der Hauptverzweigung erneut durch. Beispiel:

	git push azure master

****

**Symptom**: Fehler – Änderungen an Remote-Repository vorgenommen, die Web-App wurde jedoch nicht aktualisiert.

**Ursache**: Dieser Fehler kann auftreten, wenn Sie eine Node.js-Anwendung mit einer "package.json"-Datei bereitstellen, die zusätzliche erforderliche Module angibt.

**Lösung**: Zusätzliche Meldungen mit "npm ERR!" sollten vor diesem Fehler protokolliert werden – sie können zusätzlichen Kontext für diesen Fehler bereitstellen. Es folgen bekannte Ursachen für diesen Fehler und die entsprechende "npm ERR!"-Meldung:

* **Ungültige package.json-Datei**: npm ERR! Couldn't read dependencies.

* **Systemeigenes Modul ohne binäre Distribution für Windows**:

	* npm ERR! `cmd "/c" "node-gyp rebuild"` failed with 1

		ODER

	* npm ERR! [modulename@version] preinstall: `make || gmake`


## Zusätzliche Ressourcen

* [Verwenden von PowerShell für Azure]
* [Verwenden des Azure-Befehlszeilentools für Mac und Linux]
* [Git-Dokumentation]
* [Projekt Kudu](https://github.com/projectkudu/kudu/wiki)

>[AZURE.NOTE] Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Änderungen
* Hinweise zu den Änderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).

[Azure Developer Center]: http://azure.microsoft.com/develop/overview/
[Azure-Portal]: https://portal.azure.com
[Git website]: http://git-scm.com
[Installieren von Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Verwenden von PowerShell für Azure]: ../articles/install-configure-powershell.md
[Verwenden des Azure-Befehlszeilentools für Mac und Linux]: ../articles/xplat-cli-install.md
[Git-Dokumentation]: http://git-scm.com/documentation

[portal-select-website]: ./media/publishing-with-git/git-select-website.png
[git-WhereIsYourSourceCode]: ./media/publishing-with-git/git-WhereIsYourSourceCode.png
[git-instructions]: ./media/publishing-with-git/git-instructions.png
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
[Schnellstart – Mercurial]: http://mercurial.selenic.com/wiki/QuickStart
[Verwenden von Dropbox zum Freigeben von Git-Repositorys]: https://gist.github.com/trey/2722927
[Continuous delivery to Azure using Visual Studio Team Services]: ../articles/cloud-services/cloud-services-continuous-delivery-use-vso.md

<!---HONumber=AcomDC_0211_2016-->