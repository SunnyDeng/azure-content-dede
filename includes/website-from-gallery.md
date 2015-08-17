Azure Marketplace bietet eine breite Auswahl an beliebten Web-Apps, die von Microsoft, anderen Unternehmen oder Open Source-Softwareinitiativen entwickelt wurden. Für über den Azure Marketplace erstellte Web-Apps muss außer dem Browser, der für die Verbindung zum [Azure-Vorschauportal](http://go.microsoft.com/fwlink/?LinkId=529715) verwendet wird, keinerlei Software installiert werden.

In diesem Lernprogramm lernen Sie Folgendes:

- Erstellen einer neuen Web-App mit dem Azure Marketplace

- Bereitstellen der Web-App über das Azure-Vorschauportal
 
Sie erstellen einen WordPress-Blog unter Verwendung einer Standardvorlage. In der folgenden Abbildung wird die fertige Anwendung dargestellt:


![Wordpress-Blog][13]

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Erstellen einer Web-App im Portal

1. Melden Sie sich beim Azure-Vorschauportal an.

2. Öffnen Sie den Azure Marketplace durch Klicken auf das Symbol **Marketplace**:

    ![Marketplace-Symbol][marketplace]

    Oder klicken Sie auf das Symbol **Neu** oben rechts im Dashboard, und wählen Sie unten in der Liste den Eintrag **Marketplace** aus.
	
    ![Neu erstellen][5]
	
3. Wählen Sie **Web und mobil** aus. Suchen Sie nach **WordPress**, und klicken Sie dann auf das Symbol **WordPress**.

	![WordPress-Symbol in der Liste][7]
	
5. Wählen Sie nach dem Lesen der Beschreibung der WordPress-App **Erstellen** aus.

6. Klicken Sie auf **Web-App**, und geben Sie die erforderlichen Werte für die Konfiguration Ihrer Web-App ein.
	
    ![App konfigurieren][8]

7. Klicken Sie auf **Datenbank**, und geben Sie die erforderlichen Werte für die Konfiguration Ihrer MySQL-Datenbank ein.

    ![Datenbank konfigurieren][database]

8. Geben Sie einen Namen für die neue Ressourcengruppe an.

    ![Ressourcengruppe festlegen][groupname]

8. Klicken Sie ggf. auf **ABONNEMENT**, und geben Sie das zu verwendende Abonnement an.

7. Klicken Sie nach der Definition der Web-App auf **Erstellen**, und warten Sie, bis die neue Web-App erstellt ist.

   Sobald die App erstellt ist, sehen Sie eine Ressourcengruppe mit der Web-App und der Datenbank.

   ![Gruppe anzeigen][resourcegroup]

## Starten und Verwalten Ihrer WordPress-Web-App
	
1. Klicken Sie auf die neue Web-App, um deren Details anzuzeigen.

    ![Dashboard starten][10]

2. Klicken Sie auf der Seite **Essentials** auf **Durchsuchen** oder auf den Link unter **URL**, um die Willkommensseite der Web-App zu öffnen.

    ![Website-URL][browse]

3. Wenn Sie WordPress nicht installiert haben, geben Sie die für WordPress erforderlichen Konfigurationsinformationen ein, und klicken Sie auf **WordPress installieren**, um die Konfiguration abzuschließen und die Anmeldeseite der Web-App zu öffnen.

4. Klicken Sie auf **Anmelden**, und geben Sie Ihre Anmeldeinformationen ein.

5. Sie besitzen nun eine neue WordPress-Web-App, die der nachfolgenden Web-App ähnlich ist.

	![Ihre WordPress-Website][13]






[5]: ./media/website-from-gallery/start-marketplace.png
[6]: ./media/website-from-gallery/wordpressgallery-02.png
[7]: ./media/website-from-gallery/search-web-app.png
[8]: ./media/website-from-gallery/set-web-app.png
[9]: ./media/website-from-gallery/wordpressgallery-05.png
[10]: ./media/website-from-gallery/select-web.png
[13]: ./media/website-from-gallery/wordpressgallery-09.png
[webapps]: ./media/website-from-gallery/selectwebapps.png
[database]: ./media/website-from-gallery/set-db.png
[resourcegroup]: ./media/website-from-gallery/show-rg.png
[browse]: ./media/website-from-gallery/browse-web.png
[marketplace]: ./media/website-from-gallery/marketplace-icon.png
[groupname]: ./media/website-from-gallery/set-rg.png

<!---HONumber=August15_HO6-->