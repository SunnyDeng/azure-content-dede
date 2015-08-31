<properties
	pageTitle="Speichern von Projektcode in der Quellcodeverwaltung | Microsoft Azure"
	description="Erfahren Sie, wie Sie Serverskriptdateien und -module in einem lokalen Git-Repository auf Ihrem Computer speichern."
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="08/18/2015" 
	ms.author="ggailey777"/>

# Speichern von Projektcode in der Quellcodeverwaltung

> [AZURE.SELECTOR-LIST (Platform | Backend)]
- [(Any | .NET)](mobile-services-dotnet-backend-store-code-source-control.md)
- [(Any | Javascript)](mobile-services-store-scripts-source-control.md)

In diesem Thema erfahren Sie, wie Sie die von Azure Mobile Services bereitgestellte Quellcodeverwaltung verwenden, um Ihre Serverskripts zu speichern. Sie können Skripts und andere JavaScript-Back-End-Codedateien aus Ihrem lokalen Git-Repository in Ihre Produktionsumgebung im mobilen Dienst befördern. Außerdem erfahren Sie, wie Sie freigegebenen Code definieren, der möglicherweise für mehrere Skripts erforderlich ist, und wie Sie die Datei „package.jason“ verwenden, um Ihrem mobilen Dienst Node.js-Module hinzuzufügen.

Dieses Lernprogramm setzt voraus, dass Sie bereits einen mobilen Dienst durch Bearbeiten eines der Lernprogramme [Erste Schritte mit Mobile Services] oder [Hinzufügen von Mobile Services zu einer vorhandenen App] erstellt haben.

##<a name="enable-source-control"></a>Aktivieren der Quellcodeverwaltung für Ihren mobilen Dienst

[AZURE.INCLUDE [mobile-services-enable-source-control](../../includes/mobile-services-enable-source-control.md)]

##<a name="clone-repo"></a>Installieren von Git und Erstellen des lokalen Repositorys

1. Installieren Sie Git auf Ihrem lokalen Computer.

	Die erforderlichen Schritte zum Installieren von Git variieren je nach Betriebssystem. Informationen zu Betriebssystem-spezifischen Distributionen und zur Installation finden Sie unter [Installieren von Git].

	> [AZURE.NOTE]Einige Betriebssysteme verfügen über eine Befehlszeilenversion und eine GUI-Version von Git. Die in diesem Artikel bereitgestellten Anweisungen verwenden die Befehlszeilenversion.

2. Öffnen Sie eine Befehlszeile, zum Beispiel **GitBash** (Windows) oder **Bash** (Unix Shell). Auf OS X-Systemen können Sie auf die Befehlszeile über die **Terminal**-Anwendung zugreifen.

3. Wechseln Sie in der Befehlszeile zu dem Verzeichnis, in dem Sie Ihre Skripts speichern werden. Beispiel: `cd SourceControl`.

4. Erstellen Sie mit dem folgenden Befehl eine lokale Kopie Ihres neuen Git-Repositorys. Ersetzen Sie `<your_git_URL>` durch die URL Ihres Git-Repositorys für den mobilen Dienst:

		git clone <your_git_URL>

5. Geben Sie Benutzername und Kennwort ein, die Sie bei der Aktivierung der Quellcodeverwaltung in Ihrem mobilen Dienst festgelegt haben. Nach der erfolgreichen Anmeldung sehen Sie eine Reihe von Antworten wie folgt:

		remote: Counting objects: 8, done.
		remote: Compressing objects: 100% (4/4), done.
		remote: Total 8 (delta 1), reused 0 (delta 0)
		Unpacking objects: 100% (8/8), done.

6. Wechseln Sie in das Verzeichnis, in dem Sie den `git clone`-Befehl ausgeführt haben, und beachten Sie die folgende Verzeichnisstruktur:

	![4][4]

	In diesem Fall wurde ein neues Verzeichnis mit dem Namen des mobilen Dienstes erstellt. das als lokales Repository für den Datendienst dient.

7. Öffnen Sie das Unterverzeichnis .\\service\\table und beachten Sie, dass dieses eine TodoItem.json-Datei enthält. Dabei handelt es sich um eine JSON-Darstellung der Operationsberechtigungen für die Tabelle TodoItem.

	Falls Sie Serverskripts für diese Tabelle definiert haben, sehen Sie außerdem eine oder mehrere Dateien mit dem Namen <code>TodoItem._&lt;operation&gt;_.js</code>. Diese Dateien enthalten die Skripts für die jeweilige Tabellenoperation. Zeitplanskripts und benutzerdefinierte API-Skripts werden in separaten Ordnern mit den jeweiligen Namen abgelegt. Weitere Informationen finden Sie unter [Quellcodeverwaltung].

Sie haben nun Ihr lokales Repository erstellt und können Änderungen an Serverskripts vornehmen und diese Änderungen anschließend in Ihren mobilen Dienst befördern.

##<a name="deploy-scripts"></a>Bereitstellen geänderter Skriptdateien in Ihrem mobilen Dienst

1. Öffnen Sie das Unterverzeichnis .\\service\\table und erstellen Sie die Datei todoitem.insert.js, falls diese dort noch nicht existiert.

2. Öffnen Sie die Datei todoitem.insert.js in einem Text-Editor, fügen Sie den folgenden Code ein und speichern Sie Ihre Änderungen:

		function insert(item, user, request) {
		    request.execute();
		    console.log(JSON.stringify(item, null, 4));
		}

	Dieser Code schreibt das eingefügte Element in das Protokoll. Falls diese Datei bereits Code enthält, fügen Sie einfach gültigen JavaScript-Code hinzu, z. B. einen Aufruf an `console.log()`, und speichern Sie Ihre Änderungen.

3. Geben Sie den folgenden Befehl in der Git-Eingabeaufforderung ein, um die neue Skriptdatei zur Quellcodeverwaltung hinzuzufügen:

		$ git add .


4. Geben Sie den folgenden Befehl ein, um die Änderungen nach Git zu übernehmen:

		$ git commit -m "updated the insert script"

5. Geben Sie den folgenden Befehl ein, um die Änderungen in das Remote-Repository zu übertragen:

		$ git push origin master

	Daraufhin wird eine Reihe von Ausgaben für die Bereitstellung des Commits im mobilen Dienst angezeigt.

6. Kehren Sie zurück zum Verwaltungsportal, klicken Sie auf die Registerkarte **Daten**, klicken Sie anschließend auf die Tabelle **TodoItem**, klicken Sie auf **Skript**, und wählen Sie dann den Vorgang **Einfügen** aus.
7. 
	Beachten Sie, dass das angezeigte Skript für die Einfügen-Operation denselben JavaScript-Code enthält, den Sie soeben in Ihr Repository hochgeladen haben.

##<a name="use-npm"></a>Verwenden von freigegebenem Code und Node.js-Modulen in Ihren Serverskripts.

Mobile Services bietet Zugang zum kompletten Umfang der Node.js-Basismodule, und Sie können diese Module in Ihrem Code mit der **require**-Funktion einbinden. Außerdem kann Ihr mobiler Dienst Node.js-Module verwenden, die nicht Teil des Node.js-Standardpakets sind, und Sie können sogar eigenen freigegebenen Code als Node.js-Modul definieren. Weitere Informationen zur Erstellung von Modulen finden Sie unter [Module] in der Referenzdokumentation der Node.js-API.

Die empfohlene Vorgehensweise zum Hinzufügen von Node.js-Modulen zu Ihrem mobilen Dienst besteht darin, Referenzen zur package.json-Datei des Diensts hinzuzufügen. Als Nächstes fügen Sie das Node.js-Modul [node-uuid] zu Ihrem mobilen Dienst hinzu, indem Sie die package.json-Datei aktualisieren. Wenn die Aktualisierung mithilfe von Push an Azure übertragen wird, wird der mobile Dienst neu gestartet, und das Modul wird installiert. Mit diesem Modul können Sie nun neue GUID-Werte für die **uuid**-Eigenschaft in eingefügten Elementen generieren.

2. Wechseln Sie zum Ordner `.\service` Ihres lokalen Git-Repositorys, und öffnen Sie die Datei "package.json" in einem Text-Editor. Fügen Sie dem Objekt **dependencies** das folgende Feld hinzu:

		"node-uuid": "~1.4.3"

	>[AZURE.NOTE]Durch diese Änderung in der Datei "package.json" wird nach dem Commit-Push ein Neustart in Ihrem mobilen Dienst ausgelöst.

4. Öffnen Sie nun das Unterverzeichnis .\\service\\table, öffnen Sie die Datei todoitem.insert.js und ändern Sie den Code wie folgt:

		function insert(item, user, request) {
		    var uuid = require('node-uuid');
		    item.uuid = uuid.v1();
		    request.execute();
		    console.log(item);
		}

	Dieser Code fügt eine uuid-Spalte zur Tabelle hinzu und füllt diese Spalte mit eindeutigen GUID-Bezeichnern.

5. Geben Sie wie bereits im vorigen Abschnitt die folgenden Befehle in der Git-Eingabeaufforderung ein:

		$ git add .
		$ git commit -m "added node-uuid module"
		$ git push origin master
		
	Daraufhin wird die neue Datei hinzugefügt, Ihre Änderungen übernommen und das neue Modul node-uuid sowie die Änderungen am Skript todoitem.insert.js an Ihren mobilen Dienst übertragen.

## <a name="next-steps"> </a>Nächste Schritte

Nach Abschluss dieses Lernprogramms sind Sie in der Lage, Ihre Skripts in einer Quellcodeverwaltung zu speichern. Die folgenden Artikel enthalten weiterführende Informationen zu Serverskripts und benutzerdefinierten APIs:

+ [Arbeiten mit Serverskripts in Mobile Services] <br/>
Beschreibt die Verwendung von Serverskripts, Auftragsplaner und benutzerdefinierten APIs.

+ [Aufrufen einer benutzerdefinierten API vom Client] <br/>
 Zeigt, wie benutzerdefinierte APIs erstellt werden, die vom Client aufgerufen werden können.

<!-- Anchors. -->
[Enable source control in your mobile service]: #enable-source-control
[Install Git and create the local repository]: #clone-repo
[Deploy updated script files to your mobile service]: #deploy-scripts
[Leverage shared code and Node.js modules in your server scripts]: #use-npm

<!-- Images. -->
[4]: ./media/mobile-services-store-scripts-source-control/mobile-source-local-repo.png
[5]: ./media/mobile-services-store-scripts-source-control/mobile-portal-data-tables.png
[6]: ./media/mobile-services-store-scripts-source-control/mobile-insert-script-source-control.png

<!-- URLs. -->
[Git website]: http://git-scm.com
[Quellcodeverwaltung]: http://msdn.microsoft.com/library/windowsazure/c25aaede-c1f0-4004-8b78-113708761643
[Installieren von Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Erste Schritte mit Mobile Services]: mobile-services-ios-get-started.md
[Hinzufügen von Mobile Services zu einer vorhandenen App]: mobile-services-ios-get-started-data.md
[Arbeiten mit Serverskripts in Mobile Services]: mobile-services-how-to-use-server-scripts.md
[Azure Management Portal]: https://manage.windowsazure.com/
[Aufrufen einer benutzerdefinierten API vom Client]: mobile-services-ios-call-custom-api.md
[Module]: http://nodejs.org/api/modules.html
[node-uuid]: https://npmjs.org/package/node-uuid

<!---HONumber=August15_HO8-->