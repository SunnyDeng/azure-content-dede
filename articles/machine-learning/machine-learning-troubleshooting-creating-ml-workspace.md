<properties
	pageTitle="Problembehandlung: Erstellen eines Azure Machine Learning-Arbeitsbereichs und Verbindungsaufbau | Microsoft Azure"
	description="Lösungen für häufige Probleme beim Erstellen eines Azure Machine Learning-Arbeitsbereichs und beim Herstellen einer Verbindung zu diesem Bereich"
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
	ms.date="03/09/2016"
	ms.author="garye"/>


# Handbuch zur Problembehandlung: Erstellen eines Azure Machine Learning-Arbeitsbereichs und Verbindungsaufbau

Dieser Leitfaden bietet Lösungen für einige gängige Schwierigkeiten beim Einrichten von Azure Machine Learning-Arbeitsbereichen.

[AZURE.INCLUDE [machine-learning-kostenlose-Testversion](../../includes/machine-learning-free-trial.md)]

## Besitzer des Arbeitsbereichs

Beim Erstellen eines neuen Azure Machine Learning-Arbeitsbereichs müssen Sie im Feld "WORKSPACE OWNER" ein gültiges Microsoft-Konto (ehemals "Windows Live ID") eingeben, z. B. john-contoso@live.com oder john-contoso@hotmail.com. Es muss ein Microsoft-Konto sein, z. B. kann es nicht Ihr Unternehmens-E-Mail-Konto sein. Besuchen Sie [www.live.com](http://www.live.com), um ein kostenloses Microsoft-Konto zu erstellen.

Beachten Sie, dass das Konto, mit dem Sie sich beim klassischen Azure-Portal angemeldet haben, um einen Arbeitsbereich zu erstellen, nicht automatisch über die Berechtigung zum *Öffnen* dieses Arbeitsbereichs verfügt, sofern Sie nicht dieses Konto als Besitzer angeben. Um einen Arbeitsbereich in Machine Learning Studio zu öffnen, müssen Sie bei dem Microsoft-Konto angemeldet sein, das als Besitzer des Arbeitsbereichs definiert wurde, oder Sie müssen eine Einladung des Besitzers zur Teilnahme am Arbeitsbereich erhalten haben. Sie können jedoch im klassischen Azure-Portal den Arbeitsbereich *verwalten*, einschließlich der Möglichkeit zum Ändern des Besitzers und der Zugriffskonfiguration.

Weitere Informationen zum Verwalten eines Arbeitsbereichs finden Sie unter [Verwalten eines Azure Machine Learning-Arbeitsbereichs].

[Verwalten eines Azure Machine Learning-Arbeitsbereichs]: machine-learning-manage-workspace.md

## Zulässige Regionen

Machine Learning ist derzeit in einer begrenzten Anzahl an Regionen verfügbar. Falls Ihr Abonnement keine dieser Regionen beinhaltet, wird eventuell die Fehlermeldung „Sie verfügen über keine Abonnements für die zulässigen Regionen“ angezeigt.

Um anzufordern, dass Ihrem Abonnement eine Region hinzugefügt wird, wählen Sie im klassischen Azure-Portal **Microsoft-Support kontaktieren** aus und dann **Abrechnung** als Problemtyp. Folgen Sie dann den Anweisungen, um Ihre Anfrage zu übermitteln.

![Microsoft-Support kontaktieren][screen1]

## Speicherkonto

Der Machine Learning-Dienst benötigt ein Speicherkonto zum Speichern von Daten. Sie können ein bestehendes Speicherkonto verwenden oder ein neues Speicherkonto erstellen, wenn Sie den neuen Machine Learning-Arbeitsbereich einrichten (falls Ihr Kontingent ausreicht, um ein neues Speicherkonto zu erstellen).

<!-- These instructions no longer work, but I'm not sure what to replace them with
To see if you can create a new storage account, in the Classic Portal, go to **Settings** and then click **Usage**.
-->

![Arbeitsbereich erstellen][screen2]

Nachdem Sie den neuen Machine Learning-Arbeitsbereich erstellt haben, können Sie sich mit dem Microsoft-Konto in Machine Learning Studio anmelden, das Sie als Besitzer des Arbeitsbereichs angegeben haben. Wenn die Fehlermeldung "Arbeitsbereich wurde nicht gefunden." (ähnlich wie im folgenden Screenshot) angezeigt wird, führen Sie die folgenden Schritte durch, um Ihre Browsercookies zu löschen.

![Arbeitsbereich nicht gefunden][screen3]

**So löschen Sie Ihre Browsercookies**

Falls Sie Internet Explorer verwenden, klicken Sie oben rechts auf **Extras** und anschließend auf **Internetoptionen**.

![Internetoptionen][screen4]

Klicken Sie in der Registerkarte **Allgemein** auf **Löschen...**

![Registerkarte "Allgemein"][screen5]

Wählen Sie im Dialogfeld **Browserverlauf löschen** den Eintrag **Cookies und Websitedaten** aus und klicken Sie auf **Löschen**.

![Cookies löschen][screen6]

Starten Sie nach dem Löschen der Cookies den Browser neu, und wechseln Sie anschließend zur Seite [Microsoft Azure Machine Learning](https://studio.azureml.net). Wenn Sie zur Eingabe von Benutzername und Kennwort aufgefordert werden, geben Sie das Microsoft-Konto ein, das Sie als Besitzer des Arbeitsbereichs eingerichtet haben.

Es ist unser Ziel, die Machine Learning-Erfahrung so glatt und nahtlos wie möglich zu gestalten. Bitte schreiben Sie uns Ihre Kommentare und Probleme im [Azure Machine Learning-Forum](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning), um uns zu helfen, den Dienst zu verbessern.

[screen1]: media/machine-learning-troubleshooting-creating-ml-workspace/screen1.png
[screen2]: media/machine-learning-troubleshooting-creating-ml-workspace/screen2.png
[screen3]: media/machine-learning-troubleshooting-creating-ml-workspace/screen3.png
[screen4]: media/machine-learning-troubleshooting-creating-ml-workspace/screen4.png
[screen5]: media/machine-learning-troubleshooting-creating-ml-workspace/screen5.png
[screen6]: media/machine-learning-troubleshooting-creating-ml-workspace/screen6.png

<!---HONumber=AcomDC_0316_2016-->