<properties 
   pageTitle="Archivieren von AS2-Nachrichten" 
   description="Dieses Thema behandelt die Archivierung von AS2-Nachrichten" 
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


#Archivieren von AS2-Nachrichten

**AS2-Connector** stellt die Möglichkeit bereit, Nachrichten zu archivieren. Bei der Archivierung wird die Nachricht in dem **Azure-Blobcontainer** gespeichert, der Teil der Paketeinstellungen ist.

Die Archivierung wird an zwei Punkten sowohl für Nachrichten als auch für Bestätigungen (MDNs) bereitgestellt:

1. **Trigger bei Empfang/Decodierung:** Die Nachricht wird archiviert, sobald sie bei der API-App-Instanz eingegangen ist. 
2. **Codierungs-/Sendeaktion:** Die codierte Nachricht wird archiviert, nachdem die gesamte Verarbeitung abgeschlossen ist und kurz bevor sie an den Partner gesendet wird. 

##Vorgehensweise: Abrufen der archivierten URL der Nachricht

Navigieren Sie zur AS2-Connector-Instanz der API-App, und klicken Sie auf "Nachverfolgung". Grenzen Sie die Nachverfolgungsinformationen mithilfe der Filterparameter ein. Wenn Ihre Nachricht angezeigt wird, klicken Sie darauf, um die Detailansicht anzuzeigen. Die archivierte URL für die Nachricht wird in dieser detaillierten Ansicht angezeigt.

![][1]

##Vorgehensweise: Abrufen der archivierten Nachricht

Verwenden Sie die oben abgerufene URL, um die archivierte Nachricht aus dem Azure-Blobspeicher abzurufen.


<!--Image references-->
[1]: ./media/app-service-logic-archive-as2-messages/Tracking.jpg
 

<!---HONumber=July15_HO3-->