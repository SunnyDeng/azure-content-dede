<properties 
   pageTitle="Nachverfolgen von B2B-Nachrichten" 
   description="In diesem Thema wird die Nachverfolgung der B2B-Verarbeitung behandelt." 
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
   ms.date="10/01/2015"
   ms.author="rajram"/>


# Nachverfolgen von B2B-Nachrichten

## B2B-Nachverfolgungsinformationen
Die B2B-Kommunikation umfasst die Nachrichtenverarbeitung zwischen Handelspartnern. Die Beziehungen werden als Vereinbarungen zwischen zwei Handelspartnern definiert. Nachdem die Kommunikation eingerichtet wurde, wird eine Möglichkeit zur Überwachung benötigt, ob die Kommunikation wie erwartet erfolgt.

Wir haben die Nachrichtennachverfolgung für die folgenden B2B-Szenarios implementiert: AS2, EDIFACT und X12.

## AS2
Nachdem Sie eine Instanz einer AS2-API-App erstellt haben, navigieren Sie zu dieser Instanz, und wählen Sie "Nachverfolgung" aus. Hier können Sie sämtliche AS2-Nachverfolgungsinformationen anzeigen und filtern.

![][1]

## EDIFACT
Nachdem Sie eine Instanz einer EDIFACT-API-App erstellt haben, navigieren Sie zu dieser Instanz, und wählen Sie "Nachverfolgung" aus. Hier können Sie sämtliche EDIFACT-Nachverfolgungsinformationen anzeigen und filtern. Darüber hinaus können Sie Daten auf Austausch-, Gruppen- und Transaktionssatzebene in einer einzigen Ansicht anzeigen.

Wenn Batches als Teil der EDIFACT-Vereinbarungen in der zugehörigen Trading Partner Management-API-App erstellt werden, sind all diese Batches im Abschnitt "Batchverarbeitung" enthalten. Sie können einen Batch auswählen, um die aktive Nachricht (sofern vorhanden) und die Informationen für die abgeschlossenen Batches anzuzeigen.

![][2]

## X12
Nachdem Sie eine Instanz einer X12-API-App erstellt haben, navigieren Sie zu dieser Instanz, und wechseln Sie zu "Nachverfolgung". Hier können Sie sämtliche X12-Nachverfolgungsinformationen anzeigen und filtern. Darüber hinaus können Sie Daten auf Austausch-, Gruppen- und Transaktionssatzebene in einer einzigen Ansicht anzeigen.

Wenn Batches als Teil der X12-Vereinbarungen in der zugehörigen Trading Partner Management-API-App erstellt werden, sind all diese Batches im Abschnitt "Batchverarbeitung" enthalten. Sie können einen Batch auswählen, um die aktive Nachricht (sofern vorhanden) und die Informationen für die abgeschlossenen Batches anzuzeigen.

<!--Image references-->
[1]: ./media/app-service-logic-track-b2b-messages/AS2Tracking.jpg
[2]: ./media/app-service-logic-track-b2b-messages/EDIFACTTracking.jpg

<!---HONumber=Oct15_HO2-->