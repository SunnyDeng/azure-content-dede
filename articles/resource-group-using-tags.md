<properties 
	pageTitle="Verwenden von Tags zum Organisieren von Azure-Ressourcen" 
	description="Zeigt, wie Sie Tags zum Organisieren von Ressourcen für die Abrechnung und Verwaltung anwenden können." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac"
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="AzurePortal" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/15/2015" 
	ms.author="tomfitz"/>


# Verwenden von Tags zum Organisieren von Azure-Ressourcen

Das Azure-Portal und der zugrunde liegende Ressourcen-Manager werden zum Organisieren von Ressourcen und Personalisieren der Benutzererfahrung verwendet.

Abonnements sind im klassischen Azure-Portal die einzige Möglichkeit zum Kategorisieren und Gruppieren Ihrer Ressourcen. Mit dem Azure-Portal [haben wir Ressourcengruppen eingeführt](./resource-group-portal.md), die es Ihnen ermöglichen, verknüpfte Entitäten zu gruppieren. Dies wurde noch nützlicher, als [wir den rollenbasierten Zugriff eingeführt](./role-based-access-control-configure.md) haben. Jetzt können Sie in diesem Sinne Ihre Ressourcen mit Schlüssel-Wert-Paaren markieren, um Ressourcen für Abonnements über Ressourcengruppen hinweg und, innerhalb des Portals, über Abonnements hinweg weiter zu kategorisieren und anzuzeigen.

Gruppieren Sie Ressourcen nach Team, Projekt oder sogar Umgebung, um sich genau darauf zu konzentrieren, was Sie sehen müssen und wann Sie es benötigen.


## Markierungen im Azure-Portal

Das Markieren von Ressourcen und Ressourcengruppen im Portal ist einfach. Verwenden Sie den Durchsuchenhub, um zu der Ressource oder Ressourcengruppe zu navigieren, die Sie markieren möchten. Klicken Sie dann auf den Tags-Teil im Übersichtsabschnitt oben im Fenster.

![Tags-Bereich im Blatt für Ressourcen oder Ressourcengruppen](./media/resource-group-using-tags/rgblade.png)

Dies öffnet ein Fenster mit der Liste der Tags, die bereits angewendet wurden. Ist dies der erste Tag, wird die Liste leer sein. Um ein Tag hinzuzufügen, geben Sie einfach einen Namen und Wert an, und drücken Sie die EINGABETASTE. Nachdem Sie einige Tags hinzugefügt haben, sehen Sie die AutoVervollständigen-Optionen basierend auf bereits vorhandenen Tag-Namen und -Werten, um eine konsistente Klassifizierung Ihrer Ressourcen sicherzustellen und häufige Fehler, wie Rechtschreibfehler, zu vermeiden.

![Markieren von Ressourcen mit Name-Wert-Paaren](./media/resource-group-using-tags/tag-resources.png)

Von hier aus können Sie auf jedes einzelne Tag zum Anzeigen einer Liste aller Ressourcen mit demselben Tag klicken. Wenn dies das erste Tag ist, ist diese Liste natürlich nicht sehr interessant. Wechseln wir jetzt zu PowerShell, um alle unsere Ressourcen schnell zu markieren.


## Markieren mit PowerShell

Holen Sie sich zunächst das aktuelle [Azure PowerShell-Modul](./install-configure-powershell.md). Wenn Sie das Azure PowerShell-Modul zum ersten Mal verwenden, [lesen Sie die Dokumentation](./install-configure-powershell.md), um einen schnelleren Einstieg zu finden. Für diesen Artikel wird davon ausgegangen, dass Sie bereits ein Konto hinzugefügt und ein Abonnement mit den zu markierenden Ressourcen ausgewählt haben.

Markierungen sind nur für Ressourcen und Ressourcengruppen verfügbar, die im [Ressourcen-Manager](http://msdn.microsoft.com/library/azure/dn790568.aspx) verfügbar sind, daher müssen Sie zunächst zur Verwendung des Ressourcen-Managers wechseln. Weitere Informationen finden Sie unter [Verwenden von Azure PowerShell mit dem Azure-Ressourcen-Manager](powershell-azure-resource-manager.md).

    Switch-AzureMode AzureResourceManager

Markierungen sind direkt in Ressourcen und Ressourcengruppen vorhanden. Um festzustellen, welche Markierungen bereits angewendet wurden, können wir einfach eine Ressource oder Ressourcengruppe mit `Get-AzureResource` oder `Get-AzureResourceGroup` abrufen. Beginnen wir mit einer Ressourcengruppe.

![Abrufen von Markierungen mit Get-AzureResourceGroup in PowerShell](./media/resource-group-using-tags/Get-AzureResourceGroup-in-PowerShell.png)

Dieses Cmdlet gibt mehrere Teile der Metadaten für die Ressourcengruppe zurück, einschließlich welche Tags ggf. angewendet wurden. Um eine Ressourcengruppe zu markieren, verwenden wir einfach `Set-AzureResourceGroup` und geben einen Namen und Wert für die Markierung an.

![Festlegen von Markierungen mit Set-AzureResourceGroup in PowerShell](./media/resource-group-using-tags/Set-AzureResourceGroup-in-PowerShell.png)

Denken Sie daran, dass die Tags als Ganzes aktualisiert werden. Wenn Sie einer Ressource, die bereits markiert wurde, ein Tag hinzufügen, müssen Sie zum Speichern ein Array mit allen Tags verwenden, die Sie beibehalten möchten. Um eines zu entfernen, speichern Sie einfach das Array ohne dasjenige, das Sie entfernen möchten.

Der Prozess ist für Ressourcen der gleiche, Sie verwenden jedoch die Cmdlets `Get-AzureResource` und `Set-AzureResource`. Um Ressourcen oder Ressourcengruppen mit einer bestimmten Markierung abzurufen, verwenden Sie die Cmdlets `Get-AzureResource` oder `Get-AzureResourceGroup` mit dem `-Tag`-Parameter.

![Abrufen von markierten Ressourcen und Ressourcengruppen mit Get-AzureResource und Get-AzureResourceGroup in PowerShell](./media/resource-group-using-tags/Get-AzureResourceGroup-with-tags-in-PowerShell.png)


## Markieren mit dem Ressourcen-Manager

Das Vorschauportal und PowerShell verwenden im Hintergrund die [Ressourcen-Manager-REST-API](http://msdn.microsoft.com/library/azure/dn790568.aspx). Wenn Sie das Tagging in eine andere Umgebung integrieren müssen, können Sie Tags mit GET für die Ressourcen-ID abrufen und die Tags mit einem PATCH-Aufruf aktualisieren.


## Verwalten der Taxonomie

Zuvor haben wir uns damit befasst, wie AutoVervollständigen die Konsistenz sicherstellen und Fehler vermeiden kann. AutoVervollständigen wird basierend auf der Taxonomie verfügbarer Tags, die für das Abonnement eingerichtet werden, aufgefüllt. Jedes Tag, das Sie einer Ressource oder Ressourcengruppe hinzufügen, wird automatisch der abonnementweiten Taxonomie hinzugefügt. Sie können diese Taxonomie aber auch mit Tagnamen und -werten auffüllen, die Sie als Ressourcen verwenden möchten und in Zukunft markiert werden.

Verwenden Sie das Cmdlet `Get-AzureTag`, um mithilfe von PowerShell eine Liste aller Markierungen innerhalb eines Abonnements abzurufen.

![Get-AzureTag in PowerShell](./media/resource-group-using-tags/Get-AzureTag-in-PowerShell.png)


Möglicherweise sehen Sie Tags, die mit "hidden-" und "link:" beginnen. Hierbei handelt es sich um interne Tags, die Sie ignorieren und nicht ändern sollten.

Verwenden Sie das Cmdlet `New-AzureTag`, um der Taxonomie neue Markierungen hinzuzufügen. Diese Tags werden in die AutoVervollständigen-Funktion eingeschlossen, obwohl sie noch nicht auf Ressourcen oder Ressourcengruppen angewendet wurden. Um einen Markierungsnamen/Markierungswert zu entfernen, entfernen Sie zuerst die Markierung aus allen Ressourcen, mit denen es möglicherweise verwendet wird, und entfernen Sie es dann mit dem Cmdlet `Remove-AzureTag` aus der Taxonomie.

Um die Taxonomie von Tags im Portal anzuzeigen, verwenden Sie den Durchsuchenhub, um alles anzuzeigen, und wählen Sie dann Tags aus.

![Suchen von Markierungen über den Browse-Hub](./media/resource-group-using-tags/browse-tags.png)

Fixieren Sie die wichtigsten Tags für den schnellen Zugriff auf dem Startboard, und Sie können loslegen. Viel Spaß!

![Anheften von Markierungen am das Startmenü](./media/resource-group-using-tags/pin-tags.png)

## Tags und Abrechnung

Abrechnungsdaten können für unterstützte Dienste mithilfe von Tags gruppiert werden. So können Sie beispielsweise mithilfe von [in den Azure-Ressourcen-Manager integrierten virtuellen Computern](/virtual-machines/virtual-machines-azurerm-versus-azuresm.md) Tags definieren und anwenden, um die Abrechnung für virtuelle Computer zu organisieren. Wenn Sie mehrere virtuelle Computer für verschiedene Organisationen betreiben, können Sie die Nutzung mithilfe von Tags nach Kostenstelle gruppieren. Mit Tags können Sie auch Kosten nach Laufzeitumgebung kategorisieren (beispielsweise zur Abrechnung der Nutzung virtueller Computer in der Produktionsumgebung).

Informationen zu Tags können über die [Nutzungs-API](billing-usage-rate-card-overview.md) oder aus der Nutzungsdatei im CSV-Format abgerufen werden, die Sie aus dem [Azure-Kontenportal](https://account.windowsazure.com/) oder aus dem [EA-Portal](https://ea.azure.com) herunterladen können.

Wenn Sie die CSV-Nutzungsdatei für Dienste herunterladen, die die Verwendung von Tags für die Abrechnung unterstützen, sind die Tags in der Spalte **Tags** enthalten. Ausführlichere Informationen finden Sie unter [Informationen zu Ihrer Rechnung für Microsoft Azure](billing-understand-your-bill.md).

![Anzeigen von Tags in der Abrechnung](./media/resource-group-using-tags/billing_csv.png)

## Nächste Schritte
Erste Schritte

- [Azure Resource Manager Overview](./resource-group-overview.md) (Übersicht über den Azure-Ressourcen-Manager)  
- [Verwenden von Windows PowerShell mit dem Ressourcen-Manager](./powershell-azure-resource-manager.md)
- [Verwenden der plattformübergreifenden Azure-Befehlszeilenschnittstelle mit dem Ressourcen-Manager](./xplat-cli-azure-resource-manager.md)  
- [Using the Azure Portal to manage your Azure resources](./resource-group-portal.md) (Verwenden des Azure-Portals zum Verwalten Ihrer Azure-Ressourcen)  
  
Erstellen und Bereitstellen von Anwendungen
  
- [Authoring Azure Resource Manager Templates](./resource-group-authoring-templates.md) (Erstellen von Vorlagen im Azure-Ressourcen-Manager)  
- [Deploy an application with Azure Resource Manager Template](./resource-group-template-deploy.md) (Bereitstellen einer Anwendung mit einer Vorlage im Azure-Ressourcen-Manager)  
- [Troubleshooting Resource Group Deployments in Azure](./resource-group-deploy-debug.md) (Problembehandlung beim Bereitstellen von Ressourcengruppen in Azure)  
- [Azure Resource Manager Template Functions](./resource-group-template-functions.md) (Vorlagenfunktionen im Azure-Ressourcen-Manager)  
- [Advanced Template Operations](./resource-group-advanced-template.md) (Erweiterte Anwendungen mit Vorlagen)  
  
Zugriffsverwaltung und -überwachung
  
- [Verwalten und Überwachen des Ressourcen-Zugriffs](./resource-group-rbac.md)  
- [Authentifizieren eines Dienstprinzipals mit Azure-Ressourcen-Manager](./resource-group-authenticate-service-principal.md)  
- [Erstellen neuer Azure-Dienstprinzipale mit dem Azure-Portal](./resource-group-create-service-principal-portal.md)  
  

<!---HONumber=July15_HO3-->