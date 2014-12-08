<properties title="Step 1: Create an Azure Machine Learning workspace" pageTitle="Schritt 1: Erstellen eines Machine Learning-Arbeitsbereichs | Azure" description="Step 1: Create a new Azure Machine Learning Studio workspace" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/06/2014" ms.author="garye" />


Dies ist der erste Teil der exemplarischen Vorgehensweise [Entwickeln einer Vorhersagelösung mit Azure ML][develop]:

[develop]: ../machine-learning-walkthrough-develop-predictive-solution/

1.	**Erstellen eines ML-Arbeitsbereichs**
2.	[Hochladen vorhandener Daten][upload-data]
3.	[Erstellen eines neuen Experiments][create-new]
4.	[Trainieren und Bewerten der Modelle][train-models]
5.	[Veröffentlichen des Webdiensts][publish]
6.	[Zugreifen auf den Webdienst][access-ws]

[create-workspace]: ../machine-learning-walkthrough-1-create-ml-workspace/
[upload-data]: ../machine-learning-walkthrough-2-upload-data/
[create-new]: ../machine-learning-walkthrough-3-create-new-experiment/
[train-models]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
[publish]: ../machine-learning-walkthrough-5-publish-web-service/
[access-ws]: ../machine-learning-walkthrough-6-access-web-service/

----------

# Schritt 1: Erstellen eines Azure Machine Learning-Arbeitsbereichs

Damit Sie ML Studio verwenden können, benötigen Sie einen ML-Arbeitsbereich. Dieser Arbeitsbereich enthält die Tools, die zum Erstellen, Verwalten und Veröffentlichen von Experimenten erforderlich sind.  

##Erstellen eines Arbeitsbereichs  

1.	Melden Sie sich mit Ihrem Azure-Konto an.
2.	Klicken Sie im Microsoft Azure-Dienstbereich auf **MACHINE LEARNING**.  
![Create workspace][1]

3.	Klicken Sie auf **ML-ARBEITSBEREICH ERSTELLEN**.
4.	Geben Sie auf der Seite **SCHNELLERFASSUNG** Ihre Arbeitsbereichsinformationen ein, und klicken Sie dann auf **ML-ARBEITSBEREICH ERSTELLEN**.

	>**HINWEIS**: Der **BESITZER DES ARBEITSBEREICHS** ist Ihr Microsoft-Konto (name@outlook.com) bzw. Ihr Organisations-Konto.

Nach der Erstellung wird ihr ML-Arbeitsbereich auf der Seite **Machine Learning** angezeigt.  

>**Tipp**: Sie können die Experimente, an denen Sie arbeiten, mit anderen teilen, indem Sie andere Personen zum Arbeitsbereich einladen. Dazu wechseln Sie in ML Studio zur Seite **EINSTELLUNGEN**. Sie benötigen einfach das Microsoft-Konto oder Organisations-Konto für die einzelnen Benutzer.

----------

**Nächster Schritt: [Hochladen vorhandener Daten][upload-data]**

[1]: ./media/machine-learning-walkthrough-1-create-ml-workspace/create1.png
