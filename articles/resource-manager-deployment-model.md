<properties
   pageTitle="Grundlegendes zu den Unterschieden zwischen dem Modell der Ressourcen-Manager-Bereitstellung und dem Modell der klassischen Bereitstellung"
   description="Beschreibt die Unterschiede zwischen dem Ressourcen-Manager-Bereitstellungsmodell und dem klassischen Bereitstellungsmodell (auch als ";Dienstverwaltungsmodell"; bekannt)."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/02/2015"
   ms.author="tomfitz"/>

# Grundlegendes zur Bereitstellung über den Ressourcen-Manager im Vergleich zur klassischen Bereitstellung

Das Ressourcen-Manager-Bereitstellungsmodell bietet eine neue Möglichkeit, die Dienste, aus denen Ihre App besteht, bereitzustellen und zu verwalten. Dieses neue Modell weist einige signifikante Unterschiede zum klassischen Bereitstellungsmodell auf, und die beiden Modelle sind nicht vollständig miteinander kompatibel. Um die Bereitstellung und Verwaltung der Ressourcen zu vereinfachen, empfiehlt Microsoft die Verwendung des Ressourcen-Managers für neue Ressourcen und, sofern möglich, die erneute Bereitstellung vorhandener Ressourcen über den Ressourcen-Manager.

Das klassische Bereitstellungsmodell ist Ihnen möglicherweise unter den Bezeichnungen "Service-Management-Modell" oder "Dienstverwaltungsmodell" bekannt.

Dieses Thema beschreibt die Unterschiede zwischen den beiden Modellen sowie einige Probleme, die beim Wechsel vom klassischen zum Ressourcen-Manager-Modell auftreten können. Das Thema bietet einen Überblick über die Modelle, geht jedoch nicht detailliert auf die Unterschiede zwischen einzelnen Diensten ein. Detaillierte Informationen zum Verschieben von Compute-, Speicher- und Netzwerkressourcen finden Sie unter [Azure-Compute-, Netzwerk- und Speicheranbieter unter dem Azure-Ressourcen-Manager](./virtual-machines/virtual-machines-azurerm-versus-azuresm.md).

Viele Ressourcen funktionieren problemlos sowohl im klassischen als auch im Ressourcen-Manager-Modell. Diese Ressourcen unterstützen den Ressourcen-Manager vollständig, selbst wenn sie im klassischen Modell erstellt wurden. Sie können diese Ressourcen ohne Bedenken oder zusätzlichen Aufwand zum Ressourcen-Manager verschieben.

Aufgrund der Architekturunterschiede zwischen den Modellen stellen einige Ressourcenanbieter jedoch zwei Versionen ihrer Ressource bereit: eine für das klassische, eine für das Ressourcen-Manager-Modell. Für folgende Ressourcenanbieter bestehen Unterschiede zwischen den beiden Modellen:

- Compute
- Speicher
- Netzwerk

Für diese Ressourcentypen müssen Sie darauf achten, welche Version Sie verwenden, da sich die unterstützten Vorgänge unterscheiden.

Informationen zu den Architekturunterschieden zwischen den beiden Modellen finden Sie unter [Architektur des Azure-Ressourcen-Managers](virtual-machines/virtual-machines-azure-resource-manager-architecture.md).

## Merkmale des Ressourcen-Managers

Ressourcen, die über den Ressourcen-Manager erstellt wurden, weisen folgende Merkmale auf:

- Erstellt mit einer der beiden folgenden Methoden:

  - Das [Azure-Portal](https://portal.azure.com/)

        ![Azure portal](./media/resource-manager-deployment-model/preview-portal.png)

        Für Compute-, Storage- und Netzwerkressourcen haben Sie die Möglichkeit, entweder den Ressourcen-Manager oder die klassische Bereitstellung zu nutzen. Wählen Sie **Ressourcen-Manager** .

        ![Resource Manager deployment](./media/resource-manager-deployment-model/select-resource-manager.png)

  - In Azure PowerShell-Versionen vor 1.0 werden Befehle im Modus **AzureResourceManager** ausgeführt.

            PS C:\> Switch-AzureMode -Name AzureResourceManager

  - Verwenden Sie in Azure PowerShell 1.0 für Befehle die Version mit dem Ressourcen-Manager. Diese Befehle haben das Format *Verb-AzureRm*, wie unten dargestellt.

            PS C:\> Get-AzureRmResourceGroupDeployment

  - [Azure-Ressourcen-Manager-REST-API](https://msdn.microsoft.com/library/azure/dn790568.aspx) für REST-Vorgänge.
  - Befehle der Azure-Befehlszeilenschnittstelle, die im Modus **arm** ausgeführt werden.

            azure config mode arm

- Der Name des Ressourcentyps enthält nicht das Wort **(klassisch)**. Die folgende Abbildung zeigt den Typ **Speicherkonto**.

    ![Web-App](./media/resource-manager-deployment-model/resource-manager-type.png)

## Merkmale der klassischen Bereitstellung

Ressourcen, die im klassischen Bereitstellungsmodell erstellt wurden, weisen folgende Merkmale auf:

- Erstellt mit einer der beiden folgenden Methoden:

  - [Klassisches Portal](https://manage.windowsazure.com)

        ![Classic portal](./media/resource-manager-deployment-model/azure-portal.png)

        Alternativ können Sie das Vorschauportal nutzen und eine **klassische** Bereitstellung (für Compute-, Speicher- und Netzwerkressourcen) festlegen.

        ![Classic deployment](./media/resource-manager-deployment-model/select-classic.png)

  - In Azure PowerShell-Versionen vor 1.0 werden Befehle im Modus **AzureServiceManagement** ausgeführt (dies ist der Standardmodus, wenn Sie also nicht speziell zu „AzureResourceManager“ wechseln, befinden Sie sich bereits im AzureServiceManagement-Modus).

            PS C:\> Switch-AzureMode -Name AzureServiceManagement

  - Verwenden Sie in Azure PowerShell 1.0 die Dienstverwaltungsversion der Befehle. Diese Befehlsnamen haben **nicht** das Format *Verb-AzureRm*, wie unten dargestellt.

            PS C:\> Get-AzureDeployment

  - [Dienstverwaltungs-REST-API](https://msdn.microsoft.com/library/azure/ee460799.aspx) für REST-Vorgänge.
  - Befehle der Azure-Befehlszeilenschnittstelle, die im Modus **asm** oder im Standardmodus ausgeführt werden.
- Der Name des Ressourcentyps enthält das Wort **(klassisch)**. Die folgende Abbildung zeigt den Typ **Speicherkonto (klassisch)**.

    ![Klassischer Typ](./media/resource-manager-deployment-model/classic-type.png)

Sie können weiterhin das Portal zur Verwaltung von Ressourcen verwenden, die über die klassische Bereitstellung erstellt wurden.

## Vorteile der Verwendung von Ressourcen-Manager und Ressourcengruppen

Mit dem Ressourcen-Manager wurde das Konzept der Ressourcengruppen eingeführt. Jede über den Ressourcen-Manager erstellte Ressource befindet sich in einer Ressourcengruppe. Das Ressourcen-Manager-Bereitstellungsmodell bietet verschiedene Vorteile:

- Sie müssen die Dienste für Ihre Lösung nicht mehr einzeln bearbeiten, sondern können sie als Gruppe bereitstellen, verwalten und überwachen.
- Sie können die Anwendung während des gesamten App-Lebenszyklus wiederholt bereitstellen und sicher sein, dass Ihre Ressourcen einheitlich bereitgestellt werden.
- Sie können deklarative Vorlagen verwenden, um Ihre Bereitstellung zu definieren. 
- Sie können die Abhängigkeiten zwischen Ressourcen definieren, sodass diese in der richtigen Reihenfolge bereitgestellt werden.
- Sie können die Zugriffssteuerung auf alle Dienste in der Ressourcengruppe anwenden, da die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) standardmäßig in die Verwaltungsplattform integriert ist.
- Sie können Tags auf Ressourcen anwenden, um alle Ressourcen in Ihrem Abonnement logisch zu organisieren.


Vor der Einführung des Ressourcen-Managers befanden sich die über die klassische Bereitstellung erstellten Ressourcen nicht in Ressourcengruppen. Nach Einführung des Ressourcen-Managers wurden alle Ressourcen nachträglich zu Standardressourcengruppen hinzugefügt. Wenn Sie jetzt eine Ressource über die klassische Bereitstellung erstellen, wird diese automatisch in einer standardmäßigen Ressourcengruppe für diesen Dienst erstellt, auch wenn Sie während der Bereitstellung diese Ressourcengruppe nicht angegeben haben. Jedoch bedeutet die bloße Tatsache, dass eine Ressource sich in einer Ressourcengruppe befindet, noch nicht, dass diese Ressource in das Ressourcen-Manager-Modell konvertiert wurde. Für Virtual Machines, Storage und Virtual Networks gilt: Wenn die Ressource über die klassische Bereitstellung erstellt wurde, müssen Sie diese weiterhin mit klassischen Vorgängen ausführen.

Sie können Ressourcen in eine andere Ressourcengruppe verschieben und einer vorhandenen Ressourcengruppe neue Ressourcen hinzufügen. Eine Ressourcengruppe kann daher eine Kombination aus Ressourcen enthalten, die mit dem Ressourcen-Manager und über die klassische Bereitstellung erstellt wurden. Diese Ressourcenkombination kann zu unerwarteten Ergebnissen führen, da die Ressourcen nicht die gleichen Vorgänge unterstützen.

Durch Verwendung von deklarativen Vorlagen können Sie Ihre Bereitstellungsskripts vereinfachen. Anstatt zu versuchen, vorhandene Dienstverwaltungsskripts in Skripts für den Ressourcen-Manager zu konvertieren, empfiehlt es sich, die Bereitstellungsstrategie zu überarbeiten und Ihre Infrastruktur und Konfiguration in einer Vorlage zu definieren.

## Verwenden von Tags

Mit Tags können Sie Ihre Ressourcen logisch organisieren. Nur Ressourcen, die mit dem Ressourcen-Manager erstellt wurden, unterstützen Tags. Sie können keine Tags auf klassische Ressourcen anwenden.

Weitere Informationen zur Verwendung von Tags im Ressourcen-Manager finden Sie unter [Verwenden von Tags zum Organisieren von Azure-Ressourcen](resource-group-using-tags.md).

## Unterstützte Vorgänge für die Bereitstellungsmodelle

Ressourcen, die Sie im klassischen Bereitstellungsmodell erstellen, unterstützen keine Ressourcen-Manager-Vorgänge. In einigen Fällen können Sie mithilfe eines Ressourcen-Manager-Befehls Informationen zu einer mit der klassischen Bereitstellung erstellten Ressource abrufen oder administrative Aufgaben ausführen, z. B. eine klassische Ressource in eine andere Ressourcengruppe verschieben. Dies sollte jedoch nicht zu der Annahme verleiten, dass der entsprechende Ressourcentyp Ressourcen-Manager-Vorgänge unterstützt. Nehmen Sie beispielsweise an, Sie verfügen über eine Ressourcengruppe, die sowohl mit dem Ressourcen-Manager als auch mit dem klassischen Modell erstellte virtuelle Computer enthält. Wenn Sie folgenden PowerShell-Befehl ausführen, werden alle virtuellen Computer angezeigt:

    PS C:\> Get-AzureRmResourceGroup -Name ExampleGroup
    ...
    Resources :
     Name                 Type                                          Location
     ================     ============================================  ========
     ExampleClassicVM     Microsoft.ClassicCompute/domainNames          eastus
     ExampleClassicVM     Microsoft.ClassicCompute/virtualMachines      eastus
     ExampleResourceVM    Microsoft.Compute/virtualMachines             eastus
    ...

Wenn Sie jedoch den Befehl "Get-AzureVM" ausführen, werden nur die virtuellen Computer abgerufen, die mit dem Ressourcen-Manager erstellt wurden.

    PS C:\> Get-AzureVM -ResourceGroupName ExampleGroup
    ...
    Id       : /subscriptions/xxxx/resourceGroups/ExampleGroup/providers/Microsoft.Compute/virtualMachines/ExampleResourceVM
    Name     : ExampleResourceVM
    ...

Im Allgemeinen sollten Sie davon ausgehen, dass Ressourcen, die über die klassische Bereitstellung erstellt wurden, mit Ressourcen-Manager-Befehlen funktionieren.

Bei der Arbeit mit Ressourcen, die mit dem Ressourcen-Manager erstellt wurden, sollten Sie Ressourcen-Manager-Vorgänge verwenden und nicht zu Dienstverwaltungsvorgängen zurückkehren.

## Überlegungen zu virtuellen Computern

Bei der Arbeit mit virtuellen Computern sind einige wichtige Aspekte zu berücksichtigen.

- Virtuelle Computer, die mit dem klassischen Bereitstellungsmodell erstellt wurden, können nicht in ein virtuelles Netzwerk eingebunden werden, das mit dem Ressourcen-Manager bereitgestellt wurde.
- Virtuelle Computer, die mit dem Ressourcen-Manager-Bereitstellungsmodell erstellt wurden, müssen in ein virtuelles Netzwerk eingebunden werden.
- Virtuelle Computer, die mit dem klassischen Bereitstellungsmodell erstellt wurden, müssen nicht in ein virtuelles Netzwerk eingebunden werden.

Wenn Ausfallzeiten für Ihre virtuellen Computer eingeplant werden können, dann können Sie diese mit [ASM2ARM-PowerShell-Skripts](https://github.com/fullscale180/asm2arm) von der klassischen Bereitstellung zu Ressourcen-Manager übertragen.

Eine Liste der entsprechenden Befehle der Azure-Befehlszeilenschnittstelle beim Wechsel von der klassischen zur Ressourcen-Manager-Bereitstellung finden Sie unter [Befehlsentsprechungen für Ressourcen-Manager und Dienstverwaltung für VM-Vorgänge](./virtual-machines/xplat-cli-azure-manage-vm-asm-arm.md).

Detaillierte Informationen zum Verschieben von Compute-, Speicher- und Netzwerkressourcen finden Sie unter [Azure-Compute-, Netzwerk- und Speicheranbieter unter dem Azure-Ressourcen-Manager](./virtual-machines/virtual-machines-azurerm-versus-azuresm.md).

Informationen zum Verbinden virtueller Netzwerke aus verschiedenen Bereitstellungsmodellen finden Sie unter [Herstellen einer Verbindung zwischen klassischen VNets und neuen VNets](./virtual-network/virtual-networks-arm-asm-s2s.md).

## Nächste Schritte

- Informationen zum Erstellen deklarativer Bereitstellungsvorlagen finden Sie unter [Erstellen von Azure-Ressourcen-Manager-Vorlagen](resource-group-authoring-templates.md).
- Die Befehle zum Bereitstellen einer Vorlage finden Sie unter [Bereitstellen einer Anwendung mit einer Azure-Ressourcen-Manager-Vorlage](resource-group-template-deploy.md).

<!---HONumber=AcomDC_1203_2015-->
