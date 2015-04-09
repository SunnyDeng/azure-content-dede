<properties 
	pageTitle="Erstellen einer AngularJS-Single-Page-App mit Azure AD" 
	description="Veranschaulicht die Verwendung von Active Directory Authentication Library (ADAL) für JavaScript zum Sichern einer AngularJS-basierten Single-Page-App, die mit einem ASP.NET Web-API-Back-End implementiert ist und eine andere ASP.NET Web-API mit CORS aufruft." 
	services="" 
	documentationCenter="" 
	authors="Justinha" 
	manager="terrylan" 
	editor="LisaToft"/>

<tags 
	ms.service="active-directory" 
	ms.devlang="javascript" 
	ms.topic="hero-article" 
	ms.tgt_pltfrm="na" 
	ms.workload="identity" 
	ms.date="02/20/2015" 
	ms.author="justinha"/>


# Erstellen einer AngularJS-Single-Page-App mit Azure AD 

In diesem Lernprogramm wird die Verwendung von Active Directory Authentication Library (ADAL) für JavaScript zum Sichern einer AngularJS-basierten Single-Page-App veranschaulicht, die mit einem ASP.NET Web-API-Back-End implementiert ist und eine andere ASP.NET Web-API mit CORS aufruft. Das in diesem Lernprogramm verwendete Codebeispiel finden Sie unter [AzureADSamples/SinglePageApp-AngularJS-DotNet](https://github.com/AzureADSamples/SinglePageApp-AngularJS-DotNet) auf Github.

ADAL für JavaScript ist eine Open-Source-Bibliothek.  Möglichkeiten der Verteilung, den Quellcode und Beiträge finden Sie im [ADAL JS-Repository](https://github.com/AzureAD/azure-activedirectory-library-for-js).

Weitere Informationen zur Funktionsweise der Protokolle in diesem Szenario und anderen Szenarien finden Sie unter [Authentifizierungsszenarien für Azure AD](http://go.microsoft.com/fwlink/?LinkId=394414).

## Ausführen dieses Beispiels

Der Anfang ist denkbar einfach.  Zum Ausführen dieses Beispiels benötigen Sie Folgendes:

- Visual Studio 2013
- Eine Internetverbindung
- Ein Azure-Abonnement (eine [kostenlose Testversion](https://account.windowsazure.com/organization) ist ausreichend)

Jedem Azure-Abonnement ist ein Azure AD-Mandant (Azure Active Directory) zugeordnet. Alle in diesem Beispiel verwendeten Azure AD-Features stehen kostenlos zur Verfügung.

## Klonen oder Herunterladen dieses Repositorys

Gehen Sie in der Shell oder Befehlszeile folgendermaßen vor:
`git clone https://github.com/AzureADSamples/SinglePageApp-WebAPI-AngularJS-DotNet.git`

## Registrieren des To Go API-Diensts bei Ihrem Azure Active Directory-Mandanten

1. Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com) an.
2. Klicken Sie in der linken Navigationsleiste auf **Active Directory**.
3. Klicken Sie auf das Verzeichnis, in dem die Beispielanwendung registriert werden soll.
4. Klicken Sie auf die Registerkarte **Anwendungen**.
5. Klicken Sie in der Taskleiste auf **Hinzufügen**.
6. Klicken Sie auf **Eine von meinem Unternehmen entwickelte Anwendung hinzufügen**.
7. Geben Sie einen Anzeigenamen für die Anwendung ein, z. B. "To Go API", wählen Sie **Webanwendung** und/oder **Web-API** aus, und klicken Sie auf **Weiter**.
8. Geben Sie als Anmelde-URL die Basis-URL für das Beispiel ein, standardmäßig `https://localhost:44327/`.
9. Geben Sie als App-ID-URI `https://<your_directory_name>/ToGoAPI` ein, und ersetzen Sie dabei `<your_directory_name>` durch den Namen Ihres Azure AD-Verzeichnisses.  Speichern Sie die Konfiguration.

Fertig!  Bevor Sie mit dem nächsten Schritt fortfahren, müssen Sie den App-ID-URI der API suchen.

1. Klicken Sie im Azure-Portal auf die Registerkarte **Konfigurieren** Ihrer Anwendung.
2. Suchen Sie den App-ID-URI-Wert, und kopieren Sie ihn in die Zwischenablage.

## Konfigurieren der To Go API zur Verwendung des Azure Active Directory-Mandanten

1. Öffnen Sie die Projektmappe in Visual Studio 2013.
2. Öffnen Sie im ToGoAPI-Projekt die Datei  `web.config`.
3. Suchen Sie den App-Schlüssel `ida:Tenant`, und ersetzen Sie den Wert durch den Namen Ihres Azure AD-Mandanten.
4. Suchen Sie den App-Schlüssel `ida:Audience`, und ersetzen Sie den Wert durch den App-ID-URI, den Sie aus dem Azure-Portal kopiert haben.
5. Öffnen Sie im ToGoAPI-Projekt die Datei `Controllers/ToGoListController.cs`.  Geben Sie im `[EnableCors...]`-Attribut die Adresse des To Do SPA-Clients ein.  Standardmäßig ist dies `https://localhost:44326`.  Denken Sie daran, den nachgestellten Schrägstrich auszulassen.
5. Öffnen Sie im TodoSPA-Projekt die Datei  `App/Scripts/App.js`, und suchen Sie die Deklaration des `endpoints`-Objekts.
6. Geben Sie eine Zuordnung der To Go API-Endpunktadresse zum zugehörigen Ressourcenbezeichner bzw. zum App-ID-URI ein.  Der Name der Eigenschaft des `endpoints`-Objekts muss die Adresse der To Go API sein.  Standardmäßig ist dies `https://localhost:44327/`.  Der Wert dieser Eigenschaft muss der App-ID-URI sein, den Sie aus dem Portal kopiert haben, z. B. `https://<your_tenant_name>/ToGoAPI`.
8. Machen Sie sich noch keine Gedanken über die anderen Konfigurationswerte in dieser Datei, wir kommen gleich darauf zurück.
9. Öffnen Sie im ToGoAPI-Projekt die Datei `App/Scripts/toGoListSvc.js`.  Ersetzen Sie den Wert der `apiEndpoint`-Variablen durch die Adresse der To Go API.  Standardmäßig ist dies `https://localhost:44327/`.

## Registrieren der To Go-Single-Page-Anwendung bei Ihrem Azure Active Directory-Mandanten

1. Melden Sie sich erneut beim [Azure-Verwaltungsportal](https://manage.windowsazure.com) an.
2. Klicken Sie in der linken Navigationsleiste auf **Active Directory**.
3. Klicken Sie auf den Verzeichnismandanten, bei dem die Beispielanwendung registriert werden soll.
4. Klicken Sie auf die Registerkarte **Anwendungen**.
5. Klicken Sie in der Taskleiste auf **Hinzufügen**.
6. Klicken Sie auf **Eine von meinem Unternehmen entwickelte Anwendung hinzufügen**.
7. Geben Sie einen Anzeigenamen für die Anwendung ein, z. B. "To Do SPA", wählen Sie **Webanwendung und/oder Web-API** aus, und klicken Sie auf **Weiter**.
8. Geben Sie als Anmelde-URL die Basis-URL für das Beispiel ein, standardmäßig `https://localhost:44326/`.
9. Geben Sie als App-ID-URI `https://<your_directory_name>/ToDoSPA` ein, und ersetzen Sie dabei `<your_directory_name>` durch den Namen Ihres Azure AD-Verzeichnisses.
10. Klicken Sie im Abschnitt **Berechtigungen für andere Anwendungen** auf **Anwendung hinzufügen**.  Wählen Sie **Sonstiges** in der Dropdownliste **Anzeigen** aus, und klicken Sie auf das obere Häkchen.  Suchen Sie die To Go API, klicken Sie darauf, und klicken Sie auf das untere Häkchen, um die Anwendung hinzuzufügen.  Wählen Sie **Auf To Go API zugreifen** in der Dropdownliste **Delegierte Berechtigungen** aus, und speichern Sie die Konfiguration.

Fertig!  Bevor Sie mit dem nächsten Schritt fortfahren, müssen Sie die Client-ID der Anwendung suchen.

1. Klicken Sie im Azure-Portal auf die Registerkarte **Konfigurieren** Ihrer Anwendung.
2. Suchen Sie den Client-ID-Wert, und kopieren Sie ihn in die Zwischenablage.


## Aktivieren der impliziten OAuth2-Gewährung für Ihre Anwendung

Standardmäßig ist für Anwendungen, die in Azure AD bereitgestellt werden, die Verwendung der impliziten OAuth2-Gewährung nicht aktiviert. Um dieses Beispiel auszuführen, müssen Sie dies explizit angeben.

1. Nach den vorherigen Schritte sollte im Browser immer noch das Azure-Verwaltungsportal mit der Registerkarte **Konfigurieren** Ihres Anwendungseintrags angezeigt werden.
2. Klicken Sie auf die Schaltfläche **Manifest verwalten** in der Taskleiste, um die Manifestdatei für Ihre Anwendung herunterzuladen, und speichern Sie sie auf Datenträger.
3. Öffnen Sie die Manifestdatei mit einem Texteditor. Suchen Sie nach der `oauth2AllowImplicitFlow`-Eigenschaft. Sie werden feststellen, dass sie auf `false` festgelegt ist; ändern Sie die Einstellung in `true`, und speichern Sie die Datei.
4. Klicken Sie auf die Schaltfläche **Manifest verwalten**, um die aktualisierte Manifestdatei hochzuladen. Speichern Sie die Konfiguration der App.

## Konfigurieren der To Do SPA zur Verwendung des Azure Active Directory-Mandanten

1. Öffnen Sie die Projektmappe in Visual Studio 2013.
2. Öffnen Sie im TodoSPA-Projekt die Datei `web.config`.
3. Suchen Sie den App-Schlüssel `ida:Tenant`, und ersetzen Sie den Wert durch den Namen Ihres Azure AD-Verzeichnisses.
4. Suchen Sie den App-Schlüssel `ida:Audience`, und ersetzen Sie den Wert durch die Client-ID aus dem Azure-Portal.
5. Öffnen Sie im TodoSPA-Projekt die Datei `App/Scripts/App.js` erneut, und suchen Sie die Zeile `adalAuthenticationServiceProvider.init(`.
6. Ersetzen Sie den Wert von `tenant` durch den Namen Ihres Azure AD-Verzeichnisses.
7. Ersetzen Sie den Wert von `clientId` durch die Client-ID aus dem Azure-Portal.

## Ausführen des Beispiels

Bereinigen Sie die Projektmappe, erstellen Sie sie neu, und führen Sie sie aus. 

Sie können den Anmeldevorgang auslösen, indem Sie in der rechten oberen Ecke auf den Anmeldelink klicken, oder indem Sie direkt auf die Registerkarten **To Do List** oder **To Go List** klicken.  Untersuchen Sie das Beispiel, indem Sie sich anmelden, Elemente zur Aufgabenliste hinzufügen, das Benutzerkonto entfernen und von vorne beginnen.  Fügen Sie der To Go-Liste Orte hinzu, indem Sie CRUD-Vorgänge für die To Go API mit CORS ausführen.

## Bereitstellen dieses Beispiels in Azure

Um die To Do SPA und die To Go API in Azure-Websites bereitzustellen, erstellen Sie zwei Websites, veröffentlichen jedes Projekt in einer Website und aktualisieren beide Projekte so, dass sie auf die neuen Adressen anstatt auf IIS Express verweisen.

### Erstellen der Azure-Website für To Go API

1. Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com).
2. Klicken Sie in der linken Navigationsleiste auf **Websites**.
3. Klicken Sie in der unteren linken Ecke auf **Neu**, wählen Sie **Server** > **Website** > **Benutzerdefiniert erstellen**, wählen Sie den Hostingplan und die Region aus, und weisen Sie der Website einen Namen zu, z. B. togo-contoso.azurewebsites.net.  Wählen Sie eine Datenbank aus, die Sie verwenden möchten, oder erstellen Sie eine neue.  Klicken Sie auf **Website erstellen**.
4. Nachdem die Website erstellt wurde, klicken Sie darauf, um sie zu verwalten.  Laden Sie zum Ausführen der nächsten Schrittfolge die PUBLISHSETTINGS-Datei herunter, und speichern Sie sie.  Sie können die Bereitstellung auch mit anderen Methoden wie z. B. aus der Quellcodeverwaltung durchführen. Weitere Informationen zur Verwendung einer PUBLISHSETTINGS-Datei finden Sie unter [Gewusst wie: Verbinden mit Ihrem Abonnement](http://azure.microsoft.com/documentation/articles/install-configure-powershell/#Connect). 

### Erstellen der Azure-Website für To Do SPA

1. Navigieren Sie zum [Azure-Verwaltungsportal](https://manage.windowsazure.com).
2. Klicken Sie in der linken Navigationsleiste auf **Websites**.
3. Klicken Sie in der unteren linken Ecke auf **Neu**, wählen Sie **Server** > **Website** > **Benutzerdefiniert erstellen**, wählen Sie den Hostingplan und die Region aus, und weisen Sie der Website einen Namen zu, z. B. todo-contoso.azurewebsites.net.   Wählen Sie eine Datenbank aus, die Sie verwenden möchten; Sie können die gleiche Datenbank wie für To Go API verwenden.  Klicken Sie auf **Website erstellen**.
4. Nachdem die Website erstellt wurde, klicken Sie darauf, um sie zu verwalten.  Laden Sie erneut die PUBLISHSETTINGS-Datei für diese Website herunter, und speichern Sie sie.

### Aktualisieren beider Projekte für die Verwendung von Azure-Websites

1. Wechseln Sie in Visual Studio zum TodoSPA-Projekt.
2. Es sind zwei Änderungen erforderlich.  Ersetzen Sie in `App\Scripts\app.js` den Eigenschaftsnamen des `endpoints`-Objekts durch die neue Adresse der To Go API, z. B. `https://togo-contoso.azurewebsites.net/`. . Ersetzen Sie in `App\Scripts\toGoListSvc.js` die `apiEndpoint`-Variable durch den gleichen Wert.
3. Im ToGoAPI-Projekt ist nur eine Änderung erforderlich. Aktualisieren Sie in `Controllers\ToGoListController.cs`, das `[EnableCors...]`-Attribut so, dass es die neue Adresse der To Do SPA angibt, z. B. `https://todo-contoso.azurewebsites.net`.  Denken Sie auch hier daran, den nachgestellten Schrägstrich auszulassen.

### Veröffentlichen der To Go API in Azure-Websites

1. Wechseln Sie zu Visual Studio und dort zum ToGoAPI-Projekt.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen** aus. Klicken Sie auf **Importieren**, und importieren Sie das heruntergeladene To Go API-Veröffentlichungsprofil.
6. Aktualisieren Sie auf der Registerkarte **Verbindung** die Ziel-URL in das https-Format, z. B. https://togo-constoso.azurewebsites.net. Klicken Sie auf **Weiter**.
7. Vergewissern Sie sich auf der Registerkarte **Einstellungen**, dass **Organisationsauthentifizierung aktivieren** NICHT ausgewählt ist.  Klicken Sie auf **Veröffentlichen**.
8. Visual Studio veröffentlicht das Projekt und öffnet automatisch einen Browser zur URL des Projekts.  Wenn die Standardwebseite des Projekts angezeigt wird, war die Veröffentlichung erfolgreich.

### Veröffentlichen der To Do SPA in Azure-Websites

1. Wechseln Sie zu Visual Studio und dort zum TodoSPA-Projekt.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen** aus.  Klicken Sie auf **Importieren**, und importieren Sie die heruntergeladene PUBLSIHSETTINGS-Datei für die To Do SPA.
6. Aktualisieren Sie auf der Registerkarte **Verbindung** die Ziel-URL in das https-Format, z. B. https://todo-contoso.azurewebsites.net.  Klicken Sie auf **Weiter**.
7. Vergewissern Sie sich auf der Registerkarte **Einstellungen**, dass **Organisationsauthentifizierung aktivieren** NICHT ausgewählt ist.  Klicken Sie auf **Veröffentlichen**.
8. Visual Studio veröffentlicht das Projekt und öffnet automatisch einen Browser zur URL des Projekts.  Wenn die Standardwebseite des Projekts angezeigt wird, war die Veröffentlichung erfolgreich.

### Aktualisieren der To Do SPA-Konfiguration im Verzeichnismandanten

1. Navigieren Sie zum [Azure-Verwaltungsportal](https://manage.windowsazure.com).
2. Klicken Sie im linken Navigationsbereich auf **Active Directory**, und wählen Sie Ihren Mandanten aus.
3. Wählen Sie auf der Registerkarte **Anwendungen** die Anwendung **To Do SPA** aus.
4. Aktualisieren Sie auf der Registerkarte **Konfigurieren** die Felder **Anmelde-URL** und **Antwort-URL** in die Adresse Ihrer SPA, z. B. https://todo-contoso.azurewebsites.net.  Speichern Sie die Konfiguration.

## Informationen zum Code

Die Schlüsseldateien mit der Authentifizierungslogik sind folgende:

- **App.js** - fügt die ADAL-Modulabhängigkeit ein, stellt die App-Konfigurationswerte zur Verfügung, die von ADAL zum Steuern von Protokollinteraktionen mit Azure AD verwendet werden, und zeigt an, welche Routen nicht ohne vorherige Authentifizierung verwendet werden dürfen

- **index.html** - enthält einen Verweis auf adal.js

- **HomeController.js** - demonstriert die Nutzung der login()- und logOut()-Methoden in ADAL

- **UserDataController.js** - zeigt das Extrahieren von Benutzerinformationen aus dem zwischengespeicherten ID-Token.
   
Besonderer Dank an @matvelloso für die Unterstützung bei der Erstellung dieses Lernprogramms.


## Nächste Schritte

Nachfolgend sind einige zusätzliche Ressourcen aufgeführt, die Sie bei der Verwendung von Azure AD zum Hinzufügen von Authentifizierung und Autorisierung zu Webanwendungen und Web-APIs unterstützen: 

+ [Azure Active Directory-Codebeispiele](https://msdn.microsoft.com/library/azure/dn646737.aspx)
+ [Authentifizierungsszenarien für Azure AD](https://msdn.microsoft.com/library/azure/dn499820.aspx)




<!--HONumber=47-->
