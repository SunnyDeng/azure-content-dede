<properties 
	pageTitle="Speichern von Projektcode in der Quellcodeverwaltung – Azure Mobile Services" 
	description="Erfahren Sie, wie Sie Ihr .NET-Back-End-Projekt in einem lokalen Git-Repository auf Ihrem Computer speichern und von dort veröffentlichen." 
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
	ms.date="05/20/2015" 
	ms.author="glenga"/>

# Speichern von Projektcode in der Quellcodeverwaltung

> [AZURE.SELECTOR-LIST (Platform | Backend)]
- [(Any | .NET)](mobile-services-dotnet-backend-store-code-source-control.md)
- [(Any | Javascript)](mobile-services-store-scripts-source-control.md)

In diesem Thema erfahren Sie, wie Sie die von Azure Mobile Services bereitgestellte Quellcodeverwaltung verwenden, um ein .NET-Back-End-Dienstprojekt zu speichern. Ihr Projekt kann veröffentlicht werden, indem Sie es einfach aus Ihrem lokalen Git-Repository in Ihren mobilen Dienst in der Produktionsumgebung hochladen.

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

6. Wechseln Sie zu dem Verzeichnis, in dem Sie den `git clone`-Befehl ausgeführt haben, und beachten Sie, dass ein neues Verzeichnis mit dem Namen des mobilen Diensts erstellt wird. Bei einem mobilen .NET Back-End-Dienst ist das Git-Repository anfängliche leer.

Nachdem Sie nun Ihr lokales Repository erstellt haben, können Sie Ihr .NET-Back-End-Dienstprojekt aus diesem Repository heraus veröffentlichen.

##<a name="deploy-scripts"></a>Veröffentlichen Ihres Projekts mithilfe von Git

1. Erstellen Sie ein neues mobiles .NET-Back-End-Dienstprojekt in Visual Studio 2013, oder verschieben Sie ein vorhandenes Projekt in das neue lokale Repository.  

	Für einen schnellen Test laden Sie das und Mobile Services-Schnellstartprojekt herunter, und speichern Sie es in diesem Ordner.

2. Entfernen Sie alle NuGet-Paketordner, sodass nur noch die Datei "packages.config" vorhanden ist.

	Mobile Services stellt automatisch Ihre NuGet-Pakete auf Grundlage der Datei "packages.config" wieder her. Sie können auch eine Datei ".gitignore" definieren, um zu verhindern, dass die Paketverzeichnisse hinzugefügte werden.
 
3. Geben Sie den folgenden Befehl in der Git-Eingabeaufforderung ein, um die neue Skriptdatei zur Quellcodeverwaltung hinzuzufügen:

		$ git add .
	
4. Geben Sie den folgenden Befehl ein, um die Änderungen nach Git zu übernehmen:

		$ git commit -m "adding the .NET backend service project"

5. Geben Sie den folgenden Befehl ein, um die Änderungen in das Remoterepository zu übertragen, und geben Sie Ihre Anmeldeinformationen an:

		$ git push origin master
	
	Es sollte eine Reihe von Befehlen angezeigt werden, die anzeigt, dass das Projekt in den Mobile Services bereitgestellt wird, Pakete hinzugefügt werden und der Dienst neu gestartet wird.

6. Wechseln Sie zu der URL Ihres mobilen .NET-Back-End-Diensts, woraufhin Folgendes angezeigt werden sollte:

	![Mobile Services-Startseite](./media/mobile-services-dotnet-backend-store-code-source-control/mobile-service-startup.png)

Ihr mobiles Dienstprojekt wird jetzt in der Quellcodeverwaltung verwaltet, und Sie können Dienstaktualisierungen veröffentlichen, indem Sie einfach Aktualisierungen aus Ihrem lokalen Repository per Push bereitstellen. Informationen zum Vornehmen von Änderungen am Datenmodell in einem mobilen .NET-Back-End-Dienst, der eine SQL-Datenbank verwendet, finden Sie unter [Ändern des Datenmodells eines mobilen .NET-Back-End-Diensts].

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Git website]: http://git-scm.com
[Source control]: http://msdn.microsoft.com/library/windowsazure/c25aaede-c1f0-4004-8b78-113708761643
[Installieren von Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Erste Schritte mit Mobile Services]: mobile-services-dotnet-backend-ios-get-started.md
[Hinzufügen von Mobile Services zu einer vorhandenen App]: mobile-services-dotnet-backend-ios-get-started-data.md
[Azure Management Portal]: https://manage.windowsazure.com/
[Call a custom API from the client]: mobile-services-dotnet-backend-ios-call-custom-api.md
[Ändern des Datenmodells eines mobilen .NET-Back-End-Diensts]: mobile-services-dotnet-backend-how-to-use-code-first-migrations.md
 

<!---HONumber=July15_HO4-->