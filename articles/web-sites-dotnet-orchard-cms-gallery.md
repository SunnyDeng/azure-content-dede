<properties linkid="develop-dotnet-website-from-gallery" urlDisplayName="Web Site from Gallery" pageTitle="Create an Orchard CMS web site from the gallery in Azure" metaKeywords="Azure build website, manage website Azure" description="A tutorial that teaches you how to create a new web site in Azure. Also learn how to launch and manage your site using the Management Portal." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Create an Orchard CMS web site from the gallery in Azure" authors="" solutions="" manager="" editor="" />

Erstellen einer Orchard CMS-Website über die Galerie in Azure
=============================================================

In der Galerie ist eine breite Auswahl an beliebten Webanwendungen verfügbar, die von Microsoft, anderen Unternehmen oder Open Source-Softwareinitiativen entwickelt wurden. Für über die Galerie erstelle Webanwendungen muss außer dem Browser, der für die Verbindung zum [Azure-Verwaltungsportal](http://manage.windowsazure.com) genutzt wird, keinerlei Software installiert werden. Weitere Informationen über die Webanwendungen in der Galerie finden Sie unter [Windows Web App Gallery](http://www.microsoft.com/web/gallery/categories.aspx).

In diesem Lernprogramm lernen Sie Folgendes:

-   Erstellen einer neuen Website über die Galerie

-   Starten und Verwalten der Website über das Verwaltungsportal

Sie erstellen eine Orchard CMS-Site, die eine Standardvorlage verwendet. [Orchard](http://www.orchardproject.net/) ist eine kostenlose, .NET-basierte Open-Source-CMS-Anwendung, mit der Sie benutzerdefinierte, inhaltsgesteuerte Websites erstellen können. Orchard CMS umfasst ein Framework für die Erweiterbarkeit, über das Sie [weitere Module und Themen herunterladen](http://gallery.orchardproject.net/) können, um Ihre Site anzupassen. Die folgende Abbildung zeigt die Orchard CMS-Site, die Sie im Rahmen dieses Lernprogramms erstellen.

![Orchard-Blog](./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-08.png)

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

Erstellen einer Orchard-Website über die Galerie
------------------------------------------------

1.  Melden Sie sich beim [Azure-Verwaltungsportal](http://manage.windowsazure.com) an.

2.  Klicken Sie unten links im Portal auf das Symbol **Neu**.

    ![Neu erstellen](./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-01.png)

3.  Klicken Sie auf das Symbol **Web Site** und dann auf **From Gallery**.

    ![Über Galerie erstellen](./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-02.png)

4.  Suchen Sie das Symbol **Orchard CMS** in der Liste, und klicken Sie darauf. Klicken Sie anschließend zum Fortfahren auf den Pfeil.

    ![Orchard aus der Liste](./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-03.png)

5.  Geben Sie auf der Seite **Configure Your App** Werte für alle Felder ein bzw. wählen Sie sie aus:

-   Geben Sie den gewünschten URL-Namen ein.
-   Wählen Sie die Ihnen am nächsten gelegene Region aus. (Hierdurch wird eine optimale Leistung sichergestellt.)

    ![App konfigurieren](./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-04.png)

1.  Klicken Sie auf das Häkchen in der unteren rechten Ecke des Felds, um mit der Bereitstellung der neuen Orchard CMS-Website zu beginnen.

Azure startet den Erstellungs- und Bereitstellungsvorgang. Während die Website erstellt und bereitgestellt wird, wird der Status beider Vorgänge unten im Website-Verwaltungsportal angezeigt. Nachdem alle Vorgänge durchgeführt wurden, wird eine Meldung angezeigt, dass die Website erstellt wurde.

Starten und Verwalten Ihrer Orchard-Website
-------------------------------------------

1.  Klicken Sie auf der Seite **Web Sites** auf den Namen Ihrer neuen Site, und klicken Sie dann unten im Portal auf **Browse**, um die Begrüßungsseite Ihrer Website zu öffnen.

    ![Dashboard starten](./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-05.png)

    ![Durchsuchen-Schaltfläche](./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-12.png)

2.  Geben Sie die für Orchard erforderlichen Konfigurationsdaten ein, und klicken Sie auf **Finish Setup**, um die Konfiguration zu beenden und die Startseite der Website zu öffnen.

    ![Bei Orchard anmelden](./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-07.png)

    Sie haben nun eine neue WordPress-Website, die der unten dargestellten Website ähnelt.

    ![Ihre Orchard-Site](./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-08.png)

3.  In der [Orchard Documentation](http://docs.orchardproject.net/) (Orchard-Dokumentation, in englischer Sprache) erfahren Sie mehr über Orchard und zur Konfiguration Ihrer neuen Website.

Nächste SchritteNächster Schritt
--------------------------------

-   [Entwickeln und Bereitstellen einer Website mit Microsoft WebMatrix](/de-de/develop/net/tutorials/website-with-webmatrix/) – Erfahren Sie, wie Sie eine Azure-Website in WebMatrix bearbeiten.
-   [Bereitstellen einer sicheren ASP.NET MVC-Anwendung mit Mitgliedschafts-, OAuth- und SQL-Datenbank für eine Azure-Website](/de-de/develop/net/tutorials/web-site-with-sql-database/) – Erfahren Sie, wie Sie mit Visual Studio eine neue Website erstellen.

