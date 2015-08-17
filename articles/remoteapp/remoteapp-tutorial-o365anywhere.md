<properties
   pageTitle="Bereitstellen der gleichen Office 365-Umgebung auf jedem Gerät mit Azure RemoteApp"
   description="Erfahren Sie, wie Sie mithilfe von Azure RemoteApp jede Office 365-App für Benutzer freigeben können."
   services="remoteapp"
   documentationCenter=""
   authors="guscatalano"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="07/07/2015"
   ms.author="guscatal;elizapo"/>


# Holen Sie sich die Office 365-Lösung für jedes Gerät

In diesem Artikel wird behandelt, wie Office 365 auf jedem Gerät in Ihrem Unternehmen bereitgestellt werden kann. Ihre Benutzer erhalten die gleichen Funktionen und die gleiche Benutzeroberfläche für Android, Apple und Windows.

Dies wird mithilfe von Azure RemoteApp erreicht, indem Office 365 auf skalierbaren virtuellen Computern in Azure gehostet wird, mit denen Benutzer eine Verbindung herstellen können. Diese Gruppe virtueller Computer nennen wir „Cloud-Sammlung“.

## Erstellen einer Cloud-Sammlung

Navigieren Sie nach der Erstellung eines Azure-Kontos zunächst zu **RemoteApp**, indem Sie im linken Bereich auf den entsprechenden Link klicken. ![Anzeigen von Azure RemoteApp im Azure-Portal](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/1-menu.png)

Klicken Sie anschließend am unteren Rand auf **Neu**, und führen Sie eine Schnellerfassung für eine Sammlung durch. Geben Sie einen Namen, die Region, das Abonnement, den Plan und das Bild "Office Professional 2013" ein, das wir bereitstellen. ![Dialogfeld "Erstellen"](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/2-quickcreate.PNG)

Sobald Sie alles eingetragen haben, sollte der Prozess der Sammlungserstellung beginnen. Dies kann bis zu einer Stunde dauern.

![Warten](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/3-waiting.PNG)

Sobald der Vorgang abgeschlossen ist, sollte das Ergebnis wie folgt aussehen. Durch Klicken auf **Veröffentlichen** sehen Sie, dass die meisten Office-Anwendungen bereits veröffentlicht wurden. ![Sammlung erstellt](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/4-done.PNG)

![Veröffentlichte Apps](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/5-publish.PNG)

An diesem Punkt können Sie auch weitere Benutzer mit Zugriff auf diese Sammlung hinzufügen. Klicken Sie hierzu auf **Benutzerzugriff**. ![Konfigurieren des Benutzerzugriffs](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/6-user.PNG)

Versuchen Sie jetzt, eine Verbindung mit Office 365 herzustellen.

## Herstellen einer Verbindung mit Office 365

Rufen Sie [https://www.remoteapp.windowsazure.com/](https://www.remoteapp.windowsazure.com/) auf, und klicken Sie auf **Client installieren**, um den Azure RemoteApp-Client auf dem verwendeten Gerät zu installieren. Die folgenden Screenshots beziehen sich auf Windows.

Beim Start der Anwendung werden Sie aufgefordert, sich mit Ihrem Microsoft-Konto (ehemals Live-ID) anzumelden. Verwenden Sie hierbei vorerst die gleichen Anmeldedaten wie für Ihr Azure-Konto. Nach erfolgreicher Anmeldung werden Sie auf neue Einladungen aufmerksam gemacht. Klicken Sie auf die Benachrichtigung, um eine Liste wie in der folgenden Abbildung anzuzeigen. Nehmen Sie die Einladung für die E-Mail-Adresse des Azure-Kontobesitzers an.

![Neue Einladung](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/7-araclient.PNG)

So sieht es aus, wenn es neue Einladungen gibt.

![Akzeptieren einer Anwendung](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/8-invitation.PNG)

Nachdem Sie die Einladung angenommen haben, werden alle Office-Apps im Azure RemoteApp-Client angezeigt.

![Liste der Apps](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/9-work.PNG)

Klicken Sie auf die Anwendungen, um sie auf dem virtuellen Azure-Computer zu starten. Das war's! Viel Spaß!

![wird gestartet](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/10-arastart.PNG)

![PowerPoint](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/11-pp.PNG)
 

<!---HONumber=August15_HO6-->