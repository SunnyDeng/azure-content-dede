<properties
   pageTitle="Kennzeichnen eines virtuellen Computers | Microsoft Azure"
   description="Informieren Sie sich über das Kennzeichnen eines mit dem Ressourcen-Manager-Bereitstellungsmodell erstellten virtuellen Azure-Computers."
   services="virtual-machines"
   documentationCenter=""
   authors="mmccrory"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure-services"
   ms.date="07/23/2015"
   ms.author="dkshir;memccror"/>

# Gewusst wie: Markieren eines virtuellen Computers in Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Dieser Artikel behandelt das Kennzeichnen eines mit dem Ressourcen-Manager-Bereitstellungsmodell erstellten virtuellen Computers.

Dieser Artikel beschreibt verschiedene Methoden zum Markieren eines virtuellen Computers in Azure mithilfe des Azure-Ressourcen-Managers. Tags sind benutzerdefinierte Schlüssel-Wert-Paare, die direkt auf einer Ressource oder einer Ressourcengruppe platziert werden können. Azure unterstützt derzeit bis zu 15 Tags pro Ressource und Ressourcengruppe. Tags können zum Zeitpunkt der Erstellung auf einer Ressource platziert werden oder zu einer vorhandenen Ressource hinzugefügt werden. Beachten Sie, dass Tags nur für Ressourcen unterstützt werden, die über den Azure-Ressourcen-Manager erstellt werden.

## Markieren eines virtuellen Computers mittels Vorlagen

Zunächst sehen wir uns das Verwenden von Tags mithilfe von Vorlagen an. [Diese Vorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/101-tags-vm) platziert Tags auf den folgenden Ressourcen: Compute (virtueller Computer), Speicher (Storage-Konto) und Netzwerk (öffentliche IP-Adresse, Virtual Network und Netzwerkschnittstelle).

Klicken Sie unter dem [Vorlagenlink](https://github.com/Azure/azure-quickstart-templates/tree/master/101-tags-vm) auf die Schaltfläche **Bereitstellen in Azure**. Dadurch werden Sie ans [Azure-Vorschauportal](http://portal.azure.com/) weitergeleitet, in dem Sie diese Vorlage bereitstellen können.

![Einfache Bereitstellung mit Tags](./media/virtual-machines-tagging-arm/deploy-to-azure-tags.png)

Diese Vorlage enthält die folgenden Tags: *Abteilung*, *Anwendung* und *Erstellt von*. Sie können diese Tags direkt in der Vorlage hinzufügen oder bearbeiten, wenn Sie andere Tagnamen wünschen.

![Azure-Tags in einer Vorlage](./media/virtual-machines-tagging-arm/azure-tags-in-a-template.png)

Wie Sie sehen können, werden die Tags als Schlüssel-Wert-Paare definiert und durch einen Doppelpunkt (:) getrennt. Die Tags müssen im folgenden Format definiert werden:

        “tags”: {
            “Key1” : ”Value1”,
            “Key2” : “Value2”
        }

Speichern Sie Vorlagendatei nach dem Bearbeiten mit den Tags Ihrer Wahl.

Anschließend können Sie im Abschnitt **Parameter bearbeiten** die Werte für die Tags ausfüllen.

![Bearbeiten von Tags im Azure-Portal](./media/virtual-machines-tagging-arm/edit-tags-in-azure-portal.png)

Klicken Sie auf **Erstellen**, um diese Vorlage mit Ihren Tagwerten bereitzustellen.


## Tags über das Portal erstellen

Nachdem Sie Ihre Ressourcen mit Tags erstellt haben, können Sie sie im Portal anzeigen, hinzufügen und löschen.

Wählen Sie das Tag-Symbol, um die Tags anzuzeigen:

![Symbol für Tags im Azure-Portal](./media/virtual-machines-tagging-arm/azure-portal-tags-icon.png)

Fügen Sie ein neues Tag über das Portal durch Definieren Ihres eigenen Schlüssels-Wert-Paares hinzu, und speichern Sie es.

![Hinzufügen eines neuen Tags im Azure-Portal](./media/virtual-machines-tagging-arm/azure-portal-add-new-tag.png)

Ihr neues Tag sollte jetzt in der Liste der Tags für die Ressource angezeigt werden.

![Neues gespeichertes Tag im Azure-Portal](./media/virtual-machines-tagging-arm/azure-portal-saved-new-tag.png)


## Markieren mit PowerShell

Zum Erstellen, Hinzufügen und Löschen von Tags über PowerShell müssen Sie zunächst Ihre [PowerShell-Umgebung mit dem Azure-Ressourcen-Manager][] einrichten. Sobald Sie die Einrichtung abgeschlossen haben, können Sie die Tags auf Compute-, Netzwerk- und Speicherressourcen platzieren, entweder während der Erstellung oder nachdem die Ressource mithilfe von PowerShell erstellt wurde. Dieser Artikel konzentriert sich auf das Anzeigen und Bearbeiten von Tags, die auf virtuellen Computern platziert sind.

Navigieren Sie zuerst über das `Get-AzureVM`-Cmdlet zu einem virtuellen Computer.

        PS C:\> Get-AzureVM -ResourceGroupName "MyResourceGroup" -Name "MyWindowsVM"

Wenn der virtuelle Computer bereits Tags enthält, werden auf der Ressource anschließend alle Tags angezeigt:

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

Wenn Sie Tags über PowerShell hinzufügen möchten, können Sie den Befehl `Set-AzureResource` verwenden. Beachten Sie, dass beim Aktualisieren von Tags über PowerShell die Tags als Ganzes aktualisiert werden. Wenn Sie ein Tag auf eine Ressource hinzufügen, die bereits über Tags verfügt, müssen Sie alle Tags einschließen, die auf der Ressource platziert werden sollen. Es folgt ein Beispiel für das Hinzufügen zusätzlicher Tags zu einer Ressource über PowerShell-Cmdlets.

Das erste Cmdlet legt alle Tags, die auf *MyWindowsVM* platziert sind, mithilfe der `Get-AzureResource`- und `Tags`-Funktion auf die *Tags*-Variable fest.

        PS C:\> $tags = (Get-AzureResource -Name MyWindowsVM -ResourceGroupName MyResourceGroup -ResourceType "Microsoft.Compute/virtualmachines" -ApiVersion 2015-05-01-preview).Tags

Der zweite Befehl zeigt die Tags für die angegebene Variable an.

        PS C:\> $tags

        Name		Value
        ----                           -----
        Value		MyDepartment
        Name		Department
        Value		MyApp1
        Name		Application
        Value		MyName
        Name		Created By
        Value		Production
        Name		Environment

Der dritte Befehl fügt ein zusätzliches Tag für die *Tags*-Variable hinzu. Beachten Sie, wie **+=** verwendet wird, um das neue Schlüssel-Wert-Paar an die *Tags*-Liste anzufügen.

        PS C:\> $tags +=@{Name="Location";Value="MyLocation"}

Der vierte Befehl legt alle Tags, die gemäß der *Tags*-Variable definiert sind, auf die angegebene Ressource fest. In diesem Fall ist dies „MyWindowsVM“.

        PS C:\> Set-AzureResource -Name MyWindowsVM -ResourceGroupName MyResourceGroup -ResourceType "Microsoft.Compute/VirtualMachines" -ApiVersion 2015-05-01-preview -Tag $tags

Der fünfte Befehl zeigt alle Tags auf der Ressource an. Wie Sie sehen können, wird der *Speicherort* jetzt als Tag mit *MeinStandort* als Wert definiert.

        PS C:\> (Get-AzureResource -ResourceName "MyWindowsVM" -ResourceGroupName "MyResourceGroup" -ResourceType "Microsoft.Compute/VirtualMachines" -ApiVersion 2015-05-01-preview).Tags

        Name		Value
        ----                           -----
        Value		MyDepartment
        Name		Department
        Value		MyApp1
        Name		Application
        Value		MyName
        Name		Created By
        Value		Production
        Name		Environment
        Value		MyLocation
        Name		Location

Weitere Informationen zum Festlegen von Tags über PowerShell finden Sie in den [Azure Resource-Cmdlets][].


## Erstellen von Tags mit der Azure-Befehlszeilenschnittstelle

Das Erstellen von Tags wird auch für Ressourcen unterstützt, die bereits über die Azure-Befehlszeilenschnittstelle erstellt wurden. Richten Sie zunächst die [Umgebung für Ihre Azure-Befehlszeilenschnittstelle][] ein. Melden Sie sich über die Azure-Befehlszeilenschnittstelle bei Ihrem Abonnement an und wechseln Sie in den ARM-Modus. Sie können alle Eigenschaften für einen bestimmten virtuellen Computer einschließlich der Tags anzeigen, indem Sie den folgenden Befehl verwenden:

        azure vm show -g MyResourceGroup -n MyVM

Im Gegensatz zu PowerShell müssen Sie nicht alle Tags (alte und neue) vor der Verwendung des `azure vm set`-Befehls angeben, wenn Sie Tags auf eine Ressource hinzufügen, die bereits welche enthält. Stattdessen können Sie mit diesem Befehl ein Tag an die Ressource anhängen. Zum Hinzufügen eines neuen VM-Tags über die Azure-Befehlszeilenschnittstelle können Sie den `azure vm set`-Befehl zusammen mit dem Tag-Parameter **-t** verwenden:

        azure vm set -g MyResourceGroup -n MyVM –t myNewTagName1=myNewTagValue1;myNewTagName2=myNewTagValue2

Um alle Tags zu entfernen, können Sie den **–T**-Parameter im `azure vm set`-Befehl verwenden.

        azure vm set – g MyResourceGroup –n MyVM -T


Nun, da wir unseren Ressourcen Tags über PowerShell, die Azure-Befehlszeilenschnittstelle und das Portal zugewiesen haben, werfen Sie einen Blick auf die Nutzungsdetails, um die Tags im Abrechnungsportal anzuzeigen.


## Anzeigen der Tags in den Nutzungsdetails

Tags auf Compute-, Netzwerk- und Speicherressourcen über den Azure-Ressourcen-Manager werden in Ihren Nutzungsdetails im Abrechnungsportal aufgefüllt.

Klicken Sie auf **Nutzungsdetails herunterladen**, um die Nutzungsdetails in Ihrem Abonnement anzuzeigen.

![Nutzungsdetails im Azure-Portal](./media/virtual-machines-tagging-arm/azure-portal-tags-usage-details.png)

Wählen Sie die Abrechnungsanweisung und die **Version 2** der Nutzungsdetails aus:

![Version 2 Vorschau der Nutzungsdetails im Azure-Portal](./media/virtual-machines-tagging-arm/azure-portal-version2-usage-details.png)

Den Verwendungsdetails können Sie alle Tags in der Spalte **Tags** entnehmen:

![Spalte für Tags im Azure-Portal](./media/virtual-machines-tagging-arm/azure-portal-tags-column.png)

Durch Analysieren dieser Tags zusammen mit der Nutzung erhalten Organisationen neue Einblicke in die Nutzung Ihrer Daten.


## Zusätzliche Ressourcen

* [Übersicht über den Azure Resource Manager][]
* [Using tags to organize your Azure resources][] (Verwenden von Tags zum Organisieren von Azure-Ressourcen)
* [Erläuterungen zur Rechnung für Azure][]
* [Gewinnen von Einblicken in den Ressourcenverbrauch unter Microsoft Azure][]




[PowerShell-Umgebung mit dem Azure-Ressourcen-Manager]: ../powershell-azure-resource-manager.md
[Azure Resource-Cmdlets]: https://msdn.microsoft.com/DE-DE/library/azure/dn757692.aspx
[Umgebung für Ihre Azure-Befehlszeilenschnittstelle]: ./xplat-cli-azure-resource-manager.md
[Übersicht über den Azure Resource Manager]: ../resource-group-overview.md
[Using Tags to organize your Azure Resources]: ../resource-group-using-tags.md
[Erläuterungen zur Rechnung für Azure]: ../billing-understand-your-bill.md
[Gewinnen von Einblicken in den Ressourcenverbrauch unter Microsoft Azure]: ../billing-usage-rate-card-overview.md

<!---HONumber=Sept15_HO4-->