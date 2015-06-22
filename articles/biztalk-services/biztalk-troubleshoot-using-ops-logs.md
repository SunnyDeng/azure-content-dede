<properties 
	pageTitle="BizTalk Services: Fehlerbehebung mit Operationsprotokollen | Azure" 
	description="Behandeln Sie Probleme in BizTalk Services mithilfe von Vorgangsprotokollen. MABS, WABS" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="nitinme" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2015" 
	ms.author="mandia"/>


# BizTalk Services: Fehlerbehebung mit Operationsprotokollen

Operationsprotokolle sind eine Verwaltungsdienstfunktion aus dem Azure-Verwaltungsportal, mit denen Sie Verlaufsprotokolle zu Operationen anzeigen können, die in Ihren Azure-Diensten ausgeführt wurden, einschließlich BizTalk Services. So können Sie die Verlaufsdaten zu Verwaltungsvorgängen für Ihr Abonnement von BizTalk Services für bis zu 180 Tage anzeigen.

<div class="dev-callout"><b>Hinweis</b>
<p>Diese Funktion erstellt nur Protokolle für Verwaltungsoperationen in BizTalk Services, beispielsweise wenn der Dienst gestartet oder gesichert wurde usw. Diese Operationen werden nachverfolgt, unabhängig davon, ob sie aus dem Azure-Verwaltungsportal oder mit den <a href="http://msdn.microsoft.com/library/windowsazure/dn232347.aspx">REST-APIs für BizTalk Services</a> ausgeführt werden. Eine vollständige Liste der Operationen, die mit Verwaltungsdiensten nachverfolgt werden, finden Sie unter <a href="#bizops">Operationen, die mit Azure Management Services nachverfolgt werden</a>.</p>
<p>Dies umfasst Protokolle für Aktivitäten bezüglich der Laufzeit von BizTalk Services (beispielsweise Meldungen, die von Brücken verarbeitet werden usw.). Verwenden Sie im BizTalk Services-Portal die Ansicht "Überwachung", um diese Protokolle anzeigen zu können. Weitere Informationen finden Sie unter <a HREF="http://msdn.microsoft.com/library/windowsazure/hh949805.aspx">Tracking Messages</a> (Nachverfolgung von Meldungen, in englischer Sprache).</p>
</div>

##<a name="viewlogs"></a>Anzeigen von BizTalk-Operationsprotokollen
1. Klicken Sie im Azure-Verwaltungsportal auf "Management Services", und klicken Sie dann auf die Registerkarte "Operation Logs".
2. Sie können die Protokolle anhand verschiedener Parameter filtern, beispielsweise Abonnement, Datumsbereich, Diensttyp (zum Beispiel BizTalk Services), Dienstname oder Status (der Operation, beispielsweise erfolgreich oder fehlgeschlagen).
3. Klicken Sie auf das Häkchen, um die gefilterte Liste anzuzeigen. Die folgende Abbildung zeigt Aktivitäten bezüglich testbiztalkservice.
	![View operation logs][ViewLogs] 
4. Wenn Sie mehr Informationen zu einer bestimmten Operation anzeigen möchten, wählen Sie die Zeile aus, und klicken Sie unten auf der Seite auf <b>Details</b>.


##<a name="bizops"></a>Operationen, die mit Azure Management Services nachverfolgt werden
In der folgenden Tabelle finden Sie Operationen, die mit Azure Management Services nachverfolgt werden.

<table border="1" cellpadding="5">
<tr>
<td>CreateBizTalkService</td> 
<td align="left">Operation zur Erstellung eines neuen BizTalk-Dienstes</td> 
</tr> 
<tr>
<td>DeleteBizTalkService</td> 
<td align="left">Operation zum Löschen eines BizTalk-Dienstes</td>  
</tr> 
<tr>
<td>RestartBizTalkService</td> 
<td align="left">Operation zum Neustart eines BizTalk-Dienstes</td> 
</tr>
<tr>
<td>StartBizTalkService</td> 
<td align="left">Operation zum Starten eines BizTalk-Dienstes</td> 
</tr>
<tr>
<td>StopBizTalkService</td> 
<td align="left">Operation zum Beenden eines BizTalk-Dienstes</td> 
</tr>
<tr>
<td>DisableBizTalkService</td> 
<td align="left">Operation zum Deaktivieren eines BizTalk-Dienstes</td> 
</tr>
<tr>
<td>EnableBizTalkService</td> 
<td align="left">Operation zum Aktivieren eines BizTalk-Dienstes</td> 
</tr>
<tr>
<td>BackupBizTalkService</td> 
<td align="left">Operation zum Sichern eines BizTalk-Dienstes</td> 
</tr>
<tr>
<td>RestoreBizTalkService</td> 
<td align="left">Operation zur Wiederherstellung eines BizTalk-Dienstes aus einer bestimmten Sicherungskopie</td> 
</tr>
<tr>
<td>SuspendBizTalkService</td> 
<td align="left">Operation zum Anhalten eines BizTalk-Dienstes</td> 
</tr>
<tr>
<td>ResumeBizTalkService</td> 
<td align="left">Operation zum Fortsetzen eines BizTalk-Dienstes</td> 
</tr>
<tr>
<td>ScaleBizTalkService</td> 
<td align="left">Operation zur Skalierung eines BizTalk-Dienstes nach oben oder unten</td> 
</tr>
<tr>
<td>ConfigUpdateBizTalkService</td> 
<td align="left">Operation zur Aktualisierung der Konfiguration eines BizTalk-Dienstes</td> 
</tr>
<tr>
<td>ServiceUpdateBizTalkService</td> 
<td align="left">Operation für ein Upgrade oder Downgrade eines BizTalk-Dienstes auf eine andere Version</td> 
</tr>
<tr>
<td>PurgeBackupBizTalkService</td> 
<td align="left">Operation zum Bereinigen von Sicherungskopien eines BizTalk-Dienstes nach der Aufbewahrungszeit</td> 
</tr>
</table>


## Weitere Informationen
- [Backup BizTalk Service (Sichern von BizTalk Services, in englischer Sprache)](http://go.microsoft.com/fwlink/p/?LinkID=325584)
- [Restore BizTalk Service from Backup (Wiederherstellen von BizTalk Services aus Sicherungskopien, in englischer Sprache)](http://go.microsoft.com/fwlink/p/?LinkID=325582)
- [BizTalk Services: Übersicht über Developer, Basic, Standard und Premium Edition](http://go.microsoft.com/fwlink/p/?LinkID=302279)
- [BizTalk Services: Bereitstellen mit dem Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/p/?LinkID=302280)
- [BizTalk Services: Bereitstellungsstatusübersicht](http://go.microsoft.com/fwlink/p/?LinkID=329870)
- [BizTalk Services: Registerkarten "Dashboard", "Überwachen" und "Skalieren"](http://go.microsoft.com/fwlink/p/?LinkID=302281)
- [BizTalk Services: Drosselung](http://go.microsoft.com/fwlink/p/?LinkID=302282)
- [BizTalk Services: Name und Schlüssel des Ausstellers](http://go.microsoft.com/fwlink/p/?LinkID=303941)
- [Wie verwende ich das Azure BizTalk Services SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[ViewLogs]: ./media/biztalk-troubleshoot-using-ops-logs/Operation-Logs.png

<!--HONumber=46--> 
 