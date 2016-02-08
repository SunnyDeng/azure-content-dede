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
    ms.date="11/10/2015"
    ms.author="sngun" />

# Integration der Quellcodeverwaltung in Azure Automation

Durch die Integration der Quellcodeverwaltung können Sie Runbooks in Ihrem Automation-Konto einem GitHub-Repository für die Quellcodeverwaltung zuordnen. Sie können mit der Quellcodeverwaltung einfach mit Ihrem Team zusammenarbeiten, Änderungen nachverfolgen und Ihre Runbooks auf frühere Versionen zurücksetzen. Die Quellcodeverwaltung ermöglicht beispielsweise das Synchronisieren verschiedener Verzweigungen der Quellcodeverwaltung Ihrer Automation-Konten für Entwicklung, Test oder Produktion. Dadurch können Sie Code, der in Ihrer Entwicklungsumgebung getestet wurde, in Ihr Automation-Produktionskonto hochstufen.

Mit der Quellcodeverwaltung können Sie Code aus Azure Automation in die Quellcodeverwaltung übertragen oder Ihre Runbooks aus der Quellcodeverwaltung in Azure Automation verschieben. Dieser Artikel beschreibt die Quellcodeverwaltung in Ihrer Azure-Automation-Umgebung. Wir beginnen zunächst mit dem Konfigurieren von Azure Automation, um Zugriff auf Ihr GitHub-Repository zu erhalten, und durchlaufen dann verschiedene Vorgänge, die mithilfe einer Quellcodeverwaltung möglich sind.


>[AZURE.NOTE] Die Quellcodeverwaltung unterstützt das Abrufen und Verschieben von [PowerShell-Workflow-Runbooks](automation-runbook-types.md#powershell-workflow-runbooks) sowie [PowerShell-Runbooks](automation-runbook-types.md#powershell-runbooks). [Grafische Runbooks](automation-runbook-types.md#graphical-runbooks) werden noch nicht unterstützt.<br><br>


Sie müssen nur zwei Schritte ausführen, um die Quellcodeverwaltung für Ihr Automation-Konto zu konfigurieren. Wenn Sie ein GitHub-Konto besitzen, ist nur ein Schritt erforderlich. Sie lauten wie folgt:
## Schritt 1: Erstellen eines GitHub-Repositorys

Wenn Sie bereits ein GitHub-Konto und ein Repository besitzen, das Sie mit Azure Automation verknüpfen möchten, dann melden Sie sich bei Ihrem vorhandenen Konto an, und beginnen Sie mit Schritt 2. Navigieren Sie andernfalls zu [GitHub](https://github.com/), registrieren Sie sich für ein neues Konto, und [erstellen Sie ein neues Repository](https://help.github.com/articles/create-a-repo/).


## Schritt 2: Einrichten der Quellcodeverwaltung in Azure Automation

1. Klicken Sie im Azure-Vorschauportal auf dem Blatt „Automation-Konto“ auf **Quellcodeverwaltung einrichten**. 
 
    ![Einrichten der Quellcodeverwaltung](media/automation-source-control-integration/automation_01_SetUpSourceControl.png)

2. Das Blatt **Quellcodeverwaltung** wird geöffnet. Hier können Sie Ihre Kontodaten für GitHub konfigurieren. Nachfolgend sehen Sie die Liste der zu konfigurierenden Parameter:

    |**Parameter** |**Beschreibung** |
    |:---|:---| 
    |Quelle auswählen | Wählen Sie die Quelle aus. Derzeit wird nur **GitHub** unterstützt. |
    |Autorisierung | Klicken Sie auf die Schaltfläche **Autorisieren**, um Azure Automation Zugriff auf Ihr GitHub-Repository zu gewähren. Wenn Sie bereits in einem anderen Fenster bei Ihrem GitHub-Konto angemeldet sind, werden die Anmeldeinformationen dieses Kontos verwendet. Nach erfolgreicher Autorisierung zeigt das Blatt unter **Authorization Property** Ihren GitHub-Benutzernamen an. |
    |Repository auswählen | Wählen Sie ein GitHub-Repository aus der Liste der verfügbaren Repositorys aus. |
    |Verzweigung auswählen | Wählen Sie eine Verzweigung aus der Liste der verfügbaren Verzweigungen aus. Wenn Sie keine Verzweigungen erstellt haben, wird nur die **master**-Verzweigung angezeigt. |
    |Runbook-Ordnerpfad | Der Runbook-Ordnerpfad gibt den Pfad im GitHub-Repository zum Abrufen und Verschieben Ihres Codes an. Er muss im Format **/Ordnername/Unterordnername** eingegeben werden. Nur Runbooks im Runbook-Ordnerpfad werden mit Ihrem Automation-Konto synchronisiert. Runbooks in den Unterordnern des Runbook-Ordnerpfads werden **NICHT** synchronisiert. Mit **/** synchronisieren Sie alle Runbooks im Repository. |


3. Nehmen wir an, Sie haben ein Repository **PowerShell-Skripts** genannt. Dieses enthält einen Ordner namens **Stammordner**, der wiederum einen Ordner namens **Unterordner** enthält. Mit den folgenden Zeichenfolgen können Sie jede der Ordnerebenen synchronisieren:

    1. Der Pfad zum Synchronisieren von Runbooks im gesamten **Repository** lautet */*.
    2. Der Pfad zum Synchronisieren von Runbooks im **Stammordner** lautet */Stammordner*.
    3. Der Pfad zum Synchronisieren von Runbooks im **Unterordner** lautet */Stammordner/Unterordner*.
  

4. Nach dem Konfigurieren der Parameter werden sie auf dem Blatt **Quellcodeverwaltung einrichten** angezeigt.
 
    ![Blatt „Konfigurieren“](media/automation-source-control-integration/automation_02_SourceControlConfigure.png)


5. Nach dem Klicken auf „OK“ ist die Integration der Quellcodeverwaltung für Ihr Automation-Konto konfiguriert und sollte mit Ihren GitHub-Informationen aktualisiert sein. Sie können jetzt auf diesen Teil klicken, um den gesamten Verlauf Ihrer Synchronisierungsaufträge für die Quellcodeverwaltung anzuzeigen.

    ![Repository-Werte](media/automation-source-control-integration/automation_03_RepoValues.png)

6. Nach dem Einrichten der Quellcodeverwaltung werden die folgenden Automation-Ressourcen in Ihrem Automation-Konto erstellt: Zwei [Variablenassets](automation-variables.md) werden erstellt.
      
    * Die Variable **Microsoft.Azure.Automation.SourceControl.Connection** enthält die Werte der Verbindungszeichenfolge (wie unten dargestellt).  

    |**Parameter** |**Wert** |
    |:---|:---|
    | Name | Microsoft.Azure.Automation.SourceControl.Connection |
    | Typ | String |
    | Wert | {„Verzweigung“:<*Name Ihrer Verzweigung*>, „Runbook-Ordnertyp“:<*Runbook-Ordnerpfad*>, „Anbietertyp“:<*hat einen Wert 1 für GitHub*>, „Verzeichnis“:<*Name Ihres Repositorys*>, „Benutzername“<*Ihr GitHub-Benutzername*>} | <br>


    * Die Variable **Microsoft.Azure.Automation.SourceControl.OauthToken**, enthält den sicher verschlüsselten Wert Ihres OAuthTokens.  

    |**Parameter** |**Wert** |
    |:---|:---|
    | Name | Microsoft.Azure.Automation.SourceControl.OauthToken |
    | Typ | Unbekannte (Verschlüsselt) |
    | Wert | <*Verschlüsseltes OAuthToken*> |  

    ![Variablen](media/automation-source-control-integration/automation_04_Variables.png)

    * **Automation-Quellcodeverwaltung** wird Ihrem GitHub-Konto als autorisierte Anwendung hinzugefügt. So zeigen Sie die Anwendung an: Navigieren Sie auf Ihrer GitHub-Startseite zu **Profil** > **Einstellungen** > **Anwendungen**. Diese Anwendung ermöglicht Azure-Automation die Synchronisierung Ihres GitHub-Repositorys mit einem Automation-Konto.  

    ![Git-Anwendung](media/automation-source-control-integration/automation_05_GitApplication.png)


## Verwenden der Quellcodeverwaltung in Automation


### Einchecken eines Runbooks aus Azure Automation in die Quellcodeverwaltung

Durch Einchecken des Runbooks können Sie die Änderungen, die Sie an einem Runbook in Azure Automation vorgenommen haben, in das Quellcodeverwaltungs-Repository übertragen. Mit den folgenden Schritten können Sie ein Runbook einchecken:

1. In Ihrem Automation-Konto können Sie [ein neues Textrunbook erstellen](automation-first-runbook-textual.md) oder [ein vorhandenes Textrunbook bearbeiten](automation-edit-textual-runbook.md). Dieses Runbook kann ein PowerShell-Workflow oder eines PowerShell-Skript-Runbook sein.  

2. Speichern Sie Ihr Runbook nach dem Bearbeiten, und klicken Sie auf **Einchecken** auf dem Blatt **Bearbeiten**.

    ![Schaltfläche „Einchecken“](media/automation-source-control-integration/automation_06_CheckinButton.png)


     >[AZURE.NOTE] Beim Einchecken aus Azure Automation wird der derzeit in Ihrer Quellcodeverwaltung vorhandene Code überschrieben. Die entsprechende Git-Befehlszeile zum Einchecken lautet **git add + git commit + git push**.

3. Wenn Sie auf **Einchecken** klicken, wird eine Bestätigungsmeldung angezeigt. Klicken Sie zum Fortfahren auf „Ja“.

    ![Meldung beim Einchecken](media/automation-source-control-integration/automation_07_CheckinMessage.png)

4. Beim Einchecken wird das Quellcodeverwaltungs-Runbook gestartet: **Sync-MicrosoftAzureAutomationAccountToGitHubV1**. Dieses Runbook stellt eine Verbindung zu GitHub her und überträgt Änderungen in Azure Automation in Ihr Repository. Wenn Sie den Verlauf des Eincheckvorgangs anzeigen möchten, wechseln Sie zurück zur Registerkarte **Integration der Quellcodeverwaltung**, und klicken Sie, um das Blatt „Repository Synchronization“ zu öffnen. Dieses Fenster zeigt alle Quellcodeverwaltungsaufträge. Wählen Sie den Auftrag, den Sie anzeigen möchten, und klicken Sie, um die Details anzuzeigen.

    ![Runbook zum Einchecken](media/automation-source-control-integration/automation_08_CheckinRunbook.png)

    >[AZURE.NOTE] Quellcodeverwaltungs-Runbooks sind spezielle Automation-Runbooks, die Sie weder anzeigen noch bearbeiten können. Obwohl sie nicht in Ihrer Runbookliste angezeigt werden, enthält die Auftragsliste Synchronisierungsaufträge.
 
5. Der Name des geänderten Runbooks wird als Eingabeparameter an das Runbook „Einchecken“ gesendet. Sie können [die Auftragsdetails anzeigen](automation-runbook-execution.md#viewing-job-status-using-the-azure-management-portal), indem Sie das Runbook auf dem Blatt **Repository Synchronization** erweitern.

    ![Eingabe zum Einchecken](media/automation-source-control-integration/automation_09_CheckinInput.png)

6. Aktualisieren Sie das GitHub-Repository nach Abschluss des Auftrags, um die Änderungen anzuzeigen. Ihr Repository sollte ein Commit mit der Meldung darüber enthalten, dass ***Runbookname* in Azure Automation aktualisiert** wurde.



### Synchronisieren von Runbooks aus der Quellcodeverwaltung in Azure Automation 

Mit der Synchronisierungsschaltfläche auf dem Blatt „Repository-Synchronisierung“ können Sie alle Runbooks im Runbook-Ordnerpfad des Repositorys in Ihr Automation-Konto verschieben. Dasselbe Repository kann mit mehreren Automation-Konten synchronisiert werden. Mit den folgenden Schritten können Sie ein Runbook synchronisieren:

1. Öffnen Sie in dem Automation-Konto, in dem Sie die Quellcodeverwaltung einrichten, das Blatt **Integration der Quellcodeverwaltung/Repository Synchronization**, und klicken Sie auf **Synchronisieren**. Klicken Sie in der angezeigten Bestätigungsmeldung auf **Ja**, um den Vorgang fortzusetzen.  

    ![Taste „Synchronisierung“](media/automation-source-control-integration/automation_10_SyncButtonwithMessage.png)

2. Die Synchronisierung startet das Runbook **Sync-MicrosoftAzureAutomationAccountFromGitHubV1**. Dieses Runbook stellt eine Verbindung zu GitHub her und überträgt die Änderungen aus Ihrem Repository in Azure Automation. Sie sollten daraufhin einen neuen Auftrag auf dem Blatt **Repository Synchronization** für diese Aktion sehen. Um Details zum Synchronisierungsauftrag anzuzeigen, klicken Sie, und öffnen Sie das Blatt „Auftragsdetails“.
 
    ![Runbook synchronisieren](media/automation-source-control-integration/automation_11_SyncRunbook.png)

 
    >[AZURE.NOTE] Die Synchronisierung der Quellcodeverwaltung überschreibt die Entwurfsversion für **ALLE** Runbooks in Ihrem Automation-Konto, die derzeit in der Quellcodeverwaltung vorhanden sind. Die entsprechende Git-Befehlszeile zum Synchronisieren lautet **git pull**.


## Problembehandlung bei der Quellcodeverwaltung

Wenn Fehler mit dem Einchecken oder Synchronisieren auftreten, wird der Auftragsstatus angehalten, und Sie können im Auftragsblatt weitere Einzelheiten zu dem Fehler anzeigen. Unter **Alle Protokolle** können Sie alle PowerShell-Streams einsehen, die diesem Auftrag zugeordnet sind. Sie finden dort die erforderlichen Details, um Probleme mit dem Einchecken oder Synchronisieren zu beheben. Sie sehen dort auch die Abfolge der Aktionen, die beim Synchronisieren oder Einchecken eines Runbooks aufgetreten sind.

![Bild „Alle Protokolle“](media/automation-source-control-integration/automation_13_AllLogs.png)

## Trennen der Verbindung zur Quellcodeverwaltung

Klicken Sie zum Trennen der Verbindung zu Ihrem GitHub-Konto auf dem Blatt „Repository Synchronization“ auf **Trennen**. Nach dem Trennen der Verbindung mit der Quellcodeverwaltung verbleiben zuvor synchronisierte Runbooks weiterhin in Ihrem Automation-Konto, aber das Blatt „Repository Synchronization“ ist nicht aktiviert.

  ![Schaltfläche „Trennen“](media/automation-source-control-integration/automation_12_Disconnect.png)



## Nächste Schritte

Weitere Informationen zur Integration der Quellcodeverwaltung finden Sie in den folgenden Ressourcen: - [Source Control Integration in Azure Automation](https://azure.microsoft.com/blog/azure-automation-source-control-13/) - [Vote for your favorite source control system](https://www.surveymonkey.com/r/?sm=2dVjdcrCPFdT0dFFI8nUdQ%3d%3d) - [Azure Automation: Integrating Runbook Source Control using Visual Studio Online](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/) (alle in englischer Sprache)

<!---HONumber=AcomDC_0128_2016-->