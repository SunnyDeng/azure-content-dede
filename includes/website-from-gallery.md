In der Galerie ist eine breite Auswahl an beliebten Webanwendungen verfügbar, die von Microsoft, anderen Unternehmen oder Open Source-Softwareinitiativen entwickelt wurden. Für über die Galerie erstellte Webanwendungen muss außer dem Browser, der für die Verbindung zum Azure-Verwaltungsportal verwendet wird, keinerlei Software installiert werden.

In diesem Lernprogramm lernen Sie Folgendes:

-   Erstellen einer neuen Website über die Galerie

-   Bereitstellen der Website über das Azure-Portal

Sie erstellen einen WordPress-Blog unter Verwendung einer Standardvorlage. In der folgenden Abbildung wird die fertige Anwendung dargestellt:

![Wordpress-Blog][Wordpress-Blog]

<div class="dev-callout"><strong>Hinweis</strong>
<p>Sie ben&ouml;tigen ein Azure-Konto, um dieses Lernprogramm auszuf&uuml;hren. Sie k&ouml;nnen ein kostenloses Textkonto in wenigen Minuten erstellen. Ausf&uuml;hrliche Informationen finden Sie unter <a href="http://www.windowsazure.com/de-de/develop/php/tutorials/create-a-windows-azure-account/" target="_blank">Create a Windows Azure account (in englischer Sprache)</a>.</p>
</div>

## Erstellen einer Website im Portal

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an.

2.  Klicken Sie unten links im Dashboard auf das Symbol **Neu**.

    ![Neu erstellen][Neu erstellen]

3.  Klicken Sie auf das Symbol **Website** und dann auf **Aus Galerie**.

    ![Über Galerie erstellen][Über Galerie erstellen]

4.  Suchen Sie das WordPress-Symbol in der Liste, klicken Sie darauf und dann auf **Next**.

    ![WordPress-Symbol in der Liste][WordPress-Symbol in der Liste]

5.  Geben Sie auf der Seite **Configure Your App** Werte für alle Felder ein bzw. wählen Sie sie aus:

-   Geben Sie den gewünschten URL-Namen ein.
-   Lassen Sie **Create a new MySQL database** im Feld **Database** aktiviert.
-   Wählen Sie die Ihnen am nächsten gelegene Region aus.

    ![App konfigurieren][App konfigurieren]

1.  Klicken Sie auf **Next**.

2.  Geben Sie auf der Seite **Create New Database** einen Namen für die neue MySQL-Datenbank ein, oder verwenden Sie den Standardnamen. Wählen Sie die Ihnen am nächsten gelegene Region als Hostingstandort aus. Aktivieren Sie das Kontrollkästchen unten am Bildschirm, um den allgemeinen Geschäftsbestimmungen von ClearDB für Ihre gehostete MySQL-Datenbank zuzustimmen. Klicken Sie dann auf das Häkchen, um die Websiteerstellung abzuschließen.

    ![Datenbank erstellen][Datenbank erstellen]

Nachdem Sie auf **Complete** geklickt haben, startet Azure den Erstellungs- und Bereitstellungsvorgang. Während die Website erstellt und bereitgestellt wird, wird der Status beider Vorgänge unten auf der Seite angezeigt. Nachdem alle Vorgange abgeschlossen sind, wird eine letzte Statusmeldung über die erfolgreiche Bereitstellung der Website angezeigt.

## Starten und Verwalten Ihrer WordPress-Website

1.  Klicken Sie auf der Seite **Websites** auf Ihre neue Website, um das zugehörige Dashboard zu öffnen.

    ![Dashboard starten][Dashboard starten]

2.  Blättern Sie auf der Verwaltungsseite für das **Dashboard** nach unten, und klicken Sie links unterhalb von **Site Url** auf den Link, um die Begrüßungsseite der Website zu öffnen.

    ![Website-URL][Website-URL]

3.  Geben Sie die von WordPress benötigten Konfigurationsinformationen ein, und klicken Sie auf **WordPress installieren**, um die Konfiguration abzuschließen und die Anmeldeseite der Website zu öffnen.

    ![Bei WordPress anmelden][Bei WordPress anmelden]

4.  Melden Sie sich an Ihrer neuen WordPress-Website an, indem Sie Ihren Benutzernamen und Ihr Kennwort auf der Seite **Willkommen** eingeben.

5.  Sie haben nun eine neue WordPress-Website, die der unten dargestellten Website ähnelt.

    ![Ihre WordPress-Website][Wordpress-Blog]

  [Wordpress-Blog]: ./media/website-from-gallery/wordpressgallery-09.png
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com
  [Neu erstellen]: ./media/website-from-gallery/wordpressgallery-01.png
  [Über Galerie erstellen]: ./media/website-from-gallery/wordpressgallery-02.png
  [WordPress-Symbol in der Liste]: ./media/website-from-gallery/wordpressgallery-03.png
  [App konfigurieren]: ./media/website-from-gallery/wordpressgallery-04.png
  [Datenbank erstellen]: ./media/website-from-gallery/wordpressgallery-05.png
  [Dashboard starten]: ./media/website-from-gallery/wordpressgallery-06.png
  [Website-URL]: ./media/website-from-gallery/wordpressgallery-07.png
  [Bei WordPress anmelden]: ./media/website-from-gallery/wordpressgallery-08.png
