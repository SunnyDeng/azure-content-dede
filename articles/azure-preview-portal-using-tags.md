<properties 
	pageTitle="Verwenden von Tags zum Organisieren von Azure-Ressourcen" 
	description="" 
	services="" 
	documentationCenter="" 
	authors="flanakin" 
	writer="" 
	manager="carolz" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/08/2014" 
	ms.author="micflan"/>


# Verwenden von Tags zum Organisieren von Azure-Ressourcen

Das Azure Preview-Portal und die zugrunde liegenden Ressourcen-Manager werden zum Organisieren von Ressourcen und Anpassen der Benutzererfahrung verwendet - nur für Sie. 

Abonnements sind im vollständigen Azure-Portal die einzige Möglichkeit zum Kategorisieren und Gruppieren Ihrer Ressourcen. Mit dem Vorschauportal [haben wir Ressourcengruppen eingeführt](http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-resource-groups), die es Ihnen ermöglichen, verknüpfte Entitäten zu gruppieren. Das wurde noch wichtiger, als [wir den rollenbasierten Zugriff eingeführt haben](http://azure.microsoft.com/documentation/articles/role-based-access-control-configure). Jetzt können Sie in diesem Sinne Ihre Ressourcen mit Schlüssel-Wert-Paaren markieren, um Ressourcen für Abonnements über Ressourcengruppen hinweg und, innerhalb des Portals, über Abonnements hinweg weiter zu kategorisieren und anzuzeigen.

Gruppieren Sie Ressourcen nach Team, Projekt oder sogar Umgebung, um sich genau darauf zu konzentrieren, was Sie sehen müssen und wann Sie es benötigen. 


## Tags im Azure Preview-Portal

Das Markieren von Ressourcen und Ressourcengruppen im Vorschauportal ist einfach. Verwenden Sie den Durchsuchenhub, um zu der Ressource oder Ressourcengruppe zu navigieren, die Sie markieren möchten. Klicken Sie dann auf den Tags-Teil im Übersichtsabschnitt oben im Fenster. 

![Tags part on resource and resource group blades](./media/azure-preview-portal-using-tags/rgblade.png)

Dies öffnet ein Fenster mit der Liste der Tags, die bereits angewendet wurden. Ist dies der erste Tag, wird die Liste leer sein. Um ein Tag hinzuzufügen, geben Sie einfach einen Namen und Wert an, und drücken Sie die EINGABETASTE. Nachdem Sie einige Tags hinzugefügt haben, sehen Sie die AutoVervollständigen-Optionen basierend auf bereits vorhandenen Tag-Namen und -Werten, um eine konsistente Klassifizierung Ihrer Ressourcen sicherzustellen und häufige Fehler, wie Rechtschreibfehler, zu vermeiden.

![Tag resources with name/value pairs](./media/azure-preview-portal-using-tags/tag-resources.png)

Von hier aus können Sie auf jedes einzelne Tag zum Anzeigen einer Liste aller Ressourcen mit demselben Tag klicken. Wenn dies das erste Tag ist, ist diese Liste natürlich nicht sehr interessant. Wechseln wir jetzt zu PowerShell, um alle unsere Ressourcen schnell zu markieren.


## Markieren mit PowerShell

Holen Sie sich zunächst das aktuelle [Azure PowerShell-Modul](http://azure.microsoft.com/documentation/articles/install-configure-powershell/). Wenn Sie das Azure PowerShell-Modul zum ersten Mal verwenden, [lesen Sie die Dokumentation](http://azure.microsoft.com/documentation/articles/install-configure-powershell), um den Vorgang zu beschleunigen. Für die Zwecke dieses Artikels gehen wir davon aus, dass Sie bereits ein Konto hinzugefügt und ein Abonnement mit den Ressourcen, die Sie markieren möchten, ausgewählt haben.

Tags sind nur für Ressourcen und Ressourcengruppen verfügbar, die im [Ressourcen-Manager](http://msdn.microsoft.com/library/azure/dn790568.aspx) verfügbar sind, daher müssen Sie zunächst zur Verwendung des Ressourcen-Managers wechseln. Weitere Informationen finden Sie unter [Verwenden von Windows PowerShell mit Resource Manager](http://azure.microsoft.com/documentation/articles/powershell-azure-resource-manager/).

  Switch-AzureMode AzureResourceManager

Tags sind direkt in Ressourcen und Ressourcengruppen vorhanden. Um festzustellen, welche Tags bereits angewendet wurden, können wir einfach eine Ressource oder Ressourcengruppe mit `Get-AzureResource` oder `Get-AzureResourceGroup` abrufen. Beginnen wir mit einer Ressourcengruppe.

![Getting tags with Get-AzureResourceGroup in PowerShell](./media/azure-preview-portal-using-tags/Get-AzureResourceGroup-in-PowerShell.png)

Dieses Cmdlet gibt mehrere Teile der Metadaten für die Ressourcengruppe zurück, einschließlich welche Tags ggf. angewendet wurden. Um eine Ressourcengruppe  zu markieren, verwenden wir einfach `Set-AzureResourceGroup` und geben einen Tagnamen und Wert an.

![Setting tags with Set-AzureResourceGroup in PowerShell](./media/azure-preview-portal-using-tags/Set-AzureResourceGroup-in-PowerShell.png)

Denken Sie daran, dass die Tags als Ganzes aktualisiert werden. Wenn Sie ein Tag zu einer Ressource hinzufügen, die bereits markiert wurde, müssen Sie zum Speichern ein Array mit allen Tags verwenden, die Sie beibehalten möchten. Um eines zu entfernen, speichern Sie einfach das Array ohne dasjenige, das Sie entfernen möchten. 

Der Prozess ist für Ressourcen der gleiche, außer Sie verwenden die `Get-AzureResource`- und `Set-AzureResource`-Cmdlets. Um Ressourcen oder Ressourcengruppen mit einem bestimmten Tag abzurufen, verwenden Sie das `Get-AzureResource`- oder `Get-AzureResourceGroup`-Cmdlet mit dem `-Tag`-Parameter.

![Getting tagged resources and resource groups with Get-AzureResource and Get-AzureResourceGroup in PowerShell](./media/azure-preview-portal-using-tags/Get-AzureResourceGroup-with-tags-in-PowerShell.png)


## Markieren mit dem Ressourcen-Manager

Das Vorschauportal und PowerShell verwenden im Hintergrund die [Ressourcen-Manager-REST-API](http://msdn.microsoft.com/library/azure/dn790568.aspx). Wenn Sie das Tagging in eine andere Umgebung integrieren müssen, können Sie Tags mit GET für die Ressourcen-ID abrufen und die Tags mit einem PATCH-Aufruf aktualisieren.


## Verwalten der Taxonomie

Zuvor haben wir uns damit befasst, wie AutoVervollständigen die Konsistenz sicherstellen und Fehler vermeiden kann. AutoVervollständigen wird basierend auf der Taxonomie verfügbarer Tags, die für das Abonnement eingerichtet werden, aufgefüllt. Jedes Tag, das Sie einer Ressource oder Ressourcengruppe hinzufügen, wird automatisch der abonnementweiten Taxonomie hinzugefügt. Sie können diese Taxonomie aber auch mit Tagnamen und -werten auffüllen, die Sie als Ressourcen verwenden möchten und in Zukunft markiert werden.

Verwenden Sie das `Get-AzureTag`-Cmdlet, um eine Liste aller Tags innerhalb eines Abonnements mithilfe von PowerShell zu erhalten.

![Get-AzureTag in PowerShell](./media/azure-preview-portal-using-tags/Get-AzureTag-in-PowerShell.png)


Möglicherweise sehen Sie Tags, die mit "hidden-" und "link:" beginnen. Hierbei handelt es sich um interne Tags, die Sie ignorieren und nicht ändern sollten. 

Verwenden Sie das `New-AzureTag`-Cmdlet, um der Taxonomie neue Tags hinzuzufügen. Diese Tags werden in die AutoVervollständigen-Funktion eingeschlossen, obwohl sie noch nicht auf Ressourcen oder Ressourcengruppen angewendet wurden. Um einen Tagnamen/Tagwert zu entfernen, entfernen Sie zuerst das Tag von allen Ressourcen, mit denen es möglicherweise verwendet werden, und verwenden Sie dann das `Remove-AzureTag`-Cmdlet, um es aus der Taxonomie zu entfernen.

Um die Taxonomie von Tags im Portal anzuzeigen, verwenden Sie den Durchsuchenhub, um alles anzuzeigen, und wählen Sie dann Tags aus.

![Find tags via the Browse hub](./media/azure-preview-portal-using-tags/browse-tags.png)

Fixieren Sie die wichtigsten Tags für den schnellen Zugriff auf dem Startboard, und Sie können loslegen. Viel Spaß!

![Pin tags to the Startboard](./media/azure-preview-portal-using-tags/pin-tags.png)


<!--HONumber=46--> 
