<properties
   pageTitle="Skalieren von Webdiensten | Microsoft Azure"
   description="Erfahren Sie, wie Sie einen Webdienst skalieren, indem Sie die Parallelität erhöhen und neue Endpunkte hinzufügen."
   services="machine-learning"
   documentationCenter=""
   authors="neerajkh"
   manager="srikants"
   editor="cgronlun"
   keywords="Azure Machine Learning, Webdienste, Operationalisierung, Skalierung, Endpunkt, Parallelität"
   />
<tags
   ms.service="machine-learning"
   ms.devlang="NA"
   ms.workload="data-services"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.date="10/27/2015"
   ms.author="neerajkh"/>

# Skalieren von Webdiensten

## Erhöhen der Parallelität

Standardmäßig ist jeder veröffentlichte Webdienst darauf konfiguriert, 20 Anforderungen gleichzeitig zu unterstützen. Sie können diese Parallelität über das [Azure-Verwaltungsportal](https://manage.windowsazure.com/) auf 200 gleichzeitige Anforderungen erhöhen, wie in der nachstehenden Abbildung dargestellt.

Wechseln Sie zum [Azure-Verwaltungsportal](https://manage.windowsazure.com/), klicken Sie auf das Machine Learning-Symbol auf der linken Seite, wählen Sie den Arbeitsbereich für die Veröffentlichung des Webdiensts aus, klicken Sie auf den gewünschten Webdienst, wählen Sie den Endpunkt aus, für den die Parallelität erhöht werden soll, und klicken Sie dann auf **KONFIGURIEREN**. Erhöhen Sie die Parallelität mit dem Schieberegler, und klicken Sie dann im unteren Bereich auf **SPEICHERN**.

Informationen zum Erhöhen der Parallelität finden Sie unter [Skalieren von API-Endpunkten](machine-learning-scaling-endpoints.md).

   ![Machine Learning, Skalieren von Endpunkten.][1]

## Hinzufügen von neuen Endpunkten für denselben Webdienst

Das Skalieren des Webdiensts ist eine häufige Aufgabe zum Unterstützen von mehr als 200 gleichzeitigen Anforderungen, zum Erhöhen der Verfügbarkeit durch mehrere Endpunkte oder zum Bereitstellen eines separaten Endpunkts für andere Consumer des Webdiensts. Der Benutzer kann die Skalierung erhöhen, indem er zusätzliche Endpunkte für denselben Webdienst hinzufügt. Der Benutzer kann über das [Azure-Verwaltungsportal](https://manage.windowsazure.com/) zusätzliche Endpunkte hinzufügen, wie in der nachstehenden Abbildung dargestellt:

Wechseln Sie zum [Azure-Verwaltungsportal](https://manage.windowsazure.com/), klicken Sie auf das Machine Learning-Symbol auf der linken Seite, wählen Sie den Arbeitsbereich für die Veröffentlichung des Webdiensts aus, klicken Sie auf den gewünschten Webdienst, wählen Sie im unteren Bereich **ENDPUNKT HINZUFÜGEN** aus, und geben Sie dann einen Namen, eine Beschreibung und die gewünschte Währung für den neuen Endpunkt an.

Informationen zum Hinzufügen neuer Endpunkte finden Sie unter [Erstellen von Endpunkten](machine-learning-create-endpoint.md).

   ![Machine Learning, Hinzufügen neuer Endpunkte.][2]

<!--Image references-->
[1]: ./media/machine-learning-scaling-webservice/machlearn-1.png
[2]: ./media/machine-learning-scaling-webservice/machlearn-2.png

<!---HONumber=Nov15_HO1-->