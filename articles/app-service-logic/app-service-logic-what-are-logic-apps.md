<properties 
	pageTitle="Was sind Logik-Apps?" 
	description="Weitere Informationen zu App Service-Logik-Apps" 
	authors="kevinlam1" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article" 
	ms.date="03/01/2016"
	ms.author="klam"/>

#Was sind Logik-Apps?

| Kurzübersicht |
| --------------- |
| [Logik-Apps – Definitionssprache](https://msdn.microsoft.com/library/azure/mt643789.aspx) |
| [Logik-Apps Managed API Documentation](https://azure.microsoft.com/documentation/articles/apis-list) (Verwaltete API für Logik-Apps – Dokumentation) |
| [Logik-Apps – Forum](https://social.msdn.microsoft.com/Forums/home?forum=azurelogicapps) |

Azure App Service ist eine vollständig verwaltete PaaS-Lösung (Platform-as-a-Service) für Entwickler, die das Erstellen von Web-Apps, mobilen Apps und Integrations-Apps vereinfacht. Logik-Apps sind ein Teil dieser Produktfamilie. Sie ermöglichen es technisch orientierten Benutzern und Entwicklern, die Ausführung von Geschäftsprozessen und Workflows zu automatisieren. Die praktische Umsetzung erfolgt mithilfe eines leicht zu bedienenden visuellen Designers.

Außerdem können Logik-Apps mit integrierten [verwalteten APIs][managedapis] kombiniert werden, um selbst schwierige Integrationsszenarios einfach zu meistern:

![Datenfluss-App-Designer](./media/app-service-logic-what-are-logic-apps/LogicAppCapture2.png)

Wie erwähnt können Sie mit Logik-Apps Geschäftsprozesse automatisieren. Hier sind einige Beispiele:
 
* Sie können z. B. neue Datensätze in Ihrer SQL-Datenbank automatisch replizieren und per E-Mail an die Mitarbeiter mit Kundenkontakt senden.   
* Sie können automatisch negative Tweets suchen und an einen Pufferkanal senden.

Ähnliche Szenarios können über den visuellen Designer konfiguriert werden, ohne eine einzige Codezeile schreiben zu müssen. Erste Schritte zum sofortigen [Erstellen Ihrer Logik-App][create].

## Gründe für Logik-Apps

Mit Logik-Apps können Entwickler Workflows entwerfen, die mit einem Trigger beginnen und dann eine Reihe von Schritten ausführen. Bei jedem Schritt wird eine API aufgerufen, und zugleich werden eine sichere Authentifizierung sowie die Einhaltung bewährter Methoden wie Prüfpunktausführung und beständige Ausführung gewährleistet.

Wenn Sie einen beliebigen Geschäftsprozess automatisieren möchten (z. B. negative Tweets finden und an Ihren internen Pufferkanal senden oder neue Kundendatensätze aus SQL gleich bei ihrem Eintreffen in das CRM-System replizieren), können Sie mit Logik-Apps ganz leicht verschiedene Datenquellen integrieren, ob Cloud oder lokal. Weitere Inspiration erhalten Sie durch unsere [verwalteten APIs][managedapis]. Führen Sie jetzt die [ersten Schritte][create] durch, um Ihre Möglichkeiten kennenzulernen.

Mit unseren [verwalteten BizTalk-APIs][biztalk] können Sie darüber hinaus ausgereifte Integrationsszenarios erstellen – dank [Regelmodul][rules], [Handelspartnerverwaltung][tpm] und vielem mehr.

- **Leicht zu bedienende Entwurfstools** – Logik-Apps können von A bis Z im Browser entwickelt werden. Beginnen Sie mit einem Trigger - von einem einfachen Zeitplan bis zum Auftauchen eines Tweets zu Ihrem Unternehmen. Orchestrieren Sie anschließend eine beliebige Anzahl von Aktionen mithilfe des umfassenden Angebots an Connectors.

- **Einfaches Zusammensetzen von SaaS** – Zusammensetzungsaufgaben, die einfach zu beschreiben sind, lassen sich nur schwierig in Code implementieren. Mit Logik-Apps ist es kinderleicht, verschiedenartige Systeme miteinander zu verbinden. Möchten Sie in Ihrer CRM-Software eine Aufgabe erstellen, die auf Aktivität in Ihren Facebook- oder Twitter-Konten basiert? Wollen Sie Ihre Marketinglösung in der Cloud mit Ihrem lokalen Abrechnungssystem verbinden? Logik-Apps sind die schnellste und zuverlässigste Möglichkeit, Lösungen für diese Probleme bereitzustellen.

- **Schneller Einstieg mit Vorlagen** – Um Ihnen den Einstieg zu erleichtern, bieten wir einen [Katalog mit Vorlagen][templates], mit deren Hilfe Sie einige allgemeine Lösungen rasch erstellen können. Von BizTalk-Lösungen bis zu einfachen SaaS-Konnektivitätslösungen und sogar einigen, die "just for fun" sind – der Katalog ist die schnellste Möglichkeit, sich mit der Leistungsfähigkeit von Logik-Apps vertraut zu machen.

- **Integrierte Erweiterbarkeit:** Sie finden die benötigte API nicht? Logik-Apps sind für das Arbeiten mit API-Apps ausgelegt. Sie können problemlos eine eigene API-App entwickeln und anschließend als benutzerdefinierte API nutzen. Entwickeln Sie eine neue App nur für sich allein, oder veröffentlichen Sie sie im Marketplace, um sie kommerziell zu verwerten.

- **Hohes Integrationspotenzial** – Fangen Sie einfach an, und wachsen Sie Ihrem Bedarf entsprechend. Logik-Apps können problemlos die Leistungsstärke von BizTalk, der branchenführenden Integrationslösung von Microsoft nutzen, damit Integrationsexperten die Lösungen erstellen können, die sie brauchen. Erfahren Sie mehr über die [von Logik-Apps bereitgestellten BizTalk-Funktionen][biztalk].

## Logik-App – Konzepte

Es folgt eine Beschreibung einiger der wichtigsten Elemente von Logik-Apps.

- **Workflow** – Logik-Apps bieten eine grafische Möglichkeit zum Abbilden Ihrer Geschäftsprozesse als Folge von Schritten bzw. Workflow.
- **Verwaltete APIs:** Ihre Logik-Apps benötigen Zugriff auf Daten und Dienste. Ihr Zweck ist, Ihnen beim Herstellen von Verbindungen und Arbeiten mit Ihren Daten zu helfen. Die Liste der derzeit verfügbaren APIs finden Sie unter [Verwaltete APIs][managedapis].
- **Trigger:** Einige verwaltete APIs können auch als Trigger fungieren. Ein Trigger startet eine neue Instanz eines Workflows basierend auf einem bestimmten Ereignis, z. B. Eingang einer E-Mail oder einer Änderung in Ihrem Azure Storage-Konto.
-  **Aktionen** – Jeder Schritt nach dem Trigger in einem Workflow wird als Aktion bezeichnet. Jede Aktion wird in der Regel einem Vorgang in Ihren verwalteten oder benutzerdefinierten API-Apps zugeordnet.
- **BizTalk:** Für anspruchsvollere Integrationsszenarios bieten Logik-Apps Funktionen von BizTalk. BizTalk ist die branchenführende Integrationsplattform von Microsoft. Mit BizTalk-API-Apps können Sie z. B. Validierungen, Transformationen und Regeln auf einfache Weise in Ihre Logik-App-Workflows einbinden. Unter [Was sind BizTalk-API-Apps?][biztalk] finden Sie weitere Informationen.

## Erste Schritte

Befolgen Sie zum Einstieg in Logik-Apps das Lernprogramm [Erstellen einer Logik-App][create].

Weitere Informationen zur Azure App Service-Plattform finden Sie unter [Azure App Service][appservice].

[biztalk]: app-service-logic-what-are-biztalk-api-apps.md
[appservice]: ../app-service/app-service-value-prop-what-is.md
[create]: app-service-logic-create-a-logic-app.md
[managedapis]: ../connectors/apis-list.md
[tpm]: app-service-logic-create-a-trading-partner-agreement.md
[rules]: app-service-logic-use-biztalk-rules.md
[templates]: app-service-logic-use-logic-app-templates.md

<!---HONumber=AcomDC_0309_2016-->