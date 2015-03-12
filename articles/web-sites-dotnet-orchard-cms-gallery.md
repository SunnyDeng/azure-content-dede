<properties 
	pageTitle="Erstellen einer Orchard CMS-Website über die Galerie in Azure" 
	description="In diesem Lernprogramm erfahren Sie, wie Sie eine neue Website in Azure erstellen. Sie erfahren außerdem, wie Sie die Website über das Verwaltungsportal starten und verwalten." 
	services="web-sites" 
	documentationCenter=".net" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="10/21/2014" 
	ms.author="tomfitz"/>

# Erstellen einer Orchard CMS-Website über die Galerie in Azure

In der Galerie ist eine breite Auswahl an beliebten Webanwendungen verfügbar, die von Microsoft, anderen Unternehmen oder Open Source-Softwareinitiativen entwickelt wurden. Für über die Galerie erstellte Webanwendungen muss außer dem Browser, der für die Verbindung zum [Azure-Verwaltungsportal](http://manage.windowsazure.com) verwendet wird, keinerlei Software installiert werden. Weitere Informationen über die Webanwendungen in der Galerie finden Sie unter [Windows Web App Gallery](http://www.microsoft.com/web/gallery/categories.aspx).

In diesem Lernprogramm lernen Sie Folgendes:

- Erstellen einer neuen Website über die Galerie

- Starten und Verwalten der Website über das Verwaltungsportal
 
Sie erstellen eine Orchard CMS-Site, die eine Standardvorlage verwendet. [Orchard](http://www.orchardproject.net/) ist eine kostenlose, .NET-basierte Open-Source-CMS-Anwendung, mit der Sie benutzerdefinierte, inhaltsgesteuerte Websites erstellen können. Orchard CMS umfasst ein Framework für die Erweiterbarkeit, über das Sie [weitere Module und Themen herunterladen](http://gallery.orchardproject.net/) können, um Ihre Website anzupassen. Die folgende Abbildung zeigt die Orchard CMS-Site, die Sie im Rahmen dieses Lernprogramms erstellen.

![Orchard blog][13]

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

<h2>Erstellen einer Orchard-Website über die Galerie</h2>

1. Melden Sie sich am [Azure-Verwaltungsportal](http://manage.windowsazure.com) an.

2. Klicken Sie unten links im Portal auf das Symbol **New** (in englischer Sprache).
	
	![Create New][1]

3. Klicken Sie auf das Symbol **Website** und dann auf **From Gallery** (in englischer Sprache).
	
	![Create From Gallery][2]

4. Klicken Sie in der Liste auf das Symbol **Orchard CMS**. Klicken Sie anschließend zum Fortfahren auf den Pfeil.
	
	![Orchard from list][3]

5. Geben Sie auf der Seite **Configure Your App** (in englischer Sprache) Werte für alle Felder ein, bzw. wählen Sie sie aus:
	
- Geben Sie den gewünschten URL-Namen ein.	
- Wählen Sie die Ihnen am nächsten gelegene Region aus. (Hierdurch wird eine optimale Leistung sichergestellt.)

	![configure your app][4]

6. Klicken Sie auf das Häkchen in der unteren rechten Ecke des Felds, um mit der Bereitstellung der neuen Orchard CMS-Website zu beginnen.

Azure startet den Erstellungs- und Bereitstellungsvorgang. Während die Website erstellt und bereitgestellt wird, wird der Status beider Vorgänge unten im Websites-Verwaltungsportal angezeigt. Nachdem alle Vorgänge durchgeführt wurden, wird eine Meldung angezeigt, dass die Website erstellt wurde.

<h2>Starten und Verwalten Ihrer Orchard-Website</h2>

1. Klicken Sie auf der Seite **Websites** auf den Namen Ihrer neuen Website und dann unten im Portal auf **Browse** (in englischer Sprache), um die Willkommensseite Ihrer Website zu öffnen.

	![launch dashboard][5]

	![browse button][12]

2. Geben Sie die für Orchard erforderlichen Konfigurationsdaten ein, und klicken Sie zum Fertigstellen der Konfiguration und Öffnen der Startseite der Website auf **Finish Setup** (in englischer Sprache).

	![login to Orchard][7]

	Sie haben nun eine neue Orchard-Website, die der unten dargestellten Website ähnelt.  

	![your Orchard site][13]

3. In der [Orchard-Dokumentation](http://docs.orchardproject.net/) erfahren Sie mehr über Orchard und zur Konfiguration Ihrer neuen Website.

<h2>Nächster Schritt</h2>
* [Entwickeln und Bereitstellen einer Website mit Microsoft WebMatrix](/de-de/develop/net/tutorials/website-with-webmatrix/) - Erfahren Sie, wie Sie eine Azure-Website in WebMatrix bearbeiten.
* [Bereitstellen einer sicheren ASP.NET MVC-Anwendung mit Mitgliedschafts-, OAuth- und SQL-Datenbank für eine Azure-Website](/de-de/develop/net/tutorials/web-site-with-sql-database/) - Erfahren Sie, wie Sie mit Visual Studio eine neue Website erstellen.

[1]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-01.png
[2]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-02.png
[3]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-03.png
[4]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-04.png
[5]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-05.png
[7]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-07.png
[12]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-12.png
[13]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-08.png






<!--HONumber=42-->
