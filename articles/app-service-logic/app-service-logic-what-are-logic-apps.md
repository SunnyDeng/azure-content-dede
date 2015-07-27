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
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/08/2015"
	ms.author="klam"/>

#Was sind Logik-Apps?

Azure App Service ist eine vollständig verwaltete PaaS-Lösung (Platform-as-a-Service) für Entwickler, die das Erstellen von Web-, mobilen und Integrations-Apps vereinfacht. Logik-Apps sind ein Teil dieser Produktfamilie und ermöglichen technisch orientierten Benutzern und Entwicklern das Automatisieren der Ausführung von Geschäftsprozessen und des Workflows mithilfe eines leicht zu bedienenden visuellen Designers.

Außerdem können Logik-Apps mit [Connectors][connectors] aus unserem Marketplace kombiniert werden, um selbst schwierige Integrationsszenarien einfach zu meistern.

![Datenfluss-App-Designer](./media/app-service-logic-what-are-logic-apps/Designer.png)

Wenn Sie automatisch neue Datensätze in Ihrer SQL-Datenbank replizieren und per E-Mail an den Empfang senden möchten oder automatisch negative Tweets finden und an einen Pufferkanal senden möchten

##Gründe für Logik-Apps

Logik-Apps ermöglichen Entwicklern das Entwerfen von Workflows, die bei einem Trigger beginnen und dann eine Reihe von Schritten ausführen. Bei jedem Schritt wird eine App Service-API-App aufgerufen, und zugleich werden eine sichere Authentifizierung und Best Practices wie Prüfpunktausführung und beständige Ausführung gewährleistet.

Wenn Sie einen beliebigen Geschäftsprozess automatisieren möchten (z. B. negative Tweets finden und an Ihren internen Pufferkanal senden oder neue Kundendatensätze aus SQL gleich bei ihrem Eintreffen in das CRM-System replizieren), können Sie mit Logik-Apps ganz leicht verschiedene Datenquellen integrieren, ob Cloud oder lokal. Weitere Inspiration erhalten Sie durch unsere [Connectors][connectors]. Führen Sie jetzt die [ersten Schritte][create] durch, um Ihre Optionen kennen zu lernen.

Mit unseren [BizTalk-API][biztalk]-Apps können Sie darüber hinaus ausgereifte Integrationsszenarien erstellen – dank [Regelmodul][rules], [Handelspartnerverwaltung][tpm] und vielem mehr.

- **Leicht zu bedienende Entwurfstools** – Logik-Apps können von A bis Z im Browser entwickelt werden. Beginnen Sie mit einem Trigger - von einem einfachen Zeitplan bis zum Auftauchen eines Tweets zu Ihrem Unternehmen. Orchestrieren Sie anschließend eine beliebige Anzahl von Aktionen mithilfe des umfassenden Angebots an Connectors.

- **Einfaches Zusammensetzen von SaaS** – Zusammensetzungsaufgaben, die einfach zu beschreiben sind, lassen sich nur schwierig in Code implementieren. Mit Logik-Apps ist es kinderleicht, verschiedenartige Systeme miteinander zu verbinden. Möchten Sie in Ihrer CRM-Software eine Aufgabe erstellen, die auf Aktivität in Ihren Facebook- oder Twitter-Konten basiert? Wollen Sie Ihre Marketinglösung in der Cloud mit Ihrem lokalen Abrechnungssystem verbinden? Logik-Apps sind die schnellste und zuverlässigste Möglichkeit, Lösungen für diese Probleme bereitzustellen.

- **Schneller Einstieg mit Vorlagen** – Um Ihnen den Einstieg zu erleichtern, bieten wir einen [Katalog mit Vorlagen][templates], mit deren Hilfe Sie einige allgemeine Lösungen rasch erstellen können. Von BizTalk-Lösungen bis zu einfachen SaaS-Konnektivitätslösungen und sogar einigen, die "just for fun" sind – der Katalog ist die schnellste Möglichkeit, sich mit der Leistungsfähigkeit von Logik-Apps vertraut zu machen.

- **Integrierte Erweiterbarkeit** – Sie finden nicht den Connector, den Sie brauchen? Logik-Apps sind Teil der App Service-Produktfamilie und für das Arbeiten mit API-Apps ausgelegt. Sie können problemlos eine eigene API-App entwickeln und anschließend als Connector nutzen. Entwickeln Sie eine neue App nur für sich allein, oder veröffentlichen Sie sie im Marketplace, um sie kommerziell zu verwerten.

- **Hohes Integrationspotenzial** – Fangen Sie einfach an, und wachsen Sie Ihrem Bedarf entsprechend. Logik-Apps können problemlos die Leistungsstärke von BizTalk, der branchenführenden Integrationslösung von Microsoft nutzen, damit Integrationsexperten die Lösungen erstellen können, die sie brauchen. Erfahren Sie mehr über die [von App Services gebotenen BizTalk-Funktionen][biztalk].

## Logik-App – Konzepte

Es folgt eine Beschreibung einiger der wichtigsten Elemente von Logik-Apps.

- **Workflow** – Logik-Apps bieten eine grafische Möglichkeit zum Abbilden Ihrer Geschäftsprozesse als Folge von Schritten bzw. Workflow.
- **Connectors** – Ihre Logik-Apps benötigen Zugriff auf Daten und Dienste. Ein Connector ist eine besondere Art von API-App. Sein Zweck ist, Ihnen beim Herstellen von Verbindungen und Arbeiten mit Ihren Daten zu helfen. Die Liste der aktuell verfügbaren Connectors finden Sie unter [Verwenden von Connectors][connectors].
- **Trigger** – Einige Connectors können auch als Trigger fungieren. Ein Trigger startet eine neue Instanz eines Workflows basierend auf einem bestimmten Ereignis, z. B. Eingang einer E-Mail oder einer Änderung in Ihrem Azure Storage-Konto.
-  **Aktionen** – Jeder Schritt nach dem Trigger in einem Workflow wird als Aktion bezeichnet. Jede Aktion wird in der Regel einem Vorgang in Ihrem Connector oder benutzerdefinierten API-Apps zugeordnet.
- **BizTalk** – Für anspruchsvollere Integrationsszenarien bietet Azure App Services Funktionen von Biztalk. BizTalk ist die branchenführenden Integrationsplattform von Microsoft. Die BizTalk-API-Apps ermöglichen die problemlose Einbeziehung von u. a. Validierung, Transformation und Regeln in Ihre Logik-App-Workflows. Unter [Was sind BizTalk-API-Apps?][biztalk] finden Sie weitere Informationen.

## Erste Schritte

Befolgen Sie zum Einstieg in Logik-Apps das Lernprogramm [Erstellen einer Logik-App][create].

Weitere Informationen zur Azure App Service-Plattform finden Sie unter [Azure App Service][appservice].

[biztalk]: app-service-logic-what-are-biztalk-api-apps.md
[appservice]: ../app-service/app-service-value-prop-what-is.md
[create]: app-service-logic-create-a-logic-app.md
[connectors]: app-service-logic-connectors-list.md
[tpm]: app-service-logic-create-a-trading-partner-agreement.md
[rules]: app-service-logic-use-biztalk-rules.md
[templates]: app-service-logic-use-logic-app-templates.md
 

<!---HONumber=July15_HO3-->