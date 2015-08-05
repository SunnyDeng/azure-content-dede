<properties 
	pageTitle="Erstellen einer Umbraco Web-App im Marketplace in Microsoft Azure" 
	description="Erstellen Sie ein Umbraco-Content-Management-System und stellen Sie es auf Azure App Service-Web-Apps bereit." 
	tags="azure-portal"
	services="app-service\web" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/21/2015" 
	ms.author="tomfitz"/>

#Erstellen einer Umbraco Web-App im Marketplace in Microsoft Azure#

Umbraco CMS ist ein voll ausgestattetes Open Source Content Management System, das zur Erstellung einer Vielzahl von kleineren bis hin zu komplexen Anwendungen verwendet werden kann. Der Azure Marketplace bietet eine breite Auswahl an beliebten Webanwendungen, die von Microsoft, anderen Unternehmen oder Open Source-Softwareinitiativen entwickelt wurden. Die Galerie ermöglicht es Ihnen, in wenigen Minuten eine Umbraco CMS-App zu erstellen, indem Sie die Starterkits verwenden oder Ihr eigenes Design integrieren.

Dieser Artikel präsentiert das neue Azure-Vorschauportal, das die Ressourcenverwaltung stark vereinfacht. Das neue Azure-Vorschauportal wurde konzipiert, um Ihren Softwareentwicklungsprozess durch die Platzierung von plattformübergreifenden Tools, Technologien und Diensten von Microsoft und seinen Partnern in eine einzige Arbeitsoberfläche zu beschleunigen. Anstelle der Verwendung von eigenständigen Ressourcen wie [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)-Web-Apps, Visual Studio-Projekten oder Datenbanken, können Sie Ihre gesamte Anwendung als einzelne Ressourcengruppe erstellen, verwalten und analysieren.

In diesem Lernprogramm lernen Sie Folgendes:

- Erstellen einer neuen Web-App über den Marketplace unter Verwendung des Azure-Vorschauportals
- Aufbauen einer Blog-Website unter Verwendung von Umbraco CMS 

##Erstellen einer Web-App über den Marketplace im Azure-Vorschauportal

1. Melden Sie sich beim [Azure-Vorschauportal](https://portal.azure.com/) an.

2. Wählen Sie das Symbol **Marketplace** aus.
	
	![Wählen Sie Web-Galerie][01Startboard]
	
3. Wählen Sie im **Marketplace** die Registerkarte **Web-Apps** und anschließend **Umbraco CMS** aus.
	
	![Wählen Sie Umbraco in der Web-Galerie][02WebGallery]
	
4. Zum Erstellen einer neuen Umbraco CMS-Web-App, klicken Sie auf **Erstellen**.
	
	![Klicken Sie auf Erstellen][03UmbracoCMS]
	
5. Im nächsten Schritt konfigurieren Sie alle Ressourcen, die mit Umbraco CMS verbunden sind. In diesem Fall sind die Ressourcen eine Web-App und eine SQL Server-Datenbank. Wählen Sie zuerst **Web-App** aus, um die Einstellungen der Web-App, wie die **URL**, den **App Service-Plan**, die **Web-App-Einstellungen** und den **Ort** zu konfigurieren.
	
	![Konfigurieren der Ressourcen][04AppSettings]
	
6. Konfigurieren Sie jetzt die Datenbank. Wählen Sie **Datenbank**, und wählen Sie dann **Server**. Dieses Beispiel erstellt einen SQL Server für die Datenbank in Azure.
	
	![Erstellen eines SQL Servers in Azure][05NewServer]
	
7. Nachdem Web-App und Datenbank konfiguriert sind, können Sie mit der Bereitstellung der Anwendung beginnen. Klicken Sie dazu auf **Erstellen** unten im ersten **Umbraco CMS**-Fenster, das Sie auf der vorherigen Seite gesehen habe.
	
	![Klicken Sie auf Erstellen][06UmbracoCMSGroup]
	
Nachdem die Bereitstellung abgeschlossen ist, wird im Portal das Blatt für die Ressourcengruppe Ihrer Umbraco CMS-Web-App angezeigt. Klicken Sie im Abschnitt **Zusammenfassung** auf den Web-App-Namen, um die Eigenschaften Ihrer Web-App anzuzeigen. Im Abschnitt **Zusammenfassung** können Sie auch die Ressourcen der Datenbank auswählen, um die Eigenschaften der entsprechenden Datenbank zu sehen.
	
![][07UmbracoCMSGroupBlade]

## Starten und Konfigurieren Ihrer Umbraco CMS-Web-App ##

1. Klicken Sie im Details-Fenster Ihrer Web-App auf **Durchsuchen**, um Ihre Web-App zu durchsuchen.
	
	![Ihre Website durchsuchen][08UmbracoCMSGroupRunning]
	
2. Wenn Sie die Web-App durchsuchen, startet Umbraco CMS seinen Installationsassistenten. Geben Sie die gewünschten Informationen ein und klicken Sie auf **Anpassen**.
	
	![Installieren des Umbraco-Assistenten][09InstallUmbraco7]
	
3. Geben Sie Ihre Verbindungs- und Authentifizierungdaten für die Datenbank ein, die Umbraco verwendet. Wählen Sie **Microsoft SQL Azure** als Datenbanktyp. Sie erhalten die Verbindungszeichenfolge für Ihre Datenbank im Abschnitt **Websiteeinstellungen** Ihrer Web-App.
	
	![Konfigurieren der Datenbank][10ConfigureYourDatabase]
	
4. Wenn Sie Einsteiger bei Umbraco CMS sind, können Sie ein Starterkit für die Website auswählen. Andernfalls klicken Sie auf **Nein danke, ich möchte keinen Starterkit für die Website installieren**.
	
	![Installieren einer Starter-Website][11InstallAStarterWebsite]
	
5. Der Umbraco-Installer schließt die Installation für Ihre Anwendung ab. Nachdem ihre Anwendungen konfiguriert wurde, werden Sie zum Umbraco CMS-Verwaltungsdashboard weitergeleitet.
	
	![Umbraco CMS-Dashboard][14FriendlyCMS]
	
6. Sie erstellen jetzt eine Beispieltextseite, die Sie veröffentlichen. Wählen Sie **Inhalt**, wählen Sie dann **Überlauf** und schließlich **Textseite**.
	
	![Erstellen einer Textseite][15CreateItemUnderOverflow]
	
7. Geben Sie einen Titel und einen Inhalt für Ihre Textseite ein, wie unten angezeigt. Wenn Sie fertig sind, klicken Sie auf **Speichern und veröffentlichen**.
	
	![Eingabe eines Titels und Inhalts][16EnterAName]
	
8. Warten Sie, während Ihre Seite veröffentlicht wird. Wenn die Veröffentlichung abgeschlossen ist, erhalten Sie eine kleine Benachrichtigung unten rechts in Ihrem Bildschirm. Sie können jetzt den neuen Inhalt Ihrer Web-App durchsuchen.
	
	![Veröffentlichte Seite der Website][17MyPage]
	

Fertig! Sie haben in nur wenigen Minuten erfolgreich eine Blog-Web-App mit Umbraco CMS erstellt.

##Zusätzliche Ressourcen

[Umbraco-Dokumentation](http://our.umbraco.org/documentation)

[Umbraco-Videolernprogramme](https://umbraco.com/help-and-support/video-tutorials.aspx)

[Dokumentation zum Azure-Portal](../preview-portal.md)

[Azure-Portal (Kanal 9)](http://channel9.msdn.com/Blogs/Windows-Azure/Azure-Preview-portal)

[Dokumentation zu Azure App Service-Web-Apps](/documentation/services/websites/)

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).
* Hinweise zu den Veränderungen des Portals gegenüber dem Vorschauportal finden Sie unter [Referenz zur Navigation im Vorschauportal](http://go.microsoft.com/fwlink/?LinkId=529715)

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.


<!-- IMAGES -->
[01Startboard]: ./media/web-sites-gallery-umbraco/01Startboard.PNG
[02WebGallery]: ./media/web-sites-gallery-umbraco/02WebGallery.PNG
[03UmbracoCMS]: ./media/web-sites-gallery-umbraco/03UmbracoCMS.PNG
[04AppSettings]: ./media/web-sites-gallery-umbraco/04AppSettings.PNG
[05NewServer]: ./media/web-sites-gallery-umbraco/05NewServer.PNG
[06UmbracoCMSGroup]: ./media/web-sites-gallery-umbraco/06UmbracoCMSGroup.PNG
[07UmbracoCMSGroupBlade]: ./media/web-sites-gallery-umbraco/07UmbracoCMSGroupBlade.PNG
[08UmbracoCMSGroupRunning]: ./media/web-sites-gallery-umbraco/08UmbracoCMSGroupRunning.PNG
[09InstallUmbraco7]: ./media/web-sites-gallery-umbraco/09InstallUmbraco7.png
[10ConfigureYourDatabase]: ./media/web-sites-gallery-umbraco/10ConfigureYourDatabase.png
[11InstallAStarterWebsite]: ./media/web-sites-gallery-umbraco/11InstallAStarterWebsite.png
[12ConfigureYourDatabase]: ./media/web-sites-gallery-umbraco/12ConfigureYourDatabase.png
[14FriendlyCMS]: ./media/web-sites-gallery-umbraco/14FriendlyCMS.PNG
[15CreateItemUnderOverflow]: ./media/web-sites-gallery-umbraco/15CreateItemUnderOverflow.PNG
[16EnterAName]: ./media/web-sites-gallery-umbraco/16EnterAName.PNG
[17MyPage]: ./media/web-sites-gallery-umbraco/17MyPage.PNG
 

<!---HONumber=July15_HO4-->