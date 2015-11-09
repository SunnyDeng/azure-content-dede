<properties 
	pageTitle="Konfigurieren der Azure-Schlüsseltresor-Integration für SQL Server auf virtuellen Azure-Computern"
	description="Erfahren Sie, wie Sie die Konfiguration der SQL Server-Verschlüsselung zur Verwendung mit dem Azure-Schlüsseltresor automatisieren. In diesem Thema wird beschrieben, wie Sie die Azure-Schlüsseltresor-Integration mit virtuellen SQL Server-Computern verwenden." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="rothja" 
	manager="jeffreyg"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services" 
	ms.date="10/23/2015"
	ms.author="jroth"/>

# Konfigurieren der Azure-Schlüsseltresor-Integration für SQL Server auf virtuellen Azure-Computern

## Übersicht
Es gibt mehrere SQL Server-Verschlüsselungsfunktionen, z. B. [Transparent Data Encryption (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [Column Level Encryption (CLE)](https://msdn.microsoft.com/library/ms173744.aspx) und [Sicherungsverschlüsselung](https://msdn.microsoft.com/library/dn449489.aspx). Bei diesen Arten der Verschlüsselung müssen Sie die kryptografischen Schlüssel verwalten und speichern, die Sie für die Verschlüsselung verwenden. Der Azure-Schlüsseltresor-Dienst (Azure Key Vault, AKV) ist dafür ausgelegt, die Sicherheit und Verwaltung dieser Schlüssel an einem sicheren und hoch verfügbaren Speicherort zu verbessern. Mit dem [SQL Server-Connector](http://www.microsoft.com/download/details.aspx?id=45344) kann SQL Server diese Schlüssel aus dem Azure-Schlüsseltresor verwenden.

Wenn Sie SQL Server mit lokalen Computern ausführen, können Sie die [Schritte zum Zugreifen auf den Azure-Schlüsseltresor von Ihrem lokalen SQL Server-Computer ausführen](https://msdn.microsoft.com/library/dn198405.aspx). Sie können für SQL Server auf virtuellen Azure-Computern aber Zeit sparen, indem Sie die Funktion für die *Azure-Schlüsseltresor-Integration* verwenden. Mit einigen Azure PowerShell-Cmdlets zum Aktivieren dieser Funktion können Sie die Konfiguration automatisieren, die ein virtueller SQL-Computer zum Zugreifen auf Ihren Schlüsseltresor benötigt.

Wenn diese Funktion aktiviert ist, wird der SQL Server-Connector automatisch aktiviert und der Anbieter für erweiterbare Schlüsselverwaltung für den Zugriff auf den Azure-Schlüsseltresor konfiguriert. Außerdem werden die Anmeldeinformationen erstellt, um den Zugriff auf Ihren Tresor zu ermöglichen. Wenn Sie sich die Schritte in der oben erwähnten Dokumentation für die Ausführung auf lokalen Computern angesehen haben, haben Sie erfahren, dass die Schritte 2 und 3 mit dieser Funktion automatisiert werden. Der einzige Schritt, den Sie noch manuell ausführen müssen, ist die Erstellung des Schlüsseltresors und der Schlüssel. Ab diesem Punkt ist das gesamte Setup des virtuellen SQL-Computers automatisiert. Nachdem die Funktion dieses Setup abgeschlossen hat, können Sie die T-SQL-Anweisungen ausführen, um mit der Verschlüsselung Ihrer Datenbanken oder Backups zu beginnen, wie Sie dies normalerweise auch tun.

## Vorbereiten auf die Integration des Azure-Schlüsseltresors
Es müssen mehrere Voraussetzungen erfüllt sein, damit Sie die Azure-Schlüsseltresor-Integration zum Konfigurieren Ihres virtuellen SQL Server-Computers verwenden können:

1.	[Installieren von Azure Powershell](#install-azure-powershell)
2.	[Erstellen von Azure Active Directory](#create-an-azure-active-directory)
3.	[Erstellen eines Schlüsseltresors](#create-a-key-vault)

In den folgenden Abschnitten werden diese erforderlichen Komponenten und die Informationen beschrieben, die Sie ermitteln müssen, um die PowerShell-Cmdlets später ausführen zu können.

### Installieren von Azure PowerShell
Stellen Sie sicher, dass Sie das aktuelle Azure PowerShell SDK installiert haben. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md).

### Erstellen von Azure Active Directory
Zunächst benötigen Sie für Ihr Abonnement [Azure Active Directory](https://azure.microsoft.com/trial/get-started-active-directory/) (AAD). Neben vielen weiteren Vorteilen erhalten Sie hiermit die Möglichkeit, für bestimmte Benutzer und Anwendungen die Berechtigung für Ihren Schlüsseltresor zu vergeben.

Registrieren Sie als Nächstes eine Anwendung für AAD. So erhalten Sie ein Dienstprinzipalkonto, das über Zugriff auf den Schlüsseltresor verfügt. Dieser Zugriff ist für Ihren virtuellen Computer erforderlich. Im Artikel zum Azure-Schlüsseltresor finden Sie diese Schritte im Abschnitt [Registrieren einer Anwendung unter Azure Active Directory](../key-vault/key-vault-get-started.md#register), oder Sie können die Schritte mit Screenshots unter **Abrufen einer Identität für den Anwendungsabschnitt** (in englischer Sprache) in [diesem Blogbeitrag](http://blogs.technet.com/b/kv/archive/2015/01/09/azure-key-vault-step-by-step.aspx) verfolgen. Vor dem Ausführen der Schritte müssen Sie während der Registrierung die folgenden Informationen ermitteln, da sie diese später beim Aktivieren der Azure-Schlüsseltresor-Integration auf Ihrem virtuellen SQL-Computer benötigen.

- Suchen Sie nach dem Hinzufügen der Anwendung auf der Registerkarte **KONFIGURIEREN** nach der **CLIENT-ID**. ![Azure Active Directory-Client-ID](./media/virtual-machines-sql-server-azure-key-vault-integration/aad-client-id.png)
	
	Die Client-ID wird später dem Parameter **$spName** (Dienstprinzipalname) im PowerShell-Skript zugewiesen, um die Azure-Schlüsseltresor-Integration zu ermöglichen. 
- Kopieren Sie beim Ausführen dieser Schritte während der Schlüsselerstellung den „geheimen Schlüssel“ (Secret) für den Schlüssel, wie dies im folgenden Screenshot dargestellt ist. Dieser geheime Schlüssel des Schlüssels wird später dem Parameter **$spSecret** (geheimer Schlüssel des Dienstprinzipals) im PowerShell-Skript zugewiesen. ![Geheimer Azure Active Directory-Schlüssel](./media/virtual-machines-sql-server-azure-key-vault-integration/aad-sp-secret.png)
- Sie müssen diese neue Client-ID autorisieren, damit sie über die folgenden Zugriffsberechtigungen verfügt: **encrypt**, **decrypt**, **wrapKey**, **unwrapKey**, **sign** und **verify**. Hierfür wird das [Set-AzureKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/dn903607%28v=azure.98%29.aspx)-Cmdlet verwendet. Weitere Informationen finden Sie unter [Autorisieren der Anwendung zum Verwenden des Schlüssels oder geheimen Schlüssels](../key-vault/key-vault-get-started.md#authorize).

### Erstellen eines Schlüsseltresors
Um den Azure-Schlüsseltresor zum Speichern der Schlüssel zu verwenden, die Sie für die Verschlüsselung auf Ihrem virtuellen Computer nutzen, benötigen Sie Zugriff auf den Schlüsseltresor. Wenn Sie den Schlüsseltresor noch nicht eingerichtet haben, können Sie die Schritte im Thema [Erste Schritte mit dem Azure-Schlüsseltresor](../key-vault/key-vault-get-started.md) ausführen. Vor dem Ausführen der Schritte müssen Sie während dieses Setups einige Informationen ermitteln, da sie diese später beim Aktivieren der Azure-Schlüsseltresor-Integration auf Ihrem virtuellen SQL-Computer benötigen.

Achten Sie beim Schritt zum „Erstellen eines Schlüsseltresors“ auf die zurückgegebene **vaultUri**-Eigenschaft. Hierbei handelt es sich um die Schlüsseltresor-URL. Im Beispiel dieses Schritts (siehe unten) lautet der Name des Schlüsseltresors „ContosoKeyVault“, sodass sich für den Schlüsseltresor die URL https://contosokeyvault.vault.azure.net/ ergibt.

![Geheimer Azure Active Directory-Schlüssel](./media/virtual-machines-sql-server-azure-key-vault-integration/new-azurekeyvault.png)
 
Die Schlüsseltresor-URL wird später dem Parameter **$akvURL** im PowerShell-Skript zugewiesen, um die Azure-Schlüsseltresor-Integration zu ermöglichen.

## Konfigurieren der Integration des Azure-Schlüsseltresors
Verwenden Sie PowerShell zum Konfigurieren der Azure-Schlüsseltresor-Integration. Die folgenden Abschnitte enthalten eine Übersicht über die erforderlichen Parameter sowie ein PowerShell-Beispielskript.

### Eingabeparameter
In der folgenden Tabelle sind die Parameter aufgeführt, die zum Ausführen des PowerShell-Skripts im nächsten Abschnitt erforderlich sind.

|Parameter|Beschreibung|Beispiel|
|---|---|---|
|**$akvURL**|**Schlüsseltresor-URL**|"https://contosokeyvault.vault.azure.net/"|
|**$spName**|**Dienstprinzipalname**|"fde2b411-33d5-4e11-af04eb07b669ccf2"|
|**$spSecret**|**Geheimer Schlüssel des Dienstprinzipals**|"9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="|
|**$credName**|**Anmeldeinformationsname**: Die Azure-Schlüsseltresor-Integration erstellt Anmeldeinformationen in SQL Server, damit der virtuelle Computer Zugriff auf den Schlüsseltresor hat. Wählen Sie einen Namen für diese Anmeldeinformation.|"mycred1"|
|**$vmName**|**Name des virtuellen Computers**: Der Name eines bereits erstellten SQL-Computers.|"myvmname"|
|**$serviceName**|**Dienstname**: Der Clouddienstname, der dem virtuellen SQL-Computer zugeordnet ist.|"mycloudservicename"|

### Aktivieren der Azure-Schlüsseltresor-Integration mit PowerShell
Mit dem **New-AzureVMSqlServerKeyVaultCredentialConfig**-Cmdlet wird ein Konfigurationsobjekt für die Funktion „Azure-Schlüsseltresor-Integration“ erstellt. Mit **Set-AzureVMSqlServerExtension** wird diese Integration mit dem Parameter **KeyVaultCredentialSettings** konfiguriert. Die folgenden Schritte zeigen, wie Sie diese Befehle verwenden.

1. Konfigurieren Sie in Azure PowerShell zuerst die Eingabeparameter mit Ihren speziellen Werten, wie dies in den vorherigen Abschnitten dieses Themas beschrieben ist. Das folgende Skript ist ein Beispiel.
	
		$akvURL = "https://contosokeyvault.vault.azure.net/"
		$spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
		$spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
		$credName = "mycred1"
		$vmName = "myvmname"
		$serviceName = "mycloudservicename"
2.	Verwenden Sie anschließend das folgende Skript, um die Azure-Schlüsseltresor-Integration zu konfigurieren.
	
		$secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
		$akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
		Get-AzureVM –ServiceName $serviceName –Name $vmName | Set-AzureVMSqlServerExtension –KeyVaultCredentialSettings $akvs | Update-AzureVM

Über die SQL-IaaS-Agent-Erweiterung wird der virtuelle SQL-Computer mit dieser neuen Konfiguration aktualisiert.

## Nächste Schritte
Nach dem Aktivieren der Azure-Schlüsseltresor-Integration können Sie die SQL Server-Verschlüsselung auf Ihrem virtuellen SQL-Computer aktivieren. Zuerst müssen Sie in Ihrem Schlüsseltresor einen asymmetrischen Schlüssel und in SQL Server auf Ihrem virtuellen Computer einen symmetrischen Schlüssel erstellen. Sie können dann T-SQL-Anweisungen ausführen, um die Verschlüsselung für Ihre Datenbanken und Backups zu aktivieren.

Es gibt verschiedene Arten der Verschlüsselung, die Sie nutzen können:

- [Transparent Data Encryption (TDE)](https://msdn.microsoft.com/library/bb934049.aspx)
- [Verschlüsselte Sicherungen](https://msdn.microsoft.com/library/dn449489.aspx)
- [Column Level Encryption (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)

Die folgenden Transact-SQL-Skripts enthalten Beispiele für jeden dieser Bereiche.

>[AZURE.NOTE]Jedes Beispiel basiert auf diesen beiden erforderlichen Komponenten: einem asymmetrischen Schlüssel aus Ihrem Schlüsseltresor mit dem Namen **CONTOSO\_KEY** und Anmeldeinformationen, die mit der Funktion für die Azure-Schlüsseltresor-Integration erstellt wurden und den Namen **Azure\_EKM\_TDE\_cred** haben.

### Transparent Data Encryption (TDE)
1. Erstellen Sie eine SQL Server-Anmeldung, die vom Datenbankmodul für TDE verwendet werden kann, und fügen Sie die Anmeldeinformationen hinzu.
	
		USE master;
		-- Create a SQL Server login associated with the asymmetric key 
		-- for the Database engine to use when it loads a database 
		-- encrypted by TDE.
		CREATE LOGIN TDE_Login 
		FROM ASYMMETRIC KEY CONTOSO_KEY;
		GO
		
		-- Alter the TDE Login to add the credential for use by the 
		-- Database Engine to access the key vault
		ALTER LOGIN TDE_Login 
		ADD CREDENTIAL Azure_EKM_TDE_cred;
		GO
	
2. Erstellen Sie den Datenbankverschlüsselungsschlüssel, der für TDE verwendet werden soll.
	
		USE ContosoDatabase;
		GO
		
		CREATE DATABASE ENCRYPTION KEY 
		WITH ALGORITHM = AES_128 
		ENCRYPTION BY SERVER ASYMMETRIC KEY CONTOSO_KEY;
		GO
		
		-- Alter the database to enable transparent data encryption.
		ALTER DATABASE ContosoDatabase 
		SET ENCRYPTION ON;
		GO

### Verschlüsselte Sicherungen
1. Erstellen Sie eine SQL Server-Anmeldung, die vom Datenbankmodul für die Verschlüsselung von Sicherungen verwendet werden kann, und fügen Sie die Anmeldeinformationen hinzu.
	
		USE master;
		-- Create a SQL Server login associated with the asymmetric key 
		-- for the Database engine to use when it is encrypting the backup.
		CREATE LOGIN Backup_Login 
		FROM ASYMMETRIC KEY CONTOSO_KEY;
		GO 
		
		-- Alter the Encrypted Backup Login to add the credential for use by 
		-- the Database Engine to access the key vault
		ALTER LOGIN Backup_Login 
		ADD CREDENTIAL Azure_EKM_Backup_cred ;
		GO
	
2. Sichern Sie die Datenbank, indem Sie die Verschlüsselung mit dem asymmetrischen Schlüssel angeben, der im Schlüsseltresor gespeichert ist.
	
		USE master;
		BACKUP DATABASE [DATABASE_TO_BACKUP]
		TO DISK = N'[PATH TO BACKUP FILE]' 
		WITH FORMAT, INIT, SKIP, NOREWIND, NOUNLOAD, 
		ENCRYPTION(ALGORITHM = AES_256, SERVER ASYMMETRIC KEY = [CONTOSO_KEY]);
		GO

### Column Level Encryption (CLE)
Mit diesem Skript wird ein symmetrischer Schlüssel erstellt, der durch den asymmetrischen Schlüssel im Schlüsseltresor geschützt ist, und anschließend wird der symmetrische Schlüssel zum Verschlüsseln der Daten in der Datenbank verwendet.

	CREATE SYMMETRIC KEY DATA_ENCRYPTION_KEY
	WITH ALGORITHM=AES_256
	ENCRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;
	
	DECLARE @DATA VARBINARY(MAX);
	
	--Open the symmetric key for use in this session
	OPEN SYMMETRIC KEY DATA_ENCRYPTION_KEY 
	DECRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;
	
	--Encrypt syntax
	SELECT @DATA = ENCRYPTBYKEY(KEY_GUID('DATA_ENCRYPTION_KEY'), CONVERT(VARBINARY,'Plain text data to encrypt'));
	
	-- Decrypt syntax
	SELECT CONVERT(VARCHAR, DECRYPTBYKEY(@DATA));
	
	--Close the symmetric key
	CLOSE SYMMETRIC KEY DATA_ENCRYPTION_KEY;

## Zusätzliche Ressourcen
Weitere Informationen zur Verwendung dieser Verschlüsselungsfunktionen finden Sie unter [Verwenden der erweiterbaren Schlüsselverwaltung mit SQL Server-Verschlüsselungsfunktionen](https://msdn.microsoft.com/library/dn198405.aspx#UsesOfEKM).

Beachten Sie, dass bei den Schritten in diesem Artikel davon ausgegangen wird, dass bei Ihnen SQL Server bereits auf einem virtuellen Azure-Computer ausgeführt wird. Ist dies nicht der Fall, helfen Ihnen die Informationen unter [Bereitstellen eines virtuellen Computers mit SQL Server in Azure](virtual-machines-provision-sql-server.md) weiter. Weitere Informationen zum Ausführen von SQL Server auf virtuellen Azure-Computern finden Sie in der [Übersicht zu SQL Server auf virtuellen Azure-Computern](virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=Nov15_HO1-->