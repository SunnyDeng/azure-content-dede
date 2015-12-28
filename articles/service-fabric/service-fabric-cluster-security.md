<properties
   pageTitle="Vorgehensweise: Schützen eines Service Fabric-Clusters | Microsoft Azure"
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
   ms.date="11/10/2015"
   ms.author="chackdan"/>

# Schützen eines Service Fabric-Clusters

Ein Service Fabric-Cluster ist eine Ressource, die sich in Ihrem Besitz befindet und die Sie schützen müssen, um den unbefugten Zugriff auf die Ressource zu verhindern. Dies gilt besonders, wenn darin Workloads für die Produktion ausgeführt werden. In diesem Dokument wird die Vorgehensweise Schritt für Schritt beschrieben.

##  Wichtige Cluster-Sicherheitsszenarien

Service Fabric bietet Sicherheit für die folgenden Szenarien:

1. **Knoten-zu-Knoten-Sicherheit** oder Schützen eines Clusters für die Kommunikation von Knoten zu Knoten. Schützt die Kommunikation zwischen den VMs/Computern im Cluster. So wird sichergestellt, dass nur Computer, die zum Beitreten zum Cluster berechtigt sind, Anwendungen und Dienste im Cluster hosten können.

	![Knoten zu Knoten][Node-to-Node]

2. **Client-zu-Knoten-Sicherheit** oder Schützen eines Service Fabric-Clients, der mit einem bestimmten Knoten im Cluster kommuniziert. Authentifiziert und schützt die Clientkommunikation. So wird sichergestellt, dass nur autorisierte Benutzer auf den Cluster und die Anwendungen zugreifen können, die im Windows Fabric-Cluster bereitgestellt werden. Clients werden entweder über ihre Windows-Sicherheitsanmeldeinformationen oder ihre Zertifikat-Sicherheitsanmeldeinformationen eindeutig identifiziert.

	![Client zu Knoten][Client-to-Node]

	Für beide Kommunikationsszenarien (Knoten zu Knoten oder Client zu Knoten) bietet Service Fabric Unterstützung für die Verwendung der [Zertifikatsicherheit](https://msdn.microsoft.com/library/ff649801.aspx) oder der [Windows-Sicherheit](https://msdn.microsoft.com/library/ff649396.aspx). Die Auswahl für die Knoten-zu-Knoten- bzw. Client-zu-Knoten-Sicherheit erfolgt unabhängig voneinander und kann identisch oder jeweils unterschiedlich sein.

	Azure Service Fabric verwendet X509-Serverzertifikate, die Sie als Teil der Knotentyp-Konfigurationen angeben, wenn Sie einen Cluster erstellen. Eine kurze Übersicht, was diese Zertifikate sind und wie Sie sie erwerben bzw. erstellen können, finden Sie am unteren Seitenrand.

3. **Rollenbasierte Zugriffssteuerung (Role Based Access Control, RBAC)**: Möglichkeit, die Administratorvorgänge aus den schreibgeschützten Vorgängen auf dem Cluster auf einen Satz von Zertifikaten zu beschränken.

4. **Dienstkonten und RunAs**: Service Fabric selbst wird als Windows-Dienstprozess (Fabric.exe) ausgeführt, und das Sicherheitskonto, unter dem der Prozess „Fabric.exe“ ausgeführt wird, ist konfigurierbar. Die Prozesskonten, unter denen „Fabric.exe“ auf den einzelnen Knoten im Cluster ausgeführt wird, können geschützt werden. Dies gilt auch für die Diensthostprozesse, die für die einzelnen Dienste aktiviert werden. Weitere Details finden Sie unter [Anwendungssicherheit und RunAs](service-fabric-application-runas-security.md).
  

## Sichern eines Service Fabric-Clusters mit Zertifikaten.

Für die Einrichtung eines sicheren Service Fabric-Clusters benötigen Sie mindestens ein Server- bzw. X509-Zertifikat. Sie laden das Zertifikat dann in den Azure-Schlüsseltresor hoch und verwenden es im Clustererstellungsprozess.

Der Vorgang umfasst drei Schritte:

1. Erwerben des X509-Zertifikats
2. Hochladen des Zertifikats in den Azure-Schlüsseltresor
3. Eingeben des Speicherorts und der Details zum Zertifikat im Vorgang zur Erstellung des Service Fabric-Clusters.

 
## Schritt 1: Erwerben des X509-Zertifikats

1. Für Cluster, die Produktionsworkloads ausführen, müssen Sie ein von der [Zertifizierungsstelle](https://en.wikipedia.org/wiki/Certificate_authority) signiertes X509-Zertifikat zur Sicherung des Clusters verwenden. Informationen dazu, wie Sie diese Zertifikate erhalten, finden Sie unter [http://msdn.microsoft.com/library/aa702761.aspx](http://msdn.microsoft.com/library/aa702761.aspx).
2. Für Cluster, die Sie nur für Testzwecke verwenden, können Sie ein selbstsigniertes Zertifikat verwenden. Die Vorgehensweise ist unter Schritt 2.5 beschrieben.


## Schritt 2: Hochladen des X509-Zertifikats in den Schlüsseltresor

Dies ist ein komplexer Vorgang. Wir haben daher ein PowerShell-Modul in ein Git-Repository hochgeladen, mit dem dieser Vorgang für Sie durchgeführt wird.

**Schritt 2.1**: Kopieren Sie diesen Ordner aus [diesem Git-Repository](https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers) auf Ihren Computer.

**Schritt 2.2**: Stellen Sie sicher, dass Azure SDK 1.0+ auf Ihrem Computer installiert ist.

**Schritt 2.3**: Öffnen Sie ein PowerShell-Fenster, und importieren Sie die Datei „ServiceFabricRPHelpers.psm“.

```
Remove-Module ServiceFabricRPHelpers
```

Kopieren Sie Folgendes, und ändern Sie den Pfad zur PSM1-Datei in den Pfad für Ihren Computer. Beispiel:

```
Import-Module "C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"
```
  

**Schritt 2.4**: Führen Sie diese Schritte aus, wenn Sie ein Zertifikat verwenden, das Sie bereits beschafft haben. Fahren Sie andernfalls mit Schritt 2.5 fort.


Melden Sie sich am Azure-Konto an.

```
Login-AzureRmAccount
```

Das Skript erstellt eine neue Ressourcengruppe bzw. einen Tresor, sofern diese Elemente noch nicht vorhanden sind.

```
Invoke-AddCertToKeyVault -SubscriptionId <you subscription id> -ResourceGroupName <string> -Location <region> -VaultName <Name of the Vault> -CertificateName <Name of the Certificate> -Password <Certificate password> -UseExistingCertificate -ExistingPfxFilePath <Full path to the .pfx file> 
```
Hier ist ein ausgefülltes Skript als Beispiel angegeben.

```
Invoke-AddCertToKeyVault -SubscriptionId 35389201-c0b3-405e-8a23-9f1450994307 -ResourceGroupName chackdankeyvault4doc -Location westus -VaultName chackdankeyvault4doc  -CertificateName chackdantestcertificate2 -Password abcd123 -UseExistingCertificate -ExistingPfxFilePath C:\MyCertificates\ChackdanTestCertificate.pfx 
```

Nach dem erfolgreichen Abschluss des Skripts erhalten Sie eine Ausgabe wie unten angegeben. Diese benötigen Sie für Schritt 3.

1. **Zertifikatfingerabdruck**: 2118C3BCE6541A54A0236E14ED2CCDD77EA4567A
2. **SourceVault** /Ressourcen-ID des Schlüsseltresors: /subscriptions/35389201-c0b3-405e-8a23-9f1450994307/resourceGroups/chackdankeyvault4doc/providers/Microsoft.KeyVault/vaults/chackdankeyvault4doc
3. **Zertifikat-URL** /URL zum Zertifikatspeicherort im Schlüsseltresor: https://chackdankeyvalut4doc.vault.azure.net:443/secrets/chackdantestcertificate3/ebc8df6300834326a95d05d90e0701ea 

Sie verfügen über die Informationen, die Sie zum Einrichten eines sicheren Clusters benötigen. Fahren Sie mit Schritt 3 fort.


**Schritt 2.5**: Dieser Schritt ist erforderlich, wenn Sie ein neues selbstsigniertes Zertifikat erstellen und in den Schlüsseltresor hochladen möchten.

Melden Sie sich am Azure-Konto an.

```
Login-AzureRmAccount
```

Das Skript erstellt eine neue Ressourcengruppe bzw. einen Tresor, sofern diese Elemente noch nicht vorhanden sind.

```
Invoke-AddCertToKeyVault -SubscriptionId <you subscription id> -ResourceGroupName <string> -Location <region> -VaultName <Name of the Vault> -CertificateName <Name of the Certificate> -Password <Certificate password> -CreateSelfSignedCertificate -DnsName <string- see note below.> -OutputPath <Full path to the .pfx file> 
```
Der Ausgabepfad (OutputPath), den Sie für das Skript angegeben haben, enthält das neue selbstsignierte Zertifikat, das wir in den Schlüsseltresor hochgeladen haben.


**Hinweis** Der DNS-Name <String> gibt einen oder mehrere DNS-Namen an, die in die Erweiterung des alternativen Antragstellernamens des Zertifikats abgelegt werden, wenn ein zu kopierendes Zertifikat nicht über den CloneCert-Parameter angegeben wird. Der erste DNS-Name wird auch als Antragstellername gespeichert. Wenn kein Signaturzertifikat angegeben ist, wird der erste DNS-Name auch als Ausstellername gespeichert.

Weitere Informationen zur allgemeinen Erstellung eines selbstsignierten Zertifikats finden Sie unter [https://technet.microsoft.com/library/hh848633.aspx](https://technet.microsoft.com/library/hh848633.aspx).

Hier ist ein ausgefülltes Skript als Beispiel angegeben. ```
Invoke-AddCertToKeyVault -SubscriptionId 35389201-c0b3-405e-8a23-9f1450994307 -ResourceGroupName chackdankeyvault4doc -Location westus -VaultName chackdankeyvault4doc  -CertificateName chackdantestcertificate3 -Password abcd123 -CreateSelfSignedCertificate -DnsName www.chackdan.westus.azure.com -OutputPath C:\MyCertificates
```

Da es sich um ein selbstsigniertes Zertifikat handelt, müssen Sie es in den Speicher „Vertrauenswürdige Personen“ Ihres Computers importieren, bevor Sie es zum Herstellen einer Verbindung mit einem sicheren Cluster verwenden können. ```
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath C:C:\MyCertificates\ChackdanTestCertificate.pfx -Password (Read-Host -AsSecureString -Prompt "Enter Certificate Password ")
``` ```
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath C:C:\MyCertificates\ChackdanTestCertificate.pfx -Password (Read-Host -AsSecureString -Prompt "Enter Certificate Password ")
```

Nach dem erfolgreichen Abschluss des Skripts erhalten Sie eine Ausgabe wie unten angegeben. Diese benötigen Sie für Schritt 3.

1. **Zertifikatfingerabdruck**: 64881409F4D86498C88EEC3697310C15F8F1540F
2. **SourceVault** /Ressourcen-ID des Schlüsseltresors: /subscriptions/35389201-c0b3-405e-8a23-9f1450994307/resourceGroups/chackdankeyvault4doc/providers/Microsoft.KeyVault/vaults/chackdankeyvault4doc
3. **Zertifikat-URL** /URL zum Zertifikatspeicherort im Schlüsseltresor: https://chackdankeyvalut4doc.vault.azure.net:443/secrets/chackdantestcertificate3/fvc8df6300834326a95d05d90e0720ea 

##Schritt 3: Einrichten eines sicheren Clusters 

Führen Sie die Schritte unter [Erstellung des Service Fabric-Clusters](service-fabric-cluster-creation-via-portal.md) aus, bis Sie zu den Sicherheitskonfigurationen gelangen. Im Folgenden wird beschrieben, wie Sie Sicherheitskonfigurationen einrichten.

Die zu verwendenden Zertifikate werden auf der Knotentyp-Ebene unter den Sicherheitskonfigurationen angegeben. Sie müssen diese für jeden Knotentyp in Ihrem Cluster angeben. In diesem Dokument wird die Vorgehensweise im Portal erläutert. Sie können jedoch auch eine ARM-Vorlage verwenden.

![SecurityConfigurations\_01][SecurityConfigurations_01]

Erforderliche Parameter

- **Sicherheitsmodus** Wählen Sie unbedingt „X509-Zertifikat“. Hiermit wird Service Fabric signalisiert, das Sie einen sicheren Cluster einrichten möchten. 
- **Clusterschutzebene** In den [Grundlagen der Schutzebene](https://msdn.microsoft.com/library/aa347692.aspx) erfahren Sie, was diese Werte bedeuten. Wir lassen drei Werte zu: EncryptAndSign, Sign, None. Am besten behalten Sie den Standardwert „EncryptAndSign“ bei, sofern Sie nicht mit der Thematik vertraut sind.
- **Quelltresor** bezieht sich auf die Ressourcen-ID des Schlüsseltresors und muss folgendes Format aufweisen: ```
/subscriptions/<Sub ID>/resourceGroups/<Resource group name>/providers/Microsoft.KeyVault/vaults/<vault name>
```

- **Zertifikat-URL** bezieht sich auf die Speicherort-URL in Ihrem Schlüsseltresor, in dem das Zertifikat hochgeladen wurde, und muss folgendes Format aufweisen: ```
https://<name of the vault>.vault.azure.net:443/secrets/<exact location>
https://chackdan-kmstest-eastus.vault.azure.net:443/secrets/MyCert/6b5cc15a753644e6835cb3g3486b3812
```
- **Zertifikatfingerabdruck** bezieht sich auf den Fingerabdruck des Zertifikats und ist unter der zuvor festgelegten URL zu finden.

Optionale Parameter – Sie können optional weitere Zertifikate angeben, die Ihr Clientcomputer zum Ausführen weiterer Vorgänge im Cluster verwendet. Standardmäßig wird der Fingerabdruck, den Sie in den erforderlichen Parametern angegeben haben, der autorisierten Liste der Fingerabdrücke hinzugefügt, die in Clientvorgängen zugelassen werden.

Admin-Client – Mit diesen Informationen wird überprüft, ob der Client für die Verbindung zum Endpunkt der Clusterverwaltung die richtigen Anmeldedaten zum Ausführen von Administratorvorgängen sowie schreibgeschützten Vorgängen auf dem Cluster verwendet. Sie können weitere Zertifikate angeben, wenn Sie für Administratorvorgänge autorisiert werden möchten.


- **Autorisieren mit** – Gibt für Service Fabric an, ob dieses Zertifikat mit dem Antragstellernamen oder mit dem Fingerabdruck gesucht werden soll. Die Verwendung des Antragstellernamens für die Autorisierung ist aus Sicherheitsgründen nicht empfehlenswert, lässt jedoch mehr Flexibilität zu.


- **Antragstellername** ist nur erforderlich, wenn Sie angegeben haben, dass die Autorisierung mit dem Antragstellernamen erfolgen soll.
- **Aussteller-Fingerabdruck** stellt eine zusätzliche Sicherheitsstufe dar, die der Server ausführen kann, wenn ein Client seine Anmeldeinformationen an den Server übermittelt.

Schreibgeschützter Client – Mit diesen Informationen wird überprüft, ob der Client für die Verbindung zum Endpunkt der Clusterverwaltung die richtigen Anmeldedaten zum Ausführen von Administratorvorgängen sowie schreibgeschützten Vorgängen auf dem Cluster verwendet. Sie können weitere Zertifikate angeben, wenn Sie für schreibgeschützte Vorgänge autorisiert werden möchten.


- **Autorisieren mit** – Gibt für Service Fabric an, ob dieses Zertifikat mit dem Antragstellernamen oder mit dem Fingerabdruck gesucht werden soll. Die Verwendung des Antragstellernamens für die Autorisierung ist aus Sicherheitsgründen nicht empfehlenswert, lässt jedoch mehr Flexibilität zu.

- **Antragstellername** ist nur erforderlich, wenn Sie angegeben haben, dass die Autorisierung mit dem Antragstellernamen erfolgen soll.
- **Aussteller-Fingerabdruck** stellt eine zusätzliche Sicherheitsstufe dar, die der Server ausführen kann, wenn ein Client seine Anmeldeinformationen an den Server übermittelt.


## Aktualisieren der Zertifikate im Cluster

Mit Service Fabric können Sie primäre und sekundäre Zertifikate angeben. Das bei der Erstellung festgelegte Zertifikat wird standardmäßig zum primären Zertifikat erklärt. Wenn Sie ein weiteres Zertifikat hinzufügen möchten, müssen Sie das Zertifikat den virtuellen Computern im Cluster bereitstellen. Oben in Schritt 2 ist beschrieben, wie Sie ein neues Zertifikat in den Schlüsseltresor hochladen können. Hierfür können Sie wie beim ersten Zertifikat denselben Schlüsseltresor verwenden.

Informationen hierzu finden Sie im Dokument zum [Bereitstellen von Zertifikaten auf virtuellen Computern über einen von Kunden verwalteten Schlüsseltresor](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx).

Sobald dieser Vorgang erfolgreich abgeschlossen ist, signalisieren Sie Service Fabric über das Portal oder ARM, dass Sie über ein sekundäres Zertifikat verfügen, das ebenfalls verwendet werden kann. Sie benötigen lediglich einen Fingerabdruck.

Dies ist der Prozess: Suchen Sie im Portal nach der gewünschten Clusterressource, die Sie diesem Zertifikat hinzufügen möchten. Klicken auf die Zertifikateinstellung, geben Sie den sekundären Zertifikatfingerabdruck ein, und speichern Sie. Eine Bereitstellung wird gestartet und bei erfolgreichem Abschluss dieser Bereitstellung können Sie sowohl die primären als auch die sekundären Zertifikatverwaltungsvorgänge im Cluster ausführen.

![SecurityConfigurations\_02][SecurityConfigurations_02]

Bei Bedarf können Sie nun auch eines der Zertifikate entfernen. Speichern Sie nach dem Entfernen unbedingt, damit eine neue Bereitstellung gestartet wird. Nach Abschluss dieser Bereitstellung kann das entfernte Zertifikat keine Verbindung mehr zum Cluster herstellen. Für einen sicheren Cluster muss stets mindestens ein gültiges (nicht widerrufenes oder abgelaufenes) Zertifikat bereitgestellt werden. Andernfalls können Sie nicht auf den Cluster zugreifen.

Wenn eines der Zertifikate bald abläuft, werden Sie über ein diagnostisches Ereignis informiert.



## Was sind X509-Zertifikate?

Digitale X509-Zertifikate werden in der Regel verwendet, um Clients und Server zu authentifizieren, zu verschlüsseln und Nachrichten digital zu signieren. Weitere Informationen zu diesen Zertifikaten finden Sie unter [http://msdn.microsoft.com/library/ms731899.aspx](http://msdn.microsoft.com/library/ms731899.aspx).

**Hinweis**

1. Zertifikate, die in Clustern mit Produktionsworkloads verwendet werden, müssen entweder mit einem korrekt konfigurierten Windows Server-Zertifikatdienst oder über eine genehmigte [Zertifizierungsstelle](https://en.wikipedia.org/wiki/Certificate_authority) erstellt werden.
2. Verwenden Sie niemals temporäre oder Testzertifikate, die mit Tools wie MakeCert.exe erstellt wurden.
3. Für Cluster, die Sie nur für Testzwecke verwenden, können Sie ein selbstsigniertes Zertifikat verwenden. 


## Was sind Server- und Clientzertifikate?

**Server-/X509-Zertifikate**

Serverzertifikate müssen primär den Server (Knoten) für Clients oder Server (Knoten) für Server (Knoten) authentifizieren. Wenn ein Client oder Knoten einen Knoten authentifiziert, wird zuerst der Wert des allgemeinen Namens im Antragstellerfeld verglichen, um sicherzustellen, dass er in der konfigurierten Liste der allgemeinen Namen vertreten ist. Dieser allgemeine Name oder einer der alternativen Antragstellernamen des Zertifikats muss in der Liste der zulässigen allgemeinen Namen vorhanden sein.

Im folgenden Artikel erfahren Sie, wie Zertifikate mit alternativen Antragstellernamen (SAN) generiert werden: [http://support.microsoft.com/kb/931351](http://support.microsoft.com/kb/931351).
 
**Hinweis:** Im Antragstellerfeld können mehrere Werte eingegeben werden. Jeder davon ist mit einer Initialisierung versehen, die den Wert als Präfix angibt. In den meisten Fällen verwendet die Initialisierung „CN“ für den allgemeinen Namen, z. B. „CN = www.contoso.com“. Das Feld für den Antragsteller kann auch leer gelassen werden. Beachten Sie auch das optionale Feld für den alternativen Antragstellernamen. Wird es ausgefüllt, muss es sowohl den allgemeinen Namen des Zertifikats sowie einen Eintrag pro alternativen Antragstellernamen enthalten. Diese werden als Werte für DNS-Namen eingegeben.

Beachten Sie außerdem, dass der Wert des Felds „Beabsichtigte Zwecke“ des Zertifikats einen entsprechenden Wert enthalten muss, z. B. „Serverauthentifizierung“ oder „Clientauthentifizierung“.

**Clientzertifikate**

Clientzertifikate werden in der Regel nicht von einer Drittanbieter-Zertifizierungsstelle ausgestellt. Stattdessen enthält der persönliche Speicher des aktuellen Benutzerspeicherorts in der Regel Zertifikate, die von einer Stammzertifizierungsstelle mit dem beabsichtigten Zweck „Clientauthentifizierung“ platziert werden. Der Client kann ein solches Zertifikat verwenden, wenn eine gegenseitige Authentifizierung erforderlich ist. Sämtliche Verwaltungsvorgänge für Service Fabric-Cluster erfordern Serverzertifikate. Clientzertifikate sollten nicht verwendet werden.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte
- [Service Fabric-Cluster-Upgradeprozess und Erwartungen](service-fabric-cluster-upgrade.md)
- [Verwalten von Service Fabric-Anwendungen in Visual Studio](service-fabric-manage-application-in-visual-studio.md)
- [Einführung in das Service Fabric-Integritätsmodell](service-fabric-health-introduction.md)

<!--Image references-->
[SecurityConfigurations_01]: ./media/service-fabric-cluster-security/SecurityConfigurations_01.png
[SecurityConfigurations_02]: ./media/service-fabric-cluster-security/SecurityConfigurations_02.png
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

<!---HONumber=AcomDC_1217_2015-->