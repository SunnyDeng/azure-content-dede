<properties
   pageTitle="Verwalten von Service Fabric-Anwendungen in Visual Studio"
   description="Sie können Ihre Microsoft Azure Service Fabric-Anwendungen und -Dienste in Visual Studio verwalten."
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/14/2015"
   ms.author="jesseb"/>

# Verwalten von Service Fabric-Anwendungen in Visual Studio

Sie können Ihre Microsoft Azure Service Fabric-Anwendungen und -Dienste in Visual Studio verwalten. Sobald Sie die [Einrichtung Ihrer Entwicklungsumgebung](../service-fabric-setup-your-development-environment) abgeschlossen haben, können Sie mit Visual Studio Service Fabric-Anwendungen erstellen, Dienste hinzufügen und Anwendungen packen, registrieren und im lokalen Entwicklungscluster bereitstellen.

Um die Service Fabric-Anwendung zu verwalten, klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Anwendungsprojekt.

![Service Fabric-Anwendung mit Rechtsklick auf das Anwendungsprojekt verwalten][manageservicefabric]

## Bereitstellen einer Service Fabric-Anwendung

Beim Bereitstellen einer Service Fabric-Anwendung werden die folgenden Schritte in einem Vorgang ausgeführt.

1. Erstellen des Anwendungspakets
2. Hochladen des Anwendungspakets in den Image-Speicher
3. Registrieren des Anwendungstyps
4. Entfernen ausgeführter Anwendungsinstanzen
5. Erstellen einer neuen Anwendungsinstanz

Um die Anwendung mit Visual Studio bereitzustellen, klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf die Service Fabric-Anwendung, und klicken Sie auf **Bereitstellen**. Sie können auch **F5** drücken, um die Anwendung bereitzustellen und den Debugger an alle Anwendungsinstanzen anzufügen.

Die Bereitstellung kann mit **Bereitstellung entfernen** entfernt werden. Hierbei werden die oben genannten Bereitstellungsschritte rückgängig gemacht.

1. Entfernen ausgeführter Anwendungsinstanzen
2. Aufheben der Registrierung des Anwendungstyps
3. Entfernen des Anwendungspakets aus dem Image-Speicher

## Hinzufügen von Diensten zur Service Fabric-Anwendung

Sie können der Anwendung neue Fabric Services-Dienste hinzufügen, um die Funktionalität der Anwendung zu erweitern. Um sicherzustellen, dass der Dienst im Anwendungspaket enthalten ist, fügen Sie den Dienst über das Menüelement **Neue Fabric-Dienst** hinzu.

![Neuen Fabric-Dienst zur Anwendung hinzufügen][newservice]

Wählen Sie den Service Fabric-Projekttyp aus, der zur Anwendung hinzugefügt werden soll, und geben Sie einen Namen für den Dienst an. Lesen Sie die Informationen in [Auswählen eines Frameworks für den Dienst](service-fabric-choose-framework.md), um zu entscheiden, welcher Diensttyp verwendet werden soll.

![Projekttyp des Fabric-Diensts auswählen, das zur Anwendung hinzugefügt werden soll][addserviceproject]

Der neue Dienst wird zur Projektmappe und dem vorhandenen Anwendungspaket hinzugefügt. Die Dienstverweise und eine Standarddienstinstanz werden zum Anwendungsmanifest hinzugefügt. Der Dienst wird erstellt und mit der nächsten Bereitstellung der Anwendung gestartet.

![Der neue Dienst wird zum Anwendungsmanifest hinzugefügt][newserviceapplicationmanifest]

## Packen einer Service Fabric-Anwendung

Um die Anwendung und die zugehörigen Dienste in einem Cluster bereitzustellen, muss ein Anwendungspaket erstellt werden. Das Paket ordnet das Anwendungsmanifest, das bzw. die Dienstmanifeste und andere erforderliche Dateien in einem bestimmten Layout an. Visual Studio richtet das Paket im Projektordner der Anwendung im Verzeichnis "Pkg" ein und verwaltet es. Klicken Sie zum Erstellen oder Aktualisieren des Anwendungspakets auf **Packen**. Dies ist möglicherweise erforderlich, falls Sie die Anwendung mithilfe benutzerdefinierter Powershell-Scripts bereitstellen möchten.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte

- [Service Fabric-Anwendungsmodell](../service-fabric-application-model)
- [Service Fabric-Anwendungsbereitstellung](../service-fabric-deploy-remove-applications)
- [Debuggen einer Service Fabric-Anwendung](../service-fabric-debugging-your-application)
- [Visualisieren des Clusters mit Service Fabric-Explorer](../service-fabric-visualizing-your-cluster)

<!--Image references-->
[addserviceproject]: ./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]: ./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]: ./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
 

<!---HONumber=July15_HO2-->