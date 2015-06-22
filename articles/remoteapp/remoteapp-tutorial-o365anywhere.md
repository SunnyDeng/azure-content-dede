<properties
   pageTitle="Holen Sie sich die Office 365-Lösung für jedes Gerät"
   description="Erfahren Sie, wie Sie mithilfe von RemoteApp jede Office 365-App für Benutzer freigeben können."
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
   ms.date="04/14/2015"
   ms.author="guscatal"/>


Holen Sie sich die Office 365-Lösung für jedes Gerät
===================


In diesem Artikel wird behandelt, wie Office 365 auf jedem Gerät in Ihrem Unternehmen bereitgestellt werden kann. Ihre Benutzer erhalten die gleichen Funktionen und die gleiche Benutzeroberfläche für Android, Apple und Windows.

Dies wird mithilfe von Azure RemoteApp erreicht, indem Office 365 auf skalierbaren virtuellen Computern in Azure gehostet wird, mit denen Benutzer eine Verbindung herstellen können. Diesen Satz virtueller Computer nennen wir „Cloud-Sammlung“.

----------


Erstellen einer Cloud-Sammlung
-------------
Nachdem Sie ein Azure-Konto erstellt haben, navigieren Sie zunächst zur "RemoteApp" durch Klicken auf den Link auf der linken Seite. ![Anzeigen von Azure RemoteApp im Azure-Portal](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/1-menu.png)

Fahren Sie durch Klicken auf "Neu" unten und "Schnellerfassung" einer Sammlung fort. Geben Sie einen Namen, die Region, das Abonnement, den Plan und das Bild "Office Professional 2013" ein, das wir bereitstellen. ![Dialogfeld "Erstellen"](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/2-quickcreate.PNG)

Sobald Sie alles eingetragen haben, sollte der Prozess der Sammlungserstellung beginnen. Dies kann bis zu einer Stunde dauern.

![Warten](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/3-waiting.PNG)

Sobald der Vorgang abgeschlossen ist, sollte das Ergebnis wie folgt aussehen. Durch Klicken auf "Veröffentlichen" sehen Sie, dass die meisten Office-Anwendungen bereits veröffentlicht wurden. ![Sammlung erstellt](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/4-done.PNG)

![Veröffentlichte Apps](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/5-publish.PNG)

An diesem Punkt können Sie auch weitere Benutzer hinzufügen, die Zugriff auf diese Auflistung haben. Klicken Sie hierfür auf "Benutzerzugriff" ![Konfigurieren des Benutzerzugriffs](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/6-user.PNG)

Versuchen Sie jetzt, eine Verbindung mit Office 365 herzustellen.

Mit Office 365 verbinden
-------------
Wechseln Sie zu https://www.remoteapp.windowsazure.com/ und klicken Sie auf "Client installieren", um den Azure RemoteApp-Client auf Ihrem Gerät zu installieren. Die folgenden Screenshots sind für Windows.

Sobald die Anwendung gestartet wird, müssen Sie sich mit Ihrer Live ID anmelden. Verwenden Sie vorerst die gleichen Anmeldedaten wie für Ihr Azure-Konto. Wenn Sie angemeldet sind, sollten Sie eine Benachrichtigung über neue Einladungen sehen. Klicken Sie darauf, sodass eine Liste wie die unten angezeigte erscheint, und nehmen Sie die Einladung an, die der Kontobesitzer-E-Mail Ihres Azure entspricht.

![Neue Einladung](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/7-araclient.PNG)

So sieht es aus, wenn es neue Einladungen gibt.

![Akzeptieren einer Anwendung](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/8-invitation.PNG)

Nachdem Sie die Einladung angenommen haben, sollten alle Office-Apps im Azure RemoteApp-Client angezeigt werden.

![Liste der Apps](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/9-work.PNG)

Starten Sie die Anwendungen auf dem virtuellen Azure-Computer, indem Sie darauf klicken. Sie sind nun fertig. Viel Spaß!

![wird gestartet](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/10-arastart.PNG)

![PowerPoint](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/11-pp.PNG)

<!--HONumber=52-->
 