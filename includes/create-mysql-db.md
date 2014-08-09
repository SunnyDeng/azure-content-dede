
# Erstellen einer MySQL-Datenbank in Azure

Diese Anleitung zeigt, wie Sie mit [ClearDB][1] eine MySQL-Datenbank über den [Azure Store](/en-us/store/overview/) erstellen und wie Sie eine MySQL-Datenbank als verknüpfte Ressource erstellen, wenn Sie eine [Azure-Website](/de-de/manage/services/web-sites/) erstellen. [ClearDB][1] ist ein fehlertoleranter Datenbank-Dienstanbieter, mit dem Sie MySQL-Datenbanken in Azure-Datencentern ausführen und verwalten und aus jeder Anwendung Verbindungen zu diesen herstellen können.

## Inhaltsverzeichnis

* [Gewusst wie: Erstellen einer MySQL-Datenbank über den Azure
  Store](#CreateFromStore)
* [Gewusst wie: Erstellen einer MySQL-Datenbank als verknüpfte Ressource
  für eine Azure-Website](#CreateForWebSite)
<div class="dev-callout"> 
<b>Hinweis</b> 
<p>Wenn Sie beim Erstellen einer Website eine MySQL-Datenbank erstellen, können Sie nur eine kostenlose Datenbank erstellen. Wenn Sie eine MySQL-Datenbank über den Azure Store erstellen, können Sie eine kostenlose Datenbank erstellen oder unter bezahlten Optionen wählen.</p> 
</div>

 <h2><a id="CreateFromStore" ></a>Gewusst wie: Erstellen einer MySQL-Datenbank über den Azure Store</h2>


Gehen Sie folgendermaßen vor, um eine MySQL-Datenbank über den [Azure Store](/en-us/store/overview/) zu erstellen:

1.  Melden Sie sich im [Azure-Verwaltungsportal][2] an. 2.  Klicken Sie unten auf der Seite auf **+NEU**, und wählen Sie anschließend **STORE** aus.
    
    ![Add-On im Store
    auswählen](./media/create-mysql-db/select-store.png)

3.  Wählen Sie **ClearDB MySQL-Datenbank** aus, und klicken Sie unten im Fenster auf den Pfeil.
    
    ![ClearDB MySQL-Datenbank
    auswählen](./media/create-mysql-db/select-cleardb-mysql.png)

4.  Wählen Sie einen Plan, geben Sie einen Datenbanknamen ein, wählen Sie eine Region aus, und klicken Sie unten im Fenster auf den Pfeil.
    
    ![MySQL-Datenbank im Store
    kaufen](./media/create-mysql-db/purchase-mysql.png)

5.  Klicken Sie auf das Häkchen, um den Kauf abzuschließen.
    
    ![Kauf prüfen und
    abschließen](./media/create-mysql-db/complete-mysql-purchase.png)

6.  Nachdem die Datenbank erstellt wurde, können Sie sie auf der Registerkarte **ADD-ONS** im Verwaltungsportal verwalten.
    
    ![MySQL-Datenbank im Azure-Portal
    verwalten](./media/create-mysql-db/manage-mysql-add-on.png)

7.  Sie können die Datenbankverbindungsinformationen abrufen, indem Sie unten auf der Seite auf **Verbindungsinformationen** klicken (siehe oben).
    
    ![MySQL-Verbindungsinformationen](./media/create-mysql-db/mysql-conn-info.png)

<h2><a id="CreateForWebSite" ></a>Gewusst wie: Erstellen einer MySQL-Datenbank als verknüpfte Ressource für eine Azure-Website</h2>


Um beim Erstellen einer [Azure-Website](/de-de/manage/services/web-sites/) eine MySQL-Datenbank als verknüpfte Ressource zu erstellen, gehen Sie folgendermaßen vor:

1.  Melden Sie sich im [Azure-Verwaltungsportal][2] an. 2.  Klicken Sie unten auf der Seite auf **+NEU**, wählen Sie **COMPUTE**, **WEBSITE** und **CREATE WITH DATABASE**.
    
    ![Website mit Datenbank
    erstellen](./media/create-mysql-db/custom_create.png)

3.  Geben Sie einen **URL** für Ihre Website ein, wählen Sie die **REGION** für Ihre Website, und wählen Sie in der Dropdownliste **DATENBANK** die Option **Create a new MySQL database**. Optional können Sie den Standardnamen für die Verbindungszeichenfolge ersetzen. Klicken Sie auf den Pfeil unten auf der Seite.
    
    ![Websitedetails
    angeben](./media/create-mysql-db/provide-website-details.png)

4.  Geben Sie einen **NAMEN** für die Datenbank ein, wählen Sie die **REGION** für die Datenbank aus (muss mit der Region für die Website übereinstimmen), stimmen Sie den ClearDB-Bedingungen zu, und klicken Sie auf das Häkchen unten im Fenster.
    
    ![MySQL-Details
    angeben](./media/create-mysql-db/provide-mysql-details.png)

5.  Klicken Sie nach der Erstellung der Website auf den Namen Ihrer Website, um zum Dashboard Ihrer Website zu gehen.
    
    ![Zum Website-Dashboard
    navigieren](./media/create-mysql-db/go-to-website-dashboard.png)

6.  Klicken Sie auf **Konfigurieren**.
    
    ![Zur Registerkarte "Konfigurieren"
    navigieren](./media/create-mysql-db/go-to-configure-tab.png)

7.  Führen Sie einen Bildlauf zum Abschnitt **Verbindungszeichenfolgen** aus, und klicken Sie auf **Show Connection Strings**.
    
    ![Verbindungszeichenfolge
    anzeigen](./media/create-mysql-db/show-conn-string.png)

8.  Kopieren Sie Verbindungszeichenfolge zur Verwendung in Ihrer Anwendung.
    
    ![Verbindungszeichenfolge
    anzeigen](./media/create-mysql-db/shown-conn-string.png)
<div class="dev-callout"> 
<b>Hinweis</b> 
<p>Ihre Websiteanwendung greift über den Namen der Verbindungszeichenfolge auf Verbindungszeichenfolge zu. In .NET-Anwendungen stehen Verbindungszeichenfolgen im Objekt <b>connectionStrings</b> zur Verfügung. In anderen Programmiersprachen stehen Verbindungszeichenfolgen als Umgebungsvariablen zur Verfügung. Weitere Informationen finden Sie unter <a href="/de-de/manage/services/web-sites/how-to-configure-websites/">Konfigurieren von Websites</a>.</p> 
</div>

 

[1]: http://www.cleardb.com/
[2]: http://manage.windowsazure.com