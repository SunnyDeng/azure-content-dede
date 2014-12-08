#Erstellen einer MySQL-Datenbank in Azure

Diese Anleitung zeigt, wie Sie mit [ClearDB] eine MySQL-Datenbank über den [Azure Store] erstellen und wie Sie eine MySQL-Datenbank als verknüpfte Ressource erstellen, wenn Sie eine [Azure-Website][waws] erstellen. [ClearDB] ist ein fehlertoleranter Datenbank-Dienstanbieter, mit dem Sie MySQL-Datenbanken in Azure-Datencentern ausführen und verwalten und aus jeder Anwendung Verbindungen zu diesen herstellen können.  

##Inhaltsverzeichnis
* [Gewusst wie: Erstellen einer MySQL-Datenbank über den Azure Store](#CreateFromStore)
* [Gewusst wie: Erstellen einer MySQL-Datenbank als verknüpfte Ressource für eine Azure-Website](#CreateForWebSite)

<div class="dev-callout"> 
<b>Hinweis</b> 
<p>Wenn Sie beim Erstellen einer Website eine MySQL-Datenbank erstellen, kann dies nur eine kostenlose sein. Wenn Sie eine MySQL-Datenbank über den Azure Store erstellen, können Sie eine kostenlose Datenbank erstellen oder unter bezahlten Optionen wählen.</p> 
</div>

<h2><a id="CreateFromStore"></a>Gewusst wie: Erstellen einer MySQL-Datenbank über den Azure Store</h2>

Gehen Sie folgendermaßen vor, um eine MySQL-Datenbank über den [Azure Store] zu erstellen:

1. Melden Sie sich beim [Azure-Verwaltungsportal][portal] an.
2. Klicken Sie unten auf der Seite auf **+NEU**, und wählen Sie anschließend **STORE** aus.

	![Select add-on from store](./media/create-mysql-db/select-store.png)

3. Wählen Sie **ClearDB MySQL-Datenbank** aus, und klicken Sie unten im Fenster auf den Pfeil.

	![Select ClearDB MySQL Database](./media/create-mysql-db/select-cleardb-mysql.png)

4. Wählen Sie einen Plan, geben Sie einen Datenbanknamen ein, wählen Sie eine Region aus, und klicken Sie unten im Fenster auf den Pfeil.

	![Purchase MySQL database from store](./media/create-mysql-db/purchase-mysql.png)

5. Klicken Sie auf das Häkchen, um den Kauf abzuschließen.

	![Review and complete your purchase](./media/create-mysql-db/complete-mysql-purchase.png)

6. Nachdem die Datenbank erstellt wurde, können Sie sie auf der Registerkarte **ADD-ONS** im Verwaltungsportal verwalten.

	![Manage MySQL database in Azure portal](./media/create-mysql-db/manage-mysql-add-on.png)

7. Sie können die Datenbankverbindungsinformationen abrufen, indem Sie unten auf der Seite auf **VERBINDUNGSINFORMATIONEN** klicken (siehe oben).

	![MySql connection information](./media/create-mysql-db/mysql-conn-info.png) 


<h2><a id="CreateForWebSite"></a>Gewusst wie: Erstellen einer MySQL-Datenbank als verknüpfte Ressource für eine Azure-Website</h2>

Um beim Erstellen einer [Azure-Website][waws] eine MySQL-Datenbank als verknüpfte Ressource zu erstellen, gehen Sie folgendermaßen vor:

1. Melden Sie sich beim [Azure-Verwaltungsportal][portal] an.
2. Klicken Sie unten auf der Seite auf **+NEU**, und wählen Sie **SERVER**, **WEBSITE** und **MIT DATENBANK ERSTELLEN** aus.

	![Create website with database](./media/create-mysql-db/custom_create.png)

3. Geben Sie eine **URL** für Ihre Website ein, wählen Sie die **REGION** für Ihre Website, und wählen Sie in der Dropdownliste **DATENBANK** die Option **Create a new MySQL database**. Optional können Sie den Standardnamen für die Verbindungszeichenfolge ersetzen. Klicken Sie auf den Pfeil unten auf der Seite.

	![Provide website details](./media/create-mysql-db/provide-website-details.png) 

4. Geben Sie einen **NAMEN** für die Datenbank ein, wählen Sie die **REGION** für die Datenbank aus (muss mit der Region für die Website übereinstimmen), stimmen Sie den ClearDB-Bedingungen zu, und klicken Sie auf das Häkchen unten im Fenster.

	![Provide MySQL details](./media/create-mysql-db/provide-mysql-details.png)

5. Klicken Sie nach Erstellung der Website auf deren Namen, um zum Dashboard Ihrer Website zu gelangen.

	![Go to web site dashboard](./media/create-mysql-db/go-to-website-dashboard.png)

6. Klicken Sie auf **KONFIGURIEREN**.

	![Go to configure tab](./media/create-mysql-db/go-to-configure-tab.png)

7. Scrollen Sie zum Abschnitt **Verbindungszeichenfolgen**, und klicken Sie auf **Verbindungszeichenfolgen anzeigen**. 

	![Show connection string](./media/create-mysql-db/show-conn-string.png)

8. Kopieren Sie Verbindungszeichenfolge zur Verwendung in Ihrer Anwendung.

	![Shown connection string](./media/create-mysql-db/shown-conn-string.png)

> [WACOM.NOTE] Ihre Websiteanwendung greift über den Namen der Verbindungszeichenfolge auf Verbindungszeichenfolge zu. In .NET-Anwendungen stehen Verbindungszeichenfolgen im Objekt **connectionStrings** zur Verfügung. In anderen Programmiersprachen stehen Verbindungszeichenfolgen als Umgebungsvariablen zur Verfügung. Weitere Informationen finden Sie unter [Konfigurieren von Websites][configure].

[ClearDB]: http://www.cleardb.com/
[waws]: /de-de/documentation/services/web-sites/
[Azure Store]: /de-de/gallery/store/
[Portal]: http://manage.windowsazure.com
[Konfigurieren]: ../web-sites-configure/
