<properties 
	pageTitle="Hinzufügen einer Anwendung zu Ihrer Java-Website" 
	description="In diesem Lernprogramm wird gezeigt, wie Sie eine Seite oder Anwendung zu Ihrer Java-Website in Microsoft Azure hinzufügen." 
	services="web-sites" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="09/25/2014" 
	ms.author="robmcm"/>

# Hinzufügen einer Anwendung zur Java-Website in Azure

Nachdem Sie Ihre Java-Website wie unter [Erste Schritte mit Microsoft Azure-Websites und Java](web-sites-java-get-started.md) beschriebben initialisiert haben, können Sie die Anwendung hochladen, indem Sie Ihre WAR-Datei im Ordner **webapps** ablegen.

Der Navigationspfad zum Ordner **webapps** hängt davon ab, wie Sie Ihre Website einrichten.

- Wenn Sie Ihre Website mithilfe der Azure-Anwendungsgalerie einrichten, weist der Pfad zum Ordner **webapps** das Format **d:\home\site\wwwroot\bin\application_server\webapps** auf, wobei **application_server** für den Namen des für Ihre Website verwendeten Anwendungsservers steht. 
- Wenn Sie Ihre Website über die Azure-Konfigurationsbenutzeroberfläche einrichten, weist der Pfad zum Ordner **webapps** das Format **d:\home\site\wwwroot\webapps**. 

Beachten Sie, dass Sie die Quellcodeverwaltung zum Hochladen Ihrer Anwendung oder Webseiten verwenden können, auch in Szenarien mit fortlaufender Integration. Anweisungen zur Verwendung der Quellcodeverwaltung mit Ihrer Website finden Sie unter [Veröffentlichen aus der Quellcodeverwaltung auf Azure-Websites](web-sites-publish-source-control.md). FTP stellt ebenfalls eine Möglichkeit zum Hochladen Ihrer Anwendung oder Webseiten dar.

Hinweis für Tomcat-Websites: Nachdem Sie Ihre WAR-Datei in den Ordner **webapps** hochgeladen haben, erkennt der Tomcat-Anwendungsserver die neu hinzugefügte Datei und lädt diese automatisch. Beachten Sie, dass Sie beim Kopieren von Dateien in das ROOT-Verzeichnis den Anwendungsserver neu starten müssen, damit diese Dateien verwendet werden. Dies gilt nicht für WAR-Dateien. Die Funktion für das automatische Laden für die auf Azure ausgeführten Tomcat-Java-Websites basiert auf einer neuen, hinzugefügten WAR-Datei oder neuen Dateien oder Verzeichnissen, die dem Ordner **webapps** hinzugefügt wurden. 





<!--HONumber=42-->
