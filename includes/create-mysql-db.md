# Erstellen einer MySQL-Datenbank in Azure

Diese Anleitung zeigt, wie Sie mit [ClearDB][ClearDB] eine MySQL-Datenbank über den [Azure Store][Azure Store] erstellen und wie Sie eine MySQL-Datenbank als verknüpfte Ressource erstellen, wenn Sie eine [Azure-Website][Azure-Website] erstellen. [ClearDB][ClearDB] ist ein fehlertoleranter Datenbank-Dienstanbieter, mit dem Sie MySQL-Datenbanken in Azure-Datencentern ausführen und verwalten und aus jeder Anwendung Verbindungen zu diesen herstellen können.

## Inhaltsverzeichnis

-   [Gewusst wie: Erstellen einer MySQL-Datenbank über den Azure Store][Gewusst wie: Erstellen einer MySQL-Datenbank über den Azure Store]
-   [Gewusst wie: Erstellen einer MySQL-Datenbank als verknüpfte Ressource für eine Azure-Website][Gewusst wie: Erstellen einer MySQL-Datenbank als verknüpfte Ressource für eine Azure-Website]

<div class="dev-callout"> 
<b>Hinweis</b> 
<p>Wenn Sie beim Erstellen einer Website eine MySQL-Datenbank erstellen, kann dies nur eine kostenlose sein. Wenn Sie eine MySQL-Datenbank &uuml;ber den Azure Store erstellen, k&ouml;nnen Sie eine kostenlose Datenbank erstellen oder unter bezahlten Optionen w&auml;hlen.</p> 
</div>

## <span id="CreateFromStore"></span></a>Gewusst wie: Erstellen einer MySQL-Datenbank über den Azure Store

Gehen Sie folgendermaßen vor, um eine MySQL-Datenbank über den [Azure Store][Azure Store] zu erstellen:

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an.
2.  Klicken Sie unten auf der Seite auf **+NEU**, und wählen Sie anschließend **STORE** aus.

    ![Add-On im Store auswählen][Add-On im Store auswählen]

3.  Wählen Sie **ClearDB MySQL-Datenbank** aus, und klicken Sie unten im Fenster auf den Pfeil.

    ![ClearDB MySQL-Datenbank auswählen][ClearDB MySQL-Datenbank auswählen]

4.  Wählen Sie einen Plan, geben Sie einen Datenbanknamen ein, wählen Sie eine Region aus, und klicken Sie unten im Fenster auf den Pfeil.

    ![MySQL-Datenbank im Store kaufen][MySQL-Datenbank im Store kaufen]

5.  Klicken Sie auf das Häkchen, um den Kauf abzuschließen.

    ![Kauf prüfen und abschließen][Kauf prüfen und abschließen]

6.  Nachdem die Datenbank erstellt wurde, können Sie sie auf der Registerkarte **ADD-ONS** im Verwaltungsportal verwalten.

    ![MySQL-Datenbank im Azure-Portal verwalten][MySQL-Datenbank im Azure-Portal verwalten]

7.  Sie können die Datenbankverbindungsinformationen abrufen, indem Sie unten auf der Seite auf **Verbindungsinformationen** klicken (siehe oben).

    ![MySQL-Verbindungsinformationen][MySQL-Verbindungsinformationen]

## <span id="CreateForWebSite"></span></a>Gewusst wie: Erstellen einer MySQL-Datenbank als verknüpfte Ressource für eine Azure-Website

Um beim Erstellen einer [Azure-Website][Azure-Website] eine MySQL-Datenbank als verknüpfte Ressource zu erstellen, gehen Sie folgendermaßen vor:

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an.
2.  Klicken Sie unten auf der Seite auf **+NEU**, und wählen Sie **SERVER**, **WEBSITE** und **MIT DATENBANK ERSTELLEN** aus.

    ![Website mit Datenbank erstellen][Website mit Datenbank erstellen]

3.  Geben Sie eine **URL** für Ihre Website ein, und wählen Sie die **REGION** für Ihre Website aus. Wählen Sie anschließend aus der Dropdownliste **DATENBANK** die Option **Neue MySQL-Datenbank erstellen** aus. Optional können Sie den Standardnamen für die Verbindungszeichenfolge ersetzen. Klicken Sie auf den Pfeil unten auf der Seite.

    ![Websitedetails angeben][Websitedetails angeben]

4.  Geben Sie einen **NAMEN** für die Datenbank ein, wählen Sie die **REGION** für die Datenbank aus (muss mit der Region für die Website übereinstimmen), stimmen Sie den ClearDB-Bedingungen zu, und klicken Sie auf das Häkchen unten im Fenster.

    ![MySQL-Details angeben][MySQL-Details angeben]

5.  Klicken Sie nach Erstellung der Website auf deren Namen, um zum Dashboard Ihrer Website zu gelangen.

    ![Zum Website-Dashboard navigieren][Zum Website-Dashboard navigieren]

6.  Klicken Sie auf **Konfigurieren**.

    ![Zur Registerkarte "Konfigurieren" navigieren][Zur Registerkarte "Konfigurieren" navigieren]

7.  Scrollen Sie zum Abschnitt **Verbindungszeichenfolgen**, und klicken Sie auf **Verbindungszeichenfolgen anzeigen**.

    ![Verbindungszeichenfolge anzeigen][Verbindungszeichenfolge anzeigen]

8.  Kopieren Sie Verbindungszeichenfolge zur Verwendung in Ihrer Anwendung.

    ![Verbindungszeichenfolge anzeigen][1]

> [WACOM.NOTE] Ihre Websiteanwendung greift über den Namen der Verbindungszeichenfolge auf Verbindungszeichenfolgen zu. In .NET-Anwendungen stehen Verbindungszeichenfolgen im Objekt **connectionStrings** zur Verfügung. In anderen Programmiersprachen stehen Verbindungszeichenfolgen als Umgebungsvariablen zur Verfügung. Weitere Informationen finden Sie unter [Konfigurieren von Websites][Konfigurieren von Websites].

  [ClearDB]: http://www.cleardb.com/
  [Azure Store]: /de-de/gallery/store/
  [Azure-Website]: /de-de/documentation/services/web-sites/
  [Gewusst wie: Erstellen einer MySQL-Datenbank über den Azure Store]: #CreateFromStore
  [Gewusst wie: Erstellen einer MySQL-Datenbank als verknüpfte Ressource für eine Azure-Website]: #CreateForWebSite
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com
  [Add-On im Store auswählen]: ./media/create-mysql-db/select-store.png
  [ClearDB MySQL-Datenbank auswählen]: ./media/create-mysql-db/select-cleardb-mysql.png
  [MySQL-Datenbank im Store kaufen]: ./media/create-mysql-db/purchase-mysql.png
  [Kauf prüfen und abschließen]: ./media/create-mysql-db/complete-mysql-purchase.png
  [MySQL-Datenbank im Azure-Portal verwalten]: ./media/create-mysql-db/manage-mysql-add-on.png
  [MySQL-Verbindungsinformationen]: ./media/create-mysql-db/mysql-conn-info.png
  [Website mit Datenbank erstellen]: ./media/create-mysql-db/custom_create.png
  [Websitedetails angeben]: ./media/create-mysql-db/provide-website-details.png
  [MySQL-Details angeben]: ./media/create-mysql-db/provide-mysql-details.png
  [Zum Website-Dashboard navigieren]: ./media/create-mysql-db/go-to-website-dashboard.png
  [Zur Registerkarte "Konfigurieren" navigieren]: ./media/create-mysql-db/go-to-configure-tab.png
  [Verbindungszeichenfolge anzeigen]: ./media/create-mysql-db/show-conn-string.png
  [1]: ./media/create-mysql-db/shown-conn-string.png
  [Konfigurieren von Websites]: ../web-sites-configure/
