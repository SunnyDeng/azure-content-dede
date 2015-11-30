<properties
	pageTitle="Vorgehensweise beim Arbeiten mit dem Node-Back-End-Server-SDK für Mobile Apps | Azure App Service"
	description="Sie erhalten Informationen zum Arbeiten mit dem Node-Back-End-Server-SDK für Mobile App Service-Apps unter Azure."
	services="app-service\mobile"
	documentationCenter=""
	authors="adrianha"
	manager=""
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="node"
	ms.topic="article"
	ms.date="11/13/2015"
	ms.author="adrianha"/>

# Verwenden des Node-SDK für Azure Mobile Apps

Dieser Artikel enthält ausführliche Informationen und Beispiele, mit denen veranschaulicht wird, wie Sie in mobilen Azure App Service-Apps ein Node-Back-End verwenden.

	> [AZURE.NOTE] This SDK is in PREVIEW.  As a result, we do not recommend that you use this SDK in production.  The examples
	in this document use v2.0.0-beta1 of azure-mobile-apps.

## <a name="Introduction"></a>Einführung

Mobile Azure App Service-Apps verfügen über eine Funktion zum Hinzufügen einer für Mobilgeräte optimierten Datenzugriff-Web-API zu einer Webanwendung. Das SDK für mobile Azure App Service-Apps wird für ASP.NET- und NodeJS-Webanwendungen bereitgestellt und umfasst die folgenden Vorgänge:

- Tabellenvorgänge (Lesen, Einfügen, Aktualisieren, Löschen) für den Datenzugriff
- Vorgänge der benutzerdefinierten API

Beide Vorgänge ermöglichen die Authentifizierung über alle Identitätsanbieter hinweg, die vom Azure App Service zugelassen werden, z. B. Identitätsanbieter per sozialem Netzwerk, wie Facebook, Twitter, Google und Microsoft, oder Azure Active Directory für die Unternehmensidentität.

Beispiele für die einzelnen Anwendungsfälle finden Sie im [Verzeichnis mit den Beispielen bei GitHub].

### <a name="howto-cmdline-basicapp"></a>Erstellen eines einfachen Node-Back-Ends über die Befehlszeile

Jedes Node-Back-End einer mobilen Azure App Service-App verhält sich am Anfang wie eine ExpressJS-Anwendung. ExpressJS ist das am häufigsten verwendete Webdienst-Framework, das es für Node gibt. Sie können wie folgt eine einfache ExpressJS-Node-Anwendung erstellen:

1. Erstellen Sie in einem Befehlsfenster oder PowerShell-Fenster ein neues Verzeichnis für Ihr Projekt.

	```
	mkdir basicapp
	```

2. Führen Sie „npm init“ aus, um die Paketstruktur zu initialisieren.

	```
	cd basicapp
	npm init
	```

	Im Rahmen des Befehls „npm init“ werden einige Fragen gestellt, um das Projekt initialisieren zu können. Unten ist eine Beispielausgabe angegeben.

	![Ausgabe von npm init][0]

3. Installieren Sie die express- und azure-mobile-apps-Bibliotheken aus dem Repository „npm“.

	```
	npm install --save express azure-mobile-apps
	```

4. Erstellen Sie die Datei „app.js“, um den einfachen mobilen Server zu implementieren.

	```
	var express = require('express'),
		azureMobileApps = require('azure-mobile-apps');

	var app = express(),
		mobile = azureMobileApps();

	// Define a TodoItem table
	mobile.tables.add('TodoItem');

	// Add the mobile API so it is accessible as a Web API
	app.use(mobile);

	// Start listening on HTTP
	app.listen(process.env.PORT || 3000);
	```

Mit dieser Anwendung wird eine einfache, für Mobilgeräte optimierte WebAPI mit einem einzelnen Endpunkt erstellt: /tables/TodoItem. Hiermit ist der authentifizierungsfreie Zugriff auf einen zugrunde liegenden SQL-Datenspeicher mit einem dynamischem Schema möglich. Mit der Anwendung können Sie die Schnellstarts der Clientbibliothek verfolgen:

- [iOS-Client-Schnellstart]
- [Xamarin.iOS-Client-Schnellstart]
- [Xamarin.Android-Client-Schnellstart]
- [Xamarin.Forms-Client-Schnellstart]
- [Windows Phone-Client-Schnellstart]
- [HTML/JavaScript-Client-Schnellstart]

Den Code für diese einfache Anwendung finden Sie im [basicapp-Beispiel bei GitHub].

### <a name="howto-vs2015-basicapp"></a>Erstellen eines Node-Back-Ends mit Visual Studio 2015

Für Visual Studio 2015 ist eine Erweiterung zum Entwickeln der Node-Anwendung in der IDE erforderlich. Laden Sie [Node.js-Tools 1.1 für Visual Studio] herunter, und installieren Sie sie, um zu beginnen. Erstellen Sie eine Express 4.x-Anwendung, nachdem die Installation der Node.js-Tools für Visual Studio abgeschlossen ist:

1. Öffnen Sie das Dialogfeld **Neues Projekt** (über **Datei** > **Neu** > **Projekt...**).

2. Erweitern Sie **Vorlagen** > **JavaScript** > **Node.js**.

3. Wählen Sie die **einfache Azure Node.js Express 4-Anwendung aus**.

4. Geben Sie den Projektnamen ein. Klicken Sie auf *OK*.

	![Visual Studio 2015 – Neues Projekt][1]

5. Klicken Sie mit der rechten Maustaste auf den Knoten **npm**, und wählen Sie **Neue NPM-Pakete installieren...**.

6. Bei der Erstellung der ersten Node-Anwendung müssen Sie den NPM-Katalog aktualisieren. Klicken Sie auf **Aktualisieren**.

7. Geben Sie in das Suchfeld den Text _azure-mobile-apps_ ein. Klicken Sie auf das Paket **azure-mobile-apps 2.0.0** und dann auf **Paket installieren**.

	![Neue NPM-Pakete installieren][2]

8. Klicken Sie auf **Schließen**.

9. Öffnen Sie die Datei _app.js_, um Unterstützung für das Azure Mobile Apps-SDK hinzuzufügen:

  a. Fügen Sie in Zeile 6 den folgenden Code hinzu:

    ```
    var bodyParser = require('body-parser');
    var azureMobileApps = require('azure-mobile-apps');
    ```

  b. Fügen Sie ungefähr in Zeile 27 den folgenden Code hinzu:

	```
	app.use('/users', users);

	// Azure Mobile Apps Initialization
	var mobile = azureMobileApps();
	mobile.tables.add('TodoItem');
	app.use('mobile');
	```

  c. Speichern Sie die Datei .

10. Führen Sie die Anwendung entweder lokal aus (die API wird unter http://localhost:3000 bereitgestellt), oder veröffentlichen Sie sie unter Azure.

### <a name="howto-publish-to-azure"></a>Veröffentlichen des Node-Back-Ends unter Azure

Microsoft Azure verfügt über viele Verfahren zum Veröffentlichen Ihres Node-Back-Ends für mobile Azure App Service-Apps unter dem Azure-Dienst. Hierzu gehören die Nutzung von Bereitstellungstools, die in Visual Studio integriert sind, Befehlszeilentools und Optionen für die fortlaufende Bereitstellung, die auf der Quellcodeverwaltung basieren. Weitere Informationen zu diesem Thema finden Sie im [Azure App Service-Bereitstellungshandbuch].

Azure App Service verfügt über spezielle Hinweise für die Node-Anwendung, die Sie vor dem Bereitstellen lesen sollten:

- So wird‘s gemacht: [Angeben der Node-Version]
- So wird‘s gemacht: [Verwenden von Node-Modulen]

## <a name="TableOperations"></a>Tabellenvorgänge

Das azure-mobile-apps-Node-Server-SDK verfügt über Verfahren zum Verfügbarmachen von Datentabellen, die in SQL Azure als WebAPI gespeichert sind. Es werden fünf Vorgänge bereitgestellt.

| Vorgang | Beschreibung |
| --------- | ----------- |
| GET /tables/\_tablename\_ | Alle Datensätze der Tabelle abrufen |
| GET /tables/\_tablename\_/:id | Bestimmten Datensatz der Tabelle abrufen |
| POST /tables/\_tablename\_ | Neuen Datensatz in der Tabelle erstellen |
| PUT /tables/\_tablename\_/:id | Vorhandenen Datensatz in der Tabelle aktualisieren |
| DELETE /tables/\_tablename\_/:id | Datensatz in der Tabelle löschen |

Diese WebAPI unterstützt [OData] und erweitert das Tabellenschema, um die [Synchronisierung von Offline-Daten] zu unterstützen.

### <a name="howto-dynamicschema"></a>Definieren von Tabellen mit einem dynamischen Schema

Bevor eine Tabelle verwendet werden kann, muss sie definiert werden. Tabellen können mit einem statischen Schema (der Entwickler definiert die Spalten im Schema) oder dynamisch (das SDK steuert das Schema basierend auf eingehenden Anforderungen) definiert werden. Außerdem kann der Entwickler bestimmte Aspekte der WebAPI steuern, indem er der Definition JavaScript-Code hinzufügt.

Die bewährte Methode hierbei lautet: Definieren Sie jede Tabelle in einer JavaScript-Datei im Verzeichnis „tables“, und verwenden Sie dann die tables.import()-Methode, um die Tabellen zu importieren. Zum Erweitern der einfachen App (basic-app) wird die Datei „app.js“ angepasst:

```
var express = require('express'),
	azureMobileApps = require('azure-mobile-apps');

var app = express(),
	mobile = azureMobileApps();

// Define the database schema that is exposed
mobile.tables.import('./tables');

// Provide initialization of any tables that are statically defined
mobile.tables.initialize().then(function () {
	// Add the mobile API so it is accessible as a Web API
	app.use(mobile);

	// Start listening on HTTP
	app.listen(process.env.PORT || 3000);
});
```

Definieren Sie die Tabelle in „./tables/TodoItem.js“:

```
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Additional configuration for the table goes here

module.exports = table;
```

Für Tabellen werden standardmäßig dynamische Schemas verwendet. Um das dynamische Schema global zu deaktivieren, legen Sie die App-Einstellung **MS\_DynamicSchema** im Azure-Portal auf „false“ fest.

Ein vollständiges Beispiel finden Sie im [todo-Beispiel bei GitHub].

### <a name="howto-staticschema"></a>Definieren von Tabellen mit einem statischen Schema

Sie können die Spalten explizit definieren, die über die WebAPI verfügbar gemacht werden sollen. Das azure-mobile-apps-Node-SDK fügt automatisch alle zusätzlichen Spalten, die für die Synchronisierung von Offlinedaten benötigt werden, der von Ihnen bereitgestellten Liste hinzu. Für die Schnellstart-Clientanwendungen ist beispielsweise eine Tabelle mit zwei Spalten erforderlich: „text“ (eine Zeichenfolge) und „complete“ (boolescher Wert). Dies kann wie folgt in der JavaScript-Datei mit der Tabellendefinition (im Verzeichnis „tables“) festgelegt werden:

```
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table
table.columns = {
	"text": "string",
	"complete": "boolean"
};

// Turn off dynamic schema
table.dynamicSchema = false;

module.exports = table;
```

Wenn Sie Tabellen statisch definieren, müssen Sie auch die tables.initialize()-Methode aufrufen, um das Datenbankschema beim Starten zu erstellen. Die tables.initialize()-Methode gibt eine [Zusage] zurück. Hiermit wird sichergestellt, dass der Webdienst keine Anforderungen bereitstellt, bevor die Datenbank initialisiert wurde.

### <a name="howto-sqlexpress-setup"></a>Verwenden von SQL Express als Entwicklungsdatenspeicher auf Ihrem lokalen Computer

Das Node-SDK für mobile Azure-Apps umfasst drei standardmäßige Optionen zum Bereitstellen von Daten:

- Verwenden des **memory**-Treibers zum Bereitstellen eines nicht beständigen Beispielspeichers
- Verwenden des **sql**-Treibers zum Bereitstellen eines SQL Express-Datenspeichers für die Entwicklung
- Verwenden des **sql**-Treibers zum Bereitstellen eines SQL Azure-Datenspeichers für die Produktion

Das Node-SDK für Azure Mobile Apps verwendet das [mssql-Node-Paket], um eine Verbindung mit SQL Express und SQL Azure einzurichten und zu nutzen. Für dieses Paket müssen Sie TCP-Verbindungen auf Ihrer SQL Express-Instanz aktivieren.

   >[AZURE.NOTE]Beim memory-Treiber wird keine vollständige Gruppe von Elementen für Testzwecke bereitgestellt. Wenn Sie Ihr Back-End lokal testen möchten, empfehlen wir die Verwendung eines SQL Express-Datenspeichers und des sql-Treibers.

1. Laden Sie [Microsoft SQL Server 2014 Express] herunter, und installieren Sie die Anwendung. Stellen Sie sicher, dass Sie die Edition „SQL Server 2014 Express with Tools“ installieren. Falls Sie nicht unbedingt 64-Bit-Unterstützung benötigen, können Sie die 32-Bit-Version verwenden, für die bei der Ausführung weniger Arbeitsspeicher verbraucht wird.

2. Führen Sie den SQL Server 2014-Konfigurations-Manager aus.

  a. Erweitern Sie links im Strukturmenü den Knoten **SQL Server-Netzwerkkonfiguration**. b. Klicken Sie auf **Protokolle für SQLEXPRESS**. c. Klicken Sie mit der rechten Maustaste auf **TCP/IP**, und wählen Sie **Aktivieren**. Klicken Sie im Popupdialogfenster auf **OK**. d. Klicken Sie mit der rechten Maustaste auf **TCP/IP**, und wählen Sie **Eigenschaften**. e. Klicken Sie auf die Registerkarte **IP-Adressen**. f. Suchen Sie nach dem Knoten **IPAll**. Geben Sie im Feld **TCP-Port** die Zahl **1433** ein.

  ![SQL Express für TCP/IP konfigurieren][3]

  g. Klicken Sie auf **OK**. Klicken Sie im Popupdialogfenster auf **OK**. h. Klicken Sie im linken Strukturmenü auf **SQL Server-Dienste**. i. Klicken Sie mit der rechten Maustaste auf **SQL Server (SQLEXPRESS)**, und wählen Sie **Neu starten** j. Schließen Sie den SQL Server 2014-Konfigurations-Manager.

3. Ausführen von SQL Server 2014 Management Studio und Herstellen einer Verbindung mit Ihrer lokalen SQL Express-Instanz

  a. Klicken Sie mit der rechten Maustaste im Objekt-Explorer auf Ihre Instanz, und wählen Sie **Eigenschaften** b. Wählen Sie die Seite **Sicherheit**. c. Stellen Sie sicher, dass der **SQL Server- und Windows-Authentifizierungsmodus** ausgewählt ist. d. Klicken Sie auf **OK**

  ![SQL Express-Authentifizierung konfigurieren][4]

  e. Erweitern Sie im Objekt-Explorer die Option **Sicherheit** > **Anmeldungen**. f. Klicken Sie mit der rechten Maustaste auf **Anmeldungen**, und wählen Sie **Neue Anmeldung...**. g. Geben Sie einen Anmeldenamen ein. Wählen Sie **SQL Server-Authentifizierung**. Geben Sie ein Kennwort ein, und geben Sie das gleiche Kennwort dann noch einmal unter **Kennwort bestätigen** ein. Beachten Sie, dass das Kennwort die unter Windows erforderliche Kennwortkomplexität aufweisen muss. h. Klicken Sie auf **OK**.

  ![Neuen Benutzer zu SQL Express hinzufügen][5]

  i. Klicken Sie mit der rechten Maustaste auf die neue Anmeldung, und wählen Sie **Eigenschaften**. j. Wählen Sie die Seite **Serverrollen**. k. Aktivieren Sie das Kontrollkästchen neben der Serverrolle **dbcreator**. l. Klicken Sie auf **OK**. m. Schließen Sie SQL Server 2015 Management Studio.

Notieren Sie sich den gewählten Benutzernamen und das gewählte Kennwort. Unter Umständen müssen Sie je nach Ihren speziellen Datenbankanforderungen weitere Serverrollen oder Berechtigungen zuweisen.

Die Node-Anwendung liest die **SQLCONNSTR\_MS\_TableConnectionString**-Umgebungsvariable, um die Verbindungszeichenfolge für diese Datenbank auszulesen. Sie können dies in Ihrer Umgebung festlegen. Beispielsweise können Sie PowerShell verwenden, um diese Umgebungsvariable festzulegen:

```
$env:SQLCONNSTR_MS_TableConnectionString = "Server=127.0.0.1; Database=mytestdatabase; User Id=azuremobile; Password=T3stPa55word;"
```

Beachten Sie, dass Sie auf die Datenbank über eine TCP/IP-Verbindung zugreifen und einen Benutzernamen und ein Kennwort für die Verbindung angeben müssen.

### <a name="howto-config-localdev"></a>Konfigurieren des Projekts für die lokale Entwicklung

Azure Mobile Apps liest eine JavaScript-Datei mit dem Namen _azureMobile.js_ aus dem lokalen Dateisystem. Sie sollten diese Datei nicht verwenden, um das Azure Mobile Apps-SDK in der Produktion zu konfigurieren. Verwenden Sie stattdessen die App-Einstellungen im [Azure-Portal]. Von der Datei _azureMobile.js_ sollte ein Konfigurationsobjekt exportiert werden. Die am häufigsten verwendeten Einstellungen lauten:

- Datenbankeinstellungen
- Diagnoseprotokollierungseinstellungen
- Alternative CORS-Einstellungen

Hier ist eine Beispieldatei _azureMobile.js_ zum Implementieren der oben beschriebenen Datenbankeinstellungen angegeben:

```
module.exports = {
	cors: {
		origins: [ 'localhost' ]
	},
	data: {
		provider: 'sql',
		server: '127.0.0.1',
		database: 'mytestdatabase',
		user: 'azuremobile',
		password: 'T3stPa55word'
	},
	logging: {
		level: 'verbose'
	}
};
```

Wir empfehlen Ihnen, _azureMobile.js_ Ihrer _.gitignore_-Datei hinzuzufügen (oder eine andere Datei zum Ignorieren der Quellcodeverwaltung), um zu verhindern, dass Kennwörter in der Cloud gespeichert werden. Konfigurieren Sie die Produktionseinstellungen in App-Einstellungen immer im [Azure-Portal].

### <a name="howto-use-sqlazure"></a>Verwenden von SQL Azure als Datenspeicher für die Produktion

<!-- ALTERNATE INCLUDE - we can't use ../includes/app-service-mobile-dotnet-backend-create-new-service.md - slightly different semantics -->

Das Verwenden von SQL Azure als Datenspeicher ist über alle Azure App Service-Anwendungstypen hinweg identisch. Führen Sie diese Schritte zum Erstellen eines neuen mobilen App-Back-Ends aus, falls Sie dies noch nicht getan haben.

1. Melden Sie sich beim [Azure-Portal] an.

2. Klicken Sie oben links im Fenster auf **+NEU** > **Web und mobil** > **Mobile App**, und geben Sie dann einen Namen für Ihr mobiles App-Back-End an.

3. Geben Sie im Feld **Ressourcengruppe** den gleichen Namen wie für Ihre App ein.

4. Es wird der App Service-Tarif "Standard" ausgewählt. Wenn Sie Ihren App Service-Tarif ändern möchten, klicken Sie dazu auf App Services-Plan > **+ Neu erstellen**. Geben Sie einen Namen für den neuen App Service-Tarif ein, und wählen Sie einen geeigneten Speicherort. Klicken Sie auf den Tarif, und wählen Sie einen geeigneten Tarif für den Dienst. Wählen Sie **Alle anzeigen** aus, um mehr Tarifoptionen anzuzeigen, z. B. **Free** und **Shared**. Nachdem Sie den Tarif ausgewählt haben, klicken Sie auf die Schaltfläche **Auswählen**. Klicken Sie wieder auf dem Blatt **App Service-Plan** auf **OK**.

5. Klicken Sie auf **Erstellen**. Damit erstellen Sie ein mobiles App-Back-End, auf dem Sie später das Serverprojekt bereitstellen. Das Bereitstellen eines mobilen App-Back-Ends kann einige Minuten in Anspruch nehmen. Nachdem das mobile App-Back-End bereitgestellt wurde, wird im Portal das Blatt **Einstellungen** für das mobile App-Back-End geöffnet.

Nach der Erstellung des mobilen App-Back-Ends können Sie wählen, ob Sie entweder für eine vorhandene SQL Azure-Datenbank eine Verbindung mit Ihrem mobilen App-Back-End herstellen oder eine neue SQL Azure-Datenbank erstellen. Bei dieser Vorgehensweise erstellen wir eine neue SQL-Datenbank.

    > [AZURE.NOTE] If you already have a database in the same location as the new mobile app backend, you can instead choose **Use an existing database** and then select that database. The use of a database in a different location is not recommended because of additional bandwidth costs and higher latencies.

6. Klicken Sie im neuen mobilen App-Back-End auf **Einstellungen** > **Mobile App** > **Daten** > **+Hinzufügen**.

7. Klicken Sie auf dem Blatt **Add data connection** auf **SQL-Datenbank – Erforderliche Einstellungen konfigurieren** > **Neue Datenbank erstellen**. Geben Sie den Namen der neuen Datenbank in das Feld **Name** ein.

8. Klicken Sie auf **Server**. Geben Sie auf dem Blatt **Neuer Server** in **Servername** einen eindeutigen Namen ein, und legen Sie einen geeigneten **Server admin login** mit einem **Kennwort** an. Stellen Sie sicher, dass **Allow azure services to access server** aktiviert ist. Klicken Sie auf **OK**.

	![SQL Azure-Datenbank erstellen][6]

9. Klicken Sie auf dem Blatt **Neue Datenbank** auf **OK**.

10. Wählen Sie auf dem Blatt **Add data connection** die **Verbindungszeichenfolge** aus, und geben Sie den Anmeldenamen und das Kennwort ein, das Sie gerade beim Erstellen der Datenbank angegeben haben. Wenn Sie eine vorhandene Datenbank verwenden, geben Sie die Anmeldeinformationen für diese Datenbank an. Klicken Sie nach der Eingabe auf **OK**.

11. Klicken Sie auf dem Blatt **Add data connection** auf **OK**, um die Datenbank zu erstellen.

<!-- END OF ALTERNATE INCLUDE -->

Das Erstellen der Datenbank kann einige Minuten dauern. Im Bereich **Benachrichtigungen** können Sie den Fortschritt der Bereitstellung überwachen. Fahren Sie erst fort, wenn die Datenbank erfolgreich bereitgestellt wurde. Nach der erfolgreichen Bereitstellung wird für die SQL Azure-Datenbankinstanz in den Einstellungen des mobilen App-Back-Ends eine Verbindungszeichenfolge erstellt. Sie können diese App-Einstellungen unter **Einstellungen** > **Anwendungseinstellungen** > **Verbindungszeichenfolgen** anzeigen.

### <a name="howto-tables-auth"></a>Erzwingen der Authentifizierung für den Zugriff auf Tabellen

Wenn Sie die App Service-Authentifizierung mit dem tables-Endpunkt verwenden möchten, müssen Sie die App Service-Authentifizierung hierfür zuerst im [Azure-Portal] konfigurieren. Weitere Informationen zum Konfigurieren der Authentifizierung in einem Azure App Service finden Sie im Konfigurationshandbuch für den Identitätsanbieter, den Sie verwenden möchten:

- [So wird‘s gemacht: Konfigurieren der Azure Active Directory-Authentifizierung]
- [So wird‘s gemacht: Konfigurieren der Facebook-Authentifizierung]
- [So wird‘s gemacht: Konfigurieren der Google-Authentifizierung]
- [So wird‘s gemacht: Konfigurieren der Microsoft-Authentifizierung]
- [So wird‘s gemacht: Konfigurieren der Twitter-Authentifizierung]

Jede Tabelle verfügt über eine access-Eigenschaft, die zum Steuern des Zugriffs auf die Tabelle verwendet werden kann. Das folgende Beispiel zeigt eine statisch definierte Tabelle mit erforderlicher Authentifizierung.

```
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table
table.columns = {
	"text": "string",
	"complete": "boolean"
};

// Turn off dynamic schema
table.dynamicSchema = false;

// Require authentication to access the table
table.access = 'authenticated';

module.exports = table;
```

Die access-Eigenschaft kann zwei Werte haben:

  - *authenticated* gibt an, dass die Clientanwendung zusammen mit der Anforderung ein gültiges Authentifizierungstoken senden muss.
  - *disabled* gibt an, dass die Tabelle derzeit deaktiviert ist.

Wenn die access-Eigenschaft nicht definiert ist, ist der Zugriff ohne Authentifizierung zulässig.

### <a name="howto-tables-disabled"></a>Deaktivieren des Zugriffs auf bestimmte Tabellenvorgänge

Die access-Eigenschaft kann nicht nur in der Tabelle angezeigt werden, sondern sie kann auch verwendet werden, um einzelne Vorgänge zu steuern. Es gibt vier Vorgänge:

  - *read* ist der RESTful-GET-Vorgang in der Tabelle
  - *insert* ist der RESTful-POST-Vorgang in der Tabelle
  - *update* ist der RESTful-PATCH-Vorgang in der Tabelle
  - *delete* ist der RESTful-DELETE-Vorgang in der Tabelle

Es kann beispielsweise sein, dass Sie eine schreibgeschützte nicht authentifizierte Tabelle bereitstellen möchten. Dies ist mit der folgenden Tabellendefinition möglich:

```
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Read-Only table - only allow READ operations
table.read.access = undefined;
table.insert.access = 'disabled';
table.update.access = 'disabled';
table.delete.access = 'disabled';

module.exports = table;
```

### <a name="howto-tables-query"></a>Anpassen der Abfrage für Tabellenvorgänge

Eine häufige Anforderung an Tabellenvorgänge ist das Bereitstellen einer eingeschränkten Anzeige von Daten. Beispielsweise können Sie eine Tabelle bereitstellen, die mit der authentifizierten Benutzer-ID gekennzeichnet ist, damit der Benutzer nur seine eigenen Datensätze lesen oder aktualisieren kann. Mit der folgenden Tabellendefinition werden diese Funktionen bereitgestellt:

```
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define a static schema for the table
table.columns = {
	"userId": "string",
	"text": "string",
	"complete": "boolean"
};
table.dynamicSchema = false;

// Require authentication for this table
table.access = 'authenticated';

// Ensure that only records for the authenticated user are retrieved
table.read(function (context) {
	context.query.where({ userId: context.user.id });
	return context.execute();
});

// When adding records, add or overwrite the userId with the authenticated user
table.insert(function (context) {
	context.item.userId = context.user.id;
	return context.execute();
}

module.exports = table;
```

Vorgänge, bei denen normalerweise eine Abfrage ausgeführt wird, verfügen über eine Abfrageeigenschaft, die Sie mit einer Where-Klausel anpassen können. Die Abfrageeigenschaft ist ein [QueryJS]-Objekt, das zum Konvertieren einer OData-Abfrage in ein Element verwendet wird, das vom Daten-Back-End verarbeitet werden kann. Für einfache Gleichheit (wie im obigen Fall) kann eine Zuordnung verwendet werden. Es ist auch relativ einfach, bestimmte SQL-Klauseln hinzuzufügen:

```
context.query.where('myfield eq ?', 'value');
```

### <a name="howto-tables-softdelete"></a>Konfigurieren des vorläufigen Löschens in einer Tabelle

Beim vorläufigen Löschen werden Datensätze nicht tatsächlich gelöscht. Stattdessen werden sie in der Datenbank als gelöscht markiert, indem die Spalte „deleted“ auf „true“ festgelegt wird. Das Azure Mobile Apps-SDK entfernt vorläufig gelöschte Datensätze automatisch aus Ergebnissen, es sei denn, im Mobile Client-SDK wird „IncludeDeleted()“ verwendet. Legen Sie in der Tabellendefinitionsdatei die softDelete-Eigenschaft fest, um eine Tabelle für das vorläufige Löschen zu konfigurieren. Beispiel:

```
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table
table.columns = {
	"text": "string",
	"complete": "boolean"
};

// Turn off dynamic schema
table.dynamicSchema = false;

// Turn on Soft Delete
table.softDelete = true;

// Require authentication to access the table
table.access = 'authenticated';

module.exports = table;
```

Sie müssen ein Verfahren zum Bereinigen von Datensätzen einrichten, und zwar entweder über eine Clientanwendung, einen WebJob oder einen benutzerdefinierten Mechanismus.

### <a name="howto-tables-seeding"></a>Durchführen des Seedings für Ihre Datenbank mit Daten

Beim Erstellen einer neuen Anwendung kann es sein, dass Sie für eine Tabelle das Seeding mit Daten durchführen möchten. Sie können dies in der JavaScript-Datei mit der Tabellendefinition wie folgt durchführen:

```
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table
table.columns = {
	"text": "string",
	"complete": "boolean"
};
table.seed = [
	{ text: 'Example 1', complete: false },
	{ text: 'Example 2', complete: true }
];

// Turn off dynamic schema
table.dynamicSchema = false;

// Require authentication to access the table
table.access = 'authenticated';

module.exports = table;
```

Es ist wichtig zu beachten, dass das Seeding von Daten nur durchgeführt wird, wenn die Tabelle mit dem Azure Mobile Apps-SDK erstellt wird. Falls die Tabelle in der Datenbank bereits vorhanden ist, werden keine Daten in die Tabelle eingefügt. Wenn das dynamische Schema aktiviert ist, wird das Schema aus den Seeding-Daten abgeleitet.

Wir empfehlen, die initialize()-Methode explizit aufzurufen, um die Tabelle zu erstellen, wenn die Ausführung des Diensts beginnt.

## <a name="CustomAPI"></a>Benutzerdefinierte API

Zusätzlich zur Datenzugriff-API über den /tables-Endpunkt können Azure Mobile Apps auch eine benutzerdefinierte API-Abdeckung bereitstellen. Benutzerdefinierte APIs werden auf ähnliche Weise wie Tabellendefinitionen definiert und können auf die gleichen Funktionen zugreifen, einschließlich der Authentifizierung.

Wenn Sie die App Service-Authentifizierung mit einer benutzerdefinierten API verwenden möchten, müssen Sie die App Service-Authentifizierung hierfür zuerst im [Azure-Portal] konfigurieren. Weitere Informationen zum Konfigurieren der Authentifizierung in einem Azure App Service finden Sie im Konfigurationshandbuch für den Identitätsanbieter, den Sie verwenden möchten:

- [So wird‘s gemacht: Konfigurieren der Azure Active Directory-Authentifizierung]
- [So wird‘s gemacht: Konfigurieren der Facebook-Authentifizierung]
- [So wird‘s gemacht: Konfigurieren der Google-Authentifizierung]
- [So wird‘s gemacht: Konfigurieren der Microsoft-Authentifizierung]
- [So wird‘s gemacht: Konfigurieren der Twitter-Authentifizierung]

### <a name="howto-customapi-basic"></a>Definieren einer einfachen benutzerdefinierten API

Benutzerdefinierte APIs werden auf ähnliche Weise wie die Tabellen-API definiert.

1. Erstellen des Verzeichnisses **api**
2. Erstellen Sie eine JavaScript-Datei mit einer API-Definition im Verzeichnis **api**.
3. Verwenden Sie die import-Methode, um das Verzeichnis **api** zu importieren.

Hier ist die API-Prototypdefinition angegeben, die auf dem bereits verwendeten basic-app-Beispiel basiert.

```
var express = require('express'),
	azureMobileApps = require('azure-mobile-apps');

var app = express(),
	mobile = azureMobileApps();

// Import the Custom API
mobile.api.import('./api');

// Add the mobile API so it is accessible as a Web API
app.use(mobile);

// Start listening on HTTP
app.listen(process.env.PORT || 3000);
```

Wir verwenden eine einfache API, bei der das Serverdatum mit der _Date.now()_-Methode zurückgegeben wird. Dies ist die Datei „api/date.js“:

```
var api = {
	get: function (req, res, next) {
		var date = { currentTime: Date.now() };
		res.status(200).type('application/json').send(date);
	});
};

module.exports = api;
```

Jeder Parameter entspricht einem der standardmäßigen RESTful-Verben: GET, POST, PATCH oder DELETE. Bei der Methode handelt es sich um eine [ExpressJS Middleware]-Standardfunktion, mit der die erforderliche Ausgabe gesendet wird.

### <a name="howto-customapi-auth"></a>Erzwingen der Authentifizierung für den Zugriff auf eine benutzerdefinierte API

Das Azure Mobile Apps-SDK implementiert die Authentifizierung sowohl für den tables-Endpunkt als auch für benutzerdefinierte APIs auf die gleiche Weise. Fügen Sie eine **access**-Eigenschaft hinzu, um die Authentifizierung für die im vorherigen Abschnitt entwickelte API hinzuzufügen:

```
var api = {
	get: function (req, res, next) {
		var date = { currentTime: Date.now() };
		res.status(200).type('application/json').send(date);
	});
};
// All methods must be authenticated.
api.access = 'authenticated';

module.exports = api;
```

Sie können die Authentifizierung auch für bestimmte Vorgänge angeben:

```
var api = {
	get: function (req, res, next) {
		var date = { currentTime: Date.now() };
		res.status(200).type('application/json').send(date);
	});
};
// The GET methods must be authenticated.
api.get.access = 'authenticated';

module.exports = api;
```

Dasselbe Token, das für den tables-Endpunkt verwendet wird, muss für benutzerdefinierte APIs verwendet werden, die eine Authentifizierung benötigen.

## <a name="Debugging"></a>Debuggen und Problembehandlung

Der Azure App Service stellt mehrere Debug- und Problembehandlungsverfahren für Node-Anwendungen bereit. All diese Verfahren sind verfügbar.

- [Überwachen eines Azure App Service]
- [Aktivieren der Diagnoseprotokollierung in Azure App Service]
- [Problembehandlung für einen Azure App Service in Visual Studio]

### <a name="howto-diagnostic-logs"></a>Schreiben in die Diagnoseprotokolle von Azure Mobile Apps

Node-Anwendungen haben Zugriff auf einen großen Bereich von Diagnoseprotokoll-Tools. Intern nutzt das Azure Mobile Apps-Node-SDK [Winston] für die Diagnoseprotokollierung. Diese Funktion wird automatisch aktiviert, indem der Debugmodus aktiviert wird oder die App-Einstellung **MS\_DebugMode** im [Azure-Portal] auf „true“ festgelegt wird. Generierte Protokolle werden im Diagnoseprotokoll auf dem [Azure-Portal] angezeigt.

<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png
[1]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-new-project.png
[2]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-install-npm.png
[3]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-config.png
[4]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-authconfig.png
[5]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-newuser-1.png
[6]: ../../includes/media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png

<!-- URLs -->
[iOS-Client-Schnellstart]: app-service-mobile-ios-get-started.md
[Xamarin.iOS-Client-Schnellstart]: app-service-mobile-xamarin-ios-get-started.md
[Xamarin.Android-Client-Schnellstart]: app-service-mobile-xamarin-android-get-started.md
[Xamarin.Forms-Client-Schnellstart]: app-service-mobile-xamarin-forms-get-started.md
[Windows Phone-Client-Schnellstart]: app-service-mobile-windows-store-dotnet-get-started.md
[HTML/JavaScript-Client-Schnellstart]: app-service-html-get-started.md
[Synchronisierung von Offline-Daten]: app-service-mobile-offline-data-sync.md
[So wird‘s gemacht: Konfigurieren der Azure Active Directory-Authentifizierung]: app-service-mobile-how-to-configure-active-directory-authentication.md
[So wird‘s gemacht: Konfigurieren der Facebook-Authentifizierung]: app-service-mobile-how-to-configure-facebook-authentication.md
[So wird‘s gemacht: Konfigurieren der Google-Authentifizierung]: app-service-mobile-how-to-configure-google-authentication.md
[So wird‘s gemacht: Konfigurieren der Microsoft-Authentifizierung]: app-service-mobile-how-to-configure-microsoft-authentication.md
[So wird‘s gemacht: Konfigurieren der Twitter-Authentifizierung]: app-service-mobile-how-to-configure-twitter-authentication.md
[Azure App Service-Bereitstellungshandbuch]: ../app-service-web/web-site-deploy.md
[Überwachen eines Azure App Service]: ../app-service-web/web-sites-monitor.md
[Aktivieren der Diagnoseprotokollierung in Azure App Service]: ../app-service-web/web-sites-enable-diagnostic-log.md
[Problembehandlung für einen Azure App Service in Visual Studio]: ../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md
[Angeben der Node-Version]: ../nodejs-specify-node-version-azure-apps.md
[Verwenden von Node-Modulen]: ../nodejs-use-node-mobiles-azure-apps.md
[Create a new Azure App Service]: ../app-service-web/

[Azure-Portal]: https://portal.azure.com/
[OData]: http://www.odata.org
[Zusage]: https://developer.mozilla.org/de-DE/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp-Beispiel bei GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[todo-Beispiel bei GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[Verzeichnis mit den Beispielen bei GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js-Tools 1.1 für Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql-Node-Paket]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/de-DE/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS Middleware]: http://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston

<!---HONumber=Nov15_HO4-->