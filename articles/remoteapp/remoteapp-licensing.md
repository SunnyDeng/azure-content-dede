<properties 
    pageTitle="Lizenzierung in Azure RemoteApp" 
    description="Erfahren Sie, wie die Lizenzierung in Azure RemoteApp funktioniert." 
    services="remoteapp" 
    solutions="" documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/12/2015" 
    ms.author="elizapo" />


# Wie funktioniert die Lizenzierung in Azure RemoteApp?


Sie haben Ihren Azure RemoteApp-Dienst eingerichtet, die Vorlagen erstellt und sind nun bereit, Apps für Ihre Benutzer zu veröffentlichen. Es gibt allerdings noch einen letzten Punkt, der zu berücksichtigt bleibt: die Lizenzierung. Wie genau funktioniert die Lizenzierung für RemoteApp und die Apps, die Sie über RemoteApp freigeben?

RemoteApp erfordert keine Windows-Lizenzen oder Remote Desktop-Clientzugriffslizenzen. Ihr Abonnement gilt auch für RemoteApp selbst. (Details finden Sie in den [Preisplänen](../../../pricing/details/remoteapp/).)

Wenn Sie eines der in Ihrem Abonnement enthaltenen Images verwenden, können Sie beliebige der Apps freigeben, die Sie auf diesem Image installiert haben. Es ist in diesem Fall keine separate Lizenz erforderlich. Wenn Sie beispielsweise das Windows Server 2012 R2-Vorlagenimage zum Erstellen Ihrer Sammlung verwenden, können Sie System Center Endpoint Protection für Ihre Benutzer freigeben. Ausgenommen von dieser Regel sind nur Office 365 ProPlus, für das ein separates Abonnement erforderlich ist, und Office 2013, das in einer Produktionssammlung nicht freigegeben werden kann.

Wenn Sie das in RemoteApp enthaltene Office 365-Vorlagenimage verwenden möchten, benötigen Sie einen *vorhandenen* Office 365 ProPlus-Plan. Dasselbe gilt für alle Office 365-Apps, die Sie mithilfe einer benutzerdefinierten Vorlage veröffentlichen. Sie müssen die Apps mit Ihrem eigenen Abonnement aktivieren. Dies gilt sowohl für Test- als auch für kostenpflichtige Abonnements. Wenn Sie das Office 365-Vorlagenimage während des Testzeitraums verwenden möchten *und nicht bereits ein Abonnement haben*, rufen Sie die Office 365-Seite auf, um sich für ein Testabonnement zu [registrieren](https://go.microsoft.com/fwlink/p/?LinkID=403802). Weitere Informationen finden Sie unter [Wie arbeiten RemoteApp und Office zusammen?](remoteapp-o365.md).

Wenn Sie während des Testzeitraums kein Office 365-Testabonnement wünschen, verwenden Sie das Vorlagenimage von Office 2013 Professional Plus, das im Lieferumfang von RemoteApp enthalten ist. Dieses Vorlagenimage kann nur 30 Tage verwendet und nicht in eine kostenpflichtige Sammlung konvertiert werden.

Für andere Apps müssen Sie sicherstellen, dass Sie die Lizenz zum Freigeben der App verfügen.

Das ist einleuchtend, nicht wahr? Sie können jede App veröffentlichen, zu deren Freigabe Sie von Rechts wegen berechtigt sind. Dabei müssen Sie sicherstellen, dass Sie tatsächlich berechtigt sind, die Programme freizugeben.

Bitte beachten Sie, dass Sie in einer Cloudsammlung keine Client-Zugriffslizenz (CAL) und keinen Volumenlizenzvertrag verwenden können. Sie *können* jedoch einen Volumenlizenzvertrag verwenden, um Anwendungen (mit Ausnahme von Office) in einer Hybridsammlung zu aktivieren. Sie müssen sie lediglich auf dem Vorlagenimage von den Volumenlizenzmedien installieren. Befolgen Sie die Hinweise des Herstellers der Anwendung zum Installieren von Lizenzen in einer Remotedesktop-Umgebung.
 

<!---HONumber=August15_HO7-->