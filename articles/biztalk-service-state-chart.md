<properties linkid="manage-services-biztalk-state-chart" urlDisplayName="BizTalk Services: Service state chart" pageTitle="BizTalk Services: Service state chart | Azure" metaKeywords="" description="" metaCanonical="" services="biztalk-services" documentationCenter="" title="BizTalk Services: Service state chart" authors="mandia" solutions="" manager="paulettm" editor="cgronlun" />

BizTalk Services: Dienststatusübersicht
=======================================

Je nach aktuellem Status des BizTalk-Diensts stehen Vorgänge bereit, die Sie in eben diesem BizTalk-Dienst entweder ausführen können oder nicht ausführen können.

Sie stellen beispielsweise einen neuen BizTalk-Dienst im Azure-Verwaltungsportal bereit. Wenn dieser Vorgang erfolgreich abgeschlossen ist, befindet sich der BizTalk-Dienst im aktiven Status. Im aktiven Status können Sie den BizTalk-Dienst beenden. Wenn das Beenden erfolgreich durchgeführt wird, geht der BizTalk-Dienst in den Status "Beendet" über. Wenn das Beenden fehlschlägt, geht der BizTalk-Dienst in den Status "StopFailed" über. Im Status "StopFailed" können Sie den BizTalk-Dienst neu starten. Wenn Sie einen nicht zulässigen Vorgang auszuführen versuchen, wie etwa die Wiederaufnahme des BizTalk-Diensts, tritt der folgende Fehler auf:

**Vorgang nicht zulässig**

Informationen zum Bereitstellen eines BizTalk-Diensts finden Sie unter [BizTalk Services: Bereitstellen mit dem Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/p/?LinkID=302280).

Die folgenden Tabellen listen jene Vorgänge auf, die ausgeführt werden können, wenn sich der BizTalk-Dienst in einem spezifischen Status befindet. Ein Kreuz bedeutet dabei, dass der Vorgang ausgeführt werden kann, wenn der betreffende Status zutrifft. Ein leerer Eintrag bedeutet dagegen, dass der Vorgang nicht ausgeführt werden kann, wenn der betreffende Status zutrifft.

#### Start-, Stopp-, Neustart-, Unterbrechungs-, Wiederaufnahme- und Lösch-Vorgänge

<table data-morhtml="true" border="1">
<tr data-morhtml="true">
        <th data-morhtml="true" colspan="15">Vorgang</th>
</tr>

<tr data-morhtml="true">
        <th data-morhtml="true" rowspan="18">BizTalk Service-Status</th>
</tr>
<tr data-morhtml="true" bgcolor="FAF9F9">
        <th data-morhtml="true"> </th>
        <th data-morhtml="true">Starten</th>
        <th data-morhtml="true">Beenden</th>
        <th data-morhtml="true">Neu starten</th>
        <th data-morhtml="true">Unterbrechen</th>
        <th data-morhtml="true">Wiederaufnehmen</th>
        <th data-morhtml="true">L&ouml;schen</th>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">Aktiv</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">Deaktiviert</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">Unterbrochen</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">Beendet</b></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">Dienst-Update fehlgeschlagen</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">DisableFailed</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">EnableFailed</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">StartFailed<br data-morhtml="true" /> StopFailed<br data-morhtml="true" /> RestartFailed</b></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">SuspendedFailed<br data-morhtml="true" /> ResumeFailed</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">CreatedFailed<br data-morhtml="true" /> RestoreFailed<br data-morhtml="true" /></b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">ConfigUpdateFailed</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">ScaleFailed</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
</table>

#### Skalierungs-, Konfigurations-Update- und Sicherungs-Vorgänge

<table data-morhtml="true" border="1">
<tr data-morhtml="true">
        <th data-morhtml="true" colspan="15">Vorgang</th>
</tr>

<tr data-morhtml="true">
        <th data-morhtml="true" rowspan="18">BizTalk Service-Status</th>
</tr>
<tr data-morhtml="true" bgcolor="FAF9F9">
        <th data-morhtml="true"> </th>
        <th data-morhtml="true">Skalieren</th>
        <th data-morhtml="true">Konfiguration aktualisieren</th>
        <th data-morhtml="true">Sicherung</th>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">Aktiv</b></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">Deaktiviert</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">Unterbrochen</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">Beendet</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">Dienst-Update fehlgeschlagen</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">DisableFailed</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">EnableFailed</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">StartFailed<br data-morhtml="true" /> StopFailed<br data-morhtml="true" /> RestartFailed</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"> </td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">SuspendedFailed<br data-morhtml="true" /> ResumeFailed</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">CreatedFailed<br data-morhtml="true" /> RestoreFailed<br data-morhtml="true" /></b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">ConfigUpdateFailed</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"> </td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">ScaleFailed</b></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
</tr>
</table>


Weitere Informationen
---------------------

-   [BizTalk Services: Bereitstellen mit dem Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/p/?LinkID=302280)
-   [BizTalk Services: Registerkarten "Dashboard", "Überwachen" und "Skalieren"](http://go.microsoft.com/fwlink/p/?LinkID=302281)
-   [BizTalk Services: Übersicht über Developer, Basic, Standard und Premium Edition](http://go.microsoft.com/fwlink/p/?LinkID=302279)
-   [BizTalk Services: Sichern und Wiederherstellen](http://go.microsoft.com/fwlink/p/?LinkID=329873)
-   [BizTalk Services: Drosselung](http://go.microsoft.com/fwlink/p/?LinkID=302282)
-   [BizTalk Services: Name und Schlüssel des Ausstellers](http://go.microsoft.com/fwlink/p/?LinkID=303941)
-   [Wie verwende ich das Azure BizTalk Services SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)

