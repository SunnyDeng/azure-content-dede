<properties
   pageTitle="Verwenden des Azure Machine Learning-Connectors in Logik-Apps | Microsoft Azure App Service"
   description="Erstellen und Konfigurieren des Azure Machine Learning-Connectors und Verwenden des Connectors in einer Logik-App in Azure App Service"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="11/11/2015"
   ms.author="jehollan"/>
   
# Übersicht
Mit dem Azure ML-Connector für Logik-Apps können Sie Azure ML-APIs für die Batchbewertung über den Batchausführungsdienst (Batch Execution Service, BES) und für das erneute Training von Modellen aufrufen. Mit diesen Features und den Logik-App-Triggern lassen sich Zeitpläne für Batchaufträge sowie ein geplantes erneutes Training von Modellen einrichten.

 ![][1]
 
## Erste Schritte mit dem Azure Machine Learning-Connector und Hinzufügen des Connectors zur Logik-App
Erstellen Sie zuerst in Azure ML Studio ein Experiment, richten Sie einen Webdienst ein, und stellen Sie ihn bereit. Anschließend können Sie die API-URL und den Schlüssel der BES-Post-URL verwenden, die Sie auf der Hilfeseite des BES finden ([weitere Informationen](https://github.com/Azure/azure-content/blob/master/articles/machine-learning/machine-learning-walkthrough-5-publish-web-service.md)).

Um einen BES-Auftrag mithilfe des Connectors auszuführen, fügen Sie den Azure ML-Connector Ihrer Logik-App hinzu. Anschließend geben Sie die erforderlichen Informationen ein (weitere Informationen weiter unten). Um das erneute Trainieren einzurichten, fügen Sie einen zweiten Azure ML-Connector hinzu und stellen die entsprechenden Eingabeparameter bereit (weitere Informationen zum Einrichten eines Modells für das erneute Trainieren [finden Sie hier](machine-learning-retrain-models-programmatically.md)).

## Ausführen eines Azure ML-Batchausführungsauftrags
Der Azure ML-Connector bietet die folgenden vier Optionen zum Ausführen von Batchausführungsjobs: 1. Batchauftrag mit Eingabe und Ausgabe: Das Experiment verfügt über ein Webdienst-Eingabemodul und ein Webdienst-Ausgabemodul. 2. Batchauftrag ohne Eingabe und Ausgabe: Das Experiment verfügt weder über ein Webdienst-Eingabemodul noch ein Webdienst-Ausgabemodul (und verwendet z. B. Reader- und Writer-Module). 3. Batchauftrag nur mit Eingabe: Das Experiment verfügt über ein Webdienst-Eingabemodul, aber nicht über ein Webdienst-Ausgabemodul (und verwendet z. B. ein Writer-Modul). 4. Batchauftrag nur mit Ausgabe: Das Experiment verfügt über kein Webdienst-Eingabemodul, aber über ein Webdienst-Ausgabemodul (und verwendet z. B. ein Reader-Modul). Beachten Sie, dass Anforderungen beim Batchausführungsdienst asynchron verarbeitet werden. Abhängig vom Umfang Ihrer Daten und der Komplexität des Modells kann die Ausführung des Auftrags einige Zeit in Anspruch nehmen. Wenn der Auftrag abgeschlossen ist, gibt der Connector das Ergebnis der Ausgabe zurück.

### Ausführen der Batchausführung mit Eingabe und Ausgabe
Wenn das Studio-Experiment über ein Webdienst-Eingabemodul und ein Webdienst-Ausgabemodul verfügt, müssen Sie Informationen über das Storage-Blob-Konto und den Speicherort bereitstellen ([weitere Informationen](machine-learning-consume-web-services.md)). Außerdem können Sie globale Parameter (für Webdienste)einschließen, wenn sie in Ihrem Experiment eingerichtet sind ([weitere Informationen](machine-learning-web-service-parameters.md)).

![][2]

Klicken Sie auf die Auslassungspunkte, um die Felder für globale Parameter ein- und auszublenden Dadurch können Sie globale Parameter in einer durch Trennzeichen getrennten Liste mit Feldern und Werten angeben.

![][3]

Andere Varianten für BES-Aufträge, z. B. Aufträge ohne Webdiensteingabe oder -ausgabe, sind ebenfalls über den Connector verfügbar.

## Einrichten des erneuten Trainings

Verwenden Sie die Aktion „Erneutes Training einrichten“, um ein einmaliges oder ein nach einem Zeitplan ausgeführtes erneutes Training Ihres ML-Modells einzurichten In Kombination mit einem aus dem Connector erstellten Batchausführungsauftrag können Sie die Schritte zum Trainieren und Aktualisieren des Modells eines Webdiensts ausführen. In diesem Workflow verwenden Sie den Connector zweimal. 1. Der erste Connector dient zum Ausführen eines BES-Auftrags, mit dem das Modell erneut trainiert und die Ausgabe zurückgegeben wird. Die Ausgabe dieser Ausführung enthält die URL des neuen Modells (.ilearner). Sie kann auch – wenn Sie dies im Experiment so eingerichtet haben – die URL der Ausgabe des Auswertungsmoduls enthalten, bei der es sich um eine CSV-Datei handelt. Im nächsten Schritt können Sie die Daten aus der Ausgabe des Auswertungsmoduls verwenden, um zu entscheiden, ob Sie das Modell in Ihrem Webdienst einsetzen (z. B. bei einer Exaktheit > 0,85). 1. Der zweite Connector dient zum Einrichten des erneuten Trainings. Er verwendet Parameter aus der Ausgabe des ersten Connectors, um optional den Status des aktualisierten Modells zu überprüfen, ob die Bedingungen für das Aktualisieren des Modells erfüllt sind. Wenn dies der Fall ist, aktualisiert der Connector den Webdienst mit dem neu trainierten Modell. * Sie können beispielsweise mit der URL des neu trainierten Modells auf die Ausgabe des BES-Auftrags zugreifen, indem Sie `@{body('besconnector').Results.output2.FullURL}` in das Feld „URL des erneut trainierten Modells“ eingeben. Hier wird davon ausgegangen, dass die Ausgabe in Ihrem Trainingsexperiment „output2“ heißt. * Als Ressourcennamen verwenden Sie den vollständigen Namen des gespeicherten trainierten Modells im Vorhersageexperiment, z. B. MyTrainedModel [trainiertes Modell] * Im Feld „Auswertungsergebnisschlüssel“ können Sie jeden Parameter eingeben, der in der Ausgabe des Auswertungsmoduls des Trainingsexperiments zurückgegeben wurde (falls Sie diese eingeschlossen haben). Sie können die Liste der verfügbaren Parameter anzeigen, indem Sie in Azure ML Studio im Trainingsexperiment die Ergebnisse des Auswertungsmoduls visualisieren. Für ein Klassifizierungsexperiment würden dazu gehören: Exaktheit, Genauigkeit, Sensitivität, F-Maß, Fläche unter der Kurve, Durchschnittlicher Log-Verlust und Trainings-Log-Verlust.

![][4]
 
### Geplantes erneutes Training
 
Mithilfe von Logik-App-Triggern können Sie den Connector so einrichten, dass er nach einem Zeitplan ausgeführt wird. Auf diese Weise kann ein Modell regelmäßig mit neu eingetroffenen Daten erneut trainiert werden. Der BES-Auftrag trainiert das Modell erneut, und mit der für das erneute Training festgelegten Aktion wird das Modell aktualisiert, sobald das erneute Training abgeschlossen ist.
 
![][5]
 
## Connector-Ausgabe 
 
**BES**: Nach erfolgreichem Abschluss des Batchauftrags enthält die Connector-Ausgabe folgende Informationen für jede Webdienstausgabe:
 
 ![][6]
 
Beachten Sie, dass diese Informationen nur zur Verfügung stehen, wenn Sie eine Webdienstausgabe eingeschlossen haben. Sie stehen z. B. nicht zur Verfügung, wenn Sie ein Writer-Modul verwenden, um aus dem Experiment in Studio in eine Datenbank zu schreiben.

**Erneutes Training**: Nach erfolgreichem Abschluss des erneuten Trainings enthält die Ausgabe folgende Informationen:

![][7]

## Zusammenfassung

Mithilfe des Azure ML-Connectors für Logik-Apps können Sie Aufträge für die Batchbewertung und für das erneute Training von Modellen bedarfsgesteuert oder nach einem Wiederholungszeitplan ausführen. Durch die Kombination der beiden Aktionen können Sie Ihre Daten automatisch bewerten und das Modell in Ihrem Webdienst erneut trainieren, auswerten und aktualisieren, ohne dafür Code schreiben zu müssen.

 <!--Image references-->
[1]: ./media/app-service-logic-connector-azureml/img1.png
[2]: ./media/app-service-logic-connector-azureml/img2.png
[3]: ./media/app-service-logic-connector-azureml/img3.png
[4]: ./media/app-service-logic-connector-azureml/img4.png
[5]: ./media/app-service-logic-connector-azureml/img5.png
[6]: ./media/app-service-logic-connector-azureml/img6.png
[7]: ./media/app-service-logic-connector-azureml/img7.png

<!---HONumber=AcomDC_1217_2015-->