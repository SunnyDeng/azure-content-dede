<properties 
	pageTitle="Aufgabenliste für Verwaltung und Entwicklung in BizTalk Services | Microsoft Azure BizTalk Services" 
	description="" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="msftman" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/02/2015" 
	ms.author="deonhe"/>

# Aufgabenliste für Verwaltung und Entwicklung in BizTalk Services  

## Erste Schritte
Bei der Arbeit mit Microsoft Azure BizTalk Services sind verschiedene lokale und cloudbasierte Komponenten zu berücksichtigen. Betrachten Sie zunächst den folgenden Prozessablauf:

|Schritt|Wer ist verantwortlich?|Aufgabe|Verwandte Links|
|----|----|----|----|
1\.|Administrator|Erstellen Sie das Microsoft Azure-Abonnement über ein Microsoft-Konto oder ein Organisationskonto.|[Klassisches Azure-Portal](http://go.microsoft.com/fwlink/p/?LinkID=213885)|
|2\.|Administrator|Erstellen Sie einen BizTalk Service, oder stellen Sie ihn bereit.|[Erstellen eines BizTalk Service im klassischen Azure-Portal](http://go.microsoft.com/fwlink/p/?LinkID=302280)|
|3\.|Administrator|Registrieren Sie Ihre BizTalk Services-Bereitstellung oder die Bereitstellung Ihres Unternehmens.|[Registrieren und Aktualisieren einer BizTalk Service-Bereitstellung im BizTalk Services-Portal](https://msdn.microsoft.com/library/azure/hh689837.aspx)|
|4\.|Administrator|Gilt, wenn die Anwendung mithilfe von BizTalk Adapter Service eine Verbindung zu einem lokalen Line-of-Business-System (LOB) herstellt oder ein Warteschlangen- oder Themenziel verwendet. Erstellen Sie den Azure Service Bus-Namespace. Teilen Sie dem Entwickler die Werte für Namespace, Service Bus-Ausstellername und Service Bus-Ausstellerschlüssel mit.|[Erstellen oder Ändern eines Service Bus-Dienstnamespace]() und [Abrufen der Werte für Ausstellernamen und -schlüssel]()|
|5\.|Developer|Installieren Sie das SDK, und erstellen Sie das BizTalk Service-Projekt in Visual Studio.|[Installieren des Azure BizTalk Services SDK]() und [Erstellen von formatierten Messagingendpunkten in Azure]()|
|6\.|Developer|Stellen Sie Ihr BizTalk Service-Projekt in Ihrem bei Azure gehosteten BizTalk Service bereit.|[Bereitstellen und Aktualisieren des BizTalk Services-Projekts](https://msdn.microsoft.com/library/azure/hh689881.aspx)|
|7\.|Administrator|Gilt, wenn Sie EDI verwenden. Sie können im Microsoft Azure BizTalk Services-Portal Partner hinzufügen und Vereinbarungen erstellen. Wenn Sie eine Vereinbarung erstellen, können Sie die vom Entwickler in den Vereinbarungseinstellungen erstellten Brücken oder Transformationen hinzufügen.|[Konfigurieren von EDI, AS2 und EDIFACT im BizTalk Services-Portal](https://msdn.microsoft.com/library/azure/hh689853.aspx)|
|8\.|Administrator|Überwachen Sie über das klassische Azure-Portal die Integrität Ihres BizTalk Service, einschließlich Leistungsmetriken.|[BizTalk Services: Registerkarten "Dashboard", "Überwachen" und "Skalieren"](http://go.microsoft.com/fwlink/p/?LinkID=302281)|
|9\.|Administrator|Verwalten Sie über das Microsoft Azure BizTalk Services-Portal die von BizTalk Services verwendeten Artefakte, und verfolgen Sie Nachrichten nach, während sie von den Brückendateien verarbeitet werden.|[Verwenden des BizTalk Services-Portals](https://msdn.microsoft.com/library/azure/dn874043.aspx)|
|10\.|Administrator|Erstellen Sie einen Sicherungsplan zum Sichern des BizTalk Service.|[Geschäftskontinuität und Notfallwiederherstellung in BizTalk Services](https://msdn.microsoft.com/library/azure/dn509557.aspx) |  
## Nächste Schritte
[Tutorials und Beispiele](https://msdn.microsoft.com/library/azure/hh689895.aspx)

[Erstellen des Projekts in Visual Studio](https://msdn.microsoft.com/library/azure/hh689811.aspx)

[Installieren des Azure BizTalk Services-SDKs](https://msdn.microsoft.com/library/azure/hh689760.aspx)

## Konzepte
[Erstellen des Projekts in Visual Studio](https://msdn.microsoft.com/library/azure/hh689811.aspx) [EDI, AS2 und EDIFACT-Messaging (B2B)](https://msdn.microsoft.com/library/azure/hh689898.aspx)
## Weitere Ressourcen  
[Quelle, Ziel und Bridge-Messagingendpunkte](https://msdn.microsoft.com/library/azure/hh689877.aspx) [Erlernen und Erstellen von Nachrichtenzuordnungen und -transformationen](https://msdn.microsoft.com/library/azure/hh689905.aspx) [Verwenden des BizTalk Adapter Service](https://msdn.microsoft.com/library/azure/hh689889.aspx) [BizTalk Services-Dokumentation](http://go.microsoft.com/fwlink/p/?LinkID=303664)

<!---HONumber=AcomDC_1203_2015-->