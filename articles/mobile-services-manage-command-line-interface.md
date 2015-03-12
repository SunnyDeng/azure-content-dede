<properties 
	pageTitle="Verwalten von Mobile Services über die Befehlszeile - Azure-Lernprogramm" 
	description="Erfahren Sie, wie Sie Azure Mobile Services mithilfe von Befehlszeilentools erstellen, bereitstellen und verwalten." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="glenga"/>

# Automatisieren von mobilen Diensten mit Befehlszeilentools 

In diesem Thema erfahren Sie, wie Sie die Azure-Befehlszeilentools verwenden, um das Erstellen und Verwalten von Azure Mobile Services zu automatisieren. In diesem Thema erfahren Sie, wie Sie mithilfe der Befehlszeilentools die Installation und die ersten Schritte vornehmen und wie Sie diese Tools einsetzen, um die folgenden Mobile Services-Aufgaben durchzuführen:

-	[Erstellen eines neuen mobilen Diensts]
-	[Erstellen einer neuen Tabelle]
-   [Registrieren eines Skripts für einen Tabellenvorgang][Registrieren eines Skripts für einen Tabellenvorgang]
-   [Auflisten von Tabellen]
- 	[Löschen einer vorhandenen Tabelle]
-	[Auflisten von mobilen Diensten]
-   [Löschen eines vorhandenen mobilen Diensts]
 
Bei der Zusammenführung in eine einzelne Skript- oder Batch-Datei automatisieren diese individuellen Befehle den Erstellungs-, den Verifizierungs- oder den Löschungsvorgang eines mobilen Diensts. 

Um die Azure-Befehlszeilentools zur Verwaltung von Mobile Services verwenden zu können, benötigen Sie ein Azure-Konto, bei dem die Azure Mobile Services-Funktion aktiviert ist.

+ Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Weitere Informationen finden Sie unter <a href="http://azure.microsoft.com/pricing/free-trial/" target="_blank">Kostenlose Azure-Testversion</a>.

+ Wenn Sie bereits über ein Konto verfügen und die Azure Mobile Services-Vorschau aktivieren möchten, finden Sie unter <a href="http://azure.microsoft.com/documentation/articles/php-create-account/#enable" target="_blank">Aktivieren der Azure-Vorschaufeatures</a> weitere Informationen dazu.

In diesem Thema wird eine Auswahl von allgemeinen Verwaltungsaufgaben behandelt, die von den Azure-Befehlszeilentools unterstützt werden. Weitere Informationen finden Sie unter [Dokumentation zu Azure-Befehlszeilentools][reference-docs].

<!--+  Sie müssen die Azure-Befehlszeilentools auf Ihrem lokalen Computer herunterladen und installieren. Befolgen Sie hierzu die Anweisungen im ersten Abschnitt dieses Themas. 

+ (Optional) Wenn Sie HTTP-Anforderungen direkt über die Befehlszeile ausführen möchten, müssen Sie cURL oder ein gleichwertiges Tool verwenden. cURL wird von vielen Plattformen unterstützt. Installieren Sie cURL für die benötigte Plattform über die <a href=http://go.microsoft.com/fwlink/p/?LinkId=275676 target="_blank">cURL-Downloadseite</a>.-->

<h2><a name="install"></a>Installieren der Azure-Befehlszeilentools</h2>

Die folgende Liste enthält Informationen zur Installation der Befehlszeilentools, abhängig von Ihrem Betriebssystem:

* **Windows**: Laden Sie den [Azure-Befehlszeilentools-Installer][windows-installer] herunter. Öffnen Sie die heruntergeladene MSI-Datei, und führen Sie die Installationsschritte durch, zu denen Sie aufgefordert werden.

* **Mac**: Laden Sie den [Azure SDK Installer][mac-installer] herunter. Öffnen Sie die heruntergeladene PKG-Datei, und führen Sie die Installationsschritte durch, zu denen Sie aufgefordert werden.

* **Linux**: Installieren Sie die neueste Version von [Node.js][nodejs-org] (siehe [Installieren von Node.js mithilfe des Paket-Managers][install-node-linux]),und führen Sie dann den folgenden Befehl aus:

		npm install azure-cli -g

Geben Sie `azure` an der Eingabeaufforderung ein, um die Installation zu überprüfen. Wenn die Installation erfolgreich ist, wird eine Liste aller verfügbaren `azure`-Befehle angezeigt.
<h2><a name="import-account"></a>Herunterladen und Importieren der Veröffentlichungseinstellungen</h2>

Laden Sie zunächst Ihre Veröffentlichungseinstellungen herunter, und importieren Sie sie. Anschließend können Sie die Tools verwenden, um Azure Services zu erstellen und zu verwalten. Mit dem `account download`-Befehl können Sie die Veröffentlichungseinstellungen herunterladen:

		azure account download

Ihr Standardbrowser wird geöffnet, und Sie werden aufgefordert, sich beim Verwaltungsportal anzumelden. Nachdem Sie sich angemeldet haben, wird die `.publishsettings`-Datei heruntergeladen. Notieren Sie sich den Speicherort, unter dem die Datei gespeichert wird.

Importieren Sie anschließend die `.publishsettings`-Datei, indem Sie den folgenden Befehl ausführen und dabei "<path-to-settings-file>" durch den Pfad zu Ihrer `.publishsettings`-Datei ersetzen:

		azure account import <path-to-settings-file>

Sie können alle vom <code>import</code>-Befehl gespeicherten Informationen entfernen, indem Sie den <code>account clear</code>-Befehl verwenden:

		azure account clear

Verwenden Sie die help-Option, um eine Liste der Optionen für `account`-Befehle anzuzeigen:

		azure account -help

Nachdem Sie Ihre Veröffentlichungseinstellungen importiert haben, löschen Sie die `.publishsettings`-Datei aus Sicherheitsgründen. Weitere Informationen finden Sie unter [Installieren der Azure-Befehlszeilentools für Mac und Linux]. Sie können nun damit beginnen, Azure Mobile Services aus der Befehlszeile heraus oder in Batch-Dateien zu erstellen und zu verwalten.  

<h2><a name="create-service"></a>Erstellen eines mobilen Diensts</h2>

Sie können die Befehlszeilentools verwenden, um eine neue mobile Dienstinstanz zu erstellen. Während Sie den mobilen Dienst erstellen, können Sie außerdem eine SQL-Datenbankinstanz auf einem neuen Server erstellen. 

Über den folgenden Befehl wird eine neue mobile Dienstinstanz in Ihrem Abonnement erstellt, wobei `<service-name>` den Namen des neuen mobilen Diensts darstellt. Außerdem ist `<server-admin>` der Anmeldename des neuen Servers, und `<server-password>` ist das Kennwort für die neue Anmeldung:

		azure mobile create <service-name> <server-admin> <server-password>

Wenn der angegebene mobile Dienst vorhanden ist, tritt beim Ausführen des `mobile create`-Befehls ein Fehler auf. Sie sollten bei Ihren automatisierten Skripts einen mobilen Dienst löschen, bevor Sie diesen neu zu erstellen versuchen.

<h2><a name="list-services"></a>Auflisten vorhandener mobiler Dienste in einem Abonnement</h2>

Der folgende Befehl gibt eine Liste aller mobilen Dienste in einem Azure-Abonnement zurück:

		azure mobile list

Dieser Befehl zeigt zudem den aktuellen Status und die URL des jeweiligen mobilen Diensts an.

<h2><a name="delete-service"></a>Löschen ein vorhandenes mobiles Diensts</h2>

Sie können die Befehlszeilentools verwenden, um einen vorhandenen mobilen Dienst zusammen mit der zugehörigen SQL-Datenbank und dem entsprechenden Server zu löschen. Mit dem folgenden Befehl wird der mobile Dienst gelöscht, wobei <service-name> der Name des zu löschenden mobilen Diensts ist:

		azure mobile delete <service-name> -a -q

Durch Einbeziehen der Parameter -a und -q löscht dieser Befehl außerdem die SQL-Datenbank und den Server, die vom mobilen Dienst verwendet werden, ohne dass dabei eine Eingabeaufforderung angezeigt wird.

> [AZURE.NOTE] Wenn Sie den <code>-q</code>-Parameter nicht zusammen mit <code>-a</code> oder <code>-d</code> angeben, wird die Ausführung unterbrochen, und Sie werden aufgefordert, Löschoptionen für die SQL-Datenbank auszuwählen. Verwenden Sie ausschließlich den <code>-a</code>-Parameter, wenn kein weiterer Dienst die Datenbank oder den Server verwendet. Verwenden Sie anderenfalls den <code>-d</code>-Parameter, um nur die Daten zu löschen, welche zu dem zu löschenden mobilen Dienst gehören.

<h2><a name="create-table"></a>Erstellen einer Tabelle im mobilen Dienst</h2>

Mit dem folgenden Befehl wird eine Tabelle im angegebenen mobilen Dienst erstellt, wobei <service-name> der Name des mobilen Diensts und <table-name><table-name>der Name der zu erstellenden Tabelle ist:

		azure mobile table create <service-name> <table-name>

Damit wird eine neue Tabelle mit den Standardberechtigungen, `application`, für folgende Tabellenvorgänge erstellt: `insert`, `read`, `update`, und `delete`. 

Mit dem folgenden Befehl wird eine neue Tabelle erstellt, die die öffentliche Berechtigung `read` aufweist, die aber zugleich die Berechtigung `delete` nur bei Administratoren gewährt:

		azure mobile table create <service-name> <table-name> -p read=public,delete=admin

Die folgende Tabelle enthält den Skriptberechtigungswert im Gegensatz zum Berechtigungswert im [Azure-Verwaltungsportal].

<table border="1" width="100%"><tr><th>Skriptwert</th><th>Verwaltungsportalwert</th></tr>
<tr><td><code>public</code></td><td>Jeder Benutzer</td></tr>
<tr><td><code>application</code> (Standard)</td><td>Jeder Benutzer mit Anwendungsschlüssel</td></tr>
<tr><td><code>user</code></td><td>Nur authentifizierte Benutzer</td></tr>
<tr><td><code>admin	</code></td><td>Nur Skripts und Administratoren</td></tr></table>

Beim Ausführen des `mobile table create`-Befehls tritt ein Fehler auf, wenn die angegebene Tabelle bereits vorhanden ist. Sie sollten bei Ihren automatisierten Skripts eine Tabelle löschen, bevor Sie diese neu zu erstellen versuchen.

<h2><a name="list-tables"></a>Auflisten vorhandener Tabellen in einem mobilen Dienst</h2>

Der folgende Befehl gibt eine Liste aller Tabellen in einem mobilen Dienst zurück, wobei <service-name> der Name des mobilen Diensts ist:

		azure mobile table list <service-name>

Dieser Befehl zeigt außerdem die Anzahl der Indizes zu jeder Tabelle und die Anzahl der Datenzeilen an, die sich aktuell in der Tabelle befinden.

<h2><a name="delete-table"></a>Löschen einer vorhandenen Tabelle aus einem mobilen Dienst</h2>

Mit dem folgenden Befehl wird eine Tabelle aus dem angegebenen mobilen Dienst gelöscht, wobei <service-name> der Name des mobilen Diensts und <table-name><table-name>der Name der zu löschenden Tabelle ist:

		azure mobile table delete <service-name> <table-name> -q

Verwenden Sie in automatisierten Skripts den Parameter -q, um die Tabelle zu löschen, ohne dass dabei eine Bestätigungsaufforderung angezeigt wird, welche die Ausführung blockiert.

<h2><a name="register-script"></a>Registrieren eines Skripts für einen Tabellenvorgang</h2>

Mit dem folgenden Befehl wird eine Funktion an einen Vorgang an einer Tabelle hochgeladen und registriert, wobei <service-name> der Name des mobilen Diensts, <table-name><table-name>" der Name der Tabelle und "<operation>" ist der Tabellenvorgang, der `read`, `insert`, `update` oder `delete` sein kann:

		azure mobile script upload <service-name> table/<table-name>.<operation>.js

Beachten Sie, dass mit diesem Vorgang eine JavaScript (.js)-Datei vom lokalen Computer hochgeladen wird. Der Name der Datei muss sich aus dem Tabellen- und Vorgangsnamen zusammensetzen, und die Datei muss sich im Unterordner `table` relativ zum Verzeichnis befinden, in dem der Befehl ausgeführt wird. Beim folgenden Vorgang wird beispielsweise ein neues `insert`-Skript hochgeladen und registriert, das zur Tabelle `TodoItems` gehört:

		azure mobile script upload todolist table/todoite
	ms.insert.js

Die Funktionsdeklaration in der Skriptdatei muss ebenfalls mit dem registrierten Tabellenvorgang übereinstimmen. Dies bedeutet, dass bei einem  `insert`-Skript das hochgeladene Skript eine Funktion mit folgender Signatur enthält:

		function insert(item, user, request) {
		    ...
		} 

Weitere Informationen zum Registrieren von Skripts finden Sie unter [Mobile Services: Serverskriptreferenz].

<!--<h2><a name="test-service"></a>Testen des mobilen Diensts</h2>

Wenn Sie die Erstellung Ihres mobilen Diensts automatisieren möchten, können Sie optional cURL oder einen anderen Befehlszeilen-Anforderungsgenerator verwenden. 

## <a name="nextsteps"> </a>Nächste Schritte
Nächste Schritte...
-->
<!-- Anchors. -->
[Herunterladen und Installieren der Befehlszeilentools]: #install
[Herunterladen und Importieren der Veröffentlichungseinstellungen]: #import
[Erstellen Sie einen neuen mobilen Diensts]: #create-service
[Abrufen des Hauptschlüssels]: #get-master-key
[Erstellen einer neuen Tabelle]: #create-table
[Registrieren eines neuen Tabellenskripts]: #register-script
[Löschen einer vorhandenen Tabelle]: #delete-table
[Löschen eines vorhandenen mobilen Diensts]: #delete-service
[Testen des mobilen Diensts]: #test-service
[Auflisten von mobilen Diensten]: #list-services
[Auflisten von Tabellen]: #list-tables
[Nächste Schritte]: #next-steps

<!-- Images. -->











<!-- URLs. -->
[Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/p?LinkId=262293

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager

[mac-installer]: http://go.microsoft.com/fwlink/p?LinkId=252249
[windows-installer]: http://go.microsoft.com/fwlink/p?LinkID=275464
[reference-docs]: /de-de/manage/linux/other-resources/command-line-tools/#Commands_to_manage_mobile_services
[Installieren der Azure-Befehlszeilentools für Mac und Linux]: http://go.microsoft.com/fwlink/p/?LinkId=275795



<!--HONumber=42-->
