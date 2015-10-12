<properties 
   pageTitle="Verwendung des BizTalk-Handelspartnerverwaltungs-Connectors in Logik-Apps | Microsoft Azure App Service" 
   description="Erstellen und Konfigurieren des BizTalk-Handelspartnerverwaltungs-Connectors oder einer API-App und Verwenden in einer Logik-App in Azure App Service" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajeshramabathiran" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="08/23/2015"
   ms.author="rajram"/>

# Erste Schritte mit der BizTalk-Handelspartnerverwaltung und das Hinzufügen zur Logik-App
Mit dem BizTalk-Dienst für die Handelspartnerverwaltung (Trading Partner Management, TPM) können Sie Business-to-Business-Beziehungen, z. B. Partner und Vereinbarungen, zusammen mit zugeordneten Artefakten, z. B. Schemas und Zertifikaten, definieren und dauerhaft einrichten. Diese Beziehungen können dann von verwandten API-Diensten, z. B. AS2, EDIFACT und X12, durchgesetzt werden.

Die TPM-API-App ist die Basisanforderung des AS2-Connectors und der X12- oder EDIFACT-API-Apps. Sie können die BizTalk-Handelspartnerverwaltung dem geschäftlichen Workflow hinzufügen und Daten im Rahmen dieses Business-to-Business-Workflows in einer Logik-App verarbeiten.

## Voraussetzungen
- Leere SQL Azure-Datenbank – Sie müssen zuerst eine leere SQL Azure-Datenbank erstellen, bevor Sie eine neue TPM-API-App erstellen.

## Grundlegendes zu Partnern, Vereinbarungen und Profilen
Klicken Sie [hier][1], um weitere Informationen zur Handelspartnervereinbarung zu erhalten.

## Mehr mit Ihrem Connector machen
Nachdem der Connector nun erstellt ist, können Sie ihn mit Logik-App in einem Geschäftsworkflow hinzufügen. Informationen finden Sie unter [Was sind Logik-Apps?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE]Wenn Sie Azure Logik-Apps ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [Logik-App testen](https://tryappservice.azure.com/?appservice=logic) sofort kostenlos eine kurzlebige Starter-Logik-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

Anzeigen der Swagger-REST-API-Referenz unter [Referenz zu Connectors und API-Apps](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Sie können auch Leistungsstatistiken überprüfen und die Sicherheit zum Connector steuern. Informationen finden Sie unter [Verwalten und Überwachen integrierter API-Apps und Connectors](app-service-logic-monitor-your-connectors.md).

<!--References-->
[1]: app-service-logic-create-a-trading-partner-agreement.md

<!---HONumber=Oct15_HO1-->