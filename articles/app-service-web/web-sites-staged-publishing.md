<properties
	pageTitle="Einrichten von Stagingumgebungen für Web-Apps in Azure App Service"
	description="Erfahren Sie, wie Sie Stagingveröffentlichungen Ihrer Web-Apps in Azure App Service verwenden."
	services="app-service"
	documentationCenter=""
	authors="cephalin"
	writer="cephalin"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/09/2016"
	ms.author="cephalin"/>

# Einrichten von Stagingumgebungen für Web-Apps in Azure App Service
<a name="Overview"></a>

Sie können die Bereitstellung Ihrer Web-App in [App Service](http://go.microsoft.com/fwlink/?LinkId=529714) in einem separaten Bereitstellungsslot anstelle des Standard-Produktionsslots vornehmen, wenn die Ausführung im App-Service-Planmodus **Standard** oder **Premium** erfolgt. Bereitstellungsslots sind Live-Web-Apps mit eigenen Hostnamen. Elemente für Web-App-Inhalte und -Konfigurationen können zwischen zwei Bereitstellungsslots, einschließlich des Produktionsslots, ausgetauscht werden. Die Bereitstellung von Anwendungen in einem Bereitstellungsslot hat die folgenden Vorteile:

- Sie können Web-App-Änderungen in einem Stagingbereitstellungsslot überprüfen, bevor Sie die Web-App in den Produktionsslot überführen.

- Indem Sie eine Web-App zuerst in einem Slot bereitstellen und sie dann in den Produktionsslot überführen, stellen Sie sicher, dass alle Instanzen erst nach einer Anlaufzeit in den Produktionsslot übernommen werden. Dadurch vermeiden Sie Ausfallzeit bei der Bereitstellung der Web-App. Die Verkehrsweiterleitung ist nahtlos, und es werden keine Anfragen aufgrund von Überführungsoperationen fallengelassen. Dieser gesamte Wortflow kann durch Konfigurieren von [Auto Swap](#configure-auto-swap-for-your-web-app) automatisiert werden, wenn keine Überprüfung vor dem Austauschen erforderlich ist.

- Nach einem Austausch befindet sich im Slot mit der zuvor bereitgestellten Web-App jetzt die vorherige Produktions-Web-App. Wenn die in den Produktionsslot überführten Änderungen nicht Ihren Erwartungen entsprechen, können Sie denselben Austausch sofort noch einmal vornehmen, um die "letzte als gut befundene Website" zurückzuerhalten.

Jeder App Service-Planmodus unterstützt eine andere Anzahl von Bereitstellungsslots. Informationen zum Herausfinden, wie viele Slots Ihr Web-App-Modus unterstützt, finden Sie unter [App-Service-Preisdetails](/pricing/details/app-service/).

- Wenn Ihre Web-App mehrere Slots aufweist, können Sie den Modus nicht ändern.

- Die Skalierung ist für Nicht-Produktionsslots nicht verfügbar,

- Die Verwaltung verknüpfter Ressourcen wird für Nicht-Produktionsslots nicht unterstützt. Sie können diese negativen Auswirkungen für einen Produktionsslot nur im [Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715) vermeiden, indem Sie den Nicht-Produktionsslot vorübergehend in einen anderen App Service-Planmodus verschieben. Beachten Sie, dass der Nicht-Produktionsslot wieder im selben Modus freigegeben werden muss wie der Produktionsslot, bevor Sie die beiden Slots austauschen können.


[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

<a name="Add"></a>
## Hinzufügen eines Bereitstellungsslots zu einer Web-App ##

Die Web-App muss im **Standard**- oder **Premium**-Modus ausgeführt werden, damit mehrere Bereitstellungsslots aktiviert werden können.

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com/) das Blatt Ihrer Web-App.
2. Klicken Sie auf **Einstellungen** und dann auf **Bereitstellungsslots**. Klicken Sie dann auf dem Blatt **Bereitstellungsslots**auf **Bereitstellungsslot hinzufügen**.

	![Neuen Bereitstellungs-Steckplatz hinzufügen][QGAddNewDeploymentSlot]

	> [AZURE.NOTE]
	Wenn die Web-App nicht bereits im **Standard**- oder **Premium** -Modus ausgeführt wird, wird eine Meldung angezeigt, in der auf die unterstützten Modi für die Veröffentlichung in einer Stagingumgebung hingewiesen wird. An diesem Punkt können Sie **Upgrade** auswählen und zur Registerkarte **Skalierung** der Web-App navigieren, bevor Sie den Vorgang fortsetzen.

2. Weisen Sie dem Slot auf dem Blatt **Slot hinzufügen** einen Namen zu, und wählen Sie aus, ob Sie die Web-App-Konfiguration von einem anderen vorhandenen Bereitstellungsslot klonen möchten. Klicken Sie auf das Häkchen, um fortzufahren.

	![Konfigurationsquelle][ConfigurationSource1]

	Beim ersten Hinzufügen eines Slots stehen Ihnen nur zwei Optionen zur Verfügung: Klonen einer Konfiguration vom Standardslot in einen Produktionsslot oder kein Klonen der Konfiguration.

	Nachdem Sie mehrere Slots erstellt haben, können Sie die Konfiguration von einem anderen Slot als dem Produktionsslot klonen:

	![Konfigurationsquellen][MultipleConfigurationSources]

5. Klicken Sie auf dem Blatt **Bereitstellungsslots** auf den Bereitstellungsslot, um ein Blatt für den Slot mit einem Satz von Metriken und Konfiguration wie bei anderen Web-Apps zu öffnen. **Ihr Web-App-Name-Bereitstellungsslot-Name** wird oben im Blatt angezeigt, um Sie daran zu erinnern, dass der Bereitstellungsslot angezeigt wird.

	![Bereitstellungs-Steckplatz-Titel][StagingTitle]

5. Klicken Sie auf der Seite des Slots auf die App-URL. Beachten Sie, dass der Bereitstellungsslot über einen eigenen Hostnamen verfügt und eine Live-App ist. Weitere Informationen darüber, wie Sie den öffentlichen Zugriff auf den Bereitstellungsslot beschränken, finden Sie unter [App Service-Web-App – Blockieren des Webzugangs auf Nicht-Produktionsslots](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/).

Nach der Erstellung des Bereitstellungsslots ist kein Inhalt vorhanden. Sie können die Bereitstellung im Slot von einem anderen Repositoryzweig oder einem ganz anderen Repository vornehmen. Darüber hinaus können Sie die Konfiguration des Slots ändern. Verwenden Sie für Aktualisierungen des Inhalts das Veröffentlichungsprofil oder die Bereitstellungsanmeldeinformationen, die dem Bereitstellungsslot zugeordnet sind. Beispiel: [Für die Veröffentlichung in diesem Slot können Sie Git verwenden](web-sites-publish-source-control.md).

<a name="AboutConfiguration"></a>
## Konfiguration für Bereitstellungsslots ##
Wenn Sie die Konfiguration von einem anderen Bereitstellungsslot klonen, kann die geklonte Konfiguration bearbeitet werden. Darüber hinaus folgen einige Konfigurationselemente bei einem Austausch dem Inhalt (nicht Slot-spezifisch), während andere Konfigurationselemente nach einem Austausch beim Slot verbleiben (Slot-spezifisch). Im Folgenden ist die Konfiguration aufgeführt, die sich beim Austauschen der Slots ändert.

**Einstellungen, die ausgetauscht werden**:

- Allgemeine Einstellungen – z. B. Framework-Version, 32/64-Bit-Angabe, WebSockets
- App-Einstellungen (können so konfiguriert werden, dass sie beim Slot verbleiben)
- Verbindungszeichenfolgen (können so konfiguriert werden, dass sie beim Slot verbleiben)
- Handlerzuordnungen
- Überwachungs- und Diagnoseeinstellungen
- WebJobs-Inhalte

**Einstellungen, die nicht ausgetauscht werden**:

- Veröffentlichungsendpunkte
- Benutzerdefinierte Domänennamen
- SSL-Zertifikate und -Bindungen
- Skalierungseinstellungen
- WebJobs-Planer

Um eine App-Einstellung oder einen Verbindungszeichenfolge so zu konfigurieren, dass sie beim Slot verbleibt (nicht ausgetauscht wird), greifen Sie auf das Blatt **Anwendungseinstellungen** für einen bestimmten Slot zu, und aktivieren Sie dann das Kontrollkästchen **Slot-Einstellung** für die Konfigurationselemente, die beim Slot verbleiben sollten. Beachten Sie, dass das Markieren eines Konfigurationselements als Slot-spezifisch dafür sorgt, dass dieses Element in allen Bereitstellungsslots für die Web-App als nicht austauschbar festgelegt wird.

![Slot-Einstellungen][SlotSettings]

<a name="Swap"></a>
## Überführung von Bereitstellungsslots ##

>[AZURE.IMPORTANT] Bevor Sie eine Web-App aus einem Bereitstellungsslot in die Produktion überführen, stellen Sie sicher, dass alle nicht Slot-spezifischen Einstellungen im Austauschziel genau wie gewünscht konfiguriert sind.

1. Um Bereitstellungsslots auszutauschen, klicken Sie in der Befehlsleiste der Web-App oder der Befehlsleiste eines Bereitstellungsslots auf die Schaltfläche **Austauschen**. Stellen Sie sicher, dass die Austauschquelle und das Austauschziel ordnungsgemäß festgelegt wurden. Normalerweise ist das Austauschziel ein Produktionsslot.  

	![Schaltfläche Swap][SwapButtonBar]

3. Klicken Sie auf **OK**, um den Vorgang abzuschließen. Wenn der Vorgang abgeschlossen ist, wurden die Bereitstellungsslots ausgetauscht.

## Konfigurieren von Auto Swap für Ihre Web-App ##

Auto Swap rationalisiert DevOps-Szenarios, bei denen Ihre Web-App ständig ohne Kaltstarts und ohne Ausfallzeiten für Endkunden der Web-App bereitgestellt werden soll. Wenn ein Bereitstellungsslot für Auto Swap in der Produktion konfiguriert wurde, tauscht App Service bei jeder Codeaktualisierung per Push die Web-App in die Produktion, nachdem bereits eine Anlaufzeit im Slot verbraucht wurde.

>[AZURE.IMPORTANT] Wenn Sie Auto Swap für einen Slot aktivieren, stellen Sie sicher, dass die Slotkonfiguration genau der erforderlichen Konfiguration für den Zielslot (normalerweise der Produktionsslot) entspricht.

Das Konfigurieren von Auto Swap für einen Slot ist einfach. Führen Sie die folgenden Schritte aus:

1. Wählen Sie auf dem Blatt **Bereitstellungsslots** einen Nicht-Produktionsslot aus, und klicken Sie für das Blatt dieses Slots auf **Alle Einstellungen**.  

	![][Autoswap1]

2. Klicken Sie auf **Anwendungseinstellungen**. Wählen Sie**Ein** für **Auto Swap**, wählen Sie unter **Auto Swap-Slot** den gewünschten Zielslot aus, und klicken Sie in der Befehlsleiste auf **Speichern**. Stellen Sie sicher, dass die Konfiguration für den Slot genau der für den Zielslot vorgesehenen Konfiguration entspricht.

	Auf der Registerkarte **Benachrichtigungen** wird in grüner Schrift der Text **ERFOLGREICH** angezeigt, sobald der Vorgang abgeschlossen wurde.

	![][Autoswap2]

	>[AZURE.NOTE] Um Auto Swap für Ihre Web-App zu testen, können Sie zunächst unter **Auto Swap-Slot** einen Nicht-Produktionszielslot auswählen, um sich mit der Funktion vertraut zu machen.

3. Führen Sie einen Code-Push für diesen Bereitstellungsslot aus. Auto Swap erfolgt nach kurzer Zeit, und die Aktualisierung wird an der URL des Zielslots wiedergegeben.

<a name="Multi-Phase"></a>
## Verwenden eines mehrstufigen Austauschs für Ihre Web-App ##

Ein mehrstufiger Austausch steht zur Verfügung, um die Validierung im Kontext von Konfigurationselementen zu vereinfachen, die in einem Slot wie Verbindungszeichenfolgen verbleiben sollen. In diesen Fällen kann es hilfreich sein, solche Konfigurationselemente aus dem Austauschziel auf die Austauschquelle anzuwenden und vor dem tatsächlichen Austausch zu validieren. Sobald die Konfigurationselemente des Austauschziels auf die Austauschquelle angewendet wurden, können die verfügbaren Aktionen entweder zum Abschließen des Austauschs oder zum Wiederherstellen der ursprünglichen Konfiguration der Austauschquelle genutzt werden, wodurch der Austausch außerdem abgebrochen wird. Beispiele für Azure PowerShell-Cmdlets für den mehrstufigen Austausch sind im Abschnitt „Azure-PowerShell-Cmdlets für Bereitstellungsslots“ enthalten.

<a name="Rollback"></a>
## So setzen Sie eine Produktions-App nach dem Austausch wieder zurück ##

Wenn nach dieser Aktion Fehler in der Produktionswebsite feststellen, führen Sie ein Rollback in den Zustand vor dem Austausch aus, indem Sie dieselben beiden Slots sofort austauschen.

<a name="Warm-up"></a>
## Benutzerdefiniertes Aufwärmen vor dem Austausch ##

Einige Apps erfordern benutzerdefinierte Aufwärmaktionen. Über das Konfigurationselement applicationInitialization in der Datei „web.config“ können Sie benutzerdefinierte Initialisierungsaktionen angeben, die vor dem Empfang einer Anforderung ausgeführt werden. Der Austauschvorgang wartet dann, bis diese benutzerdefinierte Aufwärmphase abgeschlossen ist. Im Folgenden finden Sie ein Beispielfragment aus der Datei „Web.config“.

    <applicationInitialization>
        <add initializationPage="/" hostName="[web app hostname]" />
        <add initializationPage="/Home/About" hostname="[web app hostname]" />
    </applicationInitialization>

<a name="Delete"></a>
## So löschen Sie einen Bereitstellungsslot##

Klicken Sie auf dem Blatt für den Bereitstellungsslot in der Befehlsleiste auf **Löschen**.

![Löschen eines Bereitstellungsslots][DeleteStagingSiteButton]

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>
## Azure PowerShell-Cmdlets für Bereitstellungsslots

Azure PowerShell ist ein Modul, das Cmdlets für die Verwaltung von Azure über Windows PowerShell bietet, einschließlich Unterstützung bei der Verwaltung von Web-App-Bereitstellungsslots für Azure App Service.

- Informationen zum Installieren und Konfigurieren von Azure PowerShell sowie zur Authentifizierung von Azure PowerShell mit Ihrem Azure-Abonnement finden Sie unter [Installieren und Konfigurieren von Microsoft Azure PowerShell](../powershell-install-configure.md).  

----------

### Web-App erstellen

```
New-AzureRmWebApp -ResourceGroupName [resource group name] -Name [web app name] -Location [location] -AppServicePlan [app service plan name]
```

----------

### Erstellen eines Bereitstellungsslots für eine Web-App

```
New-AzureRmWebAppSlot -ResourceGroupName [resource group name] -Name [web app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

----------

### Initiieren des mehrstufigen Austauschs und Zuweisen einer Zielslotkonfiguration zu einem Quellslot

```
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [web app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

----------

### Zurücksetzen der ersten Phase des mehrstufigen Austauschs und Wiederherstellen der Quellslotkonfiguration

```
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [web app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

----------

### Überführen von Bereitstellungsslots

```
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [web app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

----------

### Löschen von Bereitstellungsslots

```
Remove-AzureRmResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [web app name]/[slot name] -ApiVersion 2015-07-01
```

----------

<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>
## Befehle der Azure-Befehlszeilenschnittstelle (Azure-CLI) für Bereitstellungsslots

Die Azure-Befehlszeilenschnittstelle bietet plattformübergreifende Befehle für das Arbeiten mit Azure, einschließlich Unterstützung für die Verwaltung von Web-App-Bereitstellungsslots.

- Anweisungen zur Installation und Konfiguration der Azure-Befehlszeilenschnittstelle, einschließlich Informationen zur Verbindung der Azure-Befehlszeilenschnittstelle mit Ihrem Azure-Abonnement, finden Sie unter [Installieren und Konfigurieren der Azure-Befehlszeilenschnittstelle](../xplat-cli-install.md).

-  Um eine Liste der verfügbaren Befehle für Azure App Service in der Azure-Befehlszeilenschnittstelle zu erhalten, rufen Sie `azure site -h` auf.

----------
### azure site list
Um Informationen zu den Web-Apps im aktuellen Abonnement zu erhalten, rufen Sie **azure site list** auf, wie in folgendem Beispiel.

`azure site list webappslotstest`

----------
### azure site create
Um einen Bereitstellungsslot zu erstellen, rufen Sie **azure site create** auf, und geben Sie den Namen einer vorhandenen Web-App und den Namen des zu erstellenden Slots wie im folgenden Beispiel an.

`azure site create webappslotstest --slot staging`

Um die Quellcodeverwaltung für den neuen Steckplatz zu aktivieren, verwenden Sie die Option **--git** wie in folgendem Beispiel.

`azure site create --git webappslotstest --slot staging`

----------
### azure site swap
Um den aktualisierten Bereitstellungsslot zur Produktions-App zu machen, verwenden Sie für die Überführung den Befehl **azure site swap** wie im folgenden Beispiel. Die Produktions-App weist keine Ausfallzeiten auf und durchläuft keinen Kaltstart.

`azure site swap webappslotstest`

----------
### azure site delete
Um einen nicht mehr benötigten Bereitstellungsslot zu löschen, verwenden Sie wie im folgenden Beispiel den Befehl **azure site delete**.

`azure site delete webappslotstest --slot staging`

----------

>[AZURE.NOTE] Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Nächste Schritte ##
[Azure App Service-Web-App – Blockieren des Webzugriffs auf Nicht-Produktionsslots](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)

[Kostenlose Microsoft Azure-Testversion](/pricing/free-trial/)

## Änderungen
* Hinweise zu den Änderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).

<!-- IMAGES -->
[QGAddNewDeploymentSlot]: ./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png
[AddNewDeploymentSlotDialog]: ./media/web-sites-staged-publishing/AddNewDeploymentSlotDialog.png
[ConfigurationSource1]: ./media/web-sites-staged-publishing/ConfigurationSource1.png
[MultipleConfigurationSources]: ./media/web-sites-staged-publishing/MultipleConfigurationSources.png
[SiteListWithStagedSite]: ./media/web-sites-staged-publishing/SiteListWithStagedSite.png
[StagingTitle]: ./media/web-sites-staged-publishing/StagingTitle.png
[SwapButtonBar]: ./media/web-sites-staged-publishing/SwapButtonBar.png
[SwapConfirmationDialog]: ./media/web-sites-staged-publishing/SwapConfirmationDialog.png
[DeleteStagingSiteButton]: ./media/web-sites-staged-publishing/DeleteStagingSiteButton.png
[SwapDeploymentsDialog]: ./media/web-sites-staged-publishing/SwapDeploymentsDialog.png
[Autoswap1]: ./media/web-sites-staged-publishing/AutoSwap01.png
[Autoswap2]: ./media/web-sites-staged-publishing/AutoSwap02.png
[SlotSettings]: ./media/web-sites-staged-publishing/SlotSetting.png
 

<!---HONumber=AcomDC_0309_2016-->