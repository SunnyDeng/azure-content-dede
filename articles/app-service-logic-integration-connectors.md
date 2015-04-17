<properties 
	pageTitle="BizTalk-Integrations-API-Apps in Microsoft Azure App Service | Azure" 
	description="Erfahren Sie mehr über das Erstellen und Konfigurieren der BizTalk-Integrations-API-Apps; Microservices-Architektur" 
	services="app-service\logic" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="mandia"/>


# BizTalk-Integrations-API-Apps in Microsoft Azure App Service
Microsoft Azure App Service (oder kurz App Service) bietet viele BizTalk-API-Apps, die für die Integration von Umgebungen wichtig sind. Diese API-Apps basieren auf Konzepten und Tools, die in BizTalk Server verwendet werden, aber nun als Teil von Azure App Service verfügbar sind. 

Eine Kategorie dieser API-Apps sind die BizTalk-Integrations-API-Apps. Mithilfe dieser BizTalk-API-Apps können Sie wie bei einer lokalen BizTalk Server-Lösung problemlos Geschäftsregeln hinzuzufügen, XML-Nachrichten transformieren und überprüfen, Flatfiles und JSON-Daten codieren und vieles mehr.  

Diese Integrations-API-Apps bieten Funktionen für "Aktionen". Eine Aktion ist das Ergebnis, wie z. B. der Überprüfung einer eingegangenen XML-Nachricht im Abgleich mit einem XML-Schema.


## Was sind Integrations-API-Apps?
BizTalk-Integrations-API-Apps sind vorhandene, vorab erstellte API-Apps für die Datenverarbeitung und Ausgabegenerierung. Einige dieser API-Apps ermöglichen beispielsweise das Zusammenarbeiten verschiedener Dateiformate, und einige wenden Geschäfts- oder Anwendungslogik an. Zu den Integrations-API-Apps gehören u. a.: 

API-Apps | Beschreibung
--- | ---
<ul><li>X12-API-App</li><li>AS2-Connector</li><li>EDIFACT-API-App</li><li>Trading Partner Management-API-App</li> | Diese API-Apps bieten Business-to-Business-Funktionen (B2B). [B2B-Connectors](app-service-logic-b2b-connectors.md) bietet weitere Details zu diesen API-Apps.
BizTalk Flat File Encoder | Eine API-App vom Typ **Aktion**. Ermöglicht die Zusammenarbeit von Flatfiledaten (wie Excel, CSV) und XML-Daten (Interoperabilität). Sie können damit eine Flatfile-Instanz in XML konvertieren und umgekehrt.
BizTalk JSON Encoder | Eine API-App vom Typ **Aktion**. Ermöglicht das Zusammenarbeiten von JSON- und XML-Daten (Interoperabilität). Sie können damit eine JSON-Instanz in XML konvertieren und umgekehrt.
BizTalk-Regeln | Eine API-App vom Typ **Aktion**. Sie können Geschäftslogik oder "Regeln" implementieren und anwenden, um einen Geschäftsvorgang zu steuern. Regeln sind dynamisch und können sich jederzeit ändern. BizTalk-Regeln ermöglichen Benutzern, die Geschäftslogik zu aktivieren, ohne Code zu schreiben, und können ohne Auswirkungen auf Anwendungen einfach aktualisiert werden.
BizTalk-Transformation | Eine API-App vom Typ **Aktion**. Konvertiert Daten von einem Format in ein anderes. Sie können verschiedene integrierte Funktionen zum Bearbeiten von Zeichenfolgen, Vervollständigen arithmetischer Ausdrücke, Formatieren von Datum und Uhrzeit usw. verwenden. 
BizTalk XML Validator | Eine API-App vom Typ **Aktion**. Überprüft XML-Daten im Abgleich mit vordefinierten XML-Schemas. Sie können   Schemas basierend auf Flatfile-Instanzen, JSON-Instanzen oder vorhandenen Connectors verwenden. 
BizTalk XPath Extractor | Eine API-App vom Typ **Aktion**. Dient zum Suchen und Extrahieren von Daten aus XML-Inhalten basierend auf einem bestimmten XPath.
Warten |  Verzögert die Ausführung für einen Zeitraum, den Sie eingeben, oder bis zu einem bestimmten Zeitpunkt. Falls einer Logik-App hinzugefügt, kann der Connector zum Verzögern der Ausführung der gesamten Anwendung verwendet werden.

> [AZURE.NOTE] Wenn die Eingabe-XML einen einfachen Knoten mit einem Attribut aufweist (wie "<authorid= "1">abc</author>"), dann ist die JSON-Ausgabe der Bibliothek {"author": {"@id" : "1", "#text": "abc"}}. Zum Verarbeiten des Attributs "Id" wird ein neuer "#text"-Schlüssel für den Textinhalt des Knotens hinzugefügt. Um diese Art von Knoten zu verarbeiten, fügen Sie einen Konstantenschlüssel hinzu. Dies ist standardmäßig die "Newtonsoft.Json"-Bibliothek. Wenn Sie dies in SQL einfügen, verwenden Sie "JSONOutput.Author.#text" und nicht "JsonOutput.Author".

Mithilfe dieser API-Apps können Sie verschiedene Messaging- oder Datenaufgaben meistern. Mithilfe der BizTalk-Regel-API-App können Sie z. B. eine Bestellung empfangen und einen Rabatt anwenden, sobald eine bestimmte Menge bestellt wird. Oder Sie können je nach Postleitzahl einen bestimmten Steuersatz berechnen. 

Sie können beliebig viele API-Apps ohne großen Aufwand erstellen. Sie können API-Apps auch in mehreren Szenarien oder Workflows wiederverwenden.

Angenommen, es gilt eine Unternehmensrichtlinie, gemäß der ein Rabatt von 10 % gilt, wenn ein Kunde 100 Artikel Ihres Produkts bestellt. In Ihrer App können Sie die BizTalk-Regel-API-App hinzufügen, die die Bestellmenge prüft und bei mehr als 100 den 10 %-igen Rabatt anwendet. 

Sie können diese Unternehmensrichtlinie auch erweitern. Angenommen, Ihr Ziel ist die Umsatzsteigerung in Österreich. Zusätzlich zu den 10 % Rabatt für Bestellungen von mindestens 100 Artikeln können Sie auch einen vergünstigten Versand anbieten, wenn die Bestellung aus Österreich stammt. Sie können diese auf Österreich bezogene Bedingung problemlos Ihrer vorhandenen BizTalk-Regel hinzufügen. 

Sie erreichen dies über diese API-Apps und ohne das Schreiben von Code. Lassen Sie uns anfangen. 


## Erstellen der API-Apps
Integrations-API-Apps können im Azure-Portal oder mithilfe von REST-APIs erstellt werden. 


### Erstellen von API-Apps mithilfe von REST-APIs
http://go.microsoft.com/fwlink/p/?LinkId=529766


### Erstellen von Integrations-API-Apps im Azure-Portal
Im Azure-Portal können Sie beim Erstellen der Logik-Apps, Web-Apps oder mobilen Apps BizTalk-Integrations-API-Apps erstellen. Sie können ihn auch auf einem eigenen Blatt erstellen. Beide Möglichkeiten sind einfach, weshalb der Vorgang von Ihren Anforderungen oder Einstellungen abhängt. Es gibt Benutzer, die lieber zuerst alle Integrations-API-Apps mit ihren bestimmten Eigenschaften erstellen. Anschließend erstellen sie die Logik-, Web- oder mobilen Apps und fügen die Integrations-API-Apps hinzu, die Sie erstellt haben.  

Über die folgenden Schritte erstellen Sie BizTalk-Integrations-API-Apps auf dem Blatt "API-Apps":

1. Wählen Sie im Azure-Portal im Startmenü (auf der Startseite) **Marketplace** aus. **API-Apps** zeigt alle vorhandenen API-Apps und Connectors. Sie können eine bestimmte BizTalk-API-App auch **suchen**.
2. Wählen Sie die API-App aus. Wählen Sie im neuen Blatt **Erstellen** aus. 
3. Geben Sie die Eigenschaften ein: 

	Eigenschaft | Beschreibung
--- | ---
Name | Geben Sie für Ihre API-App einen beliebigen Namen ein. Sie können sie z. B.  *RulesDiscountTaxCode* oder  *APIAppValidateXML* nennen.
App Service-Plan | Listet Ihren Zahlungsplan auf. Sie können ihn ändern, wenn Sie mehr oder weniger Ressourcen benötigen.
Preisstufe | Schreibgeschützte Eigenschaft, die die Preiskategorie innerhalb Ihres Azure-Abonnements auflistet. 
Ressourcengruppe | Erstellen Sie eine neue oder verwenden Sie eine vorhandene Gruppe. Alle API-Apps und Connectors für Ihre Logik- Apps, Web-Apps und mobilen Apps müssen in derselben Ressourcengruppe enthalten sein. <br/><br/>Unter [Verwenden von Ressourcengruppen](azure-preview-portal-using-resource-groups.md) wird diese Eigenschaft erläutert. 
Abonnement | Schreibgeschützte Eigenschaft, die Ihr aktuelles Abonnement auflistet.
Standort | Der geografische Standort, an dem Ihr Azure-Dienst gehostet wird. 
Zum Startmenü hinzufügen | Wählen Sie diese Option aus, um die Integrations-API-Apps Ihrem Startmenü (der Startseite) hinzufügen.


## Konfigurieren der BizTalk-API-Apps
Öffnen Sie im Azure-Verwaltungsportal Ihre BizTalk-API-App. Im Abschnitt **Komponenten** können Sie die zusätzlichen Komponenten hinzufügen, die zum Vervollständigen der API-App erforderlich sind: 

	API-App | Aufgaben
--- | ---
BizTalk Flat File Encoder | Geben Sie eine Flatfile ein, z. B. eine Excel- oder CSV-Datei, die in XML umgewandelt werden soll. Oder geben Sie eine XML-Datei ein, die in eine Flatfile umgewandelt werden soll.
BizTalk JSON Encoder | Geben Sie eine JSON-Datei ein, die in XML konvertiert werden soll. Oder geben Sie eine XML-Datei, die in JSON konvertiert werden soll. 
BizTalk-Regeln | Fügen Sie Ihre Vokabulare hinzu, und erstellen Sie IF THEN-Regeln. Siehe [Verwenden von BizTalk-Regeln](app-service-logic-use-biztalk-rules.md). 
BizTalk-Transformation | Fügen Sie eine Zuordnung hinzu, und geben Sie ein XML-Eingabeschema und XML-Ausgabeschema ein. Verwenden Sie die integrierten Funktionen, um eingehende Nachrichten oder Daten entsprechend Ihrem XML-Ausgabeschema zu bearbeiten. Siehe [Transformieren von XML-Dokumenten](app-service-logic-transform-xml-documents.md). 
BizTalk XML Validator | Geben Sie den XML-Code ein, der im Abgleich mit einem vordefinierten XML-Schema überprüft werden soll. Sie können Schemas basierend auf Flatfile-Instanzen, JSON-Instanzen oder vorhandenen Connectors nutzen. 
BizTalk XPath Extractor | Dient zum Suchen und Extrahieren von Daten aus XML-Inhalten basierend auf einem bestimmten XPath.
Warten |  Geben Sie einen Zeitraum oder bestimmten Zeitpunkt für die Ausführung von Web-Apps, mobilen Apps oder Logik-Apps ein.


## Überwachen Ihrer API-Apps
Öffnen Sie im Azure-Verwaltungsportal Ihre BizTalk-API-App. Im Abschnitt **Vorgänge** können Sie verschiedene Verwaltungsvorgänge anzeigen. Dazu zählen z. B.:

- Anzeigen von Informations- und Fehlerereignissen
- Anzeigen der Speicherauslastung und Threadanzahl des Arbeitsprozesses (w3wp)
- Anzeigen des Anwendungs- und Webserverprotokolls

Unter [Überwachen von Logik-Apps](app-service-logic-monitor-your-logic-apps.md) finden Sie weitere Informationen.


## Hinzufügen der BizTalk-API-Apps zu Ihrer Anwendung 
Microsoft Azure App Service stellt verschiedene Anwendungstypen zur Verfügung, die diese Integrations-API-Apps verwenden können. Sie können neue erstellen oder vorhandene BizTalk-API-Apps verwenden und diese Logik-Apps, mobilen Apps oder Web-Apps hinzufügen.

Wenn Sie in Ihrer App die BizTalk-API-Apps in der Galerie auswählen, werden sie automatisch Ihrer App hinzugefügt.

Über die folgenden Schritte werden BizTalk-API-Apps Logik-Apps, mobilen Apps oder Web-Apps hinzugefügt: 

1. Wechseln Sie im Azure-Portal im Startmenü (auf der Startseite) zu **Marketplace**, und suchen Sie Ihre Logik-Apps, mobilen Apps oder Web-Apps. 

	Wenn Sie eine neue Anwendung erstellen, suchen Sie nach "Logik-App", "Mobile App" oder "Web-App". Wählen Sie die App im neuen Blatt aus, und klicken Sie auf **Erstellen**. [Erstellen einer Logik-App](app-service-logic-create-a-logic-app.md) erläutert die Schritte. 

2. Öffnen Sie Ihre App, und wählen Sie **Trigger und Aktionen** aus. 

3. Fügen Sie über die **Galerie** die von Ihnen erstellte BizTalk-API-App hinzu, die automatisch Ihrer App hinzugefügt wird. 

4. Klicken Sie zum Speichern der Änderungen auf **OK**.


## Weitere Ressourcen zu Integrations-API-Apps
[Erstellen einer EAI-Logik-App mithilfe von VETR](app-service-logic-create-EAI-logic-app-using-VETR.md)<br/>
[Transformieren von XML-Dokumenten](app-service-logic-transform-xml-documents.md)<br/>
[Verwenden von BizTalk-Regeln](app-service-logic-use-biztalk-rules.md)<br/>
[Was sind Connectors und BizTalk-API-Apps?](app-service-logic-what-are-biztalk-api-apps.md)


## Weitere Informationen zu Logik- und Web-Apps
[Was sind Logik-Apps??](app-service-logic-what-are-logic-apps.md)<br/>
[Websites und Web-Apps in Azure App Service](app-service-web-app-azure-portal.md)


## Weitere Connectors
[Enterprise-Connectors](app-service-logic-enterprise-connectors.md)<br/>
[Business-to-Business-Connectors](app-service-logic-b2b-connectors.md)<br/>
[Connectors für soziale Netzwerke](app-service-logic-social-connectors.md)<br/>
[Protokollconnectors](app-service-logic-protocol-connectors.md)<br/>
[App- und Data Services-Connectors](app-service-logic-data-connectors.md)<br/>
[Liste mit Connectors und API-Apps](app-service-logic-connectors-list.md)

<!--HONumber=49-->