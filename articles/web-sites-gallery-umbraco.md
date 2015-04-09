<properties 
	pageTitle="Erstellen einer Umbraco-Website aus der Galerie in Microsoft Azure" 
	description="Erstellen Sie ein Umbraco-Content-Management-System und stellen Sie es auf einer Azure-Website bereit." 
	services="web-sites" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/25/2015" 
	ms.author="tomfitz"/>

#Erstellen einer Umbraco-Website aus der Galerie in Microsoft Azure

Umbraco CMS ist ein voll ausgestattetes Open Source Content Management System, das zur Erstellung einer Vielzahl von kleineren bis hin zu komplexen Anwendungen verwendet werden kann. Die Azure Websites-Anwendungsgalerie bietet eine breite Auswahl an beliebten Webanwendungen, die von Microsoft, anderen Unternehmen oder Open Source-Softwareinitiativen entwickelt wurden. Die Galerie ermöglicht es Ihnen, in nur wenigen Minuten eine Umbraco CMS-Seite zu erstellen, indem Sie die Starterkits verwenden oder Ihr eigenes Design integrieren. 

Dieser Artikel präsentiert das neue Azure-Vorschauportal, das die Ressourcenverwaltung stark vereinfacht. Das neue Azure-Portal wurde konzipiert, um Ihren Softwareentwicklungsprozess durch die Platzierung von plattformübergreifenden Tools, Technologien und Diensten von Microsoft und seinen Partnern in eine einzige Arbeitsoberfläche zu beschleunigen. Anstelle der Verwendung von unabhängigen Ressourcen, wie Azure-Websites, Visual Studio-Projekten oder Datenbanken, können Sie Ihre gesamte Anwendung als eine einzige Ressourcengruppe erstellen, verwalten und analysieren. 

In diesem Lernprogramm lernen Sie Folgendes:

- Erstellen einer neuen Seite mithilfe der Galerie unter Verwendung des neuen Azure-Vorschauportals
- Aufbauen einer Blog-Website unter Verwendung von Umbraco CMS 

##Erstellen einer Website aus der Galerie im Azure-Portal

1. Melden Sie sich beim [Windows Azure-Verwaltungsportal](https://portal.azure.com/) an.

2. Wählen Sie das Symbol der **Azure-Galerie**.
	
	![Choose Web Gallery][01Startboard]
	
3. Wählen Sie in der **Galerie** die Registerkarte **Web** und anschließend **Umbraco CMS**.
	
	![Select Umbraco in the Web Gallery][02WebGallery]
	
4. Zum Erstellen einer neuen Umbraco CMS-Website, klicken Sie auf **Erstellen**.
	
	![Click Create][03UmbracoCMS]
	
5. Im nächsten Schritt konfigurieren Sie alle Ressourcen, die mit Umbraco CMS verbunden sind. In diesem Fall sind die Ressourcen eine Website und eine SQL Server-Datenbank. Wählen Sie zuerst **Website**, um die Einstellungen der Website, wie die **URL** der Seite, das **Webhosting**, die **Web-App-Einstellungen** und den **Ort** zu konfigurieren. 
	
	![Configure resources][04AppSettings]
	
6. Konfigurieren Sie jetzt die Datenbank. Wählen Sie **Datenbank** und wählen Sie anschließend **Eine neue Datenbank erstellen**. Dieses Beispiel erstellt einen SQL Server für die Datenbank in Azure.
	
	![Create a SQL Server on Azure][05NewServer]
	
7. Jetzt sind die Website und die Datenbank konfiguriert und Sie können mit der Bereitstellung der App beginnen, indem Sie am unteren Ende des ersten **Umbraco CMS**-Fensters, das Sie auf der vorherigen Seite gesehen haben, auf **Erstellen** klicken.
	
	![Click Create][06UmbracoCMSGroup]
	
Nachdem die Bereitstellung abgeschlossen ist, wird Ihnen im Startboard des Portals angezeigt, dass Ihre Ressourcengruppe für Umbraco CMS, in diesem Fall **UmbracoCMSgroup**, erstellt wurde. Klicken Sie im Abschnitt **Zusammenfassung** auf den Namen der Website (hier **umbracocmsgroup**), um die Eigenschaften Ihrer Website zu sehen. Im Abschnitt **Zusammenfassung** können Sie auch die Ressourcen der Datenbank auswählen, um die Eigenschaften der entsprechenden Datenbank zu sehen.
	
![][07UmbracoCMSGroupBlade]

## Starten und Konfigurieren Ihrer Umbraco CMS-Website

1. Klicken Sie im Details-Fenster Ihrer Website auf **Durchsuchen**, um Ihre Website zu durchsuchen (in diesem Fall umbracocmsgroup.azurewebsites.net).
	
	![Browse to your site][08UmbracoCMSGroupRunning]
	
2. Wenn Sie die Website durchsuchen, startet Umbraco CMS seinen Installationsassistenten. Geben Sie die gewünschten Informationen ein und klicken Sie auf **Anpassen**.
	
	![Install Umbraco wizard][09InstallUmbraco7]
	
3. Geben Sie Ihre Verbindungs- und Authentifizierungdaten für die Datenbank ein, die Umbraco verwendet. Wählen Sie **Microsoft SQL Azure** als Datenbanktyp.  Sie erhalten die Verbindungszeichenfolge für Ihre Datenbank im Abschnitt **Seiteneinstellungen** Ihrer Website.
	
	![Configure your database][10ConfigureYourDatabase] 
	
4. Wenn Sie Einsteiger bei Umbraco CMS sind, können Sie ein Starterkit für die Website auswählen. Andernfalls klicken Sie auf **Nein danke, ich möchte keinen Starterkit für die Website installieren**.
	
	![Install a starter website][11InstallAStarterWebsite]
	
5. Der Umbraco-Installer schließt die Installation für Ihre Anwendung ab. Nachdem ihre Anwendungen konfiguriert wurde, werden Sie zum Umbraco CMS-Verwaltungsdashboard weitergeleitet.
	
	![Umbraco CMS dashboard][14FriendlyCMS]
	
6. Sie erstellen jetzt eine Beispieltextseite, die Sie veröffentlichen. Wählen Sie **Inhalt**, wählen Sie dann **Überlauf** und schließlich **Textseite**.
	
	![Create a text page][15CreateItemUnderOverflow]
	
7. Geben Sie einen Titel und einen Inhalt für Ihre Textseite ein, wie unten angezeigt. Wenn Sie fertig sind, klicken Sie auf **Speichern und veröffentlichen**.
	
	![Enter a title and some content][16EnterAName]
	
8. Warten Sie, während Ihre Seite veröffentlicht wird. Wenn die Veröffentlichung abgeschlossen ist, erhalten Sie eine kleine Benachrichtigung unten rechts in Ihrem Bildschirm. Sie können jetzt den neuen Inhalt Ihrer Website durchsuchen. 
	
	![Published web site page][17MyPage]
	

Fertig! Sie haben in nur wenigen Minuten erfolgreich eine Blog-Website mit Umbraco CMS erstellt. 

##Zusätzliche Ressourcen

[Umbraco-Dokumentation](http://our.umbraco.org/documentation)

[Umbraco-Videolernprogramme](https://umbraco.com/help-and-support/video-tutorials.aspx)

[Übersicht über das Microsoft Azure-Vorschauportal](http://azure.microsoft.com/overview/preview-portal/)

[Dokumentation zum Microsoft Azure-Vorschauportal](http://azure.microsoft.com/documentation/preview-portal/)

[Azure-Vorschauportal (Kanal 9)](http://channel9.msdn.com/Blogs/Windows-Azure/Azure-Preview-portal) 

[Dokumentation zu Microsoft Azure-Websites](http://azure.microsoft.com/documentation/services/web-sites/)


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

<!--HONumber=49-->