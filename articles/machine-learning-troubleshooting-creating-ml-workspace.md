<properties title="Troubleshooting Guide: Creating and connecting to an Azure Machine Learning workspace" pageTitle="Troubleshooting Guide: Creating and connecting to an Azure Machine Learning workspace | Azure" description="Solutions for common issues in creating and connecting to an Azure Machine Learning workspace " metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

# Handbuch zur Problembehandlung: Erstellen eines Azure Machine Learning-Arbeitsbereichs und Verbindungsaufbau

Dieser Leitfaden bietet Lösungen für einige gängige Schwierigkeiten beim Einrichten von Azure ML-Arbeitsbereichen.

## Besitzer des Arbeitsbereichs

Bei der Erstellung eines neuen Azure ML-Arbeitsbereichs müssen Sie in das Feld BESITZER DES ARBEITSBEREICHS ein gültiges Microsoft-Konto (ehemals Windows Live ID) eingeben, wie z. B. <john-contoso@live.com> oder <john-contoso@hotmail.com>. Sie können an dieser Stelle kein externes Konto verwenden, wie z. B. das E-Mail-Konto Ihrer Firma. Besuchen Sie [www.live.com][www.live.com], um ein kostenloses Microsoft-Konto zu erstellen.

## Zulässige Regionen

Azure ML befindet sich momentan in der Region USA (Mitte/Süden) in der Vorschauphase. Falls Ihr Abonnement die Region USA (Mitte/Süden) nicht beinhaltet, erhalten Sie eine Fehlermeldung "Sie besitzen kein Abonnement in den zulässigen Regionen. Zulässige Regionen: USA (Mitte/Süden)."

Wählen Sie in diesem Fall "Microsoft-Support kontaktieren" (siehe unten) in Ihrem Azure-Portal aus und wählen Sie **Abrechnung** als **SUPPORT-TYP** aus, um diese Region zu Ihrem Abonnement hinzugefügt zu bekommen. Im Lauf der Zeit wird Azure ML in weiteren Regionen eingeführt.

![Microsoft-Support kontaktieren][Microsoft-Support kontaktieren]

## Speicherkonto

Der Azure ML-Dienst benötigt ein Speicherkonto zum Speichern von Daten. Sie können ein bestehendes Speicherkonto in der Region USA (Mitte/Süden) verwenden oder ein neues Speicherkonto erstellen, wenn Sie den neuen ML-Arbeitsbereich einrichten (falls Ihr Kontingent ausreicht, um ein neues Speicherkonto zu erstellen). Um herauszufinden, ob Sie ein neues Speicherkonto erstellen können, klicken Sie im Azure-Portal auf **Einstellungen** und auf **Nutzung**.

![Arbeitsbereich erstellen][Arbeitsbereich erstellen]

Nachdem Sie den neuen ML-Arbeitsbereich erstellt haben, können Sie sich mit dem Microsoft-Konto in ML Studio anmelden, das Sie als Besitzer des Arbeitsbereichs angegeben haben. Falls Sie den Fehler "Arbeitsbereich nicht gefunden" (siehe unten) erhalten, führen Sie die folgenden Schritte aus, um das Problem zu beheben.

![Arbeitsbereich nicht gefunden][Arbeitsbereich nicht gefunden]

1.  Löschen Sie Ihre Browsercookies.

    Falls Sie Internet Explorer verwenden, klicken Sie oben rechts auf **Extras** und anschließend auf **Internetoptionen**.

    ![Internetoptionen][Internetoptionen]

    Klicken Sie in der Registerkarte **Allgemein** auf **Löschen...**

    ![Registerkarte "Allgemein"][Registerkarte "Allgemein"]

    Wählen Sie im Dialogfeld **Browserverlauf löschen** den Eintrag **Cookies und Websitedaten** aus und klicken Sie auf **Löschen**.

    ![Cookies löschen][Cookies löschen]

2.  Nachdem Sie Ihre Cookies gelöscht haben, starten Sie Ihren Browser neu und öffnen Sie die Adresse [][]<https://studio.azureml.net></a>. Wenn Sie zur Eingabe von Benutzername und Kennwort aufgefordert werden, geben Sie das Microsoft-Konto ein, das Sie als Besitzer des Arbeitsbereichs eingerichtet haben.

Es ist unser Ziel, die Azure ML-Erfahrung so glatt und nahtlos wie möglich zu gestalten. Bitte schreiben Sie uns Ihre Kommentare und Probleme weiter unten oder im [Azure ML-Forum][Azure ML-Forum], um uns zu helfen, den Dienst zu verbessern.

  [www.live.com]: http://www.live.com
  [Microsoft-Support kontaktieren]: ./media/machine-learning-troubleshooting-creating-ml-workspace/screen1.png
  [Arbeitsbereich erstellen]: ./media/machine-learning-troubleshooting-creating-ml-workspace/screen2.png
  [Arbeitsbereich nicht gefunden]: ./media/machine-learning-troubleshooting-creating-ml-workspace/screen3.png
  [Internetoptionen]: ./media/machine-learning-troubleshooting-creating-ml-workspace/screen4.png
  [Registerkarte "Allgemein"]: ./media/machine-learning-troubleshooting-creating-ml-workspace/screen5.png
  [Cookies löschen]: ./media/machine-learning-troubleshooting-creating-ml-workspace/screen6.png
  []: https://studio.azureml.net
  [Azure ML-Forum]: http://social.msdn.microsoft.com/Forums/windowsazure/de-de/home?forum=MachineLearning
