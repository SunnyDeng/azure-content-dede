<properties
	pageTitle="Verwalten Sie Ihre erste API in Azure API Management"
	description="Erfahren Sie mehr über das Erstellen von APIs und Vorgängen sowie über die ersten Schritte mit der API-Verwaltung."
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
	ms.topic="article"
	ms.date="06/16/2015"
	ms.author="sdanie"/>

# Verwalten Sie Ihre erste API in Azure API Management

## <a name="overview"> </a>Übersicht

Diese Anleitung beschreibt die ersten Schritte mit der API-Verwaltung und Ihren ersten API-Aufruf.

## <a name="concepts"> </a>Was Azure API Management ist?

Azure können-API Sie alle Back-End-und ein Programm starten, vollwertige API basieren.

Allgemeine Szenarien:

* **Sichern von mobilen Infrastruktur** durch gating Zugriff mit API-Schlüssel, verhindern von DOS-Angriffen Drosselung oder mithilfe von Erweiterte Sicherheitsrichtlinien, z. B. die Überprüfung von JWT-token
* **Aktivieren der ISV-Partner Ökosysteme** bietet schnelle Partner Onboarding über das Entwicklerportal und Erstellen einer Fassade API, von einer internen Implementierungen Partner-Nutzung nicht unbedingt zu entkoppeln.
* **Eine interne API Programmausführung** durch bietet einen zentralen Ort für die Organisation in Bezug auf die Verfügbarkeit und die neuesten Änderungen an APIs kommunizieren, gating auf der Grundlage der Organisations-Konten basieren alle auf einen sicheren Kanal zwischen den API-Gateway und der Back-End-


Das System ist erfundenen aus den folgenden Komponenten:

* Die **-API-Gateway** ist der Endpunkt, der:
  * API-Aufrufe und leitet sie an die Back-Ends akzeptiert
  * API-Schlüssel, JWT-Tokens, Zertifikate und anderer Anmeldeinformationen überprüft
  * Erzwingt die Kontingente und Begrenzung der Datenübertragungsrate
  * Wandelt die API im Handumdrehen ohne Codeänderungen
  * Back-End-Antworten zwischengespeichert Setupprogramm
  * Protokolle rufen Sie Metadaten für die Analyse

* Die **Publisher Portal** ist die Verwaltungsschnittstelle, in dem Sie Setup API-Programm:
	* Definieren oder API-Schema importieren
	* Paket-APIs in Produkte
	* Einrichtung von Richtlinien wie Kontingente oder Transformationen auf die APIs
	* erhalten Sie Einblicke aus der Analyse
	* Verwalten von Benutzern

* Die **-Entwicklerportal** dient als die wichtigsten Webpräsenz für Entwickler können sie hier:
	* API-Dokumentation lesen
	* Probieren Sie eine API über die interaktive Konsole
	* Erstellen eines Kontos und kostenlos für API-Schlüssel
	* Analyse der Zugriff auf eigene Verwendung


## <a name="create-service-instance"> </a>Erstellen Sie eine Instanz der API-Verwaltung

> Um dieses Lernprogramm abzuschließen, benötigen Sie ein Azure-Konto. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion][].

Der erste Schritt bei der Arbeit mit der API-Verwaltung ist die Erstellung einer Dienstinstanz. Melden Sie sich beim [Azure-Verwaltungsportal][] an und klicken Sie auf **Neu**, **App-Dienste**, **API-Verwaltung**, **Erstellen**.

![Neue Instanz der API-Verwaltung][api-management-create-instance-menu]

Geben Sie unter **URL** einen eindeutigen Unterdomänennamen für die Dienst-URL ein.

Wählen Sie die gewünschten **Abonnement** und **Region** für die Dienstinstanz. Treffen Sie Ihre Auswahl und klicken Sie auf Weiter.

![Neuer API-Verwaltungs-Dienst][api-management-create-instance-step1]

Geben Sie **Contoso Ltd.** unter **Organisationsname** ein und geben Sie Ihre E-Mail-Adresse in das Feld Administrator-E-Mail ein.

>Diese E-Mail-Adresse wird für Benachrichtigungen vom API-Verwaltungssystem verwendet. Weitere Informationen finden Sie unter [Benachrichtigungen konfigurieren][].

![Neuer API-Verwaltungs-Dienst][api-management-create-instance-step2]

API Management Service-Instanzen sind in drei Ausführungen verfügbar: Developer, Standard und Premium. Standardmäßig werden neue Dienstinstanzen der API-Verwaltung mit der Entwickler-Stufe erstellt. Überprüfen Sie die Standard- oder Premium-Dienstebene zu aktivieren, die **Erweiterte Einstellungen** Kontrollkästchen, und wählen Sie die gewünschte Ebene auf dem folgenden Bildschirm.

>Microsoft Azure bietet drei Stufen, in dem Sie Ihre API Management-Dienst ausführen können: Developer, Standard und Premium. Die Entwickler-Stufe ist für Entwicklung, Tests und API-Pilotprogramme konzipiert, bei denen eine hohe Verfügbarkeit nicht relevant ist. In den Standard- und Premium-Ebenen können Sie die Anzahl der reservierten Einheiten um mehr Datenverkehr verarbeiten skalieren. Die Standard- und Premium-Ebene bieten Ihre API Management-Dienst die größte Verarbeitungsleistung und Leistung. In diesem Lernprogramm kann mit einer beliebigen Ebene ausgeführt werden. Weitere Informationen zu API Management-Ebenen, finden Sie unter [API Management Preise][].

Aktivieren Sie das Kontrollkästchen, um Ihre Dienstinstanz zu erstellen.

![Neuer API-Verwaltungs-Dienst][api-management-instance-created]

Nachdem die Instanz erstellt wurde, besteht der nächste Schritt zum Erstellen oder Importieren einer API.

## <a name="create-api"> </a>Importieren einer API

Eine API besteht aus einem Satz von Operationen, die von Clientanwendungen aufgerufen werden können. API-Operationen funktionieren als Proxies für existierende Webdienste.

APIs können erstellt werden und Vorgänge manuell hinzugefügt oder importiert werden. In diesem Lernprogramm importieren wir eine die API für eine Beispiel-Rechner Web Service von Microsoft bereitgestellt und auf Azure gehostet.

>Anweisungen zum Erstellen einer API und Vorgänge manuell hinzufügen, finden Sie unter [APIs erstellen](api-management-howto-create-apis.md) und [zum Hinzufügen von Vorgängen zu einer API](api-management-howto-add-operations.md).

APIs werden vom Verleger API-Portal konfiguriert die über Azure-Verwaltungsportal zugegriffen wird. Um die Verleger-Portal zu erreichen, klicken Sie auf **verwalten** in Azure-Portal für Ihre API Management-Dienst.

![Herausgeberportal][api-management-management-console]

Um den API-Rechner zu importieren, klicken Sie auf **APIs** aus der **API Management** im Menü auf der linken Seite, und klicken Sie dann auf **API importieren**.

![Schaltfläche "Importieren" API "][api-management-import-api]

![Neue API hinzufügen][api-management-import-new-api]

Führen Sie die folgenden Schritte aus, um den Rechner API zu konfigurieren.

1. Klicken Sie auf **From URL**, geben Sie **http://calcapi.cloudapp.net/calcapi.json** in die **Spezifikation Dokument-URL** Textfeld, und klicken Sie auf die **Swagger** Optionsfeld. 2. Typ **Calc** in den **Web-API-URL-Suffix** Textbox.
3. Klicken Sie in der **Produkte (optional)** und wählen Sie **Starter**.
4. Klicken Sie auf **Speichern** die API zu importieren.

Nachdem die API importiert wurde, wird die Zusammenfassungsseite für die API im Portal Verleger angezeigt.

![API-Zusammenfassung][api-management-imported-api-summary]

Der API-Abschnitt über mehrere Registerkarten verfügt. Die Registerkarte **Zusammenfassung** enthält verschiedene Metriken und Informationen über die API. Die [Einstellungen](api-management-howto-create-apis.md#configure-api-settings) Registerkarte dient zum Anzeigen und Bearbeiten der Konfiguration einer API. Die [Operations](api-management-howto-add-operations.md) Registerkarte wird verwendet, um die API-Vorgänge zu verwalten. Die **Security** Registerkarte kann verwendet werden, um die Proxy-Authentifizierung für den Back-End-Server mit der Standardauthentifizierung konfigurieren oder [gegenseitige Zertifikatauthentifizierung](api-management-howto-mutual-certificates.md), und so konfigurieren Sie [Benutzerautorisierung mit OAuth 2.0](api-management-howto-oauth2.md). Die dem **Probleme** Registerkarte dient zum Anzeigen der Probleme, die von den Entwicklern, die mithilfe der APIs und die **Produkte** Registerkarte wird verwendet, um die Produkte zu konfigurieren, die diese API enthalten.

Standardmäßig enthält jede API-Verwaltungsinstanz zwei Beispielprodukte:

-	**Starter**
-	**Unbegrenzt**

In diesem Lernprogramm wurde die grundlegende Calculator-API beim Import der API des Starter-Produkts hinzugefügt.

Um eine API aufgerufen werden, müssen Entwickler zunächst ein Produkt abonnieren, die sie auf ihn zugreifen können. Entwickler können Produkte im Entwicklerportal abonnieren oder Administratoren können Entwickler Produkte in der Publisher-Portal anmelden. Sie sind Administrator, da die API Management-Instanz in den vorherigen Schritten im Lernprogramm erstellt werden, damit Sie bereits standardmäßig jedes Produkt abonniert haben.

## <a name="call-operation"> </a>Aufrufen eines Vorgangs aus dem Entwicklerportal

Operationen können direkt aus dem Entwicklerportal aufgerufen werden. Dies ist ein einfacher Weg, um die Operationen einer API anzuzeigen und zu testen. In diesem Lernprogramm Schritt rufen Sie die **grundlegende Rechner** -APIs **Hinzufügen von zwei Ganzzahlen** Vorgang. Klicken Sie auf **-Entwicklerportal** aus dem Menü oben rechts auf der Publisher-Portal.

![Entwicklerportal][api-management-developer-portal-menu]

Klicken Sie auf **APIs** aus der oberen Menü, und klicken Sie dann auf **grundlegende Rechner** um die verfügbaren Vorgänge anzuzeigen.

![Entwicklerportal][api-management-developer-portal-calc-api]

Beachten Sie die Beispiel-Beschreibungen und die Parameter, die importiert wurden, sowie die API und Vorgänge, Bereitstellen von Dokumentation für die Entwickler, die diesen Vorgang verwenden. Diese Beschreibungen können auch hinzugefügt werden, wenn Vorgänge manuell hinzugefügt werden.

Aufrufen der **Hinzufügen von zwei Ganzzahlen** Vorgang, klicken Sie auf **versuchen Sie es**.

![Probieren Sie es][api-management-developer-portal-calc-api-console]

Sie können einige Werte für die Parameter eingeben oder übernehmen Sie die Standardeinstellungen, und auf **Senden**.

![HTTP Get][api-management-invoke-get]

Nachdem ein Vorgang aufgerufen wird, zeigt das Entwicklerportal der **Antwortstatus**, der **Antwortheader**, und alle **Antwortinhalt**.

![Antwort][api-management-invoke-get-response]

## <a name="view-analytics"> </a>Analysen anzeigen

Analysen für anzeigen **grundlegende Rechner**, wechseln Sie zurück zum Verleger Portal durch Auswahl **verwalten** aus dem Menü oben rechts neben das Entwicklerportal.

![Verwalten][api-management-manage-menu]

Die Standardansicht für das Portal Verleger ist die **Dashboard**, die Übersicht über die API Management-Instanz bereitstellt.

![Dashboard][api-management-dashboard]

Richten Sie den Mauszeiger über dem Diagramm für **grundlegende Rechner** die Metriken für die Verwendung der API für einen bestimmten Zeitraum angezeigt.

>Wenn alle Zeilen im Diagramm nicht angezeigt wird, wechseln Sie zurück zum Entwicklerportal einige Aufrufe in der API, warten Sie einige Minuten und kehre dann zum Dashboard.

![Analyse][api-management-mouse-over]

Klicken Sie auf **Details anzeigen**, um die Zusammenfassungsseite für die API anzuzeigen, inklusive einer größeren Version der angezeigten Metriken.

![Zusammenfassung][api-management-api-summary-metrics]

Um detaillierte Metriken und Berichte anzuzeigen, klicken Sie auf **Analyse** im Menü **API-Verwaltung** auf der linken Seite.

![Übersicht][api-management-analytics-overview]

Der **Analyse**-Bereich enthält die folgenden vier Registerkarten.

-	**Auf einen Blick** enthält Metriken für Nutzung und Integrität sowie eine Liste der aktivsten Entwickler, Produkte, APIs und Operationen.
-	**Nutzung** enthält detaillierte Ansichten für API-Aufrufe und Bandbreitennutzung inklusive einer geografischen Darstellung.
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
[Benachrichtigungen konfigurieren]: api-management-howto-configure-notifications.md
[Responses]: api-management-howto-add-operations.md#responses
[How create and publish a product]: api-management-howto-add-products.md
[Erste Schritte bei der erweiterten API-Konfiguration]: api-management-get-started-advanced.md
[API Management Preise]: http://azure.microsoft.com/pricing/details/api-management/

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/

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
 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->