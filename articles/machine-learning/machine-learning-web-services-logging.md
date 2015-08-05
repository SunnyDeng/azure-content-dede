<properties 
	pageTitle="Protokollierung für Machine Learning-Webdienste | Microsoft Azure" 
	description="Erfahren Sie, wie Sie die Protokollierung für Machine Learning-Webdienste aktivieren können. Die Protokollierung stellt zusätzliche Informationen zur Problembehandlung von APIs bereit." 
	services="machine-learning" 
	documentationCenter="" 
	authors="raymondlaghaeian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="big-data" 
	ms.date="06/30/2015"
	ms.author="raymondl;garye"/>

#Aktivieren der Protokollierung für Machine Learning-Webdienste  

Dieses Dokument enthält Informationen zu den Protokollierungsfunktionen der Azure-ML-Webdienste. Durch das Aktivieren der Protokollierung in Webdiensten werden zusätzliche Informationen zur Problembehandlung von der APIs bereitgestellt, die über eine einfache Fehlernummer und -meldung hinausgehen.

-	So aktivieren Sie die Protokollierung in Webdiensten:   
	-	Zum Aktivieren der Protokollierung müssen Sie vom Azure-Portal aus auf das Web Service-Dashboard zugreifen, Aktivieren Sie dann die Protokollierung durch Klicken auf die Schaltfläche "Ja", und klicken Sie dann auf "Speichern".  
-	Diese Auswirkungen hat die Aktivierung der Protokollierung:  
	-	Wenn die Protokollierung aktiviert ist, werden alle Diagnoseergebnisse/Fehler vom Standardendpunkt in dem Azure-Speicherkonto protokolliert, das mit dem Arbeitsbereich des Benutzers verknüpft ist. Dieses Speicherkonto wird in der Azure Portal-Dashboardansicht (am unteren Rand des Abschnitts "Auf einen Blick") im Arbeitsbereich angezeigt.  
-	Hier ist die Funktion verfügbar:  
	-	Derzeit aktivieren wir die Protokollierung für den Standardendpunkt des Webdiensts. Wir werden dieses Feature bald für die zusätzliche Endpunkte anbieten, die der Benutzer im Azure-Portal erstellen kann.  
-	So zeigen Sie die Protokolle an:  
	-	Die Protokolle können jedem der zum Durchsuchen von Azure-Speicherkonten verfügbaren Tools angezeigt werden. Am einfachsten ist es, im Azure-Portal zum Speicherkonto zu navigieren und dann auf die Registerkarte "CONTAINER" zu klicken. Sie sollten dann einen Container mit dem Namen **ml-diagnostics** sehen. Dieser Container enthält alle Diagnoseinformationen für alle Webdienst-Endpunkte für alle Arbeitsbereiche, die diesem Speicherkonto zugeordnet sind.  
-	Das Protokoll enthält folgende Blob-Details:  
	-	Jedes Blob im Container enthält die Diagnoseinformationen für genau eines der folgenden Ereignisse:
		-	Eine Ausführung der Batch-Execution-Methode  
		-	Eine Ausführung der Request-Response-Methode  
		-	Initialisierung des Request-Response-Containers  
	-	Den Name der einzelnen Blobs ist ein Präfix vorangestellt, das folgendes Format hat: {Arbeitsbereich-Id}-{Webdienst-Id}-{Endpunkt-Id}/{Protokolltyp}  
-	Als Protokolltyp kann einer der folgenden Werte angegeben werden: batch score/requests score/init  

 

<!---HONumber=July15_HO4-->