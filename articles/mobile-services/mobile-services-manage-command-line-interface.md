<properties
	pageTitle="Verwalten von Mobile Services über die Befehlszeile | Microsoft Azure"
	description="Erfahren Sie, wie Sie Azure Mobile Services mithilfe von Befehlszeilentools erstellen, bereitstellen und verwalten."
	services="mobile-services"
	documentationCenter="Mobile"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="NA"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="01/27/2016"
	ms.author="glenga"/>

# Automatisieren von mobilen Diensten mit Befehlszeilentools

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


##Übersicht

In diesem Thema erfahren Sie, wie Sie die Azure-Befehlszeilentools verwenden, um das Erstellen und Verwalten von Azure Mobile Services zu automatisieren. In diesem Thema erfahren Sie, wie Sie mithilfe der Befehlszeilentools die Installation und die ersten Schritte vornehmen und wie Sie diese Tools einsetzen, um die wichtigsten Mobile Services-Aufgaben durchzuführen.

Bei der Zusammenführung in eine einzelne Skript- oder Batch-Datei automatisieren diese individuellen Befehle den Erstellungs-, den Verifizierungs- oder den Löschungsvorgang eines mobilen Diensts.

In diesem Thema wird eine Auswahl von allgemeinen Verwaltungsaufgaben behandelt, die von den Azure-Befehlszeilentools unterstützt werden. Weitere Informationen finden Sie unter [Azure-Befehlszeilentools-Dokumentation][reference-docs].

##Installieren der Azure-Befehlszeilentools

Die folgende Liste enthält Informationen zur Installation der Befehlszeilentools, abhängig von Ihrem Betriebssystem:

* **Windows**: Laden Sie den [Azure-Befehlszeilentools-Installer][windows-installer] herunter. Öffnen Sie die heruntergeladene MSI-Datei, und führen Sie die Installationsschritte durch, zu denen Sie aufgefordert werden.

* **Mac**: Laden Sie den [Azure SDK Installer][mac-installer] herunter. Öffnen Sie die heruntergeladene PKG-Datei, und führen Sie die Installationsschritte durch, zu denen Sie aufgefordert werden.

* **Linux**: Installieren Sie die neueste Version von [Node.js][nodejs-org] (siehe [Install Node.js via Package Manager][install-node-linux] (Installieren von Node.js mithilfe des Paket-Managers, in englischer Sprache)), und führen Sie dann den folgenden Befehl aus:

	npm install azure-cli -g

Geben Sie `azure` an der Eingabeaufforderung ein, um die Installation zu überprüfen. Wenn die Installation erfolgreich ist, wird eine Liste aller verfügbaren `azure`-Befehle angezeigt.

##Herunterladen und Importieren der Veröffentlichungseinstellungen

Laden Sie zunächst Ihre Veröffentlichungseinstellungen herunter, und importieren Sie sie. Anschließend können Sie die Tools verwenden, um Azure Services zu erstellen und zu verwalten. Mit dem Befehl `account download` können Sie die Veröffentlichungseinstellungen herunterladen:

	azure account download

Ihr Standardbrowser wird geöffnet, und Sie werden aufgefordert, sich beim klassischen Azure-Portal anzumelden. Nachdem Sie sich angemeldet haben, wird die `.publishsettings`-Datei heruntergeladen. Notieren Sie sich den Speicherort, unter dem die Datei gespeichert wird.

Importieren Sie als Nächstes die `.publishsettings`-Datei, indem Sie den folgenden Befehl ausführen und dabei `<path-to-settings-file>` durch den Pfad zu Ihrer `.publishsettings`-Datei ersetzen:

	azure account import <path-to-settings-file>

Sie können alle vom Befehl <code>import</code> gespeicherten Informationen entfernen, indem Sie den Befehl <code>account clear</code> verwenden:

	azure account clear

Verwenden Sie die Option `-help`, um eine Liste der Optionen für `account`-Befehle anzuzeigen.

	azure account -help

Nachdem Sie Ihre Veröffentlichungseinstellungen importiert haben, löschen Sie die `.publishsettings`-Datei aus Sicherheitsgründen. Weitere Informationen finden Sie im Thema zur [Installation der Azure-Befehlszeilentools für Mac und Linux]. Sie können nun damit beginnen, Azure Mobile Services aus der Befehlszeile heraus oder in Batch-Dateien zu erstellen und zu verwalten.

##Erstellen eines mobilen Diensts

Sie können die Befehlszeilentools verwenden, um eine neue mobile Dienstinstanz zu erstellen. Während Sie den mobilen Dienst erstellen, können Sie außerdem eine SQL-Datenbankinstanz auf einem neuen Server erstellen.

Über den folgenden Befehl wird eine neue mobile Dienstinstanz in Ihrem Abonnement erstellt, wobei `<service-name>` den Namen des neuen mobilen Diensts darstellt. Außerdem ist `<server-admin>` der Anmeldename des neuen Servers, und `<server-password>` ist das Kennwort für die neue Anmeldung:

	azure mobile create <service-name> <server-admin> <server-password>

Der Befehl `mobile create` schlägt fehl, wenn der angegebene mobile Dienst vorhanden ist. Sie sollten bei Ihren automatisierten Skripts einen mobilen Dienst löschen, bevor Sie diesen neu zu erstellen versuchen.

##Auflisten vorhandener mobiler Dienste in einem Abonnement

> [AZURE.NOTE] In der Befehlszeilenschnittstelle funktionieren Befehle im Zusammenhang mit "List" und "Script" nur mit dem JavaScript-Back-End.

Der folgende Befehl gibt eine Liste aller mobilen Dienste in einem Azure-Abonnement zurück:

	azure mobile list

Dieser Befehl zeigt zudem den aktuellen Status und die URL des jeweiligen mobilen Diensts an.

##Löschen ein vorhandenes mobiles Diensts

Sie können die Befehlszeilentools verwenden, um einen vorhandenen mobilen Dienst zusammen mit der zugehörigen SQL-Datenbank und dem entsprechenden Server zu löschen. Mit dem folgenden Befehl wird der mobile Dienst gelöscht, wobei `<service-name>` der Name des zu löschenden mobilen Diensts ist:

	azure mobile delete <service-name> -a -q

Durch Einbeziehen der Parameter `-a` und `-q` löscht dieser Befehl außerdem die SQL-Datenbank und den Server, die vom mobilen Dienst verwendet werden, ohne dass dabei eine Eingabeaufforderung angezeigt wird.

> [AZURE.NOTE] Wenn Sie den Parameter <code>-q</code> nicht zusammen mit <code>-a</code> oder <code>-d</code> angeben, wird die Ausführung unterbrochen, und Sie werden aufgefordert, Löschoptionen für die SQL-Datenbank auszuwählen. Verwenden Sie ausschließlich den Parameter <code>-a</code>, wenn kein weiterer Dienst die Datenbank oder den Server verwendet. Verwenden Sie anderenfalls den Parameter <code>-d</code>, um nur die Daten zu löschen, welche zu dem mobilen Dienst gehören, der gelöscht werden soll.

##Erstellen einer Tabelle im mobilen Dienst

Mit dem folgenden Befehl wird eine Tabelle im angegebenen mobilen Dienst erstellt. Dabei ist `<service-name>` der Name des mobilen Diensts, und `<table-name>` ist der Name der zu erstellenden Tabelle:

	azure mobile table create <service-name> <table-name>

Damit wird eine neue Tabelle mit den Standardberechtigungen, `application`, für folgende Tabellenvorgänge erstellt: `insert`, `read`, `update` und `delete`.

Mit dem folgenden Befehl wird eine neue Tabelle erstellt, die die öffentliche Berechtigung `read` aufweist, die aber zugleich die Berechtigung `delete` nur bei Administratoren gewährt:

	azure mobile table create <service-name> <table-name> -p read=public,delete=admin

Die folgende Tabelle enthält den Skriptberechtigungswert im Gegensatz zum Berechtigungswert im [klassischen Azure-Portal].

|Skriptwert|Portalwert| |========|========| |`public`|Jeder| |`application`(Standard)|Jeder mit dem Anwendungsschlüssel| |`user`|Nur authentifizierte Benutzer| |`admin`|Nur Skripts und Admins|

Der Befehl `mobile table create` schlägt fehl, wenn die angegebene Tabelle bereits vorhanden ist. Sie sollten bei Ihren automatisierten Skripts eine Tabelle löschen, bevor Sie diese neu zu erstellen versuchen.

##Auflisten vorhandener Tabellen in einem mobilen Dienst

Der folgende Befehl gibt eine Liste aller Tabellen in einem mobilen Dienst zurück, wobei `<service-name>` der Name des mobilen Diensts ist:

	azure mobile table list <service-name>

Dieser Befehl zeigt außerdem die Anzahl der Indizes zu jeder Tabelle und die Anzahl der Datenzeilen an, die sich aktuell in der Tabelle befinden.

##Löschen einer vorhandenen Tabelle aus einem mobilen Dienst

Mit dem folgenden Befehl wird eine Tabelle aus dem mobilen Dienst gelöscht. Dabei ist `<service-name>` der Name des mobilen Diensts, und `<table-name>` ist der Name der zu löschenden Tabelle:

	azure mobile table delete <service-name> <table-name> -q

Verwenden Sie in Automatisierungsskripts den Parameter `-q`, um die Tabelle zu löschen, ohne dass dabei eine Bestätigungsaufforderung angezeigt wird, die die Ausführung blockiert.

##Registrieren eines Skripts für einen Tabellenvorgang

Mit dem folgenden Befehl wird eine Funktion für einen Tabellenvorgang hochgeladen und registriert. Dabei entspricht `<service-name>` dem Namen des mobilen Diensts, `<table-name>` dem Namen der Tabelle und `<operation>` dem Tabellenvorgang, der `read`, `insert`, `update` oder `delete` lauten kann:

	azure mobile script upload <service-name> table/<table-name>.<operation>.js

Beachten Sie, dass mit diesem Vorgang eine JavaScript (.js)-Datei vom lokalen Computer hochgeladen wird. Der Name der Datei muss sich aus dem Tabellen- und Vorgangsnamen zusammensetzen, und die Datei muss sich im Unterordner `table` relativ zum Verzeichnis befinden, in dem der Befehl ausgeführt wird. Beim folgenden Vorgang wird beispielsweise ein neues `insert`-Skript hochgeladen und registriert, das zur Tabelle `TodoItems` gehört:

	azure mobile script upload todolist table/todoitems.insert.js

Die Funktionsdeklaration in der Skriptdatei muss ebenfalls mit dem registrierten Tabellenvorgang übereinstimmen. Dies bedeutet, dass bei einem `insert`-Skript das hochgeladene Skript eine Funktion mit folgender Signatur enthält:

	function insert(item, user, request) {
	    ...
	}

Weitere Informationen über das Registrieren von Skripts finden Sie unter [Mobile Services: Serverskriptreferenz].

<!-- Anchors. -->
[Download and install the command-line tools]: #install
[Download and import publish settings]: #import
[Create a new mobile service]: #create-service
[Get the master key]: #get-master-key
[Create a new table]: #create-table
[Register a new table script]: #register-script
[Delete an existing table]: #delete-table
[Delete an existing mobile service]: #delete-service
[Test the mobile service]: #test-service
[List mobile services]: #list-services
[List tables]: #list-tables
[Next steps]: #next-steps

<!-- Images. -->











<!-- URLs. -->
[Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/p?LinkId=262293

[klassischen Azure-Portal]: https://manage.windowsazure.com/
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager

[mac-installer]: http://go.microsoft.com/fwlink/p?LinkId=252249
[windows-installer]: http://go.microsoft.com/fwlink/p?LinkID=275464
[reference-docs]: http://azure.microsoft.com/documentation/articles/virtual-machines-command-line-tools/#Commands_to_manage_mobile_services
[Installation der Azure-Befehlszeilentools für Mac und Linux]: http://go.microsoft.com/fwlink/p/?LinkId=275795

<!---HONumber=AcomDC_0211_2016-->