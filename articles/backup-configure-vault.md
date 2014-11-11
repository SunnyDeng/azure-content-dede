<properties linkid="manage-services-recovery-configure-backup-vault" urlDisplayName="Configure a Backup Vault" pageTitle="Configure Azure Recovery Services to quickly and easily back-up Windows Server" metaKeywords="disaster recovery" description="Use this tutorial to learn how to use the Backup service in Microsoft's Azure cloud offering to back up Windows Server to the cloud." metaCanonical="" services="recovery-services" documentationCenter="" title="Configure Azure Backup to quickly and easily back-up Windows Server" authors="raynew" solutions="" manager="johndaw" editor="tysonn" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="raynew" />

# <span id="configure-a-backup-vault-tutorial"></span></a>Konfigurieren der Azure-Sicherung für schnelle und einfache Sicherung von Windows Server

<div class="dev-callout"> 
<strong>Hinweis</strong>
 
<p>Sie ben&ouml;tigen ein Azure-Konto, um dieses Lernprogramm auszuf&uuml;hren. In diesem Lernprogramm erfahren Sie, wie Sie das Feature Azure Backup aktivieren. Fr&uuml;her mussten Sie ein X.509 v3-Zertifikat erstellen oder beziehen, um Ihren Sicherungsserver zu registrieren. Zertifikate werden weiterhin unterst&uuml;tzt, aber jetzt k&ouml;nnen Sie direkt auf der Seite &quot;Schnellstart&quot; Tresoranmeldeinformationen generieren, wodurch der Azure-Tresor einfacher bei einem Server registriert werden kann. </p>
<ul> 
<li>Wenn Sie noch kein Konto haben, k&ouml;nnen Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter <a href="/de-de/pricing/free-trial/">Kostenlose Azure-Testversion</a>.</li> 
 
</ul>
 
</div>

Sie müssen einen Sicherungstresor in Ihrer geografischen Region erstellen, in dem die Daten gespeichert werden sollen, um Dateien und Daten von Windows Server in Azure zu sichern. In diesem Lernprogramm wird Folgendes erläutert: das Erstellen des Tresors, in dem Sie Sicherungen speichern, das Herunterladen von Tresoranmeldeinformationen, das Installieren eines Sicherungs-Agents sowie eine Übersicht der Sicherungsverwaltungsaufgaben, die über das Verwaltungsportal verfügbar sind.

## <span id="create"></span></a>Erstellen eines Sicherungstresors

1.  Melden Sie sich im [Verwaltungsportal][Verwaltungsportal] an.

2.  Klicken Sie auf **Neu**, zeigen Sie auf **Datendienste** und dann auf **Wiederherstellungsdienste**, klicken Sie auf **Sicherungstresor**, und klicken Sie anschließend auf **Schnellerfassung**.

3.  Geben Sie unter **Name** einen benutzerfreundlichen Namen zur Identifizierung des Sicherungstresors ein.

4.  Wählen Sie unter **Region** die geografische Region für den Sicherungstresor aus.
     ![Neuer Sicherungstresor][Neuer Sicherungstresor]

5.  Klicken Sie auf **Tresor erstellen**.

    Es kann eine Weile dauern, bis der Sicherungstresor fertiggestellt wird. Sie können die Benachrichtigungen unten im Portal überwachen, um den Status zu überprüfen. Wenn der Sicherungstresor erfolgreich erstellt wurde, erhalten Sie eine entsprechende Nachricht, und in den Ressourcen der Wiederherstellungsdienste wird der Tresor als **Aktiv** angezeigt wird.
    ![Erstellung eines Sicherungstresors][Erstellung eines Sicherungstresors]

6.  Wenn mit Ihrem Organisationskonto mehrere Abonnements verknüpft sind, wählen Sie das Konto aus, das mit dem Sicherungstresor verknüpft werden soll.

## <span id="upload"></span></a>Herunterladen von Tresoranmeldeinformationen

Tresoranmeldeinformationen werden nun anstelle von Zertifikaten verwendet, um einen Azure-Dienst beim Server zu registrieren. Sie können auch weiterhin Zertifikate verwenden, allerdings sind Tresoranmeldeinformationen einfacher zu verwenden, weil Sie diese mithilfe des Azure-Portals generieren und herunterladen können.

1.  Melden Sie sich im [Verwaltungsportal][Verwaltungsportal] an.

2.  Klicken Sie auf **Wiederherstellungsdienste**, und wählen Sie den Sicherungstresor aus, den Sie bei einem Server registrieren möchten. Die Seite "Schnellstart" für diesen Sicherungstresor wird angezeigt.

3.  Klicken Sie auf der Seite "Schnellstart" auf **Tresoranmeldeinformationen herunterladen**, damit die Tresoranmeldeinformationen, die Sie verwenden, um Ihren Server bei dem Sicherungstresor zu registrieren, generiert und heruntergeladen werden.

4.  Das Portal generiert Tresoranmeldeinformationen mit einer Kombination aus dem Tresornamen und dem aktuellen Datum. Klicken Sie auf **Speichern**, um die Tresoranmeldeinformationen in den Download-Ordner des lokalen Kontos herunterzuladen. Sie können auch im Menü **Speichern** die Option **Speichern unter** auswählen, um einen anderen Speicherort anzugeben. Sie können die Tresoranmeldeinformationen nicht bearbeiten, sodass es keinen Grund gibt, auf "Öffnen" zu klicken. Sobald die Anmeldeinformationen heruntergeladen wurden, werden Sie aufgefordert, den Ordner zu öffnen. Klicken Sie auf **x**, um dieses Menü zu schließen.

## <span id="download"></span></a>Herunterladen und Installieren eines Sicherungs-Agents

1.  Im [Verwaltungsportal][Verwaltungsportal].

2.  Klicken Sie auf **Wiederherstellungsdienste**, und wählen Sie einen Sicherungstresor aus, um die zugehörige Seite "Schnellstart" anzuzeigen.

3.  Wählen Sie auf der Seite "Schnellstart" den Agent aus, den Sie herunterladen möchten. Sie können **Azure-Sicherungs-Agent herunterladen**, **Windows Server und System Center Data Protection Manager** oder **Windows Server Essentials** auswählen. Weitere Informationen finden Sie unter:

    -   [Install Azure Backup Agent for Windows Server 2012 and System Center 2012 SP1 - Data Protection Manager (Installieren des Windows Azure-Sicherungs-Agents für Windows Server 2012 und System Center 2012 SP1 – Data Protection Manager, in englischer Sprache)][Install Azure Backup Agent for Windows Server 2012 and System Center 2012 SP1 - Data Protection Manager (Installieren des Windows Azure-Sicherungs-Agents für Windows Server 2012 und System Center 2012 SP1 – Data Protection Manager, in englischer Sprache)]
    -   [Install Azure Backup Agent for Windows Server 2012 Essentials (Installieren des Sicherungs-Agents für Windows Server 2012 Essentials, in englischer Sprache)][Install Azure Backup Agent for Windows Server 2012 Essentials (Installieren des Sicherungs-Agents für Windows Server 2012 Essentials, in englischer Sprache)]

Wenn Sie den Agent installiert haben, können Sie die passende lokale Verwaltungsschnittstelle verwenden (wie das Microsoft Management Console-Snap-In, die Data Protection Manager-Konsole in Systemcenter oder das Windows Server Essentials-Dashboard), um die Sicherungsrichtlinie für den Server festzulegen.

## <span id="manage"></span></a>Verwalten von Sicherungstresoren und Sicherungsservern

1.  Melden Sie sich im [Verwaltungsportal][Verwaltungsportal] an.

2.  Klicken Sie auf **Wiederherstellungsdienste** und dann auf den Namen des Sicherungstresors, um die Seite "Schnellstart" anzuzeigen.

3.  Klicken Sie auf **Dashboard**, um die Verwendungsübersicht für den Server anzuzeigen. Unten im Dashboard können Sie folgende Aufgaben ausführen:

    -   **Zertifikat verwalten**. Wenn ein Zertifikat zum Registrieren des Servers verwendet wurde, verwenden Sie diese Aufgabe, um das Zertifikat zu aktualisieren. Wenn Sie Tresoranmeldeinformationen verwenden, verwenden Sie **Zertifikat verwalten** nicht.
    -   **Löschen**. Dies wird verwendet, um den aktuellen Sicherungstresor zu löschen. Wenn ein Sicherungstresor nicht mehr verwendet wird, können Sie ihn löschen, um Speicherplatz freizugeben. **Löschen** wird erst aktiviert, wenn alle registrierten Server aus dem Tresor gelöscht wurden.
    -   **Tresorsanmeldeinformationen**. Verwenden Sie diese Menüoption in der Schnelleinsicht, um Ihre Tresoranmeldeinformationen zu konfigurieren.

4.  Klicken Sie auf **Protected Items**, um die Elemente anzuzeigen, die von den Servern gesichert wurden. Diese Liste dient nur zu Informationszwecken.
    ![Geschützte Elemente][Geschützte Elemente]

5.  Klicken Sie auf **Servers**, um die Namen der Server anzuzeigen, die für diesen Tresor registriert sind. Jetzt können Sie folgende Aufgaben ausführen:

    -   **Erneute Registrierung zulassen**. Wenn diese Option für einen Server ausgewählt ist, können Sie den Registrierungs-Assistenten des Agents verwenden, um den Server erneut für den Sicherungstresor zu registrieren. Eine erneute Registrierung ist möglicherweise nötig, wenn im Zertifikat ein Fehler auftritt oder wenn ein Server erneut erstellt werden muss. Je Servername ist nur eine erneute Registrierung möglich.
    -   **Löschen**. Dies wird verwendet, um einen Server aus dem Sicherungstresor zu löschen. Alle gespeicherten Daten dieses Servers werden sofort gelöscht.

        ![Server gelöscht][Server gelöscht]

## <span id="next"></span></a>Nächste Schritte

-   Weitere Informationen zur Azure-Sicherung finden Sie unter [Übersicht über die Windows Azure-Sicherung][Übersicht über die Windows Azure-Sicherung].

-   Besuchen Sie das [Azure Sicherungs-Forum][Azure Sicherungs-Forum].

  [Verwaltungsportal]: https://manage.windowsazure.com
  [Neuer Sicherungstresor]: http://i.imgur.com/506c7ch.png
  [Erstellung eines Sicherungstresors]: http://i.imgur.com/grtLcKM.png
  [Geschützte Elemente]: ./media/backup-configure-vault/RS_protecteditems.png
  [Server gelöscht]: ./media/backup-configure-vault/RS_deletedserver.png
  [Übersicht über die Windows Azure-Sicherung]: http://go.microsoft.com/fwlink/p/?LinkId=222425
  [Azure Sicherungs-Forum]: http://go.microsoft.com/fwlink/p/?LinkId=290933
