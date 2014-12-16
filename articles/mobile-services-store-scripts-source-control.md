<properties urlDisplayName="Store server scripts in source control" pageTitle="Speichern von Serverskripts in der Quellcodeverwaltung - Azure Mobile Services" metaKeywords="" description="Learn how to store your server script files and modules in a local Git repo on your computer." metaCanonical="" services="" documentationCenter="Mobile" title="Store server scripts in source control" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />


# Speichern von Serverskripts in der Quellcodeverwaltung

Dieser Artikel beschreibt die Einrichtung einer Quellcodeverwaltung für Azure Mobile Services, um Ihre Serverskripts in einem Git-Repository zu speichern. Sie können Skripts und andere JavaScript-Codedateien aus Ihrem lokalen Repository in Ihre Produktionsumgebung im mobilen Dienst befördern. Außerdem lernen Sie, freigegebenen Code zur Verwendung in mehreren Skripts zu definieren und Node.js-Module hochzuladen. 

Dieses Lernprogramm führt Sie durch die folgenden Schritte:

1. [Aktivieren der Quellcodeverwaltung für Ihren mobilen Dienst].
2. [Installieren von Git und Erstellen des lokalen Repositorys].
3. [Bereitstellen geänderter Skriptdateien in Ihrem mobilen Dienst].
4. [Verwenden von freigegebenem Code und Node.js-Modulen in Ihren Serverskripts].

Dieses Lernprogramm setzt voraus, dass Sie bereits einen mobilen Dienst aus einem der Lernprogramme [Erste Schritte mit Mobile Services] bzw. [Erste Schritte mit Daten] erstellt haben.

<h2><a name="enable-source-control"></a>Aktivieren der Quellcodeverwaltung für Ihren mobilen Dienst</h2>

1. Melden Sie sich beim [Azure-Verwaltungsportal] an, klicken Sie auf **Mobile Services** und anschließend auf Ihren mobilen Dienst.

	![][0]

2. Klicken Sie auf die Registerkarte **Dashboard**, anschließend unter **Schnellübersicht** auf **Quellcodeverwaltung einrichten** und zuletzt als Bestätigung auf **Ja**.

	![][1]

	> [WACOM.NOTE]
	> Die Quellcodeverwaltung ist ein Vorschaufeature. Sie sollten Ihre Skriptdateien regelmäßig sichern, auch wenn diese in Mobile Services gespeichert sind.

3. Geben Sie **Benutzername** und **Neues Kennwort** ein, bestätigen Sie das Kennwort und klicken Sie auf die Schaltfläche Prüfen. 

	![][2]

	Das Git-Repository wird nun in Ihrem mobilen Dienst erstellt. Notieren Sie die verwendeten Anmeldeinformationen; Sie benötigen diese für den Zugriff auf Ihr Repository.

4. Klicken Sie auf die Registerkarte Konfigurieren und sehen Sie sich die neuen **Quellcodeverwaltung**-Felder an.

	![][3]

	Die URL des Git-Repositorys wird ebenfalls angezeigt. Sie werden diese URL verwenden, um das Repository auf Ihrem lokalen Computer zu klonen.

Sie haben nun die Quellcodeverwaltung für Ihren mobilen Dienst aktiviert. Nun können Sie das Repository mithilfe von Git auf Ihrem lokalen Computer klonen.

<h2><a name="clone-repo"></a>Installieren von Git und Erstellen des lokalen Repositorys</h2>

1. Installieren Sie Git auf Ihrem lokalen Computer. 

	Die erforderlichen Schritte zum Installieren von Git variieren je nach Betriebssystem. Informationen zu betriebssystemspezifischen Distributionen und zur Installation finden Sie unter [Installieren von Git].

	> [WACOM.NOTE]
	> In einigen Betriebssystemen sind eine Befehlszeilenversion und eine GUI-Version von Git verfügbar. In den in diesem Artikel bereitgestellten Anweisungen wird die Befehlszeilenversion verwendet.

2. Öffnen Sie eine Befehlszeile, z. B. **GitBash** (Windows) oder **Bash** (Unix Shell). Auf OS X-Systemen können Sie über die **Terminal**-Anwendung auf die Befehlszeile zugreifen.

3. Wechseln Sie in der Befehlszeile zu dem Verzeichnis, in dem Sie Ihre Skripts speichern werden. Beispiel: `cd SourceControl`.

4. Erstellen Sie mit dem folgenden Befehl eine lokale Kopie Ihres neuen Git-Repositorys. Ersetzen Sie `<your_git_URL>` durch die URL Ihres Git-Repositorys für den mobilen Dienst:

		git clone <your_git_URL>

5. Geben Sie Benutzername und Kennwort ein, die Sie bei der Aktivierung der Quellcodeverwaltung in Ihrem mobilen Dienst festgelegt haben. Nach der erfolgreichen Anmeldung sehen Sie eine Reihe von Antworten wie folgt:

		remote: Counting objects: 8, done.
		remote: Compressing objects: 100% (4/4), done.
		remote: Total 8 (delta 1), reused 0 (delta 0)
		Unpacking objects: 100% (8/8), done.

6. Wechseln Sie in das Verzeichnis, indem Sie den 'git clone'-Befehl ausgeführt haben, und beachten Sie die folgende Verzeichnisstruktur:

	![4][4]

	In diesem Fall wurde ein neues Verzeichnis mit dem Namen des mobilen Dienstes erstellt, das als lokales Repository für den Datendienst dient. 

7. Öffnen Sie das Unterverzeichnis .\service\table und beachten Sie, dass dieses eine TodoItem.json-Datei enthält. Dabei handelt es sich um eine JSON-Darstellung der Operationsberechtigungen für die Tabelle TodoItem. 

		Falls Sie Serverskripts für diese Tabelle definiert haben, sehen Sie außerdem eine oder mehrere Dateien mit dem Namen <code>TodoItem._&lt;operation&gt;_.js</code>. Diese Dateien enthalten die Skripts für die jeweilige Tabellenoperation. Zeitplanskripts und benutzerdefinierte API-Skripts werden in separaten Ordnern mit den jeweiligen Namen abgelegt. Weitere Informationen finden Sie unter [Quellcodeverwaltung].

Sie haben nun Ihr lokales Repository erstellt und können Änderungen an Serverskripts vornehmen und diese Änderungen anschließend in Ihren mobilen Dienst befördern.

<h2><a name="deploy-scripts"></a>Bereitstellen geänderter Skriptdateien in Ihrem mobilen Dienst</h2>

1. Öffnen Sie das Unterverzeichnis .\service\table und erstellen Sie die Datei todoitem.insert.js, falls diese dort noch nicht existiert.

2. Öffnen Sie die Datei todoitem.insert.js in einem Text-Editor, fügen Sie den folgenden Code ein und speichern Sie Ihre Änderungen:

		function insert(item, user, request) {
		    request.execute();
		    console.log(JSON.stringify(item, null, 4));
		}
	
	Dieser Code schreibt das eingefügte Element in das Protokoll. Falls diese Datei bereits Code enthält, fügen Sie einfach etwas gültigen JavaScript-Code ein, wie z. B. einen Aufruf an `console.log()`, und speichern Sie Ihre Änderungen. 

3. Geben Sie den folgenden Befehl in der Git-Eingabeaufforderung ein, um die neue Skriptdatei zur Quellcodeverwaltung hinzuzufügen:

		$ git add .
	

4. Geben Sie den folgenden Befehl ein, um die Änderungen nach Git zu übernehmen:

		$ git commit -m "updated the insert script"

5. Geben Sie den folgenden Befehl ein, um die Änderungen in das Remote-Repository zu übertragen:

		$ git push origin master
	
	Daraufhin wird eine Reihe von Ausgaben für die Bereitstellung des Commits im mobilen Dienst angezeigt.

6. Klicken Sie im Verwaltungsportal auf die Registerkarte **Daten** und dann auf die Tabelle **TodoItem**.

	![][5]

3. Klicken Sie auf **Skript**, und dann auf den Vorgang **Einfügen**.

	![][6]

	Beachten Sie, dass das angezeigte Skript für die Einfügen-Operation denselben JavaScript-Code enthält, den Sie soeben in Ihr Repository hochgeladen haben.

<h2><a name="use-npm"></a>Verwenden von freigegebenem Code und Node.js-Modulen in Ihren Serverskripts</h2>
Mobile Services bietet Zugang zum kompletten Umfang der Node.js-Basismodule, und Sie können diese Module in Ihrem Code mit der **require**-Funktion einbinden. Außerdem kann Ihr mobiler Dienst Node.js-Module verwenden, die nicht Teil des Node.js-Standardpakets sind, und Sie können sogar eigenen freigegebenen Code als Node.js-Modul definieren. Weitere Informationen zur Erstellung von Modulen finden Sie in der Dokumentation [Modules][Node.js API: Module] in der Node.js-API-Referenzdokumentation.

Anschließend fügen Sie das Node.js-Modul [node-uuid] mithilfe der Quellcodeverwaltung und des Node.js-Paket-Managers (NPM) zu Ihrem mobilen Dienst hinzu. Mit diesem Modul können Sie nun neue GUID-Werte für die **uuid**-Eigenschaft in eingefügten Elementen generieren. 

1. Installieren Sie "Node.js" auf Ihrem lokalen Computer, falls Sie dies noch nicht getan haben. Folgen Sie dazu den Schritten auf der <a href="http://nodejs.org/" target="_blank">Node.js-Website</a>. 

2. Öffnen Sie den Ordner `.\service` in Ihrem lokalen Git-Repository und führen Sie den folgenden Befehl in der Eingabeaufforderung aus:

		npm install node-uuid

	NPM erstellt das Verzeichnis `node_modules` am aktuellen Ort und installiert das Modul [node-uuid] im Unterverzeichnis `\node-uuid`. 

	<div class="dev-callout">
	<strong>Hinweis</strong>
	<p>Falls <code>node_modules</code>  bereits in der Verzeichnishierarchie existiert, erstellt NPM das Unterverzeichnis <code>\node-uuid</code>  dort, anstatt ein neues <code>node_modules</code> -Verzeichnis im Repository anzulegen. In diesem Fall können Sie das existierende <code>node_modules</code> -Verzeichnis löschen.</p>
	</div>

4. Öffnen Sie nun das Unterverzeichnis .\service\table, öffnen Sie die Datei todoitem.insert.js und ändern Sie den Code wie folgt:

		function insert(item, user, request) {
		    var uuid = require('node-uuid');
		    item.uuid = uuid.v1();
		    request.execute();
		    console.log(JSON.stringify(item, null, 4));
		}

	Dieser Code fügt eine uuid-Spalte zur Tabelle hinzu und füllt diese Spalte mit eindeutigen GUID-Bezeichnern.

5. Geben Sie wie bereits im vorigen Abschnitt die folgenden Befehle in der Git-Eingabeaufforderung ein: 

		$ git add .
		$ git commit -m "added node-uuid module"
		$ git push origin master
		
	Daraufhin wird die neue Datei hinzugefügt, Ihre Änderungen übernommen und das neue Modul node-uuid sowie die Änderungen am Skript todoitem.insert.js an Ihren mobilen Dienst übertragen.

## <a name="next-steps"> </a>Nächste Schritte

Nach Abschluss dieses Lernprogramms sind Sie in der Lage, Ihre Skripts in einer Quellcodeverwaltung zu speichern. Die folgenden Artikel enthalten weiterführende Informationen zu Serverskripts und benutzerdefinierten APIs: 

+ [Arbeiten mit Serverskripts in Mobile Services]
	<br/>Veranschaulicht die Arbeit mit Serverskripts, Auftragsplaner und benutzerdefinierten APIs.

+ [Definieren benutzerdefinierter APIs mit Unterstützung von Pull-Benachrichtigungen] 
	<br/> Zeigt die Verwendung benutzerdefinierter APIs, um periodische Benachrichtigungen zu unterstützen, die live-Kacheln in einer Windows Store-App aktualisieren.

<!-- Anchors. -->
[Aktivieren der Quellcodeverwaltung für Ihren mobilen Dienst]: #enable-source-control
[Installieren von Git und Erstellen des lokalen Repositorys]: #clone-repo
[Bereitstellen geänderter Skriptdateien in Ihrem mobilen Dienst]: #deploy-scripts
[Verwenden von freigegebenem Code und Node.js-Modulen in Ihren Serverskripts]: #use-npm

<!-- Images. -->
[0]: ./media/mobile-services-store-scripts-source-control/mobile-services-selection.png
[1]: ./media/mobile-services-store-scripts-source-control/mobile-setup-source-control.png
[2]: ./media/mobile-services-store-scripts-source-control/mobile-source-control-credentials.png
[3]: ./media/mobile-services-store-scripts-source-control/mobile-source-control-configure.png
[4]: ./media/mobile-services-store-scripts-source-control/mobile-source-local-repo.png
[5]: ./media/mobile-services-store-scripts-source-control/mobile-portal-data-tables.png
[6]: ./media/mobile-services-store-scripts-source-control/mobile-insert-script-source-control.png

<!-- URLs. -->
[Git-Website]: http://git-scm.com
[Quellcodeverwaltung]: http://msdn.microsoft.com/de-de/library/windowsazure/c25aaede-c1f0-4004-8b78-113708761643
[Installieren von Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started
[Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-dotnet
[Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-dotnet
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/develop/mobile/tutorials/get-started-with-push-dotnet
[Autorisieren von Benutzern mit Skripts]: /de-de/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[Arbeiten mit Serverskripts in Mobile Services]: /de-de/develop/mobile/how-to-guides/work-with-server-scripts
[JavaScript und HTML]: /de-de/develop/mobile/tutorials/get-started-with-users-js
[WindowsAzure.com]: http://www.windowsazure.com/
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Definieren benutzerdefinierter APIs mit Unterstützung von Pull-Benachrichtigungen ]: /de-de/develop/mobile/tutorials/create-pull-notifications-dotnet
[Node.js-API-Dokumentation: Module]: http://nodejs.org/api/modules.html
[node-uuid]: https://npmjs.org/package/node-uuid
