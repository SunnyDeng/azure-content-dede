<properties 
	pageTitle="In verschiedenen Status in BizTalk Services zulässige Aufgaben | Microsoft Azure"
	description="Zulässige Aktionen/Vorgänge in verschiedenen MABS-Status: Beenden, Starten, Neustarten, Anhalten, Wiederaufnehmen, Löschen, Skalieren, Aktualisieren der Konfiguration und Sichern"
	services="biztalk-services"
	documentationCenter=""
	authors="MandiOhlinger"
	manager="dwrede"
	editor="cgronlun"/>

<tags 
	ms.service="biztalk-services"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/26/2015"
	ms.author="mandia"/>



# BizTalk Services: Dienststatusübersicht
Je nach aktuellem Status des BizTalk-Diensts stehen Vorgänge bereit, die Sie in eben diesem BizTalk-Dienst entweder ausführen können oder nicht ausführen können.

Sie stellen beispielsweise einen neuen BizTalk-Dienst im Azure-Verwaltungsportal bereit. Wenn dieser Vorgang erfolgreich abgeschlossen ist, befindet sich der BizTalk-Dienst im aktiven Status. Im aktiven Status können Sie den BizTalk-Dienst beenden. Wenn das Beenden erfolgreich durchgeführt wird, geht der BizTalk-Dienst in den Status "Beendet" über. Wenn das Beenden fehlschlägt, geht der BizTalk-Dienst in den Status "StopFailed" über. Im Status "StopFailed" können Sie den BizTalk-Dienst neu starten. Wenn Sie einen nicht zulässigen Vorgang auszuführen versuchen, wie etwa die Wiederaufnahme des BizTalk-Diensts, tritt der folgende Fehler auf:

**Vorgang nicht zulässig**

Informationen zum Bereitstellen eines BizTalk Service finden Sie unter [BizTalk Services: Bereitstellen mit dem Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/p/?LinkID=302280).

In den folgenden Tabellen sind diejenigen Vorgänge oder Aktionen aufgelistet, die ausgeführt werden können, wenn sich der BizTalk Service in einem spezifischen Status befindet. Ein Häkchen bedeutet dabei, dass der Vorgang zulässig ist, wenn der betreffende Status zutrifft. Ein leerer Eintrag bedeutet dagegen, dass der Vorgang nicht ausgeführt werden kann, wenn der betreffende Status zutrifft.

## Vorgänge zum Starten, Beenden, Neustarten, Anhalten, Wiederaufnehmen und Löschen
<table border="1">
<tr>
        <th colspan="15">Vorgang oder Aktion</th>
</tr>

<tr>
        <th rowspan="18">BizTalk Service-Status</th>
</tr>
<tr bgcolor="FAF9F9">
        <th> </th>
        <th>Starten</th>
        <th>Beenden</th>
        <th>Neu starten</th>
        <th>Anhalten</th>
        <th>Wiederaufnehmen</th>
        <th>Löschen</th>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Aktiv</b></td>
<td> </td>
<td><center>x</center></td>
<td><center>x</center></td>
<td><center>x</center></td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Deaktiviert</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Angehalten</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>x</center></td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Beendet</b></td>
<td><center>x</center></td>
<td> </td>
<td><center>x</center></td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Fehler beim Dienstupdate</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>DisableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>EnableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>StartFailed<br/>
StopFailed<br/>
RestartFailed</b></td>
<td><center>x</center></td>
<td><center>x</center></td>
<td><center>x</center></td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>SuspendedFailed<br/>
ResumeFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td><center>x</center></td>
<td><center>x</center></td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>CreatedFailed<br/>
RestoreFailed<br/></b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ConfigUpdateFailed</b></td>
<td> </td>
<td> </td>
<td><center>x</center></td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ScaleFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
</table>
<br/>

## Vorgänge zum Skalieren, Aktualisieren der Konfiguration und Sichern
<table border="1">
<tr>
        <th colspan="15">Vorgang oder Aktion</th>
</tr>

<tr>
        <th rowspan="18">BizTalk Service-Status</th>
</tr>
<tr bgcolor="FAF9F9">
        <th> </th>
        <th>Skalieren</th>
        <th>Konfiguration aktualisieren</th>
        <th>Sicherung</th>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Aktiv</b></td>
<td><center>x</center></td>
<td><center>x</center></td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Deaktiviert</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Angehalten</b></td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Beendet</b></td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Fehler beim Dienstupdate</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>DisableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>EnableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>StartFailed<br/>
StopFailed<br/>
RestartFailed</b></td>
<td> </td>
<td><center>x</center></td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>SuspendedFailed<br/>
ResumeFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>CreatedFailed<br/>
RestoreFailed<br/></b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ConfigUpdateFailed</b></td>
<td> </td>
<td><center>x</center></td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ScaleFailed</b></td>
<td><center>x</center></td>
<td> </td>
<td> </td>
</tr>
</table>

## Weitere Informationen
- [BizTalk Services: Erstellen eines BizTalk Service im Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [BizTalk Services: Registerkarten "Dashboard", "Überwachen" und "Skalieren"](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [BizTalk Services: Editionsübersicht](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [BizTalk Services: Sichern und Wiederherstellen](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [BizTalk Services: Drosselung](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
- [BizTalk Services: Name und Schlüssel des Ausstellers](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
- [Wie verwende ich das Azure BizTalk Services SDK?](http://go.microsoft.com/fwlink/p/?LinkID=302335)


 

<!---HONumber=August15_HO9-->