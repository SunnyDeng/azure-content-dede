<properties
	pageTitle="Ressourcen-Manager-Änderungen in der Azure PowerShell 1.0-Vorschau | Microsoft Azure"
	description="Beschreibt die Änderungen bei den Ressourcen-Manager-Cmdlets, die für die Azure PowerShell 1.0-Vorschau vorgenommen wurden."
	services="azure-resource-manager"
	documentationCenter="na"
	authors="ravbhatnagar"
	manager="ryjones"
	editor=""/>

<tags
	ms.service="azure-resource-manager"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="powershell"
	ms.workload="na"
	ms.date="01/26/2016"
	ms.author="gauravbh;tomfitz"/>

#Änderungen bei den PowerShell-Verwaltungs-Cmdlets für den Azure-Ressourcen-Manager

Im Rahmen der Azure PowerShell 1.0-Vorschauversion wurden eine Reihe von Verbesserungen an den Verwaltungs-Cmdlets vorgenommen. Diese Verbesserungen wurden zusätzlich zu den Cmdlet-Namensänderungen vorgenommen, die Teil der PowerShell 1.0-Vorschau sind. Einige dieser Verbesserungen gehen mit größeren Änderungen einher und können zur Beschädigung Ihrer vorhandenen Skripts führen. Wir hoffen, dass diese Änderungen eine Verbesserung Ihrer Arbeitsweise zur Folge haben. Wir würden uns über Feedback zu diesen Änderungen freuen und werden Ihre Anregungen in Azure PowerShell 1.0 berücksichtigen. Daher möchten wir Sie bitten, die neuen Cmdlets zu testen und uns Ihr Feedback zu senden.

##Trennung von Vorlagenbereitstellung und Ressourcengruppen

In Release 0.9.8 waren sämtliche Parameter für die Vorlagenbereitstellung auch in den Ressourcengruppen-Cmdlets verfügbar. Folglich konnten Sie in "New-AzureResourceGroup" eine neue Ressourcengruppe erstellen und gleichzeitig Einzelheiten zu den Vorlagen angeben, die bereitgestellt werden sollen. Dieselbe Funktion für die Vorlagenbereitstellung war auch in "New-AzureResourceGroupDeployment" vorhanden. In der PowerShell 1.0-Vorschauversion wurden diese Funktionen voneinander getrennt. "New-AzureRMResourceGroup" ermöglicht nun die Erstellung neuer Ressourcengruppen, während die Bereitstellung der Vorlage über "New-AzureRmResourceGroupDeployment" erfolgt. Eine gemeinsame Verwendung beider Funktionen ist mithilfe von Pipes möglich. Auf diese Weise werden die Cmdlets verständlicher und benutzerfreundlicher.

##Konsolidierte Überwachungsprotokoll-Cmdlets

Für Überwachungsprotokolle waren diverse Cmdlets verfügbar, um Protokolle für verschiedene Bereiche abzurufen. Dazu zählten u. a. die folgenden Cmdlets: "Get-AzureResourceProviderLog", "Get-AzureResourceGroupLog", "Get-AzureSubscriptionIdLog" und "Get-AzureResourceLog". Häufig musste zum Abrufen von Protokollen eine Kombination dieser Cmdlets ausgeführt werden. Dadurch war die Benutzerfreundlichkeit beeinträchtigt. Wir haben diese Funktionalität nun in einem einzigen Cmdlet konsolidiert, das mithilfe von Parametern für verschiedene Bereiche aufgerufen werden kann. "Get-AzureRmLog" kann nun mit verschiedenen Parametern aufgerufen werden, um den Bereich anzugeben.

In Version 0.9.8 würden Sie Code ausführen, der dem Beispiel unten ähnelt, um die Protokolle für eine bestimmte Ressourcengruppe abzurufen:

    Get-AzureResourceGroupLog -ResourceGroup <resource-group-name>

Sie würden wie folgt vorgehen, um die Protokolle für eine bestimmte Ressource abzurufen:

     Get-AzureResourceLog -ResourceId
     /subscriptions/#######-####-####-####-############/resourcegroups/<resource-group-name>/providers/<provider-namespace>/
     <resource-name>

In Vorschau-Version 1.0 erhalten Sie die gleichen Informationen, indem Sie die unten angegebenen Cmdlets ausführen. Zum Abrufen von Protokollen für eine Ressourcengruppe führen Sie Folgendes aus:

     Get-AzureRmLog -ResourceGroup <resource-group-name>
     
Zum Abrufen einer bestimmten Ressource führen Sie Folgendes aus:

     Get-AzureRmLog -ResourceId /subscriptions/#######-####-####-####-############/resourcegroups/<resource-group-name>
     /providers/<provider-namespace>/<resource-name>

##Änderungen am Get-Cmdlet für Ressourcen und Ressourcengruppen

Die Cmdlets "Get-AzureRmResource" und "Get-AzureRmResourceGroup" wurden so geändert, dass die Abfragen dieser Cmdlets nun direkt und ausschließlich für den Ressourcenanbieter ausgeführt werden. Wir haben die Funktionalität für Abfragen, die für den Cache ausgeführt werden, von diesen Cmdlets getrennt und in den neuen Cmdlets "Find-AzureRmResource*" zusammengefasst. Wenn Sie nach einer Ressourcengruppe mit einem bestimmten Tag suchen möchten, können Sie zu diesem Zweck das neue Cmdlet "Find-AzureRmResourceGroup" nutzen. Mit dieser Änderung wurden die Parameter für Abfragen, die für Tags ausgeführt werden, aus den Cmdlets "Get-AzureRmResource" und "Get-AzureRmResourceGroup" entfernt.

In Version 0.9.8 können Sie Folgendes ausführen, um nach allen Ressourcengruppen zu suchen, die ein bestimmtes Tag enthalten:

    Get-AzureResourceGroup -Tag <tag-object>

In Vorschau-Version 1.0 führen Sie das folgende Cmdlet aus, um das oben beschriebene Szenario zu erreichen:

    Find-AzureRmResourceGroup -Tag <tag-object>
    
##Die Cmdlets "Test-AzureResource" und "Test-AzureResourceGroup" wurden entfernt

Diese Cmdlets konnten nicht für alle Szenarien und Ressourcentypen zuverlässig ausgeführt werden. Wir arbeiten an einer besseren Lösung, um diese Funktionalität bereitzustellen. Bis zur Verfügbarkeit dieser neuen Lösung sollen keine Cmdlets weiterverwendet werden, die nicht zuverlässig sind. Wir haben diese Cmdlets in diesem Release entfernt und werden in einem zukünftigen Release eine zuverlässige Lösung hinzufügen.

##Verbesserungen bei "Get-AzureRmResourceProvider"

Sie können das Cmdlet "Get-AzureRmResourceProvider" nun verwenden, um Standortinformationen für Ressourcenanbieter abzurufen. Als Ergebnis werden die verfügbaren Anbieter und Typen für eine bestimmte Region angezeigt. Darüber hinaus können Sie dieses Cmdlet zum Abrufen der Liste verfügbarer Standorte für einen bestimmten Anbieter nutzen. Das Cmdlet "Get-AzureLocation" wurde entfernt, da sämtliche Standortinformationen nun mit dem Cmdlet "Get-AzureRmResourceProvider" abgerufen werden können.

In Version 0.9.8 führen Sie zum Abrufen der Liste mit allen unterstützten Standorten Folgendes aus:

     Get-AzureLocation

Zum Abrufen des Registrierungsstatus der Anbieter führen Sie Folgendes aus:

     Get-AzureProvider -ListAvailable

In Vorschau-Version 1.0 erreichen Sie dies, indem Sie wie unten gezeigt nur ein Cmdlet verwenden:

     Get-AzureRmResourceProvider -Location <location>

Hierbei wird das Ergebnis gefiltert, um nur die Anbieter und Typen anzuzeigen, die am angegebenen Ort verfügbar sind.

Sie können das Ergebnis auch nach einem bestimmten Anbieter filtern, wie hier gezeigt:

     Get-AzureRmResourceProvider -ProviderNamespace <provider-namespace>

Hiermit wird das Ergebnis gefiltert, um nur die Orte anzuzeigen, die für den angegebenen Anbieter unterstützt werden.

##Richtlinien-Cmdlets

Der Azure-Ressourcen-Manager bietet nun Richtlinienunterstützung. In diesem Release wurden PowerShell-Cmdlets für die Richtlinienverwaltung hinzugefügt. Weitere Einzelheiten zu Richtlinien finden Sie unter [Verwenden von Richtlinien für Ressourcenverwaltung und Zugriffssteuerung](resource-manager-policy.md).

<!---HONumber=AcomDC_0128_2016-->