<properties
   pageTitle="Upgrade eines Service Fabric-Clusters | Microsoft Azure"
   description="Informationen zum Upgrade des Fabric-Codes und/oder der Konfiguration, die in einem Service Fabric-Cluster ausgeführt wird, einschließlich Upgrade von Zertifikaten, Hinzufügen von Anwendungsports, Betriebssystempatches usw. Was können Sie erwarten, wenn die Upgrades durchgeführt werden?"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/03/2015"
   ms.author="chackdan"/>

# Upgrade eines Service Fabric-Clusters

Ein Service Fabric-Cluster ist eine Ressource, die Sie besitzen, die jedoch teilweise von Microsoft verwaltet wird. In diesem Artikel wird beschrieben, was automatisch verwaltet wird und was Sie selbst konfigurieren können.

## Automatisch verwaltete Clusterkonfiguration

Microsoft wartet den Fabric-Code und die Konfiguration, die in einem Cluster ausgeführt wird. Bei Bedarf führen wir automatisch überwachte Upgrades der Software durch. Diese Upgrades können sich auf Code, die Konfiguration oder beides beziehen. Um sicherzustellen, dass Ihre Anwendung nicht oder nur minimal während dieser Upgrades beeinträchtigt wird, führen wir die Upgrades in drei Phasen durch.

### Phase 1: Upgrade erfolgt unter Befolgung aller Integritätsrichtlinien für den Cluster

In dieser Phase erfolgen die Upgrades nacheinander in den einzelnen Upgradedomänen. Die Anwendungen, die im Cluster ausgeführt wurden, werden ohne Ausfallzeit fortgesetzt. Die Clusterintegritätsrichtlinien (eine Kombination aus der Integrität der Knoten und aller im Cluster ausgeführten Anwendungen) werden während des Upgrades befolgt.

Wenn die Clusterintegritätsrichtlinien nicht eingehalten werden, wird das Upgrade rückgängig gemacht. Der Besitzer des Abonnements erhält eine E-Mail mit der Information, dass das Upgrade eines Clusters rückgängig gemacht werden musste. Ferner enthalten sind Angaben zu Abhilfemaßnahmen und dazu, dass Phase 2 in „n“ Tagen erfolgen wird.„n“ ist eine Variable. Wir versuchen noch weitere Male, dasselbe Upgrade auszuführen, um Upgrades auszuschließen, die aus Infrastrukturgründen misslungen sind. Nach „n“ Tagen nach Senden der E-Mail fahren wir mit Phase 2 fort.

Wenn die Clusterintegritätsrichtlinien erfüllt sind, wird das Upgrade als erfolgreich betrachtet und als abgeschlossen markiert. Dies kann in dieser Phase während des anfänglichen oder einer der Wiederholungen des Upgrades erfolgen. Bei einer erfolgreichen Ausführung gibt es keine Bestätigung per E-Mail. (Dadurch soll das Senden zu vieler E-Mails verhindert werden. Der Empfang einer E-Mail soll eine Ausnahme vom Normalfall sein. Wir erwarten, dass die meisten Clusterupgrades ohne Beeinträchtigung der Verfügbarkeit der Anwendung erfolgen).

Weitere Informationen zum Festlegen benutzerdefinierter Integritätsrichtlinien für Ihren Cluster finden Sie unter [Upgrade- und Integritätsparameter für Cluster](service-fabric-cluster-health-parameters.md).

### Phase 2: Das Upgrade erfolgt unter ausschließlicher Befolgung der Standardintegritätsrichtlinien

Die Integritätsrichtlinien werden so festgelegt, dass die Anzahl der Anwendungen, die am Anfang des Upgrades fehlerfrei waren, diesen Status für die Dauer des Upgradeprozesses beibehalten. Wie in Phase 1 erfolgen die Upgrades nacheinander in den einzelnen Upgradedomänen. Die Anwendungen, die im Cluster ausgeführt wurden, werden ohne Ausfallzeit fortgesetzt. Die Clusterintegritätsrichtlinien (eine Kombination aus der Integrität der Knoten und aller im Cluster ausgeführten Anwendungen) werden während des Upgrades befolgt.

Wenn die geltenden Clusterintegritätsrichtlinien nicht eingehalten werden, wird das Upgrade rückgängig gemacht. Der Besitzer des Abonnements erhält eine E-Mail mit der Information, dass das Upgrade eines Clusters rückgängig gemacht werden musste. Ferner enthalten sind Angaben zu Abhilfemaßnahmen und dazu, dass Phase 3 in „n“ Tagen erfolgen wird.„n“ ist eine Variable.

Wir versuchen noch weitere Male, dasselbe Upgrade auszuführen, um Upgrades auszuschließen, die aus Infrastrukturgründen misslungen sind. Mehrere Tage vor Ablauf von „n“ Tagen wird eine Erinnerungs-E-Mail gesendet. Nach „n“ Tagen ab dem Sendedatum der E-Mail fahren wir mit Phase 3 fort. Die E-Mails, die wir Ihnen in Phase 2 senden, müssen ernst genommen werden und Abhilfemaßnahmen erfolgen.

Wenn die Clusterintegritätsrichtlinien erfüllt sind, wird das Upgrade als erfolgreich betrachtet und als abgeschlossen markiert. Dies kann in dieser Phase während des anfänglichen oder einer der Wiederholungen des Upgrades erfolgen. Bei einer erfolgreichen Ausführung gibt es keine Bestätigung per E-Mail.

### Phase 3: Upgrade erfolgt unter Befolgung aggressiver Integritätsrichtlinien

Diese Integritätsrichtlinien zielen auf die Vervollständigung des Upgrades und nicht auf die Integrität der Anwendungen ab. Nur sehr wenige Clusterupgrades gelangen in diese Phase. Wenn Ihr Cluster in diese Phase gelangt, besteht eine hohe Wahrscheinlichkeit, dass Ihre Anwendung instabil wird und/oder Verfügbarkeit einbüßt.

Ähnlich wie in den beiden anderen Phasen erfolgen Upgrades om Phase 3 nacheinander in den einzelnen Upgradedomänen.

Wenn die geltenden Clusterintegritätsrichtlinien nicht erfüllt sind, wird das Upgrade rückgängig gemacht. Wir versuchen noch weitere Male, dasselbe Upgrade auszuführen, um Upgrades auszuschließen, die aus Infrastrukturgründen misslungen sind. Danach wird der Cluster in einen Status versetzt, in dem keine Unterstützung und/oder Upgrades mehr empfangen werden.

Eine E-Mail mit diesen Informationen und den Abhilfemaßnahmen wird an den Besitzer des Abonnements gesendet.Wir erwarten nicht, dass Cluster in einen Status gelangen, der die Folge des Misslingens von Phase 3 ist.

Wenn die Clusterintegritätsrichtlinien erfüllt sind, wird das Upgrade als erfolgreich betrachtet und als abgeschlossen markiert. Dies kann in dieser Phase während des anfänglichen oder einer der Wiederholungen des Upgrades erfolgen. Bei einer erfolgreichen Ausführung gibt es keine Bestätigung per E-Mail.

## Von Ihnen gesteuerte Clusterkonfiguration

Hier sind die Konfigurationseinstellungen, die Sie bei einem aktiven Cluster ändern können.

### Zertifikate

Sie können die primären oder sekundären Zertifikate einfach über das Portal oder über einen PUT-Befehl für die Ressource „servicefabric.cluster“ aktualisieren.

![Zertifikatsaktualisierung][CertificateUpgrade]

### Anwendungsports

Sie können die dem Knotentyp zugeordneten Ressourceneigenschaften des Load Balancers ändern.Dies ist über das Portal oder direkt über den ARM und PowerShell möglich.

Um einen neuen Port auf allen VMs in einem Knotentyp öffnen zu können, führen Sie die folgenden Schritte aus.

1. **Hinzufügen eines neuen Tests zum entsprechenden Load Balancer**

 Wenn Sie den Cluster mithilfe des Portals bereitgestellt haben, heißen die Load Balancer für jeden Knotentyp „loadBalancer-0“ , „loadBalancer-1“ usw. Da die Load Balancer-Namen nur in einer Ressourcengruppe eindeutig sind, empfiehlt es sich, sie nur in einer bestimmten Ressourcengruppe zu suchen.

 ![Hinzufügen von Tests][addingProbes]

2. **Hinzufügen einer neuen Regel zum Load Balancer**

  Fügen Sie demselben Load Balancer eine neue Regel mithilfe des im vorherigen Schritt erstellten Tests hinzu.

  ![Hinzufügen von Load Balancer-Regeln][AddingLBRules]

### Placement-Eigenschaften

  Für die einzelnen Knotentypen können Sie benutzerdefinierte Placement-Eigenschaften hinzufügen, die Sie in Ihrer Anwendung verwenden möchten. „NodeType“ ist eine Standardeigenschaft, die Sie verwenden können, ohne dass sie explizit hinzugefügt werden muss.

  >[AZURE.NOTE]Informationen zur Verwendung der Placement-Eigenschaft finden Sie in der [Dokumentation der Placement-Einschränkungen](service-fabric-placement-constraint.md).

### Kapazitätsmetriken

Für die einzelnen Knotentypen können Sie benutzerdefinierte Kapazitätsmetriken hinzufügen, die Sie in Ihrer Anwendung zum Melden der Auslastung verwenden möchten. Informationen zur Verwendung von Kapazitätsmetriken zum Melden der Auslastung finden Sie in der [Übersicht über das Melden der dynamischen Auslastung](service-fabric-resource-balancer-dynamic-load-reporting.md).

### Anwenden von Betriebssystempatches auf die virtuellen Computer, die den Cluster bilden
Dieses Feature ist angekündigt. Derzeit sind Sie für das Patching der VMs verantwortlich.Dies muss jeweils für eine VM erfolgen, sodass immer nur eine VM außer Betrieb genommen wird.

### Upgrade des Betriebssystems auf den virtuellen Computern, die den Cluster bilden
Wenn Sie das verwendete Betriebssystemimage aktualisieren müssen, muss das jeweils auf einer VM erfolgen, wobei Sie für dieses Upgrade zuständig sind. Bislang gibt es keine Automatisierung.


## Nächste Schritte

- Erfahren Sie, [wie Ihr Cluster skaliert wird](service-fabric-cluster-scale-up-down.md).
- Machen Sie sich mit [Anwendungsupgrades](service-fabric-application-upgrade.md) vertraut.

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes.png
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png

<!---HONumber=Nov15_HO4-->