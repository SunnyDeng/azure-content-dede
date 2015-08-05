<properties 
	pageTitle="Erstellen einer Orchard CMS-Web-App über den Azure Marketplace" 
	description="In diesem Lernprogramm erfahren Sie, wie Sie eine neue Web-App in Azure erstellen. Sie erfahren außerdem, wie Sie die Web-App über das Azure-Portal starten und verwalten" 
	tags="azure-portal"
	services="app-service\web" 
	documentationCenter=".net" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="tomfitz"/>

# Erstellen einer Orchard CMS-Web-App über den Azure Marketplace

## Übersicht

Im Marketplace ist eine breite Auswahl an beliebten Webanwendungen verfügbar, die von Microsoft, anderen Unternehmen oder Open Source-Softwareinitiativen entwickelt wurden. Für über den Marketplace erstellte Webanwendungen muss außer dem Browser, der für die Verbindung zum [Azure-Vorschauportal](http://go.microsoft.com/fwlink/?LinkId=529715) verwendet wird, keinerlei Software installiert werden. Weitere Informationen über die Webanwendungen im Marketplace finden Sie unter [Azure-Marketplace](/marketplace/).

In diesem Lernprogramm lernen Sie Folgendes:

- Erstellen einer neuen Web-App über den Marketplace

- Starten und Verwalten der Web-App über das Azure-Portal
 
Sie erstellen eine Orchard CMS-Web-App, die eine Standardvorlage verwendet. [Orchard](http://www.orchardproject.net/) ist eine kostenlose, .NET-basierte CMS-Anwendung, mit der Sie benutzerdefinierte, inhaltsgesteuerte Web-Apps und Websites erstellen können. Orchard CMS umfasst ein Framework für die Erweiterbarkeit, über das Sie [weitere Module und Themen herunterladen](http://gallery.orchardproject.net/) können, um Ihre Web-App anzupassen. Die folgende Abbildung zeigt die Orchard CMS-Web-App im [Azur App Service](http://go.microsoft.com/fwlink/?LinkId=529714), die Sie im Rahmen dieses Lernprogramms erstellen.

![Orchard-Blog][13]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Erstellen einer Orchard Web-App über den Marketplace

1. Melden Sie sich beim [Azure-Vorschauportal](http://portal.azure.com) an.

2. Klicken Sie auf **Neu** > **Web + Mobile** > **Azure Marketplace**.
	
	![Neu erstellen][1]

3. Klicken Sie auf **Web-Apps** > **Orchard CMS**. Klicken Sie im nächsten Fenster auf **Erstellen**.
	
	![Erstellen über den Marketplace][2]

4. Konfigurieren Sie die Felder URL, App Service-Plan, Ressourcengruppe und Standort der Web-App. Klicken Sie auf **Erstellen**, wenn Sie fertig sind.
	
	![Konfigurieren der App][3]

	Sobald Ihre Web-App erstellt ist, zeigt die Schaltfläche **Benachrichtigungen** "ERFOLG" in grüner Schrift an, und das Web-App-Fenster wird angezeigt.

## Starten und Verwalten Ihrer Orchard-Web-App

1. Klicken Sie im Fenster Ihrer Web-App auf **Durchsuchen**, um die Begrüßungsseite für Ihre Web-App zu öffnen.

	![Durchsuchen-Schaltfläche][12]

2. Geben Sie die für Orchard erforderlichen Konfigurationsdaten ein, und klicken Sie auf **Finish Setup**, um die Konfiguration zu beenden und die Startseite der Web-App zu öffnen.

	![Bei Orchard anmelden][7]

	Sie haben nun eine Orchard Web-App, die der unten dargestellten Web-App ähnelt.

	![Ihre Orchard-Web-App][13]

3. In der [Orchard Documentation](http://docs.orchardproject.net/) (Orchard-Dokumentation, in englischer Sprache) erfahren Sie mehr über Orchard und zur Konfiguration Ihrer neuen Web-App.

## Nächster Schritt

* [Entwickeln und Bereitstellen einer Web-App mit Microsoft WebMatrix](web-sites-dotnet-using-webmatrix.md) – Erfahren Sie, wie Sie eine App Service-Web App in WebMatrix bearbeiten. 
* [Erstellen einer ASP.NET MVC-App mit Authentifizierung und SQL-Datenbank und Bereitstellung in Azure App Service](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)– Erfahren Sie, wie Sie eine neue Web-App in Azure App Service in Visual Studio zu erstellen.

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).
* Hinweise zu den Veränderungen des Portals gegenüber dem Vorschauportal finden Sie unter [Referenz zur Navigation im Vorschauportal](http://go.microsoft.com/fwlink/?LinkId=529715)

[1]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-01.png
[2]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-02.png
[3]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-03.png
[4]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-04.png
[5]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-05.png
[7]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-07.png
[12]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-12.png
[13]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-08.png


 

<!---HONumber=July15_HO4-->