<properties
	pageTitle="Azure AD Cordova – Erste Schritte | Microsoft Azure"
	description="In diesem Thema erfahren Sie, wie eine Cordova-Anwendung erstellt wird, die sich für die Anmeldung in Azure AD integriert und über OAuth durch Azure AD geschützte APIs aufruft."
	services="active-directory"
	documentationCenter=""
	authors="vibronet"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="04/28/2015"
	ms.author="vittorib"/>

# Integrieren von Azure AD in eine Apache Cordova-App

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Mit Apache Cordova können Sie HTML5/JavaScript-Anwendungen entwickeln, die auf mobilen Geräten als vollständige systemeigene Anwendungen ausgeführt werden können. Mit Azure AD können Sie Ihren Cordova-Anwendungen Authentifizierungsfunktionen hinzufügen, die für Unternehmen geeignet sind. Dank eines Cordova-Plug-Ins, mit dem die systemeigenen SDKs von Azure AD unter iOS, Android, Windows Store und Windows Phone abgedeckt werden, können Sie Ihre Anwendung erweitern. So ist es möglich, dass die Anmeldung über die AD-Konten Ihrer Benutzer unterstützt wird, der Zugriff auf die Office 365- und Azure-API besteht und sogar Aufrufe Ihrer eigenen benutzerdefinierten Web-API geschützt werden.

In diesem Lernprogramm wird das Apache Cordova-Plug-In für die Active Directory-Authentifizierungsbibliothek (ADAL) verwendet, um eine einfache App mit den folgenden Features zu verbessern:

-	Mit wenigen Codezeilen können Sie einen AD-Benutzer authentifizieren und ein Token für das Aufrufen der Azure AD Graph-API abrufen.
-	Verwenden Sie dieses Token zum Aufrufen der Graph-API, um das Verzeichnis abzufragen und die Ergebnisse anzuzeigen.  
-	Nutzen Sie den ADAL-Tokencache zum Minimieren der Authentifizierungsaufforderungen für den Benutzer.

Dazu müssen Sie folgende Schritte ausführen:

2. Registrieren einer Anwendung unter Azure AD
2. Hinzufügen von Code zum Anfordern von Token zu Ihrer App
3. Hinzufügen von Code, um das Token zum Abfragen der Graph-API und zum Anzeigen von Ergebnissen zu verwenden
4. Erstellen des Cordova-Bereitstellungsprojekts mit allen abzudeckenden Plattformen und Verwenden des Cordova ADAL-Plug-Ins sowie Testen der Lösung in Emulatoren

## *0. Voraussetzungen*

Zum Durchführen dieses Lernprogramms benötigen Sie Folgendes:

- Einen Azure AD-Mandanten, unter dem Sie über ein Konto mit Rechten für die App-Entwicklung verfügen
- Eine Entwicklungsumgebung, die für die Verwendung von Apache Cordova konfiguriert ist  

Falls Sie beides bereits eingerichtet haben, können Sie direkt mit Schritt 1 fortfahren.

Falls Sie noch nicht über einen Azure AD-Mandanten verfügen, finden Sie hier eine [entsprechende Anleitung](active-directory-howto-tenant.md).

Installieren Sie Folgendes, wenn Sie Apache Cordova noch nicht auf Ihrem Computer eingerichtet haben:

- [Git](http://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
- [NodeJS](https://nodejs.org/download/)
- [Cordova-Befehlszeilenschnittstelle](https://cordova.apache.org/) (einfache Installation per NPM-Paket-Manager: `npm install -g cordova`)

Beachten Sie, dass dies sowohl für den PC als auch für den Macintosh funktionieren sollte.

Für jede Zielplattform gelten unterschiedliche Voraussetzungen.

- Erstellen und Ausführen einer Version für Windows-Tablet/PC oder -Smartphone
	- [Visual Studio 2013 für Windows mit Update 2 oder höher](http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-windows-8) (Express oder andere Version)
- Erstellen und Ausführen für iOS
	-   Xcode 5.x oder höher: Download unter http://developer.apple.com/downloads oder im [Mac App Store](http://itunes.apple.com/us/app/xcode/id497799835?mt=12)
	-   [ios-sim](https://www.npmjs.org/package/ios-sim): Ermöglicht Ihnen das Starten von iOS-Apps im iOS-Simulator über die Befehlszeile (einfache Installation über das Terminal: `npm install -g ios-sim`).

- Erstellen und Ausführen der Anwendung für Android
	- Installieren Sie das [Java Development Kit (JDK) 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) oder höher. Achten Sie darauf, dass `JAVA_HOME` (Umgebungsvariable) gemäß JDK-Installationspfad richtig festgelegt ist (z. B. C:\\Programme\\Java\\jdk1.7.0_75).
	- Installieren Sie das [Android SDK](http://developer.android.com/sdk/installing/index.html?pkg=tools), und fügen Sie den Speicherort `<android-sdk-location>\tools` (z. B. C:\\tools\\Android\\android-sdk\\tools) Ihrer Umgebungsvariable `PATH` hinzu.
	- Öffnen Sie den Android SDK Manager (z. B. über das Terminal: `android`), und installieren Sie Folgendes:
    - *Android 5.0.1 (API 21)*-Plattform-SDK
    - *Android SDK Build-Tools* Version 19.1.0 oder höher
    - *Android Support Repository* (Extras)

  Für das Android SDK wird keine standardmäßige Emulatorinstanz bereitgestellt. Erstellen Sie eine neue Instanz, indem Sie `android avd` über das Terminal ausführen und dann *Erstellen...* auswählen, wenn Sie die Android-App in einem Emulator ausführen möchten. Die empfohlene *API-Ebene* lautet 19 oder höher. Weitere Informationen zum Android-Emulator und zu den Optionen für die Erstellung finden Sie unter [AVD Manager](http://developer.android.com/tools/help/avd-manager.html).


## *1. Registrieren einer Anwendung unter Azure AD*

Hinweis: Dieser Schritt ist optional. Im Lernprogramm sind vorab bereitgestellte Werte vorhanden, mit denen Sie das Beispiel in Aktion sehen können, ohne dass Sie selbst Werte in Ihrem eigenen Mandanten bereitstellen müssen. Es ist jedoch ratsam, diesen Schritt auszuführen und sich mit dem Prozess vertraut zu machen, da sie ihn benötigen, wenn Sie Ihre eigenen Anwendungen erstellen.

Azure AD stellt nur Token für bekannte Anwendungen aus. Bevor Sie Azure AD über Ihre App nutzen können, müssen Sie dafür einen Eintrag in Ihrem Mandanten erstellen. Gehen Sie wie folgt vor, um in Ihrem Mandanten eine neue Anwendung zu registrieren:

- Melden Sie sich am Azure-Verwaltungsportal an.
- Klicken Sie in der linken Navigationsleiste auf „Active Directory“.
- Wählen Sie den Mandanten aus, unter dem die Beispielanwendung registriert werden soll.
- Klicken Sie auf die Registerkarte „Anwendungen“ und dann unten auf „Hinzufügen“.
- Folgen Sie den Bildschirmaufforderungen, und erstellen Sie eine neue „Systemeigene Clientanwendung“.
    - Am Namen der Anwendung sollten die Endbenutzer die Funktion der Anwendung ablesen können.
    -	Der „Umleitungs-URI“ ist der URI, der zum Zurückgeben von Token an Ihre App verwendet wird. Geben Sie `http://MyDirectorySearcherApp` ein.

Nach Abschluss der Registrierung weist AAD Ihrer App eine eindeutige Client-ID zu. Sie benötigen diesen Wert in den nächsten Abschnitten: Er befindet sich auf der Registerkarte „Konfigurieren“ der neu erstellten App.

## *2. Klonen der erforderlichen Repositorys für das Lernprogramm*

Geben Sie in der Shell oder über die Befehlszeile den folgenden Befehl ein:

    git clone https://github.com/AzureAD/azure-activedirectory-library-for-cordova.git
    git clone -b skeleton https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova.git

## *3. Erstellen der Cordova-App*

Es gibt mehrere Wege, Cordova-Anwendungen zu erstellen. In diesem Lernprogramm verwenden wir die Cordova-Befehlszeilenschnittstelle (CLI). Geben Sie in der Shell oder über die Befehlszeile den folgenden Befehl ein:


     cordova create DirSearchClient --copy-from="NativeClient-MultiTarget-Cordova/DirSearchClient"

Damit werden die Ordnerstruktur und das Gerüst für das Cordova-Projekt erstellt, und der Inhalt des Startprojekts wird in den Unterordner „www“ kopiert. Wechseln Sie in den Ordner „DirSearchClient“.

    cd .\DirSearchClient

Fügen Sie das Plug-In für die Positivliste hinzu, das zum Aufrufen der Graph-API benötigt wird.

     cordova plugin add https://github.com/apache/cordova-plugin-whitelist.git

Fügen Sie als Nächstes alle Plattformen hinzu, die Sie unterstützen möchten. Um ein funktionstüchtiges Beispiel zu erhalten, müssen Sie mindestens einen der unten aufgeführten Befehle ausführen. Beachten Sie, dass Sie iOS nicht unter Windows emulieren und Windows/Windows Phone nicht auf einem Macintosh emulieren können.

    cordova platform add android@97718a0a25ec50fedf7b023ae63bfcffbcfafb4b
    cordova platform add ios
    cordova platform add windows

Abschließend können Sie Ihrem Projekt die ADAL für das Cordova-Plug-In hinzufügen.

    cordova plugin add ../azure-activedirectory-library-for-cordova

## *3. Hinzufügen von Code zum Authentifizieren von Benutzern und Abrufen von Token aus AAD*

Mit der Anwendung, die Sie in diesem Lernprogramm entwickeln, wird eine einfache Verzeichnissuchfunktion bereitgestellt. Endbenutzer können den Alias eines beliebigen Benutzers im Verzeichnis eingeben und einige grundlegende Attribute visualisieren. Das Startprojekt enthält die Definition der grundlegenden Benutzeroberfläche der App (in „www/index.html“) und das Gerüst, das aus einfachen App-Ereigniszyklen, Benutzeroberflächenbindungen und der Anzeigelogik für Ergebnisse (in „www/js/index.js“) besteht. Die einzige Aufgabe, die für Sie übrig bleibt, ist das Hinzufügen der Logik zum Implementieren von Identitätsaufgaben.

Als Erstes müssen Sie in Ihren Code die Protokollwerte, die von AAD zum Identifizieren Ihrer App verwendet werden, und die gewünschten Ressourcen einfügen. Diese Werte werden später zum Erstellen der Tokenanforderungen verwendet. Fügen Sie den unten angegebenen Codeausschnitt ganz oben in die Datei „index.js“ ein.

```javascript
    var authority = "https://login.windows.net/common",
    redirectUri = "http://MyDirectorySearcherApp",
    resourceUri = "https://graph.windows.net",
    clientId = "a5d92493-ae5a-4a9f-bcbf-9f1d354067d3",
    graphApiVersion = "2013-11-08";
```

Die Werte `redirectUri` und `clientId` sollten mit den Werten übereinstimmen, mit denen Ihre App in AAD beschrieben wird. Sie finden die Werte im Azure-Portal auf der Registerkarte „Konfigurieren“. Dies ist weiter oben in diesem Lernprogramm unter Schritt 1 beschrieben. Hinweis: Wenn Sie sich gegen die Registrierung einer neuen App in Ihrem eigenen Mandanten entschieden haben, können Sie die oben angegebenen vorkonfigurierten Werte unverändert einfügen. Sie können das Beispiel dann ausführen, aber Sie sollten für Apps, die für die Produktion bestimmt sind, immer einen eigenen Eintrag erstellen.

Als Nächstes muss der Code für die eigentliche Tokenanforderung hinzugefügt werden. Fügen Sie den folgenden Codeausschnitt zwischen den Definitionen `search ` und `renderdata ` ein.

```javascript
    // Shows user authentication dialog if required.
    authenticate: function (authCompletedCallback) {

        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
            // Attempt to authorize user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user cridentials so triggers authentication dialog
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
        });

    },
```
Lassen Sie uns diese Funktion untersuchen, indem wir sie in ihre zwei Hauptkomponenten unterteilen. Dieses Beispiel wurde so konzipiert, dass es für einen beliebigen Mandanten funktioniert und nicht nur mit einem bestimmten Mandanten verknüpft ist. Dabei wird der Endpunkt „/common“ verwendet. Benutzer können damit bei der Authentifizierung ein beliebiges Konto eingeben. Die Anforderung wird dann an den entsprechenden Mandanten weitergeleitet. Mit diesem ersten Teil der Methode wird der ADAL-Cache untersucht, um zu ermitteln, ob bereits ein gespeichertes Token vorhanden ist. Wenn ja, werden die Mandanten, von denen es stammt, für die ADAL-Neuinitialisierung verwendet. Dies ist erforderlich, um zusätzliche Aufforderungen zu vermeiden. Bei Verwendung von „/common“ werden Benutzer nämlich immer aufgefordert, ein neues Konto einzugeben. ```javascript
        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
``` Mit dem zweiten Teil der Methode wird die eigentliche Tokenanforderung durchgeführt. Mit der `acquireTokenSilentAsync`-Methode wird die ADAL aufgefordert, ein Token für die angegebene Ressource zurückzugeben, ohne eine Benutzeroberfläche anzuzeigen. Dies ist möglich, wenn im Cache bereits ein geeignetes Zugriffstoken gespeichert ist oder wenn ein Aktualisierungstoken vorhanden ist, mit dem ein neues Zugriffstoken abgerufen werden kann, ohne dass eine Aufforderung angezeigt wird. Wenn dieser Versuch nicht erfolgreich ist, wird auf `acquireTokenAsync` zurückgegriffen. Der Benutzer wird dann visuell zum Authentifizieren aufgefordert. ```javascript
            // Attempt to authorize user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user cridentials so triggers authentication dialog
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
``` Da wir jetzt über das Token verfügen, können wir die Graph-API aufrufen und die gewünschte Suchabfrage durchführen. Fügen Sie den folgenden Codeausschnitt direkt unterhalb der Definition von `authenticate` ein.

```javascript
// Makes Api call to receive user list.
    requestData: function (authResult, searchText) {
        var req = new XMLHttpRequest();
        var url = resourceUri + "/" + authResult.tenantId + "/users?api-version=" + graphApiVersion;
        url = searchText ? url + "&$filter=mailNickname eq '" + searchText + "'" : url + "&$top=10";

        req.open("GET", url, true);
        req.setRequestHeader('Authorization', 'Bearer ' + authResult.accessToken);

        req.onload = function(e) {
            if (e.target.status >= 200 && e.target.status < 300) {
                app.renderData(JSON.parse(e.target.response));
                return;
            }
            app.error('Data request failed: ' + e.target.response);
        };
        req.onerror = function(e) {
            app.error('Data request failed: ' + e.error);
        }

        req.send();
    },

```
Über die Ausgangspunktdateien wird eine einfache Benutzeroberfläche zum Eingeben eines Benutzeralias in ein Textfeld bereitgestellt. Die Methode verwendet diesen Wert zum Erstellen einer Abfrage, Kombinieren mit dem Zugriffstoken, Senden an den Graphen und Analysieren der Ergebnisse. Mit der renderData-Methode, die bereits in der Ausgangspunktdatei vorhanden ist, werden die Ergebnisse visualisiert.

## *4. Ausführen*
Ihre App ist jetzt für die Ausführung bereit. Die Bedienung ist sehr einfach: Geben Sie nach dem Starten der App im Textfeld den Alias des Benutzers ein, nach dem Sie suchen möchten. Klicken Sie anschließend auf die Schaltfläche. Sie werden zur Authentifizierung aufgefordert. Nach erfolgreicher Authentifizierung und dem erfolgreichen Abschluss der Suche werden die Attribute des gesuchten Benutzers angezeigt. Bei den nachfolgenden Suchen wird die Suche durchgeführt, ohne dass eine Aufforderung angezeigt wird, da das zuvor abgerufene Token bereits im Cache vorhanden ist. Die genauen Schritte zum Ausführen der App variieren je nach Plattform.


##### Erstellen und Ausführen der Windows-Tablet/PC-Anwendungsversion

   `cordova run windows`

   __Hinweis__: Bei der ersten Ausführung werden Sie ggf. aufgefordert, sich für eine Entwicklerlizenz zu registrieren. Weitere Details finden Sie unter [Entwicklerlizenz](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx).


##### Erstellen und Ausführen der Anwendung unter Windows Phone 8.1

   Ausführung auf einem verbundenen Gerät: `cordova run windows --device -- --phone`

   Ausführung in einem Standardemulator: `cordova emulate windows -- --phone`

   Verwenden Sie `cordova run windows --list -- --phone`, um alle verfügbaren Ziele anzuzeigen, und `cordova run windows --target=<target_name> -- --phone`, um die Anwendung auf einem bestimmten Gerät oder Emulator auszuführen (z. B. `cordova run windows --target="Emulator 8.1 720P 4.7 inch" -- --phone`).
##### Erstellen und Ausführen auf einem Android-Gerät

   Ausführung auf einem verbundenen Gerät: `cordova run android --device`

   Ausführung in einem Standardemulator: `cordova emulate android`

   __Hinweis__: Stellen Sie sicher, dass Sie die Emulatorinstanz mit *AVD Manager* erstellt haben. Dies ist im Abschnitt *Voraussetzungen* beschrieben.

   Verwenden Sie `cordova run android --list`, um alle verfügbaren Ziele anzuzeigen, und `cordova run android --target=<target_name>`, um die Anwendung auf einem bestimmten Gerät oder Emulator auszuführen (z. B. `cordova run android --target="Nexus4_emulator"`).

##### Erstellen und Ausführen auf einem iOS-Gerät

   Ausführung auf einem verbundenen Gerät: `cordova run ios --device`

   Ausführung in einem Standardemulator: `cordova emulate ios`

   __Hinweis__: Stellen Sie sicher, dass Sie das `ios-sim`-Paket für die Ausführung in einem Emulator installiert haben. Weitere Details finden Sie im Abschnitt *Voraussetzungen*.

    Use `cordova run ios --list` to see all available targets and `cordova run ios --target=<target_name>` to run application on specific device or emulator (for example,  `cordova run android --target="iPhone-6"`).

Verwenden Sie `cordova run --help`, um weitere Optionen für die Erstellung und Ausführung anzuzeigen.

Als Referenz stellen wir hier das vollständige Beispiel (ohne Ihre Konfigurationswerte) bereit. Sie können jetzt mit anspruchsvolleren (und zugegebenermaßen interessanteren) Szenarien fortfahren. Sie können beispielsweise Folgendes testen:

[Schützen einer Node.js-Web-API mit Azure AD >>](active-directory-devquickstarts-webapi-nodejs.md)

Sehen Sie sich auch die folgenden Ressourcen an: – [AzureAD-Beispiele auf GitHub >>](https://github.com/AzureAdSamples) – [CloudIdentity.com >>](https://cloudidentity.com) – Azure AD-Dokumentation auf [Azure.com >>](http://azure.microsoft.com/documentation/services/active-directory/)
 

<!---HONumber=July15_HO3-->