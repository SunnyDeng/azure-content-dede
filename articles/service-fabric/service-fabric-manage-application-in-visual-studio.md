<properties
   pageTitle="Verwalten Ihrer Anwendungen in Visual Studio | Microsoft Azure"
   description="Verwenden Sie Visual Studio zum Erstellen, Entwickeln, Packen, Bereitstellen und Debuggen Ihrer Service Fabric-Anwendungen und -Dienste."
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
   ms.date="02/02/2016"
   ms.author="jesseb"/>

# Verwenden von Visual Studio zum Vereinfachen des Schreibens und Verwaltens Ihrer Service Fabric-Anwendung

Sie können Ihre Azure Service Fabric-Anwendungen und -Dienste in Visual Studio verwalten. Sobald Sie die [Einrichtung Ihrer Entwicklungsumgebung](service-fabric-get-started.md) abgeschlossen haben, können Sie mit Visual Studio Service Fabric-Anwendungen erstellen, Dienste hinzufügen und Anwendungen packen, registrieren und im lokalen Entwicklungscluster bereitstellen.

Um eine Anwendung zu verwalten, klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Anwendungsprojekt.

![Service Fabric-Anwendung mit Rechtsklick auf das Anwendungsprojekt verwalten][manageservicefabric]

## Bereitstellen der Service Fabric-Anwendung

Beim Bereitstellen einer Anwendung erfolgen die folgenden Schritte in einem einfachen Vorgang:

1. Erstellen des Anwendungspakets
2. Hochladen des Anwendungspakets in den Image-Speicher
3. Registrieren des Anwendungstyps
4. Entfernen ausgeführter Anwendungsinstanzen
5. Erstellen einer neuen Anwendungsinstanz

Sie können in Visual Studio auch **F5** drücken, um die Anwendung bereitzustellen und den Debugger an alle Anwendungsinstanzen anzufügen. Sie können mithilfe von **STRG+F5** eine Anwendung ohne Debuggen bereitstellen oder sie in einem lokalen bzw. Remotecluster mit dem Veröffentlichungsprofil veröffentlichen. Weitere Informationen finden Sie unter [Veröffentlichen einer Anwendung in einem Remotecluster mit Visual Studio](service-fabric-publish-app-remote-cluster.md).

### Beibehalten von Daten zwischen Testläufen

Häufig testen Sie Dienste lokal, indem Sie Testdateneingaben hinzufügen, einige Codeblöcke ändern und dann erneut lokal debuggen. Die Service Fabric-Tools von Visual Studio bieten eine nützliche Eigenschaft namens **Preserve Data on Start**, mit der die in der vorherigen Sitzung eingegebenen Daten beibehalten werden, sodass Sie sie erneut verwenden können.

#### So aktivieren Sie die Eigenschaft „Preserve Data on Start“

1. Wählen Sie im Kontextmenü des Anwendungsprojekts **Eigenschaften** aus (oder drücken Sie **F4**).
1. Legen Sie im Fenster **Eigenschaften** die Eigenschaft **Preserve Data on Start** auf **Ja** fest.

	![Festlegen der Eigenschaft „Preserve Data on Start“][preservedata]

Wenn Sie die Anwendung erneut ausführen, behandelt das Bereitstellungsskript die Bereitstellung jetzt als Upgrade und verwendet den nicht überwachten automatischen Modus, um die Anwendung schnell auf eine neuere Version mit angefügter Datumszeichenfolge zu aktualisieren. Beim Upgradevorgang werden alle Daten beibehalten, die Sie in einer vorherigen Debugsitzung eingegeben haben.

![Beispiel der neuen Anwendungsversion mit angefügtem Datum][preservedate]

Die Daten werden mithilfe der Upgradefunktion der Service Fabric-Plattform beibehalten. Weitere Informationen zum Upgrade einer Anwendung finden Sie unter [Service Fabric-Anwendungsupgrade](service-fabric-application-upgrade.md)

## Fügen Sie der Service Fabric-Anwendung einen Dienst hinzu.

Sie können der Anwendung neue Fabric-Dienste hinzufügen, um die Funktionalität der Anwendung zu erweitern. Um sicherzustellen, dass der Dienst in das Anwendungspaket aufgenommen wird, fügen Sie den Dienst über das Menüelement **Neuer Fabric-Dienst** hinzu.

![Neuen Fabric-Dienst zur Anwendung hinzufügen][newservice]

Wählen Sie den Service Fabric-Projekttyp aus, der zur Anwendung hinzugefügt werden soll, und geben Sie einen Namen für den Dienst an. Lesen Sie die Informationen in [Auswählen eines Frameworks für den Dienst](service-fabric-choose-framework.md), um zu entscheiden, welcher Diensttyp verwendet werden soll.

![Projekttyp des Fabric-Diensts auswählen, das zur Anwendung hinzugefügt werden soll][addserviceproject]

Der neue Dienst wird zur Projektmappe und dem vorhandenen Anwendungspaket hinzugefügt. Die Dienstverweise und eine Standarddienstinstanz werden zum Anwendungsmanifest hinzugefügt. Der Dienst wird erstellt und mit der nächsten Bereitstellung der Anwendung gestartet.

![Der neue Dienst wird zum Anwendungsmanifest hinzugefügt][newserviceapplicationmanifest]

## Packen der Service Fabric-Anwendung

Um die Anwendung und die zugehörigen Dienste in einem Cluster bereitzustellen, müssen Sie ein Anwendungspaket erstellen. Das Paket ordnet das Anwendungsmanifest, das bzw. die Dienstmanifeste und andere erforderliche Dateien in einem bestimmten Layout an. Visual Studio richtet das Paket im Projektordner der Anwendung im Verzeichnis "Pkg" ein und verwaltet es. Klicken Sie zum Erstellen oder Aktualisieren des Anwendungspakets im Kontextmenü der **Anwendung** auf **Packen**. Dies ist möglicherweise erforderlich, falls Sie die Anwendung mithilfe benutzerdefinierter PowerShell-Skripts bereitstellen möchten.

## Entfernen einer Anwendung

Sie können die Bereitstellung eines Anwendungstyps in Ihrem lokalen Cluster mit dem Service Fabric-Explorer aufheben. Sie können auf den Cluster-Explorer über den HTTP-Gatewayendpunkt des Clusters (in der Regel 19080 oder 19007) zugreifen, z. B. http://localhost:19080/Explorer. Hierbei werden die oben beschriebenen Bereitstellungsschritte rückgängig gemacht:

1. Entfernen ausgeführter Anwendungsinstanzen
2. Aufheben der Registrierung des Anwendungstyps

![Entfernen einer Anwendung](./media/service-fabric-manage-application-in-visual-studio/removeapplication.png)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte

- [Service Fabric-Anwendungsmodell](service-fabric-application-model.md)
- [Service Fabric-Anwendungsbereitstellung](service-fabric-deploy-remove-applications.md)
- [Verwalten von Anwendungsparametern für mehrere Umgebungen](service-fabric-manage-multiple-environment-app-configuration.md)
- [Debuggen einer Service Fabric-Anwendung](service-fabric-debugging-your-application.md)
- [Visualisieren des Clusters mit Service Fabric-Explorer](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]: ./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]: ./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]: ./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[preservedata]: ./media/service-fabric-manage-application-in-visual-studio/preservedata.png
[preservedate]: ./media/service-fabric-manage-application-in-visual-studio/preservedate.png

<!---HONumber=AcomDC_0204_2016-->