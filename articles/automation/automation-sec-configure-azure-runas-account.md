<properties
    pageTitle="Konfigurieren eines ausführenden Kontos in Azure | Microsoft Azure"
    description="Mit diesem Tutorial werden Sie schrittweise durch die Erstellung, das Testen und eine Beispielverwendung der Authentifizierung per Sicherheitsprinzipal in Azure Automation geführt."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="03/30/2016"
    ms.author="magoedte"/>

# Authentifizieren von Runbooks mit der Azure-Option „Ausführendes Konto“
In diesem Thema wird veranschaulicht, wie Sie ein Automation-Konto im Azure-Portal konfigurieren. Hierfür verwenden Sie das neue Feature „Ausführendes Konto“ (auch als Dienstprinzipal bezeichnet), um in Ihrem Abonnement mit Automation-Runbooks auf Azure Resource Manager-Ressourcen (ARM) zuzugreifen. Wenn Sie im Azure-Portal ein neues Automation-Konto erstellen, wird standardmäßig automatisch ein neuer Dienstprinzipal erstellt und der Rolle „Mitwirkender“ der rollenbasierten Zugriffssteuerung (RBAC) im Abonnement zugewiesen. Dies vereinfacht den Prozess für Sie und ermöglicht Ihnen das schnelle Erstellen und Bereitstellen von Runbooks als Unterstützung für Ihre Anforderungen an die Automation.

Mit einem Dienstprinzipal haben Sie folgende Möglichkeiten:

* Bereitstellen eines Standardverfahrens zum Authentifizieren für Azure beim Verwalten von ARM-Ressourcen mit Runbooks
* Automatisieren der Verwendung globaler Runbooks, die in Azure Alerts konfiguriert sind


>[AZURE.NOTE] Für das Azure-Feature zum [Integrieren von Warnungen](../azure-portal/insights-receive-alert-notifications.md) mit globalen Automation-Runbooks ist ein Automation-Konto erforderlich, das mit einem Dienstprinzipal konfiguriert ist. Sie können entweder ein Automation-Konto auswählen, für das bereits ein Dienstprinzipalbenutzer definiert ist, oder Sie können ein neues Konto erstellen.

Hier wird gezeigt, wie Sie das Automation-Konto über das Azure-Portal und Azure PowerShell erstellen und wie Sie diesen Dienstprinzipal für die Authentifizierung in Ihren Runbooks verwenden.

## Erstellen eines neuen Automation-Kontos über das Azure-Portal
In diesem Abschnitt führen Sie die folgenden Schritte aus, um im Azure-Portal ein neues Azure Automation-Konto und einen Dienstprinzipal zu erstellen.

>[AZURE.NOTE] Der Benutzer, der diese Schritte ausführt, *muss* Mitglied der Rolle „Abonnement-Administratoren“ sein.

1. Melden Sie sich im Azure-Portal als Dienstadministrator für das Azure-Abonnement an, das Sie verwalten möchten.
2. Wählen Sie die Option **Automation-Konten**.
3. Klicken Sie im Blatt „Automation-Konten“ auf **Hinzufügen**.<br>![Automation-Konto hinzufügen](media/automation-sec-configure-azure-runas-account/add-automation-account-properties.png)
4. Geben Sie im Blatt **Automation-Konto hinzufügen** im Feld **Name** einen Namen für das neue Automation-Konto ein.
5. Wenn Sie über mehrere Abonnements verfügen, geben Sie das für das neue Konto geltende Abonnement sowie eine neue oder vorhandene **Ressourcengruppe** und den **Speicherort** eines Azure-Rechenzentrums an.
6. Stellen Sie sicher, dass **Ja** für die Option **Ausführendes Azure-Konto erstellen** ausgewählt ist, und klicken Sie auf die Schaltfläche **Erstellen**.  
7. Während das Automation-Konto in Azure erstellt wird, können Sie den Status unter **Benachrichtigungen** im Menü nachverfolgen.

Nach Abschluss des Vorgangs wird das Automation-Konto mit einem Zertifikatobjekt mit dem Namen **AzureRunAsCertificate** erstellt, das über eine Lebensdauer von einem Jahr verfügt. Außerdem wird ein Verbindungsobjekt mit dem Namen **AzureRunAsConnection** erstellt.

## Aktualisieren eines Automation-Kontos mit PowerShell
Mit dem unten angegebenen Verfahren wird ein vorhandenes Automation-Konto aktualisiert und der Dienstprinzipal mit PowerShell erstellt. Dieses Verfahren ist erforderlich, wenn Sie ein Konto erstellt, die Erstellung von „Ausführendes Konto“ aber abgelehnt haben.

Stellen Sie vor dem Fortfahren Folgendes sicher:

1. Sie haben das [Azure Active Directory-Modul für Windows PowerShell (64-Bit-Version)](http://go.microsoft.com/fwlink/p/?linkid=236297) heruntergeladen und installiert.
2. Sie haben ein Automation-Konto erstellt. Dieses Konto wird unten im Skript als Wert für die Parameter -AutomationAccountName und -ApplicationDisplayName angegeben.

Mit dem PowerShell-Skript wird Folgendes konfiguriert:

* Eine Azure AD-Anwendung, die mit dem selbstsignierten Zertifikat authentifiziert werden kann, und ein Dienstprinzipalkonto für diese Anwendung in Azure AD. Außerdem wird die Rolle „Mitwirkender“ (kann auch in „Besitzer“ oder eine andere Rolle geändert werden) für dieses Konto in Ihrem aktuellen Abonnement zugewiesen. Weitere Informationen finden Sie im Artikel [Rollenbasierte Zugriffssteuerung in Azure Automation](../automation/automation-role-based-access-control.md).  
* Ein Automation-Zertifikatobjekt im angegebenen Automation-Konto mit dem Namen **AzureRunAsCertificate**, in dem das im Dienstprinzipal verwendete Zertifikat enthalten ist.
* Ein Automation-Verbindungsobjekt im angegebenen Automation-Konto mit dem Namen **AzureRunAsConnection**, das die Elemente applicationId, tenantId, subscriptionId und den Zertifikatfingerabdruck enthält.<br>

### Ausführen des PowerShell-Skripts

1. Speichern Sie das folgende Skript auf dem Computer. Speichern Sie es in diesem Beispiel unter dem Dateinamen **New-AzureServicePrincipal.ps1**.  

 ```
    #Requires - RunAsAdministrator
    Param (
    [Parameter(Mandatory=$true)]
    [String] $ResourceGroup,

    [Parameter(Mandatory=$true)]
    [String] $AutomationAccountName,
   
    [Parameter(Mandatory=$true)]
    [String] $ApplicationDisplayName,
   
    [Parameter(Mandatory=$true)]
    [String] $CertPlainPassword,
   
    [Parameter(Mandatory=$false)]
    [int] $NoOfMonthsUntilExpired = 12
    )
   
    Login-AzureRmAccount
    Import-Module AzureRM.Resources
		
    $CurrentDate = Get-Date
    $EndDate = $CurrentDate.AddMonths($NoOfMonthsUntilExpired)
    $KeyId = (New-Guid).Guid
    $CertPath = Join-Path $env:TEMP ($ServicePrincipalDisplayName + ".pfx")

    $Cert = New-SelfSignedCertificate -DnsName $ServicePrincipalDisplayName -CertStoreLocation cert:\LocalMachine\My -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider"

    $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
    Export-PfxCertificate -Cert ("Cert:\localmachine\my" + $Cert.Thumbprint) -FilePath $CertPath -Password $CertPassword -Force | Write-Verbose

    $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate -ArgumentList @($CertPath, $CertPlainPassword)
    $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

    $KeyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
    $KeyCredential.StartDate = $CurrentDate
    $KeyCredential.EndDate= $EndDate
    $KeyCredential.KeyId = $KeyId
    $KeyCredential.Type = "AsymmetricX509Cert"
    $KeyCredential.Usage = "Verify"
    $KeyCredential.Value = $KeyValue

    # Use Key credentials
    $Application = New-AzureRmADApplication -DisplayName $ServicePrincipalDisplayName -HomePage ("http://" + $ServicePrincipalDisplayName) -IdentifierUris ("http://" + $KeyId) -KeyCredentials $keyCredential

    New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId | Write-Verbose
    Get-AzureRmADServicePrincipal | Where {$_.ApplicationId -eq $Application.ApplicationId} | Write-Verbose

    $NewRole = $null
    $Retries = 0;
    While ($NewRole -eq $null -and $Retries -le 2)
    {
      # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
      Sleep 5
      New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose
      Sleep 5
      $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
      $Retries++;
    }

    # Get the tenant id for this subscription
    $SubscriptionInfo = Get-AzureRmSubscription
    $TenantID = $SubscriptionInfo | Select TenantId -First 1

    # Create the automation resources
    Remove-AzureRmAutomationVariable -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name ServicePrincipalApplicationID -Force -ErrorAction SilentlyContinue
    New-AzureRmAutomationVariable -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name ServicePrincipalApplicationID -Value $Application.ApplicationId -Encrypted $false | write-verbose
    Remove-AzureRmAutomationVariable -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name ServicePrincipalTenantID -Force -ErrorAction SilentlyContinue
    New-AzureRmAutomationVariable -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name ServicePrincipalTenantID -Value $TenantID.TenantId -Encrypted $false | write-verbose
    Remove-AzureRmAutomationCertificate -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name AzureRunAsCertificate -Force -ErrorAction SilentlyContinue | write-verbose
    New-AzureRmAutomationCertificate -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Path $CertPath -Name AzureRunAsCertificate -Password $CertPassword -Exportable | write-verbose

    # Create a Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
    $ConnectionAssetName = "AzureRunAsConnection"
    $SubscriptionId = $SubscriptionInfo | Select SubscriptionId -First 1
    Remove-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -Force -ErrorAction SilentlyContinue
    $ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId.SubscriptionId}
    New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
 ```


2. Starten Sie **Windows PowerShell** auf Ihrem Computer über den **Startbildschirm** mit erhöhten Benutzerrechten.
3. Navigieren Sie von der PowerShell-Befehlszeilenshell mit erhöhten Rechten zu dem Ordner, der das in Schritt 1 erstellte Skript enthält, und führen Sie das Skript aus, indem Sie die Werte für die Parameter *-ResourceGroup*, *-AutomationAccountName*, *-ApplicationDisplayName* und *-CertPlainPassword* ändern.<br> ```.\New-AzureServicePrincipal.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -ApplicationDisplayName <DisplayNameofAutomationAccount> -CertPlainPassword "<StrongPassword>"``` <br>

    >[AZURE.NOTE] Nach dem Ausführen des Skripts werden Sie aufgefordert, sich gegenüber Azure zu authentifizieren. Sie *müssen* sich mit einem Konto anmelden, das als Dienstadministrator im Abonnement eingerichtet ist. <br>

4. Nachdem das Skript erfolgreich abgeschlossen wurde, können Sie mit dem nächsten Abschnitt fortfahren, um die Konfiguration der neuen Anmeldeinformationen zu testen und zu überprüfen.

### Überprüfen der Authentifizierung als Dienstprinzipal
Als Nächstes führen wir einen kleinen Test durch, um zu bestätigen, dass Sie sich mit dem neuen Dienstprinzipal erfolgreich authentifizieren können. Wenn Sie sich nicht erfolgreich authentifizieren können, müssen Sie wieder mit Schritt 1 beginnen und alle Schritte erneut durchführen.

1. Öffnen Sie im Azure-Portal das zuvor erstellte Automation-Konto.  
2. Klicken Sie auf die Kachel **Runbooks**, um die Liste mit den Runbooks zu öffnen.
3. Erstellen Sie ein neues Runbook, indem Sie auf die Schaltfläche **Runbook hinzufügen** klicken, und wählen Sie dann im Blatt **Runbook hinzufügen** die Option **Neues Runbook erstellen**.
4. Geben Sie dem Runbook den Namen *Test-SecPrin-Runbook*, und wählen Sie „PowerShell“ als **Runbooktyp**. Klicken Sie auf **Erstellen**, um das Runbook zu erstellen.
5. Fügen Sie im Blatt **PowerShell-Runbook bearbeiten** den folgenden Code im Zeichenbereich hinzu:<br>
       ```
       $Conn = Get-AutomationConnection -Name AzureRunAsConnection
       Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
       ```
6. Klicken Sie auf **Speichern**, um das Runbook zu speichern.
7. Klicken Sie auf **Testbereich**, um das Blatt **Test** zu öffnen.
8. Klicken Sie auf **Starten**, um den Test zu starten.
9. Ein [Runbookauftrag](automation-runbook-execution.md) wird erstellt, und der dazugehörige Status wird angezeigt. Der Auftrag besitzt zunächst den Status *In der Warteschlange*, um anzugeben, dass der Auftrag darauf wartet, dass in der Cloud ein Runbook Worker verfügbar wird. Wird der Auftrag von einem Worker übernommen, wechselt der Status zu *Wird gestartet...* und anschließend zu *Wird ausgeführt...*, wenn die Ausführung des Runbooks tatsächlich gestartet wurde.  
10. Nach Abschluss des Runbookauftrags wird die Ausgabe angezeigt. In unserem Fall sollte der Status **Abgeschlossen** angezeigt werden.<br> ![Sicherheitsprinzipal-Runbooktest](media/automation-sec-configure-azure-runas-account/runbook-test-results.png)<br>
11. Schließen Sie das Blatt **Test**, um zum Zeichenbereich zurückzukehren.
12. Schließen Sie das Blatt **PowerShell-Runbook bearbeiten**.
13. Schließen Sie das Blatt **Test-SecPrin-Runbook**.

Den obigen Code zum Überprüfen, ob das neue Konto richtig eingerichtet ist, verwenden Sie in all Ihren PowerShell-Runbooks, um sich in Azure Automation zum Verwalten von ARM-Ressourcen zu authentifizieren. Natürlich können Sie sich auch weiterhin mit dem Automation-Konto authentifizieren, das Sie bisher genutzt haben.

## Nächste Schritte
- Weitere Informationen zu Dienstprinzipalen finden Sie unter [Anwendungsobjekte und Dienstprinzipalobjekte](../active-directory/active-directory-application-objects.md).

<!---HONumber=AcomDC_0330_2016-->