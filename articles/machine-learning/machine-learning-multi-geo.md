<properties
   pageTitle="Hilfedokumentation für mehrere geografische Räume | Microsoft Azure"
   description="Erfahren Sie, wie Sie einen Arbeitsbereich in einer anderen Azure-Region als SCUS (South Central-USA) erstellen und dort einen Webdienst veröffentlichen können."
   services="machine-learning"
   documentationCenter=""
   authors="tedway"
   manager="paulettm"
   editor="rmca14"
   tags=""/>

<tags
   ms.service="machine-learning"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/17/2015"
   ms.author="tedway; neerajkh"/>

# Hilfedokumentation für mehrere geografische Räume

Aktuell sind alle Ressourcen von Azure Machine Learning in der South Central-Azure-Region der USA (SCUS) gehostet. Dieser Artikel beschreibt, wie Sie in anderen Azure-Regionen einen Arbeitsbereich erstellen und einen Webdienst veröffentlichen können.

## Erstellen eines Arbeitsbereichs

1. Melden Sie sich beim Azure-Verwaltungsportal an.

2.  Klicken Sie auf **+NEW** > **DATA SERVICES** > **MACHINE LEARNING** > **QUICK CREATE**. Wählen Sie unter **LOCATION** eine andere Region, z. B. **Southeast Asia**, aus. ![Hilfe zu mehreren geografischen Räumen Bild 1][1]
3. Wählen Sie den Arbeitsbereich aus, und klicken Sie dann auf **Sign-in to ML Studio**. ![Hilfe zu mehreren geografischen Räumen Bild 2][2]

4. Sie verfügen jetzt über einen Arbeitsbereich in einer anderen geografischen Region, den Sie genau so wie jeden anderen Arbeitsbereich verwenden können. Wenn Sie zwischen Ihren Arbeitsbereichen wechseln möchten, betrachten Sie die Ecke rechts oben des Bildschirms. Klicken Sie auf die Dropdownliste, wählen Sie die Region aus, und wählen Sie dann den Arbeitsbereich aus. Alles ist bezogen auf die Region des Arbeitsbereichs lokal; z. B. befinden sich alle in einem Arbeitsbereich erstellten Webdienste in der Region, in dem sich der Arbeitsbereich befindet. ![Hilfe zu mehreren geografischen Räumen Bild 3][3]

## Öffnen Sie ein Experiment aus der Galerie

Wenn Sie ein Experiment aus der Galerie öffnen, können Sie ebenfalls auswählen, in welche Region Sie das Experiment kopieren möchten.

![Hilfe zu mehreren geografischen Räumen Bild 4][4a]

## Webdienstverwaltung

Um Webdienste programmgesteuert zu verwalten, etwa zum erneuten Trainieren, verwenden Sie die regionsspezifische Adresse: ****https://asiasoutheast.management.azureml.net**

### Was Sie beachten sollten

1.	Experimente können nur zwischen Arbeitsbereichen innerhalb der gleichen Region kopiert werden. Zukünftig werden wir auch das Kopieren von Experimenten zwischen Arbeitsbereichen aus verschiedenen Regionen ermöglichen.
2.	Die Regionsauswahl zeigt immer nur jeweils die Arbeitsbereiche einer Region an. Zukünftig können Sie die vollständige Liste aller Arbeitsbereiche, zu denen Sie Zugang haben, aus allen Regionen zugleich anzeigen.  
3.	Kostenlose Arbeitsbereiche oder Gastkonten (anonym) werden in South Central-USA erstellt und gehostet. Zukünftig werden Sie imstande sein, kostenlose bzw. Gastarbeitsbereiche in der Region Ihrer Wahl zu erstellen.  
4.	Webdienste, die aus einem Arbeitsbereich in Südostasien bereitgestellt werden, werden auch in Südostasien gehostet. Zukünftig wird es möglich sein, Experimente in einer Region zu erstellen und die generierten Webdienstendpunkte in verschiedenen Regionen bereitzustellen.  

## Weitere Informationen

Stellen Sie eine Frage im [Azure Machine Learning-Forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=MachineLearning).

<!--Image references-->
[1]: ./media/machine-learning-multi-geo/multi-geo_1.png
[2]: ./media/machine-learning-multi-geo/multi-geo_2.png
[3]: ./media/machine-learning-multi-geo/multi-geo_3.png
[4a]: ./media/machine-learning-multi-geo/multi-geo_4a.png

<!---HONumber=August15_HO8-->