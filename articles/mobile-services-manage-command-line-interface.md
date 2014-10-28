<properties linkid="develop-mobile-tutorials-command-line-administration" urlDisplayName="Command Line Administration" pageTitle="Administering a Mobile Service at the command line - Azure tutorial" metaKeywords="" description="Learn how to create, deploy, and manage your Azure Mobile Service using command-line tools." metaCanonical="" services="" documentationCenter="Mobile" title="Automate mobile services with command-line tools" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Automatisieren von mobilen Diensten mit Befehlszeilentools

In diesem Thema erfahren Sie, wie Sie die Azure-Befehlszeilentools verwenden, um das Erstellen und Verwalten von Azure Mobile Services zu automatisieren. In diesem Thema erfahren Sie, wie Sie mithilfe der Befehlszeilentools die Installation und die ersten Schritte vornehmen und wie Sie diese Tools einsetzen, um die folgenden Mobile Services-Aufgaben durchzuführen:

-   [Erstellen eines neuen mobilen Dienstes][Erstellen eines neuen mobilen Dienstes]
-   [Erstellen einer neuen Tabelle][Erstellen einer neuen Tabelle]
-   [Registrieren eines Skripts für einen Tabellenvorgang][Registrieren eines Skripts für einen Tabellenvorgang]
-   [Auflisten von Tabellen][Auflisten von Tabellen]
-   [Löschen einer vorhandenen Tabelle][Löschen einer vorhandenen Tabelle]
-   [Auflisten von mobilen Diensten][Auflisten von mobilen Diensten]
-   [Löschen eines vorhandenen mobilen Diensts][Löschen eines vorhandenen mobilen Diensts]

Bei der Zusammenführung in eine einzelne Skript- oder Batch-Datei automatisieren diese individuellen Befehle den Erstellungs-, den Verifizierungs- oder den Löschungsvorgang eines mobilen Diensts.

Um die Azure-Befehlszeilentools zur Verwaltung von Mobile Services verwenden zu können, benötigen Sie ein Azure-Konto, bei dem die Azure Mobile Services-Funktion aktiviert ist.

-   Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion][Kostenlose Azure-Testversion].

-   Wenn Sie bereits über ein Konto verfügen und die Azure Mobile Services-Vorschau aktivieren möchten, finden Sie Informationen dazu unter [Aktivieren von Azure-Vorschaufunktionen][Aktivieren von Azure-Vorschaufunktionen].

In diesem Thema wird eine Auswahl von allgemeinen Verwaltungsaufgaben behandelt, die von den Azure-Befehlszeilentools unterstützt werden. Weitere Informationen finden Sie unter [Azure-Befehlszeilentools-Dokumentation][Azure-Befehlszeilentools-Dokumentation].

<!--+  You must download and install the Azure command-line tools to your local machine. To do this, follow the instructions in the first section of this topic.   + (Optional) To be able to execute HTTP requests directly from the command-line, you must use cURL or an equivalent tool. cURL runs on a variety of platforms. Locate and install cURL for your specific platform from the <a href=http://go.microsoft.com/fwlink/p/?LinkId=275676 target="_blank">cURL download  page</a>.-->

## <a name="install"></a><span class="short-header">Installieren der Tools</span>Installieren der Azure-Befehlszeilentools

Die folgende Liste enthält Informationen zur Installation der Befehlszeilentools, abhängig von Ihrem Betriebssystem:

-   **Windows**: Laden Sie den [Azure-Befehlszeilentools-Installer][Azure-Befehlszeilentools-Installer] herunter. Öffnen Sie die heruntergeladene MSI-Datei, und führen Sie die Installationsschritte durch, zu denen Sie aufgefordert werden.

-   **Mac**: Laden Sie den [Azure SDK Installer][Azure SDK Installer] herunter. Öffnen Sie die heruntergeladene PKG-Datei, und führen Sie die Installationsschritte durch, zu denen Sie aufgefordert werden.

-   **Linux**: Installieren Sie die neueste Version von [Node.js][Node.js] (siehe [Install Node.js via Package Manager][Install Node.js via Package Manager] (Installieren von Node.js mithilfe des Paket-Managers, in englischer Sprache), und führen Sie dann den folgenden Befehl aus:

        npm install azure-cli -g

Geben Sie `azure` an der Eingabeaufforderung ein, um die Installation zu überprüfen. Wenn die Installation erfolgreich ist, wird eine Liste aller verfügbaren `azure`-Befehle angezeigt.

## <a name="import-account"></a><span class="short-header">Importieren der Einstellungen</span>Herunterladen und Importieren der Veröffentlichungseinstellungen

</p>
Laden Sie zunächst Ihre Veröffentlichungseinstellungen herunter, und importieren Sie sie. Anschließend können Sie die Tools verwenden, um Azure Services zu erstellen und zu verwalten. Mit dem Befehl `account download` können Sie die Veröffentlichungseinstellungen herunterladen:

        azure account download

Ihr Standardbrowser wird geöffnet, und Sie werden aufgefordert, sich beim Verwaltungsportal anzumelden. Nachdem Sie sich angemeldet haben, wird die `.publishsettings`-Datei heruntergeladen. Notieren Sie sich den Speicherort, unter dem die Datei gespeichert wird.

Importieren Sie als Nächstes die `.publishsettings`-Datei, indem Sie den folgenden Befehl ausführen und dabei `<path-to-settings-file>` durch den Pfad zu Ihrer `.publishsettings`-Datei ersetzen:

        azure account import <path-to-settings-file>

Sie können alle vom Befehl `import` gespeicherten Informationen entfernen, indem Sie den Befehl `account clear` verwenden:

        azure account clear

Verwenden Sie die Option `-help`, um eine Liste der Optionen für `account`-Befehle anzuzeigen.

        azure account -help

Nachdem Sie Ihre Veröffentlichungseinstellungen importiert haben, löschen Sie die `.publishsettings`-Datei aus Sicherheitsgründen. Weitere Informationen finden Sie im Thema zur [Installation der Azure-Befehlszeilentools für Mac und Linux][Installation der Azure-Befehlszeilentools für Mac und Linux]. Sie können nun damit beginnen, Azure Mobile Services aus der Befehlszeile heraus oder in Batch-Dateien zu erstellen und zu verwalten.

## <a name="create-service"></a><span class="short-header">Erstellen eines Diensts</span>Erstellen eines mobilen Diensts

Sie können die Befehlszeilentools verwenden, um eine neue mobile Dienstinstanz zu erstellen. Während Sie den mobilen Dienst erstellen, können Sie außerdem eine SQL-Datenbankinstanz auf einem neuen Server erstellen.

Über den folgenden Befehl wird eine neue mobile Dienstinstanz in Ihrem Abonnement erstellt, wobei `<service-name>` den Namen des neuen mobilen Diensts darstellt. Außerdem ist `<server-admin>` der Anmeldename des neuen Servers, und `<server-password>` ist das Kennwort für die neue Anmeldung:

        azure mobile create <service-name> <server-admin> <server-password>

Der Befehl `mobile create` schlägt fehl, wenn der angegebene mobile Dienst vorhanden ist. Sie sollten bei Ihren automatisierten Skripts einen mobilen Dienst löschen, bevor Sie diesen neu zu erstellen versuchen.

## <a name="list-services"></a><span class="short-header">Auflisten von Diensten</span>Auflisten von vorhandenen mobilen Diensten in einem Abonnement

Der folgende Befehl gibt eine Liste aller mobilen Dienste in einem Azure-Abonnement zurück:

        azure mobile list

Dieser Befehl zeigt zudem den aktuellen Status und die URL des jeweiligen mobilen Diensts an.

## <a name="delete-service"></a><span class="short-header">Löschen eines Diensts</span>Löschen eines vorhandenen mobilen Diensts

Sie können die Befehlszeilentools verwenden, um einen vorhandenen mobilen Dienst zusammen mit der zugehörigen SQL-Datenbank und dem entsprechenden Server zu löschen. Mit dem folgenden Befehl wird der mobile Dienst gelöscht, wobei `<service-name>` der Name des zu löschenden mobilen Diensts ist:

        azure mobile delete <service-name> -a -q

Durch Einbeziehen der Parameter `-a` und `-q` löscht dieser Befehl außerdem die SQL-Datenbank und den Server, die vom mobilen Dienst verwendet werden, ohne dass dabei eine Eingabeaufforderung angezeigt wird.

<div class="dev-callout"><strong>Hinweis</strong> 
   <p>Wenn Sie den Parameter <code data-inline="1">-q</code> nicht zusammen mit <code data-inline="1">-a</code> oder <code data-inline="1">-d</code> angeben, wird die Ausf&uuml;hrung angehalten, und Sie werden aufgefordert, L&ouml;schoptionen f&uuml;r die SQL-Datenbank auszuw&auml;hlen. Verwenden Sie ausschlie&szlig;lich den Parameter <code data-inline="1">-a</code>, wenn kein weiterer Dienst die Datenbank oder den Server verwendet. Verwenden Sie andernfalls den Parameter <code data-inline="1">-d</code>, um nur die Daten zu l&ouml;schen, die zum mobilen Dienst geh&ouml;ren, der gel&ouml;scht werden soll.</p>
</div>

## <a name="create-table"></a><span class="short-header">Erstellen einer Tabelle</span>Erstellen einer Tabelle im mobilen Dienst

Mit dem folgenden Befehl wird eine Tabelle im angegebenen mobilen Dienst erstellt. Dabei ist `<service-name>` der Name des mobilen Diensts, und `<table-name>` ist der Name der zu erstellenden Tabelle:

        azure mobile table create <service-name> <table-name>

Dadurch wird eine neue Tabelle mit den Standardberechtigungen, `application`, für die Tabellenvorgänge erstellt: `insert`, `read`, `update` und `delete`.

Mit dem folgenden Befehl wird eine neue Tabelle erstellt, die die öffentliche Berechtigung `read` aufweist, die aber zugleich die Berechtigung `delete` nur bei Administratoren gewährt:

        azure mobile table create <service-name> <table-name> -p read=public,delete=admin

Die folgende Tabelle enthält den Skriptberechtigungswert im Gegensatz zum Berechtigungswert im [Azure-Verwaltungsportal][Azure-Verwaltungsportal].

| Skriptwert                          | Verwaltungsportalwert                  |
|-------------------------------------|----------------------------------------|
| `public`                            | Jeder Benutzer                         |
| `application` (Standardeinstellung) | Jeder Benutzer mit Anwendungsschlüssel |
| `user`                              | Nur authentifizierte Benutzer          |
| `admin `                            | Nur Skripts und Administratoren        |

Der Befehl `mobile table create` schlägt fehl, wenn die angegebene Tabelle bereits vorhanden ist. Sie sollten bei Ihren automatisierten Skripts eine Tabelle löschen, bevor Sie diese neu zu erstellen versuchen.

## <a name="list-tables"></a><span class="short-header">Auflisten von Tabellen</span>Auflisten von vorhandenen Tabellen in einem mobilen Dienst

Der folgende Befehl gibt eine Liste aller Tabellen in einem mobilen Dienst zurück, wobei `<service-name>` der Name des mobilen Diensts ist:

        azure mobile table list <service-name>

Dieser Befehl zeigt außerdem die Anzahl der Indizes zu jeder Tabelle und die Anzahl der Datenzeilen an, die sich aktuell in der Tabelle befinden.

## <a name="delete-table"></a><span class="short-header">Löschen einer Tabelle</span>Löschen einer vorhandenen Tabelle aus dem mobilen Dienst

Mit dem folgenden Befehl wird eine Tabelle aus dem mobilen Dienst gelöscht. Dabei ist `<service-name>` der Name des mobilen Diensts, und `<table-name>` ist der Name der zu löschenden Tabelle:

        azure mobile table delete <service-name> <table-name> -q

Verwenden Sie in Automatisierungsskripts den Parameter `-q`, um die Tabelle zu löschen, ohne dass dabei eine Bestätigungsaufforderung angezeigt wird, die die Ausführung blockiert.

## <a name="register-script"></a><span class="short-header">Registrieren eines Skripts</span>Registrieren eines Skripts für einen Tabellenvorgang

Mit dem folgenden Befehl wird eine Funktion für einen Tabellenvorgang hochgeladen und registriert. Dabei entspricht `<service-name>` dem Namen des mobilen Diensts, `<table-name>` dem Namen der Tabelle und `<operation>` dem Tabellenvorgang, der `read`, `insert`, `update` oder `delete` lauten kann:

        azure mobile script upload <service-name> table/<table-name>.<operation>.js

Beachten Sie, dass mit diesem Vorgang eine JavaScript (.js)-Datei vom lokalen Computer hochgeladen wird. Der Name der Datei muss sich aus dem Tabellen- und Vorgangsnamen zusammensetzen, und die Datei muss sich im Unterordner `table` relativ zum Verzeichnis befinden, in dem der Befehl ausgeführt wird. Beim folgenden Vorgang wird beispielsweise ein neues `insert`-Skript hochgeladen und registriert, das zur Tabelle `TodoItems` gehört:

        azure mobile script upload todolist table/todoitems.insert.js

Die Funktionsdeklaration in der Skriptdatei muss ebenfalls mit dem registrierten Tabellenvorgang übereinstimmen. Dies bedeutet, dass bei einem `insert`-Skript das hochgeladene Skript eine Funktion mit folgender Signatur enthält:

        function insert(item, user, request) {
            ...
        } 

Weitere Informationen über das Registrieren von Skripts finden Sie unter [Mobile Services: Serverskriptreferenz][Mobile Services: Serverskriptreferenz].

<!--<h2><a name="test-service"></a><span class="short-header">Test the service</span>Test the new mobile service</h2>  When you are automating the creation of your mobile service, you can optionally use cURL or another command-line request generator to   ## <a name="nextsteps"> </a>Next Steps Next steps here.... --> <!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Erstellen eines neuen mobilen Dienstes]: #create-service
  [Erstellen einer neuen Tabelle]: #create-table
  [Registrieren eines Skripts für einen Tabellenvorgang]: #register-script
  [Auflisten von Tabellen]: #list-tables
  [Löschen einer vorhandenen Tabelle]: #delete-table
  [Auflisten von mobilen Diensten]: #list-services
  [Löschen eines vorhandenen mobilen Diensts]: #delete-service
  [Kostenlose Azure-Testversion]: http://www.windowsazure.com/de-de/pricing/free-trial/
  [Aktivieren von Azure-Vorschaufunktionen]: http://azure.microsoft.com/de-de/documentation/articles/php-create-account/#enable
  [Azure-Befehlszeilentools-Dokumentation]: /de-de/manage/linux/other-resources/command-line-tools/#Commands_to_manage_mobile_services
  [Azure-Befehlszeilentools-Installer]: http://go.microsoft.com/fwlink/p?LinkID=275464
  [Azure SDK Installer]: http://go.microsoft.com/fwlink/p?LinkId=252249
  [Node.js]: http://nodejs.org/
  [Install Node.js via Package Manager]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
  [Installation der Azure-Befehlszeilentools für Mac und Linux]: http://go.microsoft.com/fwlink/p/?LinkId=275795
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  [Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/p?LinkId=262293
