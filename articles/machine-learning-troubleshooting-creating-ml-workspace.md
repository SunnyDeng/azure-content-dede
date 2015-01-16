<properties title="Troubleshooting Guide: Creating and connecting to an Azure Machine Learning workspace" pageTitle="Handbuch zur Problembehandlung: Erstellen eines Azure Machine Learning-Arbeitsbereichs und Verbindungsaufbau | Azure" description="Lösungen für häufige Probleme beim Erstellen eines Azure Machine Learning-Arbeitsbereichs und beim Herstellen einer Verbindung zu diesem Bereich " metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/25/2014" ms.author="garye" />


#Handbuch zur Problembehandlung: Erstellen eines Azure Machine Learning-Arbeitsbereichs und Verbindungsaufbau

Dieser Leitfaden bietet Lösungen für einige gängige Schwierigkeiten beim Einrichten von Azure ML-Arbeitsbereichen.

##Besitzer des Arbeitsbereichs

Bei der Erstellung eines neuen Azure ML-Arbeitsbereichs müssen Sie in das Feld BESITZER DES ARBEITSBEREICHS ein gültiges Microsoft-Konto (ehemals Windows Live ID) eingeben, wie z. B. john-contoso@live.com oder john-contoso@hotmail.com. Es muss ein Microsoft-Konto sein, z. B. kann es nicht Ihr Unternehmens-E-Mail-Konto sein. Besuchen Sie [www.live.com](http://www.live.com), um ein kostenloses Microsoft-Konto zu erstellen. 

##Zulässige Regionen

Azure ML befindet sich momentan in der Region USA (Mitte/Süden) in der Vorschauphase. Falls Ihr Abonnement die Region USA (Mitte/Süden) nicht beinhaltet, erhalten Sie eine Fehlermeldung "Sie besitzen kein Abonnement in den zulässigen Regionen. Zulässige Regionen: USA (Mitte/Süden)." 

Wählen Sie in diesem Fall "Microsoft-Support kontaktieren" (siehe unten) in Ihrem Azure-Portal aus und wählen Sie **Abrechnung** als **SUPPORT-TYP** aus, um diese Region zu Ihrem Abonnement hinzugefügt zu bekommen. Im Lauf der Zeit wird Azure ML in weiteren Regionen eingeführt.

![Contact Microsoft support][screen1]

##Speicherkonto
 
Der Azure ML-Dienst benötigt ein Speicherkonto zum Speichern von Daten. Sie können ein bestehendes Speicherkonto in der Region USA (Mitte/Süden) verwenden oder ein neues Speicherkonto erstellen, wenn Sie den neuen ML-Arbeitsbereich einrichten (falls Ihr Kontingent ausreicht, um ein neues Speicherkonto zu erstellen). Um herauszufinden, ob Sie ein neues Speicherkonto erstellen können, klicken Sie im Azure-Portal auf **Einstellungen** und auf **Nutzung**.

![Create workspace][screen2]

Nachdem Sie den neuen ML-Arbeitsbereich erstellt haben, können Sie sich mit dem Microsoft-Konto in ML Studio anmelden, das Sie als Besitzer des Arbeitsbereichs angegeben haben. Falls Sie den Fehler "Arbeitsbereich nicht gefunden" (siehe unten) erhalten, führen Sie die folgenden Schritte aus, um das Problem zu beheben.

![Workspace not found][screen3]

1. Löschen Sie Ihre Browsercookies.

	Falls Sie Internet Explorer verwenden, klicken Sie oben rechts auf **Extras** und anschließend auf **Internetoptionen**.  

	![Internet options][screen4]

	Klicken Sie in der Registerkarte **Allgemein** auf **Löschen...**

	![General tab][screen5]

	Wählen Sie im Dialogfeld **Browserverlauf löschen** den Eintrag **Cookies und Websitedaten** aus und klicken Sie auf **Löschen**.

	![Delete cookies][screen6]

2. Nachdem Sie Ihre Cookies gelöscht haben, starten Sie Ihren Browser neu und öffnen Sie die Adresse [https://studio.azureml.net](https://studio.azureml.net). Wenn Sie zur Eingabe von Benutzername und Kennwort aufgefordert werden, geben Sie das Microsoft-Konto ein, das Sie als Besitzer des Arbeitsbereichs eingerichtet haben.

Es ist unser Ziel, die Azure ML-Erfahrung so glatt und nahtlos wie möglich zu gestalten. Bitte schreiben Sie uns Ihre Kommentare und Probleme weiter unten oder im [Azure ML-Forum](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=MachineLearning), um uns zu helfen, den Dienst zu verbessern. 

[screen1]:./media/machine-learning-troubleshooting-creating-ml-workspace/screen1.png
[screen2]:./media/machine-learning-troubleshooting-creating-ml-workspace/screen2.png
[screen3]:./media/machine-learning-troubleshooting-creating-ml-workspace/screen3.png
[screen4]:./media/machine-learning-troubleshooting-creating-ml-workspace/screen4.png
[screen5]:./media/machine-learning-troubleshooting-creating-ml-workspace/screen5.png
[screen6]:./media/machine-learning-troubleshooting-creating-ml-workspace/screen6.png
