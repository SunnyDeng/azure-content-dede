<properties 
	pageTitle="Hinzufügen einer Java-Anwendung in Azure App Service-Web-Apps" 
	description="In diesem Lernprogramm erfahren Sie, wie Sie eine Seite oder Anwendung zu Ihrer Instanz von Azure-App Service Web-Apps hinzufügen, die bereits für die Verwendung von Java konfiguriert ist." 
	services="app-service\web" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="03/04/2016" 
	ms.author="robmcm"/>

# Hinzufügen einer Java-Anwendung in Azure App Service-Web-Apps

Nachdem Sie Ihre Java Web-App in [Azure App Service][] wie unter [Erstellen einer Java Web-App in Azure App Service](web-sites-java-get-started.md) beschrieben, initialisiert haben, können Sie die Anwendung hochladen, indem Sie Ihre WAR-Datei im Ordner **webapps** ablegen.

iiDer Navigationspfad zum Ordner **webapps** hängt davon ab, wie Sie Ihre Web-Apps-Instanz einrichten.

- Wenn Sie Ihre Web-App mithilfe von Azure Marketplace einrichten, hat der Pfad zum Ordner **webapps** das Format **d:\\home\\site\\wwwroot\\bin\\application\_server\\webapps**, wobei **application\_server** für den Namen des Anwendungsservers steht, der für Ihre Web-Apps-Instanz verwendet wird. 
- Wenn Sie Ihre Web-App über die Azure-Konfigurationsbenutzeroberfläche einrichten, hat der Pfad zum Ordner **webapps** das Format **d:\\home\\site\\wwwroot\\webapps**. 

Beachten Sie, dass Sie die Quellcodeverwaltung zum Hochladen Ihrer Anwendung oder Webseiten verwenden können, auch in Szenarien mit fortlaufender Integration. Anweisungen zur Verwendung der Quellcodeverwaltung mit Ihrer Web-App finden Sie unter [Fortlaufende Bereitstellung mit GIT in Azure App Service](web-sites-publish-source-control.md). FTP stellt ebenfalls eine Möglichkeit zum Hochladen Ihrer Anwendung oder Webseiten dar.

Hinweis für Tomcat-Web-Apps: Nachdem Sie Ihre WAR-Datei in den Ordner **webapps** hochgeladen haben, erkennt der Tomcat-Anwendungsserver die neu hinzugefügte Datei und lädt diese automatisch. Beachten Sie, dass Sie beim Kopieren von Dateien in das ROOT-Verzeichnis den Anwendungsserver neu starten müssen, damit diese Dateien verwendet werden. Dies gilt nicht für WAR-Dateien. Die Funktion für automatisches Laden für in Azure ausgeführte Tomcat-Java-Web-Apps basiert auf einer neuen, hinzugefügten WAR-Datei oder neuen Dateien oder Verzeichnissen, die dem Ordner **webapps** hinzugefügt wurden.

## Nächste Schritte

Weitere Informationen finden Sie im [Java Developer Center](/develop/java/).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- External Links -->
[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714

<!---HONumber=AcomDC_0309_2016-->