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
	ms.date="01/06/2016"
	ms.author="coreyp"/>

# Installieren und Konfigurieren von Azure PowerShell#

<div class="dev-center-tutorial-selector sublanding"><a href="/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/manage/install-and-configure-cli/" title="Azure-Befehlszeilenschnittstelle">Azure-Befehlszeilenschnittstelle</a></div>

##Was ist Azure PowerShell?#
Azure PowerShell ist ein Modul, das Cmdlets zum Verwalten von Azure mit Windows PowerShell enthält. Sie können diese Cmdlets verwenden, um durch die Azure-Plattform bereitgestellte Lösungen und Dienste zu erstellen, zu testen, bereitzustellen und zu verwalten. In den meisten Fällen können die Cmdlets für die gleichen Aufgaben wie das Azure-Verwaltungsportal verwendet werden, z. B. zum Erstellen und Konfigurieren von Clouddiensten, virtuellen Maschinen, virtuellen Netzwerken und Web-Apps.

<a id="Install"></a>
## Schritt 1: Installieren

Es folgen die beiden Methoden, mit denen Sie Azure PowerShell installieren können. Sie können sie entweder per WebPI oder über den PowerShell-Katalog installieren:

> [AZURE.NOTE]Unter Umständen müssen Sie nach der Installation einen Neustart durchführen, um alle Befehle in der Windows PowerShell Integrated Scripting Environment (ISE) anzuzeigen.

###Installieren von Azure PowerShell per WebPI

Die Installation von Azure PowerShell 1.0 und höher per WebPI ist mit der Installation der Version 0.9.x identisch. Laden Sie [Azure Powershell](http://aka.ms/webpi-azps) herunter, und starten Sie die Installation. Falls Sie Azure PowerShell 0.9.x installiert haben, werden Sie aufgefordert, diese Version zu deinstallieren. Wenn Sie Azure PowerShell-Module aus dem PowerShell-Katalog installiert haben, müssen Sie vor der Installation die Module entfernen, um die Einheitlichkeit der Azure PowerShell-Umgebung sicherzustellen.

> [AZURE.NOTE]Falls Sie die Azure-Module aus dem PowerShell-Katalog installiert haben, werden Sie aufgefordert, diese zu deinstallieren. Dies soll Verwirrung in Bezug darauf vermeiden, welche Module Sie installiert haben und wo sich diese befinden. Module des PowerShell-Katalogs werden normalerweise unter **%Programme%\\WindowsPowerShell\\Modules** installiert. Im Gegensatz dazu installiert das WebPI-Installationsprogramm die Azure-Module unter **%Programme%\\Microsoft SDKs\\Azure\\PowerShell**. **PowerShellGet** deinstalliert Module und lässt DLL-Dateien und deren Ordner zurück, wenn bei der Deinstallation eine Modulabhängigkeit geladen wird. Wenn Sie die Module des PowerShell-Katalogs deinstalliert haben und den Fehler beim Installieren weiterhin erhalten, sollten Sie die Azure*-Ordner im Ordner **%Programme%\\WindowsPowerShell\\Modules** entfernen.

Falls Sie Azure PowerShell über den PowerShell-Katalog installiert haben und stattdessen die WebPI-Installation verwenden möchten, können Sie vor der WebPI-Installation die unten angegebenen Befehle ausführen.

    # Uninstall the AzureRM component modules
    Uninstall-AzureRM

    # Uninstall AzureRM module
    Uninstall-Module AzureRM

    # Uninstall the Azure module
    Uninstall-Module Azure

    # Or, you can remove all Azure modules
    # Uninstall-Module Azure* -Force

> [AZURE.NOTE]Es gibt ein bekanntes Problem beim PowerShell-Element **$env:PSModulePath**, das bei der Installation per WebPI auftritt. Wenn Ihr Computer einen Neustart aufgrund von Systemupdates oder anderen Installationen benötigt, kann dies dazu führen, dass **$env:PSModulePath** nicht den Pfad enthält, unter dem Azure PowerShell installiert ist. Dies kann behoben werden, indem ein Neustart des Computers durchgeführt oder der Azure PowerShell-Pfad **$env:PSModulePath** hinzugefügt wird.

###Installieren von Azure PowerShell aus dem Katalog

Installieren Sie Azure PowerShell 1.0 oder höher mit den folgenden Befehlen aus dem Katalog:

    # Install the Azure Resource Manager modules from the PowerShell Gallery
    Install-Module AzureRM
    Install-AzureRM

    # Install the Azure Service Management module from the PowerShell Gallery
    Install-Module Azure

    # Import AzureRM modules for the given version manifest in the AzureRM module
    Import-AzureRM

    # Import Azure Service Management module
    Import-Module Azure

####Weitere Informationen zu diesen Befehlen

- Mit **Install-Module AzureRM** wird ein Bootstrappingmodul für die AzureRM-Module installiert. Dieses Modul enthält Cmdlets, die das Aktualisieren, Deinstallieren und Importieren der AzureRM-Module auf sichere und einheitliche Weise unterstützen. Das AzureRM-Modul enthält eine Liste mit Modulen und dem Versionsbereich (Min. und Max.), der erforderlich ist, um sicherzustellen, dass für die Hauptversion von AzureRM keine beeinträchtigenden Änderungen eingeführt werden. Weitere Informationen zu semantischen Versionsverwaltung finden Sie unter [semver.org](http://semver.org). Dies bedeutet, dass Sie Ihre Cmdlets mit einer speziellen Version von AzureRM erstellen und sicher sein können, dass für alle Module, die über den Bootstrapper installiert werden, keine beeinträchtigenden Änderungen eingeführt werden.
- **Install-AzureRM** installiert alle Module, die im Bootstrappingmodul deklariert werden.
- **Install-Module Azure** installiert das Azure-Modul. Dieses Modul ist das Service Management-Modul aus Azure PowerShell 0.9.x. Hierfür sollten keine größeren Änderungen gelten, und es sollte gegenüber der vorherigen Version des Azure-Moduls austauschbar sein.
- **Import-AzureRM** importiert alle Module, die in der Liste des AzureRM-Moduls mit den Modulen und Versionen enthalten sind. So wird sichergestellt, dass die geladenen Azure PowerShell-Module innerhalb des Versionsbereichs liegen, der für das AzureRM-Modul erforderlich ist.
- **Import-Module Azure** importiert das Azure Service Management-Modul. Hinweis: Das Azure-Modul und die AzureRM-Module werden in Ihre PowerShell-Sitzung geladen und können zusammen verwendet werden.


## Schritt 2: Starten
Das Modul installiert eine benutzerdefinierte Konsole für Azure PowerShell. Sie können die Cmdlets über die Windows PowerShell-Standardkonsole oder über die Azure PowerShell-Konsole ausführen. Die Methode, die Sie zum Öffnen einer der beiden Konsolen verwenden, ist abhängig von der ausgeführten Windows-Version:

- Auf einem Computer, auf dem mindestens Windows 8 oder Windows Server 2012 ausgeführt wird, können Sie die integrierte Suche verwenden. Beginnen Sie auf dem **Start**bildschirm mit der Eingabe von „power“. Daraufhin wird eine entsprechende Liste von Anwendungen zurückgegeben, die auch Windows PowerShell und Azure PowerShell umfasst. Klicken Sie auf eine dieser beiden Anwendungen, um die Konsole zu öffnen. (Klicken Sie mit der rechten Maustaste auf das Symbol, um die Anwendung an den **Start**bildschirm anzuheften.)

- Verwenden Sie auf einem Computer, auf dem eine niedrigere Version als Windows 8 oder Windows Server 2012 ausgeführt wird, das Menü **Start**. Klicken Sie im Menü **Start** auf **Alle Programme** und dann auf **Azure** und **Azure PowerShell**.

Sie können auch die **Windows PowerShell ISE** ausführen, um Menüelemente und Tastenkombinationen zum Durchführen vieler Aufgaben zu nutzen, die Sie auch in der Windows PowerShell-Konsole durchführen würden. Geben Sie zum Verwenden der ISE in der Windows PowerShell-Konsole „Cmd.exe“ ein, oder geben Sie im Feld **Ausführen** den Befehl **powershell\_ise.exe** ein.

###Befehle als Starthilfe

    # To make sure the Azure PowerShell module is available after you install
    Get-Module –ListAvailable 
	
	# If the Azure PowerShell module is not listed when you run Get-Module, you may need to import it
    Import-Module Azure 
	
    # To login to Azure Resource Manager
    Login-AzureRmAccount

    # You can also use a specific Tenant if you would like a faster login experience
    # Login-AzureRmAccount -TenantId xxxx

    # To view all subscriptions for your account
    Get-AzureRmSubscription

    # To select a default subscription for your current session
    Get-AzureRmSubscription –SubscriptionName “your sub” | Select-AzureRmSubscription

    # View your current Azure PowerShell session context
    # This session state is only applicable to the current session and will not affect other sessions
    Get-AzureRmContext

    # To select the default storage context for your current session
    Set-AzureRmCurrentStorageAccount –ResourceGroupName “your resource group” –StorageAccountName “your storage account name”

    # View your current Azure PowerShell session context
    # Note: the CurrentStorageAccount is now set in your session context
    Get-AzureRmContext

    # To import the Azure.Storage data plane module (blob, queue, table)
    Import-Module Azure.Storage

    # To list all of the blobs in all of your containers in all of your accounts
    Get-AzureRmStorageAccount | Get-AzureStorageContainer | Get-AzureStorageBlob


## Schritt 3: Verbinden
Für die Cmdlets ist Ihr Abonnement erforderlich, damit Ihre Dienste verwaltet werden können. Sie können ein Azure-Abonnement erwerben, falls Sie noch keines besitzen. Eine Anleitung finden Sie unter [Azure erwerben](http://go.microsoft.com/fwlink/p/?LinkId=320795).

1. Geben Sie **Login-AzureRmAccount** ein.

2. Geben Sie die dem Konto zugeordnete E-Mail-Adresse und das zugehörige Kennwort ein. Die Anmeldeinformationen werden von Azure authentifiziert und gespeichert, dann wird das Fenster geschlossen.

--ODER--

Melden Sie sich mit Ihrem Geschäfts- oder Schulkonto an:

    $cred = Get-Credential
    Login-AzureRmAccount -Credential $cred
> [AZURE.NOTE]Wenn Sie Ihrem Unternehmenskonto mehrere Mandanten zugeordnet haben, geben Sie den Parameter „TenantId“ ein:

    $loadersubscription = Get-AzureRmSubscription -SubscriptionName $YourSubscriptionName -TenantId $YourAssociatedSubscriptionTenantId


> [AZURE.NOTE]Diese nicht-interaktive Anmeldemethode funktioniert nur mit einem Geschäfts- oder Schulkonto. Bei einem Geschäfts- oder Schulkonto handelt es sich um ein von Ihrem Unternehmen bzw. Ihrer Bildungseinrichtung verwaltetes Benutzerkonto, das in der entsprechenden Azure Active Directory-Instanz definiert ist. Wenn Sie derzeit kein Geschäfts- oder Schulkonto besitzen und sich mit einem Microsoft-Konto bei Ihrem Azure-Abonnement anmelden, können Sie mit den folgenden Schritten auf einfache Weise ein solches Konto erstellen.

> 1. Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com) an, und klicken Sie auf **Active Directory**.

> 2. Wenn kein Verzeichnis vorhanden ist, wählen Sie **Create your directory** (Verzeichnis erstellen) aus, und stellen Sie die geforderten Informationen bereit.

> 3. Wählen Sie Ihr Verzeichnis aus, und fügen Sie einen neuen Benutzer hinzu. Mit diesem neuen Benutzer können Sie sich über ein Geschäfts- oder Schulkonto anmelden. Während der Erstellung des Benutzers erhalten Sie sowohl eine E-Mail-Adresse für den Benutzer als auch ein temporäres Kennwort. Speichern Sie diese Informationen, da sie in Schritt 5 weiter unten benötigt werden.

> 4. Wählen Sie im Verwaltungsportal **Einstellungen** und anschließend **Administratoren** aus. Wählen Sie **Hinzufügen** aus, und fügen Sie den neuen Benutzer als Co-Administrator hinzu. Dies ermöglicht es dem Geschäfts- oder Schulkonto, Ihr Azure-Abonnement zu verwalten.

> 5. Melden Sie sich schließlich vom Azure-Portal ab und dann unter Verwendung des Geschäfts- oder Schulkontos erneut beim Azure-Portal an. Wenn Sie sich zu diesem Zeitpunkt zum ersten Mal mit diesem Konto anmelden, werden Sie aufgefordert, das Kennwort zu ändern.

> Weitere Informationen zur Anmeldung bei Microsoft Azure mit einem Geschäfts- oder Schulkonto finden Sie unter [Anmelden bei Microsoft Azure als Organisation](sign-up-organization.md).

> Weitere Informationen zur Authentifizierungs- und Abonnemontverwaltung in Azure finden Sie unter [Verwalten von Konten, Abonnements und Administratorrollen](http://go.microsoft.com/fwlink/?LinkId=324796).

### Anzeigen von Konto- und Abonnementinformationen

Sie können über mehrere Konten und Abonnements zur Verwendung durch Azure PowerShell verfügen Sie können mehrere Konten hinzufügen, indem Sie **Add-AzureRmAccount** mehrmals ausführen.

Geben Sie zum Anzeigen der verfügbaren Azure-Konten **Get-AzureAccount** ein.

Um Ihre Azure-Abonnements anzuzeigen, geben Sie **Get-AzureRmSubscription** ein.

##<a id="Help"></a>Hilfe##

Diese Ressourcen enthalten Hilfethemen für bestimmte Cmdlets:


-   Sie können in der Konsole das integrierte Hilfesystem verwenden. Das Cmdlet **Get-Help** ermöglicht den Zugriff auf dieses System. 

- Hilfe von der Community erhalten Sie in den folgenden beliebten Foren:

 - [Azure-Forum auf MSDN](http://go.microsoft.com/fwlink/p/?LinkId=320212)
 - [Stackoverflow](http://go.microsoft.com/fwlink/?LinkId=320213)

##Weitere Informationen


Weitere Informationen zur Verwendung von Cmdlets finden Sie unter den folgenden Ressourcen:

Einfache Anleitungen zur Verwendung von Windows PowerShell finden Sie unter [Verwenden von Windows PowerShell](http://go.microsoft.com/fwlink/p/?LinkId=321939).

Referenzinformationen zu den Cmdlets finden Sie unter [Azure-Cmdlet-Referenz](https://msdn.microsoft.com/library/windowsazure/jj554330.aspx).

Beispielskripts und Anleitungen als Unterstützung beim Erlernen der Skripterstellung zum Verwalten von Azure finden Sie im [Script Center](http://go.microsoft.com/fwlink/p/?LinkId=321940).

<!---HONumber=AcomDC_0107_2016-->