<properties
   pageTitle="Continuous Integration für Service Fabric | Microsoft Azure"
   description="Hier erhalten Sie eine Übersicht über die Einrichtung der Continuous Integration für eine Service Fabric-Anwendung mit Visual Studio Team Services (VSTS)."
   services="service-fabric"
   documentationCenter="na"
   authors="cawams"
   manager="timlt"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="01/27/2016"
   ms.author="cawa" />

# Einrichten der Continuous Integration für eine Service Fabric-Anwendung mit Visual Studio Team Services (VSTS)

In diesem Artikel werden die Schritte zum Einrichten der Continuous Integration (CI) für eine Service Fabric-Anwendung mit Visual Studio Team Services (VSTS) beschrieben, um die automatische Erstellung, Verpackung und Bereitstellung Ihrer Anwendung sicherzustellen. Beachten Sie, dass die Anweisungen jedes Mal eine Neuerstellung des Clusters zur Folge haben.

Dieses Dokument wurde für die aktuelle Prozedur erstellt und wird wahrscheinlich im Laufe der Zeit geändert.

## Voraussetzungen

Richten Sie zunächst Ihr Projekt in Visual Studio Team Services ein.

1. Richten Sie mithilfe Ihres [Microsoft-Kontos](http://www.microsoft.com/account) ein Team Services-Konto ein, sofern Sie noch keines erstellt haben.

2. Erstellen Sie in Team Services mithilfe Ihres Microsoft-Kontos ein neues Projekt.

3. Führen Sie für die Quelle Ihrer neuen oder vorhandenen Service Fabric-App einen Pushvorgang an dieses Projekt durch.

Weitere Informationen zur Verwendung von Team Services-Projekten finden Sie unter [Herstellen einer Verbindung mit Visual Studio](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online).

## Einrichten Ihres Dienstprinzipals

### Einrichten der Authentifizierung für die Automatisierung

Bevor Sie den Buildcomputer einrichten können, müssen Sie einen [Dienstprinzipal](../resource-group-create-service-principal-portal.md) erstellen. Der Build-Agent verwendet ihn, um sich bei Azure zu authentifizieren. Außerdem müssen Sie ein Zertifikat erstellen und es in einen Azure-Schlüsseltresor hochladen, da der Schlüsseltresor die Dienstprinzipal-Authentifizierung nicht unterstützt. Diese Schritte können auf einem beliebigen Computer ausgeführt werden. Der Entwicklungscomputer ist dabei eine gute Wahl.

### Installieren von Azure PowerShell und Anmelden

1.	Installieren Sie Azure PowerShell.
2. Installieren Sie „PowerShellGet“. Installieren Sie hierzu [Windows Management Framework 5.0](http://www.microsoft.com/download/details.aspx?id=48729), da „PowerShellGet“ darin enthalten ist.

    >[AZURE.NOTE] Bei Verwendung von Windows 10 mit neuesten Updates können Sie diesen Schritt überspringen.

3.	Installieren und aktualisieren Sie das AzureRM-Modul. Wenn Sie eine ältere Version von Azure PowerShell installiert haben, entfernen Sie sie:

    a. Klicken Sie mit der rechten Maustaste auf die Schaltfläche „Start“, und wählen Sie **Programme hinzufügen/entfernen**.

    b. Suchen Sie nach „Azure PowerShell“, und deinstallieren Sie es.

    c. Öffnen Sie eine PowerShell-Eingabeaufforderung.

    d. Installieren Sie das AzureRM-Modul mit dem Befehl `Install-Module AzureRM`.

    e. Aktualisieren Sie das AzureRM-Modul mit dem Befehl `Update-AzureRM`.

3.	Deaktivieren (oder aktivieren) Sie die Azure-Datensammlung.

    Andernfalls werden Sie von Azure-Cmdlets immer wieder gefragt, ob Sie die Datensammlung verwenden möchten. Dabei wird auf eine Benutzereingabe gewartet, was die Automatisierung blockiert. Treffen Sie daher bereits vorab eine Entscheidung, um diese Nachfrage zu unterdrücken. Führen Sie dazu einen der folgenden Befehle aus:

    - Enable-AzureRmDataCollection

    - Disable-AzureRmDataCollection

4.	Melden Sie sich bei Azure PowerShell an.

    a. Führen Sie den Befehl `Login-AzureRmAccount` aus.

    b. Geben Sie im angezeigten Dialogfeld Ihre Azure-Anmeldeinformationen ein.

    c. Führen Sie den Befehl `Get-AzureRmSubscription` aus.

    d. Suchen Sie das Abonnement, das Sie verwenden möchten.

    e. Führen Sie den Befehl `Select-AzureRmSubscription -SubscriptionId <id for your subscription>` aus.

### Erstellen eines Dienstprinzipals

1.	Laden Sie die Datei [ServiceFabricContinuousIntegrationScripts.zip](https://gallery.technet.microsoft.com/Set-up-continuous-f8b251f6) herunter, und extrahieren Sie sie auf diesem Computer in einem Ordner.

2.	Wechseln Sie in einer PowerShell-Eingabeaufforderung mit Administratorrechten innerhalb des extrahierten Archivs in das Verzeichnis `Powershell\Manual`.

3.	Wählen Sie mithilfe des folgenden Befehls ein Kennwort für den Dienstprinzipal. Merken Sie sich dieses Kennwort, da es als Buildvariable verwendet wird.

    ```
    $password = Read-Host -AsSecureString
    ```
4.	Führen Sie das PowerShell-Skript „Create-ServicePrincipal.ps1“ mit folgenden Parametern aus:

|Parameter|Wert|
|---|---|
|DisplayName|Ein beliebiger Name.|
|HomePage|Ein beliebiger URI. Muss nicht tatsächlich vorhanden sein.|
|IdentifierUri|Ein beliebiger eindeutiger URI. Muss nicht tatsächlich vorhanden sein.|
|SecurePassword|$password|

Nach Abschluss des Skripts werden die folgenden drei Werte ausgegeben. Notieren Sie sich die Werte, da sie als Buildvariablen verwendet werden.

*  `ServicePrincipalId`
*  `ServicePrincipalTenantId`
*  `ServicePrincipalSubscriptionId`

### Erstellen und Hochladen eines Zertifikats an einen neuen Azure-Schlüsseltresor

>[AZURE.NOTE] Dieses Beispielskript generiert ein selbstsigniertes Zertifikat – dies ist keine sichere Vorgehensweise und nur für Experimente zulässig. Befolgen Sie die Richtlinien Ihres Unternehmens, um stattdessen ein legitimes Zertifikat zu erhalten.

1.	Wechseln Sie über eine PowerShell-Eingabeaufforderung mit Administratorrechten zu dem Verzeichnis, in dem Sie `Manual.zip` extrahiert haben.

2.	Führen Sie das PowerShell-Skript `CreateAndUpload-Certificate.ps1` mit den folgenden Parametern aus:

| Parameter | Wert |
| --- | --- |
| KeyVaultLocation | Ein beliebiger Wert. Dieser Parameter muss dem Ort entsprechen, an dem Sie den Cluster erstellen möchten. |
| CertificateSecretName | Ein beliebiger Wert. |
| SecureCertificatePassword | Ein beliebiger Wert. Dieser Parameter wird verwendet, wenn Sie das Zertifikat auf Ihrem Buildcomputer importieren. |
| KeyVaultResourceGroupName | Ein beliebiger Wert. Verwenden Sie jedoch nicht den Ressourcengruppennamen, den Sie für Ihren Cluster verwenden möchten. |
| KeyVaultName | Ein beliebiger Wert. |
| PfxFileOutputPath|Ein beliebiger Wert. Diese Datei wird verwendet, wenn Sie das Zertifikat in Ihren Buildcomputer importieren. |

Nach Abschluss des Skripts werden die folgenden drei Werte ausgegeben. Notieren Sie sich diese Werte, da sie als Buildvariablen verwendet werden.

* `ServiceFabricCertificateThumbprint`
* `ServiceFabricKeyVaultId`
* `ServiceFabricCertificateSecretId`

## Einrichten Ihres Buildcomputers

### Installieren von Visual Studio 2015

Wenn Sie bereits einen Computer bereitgestellt haben (oder Ihren eigenen bereitstellen möchten), installieren Sie auf dem ausgewählten Computer [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).

Wenn Sie noch nicht über einen Computer verfügen, können Sie schnell eine virtuelle Azure-Maschine (VM) mit vorinstalliertem Visual Studio 2015 bereitstellen. Gehen Sie dazu folgendermaßen vor:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Wählen Sie links oben auf dem Bildschirm den Befehl **Neu** aus.

3. Wählen Sie die Option **Marketplace** aus.

4. Suchen Sie nach **Visual Studio 2015**.

5. Wählen Sie **Compute** > **Virtueller Computer** > **Aus Katalog** aus.

6. Wählen Sie das Image **Visual Studio Enterprise 2015 Update 1 With Azure SDK 2.8 on Windows Server 2012 R2** aus.

    >[AZURE.NOTE] Azure SDK ist zwar keine erforderliche Komponente, es sind jedoch derzeit keine Images verfügbar, bei denen nur Visual Studio 2015 installiert ist.

7.	Befolgen Sie die Anweisungen im Dialogfeld, um Ihren virtuellen Computer zu erstellen.

### Installieren des Service Fabric-SDKs

Installieren Sie das [Service Fabric-SDK](https://azure.microsoft.com/campaigns/service-fabric/) auf Ihrem Computer.

### Installieren von Azure PowerShell

Führen Sie zum Installieren von Azure PowerShell die Schritte aus dem vorherigen Abschnitt (**Installieren von Azure PowerShell und Anmelden**) aus. Überspringen Sie dabei den Unterabschnitt **Melden Sie sich bei Azure PowerShell an**.

### Registrieren der Azure PowerShell-Module beim lokalen Dienstkonto

>[AZURE.NOTE] Führen Sie diesen Schritt aus, *bevor* Sie den Build-Agent starten. Andernfalls wird die neue Umgebungsvariable nicht verwendet.

1. Drücken Sie WINDOWS-TASTE+R, geben Sie **regedit** ein, und drücken Sie anschließend die EINGABETASTE.

2. Klicken Sie mit der rechten Maustaste auf den Knoten `HKEY_Users\.Default\Environment`, und wählen Sie **Neu > Wert der erweiterbaren Zeichenfolge** aus.

3. Geben Sie `PSModulePath` für den Namen und `%PROGRAMFILES%\WindowsPowerShell\Modules` für den Wert ein. Ersetzen Sie `%PROGRAMFILES%` mit dem Wert der Umgebungsvariablen `PROGRAMFILES`.

### Importieren Ihres Automation-Zertifikats

1.	Importieren Sie das Zertifikat in Ihren Buildcomputer. Gehen Sie dazu folgendermaßen vor:

    a. Kopieren Sie die durch das Skript „CreateAndUpload-Certificate.ps1“ erstellte PFX-Datei auf den Buildcomputer.

    b. Öffnen Sie eine PowerShell-Eingabeaufforderung mit Administratorrechten, und führen Sie die folgenden Befehle aus. Verwenden Sie dabei das Kennwort, das Sie zuvor an `CreateAndUpload-Certificate.ps1` übergeben haben.

        ```
        $password = Read-Host -AsSecureString
        Import-PfxCertificate -FilePath <path/to/cert.pfx> -CertStoreLocation Cert:\LocalMachine\My -Password $password -Exportable
        ```

2.	Führen Sie den Zertifikat-Manager aus.

    a. Öffnen Sie die Windows-Systemsteuerung. Klicken Sie mit der rechten Maustaste auf die Schaltfläche „Start“, und wählen Sie **Systemsteuerung** aus.

    b. Suchen Sie nach **Zertifikat**.

    c. Wählen Sie **Verwaltung** > **Computerzertifikate verwalten** aus.

3.	Erteilen Sie dem lokalen Dienstkonto die Berechtigung zur Verwendung Ihres Automation-Zertifikats.

    a. Erweitern Sie unter **Zertifikate – lokaler Computer** den Knoten **Persönlich**, und wählen Sie anschließend **Zertifikate** aus.

    b. Suchen Sie in der Liste nach Ihrem Zertifikat.

    c. Klicken Sie mit der rechten Maustaste auf Ihr Zertifikat, und wählen Sie dann **Alle Aufgaben** > **Private Schlüssel verwalten**.

    d. Wählen Sie die Schaltfläche **Hinzufügen**, geben Sie **Lokaler Dienst** ein, und klicken Sie dann auf **Namen überprüfen**.

    e. Wählen Sie die Schaltfläche **OK** aus, und schließen Sie anschließend den Zertifikat-Manager.

![](media/service-fabric-set-up-continuous-integration/windows-certificate-manager.png)

### Registrieren Ihres Build-Agents

1.	Laden Sie „agent.zip“ herunter. Gehen Sie dazu folgendermaßen vor:

    a. Melden Sie sich bei Ihrem Teamprojekt an. Beispiel: ****https://[your-VSTS-account-name].visualstudio.com**.

    b. Wählen Sie das Zahnradsymbol in der rechten oberen Bildschirmecke aus.

    c. Wählen Sie in der Systemsteuerung die Registerkarte **Agentpools** aus.

    d. Wählen Sie **Download-Agent** aus, um die Datei „agent.zip“ herunterzuladen.

    e. Kopieren Sie „agent.zip“ auf den zuvor erstellten Buildcomputer.

    f. Entzippen Sie „agent.zip“ auf dem Buildcomputer unter `C:\agent` (oder an einem anderen Speicherort mit einem kurzen Pfad).

        >[AZURE.NOTE] If you plan on building ASP.NET 5 Web Services, it's recommended that you  choose the shortest name possible for this folder to avoid running into **PathTooLongExceptions** errors during deployment.

2.	Führen Sie an einer Eingabeaufforderung mit Administratorrechten das Skript `C:\agent\ConfigureAgent.cmd` aus. Das Skript fordert Sie zur Eingabe folgender Parameter auf:

|Parameter|Wert|
|---|---|
|Agent Name|Übernehmen Sie den Standardwert. (`Agent-[machine name]`)|
|TFS Url|Geben Sie die URL zu Ihrem Teamprojekt ein. Beispiel: `https://[your-VSTS-account-name].visualstudio.com`.|
|Agent Pool|Geben Sie den Namen Ihres Agentpools ein. (Falls Sie keinen Agentpool erstellt haben, übernehmen Sie den Standardwert.)|
|Work folder|Übernehmen Sie den Standardwert. In diesem Ordner erstellt der Build-Agent Ihre Anwendung. Hinweis: Wenn Sie ASP.NET 5-Webdienste erstellen möchten, empfiehlt sich die Wahl eines möglichst kurzen Ordnernamens. Andernfalls treten bei der Bereitstellung unter Umständen Fehler vom Typ „PathTooLongExceptions“ auf.|
|Install as Windows Service?|Ändern Sie den Standardwert „N“ in **Y**.|
|User account to run the service|Übernehmen Sie den Standardwert. (`NT AUTHORITY\LocalService`)|
|Un-configure existing agent?|Übernehmen Sie den Standardwert. (**N**)|

3.  Sie werden zur Angabe von Anmeldeinformationen aufgefordert. Geben Sie die Anmeldeinformationen für Ihr Microsoft-Konto ein, das über Rechte für Ihr Teamprojekt verfügt.

4.  Vergewissern Sie sich, dass Ihr Build-Agent registriert wurde. Gehen Sie dazu folgendermaßen vor:

    a. Wechseln Sie wieder zu Ihrem Webbrowser. Dieser müsste die Seite `https://[your-VSTS-account-name].visualstudio.com/_admin/_AgentPool` anzeigen. Aktualisieren Sie die Seite.

    b. Wählen Sie den Agentpool aus, den Sie zuvor beim Ausführen von „ConfigureAgent.ps1“ ausgewählt haben.

    c. Vergewissern Sie sich, dass der Build-Agent in der Liste enthalten und die Statusanzeige grün ist. Wenn die Statusanzeige rot ist, kann der Build-Agent keine Verbindung mit Team Services herstellen.

![](media/service-fabric-set-up-continuous-integration/vso-configured-agent.png)


## Erstellen Ihrer Builddefinition

>[AZURE.NOTE] Die Builddefinition, die Sie anhand dieser Anweisungen erstellen, unterstützt nicht mehrere parallele Builds, auch nicht auf separaten Computern. Das liegt daran, dass die einzelnen Builds jeweils die gleiche Ressourcengruppe/den gleichen Cluster beanspruchen würden. Wenn Sie mehrere Build-Agents ausführen möchten, müssen Sie die folgenden Anweisungen/Skripts anpassen, um diese Störung zu vermeiden.

### Fügen Sie der Quellcodeverwaltung für Ihre Anwendung die Continuous Integration-Skripts hinzu.

1.	Extrahieren Sie [ServiceFabricContinuousIntegrationScripts.zip](https://gallery.technet.microsoft.com/Set-up-continuous-f8b251f6) in einem beliebigen Ordner Ihres Computers. Kopieren Sie den Inhalt von `Powershell\Automation` in einen beliebigen Ordner der Quellcodeverwaltung.

2.	Checken Sie die resultierenden Dateien ein.

### Erstellen der Builddefinition

1.	Erstellen Sie eine leere Builddefinition. Gehen Sie dazu folgendermaßen vor:

    a. Öffnen Sie Ihr Projekt in Visual Studio Team Services.

    b. Wählen Sie die Registerkarte **Erstellen** aus.

    c. Wählen Sie das grüne Pluszeichen (**+**) aus, um eine neue Builddefinition zu erstellen.

    d. Wählen Sie die Option **Leer** und anschließend die Schaltfläche **Weiter** aus.

    e. Vergewissern Sie sich, dass das richtige Repository und die richtige Verzweigung ausgewählt sind.

    f. Wählen Sie die Agent-Warteschlange aus, bei der Sie Ihren Build-Agent registriert haben, und aktivieren Sie das Kontrollkästchen **Continuous Integration**.

2.	Erstellen Sie auf der Registerkarte **Variablen** die folgenden Variablen mit den angegebenen Werten:

|Variable|Wert|Geheimer Schlüssel|Zur Warteschlangenzeit zulassen|
|---|---|---|---|
|BuildConfiguration|Version||X|
|BuildPlatform|x64|||
|ServicePrincipalPassword|Das Kennwort, das Sie an „CreateServicePrincipal.ps1“ übergeben haben.|X||
|ServicePrincipalId|Aus der Ausgabe von „CreateServicePrincipal.ps1“.|||
|ServicePrincipalTenantId|Aus der Ausgabe von „CreateServicePrincipal.ps1“.|||
|ServicePrincipalSubscriptionId|Aus der Ausgabe von „CreateServicePrincipal.ps1“.|||
|ServiceFabricCertificateThumbprint|Aus der Ausgabe von „GenerateCertificate.ps1“.|||
|ServiceFabricKeyVaultId|Aus der Ausgabe von „GenerateCertificate.ps1“.|||
|ServiceFabricCertificateSecretId|Aus der Ausgabe von „GenerateCertificate.ps1“.|||
|ServiceFabricClusterName|Ein beliebiger Name.|||
|ServiceFabricClusterResourceGroupName|Ein beliebiger Name.|||
|ServiceFabricClusterLocation|Ein beliebiger Name, der dem Ort Ihres Schlüsseltresors entspricht.|||
|ServiceFabricClusterAdminPassword|Ein beliebiger Name.|X||
|ServiceFabricClusterResourceGroupTemplateFilePath|`<path/to/extracted/automation/scripts/ArmTemplate-Full-3xVM-Secure.json>`|||
|ServiceFabricPublishProfilePath|`<path/to/your/publish/profiles/MyPublishProfile.xml>` Hinweis: Der Verbindungsendpunkt in Ihrem Veröffentlichungsprofil wird ignoriert. Stattdessen wird der Verbindungsendpunkt für Ihren temporären Cluster verwendet.|||
|ServiceFabricDeploymentScriptPath|`<path/to/Deploy-FabricApplication.ps1>`|||
|ServiceFabricApplicationProjectPath|`<path/to/your/fabric/application/project/folder>` Hier muss der Ordner mit Ihrer SFPROJ-Datei angegeben werden.||||

3.  Speichern Sie die Builddefinition, und benennen Sie sie. (Der Name kann später bei Bedarf geändert werden.)

### Hinzufügen eines Buildschritts
@<Author GitHub alias> – Bitte die bearbeitete Kopie Ihres Artikels überprüfen und als Kommentar eingegebene Fragen beantworten. Bitte um Mitteilung, ob die technische Bedeutung irgendwo geändert wurde.

1.	Wählen Sie auf der Registerkarte **Erstellen** den Befehl **Buildschritt hinzufügen...** aus.

2.	Wählen Sie **Erstellen** > **MSBuild** aus.

3.	Wählen Sie neben dem Namen des Buildschritts das Stiftsymbol aus, und ändern Sie den Namen in **Build**.

4.	Wählen Sie neben dem Feld **Lösung** die Schaltfläche **...** und anschließend Ihre SLN-Datei aus.

5.	Geben Sie unter **Plattform** Folgendes ein: `$(BuildPlatform)`.

6.	Geben Sie unter **Konfiguration** Folgendes ein: `$(BuildConfiguration)`.

7.	Aktivieren Sie das Kontrollkästchen **NuGet-Pakete wiederherstellen** (sofern nicht bereits aktiviert).

8.	Speichern Sie die Builddefinition.

### Hinzufügen eines Paketschritts

1.	Wählen Sie auf der Registerkarte **Erstellen** den Befehl **Buildschritt hinzufügen...** aus.

2.	Wählen Sie **Erstellen** > **MSBuild** aus.

3.	Wählen Sie neben dem Namen des Buildschritts das Stiftsymbol aus, und ändern Sie den Namen in **Paket**.

4.	Wählen Sie neben dem Feld **Lösung** die Schaltfläche **...** und anschließend die SFPROJ-Datei Ihres Anwendungsprojekts aus.

5.	Geben Sie unter **Plattform** Folgendes ein: `$(BuildPlatform)`.

6.	Geben Sie unter **Konfiguration** Folgendes ein: `$(BuildConfiguration)`.

7.	Geben Sie unter **MSBuild-Argumente** Folgendes ein: `/t:Package`.

8.	Deaktivieren Sie das Kontrollkästchen **NuGet-Pakete wiederherstellen** (sofern nicht bereits deaktiviert).

9.	Speichern Sie die Builddefinition.

### Hinzufügen eines Schritts „Clusterressourcengruppe entfernen“

Wenn bei einem vorherigen Buildvorgang keine Bereinigung stattgefunden hat (beispielsweise, weil der Buildvorgang vor der Bereinigung abgebrochen wurde), ist unter Umständen eine Ressourcengruppe vorhanden, die einen Konflikt mit der neuen Ressourcengruppe verursachen könnte. Bereinigen Sie zur Vermeidung von Konflikten alle übrig gebliebenen Ressourcengruppen (und die dazugehörigen Ressourcen), bevor Sie eine neue erstellen.

1.	Wählen Sie auf der Registerkarte **Erstellen** den Befehl **Buildschritt hinzufügen...** aus.

2.	Wählen Sie **Hilfsprogramm** > **PowerShell** aus.

3.	Wählen Sie neben dem Namen des Buildschritts das Stiftsymbol aus, und ändern Sie den Namen in **Clusterressourcengruppe entfernen**.

4.	Wählen Sie neben **Skriptdateiname** den Befehl **...** aus. Navigieren Sie zu dem Ort, an dem Sie die Automatisierungsskripts extrahiert haben, und wählen Sie **Remove-ClusterResourceGroup.ps1** aus.

5.	Geben Sie unter **Argumente** Folgendes ein: `-ServicePrincipalPassword "$(ServicePrincipalPassword)"`.

6.	Speichern Sie die Builddefinition.

### Hinzufügen eines Schritts „In sicherem Cluster bereitstellen“

1.	Wählen Sie auf der Registerkarte **Erstellen** den Befehl **Buildschritt hinzufügen...** aus.

2.	Wählen Sie **Hilfsprogramm** > **PowerShell** aus.

3.	Wählen Sie neben dem Namen des Buildschritts das Stiftsymbol aus, und ändern Sie den Namen in **In sicherem Cluster bereitstellen**.

4.	Wählen Sie neben **Skriptdateiname** die Schaltfläche **...** aus. Navigieren Sie zu dem Ort, an dem Sie die Automatisierungsskripts extrahiert haben, und wählen Sie **ProvisionAndDeploy-SecureCluster.ps1** aus.

5.	Geben Sie unter **Argumente** Folgendes ein: `-ServicePrincipalPassword "$(ServicePrincipalPassword)" -ServiceFabricClusterAdminPassword "$(ServiceFabricClusterAdminPassword)"`.

6.	Speichern Sie die Builddefinition.

### Hinzufügen eines Schritts „Clusterressourcengruppe entfernen“

Nachdem Sie den temporären Cluster nun nicht mehr benötigen, müssen Sie ihn bereinigen. Andernfalls wird er Ihnen weiter in Rechnung gestellt. Dieser Schritt entfernt die Ressourcengruppe. Dabei werden der Cluster und alle anderen Ressourcen in der Gruppe entfernt.

>[AZURE.NOTE] Es gibt einen Unterschied zwischen diesem Schritt und dem vorherigen „Clusterressourcengruppe entfernen“-Schritt: Für diesen sollte „Immer ausführen“ aktiviert sein.

1.	Wählen Sie auf der Registerkarte **Erstellen** den Befehl **Buildschritt hinzufügen...** aus.

2.	Wählen Sie **Hilfsprogramm** > **PowerShell** aus.

3.	Wählen Sie neben dem Namen des Buildschritts das Stiftsymbol aus, und ändern Sie den Namen in **Clusterressourcengruppe entfernen**.

4.	Wählen Sie neben **Skriptdateiname** die Schaltfläche **...** aus. Navigieren Sie zu dem Ort, an dem Sie die Automatisierungsskripts extrahiert haben, und wählen Sie **RemoveClusterResourceGroup.ps1** aus.

5.	Geben Sie unter **Argumente** Folgendes ein: `-ServicePrincipalPassword "$(ServicePrincipalPassword)`.

6.	Aktivieren Sie unter **Steuerungsoptionen** das Kontrollkästchen **Immer ausführen**.

7.	Speichern Sie die Builddefinition.

### Ausprobieren

Klicken Sie auf **Build in Warteschlange**, um einen Build zu starten. Buildvorgänge werden auch beim Pushvorgang oder Einchecken ausgelöst.


## Alternativen

Mit den oben aufgeführten Anweisungen wird für jeden Buildvorgang ein neuer Cluster erstellt und am Ende des Buildvorgangs wieder entfernt. Wenn Sie stattdessen bei jedem Buildvorgang ein Anwendungsupgrade (für einen vorhandenen Cluster) durchführen möchten, führen Sie die folgenden Schritte aus:

1.	Erstellen Sie manuell über das Azure-Portal oder über Azure PowerShell einen Testcluster. Sie können das `ProvisionAndDeploy-SecureCluster.ps1`-Skript als Referenz verwenden.

2.	Konfigurieren Sie Ihr Veröffentlichungsprofil für die Unterstützung von Anwendungsupgrades. Entsprechende Anweisungen finden Sie [hier](service-fabric-visualstudio-configure-upgrade.md).

3.	Ersetzen Sie den Schritt **In sicherem Cluster bereitstellen** durch einen Schritt, der direkt „Deploy-FabricApplication.ps1“ aufruft (und Ihr Veröffentlichungsprofil übergibt).

4.	Entfernen Sie beide Schritte vom Typ **Clusterressourcengruppe entfernen** aus Ihrer Builddefinition.

## Nächste Schritte

Weitere Informationen zu Continuous Integration für Service Fabric-Anwendungen finden Sie in den folgenden Artikeln:

- [Startseite der Builddokumentation](https://msdn.microsoft.com/Library/vs/alm/Build/overview)
- [Bereitstellen eines Build-Agents](https://msdn.microsoft.com/Library/vs/alm/Build/agents/windows)
- [Erstellen und Konfigurieren einer Builddefinition](https://msdn.microsoft.com/Library/vs/alm/Build/vs/define-build)

<!---HONumber=AcomDC_0316_2016-->