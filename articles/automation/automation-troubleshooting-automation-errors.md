<properties 
   pageTitle="Tipps zur Problembehandlung für häufige Fehler in Azure Automation | Microsoft Azure"
   description="Dieser Artikel enthält grundlegende Problembehandlungsschritte zur Behebung häufiger Fehler, die bei der Verwendung von Azure Automation auftreten können."
   services="automation"
   documentationCenter=""
   authors="SnehaGunda"
   manager="stevenka"
   editor="tysonn" 
   tags="top-support-issue"/>
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/25/2016"
   ms.author="sngun; v-reagie"/>

# Tipps zur Problembehandlung für häufige Fehler in Azure Automation

In diesem Artikel werden einige häufige Fehler beschrieben, die bei der Arbeit mit Azure Automation auftreten können, und es werden mögliche Abhilfemaßnahmen vorgeschlagen.

## Problembehandlung für Authentifizierungsfehler bei der Arbeit mit Azure Automation-Runbooks  

### Szenario: Fehler beim Anmelden am Azure-Konto

**Fehler:** Beim Verwenden des Cmdlets Add-AzureAccount oder Login-AzureRmAccount wird der Fehler „Unknown\_user\_type: Unbekannter Benutzertyp“ ausgegeben.

**Ursache des Fehlers:** Dieser Fehler tritt auf, wenn der Assetname für die Anmeldeinformationen ungültig ist oder Benutzername und Kennwort, die Sie zur Einrichtung des Automation-Anmeldeinformationsassets verwendet haben, nicht gültig sind.

**Tipps zur Problembehandlung:** Führen Sie die folgenden Schritte aus, um zu ermitteln, wo der Fehler liegt:

1. Stellen Sie sicher, dass keine Sonderzeichen (einschließlich des Zeichens **@**) im Namen des Assets mit den Automation-Anmeldeinformationen enthalten sind, die Sie zum Herstellen der Verbindung mit Azure verwenden.  

2. Überprüfen Sie, ob Sie den Benutzernamen und das Kennwort aus den Azure Automation-Anmeldeinformationen in Ihrem lokalen PowerShell ISE-Editor verwenden können. Hierfür können Sie die folgenden Cmdlets in der PowerShell ISE ausführen:

        $Cred = Get-Credential  
        #Using Azure Service Management   
        Add-AzureAccount –Credential $Cred  
        #Using Azure Resource Manager  
        Login-AzureRmAccount –Credential $Cred

3. Wenn die Authentifizierung lokal fehlschlägt, bedeutet dies, dass Sie Ihre Azure Active Directory-Anmeldeinformationen nicht richtig eingerichtet haben. Informationen zur richtigen Einrichtung des Azure Active Directory-Kontos finden Sie im Blogbeitrag [Authenticating to Azure using Azure Active Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) (Authentifizieren gegenüber Azure per Azure Active Directory).

  <br/>
### Szenario: Azure-Abonnement nicht gefunden

**Fehler:** Beim Verwenden des Cmdlets Select-AzureSubscription oder Select-AzureRmSubscription wird Fehler „Das Abonnement mit dem Namen ``<subscription name>`` wurde nicht gefunden“ ausgegeben.

**Ursache des Fehlers:** Dieser Fehler tritt auf, wenn der Name des Abonnements ungültig ist oder wenn der Azure Active Directory-Benutzer, der versucht, die Abonnementdetails abzurufen, nicht als Administrator des Abonnements konfiguriert ist.

**Tipps zur Problembehandlung:** Führen Sie die folgenden Schritte aus, um zu ermitteln, ob die Authentifizierung gegenüber Azure richtig durchgeführt wurde und ob Sie Zugriff auf das Abonnement haben, das Sie auswählen möchten:

1. Achten Sie darauf, **Add-AzureAccount** auszuführen, bevor Sie das Cmdlet **Select-AzureSubscription** ausführen.  

2. Wenn diese Fehlermeldung weiterhin angezeigt wird, ändern Sie Ihren Code, indem Sie das Cmdlet **Get-AzureSubscription** nach dem Cmdlet **Add-AzureAccount** hinzufügen und dann den Code ausführen. Überprüfen Sie jetzt, ob die Ausgabe von Get-AzureSubscription Ihre Abonnementdetails enthält.
    * Falls in der Ausgabe keine Abonnementdetails angezeigt werden, bedeutet dies, dass das Abonnement noch nicht initialisiert wurde.  
    * Wenn die Abonnementdetails in der Ausgabe zu sehen sind, sollten Sie sich vergewissern, dass Sie den richtigen Abonnementnamen bzw. die richtige ID für das Cmdlet **Select-AzureSubscription** verwenden.   

  <br/>
### Szenario: Fehler bei der Authentifizierung gegenüber Azure, da Multi-Factor Authentication aktiviert ist

**Fehler:** Der Fehler „Add-AzureAccount: AADSTS50079: Sichere Authentifizierungsregistrierung (Nachweis) erforderlich“ wird ausgegeben, wenn Sie sich für Azure mit Ihrem Azure-Benutzernamen und -Kennwort authentifizieren.

**Ursache des Fehlers:** Falls Sie für Ihr Azure-Konto über Multi-Factor Authentication verfügen, können Sie für die Authentifizierung gegenüber Azure keinen Azure Active Directory-Benutzer verwenden. Stattdessen müssen Sie ein Zertifikat oder einen Dienstprinzipal für die Authentifizierung gegenüber Azure verwenden.

**Tipps zur Problembehandlung:** Informationen zur Verwendung eines Zertifikats mit den Azure Service Management-Cmdlets finden Sie unter [Managing Azure Services with the Microsoft Azure Automation Preview Service](http://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) (Verwalten von Azure Services mit dem Microsoft Azure Automation Preview-Dienst). Informationen zur Verwendung eines Dienstprinzipals mit Azure-Ressourcen-Manager-Cmdlets finden Sie unter [Erstellen eines Dienstprinzipals mit dem Azure-Portal](./resource-group-create-service-principal-portal.md) und [Authentifizieren eines Dienstprinzipals mit dem Azure-Ressourcen-Manager](./resource-group-authenticate-service-principal.md).

  <br/>
## Problembehandlung für häufige Fehler bei der Arbeit mit Runbooks  
### Szenario: Runbookfehler aufgrund eines deserialisierten Objekts

**Fehler:** Für Ihr Runbook tritt folgender Fehler auf: „Der Parameter ``<ParameterName>`` kann nicht gebunden werden. Der Wert ``<ParameterType>`` mit dem Typ ‚Deserialisiert‘ ``<ParameterType>`` kann nicht in den Typ ``<ParameterType>`` konvertiert werden.“

**Ursache des Fehlers:** Wenn es sich bei Ihrem Runbook um einen PowerShell-Workflow handelt, werden komplexe Objekte in einem deserialisierten Format gespeichert, um den Runbookstatus beizubehalten, wenn der Workflow angehalten wird.

**Tipps zur Problembehandlung:** Sie können dieses Problem mit einer der folgenden drei Lösungen beheben:

1. Wenn Sie komplexe Objekte von einem Cmdlet an ein anderes übergeben, sollten Sie diese Cmdlets mit einem InlineScript umschließen.  
2. Übergeben Sie den Namen oder Wert, den Sie aus dem komplexen Objekt benötigen, anstatt das gesamte Objekt zu übergeben.  

3. Verwenden Sie anstelle eines PowerShell-Workflow-Runbooks ein PowerShell-Runbook.

  <br/>
### Szenario: Fehler beim Runbookauftrag, da das zugeordnete Kontingent überschritten wurde

**Fehler:** Ihr Runbookauftrag schlägt mit dem Fehler „Das Kontingent für die monatliche Gesamtausführungsdauer des Auftrags wurde für dieses Abonnement erreicht“ fehl.

**Ursache des Fehlers:** Dieser Fehler tritt auf, wenn die Auftragsausführung das kostenlose Kontingent von 500 Minuten für Ihr Konto überschreitet. Dieses Kontingent gilt für alle Arten von Auftragsausführungsaufgaben, z. B. Testen eines Auftrags, Starten eines Auftrags im Portal, Ausführen eines Auftrags per Webhook und Planen der Ausführung eines Auftrags per Azure-Portal oder in Ihrem Rechenzentrum. Weitere Informationen zu den Preisen für Automation finden Sie unter [Automation – Preise](https://azure.microsoft.com/pricing/details/automation/).

**Tipps zur Problembehandlung:** Wenn Sie mehr als 500 Minuten an Verarbeitungszeit pro Monat nutzen möchten, müssen Sie Ihr Abonnement vom Tarif „Free“ auf den Tarif „Basic“ umstellen. Sie können das Upgrade auf den Tarif „Basic“ mit den folgenden Schritten durchführen:

1. Melden Sie sich bei Ihrem Azure-Abonnement an.  
2. Wählen Sie das Automation-Konto aus, das Sie aktualisieren möchten.  
3. Klicken Sie auf **Einstellungen** > **Tarif und Nutzung** > **Tarif**.  
4. Wählen Sie auf dem Blatt **Tarif wählen** die Option **Basic**.    

  <br/>
### Szenario: Cmdlet wird beim Ausführen eines Runbooks nicht erkannt

**Fehler:** Für Ihren Runbookauftrag tritt der folgende Fehler auf: „``<cmdlet name>``: Die Benennung ``<cmdlet name>`` wurde nicht als Name eines Cmdlets, einer Funktion, einer Skriptdatei oder eines ausführbaren Programms erkannt.“

**Ursache des Fehlers:** Dieser Fehler wird verursacht, wenn das PowerShell-Modul das Cmdlet nicht findet, das Sie in Ihrem Runbook verwenden. Dies kann der Fall sein, weil das Modul mit dem Cmdlet unter dem Konto nicht vorhanden ist, ein Namenskonflikt mit einem Runbooknamen besteht oder das Cmdlet auch in einem anderen Modul vorhanden ist und Automation den Namen nicht auflösen kann.

**Tipps zur Problembehandlung:** Sie können dieses Problem wie folgt beheben:

- Überprüfen Sie, ob Sie den Cmdlet-Namen richtig eingegeben haben und ob der Pfad zum Cmdlet korrekt ist.  

- Stellen Sie sicher, dass das Cmdlet unter Ihrem Automation-Konto vorhanden ist und dass keine Konflikte bestehen. Überprüfen Sie wie folgt, ob das Cmdlet vorhanden ist: Öffnen Sie ein Runbook im Bearbeitungsmodus, und suchen Sie in der Bibliothek nach dem gewünschten Cmdlet, oder führen Sie **Get-Command ``<CommandName>``** aus. Nachdem Sie überprüft haben, dass das Cmdlet für das Konto verfügbar ist und dass keine Namenskonflikte mit anderen Cmdlets oder Runbooks bestehen, sollten Sie es der Canvas hinzufügen und sicherstellen, dass Sie in Ihrem Runbook einen gültigen Parametersatz verwenden.

- Falls ein Namenskonflikt vorliegt und das Cmdlet in zwei unterschiedlichen Modulen verfügbar ist, können Sie dies beheben, indem Sie den vollqualifizierten Namen für das Cmdlet verwenden. Sie können beispielsweise **ModuleName\\CmdletName** verwenden.

- Wenn Sie das Runbook lokal in einer Hybrid Worker-Gruppe ausführen, stellen Sie sicher, dass das Modul/Cmdlet auf dem Computer installiert ist, auf dem der Hybrid Worker gehostet wird.

  <br/>
## Problembehandlung für häufige Fehler beim Importieren von Modulen 

### Szenario: Fehler beim Modulimport oder Cmdlets können nach dem Importieren nicht ausgeführt werden

**Fehler:** Ein Modul kann nicht importiert werden, oder der Import ist erfolgreich, aber es werden keine Cmdlets extrahiert.

**Ursache des Fehlers:** Einige häufige Ursachen, warum ein Modul nicht erfolgreich nach Azure Automation importiert wird, lauten:

- Die Struktur stimmt nicht mit der Struktur überein, die für Automation erforderlich ist.  

- Das Modul ist von einem anderen Modul abhängig, das für Ihr Automation-Konto nicht bereitgestellt wurde.

- Für das Modul fehlen die Abhängigkeiten im Ordner.

- Das Cmdlet **New-AzureRmAutomationModule** wird zum Hochladen des Moduls verwendet, und Sie haben nicht den vollständigen Speicherpfad angegeben oder haben das Modul nicht mit einer öffentlich zugänglichen URL geladen.

**Tipps zur Problembehandlung:** Sie können dieses Problem wie folgt beheben:

- Stellen Sie sicher, dass für das Modul das folgende Format eingehalten wird: ModuleName.Zip **->** ModuleName oder Versionsnummer **->** (ModuleName.psm1, ModuleName.psd1).

- Öffnen Sie die PSD1-Datei, und prüfen Sie, ob für das Modul Abhängigkeiten bestehen. Wenn ja, laden Sie diese Module in das Automation-Konto hoch.

- Stellen Sie sicher, dass alle referenzierten DLLs im Modulordner vorhanden sind.

  <br/>

## Beheben von Problemen beim Arbeiten mit der Konfiguration des gewünschten Zustands (Desired State Configuration, DSC)  

### Szenario: Der Knoten hat den Fehlerstatus „Nicht gefunden“

**Fehler:** Der Knoten weist den Status „Fehler“ mit der Meldung „Fehler beim Versuch, die Aktion vom Server ‚https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction‘ abzurufen, weil keine gültige <guid>-Konfiguration gefunden wurde.“ auf.

**Grund dieses Fehlers:** Dieser Fehler tritt normalerweise auf, wenn der Knoten einem Konfigurationsnamen (z. B. ABC) anstatt einem Knotenkonfigurationsnamen (z. B. ABC.WebServer) zugewiesen ist.

**Tipps zur Problembehandlung:** Vergewissern Sie sich, dass der Name der Knotenkonfiguration und nicht der Name der Konfiguration verwendet wird. Sie können entweder im Portal auf dem Blatt „Knoten“ auf die Schaltfläche „Knotenkonfiguration zuweisen“ klicken oder das Cmdlet „Set-AzureRMAutomationDscNode“ verwenden, um den Knoten einer gültigen Knotenkonfiguration zuzuordnen.

### Szenario: Bei der Kompilierung der Konfiguration wurden keine Knotenkonfigurationen (MOF-Dateien) erstellt.

**Fehler:** Der DSC-Kompilierungsauftrag wurde mit dem folgenden Fehler angehalten: „Kompilierung erfolgreich abgeschlossen, ohne dass MOF-Dateien mit Knotenkonfigurationen erstellt wurden.“

**Grund des Fehlers:** Wenn der Ausdruck neben „node“ in der DSC-Konfiguration mit „$null“ ausgewertet wird, werden keine Knotenkonfigurationen erstellt.

**Tipps zur Problembehandlung:** Vergewissern Sie sich, dass der Ausdruck neben „node“ nicht mit „$null“ ausgewertet wird. Wenn Sie Konfigurationsdaten übergeben, stellen Sie sicher, dass Sie die erwarteten Werte übergeben, die die Konfiguration aus den Konfigurationsdaten verlangt. Beispiel: $AllNodes. Weitere Informationen finden Sie unter https://azure.microsoft.com/de-DE/documentation/articles/automation-dsc-compile/#configurationdata.

### Szenario: DSC-Knotenbericht bleibt mit dem Status „In Bearbeitung“ hängen.

**Fehler:** DSC-Agent gibt die Meldung „Keine Instanz mit den angegebenen Eigenschaftswerten gefunden.“ aus.

**Grund des Fehlers:** Sie haben Ihre WMF-Version aktualisiert und WMI beschädigt.

**Tipps zur Problembehandlung:** Folgen Sie den Anweisungen in diesem Beitrag, um das Problem zu beheben: https://msdn.microsoft.com/de-DE/powershell/wmf/limitation_dsc

### Szenario: In einer DSC-Konfiguration können keine Anmeldeinformationen verwendet werden. 

**Fehler:** Der DSC-Kompilierungsauftrag wurde mit dem folgenden Fehler angehalten: Fehler „System.InvalidOperationException“ beim Verarbeiten der „Credential“-Eigenschaft vom Typ „<some resource name>“: Das Konvertieren und Speichern eines verschlüsselten Kennworts als Klartext ist nur zulässig, wenn „PSDscAllowPlainTextPassword“ auf „true“ festgelegt ist.

**Grund des Fehlers:** Sie haben versucht, Anmeldeinformationen in einer Konfiguration zu verwenden, aber nicht die ordnungsgemäßen Konfigurationsdaten übergeben, über die „PSAllowPlainTextPassword“ für jede Knotenkonfiguration auf „true“ festgelegt wird.

**Tipps zur Problembehandlung:** Stellen Sie sicher, dass Sie die ordnungsgemäßen Konfigurationsdaten übergeben, über die „PSAllowPlainTextPassword“ für jede Knotenkonfiguration auf „true“ festgelegt wird. Weitere Informationen finden Sie unter https://azure.microsoft.com/de-DE/documentation/articles/automation-dsc-compile/#assets.

  <br/>

## Nächste Schritte

Sie haben folgende Möglichkeiten, wenn Sie die oben genannten Schritte zur Problembehandlung befolgt haben und an diesem Punkt des Artikels weitere Hilfe benötigen:

- Wenden Sie sich an Azure-Experten. Posten Sie Ihr Problem in den [MSDN Azure- oder Stack Overflow-Foren](https://azure.microsoft.com/support/forums/).

- Erstellen Sie einen Azure-Supportfall. Klicken Sie auf der [Azure-Support-Website](https://azure.microsoft.com/support/options/) unter **Technischer und Abrechnungssupport** auf **Support erhalten**.

- Senden Sie im [Script Center](https://azure.microsoft.com/documentation/scripts/) eine Skriptanforderung, wenn Sie nach einer Azure Automation-Runbooklösung oder einem Integrationsmodul suchen.

- Veröffentlichen Sie Feedback oder Vorschläge zu Features für Azure Automation unter [User Voice](https://feedback.azure.com/forums/34192--general-feedback) (Aussagen von Benutzern).

<!---HONumber=AcomDC_0218_2016-->