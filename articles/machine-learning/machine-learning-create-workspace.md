<properties 
	pageTitle="Erstellen eines Machine Learning-Arbeitsbereichs | Microsoft Azure" 
	description="Erstellen eines Arbeitsbereichs für Azure Machine Learning Studio" 
	services="machine-learning" 
	documentationCenter="" 
	authors="garyericson" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/01/2015" 
	ms.author="garye"/>


# Erstellen eines Azure Machine Learning-Arbeitsbereichs 

Um Azure Machine Learning Studio verwenden zu können, benötigen Sie einen Machine Learning-Arbeitsbereich. Dieser Arbeitsbereich enthält die Tools, die zum Erstellen, Verwalten und Veröffentlichen von Experimenten erforderlich sind.

[AZURE.INCLUDE [machine-learning-kostenlose-Testversion](../../includes/machine-learning-free-trial.md)]

## Erstellen eines Arbeitsbereichs

1. Melden Sie sich mit Ihrem Microsoft Azure-Konto an.
2. Klicken Sie im Microsoft Azure-Dienstbereich auf **MACHINE LEARNING**.

    ![Machine Learning-Dienst][1]

3. Klicken Sie im unteren Seitenbereich auf **+NEU**.
4. Klicken Sie auf **DATA SERVICES**, dann **COMPUTERLERNEN**, dann **SCHNELLERFASSUNG**.

	![Schnellerfassung eines neuen Arbeitsbereichs][3]

5. Geben Sie im Feld **ARBEITSBEREICHSNAME** für den Arbeitsbereich einen Namen ein, und legen Sie den **WORKSPACE OWNER** fest. Der Besitzer des Arbeitsbereichs muss einem gültigen Microsoft-Konto entsprechen (z. B. name@outlook.com).

    > [AZURE.NOTE]Später können Sie die Experimente, an denen Sie arbeiten, mit anderen teilen, indem Sie andere Personen zum Arbeitsbereich einladen. Dies können Sie in Machine Learning Studio auf der Seite **EINSTELLUNGEN** vornehmen. Sie benötigen nur das Microsoft-Konto oder Organisationskonto des betreffenden Benutzers.

6. Geben Sie den Azure-**SPEICHERORT** ein und geben Sie dann ein vorhandenes Azure-**SPEICHERKONTO** an, oder wählen Sie **Neues Speicherkonto erstellen** aus, um ein neues zu erstellen.
7. Klicken Sie auf **ML-ARBEITSBEREICH ERSTELLEN**.

Nach dem Erstellen Ihres Machine Learning-Arbeitsbereichs wird dieser auf der Seite **Machine Learning** angezeigt.

Informationen zum Verwalten des Arbeitsbereichs finden Sie unter [Verwalten eines Azure Machine Learning-Arbeitsbereichs]. Sollte ein Problem beim Erstellen des Arbeitsbereichs auftreten, finden Sie weitere Informationen unter [Handbuch zur Problembehandlung: Erstellen und Verbinden eines Machine Learning-Arbeitsbereichs].

[Verwalten eines Azure Machine Learning-Arbeitsbereichs]: machine-learning-manage-workspace.md
[Handbuch zur Problembehandlung: Erstellen und Verbinden eines Machine Learning-Arbeitsbereichs]: machine-learning-troubleshooting-creating-ml-workspace.md
 
<!-- ![List of Machine Learning workspaces][2] -->

<!--Anchors-->
[To create a workspace]: #createworkspace

<!--Image references-->
[1]: media/machine-learning-create-workspace/cw1.png
[2]: media/machine-learning-create-workspace/cw2.png
[3]: media/machine-learning-create-workspace/cw3.png



<!--Link references-->

<!---HONumber=July15_HO3-->