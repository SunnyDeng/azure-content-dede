<properties title="Publish an app in RemoteApp" pageTitle="Veröffentlichen einer App in RemoteApp" description="Informationen zum Veröffentlichen von Anwendungen und Ressourcen in RemoteApp" metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/11/2014" ms.author="elizapo" />

#Gewusst wie: Veröffentlichen einer App in RemoteApp

Nach dem Erstellen der Cloud- oder Hybrid-Sammlung müssen Sie die Apps oder Ressourcen, die Sie für Ihre Benutzer zur Verfügung stellen möchten, veröffentlichen. Die Vorlagenimages, die mit Ihrem Abonnement bereitgestellt wurden, enthalten nur wenige Apps, die standardmäßig veröffentlicht werden. Um die anderen Apps freizugeben, müssen Sie diese zuerst veröffentlichen.
 
Klicken Sie im Portal auf der Registerkarte **Veröffentlichung** auf **Veröffentlichen**. Sie können entweder eine App aus dem Startmenü des Vorlagenimages hinzufügen oder den Pfad angeben, in dem die App auf dem Vorlagenimage installiert ist. Wenn Sie die App aus dem Startmenü hinzufügen möchten, wählen Sie die zu veröffentlichende App in der Liste aus. Wenn Sie den Pfad zur App bereitstellen möchten, geben Sie einen Namen für die App und den Pfad zur App an. Verwenden Sie Variablen beim Pfad, z. B. "%systemdrive%" anstelle von "c:".

**Hinweis:** Wenn Sie Ihre App aus dem Startmenü hinzufügen möchten, müssen Sie *diese Anwendung dem Startmenü des Vorlagenimages hinzugefügt haben*. Sonst wird RemoteApp nur angezeigt, was im Startmenü *ist*. Das kann zu Irritationen führen. Wenn Sie beim Erstellen der Vorlage vergessen haben, die App zum Startmenü hinzuzufügen, sollten Sie den Pfad zur App angeben. (Alternativ können Sie das Vorlagenimage neu erstellen, was jedoch arbeitsintensiver ist.)

<!--HONumber=35.2-->
