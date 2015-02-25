<properties 
	pageTitle="Stagingbereitstellung auf Microsoft Azure-Websites" 
	description="Erfahren Sie mehr über das Veröffentlichen in einer Stagingumgebung in Microsoft Azure Websites." 
	services="web-sites" 
	documentationCenter="" 
	authors="cephalin" 
	writer="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="9/9/2014" 
	ms.author="cephalin"/>

<a name="Overview"></a>
#Stagingbereitstellung auf Microsoft Azure-Websites
Wenn Sie Ihre Anwendung auf Azure-Websites bereitstellen, kann die Bereitstellung in einem separaten Bereitstellungsslot anstelle des Standardproduktionsslots erfolgen, bei dem es sich tatsächlich um eine Livewebsite mit einen Hostnamen handelt. Diese Option ist im Webhostingplan **Standard** verfügbar. Darüber hinaus können Sie die Websites und Websitekonfigurationen zwischen den beiden Bereitstellungsslots, einschließlich des Produktionsslots, austauschen. Die Bereitstellung von Anwendungen in einem Bereitstellungsslot hat die folgenden Vorteile:

- Sie können Websiteänderungen in einem Stagingbereitstellungsslot überprüfen, bevor Sie die Website in den Produktionsslot überführen.

- Nach der Überführung enthält der Slot mit der vorherigen Stagingwebsite die vorherige Produktionswebsite. Wenn die in den Produktionsslot überführten Änderungen nicht Ihren Erwartungen entsprechen, können Sie denselben Austausch sofort noch einmal vornehmen, um die "letzte als gut befundene Website" zurückzuerhalten. 
 
- Indem Sie eine Website zuerst in einem Slot bereitstellen und sie dann in den Produktionsslot überführen, stellen Sie sicher, dass alle Instanzen erst nach einer Anlaufzeit in den Produktionsslot übernommen werden. Dadurch vermeiden Sie Downtime bei der Bereitstellung der Website. Die Verkehrsweiterleitung ist nahtlos, und es werden keine Anfragen aufgrund von Überführungsoperationen fallengelassen. 

Zusätzlich zum Produktionsslot werden für jede Website im Plan **Standard** vier Bereitstellungsslots unterstützt. 

## Inhaltsverzeichnis
- [Hinzufügen eines Bereitstellungsslots zu einer Website](#Add)
- [Konfiguration für Bereitstellungsslots](#AboutConfiguration)
- [Tauschen von Bereitstellungsslots](#Swap)
- [Zurücksetzung einer Produktionswebsite in die Stagingphase](#Rollback)
- [Löschen eines Websiteslots](#Delete)
- [Azure PowerShell-Cmdlets für Websiteslots](#PowerShell)
- [Befehle der plattformübergreifenden Azure-Befehlszeilenschnittstelle (xplat-cli) für Websiteslots](#CLI)

<a name="Add"></a>
##Hinzufügen eines Bereitstellungsslots zu einer Website

Die Website muss im Hostingplan **Standard** ausgeführt werden, damit mehrere Bereitstellungsslots aktiviert werden können. 

1. Klicken Sie auf der Seite "Schnellstart" oder auf der Seite "Auf einen Blick" für die Website auf **Einen neuen Bereitstellungsslot hinzufügen**. 
	
	![Add a new deployment slot][QGAddNewDeploymentSlot]
	
	> [AZURE.NOTE]
	> Falls sich die Website noch nicht im Modus **Standard** befindet, wird folgende Meldung angezeigt: **Um die Veröffentlichung in einer Stagingumgebung zu aktivieren, müssen Sie sich im Standardmodus befinden**. An diesem Punkt können Sie **Upgrade** auswählen und zur Registerkarte **Skalieren** der Website navigieren, bevor Sie den Vorgang fortsetzen.
	
2. Weisen Sie dem Slot im Dialogfeld **Neuen Bereitstellungsslot hinzufügen** einen Namen zu, und wählen Sie aus, ob Sie die Websitekonfiguration von einem anderen vorhandenen Bereitstellungsslot klonen möchten. Klicken Sie auf das Häkchen, um fortzufahren. 
	
	![Configuration Source][ConfigurationSource1]
	
	Bei der ersten Bereitstellung eines Slots stehen Ihnen nur zwei Optionen zur Verfügung: Klonen einer Konfiguration vom Standardslot in einen Produktionsslot oder kein Klonen der Konfiguration. 
	
	Nachdem Sie mehrere Slots erstellt haben, können Sie die Konfiguration von einem anderen Slot als dem Produktionsslot klonen:
	
	![Configuration sources][MultipleConfigurationSources]

5. Erweitern Sie in der Liste der Websites das Zeichen links vom Websitenamen, um den Bereitstellungsslot anzuzeigen. Dieser besteht aus dem Websitenamen gefolgt vom Namen des Bereitstellungsslots. 
	
	![Site List with Deployment Slot][SiteListWithStagedSite]
	
4. Wenn Sie auf den Namen des Website-Bereitstellungsslots klicken, wird wie bei jeder anderen Website eine Seite mit einigen Registerkarten geöffnet. <strong><i>Ihr-Websitename</i>(<i>Name-Bereitstellungsslot</i>)</strong> wird oben auf der Portalseite angezeigt, um Sie daran zu erinnern, dass Sie den Website-Bereitstellungsslot anzeigen.
	
	![Deployment Slot Title][StagingTitle]
	
5. Klicken Sie auf die Website-URL in der Dashboardansicht. Beachten Sie, dass der Bereitstellungsslot über einen eigenen Hostnamen verfügt und eine Livewebsite ist. Weitere Informationen darüber, wie Sie den öffentlichen Zugriff auf den Bereitstellungsslot beschränken, finden Sie unter [Azure-Websites - Blockieren des Webzugriffs auf Nicht-Produktionsslots](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/).

	-	 

Es gibt keinen Inhalt. Sie können die Bereitstellung im Slot von einem anderen Repositoryzweig oder einem ganz anderen Repository vornehmen. Darüber hinaus können Sie die Konfiguration des Slots ändern. Verwenden Sie für Aktualisierungen des Inhalts das Veröffentlichungsprofil oder die Bereitstellungsanmeldeinformationen, die dem Bereitstellungsslot zugeordnet sind.  Beispiel: Für die [Veröffentlichung in diesem Slot können Sie Git verwenden](http://azure.microsoft.com/de-de/documentation/articles/web-sites-publish-source-control/).

<a name="AboutConfiguration"></a>
##Konfiguration für Bereitstellungsslots
Wenn Sie die Konfiguration von einem anderen Bereitstellungsslot klonen, kann die geklonte Konfiguration bearbeitet werden. Im Folgenden ist die Konfiguration aufgeführt, die sich beim Austauschen der Slots ändert.

**Konfigurationseinstellunge, die sich beim Tauschen eines Slots ändern**:

- Allgemeine Einstellungen
- Verbindungszeichenfolgen
- Handlerzuordnungen
- Überwachungs- und Diagnoseeinstellungen

**Konfigurationseinstellunge, die sich beim Tauschen eines Slots nicht ändern**:

- Veröffentlichungsendpunkte
- Benutzerdefinierte Domänennamen
- SSL-Zertifikate und -Bindungen
- Skalierungseinstellungen

**Hinweise**:

- Mehrere Bereitstellungsslots sind nur für Websites im Webhostingplan **Standard** verfügbar.

- Wenn Ihre Website über mehrere Slots verfügt, können Sie den Hostingplan nicht ändern.

- Ein Slot, der in einen Produktionsslot überführt werden soll, muss genauso konfiguriert werden, wie er im Produktionsslot vorliegen soll.

- Standardmäßig wird ein Bereitstellungs-Steckplatz zur selben Datenbank wie die Produktionswebsite zeigen. Sie können jedoch den Bereitstellungsslot konfigurieren, dass er zu einer anderen Datenbank zeigt, indem Sie die Verbindungszeichenfolge(n) der Datenbank für den Bereitstellungsslot ändern. Sie können dann die ursprüngliche Verbindungszeichenfolge(n) der Datenbank auf dem Bereitstellungs-Steckplatz wiederherstellen, bevor Sie diesen in die Produktion überführen.


<a name="Swap"></a>
##Überführung von Bereitstellungsslots

1. Um Bereitstellungsslots zu tauschen, wählen Sie den Bereitstellungsslot in der Liste der auszutauschenden Websites aus, und klicken Sie auf der Befehlsleiste auf die Schaltfläche **Austauschen**. 
	
	![Swap Button][SwapButtonBar]
	
2. Das Dialogfenster "Bereitstellungen austauschen" wird angezeigt. Sie können im Dialogfeld auswählen, welcher Websiteslot die Quelle und welcher das Ziel sein soll.
	
	![Swap Deployments Dialog][SwapDeploymentsDialog]
	
3. Klicken Sie auf das Häkchen, um den Vorgang abzuschließen. Wenn der Vorgang abgeschlossen ist, wurden die Websiteslots ausgetauscht.


<a name="Rollback"></a>
##Zurücksetzung einer Produktionswebsite in die Stagingphase
Wenn Sie nach dieser Aktion Fehler in der Produktionswebsite feststellen, führen Sie ein Rollback in den Zustand vor dem Austausch aus, indem Sie beiden Slots sofort austauschen. 

<a name="Delete"></a>
##Löschen eines Websiteslots

Klicken Sie auf der Befehlsleiste unten auf der Seite des Azure-Websiteportals auf **Löschen**. Sie erhalten die Option, die Website und alle Bereitstellungsslots bzw. nur den Bereitstellungsslot zu löschen. 

![Delete a Site Slot][DeleteStagingSiteButton]

**Hinweise**:

- Die Skalierung ist für Nicht-Produktionsslots nicht verfügbar, sondern nur für Produktionsslots.

- Die Verwaltung verknüpfter Ressourcen wird für Nicht-Produktionsslots nicht unterstützt. 

- Sie können bei Bedarf weiterhin direkt im Produktionsslot veröffentlichen.

- Standardmäßig verwenden Ihre Bereitstellungsslots (Websites) dieselben Ressourcen wie Ihre Produktionsslots (Websites) und werden auf denselben virtuellen Computern ausgeführt. Wenn Sie Belastungstests auf einem Stagingslot ausführen, wird die Produktionsumgebung eine vergleichbare Belastung erfahren. 
	
	> [AZURE.NOTE] Sie können diese negativen Auswirkungen für einen Produktionsslot nur im [Azure-Vorschauportal](https://portal.azure.com) vermeiden, indem Sie den Nicht-Produktionsslot vorübergehend in einen anderen Webhostingplan verschieben.  Beachten Sie, dass Test- und Produktionsslot auch hier denselben Webhostingplan verwenden müssen, bevor Sie den Testslot in den Produktionsslot überführen können.

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>
##Azure PowerShell-Cmdlets für Websiteslots 

Azure PowerShell ist ein Modul, das Cmdlets für die Verwaltung von Azure über Windows PowerShell bietet, einschließlich Unterstützung bei der Verwaltung von Bereitstellungsslots für Azure-Websites. 

- Informationen zum Installieren und Konfigurieren von Azure PowerShell sowie zur Authentifizierung von Azure PowerShell mit Ihrem Microsoft Azure-Abonnement finden Sie unter [Installieren und Konfigurieren von Microsoft Azure PowerShell](http://www.windowsazure.com/de-de/documentation/articles/install-configure-powershell).  

- Um eine Liste der verfügbaren Cmdlets für Azure-Websites in PowerShell zu erhalten, rufen Sie  `help AzureWebsite` auf. 

----------

###Get-AzureWebsite
Das **Get-AzureWebsite** stellt Informationen über Azure-Websites für das aktuelle Abonnement bereit. Beispiel: 

`Get-AzureWebsite siteslotstest`

----------

###New-AzureWebsite
Sie können einen Websiteslot für jede Website im Standardmodus mithilfe des Cmdlets **New-AzureWebsite** erstellen und die Namen der Website und des Slots angeben. Geben Sie zudem dieselbe Region wie die Website für die Erstellung des Bereitstellungsslot an, wie in folgendem Beispiel. 

`New-AzureWebsite siteslotstest -Slot staging -Location "West US"`

----------

###Publish-AzureWebsiteProject
Sie können das Cmdlet **Publish-AzureWebsiteProject** wie in folgendem Beispiel für die Inhaltsbereitstellung verwenden. 

`Publish-AzureWebsiteProject -Name siteslotstest -Slot staging -Package [path].zip`

----------

###Show-AzureWebsite
Nachdem die Aktualisierungen des Inhalts und der Konfiguration auf den neuen Slot angewendet wurden, können Sie die Aktualisierungen validieren, indem Sie mithilfe des Cmdlet **Show-AzureWebsite** zum Slot navigieren.

`Show-AzureWebsite -Name siteslotstest -Slot staging`

----------

###Switch-AzureWebsiteSlot
Das Cmdlet **Switch-AzureWebsiteSlot** kann einen Überführungsvorgang ausführen, um den aktualisierten Bereitstellungsslot wie in folgendem Beispiel zur Produktionswebsite zu machen. Die Produktionswebsite wird keine Ausfallzeit haben sowie keinen Kaltstart erleben. 

`Switch-AzureWebsiteSlot -Name siteslotstest`

----------

###Remove-AzureWebsite
Wird ein Bereitstellungsslot nicht mehr benötigt, kann dieser mithilfe des Cmdlets **Remove-AzureWebsite** wie in folgendem Beispiel gelöscht werden.

`Remove-AzureWebsite -Name siteslotstest -Slot staging` 

----------

<!-- ======== XPLAT-CLI =========== -->

<a name="CLI"></a>
##Befehle der plattformübergreifenden Azure-Befehlszeilenschnittstelle (xplat-cli) für Websiteslots

Die plattformübergreifende Azure-Befehlszeilenschnittstelle (xplat-cli) bietet plattformübergreifende Befehle für das Arbeiten mit Azure, einschließlich Unterstützung für die Verwaltung von Bereitstellungsslots auf Azure-Websites. 

- Anweisungen zur Installation und Konfiguration von xplat-cli, einschließlich Informationen zur Verbindung von xplat-cli mit Ihrem Azure-Abonnement, finden Sie unter [Installieren und Konfigurieren der plattformübergreifenden Azure-Befehlszeilenschnittstelle](http://www.windowsazure.com/de-de/documentation/articles/xplat-cli). 

-  Um eine Liste der verfügbaren Befehle für Azure-Websites in "xplat-cli" zu erhalten, rufen Sie  `azure site -h` auf. 

----------
###azure site list
Zum Erhalten von Informationen zu Azure-Websites im aktuellen Abonnement rufen Sie **azure site list** wie in folgendem Beispiel auf.
 
`azure site list siteslotstest`

----------
###azure site create
Um einen Websiteslot für eine Website im Standardmodus zu erstellen, rufen Sie **azure site create** auf, und geben Sie den Namen einer vorhandenen Website und den Namen des zu erstellenden Slots wie im folgenden Beispiel an.

`azure site create siteslotstest --slot staging`

Um die Quellcodeverwaltung für den neuen Slot zu aktivieren, verwenden Sie die Option **--git** wie in folgendem Beispiel.
 
`azure site create --git siteslotstest --slot staging`

----------
###azure site swap
Um den aktualisierten Bereitstellungsslot zur Produktionswebsite zu machen, verwenden Sie für die Überführung den Befehl **azure site swap** wie im folgenden Beispiel. Die Produktionswebsite wird keine Ausfallzeit haben sowie keinen Kaltstart erleben. 

`azure site swap siteslotstest`

----------
###azure site delete
Um einen nicht mehr benötigten Bereitstellungsslot zu löschen, verwenden Sie wie im folgenden Beispiel den Befehl **azure site delete**.

`azure site delete siteslotstest --slot staging`

----------
## Nächste Schritte
[Azure-Websites - Blockieren des Webzugriffs auf Nicht-Produktionsslots](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)

[Microsoft Azure-Testversion](http://azure.microsoft.com/de-de/pricing/free-trial/)


<!-- IMAGES -->
[QGAddNewDeploymentSlot]:  ./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png
[AddNewDeploymentSlotDialog]: ./media/web-sites-staged-publishing/AddNewDeploymentSlotDialog.png
[ConfigurationSource1]: ./media/web-sites-staged-publishing/ConfigurationSource1.png
[MultipleConfigurationSources]: ./media/web-sites-staged-publishing/MultipleConfigurationSources.png
[SiteListWithStagedSite]: ./media/web-sites-staged-publishing/SiteListWithStagedSite.png
[StagingTitle]: ./media/web-sites-staged-publishing/StagingTitle.png
[SwapButtonBar]: ./media/web-sites-staged-publishing/SwapButtonBar.png
[SwapConfirmationDialog]:  ./media/web-sites-staged-publishing/SwapConfirmationDialog.png
[DeleteStagingSiteButton]: ./media/web-sites-staged-publishing/DeleteStagingSiteButton.png
[SwapDeploymentsDialog]: ./media/web-sites-staged-publishing/SwapDeploymentsDialog.png




<!--HONumber=42-->
