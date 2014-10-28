<properties title="Create an Umbraco website from the gallery in Microsoft Azure" pageTitle="Create an Umbraco website from the gallery in Microsoft Azure" description="required" metaKeywords="Azure, gallery, Umbraco, web site, website" services="web-sites" solutions="web" documentationCenter="" authors="timamm" manager="paulettm" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timamm"></tags>

# Erstellen einer Umbraco-Website aus der Galerie in Microsoft Azure

Umbraco CMS ist ein voll ausgestattetes Open Source Content Management System, das zur Erstellung einer Vielzahl von kleineren bis hin zu komplexen Anwendungen verwendet werden kann. Die Azure Websites-Anwendungsgalerie bietet eine breite Auswahl an beliebten Webanwendungen, die von Microsoft, anderen Unternehmen oder Open Source-Softwareinitiativen entwickelt wurden. Die Galerie ermöglicht es Ihnen, in nur wenigen Minuten eine Umbraco CMS-Seite zu erstellen, indem Sie die Starterkits verwenden oder Ihr eigenes Design integrieren.

Dieser Artikel präsentiert das neue Azure-Vorschauportal, das die Ressourcenverwaltung stark vereinfacht. Das neue Azure-Portal wurde konzipiert, um Ihren Softwareentwicklungsprozess durch die Platzierung von plattformübergreifenden Tools, Technologien und Diensten von Microsoft und seinen Partnern in eine einzige Arbeitsoberfläche zu beschleunigen. Anstelle der Verwendung von unabhängigen Ressourcen, wie Azure-Websites, Visual Studio-Projekten oder Datenbanken, können Sie Ihre gesamte Anwendung als eine einzige Ressourcengruppe erstellen, verwalten und analysieren.

In diesem Lernprogramm lernen Sie Folgendes:

-   Erstellen einer neuen Seite mithilfe der Galerie unter Verwendung des neuen Azure-Vorschauportals
-   Aufbauen einer Blog-Website unter Verwendung von Umbraco CMS

## Erstellen einer Website aus der Galerie im Azure-Portal

1.  Melden Sie sich am [Windows Azure-Verwaltungsportal][Windows Azure-Verwaltungsportal] an.

2.  Wählen Sie das Symbol der **Azure-Galerie**.

    ![Wählen Sie Web-Galerie][Wählen Sie Web-Galerie]

3.  Wählen Sie in der **Galerie** die Registerkarte **Web** und anschließend **Umbraco CMS**.

    ![Wählen Sie Umbraco in der Web-Galerie][Wählen Sie Umbraco in der Web-Galerie]

4.  Zum Erstellen einer neuen Umbraco CMS-Website, klicken Sie auf **Erstellen**.

    ![Klicken Sie auf Erstellen][Klicken Sie auf Erstellen]

5.  Im nächsten Schritt konfigurieren Sie alle Ressourcen, die mit Umbraco CMS verbunden sind. In diesem Fall sind die Ressourcen eine Website und eine SQL Server-Datenbank. Wählen Sie zuerst **Website**, um die Einstellungen der Website, wie die **URL** der Seite, das **Webhosting**, die **Web-Anwendungseinstellungen** und den **Ort** zu konfigurieren.

    ![Konfigurieren der Ressourcen][Konfigurieren der Ressourcen]

6.  Konfigurieren Sie jetzt die Datenbank. Wählen Sie **Datenbank** und wählen Sie anschließend **Eine neue Datenbank erstellen**. Dieses Beispiel erstellt einen SQL Server für die Datenbank in Azure.

    ![Erstellen eines SQL Servers in Azure][Erstellen eines SQL Servers in Azure]

7.  Jetzt sind die Website und die Datenbank konfiguriert und Sie können mit der Bereitstellung der Anwendung beginnen, indem Sie am unteren Ende des ersten **Umbraco CMS**-Fensters, das Sie auf der vorherigen Seite gesehen haben, auf **Erstellen** klicken.

    ![Klicken Sie auf Erstellen][1]

Nachdem die Bereitstellung abgeschlossen ist, wird Ihnen im Startboard des Portals angezeigt, dass Ihre Ressourcengruppe für Umbraco CMS, in diesem Fall **UmbracoCMSgroup**, erstellt wurde. Klicken Sie im Abschnitt **Zusammenfassung** auf den Namen der Website (hier **umbracocmsgroup**), um die Eigenschaften Ihrer Website zu sehen. Im Abschnitt **Zusammenfassung** können Sie auch die Ressourcen der Datenbank auswählen, um die Eigenschaften der entsprechenden Datenbank zu sehen.

![][]

## Starten und Konfigurieren Ihrer Umbraco CMS-Website

1.  Klicken Sie im Details-Fenster Ihrer Website auf **Durchsuchen**, um Ihre Seite zu durchsuchen (in diesem Fall umbracocmsgroup.azurewebsites.net).

    ![Ihre Website durchsuchen][Ihre Website durchsuchen]

2.  Wenn Sie die Website durchsuchen, startet Umbraco CMS seinen Installationsassistenten. Geben Sie die gewünschten Informationen ein und klicken Sie auf **Anpassen**.

    ![Installieren des Umbraco-Assistenten][Installieren des Umbraco-Assistenten]

3.  Geben Sie Ihre Verbindungs- und Authentifizierungdaten für die Datenbank ein, die Umbraco verwendet. Wählen Sie **Microsoft SQL Azure** als Datenbanktyp. Sie erhalten die Verbindungszeichenfolge für Ihre Datenbank im Abschnitt **Seiteneinstellungen** Ihrer Website.

    ![Konfigurieren der Datenbank][Konfigurieren der Datenbank]

4.  Wenn Sie Einsteiger bei Umbraco CMS sind, können Sie ein Starterkit für die Website auswählen. Andernfalls klicken Sie auf **Nein danke, ich möchte keinen Starterkit für die Website installieren**.

    ![Installieren einer Starter-Website][Installieren einer Starter-Website]

5.  Der Umbraco-Installer schließt die Installation für Ihre Anwendung ab. Nachdem ihre Anwendungen konfiguriert wurde, werden Sie zum Umbraco CMS-Verwaltungsdashboard weitergeleitet.

    ![Umbraco CMS-Dashboard][Umbraco CMS-Dashboard]

6.  Sie erstellen jetzt eine Beispieltextseite, die Sie veröffentlichen. Wählen Sie **Inhalt**, wählen Sie dann **Überlauf** und schließlich **Textseite**.

    ![Erstellen einer Textseite][Erstellen einer Textseite]

7.  Geben Sie einen Titel und einen Inhalt für Ihre Textseite ein, wie unten angezeigt. Wenn Sie fertig sind, klicken Sie auf **Speichern und veröffentlichen**.

    ![Eingabe eines Titels und Inhalts][Eingabe eines Titels und Inhalts]

8.  Warten Sie, während Ihre Seite veröffentlicht wird. Wenn die Veröffentlichung abgeschlossen ist, erhalten Sie eine kleine Benachrichtigung unten rechts in Ihrem Bildschirm. Sie können jetzt den neuen Inhalt Ihrer Website durchsuchen.

    ![Veröffentlichte Seite der Website][Veröffentlichte Seite der Website]

Fertig! Sie haben in nur wenigen Minuten erfolgreich eine Blog-Website mit Umbraco CMS erstellt.

## Zusätzliche Ressourcen

[Umbraco Dokumentation][Umbraco Dokumentation]

[Umbraco Video-Lernprogramme][Umbraco Video-Lernprogramme]

[Übersicht über das Microsoft Azure-Vorschauportal][Übersicht über das Microsoft Azure-Vorschauportal]

[Dokumentation zum Microsoft Azure-Vorschauportal][Dokumentation zum Microsoft Azure-Vorschauportal]

[Azure-Vorschauportal (Kanal 9)][Azure-Vorschauportal (Kanal 9)]

[Dokumentation zu Microsoft Azure-Websites][Dokumentation zu Microsoft Azure-Websites]

<!-- IMAGES -->

  [Windows Azure-Verwaltungsportal]: https://portal.azure.com/
  [Wählen Sie Web-Galerie]: ./media/web-sites-gallery-umbraco/01Startboard.PNG
  [Wählen Sie Umbraco in der Web-Galerie]: ./media/web-sites-gallery-umbraco/02WebGallery.PNG
  [Klicken Sie auf Erstellen]: ./media/web-sites-gallery-umbraco/03UmbracoCMS.PNG
  [Konfigurieren der Ressourcen]: ./media/web-sites-gallery-umbraco/04AppSettings.PNG
  [Erstellen eines SQL Servers in Azure]: ./media/web-sites-gallery-umbraco/05NewServer.PNG
  [1]: ./media/web-sites-gallery-umbraco/06UmbracoCMSGroup.PNG
  []: ./media/web-sites-gallery-umbraco/07UmbracoCMSGroupBlade.PNG
  [Ihre Website durchsuchen]: ./media/web-sites-gallery-umbraco/08UmbracoCMSGroupRunning.PNG
  [Installieren des Umbraco-Assistenten]: ./media/web-sites-gallery-umbraco/09InstallUmbraco7.png
  [Konfigurieren der Datenbank]: ./media/web-sites-gallery-umbraco/10ConfigureYourDatabase.png
  [Installieren einer Starter-Website]: ./media/web-sites-gallery-umbraco/11InstallAStarterWebsite.png
  [Umbraco CMS-Dashboard]: ./media/web-sites-gallery-umbraco/14FriendlyCMS.PNG
  [Erstellen einer Textseite]: ./media/web-sites-gallery-umbraco/15CreateItemUnderOverflow.PNG
  [Eingabe eines Titels und Inhalts]: ./media/web-sites-gallery-umbraco/16EnterAName.PNG
  [Veröffentlichte Seite der Website]: ./media/web-sites-gallery-umbraco/17MyPage.PNG
  [Umbraco Dokumentation]: http://our.umbraco.org/documentation
  [Umbraco Video-Lernprogramme]: https://umbraco.com/help-and-support/video-tutorials.aspx
  [Übersicht über das Microsoft Azure-Vorschauportal]: http://azure.microsoft.com/de-de/overview/preview-portal/
  [Dokumentation zum Microsoft Azure-Vorschauportal]: http://azure.microsoft.com/de-de/documentation/preview-portal/
  [Azure-Vorschauportal (Kanal 9)]: http://channel9.msdn.com/Blogs/Windows-Azure/Azure-Preview-portal
  [Dokumentation zu Microsoft Azure-Websites]: http://azure.microsoft.com/de-de/documentation/services/web-sites/
