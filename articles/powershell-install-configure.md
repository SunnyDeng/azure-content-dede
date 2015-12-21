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
	ms.date="12/02/2015"
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
Für die Cmdlets ist Ihr Abonnement erforderlich, damit Ihre Dienste verwaltet werden können. Sie können ein Azure-Abonnement erwerben, falls Sie noch keines besitzen. Eine Anleitung finden Sie unter [Azure erwerben](http://go.microsoft.com/fwlink/p/?LinkId=320795).

1. Geben Sie **Add-AzureAccount** ein.

2. Geben Sie die dem Konto zugeordnete E-Mail-Adresse und das zugehörige Kennwort ein. Die Anmeldeinformationen werden von Azure authentifiziert und gespeichert, dann wird das Fenster geschlossen.

--ODER--

Melden Sie sich mit Ihrem Geschäfts- oder Schulkonto an:

        $cred = Get-Credential
        Add-AzureAccount -Credential $cred

> [AZURE.NOTE]Diese nicht-interaktive Anmeldemethode funktioniert nur mit einem Geschäfts- oder Schulkonto. Bei einem Geschäfts- oder Schulkonto handelt es sich um ein von Ihrem Unternehmen bzw. Ihrer Bildungseinrichtung verwaltetes Benutzerkonto, das in der entsprechenden Azure Active Directory-Instanz definiert ist. Wenn Sie derzeit kein Geschäfts- oder Schulkonto besitzen und sich mit einem Microsoft-Konto bei Ihrem Azure-Abonnement anmelden, können Sie mit den folgenden Schritten auf einfache Weise ein solches Konto erstellen.

> 1. Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com) an, und klicken Sie auf **Active Directory**.

> 2. Wenn kein Verzeichnis vorhanden ist, wählen Sie **Create your directory** (Verzeichnis erstellen) aus, und stellen Sie die geforderten Informationen bereit.

> 3. Wählen Sie Ihr Verzeichnis aus, und fügen Sie einen neuen Benutzer hinzu. Mit diesem neuen Benutzer können Sie sich über ein Geschäfts- oder Schulkonto anmelden. Während der Erstellung des Benutzers erhalten Sie sowohl eine E-Mail-Adresse für den Benutzer als auch ein temporäres Kennwort. Speichern Sie diese Informationen, da sie in Schritt 5 weiter unten benötigt werden.

> 4. Wählen Sie im Verwaltungsportal **Einstellungen** und anschließend **Administratoren** aus. Wählen Sie **Hinzufügen** aus, und fügen Sie den neuen Benutzer als Co-Administrator hinzu. Dies ermöglicht es dem Geschäfts- oder Schulkonto, Ihr Azure-Abonnement zu verwalten.

> 5. Melden Sie sich schließlich vom Azure-Portal ab und dann unter Verwendung des Geschäfts- oder Schulkontos erneut beim Azure-Portal an. Wenn Sie sich zu diesem Zeitpunkt zum ersten Mal mit diesem Konto anmelden, werden Sie aufgefordert, das Kennwort zu ändern.

> Weitere Informationen zur Anmeldung bei Microsoft Azure mit einem Geschäfts- oder Schulkonto finden Sie unter [Anmelden bei Microsoft Azure als Organisation](sign-up-organization.md).

### Anzeigen von Konto- und Abonnementinformationen

Sie können über mehrere Konten und Abonnements zur Verwendung durch Azure PowerShell verfügen Sie können mehrere Konten hinzufügen, indem Sie **Add-AzureAccount** mehrmals ausführen.

Geben Sie zum Anzeigen der verfügbaren Azure-Konten **Get-AzureAccount** ein.

Um Ihre Azure-Abonnements anzuzeigen, geben Sie **Get-AzureSubscription** ein.

## Schritt 4: Testen<a id="Ex"></a>


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

<!---HONumber=AcomDC_1210_2015-->