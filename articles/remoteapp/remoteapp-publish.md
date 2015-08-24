<properties
    pageTitle="Veröffentlichen einer App in Azure RemoteApp"
    description="Erfahren Sie, wie Sie Anwendungen und Ressourcen in Azure RemoteApp veröffentlichen."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/12/2015"
    ms.author="elizapo" />


# Gewusst wie: Veröffentlichen einer App in RemoteApp

Nach dem Erstellen der RemoteApp-Sammlung müssen Sie die Apps oder Ressourcen, die Sie für Ihre Benutzer zur Verfügung stellen möchten, veröffentlichen. Die Vorlagenimages, die mit Ihrem Abonnement bereitgestellt wurden, enthalten nur wenige Apps, die standardmäßig veröffentlicht werden. Um die anderen Apps freizugeben, müssen Sie diese zuerst veröffentlichen.

> [AZURE.NOTE]Müssen Sie eine App aktualisieren? Sie müssen zunächst [das Image aktualisieren](remoteapp-update.md).

Klicken Sie im Portal auf der Registerkarte **Veröffentlichung** auf **Veröffentlichen**. Sie können entweder eine App aus dem **Startmenü** des Vorlagenimages hinzufügen oder den Pfad angeben, in dem die App auf dem Vorlagenimage installiert ist. Wenn Sie die App aus dem Startmenü hinzufügen möchten, wählen Sie die zu veröffentlichende App in der Liste aus. Wenn Sie den Pfad zur App bereitstellen möchten, geben Sie einen Namen für die App und den Pfad zur App an. Verwenden Sie Variablen im Pfad, z. B. "%systemdrive%" anstelle von "c:".

> [AZURE.NOTE]Wenn Sie die App aus dem Startmenü hinzufügen möchten, müssen Sie diese *App im Vorlagenimage dem Startmenü hinzugefügt haben*. Andernfalls wird in RemoteApp nur angezeigt, was sich *im* Startmenü befindet, was zu Verwechslungen führen kann. Wenn Sie beim Erstellen der Vorlage vergessen haben, die App dem **Startmenü** hinzufügen, fügen Sie der App den Pfad hinzu. (Oder erstellen Sie Ihr Vorlagenimage neu. Dies bedeutet allerdings einen etwas höheren Arbeitsaufwand.)
 

<!---HONumber=August15_HO7-->