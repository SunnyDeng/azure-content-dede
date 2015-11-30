<properties
   pageTitle="Sichern eines Service Fabric-Clusters| Microsoft Azure"
   description="Sichern eines Service Fabric-Clusters Welche Optionen stehen zur Verfügung?"
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

# Sichern eines Service Fabric-Clusters mit Zertifikaten.

Für die Einrichtung eines sicheren Service Fabric-Clusters benötigen Sie mindestens ein Server- bzw. X509-Zertifikat. Dieses laden Sie dann in den Azure-Schlüsseltresor hoch und verwenden es wie in den Informationen zur [Erstellung des Service Fabric-Clusters ](service-fabric-cluster-creation-via-portal.md) beschrieben.

Der Vorgang umfasst drei Schritte:

1. Erwerben des X509-Zertifikats
2. Hochladen des Zertifikats in den Azure-Schlüsseltresor
3. Eingeben des Speicherorts und der Details zum Zertifikat im Vorgang zur Erstellung des Service Fabric-Clusters.

Bevor wir zu sehr ins Detail gehen, besprechen wir die Grundlagen der Szenarien.

##  Welche Szenarios werden behandelt?

Service Fabric bietet Sicherheit für die folgenden Szenarien:

1. Sichern eines Clusters für die Kommunikation zwischen den Knoten
2. Sichern eines Service Fabric-Clients, der mit einem bestimmten Knoten im Cluster kommuniziert
3. Rollenbasierte Zugriffssteuerung (Rolle Based Access Control, RBAC) – Möglichkeit, die Administratorvorgänge aus den schreibgeschützten Vorgängen auf dem Cluster auf einen Satz von Zertifikaten zu beschränken   

Service Fabric verwendet X509-Serverzertifikate, die Sie als Teil der Knotentyp-Konfigurationen angeben, wenn Sie einen Cluster erstellen. Eine kurze Übersicht, was diese Zertifikate sind und wie Sie sie erwerben bzw. erstellen können, finden Sie am unteren Seitenrand.

 
## Erwerben des X509-Zertifikats

1. Für Cluster, die Produktionsworkloads ausführen, müssen Sie ein von der [Zertifizierungsstelle](https://en.wikipedia.org/wiki/Certificate_authority) signiertes X509-Zertifikat zur Sicherung des Clusters verwenden. Informationen dazu, wie diese Zertifikate erhalten, finden Sie unter [http://msdn.microsoft.com/library/aa702761.aspx](http://msdn.microsoft.com/library/aa702761.aspx).
2. Für Cluster, die Sie nur für Testzwecke verwenden, können Sie ein selbstsigniertes Zertifikat verwenden.


## Erstellen eines selbstsignierten Zertifikats für Testzwecke

Informationen zum Erstellen eines selbstsignierten Zertifikats finden Sie unter [https://technet.microsoft.com/library/hh848633.aspx](https://technet.microsoft.com/library/hh848633.aspx)
    
Diesen PS verwende ich zum Erstellen meiner Testzertifikate. Lesen Sie jedoch unbedingt das oben genannte Dokument, um sicherzustellen, dass es Ihren Anforderungen entspricht. ```
$password = Read-Host -AsSecureString 
``` ```
New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName ChackdanTestCertificate | Export-PfxCertificate -FilePath E:\MyCertificates\ChackdanTestCertificate.pfx -Password $password
```

**Hinweis** Der DNS-Name <String> gibt einen oder mehrere DNS-Namen an, die in die Erweiterung des alternativen Antragstellernamens des Zertifikats abgelegt werden, wenn ein zu kopierendes Zertifikat nicht über den CloneCert-Parameter angegeben wird. Der erste DNS-Name wird auch als Antragstellername gespeichert. Wenn kein Signaturzertifikat angegeben ist, wird der erste DNS-Name auch als Ausstellername gespeichert.

## Hochladen des X509-Zertifikats in den Schlüsseltresor

Informationen zum Hochladen eines Zertifikats in den Schlüsseltresor finden Sie in der [Dokumentation zum Schlüsseltresor](https://azure.microsoft.com/documentation/articles/key-vault-get-started/).

Notieren Sie die URL des Quelltresors, die Zertifikat-URL und den Zertifikatfingerabdruck, da Sie diese zum Einrichten des sicheren Secure Fabric-Clusters benötigen. Die benötigen Daten sehen wie folgt aus:



1. **Resourcen-ID der URL zum Schlüsseltresor/Quelltresor** : /subscriptions/6c653126-e4ba-42cd-a1dd-f7bf96af7a47/resourceGroups/chackdan-keyvault/providers/Microsoft.KeyVault/vaults/chackdan-kmstest
2. **URL zum Speicherort des Zertifikats im Schlüsseltresor** : https://chackdan-kmstest.vault.azure.net:443/secrets/MyCert/dcf17bdbb86b42ad864e8e827c268431 
3. **Zertifikatfingerabdruck** : 2118C3BCE6541A54A0236E14ED2CCDD77EA4567A



##Einrichten eines sicheren Clusters 

Die zu verwendenden Zertifikate werden auf der Knotentyp-Ebene unter den Sicherheitskonfigurationen angegeben. Sie müssen diese für jeden Knotentyp in Ihrem Cluster angeben. In diesem Dokument wird die Vorgehensweise im Portal erläutert. Sie können jedoch auch eine ARM-Vorlage verwenden.

![SecurityConfigurations\_01][SecurityConfigurations_01]

Erforderliche Parameter

- **Sicherheitsmodus** Wählen Sie unbedingt „X509-Zertifikat“. Dieses signalisiert Service Fabric, das Sie einen sicheren Cluster einrichten möchten. 
- **Clusterschutzebene** In den [Grundlagen der Schutzebene](https://msdn.microsoft.com/library/aa347692.aspx) erfahren Sie, was diese Werte bedeuten. Wir lassen drei Werte zu: EncryptAndSign, Sign, None. Am besten behalten Sie den Standardwert „EncryptAndSign“ bei, insofern Sie nicht mit der Thematik vertraut sind.
- **Quelltresor** bezieht sich auf die Ressourcen-ID des Schlüsseltresor und muss folgendes Format aufweisen: ```
/subscriptions/<Sub ID>/resourceGroups/<Resource group name>/providers/Microsoft.KeyVault/vaults/<vault name>
```

- **Zertifikat-URL** bezieht sich auf die Speicherort-URL in Ihrem Schlüsseltresor, in dem das Zertifikat hochgeladen wurde und muss folgendes Format aufweisen: ```
https://<name of the vault>.vault.azure.net:443/secrets/<exact location>
https://chackdan-kmstest-eastus.vault.azure.net:443/secrets/MyCert/6b5cc15a753644e6835cb3g3486b3812
```
- **Zertifikatfingerabdruck** bezieht sich auf den Fingerabdruck des Zertifikats und ist unter der zuvor festgelegten URL zu finden.

Optionale Parameter – Sie können optional weitere Zertifikate angeben, die Ihr Clientcomputer zum Ausführen weiterer Vorgänge im Cluster verwendet. Standardmäßig wird der Fingerabdruck, den Sie in den erforderlichen Parametern angegeben haben, der autorisierten Liste der Fingerabdrücke hinzugefügt, die in Clientvorgängen zugelassen werden.

Admin-Client – Mit diesen Informationen wird überprüft, ob der Client für die Verbindung zum Endpunkt der Clusterverwaltung die richtigen Anmeldedaten zum Ausführen von Administratorvorgängen sowie schreibgeschützten Vorgängen auf dem Cluster verwendet. Sie können weitere Zertifikate angeben, wenn Sie für Administratorvorgänge autorisiert werden möchten.


- **Autorisieren mit** – Gibt Service Fabric an, ob dieses Zertifikat mit dem Antragstellernamen oder mit dem Fingerabdruck gesucht werden soll. Die Verwendung des Antragstellernamens für die Autorisierung ist aus Sicherheitsgründen nicht empfehlenswert, lässt jedoch mehr Flexibilität zu.


- **Antragstellername** ist nur erforderlich, wenn Sie angegeben haben, dass die Autorisierung mit dem Antragstellernamen erfolgen soll
- **Aussteller-Fingerabdruck** stellt eine zusätzliche Sicherheitsstufe dar, die der Server ausführen kann, wenn ein Client seine Anmeldeinformationen an den Server übermittelt.

Schreibgeschützter Client – Mit diesen Informationen wird überprüft, ob der Client für die Verbindung zum Endpunkt der Clusterverwaltung die richtigen Anmeldedaten zum Ausführen von Administratorvorgängen sowie schreibgeschützten Vorgängen auf dem Cluster verwendet. Sie können weitere Zertifikate angeben, wenn Sie für schreibgeschützte Vorgänge autorisiert werden möchten.


- **Autorisieren mit** – Gibt Service Fabric an, ob dieses Zertifikat mit dem Antragstellernamen oder mit dem Fingerabdruck gesucht werden soll. Die Verwendung des Antragstellernamens für die Autorisierung ist aus Sicherheitsgründen nicht empfehlenswert, lässt jedoch mehr Flexibilität zu.

- **Antragstellername** ist nur erforderlich, wenn Sie angegeben haben, dass die Autorisierung mit dem Antragstellernamen erfolgen soll
- **Aussteller-Fingerabdruck** stellt eine zusätzliche Sicherheitsstufe dar, die der Server ausführen kann, wenn ein Client seine Anmeldeinformationen an den Server übermittelt.


## Aktualisieren der Zertifikate im Cluster

Mit Service Fabric können Sie primäre und sekundäre Zertifikate angeben. Das bei der Erstellung festgelegte Zertifikat wird standardmäßig zum primären Zertifikat erklärt. Wenn Sie ein weiteres Zertifikat hinzufügen möchten, müssen Sie das Zertifikat den virtuellen Computern im Cluster bereitstellen. Informationen dazu finden Sie im Dokument zum [Bereitstellen von Zertifikaten auf virtuellen Computern über einen von Kunden verwalteten Schlüsseltresor](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx).

Sobald dieser Vorgang erfolgreich abgeschlossen ist, signalisieren Sie Service Fabric über das Portal oder ARM, dass Sie über ein sekundäres Zertifikat verfügen, das ebenfalls verwendet werden kann. Sie benötigen lediglich einen Fingerabdruck.

Suchen Sie im Portal nach der gewünschten Clusterressource, die Sie diesem Zertifikat hinzufügen möchten, klicken auf die Zertifikateinstellung, geben Sie den sekundären Zertifikatfingerabdruck ein und speichern Sie. Eine Bereitstellung wird gestartet und bei erfolgreichem Abschluss dieser Bereitstellung können Sie sowohl die primären als auch die sekundären Zertifikatverwaltungsvorgänge im Cluster ausführen.

![SecurityConfigurations\_02][SecurityConfigurations_02]

Bei Bedarf können Sie nun auch eines der Zertifikate entfernen. Speichern Sie nach dem Entfernen unbedingt, damit eine neue Bereitstellung gestartet wird. Nach Abschluss dieser Bereitstellung kann das entfernte Zertifikat keine Verbindung mehr zum Cluster herstellen. Für einen sicheren Cluster muss stets mindestens ein gültiges (nicht widerrufenes oder abgelaufenes) Zertifikat bereitgestellt werden. Andernfalls können Sie nicht auf den Cluster zugreifen.

Wenn eines der Zertifikate bald abläuft, werden Sie über ein diagnostisches Ereignis informiert.



## Was sind X509-Zertifikate?

Digitale X509-Zertifikate werden in der Regel verwendet, um Clients und Server zu authentifizieren, zu verschlüsseln und Nachrichten digital zu signieren. Weitere Informationen zu diesen Zertifikaten finden Sie unter [http://msdn.microsoft.com/library/ms731899.aspx](http://msdn.microsoft.com/library/ms731899.aspx).

**Hinweis**

1. In Clustern mit Produktionsarbeitslasten verwendete Zertifikate müssen entweder mit einem ordnungsgemäß konfigurierten Windows Server-Zertifikatdienst oder über eine genehmigte [Zertifizierungsstelle](https://en.wikipedia.org/wiki/Certificate_authority). erstellt werden.
2. Verwenden Sie niemals temporäre oder Testzertifikate, die mit Tools wie MakeCert.exe erstellt wurden.
3. Für Cluster, die Sie nur für Testzwecke verwenden, können Sie ein selbstsigniertes Zertifikat verwenden. 


##Was sind Server- und Clientzertifikate?

**Server-/X509-Zertifikate**

Serverzertifikate müssen primär den Server (Knoten) für Clients oder Server (Knoten) für Server (Knoten) authentifizieren. Wenn ein Client oder Knoten einen Knoten authentifiziert, wird zuerst der Wert des allgemeinen Namens im Antragstellerfeld verglichen, um sicherzustellen, dass er in der konfigurierten Liste der allgemeinen Namen vertreten ist. Dieser allgemeine Name oder einer der alternativen Antragstellernamen des Zertifikats muss in der Liste der zulässigen allgemeinen Namen vorhanden sein.

Im folgenden Artikel erfahren Sie, wie Zertifikate mit alternativen Antragstellernamen (SAN) generiert werden. [http://support.microsoft.com/kb/931351](http://support.microsoft.com/kb/931351)
 
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

<!---HONumber=Nov15_HO4-->