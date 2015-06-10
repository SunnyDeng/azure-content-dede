<properties
	pageTitle="Erste Schritte in Azure AD iOS | Microsoft Azure"
	description="In diesem Thema erfahren Sie, wie eine iOS-Anwendung erstellt wird, die sich für die Anmeldung in Azure AD integriert und über OAuth durch Azure AD geschützte APIs aufruft."
	services="active-directory"
	documentationCenter="ios"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="04/28/2015"
	ms.author="brandwe"/>

# Integrieren von Azure AD in eine iOS-Anwendung

[AZURE.INCLUDE [active-directory-devguide](../includes/active-directory-devguide.md)]

Azure AD erleichtert Ihnen bei der Entwicklung von Desktop-Anwendungen die Authentifizierung der Benutzer über deren Active Directory-Konten. Außerdem ermöglicht es in Ihren Anwendungen die sichere Nutzung jeder durch Azure AD geschützten Web-API wie der Azure-API oder Office 365-APIs.

Für iOS-Clients, die auf geschützte Ressourcen zugreifen müssen, bietet Azure AD die Active Directory-Authentifizierungsbibliothek (ADAL). Die einzige Aufgabe von ADAL besteht darin, Ihrer Anwendung das Abrufen von Zugriffstoken zu erleichtern. Um Ihnen zu zeigen, wie einfach das geht, wollen wir nun eine Objective C-Anwendung mit einer Aufgabenliste entwickeln, die folgende Aktionen ausführt:

-	Abrufen der Zugriffstoken zum Aufrufen der Azure AD Graph-API mit dem [OAuth 2.0-Authentifizierungsprotokoll](https://msdn.microsoft.com/library/azure/dn645545.aspx)
-	Durchsuchen eines Verzeichnisses nach Benutzern mit einem bestimmten Aliasnamen
-	Abmelden von Benutzern

Zur Entwicklung der vollständigen Arbeitsanwendung müssen Sie folgende Schritte ausführen:

2. Registrieren Ihrer Anwendung bei Azure AD
3. Installieren und Konfigurieren von ADAL
5. Verwenden von ADAL zum Abrufen von Tokens aus Azure AD

Beginnen Sie, indem Sie [das Anwendungsgerüst](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip) oder [das vollständige Beispiel herunterladen](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip). Außerdem benötigen Sie einen Azure AD-Mandanten, in dem Sie Benutzer erstellen und Ihre Anwendung registrieren können. Wenn Sie über noch keinen Mandanten verfügen, [erfahren Sie hier, wie Sie einen erhalten](active-directory-howto-tenant.md).

## Schritt 1: Herunterladen und Ausführen des .Net- oder Node.js REST API TODO Sample Server

Dieses Beispiel ist speziell für die Arbeit mit dem vorhandenen Beispiel zum Erstellen einer ToDo REST-API für Microsoft Azure Active Directory für einen einzelnen Mandanten vorgesehen. Dies ist eine Voraussetzung für den Schnellstart.

Informationen zu dessen Einrichtung finden Sie hier unter dem vorhandenen Beispiel:

* [Microsoft Azure Active Directory-Beispiel-REST-API-Dienst für Node.js](active-directory-devquickstarts-webapi-nodejs.md)

## Schritt 2: Registrieren Ihrer Web-API beim Microsoft Azure AD-Mandanten

**Was mache ich?**

*Microsoft Active Directory unterstützt das Hinzufügen von zwei Anwendungstypen: Web-APIs, die Benutzern und Anwendungen Dienste anbieten (im Web oder in einer Anwendung, die auf einem Gerät ausgeführt wird), die auf diese Web-APIs zugreifen. In diesem Schritt registrieren Sie die Web-API, die Sie lokal zum Testen dieses Beispiels ausführen. Normalerweise handelt es sich bei dieser Web-API um einen REST-Dienst, der die Funktionen anbietet, auf die Ihre Anwendung zugreifen können soll. Microsoft Azure Active Directory kann jeden beliebigen Endpunkt schützen!*

*In diesem Thema wird davon ausgegangen, dass Sie die zuvor erwähnte TODO-REST-API registrieren. Allerdings funktioniert diese Methode für jede Web-API, die Azure Active Directory schützen soll.*

Schritte zum Registrieren einer Web-API bei Microsoft Azure AD

1. Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com) an.
2. Klicken Sie in der linken Navigationsleiste auf Active Directory.
3. Klicken Sie auf den Verzeichnismandanten, bei dem die Beispielanwendung registriert werden soll.
4. Klicken Sie auf die Registerkarte Anwendungen.
5. Klicken Sie in der Taskleiste auf Hinzufügen.
6. Klicken Sie auf „Eine von meinem Unternehmen entwickelte Anwendung hinzufügen“.
7. Geben Sie einen Anzeigenamen für die Anwendung ein, z. B. „TodoListService“, wählen Sie „Webanwendung und/oder Web-API“ aus, und klicken Sie auf „Weiter“.
8. Geben Sie als Anmelde-URL die Basis-URL für das Beispiel ein, standardmäßig `https://localhost:8080`.
9. Geben Sie als App-ID-URI `https://<your_tenant_name>/TodoListService` ein, und ersetzen Sie dabei `<your_tenant_name>` durch den Namen Ihres Azure AD-Mandanten. Klicken Sie auf „OK“, um die Registrierung abzuschließen.
10. Klicken Sie im Azure-Portal auf die Registerkarte Konfigurieren Ihrer Anwendung.
11. **Suchen Sie die Client-ID, und kopieren Sie diese**. Sie benötigen diesen Wert später zum Konfigurieren Ihrer Anwendung.

## Schritt 3: Registrieren der systemeigenen iOS-Beispiel-Clientanwendung

Zunächst müssen Sie Ihre Webanwendung registrieren. Danach müssen Sie Azure Active Directory über Ihre Anwendung informieren. Nur so kann die Anwendung mit der eben registrierten Web-API kommunizieren.

**Was mache ich?**

*Wie bereits erwähnt, unterstützt Microsoft Azure Active Directory das Hinzufügen von zwei Anwendungstypen. Web-APIs, die Benutzern und Anwendungen Dienste anbieten (im Web oder in einer Anwendung, die auf einem Gerät ausgeführt wird), die auf diese Web-APIs zugreifen. In diesem Schritt registrieren Sie die Anwendung dieses Beispiels. Dies ist erforderlich, damit die Anwendung Zugriff auf die eben registrierte Web-API anfordern kann. Azure Active Directory verweigert Ihrer Anwendung sogar die Anfrage nach einer Anmeldung, wenn sie nicht registriert ist. Dies ist ein Teil der Sicherheit dieses Modells.*

*Hier wird davon ausgegangen, dass Sie die oben genannte Beispielanwendung registrieren. Die gleiche Methode funktioniert jedoch für jede selbst entwickelte Anwendung.*

**Warum integriere ich sowohl eine Anwendung als auch eine Web-API in ein- und denselben Mandanten?**

*Wie Sie richtig vermuten, können Sie auch eine Anwendung erstellen, die auf eine externe API zugreift, die von einem anderen Mandanten aus in Azure Active Directory registriert ist. Wenn Sie dies tun, werden Ihre Kunden aufgefordert, der Verwendung der API in der Anwendung zuzustimmen. Das Schöne ist aber, dass die Active Directory-Authentifizierungsbibliothek (ADAL) für iOS diese Zustimmung für Sie übernimmt! Sobald wir uns mit den erweiterten Funktionen beschäftigen, werden Sie feststellen, dass dies ein wichtiger Teil der Aufgaben ist, die zum Zugriff auf die Suite der Microsoft-APIs von Azure und Office sowie anderer Dienstanbieter erforderlich sind. Da Sie aber nun sowohl Ihre Web-API als auch Ihre Anwendung unter dem gleichen Mandanten registriert haben, wird Ihnen keine Aufforderung zur Zustimmung angezeigt. Dies ist eher die Regel, wenn Sie eine Anwendung nur für Ihr eigenes Unternehmen entwickeln.*

1. Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com) an.
2. Klicken Sie in der linken Navigationsleiste auf Active Directory.
3. Klicken Sie auf den Verzeichnismandanten, bei dem die Beispielanwendung registriert werden soll.
4. Klicken Sie auf die Registerkarte Anwendungen.
5. Klicken Sie in der Taskleiste auf Hinzufügen.
6. Klicken Sie auf „Eine von meinem Unternehmen entwickelte Anwendung hinzufügen“.
7. Geben Sie einen Anzeigenamen für die Anwendung ein, z. B. „TodoListClient-iOS“, wählen Sie „Systemeigene Clientanwendung“ aus, und klicken Sie auf „Weiter“.
8. Geben Sie als Umleitungs-URI `http://TodoListClient` ein. Klicken Sie auf „Fertig stellen“.
9. Klicken Sie auf die Registerkarte „Konfigurieren“ der Anwendung.
10. Suchen Sie die Client-ID, und kopieren Sie diese. Sie benötigen diesen Wert später zum Konfigurieren Ihrer Anwendung.
11. Klicken Sie im Abschnitt „Berechtigungen für andere Anwendungen“ auf „Anwendung hinzufügen“. Wählen Sie „Sonstiges“ in der Dropdownliste „Anzeigen“ aus, und klicken Sie auf das obere Häkchen. Suchen Sie „TodoListService“, klicken Sie darauf, und klicken Sie auf das untere Häkchen, um die Anwendung hinzuzufügen. Wählen Sie „Auf TodoListService zugreifen“ in der Dropdownliste „Delegierte Berechtigungen“ aus, und speichern Sie die Konfiguration.


## Schritt 4: Herunterladen des Codes der systemeigenen iOS-Beispiel-Clientanwendung

* `$ git clone git@github.com:AzureADSamples/NativeClient-iOS.git`

## Schritt 5: Herunterladen von ADAL für iOS und Hinzufügen zu Ihrem XCode-Arbeitsbereich

#### Herunterladen von ADAL für iOS

* `git clone git@github.com:MSOpenTech/azure-activedirectory-library-for-ios.git`

#### Importieren der Bibliothek in Ihren Arbeitsbereich

Klicken Sie in XCode mit der rechten Maustaste auf Ihr Projektverzeichnis, und wählen Sie „Dateien hinzufügen zu iOS-Beispiel“ aus.

Wählen Sie auf Aufforderung das Verzeichnis aus, in dem Sie ADAL für iOS geklont haben.

#### Hinzufügen von libADALiOS.a zu verknüpften Frameworks und Bibliotheken

Klicken Sie unter „Verknüpfte Frameworks und Bibliotheken“ auf die Schaltfläche „Hinzufügen“, und fügen Sie die Bibliotheksdatei aus den importierten Frameworks hinzu.

Erstellen Sie ein Projekt-Build, um sicherzustellen, dass alles ordnungsgemäß kompiliert wird.


## Schritt 6: Konfigurieren Ihrer Web-API-Daten in der Datei settings.plist

Unter „Verwendete Dateien“ finden Sie auch eine Datei namens „settings.plist“. Sie enthält die folgenden Informationen:

```XML
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>authority</key>
	<string>https://login.windows.net/common/oauth2/token</string>
	<key>clientId</key>
	<string>xxxxxxx-xxxxxx-xxxxxxx-xxxxxxx</string>
	<key>resourceString</key>
	<string>https://localhost/todolistservice</string>
	<key>redirectUri</key>
	<string>http://demo_todolist_app</string>
	<key>userId</key>
	<string>user@domain.com</string>
	<key>taskWebAPI</key>
	<string>https://localhost/api/todolist/</string>
</dict>
</plist>
```

Ersetzen Sie die Angaben in dieser PLIST-Datei durch die Einstellungen für Ihre Web-API.

##### HINWEIS

Für den [Azure Active Directory-Beispiel-REST-API-Dienst für Node.js](https://github.com/AzureADSamples/WebAPI-Nodejs) können Sie die aktuellen Standardeinstellungen verwenden. Allerdings müssen Sie die Client-ID Ihrer Web-API angeben. Wenn Sie Ihre eigene API ausführen, müssen Sie die Endpunkte entsprechend aktualisieren.

## Schritt 7: Erstellen und Ausführen der Anwendung

Sie sollten eine Verbindung zum REST-API-Endpunkt herstellen können und nach den Anmeldeinformationen Ihres Azure Active Directory-Kontos gefragt werden.

Sehen Sie sich auch die folgenden Ressourcen an: – [AzureAD-Beispiele auf GitHub >>](https://github.com/AzureAdSamples) – Azure AD-Dokumentation auf [Azure.com >>](http://azure.microsoft.com/documentation/services/active-directory/)

<!---HONumber=58-->