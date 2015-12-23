<properties
   pageTitle="Continuous Integration für Service Fabric | Microsoft Azure"
   description="Hier erhalten Sie einen Überblick über die Einrichtung der Continuous Integration für eine Service Fabric-Anwendung mit Visual Studio Online (VSO)."
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
   ms.date="10/16/2015"
   ms.author="cawa" />

# Einrichten der Continuous Integration für eine Service Fabric-Anwendung mit Visual Studio Online (VSO)

Dieser Artikel beschäftigt sich mit der Einrichtung der Continuous Integration (CI) für eine Service Fabric-Anwendung mit Visual Studio Online (VSO), um die automatische Erstellung, Verpackung und Bereitstellung Ihrer Anwendung zu ermöglichen. Hinweis: Dieses Dokument wurde für die aktuelle Umgebung erstellt und wird wahrscheinlich im Laufe der Zeit an zukünftige Entwicklungen angepasst. Außerdem ist zu beachten, dass die Anweisungen jedes Mal eine Neuerstellung des Clusters zur Folge haben.

## Voraussetzungen

Richten Sie zunächst Ihr Projekts in Visual Studio Online ein.

1. Erstellen Sie mithilfe Ihres [Microsoft-Kontos](http://www.microsoft.com/account) ein VSO-Konto, sofern Sie noch keins besitzen.
2. Erstellen Sie in VSO mithilfe Ihres Microsoft-Kontos ein neues Projekt.
3. Führen Sie für die Quelle Ihrer neuen oder vorhandenen Service Fabric-App einen Pushvorgang an dieses Projekt durch.

Weitere Informationen zur Verwendung von VSO-Projekten finden Sie unter [Herstellen einer Verbindung mit Visual Studio](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online) .

## Einrichtungsschritte

### Einrichten der Authentifizierung für die Automatisierung

Bevor Sie den Buildcomputer einrichten können, müssen Sie einen Dienstprinzipal einrichten, den die Automatisierung zur Authentifizierung bei Azure verwendet. Zudem müssen Sie ein Zertifikat erstellen und an einen Schlüsseltresor hochladen, da der Azure-Schlüsseltresor keine Dienstprinzipalauthentifizierung unterstützt. Diese Schritte können auf einem beliebigen Computer ausgeführt werden. Der Entwicklungscomputer ist dabei eine gute Wahl.

### Installieren von Azure PowerShell und Anmelden

1.	Installieren Sie Azure PowerShell.
    - Installieren Sie „PowerShellGet“. Installieren Sie hierzu [Windows Management Framework 5.0](http://www.microsoft.com/download/details.aspx?id=48729), da „PowerShellGet“ darin enthalten ist.

    >[AZURE.NOTE]Bei Verwendung von Windows 10 mit neuesten Updates können Sie diesen Schritt überspringen.


1.	Installieren und aktualisieren Sie das AzureRM-Modul.
    1.  Wenn Sie eine ältere Version von Azure PowerShell installiert haben, entfernen Sie sie. Klicken Sie mit der rechten Maustaste auf die Schaltfläche „Start“, und wählen Sie „Programme hinzufügen/entfernen“. Suchen Sie nach „Azure PowerShell“, und deinstallieren Sie es.
    1.  Öffnen Sie eine PowerShell-Eingabeaufforderung.
    1.	Installieren Sie das AzureRM-Modul mit dem Befehl `Install-Module AzureRM`.
    1.	Aktualisieren Sie das AzureRM-Modul mit dem Befehl `Update-AzureRM`.
1.	Deaktivieren (oder aktivieren) Sie die Azure-Datensammlung.

    Andernfalls werden Sie von Azure-Cmdlets immer wieder gefragt, ob Sie die Datensammlung verwenden möchten. Dabei wird auf eine Benutzereingabe gewartet, was die Automatisierung blockiert. Treffen Sie daher bereits vorab eine Entscheidung, um diese Nachfrage zu unterdrücken. Führen Sie dazu einen der folgenden Befehle aus:

    - Enable-AzureRmDataCollection
    - Disable-AzureRmDataCollection

1.	Melden Sie sich bei Azure PowerShell an.
    1. Öffnen Sie eine PowerShell-Eingabeaufforderung mit Administratorrechten, und führen Sie den Befehl `Login-AzureRMAccount` aus.
    1. Geben Sie im angezeigten Dialogfeld Ihre Azure-Anmeldeinformationen ein.
    1. Führen Sie den Befehl `Get-AzureRmSubscription` aus.
    1. Suchen Sie das zu verwendende Abonnement, und führen Sie den Befehl `Select-AzureRmSubscription -SubscriptionId <id for your subscription>` aus.

### Erstellen eines Dienstprinzipals

1.	Extrahieren Sie [ServiceFabricCIManualScripts.zip](http://go.microsoft.com/fwlink/?LinkId=703773) in einen Ordner auf diesem Computer.
1.	Wechseln Sie über eine PowerShell-Eingabeaufforderung mit Administratorrechten zu dem Verzeichnis, in das Sie „ServiceFabricCIManualScripts.zip“ extrahiert haben.
1.	Wählen Sie mithilfe des folgenden Befehls ein Kennwort für den Dienstprinzipal. Merken Sie sich dieses Kennwort, da es als Buildvariable verwendet wird.

    ```
    $password = Read-Host -AsSecureString
    ```
1.	Führen Sie das PowerShell-Skript „Create-ServicePrincipal.ps1“ mit folgenden Parametern aus:

    |Parameter|Wert|
    |---|---|
    |ServicePrincipalDisplayName|Ein beliebiger Name.|
    |ServicePrincipalHomePage|Ein beliebiger URI. Muss nicht tatsächlich vorhanden sein.|
    |ServicePrincipalIdentifierUri|Ein beliebiger eindeutiger URI. Muss nicht tatsächlich vorhanden sein.|
    |ServicePrincipalSecurePassword|$password|

    Nach Abschluss des Skripts werden die folgenden drei Werte ausgegeben. Notieren Sie sich die Werte, da sie als Buildvariablen verwendet werden.

    - ServicePrincipalId
    - ServicePrincipalTenantId
    - ServicePrincipalSubscriptionId

### Erstellen und Hochladen eines Zertifikats an einen neuen Azure-Schlüsseltresor

1.	Wechseln Sie über eine PowerShell-Eingabeaufforderung mit Administratorrechten zu dem Verzeichnis, in das Sie „ServiceFabricCIManualScripts.zip“ extrahiert haben.
1.	Führen Sie das PowerShell-Skript „CreateAndUpload-Certificate.ps1“ mit folgenden Parametern aus:

    |Parameter|Wert|
    |---|---|
    |ServiceFabricKeyVaultLocation|Ein beliebiger Wert. Muss dem Ort entsprechen, an dem Sie den Cluster erstellen möchten.|
    |ServiceFabricCertificateSecretName|Ein beliebiger Wert.|
    |ServiceFabricSecureCertificatePassword|Ein beliebiger Wert. Wird verwendet, wenn Sie das Zertifikat in Ihren Buildcomputer importieren.|
    |ServiceFabricKeyVaultResourceGroupName|Ein beliebiger Wert. Verwenden Sie jedoch nicht den Ressourcengruppennamen, den Sie für Ihren Cluster verwenden möchten.|
    |ServiceFabricKeyVaultName|Ein beliebiger Wert.|
    |ServiceFabricPfxFileOutputPath|Ein beliebiger Wert. Diese Datei wird verwendet, wenn Sie das Zertifikat in Ihren Buildcomputer importieren.|

    Nach Abschluss des Skripts werden die folgenden drei Werte ausgegeben. Notieren Sie sich diese Werte, da sie als Buildvariablen verwendet werden.

    - ServiceFabricCertificateThumbprint
    - ServiceFabricKeyVaultId
    - ServiceFabricCertificateSecretId


## Einrichten Ihres Buildcomputers

Beachten Sie, dass die Builddefinition, die Sie anhand dieser Anweisungen erstellen, keine parallelen Builds unterstützt (auch nicht auf separaten Computern). Das liegt daran, dass die einzelnen Builds jeweils die gleiche Ressourcengruppe/den gleichen Cluster beanspruchen würden. Wenn Sie mehrere Build-Agents ausführen möchten, müssen Sie die folgenden Anweisungen/Skripts anpassen, um diese Störung zu vermeiden.

### Installieren von Visual Studio 2015

1.	Wenn Sie bereits einen Computer bereitgestellt haben (oder Ihren eigenen bereitstellen möchten), installieren Sie auf diesem Computer [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
2.	Wenn Sie noch nicht über einen Computer verfügen, können Sie schnell einen virtuellen Azure-Computer mit vorinstalliertem Visual Studio 2015 bereitstellen. Gehen Sie dazu folgendermaßen vor:
    1.	Melden Sie sich am [Azure-Verwaltungsportal](http://portal.azure.com) an.
    1.	Wählen Sie links oben auf dem Bildschirm den Befehl **Neu** aus.
    1.	Wählen Sie die Option **Marketplace** aus.
    1.	Suchen Sie nach **Visual Studio 2015**.
    1.	Wählen Sie **Compute** > **Virtueller Computer** > **Aus Katalog** aus.
    1.	Wählen Sie das Image **Visual Studio Enterprise 2015 With Azure SDK 2.7 on Windows Server 2012 R2** aus.

        >[AZURE.NOTE]Azure SDK ist zwar keine erforderliche Komponente, es sind jedoch keine Images verfügbar, bei denen nur Visual Studio 2015 installiert ist.

    1.	Befolgen Sie die Anweisungen im Dialogfeld, um Ihren virtuellen Computer zu erstellen. (Für eine optimale Datenträger- und CPU-Leistung empfiehlt sich die Verwendung eines virtuellen Computers der D-Serie.)

### Installieren des Service Fabric-SDKs

Installieren Sie das [Service Fabric-SDK](https://azure.microsoft.com/campaigns/service-fabric/).

### Registrieren des NuGet-Repositorys des Service Fabric-SDKs

1.	Erstellen Sie auf dem Buildcomputer das Verzeichnis `%SYSTEMDRIVE%\Windows\ServiceProfiles\LocalService\AppData\Roaming\NuGet` (sofern noch nicht vorhanden).
2.	Befindet sich in diesem Verzeichnis bereits eine Datei namens „NuGet.config“, öffnen Sie sie, und fügen Sie das folgende Element als untergeordnetes Element von `<packageSources>` hinzu.

    ```
    <add key="Service Fabric SDK" value="<path to service fabric SDK>\packages" />`
    ```

3.	Ist die Datei „NuGet.config“ noch nicht vorhanden, erstellen Sie sie mit folgendem Inhalt. Ersetzen Sie dabei `<path to service fabric SDK>` durch den Pfad zum Service Fabric-SDK auf dem Buildcomputer.
>[AZURE.NOTE]`<path to service fabric SDK>` ist standardmäßig `%ProgramFiles%\Microsoft SDKs\Service Fabric`.

    NuGet.config:

    ```
    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <packageSources>
        <!-- Add this repository to the list of available repositories -->
        <add key="Service Fabric SDK" value="<path to service fabric SDK>\packages" />
      </packageSources>
    </configuration>
    ```

### Installieren von Azure PowerShell

Führen Sie zum Installieren von Azure PowerShell die Schritte aus dem vorherigen Abschnitt (**Installieren von Azure PowerShell und Anmelden**) aus. Überspringen Sie dabei den Unterabschnitt zur Anmeldung bei Azure PowerShell.

### Registrieren der Azure PowerShell-Module beim lokalen Dienstkonto

1. Drücken Sie WINDOWS-TASTE+R, geben Sie **regedit** ein, und drücken Sie anschließend die EINGABETASTE.
2. Klicken Sie mit der rechten Maustaste auf den Knoten `HKEY_Users\.Default\Environment`, und wählen Sie **Neu > Wert der erweiterbaren Zeichenfolge** aus.
3. Geben Sie `PSModulePath` für den Namen und `%PROGRAMFILES%\WindowsPowerShell\Modules` für den Wert ein.

>[AZURE.NOTE]Führen Sie diesen Schritt aus, *bevor* Sie den Build-Agent starten. Andernfalls wird die neue Umgebungsvariable nicht verwendet.

### Importieren Ihres Automation-Zertifikats

1.	Importieren Sie das Zertifikat in Ihren Buildcomputer. Gehen Sie dazu folgendermaßen vor:
    1. Kopieren Sie die durch das Skript „CreateAndUpload-Certificate.ps1“ erstellte PFX-Datei auf den Buildcomputer.
    1. Öffnen Sie ein PowerShell-Administratorkonto, und führen Sie die folgenden Befehle aus. Verwenden Sie dabei das Kennwort, das Sie zuvor an „GenerateCertificate.ps1“ übergeben haben.

        ```
        $password = Read-Host -AsSecureString
        Import-PfxCertificate -FilePath <path/to/cert.pfx> -CertStoreLocation Cert:\LocalMachine\My -Password $password -Exportable
        ```  

1.	Führen Sie den Zertifikat-Manager aus.
    1. Öffnen Sie die Windows-Systemsteuerung. Klicken Sie mit der rechten Maustaste auf die Schaltfläche „Start“, und wählen Sie **Systemsteuerung** aus.
    1. Suchen Sie nach **Zertifikat**.
    1. Wählen Sie **Verwaltung** > **Computerzertifikate verwalten** aus.


1.	Erteilen Sie dem lokalen Dienstkonto die Berechtigung zur Verwendung Ihres Automation-Zertifikats.
    1.	Erweitern Sie unter **Zertifikate – lokaler Computer** den Knoten **Persönlich**, und wählen Sie anschließend **Zertifikate** aus.
    1.	Suchen Sie in der Liste nach Ihrem Zertifikat.
    1.	Klicken Sie mit der rechten Maustaste auf Ihr Zertifikat, und wählen Sie dann **Alle Aufgaben** > **Private Schlüssel verwalten**.
    1.	Wählen Sie die Schaltfläche **Hinzufügen** aus, geben Sie **Lokaler Dienst** ein, und wählen Sie dann **Namen überprüfen** aus.
    1.	Wählen Sie die Schaltfläche **OK** aus, und schließen Sie anschließend den Zertifikat-Manager.

![](media/service-fabric-set-up-continuous-integration/windows-certificate-manager.png)

### Registrieren Ihres Build-Agents

1.	Laden Sie „agent.zip“ herunter. Gehen Sie dazu folgendermaßen vor:
    1.	Melden Sie sich bei Ihrem Teamprojekt an. Beispiel: ****https://[your-VSO-account-name].visualstudio.com**.
    1.	Wählen Sie das Zahnradsymbol in der rechten oberen Bildschirmecke aus.
    1.	Wählen Sie in der Systemsteuerung die Registerkarte **Agentpools** aus.
    1.	Wählen Sie **Download-Agent** aus, um die Datei „agent.zip“ herunterzuladen.
    1.	Kopieren Sie „agent.zip“ auf den zuvor erstellten Buildcomputer.
    1.	Entzippen Sie „agent.zip“ auf dem Buildcomputer unter `C:\agent` (oder an einem anderen Speicherort mit einem kurzen Pfad).

        >[AZURE.NOTE]Wenn Sie ASP.NET 5-Webdienste erstellen möchten, empfiehlt sich die Wahl eines möglichst kurzen Ordnernamens. Andernfalls treten bei der Bereitstellung unter Umständen Fehler vom Typ **PathTooLongExceptions** auf.

1.	Führen Sie an einer PowerShell-Eingabeaufforderung mit Administratorrechten das Skript `C:\agent\ConfigureAgent.ps1` aus. Das Skript fordert Sie zur Eingabe folgender Parameter auf:

    |Parameter|Wert|
    |---|---|
    |Agent Name|Übernehmen Sie den Standardwert. (`Agent-[machine name]`)
    |TFS Url|Geben Sie die URL zu Ihrem Teamprojekt ein. Beispiel: `https://[your-VSO-account-name].visualstudio.com`.
    |Agent Pool|Geben Sie den Namen Ihres Agentpools ein. (Falls Sie keinen Agentpool erstellt haben, übernehmen Sie den Standardwert.)|
    |Work folder|Übernehmen Sie den Standardwert. In diesem Ordner erstellt der Build-Agent Ihre Anwendung. Hinweis: Wenn Sie ASP.NET 5-Webdienste erstellen möchten, empfiehlt sich die Wahl eines möglichst kurzen Ordnernamens. Andernfalls treten bei der Bereitstellung unter Umständen Fehler vom Typ „PathTooLongExceptions“ auf.|
    |Install as Windows Service?|Ändern Sie den Standardwert „N“ in „Y“.|
    |User account to run the service|Übernehmen Sie den Standardwert. (`NT AUTHORITY\LocalService)`)|
    |Un-configure existing agent?|Übernehmen Sie den Standardwert. (**N**)|

1. Sie werden zur Angabe von Anmeldeinformationen aufgefordert. Geben Sie die Anmeldeinformationen für Ihr Microsoft-Konto ein, das über Rechte für Ihr Teamprojekt verfügt.
1. Vergewissern Sie sich, dass Ihr Build-Agent registriert wurde. Gehen Sie dazu folgendermaßen vor:

    1. Wechseln Sie wieder zur Ihrem Webbrowser. Dieser müsste die Seite `https://[your-VSO-account-name].visualstudio.com/_admin/_AgentPool` anzeigen. Aktualisieren Sie die Seite.
    1. Wählen Sie den Agentpool aus, den Sie zuvor beim Ausführen von „ConfigureAgent.ps1“ ausgewählt haben.
    1. Vergewissern Sie sich, dass der Build-Agent in der Liste enthalten und die Statusanzeige grün ist. Ist die Statusanzeige rot, kann der Build-Agent keine Verbindung mit VSO herstellen.

![](media/service-fabric-set-up-continuous-integration/vso-configured-agent.png)


## Einrichten Ihrer Builddefinition

### Fügen Sie der Quellcodeverwaltung für Ihre Anwendung die Continuous Integration-Skripts hinzu.

1.	Extrahieren Sie [ServiceFabricCIAutomationScripts.zip](http://go.microsoft.com/fwlink/?LinkId=703775) in einen beliebigen Ordner der Quellcodeverwaltung.
1.	Checken Sie die resultierenden Dateien ein.

### Erstellen der Builddefinition

1.	Erstellen Sie eine leere Builddefinition. Gehen Sie dazu folgendermaßen vor:
    1.	Öffnen Sie Ihr Projekt in Visual Studio Online.
    1.	Wählen Sie die Registerkarte **Erstellen** aus.
    1.	Wählen Sie das grüne Pluszeichen (**+**) aus, um eine neue Builddefinition zu erstellen.
    1.	Wählen Sie die Option **Leer** und anschließend die Schaltfläche **Weiter** aus.
    1.  Vergewissern Sie sich, dass das richtige Repository und die richtige Verzweigung ausgewählt sind.
    1.  Wählen Sie die Agent-Warteschlange aus, bei der Sie Ihren Build-Agent registriert haben, und aktivieren Sie das Kontrollkästchen **Continuous Integration**.
1.	Erstellen Sie auf der Registerkarte **Variablen** die folgenden Variablen mit den angegebenen Werten:

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
    |ServiceFabricClusterResourceGroupName|Ein beliebiger Name.|||
    |ServiceFabricClusterName|Ein beliebiger Name.|||
    |ServiceFabricClusterLocation|Ein beliebiger Name, der dem Ort Ihres Schlüsseltresors entspricht.|||
    |ServiceFabricClusterAdminPassword|Ein beliebiger Name.|X||
    |ServiceFabricClusterResourceGroupTemplateFilePath|`<path/to/extracted/automation/scripts/ArmTemplate-Full-3xVM-Secure.json>`|||
    |ServiceFabricPublishProfilePath|`<path/to/your/publish/profiles/MyPublishProfile.xml>` Hinweis: Der Verbindungsendpunkt in Ihrem Veröffentlichungsprofil wird ignoriert. Stattdessen wird der Verbindungsendpunkt für Ihren temporären Cluster verwendet.|||
    |ServiceFabricDeploymentScriptPath|`<path/to/Deploy-FabricApplication.ps1>`|||
    |ServiceFabricApplicationProjectPath|`<path/to/your/fabric/application/project/folder>` Hier muss der Ordner mit Ihrer SFPROJ-Datei angegeben werden.||||

1.	Wählen Sie auf der Registerkarte **Trigger** die Optionen **Continuous Integration** und **Batchänderungen** aus.
1.	Wählen Sie auf der Registerkarte **Allgemein** die Warteschlange aus, bei der Sie Ihre Build-Agents registriert haben.
1.	Speichern Sie die Builddefinition, und benennen Sie sie. (Der Name kann später bei Bedarf geändert werden.)

### Hinzufügen eines Buildschritts

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

### Hinzufügen eines Schritts zum Entfernen der Clusterressourcengruppe

Wenn bei einem vorherigen Buildvorgang keine Bereinigung stattgefunden hat (beispielsweise, weil der Buildvorgang vor der Bereinigung abgebrochen wurde), ist unter Umständen eine Ressourcengruppe vorhanden, die einen Konflikt mit einer neuen Ressourcengruppe verursacht. Bereinigen Sie zur Vermeidung von Konflikten alle übrig gebliebenen Ressourcengruppen (und die dazugehörigen Ressourcen), bevor Sie eine neue erstellen.

1.	Wählen Sie auf der Registerkarte **Erstellen** den Befehl **Buildschritt hinzufügen...** aus.
2.	Wählen Sie **Hilfsprogramm** > **PowerShell** aus.
3.	Wählen Sie neben dem Namen des Buildschritts das Stiftsymbol aus, und ändern Sie den Namen in **Clusterressourcengruppe entfernen**.
4.	Wählen Sie neben **Skriptdateiname** den Befehl **...** aus. Navigieren Sie zu dem Ort, an dem Sie die Automatisierungsskripts extrahiert haben, und wählen Sie **Remove-ClusterResourceGroup.ps1** aus.
5.	Geben Sie unter **Argumente** Folgendes ein: `-ServicePrincipalPassword "$(ServicePrincipalPassword)"`.
6.	Speichern Sie die Builddefinition.

### Hinzufügen eines Schritts zum Bereitstellen in einem sicheren Cluster

1.	Wählen Sie auf der Registerkarte **Erstellen** den Befehl **Buildschritt hinzufügen...** aus.
2.	Wählen Sie **Hilfsprogramm** > **PowerShell** aus.
3.	Wählen Sie neben dem Namen des Buildschritts das Stiftsymbol aus, und ändern Sie den Namen in **In sicherem Cluster bereitstellen**.
4.	Wählen Sie neben **Skriptdateiname** die Schaltfläche **...** aus. Navigieren Sie zu dem Ort, an dem Sie die Automatisierungsskripts extrahiert haben, und wählen Sie **ProvisionAndDeploy-SecureCluster.ps1** aus.
5.	Geben Sie unter „Argumente“ Folgendes ein: `-ServicePrincipalPassword "$(ServicePrincipalPassword)" -ServiceFabricClusterAdminPassword "$(ServiceFabricClusterAdminPassword)"`.
6.	Speichern Sie die Builddefinition.

### Hinzufügen eines Schritts zum Entfernen der Clusterressourcengruppe

Nachdem Sie den temporären Cluster nun nicht mehr benötigen, müssen Sie ihn bereinigen. Andernfalls wird er Ihnen weiter in Rechnung gestellt. Dieser Schritt entfernt die Ressourcengruppe. Dabei werden der Cluster und alle anderen Ressourcen in der Gruppe entfernt.

1.	Wählen Sie auf der Registerkarte **Erstellen** den Befehl **Buildschritt hinzufügen...** aus.
1.	Wählen Sie **Hilfsprogramm** > **PowerShell** aus.
1.	Wählen Sie neben dem Namen des Buildschritts das Stiftsymbol aus, und ändern Sie den Namen in **Clusterressourcengruppe entfernen**.
1.	Wählen Sie neben **Skriptdateiname** die Schaltfläche **...** aus. Navigieren Sie zu dem Ort, an dem Sie die Automatisierungsskripts extrahiert haben, und wählen Sie **RemoveClusterResourceGroup.ps1** aus.
1.	Geben Sie unter „Argumente“ Folgendes ein: `-ServicePrincipalPassword "$(ServicePrincipalPassword)`.
1.	Aktivieren Sie unter **Steuerungsoptionen** das Kontrollkästchen **Immer ausführen**.
1.	Speichern Sie die Builddefinition.

### Testen

Wählen Sie den Befehl **Build zur Warteschlange hinzufügen** aus, um einen Buildvorgang zu starten. Buildvorgänge werden auch beim Einchecken ausgelöst.


## Alternativen

Mit den oben aufgeführten Anweisungen wird für jeden Buildvorgang ein neuer Cluster erstellt und am Ende des Buildvorgangs wieder entfernt. Wenn Sie stattdessen bei jedem Buildvorgang ein Anwendungsupgrade (für einen vorhandenen Cluster) durchführen möchten, führen Sie die folgenden Schritte aus:

1.	Erstellen Sie über das Azure-Verwaltungsportal oder über Azure PowerShell manuell einen Testcluster. Dabei können Sie sich am Skript „ProvisionAndDeploy-SecureCluster.ps1“ orientieren.
1.	Konfigurieren Sie Ihr Veröffentlichungsprofil für die Unterstützung von Anwendungsupgrades. Entsprechende Anweisungen finden Sie [hier](service-fabric-visualstudio-configure-upgrade.md).

1.	Ersetzen Sie den Schritt **In sicherem Cluster bereitstellen** durch einen Schritt, der direkt „Deploy-FabricApplication.ps1“ aufruft (und Ihr Veröffentlichungsprofil übergibt).
1.	Entfernen Sie beide Schritte vom Typ **Clusterressourcengruppe entfernen** aus Ihrer Builddefinition.

## Nächste Schritte

Weitere Informationen zu Continuous Integration für Service Fabric-Anwendungen finden Sie in den folgenden Artikeln:
- [Startseite der Builddokumentation](https://msdn.microsoft.com/Library/vs/alm/Build/overview)
- [Bereitstellen eines Build-Agents](https://msdn.microsoft.com/Library/vs/alm/Build/agents/windows)
- [Erstellen und Konfigurieren einer Builddefinition](https://msdn.microsoft.com/Library/vs/alm/Build/vs/define-build)

<!---HONumber=AcomDC_1210_2015-->
