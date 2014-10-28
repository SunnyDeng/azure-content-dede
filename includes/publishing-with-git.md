# Veröffentlichen auf Azure-Websites mit Git

Azure-Websites unterstützen die fortlaufende Bereitstellung über Quellcodeverwaltungs- und Repositorytools wie BitBucket, CodePlex, Dropbox, Git, GitHub, Mercurial und TFS. Sie können diese Tools verwenden, um den Inhalt und den Code für Ihre Website zu verwalten und bei Bedarf schnell und einfach Änderungen an Ihre Website mithilfe von Push zu übertragen.

In diesem Artikel erfahren Sie, wie Sie mithilfe von Git direkt von Ihrem lokalen Computer aus auf einer Azure-Website veröffentlichen (in Azure wird diese Methode der Veröffentlichung **lokales Git** genannt). Außerdem wird erläutert, wie Sie die fortlaufende Bereitstellung von Repositorywebsites aus ermöglichen, z. B. von BitBucket, CodePlex, Dropbox, GitHub oder Mercurial. Informationen zum Verwenden von TFS für die fortlaufende Bereitstellung finden Sie unter [Continuous delivery to Azure using Visual Studio Online][Continuous delivery to Azure using Visual Studio Online] (Fortlaufende Bereitstellung für Azure mithilfe von Visual Studio Online, in englischer Sprache).

> [WACOM.NOTE] Viele der in diesem Artikel beschriebenen Git-Befehle werden automatisch ausgeführt, wenn Sie eine Website mit den [Azure-Befehlszeilentools für Mac und Linux][Azure-Befehlszeilentools für Mac und Linux] erstellen.

Die Aufgabe umfasst die folgenden Schritte:

-   [Installieren von Git][Installieren von Git]
-   [Erstellen eines lokalen Repositorys][Erstellen eines lokalen Repositorys]
-   [Hinzufügen einer Webseite][Hinzufügen einer Webseite]
-   [Aktivieren des Website-Repositorys][Aktivieren des Website-Repositorys]
-   [Bereitstellen des Projekts][Bereitstellen des Projekts]

    -   [Übertragen lokaler Dateien zu Azure mithilfe von Push (Lokales Git)][Übertragen lokaler Dateien zu Azure mithilfe von Push (Lokales Git)]
    -   [Bereitstellen von Dateien aus einer Repository-Website wie BitBucket, CodePlex, Dropbox, GitHub oder Mercurial][Bereitstellen von Dateien aus einer Repository-Website wie BitBucket, CodePlex, Dropbox, GitHub oder Mercurial]
    -   [Bereitstellen eines Visual Studio-Projekts aus BitBucket, CodePlex, Dropbox, GitHub oder Mercurial][Bereitstellen eines Visual Studio-Projekts aus BitBucket, CodePlex, Dropbox, GitHub oder Mercurial]
-   [Problembehandlung][Problembehandlung]

## <span id="Step2"></span></a>Installieren von Git

Die erforderlichen Schritte zum Installieren von Git variieren je nach Betriebssystem. Informationen zu Betriebssystem-spezifischen Distributionen und zur Installation finden Sie unter [Installieren von Git][1].

> [WACOM.NOTE] Für einige Betriebssysteme sind eine Befehlszeilenversion und eine GUI-Version von Git verfügbar. Die in diesem Artikel bereitgestellten Anweisungen verwenden die Befehlszeilenversion.

## <span id="Step2"></span></a>Erstellen eines lokalen Repositorys

Führen Sie die folgenden Aufgaben durch, um ein neues Git-Repository zu erstellen.

1.  Erstellen Sie ein Verzeichnis mit dem Namen "MyGitRepository" für Ihr Git-Repository und die Dateien Ihrer Website.

2.  Öffnen Sie eine Befehlszeile, zum Beispiel **GitBash** (Windows) oder **Bash** (Unix Shell). Auf OS X-Systemen können Sie auf die Befehlszeile über die **Terminal**-Anwendung zugreifen.

3.  Wechseln Sie mit der Befehlszeile in das Verzeichnis **MyGitRepository**.

        cd MyGitRepository

4.  Verwenden Sie den folgenden Befehl, um ein neues Git-Repository zu initialisieren.

        git init

    Eine Meldung wie **Leeres Git-Repository unter [Pfad] initialisiert** sollte zurückgegeben werden.

## <span id="Step3"></span></a>Hinzufügen einer Webseite

Azure-Websites unterstützen Anwendungen, die in verschiedenen Programmiersprachen erstellt wurden. In diesem Beispiel verwenden Sie eine statische HTML-Datei. Informationen zum Veröffentlichen von Websites in anderen Programmiersprachen auf Azure finden Sie im [Azure Developer Center][Azure Developer Center].

1.  Erstellen Sie mithilfe eines Text-Editors eine neue Datei mit dem Namen **index.html** im Stammverzeichnis des Git-Repositorys (das vorher von Ihnen erstellte Verzeichnis **MyGitRepository**).

2.  Geben Sie den folgenden Text als Inhalt der Datei **index.html** ein, und speichern Sie die Datei.

        Hello Git!

3.  Stellen Sie mit der Befehlszeile sicher, dass Sie sich im Stammverzeichnis Ihres Git-Repositorys befinden. Verwenden Sie dann folgenden Befehl, um dem Repository die Datei **index.html** hinzuzufügen:

        git add index.html 

    > [WACOM.NOTE] Sie können Hilfeinformationen für jeden Git-Befehl aufrufen, indem Sie nach dem Befehl "-help" oder "--help" eingeben. Geben Sie als Parameteroptionen für den Befehl "add" beispielsweise "git add -help" ein, um Befehlszeilenhilfe zu erhalten, oder "git add --help", um ausführlichere Hilfe anzuzeigen.

4.  Übernehmen Sie dann die Änderungen am Repository mithilfe des folgenden Befehls:

        git commit -m "Adding index.html to the repository"

    Eine Ausgabe ähnlich der folgenden sollte angezeigt werden:

        [master (root-commit) 369a79c] Adding index.html to the repository
         1 file changed, 1 insertion(+)
         create mode 100644 index.html

## <span id="Step4"></span></a>Aktivieren des Websiterepositorys

Führen Sie die folgenden Schritte durch, um ein Git-Repository für Ihre Website mithilfe des Azure-Portals zu aktivieren:

1.  Melden Sie sich beim [Azure-Portal][Azure-Portal] an.

2.  Klicken Sie auf die Schaltfläche NEU, um eine neue Website zu erstellen, für die Sie dann ein Repository aktivieren.

3.  Warten Sie, bis der Erstellungsprozess der Website in der Ansicht **Websites** abgeschlossen ist, und wählen Sie dann die Website aus.

    ![Ein Bild, das eine ausgewählte Website anzeigt][Ein Bild, das eine ausgewählte Website anzeigt]

4.  Wählen Sie die Registerkarte **DASHBOARD** aus.

5.  Wählen Sie im Abschnitt **Auf einen Blick** die Option **Bereitstellung über Quellcodeverwaltung einrichten** aus. Das folgende Dialogfeld **BEREITSTELLUNG EINRICHTEN** wird angezeigt.

    ![Git-WhereIsYourSourceCode][Git-WhereIsYourSourceCode]

6.  Wählen Sie **Lokales Git** aus, und klicken Sie dann auf den Pfeil für **Weiter**.

7.  Wenn Sie zum ersten Mal ein Repository in Azure einrichten, müssen Sie dafür Anmeldeinformationen erstellen. Sie verwenden diese, um eine Anmeldung beim Azure-Repository vorzunehmen und Änderungen aus Ihrem lokalen Git-Repository mithilfe von Push zu übertragen.

    ![][]

8.  Nach einer kurzen Verzögerung wird die Meldung angezeigt, dass Ihr Repository bereit ist.

    ![Git-Anweisungen][Git-Anweisungen]

## <span id="Step5"></span></a>Bereitstellen des Projekts

### <span id="Step6"></span></a>Übertragen lokaler Dateien zu Azure mithilfe von Push (Lokales Git)

An diesem Punkt werden im Portal Anweisungen zum Initialisieren eines lokalen Repositorys und zum Hinzufügen von Dateien angezeigt. Sie haben dies bereits in den vorherigen Schritten dieses Themas durchgeführt. Wenn Sie jedoch noch keine Anmeldeinformationen für die Bereitstellung eingerichtet haben, müssen Sie im Portal zur Registerkarte **DASHBOARD** zurückkehren und auf **Anmeldeinformationen für die Bereitstellung zurücksetzen** klicken.

Führen Sie die folgenden Schritte durch, um Ihre Website mit einem lokalen Git auf Azure zu veröffentlichen:

1.  Stellen Sie mit der Befehlszeile sicher, dass Sie sich im Stammverzeichnis Ihres lokalen Git-Repositorys befinden, das die vorher erstellte Datei **index.html** enthält.

2.  Kopieren Sie den in Schritt 3 der vom Portal angezeigten Anweisungen aufgeführten Befehl "git remote add". Dieser ähnelt dem folgenden Befehl:

        git remote add azure https://username@needsmoregit.scm.azurewebsites.net:443/NeedsMoreGit.git

    > [WACOM.NOTE] Der Befehl **remote** fügt einem Remoterepository einen benannten Verweis hinzu. In diesem Beispiel wird ein Verweis mit dem Namen "azure" für das Repository Ihrer Azure-Website erstellt.

3.  Verwenden Sie Folgendes an der Befehlszeile, um die aktuellen Repository-Inhalte aus dem lokalen Repository per Push in das "azure"-Remote-Repository zu übertragen:

        git push azure master

    Sie werden zum Eingeben des vorher beim Zurücksetzen Ihrer Anmeldeinformationen für die Bereitstellung im Portal erstellten Kennworts aufgefordert. Geben Sie das Kennwort ein (beachten Sie, dass Gitbash keine Sternchen in der Konsole anzeigt, wenn Sie Ihr Kennwort eingeben). Eine Ausgabe ähnlich der folgenden sollte angezeigt werden:

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
        * [new branch]      master -> master

    > [WACOM.NOTE] Das für Ihre Azure-Website erstellte Repository erwartet Pushanforderungen für den **master**-Pfad des Repositorys, die dann als Inhalt der Website verwendet werden.

4.  Klicken Sie unten im Portal auf **DURCHSUCHEN**, um zu überprüfen, ob **index.html** bereitgestellt wurde. Eine Seite mit "Hallo Git!" wird eingeblendet.

    ![Eine Webseite mit "Hallo Git!"][Eine Webseite mit "Hallo Git!"]

5.  Ändern Sie die Datei **index.html** mit einem Text-Editor so, dass sie "Juhu!" enthält, und speichern Sie dann die Datei.

6.  Verwenden Sie die folgenden Befehle an der Befehlszeile, um die Änderungen **hinzuzufügen** und zu **übernehmen**. Übertragen Sie die Änderungen dann per **Push** in das Remote-Repository:

        git add index.html
        git commit -m "Celebration"
        git push azure master

    Nach Abschluss des **Push**-Befehls aktualisieren Sie den Browser (möglicherweise müssen Sie Strg+F5 drücken, um den Browser ordnungsgemäß zu aktualisieren). Beachten Sie, dass der Inhalt der Seite jetzt die letzte übernommene Änderung wiedergibt.

    ![Eine Webseite mit "Juhu"][Eine Webseite mit "Juhu"]

### <span id="Step7"></span></a>Bereitstellen von Dateien aus einer Repository-Website wie BitBucket, CodePlex, Dropbox, GitHub oder Mercurial

Wenn Sie lokales Git verwenden, um lokale Dateien mithilfe von Push an Azure zu übertragen, können Sie Updates aus einem lokalen Projekt manuell mithilfe von Push an Ihre Azure-Website übertragen. Im Gegensatz dazu ergibt sich bei der Bereitstellung aus BitBucket, CodePlex, Dropbox, GitHub oder Mercurial ein fortlaufender Bereitstellungsprozess, bei dem Azure mithilfe von Pull die aktuellen Updates aus Ihrem Projekt überträgt.

Während beide Methoden dazu führen, dass Ihr Projekt auf einer Azure-Website bereitgestellt wird, ist die fortlaufende Bereitstellung nützlich, wenn mehrere Personen an einem Projekt arbeiten und Sie sicherstellen möchten, dass die aktuelle Version stets veröffentlicht wird – unabhängig davon, wer das letzte Update durchgeführt hat. Die fortlaufende Bereitstellung bietet sich auch an, wenn Sie eines der oben erwähnten Tools als zentrales Repository für Ihre Anwendung verwenden.

Das Bereitstellen von Dateien über GitHub, CodePlex oder BitBucket setzt voraus, dass Sie Ihr lokales Projekt auf einem dieser Dienste veröffentlicht haben. Weitere Informationen zum Veröffentlichen Ihres Projekts auf diesen Diensten finden Sie unter [Erstellen eines Repositorys (GitHub)][Erstellen eines Repositorys (GitHub)], [Verwenden von Git mit CodePlex][Verwenden von Git mit CodePlex], [Erstellen eines Repositorys (BitBucket)][Erstellen eines Repositorys (BitBucket)], [Verwenden von Dropbox zum Freigeben von Git-Repositorys][Verwenden von Dropbox zum Freigeben von Git-Repositorys] oder [Schnellstart – Mercurial][Schnellstart – Mercurial].

1.  Kopieren Sie die Dateien Ihrer Website zunächst in das ausgewählte Repository, das für die fortlaufende Bereitstellung verwendet wird.

2.  Gehen Sie im Azure-Portal zur Registerkarte **DASHBOARD** für Ihre Website. Wählen Sie im Abschnitt **Auf einen Blick** die Option **Bereitstellung über Quellcodeverwaltung einrichten** aus. Das Dialogfeld **Bereitstellung einrichten** wird mit eine Frage bezüglich des Speicherorts Ihres Quellcodes angezeigt.

3.  Wählen Sie die Quellcodeverwaltungsmethode aus, die Sie für die fortlaufende Bereitstellung verwenden möchten.

4.  Geben Sie bei Aufforderung die Anmeldeinformationen für den ausgewählten Dienst ein.

5.  Nachdem Sie Azure den Zugriff auf Ihr Konto gestattet haben, wird eine Liste mit Repositorys angezeigt.

    ![git-ChooseARepositoryToDeploy][git-ChooseARepositoryToDeploy]

6.  Wählen Sie das Repository aus, das Sie Ihrer Azure-Website zuordnen möchten. Klicken Sie auf das Häkchen, um fortzufahren.

    > [WACOM.NOTE] Wenn Sie die fortlaufende Bereitstellung mit GitHub oder BitBucket aktivieren, werden öffentliche und private Projekte angezeigt.

7.  Azure erstellt eine Zuordnung für das ausgewählte Repository und überträgt die Dateien mithilfe von Pull aus der Hauptverzweigung. Nach Abschluss dieses Prozesses wird im **Bereitstellungsverlauf** auf der Seite **Bereitstellungen** eine Meldung **Aktive Bereitstellung** ähnlich der folgenden angezeigt:

    ![git-githubdeployed][git-githubdeployed]

8.  An diesem Punkt wurde Ihr Projekt aus dem Repository Ihrer Wahl auf Ihrer Azure-Website bereitgestellt. Um zu überprüfen, ob die Website aktiv ist, klicken Sie unten im Portal auf den Link **Durchsuchen**. Der Browser sollte zur Website wechseln.

9.  Um zu überprüfen, ob die fortlaufende Bereitstellung stattfindet, nehmen Sie eine Änderung am Projekt vor und übertragen das Update mithilfe von Push in das Repository, das Sie dieser Website zugeordnet haben. Ihre Website sollte aktualisiert werden, um die Änderung kurz nach dem Push ins Repository wiederzugeben. Sie können auf der Seite **Bereitstellungen** Ihrer Website überprüfen, ob das Update abgerufen wurde.

    ![git-GitHubDeployed-Updated][git-GitHubDeployed-Updated]

### <span id="Step75"></span></a>Bereitstellen eines Visual Studio-Projekts aus BitBucket, CodePlex, Dropbox, GitHub oder Mercurial

Ein Visual Studio-Projekt lässt sich genauso leicht mithilfe von Push an eine Azure-Website übertragen wie eine einfache index.html-Datei. Der Azure-Websites-Bereitstellungsprozess optimiert alle Details, darunter NuGet-Wiederherstellungsabhängigkeiten und die Erstellung der Binärdateien der Anwendung. Sie können die Best Practices der Quellcodeverwaltung befolgen und Code ausschließlich in Ihrem Git-Repository verwalten und den Rest von der Azure-Websites-Bereitstellung erledigen lassen.

Die Schritte zur Übertragung Ihres Visual Studio-Projekts mithilfe von Push an eine Azure-Website sind mit denen im [vorherigen Abschnitt][Bereitstellen von Dateien aus einer Repository-Website wie BitBucket, CodePlex, Dropbox, GitHub oder Mercurial] identisch, vorausgesetzt, dass Sie Ihr Projekt und Repository wie folgt konfigurieren:

-   Fügen Sie dem Stammverzeichnis Ihres Repositorys eine `.gitignore`-Datei hinzu, und geben Sie alle Dateien und Ordner an, die Sie aus Ihrem Repository ausschließen möchten, beispielsweise die Ordner `Obj`, `Bin` und `packages` (Formatinformationen finden Sie in der [gitignore-Dokumentation][gitignore-Dokumentation]). Beispiel:

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

    > [WACOM.NOTE] Bei Verwendung von GitHub können Sie optional eine Visual Studio-spezifische .gitignore-Datei generieren, wenn Sie Ihr Repository erstellen. Diese enthält alle allgemeinen temporären Dateien, Buildergebnisse usw. und kann an Ihre speziellen Anforderungen angepasst werden.

-   Fügen Sie Ihrem Repository die gesamte Verzeichnisstruktur des Projekts hinzu, wobei sich die .sln-Datei im Stammverzeichnis des Repositorys befinden muss.

-   Damit Visual Studio automatisch fehlende Pakete wiederherstellt, [aktivieren Sie die NuGet-Paketwiederherstellung][aktivieren Sie die NuGet-Paketwiederherstellung] in Ihrem Visual Studio-Projekt.

Nachdem Sie Ihr Repository wie beschrieben eingerichtet und Ihre Azure-Website für fortlaufende Veröffentlichung aus einem der Online-Git-Repositorys konfiguriert haben, können Sie Ihre ASP.NET-Anwendung lokal in Visual Studio entwickeln und Ihren Code fortlaufend bereitstellen, indem Sie einfach Ihre Änderungen mithilfe von Push an Ihr Online-Git-Repository übertragen.

#### Funktionsweise der fortlaufenden Bereitstellung

Die fortlaufende Bereitstellung funktioniert durch Angabe der **BEREITSTELLUNGSAUSLÖSER-URL** im Abschnitt **Bereitstellungen** der Registerkarte **Konfigurieren** Ihrer Website.

![git-DeploymentTrigger][git-DeploymentTrigger]

Wenn Updates am Repository vorgenommen werden, wird eine POST-Anforderung an diese URL gesendet, die Ihre Azure-Website darüber informiert, dass das Repository aktualisiert wurde. An diesem Punkt wird das Update abgerufen und auf Ihrer Website bereitgestellt.

Weitere Informationen zum Modul hinter dem Git-Bereitstellungsprozess für Azure-Websites finden Sie unter [Project Kudu][Project Kudu] (Projekt Kudu, in englischer Sprache).

#### Angeben der zu verwendenden Verzweigung

Wenn Sie die fortlaufende Bereitstellung aktivieren, befindet sie sich standardmäßig in der **master**-Verzweigung des Repositorys. Wenn Sie eine andere Verzweigung verwenden, führen Sie die folgenden Schritte aus:

1.  Wählen Sie im Portal Ihre Website und dann **KONFIGURIEREN** aus.

2.  Geben Sie im Abschnitt **Bereitstellungen** der Seite im Feld **BEREITZUSTELLENDE VERZWEIGUNG** die Verzweigung ein, die Sie verwenden möchten, und drücken Sie die Eingabetaste. Klicken Sie schließlich auf **SPEICHERN**.

    Azure sollte das Update basierend auf Änderungen an der neuen Verzweigung sofort beginnen.

#### Deaktivieren der fortlaufenden Bereitstellung

Die fortlaufende Bereitstellung kann im Azure-**Dashboard** deaktiviert werden. Wählen Sie im Abschnitt **Auf einen Blick** die Option zum Trennen vom verwendeten Repository aus:

![git-DisconnectFromGitHub][git-DisconnectFromGitHub]

Nachdem Sie die Bestätigungsmeldung mit **Ja** beantwortet haben, kehren Sie zu **Auf einen Blick** zurück und klicken auf **Bereitstellung über Quellcodeverwaltung einrichten**, wenn Sie die Veröffentlichung von einer anderen Quelle aus einrichten möchten.

## <span id="Step8"></span></a>Problembehandlung

Die folgenden Fehler und Probleme treten häufiger auf, wenn Git zum Veröffentlichen auf einer Azure-Website verwendet wird:

------------------------------------------------------------------------

**Symptom**: Unable to access '[siteURL]': Failed to connect to [scmAddress]

**Ursache**: Dieser Fehler kann auftreten, wenn die Website nicht ausgeführt wird.

**Lösung**: Starten Sie die Website im Azure-Portal. Die Git-Bereitstellung funktioniert nur dann, wenn die Website ausgeführt wird.

------------------------------------------------------------------------

**Symptom**: Couldn't resolve host 'hostname'

**Ursache**: Dieser Fehler kann auftreten, wenn die Adressinformationen, die beim Erstellen der 'azure'-Remotewebsite eingegeben wurden, falsch waren.

**Lösung**: Verwenden Sie den Befehl `git remote -v`, um alle Remotewebsites zusammen mit der jeweils zugehörigen URL aufzulisten. Überprüfen Sie, ob die URL für die 'azure'-Remotewebsite korrekt ist. Entfernen Sie diese Remote-Website bei Bedarf und erstellen Sie sie mit der korrekten URL neu.

------------------------------------------------------------------------

**Symptom**: No refs in common and none specified; doing nothing. Sie sollten eine Verzweigung wie 'master' angeben.

**Ursache**: Dieser Fehler kann auftreten, wenn Sie beim Ausführen eines Git-Push-Vorgangs keine Verzweigung angeben und den von Git verwendeten "push.default"-Wert nicht festgelegt haben.

**Lösung**: Führen Sie den Push-Vorgang unter Angabe der Hauptverzweigung erneut durch. Beispiel:

    git push azure master

------------------------------------------------------------------------

**Symptom**: src refspec [branchname] does not match any.

**Ursache**: Dieser Fehler kann auftreten, wenn Sie versuchen, auf eine andere Verzweigung als die Hauptverzweigung auf der 'azure'-Remotewebsite per Push zu übertragen.

**Lösung**: Führen Sie den Push-Vorgang unter Angabe der Hauptverzweigung erneut durch. Beispiel:

    git push azure master

------------------------------------------------------------------------

**Symptom**: Error - Changes commited to remote repository but your website not updated.

**Ursache**: Dieser Fehler kann auftreten, wenn Sie eine "Node.js"-Anwendung mit einer Datei "package.json" bereitstellen, die zusätzliche erforderliche Module angibt.

**Lösung**: Zusätzliche Meldungen mit "npm ERR!" sollten vor diesem Fehler protokolliert werden und können zusätzlichen Kontext für diesen Fehler bereitstellen. Es folgen bekannte Ursachen für diesen Fehler und die entsprechende "npm ERR!"-Meldung:

-   **Fehlerhafte "package.json"-Datei**: npm ERR! Couldn't read dependencies.

-   **Systemeigenes Modul ohne binäre Distribution für Windows**:

    -   npm ERR! \`cmd "/c" "node-gyp rebuild"\` failed with 1

        OR

    -   npm ERR! [<modulename@version>] preinstall: \`make || gmake\`

## Zusätzliche Ressourcen

-   [Verwenden von PowerShell für Azure][Verwenden von PowerShell für Azure]
-   [Verwenden des Azure-Befehlszeilentools für Mac und Linux][Azure-Befehlszeilentools für Mac und Linux]
-   [Git-Dokumentation][Git-Dokumentation]
-   [Project Kudu][Project Kudu]

  [Continuous delivery to Azure using Visual Studio Online]: http://www.windowsazure.com/de-de/develop/net/common-tasks/publishing-with-tfs/
  [Azure-Befehlszeilentools für Mac und Linux]: /de-de/develop/nodejs/how-to-guides/command-line-tools/
  [Installieren von Git]: #Step1
  [Erstellen eines lokalen Repositorys]: #Step2
  [Hinzufügen einer Webseite]: #Step3
  [Aktivieren des Website-Repositorys]: #Step4
  [Bereitstellen des Projekts]: #Step5
  [Übertragen lokaler Dateien zu Azure mithilfe von Push (Lokales Git)]: #Step6
  [Bereitstellen von Dateien aus einer Repository-Website wie BitBucket, CodePlex, Dropbox, GitHub oder Mercurial]: #Step7
  [Bereitstellen eines Visual Studio-Projekts aus BitBucket, CodePlex, Dropbox, GitHub oder Mercurial]: #Step75
  [Problembehandlung]: #Step8
  [1]: http://git-scm.com/book/en/Getting-Started-Installing-Git
  [Azure Developer Center]: http://www.windowsazure.com/de-de/develop/overview/
  [Azure-Portal]: http://manage.windowsazure.com
  [Ein Bild, das eine ausgewählte Website anzeigt]: ./media/publishing-with-git/git-select-website.png
  [Git-WhereIsYourSourceCode]: ./media/publishing-with-git/git-WhereIsYourSourceCode.png
  []: ./media/publishing-with-git/git_credentials.png
  [Git-Anweisungen]: ./media/publishing-with-git/git-instructions.png
  [Eine Webseite mit "Hallo Git!"]: ./media/publishing-with-git/git-hello-git.png
  [Eine Webseite mit "Juhu"]: ./media/publishing-with-git/git-yay.png
  [Erstellen eines Repositorys (GitHub)]: https://help.github.com/articles/create-a-repo
  [Verwenden von Git mit CodePlex]: http://codeplex.codeplex.com/wikipage?title=Using%20Git%20with%20CodePlex&referringTitle=Source%20control%20clients&ProjectName=codeplex
  [Erstellen eines Repositorys (BitBucket)]: https://confluence.atlassian.com/display/BITBUCKET/Create+an+Account+and+a+Git+Repo
  [Verwenden von Dropbox zum Freigeben von Git-Repositorys]: https://gist.github.com/trey/2722927
  [Schnellstart – Mercurial]: http://mercurial.selenic.com/wiki/QuickStart
  [git-ChooseARepositoryToDeploy]: ./media/publishing-with-git/git-ChooseARepositoryToDeploy.png
  [git-githubdeployed]: ./media/publishing-with-git/git-GitHubDeployed.png
  [git-GitHubDeployed-Updated]: ./media/publishing-with-git/git-GitHubDeployed-Updated.png
  [gitignore-Dokumentation]: http://git-scm.com/docs/gitignore
  [aktivieren Sie die NuGet-Paketwiederherstellung]: http://docs.nuget.org/docs/workflows/using-nuget-without-committing-packages
  [git-DeploymentTrigger]: ./media/publishing-with-git/git-DeploymentTrigger.png
  [Project Kudu]: https://github.com/projectkudu/kudu/wiki
  [git-DisconnectFromGitHub]: ./media/publishing-with-git/git-DisconnectFromGitHub.png
  [Verwenden von PowerShell für Azure]: http://www.windowsazure.com/de-de/develop/nodejs/how-to-guides/powershell-cmdlets/
  [Git-Dokumentation]: http://git-scm.com/documentation
