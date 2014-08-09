<properties linkid="develop-mobile-tutorials-command-line-administration" urlDisplayName="Command Line Administration" pageTitle="Administering a Mobile Service at the command line - Azure tutorial" metaKeywords="" description="Learn how to create, deploy, and manage your Azure Mobile Service using command-line tools." metaCanonical="" services="" documentationCenter="Mobile" title="Automate mobile services with command-line tools" authors="glenga" solutions="" manager="" editor="" />

Automatisieren von mobilen Diensten mit Befehlszeilentools
==========================================================

In diesem Thema erfahren Sie, wie Sie die Azure-Befehlszeilentools verwenden, um das Erstellen und Verwalten von Azure Mobile Services zu automatisieren. In diesem Thema erfahren Sie, wie Sie mithilfe der Befehlszeilentools die Installation und die ersten Schritte vornehmen und wie Sie diese Tools einsetzen, um die folgenden Mobile Services-Aufgaben durchzuführen:

-   [Erstellen eines neuen mobilen Dienstes](#create-service)
-   [Erstellen einer neuen Tabelle](#create-table)
-   [Registrieren eines Skripts für einen Tabellenvorgang](#register-script)
-   [Auflisten von Tabellen](#list-tables)
-   [Löschen einer vorhandenen Tabelle](#delete-table)
-   [Auflisten von mobilen Diensten](#list-services)
-   [Löschen eines vorhandenen mobilen Diensts](#delete-service)

Bei der Zusammenführung in eine einzelne Skript- oder Batch-Datei automatisieren diese individuellen Befehle den Erstellungs-, den Verifizierungs- oder den Löschungsvorgang eines mobilen Diensts.

Um die Azure-Befehlszeilentools zur Verwaltung von Mobile Services verwenden zu können, benötigen Sie ein Azure-Konto, bei dem die Azure Mobile Services-Funktion aktiviert ist.

-   Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion](http://www.windowsazure.com/de-de/pricing/free-trial/).

-   Wenn Sie bereits über ein Konto verfügen und die Azure Mobile Services-Vorschau aktivieren möchten, finden Sie Informationen dazu unter [Aktivieren von Azure-Vorschaufunktionen](../create-a-windows-azure-account/#enable).

In diesem Thema wird eine Auswahl von allgemeinen Verwaltungsaufgaben behandelt, die von den Azure-Befehlszeilentools unterstützt werden. Weitere Informationen finden Sie unter [Azure-Befehlszeilentools-Dokumentation](/de-de/manage/linux/other-resources/command-line-tools/#Commands_to_manage_mobile_services).

Installieren der ToolsInstallieren der Azure-Befehlszeilentools
---------------------------------------------------------------

Die folgende Liste enthält Informationen zur Installation der Befehlszeilentools, abhängig von Ihrem Betriebssystem:

-   **Windows**: Laden Sie den [Azure-Befehlszeilentools-Installer](http://go.microsoft.com/fwlink/p?LinkID=275464) herunter. Öffnen Sie die heruntergeladene MSI-Datei, und führen Sie die Installationsschritte durch, zu denen Sie aufgefordert werden.

-   **Mac**: Laden Sie den [Azure SDK Installer](http://go.microsoft.com/fwlink/p?LinkId=252249) herunter. Öffnen Sie die heruntergeladene PKG-Datei, und führen Sie die Installationsschritte durch, zu denen Sie aufgefordert werden.

-   **Linux**: Installieren Sie die neueste Version von [Node.js](http://nodejs.org/) (siehe [Install Node.js via Package Manager](https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager) (Installieren von Node.js mithilfe des Paket-Managers, in englischer Sprache), und führen Sie dann den folgenden Befehl aus:

        npm install azure-cli -g

Geben Sie `azure` in die Eingabeaufforderung ein, um die Installation zu überprüfen. Wenn die Installation erfolgreich ist, wird eine Liste aller verfügbaren `azure`-Befehle angezeigt.

Importieren der EinstellungenHerunterladen und Importieren der Veröffentlichungseinstellungen
---------------------------------------------------------------------------------------------

Laden Sie zunächst Ihre Veröffentlichungseinstellungen herunter, und importieren Sie sie. Anschließend können Sie die Tools verwenden, um Azure Services zu erstellen und zu verwalten. Mit dem Befehl `account download` können Sie Ihre Veröffentlichungseinstellungen herunterladen:

     azure account download

Ihr Standardbrowser wird geöffnet, und Sie werden aufgefordert, sich beim Verwaltungsportal anzumelden. Nachdem Sie sich angemeldet haben, wird die `.publishsettings`-Datei heruntergeladen. Notieren Sie sich den Speicherort, unter dem die Datei gespeichert wird.

Importieren Sie anschließend die `.publishsettings`-Datei, indem Sie den folgenden Befehl ausführen und `<path-to-settings-file>` durch den Pfad zu Ihrer `.publishsettings`-Datei ersetzen:

     azure account import <path-to-settings-file>

Sie können alle vom Befehl `import` gespeicherten Informationen entfernen, indem Sie den Befehl `account clear` verwenden:

     azure account clear

Verwenden Sie die Option `-help`, um eine Liste der Optionen für `account-`Befehle anzuzeigen.

     azure account -help

Nachdem Sie Ihre Veröffentlichungseinstellungen importiert haben, löschen Sie die `.publishsettings`-Datei aus Sicherheitsgründen. Weitere Informationen finden Sie im Thema zur [Installation der Azure-Befehlszeilentools für Mac und Linux](http://go.microsoft.com/fwlink/p/?LinkId=275795). Sie können nun damit beginnen, Azure Mobile Services aus der Befehlszeile heraus oder in Batch-Dateien zu erstellen und zu verwalten.

Erstellen eines DienstsErstellen eines mobilen Diensts
------------------------------------------------------

Sie können die Befehlszeilentools verwenden, um eine neue mobile Dienstinstanz zu erstellen. Während Sie den mobilen Dienst erstellen, können Sie außerdem eine SQL-Datenbankinstanz auf einem neuen Server erstellen.

Über den folgenden Befehl wird eine neue mobile Dienstinstanz in Ihrem Abonnement erstellt, wobei `<service-name>` den Namen des neuen mobilen Diensts darstellt. Außerdem ist `<server-admin>` der Anmeldename des neuen Servers, und `<server-password>` ist das Kennwort für die neue Anmeldung:

     azure mobile create <service-name> <server-admin> <server-password>

Der Befehl `mobile create` schlägt fehl, wenn der angegebene mobile Dienst vorhanden ist. Sie sollten bei Ihren automatisierten Skripts einen mobilen Dienst löschen, bevor Sie diesen neu zu erstellen versuchen.

Auflisten von DienstenAuflisten von vorhandenen mobilen Diensten in einem Abonnement
------------------------------------------------------------------------------------

Der folgende Befehl gibt eine Liste aller mobilen Dienste in einem Azure-Abonnement zurück:

     azure mobile list

Dieser Befehl zeigt zudem den aktuellen Status und die URL des jeweiligen mobilen Diensts an.

Löschen eines DienstsLöschen eines vorhandenen mobilen Diensts
--------------------------------------------------------------

Sie können die Befehlszeilentools verwenden, um einen vorhandenen mobilen Dienst zusammen mit der zugehörigen SQL-Datenbank und dem entsprechenden Server zu löschen. Mit dem folgenden Befehl wird der mobile Dienst gelöscht, wobei `<service-name>` der Name des zu löschenden mobilen Diensts ist:

     azure mobile delete <service-name> -a -q

Durch Einbeziehen der Parameter `-a` und `-q` löscht dieser Befehl außerdem die SQL-Datenbank und den entsprechenden Server, die vom mobilen Dienst verwendet werden, ohne dass dabei eine Eingabeaufforderung angezeigt wird.

**Hinweis**

Wenn Sie den Parameter `-q` nicht zusammen mit `-a` oder `-d` angeben, wird die Ausführung unterbrochen, und Sie werden aufgefordert, Löschoptionen für die SQL-Datenbank auszuwählen. Verwenden Sie ausschließlich den Parameter `-a`, wenn kein weiterer Dienst die Datenbank oder den Server verwendet. Verwenden Sie anderenfalls den Parameter `-d`, um nur die Daten zu löschen, welche zu dem mobilen Dienst gehören, der gelöscht werden soll.

Erstellen einer TabelleErstellen einer Tabelle im mobilen Dienst
----------------------------------------------------------------

Mit dem folgenden Befehl wird eine Tabelle im angegebenen mobilen Dienst erstellt. Dabei ist `<service-name>` der Name des mobilen Diensts, und `<table-name>` ist der Name der zu erstellenden Tabelle:

     azure mobile table create <service-name> <table-name>

Dadurch wird eine neue Tabelle mit den Standardberechtigungen, `application`, für die folgenden Tabellenvorgänge erstellt: `insert`, `read`, `update` und `delete`.

Mit dem folgenden Befehl wird eine neue Tabelle erstellt, die die öffentliche Berechtigung `read` aufweist, die aber zugleich die Berechtigung `delete` nur bei Administratoren gewährt:

     azure mobile table create <service-name> <table-name> -p read=public,delete=admin

Die folgende Tabelle enthält den Skriptberechtigungswert im Gegensatz zum Berechtigungswert im [Azure-Verwaltungsportal](https://manage.windowsazure.com/).

<table border="1" width="100%"><tr><th>Skriptwert</th><th>Verwaltungsportalwert</th></tr>
<tr><td><code>public</code></td><td>Jeder Benutzer</td></tr>
<tr><td><code>application</code> (default)</td><td>Jeder Benutzer mit Anwendungsschlüssel</td></tr>
<tr><td><code>user</code></td><td>Nur authentifizierte Benutzer</td></tr>
<tr><td><code>admin	</code></td><td>Nur Skripts und Administratoren</td></tr></table>



Der Befehl `mobile table create` schlägt fehl, wenn die angegebene Tabelle bereits vorhanden ist. Sie sollten bei Ihren automatisierten Skripts eine Tabelle löschen, bevor Sie diese neu zu erstellen versuchen.

Auflisten von TabellenAuflisten von vorhandenen Tabellen in einem mobilen Dienst
--------------------------------------------------------------------------------

Der folgende Befehl gibt eine Liste aller Tabellen in einem mobilen Dienst zurück, wobei `<service-name>` der Name des mobilen Diensts ist:

     azure mobile table list <service-name>

Dieser Befehl zeigt außerdem die Anzahl der Indizes zu jeder Tabelle und die Anzahl der Datenzeilen an, die sich aktuell in der Tabelle befinden.

Löschen einer TabelleLöschen einer vorhandenen Tabelle aus dem mobilen Dienst
-----------------------------------------------------------------------------

Mit dem folgenden Befehl wird eine Tabelle aus dem mobilen Dienst gelöscht. Dabei ist `<service-name>` der Name des mobilen Diensts, und `<table-name>` ist der Name der zu löschenden Tabelle:

     azure mobile table delete <service-name> <table-name> -q

Verwenden Sie in automatisierten Skripts den Parameter `-q`, um die Tabelle zu löschen, ohne dass dabei eine Bestätigungsaufforderung angezeigt wird, welche die Ausführung blockiert.

Registrieren eines SkriptsRegistrieren eines Skripts für einen Tabellenvorgang
------------------------------------------------------------------------------

Mit dem folgenden Befehl wird eine Funktion für einen Vorgang in einer Tabelle hochgeladen und registriert, wobei `<service-name>` der Name des mobilen Diensts ist. Außerdem ist `<table-name>` der Name der Tabelle, und `<operation>` stellt den Tabellenvorgang dar, welcher `read`, `insert`, `update` oder `delete` lauten kann:

     azure mobile script upload <service-name> table/<table-name>.<operation>.js

Beachten Sie, dass mit diesem Vorgang eine JavaScript (.js)-Datei vom lokalen Computer hochgeladen wird. Der Name der Datei muss sich aus Tabellen- und Vorgangsnamen zusammensetzen, und die Datei muss sich im Unterordner `table` relativ zu dem Verzeichnis befinden, in dem der Befehl ausgeführt wird. Beim folgenden Vorgang wird beispielsweise ein neues `insert`-Skript hochgeladen und registriert, das zur Tabelle `TodoItems` gehört:

     azure mobile script upload todolist table/todoitems.insert.js

Die Funktionsdeklaration in der Skriptdatei muss ebenfalls mit dem registrierten Tabellenvorgang übereinstimmen. Das bedeutet, dass bei einem `insert`-Skript das hochgeladene Skript eine Funktion mit folgender Signatur enthält:

     function insert(item, user, request) {
            ...
        } 

Weitere Informationen über das Registrieren von Skripts finden Sie unter [Mobile Services: Serverskriptreferenz](http://go.microsoft.com/fwlink/p?LinkId=262293).

