<properties 
   pageTitle="Behandeln von Problemen mit Operational Insights"
   description="Erfahren Sie mehr über das Behandeln von Problemen mit Operational Insights"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/20/2015"
   ms.author="banders" />

#Behandeln von Problemen mit Operational Insights
Die Informationen in den folgenden Abschnitten unterstützen Sie bei der Problembehandlung. Wenn das vorliegende Problem nicht in diesem Artikel enthalten ist, können Sie den [Operational Insights-Teamblog](http://blogs.technet.com/b/momteam/archive/2014/05/29/advisor-error-3000-unable-to-register-to-the-advisor-service-amp-onboarding-troubleshooting-steps.aspx) zu Rate ziehen.

## Diagnostizieren von Verbindungsproblemen für Operational Insights

Da Microsoft Azure Operational Insights auf Daten beruht, die in die Cloud und aus der Cloud verschoben werden, können Verbindungsprobleme Vorgänge beeinträchtigen. Anhand der folgenden Informationen können Sie Ihre Verbindungsprobleme verstehen und lösen.



<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>Fehlermeldung</b></td>
		<td><b>Mögliche Ursachen</b></td>
    </tr>
    <tr align="left" valign="top">
		<td>Die Internetverbindung wurde überprüft, die Verbindung mit dem Operational Insights-Dienst konnte jedoch nicht hergestellt werden. Bitte versuchen Sie es später noch einmal.|</td>
		<td>Der Operational Insights-Dienst wird gewartet. Warten Sie, bis die Operational Insights-Wartung durchgeführt wurde.<p>Das Netzwerk hat Operational Insights blockiert. Wenden Sie sich an Ihren Netzwerkadministrator, und fordern Sie Zugriff auf Operational Insights an,  oder verwenden Sie einen anderen Server als Gateway.</td>
    </tr>
    <tr align="left" valign="top">
		<td>Internetverbindung konnte nicht hergestellt werden. Überprüfen Sie Ihre Proxyeinstellungen.</td>
		<td>Dieser Server ist nicht mit dem Internet verbunden. Überprüfen Sie den Status der Internetkonnektivität, und verbinden Sie den Server mit dem Internet.<p>Die Proxyeinstellung ist nicht korrekt. Informationen zum Einrichten oder Ändern Ihrer Proxyeinstellungen finden Sie unter <A HREF="operational-insights-proxy-filewall.md">Konfigurieren des Proxys und der Firewalleinstellungen</A>.<p>Der Proxyserver erfordert eine Authentifizierung. Informationen zum Konfigurieren von Operations Manager für die Verwendung eines Proxyservers finden Sie unter <A HREF="operational-insights-proxy-filewall.md">Konfigurieren des Proxys und der Firewalleinstellungen</A>.</td>
    </tr>
    </tbody>
    </table>

## Problembehandlung bei der SQL Server-Ermittlung

Wenn Sie Microsoft SQL Server 2008 R2 ausführen und trotz Bereitstellen des Operations Manager-Agents keine Warnungen für diesen Server angezeigt werden, liegt möglicherweise ein Ermittlungsproblem vor.

Um sicherzustellen, dass dies die Fehlerursache ist, überprüfen Sie die folgenden zwei Probleme:

- Im Operations Manager-Ereignisprotokoll wird die Ereignis-ID 4001 aufgeführt. Dieses Ereignis weist darauf hin, dass eine ungültige Klasse vorliegt.

- Wenn Sie im SQL Server-Konfigurations-Manager die SQL Server-Dienste anzeigen, sehen Sie die Fehlermeldung "Fehler bei Remoteprozeduraufruf. [0x0800706be]"

Wenn beide Probleme vorliegen, müssen Sie SQL Server 2008 R2 Service Pack 2 installieren. Informationen zum Herunterladen dieses Service Packs finden Sie unter [SQL Server 2008 R2 Service Pack 2](http://go.microsoft.com/fwlink/?LinkId=271310) im Microsoft Download Center.

Nach der Installation des Service Packs sollten innerhalb von 24 Stunden Operational Insights-Daten für den Server angezeigt werden.


<!--HONumber=52-->