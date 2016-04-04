<properties
   pageTitle="Schützen eines Service Fabric-Clusters | Microsoft Azure"
   description="Erfahren Sie, wie Sie einen Service Fabric-Cluster schützen. Welche Optionen stehen zur Verfügung?"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/01/2016"
   ms.author="chackdan"/>

# Schützen von Service Fabric-Clustern

Ein Azure Service Fabric-Cluster ist eine Ressource, die sich in Ihrem Besitz befindet. Um den unbefugten Zugriff auf die Ressource zu verhindern, müssen Sie sie absichern. Dies gilt besonders, wenn darin Workloads für die Produktion ausgeführt werden. Dieser Artikel führt Sie durch den Vorgang zum Schützen eines Service Fabric-Clusters.

## Szenarien für die Clustersicherheit

Service Fabric bietet Sicherheit für die folgenden Szenarien:

1. **Knoten-zu-Knoten-Sicherheit:** Hierdurch wird die Kommunikation zwischen den virtuellen Computern und den Computern im Cluster gesichert. So wird sichergestellt, dass nur Computer, die zum Beitreten zum Cluster berechtigt sind, Anwendungen und Dienste im Cluster hosten können.

	![Diagramm für die Kommunikation zwischen Knoten][Node-to-Node]

2. **Client-zu-Knoten-Sicherheit:** Hierdurch wird die Kommunikation zwischen einem Service Fabric-Client und einzelnen Knoten im Cluster gesichert. Bei dieser Art von Sicherheit wird die Clientkommunikation authentifiziert und geschützt. So wird sichergestellt, dass nur autorisierte Benutzer auf den Cluster und die im Cluster bereitgestellten Anwendungen zugreifen können. Clients werden entweder über ihre Windows-Sicherheitsanmeldeinformationen oder ihre Zertifikat-Sicherheitsanmeldeinformationen eindeutig identifiziert.

	![Diagramm für die Kommunikation zwischen Client und Knoten][Client-to-Node]

	Für die Knoten-zu-Knoten- oder die Client-zu-Knoten-Sicherheit können Sie entweder die [Zertifikatssicherheit](https://msdn.microsoft.com/library/ff649801.aspx) oder die [Windows-Sicherheit](https://msdn.microsoft.com/library/ff649396.aspx) verwenden. Die Auswahl für die Knoten-zu-Knoten- bzw. Client-zu-Knoten-Sicherheit erfolgt unabhängig voneinander und kann identisch oder jeweils unterschiedlich sein.

	Azure Service Fabric verwendet X.509-Serverzertifikate, die Sie als Teil der Knotentypkonfigurationen angeben, wenn Sie einen Cluster erstellen. Ein schneller Überblick darüber, was diese Zertifikate sind und wie Sie sie abrufen oder erstellen können, wird am Ende dieses Artikels bereitgestellt.

3. **Rollenbasierte Zugriffssteuerung (RBAC):** Administratorvorgänge auf dem Cluster werden auf einen bestimmten Satz von Zertifikaten beschränkt.

## Schützen eines Service Fabric-Clusters mit Zertifikaten

Zum Einrichten eines sicheren Service Fabric-Clusters benötigen Sie mindestens ein X.509-Serverzertifikat, das Sie in Azure Key Vault hochladen und bei der Erstellung des Clusters verwenden.

Der Vorgang umfasst drei Schritte:

1. Erwerben des X.509-Zertifikats
2. Hochladen des Zertifikats in Azure Key Vault
3. Eingeben des Speicherorts und der Details zum Zertifikat beim Vorgang zur Erstellung des Service Fabric-Clusters.

### Schritt 1: Erwerben des X.509-Zertifikats

1. Für Cluster, die Produktionsworkloads ausführen, müssen Sie ein von der [Zertifizierungsstelle](https://en.wikipedia.org/wiki/Certificate_authority) signiertes X.509-Zertifikat zum Schutz des Clusters verwenden. Informationen zum Abrufen dieser Zertifikate finden Sie unter [Vorgehensweise: Abrufen eines Zertifikats](http://msdn.microsoft.com/library/aa702761.aspx).
2. Für Cluster, die Sie nur für Testzwecke verwenden, können Sie ein selbstsigniertes Zertifikat verwenden. In Schritt 2.5 unten wird dieser Vorgang erläutert.

### Schritt 2: Hochladen des X.509-Zertifikats in Key Vault

Dies ist ein komplexer Vorgang. Wir haben daher ein PowerShell-Modul in ein Git-Repository hochgeladen, mit dem dieser Vorgang für Sie durchgeführt wird.

**Schritt 2.1**: Kopieren Sie diesen Ordner aus [diesem Git-Repository](https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers) auf Ihren Computer.

**Schritt 2.2**: Stellen Sie sicher, dass mindestens Azure PowerShell 1.0 auf Ihrem Computer installiert ist. Falls noch nicht erfolgt, sollten Sie unbedingt die Schritte unter [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md) befolgen.

**Schritt 2.3**: Öffnen Sie ein PowerShell-Fenster, und importieren Sie die Datei „ServiceFabricRPHelpers.psm“. (Dies ist das Modul, das Sie in Schritt 2.1 heruntergeladen haben.)

```
Remove-Module ServiceFabricRPHelpers
```

Kopieren Sie das folgende Beispiel, und ändern Sie den Pfad zur PSM1-Datei so ab, dass er dem Pfad auf Ihrem Computer entspricht.

```
Import-Module "C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"
```

**Schritt 2.4**: Wenn Sie ein Zertifikat verwenden, das Sie bereits bezogen haben, befolgen Sie das Verfahren in diesem Schritt. Fahren Sie andernfalls mit Schritt 2.5 fort, in dem erläutert wird, wie Sie das selbstsignierte Zertifikat im Schlüsseltresor erstellen und bereitstellen.

Melden Sie sich beim Azure-Konto an. Wenn dieser PowerShell-Befehl aus irgendeinem Grund Fehler verursacht, sollten Sie prüfen, ob Azure PowerShell ordnungsgemäß installiert ist.

```
Login-AzureRmAccount
```

Das folgende Skript erstellt eine neue Ressourcengruppe und/oder einen Schlüsseltresor, sofern noch nicht vorhanden. **Hinweis: Bei Verwendung eines bereits vorhandenen Schlüsseltresors muss er mithilfe dieses Skripts bereitstellungsfähig gemacht werden.**
```
Set-AzureRmKeyVaultAccessPolicy -VaultName <Name of the Vault> -ResourceGroupName <string> -EnabledForTemplateDeployment -EnabledForDeployment
```

```
Invoke-AddCertToKeyVault -SubscriptionId <your subscription id> -ResourceGroupName <string> -Location <region> -VaultName <Name of the Vault> -CertificateName <Name of the Certificate> -Password <Certificate password> -UseExistingCertificate -ExistingPfxFilePath <Full path to the .pfx file>
```
Hier ist ein ausgefülltes Skript als Beispiel angegeben.

```
Invoke-AddCertToKeyVault -SubscriptionId 35389201-c0b3-405e-8a23-9f1450994307 -ResourceGroupName chackdankeyvault4doc -Location westus -VaultName chackdankeyvault4doc  -CertificateName chackdantestcertificate2 -Password abcd123 -UseExistingCertificate -ExistingPfxFilePath C:\MyCertificates\ChackdanTestCertificate.pfx
```

Nach erfolgreichem Abschluss des Skripts erhalten Sie eine Ausgabe wie unten angegeben, die Sie für Schritt 3 (Einrichten eines sicheren Clusters) benötigen.

- **Zertifikatfingerabdruck**: 2118C3BCE6541A54A0236E14ED2CCDD77EA4567A

- **SourceVault** /Ressourcen-ID des Schlüsseltresors: /subscriptions/35389201-c0b3-405e-8a23-9f1450994307/resourceGroups/chackdankeyvault4doc/providers/Microsoft.KeyVault/vaults/chackdankeyvault4doc

- **Zertifikat-URL** /URL des Zertifikatspeicherorts im Schlüsseltresor: https://chackdankeyvalut4doc.vault.azure.net:443/secrets/chackdantestcertificate3/ebc8df6300834326a95d05d90e0701ea

Jetzt verfügen Sie über die Informationen, die Sie zum Einrichten eines sicheren Clusters benötigen. Fahren Sie mit Schritt 3 fort.

**Schritt 2.5**: Wenn Sie *kein* Zertifikat besitzen und ein neues selbstsigniertes Zertifikat erstellen und in den Schlüsseltresor hochladen möchten, gehen Sie wie folgt vor.

Melden Sie sich beim Azure-Konto an. Wenn dieser PowerShell-Befehl aus irgendeinem Grund Fehler verursacht, sollten Sie prüfen, ob Azure PowerShell ordnungsgemäß installiert ist.

```
Login-AzureRmAccount
```

Das folgende Skript erstellt eine neue Ressourcengruppe und/oder einen Schlüsseltresor, sofern noch nicht vorhanden.

```
Invoke-AddCertToKeyVault -SubscriptionId <you subscription id> -ResourceGroupName <string> -Location <region> -VaultName <Name of the Vault> -CertificateName <Name of the Certificate> -Password <Certificate password> -CreateSelfSignedCertificate -DnsName <string- see note below.> -OutputPath <Full path to the .pfx file>
```

Der Ausgabepfad (OutputPath), den Sie für das Skript angegeben haben, enthält das neue selbstsignierte Zertifikat, das das Skript in Schlüsseltresor hochgeladen hat.

>[AZURE.NOTE] Die DnsName-Zeichenfolge gibt einen oder mehrere DNS-Namen an, die in die Erweiterung des alternativen Antragstellernamens (subject-alternative-name) des Zertifikats eingefügt werden, wenn ein zu kopierendes Zertifikat nicht über den CloneCert-Parameter angegeben wird. Der erste DNS-Name wird auch als Antragstellername gespeichert. Wenn kein Signaturzertifikat angegeben ist, wird der erste DNS-Name auch als Ausstellername gespeichert.

Weitere Informationen zur Erstellung eines selbstsignierten Zertifikats finden Sie unter [https://technet.microsoft.com/library/hh848633.aspx](https://technet.microsoft.com/library/hh848633.aspx).

Hier ist ein ausgefülltes Skript als Beispiel angegeben.

```
Invoke-AddCertToKeyVault -SubscriptionId 35389201-c0b3-405e-8a23-9f1450994307 -ResourceGroupName chackdankeyvault4doc -Location westus -VaultName chackdankeyvault4doc  -CertificateName chackdantestcertificate3 -Password abcd123 -CreateSelfSignedCertificate -DnsName www.chackdan.westus.azure.com -OutputPath C:\MyCertificates
```

Da es sich um ein selbstsigniertes Zertifikat handelt, müssen Sie es in den Speicher „Vertrauenswürdige Personen“ Ihres Computers importieren, bevor Sie es zum Herstellen einer Verbindung mit einem sicheren Cluster verwenden können.

```
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath C:C:\MyCertificates\ChackdanTestCertificate.pfx -Password (Read-Host -AsSecureString -Prompt "Enter Certificate Password ")
```

```
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath C:C:\MyCertificates\ChackdanTestCertificate.pfx -Password (Read-Host -AsSecureString -Prompt "Enter Certificate Password ")
```

Nach dem erfolgreichen Abschluss des Skripts erhalten Sie eine Ausgabe wie unten angegeben. Diese benötigen Sie für Schritt 3.

- **Zertifikatfingerabdruck**: 64881409F4D86498C88EEC3697310C15F8F1540F

- **SourceVault** /Ressourcen-ID des Schlüsseltresors: /subscriptions/35389201-c0b3-405e-8a23-9f1450994307/resourceGroups/chackdankeyvault4doc/providers/Microsoft.KeyVault/vaults/chackdankeyvault4doc

- **Zertifikat-URL** /URL des Zertifikatspeicherorts im Schlüsseltresor: https://chackdankeyvalut4doc.vault.azure.net:443/secrets/chackdantestcertificate3/fvc8df6300834326a95d05d90e0720ea

### Schritt 3: Einrichten eines sicheren Clusters

Führen Sie die Schritte unter [Erstellung eines Service Fabric-Clusters](service-fabric-cluster-creation-via-portal.md) aus, bis Sie zum Abschnitt „Sicherheitskonfigurationen“ gelangen. Fahren Sie mit den hier aufgeführten Anweisungen fort, um Ihre Sicherheitskonfigurationen einzurichten:

>[AZURE.NOTE]
Die benötigten Zertifikate werden auf Knotentypebene unter den Sicherheitskonfigurationen angegeben. Sie müssen diese für jeden Knotentyp in Ihrem Cluster angeben. In diesem Dokument wird die Vorgehensweise im Portal erläutert. Sie können jedoch auch eine Azure-Ressourcen-Manager-Vorlage verwenden.

![Screenshot der Sicherheitskonfigurationen im Azure-Portal][SecurityConfigurations_01]

Erforderliche Parameter:

- **Sicherheitsmodus.** Wählen Sie **X.509-Zertifikat** aus. Dadurch geben Sie Service Fabric an, dass Sie einen sicheren Cluster einrichten möchten.
- **Clusterschutzebene.** Informationen zur Bedeutung der einzelnen Werte finden Sie im Dokument [Grundlagen der Schutzebene](https://msdn.microsoft.com/library/aa347692.aspx). Obwohl hier drei Werte („EncryptAndSign“, „Sign“ und „None“) zulässig sind, empfiehlt es sich, den Standardwert „EncryptAndSign“ beizubehalten. Sie sollten diesen Wert nur ändern, wenn Sie mit dem Vorgang vertraut sind.
- **Quelltresor.** Dies bezieht sich auf die Ressourcen-ID des Schlüsseltresors. Sie sollte folgendes Format aufweisen:

    ```
    /subscriptions/<Sub ID>/resourceGroups/<Resource group name>/providers/Microsoft.KeyVault/vaults/<vault name>
    ```

- **Zertifikat-URL.** Dies bezieht sich auf die Speicherort-URL in Ihrem Schlüsseltresor, in den das Zertifikat hochgeladen wurde. Sie sollte folgendes Format aufweisen:

    ```
    https://<name of the vault>.vault.azure.net:443/secrets/<exact location>
```
```
    https://chackdan-kmstest-eastus.vault.azure.net:443/secrets/MyCert/6b5cc15a753644e6835cb3g3486b3812
    ```

- ** Zertifikatfingerabdruck.** Dies bezieht sich auf den Fingerabdruck des Zertifikats, der unter der zuvor festgelegten URL zu finden ist.

Optionale Parameter:

 - Sie können optional weitere Zertifikate für Clientcomputer angeben, die Sie zum Ausführen von Vorgängen im Cluster verwenden. Standardmäßig wird der Fingerabdruck, den Sie in den erforderlichen Parametern angegeben haben, der autorisierten Liste der Fingerabdrücke hinzugefügt, die für Clientvorgänge zugelassen werden.

Admin-Client: Mit diesen Informationen wird überprüft, ob der Client für die Verbindung mit dem Endpunkt der Clusterverwaltung die richtigen Anmeldedaten zum Ausführen von Administratorvorgängen sowie schreibgeschützten Vorgängen auf dem Cluster verwendet. Sie können weitere Zertifikate angeben, wenn Sie für Administratorvorgänge autorisiert werden möchten.

- **Autorisieren mit.** Gibt für Service Fabric an, ob dieses Zertifikat anhand des Antragstellernamens oder anhand des Fingerabdrucks gesucht werden soll. Die Verwendung des Antragstellernamens für die Autorisierung ist aus Sicherheitsgründen nicht empfehlenswert, lässt jedoch mehr Flexibilität zu.
- **Antragstellername.** Dies ist nur erforderlich, wenn Sie angegeben haben, dass die Autorisierung mit dem Antragstellernamen erfolgen soll.
- **Fingerabdruck des Ausstellers.** Eine zusätzliche Sicherheitsprüfung, die der Server ausführen kann, wenn ein Client seine Anmeldeinformationen an den Server übermittelt.

Schreibgeschützter Client: Mit diesen Informationen wird überprüft, ob der Client für die Verbindung mit dem Endpunkt der Clusterverwaltung die richtigen Anmeldedaten zum Ausführen schreibgeschützter Vorgänge auf dem Cluster verwendet. Sie können weitere Zertifikate angeben, wenn Sie für schreibgeschützte Vorgänge autorisiert werden möchten.

- **Autorisieren mit.** Gibt für Service Fabric an, ob dieses Zertifikat anhand des Antragstellernamens oder anhand des Fingerabdrucks gesucht werden soll. Die Verwendung des Antragstellernamens für die Autorisierung ist aus Sicherheitsgründen nicht empfehlenswert, lässt jedoch mehr Flexibilität zu.
- **Antragstellername.** Dies ist nur erforderlich, wenn Sie angegeben haben, dass die Autorisierung mit dem Antragstellernamen erfolgen soll.
- **Fingerabdruck des Ausstellers.** Dies stellt eine zusätzliche Sicherheitsstufe dar, die der Server ausführen kann, wenn ein Client seine Anmeldeinformationen an den Server übermittelt.

## Aktualisieren der Zertifikate im Cluster

Mit Service Fabric können Sie zwei Zertifikate angeben, ein primäres und ein sekundäres. Standardmäßig ist das Zertifikat, das Sie zum Zeitpunkt der Erstellung angeben, das primäre Zertifikat. Um ein weiteres Zertifikat hinzuzufügen, müssen Sie das Zertifikat den virtuellen Computern im Cluster bereitstellen. Im vorherigen Schritt 2 wird erläutert, wie Sie ein neues Zertifikat in den Schlüsseltresor hochladen. Sie können denselben Schlüsseltresor wie für das erste Zertifikat verwenden. Weitere Informationen finden Sie unter [Deploy certificates to VMs from a customer-managed Key Vault](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx) (Bereitstellen von Zertifikaten für VMs über einen vom Kunden verwalteten Schlüsseltresor).

Sobald dieser Vorgang abgeschlossen ist, signalisieren Sie Service Fabric über das Portal oder über den Ressourcen-Manager, dass Sie über ein sekundäres Zertifikat verfügen, das ebenfalls verwendet werden kann. Sie benötigen lediglich einen Fingerabdruck.

Der Vorgang zum Hinzufügen eines sekundären Zertifikats wird im Folgenden beschrieben:

1. Wechseln Sie im Portal zur Clusterressource, der dieses Zertifikat hinzugefügt werden soll.
2. Klicken Sie unter **Einstellungen** auf die Zertifikateinstellung, und geben Sie den sekundären Zertifikatfingerabdruck ein.
3. Klicken Sie auf **Speichern**. Eine Bereitstellung wird gestartet, nach deren erfolgreichem Abschluss Sie sowohl das primäre als auch das sekundäre Zertifikat zum Ausführen von Verwaltungsvorgängen im Cluster verwenden können.

![Screenshot mit Zertifikatfingerabdrücken im Portal][SecurityConfigurations_02]

Es folgt der Prozess zum Entfernen eines alten Zertifikats, damit der Cluster es nicht mehr verwendet:

1. Besuchen Sie das Portal, und navigieren Sie zu den Sicherheitseinstellungen Ihres Clusters.
2. Entfernen Sie eines der Zertifikate.
3. Klicken Sie auf **Speichern**. Daraufhin beginnt eine neue Bereitstellung. Nachdem die Bereitstellung abgeschlossen ist, kann das entfernte Zertifikat nicht mehr zum Verbinden mit dem Cluster verwendet werden.

>[AZURE.NOTE] Für einen sicheren Cluster muss stets mindestens ein gültiges (nicht widerrufenes oder abgelaufenes) Zertifikat (primär oder sekundär) bereitgestellt sein. Andernfalls können Sie nicht auf den Cluster zugreifen.


## 
Arten von Zertifikaten, die von Service Fabric verwendet werden

### X.509-Zertifikate

Digitale X.509-Zertifikate werden in der Regel verwendet, um Clients und Server zu authentifizieren sowie um Nachrichten zu verschlüsseln und digital zu signieren. Weitere Informationen zu diesen Zertifikaten finden Sie unter [Arbeiten mit Zertifikaten](http://msdn.microsoft.com/library/ms731899.aspx) in der MSDN Library.

>[AZURE.NOTE]
- Zertifikate, die in Clustern mit Produktionsworkloads verwendet werden, müssen entweder mit einem korrekt konfigurierten Windows Server-Zertifikatsdienst erstellt oder über eine genehmigte [Zertifizierungsstelle](https://en.wikipedia.org/wiki/Certificate_authority) bezogen werden.
- Verwenden Sie in der Produktion niemals temporäre oder Testzertifikate, die mit Tools wie „MakeCert.exe“ erstellt wurden.
- Für Cluster, die Sie nur für Testzwecke verwenden, können Sie ein selbstsigniertes Zertifikat verwenden.

### Serverzertifikate und Clientzertifikate

#### X.509-Serverzertifikate

Serverzertifikate müssen primär einen Server (Knoten) für Clients oder einen Server (Knoten) für einen Server (Knoten) authentifizieren. Eine der ersten Prüfungen bei der Authentifizierung eines Knotens durch einen Client oder Knoten besteht darin, den Wert des allgemeinen Namens im Feld „Antragsteller“ zu prüfen. Dieser allgemeine Name oder einer der alternativen Antragstellernamen der Zertifikate muss in der Liste der zulässigen allgemeinen Namen vorhanden sein.

Im folgenden Artikel erfahren Sie, wie Zertifikate mit alternativen Antragstellernamen (SAN) generiert werden: [Hinzufügen eines alternativen Antragstellernamens zu einem gesicherten LDAP-Zertifikat](http://support.microsoft.com/kb/931351).

>[AZURE.NOTE] Das Antragstellerfeld kann mehrere Werte enthalten. Jeder davon ist mit einer Initialisierung versehen, die den Typ des Werts als Präfix angibt. In den meisten Fällen verwendet die Initialisierung „CN“ für den allgemeinen Namen, z. B. „CN = www.contoso.com“. Das Feld für den Antragsteller kann auch leer gelassen werden. Wenn das optionale Feld für den alternativen Antragstellernamen ausgefüllt ist, muss es sowohl den allgemeinen Namen des Zertifikats als auch einen Eintrag pro alternativen Antragstellernamen enthalten. Diese werden als Werte für DNS-Namen eingegeben.

Der Wert des Felds „Beabsichtigte Zwecke“ des Zertifikats muss einen entsprechenden Wert enthalten, z. B. „Serverauthentifizierung“ oder „Clientauthentifizierung“.

#### Clientzertifikate

Clientzertifikate werden in der Regel nicht von einer Drittanbieter-Zertifizierungsstelle ausgestellt. Stattdessen enthält der persönliche Speicher des aktuellen Benutzerspeicherorts in der Regel Clientzertifikate, die von einer Stammzertifizierungsstelle mit dem beabsichtigten Zweck „Clientauthentifizierung“ platziert werden. Der Client kann ein solches Zertifikat verwenden, wenn eine gegenseitige Authentifizierung erforderlich ist.

>[AZURE.NOTE] Für sämtliche Verwaltungsvorgänge für einen Service Fabric-Cluster sind Serverzertifikate erforderlich. Clientzertifikate können für Verwaltungsaufgaben nicht verwendet werden.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## Nächste Schritte

- [Service Fabric-Cluster-Upgradeprozess und Erwartungen](service-fabric-cluster-upgrade.md)
- [Verwalten von Service Fabric-Anwendungen in Visual Studio](service-fabric-manage-application-in-visual-studio.md)
- [Einführung in das Service Fabric-Integritätsmodell](service-fabric-health-introduction.md)
- [Anwendungssicherheit und RunAs](service-fabric-application-runas-security.md)

<!--Image references-->
[SecurityConfigurations_01]: ./media/service-fabric-cluster-security/SecurityConfigurations_01.png
[SecurityConfigurations_02]: ./media/service-fabric-cluster-security/SecurityConfigurations_02.png
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

<!---HONumber=AcomDC_0323_2016-->