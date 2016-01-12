## Vorbereiten auf die Integration des Azure-Schlüsseltresors
Es müssen mehrere Voraussetzungen erfüllt sein, damit Sie die Azure-Schlüsseltresor-Integration zum Konfigurieren Ihres virtuellen SQL Server-Computers verwenden können:

1.	[Installieren von Azure Powershell](#install-azure-powershell)
2.	[Erstellen von Azure Active Directory](#create-an-azure-active-directory)
3.	[Erstellen eines Schlüsseltresors](#create-a-key-vault)

In den folgenden Abschnitten werden diese erforderlichen Komponenten und die Informationen beschrieben, die Sie ermitteln müssen, um die PowerShell-Cmdlets später ausführen zu können.

### Installieren von Azure PowerShell
Stellen Sie sicher, dass Sie das aktuelle Azure PowerShell SDK installiert haben. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../articles/powershell-install-configure.md).

### Erstellen von Azure Active Directory
Zunächst benötigen Sie für Ihr Abonnement [Azure Active Directory](https://azure.microsoft.com/trial/get-started-active-directory/) (AAD). Neben vielen weiteren Vorteilen erhalten Sie hiermit die Möglichkeit, für bestimmte Benutzer und Anwendungen die Berechtigung für Ihren Schlüsseltresor zu vergeben.

Registrieren Sie als Nächstes eine Anwendung für AAD. So erhalten Sie ein Dienstprinzipalkonto, das über Zugriff auf den Schlüsseltresor verfügt. Dieser Zugriff ist für Ihren virtuellen Computer erforderlich. Im Artikel zum Azure-Schlüsseltresor finden Sie diese Schritte im Abschnitt [Registrieren einer Anwendung unter Azure Active Directory](../articles/key-vault/key-vault-get-started.md#register), oder Sie können die Schritte mit Screenshots unter **Abrufen einer Identität für den Anwendungsabschnitt** (in englischer Sprache) in [diesem Blogbeitrag](http://blogs.technet.com/b/kv/archive/2015/01/09/azure-key-vault-step-by-step.aspx) verfolgen. Vor dem Ausführen der Schritte müssen Sie während der Registrierung die folgenden Informationen ermitteln, da sie diese später beim Aktivieren der Azure-Schlüsseltresor-Integration auf Ihrem virtuellen SQL-Computer benötigen.

- Suchen Sie nach dem Hinzufügen der Anwendung auf der Registerkarte **KONFIGURIEREN** nach der **CLIENT-ID**. ![Azure Active Directory-Client-ID](./media/virtual-machines-sql-server-akv-prepare/aad-client-id.png)
	
	Die Client-ID wird später dem Parameter **$spName** (Dienstprinzipalname) im PowerShell-Skript zugewiesen, um die Azure-Schlüsseltresor-Integration zu ermöglichen. 
- Kopieren Sie beim Ausführen dieser Schritte während der Schlüsselerstellung den „geheimen Schlüssel“ (Secret) für den Schlüssel, wie dies im folgenden Screenshot dargestellt ist. Dieser geheime Schlüssel des Schlüssels wird später dem Parameter **$spSecret** (geheimer Schlüssel des Dienstprinzipals) im PowerShell-Skript zugewiesen. ![Geheimer Azure Active Directory-Schlüssel](./media/virtual-machines-sql-server-akv-prepare/aad-sp-secret.png)
- Sie müssen diese neue Client-ID autorisieren, damit sie über die folgenden Zugriffsberechtigungen verfügt: **encrypt**, **decrypt**, **wrapKey**, **unwrapKey**, **sign** und **verify**. Hierfür wird das [Set-AzureRmKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/mt603625.aspx)-Cmdlet verwendet. Weitere Informationen finden Sie unter [Autorisieren der Anwendung zum Verwenden des Schlüssels oder geheimen Schlüssels](../articles/key-vault/key-vault-get-started.md#authorize).

### Erstellen eines Schlüsseltresors
Um den Azure-Schlüsseltresor zum Speichern der Schlüssel zu verwenden, die Sie für die Verschlüsselung auf Ihrem virtuellen Computer nutzen, benötigen Sie Zugriff auf den Schlüsseltresor. Wenn Sie den Schlüsseltresor noch nicht eingerichtet haben, können Sie die Schritte im Thema [Erste Schritte mit dem Azure-Schlüsseltresor](../articles/key-vault/key-vault-get-started.md) ausführen. Vor dem Ausführen der Schritte müssen Sie während dieses Setups einige Informationen ermitteln, da sie diese später beim Aktivieren der Azure-Schlüsseltresor-Integration auf Ihrem virtuellen SQL-Computer benötigen.

Achten Sie beim Schritt zum „Erstellen eines Schlüsseltresors“ auf die zurückgegebene **vaultUri**-Eigenschaft. Hierbei handelt es sich um die Schlüsseltresor-URL. Im Beispiel dieses Schritts (siehe unten) lautet der Name des Schlüsseltresors „ContosoKeyVault“, sodass sich für den Schlüsseltresor die URL https://contosokeyvault.vault.azure.net/ ergibt.

	New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

Die Schlüsseltresor-URL wird später dem Parameter **$akvURL** im PowerShell-Skript zugewiesen, um die Azure-Schlüsseltresor-Integration zu ermöglichen.

<!---HONumber=AcomDC_1223_2015-->