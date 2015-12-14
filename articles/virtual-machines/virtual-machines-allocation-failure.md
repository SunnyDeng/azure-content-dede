<properties
	pageTitle="Problembehandlung für einen Zuordnungsfehler bei virtuellen Computern | Microsoft Azure"
	description="Problembehandlung für einen Zuordnungsfehler beim Erstellen, Neustarten oder Ändern der Größe eines virtuellen Computers in Azure"
	services="virtual-machines, azure-resource-manager"
	documentationCenter=""
	authors="jiangchen79"
	manager="felixwu"
	editor=""
	tags="top-support-issue"/>

<tags
	ms.service="virtual-machines"
	ms.workload="na"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/29/2015"
	ms.author="kenazk"/>



# Problembehandlung für einen Zuordnungsfehler beim Erstellen, Neustarten oder Ändern der Größe eines virtuellen Computers in Azure

Wenn Sie einen virtuellen Computer erstellen, beendete virtuelle Computer (Aufhebung der Zuordnung) neu starten, oder die Größe eines virtuellen Computers ändern, weist Microsoft Azure Ihrem Abonnement Compute-Ressourcen zu. Unter Umständen erhalten Sie beim Ausführen dieser Schritte auch dann gelegentlich Fehler, bevor Sie die Grenzwerte des Azure-Abonnements erreichen. In diesem Artikel werden die Ursachen einiger häufig auftretender Zuordnungsfehler erläutert und mögliche Abhilfemaßnahmen vorgeschlagen. Diese Informationen können auch hilfreich sein, wenn Sie die Bereitstellung Ihrer Dienste planen.

Wenn Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie Ihre Frage im [MSDN Azure-Forum oder im Stack Overflow-Forum](http://azure.microsoft.com/support/forums/) stellen, um dort Hilfe von Azure-Experten zu erhalten. Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Support-Website](http://azure.microsoft.com/support/options/) auf, und klicken Sie auf **Support erhalten**.

Im Abschnitt „Behandeln allgemeiner Zuordnungsfehler“ finden Sie Schritte für häufig auftretende Probleme. Der Abschnitt „Problembehandlung bei spezifischen Zuordnungsfehlerszenarien“ enthält Lösungsschritte für spezifische Fehlermeldungen. Bevor Sie beginnen, lesen Sie die Hintergrundinformationen zur Funktionsweise der Zuordnung und zu den Ursachen von Zuordnungsfehlern.

## Hintergrundinformationen
### Funktionsweise der Zuordnung
Für die Server in Azure-Rechenzentren wird eine Partitionierung in Cluster vorgenommen. Normalerweise wird versucht, eine Zuordnungsanforderung in mehreren Clustern durchzuführen. Es ist aber möglich, dass bestimmte Einschränkungen der Zuordnungsanforderung die Azure-Plattform zu dem Versuch zwingen, die Anforderung nur für einen Cluster durchzuführen. In diesem Artikel wird dies als „verknüpft mit einem Cluster“ bezeichnet. In Diagramm 1 unten ist eine normale Zuordnung dargestellt, für die versucht wird, sie für mehrere Cluster durchzuführen. In Diagramm 2 ist eine Zuordnung zu sehen, die mit Cluster 2 verknüpft ist, da dies der Ort ist, an dem der vorhandene Clouddienst „CS\_1“ oder die Verfügbarkeitsgruppe gehostet wird. ![Zuordnungsdiagramm](./media/virtual-machines-allocation-failure/Allocation1.png)

### Gründe für Zuordnungsfehler
Wenn eine Zuordnungsanforderung mit einem Cluster verknüpft ist, ist die Wahrscheinlichkeit höher, dass keine freien Ressourcen gefunden werden, da der verfügbare Ressourcenpool kleiner ist. Falls Ihre Zuordnungsanforderung mit einem Cluster verknüpft ist, der von Ihnen angeforderte Ressourcentyp für diesen Cluster aber nicht unterstützt wird, schlägt die Anforderung auch dann fehl, wenn der Cluster über eine freie Ressource verfügt. In Diagramm 3 unten ist der Fall dargestellt, in dem eine verknüpfte Zuordnung fehlschlägt, da der einzige Kandidatencluster keine freien Ressourcen aufweist. In Diagramm 4 ist der Fall dargestellt, in dem eine verknüpfte Zuordnung fehlschlägt, weil der einzige Kandidatencluster die angeforderte Größe des virtuellen Computers nicht unterstützt, obwohl der Cluster über freie Ressourcen verfügt. ![Verknüpfte Zuordnung, Fehler](./media/virtual-machines-allocation-failure/Allocation2.png)

## Problembehandlung bei allgemeinen Zuordnungsfehlern im klassischen Bereitstellungsmodell

Mit diesen Schritten können Sie viele Zuordnungsfehler virtueller Computer beheben.

- Ändern Sie die Größe des virtuellen Computers.<br> Klicken Sie auf „Alle durchsuchen“ > „Virtuelle Computer (klassisch)“ > „Ihr virtueller Computer“ > „Einstellungen“ > **Größe**. Ausführliche Schritte finden Sie unter [Ändern der Größe des virtuellen Computers](https://msdn.microsoft.com/library/dn168976.aspx).

- Löschen Sie alle virtuellen Computer aus dem Clouddienst, und erstellen Sie virtuelle Computer neu.<br> Klicken Sie auf „Alle durchsuchen“ > „Virtuelle Computer (klassisch)“ > „Ihr virtueller Computer“ > „Löschen“. Klicken Sie auf „Neu“ > „Compute“ > [Virtuelles Computerimage].

## Problembehandlung für allgemeine Zuordnungsfehler im Ressourcen-Manager-Bereitstellungsmodell

Mit diesen Schritten können Sie viele Zuordnungsfehler virtueller Computer beheben.

- Geben Sie die Zuordnung aller virtuellen Computer einer Verfügbarkeitsgruppe frei, und starten Sie die einzelnen virtuellen Computer dann neu.<br> Zum Beenden: Klicken Sie auf „Ressourcengruppen“ > „Ihre Ressourcengruppe“ > „Ressourcen“ > „Ihre Verfügbarkeitsgruppe“ > „Virtual Machines“ > „Ihr virtueller Computer“ > „Beenden“.

	Wählen Sie nach dem Beenden aller virtuellen Computer den ersten virtuellen Computer aus, und klicken Sie auf „Starten“.

## Problembehandlung bei spezifischen Zuordnungsfehlerszenarien im klassischen Bereitstellungsmodell
Dies sind häufig vorkommende Zuordnungsszenarien, die bewirken, dass eine Zuordnungsanforderung „verknüpft“ wird. Die einzelnen Szenarien werden weiter unten in diesem Artikel genauer erläutert.

- Ändern der Größe eines virtuellen Computers oder Hinzufügen zusätzlicher virtueller Computer oder Rolleninstanzen zu einem vorhandenen Clouddienst
- Neustarten beendeter virtueller Computer (Aufhebung der Zuordnung) – Teilaufhebung der Zuordnung
- Neustarten beendeter virtueller Computer (Aufhebung der Zuordnung) – Vollständige Aufhebung der Zuordnung
- Staging-/Produktionsbereitstellungen (nur Platform-as-a-Service)
- Affinitätsgruppe – Nähe von virtuellem Computer und Dienst
- Auf Affinitätsgruppe basierendes virtuelles Netzwerk

Wenn Sie einen Zuordnungsfehler erhalten, sollten Sie prüfen, ob eines der beschriebenen Szenarien für Ihren Fall zutrifft. Verwenden Sie den von der Azure-Plattform zurückgegebenen Zuordnungsfehler, um das entsprechende Szenario zu identifizieren. Falls Ihre Anforderung verknüpft ist, können Sie versuchen, einige Verknüpfungseinschränkungen zu beseitigen. So öffnen Sie Ihre Anforderung für mehr Cluster und erhöhen die Chancen für eine erfolgreiche Zuordnung.

Solange für den Fehler nicht „Die angeforderte Größe des virtuellen Computers wird nicht unterstützt“ angegeben wird, können Sie den Vorgang im Allgemeinen immer zu einem späteren Zeitpunkt wiederholen. Unter Umständen wurden im Cluster in der Zwischenzeit genügend Ressourcen für die Verarbeitung Ihrer Anforderung bereitgestellt. Falls das Problem darin besteht, dass die angeforderte Größe des virtuellen Computers nicht unterstützt wird, sollten Sie es mit einer anderen Größe probieren. Andernfalls ist es nur noch möglich, die Verknüpfungseinschränkung zu entfernen.

Zwei häufig auftretende Fehlerszenarien hängen mit der Affinitätsgruppe zusammen. In der Vergangenheit wurde die Affinitätsgruppe verwendet, um die Nähe zu virtuellen Computern und Dienstinstanzen zu schaffen oder um die Erstellung eines Virtual Network (VNet) zu ermöglichen. Seit der Einführung von regionalen Virtual Networks wird die Affinitätsgruppe zum Erstellen eines Virtual Network nicht mehr benötigt. Dank der Reduzierung der Netzwerklatenz in der Azure-Infrastruktur hat sich die Empfehlung für die Verwendung der Affinitätsgruppe zum Schaffen der Nähe von virtuellen Computern und Diensten geändert.

In Diagramm 5 unten ist die Taxonomie der Zuordnungsszenarios (mit Verknüpfung) dargestellt. ![Verknüpfte Zuordnung, Taxonomie](./media/virtual-machines-allocation-failure/Allocation3.png)

> [AZURE.NOTE]Der Fehler wird in den einzelnen Zuordnungsszenarien in Kurzform aufgelistet. Ausführliche Fehlerzeichenfolgen finden Sie im [Anhang](#appendix).

### Zuordnungsszenario: Ändern der Größe eines virtuellen Computers oder Hinzufügen zusätzlicher virtueller Computer oder Rolleninstanzen zu einem vorhandenen Clouddienst
**Fehler**

Upgrade\_VMSizeNotSupported* oder GeneralError*

**Ursache der Verknüpfung mit dem Cluster**

Die Anforderung der Größenänderung eines virtuellen Computers oder des Hinzufügens eines virtuellen Computers oder einer Rolleninstanz zu einem vorhandenen Clouddienst muss für den Originalcluster versucht werden, der den vorhandenen Clouddienst hostet. Die Erstellung eines neuen Clouddiensts ermöglicht der Azure-Plattform das Suchen nach einem anderen Cluster, der über eine freie Ressource verfügt oder der die angeforderte Größe des virtuellen Computers unterstützt.

**Problemumgehung**

Probieren Sie es mit einer anderen Größe des virtuellen Computers, wenn der Fehler „Upgrade\_VMSizeNotSupported*“ lautet. Wenn das Verwenden einer anderen Größe des virtuellen Computers keine Option ist, dafür aber die Verwendung einer anderen virtuellen IP-Adresse (VIP), erstellen Sie einen neuen Clouddienst zum Hosten des neuen virtuellen Computers. Fügen Sie den neuen Clouddienst dem regionalen Virtual Network hinzu, auf dem die vorhandenen virtuellen Computer ausgeführt werden. Falls Ihr vorhandener Clouddienst kein regionales virtuelles Netzwerk verwendet, können Sie trotzdem ein neues virtuelles Netzwerk für den neuen Clouddienst erstellen und dann [für das vorhandene VNET eine Verbindung mit dem neuen VNET herstellen](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Weitere Informationen zum [regionalen virtuellen Netzwerk](http://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/) (in englischer Sprache).

Wenn der Fehler „GeneralError*“ lautet, wird der Typ der Ressource (z. B. eine bestimmte Größe des virtuellen Computers) vom Cluster wahrscheinlich unterstützt, aber der Cluster verfügt derzeit nicht über freie Ressourcen. Versuchen Sie ähnlich wie oben, die gewünschten Compute-Ressourcen per Erstellung eines neuen Clouddiensts hinzuzufügen (für den neuen Clouddienst muss eine andere VIP verwendet werden), und verwenden Sie das regionale Virtual Network zum Verbinden Ihrer Clouddienste.

### Zuordnungsszenario: Neustarten beendeter virtueller Computer (Aufhebung der Zuordnung) - Teilaufhebung der Zuordnung

**Fehler**

GeneralError*

**Ursache der Verknüpfung mit dem Cluster**

Die **Teilaufhebung** der Zuordnung bedeutet, dass Sie mindestens einen virtuellen Computer beendet haben (Aufhebung der Zuordnung), aber **nicht alle** virtuellen Computer eines Clouddiensts. Wenn Sie einen virtuellen Computer beenden (Aufhebung der Zuordnung), werden die zugeordneten Ressourcen freigegeben. Das Neustarten dieses beendeten virtuellen Computers (Aufhebung der Zuordnung) ist daher gleichbedeutend mit einer neuen Zuordnungsanforderung. Das Neustarten von virtuellen Computern in einem Clouddienst mit Teilaufhebung der Zuordnung entspricht dem Hinzufügen von virtuellen Computern zu einem vorhandenen Clouddienst. Die Zuordnungsanforderung muss für den Originalcluster versucht werden, auf dem der vorhandene Clouddienst gehostet wird. Die Erstellung eines anderen Clouddiensts ermöglicht der Azure-Plattform das Suchen nach einem anderen Cluster, der über eine freie Ressource verfügt oder der die angeforderte Größe des virtuellen Computers unterstützt.

**Problemumgehung**

Wenn es akzeptabel ist, eine andere VIP zu verwenden, löschen Sie die beendeten virtuellen Computer (Aufhebung der Zuordnung) – aber behalten Sie die zugeordneten Datenträger bei –, und fügen Sie die virtuellen Computer über einen anderen Clouddienst wieder hinzu. Verwenden Sie ein regionales Virtual Network, um die Verbindung für Ihre Clouddienste herzustellen: 1. Wenn Ihr vorhandener Clouddienst das regionale Virtual Network nutzt, fügen Sie den neuen Clouddienst einfach demselben Virtual Network hinzu. 2. Falls Ihr vorhandener Clouddienst kein regionales virtuelles Netzwerk verwendet, erstellen Sie ein neues virtuelles Netzwerk für den neuen Clouddienst, und [stellen Sie für das vorhandene VNET dann eine Verbindung mit dem neuen VNET her](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Weitere Informationen zum [regionalen virtuellen Netzwerk](http://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/) (in englischer Sprache).

### Zuordnungsszenario: Neustarten beendeter virtueller Computer (Aufhebung der Zuordnung) - Vollständige Aufhebung der Zuordnung
**Fehler**

GeneralError*

**Ursache der Verknüpfung mit dem Cluster**

Die **vollständige** Aufhebung der Zuordnung bedeutet, dass Sie **alle** virtuellen Computer für einen Clouddienst beendet haben (Aufhebung der Zuordnung). Ab jetzt muss versucht werden, die Zuordnungsanforderungen zum Neustarten dieser virtuellen Computer auf dem Originalcluster durchzuführen, auf dem der Clouddienst gehostet wird. Die Erstellung eines neuen Clouddiensts ermöglicht der Azure-Plattform das Suchen nach einem anderen Cluster, der über eine freie Ressource verfügt oder der die angeforderte Größe des virtuellen Computers unterstützt.

**Problemumgehung**

Wenn es akzeptabel ist, eine andere VIP zu verwenden, löschen Sie die ursprünglich beendeten virtuellen Computer (Aufhebung der Zuordnung) – aber behalten Sie die zugeordneten Datenträger bei –, und löschen Sie den entsprechenden Clouddienst (die zugeordneten Compute-Ressourcen wurden bereits freigegeben, als Sie die virtuellen Computer beendet bzw. die Zuordnung dafür aufgehoben haben). Erstellen Sie einen neuen Clouddienst, um die virtuellen Computer wieder hinzuzufügen.

### Zuordnungsszenario: Staging-/Produktionsbereitstellungen (nur Platform-as-a-Service)
**Fehler**

New\_General* oder New\_VMSizeNotSupported*

**Ursache der Verknüpfung mit dem Cluster**

Die Stagingbereitstellung und Produktionsbereitstellung eines Clouddiensts werden in demselben Cluster gehostet. Wenn Sie die zweite Bereitstellung hinzufügen, wird versucht, die entsprechende Zuordnungsanforderung im demselben Cluster durchzuführen, von dem die erste Bereitstellung gehostet wird.

**Problemumgehung**

Löschen Sie die erste Bereitstellung und den ursprünglichen Clouddienst, falls dies akzeptabel ist, und stellen Sie den Clouddienst neu bereit. Bei dieser Aktion kann die erste Bereitstellung in einem Cluster enden, der über genügend freie Ressourcen zur Aufnahme beider Bereitstellungen verfügt, oder in einem Cluster, der die von Ihnen angeforderten Größen des virtuellen Computers unterstützt.

### Zuordnungsszenario: Affinitätsgruppe - Nähe von virtuellem Computer und Dienst
**Fehler**

New\_General* oder New\_VMSizeNotSupported*

**Ursache der Verknüpfung mit dem Cluster**

Alle Compute-Ressourcen, die einer Affinitätsgruppe zugewiesen sind, sind an einen Cluster gebunden. Es wird versucht, neue Anforderungen von Compute-Ressourcen in dieser Affinitätsgruppe in demselben Cluster durchzuführen, in dem die vorhandenen Ressourcen gehostet werden. Dies gilt unabhängig davon, ob die neuen Ressourcen über einen neuen Clouddienst oder einen vorhandenen Clouddienst erstellt werden.

**Problemumgehung**

Verwenden Sie keine Affinitätsgruppe, wenn dies nicht erforderlich ist, oder versuchen Sie, Ihre Compute-Ressourcen in mehreren Affinitätsgruppen zu gruppieren.

### Zuordnungsszenario: Auf Affinitätsgruppe basierendes virtuelles Netzwerk
**Fehler**

New\_General* oder New\_VMSizeNotSupported*

**Ursache der Verknüpfung mit dem Cluster**

Bevor das regionale Virtual Network eingeführt wurde, mussten Sie einem Virtual Network eine Affinitätsgruppe zuordnen. Daher gelten für Compute-Ressourcen, die in die Affinitätsgruppe eingefügt werden, die gleichen Einschränkungen wie oben im Abschnitt „Zuordnungsszenario: Affinitätsgruppe - Nähe von virtuellem Computer und Dienst“ beschrieben. Die Compute-Ressourcen sind an einen Cluster gebunden.

**Problemumgehung**

Falls Sie die Affinitätsgruppe nicht benötigen, können Sie ein neues regionales virtuelles Netzwerk für die neuen Ressourcen erstellen, die Sie hinzufügen, und dann eine [Verbindung für Ihr vorhandenes VNET mit dem neuen VNET herstellen](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Weitere Informationen zum [regionalen virtuellen Netzwerk](http://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/) (in englischer Sprache).

Alternativ dazu können Sie [Ihr auf der Affinitätsgruppe basierendes virtuelles Netzwerk zu einem regionalen virtuellen Netzwerk migrieren](http://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/), und dann versuchen, die gewünschten Ressourcen wieder hinzuzufügen.

## Problembehandlung für spezifische Zuordnungsfehlerszenarios im Azure-Ressourcen-Manager-Bereitstellungsmodell
Dies sind häufig vorkommende Zuordnungsszenarien, die bewirken, dass eine Zuordnungsanforderung „verknüpft“ wird. Die einzelnen Szenarien werden weiter unten in diesem Artikel genauer erläutert.

- Ändern der Größe eines virtuellen Computers oder Hinzufügen zusätzlicher virtueller Computer oder Rolleninstanzen zu einem vorhandenen Clouddienst
- Neustarten beendeter virtueller Computer (Aufhebung der Zuordnung) – **Teilaufhebung** der Zuordnung
- Neustarten beendeter virtueller Computer (Aufhebung der Zuordnung) – **Vollständige** Aufhebung der Zuordnung

Wenn Sie einen Zuordnungsfehler erhalten, sollten Sie prüfen, ob eines der beschriebenen Szenarien für Ihren Fall zutrifft. Verwenden Sie den von der Azure-Plattform zurückgegebenen Zuordnungsfehler, um das entsprechende Szenario zu identifizieren. Falls Ihre Anforderung mit einem vorhandenen Cluster verknüpft ist, können Sie versuchen, einige Verknüpfungseinschränkungen zu beseitigen. So öffnen Sie Ihre Anforderung für mehr Cluster und erhöhen die Chancen für eine erfolgreiche Zuordnung.

Solange für den Fehler nicht „Die angeforderte Größe des virtuellen Computers wird nicht unterstützt“ angegeben wird, können Sie den Vorgang im Allgemeinen immer zu einem späteren Zeitpunkt wiederholen. Unter Umständen wurden im Cluster in der Zwischenzeit genügend Ressourcen für die Verarbeitung Ihrer Anforderung bereitgestellt. Unten sind Problemumgehungen angegeben, falls das Problem darin besteht, dass die angeforderte Größe des virtuellen Computers nicht unterstützt wird.

### Zuordnungsszenario: Ändern der Größe eines virtuellen Computers oder Hinzufügen weiterer virtueller Computer zu einer vorhandenen Verfügbarkeitsgruppe
**Fehler**

Upgrade\_VMSizeNotSupported* oder GeneralError*

**Ursache der Verknüpfung mit dem Cluster**

Die Anforderung der Größenänderung eines virtuellen Computers oder des Hinzufügens eines virtuellen Computers zu einer vorhandenen Verfügbarkeitsgruppe muss für den Originalcluster versucht werden, der die vorhandene Verfügbarkeitsgruppe hostet. Die Erstellung einer neuen Verfügbarkeitsgruppe ermöglicht der Azure-Plattform das Suchen nach einem anderen Cluster, der über eine freie Ressource verfügt oder der die angeforderte Größe des virtuellen Computers unterstützt.

**Problemumgehung**

Probieren Sie es mit einer anderen Größe des virtuellen Computers, wenn der Fehler „Upgrade\_VMSizeNotSupported*“ lautet. Falls die Verwendung einer anderen Größe des virtuellen Computers keine Option ist, beenden Sie alle virtuellen Computer der Verfügbarkeitsgruppe. So können Sie die Größe des virtuellen Computers ändern, über den der virtuelle Computer einem Cluster zugeordnet wird, der die gewünschte Größe für den virtuellen Computer unterstützt.

Wenn der Fehler „GeneralError*“ lautet, wird der Typ der Ressource (z. B. eine bestimmte Größe des virtuellen Computers) vom Cluster wahrscheinlich unterstützt, aber der Cluster verfügt derzeit nicht über freie Ressourcen. Falls der virtuelle Computer Teil einer anderen Verfügbarkeitsgruppe sein kann, erstellen Sie einen neuen virtuellen Computer in einer anderen Verfügbarkeitsgruppe (in derselben Region). Dieser neue virtuelle Computer kann dann demselben Virtual Network hinzugefügt werden.

### Zuordnungsszenario: Neustarten beendeter virtueller Computer (Aufhebung der Zuordnung) - Teilaufhebung der Zuordnung
**Fehler**

GeneralError*

**Ursache der Verknüpfung mit dem Cluster**

Die **Teilaufhebung** der Zuordnung bedeutet, dass Sie mindestens einen virtuellen Computer beendet haben (Aufhebung der Zuordnung), aber **nicht alle** virtuellen Computer einer Verfügbarkeitsgruppe. Wenn Sie einen virtuellen Computer beenden (Aufhebung der Zuordnung), werden die zugeordneten Ressourcen freigegeben. Das Neustarten dieses beendeten virtuellen Computers (Aufhebung der Zuordnung) ist daher gleichbedeutend mit einer neuen Zuordnungsanforderung. Das Neustarten von virtuellen Computern in einer Verfügbarkeitsgruppe mit Teilaufhebung der Zuordnung entspricht dem Hinzufügen von virtuellen Computern zu einer vorhandenen Verfügbarkeitsgruppe. Die Zuordnungsanforderung muss für den Originalcluster versucht werden, auf dem die vorhandene Verfügbarkeitsgruppe gehostet wird.

**Problemumgehung**

Versuchen Sie, alle virtuellen Computer der Verfügbarkeitsgruppe zu beenden, bevor Sie den ersten neu starten. Dadurch wird sichergestellt, dass ein neuer Zuordnungsversuch ausgeführt wird und ein neuer Cluster ausgewählt werden kann, der verfügbare Kapazität aufweist.

### Zuordnungsszenario: Neustarten beendeter virtueller Computer (Aufhebung der Zuordnung) - Vollständige Aufhebung der Zuordnung
**Fehler**

GeneralError*

**Ursache der Verknüpfung mit dem Cluster**

Die **vollständige** Aufhebung der Zuordnung bedeutet, dass Sie **alle** virtuellen Computer einer Verfügbarkeitsgruppe beendet haben (Aufhebung der Zuordnung). Die Zuordnungsanforderung zum Neustarten dieser virtuellen Computer gilt für alle Cluster, die die gewünschte Größe unterstützen.

**Problemumgehung**

Versuchen Sie, eine neue Größe des virtuellen Computers für die Zuordnung auszuwählen. Versuchen Sie es später erneut, falls dies nicht funktioniert.

## Anhang
### Fehlerzeichenfolgen
**New\_VMSizeNotSupported***

Die für diese Bereitstellung erforderliche VM-Größe (oder Kombination aus VM-Größen) kann aufgrund von Einschränkungen bei der Bereitstellungsanforderung nicht bereitgestellt werden. Lockern Sie möglichst die Einschränkungen, wie etwa die Bindungen des virtuellen Netzwerks, die Bereitstellung in einem gehosteten Dienst ohne weitere Bereitstellung darin und in einer anderen Affinitätsgruppe oder ohne Affinitätsgruppe, oder testen Sie die Bereitstellung in einer anderen Region.

**New\_General***

Fehler bei der Zuordnung. Einschränkungen in der Anforderung konnten nicht erfüllt werden. Die angeforderte neue Dienstbereitstellung ist an eine Affinitätsgruppe gebunden, auf ein virtuelles Netzwerk ausgerichtet, oder unter diesem gehosteten Dienst befindet sich eine vorhandene Bereitstellung. Alle diese Bedingungen beschränken die neue Bereitstellung auf bestimmte Azure-Ressourcen. Wiederholen Sie den Vorgang später. Reduzieren Sie die VM-Größe oder die Anzahl der Rolleninstanzen. Sie können, wenn möglich, auch die zuvor erwähnten Einschränkungen entfernen oder die VM in einer anderen Region bereitstellen.

**Upgrade\_VMSizeNotSupported***

Die Bereitstellung konnte nicht aktualisiert werden. Die angeforderte VM-Größe von XXX ist möglicherweise in den Ressourcen, die die vorhandene Bereitstellung unterstützen, nicht verfügbar. Versuchen Sie es später erneut, verwenden Sie eine andere VM-Größe oder weniger Rolleninstanzen, oder erstellen Sie eine Bereitstellung unter einem leeren gehosteten Dienst mit einer neuen Affinitätsgruppe oder ohne Affinitätsgruppenbindung.

**GeneralError***

Auf dem Server ist ein interner Fehler aufgetreten. Versuchen Sie die Anforderung erneut. -oder- Für den Dienst konnte keine Zuordnung erstellt werden.

## Zusätzliche Ressourcen
### Kontaktieren des Azure-Kundensupports

Suchen Sie in den Azure-Foren bei [MSDN und Stack Overflow](http://azure.microsoft.com/support/forums/) weitere Informationen, falls Sie ihr Azure-Problem mit diesem Artikel nicht beheben konnten. Sie können auch einen Azure-Supportfall zu Ihrem Problem erstellen. Rufen Sie die [Azure-Support-Website](http://azure.microsoft.com/support/options/) auf, und klicken Sie auf „Support erhalten“. Informationen zur Nutzung von Azure-Support finden Sie unter [Häufig gestellte Fragen zum Azure-Support](http://azure.microsoft.com/support/faq/).

<!---HONumber=AcomDC_1203_2015-->