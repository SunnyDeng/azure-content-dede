<properties 
	pageTitle="Konfigurieren der Azure Recovery Services für schnelle und einfache Sicherung von Windows Server" 
	description="Mithilfe dieses Lernprogramms erfahren Sie, wie der Sicherungsdienst in der Microsoft Azure Cloud verwendet wird, um den Windows Server in der Cloud zu sichern." 
	services="site-recovery" 
	documentationCenter="" 
	authors="markgalioto" 
	manager="jwhit" 
	editor="tysonn"/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="markgal"/>



<h1><a id="configure-a-backup-vault-tutorial"></a>Konfigurieren der Azure-Sicherung für schnelle und einfache Sicherung von Windows Server</h1>
<div class="dev-callout"> 
<strong>Hinweis</strong>
 
<p>Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. In diesem Lernprogramm erfahren Sie, wie Sie das Feature Azure Backup aktivieren. Früher mussten Sie ein X.509 v3-Zertifikat erstellen oder beziehen, um Ihren Sicherungsserver zu registrieren. Zertifikate werden weiterhin unterstützt, aber jetzt können Sie direkt auf der Seite "Schnellstart" Tresoranmeldeinformationen generieren, wodurch der Azure-Tresor einfacher bei einem Server registriert werden kann. </p>
<ul> 
<li>Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten dazu finden Sie hier: <a href="/de-de/pricing/free-trial/">Kostenlose Microsoft Azure-Testversion</a>.</li> 
 

</ul>
 

</div>
  

<p>Sie müssen einen Sicherungstresor in Ihrer geografischen Region erstellen, in dem die Daten gespeichert werden sollen, um Dateien und Daten von Windows Server in Azure zu sichern. In diesem Lernprogramm wird Folgendes erläutert: das Erstellen des Tresors, in dem Sie Sicherungen speichern, das Herunterladen von Tresoranmeldeinformationen, das Installieren eines Sicherungs-Agents sowie eine Übersicht der Sicherungsverwaltungsaufgaben, die über das Verwaltungsportal verfügbar sind.</p>



<h2><a id="create"></a>Erstellen eines Sicherungstresors</h2>

1. Melden Sie sich beim [Verwaltungsportal](https://manage.windowsazure.com) an.

2. Klicken Sie auf **Neu**, dann auf **Datendienste**, **Wiederherstellungsdienste**, **Sicherungstresor**, und klicken Sie anschließend auf **Schnellerfassung**.

3. Geben Sie unter **Name** einen benutzerfreundlichen Namen zur Identifizierung des Sicherungstresors ein.

4. Wählen Sie unter **Region** die geografische Region für den Sicherungstresor aus.  
![New backup vault](http://i.imgur.com/8ptgjuo.png)

5. Klicken Sie auf **Tresor erstellen**.

	Es kann eine Weile dauern, bis der Sicherungstresor fertiggestellt wird. Sie können die Benachrichtigungen unten im Portal überwachen, um den Status zu überprüfen. Wenn der Sicherungstresor erstellt wurde, erhalten Sie eine Nachricht, dass der Tresor erfolgreich erstellt wurde und in den Ressourcen der Wiederherstellungsdienste als **Aktiv** angezeigt wird. 
![backup vault creation](http://i.imgur.com/grtLcKM.png)

3. Wenn mit Ihrem Organisationskonto mehrere Abonnements verknüpft sind, wählen Sie das Konto aus, das mit dem Sicherungstresor verknüpft werden soll.

<h2><a id="upload"></a>Herunterladen von Tresoranmeldeinformationen</h2>

Tresoranmeldeinformationen werden nun anstelle von Zertifikaten verwendet, um einen Azure-Dienst beim Server zu registrieren. Sie können auch weiterhin Zertifikate verwenden, allerdings sind Tresoranmeldeinformationen einfacher zu verwenden, weil Sie diese mithilfe des Azure-Portals generieren und herunterladen können.  

1. Melden Sie sich beim [Verwaltungsportal](https://manage.windowsazure.com) an.

2. Klicken Sie auf **Wiederherstellungsdienste**, und wählen Sie den Sicherungstresor aus, den Sie bei einem Server registrieren möchten.  Die Seite "Schnellstart" für diesen Sicherungstresor wird angezeigt.
	

3. Klicken Sie auf der Seite "Schnellstart" auf **Tresoranmeldeinformationen herunterladen**, damit die Tresoranmeldeinformationen, die Sie verwenden, um Ihren Server bei dem Sicherungstresor zu registrieren, generiert und heruntergeladen werden.

4. Das Portal generiert Tresoranmeldeinformationen mit einer Kombination aus dem Tresornamen und dem aktuellen Datum. Klicken Sie auf **Speichern**, um die Tresoranmeldeinformationen in den Download-Ordner des lokalen Kontos herunterzuladen. Sie können auch im Menü **Speichern** die Option **Speichern unter** auswählen, um einen anderen Speicherort anzugeben. Sie können die Tresoranmeldeinformationen nicht bearbeiten, sodass es keinen Grund gibt, auf "Öffnen" zu klicken. Sobald die Anmeldeinformationen heruntergeladen wurden, werden Sie aufgefordert, den Ordner zu öffnen. Klicken Sie auf **x**, um dieses Menü zu schließen.

<h2><a id="download"></a>Herunterladen und Installieren eines Sicherungs-Agents</h2>
1. Im [Verwaltungsportal](https://manage.windowsazure.com).

2. Klicken Sie auf **Wiederherstellungsdienste**, und wählen Sie einen Sicherungstresor aus, um die zugehörige Seite "Schnellstart" anzuzeigen.

3. Wählen Sie auf der Seite "Schnellstart" den Agent aus, den Sie herunterladen möchten. Sie können **Azure-Sicherungs-Agent herunterladen**, **Windows Server und System Center Data Protection Manager** oder **Windows Server Essentials** auswählen.  Weitere Informationen finden Sie unter:

	* [Installieren des Azure-Sicherungs-Agents für Windows Server 2012 und System Center 2012 SP1 - Data Protection Manager](http://technet.microsoft.com/library/hh831761.aspx#BKMK_installagent)
	* [Installieren des Sicherungs-Agents für Windows Server 2012 Essentials](http://technet.microsoft.com/library/jj884318.aspx)

Wenn Sie den Agent installiert haben, können Sie die passende lokale Verwaltungsschnittstelle verwenden (wie das Microsoft Management Console-Snap-In, die Data Protection Manager-Konsole in Systemcenter oder das Windows Server Essentials-Dashboard), um die Sicherungsrichtlinie für den Server festzulegen.
	
  

<h2><a id="manage"></a>Verwalten von Sicherungstresoren und Sicherungsservern</h2>
1. Melden Sie sich beim [Verwaltungsportal](https://manage.windowsazure.com) an.

2. Klicken Sie auf **Wiederherstellungsdienste** und dann auf den Namen des Sicherungstresors, um die Seite "Schnellstart" anzuzeigen. 

3. Klicken Sie auf **Dashboard**, um die Verwendungsübersicht für den Server anzuzeigen. Unten im Dashboard können Sie folgende Aufgaben ausführen:
	* **Zertifikat verwalten**. Wenn ein Zertifikat zum Registrieren des Servers verwendet wurde, verwenden Sie diese Aufgabe, um das Zertifikat zu aktualisieren. Wenn Sie Tresoranmeldeinformationen verwenden, verwenden Sie **Zertifikat verwalten** nicht.
	* **Löschen**. Dies wird verwendet, um den aktuellen Sicherungstresor zu löschen. Wenn ein Sicherungstresor nicht mehr verwendet wird, können Sie ihn löschen, um Speicherplatz freizugeben. **Delete** wird erst aktiviert, wenn alle registrierten Server aus dem Tresor gelöscht wurden.
	* **Tresoranmeldeinformationen**. Verwenden Sie diese Menüoption in der Schnelleinsicht, um Ihre Tresoranmeldeinformationen zu konfigurieren. 

3. Klicken Sie auf **Geschützte Elemente**, um die Elemente anzuzeigen, die von den Servern gesichert wurden. Diese Liste dient nur zu Informationszwecken.  
![Protected Items][protected-itmes]

4. Klicken Sie auf **Server**, um die Namen der Server anzuzeigen, die für diesen Tresor registriert sind. Jetzt können Sie folgende Aufgaben ausführen:
	* **Erneute Registrierung zulassen**. Wenn diese Option für einen Server ausgewählt ist, können Sie den Registrierungs-Assistenten des Agents verwenden, um den Server erneut für den Sicherungstresor zu registrieren. Eine erneute Registrierung ist möglicherweise nötig, wenn im Zertifikat ein Fehler auftritt oder wenn ein Server erneut erstellt werden muss. Je Servername ist nur eine erneute Registrierung möglich.
	* **Löschen**. Dies wird verwendet, um einen Server aus dem Sicherungstresor zu löschen. Alle gespeicherten Daten dieses Servers werden sofort gelöscht.

		![Deleted Server][deleted-server]

<h2><a id="next"></a>Nächste Schritte</h2>

- Weitere Informationen zur Azure-Sicherung finden Sie unter [Übersicht über die Azure-Sicherung](http://go.microsoft.com/fwlink/p/?LinkId=222425). 

- Besuchen Sie das [Azure Sicherungs-Forum](http://go.microsoft.com/fwlink/p/?LinkId=290933).

[new-backup-vault]: ./media/backup-configure-vault/RS_howtobackup1.png
[backup-vault-create]: ./media/backup-configure-vault/RS_howtobackup2.png
[manage-cert]: ./media/backup-configure-vault/RS_howtoupload1.png
[install-agent]: ./media/backup-configure-vault/RS_howtodownload1.png
[deleted-server]: ./media/backup-configure-vault/RS_deletedserver.png
[protected-itmes]: ./media/backup-configure-vault/RS_protecteditems.png

<!--HONumber=35.2-->

<!--HONumber=46--> 
