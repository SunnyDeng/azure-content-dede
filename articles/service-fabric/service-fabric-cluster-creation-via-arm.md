<properties
   pageTitle="Einrichten eines Service Fabric-Clusters mit einer Azure-Ressourcen-Manager-Vorlage | Microsoft Azure"
   description="Richten Sie einen Service Fabric-Cluster mit einer Azure-Ressourcen-Manager-Vorlage ein."
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
   ms.date="02/12/2016"
   ms.author="chackdan"/>


# Einrichten eines Service Fabric-Clusters mit einer Azure-Ressourcen-Manager-Vorlage

Diese Seite zeigt Ihnen das Einrichten eines Azure Service Fabric-Clusters mithilfe einer Azure-Ressourcen-Manager-Vorlage. Ihr Abonnement muss ausreichend Kerne zum Bereitstellen der virtuellen IaaS-Computer umfassen, aus denen dieser Cluster bestehen soll.

## Voraussetzungen

- Um einen sicheren Cluster einzurichten, müssen Sie zuerst ein X. 509-Zertifikat in Ihren Schlüsseltresor hochladen. Sie benötigen die Quelltresor-URL, die Zertifikat-URL und den Zertifikatfingerabdruck.
- Weitere Informationen zum Einrichten eines sicheren Clusters finden Sie unter [Service Fabric-Clustersicherheit](service-fabric-cluster-security.md).

## Abrufen einer Ressourcen-Manager-Vorlage

Ressourcen-Manager-Beispielvorlagen stehen im [Azure-Schnellstart-Vorlagenkatalog auf GitHub](https://github.com/Azure/azure-quickstart-templates) zur Verfügung. Alle Namen der Service Fabric-Vorlagen beginnen mit „Service-Fabric...“. Sie können entweder das Repository nach „Fabric“ durchsuchen oder bis zum Satz von Beispielvorlagen nach unten scrollen. Um die Vorlagen schneller finden zu können, wurden sie wie folgt benannt:

- [service-fabric-unsecure-cluster-5-node-1-nodetype](http://go.microsoft.com/fwlink/?LinkId=716923) stellt eine Vorlage für einen unsicheren Einzelknotencluster mit fünf Knoten dar.

- [service-fabric-secure-cluster-5-node-1-nodetype-wad](http://go.microsoft.com/fwlink/?LinkID=716924) stellt eine Vorlage für einen sicheren Einzelknotencluster mit fünf Knoten und WAD-Unterstützung dar.

- [service-fabric-secure-cluster-10-node-2-nodetype-wad](http://go.microsoft.com/fwlink/?LinkId=716925) stellt eine Vorlage für einen sicheren Cluster mit zwei Clustern mit je zehn Knoten und WAD-Unterstützung dar.

## Erstellen einer benutzerdefinierten Azure-Ressourcen-Manager-Vorlage

Es gibt zwei Methoden zum Erstellen einer benutzerdefinierten Ressourcen-Manager-Vorlage:

1. Sie können eine Beispielvorlage aus dem [Azure-Schnellstart-Vorlagenkatalog auf GitHub](https://github.com/Azure/azure-quickstart-templates) abrufen und ändern.

2. Sie können sich beim Azure-Portal anmelden und die Service Fabric-Portalseiten verwenden, um die anzupassende Vorlage zu generieren. Gehen Sie dazu folgendermaßen vor:

    a. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

    b. Führen Sie das Verfahren zum Erstellen des Clusters aus, das unter [Erstellen eines Service Fabric-Clusters über das Portal](service-fabric-cluster-creation-via-portal.md) beschrieben wird, aber klicken Sie nicht auf **Erstellen**. Wechseln Sie stattdessen zur **Zusammenfassung**, und laden Sie die Vorlage herunter, wie in der folgenden Abbildung gezeigt.

 ![Screenshot der Seite „Service Fabric-Cluster“ mit dem Link zum Herunterladen einer Ressource-Manager-Vorlage][DownloadTemplate]

## Bereitstellen der Ressourcen-Manager-Vorlage über Azure PowerShell

Ausführliche Verfahrensweisen zum Bereitstellen der Vorlage mit PowerShell finden Sie unter [Bereitstellen von Azure-Ressourcen-Manager-Vorlagen mithilfe von PowerShell](../resource-group-template-deploy.md).

>[AZURE.NOTE] Um Verfügbarkeit sicherzustellen und den Zustand beizubehalten, muss eine bestimmte Anzahl von Knoten in einem Service Fabric-Cluster stets in Betrieb sein. Dies wird auch als „Aufrechterhalten eines Quorums“ bezeichnet. Daher ist es üblicherweise nicht sicher, alle Computer innerhalb des Clusters herunterzufahren, sofern Sie nicht zunächst eine [vollständige Sicherung des Zustands](service-fabric-reliable-services-backup-restore.md) durchgeführt haben.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte
- [Service Fabric-Clustersicherheit](service-fabric-cluster-security.md)
- [Verwalten von Service Fabric-Anwendungen in Visual Studio](service-fabric-manage-application-in-visual-studio.md)
- [Einführung in das Service Fabric-Integritätsmodell](service-fabric-health-introduction.md)

<!--Image references-->
[DownloadTemplate]: ./media/service-fabric-cluster-creation-via-arm/DownloadTemplate.png

<!---HONumber=AcomDC_0218_2016-->