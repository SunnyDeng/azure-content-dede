<properties
   pageTitle="Azure Service Fabric-Anwendungslebenszyklus | Microsoft Azure"
   description="Beschreibt das Entwickeln, Bereitstellen, Testen, Aktualisieren, Verwalten und Entfernen von Service Fabric-Anwendungen."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>


<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/17/2015"
   ms.author="ryanwi; mani-ramaswamy"/>


# Service Fabric-Anwendungslebenszyklus
Ähnlich wie auf anderen Plattformen durchläuft eine Anwendung auf Service Fabric normalerweise die folgenden Phasen: Entwurf, Entwicklung, Test, Bereitstellung, Update, Wartung und Deinstallation. Service Fabric bietet erstklassige Unterstützung für den gesamten Anwendungslebenszyklus von Cloudanwendungen: von der Entwicklung über die Bereitstellung, die tägliche Verwaltung und die Wartung bis zur endgültigen Außerbetriebnahme. Das Dienstmodell ermöglicht die unabhängige Beteiligung verschiedener Rollen am Anwendungslebenszyklus. Dieser Artikel bietet eine Übersicht über die APIs und wie sie von den verschiedenen Rollen während der Phasen des Service Fabric-Anwendungslebenszyklus verwendet werden.

## Rollen des Dienstmodells
Es gibt folgende Rollen im Dienstmodell:

- **Dienstentwickler**: Entwickelt modulare und generische Dienste, die für neue Zwecke in mehreren Anwendungen des gleichen Typs oder in anderen Anwendungstypen wiederverwendet werden können. Ein Warteschlangendienst kann beispielsweise zum Erstellen einer Ticketing-Anwendung (Helpdesk) oder einer E-Commerce-Anwendung (Warenkorb) verwendet werden.

- **Anwendungsentwickler**: Erstellt Anwendungen durch Integrieren einer Sammlung von Diensten, um bestimmte Anforderungen zu erfüllen oder bestimmte Szenarios abzudecken. Beispielsweise können "Zustandsloser Front-End-Dienst für JSON", "Zustandsbehafteter Dienst für Auktion" und "Zustandsbehafteter Dienst für Warteschlange" in einer E-Commerce-Website integriert werden, um eine Auktionslösung zu erstellen.

- **Anwendungsadministrator**: Trifft Entscheidungen zur Anwendungskonfiguration (Ausfüllen der Konfigurationsvorlagenparameter), zur Bereitstellung (Zuordnung zu den verfügbaren Ressourcen) und zur Servicequalität. Ein Anwendungsadministrator legt z. B. das Sprachgebietsschema (Englisch für die USA oder Japanisch für Japan) der Anwendung fest. Für eine andere bereitgestellte Anwendung können andere Einstellungen festgelegt werden.

- **Operator**: Stellt Anwendungen entsprechend der Anwendungskonfiguration und den vom Anwendungsadministrator angegebenen Anforderungen bereit. Ein Operator stellt z. B. die Anwendung bereit und stellt sicher, dass sie in Azure ausgeführt wird. Operatoren überwachen den Anwendungszustand und die Leistungsinformationen und verwalten bei Bedarf die physische Infrastruktur.


## Entwickeln
1. Ein *Dienstentwickler* entwickelt unter Verwendung des Programmiermodells [Reliable Actors](service-fabric-reliable-actors-introduction.md) oder [Reliable Services](../Service-Fabric/service-fabric-reliable-services-introduction.md) verschiedene Diensttypen.
2. Ein *Dienstentwickler* beschreibt die entwickelten Diensttypen deklarativ in einer Dienstmanifestdatei, die aus einem oder mehreren Code-, Konfigurations- und Datenpaketen besteht.
3. Ein *Anwendungsentwickler* erstellt dann mit verschiedenen Diensttypen eine Anwendung.
4. Der *Anwendungsentwickler* beschreibt den Anwendungstyp deklarativ in einem Anwendungsmanifest, indem er auf die Dienstmanifeste der zugehörigen Dienste verweist und verschiedene Konfigurations- und Bereitstellungseinstellungen der zugehörigen Dienste entsprechend überschreibt und parametrisiert.

Beispiele finden Sie unter [Get started with Reliable Actors](service-fabric-reliable-actors-get-started.md) und [Get started with Reliable Services](service-fabric-reliable-services-quick-start.md) (beide in englischer Sprache).

## Bereitstellen
1. Ein *Anwendungsadministrator* passt den Anwendungstyp in einer bestimmten Anwendung so an, dass sie in einem Service Fabric-Cluster bereitgestellt wird, indem er die entsprechenden Parameter des **ApplicationType**-Elements im Anwendungsmanifest angibt.

2. Ein *Operator* lädt das Anwendungspaket mit der [CopyApplicationPackage-Methode](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage.aspx) oder dem [Cmdlet "Copy-ServiceFabricApplicationPackage"](https://msdn.microsoft.com/library/azure/mt125905.aspx) in den ImageStore-Cluster hoch. Das Anwendungspaket enthält das Anwendungsmanifest und die Auflistung der Dienstpakete. Service Fabric stellt Anwendungen aus dem Anwendungspaket bereit, das im ImageStore gespeichert ist, bei dem es sich um einen Azure-Blob-Speicher oder den Service Fabric-Systemdienst handeln kann.

3. Der *Operator* stellt dann mit der [ProvisionApplicationAsync-Methode](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync.aspx), dem [Cmdlet "Register-ServiceFabricApplicationType"](https://msdn.microsoft.com/library/azure/mt125958.aspx) oder dem [REST-API-Vorgang "Create Application"](https://msdn.microsoft.com/library/azure/dn707692.aspx) den Anwendungstyp im Zielcluster aus dem hochgeladenen Anwendungspaket bereit.

3. Nach der Bereitstellung der Anwendung startet ein *Operator* die Anwendung mit den Parametern, die unter Verwendung der [CreateApplicationAsync-Methode](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.createapplicationasync.aspx), des [Cmdlets "New-ServiceFabricApplication"](https://msdn.microsoft.com/library/azure/mt125913.aspx) oder des [REST-API-Vorgangs "Create Application"](https://msdn.microsoft.com/library/azure/dn707692.aspx) vom *Anwendungsadministrator* angegeben wurden.

4. Nachdem die Anwendung bereitgestellt wurde, erstellt ein *Operator* mit der [CreateServiceAsync-Methode](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.servicemanagementclient.createserviceasync.aspx), dem [Cmdlet "New-ServiceFabricService"](https://msdn.microsoft.com/library/azure/mt125874.aspx) oder dem [REST-API-Vorgang "Create Application"](https://msdn.microsoft.com/library/azure/dn707692.aspx) auf Grundlage der verfügbaren Diensttypen neue Dienstinstanzen für die Anwendung.

5. Die Anwendung wird nun im Service Fabric-Cluster ausgeführt.

Beispiele finden Sie unter [Deploy an application](service-fabric-deploy-remove-applications.md) (in englischer Sprache).

## Test
1. Nach der Bereitstellung im lokalen Entwicklungscluster oder in einem Testcluster führt ein *Dienstentwickler* das integrierte Failovertestszenario mithilfe der Klassen [FailoverTestScenarioParameters](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.failovertestscenarioparameters.aspx) und [FailoverTestScenario](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.failovertestscenario.aspx) oder des [Cmdlets "Invoke-ServiceFabricFailoverTestScenario"](https://msdn.microsoft.com/library/azure/mt125935.aspx) aus. Im Failovertestszenario wird ein bestimmter Dienst über wichtige Übergänge und Failover ausgeführt, um sicherzustellen, dass er weiterhin verfügbar und aktiv ist.

2. Der *Dienstentwickler* führt dann das integrierte Chaostestszenario mithilfe der Klassen [ChaosTestScenarioParametersscenarioParameters](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.chaostestscenarioparameters.aspx) und [ChaosTestScenario](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.chaostestscenario.aspx) oder des [Cmdlets "Invoke-ServiceFabricChaosTestScenario"](https://msdn.microsoft.com/library/azure/mt126036.aspx) aus. Im Chaostestszenario werden nach dem Zufallsprinzip mehrere Knoten-, Codepaket- und Replikatfehler im Cluster ausgelöst.

Beispiele finden Sie unter [Testability scenarios](service-fabric-testability-scenarios.md) (in englischer Sprache).

## Upgrade
1. Ein *Dienstentwickler* aktualisiert die zugehörigen Dienste der instanziierten Anwendung und/oder behebt Fehler und stellt eine neue Version des Dienstmanifests bereit.

2. Ein *Anwendungsentwickler* überschreibt und parametrisiert die Konfigurations- und Bereitstellungseinstellungen der zugehörigen Dienste und stellt eine neue Version des Anwendungsmanifests bereit. Der Anwendungsentwickler bindet dann die neuen Versionen der Dienstmanifeste in der Anwendung ein und stellt eine neue Version des Anwendungstyps in einem aktualisierten Anwendungspaket bereit.

3. Ein *Anwendungsadministrator* bindet durch Aktualisieren der entsprechenden Parameter die neue Version des Anwendungstyps in der Zielanwendung ein.

4. Ein *Operator* lädt das aktualisierte Anwendungspaket mit der [CopyApplicationPackage-Methode](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage.aspx) oder dem [ Cmdlet "Copy-ServiceFabricApplicationPackage"](https://msdn.microsoft.com/library/azure/mt125905.aspx) in den ImageStore-Cluster hoch. Das Anwendungspaket enthält das Anwendungsmanifest und die Auflistung der Dienstpakete.

5. Ein *Operator* stellt die neue Version der Anwendung mit der [ProvisionApplicationAsync-Methode](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync.aspx), dem [Cmdlet "Register-ServiceFabricApplicationType"](https://msdn.microsoft.com/library/azure/mt125958.aspx) oder dem [REST-API-Vorgang "Provision an Application"](https://msdn.microsoft.com/library/azure/dn707692.aspx) im Zielcluster bereit.

6. Ein *Operator* führt mit der [UpgradeApplicationAsync-Methode](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.upgradeapplicationasync.aspx), dem [Cmdlet "Start ServiceFabricApplicationUpgrade"](https://msdn.microsoft.com/library/azure/mt125975.aspx) oder dem [REST-API-Vorgang "Upgrade Application by Application Type"](https://msdn.microsoft.com/library/azure/dn707692.aspx) ein Upgrade der Zielanwendung auf die neue Version durch.

7. Ein *Operator* überprüft den Status des Upgrades mithilfe der [GetApplicationUpgradeProgressAsync-Methode](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.getapplicationupgradeprogressasync.aspx), des [Cmdlets "Get-ServiceFabricApplicationUpgrade"](https://msdn.microsoft.com/library/azure/mt125988.aspx) oder des [REST-API-Vorgangs "Get Application Upgrade Progress"](https://msdn.microsoft.com/library/azure/dn707692.aspx).

8. Bei Bedarf ändert der *Operator* die Parameter des aktuellen Anwendungsupdates mit der [UpdateApplicationUpgradeAsync-Methode](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.updateapplicationupgradeasync.aspx), dem [Cmdlet "Update-ServiceFabricApplicationUpgrade"](https://msdn.microsoft.com/library/azure/mt126030.aspx) oder dem [REST-API-Vorgang "Update Application Upgrade"](https://msdn.microsoft.com/library/azure/dn707692.aspx) und wendet sie erneut an.

9. Bei Bedarf setzt der *Operator* das aktuelle Anwendungsupdate mit der [RollbackApplicationUpgradeAsync-Methode](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.rollbackapplicationupgradeasync.aspx), dem [Cmdlet "Start-ServiceFabricApplicationRollback"](https://msdn.microsoft.com/library/azure/mt125833.aspx) oder dem [REST-API-Vorgang "Rollback Application Upgrade"](https://msdn.microsoft.com/library/azure/dn707692.aspx) zurück.

10. Service Fabric aktualisiert die im Cluster ausgeführte Zielanwendung ohne Verlust der Verfügbarkeit der einzelnen zugehörigen Dienste.

Beispiele finden Sie im [Lernprogramm für Anwendungsupgrades](service-fabric-application-upgrade-tutorial.md).

## Verwalten
1. Bei Upgrades und Patches des Betriebssystems wird Service Fabric mit der Azure-Infrastruktur verbunden, um die Verfügbarkeit aller im Cluster ausgeführten Anwendungen zu gewährleisten.

2. Bei Upgrades und Patches der Service Fabric-Plattform aktualisiert sich Service Fabric selbst ohne Verlust der Verfügbarkeit aller im Cluster ausgeführten Anwendungen.

3. Ein *Anwendungsadministrator* genehmigt das Hinzufügen oder Entfernen von Knoten in einem Cluster nach der Analyse der verlaufsbezogenen Kapazität der Verwendungsdaten und des voraussichtlichen zukünftigen Bedarfs.

4. Ein *Operator* fügt die vom *Anwendungsadministrator* angegebenen Knoten hinzu oder entfernt sie.

5. Wenn neue Knoten im Cluster hinzugefügt oder vorhandene Knoten aus dem Cluster entfernt werden, nimmt Service Fabric einen Lastenausgleich der in allen Knoten des Clusters ausgeführten Anwendungen vor, um eine optimale Leistung zu erzielen.

## Remove
1. Ein *Operator* kann mithilfe der [DeleteServiceAsync-Methode](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync.aspx), des [Cmdlets "Remove-ServiceFabricService"](https://msdn.microsoft.com/library/azure/mt126033.aspx) oder des [REST-API-Vorgangs "Delete Service"](https://msdn.microsoft.com/library/azure/dn707692.aspx) eine bestimmte Instanz eines ausgeführten Diensts im Cluster löschen, ohne die gesamte Anwendung zu entfernen.  

2. Ein *Operator* kann mithilfe der [DeleteApplicationAsync-Methode](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync.aspx), des [Cmdlets "Remove-ServiceFabricApplication"](https://msdn.microsoft.com/library/azure/mt125914.aspx) oder des [REST-API-Vorgangs "Delete Application"](https://msdn.microsoft.com/library/azure/dn707692.aspx) auch eine Anwendungsinstanz und alle ihre Dienste löschen.

3. Wenn die Anwendung und die Dienste beendet wurden, kann der *Operator* die Bereitstellung des Anwendungstyps mithilfe der [UnprovisionApplicationAsync-Methode](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.unprovisionapplicationasync.aspx), des [Cmdlets "Unregister-ServiceFabricApplicationType"](https://msdn.microsoft.com/library/azure/mt125885.aspx) oder des [REST-API-Vorgangs "Unprovision an Application"](https://msdn.microsoft.com/library/azure/dn707692.aspx) aufheben. Beim Aufheben der Bereitstellung des Anwendungstyps wird das Anwendungspaket nicht aus dem ImageStore entfernt. Sie müssen das Anwendungspaket manuell entfernen.

4. Ein *Operator* entfernt das Anwendungspaket mit der [RemoveApplicationPackage-Methode](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.removeapplicationpackage.aspx) oder dem [Cmdlet "Remove-ServiceFabricApplicationPackage"](https://msdn.microsoft.com/library/azure/mt163532.aspx) aus dem ImageStore.

Beispiele finden Sie unter [Deploy an application](service-fabric-deploy-remove-applications.md) (in englischer Sprache).

## Nächste Schritte

Weitere Informationen zum Entwickeln, Testen und Verwalten von Service Fabric-Anwendungen und Service Fabric-Diensten:

- [Develop a Service Fabric service](service-fabric-develop-your-service-index.md) (in englischer Sprache)
- [Manage a Service Fabric service](service-fabric-manage-your-service-index.md) (in englischer Sprache)
- [Test a Service Fabric service](service-fabric-test-your-service-index.md) (in englischer Sprache)
- [REST-Based Application Lifecycle Sample](service-fabric-rest-based-application-lifecycle-sample.md) (in englischer Sprache)
 

<!---HONumber=July15_HO4-->