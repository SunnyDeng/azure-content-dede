<properties urlDisplayName="Store JavaScript project code in source control" pageTitle="Speichern von Projektcode in der Quellcodeverwaltung - Azure Mobile Services" metaKeywords="" description="Learn how to store your server script files and modules in a local Git repo on your computer." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Store project code in source control" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="glenga" />

<div class="dev-center-tutorial-subselector">
	<a href="/de-de/documentation/articles/mobile-services-dotnet-backend-store-code-source-control/" title=".NET backend">.NET-Back-End</a> | <a href="/de-de/documentation/articles/mobile-services-store-scripts-source-control/"  title="JavaScript backend" class="current">JavaScript-Back-End</a>
</div>

# Speichern von Projektcode in der Quellcodeverwaltung

In diesem Thema erfahren Sie, wie Sie die von Azure Mobile Services bereitgestellte Quellcodeverwaltung verwenden, um Ihre Serverskripts zu speichern. Sie können Skripts und andere JavaScript-Back-End-Codedateien aus Ihrem lokalen Git-Repository in Ihre Produktionsumgebung im mobilen Dienst befördern. Außerdem erfahren Sie, wie Sie freigegebenen Code definieren, der möglicherweise für mehrere Skripts erforderlich ist, und wie Sie die Datei "package.jason" verwenden, um Ihrem mobilen Dienst Node.js-Module hinzuzufügen. 

Dieses Lernprogramm führt Sie durch die folgenden Schritte:

1. [Aktivieren der Quellcodeverwaltung für Ihren mobilen Dienst]
2. [Installieren von Git und Erstellen des lokalen Repositorys]
3. [Bereitstellen aktualisierter Skriptdateien an Ihren mobilen Dienst].
4. [Verwenden von freigegebenem Code und Node.js-Modulen in Ihren Serverskripts].

Für dieses Lernprogramm wird vorausgesetzt, dass Sie bereits einen mobilen Dienst aus einem der Lernprogramme [Erste Schritte mit Mobile Services] oder [Hinzufügen von Mobile Services zu einer vorhandenen App] erstellt haben.

##<a name="enable-source-control"></a>Aktivieren der Quellcodeverwaltung in Ihrem mobilen Dienst

[WACOM.INCLUDE [mobile-services-enable-source-control](../includes/mobile-services-enable-source-control.md)]

##<a name="clone-repo"></a>Installieren von Git und Erstellen des lokalen Repositorys

1. Installieren Sie Git auf Ihrem lokalen Computer. 

	Die erforderlichen Schritte zum Installieren von Git variieren je nach Betriebssystem. Informationen zu betriebssystemspezifischen Distributionen und zur Installation finden Sie unter [Installieren von Git].

	> [WACOM.NOTE]
	Einige Betriebssysteme verfügen über eine Befehlszeilenversion und eine GUI-Version von Git. Die in diesem Artikel bereitgestellten Anweisungen verwenden die Befehlszeilenversion.

2. Öffnen Sie eine Befehlszeile, zum Beispiel **GitBash** (Windows) oder **Bash** (Unix Shell). Auf OS X-Systemen können Sie über die **Terminal**-Anwendung auf die Befehlszeile zugreifen.

3. Wechseln Sie in der Befehlszeile zu dem Verzeichnis, in dem Sie Ihre Skripts speichern werden. Zum Beispiel "cd SourceControl".

4. Erstellen Sie mit dem folgenden Befehl eine lokale Kopie Ihres neuen Git-Repositorys. Ersetzen Sie dabei <your_git_URL> durch die URL Ihres Git-Repositorys für den mobilen Dienst:

		git clone <your_git_URL>

5. Geben Sie Benutzername und Kennwort ein, die Sie bei der Aktivierung der Quellcodeverwaltung in Ihrem mobilen Dienst festgelegt haben. Nach der erfolgreichen Anmeldung sehen Sie eine Reihe von Antworten wie folgt:

		remote: Counting objects: 8, done.
		remote: Compressing objects: 100% (4/4), done.
		remote: Total 8 (delta 1), reused 0 (delta 0)
		Unpacking objects: 100% (8/8), done.

6. Wechseln Sie in das Verzeichnis, von dem Sie den Befehl "git clone" ausgeführt haben, und beachten Sie die folgende Verzeichnisstruktur:

	![4][4]

	In diesem Fall wurde ein neues Verzeichnis mit dem Namen des mobilen Dienstes erstellt. das als lokales Repository für den Datendienst dient. 

7. Öffnen Sie das Unterverzeichnis .\service\table und beachten Sie, dass dieses eine TodoItem.json-Datei enthält. Dabei handelt es sich um eine JSON-Darstellung der Operationsberechtigungen für die Tabelle TodoItem. 

	Falls Sie Serverskripts für diese Tabelle definiert haben, sehen Sie außerdem mindestens eine Datei, die den Namen <code>TodoItem._&lt;operation&gt;_.js</code> aufweist und die Skripts für den angegebenen Tabellenvorgang enthält. Zeitplanskripts und benutzerdefinierte API-Skripts werden in separaten Ordnern mit den jeweiligen Namen abgelegt. Weitere Informationen finden Sie unter [Quellcodeverwaltung].

Sie haben nun Ihr lokales Repository erstellt und können Änderungen an Serverskripts vornehmen und diese Änderungen anschließend in Ihren mobilen Dienst befördern.

##<a name="deploy-scripts"></a>Bereitstellen aktualisierter Skriptdateien an Ihren mobilen Dienst

1. Öffnen Sie das Unterverzeichnis .\service\table und erstellen Sie die Datei todoitem.insert.js, falls diese dort noch nicht existiert.

2. Öffnen Sie die Datei todoitem.insert.js in einem Text-Editor, fügen Sie den folgenden Code ein und speichern Sie Ihre Änderungen:

		function insert(item, user, request) {
		    request.execute();
		    console.log(JSON.stringify(item, null, 4));
		}
	
	Dieser Code schreibt das eingefügte Element in das Protokoll. Falls diese Datei bereits Code enthält, fügen Sie einfach etwas gültigen JavaScript-Code ein, z. B. einen Aufruf an "console.log()", und speichern Sie dann Ihre Änderungen. 

3. Geben Sie den folgenden Befehl in der Git-Eingabeaufforderung ein, um die neue Skriptdatei zur Quellcodeverwaltung hinzuzufügen:

		$ git add .
	

4. Geben Sie den folgenden Befehl ein, um die Änderungen nach Git zu übernehmen:

		$ git commit -m "updated the insert script"

5. Geben Sie den folgenden Befehl ein, um die Änderungen in das Remote-Repository zu übertragen:

		$ git push origin master
	
	Daraufhin wird eine Reihe von Ausgaben für die Bereitstellung des Commits im mobilen Dienst angezeigt.

6. Klicken Sie zurück im Verwaltungsportal auf die Registerkarte **Daten** und dann auf die Tabelle **TodoItem**.

	![][5]

3. Klicken Sie auf **Skript**, und wählen Sie dann den Vorgang **Einfügen** aus.

	![][6]

	Beachten Sie, dass das angezeigte Skript für die Einfügen-Operation denselben JavaScript-Code enthält, den Sie soeben in Ihr Repository hochgeladen haben.

##<a name="use-npm"></a>Verwenden von freigegebenem Code und Node.js-Modulen in Ihren Serverskripts

Mobile Services bietet Zugang zum kompletten Umfang der Node.js-Basismodule, und Sie können diese Module mit der **require**-Funktion in Ihrem Code verwenden. Außerdem kann Ihr mobiler Dienst Node.js-Module verwenden, die nicht Teil des Node.js-Standardpakets sind, und Sie können sogar eigenen freigegebenen Code als Node.js-Modul definieren. Weitere Informationen zum Erstellen von Modulen finden Sie unter [Module][Node.js API Documentation: Modules] in der Referenzdokumentation der Node.js-API.

Die empfohlene Vorgehensweise zum Hinzufügen von Node.js-Modulen zu Ihrem mobilen Dienst besteht darin, Referenzen zur package.json-Datei des Diensts hinzuzufügen. Als Nächstes fügen Sie das Node.js-Modul [node-uuid] zu Ihrem mobilen Dienst hinzu, indem Sie die package.json-Datei aktualisieren. Wenn die Aktualisierung mithilfe von Push an Azure übertragen wird, wird der mobile Dienst neu gestartet, und das Modul wird installiert. Mit diesem Modul können Sie nun neue GUID-Werte für die **uuid**-Eigenschaft in eingefügten Elementen generieren. 

2. Wechseln Sie zum Ordner ".\service" Ihres lokalen Git-Repositorys, und öffnen Sie die package.json-Datei in einem Text-Editor.

3. Suchen Sie nach  

		npm install node-uuid

	NPM erstellt das Verzeichnis "node_modules" am aktuellen Speicherort und installiert das Modul [node-uuid] im Unterverzeichnis "\node-uuid". 

	<div class="dev-callout">
	<strong>Hinweis</strong>
	<p>Wenn <code>node_modules</code> in der Verzeichnishierarchie bereits vorhanden ist, erstellt NPM das Unterverzeichnis <code>\node-uuid</code> dort statt ein neues <code>node_modules</code> im Repository zu erstellen. In diesen Fall können Sie das vorhandene Verzeichnis <code>node_modules</code> einfach löschen.</p>
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
	<br/>Zeigt, wie Sie mit Serverskripts, dem Auftragsplaner und benutzerdefinierten APIs arbeiten.

+ [Aufrufen einer benutzerdefinierten API vom Client] 
	<br/> Zeigt, wie Sie benutzerdefinierte APIs erstellen, die vom Client aufgerufen werden können.

<!-- Anchors. -->
[Aktivieren der Quellcodeverwaltung in Ihrem mobilen Dienst]: #enable-source-control
[Installieren von Git und Erstellen des lokalen Repositorys]: #clone-repo
[Bereitstellen aktualisierter Skriptdateien an Ihren mobilen Dienst]: #deploy-scripts
[Verwenden von freigegebenem Code und Node.js-Modulen in Ihren Serverskripts]: #use-npm

<!-- Images. -->
[4]: ./media/mobile-services-store-scripts-source-control/mobile-source-local-repo.png
[5]: ./media/mobile-services-store-scripts-source-control/mobile-portal-data-tables.png
[6]: ./media/mobile-services-store-scripts-source-control/mobile-insert-script-source-control.png

<!-- URLs. -->
[Git-Website]: http://git-scm.com
[Quellcodeverwaltung]: http://msdn.microsoft.com/de-de/library/windowsazure/c25aaede-c1f0-4004-8b78-113708761643
[Installieren von Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-ios-get-started/
[Hinzufügen von Mobile Services zu einer vorhandenen App]: /de-de/documentation/articles/mobile-services-ios-get-started-data/
[Arbeiten mit Serverskripts in Mobile Services]: /de-de/documentation/articles/mobile-services-how-to-use-server-scripts/
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Aufrufen einer benutzerdefinierten API vom Client]: /de-de/documentation/articles/mobile-services-ios-call-custom-api/
[Node.js-API-Dokumentation: Module]: http://nodejs.org/api/modules.html
[node-uuid]: https://npmjs.org/package/node-uuid

<!--HONumber=35_1-->
