
# Veröffentlichen aus der Quellcodeverwaltung auf Azure-Websites

Azure-Websites unterstützen die fortlaufende Bereitstellung von Quellcodeverwaltungs- und Repository-Tools wie BitBucket, CodePlex, Dropbox, Git, GitHub, Mercurial und TFS. Sie können diese Tools verwenden, um den Inhalt und den Code für Ihre Website zu verwalten und bei Bedarf schnell und einfach Änderungen an Ihrer Website vorzunehmen.

In diesem Artikel erfahren Sie, wie Sie Git verwenden, um direkt von Ihrem lokalen Computer aus auf einer Azure-Website zu veröffentlichen
(in Azure wird diese Methode der Veröffentlichung **Lokales Git** genannt). Außerdem erfahren Sie, wie Sie die fortlaufende Bereitstellung von Repository-Websites wie BitBucket, CodePlex, DropBox, GitHub oder Mercurial aktivieren. Informationen zum Verwenden von TFS für die fortlaufende Bereitstellung finden Sie unter [Continuous delivery to Azure using Visual Studio Online][1] (Fortlaufende Bereitstellung für Azure mithilfe von Visual Studio Online, in englischer Sprache).

> [WACOM.NOTE] Viele der in diesem Artikel beschriebenen Git-Befehle werden automatisch ausgeführt, wenn Sie eine Website mit den [Azure-Befehlszeilentools für Mac und Linux](/en-us/develop/nodejs/how-to-guides/command-line-tools/) erstellen.

Die Aufgabe umfasst die folgenden Schritte:

* [Installieren von Git](#Step1)
* [Erstellen eines lokalen Repositorys](#Step2)
* [Hinzufügen einer Webseite](#Step3)
* [Aktivieren des Website-Repositorys](#Step4)
* [Bereitstellen des Projekts](#Step5)
  * [Übertragen lokaler Dateien zu Azure mithilfe von Push (Lokales Git)](#Step6)
  * [Bereitstellen von Dateien aus einer Repository-Website wie
    BitBucket, CodePlex, Dropbox, GitHub oder Mercurial](#Step7)
* [Problembehandlung](#Step8)

<h2><a id="Step2" ></a>Installieren von Git</h2>


Die erforderlichen Schritte zum Installieren von Git variieren je nach Betriebssystem. Informationen zu Betriebssystem-spezifischen Distributionen und zur Installation finden Sie unter [Installieren von Git][2].

> [WACOM.NOTE] Für einige Betriebssysteme sind eine Befehlszeilenversion und eine GUI-Version von Git verfügbar. Die in diesem Artikel bereitgestellten Anweisungen verwenden die Befehlszeilenversion.

<h2><a id="Step2" ></a>Erstellen eines lokalen Repositorys</h2>


Führen Sie die folgenden Aufgaben durch, um ein neues Git-Repository zu erstellen.

1.  Erstellen Sie ein Verzeichnis mit dem Namen **MyGitRepository** für Ihr Git-Repository und die Dateien Ihrer Website.

2.  Öffnen Sie eine Befehlszeile, zum Beispiel **GitBash** (Windows) oder **Bash** (Unix Shell). Auf OS X-Systemen können Sie auf die Befehlszeile über die **Terminal**-Anwendung zugreifen.

3.  Wechseln Sie mit der Befehlszeile in das Verzeichnis **MyGitRepository**.
    
         cd MyGitRepository

4.  Verwenden Sie den folgenden Befehl, um ein neues Git-Repository zu initialisieren.
    
         git init
    
    Eine Meldung wie **Leeres Git-Repository unter [Pfad] initialisiert** sollte zurückgegeben werden.

<h2><a id="Step3" ></a>Hinzufügen einer Webseite</h2>


Azure-Websites unterstützen Anwendungen, die in verschiedenen Programmiersprachen erstellt wurden. In diesem Beispiel verwenden Sie eine statische HTML-Datei. Informationen zum Veröffentlichen von Websites in anderen Programmiersprachen in Azure finden Sie im [Azure Developer Center][3].

1.  Erstellen Sie mithilfe eines Text-Editors eine neue Datei mit dem Namen **index.html** im Stammverzeichnis des Git-Repositorys (das vorher von Ihnen erstellte Verzeichnis **MyGitRepository**).

2.  Geben Sie den folgenden Text als Inhalt der Datei **index.html** ein, und speichern Sie die Datei.
    
         Hello Git!

3.  Stellen Sie mit der Befehlszeile sicher, dass Sie sich im Stammverzeichnis Ihres Git-Repositorys befinden. Verwenden Sie dann folgenden Befehl, um dem Repository die Datei **index.html** hinzuzufügen:
    
         git add index.html


    
    > [WACOM.NOTE] Sie können Hilfeinformationen für jeden Git-Befehl aufrufen, indem Sie nach dem Befehl "-help" oder "--help" eingeben. Die Parameteroptionen für den Befehl "add" rufen Sie beispielsweise auf, indem Sie "git add -help" eingeben, um Befehlszeilenhilfe zu erhalten, oder indem Sie "git add --help" eingeben, um ausführlichere Hilfe zu erhalten.

4.  Übernehmen Sie dann die Änderungen am Repository mithilfe des folgenden Befehls:
    
         git commit -m "Adding index.html to the repository"
    
    Eine Ausgabe ähnlich der folgenden sollte angezeigt werden:
    
         [master (root-commit) 369a79c] Adding index.html to the repository
          1 file changed, 1 insertion(+)
          create mode 100644 index.html

<h2><a id="Step4" ></a>Aktivieren des Website-Repositorys</h2>


Führen Sie die folgenden Schritte durch, um ein Git-Repository für Ihre Website mithilfe des Azure-Portals zu aktivieren:

1.  Melden Sie sich beim [Azure-Portal][4] an.

2.  Wählen Sie auf der linken Seite **Websites** und dann die Website aus, für die Sie ein Repository aktivieren möchten.
    
    ![Ein Bild, das eine ausgewählte Website anzeigt](./media/publishing-with-git/git-select-website.png)

3.  Wählen Sie die Registerkarte **DASHBOARD** aus.

4.  Wählen Sie im **Schnellansichts**abschnitt **Set up deployment from source control** aus. Das folgende Dialogfeld **EINRICHTEN DER BEREITSTELLUNG** wird angezeigt.
    
    ![Git-WhereIsYourSourceCode](./media/publishing-with-git/git-WhereIsYourSourceCode.png)

5.  Wählen Sie **Lokales Git** aus und klicken Sie dann auf den Pfeil **Weiter**.

6.  Nach einer kurzen Verzögerung wird die Meldung angezeigt, dass Ihr Repository bereit ist.
    
    ![Git-Anweisungen](./media/publishing-with-git/git-instructions.png)

<h2><a id="Step5" ></a>Bereitstellen des Projekts</h2>


<h3><a id="Step6" ></a>Übertragen lokaler Dateien zu Azure mithilfe von Push (Lokales Git)</h3>


An diesem Punkt werden im Portal Anweisungen zum Initialisieren eines lokalen Repositorys und zum Hinzufügen von Dateien angezeigt. Sie haben dies bereits in den vorherigen Schritten dieses Themas durchgeführt. Wenn Sie jedoch Ihre Anmeldeinformationen für die Bereitstellung nicht eingerichtet haben, müssen Sie Schritt 3 der Anweisungen befolgen. Folgen Sie dazu dem Link mit der Bezeichnung **Zurücksetzen Ihrer Anmeldeinformationen für die Bereitstellung**.

Führen Sie die folgenden Schritte durch, um Ihre Website mit einem lokalen Git in Azure zu veröffentlichen:

1.  Stellen Sie mit der Befehlszeile sicher, dass Sie sich im Stammverzeichnis Ihres lokalen Git-Repositorys befinden, das die vorher erstellte Datei **index.html** enthält.

2.  Kopieren Sie den in Schritt 3 der vom Portal angezeigten Anweisungen aufgeführten Befehl "git remote add". Dieser ähnelt dem folgenden Befehl:
    
         git remote add azure https://username@needsmoregit.scm.azurewebsites.net:443/NeedsMoreGit.git


   
    > [WACOM.NOTE] Der Befehl **remote** fügt einem Remoterepositoryeinen benannten Verweis hinzu. In diesem Beispiel wird ein Verweis mit dem Namen "azure" für das Repository Ihrer Azure-Website erstellt.

3.  Verwenden Sie Folgendes an der Befehlszeile, um die aktuellen Repository-Inhalte aus dem lokalen Repository per Push in das "azure"-Remote-Repository zu übertragen:
    
        git push azure master
    
    Sie werden zum Eingeben des vorher beim Zurücksetzen Ihrer Anmeldeinformationen für die Bereitstellung im Portal erstellten Kennworts aufgefordert. Geben Sie das Kennwort ein (beachten Sie, dass Gitbash keine Sternchen in der Konsole anzeigt, wenn Sie Ihr Kennwort eingeben). Eine Ausgabe ähnlich der folgenden sollte
    angezeigt werden:
    
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


   
    > [WACOM.NOTE] Das für Ihre Azure-Website erstellte Repository erwartet Pushanforderungen für den **master**-Pfad des Repositorys, die dann als Inhalt der Website verwendet werden.

4.  Klicken Sie unten im Portal auf **DURCHSUCHEN**, um zu überprüfen, ob **index.html** bereitgestellt wurde. Eine Seite mit "Hallo Git!" wird eingeblendet.
    
    ![Eine Webseite mit "Hallo Git!"](./media/publishing-with-git/git-hello-git.png)

5.  Ändern Sie die Datei **index.html** mit einem Text-Editor so, dass sie "Juhu!" enthält, und speichern Sie dann die Datei.

6.  Verwenden Sie die folgenden Befehle an der Befehlszeile, um die Änderungen **hinzuzufügen** und zu **übernehmen**. Übertragen Sie die Änderungen dann per **Push** in das Remote-Repository:
    
         git add index.html
         git commit -m "Celebration"
         git push azure master
    
    Nach Abschluss des **Push**-Befehls aktualisieren Sie den Browser (möglicherweise müssen Sie Strg+F5 drücken, um den Browser ordnungsgemäß zu aktualisieren). Beachten Sie, dass der Inhalt der Seite jetzt die letzte übernommene Änderung wiedergibt.
    
    ![Eine Webseite mit
    "Juhu"](./media/publishing-with-git/git-yay.png)

<h3><a id="Step7" ></a>Bereitstellen von Dateien aus einer Repository-Website wie BitBucket, CodePlex, Dropbox, GitHub oder Mercurial</h3>


Das Übertragen mithilfe von Push lokaler Dateien zu Azure mithilfe von Local Git ermöglicht Ihnen das manuelle Übertragen von Updates aus einem lokalen Projekt mithilfe von Push auf Ihre Azure-Website, während das Bereitstellen von BitBucket, CodePlex, Dropbox, GitHub oder Mercurial zu einem fortlaufenden Bereitstellungsprozess führt, wenn Azure die aktuellen Updates mithilfe von Pull aus Ihrem Projekt überträgt.

Während beide Methoden dazu führen, dass Ihr Projekt auf einer Azure-Website bereitgestellt wird, ist die fortlaufende Bereitstellung nützlich, wenn mehrere Personen an einem Projekt arbeiten und Sie sicherstellen möchten, dass die aktuelle Version stets veröffentlicht wird -- unabhängig davon, wer das letzte Update durchgeführt hat. Die fortlaufende Bereitstellung ist ebenfalls nützlich, wenn Sie eines der oben erwähnten Tools als zentrales Repository für Ihre Anwendung verwenden.

Das Bereitstellen von Dateien über GitHub, CodePlex oder BitBucket setzt voraus, dass Sie Ihr lokales Projekt auf einem dieser Dienste veröffentlicht haben. Weitere Informationen zum Veröffentlichen Ihres Projekts auf diesen Diensten finden Sie unter [Erstellen eines Repositorys (GitHub)][5], [Verwenden von Git mit CodePlex][6], [Erstellen eines Repositorys (BitBucket)][7], [Verwenden von Dropbox zum
Freigeben von Git-Repositorys][8] oder [Schnellstart -- Mercurial][9].

1.  Kopieren Sie die Dateien Ihrer Website zunächst in das ausgewählte Repository, das für die fortlaufende Bereitstellung verwendet wird.

2.  Wechseln Sie im Azure-Portal für Ihre Website zur Registerkarte **DASHBOARD**. Wählen Sie im **Schnellansichts**abschnitt **Set up deployment from source control** aus. Das Dialogfeld **Set Up Deployment** wird mit der Frage **Where is your source code?** angezeigt.

3.  Wählen Sie die Quellcodeverwaltungsmethode aus, die Sie für die fortlaufende Bereitstellung verwenden möchten.

4.  Geben Sie bei Aufforderung die Anmeldeinformationen für den ausgewählten Dienst ein.

5.  Nachdem Sie Azure den Zugriff auf Ihr Konto gestattet haben, wird eine Liste mit Repositorys angezeigt.
    
    ![git-ChooseARepositoryToDeploy](./media/publishing-with-git/git-ChooseARepositoryToDeploy.png)

6.  Wählen Sie das Repository aus, das Sie Ihrer Azure-Website zuordnen möchten. Klicken Sie auf das Häkchen, um fortzufahren.


   
    > [WACOM.NOTE] Wenn Sie die fortlaufende Bereitstellung mit GitHub oder BitBucket aktivieren, werden öffentliche und private Projekte angezeigt.

7.  Azure erstellt eine Zuordnung für das ausgewählte Repository und überträgt die Dateien mithilfe von Pull aus der Hauptverzweigung. Nach Abschluss dieses Prozesses wird im **Bereitstellungsverlauf** auf der Seite **Bereitstellungen** eine Meldung **Aktive Bereitstellung** ähnlich der folgenden angezeigt:
    
    ![git-githubdeployed](./media/publishing-with-git/git-GitHubDeployed.png)

8.  An diesem Punkt wurde Ihr Projekt aus dem Repository Ihrer Wahl auf Ihrer Azure-Website bereitgestellt. Um zu überprüfen, ob die Website aktiv ist, klicken Sie unten im Portal auf den Link **Durchsuchen**. Der Browser sollte zur Website wechseln.

9.  Um zu überprüfen, ob die fortlaufende Bereitstellung stattfindet, nehmen Sie eine Änderung am Projekt vor und übertragen Sie das Update per Push in das Repository, das Sie dieser Website zugeordnet haben. Ihre Website sollte aktualisiert werden, um die Änderung kurz nach dem Push ins Repository wiederzugeben. Sie können auf der Seite **Bereitstellungen** Ihrer Website überprüfen, ob das Update abgerufen wurde.
    
	![git-GitHubDeployed-Updated][git-GitHubDeployed-Updated]
    
<h4>Funktionsweise der fortlaufenden Bereitstellung</h4>


Die fortlaufende Bereitstellung funktioniert durch Angabe der **BEREITSTELLUNGSAUSLÖSER-URL** im Abschnitt **Bereitstellungen** der Registerkarte **Konfigurieren** Ihrer Website.

![git-DeploymentTrigger](./media/publishing-with-git/git-DeploymentTrigger.png)

Wenn Updates am Repository vorgenommen werden, wird eine POST-Anforderung an diese URL gesendet, die Ihre Azure-Website benachrichtigt, dass das Repository aktualisiert wurde. An diesem Punkt wird das Update abgerufen und auf Ihrer Website bereitgestellt.

<h4>Angeben der zu verwendenden Verzweigung</h4>


Wenn Sie die fortlaufende Bereitstellung aktivieren, befindet sie sich standardmäßig in der **master**-Verzweigung des Repositorys. Wenn Sie eine andere Verzweigung verwenden, führen Sie die folgenden Schritte aus:

1.  Wählen Sie im Portal Ihre Website und dann **KONFIGURIEREN** aus.

2.  Geben Sie im Abschnitt **Bereitstellungen** der Seite im Feld **BRANCH TO DEPLOY** die Verzweigung ein, die Sie verwenden möchten, und drücken Sie die Eingabetaste. Klicken Sie schließlich auf **SPEICHERN**.
    
    Azure sollte das Update basierend auf Änderungen an der neuen Verzweigung sofort beginnen.

<h4>Deaktivieren der fortlaufenden Bereitstellung</h4>


Die fortlaufende Bereitstellung kann im Azure-**Dashboard** deaktiviert werden. Wählen Sie im **Schnellansichts**abschnitt die Option zum Trennen vom verwendeten Repository aus:

![git-DisconnectFromGitHub](./media/publishing-with-git/git-DisconnectFromGitHub.png)

Nachdem Sie die Bestätigungsmeldung mit **Ja** beantwortet haben, kehren Sie in die **Schnellansicht** zurück und klicken auf **Set up deployment from source control**, wenn Sie die Veröffentlichung von einer anderen Quelle einrichten möchten.

<h2><a id="Step8" ></a>Problembehandlung</h2>


Die folgenden Fehler und Probleme treten häufiger auf, wenn Git zum Veröffentlichen auf einer Azure-Website verwendet wird:

* * *

**Symptom**: Couldn't resolve host 'hostname'

**Ursache**: Dieser Fehler kann auftreten, wenn die Adressinformationen, die beim Erstellen der 'azure'-Remotewebsite eingegeben wurden, falsch waren.

**Lösung**: Verwenden Sie den Befehl `git remote -v`, um alle Remote-Websites zusammen mit der jeweils zugehörigen URL aufzulisten. Überprüfen Sie, ob die URL für die 'azure'-Remotewebsite korrekt ist. Entfernen Sie diese Remote-Website bei Bedarf und erstellen Sie sie mit der korrekten URL neu.

* * *

**Symptom**: No refs in common and none specified; doing nothing. Sie sollten eine Verzweigung wie 'master' angeben.

**Ursache**: Dieser Fehler kann auftreten, wenn Sie beim Ausführen eines Git-Push-Vorgangs keine Verzweigung angeben und den von Git verwendeten "push.default"-Wert nicht festgelegt haben.

**Lösung**: Führen Sie den Push-Vorgang unter Angabe der Hauptverzweigung erneut durch. Zum Beispiel:

    git push azure master

* * *

**Symptom**: src refspec [branchname] does not match any.

**Ursache**: Dieser Fehler kann auftreten, wenn Sie versuchen, auf eine andere Verzweigung als die Hauptverzweigung auf der 'azure'-Remotewebsite per Push zu übertragen.

**Lösung**: Führen Sie den Push-Vorgang unter Angabe der Hauptverzweigung erneut durch. Zum Beispiel:

    git push azure master

* * *

**Symptom**: Error - Changes commited to remote repository but your web site not updated.

**Ursache**: Dieser Fehler kann auftreten, wenn Sie eine "Node.js"-Anwendung mit einer Datei "package.json" bereitstellen, die zusätzliche erforderliche Module angibt.

**Lösung**: Zusätzliche Meldungen mit "npm ERR!" sollten vor diesem Fehler protokolliert werden und können zusätzlichen Kontext für diesen Fehler bereitstellen. Es folgen bekannte Ursachen für diesen Fehler und die entsprechende "npm ERR!"-Meldung:

* **Fehlerhafte "package.json"-Datei**: npm ERR! Couldn't read dependencies.

* **Systemeigenes Modul ohne binäre Distribution für Windows**:
  
  * npm ERR! \`cmd "/c" "node-gyp rebuild"\` failed with 1
    
    ODER
  
  * npm ERR! [modulename@version] preinstall: \`make \|\| gmake\`

## Zusätzliche Ressourcen

* [Verwenden von PowerShell für Azure][10]
* [Verwenden des Azure-Befehlszeilentools für Mac und
  Linux](/en-us/develop/nodejs/how-to-guides/command-line-tools/)
* [Git-Dokumentation][11]
[git-GitHubDeployed-Updated]: ./media/publishing-with-git/git-GitHubDeployed-Updated.png


[1]: http://www.windowsazure.com/en-us/develop/net/common-tasks/publishing-with-tfs/
[2]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[3]: http://www.windowsazure.com/en-us/develop/overview/
[4]: http://manage.windowsazure.com
[5]: https://help.github.com/articles/create-a-repo
[6]: http://codeplex.codeplex.com/wikipage?title=Using%20Git%20with%20CodePlex&referringTitle=Source%20control%20clients&ProjectName=codeplex
[7]: https://confluence.atlassian.com/display/BITBUCKET/Create+an+Account+and+a+Git+Repo
[8]: https://gist.github.com/trey/2722927
[9]: http://mercurial.selenic.com/wiki/QuickStart
[10]: http://www.windowsazure.com/en-us/develop/nodejs/how-to-guides/powershell-cmdlets/
[11]: http://git-scm.com/documentation