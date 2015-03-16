In der Galerie ist eine breite Auswahl an beliebten Webanwendungen verfügbar, die von Microsoft, anderen Unternehmen oder Open Source-Softwareinitiativen entwickelt wurden. Für über die Galerie erstellte Webanwendungen muss außer dem Browser, der für die Verbindung zum Azure-Verwaltungsportal verwendet wird, keinerlei Software installiert werden. 

In diesem Lernprogramm lernen Sie Folgendes:

- Erstellen einer neuen Website über die Galerie

- Bereitstellen der Website über das Azure-Portal
 
Sie erstellen einen WordPress-Blog unter Verwendung einer Standardvorlage. In der folgenden Abbildung wird die fertige Anwendung dargestellt:


![Wordpress blog][13]

> [AZURE.IMPORTANT] Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. Sie können ein kostenloses Textkonto in wenigen Minuten erstellen. Ausführliche Informationen finden Sie unter [Erstellen von Azure-Konten](http://azure.microsoft.com/develop/php/tutorials/create-a-windows-azure-account/"%20target="_blank").

## Erstellen einer Website im Portal

1. Melden Sie sich beim [Azure-Verwaltungsportal](http://manage.windowsazure.com) an.

2. Klicken Sie unten links im Dashboard auf das Symbol **Neu**.
	
	![Create New][5]

3. Klicken Sie auf das Symbol **Website** und dann auf **Aus Galerie**.
	
	![Create From Gallery][6]

4. Suchen Sie das WordPress-Symbol in der Liste, klicken Sie darauf und dann auf **Weiter**.
	
	![WordPress from list][7]

5. Geben Sie auf der Seite **Ihre App konfigurieren** Werte für alle Felder ein, bzw. wählen Sie sie aus:
	
- Geben Sie den gewünschten URL-Namen ein.	
- Lassen Sie **Create a new MySQL database** im Feld **Database** aktiviert.
- Wählen Sie die Ihnen am nächsten gelegene Region aus.

	![configure your app][8]

6. Klicken Sie dann auf **Weiter**.

7. Geben Sie auf der Seite **Create New Database** einen Namen für die neue MySQL-Datenbank ein, oder verwenden Sie den Standardnamen. Wählen Sie die Ihnen am nächsten gelegene Region als Hostingstandort aus. Aktivieren Sie das Kontrollkästchen unten am Bildschirm, um den allgemeinen Geschäftsbestimmungen von ClearDB für Ihre gehostete MySQL-Datenbank zuzustimmen. Klicken Sie dann auf das Häkchen, um die Websiteerstellung abzuschließen. 
	
	![create database][9]

Nachdem Sie auf **Complete** geklickt haben, startet Azure den Erstellungs- und Bereitstellungsvorgang. Während die Website erstellt und bereitgestellt wird, wird der Status beider Vorgänge unten auf der Seite angezeigt. Nachdem alle Vorgänge abgeschlossen sind, wird eine letzte Statusmeldung über die erfolgreiche Bereitstellung der Website angezeigt.

## Starten und Verwalten Ihrer WordPress-Website

1. Klicken Sie auf der Seite **Websites** auf Ihre neue Website, um das zugehörige Dashboard zu öffnen.

	![launch dashboard][10]

2. Blättern Sie auf der Verwaltungsseite für das **Dashboard** nach unten, und klicken Sie links unterhalb von **Site Url** auf den Link, um die Begrüßungsseite der Website zu öffnen.

	![site URL][11] 

3. Geben Sie die von WordPress benötigten Konfigurationsinformationen ein, und klicken Sie auf **WordPress installieren**, um die Konfiguration abzuschließen und die Anmeldeseite der Website zu öffnen.

	![login to WordPress][12]

4. Melden Sie sich an Ihrer neuen WordPress-Website an, indem Sie Ihren Benutzernamen und Ihr Kennwort auf der Seite **Willkommen** eingeben.

5. Sie haben nun eine neue WordPress-Website, die der unten dargestellten Website ähnelt.  

	![your WordPress site][13]






[5]: ./media/website-from-gallery/wordpressgallery-01.png
[6]: ./media/website-from-gallery/wordpressgallery-02.png
[7]: ./media/website-from-gallery/wordpressgallery-03.png
[8]: ./media/website-from-gallery/wordpressgallery-04.png
[9]: ./media/website-from-gallery/wordpressgallery-05.png
[10]: ./media/website-from-gallery/wordpressgallery-06.png

[13]: ./media/website-from-gallery/wordpressgallery-09.png





<!--HONumber=42-->
