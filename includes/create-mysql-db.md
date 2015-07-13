Diese Anleitung zeigt, wie Sie mit [ClearDB] eine MySQL-Datenbank über den [Azure Store] erstellen und wie Sie eine MySQL-Datenbank als verknüpfte Ressource erstellen, wenn Sie eine [Azure-Website][waws] erstellen. [ClearDB] ist ein fehlertoleranter Datenbank-Dienstanbieter, mit dem Sie MySQL-Datenbanken in Azure-Datencentern ausführen und verwalten und aus jeder Anwendung Verbindungen zu diesen herstellen können.

> [AZURE.NOTE]Wenn Sie beim Erstellen einer Website eine MySQL-Datenbank erstellen, kann dies nur eine kostenlose sein. Wenn Sie eine MySQL-Datenbank über den Azure Store erstellen, können Sie eine kostenlose Datenbank erstellen oder unter bezahlten Optionen wählen.

## Erstellen einer MySQL-Datenbank über den Azure Store

Gehen Sie folgendermaßen vor, um eine MySQL-Datenbank über den [Azure Store] zu erstellen:

1. Melden Sie sich beim [Azure-Verwaltungsportal][portal] an.
2. Klicken Sie unten auf der Seite auf **+NEU** und wählen Sie anschließend **MARKETPLACE** aus.

	![Add-On im Store auswählen](./media/create-mysql-db/select-store.png)

3. Wählen Sie **ClearDB MySQL-Datenbank** aus, und klicken Sie unten im Fenster auf den Pfeil.

	![ClearDB MySQL-Datenbank auswählen](./media/create-mysql-db/select-cleardb-mysql.png)

4. Wählen Sie einen Plan, geben Sie einen Datenbanknamen ein, wählen Sie eine Region aus, und klicken Sie unten im Fenster auf den Pfeil.

	![MySQL-Datenbank im Store kaufen](./media/create-mysql-db/purchase-mysql.png)

5. Klicken Sie auf das Häkchen, um den Kauf abzuschließen.

	![Kauf prüfen und abschließen](./media/create-mysql-db/complete-mysql-purchase.png)

6. Nachdem die Datenbank erstellt wurde, können Sie sie auf der Registerkarte **ADD-ONS** im Verwaltungsportal verwalten.

	![MySQL-Datenbank im Azure-Portal verwalten](./media/create-mysql-db/manage-mysql-add-on.png)

7. Sie können die Datenbankverbindungsinformationen abrufen, indem Sie unten auf der Seite auf **Verbindungsinformationen** klicken (siehe oben).

	![MySQL-Verbindungsinformationen](./media/create-mysql-db/mysql-conn-info.png)


## Erstellen einer MySQL-Datenbank als verknüpfte Ressource für eine Azure-Website

Um beim Erstellen einer [Azure-Website][waws] eine MySQL-Datenbank als verknüpfte Ressource zu erstellen, gehen Sie folgendermaßen vor:

1. Melden Sie sich beim [Azure-Verwaltungsportal][portal] an.
2. Klicken Sie unten auf der Seite auf **+NEU**, und wählen Sie **SERVER**, **WEBSITE** und **MIT DATENBANK ERSTELLEN** aus.

	![Website mit Datenbank erstellen](./media/create-mysql-db/custom_create.png)

3. Geben Sie eine **URL** für Ihre Website ein, und wählen Sie die **REGION** für Ihre Website aus. Wählen Sie anschließend aus der Dropdownliste **DATENBANK** die Option **Neue MySQL-Datenbank erstellen** aus. Optional können Sie den Standardnamen für die Verbindungszeichenfolge ersetzen. Klicken Sie auf den Pfeil unten auf der Seite.

	![Websitedetails angeben](./media/create-mysql-db/provide-website-details.png)

4. Geben Sie einen **NAMEN** für die Datenbank ein, wählen Sie die **REGION** für die Datenbank aus (muss mit der Region für die Website übereinstimmen), stimmen Sie den ClearDB-Bedingungen zu, und klicken Sie auf das Häkchen unten im Fenster.

	![MySQL-Details angeben](./media/create-mysql-db/provide-mysql-details.png)

5. Klicken Sie nach Erstellung der Website auf deren Namen, um zum Dashboard Ihrer Website zu gelangen.

	![Zum Website-Dashboard navigieren](./media/create-mysql-db/go-to-website-dashboard.png)

6. Klicken Sie auf **Konfigurieren**.

	![Zur Registerkarte "Konfigurieren" navigieren](./media/create-mysql-db/go-to-configure-tab.png)

7. Scrollen Sie zum Abschnitt **Verbindungszeichenfolgen**, und klicken Sie auf **Verbindungszeichenfolgen anzeigen**.

	![Verbindungszeichenfolge anzeigen](./media/create-mysql-db/show-conn-string.png)

8. Kopieren Sie Verbindungszeichenfolge zur Verwendung in Ihrer Anwendung.

	![Verbindungszeichenfolge anzeigen](./media/create-mysql-db/shown-conn-string.png)

> [AZURE.NOTE]Ihre Websiteanwendung greift über den Namen der Verbindungszeichenfolge auf Verbindungszeichenfolge zu. In .NET-Anwendungen stehen Verbindungszeichenfolgen im Objekt **connectionStrings** zur Verfügung. In anderen Programmiersprachen stehen Verbindungszeichenfolgen als Umgebungsvariablen zur Verfügung. Weitere Informationen finden Sie unter [Konfigurieren von Websites][configure].

[ClearDB]: http://www.cleardb.com/
[waws]: /documentation/services/web-sites/
[Azure Store]: ../articles/store.md
[portal]: http://manage.windowsazure.com
[configure]: ../article/app-service-web/web-sites-configure.md

<!---HONumber=July15_HO1-->