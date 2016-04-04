<properties
	pageTitle="Gewusst wie: Aktivieren von App-übergreifendem SSO unter iOS mit ADAL | Microsoft Azure"
	description="Verwenden der ADAL SDK-Features zum Aktivieren von anwendungsübergreifendem SSO."
	services="active-directory"
	documentationCenter=""
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="03/17/2015"
	ms.author="brandwe"/>


# Gewusst wie: Aktivieren von App-übergreifendem SSO unter iOS mit ADAL


Die Möglichkeit des einmaligen Anmeldens (Single Sign-On, SSO), bei dem Benutzer einmal eingegebene Anmeldeinformationen automatisch auch für andere Anwendungen verwenden können, wird heute von den Kunden erwartet. Die Schwierigkeit, den Benutzernamen und das Kennwort auf einem kleinen Bildschirm einzugeben – häufig in Kombination mit einer weiteren Datenabfrage wie etwa einem Telefonanruf oder einem per SMS gesendeten Code (zweistufige Authentifizierung) – führt beim Benutzer schnell zu Unmut, wenn dieser Vorgang für Ihr Produkt mehrfach durchgeführt werden muss.

Wenn Sie außerdem eine Identitätsplattform nutzen, die andere Anwendungen verwenden können (beispielsweise Microsoft-Konten oder ein Office365-Geschäftskonto), erwarten Kunden, dass diese Anmeldeinformationen unabhängig vom Anbieter über all ihre Anwendungen hinweg verfügbar sind.

Die Microsoft Identity-Plattform nimmt Ihnen in Kombination mit unseren Microsoft Identity-SDKs diese harte Arbeit ab. So können Sie Ihren Kunden SSO entweder innerhalb Ihrer eigenen Anwendungssuite oder – mithilfe unserer Brokerfeatures und Authentifizierungsanwendungen – für das gesamte Gerät bereitstellen.

In dieser exemplarischen Vorgehensweise erfahren Sie, wie Sie unser SDK innerhalb Ihrer Anwendung konfigurieren, damit Ihre Kunden von diesen Vorteilen profitieren.

Die vorliegende exemplarische Vorgehensweise gilt für:

* Azure Active Directory
* Azure Active Directory B2C
* Azure Active Directory B2B


Hinweis: In diesem Dokument wird davon ausgegangen, dass Sie wissen, wie Sie [Anwendungen im Vorgängerportal für Azure Active Directory bereitstellen](active-directory-how-to-integrate.md), und dass Sie das [Microsoft Identity iOS SDK](https://github.com/AzureAD/azure-activedirectory-library-for-objc) in Ihre Anwendung integriert haben.

## SSO-Konzepte in der Microsoft Identity-Plattform

### Microsoft-Identitätsbroker

Microsoft stellt Anwendungen für jede mobile Plattform bereit, die als Brücke für Anmeldeinformationen von Anwendungen verschiedener Anbieter fungieren sowie spezielle erweiterte Features unterstützen, bei denen die Überprüfung der Anmeldeinformationen über eine einzige sichere Stelle erfolgt. Wir nennen diese Komponenten **Broker**. Unter iOS und Android werden diese über herunterladbare Anwendungen bereitgestellt. Diese Anwendungen können Kunden entweder unabhängig installieren, oder sie werden von Unternehmen, die einige oder alle Geräte für ihre Mitarbeiter verwalten, per Push auf das Gerät übertragen. Diese Broker unterstützen die Sicherheitsverwaltung basierend auf der Konfiguration des IT-Administrators entweder nur für einige Anwendungen oder für das gesamte Gerät. Unter Windows wird diese Funktion durch eine Kontoauswahl bereitgestellt, die in das Betriebssystem integriert ist. Die technische Bezeichnung hierfür lautet Webauthentifizierungsbroker.

Um zu verstehen, wie wir diese Broker einsetzen und wie Ihre Kunden diese während des Anmeldeflusses für die Microsoft Identity-Plattform wahrnehmen, werden diese Abläufe im Folgenden näher erläutert.

### Muster für die Anmeldung auf mobilen Geräten

Der Zugriff auf Anmeldeinformationen auf Geräten folgt für die Microsoft Identity-Plattform zwei grundlegenden Mustern:

* Nicht brokergestützte Anmeldungen
* Brokergestützte Anmeldungen

#### Nicht brokergestützte Anmeldungen

Nicht brokergestützte Anmeldungen sind Anmeldungen, die anwendungsintern erfolgen und den lokalen Gerätespeicher für diese Anwendung nutzen. Dieser Speicher kann für mehrere Anwendungen freigegeben werden, aber die Anmeldeinformationen sind eng an die App oder App-Suite gebunden, die diese Anmeldeinformationen nutzt. Dieses Muster kennen Sie wahrscheinlich von zahlreichen mobilen Anwendungen, bei denen Sie innerhalb der Anwendung einen Benutzernamen und ein Kennwort eingeben.

Diese Anmeldungen bieten folgende Vorteile:

-  Die Benutzerinteraktion läuft vollständig innerhalb der Anwendung ab.
-  Anmeldeinformationen können gemeinsam mit anderen Anwendungen genutzt werden, die das gleiche Signaturzertifikat verwenden. Auf diese Weise erzielen Sie SSO-Funktionalität für Ihre Anwendungssuite. 
-  Die Kontrolle der Anmeldeabläufe liegt sowohl vor als auch nach der Anmeldung bei der Anwendung.

Diese Anmeldungen haben folgende Nachteile:

- Benutzer können nicht bei allen Apps mit einer Microsoft-Identität von SSO profitieren, sondern nur bei den Microsoft-Identitäten, die Ihre Anwendung besitzt und konfiguriert hat.
- Ihre Anwendung kann nicht mit erweiterten Geschäftsfunktionen wie etwa dem bedingten Zugriff oder der Intune-Produktsuite verwendet werden.
- Ihre Anwendung kann keine auf Zertifikaten basierende Authentifizierung für Geschäftsbenutzer bereitstellen.

Die folgende Abbildung zeigt, wie die Microsoft Identity SDKs zusammen mit dem gemeinsam genutzten Speicher Ihrer Anwendung für die Bereitstellung von SSO sorgen:

```
+------------+ +------------+  +-------------+
|            | |            |  |             |
|   App 1    | |   App 2    |  |   App 3     |
|            | |            |  |             |
|            | |            |  |             |
+------------+ +------------+  +-------------+
| Azure SDK  | | Azure SDK  |  | Azure SDK   |
+------------+-+------------+--+-------------+
|                                            |
|            App Shared Storage              |
+--------------------------------------------+
```

#### Brokergestützte Anmeldungen

Brokergestützte Anmeldungen sind Anmeldeabläufe, die innerhalb der Brokeranwendung stattfinden und auf den Speicher und die Sicherheit des Brokers zurückgreifen, um Anmeldeinformationen für alle Anwendungen auf dem Gerät einzusetzen, die die Microsoft Identity-Plattform nutzen. Das bedeutet, dass Ihre Anwendungen bei der Benutzeranmeldung auf den Broker zurückgreifen. Unter iOS und Android wird diese Funktion über herunterladbare Anwendungen bereitgestellt. Diese Anwendungen können Kunden entweder unabhängig installieren, oder sie werden von Unternehmen, die das Gerät für ihre Benutzer verwalten, per Push auf das Gerät übertragen. Ein Beispiel für diesen Anwendungstyp ist die Azure Authenticator-App unter iOS. Unter Windows wird diese Funktion durch eine Kontoauswahl bereitgestellt, die in das Betriebssystem integriert ist. Die technische Bezeichnung hierfür lautet Webauthentifizierungsbroker. Der Ablauf variiert je nach Plattform, und eine nicht ordnungsgemäße Verwaltung kann die Benutzererfahrung beeinträchtigen. Sie kennen dieses Muster sicherlich, wenn Sie Facebook installiert haben und die Facebook-Anmeldefunktion in einer anderen Anwendung verwenden. Die Microsoft Identity-Plattform bedient sich des gleichen Musters.

Bei iOS führt dies zu einer „Übergangsanimation“, bei der Ihre Anwendung in den Hintergrund und die Azure Authenticator-Anwendungen in den Vordergrund rückt, sodass der Benutzer das gewünschte Konto für die Anmeldung auswählen kann.

Unter Android und Windows wird die Kontoauswahl im Vordergrund Ihrer Anwendung angezeigt, was für den Benutzer weniger störend ist.

#### Aufrufen des Brokers

Wenn ein kompatibler Broker auf dem Gerät installiert ist (beispielsweise die Azure Authenticator-Anwendung), übernehmen die Microsoft Identity SDKs automatisch den Aufruf des Brokers, wenn ein Benutzer angibt, dass er sich mit einem beliebigen Konto der Microsoft Identity-Plattform anmelden möchte. Hierbei kann es sich um ein persönliches Microsoft-Konto, um ein Geschäfts- oder Schulkonto oder um ein Konto handeln, das Sie über eines unserer B2C- und B2B-Produkte in Azure bereitstellen und hosten. Durch die Verwendung extrem sicherer Algorithmen und Verschlüsselungen stellen wir sicher, dass die Anmeldeinformationen auf sichere Weise abgerufen und an Ihre Anwendung zurückgegeben werden. Die technischen Details dieser Mechanismen werden nicht veröffentlicht, wurden aber in Zusammenarbeit mit Apple und Google entwickelt.

**Der Entwickler kann entscheiden, ob das Microsoft Identity SDK den Broker aufrufen oder den nicht brokergestützten Ablauf verwenden soll.** Wenn sich der Entwickler allerdings gegen den brokergestützten Ablauf entscheidet, verzichtet er auf die Vorteile der Nutzung von SSO-Anmeldeinformationen, die der Benutzer möglicherweise bereits auf dem Gerät hinzugefügt hat. Außerdem kann die Anwendung ohne brokergestützte Anmeldung keine der von Microsoft bereitgestellten Geschäftsfunktionen wie etwa den bedingten Zugriff, Intune-Verwaltungsfunktionen und eine zertifikatbasierte Authentifizierung nutzen.

Diese Anmeldungen bieten folgende Vorteile:

-  Benutzer profitieren bei allen ihren Anwendungen von SSO – unabhängig vom Anbieter.
-  Ihre Anwendung kann mit erweiterten Geschäftsfunktionen wie etwa dem bedingten Zugriff oder der Intune-Produktsuite verwendet werden.
-  Ihre Anwendung kann eine auf Zertifikaten basierende Authentifizierung für Geschäftsbenutzer bereitstellen.
- Die Anmeldung ist deutlich sicherer, da die Identität der Anwendung und des Benutzers von der Brokeranwendung mit zusätzlichen Sicherheitsalgorithmen und Verschlüsselung verifiziert wird.

Diese Anmeldungen haben folgende Nachteile:

- Unter iOS muss der Benutzer die Anmeldeinformationen außerhalb der Anwendung auswählen.
- Die Anmeldeumgebung für Ihre Kunden kann nicht innerhalb Ihrer Anwendung verwaltet werden.



Die folgende Abbildung zeigt, wie die Microsoft Identity SDKs zusammen mit den Brokeranwendungen für die Bereitstellung von SSO sorgen:

```
+------------+ +------------+   +-------------+
|            | |            |   |             |
|   App 1    | |   App 2    |   |   Someone   |
|            | |            |   |    Else's   |
|            | |            |   |     App     |
+------------+ +------------+   +-------------+
| Azure SDK  | | Azure SDK  |   | Azure SDK   |
+-----+------+-+-----+------+-  +-------+-----+
      |              |                  |
      |       +------v------+           |
      |       |             |           |
      |       | Microsoft   |           |
      +-------> Broker      |^----------+
              | Application
              |             |
              +-------------+
              |             |
              |   Broker    |
              |   Storage   |
              |             |
              +-------------+
```
              
Mit diesen Hintergrundinformationen verfügen Sie nun über ein besseres Verständnis der Funktionsweise und Implementierung von SSO innerhalb Ihrer Anwendung mithilfe der Microsoft Identity-Plattform und den zugehörigen SDKs verfügen.


## Aktivieren von App-übergreifendem SSO mit ADAL

In diesem Abschnitt verwenden wir das ADAL iOS SDK für folgende Aufgaben:

- Aktivieren von nicht brokergestütztem SSO für Ihre App-Suite
- Aktivieren der Unterstützung für brokergestütztes SSO

### Aktivieren von SSO für nicht brokergestütztes SSO

Für nicht brokergestütztes, anwendungsübergreifendes SSO übernehmen die Microsoft Identity SDKs einen Großteil der komplexen SSO-Verwaltung für Sie. Hierzu gehört das Suchen des richtigen Benutzers im Cache sowie das Verwalten einer Liste der angemeldeten Benutzer, die Sie abfragen können.

Gehen Sie folgendermaßen vor, um übergreifendes SSO für Anwendungen in Ihrem Besitz zu aktivieren:

1. Stellen Sie sicher, dass all Ihre Anwendungen die gleiche Client-ID oder Anwendungs-ID verwenden. 
* Stellen Sie sicher, dass all Ihre Anwendungen das gleiche Signaturzertifikat von Apple verwenden, um die gemeinsame Verwendung von Schlüsselbunden zu ermöglichen.
* Fordern Sie für jede Ihrer Anwendungen die gleichen Schlüsselbundberechtigung an.
* Geben Sie für die Microsoft Identity SDKs an, welchen freigegebenen Schlüsselbund wir verwenden sollen.

#### Verwenden der gleichen Client-ID/Anwendungs-ID für alle Anwendungen in Ihrer App-Suite

Damit die Microsoft Identity-Plattform weiß, dass Token anwendungsübergreifend verwendet werden dürfen, muss jede Ihrer Anwendungen die gleiche Client-ID oder Anwendungs-ID verwenden. Hierbei handelt es sich um den eindeutigen Bezeichner, den Sie bei der Registrierung Ihrer ersten Anwendung im Portal erhalten haben.

Nun fragen Sie sich vielleicht, wie unterschiedliche Apps gegenüber dem Microsoft Identity-Dienst identifiziert werden, wenn sie die gleiche Anwendungs-ID verwenden. Die Antwort lautet: mithilfe von **Umleitungs-URIs**. Jede Anwendung kann mehrere Umleitungs-URIs im Integrationsportal registrieren. Jede App innerhalb Ihrer Suite kann einen anderen Umleitungs-URI verwenden. Nachfolgend sehen Sie ein Beispiel hierfür:

Umleitungs-URI für App1: `x-msauth-mytestiosapp://com.myapp.mytestapp`

Umleitungs-URI für App2: `x-msauth-mytestiosapp://com.myapp.mytestapp2`

Umleitungs-URI für App3: `x-msauth-mytestiosapp://com.myapp.mytestapp3`

...

Diese URIs werden unterhalb der gleichen Client-ID/Anwendungs-ID geschachtelt und auf der Grundlage des Umleitungs-URIs ermittelt, den Sie in Ihrer SDK-Konfiguration an uns zurückgeben.

```
+-------------------+
|                   |
|  Client ID        |
+---------+---------+
          |
          |           +-----------------------------------+
          |           |  App 1 Redirect URI               |
          +----------^+                                   |
          |           +-----------------------------------+
          |
          |           +-----------------------------------+
          +----------^+  App 2 Redirect URI               |
          |           |                                   |
          |           +-----------------------------------+
          |
          +----------^+-----------------------------------+
                      |  App 3 Redirect URI               |
                      |                                   |
                      +-----------------------------------+

```


*Das Format dieser Umleitungs-URIs wird nachstehend erläutert. Grundsätzlich können Sie einen beliebigen Umleitungs-URI verwenden. Wenn Sie jedoch den Broker unterstützen möchten, muss ein ähnliches Format gewählt werden wie im obigen Beispiel zu sehen.*



#### Erstellen einer anwendungsübergreifenden Schlüsselbundnutzung

Das Ermöglichen einer gemeinsamen Schlüsselbundnutzung würde den Rahmen dieses Dokuments sprengen und wird von Apple in [diesem Dokument](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html) behandelt. Wichtig ist, dass Sie sich für einen Schlüsselbundnamen entscheiden und die Funktion allen Ihren Anwendungen hinzufügen.

Bei korrekter Einrichtung von Berechtigungen wird in Ihrem Projektverzeichnis eine Datei namens `entitlements.plist` angezeigt. Diese enthält in etwa Folgendes:

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>keychain-access-groups</key>
	<array>
		<string>$(AppIdentifierPrefix)com.myapp.mytestapp</string>
		<string>$(AppIdentifierPrefix)com.myapp.mycache</string>
	</array>
</dict>
</plist>
```

Informieren Sie das Microsoft Identity SDK über Ihren Schlüsselbund, sobald die Schlüsselbundberechtigung in jeder Ihrer Anwendungen aktiviert ist. Verwenden Sie hierzu in `ADAuthenticationSettings` die folgende Einstellung:

```
defaultKeychainSharingGroup=@"com.myapp.mycache";
```

> [AZURE.WARNING] 
Bei anwendungsübergreifender Nutzung eines Schlüsselbunds kann jede Anwendung Benutzer löschen und (schlimmer noch) anwendungsübergreifend alle Token löschen. Dies ist insbesondere dann katastrophal, wenn Sie über Anwendungen verfügen, die zur Ausführung von Hintergrundaufgaben auf die Token zurückgreifen. Seien Sie deshalb bei der Verwendung eines gemeinsam genutzten Schlüsselbunds bei allen Entfernungsvorgängen über die Microsoft Identity SDKs besonders vorsichtig.

Das ist alles! Das Microsoft Identity SDK verwendet jetzt Anmeldeinformationen über all Ihre Anwendungen hinweg. Die Benutzerliste wird ebenfalls über alle Anwendungsinstanzen hinweg verwendet.

### Aktivieren von SSO für brokergestütztes SSO

Die Fähigkeit einer Anwendung, einen beliebigen der auf dem Gerät installierten Broker zu verwenden, ist **standardmäßig deaktiviert**. Damit Ihre Anwendung mit dem Broker verwendet werden kann, müssen Sie einige zusätzliche Konfigurationsschritte ausführen und Ihrer Anwendung Code hinzufügen.

Erforderliche Schritte:

1. Aktivieren des Brokermodus im Anwendungscode für den MS SDK-Aufruf
2. Festlegen eines neuen Umleitungs-URIs und Bereitstellen dieses URIs in Ihrer App und App-Registrierung
3. Registrieren eines URL-Schemas
4. iOS9-Unterstützung: Hinzufügen einer Berechtigung zur Datei „info.plist“


#### Schritt 1: Aktivieren des Brokermodus in Ihrer Anwendung
Die Möglichkeit zur Verwendung des Brokers wird für Ihre Anwendung aktiviert, wenn Sie den Kontext oder die anfängliche Einrichtung Ihres Authentifizierungsobjekts erstellen. Erreicht wird dies durch Festlegen des Anmeldeinformationstyps in Ihrem Code:

```
/*! See the ADCredentialsType enumeration definition for details */
@propertyADCredentialsType credentialsType;
```
Die Einstellung `AD_CREDENTIALS_AUTO` ermöglicht es dem Microsoft Identity SDK, Aufrufe an den Broker zu richten. `AD_CREDENTIALS_EMBEDDED` verhindert dies.

#### Schritt 2: Registrieren eines URL-Schemas
Die Microsoft Identity-Plattform verwendet URLs, um den Broker aufzurufen und die Kontrolle anschließend wieder an die Anwendung abzugeben. Sie benötigen daher ein für Ihre Anwendung registriertes URL-Schema, das der Microsoft Identity-Plattform bekannt ist. Dieses kann zusätzlich zu den App-Schemas verwendet werden, die Sie ggf. bereits mit der Anwendung registriert haben.

> [AZURE.WARNING] 
Es empfiehlt sich, ein möglichst eindeutiges URL-Schema zu verwenden, um die Wahrscheinlichkeit zu verringern, dass das gleiche URL-Schema von einer anderen Anwendung verwendet wird. Die Eindeutigkeit von im App Store registrierten URL-Schemas wird von Apple nicht erzwungen.

Im Anschluss sehen Sie an einem Beispiel, wie dies in der Projektkonfiguration aussieht. Hierfür kann auch XCode verwendet werden:

```
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.myapp.mytestapp</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>x-msauth-mytestiosapp</string>
        </array>
    </dict>
</array>
```

#### Schritt 3: Festlegen eines neuen Umleitungs-URIs mit Ihrem URL-Schema

Um sicherzustellen, dass die Anmeldeinformationstoken immer an die richtige Anwendung zurückgegeben werden, muss sichergestellt werden, dass der Rückruf an Ihre Anwendung in einer Weise erfolgt, die das iOS-Betriebssystem überprüfen kann. Das iOS-Betriebssystem meldet den Brokeranwendungen von Microsoft die Paket-ID der aufrufenden Anwendung. Diese kann nicht durch eine nicht autorisierte Anwendung ausgespäht werden. Deshalb nutzen wir den Hash gemeinsam mit dem URI unserer Brokeranwendung, um sicherzustellen, dass die Token an die richtige Anwendung zurückgegeben werden. Es ist erforderlich, dass Sie diesen eindeutigen Umleitungs-URI sowohl in Ihrer Anwendung als auch in unserem Entwicklerportal als Umleitungs-URI festlegen.

Der Umleitungs-URI muss das folgende Format besitzen:

`<app-scheme>://<your.bundle.id>`

Beispiel: *x-msauth-mytestiosapp://com.myapp.mytestapp*

Dieser Umleitungs-URI muss in Ihrer App-Registrierung über das [klassische Azure-Portal](https://manage.windowsazure.com/) angegeben werden. Weitere Informationen zur Azure AD-App-Registrierung finden Sie unter [Integration in Azure Active Directory](active-directory-how-to-integrate.md).


##### Schritt 3a: Hinzufügen eines Umleitungs-URIs in Ihrer App und im Entwicklerportal zur Unterstützung der zertifikatbasierten Authentifizierung

Zur Unterstützung der zertifikatbasierten Authentifizierung muss in Ihrer Anwendung und im [klassischen Azure-Portal](https://manage.windowsazure.com/) ein zweites msauth-Element für die Abwicklung der Zertifikatauthentifizierung registriert werden, wenn Sie dieses Feature in der Anwendung unterstützen möchten.

`msauth://code/<broker-redirect-uri-in-url-encoded-form>`

Beispiel: **msauth://code/x-msauth-mytestiosapp%3A%2F%2Fcom.myapp.mytestapp*


#### Schritt 4: iOS9: Hinzufügen eines Konfigurationsparameters zu Ihrer App

ADAL prüft mithilfe von „–CanOpenURL:“, ob der Broker auf dem Gerät installiert ist. Unter iOS9 wurden die Schemas, die von einer Anwendung abgefragt werden können, von Apple fest vorgegeben. Daher muss „msauth“ in `info.plist file` dem Abschnitt „LSApplicationQueriesSchemes“ hinzugefügt werden.

<key>LSApplicationQueriesSchemes</key> <array> <string>msauth</string> </array>

### Sie haben SSO konfiguriert.

Das Microsoft Identity SDK verwendet Anmeldeinformationen nun automatisch anwendungsübergreifend und ruft den Broker auf, wenn dieser auf dem Gerät vorhanden ist.

<!---HONumber=AcomDC_0323_2016-->