<properties
   pageTitle="Azure-Datenträgerverschlüsselung für virtuelle Windows- und Linux-IaaS-Computer | Microsoft Azure"
   description="Der Artikel enthält eine Übersicht über die Microsoft Azure-Datenträgerverschlüsselung für virtuelle Windows- und Linux-IaaS-Computer."
   services="virtual-machines, cloud-services, storage"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor="TomSh"/>

<tags
   ms.service="azure-security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/29/2016"
   ms.author="devtiw"/>


#Azure-Datenträgerverschlüsselung für virtuelle Windows- und Linux-IaaS-Computer (Vorschau)

> [AZURE.NOTE] Die Informationen in diesem Dokument gelten für die Vorschauversion von Azure-Datenträgerverschlüsselung.

Bei Microsoft Azure wird sehr darauf geachtet, den Schutz Ihrer Daten und die Datenhoheit sicherzustellen. Außerdem können Sie für Ihre unter Azure gehosteten Daten eine Reihe von modernen Techniken zum Verschlüsseln, Steuern und Verwalten von Verschlüsselungsschlüsseln und Steuern und Überprüfen des Datenzugriffs nutzen. So können Azure-Kunden flexibel eine Lösung auswählen, die Ihre Anforderungen am besten erfüllt. In diesem Artikel stellen wir Ihnen die neue Technologie „Azure-Datenträgerverschlüsselung für virtuelle Windows- und Linux-IaaS-Computer“ vor, die zum Schützen und Absichern Ihrer Daten dient, um Vorgaben in den Bereichen Unternehmenssicherheit und Compliance zu erfüllen. Der Artikel enthält eine ausführliche Anleitung zur Verwendung der Funktionen einer Azure-Datenträgerverschlüsselung, z. B. die unterstützten Szenarien und die Benutzeroberflächen.

**HINWEIS**: Einige Empfehlungen in diesem Artikel können unter Umständen zu einer erhöhten Daten-, Netzwerk- oder Serverressourcenauslastung führen, was mit zusätzlichen Lizenz- oder Abonnementkosten verbunden sein kann.

## Übersicht

Azure-Datenträgerverschlüsselung ist eine neue Funktion, mit der Sie die Datenträger von virtuellen Windows- und Linux-IaaS-Computern verschlüsseln können. Bei der Azure-Datenträgerverschlüsselung werden das Branchen-Standardfeature [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) von Windows und das Feature [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) von Linux genutzt, um Volumeverschlüsselung für das Betriebssystem und die Datenträger bereitzustellen. Die Lösung ist in den [Azure-Schlüsseltresor](https://azure.microsoft.com/documentation/services/key-vault/) integriert, damit Sie die Schlüssel für die Datenträgerverschlüsselung und die geheimen Schlüssel unter Ihrem Schlüsseltresor-Abonnement steuern und verwalten können. Gleichzeitig können Sie sicherstellen, dass alle Daten auf den Datenträgern von virtuellen Computern im Ruhezustand in Ihrem Azure-Speicher verschlüsselt sind.

### Verschlüsselungsszenarien

Für die Azure-Datenträgerverschlüsselung werden die folgenden drei Kundenverschlüsselungsszenarien unterstützt:

- Aktivieren der Verschlüsselung auf neuen virtuellen IaaS-Computern, die mit einer vom Kunden verschlüsselten VHD und Verschlüsselungsschlüsseln erstellt werden

- Aktivieren der Verschlüsselung auf neuen virtuellen IaaS-VM-Computern, die über den Azure-Katalog erstellt werden

- Aktivieren der Verschlüsselung auf vorhandenen virtuellen IaaS-Computern, die bereits unter Azure ausgeführt werden

Die Lösung unterstützt bei Aktivierung in Microsoft Azure für virtuelle IaaS-Computer (öffentliche Vorschauversion) Folgendes:

- Integration in Azure-Schlüsseltresor

- Standardmäßige [virtuelle IaaS-Computer der A-, D- und G-Serie](https://azure.microsoft.com/pricing/details/virtual-machines/)

- Aktivieren der Verschlüsselung für virtuelle IaaS-Computer, die mit dem [Azure-Ressourcen-Manager](resource-group-overview.md)-Modell erstellt werden

- Alle öffentlichen Azure-[Regionen](https://azure.microsoft.com/regions/)


Für die Lösung werden folgende Szenarien, Features und Technologien im Rahmen der öffentlichen Vorschauversion nicht unterstützt:

- Einfache virtuelle Computer und standardmäßige virtuelle IaaS-Computer der DS-Serie (Storage Premium)

- Virtuelle IaaS-Computer, die mit der herkömmlichen Methode für virtuelle Computer erstellt werden

- Deaktivierung der Verschlüsselung auf dem virtuellen IaaS-Computer, die über die Azure-Datenträgerverschlüsselung aktiviert wurde
 
- Integration in den lokalen Schlüsselverwaltungsdienst

- Windows Server Technical Preview 3

- Red Hat Enterprise Linux

- Azure Files (Azure-Dateifreigabe), Netzwerkdateisystem (NFS), Dynamische Volumes, Softwarebasierte RAID-Systeme


### Verschlüsselungsfunktionen

Beim Aktivieren und Bereitstellen der Azure-Datenträgerverschlüsselung für virtuelle Azure-IaaS-Computer werden je nach bereitgestellter Konfiguration die folgenden Funktionen aktiviert:

- Verschlüsselung des Betriebssystemvolumes zum Schützen des Startvolumes im Ruhezustand im Kundenspeicher

- Verschlüsselung von Datenvolumes zum Schützen der Datenvolumes im Ruhezustand im Kundenspeicher

- Schutz der Verschlüsselungsschlüssel und geheimen Schlüssel unter dem Azure-Schlüsseltresor-Abonnement des Kunden

- Meldung des Verschlüsselungsstatus des verschlüsselten virtuellen IaaS-Computers

- Entfernen der Konfigurationseinstellungen für die Datenträgerverschlüsselung vom virtuellen IaaS-Computer

Die Azure-Datenträgerverschlüsselung für virtuelle IaaS-Computer für Windows und Linux umfasst die Datenträgerverschlüsselungserweiterung für Windows, Datenträgerverschlüsselungserweiterung für Linux, Datenträgerverschlüsselungs-PowerShell-Cmdlets, Datenträgerverschlüsselungs-CLI-Cmdlets und Azure-Ressourcen-Manager-Vorlagen für die Datenträgerverschlüsselung. Die Lösung für die Azure-Datenträgerverschlüsselung wird auf virtuellen IaaS-Computern unter dem Windows- oder Linux-Betriebssystem unterstützt. Weitere Informationen zu den unterstützten Betriebssystemen finden Sie unten im Abschnitt „Voraussetzungen“.

Für die Verschlüsselung der Datenträger von virtuellen Computern per Azure-Datenträgerverschlüsselung fallen während der öffentlichen Vorschauphase keine Gebühren an. Wir erwarten, dass dies auch dann weiterhin der Fall sein wird, wenn die Datenträgerverschlüsselung allgemein verfügbar ist. Die Preise können aber je nach Markt- und Wettbewerbssituation Änderungen unterliegen.

### Wertbeitrag

Mit der Verwaltungslösung für die Azure-Datenträgerverschlüsselung werden in der Cloud die folgenden geschäftlichen Anforderungen erfüllt:

-   Virtuelle IaaS-Computer werden im Ruhezustand geschützt, indem Verschlüsselungstechnologie nach Branchenstandard eingesetzt wird, um die Anforderungen an Unternehmenssicherheit und Compliance zu erfüllen.

-   Virtuelle IaaS-Computer werden mit vom Kunden gesteuerten Schlüsseln und Richtlinien gestartet, und die Nutzung kann im Schlüsseltresor überprüft werden.


### Verschlüsselungsworkflow 
Im Allgemeinen sind folgende Schritte erforderlich, um die Datenträgerverschlüsselung für virtuelle Windows- und Linux-Computer zu aktivieren:

1. Kunde wählt eines der drei oben angegebenen Verschlüsselungsszenarios.

2. Kunde nutzt die Aktivierung der Datenträgerverschlüsselung über die Azure-Datenträgerverschlüsselung per ARM-Vorlage, PS-Cmdlets oder CLI-Befehl und gibt die Verschlüsselungskonfiguration an.

    - Für das vom Kunden verschlüsselte VHD-Szenario lädt der Kunde die verschlüsselte VHD in sein Speicherkonto und das Verschlüsselungsschlüsselmaterial in seinen Schlüsseltresor und stellt die Verschlüsselungskonfiguration bereit, um die Verschlüsselung für einen neuen virtuellen IaaS-Computer zu aktivieren.

    - Für die neuen virtuellen Computer, die über den Azure-Katalog erstellt werden, und vorhandene virtuelle Computer, die in Azure bereits ausgeführt werden, gibt der Kunde die Verschlüsselungskonfiguration an, um die Verschlüsselung auf dem virtuellen IaaS-Computer zu aktivieren.

3. Der Kunde gewährt Zugriff auf die Azure-Plattform, um das Verschlüsselungsschlüsselmaterial (BitLocker-Verschlüsselungsschlüssel für Windows-Systeme und Passphrase für Linux) aus dem Schlüsseltresor auszulesen und so die Verschlüsselung auf dem virtuellen IaaS-Computer zu aktivieren.

4. Der Kunde stellt eine Azure AD-Identität bereit, um das Verschlüsselungsschlüsselmaterial in den Schlüsseltresor zu schreiben und so die Verschlüsselung auf dem virtuellen IaaS-Computer für die obigen Szenarien 2 und 3 zu aktivieren.

5.  Über die Azure-Dienstverwaltung wird das VM-Dienstmodell mit der Konfiguration für Verschlüsselung und Schlüsseltresor aktualisiert und der verschlüsselte virtuelle Computer für den Kunden bereitgestellt.

![Microsoft-Antischadsoftware in Azure](./media/azure-security-disk-encryption/disk-encryption-fig1.JPG)

## Voraussetzungen

Unten sind die Voraussetzungen für die Aktivierung der Azure-Datenträgerverschlüsselung auf virtuellen Azure-IaaS-Computern für die unterstützten Szenarien aufgeführt, die im Übersichtsabschnitt genannt werden.

- Benutzer müssen über ein gültiges aktives Azure-Abonnement verfügen, um in den unterstützten Regionen Ressourcen in Azure zu erstellen.

- Die Azure-Datenträgerverschlüsselung wird für die folgenden Windows Server-SKUs unterstützt: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2. Die Lösung wird nicht unter dem Betriebssystem Windows Server 2008 unterstützt. Windows Server Technical Preview wird für die öffentliche Vorschauversion nicht unterstützt.

**Hinweis**: Für Windows Server 2008 R2, MUSS .NET Framework 4.5 installiert werden, bevor die Verschlüsselung in Azure aktiviert wird. Sie können die Installation über Windows Update durchführen, indem Sie das optionale Update „Microsoft .NET Framework 4.5.2 für Windows Server 2008 R2 x64-basierte Systeme ([KB2901983](https://support.microsoft.com/kb/2901983))“ installieren.

- Die Azure-Datenträgerverschlüsselung wird für die folgenden Linux-Server-SKUs unterstützt: Ubuntu, CentOS, SUSE und SUSE Linux Enterprise Server (SLES). Red Hat Enterprise Linux wird für die öffentliche Vorschauversion nicht unterstützt.

- Alle Ressourcen (z. B. Schlüsseltresor, Speicherkonto, VM usw.) müssen derselben Azure-Region und demselben Abonnement angehören.

**Hinweis**: Die Azure-Datenträgerverschlüsselung erfordert, dass sich der Schlüsseltresor und die virtuellen Computer in derselben Azure-Region befinden. Sind sie in unterschiedlichen Regionen konfiguriert, kann die Azure-Datenträgerverschlüsselung nicht aktiviert werden.

- Informationen zum Einrichten und Konfigurieren des Azure-Schlüsseltresors für die Verwendung der Azure-Datenträgerverschlüsselung finden Sie im Abschnitt **Festlegen und Konfigurieren des Azure-Schlüsseltresors für die Azure-Datenträgerverschlüsselung** unter *Voraussetzungen* in diesem Artikel.

- Informationen zum Einrichten und Konfigurieren der Azure AD-Anwendung in Azure Active Directory für die Verwendung der Azure-Datenträgerverschlüsselung finden Sie im Abschnitt **Einrichten der Azure AD-Anwendung in Azure Active Directory** unter *Voraussetzungen* in diesem Artikel.

- Informationen zum Einrichten und Konfigurieren der Richtlinie für den Zugriff auf den Schlüsseltresor für die Azure AD-Anwendung finden Sie im Abschnitt **Festlegen der Richtlinie für den Zugriff auf den Schlüsseltresor für die Azure AD-Anwendung** unter *Voraussetzungen* in diesem Artikel.

- Informationen, wie eine vorverschlüsselte Windows-VHD vorbereitet wird, finden Sie im Abschnitt **Vorbereiten einer vorverschlüsselten Windows-VHD** im Anhang dieses Artikels.

- Informationen, wie eine vorverschlüsselte Linux-VHD vorbereitet wird, finden Sie im Abschnitt **Vorbereiten einer vorverschlüsselten Linux-VHD** im Anhang dieses Artikels.

- Die Azure-Plattform benötigt Zugriff auf die Verschlüsselungsschlüssel oder geheimen Schlüssel im Azure-Schlüsseltresor des Kunden, um sie für den virtuellen Computer zur Verfügung zu stellen. Damit kann das Betriebssystemvolume des virtuellen Computers gestartet und entschlüsselt werden. Um der Azure-Plattform Berechtigungen für den Zugriff auf den Schlüsseltresor zu gewähren, muss die **enabledForDiskEncryption**-Eigenschaft für diese Anforderung auf den Schlüsseltresor festgelegt werden. Weitere Informationen finden Sie im Abschnitt **Festlegen und Konfigurieren des Azure-Schlüsseltresors für die Azure-Datenträgerverschlüsselung** im Anhang dieses Artikels.

- Die URLs für den geheimen Schlüssel des Schlüsseltresors und den Schlüsselverschlüsselungsschlüssel (Key Encryption Key, KEK) müssen mit einer Versionsangabe versehen sein. Diese Versionsangabeneinschränkung wird von der Azure-Dienstverwaltung durchgesetzt. Hier sind Beispiele für gültige URLs für den geheimen Schlüssel und den Schlüsselverschlüsselungsschlüssel angegeben:

	- Beispiel für eine gültige URL des geheimen Schlüssels:

		**https://contosovault.vault.azure.net/secrets/BitLockerEncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

	- Beispiel für gültigen KRK-Schlüsselverschlüsselungsschlüssel:

		**https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

- Die Azure-Datenträgerverschlüsselung unterstützt keine Portnummern, die als Teil von URLs des geheimen Schlüssels für den Schlüsseltresor und des Schlüsselverschlüsselungsschlüssels angegeben werden. Beispiele für unterstützte Schlüsseltresor-URL:

 	- Nicht akzeptierte Schlüsseltresor-URL:

		**https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

	- Akzeptierte Schlüsseltresor-URL:

		**https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

- Die virtuellen IaaS-Computer müssen die folgenden Anforderungen an die Netzwerkendpunktkonfiguration erfüllen, um die Azure-Datenträgerverschlüsselung zu erfüllen:

	- Der virtuelle IaaS-Computer muss eine Verbindung mit dem Azure Active Directory-Endpunkt herstellen können [Login.windows.net], um ein Token für die Verbindungsherstellung mit dem Azure-Schlüsseltresor zu erhalten.

	- Der virtuelle IaaS-Computer muss eine Verbindung mit dem Azure-Schlüsseltresor-Endpunkt herstellen können, um die Verschlüsselungsschlüssel in den Schlüsseltresor des Kunden schreiben zu können.

	- Der virtuelle IaaS-Computer muss eine Verbindung mit dem Azure-Speicherendpunkt herstellen können, an dem das Azure-Erweiterungsrepository gehostet wird, sowie mit dem Azure-Speicherkonto, unter dem die VHD-Dateien gehostet werden.

**Hinweis:** Wenn Ihre Sicherheitsrichtlinie den Zugriff von virtuellen Azure-Computern auf das Internet beschränkt, können Sie den obigen URI auflösen, zu dem Sie eine Verbindung benötigen, und eine spezielle Regel konfigurieren, um ausgehende Verbindungen mit den IP-Adressen zuzulassen.

- Sie müssen zuerst [Azure PowerShell Version 1.0.2](https://github.com/Azure/azure-powershell/releases/tag/v1.0.2-December2015) installieren, um PowerShell-Cmdlets für die Azure-Datenträgerverschlüsselung ausführen zu können:

	- Um Azure PowerShell zu installieren und Ihrem Azure-Abonnement zuzuordnen, lesen Sie [Installieren und Konfigurieren von Azure PowerShell](powershell-install-configure.md).

	- In diesem Dokument wird vorausgesetzt, dass Sie mit den grundlegenden Konzepten vertraut sind, z. B. Modulen, Cmdlets und Sitzungen. Weitere Informationen finden Sie unter [Erste Schritte mit Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx).

**Hinweis:** Die Azure-Datenträgerverschlüsselung wird unter [Azure PowerShell SDK Version 1.1.0](https://github.com/Azure/azure-powershell/releases/tag/v1.1.0-January2016) nicht unterstützt.

- Sie müssen zuerst die richtige Azure-CLI-Version installieren, um Azure-CLI-Befehle ausführen und die Zuordnung zum Azure-Abonnement durchführen zu können:

	- Um Azure-CLI zu installieren und Ihrem Azure-Abonnement zuzuordnen, lesen Sie [Installieren und Konfigurieren von Azure-CLI](xplat-cli-install.md).

	- Informationen zum Verwenden der Azure-CLI für Mac, Linux und Windows mit dem Azure-Ressourcen-Manager finden Sie [hier](azure-cli-arm-commands.md).

- Bei der Lösung für die Azure-Datenträgerverschlüsselung wird die externe BitLocker-Schlüsselschutzvorrichtung für virtuelle Windows-IaaS-Computer verwendet. Wenn Ihre virtuellen Computer der Domäne beigetreten sind, sollten Sie keine Gruppenrichtlinien nutzen, mit denen TPM-Schutzvorrichtungen durchgesetzt werden. Ausführliche Informationen zur Gruppenrichtlinie für „BitLocker ohne kompatibles TPM zulassen“ finden Sie in [diesem Artikel](https://technet.microsoft.com/library/ee706521).
- Das für die Azure-Datenträgerverschlüsselung erforderliche PowerShell-Skript zum Erstellen einer Azure AD-Anwendung, Erstellen eines neuen Schlüsseltresors oder Einrichten eines vorhandenen Schlüsseltresors und Aktivieren der Verschlüsselung befindet sich [hier](https://github.com/Azure/azure-powershell/blob/dev/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1).

### Festlegen und Konfigurieren des Azure-Schlüsseltresors für die Azure-Datenträgerverschlüsselung

Mit der Azure-Datenträgerverschlüsselung werden die Schlüssel und geheimen Schlüssel in Ihrem Azure-Schlüsseltresor geschützt. Führen Sie die Schritte unten in den einzelnen Abschnitten aus, um den Schlüsseltresor für die Azure-Datenträgerverschlüsselung einzurichten.

#### Erstellen eines neuen Schlüsseltresors
Verwenden Sie eine der beiden unten angegebenen Optionen, um einen neuen Schlüsseltresor zu erstellen:

- Verwenden Sie die ARM-Vorlage „101-Create-KeyVault“, auf die Sie [hier](https://github.com/Azure/azure-quickstart-templates/blob/master/101-create-key-vault/azuredeploy.json) zugreifen können. 
- Verwenden Sie die Azure PowerShell-Befehle für den Schlüsseltresor wie [hier](key-vault-get-started.md) beschrieben.

**Hinweis:** Fahren Sie mit dem nächsten Abschnitt fort, falls Sie bereits einen Schlüsseltresor für Ihr Abonnement eingerichtet haben.

#### Bereitstellen eines Schlüsselverschlüsselungsschlüssels (optional)

Wenn Sie als zusätzliche Sicherheitsmaßnahme einen Schlüsselverschlüsselungsschlüssel als Wrapper für die BitLocker-Verschlüsselungsschlüssel verwenden möchten, sollten Sie Ihrem Schlüsseltresor einen Schlüsselverschlüsselungsschlüssel zur Verwendung während des Bereitstellungsprozesses hinzufügen. Verwenden Sie das [Add-AzureKeyVaultKey](https://msdn.microsoft.com/library/dn868048.aspx)-Cmdlet, um im Schlüsseltresor einen neuen Schlüsselverschlüsselungsschlüssel zu erstellen. Weitere Informationen finden Sie in der [Dokumentation zum Schlüsseltresor](https://azure.microsoft.com/documentation/services/key-vault/).

    Add-AzureKeyVaultKey [-VaultName] <string> [-Name] <string> -Destination <string> {HSM | Software}

#### Festlegen von Schlüsseltresor-Berechtigungen zur Gewährung des Zugriffs auf Schlüssel und geheime Schlüssel für die Azure-Plattform

Die Azure-Plattform benötigt Zugriff auf die Verschlüsselungsschlüssel oder geheimen Schlüssel in Ihrem Azure-Schlüsseltresor, um sie für den virtuellen Computer zur Verfügung zu stellen, damit die Volumes gestartet und entschlüsselt werden können. Um der Azure-Plattform Berechtigungen für den Zugriff auf den Schlüsseltresor zu gewähren, muss die *enabledForDiskEncryption*-Eigenschaft für den Schlüsseltresor festgelegt werden. Sie können die enabledForDiskEncryption-Eigenschaft für den Schlüsseltresor mit dem PS-Cmdlet des Schlüsseltresors festlegen:

    Set-AzureRmKeyVaultAccessPolicy -VaultName <yourVaultName> -ResourceGroupName <yourResourceGroup> -EnabledForDiskEncryption

Sie müssen die *enabledForDiskEncryption*-Eigenschaft wie oben erwähnt für Ihren Schlüsseltresor festlegen. Sie können die Eigenschaft festlegen, indem Sie https://resources.azure.com besuchen. Stellen Sie sicher, dass die oben beschriebenen Eigenschaften richtig festgelegt sind. Andernfalls kann es bei der Bereitstellung zu Fehlern kommen.

#### Einrichten der Azure AD-Anwendung in Azure Active Directory

Wenn die Verschlüsselung auf einem ausgeführten virtuellen Computer in Azure aktiviert werden muss, werden die Verschlüsselungsschlüssel von der Azure-Datenträgerverschlüsselung generiert und in den Schlüsseltresor geschrieben. Zum Verwalten von Verschlüsselungsschlüsseln im Schlüsseltresor wird die Azure AD-Authentifizierung benötigt.
 
Zu diesem Zweck sollte eine Azure AD-Anwendung erstellt werden. Ausführliche Schritte zum Registrieren einer Anwendung finden Sie im Abschnitt „Get an Identity for the Application“ (Abrufen einer Identität für die Anwendung) in [diesem Blogbeitrag](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). Der Beitrag enthält außerdem einige hilfreiche Beispiele zum Bereitstellen und Konfigurieren des Schlüsseltresors. Zu Authentifizierungszwecken kann entweder die auf einem geheimen Clientschlüssel basierende Authentifizierung oder die auf einem Clientzertifikat basierende Azure AD-Authentifizierung verwendet werden.

##### Auf geheimem Clientschlüssel basierende Authentifizierung für Azure AD

In den folgenden Abschnitten sind die erforderlichen Schritte zum Konfigurieren einer Authentifizierung für Azure AD angegeben, die auf einem geheimen Clientschlüssel basiert.

##### Erstellen einer neuen Azure AD-App mit Azure PowerShell

Verwenden Sie das unten angegebene PowerShell-Cmdlet, um eine neue Azure AD-App zu erstellen:

    $aadClientSecret = “yourSecret”
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password $aadClientSecret
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

**Hinweis:** „$azureAdApplication.ApplicationId“ ist die Azure AD-Client-ID, und „$aadClientSecret“ ist der geheime Clientschlüssel. Verwenden Sie diese Angaben später zum Aktivieren der Azure-Datenträgerverschlüsselung. Sie sollten den geheimen Azure AD-Clientschlüssel entsprechend schützen.


##### Bereitstellen der Azure AD-Client-ID und des geheimen Clientschlüssels über das Azure-Verwaltungsportal

Sie können die Azure AD-Client-ID und den geheimen Schlüssel auch über das Azure-Verwaltungsportal unter https://manage.windowsazure.com bereitstellen. Führen Sie die unten angegebenen Schritte aus, um diese Aufgabe durchzuführen:

1\. Klicken Sie wie in der Abbildung unten gezeigt auf die Registerkarte „Active Directory“:

![Azure-Datenträgerverschlüsselung](./media/azure-security-disk-encryption\disk-encryption-fig3.JPG)

2\. Klicken Sie auf „Anwendung hinzufügen“, und geben Sie den Anwendungsnamen wie unten gezeigt ein:

![Azure-Datenträgerverschlüsselung](./media/azure-security-disk-encryption\disk-encryption-fig4.JPG)

3\. Klicken Sie auf die Pfeilschaltfläche, und konfigurieren Sie die Eigenschaften der App wie unten gezeigt:

![Azure-Datenträgerverschlüsselung](./media/azure-security-disk-encryption\disk-encryption-fig5.JPG)

4\. Klicken Sie unten links auf das Häkchen, um den Vorgang abzuschließen. Die Konfigurationsseite der App wird angezeigt. Beachten Sie, dass sich die Azure AD-Client-ID unten auf der Seite befindet. Dies ist in der Abbildung unten dargestellt.
 
![Azure-Datenträgerverschlüsselung](./media/azure-security-disk-encryption\disk-encryption-fig6.JPG)

5\. Speichern Sie den geheimen Azure AD-Clientschlüssel, indem Sie auf die Schaltfläche „Speichern“ klicken. Klicken Sie auf die Schaltfläche „Speichern“, und sehen Sie sich den geheimen Schlüssel im Textfeld „Schlüssel“ an. Dies ist der geheime Azure AD-Clientschlüssel. Sie sollten den geheimen Azure AD-Clientschlüssel entsprechend schützen.

![Azure-Datenträgerverschlüsselung](./media/azure-security-disk-encryption\disk-encryption-fig7.JPG)


**Hinweis:** Der obige Ablauf wird im Portal nicht unterstützt.

##### Verwenden einer vorhandenen App

Um die unten angegebenen Befehle auszuführen, benötigen Sie das Azure AD PowerShell-Modul, das Sie [hier](https://technet.microsoft.com/library/jj151815.aspx) erhalten.

**Hinweis:** Die unten angegebenen Befehle müssen über ein neues PowerShell-Fenster ausgeführt werden. Verwenden Sie NICHT Azure PowerShell oder den Azure-Ressourcen-Manager, um diese Befehle auszuführen. Der Grund für diese Empfehlung ist, dass sich diese Cmdlets im MSOnline-Modul oder in Azure AD PowerShell befinden.

    $clientSecret = ‘<yourAadClientSecret>’ 
    $aadClientID = '<Client ID of your AAD app>'
    connect-msolservice
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type password -Value $clientSecret

#### Zertifikatbasierte Authentifizierung für Azure AD

In den folgenden Abschnitten sind die erforderlichen Schritte zum Konfigurieren einer zertifikatbasierten Authentifizierung für Azure AD angegeben.

##### Erstellen einer neuen Azure AD-App

Führen Sie die unten angegebenen PowerShell-Cmdlets aus, um eine neue Azure AD-App zu erstellen:

**Hinweis:** Ersetzen Sie die Zeichenfolge „yourpassword“ durch Ihr sicheres Kennwort, und schützen Sie das Kennwort.

    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate("C:\certificates\examplecert.pfx", "yourpassword")
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyValue $keyValue -KeyType AsymmetricX509Cert
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

Laden Sie nach Abschluss dieses Schritts eine PFX-Datei in den Schlüsseltresor hoch, und aktivieren Sie die Zugriffsrichtlinie, die zum Bereitstellen dieses Zertifikats auf einem virtuellen Computer benötigt wird.

##### Verwenden einer vorhandenen Azure AD-App
Verwenden Sie die unten angegebenen PowerShell-Cmdlets, wenn Sie die zertifikatbasierte Authentifizierung für eine vorhandene App konfigurieren. Achten Sie darauf, dass Sie die Cmdlets in einem neuen PowerShell-Fenster ausführen.

    $certLocalPath = 'C:\certs\myaadapp.cer' 
    $aadClientID = '<Client ID of your AAD app>'
    connect-msolservice
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate
    $cer.Import($certLocalPath)
    $binCert = $cer.GetRawCertData()
    $credValue = [System.Convert]::ToBase64String($binCert);
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type asymmetric -Value $credValue -Usage verify

Laden Sie nach Abschluss dieses Schritts eine PFX-Datei in den Schlüsseltresor hoch, und aktivieren Sie die Zugriffsrichtlinie, die zum Bereitstellen dieses Zertifikats auf einem virtuellen Computer benötigt wird.

##### Hochladen einer PFX-Datei in den Schlüsseltresor
Eine ausführliche Erklärung der Funktionsweise dieses Prozesses finden Sie in [diesem Blogbeitrag](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx). Für diese Aufgabe benötigen Sie nur die unten angegebenen PowerShell-Cmdlets. Stellen Sie sicher, dass Sie sie über die Azure PowerShell-Konsole ausführen:

**Hinweis:** Ersetzen Sie die Zeichenfolge „yourpassword“ durch Ihr sicheres Kennwort, und schützen Sie das Kennwort.

    $certLocalPath = 'C:\certs\myaadapp.pfx' 
    $certPassword = "yourpassword"
    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’

    $fileContentBytes = get-content $certLocalPath -Encoding Byte
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

    $jsonObject = @"
    {
    "data": "$filecontentencoded",
    "dataType" :"pfx",
    "password": "$certPassword"
    }
    "@

    $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
    $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

    Switch-AzureMode -Name AzureResourceManager
    $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
    Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName -SecretValue $secret 
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName –EnabledForDeployment

##### Bereitstellen eines Zertifikats im Schlüsseltresor auf einem vorhandenen virtuellen Computer
Führen Sie nach Abschluss des PFX-Uploads die unten angegebenen Schritte aus, um im Schlüsseltresor ein Zertifikat auf einem vorhandenen virtuellen Computer bereitzustellen:

    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’
    $vmName = ‘yourVMName’
    $certUrl = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName).Id
    $sourceVaultId = (Get-AzureRmKeyVault -VaultName $keyVaultName -ResourceGroupName $resourceGroupName).ResourceId
    $vm = Get-AzureRmVM -ResourceGroupName $resourceGroupName -Name $vmName
    $vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore "My" -CertificateUrl $certUrl
    Update-AzureRmVM -VM $vm  -ResourceGroupName $resourceGroupName


#### Festlegen der Richtlinie für den Zugriff auf den Schlüsseltresor für die Azure AD-Anwendung

Ihre Azure AD-Anwendung benötigt Rechte zum Zugreifen auf die Schlüssel oder geheimen Schlüssel im Tresor. Verwenden Sie das [Set-AzureKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/dn903607.aspx)-Cmdlet zum Gewähren von Berechtigungen für die Anwendung, und verwenden Sie hierbei die Client-ID (die während der Registrierung der Anwendung generiert wurde) als –ServicePrincipalName-Parameterwert. Beispiele hierzu finden Sie in [diesem Blogbeitrag](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). Hier ist auch ein Beispiel für die Durchführung dieser Aufgabe per PowerShell angegeben:

    $keyVaultName = ‘yourKeyVaultName’
    $aadClientID = '<youAadAppClientID>'
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys all -PermissionsToSecrets all

## Terminologie

Verwenden Sie die Terminologietabelle als Referenz, um sich mit einigen allgemeinen Begriffen vertraut zu machen, die von dieser Technologie genutzt werden:


| Terminologie | Definition |
|-----------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure AD | Azure AD steht für [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Ein Azure AD-Konto ist eine Voraussetzung zum Authentifizieren, Speichern und Abrufen von geheimen Schlüsseln aus dem Schlüsseltresor. |
| Azure-Schlüsseltresor | Der Azure-Schlüsseltresor ist ein Dienst zum Verwalten kryptografischer Schlüssel, der auf Hardwaresicherheitsmodulen mit FIPS-Überprüfung basiert, damit Ihre kryptografischen Schlüssel und vertraulichen Daten sicher und geschützt sind. Weitere Informationen finden Sie in der Dokumentation zum [Schlüsseltresor](https://azure.microsoft.com/services/key-vault/). |
| ARM | Azure-Ressourcen-Manager |
| BitLocker | Bei [BitLocker](https://technet.microsoft.com/library/hh831713.aspx) handelt es sich um eine branchenübliche Verschlüsselungstechnologie für Windows-Volumes, um die Datenträgerverschlüsselung auf virtuellen Windows-IaaS-Computern zu aktivieren. |
| BEK | BitLocker-Verschlüsselungsschlüssel (BitLocker Encryption Keys) werden verwendet, um das Startvolume des Betriebssystems und Datenvolumes zu verschlüsseln. Die BitLocker-Schlüssel sind im Azure-Schlüsseltresor als geheime Schlüssel geschützt. |
| Befehlszeilenschnittstelle (CLI) | [Azure-Befehlszeilenschnittstelle](xplat-cli-install.md) |
| DM-Crypt | [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) ist das Linux-basierte transparente Subsystem für die Datenträgerverschlüsselung, das zum Aktivieren der Datenträgerverschlüsselung auf virtuellen Linux-IaaS-Computern verwendet wird. |
| KEK | Der Schlüsselverschlüsselungsschlüssel (Key Encryption Key) ist der asymmetrische Schlüssel (RSA 2048), der zum Schützen oder Umschließen (falls erforderlich) des geheimen Schlüssels verwendet wird. Sie können einen Schlüssel mit HSM-Schutz oder einen Schlüssel mit Softwareschutz bereitstellen. Weitere Informationen finden Sie in der Dokumentation zum [Azure-Schlüsseltresor](https://azure.microsoft.com/services/key-vault/). |
| PS-Cmdlets | [Azure PowerShell-Cmdlets](powershell-install-configure.md) |

## Bereitstellungsszenarien und Benutzeroberflächen der Datenträgerverschlüsselung

Es gibt viele Szenarien, in denen Sie die Datenträgerverschlüsselung aktivieren können. Die Schritte können je nach Szenario variieren. In den folgenden Abschnitten werden diese Szenarien ausführlicher beschrieben.

### Aktivieren der Verschlüsselung auf neuen virtuellen IaaS-VM-Computern, die über den Azure-Katalog erstellt werden

Die Datenträgerverschlüsselung kann auf neuen virtuellen Windows-IaaS-Computern über den Azure-Katalog in Azure aktiviert werden, indem die [hier](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image) veröffentlichte ARM-Vorlage genutzt wird. Klicken Sie in der Azure-Schnellstartvorlage auf die Schaltfläche „In Azure bereitstellen“, geben Sie die Verschlüsselungskonfiguration im Blatt „Parameter“ ein, und klicken Sie auf „OK“. Wählen Sie Abonnement, Ressourcengruppe, Ressourcengruppenstandort, rechtliche Bedingungen und Vereinbarung aus, und klicken Sie auf die Schaltfläche „Erstellen“, um die Verschlüsselung auf einem neuen virtuellen IaaS-Computer zu aktivieren.

**Hinweis:** Mit dieser Vorlage wird ein neuer verschlüsselter virtueller Windows-Computer erstellt, indem das Windows Server 2012-Katalogimage verwendet wird.

Unten in der Tabelle sind die Parameterdetails der ARM-Vorlage für neue virtuelle Computer aus dem Azure-Katalogszenario mit Azure AD-Client-ID aufgeführt:

| Parameter | Beschreibung|
|-------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| adminUserName | Administratorbenutzername für den virtuellen Computer. |
| adminPassword | Administratorkennwort für den virtuellen Computer. |
| newStorageAccountName | Name des Speicherkontos zum Speichern von Betriebssystem- und Daten-VHDs. |
| vmSize | Die Größe des virtuellen Computers. Derzeit werden nur die standardmäßigen A-, D- und G-Serien unterstützt. |
| virtualNetworkName | Der Name des VNET, zu dem die VM-Netzwerkschnittstellenkarte gehören sollte. |
| subnetName | Name des Subnetzes im VNET, zu dem die VM-Netzwerkschnittstellenkarte gehören sollte. |
| AADClientID | Client-ID der Azure AD-App, die über Berechtigungen zum Schreiben von geheimen Schlüsseln in den Schlüsseltresor verfügt. |
| AADClientSecret | Geheimer Clientschlüssel der Azure AD-App, die über Berechtigungen zum Schreiben von geheimen Schlüsseln in den Schlüsseltresor verfügt. |
| keyVaultResourceID,ResourceID | Dient zum Identifizieren der Schlüsseltresor-Ressource für ARM. Sie können sie über das PowerShell-Cmdlet abrufen: (Get-AzureRmKeyVault -VaultName,-ResourceGroupName ).ResourceId |
| keyVaultURL | Die URL des Schlüsseltresors, in den der BitLocker-Schlüssel hochgeladen werden sollte. Sie können sie über das PowerShell-Cmdlet abrufen: (Get-AzureRmKeyVault -VaultName,-ResourceGroupName ).VaultURI |
| keyEncryptionKeyURL | Die URL des Schlüsselverschlüsselungsschlüssels, der zum Verschlüsseln des generierten BitLocker-Schlüssels verwendet wird. Dies ist optional. |
| vmName | Name des virtuellen Computers, auf dem der Verschlüsselungsvorgang durchgeführt wird. 


**Hinweis:** „KeyEncryptionKeyURL“ ist ein optionaler Parameter. Sie können Ihren eigenen Schlüsselverschlüsselungsschlüssel nutzen, um den Schutz für den Datenverschlüsselungsschlüssel (geheimer Passphrase-Schlüssel) im Schlüsseltresor zu erhöhen.

### Aktivieren der Verschlüsselung auf neuen virtuellen IaaS-Computern, die mit einer vom Kunden verschlüsselten VHD und Verschlüsselungsschlüsseln erstellt werden

Bei diesem Szenario können Sie die Verschlüsselung aktivieren, indem Sie die ARM-Vorlage, PowerShell-Cmdlets oder CLI-Befehle verwenden. In den folgenden Abschnitten werden die ARM-Vorlage und die CLI-Befehle ausführlicher erläutert.

#### Verwenden der ARM-Vorlage

Die Datenträgerverschlüsselung kann auf einer vom Kunden verschlüsselten VHD aktiviert werden, indem die [hier](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm) veröffentlichte ARM-Vorlage genutzt wird. Klicken Sie in der Azure-Schnellstartvorlage auf die Schaltfläche „In Azure bereitstellen“, geben Sie die Verschlüsselungskonfiguration im Blatt „Parameter“ ein, und klicken Sie auf „OK“. Wählen Sie Abonnement, Ressourcengruppe, Ressourcengruppenstandort, rechtliche Bedingungen und Vereinbarung aus, und klicken Sie auf die Schaltfläche „Erstellen“, um die Verschlüsselung auf einem neuen virtuellen IaaS-Computer zu aktivieren.

In der Tabelle unten sind die Parameterdetails der ARM-Vorlage für ein Szenario mit einer vom Kunden verschlüsselten VHD beschrieben:

| Parameter | Beschreibung|
|-------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| newStorageAccountName | Name des Speicherkontos zum Speichern des Betriebssystems auf einer verschlüsselten VHD. Dieses Speicherkonto sollte bereits in derselben Ressourcengruppe und an demselben Speicherort wie der virtuelle Computer erstellt worden sein. |
| osVhdUri | URI der Betriebssystem-VHD aus dem Speicherkonto |
| osType | Betriebssystem-Produkttyp (Windows/Linux) |
| virtualNetworkName | Der Name des VNET, zu dem die VM-Netzwerkschnittstellenkarte gehören sollte. Es sollte bereits in derselben Ressourcengruppe und an demselben Speicherort wie der virtuelle Computer erstellt worden sein. |
| subnetName | Name des Subnetzes im VNET, zu dem die VM-Netzwerkschnittstellenkarte gehören sollte. |
| vmSize | Die Größe des virtuellen Computers. Derzeit werden nur die standardmäßigen A-, D- und G-Serien unterstützt. |
| keyVaultResourceID | ResourceID zum Identifizieren der Schlüsseltresor-Ressource in ARM. Sie können sie mit dem PowerShell-Cmdlet abrufen: (Get-AzureRmKeyVault -VaultName &lt;yourKeyVaultName&gt; -ResourceGroupName &lt;yourResourceGroupName&gt;).ResourceId |
| keyVaultSecretUrl | ​URL des Schlüssels für die Datenträgerverschlüsselung im Schlüsseltresor |
| keyVaultKekUrl | Die URL des Schlüsselverschlüsselungsschlüssels, der zum Verschlüsseln des generierten Datenträgerverschlüsselungsschlüssels verwendet wird. |
| ​vmName | ​Name des virtuellen IaaS-Computers   



####Verwenden von PowerShell-Cmdlets

Die Datenträgerverschlüsselung kann auf einer vom Kunden verschlüsselten VHD aktiviert werden, indem die [hier](https://msdn.microsoft.com/library/azure/mt603746.aspx) veröffentlichten PowerShell-Cmdlets genutzt werden.

####Verwenden von CLI-Befehlen

Führen Sie die folgenden Schritte aus, um die Datenträgerverschlüsselung für dieses Szenario mit CLI-Befehlen zu aktivieren:

1. Legen Sie Zugriffsrichtlinien für den Schlüsseltresor fest:
	- Legen Sie das Flag „EnabledForDiskEncryption“ fest: „azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true“
	- Legen Sie Berechtigungen für die Azure AD-App zum Schreiben von geheimen Schlüsseln in den Schlüsseltresor fest: „azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys ["all"] --perms-to-secrets ["all"]“
2. Geben Sie Folgendes ein, um die Verschlüsselung auf einem vorhandenen/aktiven virtuellen Computer zu aktivieren: *azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId>*
3. Rufen Sie den Verschlüsselungsstatus ab: *„azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json“* 
4. Verwenden Sie die unten angegebenen Parameter mit dem Befehl „azure vm create“, um die Verschlüsselung auf einem neuen virtuellen Computer über eine vom Kunden verschlüsselte VHD zu aktivieren:
	- disk-encryption-key-vault-id <disk-encryption-key-vault-id>
	- disk-encryption-key-url <disk-encryption-key-url>
	- key-encryption-key-vault-id <key-encryption-key-vault-id>
	- key-encryption-key-url <key-encryption-key-url>


### Aktivieren der Verschlüsselung auf einem vorhandenen/ausgeführten virtuellen Windows-IaaS-Computer in Azure

Bei diesem Szenario können Sie die Verschlüsselung aktivieren, indem Sie die ARM-Vorlage, PowerShell-Cmdlets oder CLI-Befehle verwenden. In den folgenden Abschnitten wird ausführlicher erläutert, wie Sie dies mit der ARM-Vorlage und CLI-Befehlen aktivieren.

#### Verwenden der ARM-Vorlage

Die Datenträgerverschlüsselung kann auf einem vorhandenen/aktiven Windows-IaaS-Computer in Azure aktiviert werden, indem die [hier](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm) veröffentlichte ARM-Vorlage genutzt wird. Klicken Sie in der Azure-Schnellstartvorlage auf die Schaltfläche „In Azure bereitstellen“, geben Sie die Verschlüsselungskonfiguration im Blatt „Parameter“ ein, und klicken Sie auf „OK“. Wählen Sie Abonnement, Ressourcengruppe, Ressourcengruppenstandort, rechtliche Bedingungen und Vereinbarung aus, und klicken Sie auf die Schaltfläche „Erstellen“, um die Verschlüsselung auf einem vorhandenen/ausgeführten virtuellen IaaS-Computer zu aktivieren.

In der Tabelle unten sind die Parameterdetails der ARM-Vorlage für ein Szenario mit vorhandenem/ausgeführtem virtuellen Computer per Azure AD-Client-ID angegeben:

| Parameter | Beschreibung|
|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ​AADClientID | ​Client-ID der Azure AD-App, die über Berechtigungen zum Schreiben von geheimen Schlüsseln in den Schlüsseltresor verfügt. |
| AADClientSecret | ​Geheimer Clientschlüssel der Azure AD-App, die über Berechtigungen zum Schreiben von geheimen Schlüsseln in den Schlüsseltresor verfügt. |
| keyVaultName | Der Name des Schlüsseltresors, in den der BitLocker-Schlüssel hochgeladen werden sollte. Sie können ihn über das Cmdlet abrufen: (Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>). Vaultname |
| ​ keyEncryptionKeyURL | Die URL des Schlüsselverschlüsselungsschlüssels, der zum Verschlüsseln des generierten BitLocker-Schlüssels verwendet wird. Dies ist optional, wenn Sie in der Dropdownliste „UseExistingKek“ die Option „nokek“ wählen. Wenn Sie in der Dropdownliste „UseExistingKek“ die Option „kek“ wählen, müssen Sie den keyEncryptionKeyURL-Wert eingeben. |
| ​volumeType | ​Der Typ des Volumes, auf dem der Verschlüsselungsvorgang durchgeführt wird. Gültige Werte sind „OS“, „Data“, „All“. |
| sequenceVersion | Sequenzversion des BitLocker-Vorgangs. Diese Versionsnummer sollte jedes Mal erhöht werden, wenn auf demselben virtuellen Computer ein Vorgang für eine Datenträgerverschlüsselung durchgeführt wird. |
| ​vmName | ​Name des virtuellen Computers, auf dem der Verschlüsselungsvorgang durchgeführt wird.


**Hinweis:** „KeyEncryptionKeyURL“ ist ein optionaler Parameter. Sie können Ihren eigenen Schlüsselverschlüsselungsschlüssel nutzen, um den Schutz für den Datenverschlüsselungsschlüssel (geheimer BitLocker-Verschlüsselungsschlüssel) im Schlüsseltresor zu erhöhen.

#### Verwenden von PowerShell-Cmdlets

Ausführliche Informationen dazu, wie Verschlüsselung über Azure-Datenträgerverschlüsselung mit PowerShell-Cmdlets aktiviert wird, finden Sie im Blogbeitrag **Explore Azure disk encryption with Azure PowerShell** in [Teil 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) und [Teil 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx).

#### Verwenden von CLI-Befehlen

Führen Sie die unten angegebenen Schritte aus, um die Verschlüsselung auf einem vorhandenen/ausgeführten virtuellen Windows-IaaS-Computer in Azure mit CLI-Befehlen zu aktivieren:

1. Legen Sie Zugriffsrichtlinien für den Schlüsseltresor fest:
	- Legen Sie das Flag „EnabledForDiskEncryption“ fest: „azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true“
	- Legen Sie Berechtigungen für die Azure AD-App zum Schreiben von geheimen Schlüsseln in den Schlüsseltresor fest: „azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys ["all"] --perms-to-secrets ["all"]“
2. Geben Sie Folgendes ein, um die Verschlüsselung auf einem vorhandenen/aktiven virtuellen Computer zu aktivieren: *azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId>*
3. Rufen Sie den Verschlüsselungsstatus ab: *„azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json“* 
4. Verwenden Sie die unten angegebenen Parameter mit dem Befehl „azure vm create“, um die Verschlüsselung auf einem neuen virtuellen Computer über eine vom Kunden verschlüsselte VHD zu aktivieren:
	- disk-encryption-key-vault-id <disk-encryption-key-vault-id>
	- disk-encryption-key-url <disk-encryption-key-url>
	- key-encryption-key-vault-id <key-encryption-key-vault-id>
	- key-encryption-key-url <key-encryption-key-url>


### Aktivieren der Verschlüsselung auf einem vorhandenen/ausgeführten virtuellen Linux-IaaS-Computer in Azure

Die Datenträgerverschlüsselung kann auf einem vorhandenen/aktiven Linux-IaaS-Computer in Azure aktiviert werden, indem die [hier](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm) veröffentlichte ARM-Vorlage genutzt wird. Klicken Sie in der Azure-Schnellstartvorlage auf die Schaltfläche „In Azure bereitstellen“, geben Sie die Verschlüsselungskonfiguration im Blatt „Parameter“ ein, und klicken Sie auf „OK“. Wählen Sie Abonnement, Ressourcengruppe, Ressourcengruppenstandort, rechtliche Bedingungen und Vereinbarung aus, und klicken Sie auf die Schaltfläche „Erstellen“, um die Verschlüsselung auf einem vorhandenen/ausgeführten virtuellen IaaS-Computer zu aktivieren.

In der Tabelle unten sind die Parameterdetails der ARM-Vorlage für ein Szenario mit vorhandenem/ausgeführtem virtuellen Computer per Azure AD-Client-ID beschrieben:

| Parameter | Beschreibung|
|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ​AADClientID | ​Client-ID der Azure AD-App, die über Berechtigungen zum Schreiben von geheimen Schlüsseln in den Schlüsseltresor verfügt. |
| AADClientSecret | ​Geheimer Clientschlüssel der Azure AD-App, die über Berechtigungen zum Schreiben von geheimen Schlüsseln in den Schlüsseltresor verfügt. |
| keyVaultName | Der Name des Schlüsseltresors, in den der BitLocker-Schlüssel hochgeladen werden sollte. Sie können ihn über das Cmdlet abrufen: (Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>). Vaultname |
| ​ keyEncryptionKeyURL | Die URL des Schlüsselverschlüsselungsschlüssels, der zum Verschlüsseln des generierten BitLocker-Schlüssels verwendet wird. Dies ist optional, wenn Sie in der Dropdownliste „UseExistingKek“ die Option „nokek“ wählen. Wenn Sie in der Dropdownliste „UseExistingKek“ die Option „kek“ wählen, müssen Sie den keyEncryptionKeyURL-Wert eingeben. |
| ​volumeType | ​Der Typ des Volumes, auf dem der Verschlüsselungsvorgang durchgeführt wird. Der gültige unterstützte Wert lautet „Data“. Für virtuelle Linux-Computer wird die Aktivierung der Verschlüsselung des Betriebssystemvolumes auf einem ausgeführten virtuellen Linux-Computer nicht unterstützt. |
| sequenceVersion | Sequenzversion des BitLocker-Vorgangs. Diese Versionsnummer sollte jedes Mal erhöht werden, wenn auf demselben virtuellen Computer ein Vorgang für eine Datenträgerverschlüsselung durchgeführt wird. |
| ​vmName | ​Name des virtuellen Computers, auf dem der Verschlüsselungsvorgang durchgeführt wird.
| passPhrase | Geben Sie eine sichere Passphrase als Datenverschlüsselungsschlüssel ein. |                                                                                                                                                                                                                                                      
                                                                                                                                                            
**Hinweis:** „KeyEncryptionKeyURL“ ist ein optionaler Parameter. Sie können Ihren eigenen Schlüsselverschlüsselungsschlüssel nutzen, um den Schutz für den Datenverschlüsselungsschlüssel (geheimer Passphrase-Schlüssel) im Schlüsseltresor zu erhöhen.

#### CLI-Befehle

Die Datenträgerverschlüsselung kann auf vom Kunden verschlüsselten VHDs aktiviert werden, indem der von [hier](xplat-cli-install.md) installierte CLI-Befehl verwendet wird. Führen Sie die unten angegebenen Schritte aus, um die Verschlüsselung auf einem vorhandenen/ausgeführten virtuellen Linux-IaaS-Computer in Azure mit CLI-Befehlen zu aktivieren:

1. Legen Sie Zugriffsrichtlinien für den Schlüsseltresor fest:
	- Legen Sie das Flag „EnabledForDiskEncryption“ fest: „azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true“
	- Legen Sie Berechtigungen für die Azure AD-App zum Schreiben von geheimen Schlüsseln in den Schlüsseltresor fest: „azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys ["all"] --perms-to-secrets ["all"]“
2. Geben Sie Folgendes ein, um die Verschlüsselung auf einem vorhandenen/aktiven virtuellen Computer zu aktivieren: *azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId>*
3. Rufen Sie den Verschlüsselungsstatus ab: „azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json“ 
4. Verwenden Sie die unten angegebenen Parameter mit dem Befehl „azure vm create“, um die Verschlüsselung auf einem neuen virtuellen Computer über eine vom Kunden verschlüsselte VHD zu aktivieren.
	- *disk-encryption-key-vault-id <disk-encryption-key-vault-id>*
	- *disk-encryption-key-url <disk-encryption-key-url>*
	- *key-encryption-key-vault-id <key-encryption-key-vault-id>*
	- *key-encryption-key-url <key-encryption-key-url>*

### Abrufen des Verschlüsselungsstatus eines verschlüsselten virtuellen IaaS-Computers

Sie können den Verschlüsselungsstatus über das Azure-Verwaltungsportal, [PowerShell-Cmdlets](https://msdn.microsoft.com/library/azure/mt622700.aspx) oder CLI-Befehle abrufen. In den folgenden Abschnitten wird erläutert, wie Sie das Azure-Verwaltungsportal (Vorschau) und CLI-Befehle zum Abrufen des Verschlüsselungsstatus verwenden.

#### Abrufen des Verschlüsselungsstatus einer verschlüsselten virtuellen IaaS-Maschine mit dem Azure-Verwaltungsportal

Sie können den Verschlüsselungsstatus der virtuellen IaaS-Maschine über das Azure-Verwaltungsportal abrufen. Melden Sie sich beim Azure-Portal unter https://portal.azure.com/ an, und klicken Sie im Menü links auf den Link zu den virtuellen Maschinen, um eine Zusammenfassung der virtuellen Maschinen Ihres Abonnements anzuzeigen. Sie können die Ansicht mit den virtuellen Computern filtern, indem Sie den Abonnementnamen in der Abonnement-Dropdownliste auswählen. Klicken Sie auf die Spalten, die sich am oberen Rand des Seitenmenüs für die virtuellen Computer befinden. Wählen Sie im Blatt „Spalte auswählen“ die Spalte „Datenträgerverschlüsselung“, und klicken Sie auf „Aktualisieren“. Sie sehen, dass in der Spalte „Datenträgerverschlüsselung“ für jeden virtuellen Computer der Verschlüsselungsstatus „Aktiviert“ oder „Nicht aktiviert“ angezeigt wird. Dies ist unten in der Abbildung dargestellt.

![Microsoft-Antischadsoftware in Azure](./media/azure-security-disk-encryption/disk-encryption-fig2.JPG)

#### Abrufen des Verschlüsselungsstatus eines verschlüsselten virtuellen IaaS-Computers mit dem PS-Cmdlet für die Datenträgerverschlüsselung
Sie können den Verschlüsselungsstatus des virtuellen IaaS-Computers über das PS-Cmdlet „Get-AzureRmVMDiskEncryptionStatus“ für die Datenträgerverschlüsselung abrufen. Geben Sie zum Abrufen der Verschlüsselungseinstellungen für Ihren virtuellen Computer Ihre Azure PowerShell-Sitzung ein:

    PS C:\Windows\System32\WindowsPowerShell\v1.0> Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName <yourResourceGroupName> -VMName <yourVMName>

    OsVolumeEncrypted: True
    OsVolumeEncryptionSettings : {
      "DiskEncryptionKey": {
       SecretUrl":"https://contosovault.vault.azure.net/secrets/BitLockerEncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
            "SourceVault": {
            "ReferenceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxxx/providers/Mi                            crosoft.KeyVault/vaults/xxxxxxx"
                                }
                            },
                    "KeyEncryptionKey": null
                             }
    DataVolumesEncrypted: True

Die Werte für die Einstellungen „OSVolumeEncrypted“ und „DataVolumesEncrypted“ sind auf „True“ festgelegt. Dies bedeutet, dass beide Volumes per Azure-Datenträgerverschlüsselung verschlüsselt sind. Ausführliche Informationen dazu, wie Verschlüsselung über Azure-Datenträgerverschlüsselung mit PowerShell-Cmdlets aktiviert wird, finden Sie im Blogbeitrag **Explore Azure disk encryption with Azure PowerShell** in [Teil 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) und [Teil 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx).

#### Abrufen des Verschlüsselungsstatus des virtuellen IaaS-Computers über den CLI-Befehl für die Datenträgerverschlüsselung

Sie können den Verschlüsselungsstatus des virtuellen IaaS-Computers über den CLI-Befehl *azure vm show-disk-encryption-status* für die Datenträgerverschlüsselung abrufen. Geben Sie Ihre Azure-CLI-Sitzung ein, um die Verschlüsselungseinstellungen für Ihren virtuellen Computer abzurufen:
 
    azure vm show-disk-encryption-status --resource-group <yourResourceGroupName> --name <yourVMName> --json  


## Anhang 

### Verbinden mit Ihrem Abonnement

Lesen Sie sich den Abschnitt zu den Voraussetzungen in diesem Artikel durch, bevor Sie fortfahren. Führen Sie nach dem Sicherstellen, dass alle Voraussetzungen erfüllt sind, die folgenden Schritte aus, um eine Verbindung mit Ihrem Abonnement herzustellen:

1\. Starten Sie eine Azure PowerShell-Sitzung, und melden Sie sich mit dem folgenden Befehl bei Ihrem Azure-Konto an:

    Login-AzureRmAccount

2\. Wenn Sie über mehrere Abonnements verfügen und zur Verwendung ein bestimmtes Abonnement verwenden möchten, geben Sie den folgenden Befehl ein, um die Abonnements für Ihr Konto anzuzeigen:

    Get-AzureRmSubscription

3\. Geben Sie Folgendes ein, um das zu verwendende Abonnement anzugeben:

    Select-AzureRmSubscription -SubscriptionName <Yoursubscriptionname>

4\. Geben Sie Folgendes ein, um zu überprüfen, ob es sich um das richtige Abonnement handelt:

    Get-AzureRmSubscription
 
5\. Geben Sie Folgendes ein, um zu überprüfen, ob die Cmdlets für die Azure-Datenträgerverschlüsselung installiert sind:
 
    Get-command *diskencryption*

6\. Es sollte die unten angegebene Ausgabe erfolgen, um die PowerShell-Installation für die Azure-Datenträgerverschlüsselung zu bestätigen:

    PS C:\Windows\System32\WindowsPowerShell\v1.0> get-command *diskencryption*
    CommandType  Name                               	 Version    Source                                                             
    Cmdlet       Get-AzureRmVMDiskEncryptionStatus       1.1.0      AzureRM.Compute                                                    
    Cmdlet       Remove-AzureRmVMDiskEncryptionExtension 1.1.0      AzureRM.Compute                                                    
    Cmdlet       Set-AzureRmVMDiskEncryptionExtension    1.1.0      AzureRM.Compute                                                     

### Vorbereiten einer vorverschlüsselten Windows-VHD
Die folgenden Abschnitte sind erforderlich, um eine vorverschlüsselte Windows-VHD für die Bereitstellung als verschlüsselte VHD in Azure IaaS vorzubereiten. Die Schritte dienen zum Vorbereiten und Starten eines neuen virtuellen Windows-Computers (VHD) unter Hyper-V oder Azure.

#### Aktualisieren der Gruppenrichtlinie, um Schutz ohne TPM für das Betriebssystem zu ermöglichen
Sie müssen die BitLocker-Gruppenrichtlinieneinstellung mit dem Namen „BitLocker-Laufwerkverschlüsselung“ konfigurieren, die sich unter „ Lokale Computerrichtlinie\\Computerkonfiguration\\Administrative Vorlagen\\Windows-Komponenten“ befindet. Ändern Sie diese Einstellung wie folgt: *Betriebssystemlaufwerke – Zusätzliche Authentifizierung beim Start anfordern – BitLocker ohne kompatibles TPM zulassen*. Dies ist unten in der Abbildung dargestellt:
 
![Microsoft-Antischadsoftware in Azure](./media/azure-security-disk-encryption/disk-encryption-fig8.JPG)

#### Installieren der Komponenten der BitLocker-Funktion
Verwenden Sie für Windows Server 2012 und höher den folgenden Befehl:
 
    dism /online /Enable-Feature /all /FeatureName:Bitlocker /quiet /norestart

Verwenden Sie für Windows Server 2008 R2 den folgenden Befehl:

    ServerManagerCmd -install BitLockers

#### Vorbereiten des Betriebssystemvolumes für BitLocker mit bdehdcfg

Führen Sie den unten angegebenen Befehl aus, um die Betriebssystempartition zu kombinieren, und bereiten Sie den Computer für BitLocker vor.

    bdehdcfg -target c: shrink -quiet

#### Verwenden von BitLocker zum Schützen des Betriebssystemvolumes
Verwenden Sie den Befehl [manage-bde](https://technet.microsoft.com/library/ff829849.aspx), um die Verschlüsselung auf dem Startvolume mit einer externen Schlüsselschutzvorrichtung zu aktivieren, und legen Sie den externen Schlüssel (BEK-Datei) auf dem externen Laufwerk oder Volume ab. Die Verschlüsselung wird auf dem System-/Startvolume nach dem nächsten Neustart aktiviert.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

**Hinweis:** Der virtuelle Computer muss mit einer separaten Daten-/Ressourcen-VHD zum Abrufen des externen Schlüssels mit BitLocker vorbereitet werden.

#### Vorbereiten einer vorverschlüsselten Linux-VHD

##### Ubuntu 14:

1\. Erstellen Sie unter „/usr/local/sbin/azure\_crypt\_key.sh“ eine Datei mit dem Inhalt, der unten im Skript enthalten ist. Achten Sie auf KeyFileName, da dies der von Azure vergebene Passphrase-Name ist.

    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    sleep 2
    OPENED=0
    for SFS in /sys/block/sd*; do
        DEV=`basename $SFS`
        F=$SFS/${DEV}1/dev
        echo "> Trying device: $DEV ..." >&2
        mount /dev/${DEV}1 $MountPoint -t vfat -r >/dev/null
        if [ -f $MountPoint/$KeyFileName ]; then
                cat $MountPoint/$KeyFileName
                umount $MountPoint 2>/dev/null
                OPENED=1
                break
        fi
        umount $MountPoint 2>/dev/null
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"
     else
        echo "Success loading keyfile!" >&2
    fi


2\. Ändern Sie die Verschlüsselungskonfiguration in */etc/crypttab*. Diese sollte wie folgt aussehen:

    Sda5_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh

3\. Wenn Sie die Datei *azure\_crypt\_key.sh* in Windows bearbeitet und nach Linux kopiert haben, sollten Sie nicht vergessen, *dos2unix /usr/local/sbin/azure\_crypt\_key.sh* auszuführen. 4. Führen Sie *update-initramfs -u -k all* aus, um initramfs zu aktualisieren, damit das Schlüsselskript wirksam wird.

##### openSUSE 13.2:

1\. Bearbeiten Sie „/etc/dracut.conf add\_drivers+="vfat nls\_cp437 nls\_iso8859-1“.

2\. Kommentieren Sie diese Zeilen am Ende der Datei „/usr/lib/dracut/modules.d/90crypt/module-setup.sh“ aus:

    #    inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator


3\. Fügen Sie „DRACUT\_SYSTEMD=0“ am Anfang der Datei „/usr/lib/dracut/modules.d/90crypt/parse-crypt.sh“ an, und ändern Sie alle Vorkommen von „if [ -z "$DRACUT\_SYSTEMD" ]; then“ in „if [ 1 ]; then“.

4\. Bearbeiten Sie die Datei „/usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh“, und fügen Sie sie nach „# Open LUKS device“ an.

    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    for SFS in /dev/sd*; do
       echo "> Trying device:$SFS..." >&2
       mount ${SFS}1 $MountPoint -t vfat -r >&2
       if [ -f $MountPoint/$KeyFileName ]; then
          echo "> keyfile got..." >&2
          luksfile=$MountPoint/$KeyFileName
          break
       fi
    done

5\. Führen Sie „dracut –f -v“ aus, um initrd zu aktualisieren.

##### CentOS 7:
1\. Bearbeiten Sie „/etc/dracut.conf add\_drivers+=" vfat nls\_cp437 nls\_iso8859-1“.

2\. Kommentieren Sie diese Zeilen am Ende der Datei „/usr/lib/dracut/modules.d/90crypt/module-setup.sh“ aus:

    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator



3\. Fügen Sie „DRACUT\_SYSTEMD=0“ am Anfang der Datei „/usr/lib/dracut/modules.d/90crypt/parse-crypt.sh“ an, und ändern Sie alle Vorkommen von „if [ -z "$DRACUT\_SYSTEMD" ]; then“ in „if [ 1 ]; then“.

4\. Bearbeiten Sie die Datei „/usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh“, und fügen Sie sie nach „# Open LUKS device“ an.

    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        luksfile=$MountPoint/$KeyFileName
        break
    fi
    done


5\. Führen Sie „/usr/sbin/dracut -f -v“ aus, um initrd zu aktualisieren.

###Hochladen einer verschlüsselten VHD in ein Azure-Speicherkonto
Nachdem die BitLocker-Verschlüsselung über DM-Crypt-Verschlüsselung aktiviert wurde, muss die lokale verschlüsselte VHD in Ihr Speicherkonto hochgeladen werden.

    Add-AzureRmVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>] 

### Hochladen des geheimen Schlüssels der Datenträgerverschlüsselung für den vorverschlüsselten virtuellen Computer in den Schlüsseltresor
Der zuvor abgerufene geheime Schlüssel der Datenträgerverschlüsselung muss als geheimer Schlüssel in den Schlüsseltresor hochgeladen werden.

#### Geheimer Schlüssel der Datenträgerverschlüsselung ohne Verschlüsselung per KEK
Verwenden Sie [Set-AzureKeyVaultSecret](https://msdn.microsoft.com/library/dn868050.aspx) , um den geheimen Schlüssel im Schlüsseltresor bereitzustellen. Im Falle eines virtuellen Windows-Computers wird die BEK-Datei als base64-Zeichenfolge codiert und dann mit dem Set-AzureKeyVaultSecret-Cmdlet in den Schlüsseltresor hochgeladen. Für Linux wird die Passphrase als base64-Zeichenfolge codiert und dann in den Schlüsseltresor hochgeladen. Stellen Sie außerdem sicher, dass die folgenden Tags festgelegt sind, während der geheime Schlüssel im Schlüsseltresor erstellt wird.

    "tags":
    {
       “DiskEncryptionKeyEncryptionAlgorithm”: “RSA-OAEP (optional)”
       "DiskEncryptionKeyFileName": "Bek file name (windows) or Passphrase filename (linux)"
    }

    param(
      [Parameter(Mandatory=$True)]
      [String]$BekFilePath = "C:\vm\nbox\2640EE52-41B3-426C-87B9-484232452CE4.BEK",
      [String]$VaultName = "DiskEncryptionTestAus",
      [String]$SecretName = "BitLockerKey"
      ) 

    #"EAN//ojeIQk="
    $bekFileName = split-path $BekFilePath -leaf
    echo "Bek file name = $bekFileName"

    $secretBytes = [System.IO.File]::ReadAllBytes($BekFilePath);
    $secret = [Convert]::ToBase64String($secretBytes);
    echo "Secret = $secret"

    $secureSecret = ConvertTo-SecureString $secret -AsPlainText -Force
    $tags = @{"DiskEncryptionKeyFileName" = "$bekFileName"} 

    echo "Tags = $tags"
    echo "Vault = $VaultName"
    echo "Secret name = $SecretName"
    echo "Adding secret to Key vault"

    Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secureSecret -tags $tags


#### Geheimer Schlüssel der Datenträgerverschlüsselung mit Verschlüsselung per KEK

Der geheime Schlüssel kann optional mit einem Schlüsselverschlüsselungsschlüssel verschlüsselt werden, bevor er in den Schlüsseltresor hochgeladen wird. Verwenden Sie die Wrapper-[API](https://msdn.microsoft.com/library/azure/dn878066.aspx), um den geheimen Schlüssel zuerst mit dem Schlüsselverschlüsselungsschlüssel zu verschlüsseln. Die Ausgabe dieses Wrapper-Vorgangs ist eine base64-codierte Zeichenfolge, die dann als geheimer Schlüssel mit dem [Set-AzureKeyVaultSecret](https://msdn.microsoft.com/library/dn868050.aspx)-Cmdlet hochgeladen wird.


##Diese Anleitung herunterladen
Sie können diese Anleitung aus dem [TechNet-Katalog](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0) herunterladen.


## Weitere Informationen
[Explore Azure Disk Encryption with Azure PowerShell](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/16/explore-azure-disk-encryption-with-azure-powershell.aspx?wa=wsignin1.0)

[Explore Azure Disk Encryption with Azure PowerShell - Part 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx)

<!---HONumber=AcomDC_0204_2016-->