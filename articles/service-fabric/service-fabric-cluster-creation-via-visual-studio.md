<properties
   pageTitle="Einrichten eines Service Fabric-Clusters mithilfe von Visual Studio | Microsoft Azure"
   description="In diesem Artikel wird das Einrichten eines Service Fabric-Clusters mithilfe einer Azure-Ressourcen-Manager-Vorlage (ARM) beschrieben, die anhand eines Projekts vom Typ „Azure-Ressourcengruppe“ in Visual Studio erstellt wurde."
   services="service-fabric"
   documentationCenter=".net"
   authors="karolz-ms"
   manager="adegeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/13/2015"
   ms.author="karolz@microsoft.com"/>

# Einrichten eines Service Fabric-Clusters mithilfe von Visual Studio
In diesem Artikel wird das Einrichten eines [**Service Fabric**](http://azure.microsoft.com/documentation/services/service-fabric/)-Clusters mithilfe von **Visual Studio** und einer [**Azure-Ressourcen-Manager (ARM)**](https://azure.microsoft.com/documentation/articles/resource-group-overview/)-Vorlage beschrieben. Wir verwenden das Visual Studio-Projekt **Azure-Ressourcengruppe** zum Erstellen der Vorlage. Nach Erstellen der Vorlage kann diese direkt aus Visual Studio in Azure bereitgestellt werden. Sie kann aber auch in einem Skript oder als Teil einer Einrichtung der **fortlaufenden Integration** verwendet werden.

## Erstellen einer Service Fabric-Clustervorlage mittels eines Azure-Ressourcengruppenprojekts
Öffnen Sie zunächst Visual Studio, und erstellen Sie ein Projekt des Typs „Azure-Ressourcengruppe“ (das unter dem Ordner „Cloud“ verfügbar ist):

![Dialogfeld „Neues Projekt“ mit ausgewähltem Projekt „Azure-Ressourcengruppe“][1]

Sie können eine neue Visual Studio-Projektmappe für dieses Projekt erstellen oder es einer vorhandenen Projektmappe hinzufügen.

>[AZURE.NOTE]Wenn das Projekt „Azure-Ressourcengruppe“ nicht unter dem Knoten „Cloud“ angezeigt wird, ist das Azure SDK nicht installiert. Starten Sie den Webplattform-Installer ([den Sie bei Bedarf hier herunterladen und dann installieren können](http://www.microsoft.com/web/downloads/platform.aspx)), suchen Sie nach „Azure SDK für .NET“, und installieren Sie die Version, die mit Ihrer Visual Studio-Version kompatibel ist.

Nach Klicken auf „OK“ fordert Visual Studio Sie zum Auswählen der zu erstellenden ARM-Vorlage aus:

![Dialogfeld „Azure-Vorlage auswählen“ mit ausgewählter Service Fabric-Clustervorlage][2]

Wählen Sie die Vorlage „Service-Fabric-Cluster“ aus, und klicken Sie erneut auf „OK“. Das Projekt und die ARM-Vorlage werden jetzt erstellt.

## Vorbereiten der Vorlage für die Bereitstellung
Bevor die Vorlage bereitgestellt wird, um den Cluster zu erstellen, müssen Sie Werte für die erforderlichen Vorlagenparameter angeben. Diese Parameterwerte werden aus der Datei `ServiceFabricCluster.param.dev.json` gelesen, die sich im Ressourcengruppenprojekt unter dem Ordner `Templates` befindet. Öffnen Sie die Datei, und geben Sie die folgenden Werte an:

|Parametername |Beschreibung|
|-----------------------  |--------------------------|
|clusterLocation |Der Name der **Azure-Region**, in der sich der Service Fabric-Cluster befinden soll. Beispiel: „USA, Osten“.|
|clusterName |Der (DNS-) Name des Service Fabric-Clusters, der von der Vorlage erstellt wird. <br /><br /> Wenn Sie beispielsweise diese Parameter auf „myBigCluster“ festlegen und der `clusterLocation`-Parameter auf „USA, Osten“ festgelegt ist, lautet der Name des Clusters `myBigCluster.eastus.cloudapp.azure.com`.|
|certificateThumbprint |Der Fingerabdruck des Zertifikats zum Schutz des Clusters.|
|sourceVaultValue |Die *Ressourcen-ID* des Schlüsseltresors, in dem das Zertifikat zum Schutz des Clusters gespeichert ist.|
|certificateUrlValue |Die URL des Sicherheitszertifikats des Clusters.|

Die Visual Studio Service Fabric-ARM-Vorlage erstellt einen sicheren Cluster, der durch ein Zertifikat geschützt ist. Dieses Zertifikat wird durch die letzten drei Vorlagenparameter identifiziert (`certificateThumbprint`, `sourceVaultValue` und `certificateUrlValue`) und muss in einem **Azure Key Vault** vorhanden sein. Weitere Informationen zum Erstellen des Sicherheitszertifikats des Clusters finden Sie im Artikel [Schützen eines Service Fabric-Clusters mit Zertifikaten](service-fabric-cluster-security.md).

## Optional: Hinzufügen öffentlicher Anwendungsports
Ein weiterer Aspekt der Vorlage, den Sie ggf. vor ihrer Bereitstellung ändern möchten, ist, sind **öffentliche Anwendungsports** für den Cluster. Standardmäßig öffnet die Vorlage nur zwei öffentliche TCP-Ports (80 und 8081). Wenn Sie für Ihre Anwendung mehr benötigen, müssen Sie die Azure Load Balancer-Definition in der Vorlage ändern. Die Definition ist in der Hauptvorlagendatei (`SecureFabricCluster.json`) gespeichert. Öffnen Sie die Datei, und suchen Sie nach „loadBalancedAppPort“. Sie werden feststellen, dass jeder Port mit drei Artefakten verknüpft ist:

1. Einem Vorlagenparameter, der den Wert für den TCP-Port definiert: ```json
	"loadBalancedAppPort1": {
	    "type": "int",
	    "defaultValue": 80
	}
	```

2. Einem *Test*, der bestimmt, wie oft und wie lange der Azure Load Balancer versucht, einen bestimmten Service Fabric-Knoten zu verwenden, bevor ein Failover auf einen anderen erfolgt. Die Tests sind Teil der Load Balancer-Ressource. Hier sehen Sie die Testdefinition für den ersten Standardport der Anwendung: ```json
	{
        "name": "AppPortProbe1",
        "properties": {
            "intervalInSeconds": 5,
            "numberOfProbes": 2,
            "port": "[parameters('loadBalancedAppPort1')]",
            "protocol": "tcp"
        }
    }
	```

3. Eine *Lastenausgleichsregel*, die den Port und den Test miteinander verbindet und einen Lastenausgleich zwischen einer Gruppe von Service Fabric-Clusterknoten ermöglicht: ```json
	{
	    "name": "AppPortLBRule1",
	    "properties": {
	        "backendAddressPool": {
	            "id": "[variables('lbPoolID0')]"
	        },
	        "backendPort": "[parameters('loadBalancedAppPort1')]",
	        "enableFloatingIP": false,
	        "frontendIPConfiguration": {
	            "id": "[variables('lbIPConfig0')]"
	        },
	        "frontendPort": "[parameters('loadBalancedAppPort1')]",
	        "idleTimeoutInMinutes": 5,
	        "probe": {
	            "id": "[concat(variables('lbID0'),'/probes/AppPortProbe1')]"
	        },
	        "protocol": "tcp"
	    }
	}
    ```Wenn Anwendungen, die Sie im Cluster bereitstellen möchten, mehr Ports benötigen, müssen Sie diese hinzufügen, indem Sie zusätzliche Test und Definitionen für den Lastenausgleich erstellen. Weitere Informationen zum Arbeiten mit Azure Load Balancer über ARM-Vorlagen finden Sie unter [Erste Schritte zum Konfigurieren des internen Lastenausgleichs mit dem Azure-Ressourcen-Manager](https://azure.microsoft.com/documentation/articles/load-balancer-internal-arm-powershell/).

## Bereitstellen der Vorlage mithilfe von Visual Studio
Nach dem Speichern aller erforderlichen Parameterwerte in der Datei `ServiceFabricCluster.param.dev.json` können Sie die Vorlage bereitstellen und Ihren Service Fabric-Cluster erstellen. Klicken Sie im Visual Studio-Projektmappen-Explorer mit der rechten Maustaste auf das Projekt „Ressourcengruppe“, und wählen Sie „Bereitstellen...“ aus. Visual Studio zeigt das Dialogfeld „In Ressourcengruppe bereitstellen“ an, und fordert Sie bei Bedarf auf, sich bei Azure zu authentifizieren:

![Dialogfeld "In Ressourcengruppe bereitstellen"][3]

Im Dialogfeld können Sie eine vorhandene Azure-RM-Ressourcengruppe für den Cluster auswählen und nach Wunsch eine neue erstellen. In der Regel ist es sinnvoll, eine eigene Ressourcengruppe für einen Service Fabric-Cluster zu verwenden.

Nach Klicken auf die Schaltfläche „Bereitstellen“ werden Sie von Visual Studio aufgefordert, Parameterwerte der Vorlage zu bestätigen. Klicken Sie auf die Schaltfläche „Speichern“. Mit dem Kennwort des Administratorkontos gibt es einen Parameter ohne beständigen Wert für den Cluster. Geben Sie das Kennwort ein, wenn Visual Studio Sie dazu auffordert.

>[AZURE.NOTE]Wenn PowerShell noch nicht zum Verwalten von Azure auf dem aktuell genutzten Computer verwendet wurde, sind kleinere Wartungsaufgaben nötig:1. Aktivieren Sie die Verwendung von PowerShell-Skripts durch Ausführen des Befehls [`Set-ExecutionPolicy`](https://technet.microsoft.com/library/hh849812.aspx). Für Entwicklungscomputer ist die Richtlinie „Unrestricted“ in der Regel akzeptabel. 2. Entscheiden Sie, ob die Erfassung von Diagnosedaten von Azure PowerShell-Befehlen zugelassen werden soll, und führen Sie nach Bedarf [`Enable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619303.aspx) oder [`Disable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619236.aspx) aus. Bei Verwendung von Azure PowerShell, Version 0.9.8 oder älter, heißen diese Befehle `Enable-AzureDataCollection` bzw. `Discable-AzureDataCollection`. Dadurch lassen sich unnötige Eingabeaufforderungen während der Bereitstellung der Vorlage vermeiden.

Sie können den Status des Bereitstellungsprozesses im Visual Studio-Fenster „Ausgabe“ überwachen. Nach Abschluss der Bereitstellung der Vorlage ist Ihr neuer Cluster einsatzbereit!

Wenn keine Fehler auftreten, wechseln Sie zum [Azure-Portal](https://portal.azure.com/), und überprüfen Sie Benachrichtigungen. Falls die Bereitstellung einer Ressourcengruppe keinen Erfolg hatte, finden Sie hier detaillierte Diagnose-Informationen.

## Nächste Schritte
- [Informationen zum Einrichten eines Service Fabric-Clusters mithilfe der Azure-Portals](service-fabric-cluster-creation-via-portal.md)
- [Informationen zum Verwalten und Bereitstellen von Service Fabric-Anwendungen mithilfe von Visual Studio](service-fabric-manage-applications-in-visual-studio.md)

<!--Image references-->
[1]: ./media/service-fabric-cluster-creation-via-visual-studio/azure-resource-group-project-creation.png
[2]: ./media/service-fabric-cluster-creation-via-visual-studio/selecting-azure-template.png
[3]: ./media/service-fabric-cluster-creation-via-visual-studio/deploy-to-azure.png

<!---HONumber=Nov15_HO4-->