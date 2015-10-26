<properties
	pageTitle="Verwalten der Verfügbarkeit virtueller Computer | Microsoft Azure"
	description="Erfahren Sie, wie Sie mehrere virtuelle Computer verwenden, um hohe Verfügbarkeit für Ihre Azure-Anwendung sicherzustellen."
	services="virtual-machines"
	documentationCenter=""
	authors="kenazk"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/23/2015"
	ms.author="kenazk"/>

# Verwalten der Verfügbarkeit virtueller Computer

## Verstehen geplanter und nicht geplanter Wartung
Es gibt zwei Arten von Microsoft Azure Platform-Ereignissen, die sich auf die Verfügbarkeit von virtuellen Computern auswirken können: geplante und ungeplante Wartung.

- **Geplante Wartungsereignisse** sind regelmäßige Updates, die von Microsoft für die zugrunde liegende Azure-Plattform ausgeführt werden können, um die gesamte Verfügbarkeit, Leistung und Sicherheit der Plattforminfrastruktur, unter der die virtuellen Computer ausgeführt werden, zu verbessern. Die Mehrheit dieser Updates wird ohne Auswirkungen auf die virtuellen Computer oder Cloud-Dienste ausgeführt. Es gibt jedoch Instanzen, bei denen diese Updates einen Neustart des virtuellen Computers erfordern, um die erforderlichen Updates auf die Plattforminfrastruktur anzuwenden.

- **Ungeplante Wartungsereignisse** treten auf, wenn die Hardware oder physische Infrastruktur, die dem virtuellen Computer zugrunde liegt, einen Ausfall verursacht. Diese können Ausfälle des lokalen Netzwerks, Datenträgers oder andere Fehler auf Rackebene umfassen. Wenn ein solcher Fehler festgestellt wird, migriert die Azure-Plattform Ihren virtuellen Computer automatisch vom fehlerhaften physischen Computer, der den virtuellen Computer hostet, zu einem fehlerfreien physischen Computer. Diese Ereignisse treten selten auf, verursachen jedoch u. U. den Neustart des virtuellen Computers.

## Bewährte Methoden für die Entwicklung hoch verfügbarer Anwendungen
Um die Downtime aufgrund eines oder mehrerer dieser Ereignisse zu verringern, sollten Sie die folgenden bewährten Methoden befolgen, um die hohe Verfügbarkeit virtueller Computer zu gewährleisten:

* [Konfigurieren mehrerer virtueller Computer in einer Verfügbarkeitsgruppe für höhere Redundanz]
* [Konfigurieren einzelner Anwendungsebenen in separaten Verfügbarkeitsgruppen]
* [Kombinieren des Lastenausgleichs mit Verfügbarkeitsgruppen]
* [Vermeiden virtueller Computer, die eine Einzelinstanz darstellen, in Verfügbarkeitsgruppen]

### Konfigurieren mehrerer virtueller Computer in einer Verfügbarkeitsgruppe für höhere Redundanz
Um Redundanz für Ihre Anwendung zu gewährleisten, empfehlen wir die Gruppierung von zwei oder mehreren virtuellen Computern in einer Verfügbarkeitsgruppe. Durch diese Konfiguration wird sichergestellt, dass während eines geplanten oder ungeplanten Wartungsereignisses mindestens ein virtueller Computer verfügbar ist und die von der Azure-SLA zugesicherte Verfügbarkeit von 99,95 % eingehalten wird. Weitere Informationen zu Vereinbarungen zum Servicelevel finden Sie im Abschnitt "Clouddienste, virtuelle Computer und das virtuelle Netzwerk" unter [Vereinbarungen zum Servicelevel (SLAs)](../../../support/legal/sla/).

Jeder virtuelle Computer im Verfügbarkeitssatz wird einer Aktualisierungs- (Update Domain, UD) und Ausfalldomäne (Fault Domain, FD) der zugrunde liegenden Azure-Plattform zugewiesen. Einer bestimmten Verfügbarkeitsgruppe werden dabei fünf nicht vom Benutzer konfigurierbare Aktualisierungsdomänen zugeordnet, um Gruppen virtueller Computer und die zugehörige physische Hardware zu kennzeichnen, die gleichzeitig neu gestartet werden kann. Wenn innerhalb einer Verfügbarkeitsgruppe mehr als fünf virtuelle Computer konfiguriert sind, wird der sechste virtuelle Computer in derselben Aktualisierungsdomäne gespeichert wie der erste virtuelle Computer, der siebte in derselben Aktualisierungsdomäne wie der zweite virtuelle Computer usw. Während einer geplanten Wartung werden die Aktualisierungsdomänen möglicherweise nicht sequenziell neu gestartet. Es wird aber jeweils nur eine Aktualisierungsdomäne neu gestartet.

Durch Fehlerdomänen wird die Gruppe der virtuellen Computer definiert, die eine Stromquelle und einen Netzwerkswitch gemeinsam nutzen. Die innerhalb der Verfügbarkeitsgruppe konfigurierten virtuellen Computer werden standardmäßig auf zwei separate Fehlerdomänen verteilt. Auch wenn Verfügbarkeitsgruppen Ihre Anwendung nicht gänzlich vor Fehlern des Betriebssystems oder der Anwendung selbst schützen können, verringern sie doch die Auswirkungen potenzieller physischer Hardwarefehler, Netzwerkausfälle oder Stromunterbrechungen.

<!--Image reference-->
   ![Konfigurieren von Aktualisierungs-/Fehlerdomänen](./media/virtual-machines-manage-availability/ud-fd-configuration.png)

>[AZURE.NOTE]Anweisungen finden Sie unter [Konfigurieren einer Verfügbarkeitsgruppe für virtuelle Computer][].

### Konfigurieren einzelner Anwendungsebenen in separaten Verfügbarkeitsgruppen
Wenn die virtuellen Computer in der Verfügbarkeitsgruppe alle fast identisch sind und die Anwendung auf die gleiche Weise unterstützen, wird empfohlen, für jede einzelne Anwendungsebene eine Verfügbarkeitsgruppe zu konfigurieren. Wenn Sie eine Verfügbarkeitsgruppe mit zwei verschiedenen Ebenen anlegen, können alle virtuellen Computer derselben Anwendungsebene zur gleichen Zeit neu gestartet werden. Indem Sie mindestens zwei virtuelle Computer in einer Verfügbarkeitsgruppe für jede Ebene konfigurieren, gewährleisten Sie, dass mindestens ein virtueller Computer pro Ebene verfügbar ist.

Beispielsweise können Sie alle VMs aus dem Front-End der Anwendung, auf denen IIS, Apache, Nginx usw. ausgeführt wird, einer einzelnen Verfügbarkeitsgruppe zuordnen. Stellen Sie sicher, dass derselben Verfügbarkeitsgruppe nur virtuelle Front-End-Computer zugeordnet werden. Stellen Sie analog dazu sicher, dass virtuelle Computer der Datenebene ihre eigene Verfügbarkeitsgruppe haben. Dazu gehören beispielsweise virtuelle SQL Server-Computer oder virtuelle MySQL-Computer.

<!--Image reference-->
   ![Anwendungsebenen](./media/virtual-machines-manage-availability/application-tiers.png)


### Kombinieren des Lastenausgleichs mit Verfügbarkeitsgruppen
Kombinieren Sie den Azure-Lastenausgleich mit einer Verfügbarkeitsgruppe, um höchste Anwendungsresilienz zu erzielen. Der Azure-Lastenausgleich verteilt den Datenverkehr auf mehrere virtuelle Computer. In die virtuellen Computer der Standardebene ist der Azure-Lastenausgleich bereits integriert. Der Azure-Load Balancer ist jedoch nicht auf allen Ebenen des virtuellen Computers verfügbar. Weitere Informationen zum Lastenausgleich zwischen virtuellen Computern finden Sie unter [Lastenausgleich zwischen virtuellen Computern](../load-balance-virtual-machines.md).

Wenn der Lastenausgleich nicht für die gleichmäßige Verteilung des Datenverkehrs auf mehrere virtuelle Computer konfiguriert ist, wirkt sich ein geplantes Wartungsereignis schließlich auf den einzigen virtuellen Computer, der den Datenverkehr aufrecht erhält, aus und führt zu einem Ausfall der Anwendungsebene. Werden dagegen mehrere virtuelle Computer derselben Ebene demselben Lastenausgleich und derselben Verfügbarkeitsgruppe zugeordnet, wird der Datenverkehr kontinuierlich von mindestens einer Instanz aufrechterhalten.

### Vermeiden virtueller Computer, die eine Einzelinstanz darstellen, in Verfügbarkeitsgruppen
Vermeiden Sie es, virtuelle Computer, die eine Einzelinstanz darstellen, alleine einer Verfügbarkeitsgruppe zuzuordnen. Virtuelle Computer in dieser Konfiguration erfüllen nicht die zugesicherte SLA und sind während geplanter Azure-Wartungsereignisse nicht verfügbar. Beachten Sie, dass einzelne virtuelle Computerinstanzen innerhalb einer Verfügbarkeitsgruppe auch eine erweiterte E-Mail-Benachrichtigung im Rahmen der Benachrichtigung zur geplanten Wartung virtueller Computer mit mehreren Instanzen erhalten.

<!-- Link references -->
[Konfigurieren mehrerer virtueller Computer in einer Verfügbarkeitsgruppe für höhere Redundanz]: #configure-multiple-virtual-machines-in-an-availability-set-for-redundancy
[Konfigurieren einzelner Anwendungsebenen in separaten Verfügbarkeitsgruppen]: #configure-each-application-tier-into-separate-availability-sets
[Kombinieren des Lastenausgleichs mit Verfügbarkeitsgruppen]: #combine-the-load-balancer-with-availability-sets
[Vermeiden virtueller Computer, die eine Einzelinstanz darstellen, in Verfügbarkeitsgruppen]: #avoid-single-instance-virtual-machines-in-availability-sets
[Konfigurieren einer Verfügbarkeitsgruppe für virtuelle Computer]: virtual-machines-how-to-configure-availability.md

<!---HONumber=Oct15_HO3-->