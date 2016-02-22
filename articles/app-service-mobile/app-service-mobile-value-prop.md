<properties
	pageTitle="Was sind Mobile Apps?"
	description="Erfahren Sie, welche Vorteile App Service für Ihre mobilen Unternehmens-Apps bietet."
	services="app-service\mobile"
	documentationCenter=""
	authors="kirillg"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="na"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="kirillg"/>

# <a name="getting-started"> </a>Was sind Mobile Apps?

Azure App Service ist ein vollständiges PaaS-Angebot (Platform as a Service) für professionelle Entwickler, das einen umfangreichen Satz von Funktionen für Web-, mobile und Integrationsszenarien bereitstellt. *Mobile Apps* in *Azure App Service* bietet eine hochgradig skalierbare, global verfügbare Entwicklungsplattform für mobile Anwendungen für Unternehmensentwickler und Systemintegratoren, die umfassende Funktionen für mobile Entwickler bereitstellt.

![Mobile Apps](./media/app-service-mobile-value-prop/overview.png)

##Warum Mobile Apps?
*Mobile Apps* in *Azure App Service* bietet eine hochgradig skalierbare, global verfügbare Entwicklungsplattform für mobile Anwendungen für Unternehmensentwickler und Systemintegratoren, die umfassende Funktionen für mobile Entwickler bereitstellt. Mobile Apps bietet folgende Möglichkeiten:

- **Entwickeln systemeigener und plattformübergreifender Apps** – Unabhängig davon, ob Sie systemeigene iOS-, Android- und Windows-Apps oder plattformübergreifende Xamarin- oder Cordova-Apps (Phonegap) erstellen, können Sie mithilfe systemeigener SDKs die Vorteile von App Service nutzen.
- **Herstellen einer Verbindung mit Ihren Unternehmenssystemen** – Mit Mobile Apps können Sie die Unternehmensanmeldung innerhalb von Minuten hinzufügen und eine Verbindung mit Ihren lokalen Unternehmensressourcen oder Cloudressourcen herstellen.
- **Einfache Verbindung mit SaaS-APIs** – Mit mehr als 40 SaaS-API-Connectors können Sie Ihre App einfach in die SaaS-APIs integrieren, die aktuell von Ihrem Unternehmen genutzt werden. Möchten Sie den Kontostatus sowohl im CRM- als auch im Rechnungssystem aktualisieren? Mobile Apps bietet zahlreiche sofort einsatzfähige SaaS-APIs für Unternehmen.
- **Erstellen von offlinefähigen Apps mit Synchronisierung** – Steigern Sie die Produktivität Ihrer mobilen Mitarbeiter mithilfe von Apps, die auch offline verwendet werden können, und nutzen Sie Mobile Apps zur Datensynchronisierung im Hintergrund, wenn eine Verbindung vorhanden ist – für jede der von Ihnen verwendeten Unternehmensdatenquellen oder SaaS-APIs.
- **Pushbenachrichtigungen an Millionen Benutzer innerhalb von Sekunden** – Beteiligen Sie Ihre Kunden mit Pushbenachrichtigungen an beliebige Geräte – personalisiert, auf ihre Anforderungen zugeschnitten und zum richtigen Zeitpunkt gesendet.

## Mobile App-Konzepte
- **Einmaliges Anmelden (Single Sign-On, SSO)** – Wählen Sie aus einer ständig größer werdenden Auswahl an Identitätsanbietern, darunter Azure Active Directory, Facebook, Google, Twitter und Microsoft-Konto, und nutzen Sie Mobile Apps, um Ihrer App innerhalb von Minuten eine Authentifizierungsfunktion hinzuzufügen.
- **Offlinesynchronisierung** – Mobile Apps ermöglicht es Ihnen, auf einfache Weise stabile und reaktionsschnelle Apps zu erstellen, mit denen Mitarbeiter offline arbeiten können, wenn keine Konnektivität vorhanden ist. Sobald wieder eine Verbindung besteht, erfolgt eine Synchronisierung mit Ihren Unternehmens-Back-End-Systemen. Die Funktion zur Offlinesynchronisierung wird auf allen Clientplattformen unterstützt und kann mit beliebigen Datenquellen eingesetzt werden, darunter SQL, Table Storage, Mongo oder Document DB. Darüber hinaus werden zahlreiche SaaS-APIs unterstützt, z. B. Office 365, Salesforce, Dynamics oder lokale Datenbanken.
- **Pushbenachrichtigungen** – Mobile Apps bietet ein hochgradig skalierbares Benachrichtigungsmodul für mobile Pushbenachrichtigungen (Notification Hubs), mit dem innerhalb von Sekunden Millionen von personalisierten Pushbenachrichtigungen an Benutzer mit iOS-, Android-, Windows- oder Kindle-Geräten gesendet werden können. Sie können Notification Hubs problemlos mit einem vorhandenen App-Back-End verknüpfen, unabhängig davon, ob das Back-End lokal oder in der Cloud gehostet wird.
- **Automatische Skalierung** – App Service ermöglicht Ihnen eine schnelle vertikale oder horizontale Skalierung, um beliebige eingehende Datenlasten zu verarbeiten. Wählen Sie die Anzahl und Größe der VMs manuell aus, oder legen Sie eine automatische Skalierung fest, damit Ihr mobiles App-Back-End basierend auf der Datenlast oder einem Zeitplan automatisch skaliert wird.

## Erste Schritte ##
Absolvieren Sie zum Einstieg in Mobile Apps das Lernprogramm [Erste Schritte].

Weitere Informationen zur Azure App Service-Plattform finden Sie unter [Azure App Service].

>[AZURE.NOTE] Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](https://tryappservice.azure.com/?appServiceName=mobile) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

<!-- URLs. -->
[Migrate your Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md

[Azure App Service]: ../app-service/app-service-value-prop-what-is.md
[Erste Schritte]: app-service-mobile-ios-get-started.md

<!---HONumber=AcomDC_0211_2016-->