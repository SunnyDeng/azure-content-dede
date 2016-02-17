<properties
   pageTitle="Einrichten eines Service Fabric-Clusters mithilfe von Visual Studio | Microsoft Azure"
   description="In diesem Artikel wird das Einrichten eines Service Fabric-Clusters mithilfe einer Azure-Ressourcen-Manager-Vorlage (ARM) beschrieben, die anhand eines Azure-Ressourcengruppenprojekts in Visual Studio erstellt wurde."
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

# Einrichten eines Service Fabric-Clusters mit Visual Studio
In diesem Artikel wird das Einrichten eines Azure Service Fabric-Clusters mit Visual Studio und einer Azure-Ressourcen-Manager-Vorlage (ARM) beschrieben. Wir verwenden ein Azure-Ressourcengruppenprojekt in Visual Studio zum Erstellen der Vorlage. Nach Erstellen der Vorlage kann diese direkt aus Visual Studio in Azure bereitgestellt werden. Sie kann aber auch in einem Skript oder als Teil einer Einrichtung der fortlaufenden Integration (Continuous Integration, CI) verwendet werden.

## Erstellen einer Service Fabric-Clustervorlage mittels eines Azure-Ressourcengruppenprojekts
Öffnen Sie zunächst Visual Studio, und erstellen Sie ein Azure-Ressourcengruppenprojekt (das unter dem Ordner **Cloud** verfügbar ist):

![Dialogfeld „Neues Projekt“ mit ausgewähltem Projekt „Azure-Ressourcengruppe“][1]

Sie können eine neue Visual Studio-Projektmappe für dieses Projekt erstellen oder es einer vorhandenen Projektmappe hinzufügen.

>[AZURE.NOTE] Wenn das Azure-Ressourcengruppenprojekt nicht unter dem Knoten „Cloud“ angezeigt wird, ist das Azure SDK nicht installiert. Starten Sie den Webplattform-Installer ([installieren Sie ihn jetzt](http://www.microsoft.com/web/downloads/platform.aspx), wenn dies nicht bereits geschehen ist), suchen Sie nach „Azure SDK für .NET“, und installieren Sie die Version, die mit Ihrer Version von Visual Studio kompatibel ist.

Nachdem Sie auf die Schaltfläche „OK“ geklickt haben, fordert Visual Studio Sie auf, die Ressourcen-Manager-Vorlage auszuwählen, die Sie erstellen möchten:

![Dialogfeld „Azure-Vorlage auswählen“ mit ausgewählter Service Fabric-Clustervorlage][2]

Wählen Sie die Vorlage „Service-Fabric-Cluster“ aus, und klicken Sie erneut auf „OK“. Das Projekt und die Ressourcen-Manager-Vorlage sind jetzt erstellt.

## Vorbereiten der Vorlage für die Bereitstellung
Bevor die Vorlage bereitgestellt wird, um den Cluster zu erstellen, müssen Sie Werte für die erforderlichen Vorlagenparameter angeben. Diese Parameterwerte werden aus der Datei `ServiceFabricCluster.param.dev.json` gelesen, die sich im Ordner `Templates` des Ressourcengruppenprojekts befindet. Öffnen Sie die Datei, und geben Sie die folgenden Werte an:

|Parametername |Beschreibung|
|-----------------------  |--------------------------|
|clusterLocation |Der Name der **Azure-Region**, in der sich der Service Fabric-Cluster befinden soll. Beispiel: „USA, Osten“.|
|clusterName |Der Domänennamensystem-Name (DNS) des Service Fabric-Clusters, der von der Vorlage erstellt wird. <br /><br /> Wenn Sie beispielsweise diesen Parameter auf `myBigCluster` festlegen, und der `clusterLocation`-Parameter ist auf „USA, Osten“ festgelegt, lautet der Name des Clusters `myBigCluster.eastus.cloudapp.azure.com`.|
|certificateThumbprint |Der Fingerabdruck des Zertifikats zum Schutz des Clusters.|
|sourceVaultValue |Die *Ressourcen-ID* des Schlüsseltresors, in dem das Zertifikat zum Schutz des Clusters gespeichert ist.|
|certificateUrlValue |Die URL des Sicherheitszertifikats des Clusters.|

Die Visual Studio Service Fabric-Ressourcen-Manager-Vorlage erstellt einen sicheren Cluster, der durch ein Zertifikat geschützt ist. Dieses Zertifikat wird durch die letzten drei Vorlagenparameter (`certificateThumbprint`, `sourceVaultValue` und `certificateUrlValue`) identifiziert und muss in einem **Azure Key Vault** vorhanden sein. Weitere Informationen zum Erstellen des Sicherheitszertifikats des Clusters finden Sie im Artikel [Schützen eines Service Fabric-Clusters mit Zertifikaten](service-fabric-cluster-security.md).

## Optional: Hinzufügen öffentlicher Anwendungsports
Vielleicht möchten Sie auch die öffentlichen Anwendungsports für den Cluster vor seiner Bereitstellung ändern. Standardmäßig öffnet die Vorlage nur zwei öffentliche TCP-Ports (80 und 8081). Wenn Sie für Ihre Anwendungen mehr benötigen, ändern Sie die Azure-Load Balancer-Definition in der Vorlage. Die Definition ist in der Hauptvorlagendatei (`SecureFabricCluster.json`) gespeichert. Öffnen Sie die Datei, und suchen Sie nach `loadBalancedAppPort`. Sie werden feststellen, dass jeder Port mit drei Artefakten verknüpft ist:

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

3. Eine *Lastenausgleichsregel*, die den Port und den Test verbindet, wodurch Lastenausgleich über eine Reihe von Service Fabric-Clusterknoten hinweg möglich ist: ```json
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
    ``` Wenn die Anwendungen, die Sie für den Cluster bereitstellen möchten, mehr Ports benötigt, können Sie diese durch Erstellen zusätzlicher Tests und Lastenausgleichsregel-Definitionen hinzufügen. Weitere Informationen zum Arbeiten mit dem Azure-Load Balancer über Ressourcen-Manager-Vorlagen finden Sie unter [Erste Schritte zum Konfigurieren des internen Lastenausgleichs mit dem Azure-Ressourcen-Manager](../load-balancer/load-balancer-internal-arm-powershell.md).

## Bereitstellen der Vorlage mit Visual Studio
Nachdem Sie alle erforderlichen Parameterwerte in der Datei `ServiceFabricCluster.param.dev.json` gespeichert haben, können Sie die Vorlage bereitstellen und Ihren Service Fabric-Cluster erstellen. Klicken Sie im Visual Studio-Projektmappen-Explorer mit der rechten Maustaste auf das Ressourcengruppenprojekt, und wählen Sie **Bereitstellen** aus. Visual Studio zeigt das Dialogfeld **In Ressourcengruppe bereitstellen** an, und fordert Sie bei Bedarf auf, sich bei Azure zu authentifizieren:

![Dialogfeld "In Ressourcengruppe bereitstellen"][3]

Im Dialogfeld können Sie eine vorhandene Ressourcen-Manager-Ressourcengruppe für den Cluster auswählen und nach Wunsch eine neue erstellen. In der Regel ist es sinnvoll, eine eigene Ressourcengruppe für einen Service Fabric-Cluster zu verwenden.

Nach Klicken auf die Schaltfläche „Bereitstellen“ werden Sie von Visual Studio aufgefordert, die Parameterwerte der Vorlage zu bestätigen. Klicken Sie auf die Schaltfläche **Speichern**. Ein Parameter hat keinen persistenten Wert: das Administratorkonto-Kennwort für den Cluster. Sie müssen einen Kennwortwert bereitstellen, wenn Visual Studio Sie dazu auffordert.

>[AZURE.NOTE] Wenn PowerShell noch nicht zum Verwalten von Azure auf dem aktuell genutzten Computer verwendet wurde, sind kleinere Wartungsaufgaben nötig: 1. Aktivieren Sie die Verwendung von PowerShell-Skripts durch Ausführen des Befehls [`Set-ExecutionPolicy`](https://technet.microsoft.com/library/hh849812.aspx). Für Entwicklungscomputer ist die Richtlinie „Unrestricted“ in der Regel akzeptabel. 2. Entscheiden Sie, ob die Erfassung von Diagnosedaten von Azure PowerShell-Befehlen zugelassen werden soll, und führen Sie nach Bedarf [`Enable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619303.aspx) oder [`Disable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619236.aspx) aus. Bei Verwendung von Azure PowerShell, Version 0.9.8 oder älter, heißen diese Befehle `Enable-AzureDataCollection` bzw. `Discable-AzureDataCollection`. Dadurch lassen sich unnötige Eingabeaufforderungen während der Bereitstellung der Vorlage vermeiden.

Sie können den Status des Bereitstellungsprozesses im Ausgabefenster von Visual Studio überwachen. Nach Abschluss der Bereitstellung der Vorlage ist Ihr neuer Cluster einsatzbereit!

Wenn Fehler auftreten, wechseln Sie zum [Azure-Portal](https://portal.azure.com/), und überprüfen Sie **Benachrichtigungen**. Falls die Bereitstellung einer Ressourcengruppe keinen Erfolg hatte, finden Sie dort detaillierte Diagnoseinformationen.

## Nächste Schritte
- [Informationen zum Einrichten eines Service Fabric-Clusters mit dem Azure-Portal](service-fabric-cluster-creation-via-portal.md)
- [Informationen zum Verwalten und Bereitstellen von Service Fabric-Anwendungen mit Visual Studio](service-fabric-manage-application-in-visual-studio.md)

<!--Image references-->
[1]: ./media/service-fabric-cluster-creation-via-visual-studio/azure-resource-group-project-creation.png
[2]: ./media/service-fabric-cluster-creation-via-visual-studio/selecting-azure-template.png
[3]: ./media/service-fabric-cluster-creation-via-visual-studio/deploy-to-azure.png

<!---HONumber=AcomDC_0128_2016-->