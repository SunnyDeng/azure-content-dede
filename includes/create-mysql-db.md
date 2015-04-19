#Erstellen einer MySQL-Datenbank in Azure

In diesem Leitfaden erfahren Sie, wie Sie [ClearDB] zum Erstellen einer MySQL-Datenbank aus dem [Azure Store] verwenden und wie Sie eine MySQL-Datenbank als verknüpfte Ressource während der Erstellung einer [Azure-Website][Waws] erstellen. [ClearDB] ist ein fehlertoleranter Database-as-a-Service-Anbieter, mit dem Sie MySQL-Datenbanken in Azure-Datencentern ausführen und verwalten und von jeder beliebigen Anwendung Verbindungen zu diesen Datenbanken herstellen können.  

##Inhaltsverzeichnis
* [Gewusst wie: Erstellen einer MySQL-Datenbank über den Azure Store](#CreateFromStore)
* [Gewusst wie: Erstellen einer MySQL-Datenbank als verknüpfte Ressource für eine Azure-Website](#CreateForWebSite)

> [AZURE.NOTE] Wenn Sie beim Erstellen einer Website eine MySQL-Datenbank erstellen, kann dies nur eine kostenlose sein. Wenn Sie eine MySQL-Datenbank über den Azure Store erstellen, können Sie eine kostenlose Datenbank erstellen oder unter bezahlten Optionen wählen.

<h2><a id="CreateFromStore"></a>Gewusst wie: Erstellen einer MySQL-Datenbank über den Azure Store</h2>

Zum Erstellen einer MySQL-Datenbank aus dem [Azure Store] gehen Sie folgendermaßen vor:

1. Melden Sie sich am [Azure-Verwaltungsportal][Portal] an.
2. Klicken Sie auf **+ Neu** am unteren Rand der Seite, und wählen Sie dann **Store** aus.

	![Select add-on from store](./media/create-mysql-db/select-store.png)

3. Wählen Sie **ClearDB MySQL-Datenbank**, klicken Sie dann auf den Pfeil nach unten im Fenster.

	![Select ClearDB MySQL Database](./media/create-mysql-db/select-cleardb-mysql.png)

4. Wählen Sie einen Plan, geben Sie einen Datenbanknamen ein, wählen Sie eine Region aus, und klicken Sie unten im Fenster auf den Pfeil.

	![Purchase MySQL database from store](./media/create-mysql-db/purchase-mysql.png)

5. Klicken Sie auf das Häkchen, um den Kauf abzuschließen.

	![Review and complete your purchase](./media/create-mysql-db/complete-mysql-purchase.png)

6. Nachdem die Datenbank erstellt wurde, können Sie sie auf der Registerkarte **Add-Ons** im Verwaltungsportal verwalten.

	![Manage MySQL database in Azure portal](./media/create-mysql-db/manage-mysql-add-on.png)

7. Sie können die Informationen zur Datenbankverbindung abrufen, indem Sie unten auf der Seite auf **Verbindungsinformationen** klicken (siehe oben).

	![MySql connection information](./media/create-mysql-db/mysql-conn-info.png) 


<h2><a id="CreateForWebSite"></a>Gewusst wie: Erstellen einer MySQL-Datenbank als verknüpfte Ressource für eine Azure-Website</h2>

Zum Erstellen einer MySQL-Datenbank als verknüpfte Ressource bei der Erstellung einer [Azure-Website][Waws] gehen Sie folgendermaßen vor:

1. Melden Sie sich am [Azure-Verwaltungsportal][Portal] an.
2. Klicken Sie auf **+ Neu** am unteren Rand der Seite, und wählen Sie dann **Compute**, **Website** und **Mit Datenbank erstellen** aus.

	![Create website with database](./media/create-mysql-db/custom_create.png)

3. Geben Sie eine **URL** für Ihre Website an, wählen Sie die **Region** für Ihre Website aus, und wählen Sie dann **Eine neue MySQL-Datenbank erstellen ** aus der Dropdownliste **Datenbank** aus. Optional können Sie den Standardnamen für die Verbindungszeichenfolge ersetzen. Klicken Sie auf den Pfeil unten auf der Seite.

	![Provide website details](./media/create-mysql-db/provide-website-details.png) 

4. Geben Sie einen **Namen** für die Datenbank an, wählen Sie die **Region** für Ihre Datenbank aus (diese sollte mit der Region für Ihre Website identisch sein), stimmen Sie den Bestimmungen von ClearDB zu, und klicken Sie dann auf das Häkchen am unteren Rand des Fensters.

	![Provide MySQL details](./media/create-mysql-db/provide-mysql-details.png)

5. Klicken Sie nach Erstellung der Website auf deren Namen, um zum Dashboard Ihrer Website zu gelangen.

	![Go to web site dashboard](./media/create-mysql-db/go-to-website-dashboard.png)

6. Klicken Sie auf **Konfigurieren**.

	![Go to configure tab](./media/create-mysql-db/go-to-configure-tab.png)

7. Führen Sie einen Bildlauf nach unten bis zum Abschnitt **Verbindungszeichenfolgen** aus, und klicken Sie auf **Verbindungszeichenfolgen anzeigen**. 

	![Show connection string](./media/create-mysql-db/show-conn-string.png)

8. Kopieren Sie Verbindungszeichenfolge zur Verwendung in Ihrer Anwendung.

	![Shown connection string](./media/create-mysql-db/shown-conn-string.png)

> [AZURE.NOTE] Ihre Websiteanwendung greift über den Namen der Verbindungszeichenfolge auf Verbindungszeichenfolge zu. Bei .NET-Anwendungen stehen Verbindungszeichenfolgen in dem Objekt **connectionStrings** zur Verfügung. In anderen Programmiersprachen stehen Verbindungszeichenfolgen als Umgebungsvariablen zur Verfügung. Weitere Informationen finden Sie unter [Konfigurieren von Websites][Konfigurieren].

[ClearDB]: http://www.cleardb.com/
[waws]: /de-de/documentation/services/web-sites/
[Azure Store]: /de-de/gallery/store/
[Portal]: http://manage.windowsazure.com
[Konfigurieren]: ../web-sites-configure/
<!--HONumber=42-->
