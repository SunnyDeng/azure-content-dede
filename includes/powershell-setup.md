
## Einrichten von PowerShell

Bevor Sie Azure PowerShell verwenden können, müssen Sie die folgenden Schritte durchführen.

### Überprüfen der PowerShell-Versionen

Bevor Sie Windows PowerShell verwenden können, müssen Sie Windows PowerShell, Version 3.0 oder 4.0, installieren: Um die Version von Windows PowerShell zu finden, geben Sie diesen Befehl in eine Windows PowerShell-Eingabeaufforderung ein.

	$PSVersionTable

Die Ausgabe sollte folgendermaßen aussehen:

	Name                           Value
	----                           -----
	PSVersion                      3.0
	WSManStackVersion              3.0
	SerializationVersion           1.1.0.1
	CLRVersion                     4.0.30319.18444
	BuildVersion                   6.2.9200.16481
	PSCompatibleVersions           {1.0, 2.0, 3.0}
	PSRemotingProtocolVersion      2.2

Überprüfen Sie, dass der Wert der **PSVersion** 3.0 oder 4.0 ist. Informationen zum Installieren einer kompatiblen Version finden Sie unter [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) oder [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

Sie benötigen darüber hinaus Azure PowerShell, Version 0.8.0 oder höher. Sie können die von Ihnen installierte Azure Power Shell-Version mit diesem Befehl über die Azure PowerShell-Eingabeaufforderung prüfen.

	Get-Module azure | format-table version

Die Ausgabe sollte folgendermaßen aussehen:

	Version
	-------
	0.8.16.1

Anweisungen und einen Link zur neuesten Version finden Sie unter [Gewusst wie: Installieren und Konfigurieren von Azure PowerShell](powershell-install-configure.md).


### Festlegen Ihres Azure-Kontos und -Abonnements

Wenn Sie noch kein Abonnement haben, können Sie Ihre [MSDN-Abonnentenvorteile](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) aktivieren oder sich für eine [kostenlose Testversion](http://azure.microsoft.com/pricing/free-trial/) registrieren.

Öffnen Sie eine Azure PowerShell-Eingabeaufforderung und melden Sie sich mit diesem Befehl bei Azure an.

	Add-AzureAccount

Wenn Sie mehrere Azure-Abonnements haben, können Sie Ihre Azure-Abonnements mit diesem Befehl auflisten.

	Get-AzureSubscription

Sie erhalten den folgenden Informationstyp:

	SubscriptionId            : fd22919d-eaca-4f2b-841a-e4ac6770g92e
	SubscriptionName          : Visual Studio Ultimate with MSDN
	Environment               : AzureCloud
	SupportedModes            : AzureServiceManagement,AzureResourceManager
	DefaultAccount            : johndoe@contoso.com
	Accounts                  : {johndoe@contoso.com}
	IsDefault                 : True
	IsCurrent                 : True
	CurrentStorageAccountName : 
	TenantId                  : 32fa88b4-86f1-419f-93ab-2d7ce016dba7

Sie können das aktuelle Azure-Abonnement durch Ausführen dieser Befehle in der Azure PowerShell-Eingabeaufforderung festlegen. Ersetzen Sie alles in den Anführungszeichen, einschließlich der < and > Zeichen, durch die korrekten Namen.

	$subscr="<SubscriptionName from the display of Get-AzureSubscription>"
	Select-AzureSubscription -SubscriptionName $subscr -Current	

Weitere Informationen zu Azure-Abonnements und -Konten finden Sie unter [Verbindung mit Ihrem Abonnement](powershell-install-configure.md#Connect).

<!---HONumber=August15_HO6-->