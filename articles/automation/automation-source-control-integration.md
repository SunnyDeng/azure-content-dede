<properties 
    pageTitle="Integration der Quellcodeverwaltung in Azure Automation | Microsoft Azure"
    description="In diesem Artikel wird die Integration der Quellcodeverwaltung mit GitHub in Azure Automation erläutert."
    services="automation"
    documentationCenter=""
    authors="SnehaGunda"
    manager="stevenka"
    editor="tysonn" />    
<tags 
    ms.service="automation"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="11/04/2015"
    ms.author="sngun" />

# Integration der Quellcodeverwaltung in Azure Automation

Durch die Integration der Quellcodeverwaltung können Sie Runbooks und PowerShell-Skripts in Ihrem Automation-Konto einem GitHuB-Repository für die Quellcodeverwaltung zuordnen. Sie bietet Ihnen eine Umgebung, in der Sie einfach mit Ihrem Team zusammenarbeiten, Änderungen nachverfolgen und Ihre Runbooks auf frühere Versionen zurücksetzen können. Die Quellcodeverwaltung ermöglicht beispielsweise das Synchronisieren verschiedener Verzweigungen Ihrer Automation-Konten für Entwicklung, Test oder Produktion. Dadurch können Sie Code in die Produktionsumgebung hochstufen, die in Ihrer Entwicklungsumgebung getestet wurde. Sie können Code aus Azure Automation in die Quellcodeverwaltung übertragen oder Ihre Runbooks aus der Quellcodeverwaltung in Azure Automation verschieben. Dieser Artikel beschreibt die Quellcodeverwaltung in Ihrer Azure-Automation-Umgebung. Wir beginnen mit dem Erstellen eines GitHub-Repositorys und fahren mit der Konfiguration von Azure Automation für den Zugriff auf Ihre GitHub-Repositorys fort.


>[AZURE.NOTE]Die Quellcodeverwaltung unterstützt das Abrufen und Verschieben von [PowerShell-Workflow-Runbooks](automation-runbook-types.md#powershell-workflow-runbooks) sowie [PowerShell-Runbooks](automation-runbook-types.md#powershell-runbooks). [Grafische Runbooks](automation-runbook-types.md#graphical-runbooks) werden noch nicht unterstützt.<br><br>


Sie müssen nur zwei Schritte ausführen, um die Quellcodeverwaltung für Ihr Automation-Konto zu konfigurieren. Wenn Sie ein GitHub-Konto besitzen, ist nur ein Schritt erforderlich. Sie lauten wie folgt:
## Schritt 1: Erstellen eines GitHub-Repositorys

Wenn Sie bereits ein GitHub-Konto und ein Repository besitzen, das Sie mit Azure Automation verknüpfen möchten, dann melden Sie sich bei Ihrem vorhandenen Konto an, und beginnen Sie mit Schritt 2. Navigieren Sie andernfalls zu [GitHub](https://github.com/), registrieren Sie sich für ein neues Konto und [erstellen Sie ein neues Repository](https://help.github.com/articles/create-a-repo/).


## Schritt 2: Einrichten der Quellcodeverwaltung

* Klicken Sie im Azure-Vorschauportal auf dem Blatt „Automation-Konto“ auf **Quellcodeverwaltung**<br> ![Einrichten der Quellcodeverwaltung](media/automation-source-control-integration/automation_01_SetUpSourceControl.png)
* Das Blatt **Quellcodeverwaltung** wird geöffnet. Hier können Sie Ihre Kontodaten für GitHub konfigurieren. Nachfolgend sehen Sie die Liste der zu konfigurierenden Parameter:<br>

|Parameter |Beschreibung |
|:---|:---| 
|Quelle auswählen | Wählen Sie die Quelle aus. Derzeit wird nur **GitHub** unterstützt. |
|Autorisierung | Klicken Sie auf die Schaltfläche **Autorisieren**, um Azure Automation Zugriff auf Ihr GitHub-Repository zu gewähren. Wenn Sie bereits in einem anderen Fenster bei Ihrem GitHub-Konto angemeldet sind, werden die Anmeldeinformationen dieses Kontos verwendet. Nach erfolgreicher Autorisierung zeigt das Fenster Ihren GitHub-Benutzernamen unter **Autorisierungseigenschaft** an. |
|Repository auswählen | Wählen Sie ein GitHub-Repository aus der Liste der verfügbaren Repositorys aus. |
|Verzweigung auswählen | Wählen Sie eine Verzweigung aus der Liste der verfügbaren Verzweigungen aus. Wenn Sie keine Verzweigungen erstellt haben, wird nur die **master**-Verzweigung angezeigt. |
|Runbook-Ordnerpfad | Der Runbook-Ordnerpfad gibt den Pfad im GitHub-Repository zum Abrufen und Verschieben Ihres Codes an. Er muss im Format **/Ordnername/Unterordnername** eingegeben werden. Nur Skripts im Runbook-Ordnerpfad werden mit Ihrem Automation-Konto synchronisiert. Skripts in den Unterordnern des Runbook-Ordnerpfads werden **NICHT** synchronisiert. Mit **/** synchronisieren Sie alle PowerShell-Skripts im Repository. |


Nehmen wir an, Sie haben ein Repository **PowerShell-Skripts** genannt. Dieses enthält einen Ordner namens **Stammordner**, der wiederum einen Ordner namens **Unterordner** enthält. Wenn alle Ordner und das Repository Runbooks enthalten, die Sie synchronisieren möchten:

  1. Der Pfad zum Synchronisieren von Runbooks aus **Repository**lautet */*
  2. Der Pfad zum Synchronisieren von Runbooks aus **Stammordner**lautet */Stammordner*
  3. Der Pfad zum Synchronisieren von Runbooks aus **Unterordner**lautet */Unterordner*.<br>

* Nach dem Konfigurieren der Parameter werden sie auf dem Blatt **Einrichten der Quellcodeverwaltung** angezeigt.

![Blatt „Konfigurieren“](media/automation-source-control-integration/automation_02_SourceControlConfigure.png)


* Die Integration der Quellcodeverwaltung ist jetzt für Ihr Automation-Konto konfiguriert und sollte mit Ihren GitHub-Informationen aktualisiert sein. Sie können jetzt auf diesen Teil klicken, um all Ihre Quellcodeverwaltung-Synchronisierungsaufträge anzuzeigen. <br> ![Repository-Werte](media/automation-source-control-integration/automation_03_RepoValues.png)

* Nach dem Einrichten der Quellcodeverwaltung werden die folgenden Automation-Ressourcen in Ihrem Automation-Konto erstellt:

Zwei [variable Objekte](automation-variables.md) werden erstellt.
      

  * Die Variable **Microsoft.Azure.Automation.SourceControl.Connection** enthält die Werte der Verbindungszeichenfolge (wie unten dargestellt).<br>

|Parameter |Wert |
|:---|:---|
| Name | Microsoft.Azure.Automation.SourceControl.Connection |
| Typ | String |
| Wert | {„Verzweigung“:<*Name Ihrer Verzweigung*>, „Runbook-Ordnertyp“:<*Runbook-Ordnerpfad*>, „Anbietertyp“:<*hat einen Wert 1 für GitHub*>, „Verzeichnis“:<*Name Ihres Repositorys*>, „Benutzername“<*Ihr GitHub-Benutzername*>} |


  * Die Variable **Microsoft.Azure.Automation.SourceControl.OauthToken**, enthält den sicher verschlüsselten Wert Ihres OAuthTokens. <br>

|Parameter |Wert |
|:---|:---|
| Name | Microsoft.Azure.Automation.SourceControl.OauthToken |
| Typ | Unbekannte (Verschlüsselt) |
| Wert | <*Verschlüsseltes OAuthToken*> |

![Variablen](media/automation-source-control-integration/automation_04_Variables.png)

**Automation-Quellcodeverwaltung** wird als autorisierte Anwendung zu Ihrem GitHub-Konto hinzugefügt. Anzeigen der Anwendung: Navigieren Sie auf Ihrer der GitHub-Startseite zu Ihrem **Profil** > **Einstellungen** > **Anwendungen**. Diese Anwendung ermöglicht Azure-Automation die Synchronisierung Ihres GitHub-Repositorys mit einem Automation-Konto.

![Git-Anwendung](media/automation-source-control-integration/automation_05_GitApplication.png)


## Verwenden der Quellcodeverwaltung in Automation


### Einchecken eines Runbooks aus Azure Automation in die Quellcodeverwaltung

Durch Einchecken des Runbooks können Sie die Änderungen übertragen, die Sie an einem Runbook in Quellcodeverwaltungsrepository vorgenommen haben. Mit den folgenden Schritten können Sie ein Runbook einchecken:

* In Ihrem Automation-Konto können Sie [ein neues Textrunbook erstellen](automation-first-runbook-textual.md), oder [ein vorhandenes Textrunbook bearbeiten](automation-edit-textual-runbook.md). Dieses Runbook kann ein PowerShell-Workflow oder eines PowerShell-Skript-Runbook sein.  
* Speichern Sie Ihr Runbook nach dem Bearbeiten, und klicken Sie auf **Einchecken** auf dem Blatt **Bearbeiten**. ![Schaltfläche „Einchecken“](media/automation-source-control-integration/automation_06_CheckinButton.png)


 >[AZURE.NOTE]Beim Einchecken aus Azure Automation wird der derzeit in Ihrer Quellcodeverwaltung vorhandene Code überschrieben. Die entsprechende Git-Befehlszeile zum Einchecken lautet **git add + git commit + git push**.

* Wenn Sie auf **Einchecken** klicken, wird eine Meldung angezeigt. Klicken Sie auf „Ja“, um fortzufahren. ![Meldung beim Einchecken](media/automation-source-control-integration/automation_07_CheckinMessage.png)
* Beim Einchecken startet das Runbook: **Sync-MicrosoftAzureAutomationAccountToGitHubV1**. Dieses Runbook stellt eine Verbindung zu GitHub her und überträgt Änderungen in Azure Automation in Ihr Repository. Wenn Sie den Verlauf des Eincheckvorgangs anzeigen möchten, klicken Sie auf der Registerkarte **Integration der Quellcodeverwaltung**, um das Fenster „Repository-Synchronisierung“ zu öffnen. Dieses Fenster zeigt alle Quellcodeverwaltungsaufträge. Wählen Sie den Auftrag, den Sie anzeigen möchten, und klicken Sie, um die Details anzuzeigen. ![Runbook zum Einchecken](media/automation-source-control-integration/automation_08_CheckinRunbook.png)
* Wenn Fehler mit dem Einchecken auftreten, wird der Auftragsstatus angehalten, und Sie können im Auftragsfenster weitere Einzelheiten zu dem Fehler anzeigen. Unter **Alle Protokolle** können Sie alle PowerShell-Streams einsehen, die diesem Auftrag zugeordnet sind. Dadurch erhalten Sie Informationen, die Ihnen bei der Fehlerbehebung helfen.  
* Der Name des geänderten Runbooks wird als Eingabeparameter an das Runbook „Einchecken“ gesendet. Sie können [die Auftragsdetails anzeigen](automation-runbook-execution.md#viewing-job-status-using-the-azure-management-portal), indem Sie das Runbook auf dem Blatt **Repository-Synchronisierung** erweitern. ![Eingabe zum Einchecken](media/automation-source-control-integration/automation_09_CheckinInput.png)
* Aktualisieren Sie das GitHub-Repository nach Abschluss des Auftrags, um die Änderungen anzuzeigen. Ihr Repository sollte eine Commit mit einer Commit-Nachricht enthalten: ***Runbookname* in Azure Automation aktualisiert**.  



### Synchronisieren von Runbooks aus der Quellcodeverwaltung in Azure Automation 

Mit der Synchronisierungsschaltfläche auf dem Blatt „Runbook-Synchronisierung“ können Sie alle Runbooks im Runbook-Ordnerpfad des Repositorys in Ihr Automation-Konto verschieben. Dasselbe Repository kann mit mehreren Automation-Konten synchronisiert werden. Mit den folgenden Schritten können Sie ein Runbook synchronisieren:

* Öffnen Sie in dem Automation-Konto, in dem Sie Quellcodeverwaltung einrichten, das Blatt **Integration der Quellcodeverwaltung/Repository-Synchronisierung**, und klicken Sie auf **Synchronisieren**. Klicken Sie in der angezeigten Meldung auf **Ja**, um den Vorgang fortzusetzen. ![Taste „Synchronisierung“](media/automation-source-control-integration/automation_10_SyncButtonwithMessage.png)
* Die Synchronisierung startet das Runbook: **Sync-MicrosoftAzureAutomationAccountFromGitHubV1**. Dieses Runbook stellt eine Verbindung zu GitHub her und überträgt Änderungen aus Ihrem Repository in Azure Automation. Erweitern Sie zum Anzeigen des Runbooks das Blatt **Integration der Quellcodeverwaltung/Repository-Synchronisierung**. ![Runbook synchronisieren](media/automation-source-control-integration/automation_11_SyncRunbook.png)

 
>[AZURE.NOTE]Die Synchronisierung der Quellcodeverwaltung überschreibt die Entwurfsversion der Runbooks in Ihrem Automation-Konto für **ALLE** Runbooks, die derzeit in der Quellcodeverwaltung vorhanden sind. Die entsprechende Git-Befehlszeile zum Synchronisieren lautet **gitpull**.


## Trennen der Verbindung zur Quellcodeverwaltung

Klicken Sie zum Trennen der Verbindung zu Ihrem GitHub-Konto auf dem Blatt „Repository-Synchronisierung“ auf **Trennen**. Nach dem Trennen der Quellcodeverwaltung verbleiben zuvor synchronisierte Runbooks weiterhin in Ihrem Automation-Konto, aber das Blatt „Repository-Synchronisierung“ ist nicht aktiviert. ![Schaltfläche „Trennen“](media/automation-source-control-integration/automation_12_Disconnect.png)



## Nächste Schritte

Weitere Informationen zur Integration der Quellcodeverwaltung finden Sie in den folgenden Ressourcen:- [Azure Automation: Integration der Quellcodeverwaltung in Azure Automation](https://azure.microsoft.com/blog/azure-automation-source-control-13/) - [Abstimmen für Ihr bevorzugtes Quellcodeverwaltungssystem](https://www.surveymonkey.com/r/?sm=2dVjdcrCPFdT0dFFI8nUdQ%3d%3d) - [Azure Automation: Integrieren der Quellcodeverwaltung mit Visual Studio Online](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/)

<!---HONumber=Nov15_HO3-->