<properties linkid="manage-services-biztalk-state-chart" urlDisplayName="BizTalk Services: Service state chart" pageTitle="BizTalk Services: Service state chart | Azure" metaKeywords="" description="" metaCanonical="" services="biztalk-services" documentationCenter="" title="BizTalk Services: Service state chart" authors="mandia" solutions="integration" manager="dwrede" editor="cgronlun" />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/10/2014" ms.author="mandia" />

# BizTalk Services: Dienststatusübersicht

Je nach aktuellem Status des BizTalk-Diensts stehen Vorgänge bereit, die Sie in eben diesem BizTalk-Dienst entweder ausführen können oder nicht ausführen können.

Sie stellen beispielsweise einen neuen BizTalk-Dienst im Azure-Verwaltungsportal bereit. Wenn dieser Vorgang erfolgreich abgeschlossen ist, befindet sich der BizTalk-Dienst im aktiven Status. Im aktiven Status können Sie den BizTalk-Dienst beenden. Wenn das Beenden erfolgreich durchgeführt wird, geht der BizTalk-Dienst in den Status "Beendet" über. Wenn das Beenden fehlschlägt, geht der BizTalk-Dienst in den Status "StopFailed" über. Im Status "StopFailed" können Sie den BizTalk-Dienst neu starten. Wenn Sie einen nicht zulässigen Vorgang auszuführen versuchen, wie etwa die Wiederaufnahme des BizTalk-Diensts, tritt der folgende Fehler auf:

**Vorgang nicht zulässig**

Informationen zum Bereitstellen eines BizTalk Service finden Sie unter [BizTalk Services: Bereitstellen mit dem Azure-Verwaltungsportal][BizTalk Services: Bereitstellen mit dem Azure-Verwaltungsportal].

Die folgenden Tabellen listen jene Vorgänge auf, die ausgeführt werden können, wenn sich der BizTalk-Dienst in einem spezifischen Status befindet. Ein Kreuz bedeutet dabei, dass der Vorgang ausgeführt werden kann, wenn der betreffende Status zutrifft. Ein leerer Eintrag bedeutet dagegen, dass der Vorgang nicht ausgeführt werden kann, wenn der betreffende Status zutrifft.

#### Start-, Stopp-, Neustart-, Unterbrechungs-, Wiederaufnahme- und Lösch-Vorgänge

<table border="1">
<tr>
<th colspan="15">
Vorgang

</th>
</tr>
<tr>
<th rowspan="18">
BizTalk Service-Status

</th>
</tr>
<tr bgcolor="FAF9F9">
<th>
</th>
<th>
Starten

</th>
<th>
Beenden

</th>
<th>
Neu starten

</th>
<th>
Unterbrechen

</th>
<th>
Wiederaufnehmen

</th>
<th>
Löschen

</th>
</tr>
<tr>
<td bgcolor="FAF9F9">
<strong>Aktiv</strong>

</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<strong>Deaktiviert</strong>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<strong>Unterbrochen</strong>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<strong>Beendet</strong>

</td>
<td>
<center>
x

</center>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<strong>Dienst-Update fehlgeschlagen</strong>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<strong>DisableFailed</strong>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<strong>EnableFailed</strong>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<strong>StartFailed
 StopFailed
 RestartFailed</strong>

</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<strong>SuspendedFailed
 ResumeFailed</strong>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<strong>CreatedFailed
 RestoreFailed
</strong>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<strong>ConfigUpdateFailed</strong>

</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<strong>ScaleFailed</strong>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
</table>

#### Skalierungs-, Konfigurations-Update- und Sicherungs-Vorgänge

<table border="1">
<tr>
<th colspan="15">
Vorgang

</th>
</tr>
<tr>
<th rowspan="18">
BizTalk Service-Status

</th>
</tr>
<tr bgcolor="FAF9F9">
<th>
</th>
<th>
Skalieren

</th>
<th>
Konfiguration aktualisieren

</th>
<th>
Sicherung

</th>
</tr>
<tr>
<td bgcolor="FAF9F9">
<strong>Aktiv</strong>

</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<strong>Deaktiviert</strong>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<strong>Unterbrochen</strong>

</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<strong>Beendet</strong>

</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<strong>Dienst-Update fehlgeschlagen</strong>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<strong>DisableFailed</strong>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<strong>EnableFailed</strong>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<strong>StartFailed<br></br>
 StopFailed<br></br>
 RestartFailed</strong>

</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
<td>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<strong>SuspendedFailed<br></br>
 ResumeFailed</strong>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<strong>CreatedFailed<br></br>
RestoreFailed</strong>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<strong>ConfigUpdateFailed</strong>

</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
<td>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<strong>ScaleFailed</strong>

</td>
<td>
<center>
x

</center>
</td>
<td>
</td>
<td>
</td>
</tr>
</table>
## Weitere Informationen

-   [BizTalk Services: Bereitstellen mit dem Azure-Verwaltungsportal][BizTalk Services: Bereitstellen mit dem Azure-Verwaltungsportal]
-   [BizTalk Services: Registerkarten "Dashboard", "Überwachen" und "Skalieren"][BizTalk Services: Registerkarten "Dashboard", "Überwachen" und "Skalieren"]
-   [BizTalk Services: Übersicht über Developer, Basic, Standard und Premium Edition][BizTalk Services: Übersicht über Developer, Basic, Standard und Premium Edition]
-   [BizTalk Services: Sichern und Wiederherstellen][BizTalk Services: Sichern und Wiederherstellen]
-   [BizTalk Services: Drosselung][BizTalk Services: Drosselung]
-   [BizTalk Services: Name und Schlüssel des Ausstellers][BizTalk Services: Name und Schlüssel des Ausstellers]
-   [Wie verwende ich das Azure BizTalk Services SDK][Wie verwende ich das Azure BizTalk Services SDK]

  [BizTalk Services: Bereitstellen mit dem Azure-Verwaltungsportal]: http://go.microsoft.com/fwlink/p/?LinkID=302280
  [BizTalk Services: Registerkarten "Dashboard", "Überwachen" und "Skalieren"]: http://go.microsoft.com/fwlink/p/?LinkID=302281
  [BizTalk Services: Übersicht über Developer, Basic, Standard und Premium Edition]: http://go.microsoft.com/fwlink/p/?LinkID=302279
  [BizTalk Services: Sichern und Wiederherstellen]: http://go.microsoft.com/fwlink/p/?LinkID=329873
  [BizTalk Services: Drosselung]: http://go.microsoft.com/fwlink/p/?LinkID=302282
  [BizTalk Services: Name und Schlüssel des Ausstellers]: http://go.microsoft.com/fwlink/p/?LinkID=303941
  [Wie verwende ich das Azure BizTalk Services SDK]: http://go.microsoft.com/fwlink/p/?LinkID=302335
