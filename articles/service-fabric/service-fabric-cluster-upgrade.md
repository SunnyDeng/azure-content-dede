<properties
   pageTitle="Upgrade eines Service Fabric-Clusters | Microsoft Azure"
   description="Upgraden Sie den Service Fabric-Code und/oder die Konfiguration, die in einem Service Fabric-Cluster ausgeführt wird, einschließlich Upgrade von Zertifikaten, Hinzufügen von Anwendungsports, Betriebssystempatches usw. Was können Sie erwarten, wenn die Upgrades durchgeführt werden?"
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
   ms.date="02/16/2016"
   ms.author="chackdan"/>


# Upgrade von Service Fabric-Clustern

Ein Azure Service Fabric-Cluster ist eine Ressource, die Sie besitzen, die jedoch teilweise von Microsoft verwaltet wird. In diesem Artikel wird beschrieben, was automatisch verwaltet wird und was Sie selbst konfigurieren können.

## Automatisch verwaltete Clusterkonfiguration

Microsoft verwaltet den Fabric-Code und die Konfiguration, die in einem Cluster ausgeführt werden. Wir führen bei Bedarf automatische, überwachte Upgrades für die durch. Diese Upgrades können sich auf den Code, die Konfiguration oder beides beziehen. Um sicherzustellen, dass Ihre Anwendung während dieser Upgrades nicht oder nur minimal beeinträchtigt wird, führen wir die Upgrades in den folgenden drei Phasen durch.

### Phase 1: Ein Upgrade erfolgt unter Befolgung aller Integritätsrichtlinien für den Cluster

In dieser Phase erfolgen die Upgrades nacheinander in den einzelnen Upgradedomänen. Die Anwendungen, die im Cluster ausgeführt wurden, werden ohne Ausfallzeit fortgesetzt. Die Clusterintegritätsrichtlinien (eine Kombination aus der Integrität der Knoten und aller im Cluster ausgeführten Anwendungen) werden während des Upgrades befolgt.

Wenn die Integritätsrichtlinien des Clusters nicht erfüllt sind, wird das Upgrade zurückgesetzt. Anschließend wird eine E-Mail an den Besitzer des Abonnements gesendet. Die E-Mail enthält folgende Informationen:

- Benachrichtigung, dass wir ein Clusterupgrade zurücksetzen mussten.
- Vorgeschlagene Abhilfemaßnahmen, falls vorhanden.
- Die Anzahl der Tage (n), bis wir Phase 2 ausführen werden.

Wir versuchen, dasselbe Upgrade ein paar weitere Male auszuführen, falls ein Upgrade aus Gründen der Infrastruktur fehlgeschlagen ist. Nach n Tagen, ab dem Sendedatum der E-Mail, fahren wir mit Phase 2 fort.

Wenn die Clusterintegritätsrichtlinien erfüllt sind, wird das Upgrade als erfolgreich betrachtet und als abgeschlossen markiert. Dies kann in dieser Phase während des anfänglichen Upgrades oder während einer der Wiederholungen des Upgrades erfolgen. Bei einer erfolgreichen Ausführung gibt es keine Bestätigung per E-Mail. Dadurch soll das Senden zu vieler E-Mails verhindert werden. Der Empfang einer E-Mail soll eine Ausnahme vom Normalfall sein. Wir erwarten, dass die meisten Clusterupgrades ohne Beeinträchtigung der Verfügbarkeit der Anwendung funktionieren.

### Phase 2: Ein Upgrade erfolgt unter ausschließlicher Befolgung der Standardintegritätsrichtlinien

Die Integritätsrichtlinien werden in dieser Phase so festgelegt, dass die Anzahl der Anwendungen, die am Anfang des Upgrades fehlerfrei waren, diesen Status für die Dauer des Upgradeprozesses beibehalten. Wie in Phase 1 erfolgen in Phase 2 die Upgrades nacheinander in den einzelnen Upgradedomänen. Die Anwendungen, die im Cluster ausgeführt wurden, werden ohne Ausfallzeit fortgesetzt. Die Clusterintegritätsrichtlinien (eine Kombination aus der Integrität der Knoten und aller im Cluster ausgeführten Anwendungen) werden während des Upgrades befolgt.

Wenn die Integritätsrichtlinien des Clusters tatsächlich nicht erfüllt sind, wird das Upgrade zurückgesetzt. Anschließend wird eine E-Mail an den Besitzer des Abonnements gesendet. Die E-Mail enthält folgende Informationen:

- Benachrichtigung, dass wir ein Clusterupgrade zurücksetzen mussten.
- Vorgeschlagene Abhilfemaßnahmen, falls vorhanden.
- Die Anzahl der Tage (n), bis wir Phase 3 ausführen werden.

Wir versuchen, dasselbe Upgrade ein paar weitere Male auszuführen, falls ein Upgrade aus Gründen der Infrastruktur fehlgeschlagen ist. Mehrere Tage vor Ablauf von „n“ Tagen wird eine Erinnerungs-E-Mail gesendet. Nach „n“ Tagen ab dem Sendedatum der E-Mail fahren wir mit Phase 3 fort. Die E-Mails, die wir Ihnen in Phase 2 senden, müssen ernst genommen werden und Abhilfemaßnahmen müssen erfolgen.

Wenn die Clusterintegritätsrichtlinien erfüllt sind, wird das Upgrade als erfolgreich betrachtet und als abgeschlossen markiert. Dies kann in dieser Phase während des anfänglichen Upgrades oder während einer der Wiederholungen des Upgrades erfolgen. Bei einer erfolgreichen Ausführung gibt es keine Bestätigung per E-Mail.

### Phase 3: Ein Upgrade erfolgt unter Befolgung aggressiver Integritätsrichtlinien

Diese Integritätsrichtlinien in dieser Phase zielen auf die Vervollständigung des Upgrades und nicht auf die Integrität der Anwendungen ab. Nur sehr wenige Clusterupgrades gelangen in diese Phase. Wenn Ihr Cluster in diese Phase gelangt, besteht eine hohe Wahrscheinlichkeit, dass Ihre Anwendung instabil wird und/oder Verfügbarkeit einbüßt.

Ähnlich wie in den beiden anderen Phasen erfolgen Upgrades om Phase 3 nacheinander in den einzelnen Upgradedomänen.

Wenn die Integritätsrichtlinien des Clusters nicht erfüllt sind, wird das Upgrade zurückgesetzt. Wir versuchen, dasselbe Upgrade ein paar weitere Male auszuführen, falls ein Upgrade aus Gründen der Infrastruktur fehlgeschlagen ist. Danach wird der Cluster fixiert, sodass er keine weitere Unterstützung und/oder Upgrades empfängt.

Eine E-Mail mit diesen Informationen und den Abhilfemaßnahmen wird an den Besitzer des Abonnements gesendet. Wir erwarten nicht, dass Cluster in einen Status gelangen, der die Folge des Misslingens von Phase 3 ist.

Wenn die Clusterintegritätsrichtlinien erfüllt sind, wird das Upgrade als erfolgreich betrachtet und als abgeschlossen markiert. Dies kann in dieser Phase während des anfänglichen Upgrades oder während einer der Wiederholungen des Upgrades erfolgen. Bei einer erfolgreichen Ausführung gibt es keine Bestätigung per E-Mail.

## Von Ihnen gesteuerte Clusterkonfigurationen

Hier sind die Konfigurationseinstellungen, die Sie bei einem aktiven Cluster ändern können.

### Zertifikate

Sie können die primären oder sekundären Zertifikate einfach über das Azure-Portal (siehe unten) oder über einen PUT-Befehl für die Ressource „servicefabric.cluster“ aktualisieren.

![Screenshot mit dem Zertifikatfingerabdruck im Azure-Portal.][CertificateUpgrade]

>[AZURE.NOTE] Bevor Sie ein Zertifikat bestimmen, das Sie für die Clusterressourcen verwenden möchten, müssen Sie die folgenden Schritte ausführen. Andernfalls wird das neue Zertifikat nicht verwendet: 1. Laden Sie das neue Zertifikat in den Azure-Schlüsseltresor hoch. Eine Anleitung finden Sie unter [Service Fabric security (Service Fabric-Sicherheit)](service-fabric-cluster-security.md). Beginnen Sie mit Schritt 2 in diesem Dokument. 2. Aktualisieren Sie alle virtuellen Computer, die Ihren Cluster bilden, sodass das Zertifikat auf ihnen bereitgestellt wird. Weitere Informationen hierzu finden Sie im [Azure Key Vault-Teamblog](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx).

### Anwendungsports

Sie können Anwendungsports ändern, indem Sie die dem Knotentyp zugeordneten Ressourceneigenschaften des Load Balancers ändern. Sie können das Portal oder direkt den PowerShell-Ressourcen-Manager verwenden.

Um einen neuen Port auf allen VMs in einem Knotentyp öffnen zu können, führen Sie die folgenden Schritte aus.

1. Fügen Sie dem entsprechenden Load Balancer einen neuen Test hinzu.

    Wenn Sie den Cluster mithilfe des Portals bereitgestellt haben, heißen die Load Balancer für jeden Knotentyp „loadBalancer-0“ , „loadBalancer-1“ usw. Da die Load Balancer-Namen nur in einer Ressourcengruppe eindeutig sind, empfiehlt es sich, sie nur in einer bestimmten Ressourcengruppe zu suchen.

    ![Screenshot, der zeigt, wie einem Load Balancer im Portal ein Test hinzugefügt wird.][AddingProbes]

2. Fügen Sie dem Load Balancer eine neue Regel hinzu.

    Fügen Sie demselben Load Balancer eine neue Regel mithilfe des im vorherigen Schritt erstellten Tests hinzu.

    ![Screenshot, der zeigt, wie einem Load Balancer im Portal eine neue Regel hinzugefügt wird.][AddingLBRules]


### Placement-Eigenschaften

Für die einzelnen Knotentypen können Sie benutzerdefinierte Placement-Eigenschaften hinzufügen, die Sie in Ihrer Anwendung verwenden möchten. „NodeType“ ist eine Standardeigenschaft, die Sie verwenden können, ohne dass sie explizit hinzugefügt werden muss.

>[AZURE.NOTE] Informationen zur Verwendung der Placement-Eigenschaften finden Sie in der [Übersicht der Platzierungseinschränkungen](service-fabric-placement-constraint.md).

### Kapazitätsmetriken

Für die einzelnen Knotentypen können Sie benutzerdefinierte Kapazitätsmetriken hinzufügen, die Sie in Ihrer Anwendung zum Melden der Auslastung verwenden möchten. Informationen zur Verwendung von Kapazitätsmetriken zum Melden der Auslastung finden Sie in der [Übersicht über das Melden der dynamischen Auslastung](service-fabric-resource-balancer-dynamic-load-reporting.md).

### Betriebssystem-Patches auf den virtuellen Computern, die den Cluster bilden

Diese Funktion ist als automatisierte Funktion für die Zukunft geplant. Derzeit sind Sie jedoch noch selbst für das Patching Ihrer virtuellen Computer verantwortlich. Dies muss jeweils für eine VM erfolgen, sodass immer nur eine außer Betrieb genommen wird.

### Aktualisierung des Betriebssystems auf den virtuellen Computern, die den Cluster bilden

Wenn Sie das verwendete Betriebssystemimage auf den virtuellen Computern des Clusters aktualisieren müssen, muss das jeweils auf den einzelnen virtuellen Computern nacheinander erfolgen. Dabei sind Sie für dieses Upgrade zuständig, dies kann derzeit noch nicht automatisiert werden.

## Nächste Schritte

- Erfahren Sie, [wie Ihr Cluster skaliert wird](service-fabric-cluster-scale-up-down.md).
- Machen Sie sich mit [Anwendungsupgrades](service-fabric-application-upgrade.md) vertraut.

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes.png
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png

<!---HONumber=AcomDC_0224_2016-->