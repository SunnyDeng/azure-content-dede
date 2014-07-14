In der Galerie ist eine breite Auswahl an beliebten Webanwendungen verfügbar, die von Microsoft, anderen Unternehmen oder Open Source-Softwareinitiativen entwickelt wurden. Für über die Galerie erstellte Webanwendungen muss außer dem Browser, der für die Verbindung zum Azure-Verwaltungsportal verwendet wird, keinerlei Software installiert werden.

In diesem Lernprogramm lernen Sie Folgendes:

* Erstellen einer neuen Website über die Galerie

* Bereitstellen der Website über das Azure-Portal

Sie erstellen einen WordPress-Blog unter Verwendung einer Standardvorlage. In der folgenden Abbildung wird die fertige Anwendung dargestellt:

![Wordpress-Blog](./media/website-from-gallery/wordpressgallery-09.png)
<div class="dev-callout"><strong>Hinweis</strong>
<p>Um dieses Lernprogramm abzuschließen, benötigen Sie ein Azure-Konto. Sie können ein kostenloses Textkonto in wenigen Minuten erstellen. Ausführliche Informationen finden Sie unter <a href="http://www.windowsazure.com/en-us/develop/php/tutorials/create-a-windows-azure-account/" target="_blank">Create a Windows Azure account (in englischer Sprache)</a>.</p>
</div>
<br />

## Erstellen einer Website im Portal

1.  Melden Sie sich beim [Azure-Verwaltungsportal][1] an.

2.  Klicken Sie unten links im Dashboard auf das Symbol **Neu**.
    
    ![Neu
    erstellen](./media/website-from-gallery/wordpressgallery-01.png)

3.  Klicken Sie auf das Symbol **Web Site** und dann auf **From Gallery**.
    
    ![Über Galerie
    erstellen](./media/website-from-gallery/wordpressgallery-02.png)

4.  Suchen Sie das WordPress-Symbol in der Liste, klicken Sie darauf und dann auf **Next**.
    
    ![WordPress-Symbol in der
    Liste](./media/website-from-gallery/wordpressgallery-03.png)

5.  Geben Sie auf der Seite **Configure Your App** Werte für alle Felder ein bzw. wählen Sie sie aus:

* Geben Sie den gewünschten URL-Namen ein.
* Lassen Sie **Create a new MySQL database** im Feld **Database**
  aktiviert.
* Wählen Sie die Ihnen am nächsten gelegene Region aus.
  
  ![App
  konfigurieren](./media/website-from-gallery/wordpressgallery-04.png)

1.  Klicken Sie auf **Next**.

2.  Geben Sie auf der Seite **Create New Database** einen Namen für die neue MySQL-Datenbank ein, oder verwenden Sie den Standardnamen. Wählen Sie die Ihnen am nächsten gelegene Region als Hostingstandort aus. Aktivieren Sie das Kontrollkästchen unten am Bildschirm, um den allgemeinen Geschäftsbestimmungen von ClearDB für Ihre gehostete MySQL-Datenbank zuzustimmen. Klicken Sie dann auf das Häkchen, um die Websiteerstellung abzuschließen.
    
    ![Datenbank erstellen](./media/website-from-gallery/wordpressgallery-05.png)

Nachdem Sie auf **Complete** geklickt haben, startet Azure den Erstellungs- und Bereitstellungsvorgang. Während die Website erstellt und bereitgestellt wird, wird der Status beider Vorgänge unten auf der Seite angezeigt. Nachdem alle Vorgange abgeschlossen sind, wird eine letzte Statusmeldung über die erfolgreiche Bereitstellung der Website angezeigt.

## Starten und Verwalten Ihrer WordPress-Website

1.  Klicken Sie auf der Seite **Web Sites** auf Ihre neue Website, um das zugehörige Dashboard zu öffnen.
    
    ![Dashboard
    starten](./media/website-from-gallery/wordpressgallery-06.png)

2.  Blättern Sie auf der Verwaltungsseite für das **Dashboard** nach unten, und klicken Sie links unterhalb von **Site Url** auf den Link, um die Begrüßungsseite der Website zu öffnen.
    
    ![Website-URL](./media/website-from-gallery/wordpressgallery-07.png)

3.  Geben Sie die von WordPress benötigten Konfigurationsinformationen ein, und klicken Sie auf **Install WordPress**, um die Konfiguration abzuschließen und die Anmeldeseite der Website zu öffnen.
    
    ![Bei WordPress
    anmelden](./media/website-from-gallery/wordpressgallery-08.png)

4.  Melden Sie sich an Ihrer neuen WordPress-Website an, indem Sie Ihren Benutzernamen und Ihr Kennwort auf der Seite **Welcome** eingeben.

5.  Sie haben nun eine neue WordPress-Website, die der unten dargestellten Website ähnelt.
    
    ![Ihre WordPress-Website](./media/website-from-gallery/wordpressgallery-09.png)



[1]: http://manage.windowsazure.com