<properties
	pageTitle="Verwalten Ihrer ersten API in Azure API Management | Microsoft Azure"
	description="Erfahren Sie mehr über das Erstellen von APIs, das Hinzufügen von Vorgängen sowie über die ersten Schritte mit API Management."
	services="api-management"
	documentationCenter=""
	authors="steved0x"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="api-management"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="10/27/2015"
	ms.author="sdanie"/>

# Verwalten Ihrer ersten API in Azure API Management

## <a name="overview"> </a>Übersicht

Diese Anleitung beschreibt die ersten Schritte mit Azure API Management und Ihrem ersten API-Aufruf.

## <a name="concepts"> </a>Was ist Azure API Management?

Mithilfe von Azure API Management können Sie ein API-Programm mit vollem Funktionsumfang auf einem beliebigen Back-End starten.

Zu den häufigen Szenarios gehören:

* **Schutz einer mobilen Infrastruktur** durch Einschränkung des Zugriffs mit API-Schlüsseln, das Verhindern von DOS-Angriffen über eine Drosselung oder das Verwenden erweiterter Sicherheitsrichtlinien wie z. B. der JWT-Tokenüberprüfung.
* **Bereitstellung eines ISV-Partnerökosystems** durch eine schnelle Partnerintegration über das Entwicklerportal und das Erstellen einer API-Fassade zum Entkoppeln interner Implementierungen, die noch nicht für die Partnernutzung geeignet sind.
* **Ausführung eines internen API-Programms** durch Bereitstellung einer zentralen Stelle innerhalb der Organisation für den Austausch über Verfügbarkeit und neueste Änderungen an APIs, Einschränkung des Zugriffs basierend auf Organisationskonten – all dies basierend auf einem sicheren Kanal zwischen API-Gateway und Back-End.


Das System setzt sich aus den folgenden Komponenten zusammen:

* Das **API-Gateway** ist der Endpunkt, der folgende Aufgaben übernimmt:
  * Akzeptieren von API-Aufrufen und Weiterleiten dieser Aufrufe an Ihre Back-Ends
  * Überprüfen von API-Schlüsseln, JWT-Token, Zertifikaten und anderen Anmeldeinformationen
  * Erzwingen von Nutzungskontingenten und Übertragungsratenlimits
  * Dynamisches Transformieren Ihrer API ohne Codeänderungen
  * Zwischenspeichern von Back-End-Antworten, falls eingerichtet
  * Protokollieren von Aufrufmetadaten zu Analysezwecken

* Das **Herausgeberportal** ist die Verwaltungsoberfläche, in der Sie Ihr API-Programm einrichten. Verwenden Sie es zu folgenden Zwecken:
	* Definieren oder Importieren des API-Schemas
	* Paketieren von APIs in Produkten
	* Konfigurieren von Richtlinien wie z. B. Kontingenten oder Transformationen für die APIs
	* Gewinnen von Erkenntnissen mithilfe von Analysen
	* Verwalten von Benutzern

* Das **Entwicklerportal** dient als wichtigste Webpräsenz für Entwickler, in der die folgenden Aufgaben ausgeführt werden können:
	* Lesen der API-Dokumentation
	* Ausprobieren einer API mithilfe der interaktiven Konsole
	* Erstellen eines Kontos und Anfordern von API-Schlüsseln
	* Zugriff auf Nutzungsanalysen


## <a name="create-service-instance"> </a>Erstellen einer API Management-Instanz

>[AZURE.NOTE]Um dieses Lernprogramm abzuschließen, benötigen Sie ein Azure-Konto. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion][].

Der erste Schritt bei der Arbeit mit API Management ist die Erstellung einer Dienstinstanz. Melden Sie sich beim [Azure-Portal][] an, und klicken Sie auf **Neu**, **App Services**, **API Management** und **Erstellen**.

![Neue API Management-Instanz][api-management-create-instance-menu]

Geben Sie unter **URL** einen eindeutigen Unterdomänennamen für die Dienst-URL ein.

Wählen Sie die gewünschten Werte für **Abonnement** und **Region** für Ihre Dienstinstanz aus. Treffen Sie Ihre Auswahl, und klicken Sie auf **Weiter**.

![Neuer API Management-Dienst][api-management-create-instance-step1]

Geben Sie **Contoso Ltd.** unter **Organisationsname** ein, und geben Sie Ihre E-Mail-Adresse in das Feld **Administrator-E-Mail** ein.

>[AZURE.NOTE]Diese E-Mail-Adresse wird für Benachrichtigungen vom API Management-System verwendet. Weitere Informationen finden Sie unter [Konfigurieren von Benachrichtigungen und E-Mail-Vorlagen in Azure API Management][].

![Neuer API Management-Dienst][api-management-create-instance-step2]

API Management-Dienstinstanzen stehen in drei Tarifen zur Verfügung: Developer, Standard und Premium. Standardmäßig werden neue API Management-Dienstinstanzen im Developer-Tarif erstellt. Aktivieren Sie zum Auswählen der Tarife "Standard" oder "Premium" das Kontrollkästchen **Erweiterte Einstellungen**, und wählen Sie auf dem folgenden Bildschirm den gewünschten Tarif.

>[AZURE.NOTE]Der Tarif "Developer" ist für Entwicklung, Tests und API-Pilotprogramme konzipiert, bei denen eine hohe Verfügbarkeit nicht relevant ist. In den Tarifen "Standard" und "Premium" können Sie die Anzahl der reservierten Einheiten skalieren, um mehr Datenverkehr zu verarbeiten. Die Tarife "Standard" und "Premium" bieten für den API Management-Dienst hinsichtlich Verarbeitungsleistung und Durchsatz die besten Ergebnisse. Sie können dieses Tutorial mithilfe eines beliebigen Tarifs abschließen. Weitere Informationen zu den API Management-Tarifen finden Sie unter [API Management-Preise][].

Aktivieren Sie das Kontrollkästchen, um Ihre Dienstinstanz zu erstellen.

![Neuer API Management-Dienst][api-management-instance-created]

Nach der Erstellung der Dienstinstanz können Sie eine API erstellen oder importieren.

## <a name="create-api"> </a>Importieren einer API

Eine API besteht aus einem Satz von Operationen, die aus Clientanwendungen aufgerufen werden können. API-Operationen funktionieren als Proxys für vorhandene Webdienste.

Sie können eine API manuell erstellen (und ihr Operationen hinzufügen), oder Sie importieren eine API. In diesem Tutorial wird die API für einen Rechner-Beispielwebdienst importiert, der von Microsoft bereitgestellt und in Azure gehostet wird.

>[AZURE.NOTE]Anleitungen zur manuellen Erstellung von APIs und zum Hinzufügen von Operationen finden Sie unter [Erstellen von APIs](api-management-howto-create-apis.md) und [Hinzufügen von Operationen zu einer API](api-management-howto-add-operations.md).

APIs werden im Herausgeberportal konfiguriert, das über das Azure-Portal erreichbar ist. Um auf das Herausgeberportal zuzugreifen, klicken Sie im Azure-Portal für Ihren API Management-Dienst auf **Verwalten**.

![Herausgeberportal][api-management-management-console]

Klicken Sie zum Importieren der Rechner-API im Menü **API Management** auf der linken Seite auf **APIs**, und klicken Sie anschließend auf **API importieren**.

![Schaltfläche "API importieren"][api-management-import-api]

Führen Sie die folgenden Schritte aus, um die Rechner-API zu konfigurieren:

1. Klicken Sie auf **Aus URL**, geben Sie ****http://calcapi.cloudapp.net/calcapi.json** in das Textfeld **URL für Spezifikationsdokument** ein, und aktivieren Sie das Optionsfeld **Swagger**.
2. Geben Sie in das Textfeld **URL-Suffix für Web-API** den Wert **calc** ein.
3. Klicken Sie auf das Feld **Produkte (optional)**, und wählen Sie **Starter**.
4. Klicken Sie auf **Speichern**, um die API zu importieren.

![Neue API hinzufügen][api-management-import-new-api]

Sobald die API importiert wurde, wird die Zusammenfassungsseite für die API im Herausgeberportal angezeigt.

![API-Zusammenfassung][api-management-imported-api-summary]

Der API-Bereich umfasst verschiedene Registerkarten. Die Registerkarte **Zusammenfassung** enthält grundlegende Metriken und Informationen über die API. Auf der Registerkarte [Einstellungen](api-management-howto-create-apis.md#configure-api-settings) können Sie die Konfiguration einer API anzeigen und bearbeiten. Mithilfe der Registerkarte [Operationen](api-management-howto-add-operations.md) können Sie die API-Operationen verwalten. Auf der Registerkarte **Sicherheit** können Sie die Proxyauthentifizierung für den Back-End-Server konfigurieren, indem Sie die Standardauthentifizierung oder die [Gegenseitige Zertifikatauthentifizierung](api-management-howto-mutual-certificates.md) festlegen und die [Benutzerautorisierung mithilfe von OAuth 2.0](api-management-howto-oauth2.md) konfigurieren. Die Registerkarte **Probleme** dient zum Anzeigen der Probleme, die von den Entwicklern gemeldet werden, die Ihre APIs verwenden. Auf der Registerkarte **Produkte** werden die Produkte konfiguriert, die diese API enthalten.

Standardmäßig enthält jede API Management-Instanz zwei Beispielprodukte:

-	**Starter**
-	**Unbegrenzt**

In diesem Lernprogramm wurde die Basic Calculator-API zum Starter-Produkt hinzugefügt, als die API importiert wurde.

Um API-Aufrufe ausführen zu können, müssen Entwickler zunächst das Produkt abonnieren, das Zugriff auf die API bietet. Entwickler können die Produkte im Entwicklerportal abonnieren. Administratoren können Entwickler im Herausgeberportal für Produkte abonnieren. Sie sind standardmäßig als Administrator konfiguriert, da Sie die API Management-Dienstinstanz in den vorherigen Schritten des Lernprogramms erstellt haben. Aus diesem Grund haben Sie alle Produkte bereits standardmäßig abonniert.

## <a name="call-operation"> </a>Aufrufen einer Operation aus dem Entwicklerportal

Operationen können direkt aus dem Entwicklerportal aufgerufen werden. Dies ist ein einfacher Weg, um die Operationen einer API anzuzeigen und zu testen. In diesem Schritt des Tutorials rufen Sie die Operation **Add two integers** der API "Basic Calculator" auf. Klicken Sie im Herausgeberportal im Menü oben rechts auf **Entwicklerportal**.

![Entwicklerportal][api-management-developer-portal-menu]

Klicken Sie im obersten Menü auf **APIs** und anschließend auf **Basic Calculator**, um die verfügbaren Operationen anzuzeigen.

![Entwicklerportal][api-management-developer-portal-calc-api]

Gemeinsam mit der API und den Operationen wurden Beispielbeschreibungen und -parameter importiert. Diese bieten Entwicklern eine Dokumentation für die Ausführung dieser Operation. Diese Beschreibungen können auch hinzugefügt werden, wenn Operationen manuell eingefügt werden.

Klicken Sie auf **Try it**, um die Operation **Add two integers** aufzurufen.

![Ausprobieren][api-management-developer-portal-calc-api-console]

Sie können einige Werte für die Parameter eingeben oder die Standardwerte übernehmen. Klicken Sie dann auf **Send**.

![HTTP Get][api-management-invoke-get]

Nach dem Aufruf der Operation zeigt das Entwicklerportal den **Antwortstatus**, die **Antwortheader** sowie den **Antwortinhalt** an.

![Antwort][api-management-invoke-get-response]

## <a name="view-analytics"> </a>Anzeigen von Analysen

Um Analysen für "Basic Calculator" anzuzeigen, wechseln Sie zurück zum Herausgeberportal, indem Sie im Entwicklerportal im Menü oben rechts auf **Verwalten** klicken.

![Verwalten][api-management-manage-menu]

Die Standardansicht für das Herausgeberportal ist das **Dashboard**, das eine Übersicht für Ihre API Management-Instanz enthält.

![Dashboard][api-management-dashboard]

Bewegen Sie den Mauszeiger über die Grafik für den **Basic Calculator**, um spezielle Metriken zur Nutzung der API über einen bestimmten Zeitraum anzuzeigen.

>[AZURE.NOTE]Falls Ihr Diagramm keine Linien enthält, wechseln Sie zurück zum Entwicklerportal, und führen Sie einige Aufrufe an die API aus. Warten Sie einen Moment, und kehren Sie zum Dashboard zurück.

Klicken Sie auf **Details anzeigen**, um die Zusammenfassungsseite für die API anzuzeigen, inklusive einer größeren Version der angezeigten Metriken.

![Analyse][api-management-mouse-over]

![Zusammenfassung][api-management-api-summary-metrics]

Um detaillierte Metriken und Berichte anzuzeigen, klicken Sie auf **Analyse** im Menü **API Management** auf der linken Seite.

![Übersicht][api-management-analytics-overview]

Der Abschnitt **Analyse** enthält die folgenden vier Registerkarten:

-	**Auf einen Blick** enthält Metriken für Nutzung und Integrität sowie eine Liste der aktivsten Entwickler, Produkte, APIs und Operationen.
-	**Nutzung** enthält eine detaillierte Ansicht für API-Aufrufe und Bandbreitennutzung inklusive einer geografischen Darstellung.
-	**Integrität** konzentriert sich auf Statuscodes, Cache-Treffer und Antwortzeiten für APIs und Dienste.
-	**Aktivität** enthält Berichte mit detaillierten Informationen zur Aktivität pro Entwickler, Produkt, API und Operation.

## <a name="next-steps"> </a>Nächste Schritte

-	Lesen Sie die anderen Themen im Lernprogramm [Erste Schritte bei der erweiterten API-Konfiguration][].

[Kostenlose Azure-Testversion]: http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=api_management_hero_a

[Create an API Management instance]: #create-service-instance
[Create an API]: #create-api
[Add an operation]: #add-operation
[Add the new API to a product]: #add-api-to-product
[Subscribe to the product that contains the API]: #subscribe
[Call an operation from the Developer Portal]: #call-operation
[View analytics]: #view-analytics
[Next steps]: #next-steps


[How to manage developer accounts in Azure API Management]: api-management-howto-create-or-invite-developers.md
[Configure API settings]: api-management-howto-create-apis.md#configure-api-settings
[Konfigurieren von Benachrichtigungen und E-Mail-Vorlagen in Azure API Management]: api-management-howto-configure-notifications.md
[Responses]: api-management-howto-add-operations.md#responses
[How create and publish a product]: api-management-howto-add-products.md
[Erste Schritte bei der erweiterten API-Konfiguration]: api-management-get-started-advanced.md
[API Management-Preise]: http://azure.microsoft.com/pricing/details/api-management/

[Azure-Portal]: https://manage.windowsazure.com/

[api-management-management-console]: ./media/api-management-get-started/api-management-management-console.png
[api-management-create-instance-menu]: ./media/api-management-get-started/api-management-create-instance-menu.png
[api-management-create-instance-step1]: ./media/api-management-get-started/api-management-create-instance-step1.png
[api-management-create-instance-step2]: ./media/api-management-get-started/api-management-create-instance-step2.png
[api-management-instance-created]: ./media/api-management-get-started/api-management-instance-created.png
[api-management-import-api]: ./media/api-management-get-started/api-management-import-api.png
[api-management-import-new-api]: ./media/api-management-get-started/api-management-import-new-api.png
[api-management-imported-api-summary]: ./media/api-management-get-started/api-management-imported-api-summary.png
[api-management-calc-operations]: ./media/api-management-get-started/api-management-calc-operations.png
[api-management-list-products]: ./media/api-management-get-started/api-management-list-products.png
[api-management-add-api-to-product]: ./media/api-management-get-started/api-management-add-api-to-product.png
[api-management-add-myechoapi-to-product]: ./media/api-management-get-started/api-management-add-myechoapi-to-product.png
[api-management-api-added-to-product]: ./media/api-management-get-started/api-management-api-added-to-product.png
[api-management-developers]: ./media/api-management-get-started/api-management-developers.png
[api-management-add-subscription]: ./media/api-management-get-started/api-management-add-subscription.png
[api-management-add-subscription-window]: ./media/api-management-get-started/api-management-add-subscription-window.png
[api-management-subscription-added]: ./media/api-management-get-started/api-management-subscription-added.png
[api-management-developer-portal-menu]: ./media/api-management-get-started/api-management-developer-portal-menu.png
[api-management-developer-portal-calc-api]: ./media/api-management-get-started/api-management-developer-portal-calc-api.png
[api-management-developer-portal-calc-api-console]: ./media/api-management-get-started/api-management-developer-portal-calc-api-console.png
[api-management-invoke-get]: ./media/api-management-get-started/api-management-invoke-get.png
[api-management-invoke-get-response]: ./media/api-management-get-started/api-management-invoke-get-response.png
[api-management-manage-menu]: ./media/api-management-get-started/api-management-manage-menu.png
[api-management-dashboard]: ./media/api-management-get-started/api-management-dashboard.png

[api-management-add-response]: ./media/api-management-get-started/api-management-add-response.png
[api-management-add-response-window]: ./media/api-management-get-started/api-management-add-response-window.png
[api-management-developer-key]: ./media/api-management-get-started/api-management-developer-key.png
[api-management-mouse-over]: ./media/api-management-get-started/api-management-mouse-over.png
[api-management-api-summary-metrics]: ./media/api-management-get-started/api-management-api-summary-metrics.png
[api-management-analytics-overview]: ./media/api-management-get-started/api-management-analytics-overview.png
[api-management-analytics-usage]: ./media/api-management-get-started/api-management-analytics-usage.png
[api-management-]: ./media/api-management-get-started/api-management-.png
[api-management-]: ./media/api-management-get-started/api-management-.png

<!---HONumber=Nov15_HO1-->