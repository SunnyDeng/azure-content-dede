Wenn Sie einen virtuellen Computer erstellen, beendete virtuelle Computer (zuordnungsaufgehobene) neu starten oder die Größe eines virtuellen Computers ändern, weist Microsoft Azure Ihrem Abonnement Compute-Ressourcen zu. Unter Umständen erhalten Sie beim Ausführen dieser Schritte auch dann gelegentlich Fehler, wenn Sie die Grenzwerte des Azure-Abonnements noch nicht erreicht haben. In diesem Artikel werden die Ursachen einiger häufig auftretender Zuordnungsfehler erläutert und mögliche Abhilfemaßnahmen vorgeschlagen. Diese Informationen können auch hilfreich sein, wenn Sie die Bereitstellung Ihrer Dienste planen.

Im Abschnitt „Allgemeine Schritte zur Problembehandlung“ finden Sie Schritte für häufig auftretende Probleme. Der Abschnitt „Ausführliche Problembehandlungsschritte“ enthält Lösungsschritte für spezifische Fehlermeldungen. Bevor Sie beginnen, lesen Sie die Hintergrundinformationen zur Funktionsweise der Zuordnung und zu den Ursachen von Zuordnungsfehlern.

Suchen Sie in den Azure-Foren bei [MSDN und Stack Overflow](https://azure.microsoft.com/support/forums/), falls Sie ihr Azure-Problem mit diesem Artikel nicht beheben konnten. Sie können Ihr Problem in diesen Foren veröffentlichen oder auf Twitter an den @AzureSupport senden. Darüber hinaus können Sie eine Azure-Supportanfrage stellen, indem Sie auf der [Website des Azure-Supports](https://azure.microsoft.com/support/options/) die Option **Support erhalten** auswählen.
## Hintergrundinformationen
### Funktionsweise der Zuordnung
Für die Server in Azure-Rechenzentren wird eine Partitionierung in Cluster vorgenommen. Normalerweise wird versucht, eine Zuordnungsanforderung in mehreren Clustern durchzuführen. Es ist aber möglich, dass bestimmte Einschränkungen der Zuordnungsanforderung die Azure-Plattform zwingen, die Anforderung nur in einen Cluster zu versuchen. In diesem Artikel wird dies als „verknüpft mit einem Cluster“ bezeichnet. Das unten stehende Diagramm 1 zeigt den Fall einer normalen Zuordnung, die in mehreren Clustern versucht wird. Diagramm 2 zeigt den Fall einer mit dem Cluster 2 verknüpften Zuordnung, da dort der vorhandene Clouddienst „CS\_1“ oder die Verfügbarkeitsgruppe gehostet wird. ![Zuordnungsdiagramm](./media/virtual-machines-common-allocation-failure/Allocation1.png)

### Gründe für Zuordnungsfehler
Wenn eine Zuordnungsanforderung mit einem Cluster verknüpft ist, ist die Wahrscheinlichkeit höher, dass keine freien Ressourcen gefunden werden, da der verfügbare Ressourcenpool kleiner ist. Falls Ihre Zuordnungsanforderung mit einem Cluster verknüpft ist, der von Ihnen angeforderte Ressourcentyp für diesen Cluster aber nicht unterstützt wird, schlägt die Anforderung auch dann fehl, wenn der Cluster über eine freie Ressource verfügt. In Diagramm 3 unten ist der Fall dargestellt, in dem eine verknüpfte Zuordnung fehlschlägt, da der einzige Kandidatencluster keine freien Ressourcen aufweist. In Diagramm 4 ist der Fall dargestellt, in dem eine verknüpfte Zuordnung fehlschlägt, weil der einzige Kandidatencluster die angeforderte Größe des virtuellen Computers nicht unterstützt, obwohl der Cluster über freie Ressourcen verfügt.

![Verknüpfte Zuordnung, Fehler](./media/virtual-machines-common-allocation-failure/Allocation2.png)

## Allgemeine Schritte zur Problembehandlung
### Problembehandlung bei häufigen Zuordnungsfehlern im klassischen Bereitstellungsmodell

Mit diesen Schritten können Sie viele Zuordnungsfehler virtueller Computer beheben:

- Ändern Sie die Größe des virtuellen Computers.<br> Klicken Sie auf **Alle durchsuchen** > **Virtuelle Computer (klassisch)** > [Ihr virtueller Computer] > **Einstellungen** > **Größe**. Ausführliche Schritte finden Sie unter [Ändern der Größe des virtuellen Computers](https://msdn.microsoft.com/library/dn168976.aspx).

- Löschen Sie alle virtuellen Computer aus dem Clouddienst, und erstellen Sie die virtuellen Computer neu.<br> Klicken Sie auf **Alle durchsuchen** > **Virtuelle Computer (klassisch)** > [Ihr virtueller Computer] > **Löschen**. Klicken Sie auf **Neu** > **Compute** > [Virtuelles Computerimage].

### Problembehandlung bei häufigen Zuordnungsfehler im Azure-Ressourcen-Manager-Bereitstellungsmodell

Mit diesen Schritten können Sie viele Zuordnungsfehler virtueller Computer beheben:

- Beenden Sie alle virtuellen Computer einer Verfügbarkeitsgruppe (Aufhebung der Zuordnung), und starten Sie die einzelnen virtuellen Computer dann neu.<br> Zum Beenden: Klicken Sie auf **Ressourcengruppen** > [Ihre Ressourcengruppe] > **Ressourcen** > [Ihre Verfügbarkeitsgruppe] > **Virtual Machines** > [Ihr virtueller Computer] > **Beenden**.

	Wählen Sie nach dem Beenden aller virtuellen Computer den ersten virtuellen Computer aus, und klicken Sie auf **Starten**.

## Ausführliche Problembehandlungsschritte
### Problembehandlung bei spezifischen Zuordnungsfehlerszenarien im klassischen Bereitstellungsmodell
Dies sind häufig vorkommende Zuordnungsszenarien, die bewirken, dass eine Zuordnungsanforderung „verknüpft“ wird. Die einzelnen Szenarien werden weiter unten in diesem Artikel genauer erläutert.

- Ändern der Größe eines virtuellen Computers oder Hinzufügen weiterer virtueller Computer oder Rolleninstanzen in einem vorhandenen Clouddienst
- Neustart teilweise beendeter (zuordnungsaufgehobener) virtueller Computer
- Neustart vollständig beendeter (zuordnungsaufgehobener) virtueller Computer
- Staging/Produktionsbereitstellungen (nur Platform-as-a-Service)
- Affinitätsgruppe (Nähe von virtuellem Computer und Dienst)
- Auf Affinitätsgruppe basierendes virtuelles Netzwerk

Wenn Sie einen Zuordnungsfehler erhalten, überprüfen Sie, ob eines der beschriebenen Szenarien für Ihren Fall zutrifft. Verwenden Sie den von der Azure-Plattform zurückgegebenen Zuordnungsfehler, um das entsprechende Szenario zu identifizieren. Falls Ihre Anforderung verknüpft ist, versuchen Sie, einige Verknüpfungseinschränkungen zu beseitigen. Dadurch öffnen Sie Ihre Anforderung für mehr Cluster und erhöhen damit die Chancen für eine erfolgreiche Zuordnung.

Solange für den Fehler nicht „Die angeforderte Größe des virtuellen Computers wird nicht unterstützt“ angegeben wird, können Sie den Vorgang immer zu einem späteren Zeitpunkt wiederholen. Eventuell wurden bis dahin im Cluster genügend Ressourcen für die Verarbeitung Ihrer Anforderung freigegeben. Falls die angeforderte Größe des virtuellen Computers nicht unterstützt wird, versuchen Sie es mit einer anderen Größe. Andernfalls können Sie nur noch die Verknüpfungseinschränkung entfernen.

Zwei häufig auftretende Fehlerszenarien hängen mit der Affinitätsgruppe zusammen. In der Vergangenheit wurde die Affinitätsgruppe verwendet, um Nähe zu virtuellen Computern oder Dienstinstanzen zu schaffen oder um die Erstellung eines Virtual Network zu ermöglichen. Seit der Einführung von regionalen virtuellen Netzwerks werden Affinitätsgruppen zum Erstellen eines virtuellen Netzwerks nicht mehr benötigt. Mit der Reduzierung der Netzwerklatenz in der Azure-Infrastruktur hat sich die Empfehlung, Affinitätsgruppen zur Herstellung von Nähe zwischen virtuellen Computern und Diensten zu verwenden, geändert.

In Diagramm 5 unten ist die Taxonomie der Zuordnungsszenarios (mit Verknüpfung) dargestellt. ![Verknüpfte Zuordnung, Taxonomie](./media/virtual-machines-common-allocation-failure/Allocation3.png)

> [AZURE.NOTE] Der Fehler wird in den einzelnen Zuordnungsszenarien in Kurzform aufgelistet. Details zu den Fehlerzeichenfolgen finden Sie unter [Error string lookup](#Error string lookup) (Fehlerzeichenfolgen zum Nachschlagen).

#### Zuordnungsszenario: Ändern der Größe eines virtuellen Computers oder Hinzufügen weiterer virtueller Computer oder Rolleninstanzen zu einem vorhandenen Clouddienst
**Fehler**

Upgrade\_VMSizeNotSupported oder GeneralError

**Ursache der Verknüpfung mit dem Cluster**

Die Anforderung, die Größe eines virtuellen Computers zu ändern oder einen virtuellen Computer oder eine Rolleninstanz zu einem vorhandenen Clouddienst hinzuzufügen, muss für den Originalcluster, der den vorhandenen Clouddienst hostet, versucht werden. Die Erstellung eines neuen Clouddiensts ermöglicht der Azure-Plattform, einen anderen Cluster zu finden, der über freie Ressourcen verfügt oder die von Ihnen angeforderte Größe des virtuellen Computers unterstützt.

**Problemumgehung**

Wenn der Fehler „Upgrade\_VMSizeNotSupported*“ lautet, versuchen Sie es mit einer anderen Größe des virtuellen Computers. Wenn das Verwenden einer anderen Größe des virtuellen Computers keine Option ist, dafür aber die Verwendung einer anderen virtuellen IP-Adresse (VIP), erstellen Sie einen neuen Clouddienst zum Hosten des neuen virtuellen Computers. Fügen Sie den neuen Clouddienst dem regionalen virtuellen Netzwerken hinzu, auf dem die vorhandenen virtuellen Computer ausgeführt werden. Falls Ihr vorhandener Clouddienst kein regionales virtuelles Netzwerk verwendet, können Sie trotzdem ein neues virtuelles Netzwerk für den neuen Clouddienst erstellen und dann [das vorhandene virtuelle Netzwerk mit dem neuen virtuellen Netzwerk verbinden](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Weitere Informationen zu regionalen virtuellen Netzwerken finden Sie [hier](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Wenn der Fehler „GeneralError*“ lautet, wird der Typ der Ressource (z. B. eine bestimmte Größe des virtuellen Computers) vom Cluster wahrscheinlich unterstützt, allerdings verfügt der Cluster derzeit nicht über freie Ressourcen. Fügen Sie, ähnlich wie oben, die gewünschten Compute-Ressourcen hinzu, indem Sie einen neuen Clouddiensts erstellen (beachten Sie, dass der neue Clouddienst eine andere VIP verwenden muss) und verwenden Sie das regionale Virtual Network zum Verbinden Ihrer Clouddienste.

#### Zuordnungsszenario: Neustarten teilweise beendeter (zuordnungsaufgehobener) virtueller Computer

**Fehler**

GeneralError*

**Ursache der Verknüpfung mit dem Cluster**

Die Teilaufhebung der Zuordnung bedeutet, dass Sie mindestens einen, aber nicht alle virtuellen Computer innerhalb eines Clouddiensts beendet haben (Aufhebung der Zuordnung). Wenn Sie einen virtuellen Computer beenden (Aufhebung der Zuordnung), werden die zugeordneten Ressourcen freigegeben. Das Neustarten dieses beendeten (zuordnungsaufgehobenen) virtuellen Computers ist daher gleichbedeutend einer neue Zuordnungsanforderung. Das Neustarten virtueller Computer in einem Clouddienst, in dem die Zuordnung teilweise aufgehoben wurde, entspricht dem Hinzufügen von virtuellen Computern in vorhandene Clouddienste. Die Anforderung muss für den Originalcluster versucht werden, der den vorhandenen Clouddienst hostet. Die Erstellung eines neuen Clouddiensts ermöglicht der Azure-Plattform einen anderen Cluster zu finden, der über freie Ressourcen verfügt oder die von Ihnen angeforderte Größe des virtuellen Computers unterstützt.

**Problemumgehung**

Falls die Verwendung einer anderen VIP akzeptabel ist, löschen Sie die beendeten (zuordnungsaufgehobenen) virtuellen Computer (aber behalten Sie die zugeordneten Datenträger) und fügen Sie die virtuellen Computer über einen anderen Clouddienst wieder hinzu. Verwenden Sie ein regionales virtuelles Netzwerk, um die Verbindung für Ihre Clouddienste herzustellen:
- Wenn Ihr vorhandener Clouddienst das regionale virtuelle Netzwerk nutzt, fügen Sie den neuen Clouddienst einfach dem gleichen virtuellen Netzwerk hinzu.
- Falls Ihr vorhandener Clouddienst kein regionales virtuelles Netzwerk verwendet, können Sie ein neues virtuelles Netzwerk für den neuen Clouddienst erstellen und dann [das vorhandene virtuelle Netzwerk mit dem neuen virtuellen Netzwerk verbinden](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Weitere Informationen zu regionalen virtuellen Netzwerken finden Sie [hier](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

#### Zuordnungsszenario: Neustarten vollständig beendeter (zuordnungsaufgehobener) virtueller Computer
**Fehler**

GeneralError*

**Ursache der Verknüpfung mit dem Cluster**

Die vollständige Aufhebung der Zuordnung bedeutet, dass Sie alle virtuellen Computer für einen Clouddienst beendet (zuordnungsaufgehoben) haben. Die Zuordnungsanforderungen, den virtuellen Computer neu zu starten, müssen für den Originalcluster, der den vorhandenen Clouddienst hostet, versucht werden. Die Erstellung eines neuen Clouddiensts ermöglicht der Azure-Plattform, einen anderen Cluster zu finden, der über freie Ressourcen verfügt oder die von Ihnen angeforderte Größe des virtuellen Computers unterstützt.

**Problemumgehung**

Falls die Verwendung einer anderen VIP akzeptabel ist, löschen Sie die ursprünglichen, beendeten (zuordnungsaufgehobenen) virtuellen Computer (aber behalten Sie die zugeordneten Datenträger) und löschen Sie den entsprechenden Clouddienst (die zugeordneten Compute-Ressourcen wurden bereits freigegeben, als Sie die (zuordnungsaufgehoben) virtuellen Computer beendet haben). Erstellen Sie einen neuen Clouddienst, um die virtuellen Computer wieder hinzuzufügen.

#### Zuordnungsszenario: Staging-/Produktionsbereitstellungen (nur Platform-as-a-Service)
**Fehler**

New\_General* oder New\_VMSizeNotSupported*

**Ursache der Verknüpfung mit dem Cluster**

Die Stagingbereitstellung und Produktionsbereitstellung eines Clouddiensts werden im selben Cluster gehostet. Wenn Sie die zweite Bereitstellung hinzufügen, wird versucht, die entsprechende Zuordnungsanforderung im demselben Cluster durchzuführen, von dem die erste Bereitstellung gehostet wird.

**Problemumgehung**

Löschen Sie die erste Bereitstellung und den ursprünglichen Clouddienst und stellen Sie den Clouddienst erneut bereit. Bei dieser Aktion kann die erste Bereitstellung in einem Cluster enden, der über genügend freie Ressourcen zur Aufnahme beider Bereitstellungen verfügt, oder in einem Cluster, der die von Ihnen angeforderten Größen des virtuellen Computers unterstützt.

#### Zuordnungsszenario: Affinitätsgruppe (Nähe von virtuellem Computer und Dienst)
**Fehler**

New\_General* oder New\_VMSizeNotSupported*

**Ursache der Verknüpfung mit dem Cluster**

Jede Compute-Ressource, die einer Affinitätsgruppe zugewiesen ist, ist an ein Cluster gebunden. Es wird versucht, neue Anforderungen von Compute-Ressourcen in dieser Affinitätsgruppe im selben Cluster durchzuführen, in dem die vorhandenen Ressourcen gehostet werden. Dies gilt unabhängig davon, ob die neuen Ressourcen über einen neuen Clouddienst oder einen vorhandenen Clouddienst erstellt werden.

**Problemumgehung**

Falls dies nicht erforderlich ist, verwenden Sie keine Affinitätsgruppe oder gruppieren Sie Ihre Compute-Ressourcen in mehrere Affinitätsgruppen.

#### Zuordnungsszenario: auf Affinitätsgruppen basierendes virtuelles Netzwerk
**Fehler**

New\_General* oder New\_VMSizeNotSupported*

**Ursache der Verknüpfung mit dem Cluster**

Bevor regionale virtuelle Netzwerke eingeführt wurden, mussten Sie einem Virtual Network eine Affinitätsgruppe zuordnen. Daher gelten für Compute-Ressourcen, die in die Affinitätsgruppe eingefügt werden, die gleichen Einschränkungen wie oben im Abschnitt „Zuordnungsszenario: Affinitätsgruppe (Nähe von virtuellem Computer und Dienst)“ beschrieben. Die Compute-Ressourcen sind an ein Cluster gebunden.

**Problemumgehung**

Falls Sie keine Affinitätsgruppe benötigen, erstellen Sie ein neues regionales virtuelles Netzwerk für die neuen Ressourcen, die Sie hinzufügen, und [verbinden Sie Ihr vorhandenes virtuelles Netzwerk mit dem neuen virtuellen Netzwerk](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Weitere Informationen zu regionalen virtuellen Netzwerken finden Sie [hier](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Alternativ dazu können Sie [Ihr auf der Affinitätsgruppe basierendes virtuelles Netzwerk zu einem regionalen virtuellen Netzwerk migrieren](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/) und dann die gewünschten Ressourcen wieder hinzufügen.

### Problembehandlung für spezifische Zuordnungsfehlerszenarios im Azure-Ressourcen-Manager-Bereitstellungsmodell
Dies sind häufig vorkommende Zuordnungsszenarien, die bewirken, dass eine Zuordnungsanforderung „verknüpft“ wird. Die einzelnen Szenarien werden weiter unten in diesem Artikel genauer erläutert.

- Ändern der Größe eines virtuellen Computers oder Hinzufügen weiterer virtueller Computer oder Rolleninstanzen in einem vorhandenen Clouddienst
- Neustart teilweise beendeter (zuordnungsaufgehobener) virtueller Computer
- Neustart vollständig beendeter (zuordnungsaufgehobener) virtueller Computer

Wenn Sie einen Zuordnungsfehler erhalten, überprüfen Sie, ob eines der beschriebenen Szenarien für Ihren Fall zutrifft. Verwenden Sie den von der Azure-Plattform zurückgegebenen Zuordnungsfehler, um das entsprechende Szenario zu identifizieren. Falls Ihre Anforderung mit einem vorhandenen Cluster verknüpft ist, beseitigen Sie einige der Verknüpfungseinschränkungen. Dadurch öffnen Sie Ihre Anforderung für mehr Cluster und erhöhen die Chancen für eine erfolgreiche Zuordnung.

Solange für den Fehler nicht „Die angeforderte Größe des virtuellen Computers wird nicht unterstützt“ angegeben wird, können Sie den Vorgang immer zu einem späteren Zeitpunkt wiederholen. Eventuell wurden bis dahin im Cluster genügend Ressourcen für die Verarbeitung Ihrer Anforderung freigegeben. Für den Fall, dass die angeforderte Größe des virtuellen Computers nicht unterstützt wird, sind unten Problemumgehungen angegeben.

#### Zuordnungsszenario: Ändern der Größe eines virtuellen Computers oder Hinzufügen weiterer virtueller Computer zu einer vorhandenen Verfügbarkeitsgruppe
**Fehler**

Upgrade\_VMSizeNotSupported* oder GeneralError*

**Ursache der Verknüpfung mit dem Cluster**

Die Anforderung, die Größe eines virtuellen Computers zu ändern oder einen virtuellen Computer zu einer vorhandenen Verfügbarkeitsgruppe hinzuzufügen, muss für den Originalcluster, der die vorhandenen Verfügbarkeitsgruppe hostet, versucht werden. Die Erstellung einer neuen Verfügbarkeitsgruppe ermöglicht der Azure-Plattform, einen anderen Cluster zu finden, der über freie Ressourcen verfügt oder die von Ihnen angeforderte Größe des virtuellen Computers unterstützt.

**Problemumgehung**

Wenn der Fehler „Upgrade\_VMSizeNotSupported*“ lautet, versuchen Sie es mit einer anderen Größe des virtuellen Computers. Falls die Verwendung einer anderen Größe des virtuellen Computers keine Option ist, beenden Sie alle virtuellen Computer der Verfügbarkeitsgruppe. Sie können dann die Größe desjenigen virtuellen Computers ändern, der den virtuellen Computer einem Cluster zuordnet, der die gewünschte Größe für den virtuellen Computer unterstützt.

Wenn der Fehler „GeneralError*“ lautet, wird der Typ der Ressource (z. B. eine bestimmte Größe des virtuellen Computers) vom Cluster wahrscheinlich unterstützt, allerdings verfügt der Cluster derzeit nicht über freie Ressourcen. Falls der virtuelle Computer Teil einer anderen Verfügbarkeitsgruppe sein kann, erstellen Sie einen neuen virtuellen Computer in einer anderen Verfügbarkeitsgruppe (in derselben Region). Dieser neue virtuelle Computer kann dann demselben virtuellen Netzwerk hinzugefügt werden.

#### Zuordnungsszenario: Neustarten teilweise beendeter (zuordnungsaufgehobener) virtueller Computer
**Fehler**

GeneralError*

**Ursache der Verknüpfung mit dem Cluster**

Die Teilaufhebung der Zuordnung bedeutet, dass Sie mindestens einen, aber nicht alle virtuellen Computer innerhalb einer Verfügbarkeitsgruppe beendet (zuordnungsaufgehoben) haben. Wenn Sie einen virtuellen Computer beenden (Aufhebung der Zuordnung), werden die zugeordneten Ressourcen freigegeben. Das Neustarten dieses beendeten (zuordnungsaufgehobenen) virtuellen Computers ist daher gleichbedeutend einer neue Zuordnungsanforderung. Das Neustarten virtueller Computer in einer Verfügbarkeitsgruppe, in der die Zuordnung teilweise aufgehoben wurde, entspricht dem Hinzufügen von virtuellen Computern in vorhandene Verfügbarkeitsgruppen. Die Zuordnungsanforderung muss für den Originalcluster, der die vorhandenen Verfügbarkeitsgruppe hostet, versucht werden.

**Problemumgehung**

Beenden Sie alle virtuellen Computer der Verfügbarkeitsgruppe bevor Sie den ersten neu starten. Dadurch wird sichergestellt, dass ein neuer Zuordnungsversuch ausgeführt wird und ein neuer Cluster ausgewählt werden kann, der verfügbare Kapazität aufweist.

#### Zuordnungsszenario: Neustarten vollständig beendeter (zuordnungsaufgehobener) virtueller Computer
**Fehler**

GeneralError*

**Ursache der Verknüpfung mit dem Cluster**

Die vollständige Aufhebung der Zuordnung bedeutet, dass Sie alle virtuellen Computer für eine Verfügbarkeitsgruppe beendet (zuordnungsaufgehoben) haben. Die Zuordnungsanforderung zum Neustarten dieser virtuellen Computer gilt für alle Cluster, welche die gewünschte Größe unterstützen.

**Problemumgehung**

Wählen Sie eine neue Größe des virtuellen Computers für die Zuordnung aus. Wenn das nicht funktioniert, versuchen Sie es bitte später erneut.

## Fehlerzeichenfolgen
**New\_VMSizeNotSupported***

„Die für diese Bereitstellung erforderliche VM-Größe (oder Kombination aus VM-Größen) kann aufgrund von Einschränkungen bei der Bereitstellungsanforderung nicht bereitgestellt werden. Lockern Sie, wenn möglich, die Einschränkungen, wie etwa die Bindungen des virtuellen Netzwerks, die Bereitstellung in einem gehosteten Dienst ohne weitere Bereitstellung darin und in einer anderen Affinitätsgruppe oder ohne Affinitätsgruppe, oder testen Sie die Bereitstellung in einer anderen Region.“

**New\_General***

„Fehler bei der Zuordnung. Einschränkungen in der Anforderung konnten nicht erfüllt werden. Die angeforderte neue Dienstbereitstellung ist an eine Affinitätsgruppe gebunden, auf ein virtuelles Netzwerk ausgerichtet, oder unter diesem gehosteten Dienst befindet sich eine vorhandene Bereitstellung. Alle diese Bedingungen beschränken die neue Bereitstellung auf bestimmte Azure-Ressourcen. Wiederholen Sie den Vorgang später. Reduzieren Sie die VM-Größe oder die Anzahl der Rolleninstanzen. Sie können, wenn möglich, auch die zuvor erwähnten Einschränkungen entfernen oder den virtuellen Computer in einer anderen Region bereitstellen.“

**Upgrade\_VMSizeNotSupported***

„Die Bereitstellung konnte nicht upgegradet werden. Die angeforderte VM-Größe von XXX ist möglicherweise in den Ressourcen, die die vorhandene Bereitstellung unterstützen, nicht verfügbar. Versuchen Sie es später erneut, verwenden Sie eine andere VM-Größe oder weniger Rolleninstanzen, oder erstellen Sie eine Bereitstellung unter einem leeren gehosteten Dienst mit einer neuen Affinitätsgruppe oder ohne Affinitätsgruppenbindung.“

**GeneralError***

„Auf dem Server ist ein interner Fehler aufgetreten. Versuchen Sie die Anforderung erneut.“ Oder „Für den Dienst konnte keine Zuordnung erstellt werden.“

<!---HONumber=AcomDC_0323_2016-->