<properties
   pageTitle="Einrichten eines Service Fabric-Clusters mithilfe einer ARM-Vorlage | Microsoft Azure"
   description="Einrichten eines Service Fabric-Clusters mithilfe einer ARM-Vorlage."
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
   ms.date="11/19/2015"
   ms.author="chackdan"/>

# Einrichten eines Service Fabric-Clusters mithilfe einer ARM-Vorlage

Auf dieser Seite erhalten Sie Informationen zum Einrichten eines Service Fabric-Clusters mithilfe einer ARM-Vorlage. Hier wird davon ausgegangen, dass Ihr Abonnement ausreichend Kerne zum Bereitstellen der virtuellen IaaS-Computer umfasst, aus denen dieser Cluster besteht.

## Voraussetzungen

- Wenn Sie einen sicheren Cluster einrichten möchten, müssen Sie ein X.509-Zertifikat in Ihren Schlüsseltresor hochgeladen haben. Sie benötigen die URL des Quelltresors, die Zertifikat-URL und den Zertifikatfingerabdruck.
-  Weitere Informationen zur Vorgehensweise finden Sie unter [Service Fabric-Clustersicherheit](service-fabric-cluster-security.md).

## Abrufen einer ARM-Beispielvorlage

1. ARM-Beispielvorlagen stehen im [Azure-Schnellstart-Vorlagenkatalog auf Github](https://github.com/Azure/azure-quickstart-templates) zur Verfügung. Die Namen aller Service Fabric-Vorlagen beginnen mit „service-fabric...“. Sie können im Repository entweder nach „fabric“ suchen oder einfach zu den Beispielvorlagen scrollen.
2. Um die Vorlagen schneller finden zu können, wurden sie wie folgt benannt:
	1. [service-fabric-unsecure-cluster-5-node-1-nodetype](http://go.microsoft.com/fwlink/?LinkId=716923) stellt eine Vorlage für einen unsicheren Einzelknotencluster mit 5 Knoten dar. 
	3. [service-fabric-secure-cluster-5-node-1-nodetype-wad](http://go.microsoft.com/fwlink/?LinkID=716924) stellt eine Vorlage für einen sicheren Einzelknotencluster mit 5 Knoten und WAD-Unterstützung dar. 
	4. [service-fabric-secure-cluster-10-node-2-nodetype-wad](http://go.microsoft.com/fwlink/?LinkId=716925) stellt eine Vorlage für einen sicheren Cluster mit zwei Clustern mit je 10 Knoten und WAD-Unterstützung dar. 
	

## Erstellen einer benutzerdefinierten ARM-Vorlage

2. Sie haben die folgenden zwei Möglichkeiten: 
	1. Sie können eine Beispielvorlage aus dem [Azure-Schnellstart-Vorlagenkatalog auf GitHub](https://github.com/Azure/azure-quickstart-templates) abrufen und ändern.
	2. Melden Sie sich beim Azure-Portal an, und verwenden Sie die Service Fabric-Portalseiten, um die anzupassende Vorlage zu generieren. Das entsprechende Verfahren wird im Folgenden beschrieben.
3. Melden Sie sich beim Azure-Portal [http://aka.ms/servicefabricportal](http://aka.ms/servicefabricportal) an.
2. Führen Sie das Verfahren zum Erstellen des Clusters aus (siehe [Erstellen des Service Fabric-Cluster über das Portal](service-fabric-cluster-creation-via-portal.md)). Klicken Sie jedoch nicht auf ****create**, sondern wechseln Sie stattdessen zur Übersicht, und laden Sie die Vorlage herunter.

 ![DownloadTemplate][DownloadTemplate]

## Bereitstellen der ARM-Vorlage in Azure mithilfe von Azure PS

Ausführliche Verfahrensweisen zum Bereitstellen der Vorlage mit PowerShell finden Sie unter [Bereitstellen einer Anwendung mit einer Azure-Ressourcen-Manager-Vorlage](resource-group-template-deploy.md).

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte
- [Service Fabric-Clustersicherheit](service-fabric-cluster-security.md) 
- [Verwalten von Service Fabric-Anwendungen in Visual Studio](service-fabric-manage-application-in-visual-studio.md)
- [Einführung in das Service Fabric-Integritätsmodell](service-fabric-health-introduction.md)

<!--Image references-->
[DownloadTemplate]: ./media/service-fabric-cluster-creation-via-arm/DownloadTemplate.png

<!---HONumber=AcomDC_1210_2015-->