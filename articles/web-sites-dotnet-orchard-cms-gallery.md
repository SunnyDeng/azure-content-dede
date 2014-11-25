<properties linkid="develop-dotnet-website-from-gallery" urlDisplayName="Website from Gallery" pageTitle="Create an Orchard CMS website from the gallery in Azure" metaKeywords="Azure build website, manage website Azure" description="A tutorial that teaches you how to create a new website in Azure. Also learn how to launch and manage your site using the Management Portal." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Create an Orchard CMS website from the gallery in Azure" authors="cephalin" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin" />

# Erstellen einer Orchard CMS-Website über die Galerie in Azure

In der Galerie ist eine breite Auswahl an beliebten Webanwendungen verfügbar, die von Microsoft, anderen Unternehmen oder Open Source-Softwareinitiativen entwickelt wurden. Für über die Galerie erstelle Webanwendungen muss außer dem Browser, der für die Verbindung zum [Azure-Verwaltungsportal][Azure-Verwaltungsportal] genutzt wird, keinerlei Software installiert werden. Weitere Informationen über die Webanwendungen in der Galerie finden Sie unter [Windows Web App Gallery][Windows Web App Gallery].

In diesem Lernprogramm lernen Sie Folgendes:

-   Erstellen einer neuen Website über die Galerie

-   Starten und Verwalten der Website über das Verwaltungsportal

Sie erstellen eine Orchard CMS-Site, die eine Standardvorlage verwendet. [Orchard][Orchard] ist eine kostenlose, .NET-basierte Open-Source-CMS-Anwendung, mit der Sie benutzerdefinierte, inhaltsgesteuerte Websites erstellen können. Orchard CMS umfasst ein Framework für die Erweiterbarkeit, über das Sie [weitere Module und Themen herunterladen][weitere Module und Themen herunterladen] können, um Ihre Site anzupassen. Die folgende Abbildung zeigt die Orchard CMS-Site, die Sie im Rahmen dieses Lernprogramms erstellen.

![Orchard-Blog][Orchard-Blog]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Erstellen einer Orchard-Website über die Galerie

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an.

2.  Klicken Sie unten links im Portal auf das Symbol **Neu**.

    ![Neu erstellen][Neu erstellen]

3.  Klicken Sie auf das Symbol **Website** und dann auf **Aus Galerie**.

    ![Über Galerie erstellen][Über Galerie erstellen]

4.  Suchen Sie das Symbol **Orchard CMS** in der Liste, und klicken Sie darauf. Klicken Sie anschließend zum Fortfahren auf den Pfeil.

    ![Orchard aus der Liste][Orchard aus der Liste]

5.  Geben Sie auf der Seite **Ihre App konfigurieren** Werte für alle Felder ein, bzw. wählen Sie sie aus:

-   Geben Sie den gewünschten URL-Namen ein.
-   Wählen Sie die Ihnen am nächsten gelegene Region aus. (Hierdurch wird eine optimale Leistung sichergestellt.)

    ![App konfigurieren][App konfigurieren]

1.  Klicken Sie auf das Häkchen in der unteren rechten Ecke des Felds, um mit der Bereitstellung der neuen Orchard CMS-Website zu beginnen.

Azure startet den Erstellungs- und Bereitstellungsvorgang. Während die Website erstellt und bereitgestellt wird, wird der Status beider Vorgänge unten im Websites-Verwaltungsportal angezeigt. Nachdem alle Vorgänge durchgeführt wurden, wird eine Meldung angezeigt, dass die Website erstellt wurde.

## Starten und Verwalten Ihrer Orchard-Website

1.  Klicken Sie auf der Seite **Websites** auf den Namen Ihrer neuen Website und dann unten im Portal auf **Durchsuchen**, um die Willkommensseite Ihrer Website zu öffnen.

    ![Dashboard starten][Dashboard starten]

    ![Durchsuchen-Schaltfläche][Durchsuchen-Schaltfläche]

2.  Geben Sie die für Orchard erforderlichen Konfigurationsdaten ein, und klicken Sie auf Schaltfläche zum Fertigstellen der Einrichtung, um die Konfiguration zu beenden und die Startseite der Website zu öffnen.

    ![Bei Orchard anmelden][Bei Orchard anmelden]

    Sie haben nun eine neue WordPress-Website, die der unten dargestellten Website ähnelt.

    ![Ihre Orchard-Site][Orchard-Blog]

3.  In der [Orchard Documentation][Orchard Documentation] (Orchard-Dokumentation, in englischer Sprache) erfahren Sie mehr über Orchard und zur Konfiguration Ihrer neuen Website.

## <span class="short-header">Nächste Schritte</span>Nächster Schritt

-   [Entwickeln und Bereitstellen einer Website mit Microsoft WebMatrix][Entwickeln und Bereitstellen einer Website mit Microsoft WebMatrix] – Erfahren Sie, wie Sie eine Azure-Website in WebMatrix bearbeiten.
-   [Bereitstellen einer sicheren ASP.NET MVC-Anwendung mit Mitgliedschafts-, OAuth- und SQL-Datenbank für eine Azure-Website][Bereitstellen einer sicheren ASP.NET MVC-Anwendung mit Mitgliedschafts-, OAuth- und SQL-Datenbank für eine Azure-Website] – Erfahren Sie, wie Sie mit Visual Studio eine neue Website erstellen.

  [Azure-Verwaltungsportal]: http://manage.windowsazure.com
  [Windows Web App Gallery]: http://www.microsoft.com/web/gallery/categories.aspx
  [Orchard]: http://www.orchardproject.net/
  [weitere Module und Themen herunterladen]: http://gallery.orchardproject.net/
  [Orchard-Blog]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-08.png
  [Neu erstellen]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-01.png
  [Über Galerie erstellen]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-02.png
  [Orchard aus der Liste]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-03.png
  [App konfigurieren]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-04.png
  [Dashboard starten]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-05.png
  [Durchsuchen-Schaltfläche]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-12.png
  [Bei Orchard anmelden]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-07.png
  [Orchard Documentation]: http://docs.orchardproject.net/
  [Entwickeln und Bereitstellen einer Website mit Microsoft WebMatrix]: /de-de/develop/net/tutorials/website-with-webmatrix/
  [Bereitstellen einer sicheren ASP.NET MVC-Anwendung mit Mitgliedschafts-, OAuth- und SQL-Datenbank für eine Azure-Website]: /de-de/develop/net/tutorials/web-site-with-sql-database/
