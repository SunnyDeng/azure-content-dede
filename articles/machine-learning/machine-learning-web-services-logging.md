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
	ms.date="09/04/2015"
	ms.author="raymondl;garye"/>

#Aktivieren der Protokollierung für Machine Learning-Webdienste  

Dieses Dokument enthält Informationen zu den Protokollierungsfunktionen der Azure-ML-Webdienste. Durch das Aktivieren der Protokollierung in Webdiensten werden zusätzliche Informationen zur Problembehandlung von der APIs bereitgestellt, die über eine einfache Fehlernummer und -meldung hinausgehen.

-	So aktivieren Sie die Protokollierung in Webdiensten:   
	-	Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an.
	-	Klicken Sie auf Machine Learning, anschließend auf den Arbeitsbereich und dann auf die Menüoption "Webdienst".
	-	Klicken Sie in der Liste von Webdiensten auf den Namen des Webdiensts.
	-	Klicken Sie in der Liste der Endpunkte auf den Namen des Endpunkts.
	-	Klicken Sie auf die Menüoption "Konfigurieren".
	-	Legen Sie die "Ablaufverfolgungsebene der Diagnose" auf "Fehler" oder "Alle" fest, und klicken Sie dann auf der unteren Menüleiste auf "Speichern".
-	Diese Auswirkungen hat die Aktivierung der Protokollierung:  
	-	Wenn die Protokollierung aktiviert ist, werden alle Diagnoseergebnisse/Fehler vom ausgewählten Endpunkt in dem Azure-Speicherkonto protokolliert, das mit dem Arbeitsbereich des Benutzers verknüpft ist. Dieses Speicherkonto wird in der Azure Portal-Dashboardansicht (am unteren Rand des Abschnitts "Auf einen Blick") im Arbeitsbereich angezeigt.  
-	So zeigen Sie die Protokolle an:  
	-	Die Protokolle können jedem der zum Durchsuchen von Azure-Speicherkonten verfügbaren Tools angezeigt werden. Am einfachsten ist es, im Azure-Portal zum Speicherkonto zu navigieren und dann auf die Registerkarte "CONTAINER" zu klicken. Sie sollten dann einen Container mit dem Namen **ml-diagnostics** sehen. Dieser Container enthält alle Diagnoseinformationen für alle Webdienst-Endpunkte für alle Arbeitsbereiche, die diesem Speicherkonto zugeordnet sind.  
-	Das Protokoll enthält folgende Blob-Details:  
	-	Jedes Blob im Container enthält die Diagnoseinformationen für genau eines der folgenden Ereignisse:
		-	Eine Ausführung der Batch-Execution-Methode  
		-	Eine Ausführung der Request-Response-Methode  
		-	Initialisierung des Request-Response-Containers  
	-	Den Name der einzelnen Blobs ist ein Präfix vorangestellt, das folgendes Format hat: {Arbeitsbereich-Id}-{Webdienst-Id}-{Endpunkt-Id}/{Protokolltyp}  
-	Als Protokolltyp werden die folgenden Werten akkzeptiert:  
	- batch  
	- score/requests  
	- score/init  

 

<!---HONumber=Sept15_HO2-->