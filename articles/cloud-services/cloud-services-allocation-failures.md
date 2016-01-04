<properties
	pageTitle="Behandeln von Zuordnungsfehlern bei Clouddiensten | Microsoft Azure"
	description="Behandeln von Zuordnungsfehlern bei der Bereitstellung von Clouddiensten in Azure"
	services="azure-service-management, cloud-services"
	documentationCenter=""
	authors="kenazk"
	manager="drewm"
	editor=""
	tags="top-support-issue"/>

<tags
	ms.service="cloud-services"
	ms.workload="na"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/04/2015"
	ms.author="kenazk"/>



# Behandeln von Zuordnungsfehlern bei der Bereitstellung von Clouddiensten in Azure

## Zusammenfassung
Wenn Sie Instanzen für einen Clouddienst bereitstellen oder neue Instanzen von Web- oder Workerrollen hinzufügen, weist Microsoft Azure Compute-Ressourcen zu. Unter Umständen erhalten Sie beim Ausführen dieser Schritte auch dann gelegentlich Fehler, bevor Sie die Grenzwerte des Azure-Abonnements erreichen. In diesem Artikel werden die Ursachen einiger häufig auftretender Zuordnungsfehler erläutert und mögliche Abhilfemaßnahmen vorgeschlagen. Diese Informationen können auch hilfreich sein, wenn Sie die Bereitstellung Ihrer Dienste planen.

Wenn Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie Ihre Frage im [MSDN Azure-Forum oder im Stack Overflow-Forum](http://azure.microsoft.com/support/forums/) stellen, um dort Hilfe von Azure-Experten zu erhalten. Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Support-Website](http://azure.microsoft.com/support/options/) auf, und klicken Sie auf **Support erhalten**.

### Hintergrund – Funktionsweise der Zuordnung
Für die Server in Azure-Rechenzentren wird eine Partitionierung in Cluster vorgenommen. Es wird eine neue Zuordnungsanforderung für einen Clouddienst in mehreren Clustern versucht. Wenn die erste Instanz in einem Clouddienst bereitgestellt wird (entweder in Staging oder Produktion), wird der Clouddienst fest mit einem Cluster verknüpft. Alle weiteren Bereitstellungen für den Clouddienst werden im selben Cluster ausgeführt. In diesem Artikel wird dies als „verknüpft mit einem Cluster“ bezeichnet. In Diagramm 1 unten ist eine normale Zuordnung dargestellt, für die versucht wird, sie für mehrere Cluster durchzuführen. In Diagramm 2 ist eine Zuordnung zu sehen, die mit Cluster 2 verknüpft ist, da dies der Ort ist, an dem der vorhandene Clouddienst „CS\_1“ gehostet wird.

![Zuordnungsdiagramm](./media/cloud-services-allocation-failure/Allocation1.png)

### Gründe für Zuordnungsfehler
Wenn eine Zuordnungsanforderung mit einem Cluster verknüpft ist, ist die Wahrscheinlichkeit höher, dass keine freien Ressourcen gefunden werden, da der verfügbare Ressourcenpool auf einen Cluster beschränkt ist. Falls Ihre Zuordnungsanforderung mit einem Cluster verknüpft ist, der von Ihnen angeforderte Ressourcentyp für diesen Cluster aber nicht unterstützt wird, schlägt die Anforderung auch dann fehl, wenn der Cluster über eine freie Ressource verfügt. In Diagramm 3 unten ist der Fall dargestellt, in dem eine verknüpfte Zuordnung fehlschlägt, da der einzige Kandidatencluster keine freien Ressourcen aufweist. In Diagramm 4 ist der Fall dargestellt, in dem eine verknüpfte Zuordnung fehlschlägt, weil der einzige Kandidatencluster die angeforderte Größe des virtuellen Computers nicht unterstützt, obwohl der Cluster über freie Ressourcen verfügt.

![Verknüpfte Zuordnung, Fehler](./media/cloud-services-allocation-failure/Allocation2.png)

## Behandeln von Zuweisungsfehlern für Clouddienste
### Fehlermeldung
Möglicherweise erhalten Sie die folgende Fehlermeldung:

	"Azure operation '{operation id}' failed with code Compute.ConstrainedAllocationFailed. Details: Allocation failed; unable to satisfy constraints in request. The requested new service deployment is bound to an Affinity Group, or it targets a Virtual Network, or there is an existing deployment under this hosted service. Any of these conditions constrains the new deployment to specific Azure resources. Please retry later or try reducing the VM size or number of role instances. Alternatively, if possible, remove the aforementioned constraints or try deploying to a different region."

### Häufige Probleme
Dies sind häufig vorkommende Zuordnungsszenarios, die bewirken, dass eine Zuordnungsanforderung mit einem einzelnen Cluster „verknüpft“ wird.

- Bereitstellung in Stagingslots: Besitzt ein Clouddienst eine Bereitstellung in einem der Slots, wird der gesamte Clouddienst mit einem bestimmten Cluster verknüpft. Wenn also bereits eine Bereitstellung im Produktionsslot vorhanden ist, kann eine neue Stagingbereitstellung nur demselben Cluster wie der Produktionsslot zugeordnet werden. Wenn der Cluster seine Kapazität nahezu erreicht hat, schlägt die Anforderung möglicherweise fehl. 
 
- Skalierung: Beim Hinzufügen neuer Instanzen zu einem vorhandenen Clouddienst muss die Zuordnung in demselben Cluster erfolgen. Kleine Skalierungsanfragen können i. d. R. zugeordnet werden können, dies gilt aber nicht immer. Wenn der Cluster seine Kapazität nahezu erreicht hat, schlägt die Anforderung möglicherweise fehl.
	
- Affinitätsgruppe: Eine neue Bereitstellung für einen leeren Clouddienst kann von dem Fabric in jedem Cluster in dieser Region zugeordnet werden, es sei denn, der Clouddienst ist mit einer Affinitätsgruppe verknüpft. Bereitstellungen in derselben Affinitätsgruppe werden auch im selben Cluster versucht. Wenn der Cluster seine Kapazität nahezu erreicht hat, schlägt die Anforderung möglicherweise fehl.
	
- Affinitätsgruppenbasiertes VNET: Ältere virtuelle Netzwerke waren mit Affinitätsgruppen anstelle von Regionen verknüpft, und Clouddienste in diesen virtuellen Netzwerken wurden mit dem Cluster der Affinitätsgruppe verknüpft. Bereitstellungen für diesen Typ von virtuellen Netzwerken werden im verknüpften Cluster versucht. Wenn der Cluster seine Kapazität nahezu erreicht hat, schlägt die Anforderung möglicherweise fehl.

## Lösungen

1. Erneute Bereitstellung in einem neuen Clouddienst: Diese Lösung ist wahrscheinlich am erfolgreichsten, da hierbei die Plattform aus allen Clustern in dieser Region auswählen kann.
	
	- Bereitstellen der Workload in einem neuen Clouddienst  
	
	- Aktualisieren des CNAME- oder A-Datensatzes zur Umleitung des Datenverkehrs an den neuen Clouddienst
		
	- Wenn kein Datenverkehr mehr an die alte Website geleitet wird, können Sie den alten Clouddienst löschen. Bei dieser Lösung sollten keine Ausfallzeiten entstehen.

2. Löschen von Produktions- und Stagingslots: Bei dieser Lösung behalten Sie Ihren vorhandenen DNS-Namen, sie führt aber zu Ausfallzeiten der Anwendung.
	
	- Löschen Sie die Produktions- und Stagingslots eines vorhandenen Clouddiensts, sodass der Clouddienst leer ist. 
	
	- Erstellen Sie dann eine neue Bereitstellung im vorhandenen Clouddienst. Damit wird eine erneute Zuweisung auf allen Clustern in der Region versucht. Stellen Sie sicher, dass der Clouddienst mit keiner Affinitätsgruppe verknüpft ist.

3. Reservierte IP: Bei dieser Lösung behalten Sie Ihre IP-Adresse bei, sie führt aber zu Ausfallzeiten der Anwendung.
	
	- Erstellen Sie eine reservierte IP-Adressen für die vorhandene Bereitstellung mithilfe von PowerShell: 

	```
	New-AzureReservedIP -ReservedIPName {new reserved IP name} -Location {location} -ServiceName {existing service name}
	```
		
	- Befolgen Sie Lösung 2 oben, und geben Sie die neue reservierte IP-Adresse in der CSCFG des Diensts an.

4. Entfernen der Affinitätsgruppe für neue Bereitstellungen: Affinitätsgruppen werden nicht mehr empfohlen. Führen Sie die Schritte für Lösung 1 oben aus, um einen neuen Clouddienst bereitzustellen. Stellen Sie sicher, dass der Clouddienst zu keiner Affinitätsgruppe gehört.

5. Konvertieren in ein regionales virtuelles Netzwerk: Weitere Informationen finden Sie unter [Migrieren von Affinitätsgruppen zu einem regionalen virtuellen Netzwerk (VNet)](https://azure.microsoft.com/documentation/articles/virtual-networks-migrate-to-regional-vnet/).

## Zusätzliche Ressourcen
### Kontaktieren des Azure-Kundensupports

Suchen Sie in den Azure-Foren bei [MSDN und Stack Overflow](http://azure.microsoft.com/support/forums/) weitere Informationen, falls Sie Ihr Azure-Problem mit diesem Artikel nicht beheben konnten. Sie können auch einen Azure-Supportfall zu Ihrem Problem erstellen. Rufen Sie die [Azure-Support-Website](http://azure.microsoft.com/support/options/) auf, und klicken Sie auf „Support erhalten“. Informationen zur Nutzung von Azure-Support finden Sie unter [Microsoft Azure-Support-FAQ](http://azure.microsoft.com/support/faq/).

<!---HONumber=Nov15_HO4-->