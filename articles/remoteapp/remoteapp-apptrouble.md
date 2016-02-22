<properties 
    pageTitle="Problembehandlung bei Azure RemoteApp – Fehler beim Starten und Verbinden der Anwendung | Microsoft Azure" 
    description="Erfahren Sie, wie Sie Problemen beim Starten von Anwendungen und Herstellen einer Verbindung mit Anwendungen in Azure RemoteApp beheben." 
    services="remoteapp" 
	documentationCenter="" 
    authors="ericorman" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/02/2016" 
    ms.author="elizapo" />



#Problembehandlung bei Azure RemoteApp – Fehler beim Starten und Verbinden der Anwendung 

Anwendungen, die in Azure RemoteApp gehostet werden, können aus unterschiedlichen Gründe nicht gestartet werden. In diesem Artikel werden die verschiedenen Gründe und Fehlermeldungen beschrieben, die Benutzern möglicherweise ausgegeben werden, wenn sie versuchen, Anwendungen zu starten. Er befasst sich auch mit Verbindungsfehlern. (In diesem Artikel werden jedoch keine Probleme mit der Anmeldung beim Azure RemoteApp-Client beschrieben.)

Im Folgenden finden Sie Informationen zu Fehlermeldungen zu Fehlern beim Starten oder Verbinden von Apps.

##Die Einrichtung erfolgt jetzt … Versuchen Sie es in 10 Minuten noch einmal.

Dieser Fehler weist darauf hin, dass Azure RemoteApp eine zentrale Hochskalierung durchführt, um die erforderliche Kapazität für die Benutzer bereitzustellen. Im Hintergrund werden weitere Azure RemoteApp-VM-Instanzen erstellt, um die Kapazitätsanforderungen Ihrer Benutzer zu erfüllen. Dies dauert in der Regel ca. fünf Minuten, es kann aber auch bis zu 10 Minuten in Anspruch nehmen. In manchen Fällen erfolgt diese Skalierung nicht schnell genug, und die Ressourcen werden sofort benötigt. Ein Beispiel hierfür wäre ein 9-Uhr-Szenario, bei dem viele Benutzer Ihre App gleichzeitig in Azure RemoteApp verwenden müssen. Wenn dies auf Sie zutrifft, können wir auf dem Back-End den **Kapazitätsmodus** aktivieren. Dazu öffnen Sie ein Azure-Support-Ticket oder senden eine E-Mail an [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com). Geben Sie unbedingt Ihre Abonnement-ID in der Anforderung an.

![Die Einrichtung erfolgt jetzt …](./media/remoteapp-apptrouble/ra-apptrouble1.png)

## Es konnte nicht automatisch eine erneute Verbindung mit Ihrer Anwendung hergestellt werden. Starten Sie Ihre Anwendung neu.  

Diese Fehlermeldung wird häufig angezeigt, wenn Sie Azure RemoteApp verwenden, dann Ihren PC länger als vier Stunden in den Standbymodus versetzen und anschließend wieder aktivieren. Der Azure RemoteApp-Client versucht dann automatisch, erneut eine Verbindung herzustellen, wobei das Timeout überschritten wird. Weisen Sie die Benutzer an, zurück zur Anwendung zu wechseln und zu versuchen, sie aus dem Azure RemoteApp-Client zu öffnen.

![Es konnte nicht automatisch eine erneute Verbindung mit Ihrer Anwendung hergestellt werden](./media/remoteapp-apptrouble/ra-apptrouble2.png)

## Probleme mit dem temporären Profil 

Dieser Fehler tritt auf, wenn Ihr Benutzerprofil (Benutzerprofil-Datenträger) nicht bereitgestellt wurde, und der Benutzer ein temporäres Profil erhält. Administratoren sollten zur Auflistung im Azure-Portal navigieren und dann auf der Registerkarte **Sitzungen** versuchen, den Benutzer **abzumelden**. Damit wird die vollständige Abmeldung der Benutzersitzung erzwungen, sodass der Benutzer die App danach neu starten muss. Wenn dies fehlschlägt, wenden Sie sich an den Azure-Support, oder senden Sie eine E-Mail an [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com).

## Azure RemoteApp funktioniert nicht mehr

Diese Fehlermeldung bedeutet, dass beim Azure RemoteApp-Client ein Problem aufgetreten ist und er neu gestartet werden muss. Weisen Sie die Benutzer an, den Client zu schließen: Wählen Sie **Programm schließen** aus, und starten Sie den Azure RemoteApp-Client erneut. Wenn das Problem weiterhin auftritt, öffnen Sie ein Azure-Support-Ticket oder senden eine E-Mail an [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com).

![Azure RemoteApp funktioniert nicht mehr](./media/remoteapp-apptrouble/ra-apptrouble3.png)

## Fehler beim Zugriff der Remotedesktopverbindung auf die Ressource. Versuchen Sie noch einmal, eine Verbindung herzustellen, oder wenden Sie sich an den Netzwerkadministrator.

Dies ist eine generische Fehlermeldung. Wenden Sie sich an den Azure-Support, oder senden Sie eine E-Mail an [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com), damit wir das Problem untersuchen können.

![Generische Azure RemoteApp-Meldung](./media/remoteapp-apptrouble/ra-apptrouble4.png)

<!---HONumber=AcomDC_0211_2016-->