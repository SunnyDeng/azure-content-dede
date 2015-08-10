<properties 
   pageTitle="Anzeigen und Verwalten von StorSimple-Warnungen"
   description="Beschreibt StorSimple-Warnungen und die Verwendung des StorSimple Manager-Diensts zum Anzeigen und Löschen der Warnungen."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="07/28/2015"
   ms.author="v-sharos" />

# Anzeigen und Verwalten von StorSimple-Warnungen

## Übersicht

Auf der Registerkarte **Warnungen** des StorSimple Manager-Diensts können Sie Warnungen zu StorSimple-Geräten in Echtzeit anzeigen und löschen. Auf dieser Registerkarte können Sie die Integrität Ihrer StorSimple-Geräte und die gesamte Microsoft Azure StorSimple-Lösung zentral überwachen.

In diesem Tutorial werden häufige Warnungsbedingungen, Warnungsschweregrade und die Konfiguration von Warnungsbenachrichtigungen beschrieben. Außerdem sind Tabellen mit Kurzübersichten zu Warnungen vorhanden, in denen Sie eine bestimmte Warnung schnell finden und entsprechend reagieren können.

![Seite „Warnungen“](./media/storsimple-manage-alerts/HCS_AlertsPage.png)

## Häufige Warnungsbedingungen

Ihr StorSimple-Gerät generiert Warnungen als Reaktion auf viele verschiedene Bedingungen. Folgende Arten von Warnungsbedingungen kommen am häufigsten vor:

- **Hardwareprobleme** – Diese Warnungen informieren Sie über die Integrität der Hardware. Sie erfahren, wann Firmwareaktualisierungen erforderlich sind, ob für eine Netzwerkschnittstelle ein Problem besteht oder ob ein Fehler für ein Datenlaufwerk aufgetreten ist.

- **Konnektivitätsprobleme** – Diese Warnungen werden angezeigt, wenn es beim Übertragen von Daten zwischen Datenquellen zu Schwierigkeiten kommt. Kommunikationsprobleme können während der Übertragung von Daten auf das bzw. aus dem Azure-Speicherkonto oder aufgrund einer fehlenden Verbindung zwischen den Geräten und dem StorSimple Manager-Dienst auftreten. Sie können während des Konfigurations-, Sicherungs oder Wiederherstellungsprozesses auftreten. Die Behebung von Kommunikationsproblemen ist besonders schwierig, da die Anzahl der möglichen Fehlerquellen hoch ist. Sie sollten sich immer zuerst vergewissern, dass die Netzwerkverbindung und der Internetzugriff verfügbar sind, bevor Sie sich mit der eingehenderen Problembehandlung beschäftigen.

- **Leistungsprobleme** – Diese Warnungen werden verursacht, wenn Ihr System keine optimale Leistung aufweist, z. B. bei einer hohen Auslastung.

Außerdem werden unter Umständen Warnungen zur Sicherheit, zu Updates oder zu Fehlern bei Aufträgen angezeigt.

## Schweregrade von Warnungen

Warnungen haben unterschiedliche Schweregrade. Diese richten sich nach den Auswirkungen, die mit einer Warnungssituation verbunden sind, sowie danach, wie dringend eine Reaktion auf die Warnung erfolgen muss. Die Schweregrade lauten:

- **Kritisch** – Diese Warnung wird als Reaktion auf einen Zustand ausgegeben, der den erfolgreichen Betrieb Ihres Systems beeinträchtigt. Es ist eine Aktion erforderlich, um sicherzustellen, dass der StorSimple-Dienst nicht unterbrochen wird.

- **Warnung** – Dieser Zustand kann kritisch werden, wenn er nicht behoben wird. Sie sollten den Fall untersuchen und alle Maßnahmen ergreifen, die zum Beheben des Problems erforderlich sind.

- **Information** – Diese Warnung enthält Informationen, die für die Nachverfolgung und Verwaltung Ihres Systems hilfreich sind.

## Konfigurieren der Warnungseinstellungen

Sie können auswählen, ob Sie per E-Mail über die Warnungsbedingungen für die einzelnen StorSimple-Geräte informiert werden möchten. Sie können auch andere Empfänger von Warnungsbenachrichtigungen angeben, indem Sie deren E-Mail-Adressen im Feld **ANDERE E-MAIL-EMPFÄNGER** durch Semikolons getrennt eingeben.

>[AZURE.NOTE]Sie können maximal 20 E-Mail-Adressen pro Gerät eingeben.

Nachdem Sie die E-Mail-Benachrichtigung für ein Gerät aktiviert haben, erhalten die Mitglieder der Benachrichtigungsliste jedes Mal eine E-Mail-Nachricht, wenn eine kritische Warnung vorliegt. Die Nachrichten werden von der Adresse *storsimple-alerts-noreply@mail.windowsazure.com* gesendet und enthalten eine Beschreibung der Warnungsbedingung. Empfänger können auf **Abbestellen** klicken, um die Entfernung aus der E-Mail-Benachrichtigungsliste durchzuführen.

#### So aktivieren Sie die E-Mail-Benachrichtigungen über Warnungen für ein Gerät

1. Wählen Sie für das Gerät **Geräte** \> **Konfigurieren**.

2. Legen Sie unter **Warnungseinstellungen** Folgendes fest:

    1. Klicken Sie im Feld **E-MAIL-BENACHRICHTIGUNG SENDEN** auf **JA**.

    2. Klicken Sie im Feld **E-MAIL AN DIENSTADMINISTRATOREN** auf **JA**, wenn der Dienstadministrator und alle Co-Administratoren die Warnungsbenachrichtigungen erhalten sollen.

    3. Geben Sie im Feld **ANDERE E-MAIL-EMPFÄNGER** die E-Mail-Adressen aller anderen Empfänger ein, die die Warnungsbenachrichtigungen erhalten sollen. Geben Sie Namen im folgenden Format ein: *someone@somewhere.com*. Verwenden Sie Semikolons, um mehrere E-Mail-Adressen zu trennen. Sie können maximal 20 E-Mail-Adressen pro Gerät konfigurieren. 

    ![Seite zur Konfiguration von Warnungsbenachrichtigungen](./media/storsimple-manage-alerts/HCS_AlertNotificationConfig.png)

3. Um eine E-Mail-Benachrichtigung als Test zu senden, klicken Sie auf das Pfeilsymbol neben **TEST-E-MAIL SENDEN**. Der StorSimple Manager-Dienst zeigt beim Weiterleiten der Testbenachrichtigung Statusmeldungen an.

4. Wenn die folgende Meldung angezeigt wird, klicken Sie auf **OK**.

    ![E-Mail zum Testen der Warnungsbenachrichtigung](./media/storsimple-manage-alerts/HCS_AlertNotificationConfig3.png)

    >[AZURE.NOTE]Wenn die Testbenachrichtigung nicht gesendet werden kann, zeigt der StorSimple Manager-Dienst eine entsprechende Meldung an. Dies kann aufgrund von Datenverkehrsproblemen oder anderen Netzwerkproblemen auftreten. Klicken Sie auf **OK**, warten Sie einige Minuten, und versuchen Sie dann, die Testnachricht erneut zu senden.

## Anzeigen und Nachverfolgen von Warnungen

Im Dashboard des StorSimple Manager-Diensts haben Sie den Überblick über die Anzahl der Warnungen auf den Geräten, die unterteilt nach Schweregrad angezeigt werden.

![Dashboard „Warnungen“](./media/storsimple-manage-alerts/admin_alerts_dashboard.png)

Wenn Sie auf den Schweregrad klicken, wird die Registerkarte **Warnungen** geöffnet. Die Ergebnisse umfassen nur die Warnungen des jeweiligen Schweregrads.

![Warnungsbericht nach Warnungstyp](./media/storsimple-manage-alerts/admin_alerts_scoped.png)

Wenn Sie in der Liste auf eine Warnung klicken, werden weitere Details zur Warnung angezeigt, z. B. die letzte Meldung der Warnung, die Häufigkeit der Warnung auf dem Gerät und die empfohlene Maßnahme zum Beheben der Warnung. Wenn es sich um eine Hardwarewarnung handelt, wird auch die Hardwarekomponente angegeben.

![Beispiel für Hardwarewarnung](./media/storsimple-manage-alerts/admin_alerts_hardware.png)

Sie können die Details der Warnung in eine Textdatei kopieren, wenn Sie die Warnungsauflösung in einer separaten Datei nachverfolgen möchten oder wenn Sie die Informationen an den Microsoft Support senden möchten. Nachdem Sie die Warnungsbedingung behoben haben, sollten Sie die Warnung vom Gerät löschen, indem Sie sie auf der Registerkarte **Warnungen** markieren und auf **Löschen** klicken. Halten Sie zum Löschen mehrerer Warnungen die STRG-TASTE gedrückt, während Sie die Warnungen markieren, und klicken Sie dann auf **Löschen**. Beachten Sie, dass einige Warnungen automatisch gelöscht werden, wenn das Problem behoben ist oder wenn die Warnung mit neuen Informationen aktualisiert wird.

Wenn Sie auf **Löschen** klicken, können Sie Kommentare zur Warnung und die Schritte zur Behebung des Problems angeben. Einige Ereignisse werden gelöscht, falls ein anderes Ereignis mit neuen Informationen ausgelöst wird. In diesem Fall wird die folgende Meldung angezeigt:

![Warnungsmeldung löschen](./media/storsimple-manage-alerts/admin_alerts_system_clear.png)

## Sortieren und Überprüfen von Warnungen

Unter Umständen finden Sie es effizienter, Berichte für Warnungen auszuführen, damit Sie sie in Gruppen überprüfen und löschen können. Außerdem können auf der Registerkarte **Warnungen** bis zu 250 Warnungen angezeigt werden. Wenn Sie diese Anzahl von Warnungen überschritten haben, werden nicht alle Warnungen in der Standardansicht angezeigt. Sie können die folgenden Felder kombinieren, um anzupassen, welche Warnungen angezeigt werden:

- **Status** – Sie können Warnungen der Kategorie **Aktiv** oder **Gelöscht** anzeigen. Aktive Warnungen werden weiterhin ausgelöst, während gelöschte Warnungen entweder manuell von einem Administrator oder programmgesteuert gelöscht wurden, weil die Warnungsbedingung mit neuen Informationen aktualisiert wurde.

- **Schweregrad** – Sie können Warnungen aller Schweregrade \(Kritisch, Warnung, Information\) oder nur einen bestimmten Schweregrad anzeigen, z. B. nur kritische Warnungen.

- **Quelle** – Sie können Warnungen für alle Quellen anzeigen oder die Anzeige auf die Warnungen beschränken, die entweder vom Dienst oder von einem oder allen Geräten stammen.

- **Zeitraum** – Wenn Sie das Datum bzw. den Zeitstempel mit **Von** und **Bis** angeben, können Sie Warnungen für den gewünschten Zeitraum anzeigen.

## Kurzübersicht zu Warnungen

In den folgenden Tabellen sind einige Microsoft Azure StorSimple-Warnungen aufgeführt, die unter Umständen angezeigt werden, sowie weitere Informationen und Empfehlungen, falls verfügbar. Warnungen für StorSimple-Geräte fallen in eine der folgenden Kategorien:

- [Warnungen zur Cloudkonnektivität](#cloud-connectivity-alerts)

- [Clusterwarnungen](#cluster-alerts)

- [Warnungen zur Notfallwiederherstellung](#disaster-recovery-alerts)

- [Hardwarewarnungen](#hardware-alerts)

- [Warnungen zu Auftragsfehlern](#job-failure-alerts)

- [Leistungswarnungen](#performance-alerts)

- [Sicherheitswarnungen](#security-alerts)

- [Warnungen zum Supportpaket](#support-package-alerts)

- [Testwarnungen](#test-alerts)

- [Updatewarnungen](#update-alerts)

### Warnungen zur Cloudkonnektivität

|Warnungstext|Ereignis|Weitere Informationen/Empfohlene Maßnahmen|
|:---|:---|:---|
|Es kann keine Konnektivität zu \<\*Anmeldeinformationsname für Cloud\*\> hergestellt werden.|Die Verbindung mit dem Speicherkonto kann nicht hergestellt werden.|Anscheinend liegt ein Konnektivitätsproblem mit Ihrem Gerät vor. Führen Sie das Cmdlet **Test-HcsmConnection** in der Windows PowerShell-Schnittstelle für StorSimple auf Ihrem Gerät aus, um das Problem zu identifizieren und zu beheben. Wenn die Einstellungen korrekt sind, liegt möglicherweise ein Problem mit den Anmeldeinformationen des Speicherkontos vor, für das die Warnung ausgelöst wurde. Ermitteln Sie in diesem Fall mithilfe des Cmdlets **Test-HcsStorageAccountCredential**, ob Probleme vorliegen, die Sie lösen können.<ul><li>Überprüfen Sie die Netzwerkeinstellungen.</li><li>Überprüfen Sie die Speicherkonto-Anmeldedaten.</li></ul>|
|Wir haben seit \<\*Anzahl\*\> Minuten kein Signal mehr von Ihrem Gerät erhalten.|Es kann keine Verbindung mit dem Gerät hergestellt werden.|Anscheinend liegt ein Konnektivitätsproblem mit Ihrem Gerät vor. Führen Sie das Cmdlet **Test-HcsmConnection** in der Windows PowerShell-Schnittstelle für StorSimple auf Ihrem Gerät aus, um das Problem zu identifizieren und zu beheben, oder wenden Sie sich an Ihren Netzwerkadministrator.|

### Clusterwarnungen

|Warnungstext|Ereignis|Weitere Informationen/Empfohlene Maßnahmen|
|:---|:---|:---|
|Geräte-Failover zu \<\*Gerätename\*\>.|Das Gerät befindet sich im Wartungsmodus.|Ein Gerätefailover wurde ausgeführt, da der Wartungsmodus aufgerufen oder beendet wurde. Dies ist ein normales Verhalten, das keine Aktion erfordert. Nachdem Sie diese Warnung bestätigt haben, löschen Sie sie von der Warnungsseite.|
|Geräte-Failover zu \<\*Gerätename\*\>.|Die Firmware oder Software des Geräts wurde gerade aktualisiert.|Aufgrund eines Updates wurde ein Clusterfailover ausgeführt. Dies ist ein normales Verhalten, das keine Aktion erfordert. Nachdem Sie diese Warnung bestätigt haben, löschen Sie sie von der Warnungsseite.|
|Geräte-Failover zu \<\*Gerätename\*\>.|Der Controller wurde heruntergefahren oder neu gestartet.|Ein Gerätefailover wurde ausgeführt, da der aktive Controller von einem Administrator heruntergefahren oder neu gestartet wurde. Es ist keine Aktion erforderlich. Nachdem Sie diese Warnung bestätigt haben, löschen Sie sie von der Warnungsseite.|
|Geräte-Failover zu \<\*Gerätename\*\>.|Geplantes Failover|Überprüfen Sie, ob dieses Failover geplant war. Nachdem Sie die entsprechende Aktion ausgeführt haben, löschen Sie diese Warnung von der Warnungsseite.|
|Geräte-Failover zu \<\*Gerätename\*\>.|Nicht geplantes Failover|StorSimple führt bei ungeplanten Failovers automatisch Wiederherstellungen aus. Bitte wenden Sie sich an den Microsoft Support, wenn Ihnen eine große Anzahl an Warnungen angezeigt wird.|
|Geräte-Failover zu \<\*Gerätename\*\>.|Andere/Unbekannte Ursache|Falls eine große Anzahl dieser Warnungen angezeigt wird, wenden Sie sich an den Microsoft Support. Nachdem das Problem behoben wurde, löschen Sie diese Warnung von der Warnungsseite.|

### Warnungen zur Notfallwiederherstellung

|Warnungstext|Ereignis|Weitere Informationen/Empfohlene Maßnahmen|
|:---|:---|:---|
|Mit den Wiederherstellungsvorgängen konnten nicht alle Einstellungen für diesen Dienst wiederhergestellt werden. Die Gerätekonfigurationsdaten sind für einige Geräte inkonsistent.|Nach der Notfallwiederherstellung wurde eine Dateninkonsistenz erkannt.|Verschlüsselte Daten des Diensts sind nicht mit denen auf dem Gerät synchron. Autorisieren Sie das Gerät \<\*Gerätename\*\> in StorSimple Manager, um die Synchronisierung zu starten. Führen Sie das Cmdlet **Restore-HcsmEncryptedServiceData** in der Windows PowerShell-Schnittstelle für StorSimple auf Gerät \<\*Gerätename\*\> aus; verwenden Sie dabei das alte Kennwort als Eingabe für das Cmdlet, um das Sicherheitsprofil wiederherzustellen. Führen Sie dann das Cmdlet **Invoke-HcsmServiceDataEncryptionKeyChange** aus, um den Dienst-Datenverschlüsselungsschlüssel zu aktualisieren. Nachdem Sie die entsprechende Aktion ausgeführt haben, löschen Sie diese Warnung von der Warnungsseite.|
|Für den Dienst fand aufgrund eines unerwarteten Fehlers ein Failover auf ein sekundäres Rechenzentrum statt.|Andere/Unbekannte Ursache|Sie müssen Ihre Konfigurationseinstellungen in StorSimple Manager überprüfen, um fortfahren zu können. Nachdem Sie die entsprechende Aktion ausgeführt haben, löschen Sie diese Warnung von der Warnungsseite. Weitere Informationen zu StorSimple Manager finden Sie unter [StorSimple Manager – Administratorhandbuch](https://msdn.microsoft.com/library/azure/dn772401.aspx).|

### Hardwarewarnungen

|Warnungstext|Ereignis|Weitere Informationen/Empfohlene Maßnahmen|
|:---|:---|:---|
|Hardwarekomponente \<\*Komponenten-ID\*\> berichtet Status \<\*Status\*\>.||Manchmal werden diese Warnungen durch vorübergehende Bedingungen verursacht. In diesem Fall wird die Warnung nach einiger Zeit automatisch gelöscht. Falls das Problem weiterhin besteht, wenden Sie sich an den Microsoft Support.|
|Passive Controllerfehlfunktion.|Der passive \(sekundäre\) Controller funktioniert nicht.|Ihr Gerät ist betriebsbereit, aber einer der Controller funktioniert nicht. Versuchen Sie, diesen Controller neu zu starten. Wenden Sie sich an den Microsoft Support, wenn das Problem weiterhin besteht.|

### Warnungen zu Auftragsfehlern

|Warnungstext|Ereignis|Weitere Informationen/Empfohlene Maßnahmen|
|:---|:---|:---|
|Sicherung von \<\*Quellvolume-Gruppen-ID\*\> fehlgeschlagen.|Fehler beim Sicherungsauftrag.|Der Sicherungsvorgang kann aufgrund möglicher Konnektivitätsprobleme nicht erfolgreich abgeschlossen werden. Falls keine Konnektivitätsprobleme vorliegen, ist möglicherweise die maximale Anzahl von Sicherungen erreicht. Löschen Sie nicht mehr benötigte Sicherungen, und wiederholen Sie den Vorgang. Nachdem Sie die entsprechende Aktion ausgeführt haben, löschen Sie diese Warnung von der Warnungsseite.|
|Klonen von \<\*Quellsicherungselement-IDs\*\> zu \<\*Zielvolume-Seriennummern\*\> fehlgeschlagen.|Fehler beim Klonauftrag.|Aktualisieren Sie die Sicherungsliste, um zu überprüfen, ob die Sicherung noch gültig ist. Wenn die Sicherung gültig ist, kann es an Cloudkonnektivitätsproblemen liegen, dass der Klonvorgang nicht erfolgreich abgeschlossen werden kann. Wenn keine Konnektivitätsprobleme vorliegen, haben Sie möglicherweise die Speicherbegrenzung erreicht. Löschen Sie nicht mehr benötigte Sicherungen, und wiederholen Sie den Vorgang. Nachdem Sie das Problem mit der entsprechenden Aktion behoben haben, löschen Sie diese Warnung von der Warnungsseite.|
|Wiederherstellen von \<\*Quellsicherungselement-IDs\*\> fehlgeschlagen.|Fehler bei Wiederherstellungsauftrag.|Aktualisieren Sie die Sicherungsliste, um zu überprüfen, ob die Sicherung noch gültig ist. Wenn die Sicherung gültig ist, kann es an Cloudkonnektivitätsproblemen liegen, dass der Wiederherstellungsvorgang nicht erfolgreich abgeschlossen werden kann. Wenn keine Konnektivitätsprobleme vorliegen, haben Sie möglicherweise die Speicherbegrenzung erreicht. Löschen Sie nicht mehr benötigte Sicherungen, und wiederholen Sie den Vorgang. Nachdem Sie das Problem mit der entsprechenden Aktion behoben haben, löschen Sie diese Warnung von der Warnungsseite.|

### Leistungswarnungen

|Warnungstext|Ereignis|Weitere Informationen/Empfohlene Maßnahmen|
|:---|:---|:---|
|Die Gerätelast hat \<\*Schwellenwert\*\> überschritten.|Die Reaktionszeiten sind langsamer als erwartet.|Ihr Gerät meldet die Nutzung unter hoher Eingabe-/Ausgabeauslastung. Dies kann dazu führen, dass das Gerät nicht optimal funktioniert. Überprüfen Sie die Workloads des Geräts, und ermitteln Sie, ob einige auf ein anderes Gerät verschoben werden können oder nicht mehr erforderlich sind.|

### Sicherheitswarnungen

|Warnungstext|Ereignis|Weitere Informationen/Empfohlene Maßnahmen|
|:---|:---|:---|
|Microsoft Support-Sitzung hat begonnen.|Zugriff auf Supportsitzung durch Drittanbieter.|Bestätigen Sie, dass dieser Zugriff autorisiert ist. Nachdem Sie die entsprechende Aktion ausgeführt haben, löschen Sie diese Warnung von der Warnungsseite.|
|Kennwort für \<\*Element\*\> läuft in \<\*Zeitraum\*\> aus.||Ändern Sie das Kennwort, bevor es abläuft.|
|Sicherheitskonfigurationsinformationen fehlen für \<\*Element-ID\*\>.||Die diesem Volumecontainer zugeordneten Volumes können nicht zum Replizieren Ihrer StorSimple-Konfiguration verwendet werden. Um die sichere Speicherung Ihrer Daten sicherzustellen, empfehlen wir, den Volumecontainer und alle diesem zugeordneten Volumes zu löschen. Nachdem Sie die entsprechende Aktion ausgeführt haben, löschen Sie diese Warnung von der Warnungsseite.|
|\<\*Anzahl\*\> Anmeldeversuche für \<\*Element-ID\*\> fehlgeschlagen.|Fehler bei mehreren Anmeldeversuchen.|Ihr Gerät wurde möglicherweise angegriffen, oder ein nicht autorisierter Benutzer versucht, sich mit einem falschen Kennwort anzumelden.<ul><li>Überprüfen Sie bei Ihren autorisierten Benutzern, ob diese Versuche von einer vertrauenswürdigen Quelle stammen. Wenn es weiterhin zu einer Vielzahl von fehlerhaften Anmeldeversuchen kommt, deaktivieren Sie ggf. die Remoteverwaltung, und wenden Sie sich an Ihren Netzwerkadministrator. Nachdem Sie die entsprechende Aktion ausgeführt haben, löschen Sie diese Warnung von der Warnungsseite.</li><li>Überprüfen Sie, ob Ihre Snapshot Manager-Instanzen mit dem richtigen Kennwort konfiguriert sind. Nachdem Sie die entsprechende Aktion ausgeführt haben, löschen Sie diese Warnung von der Warnungsseite.</li></ul>|
|Ein oder mehrere Fehler beim Ändern des Dienst-Datenverschlüsselungsschlüssels.||Beim Ändern des Dienst-Datenverschlüsselungsschlüssels sind Fehler aufgetreten. Beheben Sie die Fehler, und führen Sie anschließend das Cmdlet **Invoke-HcsmServiceDataEncryptionKeyChange** in der Windows PowerShell-Schnittstelle für StorSimple auf Ihrem Gerät aus, um den Dienst zu aktualisieren. Falls das Problem weiterhin besteht, wenden Sie sich an den Microsoft Support. Nachdem das Problem behoben wurde, löschen Sie diese Warnung von der Warnungsseite.|

### Warnungen zum Supportpaket

|Warnungstext|Ereignis|Weitere Informationen/Empfohlene Maßnahmen|
|:---|:---|:---|
|Erstellung des Unterstützungspakets fehlgeschlagen.|StorSimple konnte das Paket nicht generieren.|Wiederholen Sie diesen Vorgang. Falls das Problem weiterhin besteht, wenden Sie sich an den Microsoft Support. Nachdem das Problem behoben wurde, löschen Sie diese Warnung von der Warnungsseite.|

### Testwarnungen

|Warnungstext|Ereignis|Weitere Informationen/Empfohlene Maßnahmen|
|:---|:---|:---|
|Dies ist eine Testnachricht, die von Ihrem StorSimple-Gerät gesendet wurde. Der StorSimple-Administrator hat Sie als Empfänger von Warnungsbenachrichtigungen für das Gerät hinzugefügt: \<\*Gerätename\*\>.|E-Mail zum Testen der Warnungsbenachrichtigung.|Wenden Sie sich an Ihren StorSimple-Administrator, wenn Sie der Ansicht sind, dass Sie diese Nachricht irrtümlich erhalten haben.|

### Updatewarnungen

|Warnungstext|Ereignis|Weitere Informationen/Empfohlene Maßnahmen|
|:---|:---|:---|
|Hotfix installiert.|Software/Firmwareupdate wurde abgeschlossen.|Hotfix wurde erfolgreich auf dem Gerät installiert.|
|Manuelle Updates verfügbar.|Benachrichtigung über verfügbare Updates.|Installieren Sie diese Updates mithilfe der Windows PowerShell-Schnittstelle für StorSimple auf Ihrem Gerät.|
|Neue Updates verfügbar.|Benachrichtigung über verfügbare Updates.|Sie können diese Updates entweder über die **Wartungsseite** oder mit der Windows PowerShell-Schnittstelle für StorSimple auf Ihrem Gerät installieren.|
|Updates konnten nicht installiert werden.|Die Installation der Updates war nicht erfolgreich.|Die Updates konnten nicht installiert werden. Sie können diese Updates entweder über die **Wartungsseite** oder mit der Windows PowerShell-Schnittstelle für StorSimple auf Ihrem Gerät installieren. Wenn das Problem weiterhin besteht, wenden Sie sich an den Microsoft Support.|
|Automatisch nach Updates suchen nicht möglich.|Fehler bei der automatischen Überprüfung.|Auf der **Wartungsseite** können Sie manuell nach Updates suchen.|
|Neues Update für den WUA-Agent verfügbar.|Benachrichtigung über ein verfügbares Update.|Laden Sie den aktuellen Windows Update-Agent herunter, und installieren Sie ihn über die Windows PowerShell-Schnittstelle.|
|Version der Firmwarekomponente \<\*Komponenten-ID\*\> entspricht nicht der Hardware.|Firmwareupdates wurden nicht erfolgreich installiert.|Wenden Sie sich an den Microsoft Support.|

## Nächste Schritte

[Weitere Informationen zu StorSimple-Fehlern](storsimple-troubleshoot-operational-device.md)

<!---HONumber=July15_HO5-->