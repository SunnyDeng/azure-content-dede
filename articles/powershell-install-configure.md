<properties
	pageTitle="Installieren und Konfigurieren von Azure PowerShell"
	description="Erfahren Sie, wie Sie Azure PowerShell installieren und konfigurieren."
	editor="tysonn"
	manager="stevenka"
	documentationCenter=""
	services=""
	authors="coreyp-at-msft"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="powershell"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/01/2015"
	ms.author="coreyp"/>

# Installieren und Konfigurieren von Azure PowerShell#

<div class="dev-center-tutorial-selector sublanding"><a href="/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/manage/install-and-configure-cli/" title="Azure-Befehlszeilenschnittstelle">Azure-Befehlszeilenschnittstelle</a></div>

##Was ist Azure PowerShell?#
Azure PowerShell ist ein Modul, das Cmdlets zum Verwalten von Azure mit Windows PowerShell enthält. Sie können diese Cmdlets verwenden, um durch die Azure-Plattform bereitgestellte Lösungen und Dienste zu erstellen, zu testen, bereitzustellen und zu verwalten. In den meisten Fällen können die Cmdlets für die gleichen Aufgaben wie das Azure-Verwaltungsportal verwendet werden, z. B. zum Erstellen und Konfigurieren von Clouddiensten, virtuellen Maschinen, virtuellen Netzwerken und Web-Apps.

Das Modul und der Quellcode stehen in einem öffentlich zugänglichen Repository zum Download zur Verfügung:

- [PowerShell 1.0.1](https://github.com/Azure/azure-powershell/releases/download/v1.0.1-November2015/azure-powershell.1.0.1.msi)
- [Azure PowerShell 1.0.1-Quellcode](https://github.com/Azure/azure-powershell/archive/v1.0.1-November2015.zip)

<a id="Install"></a>
## Schritt 1: Installieren
Laden Sie [PowerShell 1.0.1](https://github.com/Azure/azure-powershell/releases/download/v1.0.1-November2015/azure-powershell.1.0.1.msi) herunter und führen Sie die Installation durch. <a id="Connect"></a>

## Schritt 2: Starten
Das Modul installiert eine benutzerdefinierte Konsole für Azure PowerShell. Sie können die Cmdlets über die Windows PowerShell-Standardkonsole oder über die Azure PowerShell-Konsole ausführen.

## Schritt 3: Verbinden
Für die Cmdlets ist Ihr Abonnement erforderlich, damit Ihre Dienste verwaltet werden können. Sie können ein Azure-Abonnement erwerben, falls Sie noch keines besitzen. Anweisungen hierzu finden Sie unter [Erste Schritte mit Azure](http://go.microsoft.com/fwlink/p/?LinkId=320795).

1. Typ Add-AzureAccount

2. Geben Sie die dem Konto zugeordnete E-Mail-Adresse und das zugehörige Kennwort ein. Die Anmeldeinformationen werden von Azure authentifiziert und gespeichert, dann wird das Fenster geschlossen.

--ODER--

Melden Sie sich mit Ihrem Geschäfts- oder Schulkonto an:

        $cred = Get-Credential
        Add-AzureAccount -Credential $cred

	>
	> [AZURE.NOTE] This non-interactive login method only works with a work or school account. A work or school account is a user that is managed by your work or school, and defined in the Azure Active Directory instance for your work or school. If you do not currently have a work or school account, and are using a Microsoft account to log in to your Azure subscription, you can easily create one using the following steps.
	>
	> 1. Login to the [Azure Management Portal](https://manage.windowsazure.com), and click on **Active Directory**.
	>
	> 2. If no directory exists, select **Create your directory** and provide the requested information.
	>
	> 3. Select your directory and add a new user. This new user can sign in using a work or school account.
	>
	>     During the creation of the user, you will be supplied with both an e-mail address for the user and a temporary password. Save this  information as it is used in another step.
	>
	> 4. From the management portal, select **Settings** and then select **Administrators**. Select **Add**, and add the new user as a co-administrator. This allows the work or school account to manage your Azure subscription.
	>
	> 5. Finally, log out of the Azure portal and then log back in using the work or school account. If this is the first time logging in with this account, you will be prompted to change the password.
	>
	>For more information on signing up for Microsoft Azure with a work or school account, see [Sign up for Microsoft Azure as an Organization](sign-up-organization.md).


### Anzeigen von Konto- und Abonnementinformationen

Sie können über mehrere Konten und Abonnements zur Verwendung durch Azure PowerShell verfügen Sie können mehrere Konten hinzufügen, indem Sie **Add-AzureAccount** mehr als einmal ausführen.

Geben Sie Folgendes ein, um die verfügbaren Azure-Konten abzurufen:

	Get-AzureAccount

Geben Sie Folgendes ein, um Ihre Azure-Abonnements abzurufen:

	Get-AzureSubscription








## Schritt 4: Testen<a id="Ex"></a>


Nachdem Sie das Modul installiert und die Verbindung zwischen Ihrem Computer und dem Abonnement konfiguriert haben, können Sie eine Azure-Web-App erstellen, um zu testen, ob alles funktioniert.

1. Starten Sie die Azure PowerShell-Konsole.

2. Geben Sie einen Namen für die Web-App ein. Dieser Name muss den DNS-Benennungskonventionen entsprechen. Gültige Namen dürfen nur Buchstaben von a bis z, Ziffern von 0 bis 9 und Bindestriche (-) enthalten.

	Der Name der Web-App muss in Azure eindeutig sein. In diesem Beispiel wird "mySite" verwendet. Wählen Sie selbst aber einen anderen Namen, beispielsweise Ihren Kontonamen, gefolgt von einer Nummer.

	Geben Sie einen Befehl wie den folgenden ein, nachdem Sie einen Namen ausgewählt haben. Ersetzen Sie "mySite" durch den von Ihnen ausgewählten Web-App-Namen.

		New-AzureWebsite mySite

	Die Web-App wird erstellt, und es wird ein Objekt zurückgegeben, das die neue Web-App darstellt. Die Objekteigenschaften enthalten nützliche Informationen zu der Web-App.

3. Um Informationen zu der Web-App zu erhalten, geben Sie folgenden Befehl ein. Dadurch werden einige Informationen zu allen Web-Apps im Abonnement, darunter diejenige, die Sie gerade erstellt haben, zurückgegeben.

		Get-AzureWebsite

4. Wenn Sie weitere Informationen zu Ihrer Web-App erhalten möchten, nehmen Sie den Namen der Web-App in den Befehl auf. Achten Sie darauf, "mySite" durch den Namen Ihrer Web-App zu ersetzen.

		Get-AzureWebsite -Name mySite

5. Web-Apps werden nach der Erstellung gestartet. Geben Sie folgenden Befehl einschließlich des Namens Ihrer Web-App ein, um die Web-App zu beenden.

		Stop-AzureWebsite -Name mySite

6. Um zu überprüfen, ob die Website beendet wurde, führen Sie den Befehl "Get-AzureWebsite" erneut aus.

		Get-AzureWebsite

7. Löschen Sie die Web-App, um diesen Test abzuschließen. Geben Sie Folgendes ein:

		Remove-AzureWebsite -Name mySite

7. Überprüfen Sie, ob die Web-App gelöscht wurde, um die Aufgabe abzuschließen.

		Get-AzureWebsite -Name mySite

##<a id="Help"></a>Hilfe##

Diese Ressourcen enthalten Hilfethemen für bestimmte Cmdlets:


-   Sie können in der Konsole das integrierte Hilfesystem verwenden. Das Cmdlet **Get-Help** ermöglicht den Zugriff auf dieses System. 



- Referenzinformationen zu den Cmdlets in den Azure PowerShell-Modulen sind in der Azure-Bibliothek verfügbar. Informationen finden Sie in der [Azure-Cmdlet-Referenz](http://msdn.microsoft.com/library/windowsazure/jj554330.aspx).

Hilfe von der Community erhalten Sie in den folgenden beliebten Foren:

- [Azure-Forum auf MSDN](http://go.microsoft.com/fwlink/p/?LinkId=320212)
- [Stackoverflow](http://go.microsoft.com/fwlink/?LinkId=320213)

<!---HONumber=AcomDC_1203_2015-->