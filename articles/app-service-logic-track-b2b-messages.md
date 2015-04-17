<properties 
   pageTitle="Nachverfolgen von B2B-Nachrichten" 
   description="In diesem Thema wird die Nachverfolgung der B2B-Verarbeitung behandelt." 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="harishkragarwal" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="02/27/2015"
   ms.author="hariag"/>


# Nachverfolgen von B2B-Nachrichten

## B2B-Nachverfolgungsinformationen
Die B2B-Kommunikation umfasst die Nachrichtenverarbeitung zwischen Handelspartnern. Die Beziehungen werden als Vereinbarungen zwischen zwei Handelspartnern definiert. Nachdem die Kommunikation eingerichtet wurde, wird eine Möglichkeit zur Überwachung benötigt, ob die Kommunikation wie erwartet erfolgt. 
Im Rahmen der Integration von B2B-API-Apps in Azure App Services haben wir die Nachverfolgung von Daten aktiviert, die auch im Azure-Portal bereitgestellt wird. 

## AS2
Nachdem Sie eine Instanz einer AS2-API-App erstellt haben, navigieren Sie zu dieser Instanz und dann zum Abschnitt "Nachverfolgung". Hier können Sie alle AS2-Nachverfolgungsinformationen anzeigen und auch auf den eingeblendeten Filterblättern filtern.

![][1]  

## EDIFACT
Nachdem Sie eine Instanz einer EDIFACT-API-App erstellt haben, navigieren Sie zu dieser Instanz und dann zum Abschnitt "Nachverfolgung". Hier können Sie alle EDIFACT-Nachverfolgungsinformationen anzeigen und auch auf den eingeblendeten Filterblättern filtern.
Darüber hinaus können Sie Daten auf Austauschebene, Gruppenebene und Transaktionssatzebene anzeigen, während Sie die Ansicht durchlaufen. 

Wenn Batches als Teil des EDIFACT-Vereinbarungen in der zugehörigen Trading Partner Management-API-App erstellt wurden, sind all diese Batches im Abschnitt "Batchverarbeitung" enthalten. Bei einer genauen Untersuchung eines Batches lassen sich die Nachricht, die die aktive Nachricht darstellt (falls vorhanden) und auch die Informationen für Batches erkennen, die bislang verarbeitet wurden.

![][2]      

## X12
Nachdem Sie eine Instanz einer X12-API-App erstellt haben, navigieren Sie zu dieser Instanz und dann zum Abschnitt "Nachverfolgung". Hier können Sie alle X12-Nachverfolgungsinformationen anzeigen und auch auf den eingeblendeten Filterblättern filtern.
Darüber hinaus können Sie Daten auf Austauschebene, Gruppenebene und Transaktionssatzebene anzeigen, während Sie die Ansicht durchlaufen. 

Wenn Batches als Teil der X12-Vereinbarungen in der zugehörigen Trading Partner Management-API-App erstellt wurden, sind alle diese Batches im Abschnitt "Batchverarbeitung" enthalten. Bei einer genauen Untersuchung eines Batches lassen sich die Nachricht, die die aktive Nachricht darstellt (falls vorhanden) und auch die Informationen für Batches erkennen, die bislang verarbeitet wurden. 

X12 und EDIFACT haben ähnliche Nachverfolgungsansichten. 

<!--Image references-->
[1]: ./media/app-service-logic-track-b2b-messages/AS2Tracking.jpg
[2]: ./media/app-service-logic-track-b2b-messages/EDIFACTTracking.jpg

<!--HONumber=49-->