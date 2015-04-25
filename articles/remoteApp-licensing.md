<properties title="Azure RemoteApp licensing" pageTitle="Lizenzierung in Azure RemoteApp" description="Weitere Informationen über die Lizenzierung von Azure RemoteApp" metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/12/2014" ms.author="elizapo" />

#Wie funktioniert die Lizenzierung in Azure RemoteApp?


Sie haben Ihren Azure RemoteApp-Dienst eingerichtet, die Vorlagen erstellt und sind nun bereit, Apps für Ihre Benutzer zu veröffentlichen. Es gibt allerdings noch einen letzten Punkt, der zu berücksichtigt bleibt: die Lizenzierung. Wie sieht es mit der Lizenzierung für die Apps aus, die Sie über RemoteApp freigeben?

Sie können jede App oder jedes Programm anbieten, für das Sie eine Lizenz haben. 

Im Rahmen Ihres Azure RemoteApp-Abonnements brauchen Sie keine Windows-Lizenzen und/oder Remote Desktop-Client-Zugriffslizenzen (CALs) zu erwerben. Diese sind in den Kosten des Abonnements enthalten.

Wenn Sie das in Azure RemoteApp enthaltene Office 365-Vorlagenimage verwenden möchten, müssen Sie einen *vorhandenen* Office 365 ProPlus-Plan haben. Dasselbe gilt für alle Office 365-Apps, die Sie mithilfe einer benutzerdefinierten Vorlage veröffentlichen. Sie müssen die Apps mit Ihrem eigenen Abonnement aktivieren. Dies gilt sowohl für Test- als auch für kostenpflichtige Abonnements. Wenn Sie das Office 365-Vorlagenimage während des Testzeitraums verwenden möchten *und nicht bereits ein Abonnement haben*, rufen Sie die Office 365-Seite auf, um sich für ein Testabonnement [zu registrieren](https://go.microsoft.com/fwlink/p/?LinkID=403802). Weitere Informationen finden Sie unter [Wie arbeiten RemoteApp und Office zusammen?](http://azure.microsoft.com/de-de/documentation/articles/remoteapp-o365/).

Wenn Sie während des Testzeitraums kein Office 365-Testabonnement wünschen, verwenden Sie das Vorlagenimage von Office 2013 Professional Plus, das im Lieferumfang von RemoteApp enthalten ist. Dieses Vorlagenimage kann nur 30 Tage verwendet und nicht in eine kostenpflichtige Sammlung konvertiert werden. Nach Ablauf des Testzeitraums müssen Sie die Sammlung löschen und neu erstellen, wenn Sie in Zukunft Office 2013 verwenden möchten.  

Das ist einleuchtend, nicht wahr? Sie können jede App veröffentlichen, zu deren Freigabe Sie von Rechts wegen berechtigt sind. Dabei müssen Sie sicherstellen, dass Sie tatsächlich berechtigt sind, die Programme freizugeben.

Bitte beachten Sie, dass Sie in einer Cloud-Sammlung keine Client-Zugriffslizenz (CAL) und keinen Volumenlizenzvertrag verwenden können. Sie *können* jedoch einen Volumenlizenzvertrag verwenden, um Anwendungen (mit Ausnahme von Office) in einer Hybrid-Sammlung zu aktivieren. Sie müssen sie lediglich auf dem Vorlagenimage von den Volumenlizenzmedien installieren. Befolgen Sie die Hinweise des Herstellers der Anwendung zum Installieren von Lizenzen in einer Remotedesktop-Umgebung.

<!--HONumber=35.2-->
