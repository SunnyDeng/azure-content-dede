<properties linkid="develop-mobile-tutorials-store-scripts-in-source-control" urlDisplayName="Store server scripts in source control" pageTitle="Store server scripts in source control - Azure Mobile Services" metaKeywords="" description="Learn how to store your server script files and modules in a local Git repo on your computer." metaCanonical="" services="" documentationCenter="Mobile" title="Store server scripts in source control" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Speichern von Serverskripts in der Quellcodeverwaltung

Dieser Artikel beschreibt die Einrichtung einer Quellcodeverwaltung für Azure Mobile Services, um Ihre Serverskripts in einem Git-Repository zu speichern. Sie können Skripts und andere JavaScript-Codedateien aus Ihrem lokalen Repository in Ihre Produktionsumgebung im mobilen Dienst befördern. Außerdem lernen Sie, freigegebenen Code zur Verwendung in mehreren Skripts zu definieren und Node.js-Module hochzuladen.

Dieses Lernprogramm führt Sie durch die folgenden Schritte:

1.  [Aktivieren der Quellcodeverwaltung für Ihren mobilen Dienst][Aktivieren der Quellcodeverwaltung für Ihren mobilen Dienst].
2.  [Installieren von Git und Erstellen des lokalen Repositorys][Installieren von Git und Erstellen des lokalen Repositorys].
3.  [Bereitstellen geänderter Skriptdateien in Ihrem mobilen Dienst][Bereitstellen geänderter Skriptdateien in Ihrem mobilen Dienst].
4.  [Verwenden von freigegebenem Code und Node.js-Modulen in Ihren Serverskripts][Verwenden von freigegebenem Code und Node.js-Modulen in Ihren Serverskripts].

Dieses Lernprogramm setzt voraus, dass Sie bereits einen mobilen Dienst aus einem der Lernprogramme [Erste Schritte mit Mobile Services][Erste Schritte mit Mobile Services] bzw. [Erste Schritte mit Daten][Erste Schritte mit Daten] erstellt haben.

## <a name="enable-source-control"></a><span class="short-header">Aktivieren der Quellcodeverwaltung</span>Aktivieren der Quellcodeverwaltung für Ihren mobilen Dienst

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an, klicken Sie auf **Mobile Services** und dann auf Ihren mobilen Dienst.

    ![][]

2.  Klicken Sie auf die Registerkarte **Dashboard**, anschließend unter **Schnellübersicht** auf **Quellcodeverwaltung einrichten** und zuletzt als Bestätigung auf **Ja**.

    ![][1]

    > [WACOM.NOTE]
    > Die Quellcodeverwaltung ist ein Vorschaufeature. Sie sollten Ihre Skriptdateien regelmäßig sichern, auch wenn diese in Mobile Services gespeichert sind.

3.  Geben Sie **Benutzername** und **Neues Kennwort** ein, bestätigen Sie das Kennwort und klicken Sie auf die Schaltfläche Prüfen.

    ![][2]

    Das Git-Repository wird nun in Ihrem mobilen Dienst erstellt. Notieren Sie die verwendeten Anmeldeinformationen; Sie benötigen diese für den Zugriff auf Ihr Repository.

4.  Klicken Sie auf die Registerkarte Konfigurieren und sehen Sie sich die neuen **Quellcodeverwaltung**-Felder an.

    ![][3]

    Die URL des Git-Repositorys wird ebenfalls angezeigt. Sie werden diese URL verwenden, um das Repository auf Ihrem lokalen Computer zu klonen.

Sie haben nun die Quellcodeverwaltung für Ihren mobilen Dienst aktiviert. Nun können Sie das Repository mithilfe von Git auf Ihrem lokalen Computer klonen.

## <a name="clone-repo"></a><span class="short-header">Klonen des Repositorys</span>Installieren von Git und Erstellen des lokalen Repositorys

1.  Installieren Sie Git auf Ihrem lokalen Computer.

    Die erforderlichen Schritte zum Installieren von Git variieren je nach Betriebssystem. Informationen zu Betriebssystem-spezifischen Distributionen und zur Installation finden Sie unter [Installieren von Git][Installieren von Git].

    > [WACOM.NOTE]
    > In einigen Betriebssystemen sind eine Befehlszeilenversion und eine GUI-Version von Git verfügbar. Die in diesem Artikel bereitgestellten Anweisungen verwenden die Befehlszeilenversion.

2.  Öffnen Sie eine Befehlszeile, zum Beispiel **GitBash** (Windows) oder **Bash** (Unix Shell). Auf OS X-Systemen können Sie auf die Befehlszeile über die **Terminal**-Anwendung zugreifen.

3.  Wechseln Sie in der Befehlszeile zu dem Verzeichnis, in dem Sie Ihre Skripts speichern werden. Beispiel: `cd SourceControl`.

4.  Erstellen Sie mit dem folgenden Befehl eine lokale Kopie Ihres neuen Git-Repositorys. Ersetzen Sie `<your_git_URL>` durch die URL Ihres Git-Repositorys für den mobilen Dienst:

        git clone <your_git_URL>

5.  Geben Sie Benutzername und Kennwort ein, die Sie bei der Aktivierung der Quellcodeverwaltung in Ihrem mobilen Dienst festgelegt haben. Nach der erfolgreichen Anmeldung sehen Sie eine Reihe von Antworten wie folgt:

        remote: Counting objects: 8, done.
        remote: Compressing objects: 100% (4/4), done.
        remote: Total 8 (delta 1), reused 0 (delta 0)
        Unpacking objects: 100% (8/8), done.

6.  Wechseln Sie in das Verzeichnis, in dem Sie den `git clone`-Befehl ausgeführt haben, und beachten Sie die folgende Verzeichnisstruktur:

    ![4][4]

    In diesem Fall wurde ein neues Verzeichnis mit dem Namen des mobilen Dienstes erstellt. das als lokales Repository für den Datendienst dient.

7.  Öffnen Sie das Unterverzeichnis .\\service\\table und beachten Sie, dass dieses eine TodoItem.json-Datei enthält. Dabei handelt es sich um eine JSON-Darstellung der Operationsberechtigungen für die Tabelle TodoItem.

    Falls Sie Serverskripts für diese Tabelle definiert haben, sehen Sie außerdem mindestens eine Datei, die den Namen `TodoItem.<operation>.js` aufweist und die Skripts für den angegebenen Tabellenvorgang enthält. Zeitplanskripts und benutzerdefinierte API-Skripts werden in separaten Ordnern mit den jeweiligen Namen abgelegt. Weitere Informationen finden Sie unter [Quellcodeverwaltung][Quellcodeverwaltung].

Sie haben nun Ihr lokales Repository erstellt und können Änderungen an Serverskripts vornehmen und diese Änderungen anschließend in Ihren mobilen Dienst befördern.

## <a name="deploy-scripts"></a><span class="short-header">Bereitstellen von Skripts</span>Bereitstellen geänderter Skriptdateien in Ihrem mobilen Dienst

1.  Öffnen Sie das Unterverzeichnis .\\service\\table und erstellen Sie die Datei todoitem.insert.js, falls diese dort noch nicht existiert.

2.  Öffnen Sie die Datei todoitem.insert.js in einem Text-Editor, fügen Sie den folgenden Code ein und speichern Sie Ihre Änderungen:

        function insert(item, user, request) {
            request.execute();
            console.log(JSON.stringify(item, null, 4));
        }

    Dieser Code schreibt das eingefügte Element in das Protokoll. Falls diese Datei bereits Code enthält, fügen Sie einfach gültigen JavaScript-Code hinzu, z. B. einen Aufruf an `console.log()`, und speichern Sie Ihre Änderungen.

3.  Geben Sie den folgenden Befehl in der Git-Eingabeaufforderung ein, um die neue Skriptdatei zur Quellcodeverwaltung hinzuzufügen:

        $ git add .

4.  Geben Sie den folgenden Befehl ein, um die Änderungen nach Git zu übernehmen:

        $ git commit -m "updated the insert script"

5.  Geben Sie den folgenden Befehl ein, um die Änderungen in das Remote-Repository zu übertragen:

        $ git push origin master

    Daraufhin wird eine Reihe von Ausgaben für die Bereitstellung des Commits im mobilen Dienst angezeigt.

6.  Zurück im Verwaltungsportal klicken Sie auf die Registerkarte **Daten** und dann auf die Tabelle **TodoItem**.

    ![][5]

7.  Klicken Sie auf **Skript**, und wählen Sie dann den Vorgang **Einfügen**.

    ![][6]

    Beachten Sie, dass das angezeigte Skript für die Einfügen-Operation denselben JavaScript-Code enthält, den Sie soeben in Ihr Repository hochgeladen haben.

## <a name="use-npm"></a><span class="short-header">Freigegebener Code und Module</span>von freigegebenem Code und Node.js-Modulen in Ihren Serverskripts

Mobile Services bietet Zugang zum kompletten Umfang der Node.js-Basismodule, und Sie können diese Module in Ihrem Code mit der **require**-Funktion einbinden. Außerdem kann Ihr mobiler Dienst Node.js-Module verwenden, die nicht Teil des Node.js-Standardpakets sind, und Sie können sogar eigenen freigegebenen Code als Node.js-Modul definieren. Weitere Informationen zur Erstellung von Modulen finden Sie unter [Module][Module] in der Referenzdokumentation der Node.js-API.

Anschließend fügen Sie das Node.js-Modul [node-uuid][node-uuid] mithilfe der Quellcodeverwaltung und des Node.js-Paket-Managers (NPM) zu Ihrem mobilen Dienst hinzu. Mit diesem Modul können Sie nun neue GUID-Werte für die **uuid**-Eigenschaft in eingefügten Elementen generieren.

1.  Installieren Sie "Node.js" auf Ihrem lokalen Computer, falls Sie dies noch nicht getan haben. Folgen Sie dazu den Schritten auf der [Node.js-Website][Node.js-Website].

2.  Navigieren Sie zum Ordner `.\service` in Ihrem lokalen Git-Repository, und führen Sie den folgenden Befehl in der Eingabeaufforderung aus:

        npm install node-uuid

    NPM erstellt das Verzeichnis `node_modules` am aktuellen Ort und installiert das Modul [node-uuid][node-uuid] im Unterverzeichnis `\node-uuid`.

    <div class="dev-callout">
<strong>Hinweis</strong>
<p>Falls <code data-inline="1">node_modules</code> bereits in der Verzeichnishierarchie existiert, erstellt NPM das Unterverzeichnis <code data-inline="1">\node-uuid</code> dort, anstatt ein neues Verzeichnis <code data-inline="1">node_modules</code> im Repository anzulegen. In diesem Fall k&ouml;nnen Sie das existierende Verzeichnis <code data-inline="1">node_modules</code> l&ouml;schen.</p>
</div>

3.  Öffnen Sie nun das Unterverzeichnis .\\service\\table, öffnen Sie die Datei todoitem.insert.js und ändern Sie den Code wie folgt:

        function insert(item, user, request) {
            var uuid = require('node-uuid');
            item.uuid = uuid.v1();
            request.execute();
            console.log(JSON.stringify(item, null, 4));
        }

    Dieser Code fügt eine uuid-Spalte zur Tabelle hinzu und füllt diese Spalte mit eindeutigen GUID-Bezeichnern.

4.  Geben Sie wie bereits im vorigen Abschnitt die folgenden Befehle in der Git-Eingabeaufforderung ein:

        $ git add .
        $ git commit -m "added node-uuid module"
        $ git push origin master

    Daraufhin wird die neue Datei hinzugefügt, Ihre Änderungen übernommen und das neue Modul node-uuid sowie die Änderungen am Skript todoitem.insert.js an Ihren mobilen Dienst übertragen.

## <a name="next-steps"> </a>Nächste Schritte

Nach Abschluss dieses Lernprogramms sind Sie in der Lage, Ihre Skripts in einer Quellcodeverwaltung zu speichern. Die folgenden Artikel enthalten weiterführende Informationen zu Serverskripts und benutzerdefinierten APIs:

-   [Arbeiten mit Serverskripts in Mobile Services][Arbeiten mit Serverskripts in Mobile Services]
    Beschreibt die Verwendung von Serverskripts, Auftragsplaner und benutzerdefinierten APIs.

-   [Definieren einer benutzerdefinierten API, die Pullbenachrichtigungen unterstützt][Definieren einer benutzerdefinierten API, die Pullbenachrichtigungen unterstützt]
     Beschreibt die Verwendung benutzerdefinierter APIs zur Unterstützung regelmäßiger Benachrichtigungen, mit denen Live-Kacheln in einer Windows Store-App aktualisiert werden.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Aktivieren der Quellcodeverwaltung für Ihren mobilen Dienst]: #enable-source-control
  [Installieren von Git und Erstellen des lokalen Repositorys]: #clone-repo
  [Bereitstellen geänderter Skriptdateien in Ihrem mobilen Dienst]: #deploy-scripts
  [Verwenden von freigegebenem Code und Node.js-Modulen in Ihren Serverskripts]: #use-npm
  [Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started
  [Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-dotnet
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  []: ./media/mobile-services-store-scripts-source-control/mobile-services-selection.png
  [1]: ./media/mobile-services-store-scripts-source-control/mobile-setup-source-control.png
  [2]: ./media/mobile-services-store-scripts-source-control/mobile-source-control-credentials.png
  [3]: ./media/mobile-services-store-scripts-source-control/mobile-source-control-configure.png
  [Installieren von Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
  [4]: ./media/mobile-services-store-scripts-source-control/mobile-source-local-repo.png
  [Quellcodeverwaltung]: http://msdn.microsoft.com/de-de/library/windowsazure/c25aaede-c1f0-4004-8b78-113708761643
  [5]: ./media/mobile-services-store-scripts-source-control/mobile-portal-data-tables.png
  [6]: ./media/mobile-services-store-scripts-source-control/mobile-insert-script-source-control.png
  [Module]: http://nodejs.org/api/modules.html
  [node-uuid]: https://npmjs.org/package/node-uuid
  [Node.js-Website]: http://nodejs.org/
  [Arbeiten mit Serverskripts in Mobile Services]: /de-de/develop/mobile/how-to-guides/work-with-server-scripts
  [Definieren einer benutzerdefinierten API, die Pullbenachrichtigungen unterstützt]: /de-de/develop/mobile/tutorials/create-pull-notifications-dotnet
