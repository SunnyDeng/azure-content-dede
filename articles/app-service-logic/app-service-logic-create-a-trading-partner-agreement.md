<properties 
   pageTitle="Erstellen einer Handelspartnervereinbarung in Microsoft Azure App Service" 
   description="Erstellen von Handelspartnervereinbarungen" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="07/01/2015"
   ms.author="rajram"/>


# Erstellen einer Handelspartnervereinbarung   

Handelspartner sind die Entitäten, die an einer B2B-Kommunikation (Business-to-Business) beteiligt sind. Wenn zwei Partner eine Beziehung eingehen, wird dies als *Vereinbarung* bezeichnet. Diese Vereinbarung basiert auf der Kommunikation, die beide Partner wünschen, und ist protokoll- oder transportspezifisch. Von Azure App Service werden verschiedene B2B-Protokolle und -Transporte unterstützt:

- AS2 (Applicability Statement 2)
- EDIFACT (United Nations/Electronic Data Interchange For Administration, Commerce and Transport (UN/EDIFACT))
- X12 (ASC X12)

### BizTalk-API-Apps mit Unterstützung für B2B-Szenarien
Die folgenden API-Apps ermöglichen diese Funktionen mithilfe der funktionsreichen und selbsterklärenden Azure-Portaloberfläche:


## BizTalk-Handelspartnerverwaltung (Trading Partner Management, TPM)
- Erstellung und Verwaltung von Partnern, Profilen und Identitäten
- Speicherung und Verwaltung von EDI-Schemas
- Speicherung und Verwaltung von Zertifikaten (im AS2-Protokoll verwendet)
- Erstellung und Verwaltung von AS2-Vereinbarungen
- Erstellung und Verwaltung von EDIFACT-Vereinbarungen (einschließlich Batchverarbeitung auf Absenderseite)
- Erstellung und Verwaltung von X12-Vereinbarungen (einschließlich Batchverarbeitung auf Absenderseite)

![][1]


## AS2-Connector
- Richtet AS2-Vereinbarungen gemäß der Definition in der zugehörigen TPM-API-App-Instanz ein.
- Stellt AS2-Verarbeitungs-/Nachverfolgungsinformationen für die Problembehandlung zur Verfügung.


## BizTalk EDIFACT
- Richtet EDIFACT-Vereinbarungen gemäß der Definition in der zugehörigen TPM-API-App-Instanz ein.
- Stellt EDIFACT-Verarbeitungs-/Nachverfolgungsinformationen für die Problembehandlung zur Verfügung.
- Bietet eine Zustandsverwaltung von Batches (Start und Ende) gemäß der Definition in den EDIFACT-Vereinbarungen in der zugehörigen TPM-API-App-Instanz.


## BizTalk X12
- Richtet X12-Vereinbarungen gemäß der Definition in der zugehörigen TPM-API-App-Instanz ein. 
- Stellt X12-Verarbeitungs-/Nachverfolgungsinformationen für die Problembehandlung zur Verfügung.
- Bietet eine Zustandsverwaltung von Batches (Start und Ende) gemäß der Definition in den X12-Vereinbarungen in der zugehörigen TPM-API-App-Instanz.

Wie bereits erwähnt, erfordern AS2-, X12- und EDIFACT-API-Apps eine TPM-API-App, um wie erwartet zu funktionieren.


## Erste Schritte
So erstellen Sie Handelspartnervereinbarungen:

1. Erstellen Sie eine Instanz des **BizTalk-Handelspartnerverwaltungs**-Connectors. Hierfür ist eine leere SQL-Datenbank erforderlich. Vergewissern Sie sich, bevor Sie beginnen, dass eine leere Datenbank verfügbar und einsatzbereit ist.
2. Laden Sie Schemas und Zertifikate entsprechend den Anforderungen der Vereinbarungen hoch. Dies erfolgt durch Durchsuchen der TPM-Instanz und ein schrittweises Durchlaufen des Teils "Schemas" und/oder "Zertifikate".
3. Navigieren Sie zur erstellten TPM-Instanz und hier zum Teil **Partner**.
4. Erstellen Sie Partner wie gewünscht. Bearbeiten Sie auch die Profile den Anforderungen entsprechend, und fügen Sie die erforderlichen Identitäten hinzu.
5. Erstellen Sie nun Vereinbarungen im Teil **Vereinbarungen**. Während der Vereinbarungserstellung ist einer der ersten Schritte das Auswählen des Protokolls. Basierend auf dem gewählten Protokoll erfolgt die restliche Vereinbarungskonfiguration.

![][2]

![][3]

<!--Image references-->
[1]: ./media/app-service-logic-create-a-trading-partner-agreement/TPMResourceView.jpg
[2]: ./media/app-service-logic-create-a-trading-partner-agreement/ProtocolSelection.jpg
[3]: ./media/app-service-logic-create-a-trading-partner-agreement/X12AgreementCreation.jpg
 

<!---HONumber=July15_HO4-->