<properties 
   pageTitle="Anzeigen und Verwalten von StorSimple Virtual Array-Warnungen | Microsoft Azure"
   description="Es werden die Warnungsbedingungen und -schweregrade für StorSimple Virtual Array beschrieben, und Sie erfahren, wie Sie den StorSimple Manager-Dienst zum Verwalten von Warnungen verwenden."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/18/2016"
   ms.author="v-sharos" />

# Verwenden des StorSimple Manager-Diensts zum Anzeigen und Verwalten von Warnungen für das StorSimple Virtual Array (Vorschau)

## Übersicht

Auf der Registerkarte **Warnungen** des StorSimple Manager-Diensts können Sie Warnungen zum StorSimple Virtual Array in Echtzeit anzeigen und löschen. Auf dieser Registerkarte können Sie die Integritätsprobleme Ihrer StorSimple Virtual Arrays (auch als lokale virtuelle StorSimple-Geräte bezeichnet) und die Microsoft Azure StorSimple-Gesamtlösung zentral überwachen.

In diesem Tutorial wird beschrieben, wie Sie Warnungsbenachrichtigungen, häufige Warnungsbedingungen und Warnungsschweregrade konfigurieren und Warnungen anzeigen und nachverfolgen. Außerdem sind Tabellen mit Kurzübersichten zu Warnungen vorhanden, in denen Sie eine bestimmte Warnung schnell finden und entsprechend reagieren können.

![Seite „Warnungen“](./media/storsimple-ova-manage-alerts/alerts1.png)

## Warnungseinstellungen konfigurieren

Sie können auswählen, ob Sie per E-Mail über die Warnungsbedingungen für die einzelnen virtuellen StorSimple-Geräte informiert werden möchten. Sie können auch andere Empfänger von Warnungsbenachrichtigungen angeben, indem Sie deren E-Mail-Adressen im Feld **Andere E-Mail-Empfänger** durch Semikolons getrennt eingeben.

>[AZURE.NOTE] Sie können maximal 20 E-Mail-Adressen pro virtuellem Gerät eingeben.

Nachdem Sie die E-Mail-Benachrichtigung für ein virtuelles Gerät aktiviert haben, erhalten die Mitglieder der Benachrichtigungsliste jedes Mal eine E-Mail-Nachricht, wenn eine kritische Warnung vorliegt. Die Nachrichten werden von der Adresse *storsimple-alerts-noreply@mail.windowsazure.com* gesendet und enthalten eine Beschreibung der Warnungsbedingung. Empfänger können auf **Abonnement kündigen** klicken, um das Entfernen aus der E-Mail-Benachrichtigungsliste durchzuführen.

#### So aktivieren Sie E-Mail-Benachrichtigungen über Warnungen für ein virtuelles Gerät

1. Wechseln Sie für das virtuelle Gerät zu **Geräte** > **Konfiguration**. Wechseln Sie zum Abschnitt **Warnungseinstellungen**.

    ![Warnungseinstellungen](./media/storsimple-ova-manage-alerts/alerts2.png)

2. Legen Sie unter **Warnungseinstellungen** Folgendes fest:

    1. Klicken Sie im Feld **E-Mail-Benachrichtigung senden** auf **JA**.

    2. Klicken Sie im Feld **E-Mail an Dienstadministratoren** auf **JA**, wenn der Dienstadministrator und alle Co-Administratoren die Warnungsbenachrichtigungen erhalten sollen.

    3. Geben Sie im Feld **Andere E-Mail-Empfänger** die E-Mail-Adressen aller weiteren Empfänger ein, die die Warnungsbenachrichtigungen erhalten sollen. Geben Sie Namen im folgenden Format ein: *someone@somewhere.com*. Verwenden Sie Semikolons, um mehrere E-Mail-Adressen zu trennen. Sie können maximal 20 E-Mail-Adressen pro virtuellem Gerät konfigurieren.

        ![Konfiguration von Warnungsbenachrichtigungen](./media/storsimple-ova-manage-alerts/alerts3.png)

3. Klicken Sie unten auf der Seite auf **Speichern**, um Ihre Konfiguration zu speichern.

4. Um eine E-Mail-Benachrichtigung als Test zu senden, klicken Sie auf das Pfeilsymbol neben **Test-E-Mail senden**. Der StorSimple Manager-Dienst zeigt beim Weiterleiten der Testbenachrichtigung Statusmeldungen an.

5. Wenn die folgende Meldung angezeigt wird, klicken Sie auf **OK**.

    ![E-Mail zum Testen der Warnungsbenachrichtigung](./media/storsimple-ova-manage-alerts/alerts4.png)

    >[AZURE.NOTE] Wenn die Testbenachrichtigung nicht gesendet werden kann, zeigt der StorSimple Manager-Dienst eine entsprechende Meldung an. Klicken Sie auf **OK**, warten Sie einige Minuten, und versuchen Sie dann, die Testnachricht erneut zu senden.

    Die Testbenachrichtigung sieht in etwa wie hier dargestellt aus:

    ![Beispiel für Test-E-Mail für Warnungen](./media/storsimple-ova-manage-alerts/alerts5.png)

## Häufige Warnungsbedingungen

Ihr StorSimple Virtual Array generiert Warnungen als Reaktion auf viele verschiedene Bedingungen. Folgende Arten von Warnungsbedingungen kommen am häufigsten vor:

- **Konnektivitätsprobleme**: Diese Warnungen werden angezeigt, wenn es beim Übertragen von Daten zu Schwierigkeiten kommt. Kommunikationsprobleme können während der Übertragung von Daten auf das bzw. aus dem Azure-Speicherkonto oder aufgrund einer fehlenden Verbindung zwischen den virtuellen Geräten und dem StorSimple Manager-Dienst auftreten. Die Behebung von Kommunikationsproblemen ist besonders schwierig, da die Anzahl der möglichen Fehlerquellen hoch ist. Sie sollten sich immer zuerst vergewissern, dass die Netzwerkverbindung und der Internetzugriff verfügbar sind, bevor Sie sich mit der eingehenderen Problembehandlung beschäftigen. Informationen zu Ports und Firewalleinstellungen finden Sie unter [Systemanforderungen für StorSimple Virtual Array](storsimple-ova-system-requirements.md). Weitere Informationen zur Problembehandlung finden Sie unter [Problembehandlung mit dem Cmdlet „Test-Connection“](storsimple-troubleshoot-deployment.md).

- **Leistungsprobleme** – Diese Warnungen werden verursacht, wenn Ihr System keine optimale Leistung aufweist, z. B. bei einer hohen Auslastung.

Außerdem werden unter Umständen Warnungen zur Sicherheit, zu Updates oder zu Fehlern bei Aufträgen angezeigt.

## Schweregrade von Warnungen

Warnungen haben unterschiedliche Schweregrade. Diese richten sich nach den Auswirkungen, die mit einer Warnungssituation verbunden sind, sowie danach, wie dringend eine Reaktion auf die Warnung erfolgen muss. Die Schweregrade lauten:

- **Kritisch** – Diese Warnung wird als Reaktion auf einen Zustand ausgegeben, der den erfolgreichen Betrieb Ihres Systems beeinträchtigt. Es ist eine Aktion erforderlich, um sicherzustellen, dass der StorSimple-Dienst nicht unterbrochen wird.

- **Warnung** – Dieser Zustand kann kritisch werden, wenn er nicht behoben wird. Sie sollten den Fall untersuchen und alle Maßnahmen ergreifen, die zum Beheben des Problems erforderlich sind.

- **Information** – Diese Warnung enthält Informationen, die für die Nachverfolgung und Verwaltung Ihres Systems hilfreich sind.

## Anzeigen und Nachverfolgen von Warnungen

Im Dashboard des StorSimple Manager-Diensts behalten Sie den Überblick über die Anzahl der Warnungen auf den virtuellen Geräten, die unterteilt nach Schweregrad angezeigt werden.

![Dashboard „Warnungen“](./media/storsimple-ova-manage-alerts/alerts6.png)

Wenn Sie auf den Schweregrad klicken, wird die Registerkarte **Warnungen** geöffnet. Die Ergebnisse umfassen nur die Warnungen des jeweiligen Schweregrads.

![Warnungsbericht nach Warnungstyp](./media/storsimple-ova-manage-alerts/alerts7.png)

Wenn Sie in der Liste auf eine Warnung klicken, werden weitere Details zur Warnung angezeigt, z. B. die letzte Meldung der Warnung, die Häufigkeit der Warnung auf dem Gerät und die empfohlene Maßnahme zum Beheben der Warnung.

Sie können die Details der Warnung in eine Textdatei kopieren, wenn Sie die Informationen an den Microsoft Support senden möchten. Nachdem Sie die Empfehlung befolgt und die Warnungsbedingung lokal behoben haben, sollten Sie die Warnung löschen, indem Sie sie auf der Registerkarte **Warnungen** markieren und auf **Löschen** klicken. Zum Löschen mehrerer Warnungen wählen Sie die einzelnen Warnungen aus und klicken auf eine beliebige Spalte (außer der Spalte **Warnung**). Klicken Sie dann auf **Löschen**, nachdem Sie alle zu löschenden Warnungen ausgewählt haben. Beachten Sie, dass einige Warnungen automatisch gelöscht werden, wenn das Problem behoben ist oder wenn die Warnung mit neuen Informationen aktualisiert wird.

Wenn Sie auf **Löschen** klicken, können Sie Kommentare zur Warnung und die Schritte zur Behebung des Problems angeben.

![Warnungskommentare](./media/storsimple-ova-manage-alerts/clear-alert.png)

Klicken Sie auf das Häkchensymbol ![Häkchensymbol](./media/storsimple-ova-manage-alerts/check-icon.png), um Ihre Kommentare zu speichern.

Einige Ereignisse werden gelöscht, falls ein anderes Ereignis mit neuen Informationen ausgelöst wird. In diesem Fall wird die folgende Meldung angezeigt:

![Warnungsmeldung löschen](./media/storsimple-ova-manage-alerts/alerts8.png)

## Sortieren und Überprüfen von Warnungen

Unter Umständen finden Sie es effizienter, Berichte für Warnungen auszuführen, damit Sie sie in Gruppen überprüfen und löschen können. Außerdem können auf der Registerkarte **Warnungen** bis zu 250 Warnungen angezeigt werden. Wenn Sie diese Anzahl von Warnungen überschritten haben, werden nicht alle Warnungen in der Standardansicht angezeigt. Sie können die folgenden Felder kombinieren, um anzupassen, welche Warnungen angezeigt werden:

- **Status** – Sie können Warnungen der Kategorien **Aktiv** oder **Gelöscht** anzeigen. Aktive Warnungen werden weiterhin ausgelöst, während gelöschte Warnungen entweder manuell von einem Administrator oder programmgesteuert gelöscht wurden, weil die Warnungsbedingung mit neuen Informationen aktualisiert wurde.

- **Schweregrad** – Sie können Warnungen aller Schweregrade (Kritisch, Warnung, Information) oder nur einen bestimmten Schweregrad anzeigen, z. B. nur kritische Warnungen.

- **Quelle** – Sie können Warnungen für alle Quellen anzeigen oder die Anzeige auf die Warnungen beschränken, die entweder vom Dienst oder von einem oder allen virtuellen Geräten stammen.

- **Zeitraum** – Wenn Sie das Datum bzw. den Zeitstempel mit **Von** und **Bis** angeben, können Sie Warnungen für den gewünschten Zeitraum anzeigen.

## Kurzübersicht zu Warnungen

In den folgenden Tabellen sind einige Microsoft Azure StorSimple-Warnungen aufgeführt, die unter Umständen angezeigt werden, sowie weitere Informationen und Empfehlungen, falls verfügbar. Warnungen für virtuelle StorSimple-Geräte fallen in eine der folgenden Kategorien:

- [Warnungen zur Cloudkonnektivität](#cloud-connectivity-alerts)

- [Konfigurationswarnungen](#configuration-alerts)

- [Warnungen zu Auftragsfehlern](#job-failure-alerts)

- [Leistungswarnungen](#performance-alerts)

- [Sicherheitswarnungen](#security-alerts)

- [Updatewarnungen](#update-alerts)

### Warnungen zur Cloudkonnektivität

|Warnungstext|Ereignis|Weitere Informationen/Empfohlene Maßnahmen|
|:---|:---|:---|
|Für das Gerät *<device name>* besteht keine Verbindung mit der Cloud.|Für das benannte Gerät kann keine Verbindung mit der Cloud hergestellt werden. |Es konnte keine Verbindung mit der Cloud hergestellt werden. Dies kann einen der folgenden Gründe haben:<ul><li>Unter Umständen liegt ein Problem mit den Netzwerkeinstellungen auf Ihrem Gerät vor.</li><li>Unter Umständen liegt ein Problem mit den Anmeldeinformationen für das Speicherkonto vor.</li></ul>Weitere Informationen zur Behandlung von Verbindungsproblemen finden Sie auf der [lokalen Webbenutzeroberfläche](storsimple-ova-web-ui-admin.md) des Geräts.|

#### Verhalten von StorSimple beim Ausfall der Cloudverbindung

Was geschieht beim Ausfall der Cloudverbindung des StorSimple-Geräts in der Produktionsumgebung?

Wenn die Cloudverbindung für das StorSimple-Gerät in der Produktion ausfällt, können je nach Status des Geräts die folgenden Umstände eintreten:

- **Für die lokalen Daten auf dem Gerät**: Es tritt für einige Zeit keine Unterbrechung auf, und Lesevorgänge werden nach wie vor bedient. Bei ansteigender Anzahl ausstehender EA-Vorgänge und Überschreitung eines Grenzwerts kann es jedoch zu Ausfällen bei Lesevorgängen kommen. 

	Abhängig von der Menge der Daten auf dem Gerät werden innerhalb der ersten Stunden nach der Unterbrechung der Cloudverbindung auch Schreibvorgänge weiterhin ausgeführt. Die Schreibvorgänge werden dann langsamer, und es treten erste Fehler auf, wenn die Cloudverbindung für mehrere Stunden getrennt ist. Auf dem Gerät ist temporärer Speicherplatz für Daten vorhanden, die in die Cloud verschoben werden sollen. Dieser Bereich wird geleert, wenn die Daten gesendet werden. Bei einem Verbindungsfehler werden Daten in diesem Speicherbereich nicht in die Cloud übertragen, und ein E/A-Fehler tritt auf.
 
- **Für die in der Cloud gespeicherten Daten**: Für die meisten Cloudverbindungsfehler wird eine Fehlermeldung zurückgegeben. Sobald die Verbindung wiederhergestellt ist, werden die EA-Vorgänge wieder aufgenommen, ohne dass der Benutzer das Volume online schalten muss. In seltenen Fällen ist unter Umständen ein Benutzereingriff erforderlich, um das Volume aus dem klassischen Azure-Portal wieder online zu schalten.
 
- **Für aktuell ausgeführte Cloudmomentaufnahmen**: Der Vorgang wird innerhalb von 4–5 Stunden mehrfach erneut versucht, und wenn die Verbindung nicht wiederhergestellt wird, tritt ein Fehler bei den Cloudmomentaufnahmen auf.

### Konfigurationswarnungen

|Warnungstext|Ereignis|Weitere Informationen/Empfohlene Maßnahmen|
|:---|:---|:---|
|Die Konfiguration für lokale virtuelle Geräte wird nicht unterstützt.|Die Leistung ist langsam.|Die aktuelle Konfiguration kann zu Leistungseinbußen führen. Stellen Sie sicher, dass Ihr Server die Mindestanforderungen an die Konfiguration erfüllt. Weitere Informationen finden Sie unter [StorSimple Virtual Array-Anforderungen](storsimple-ova-system-requirements.md). 
|Der bereitgestellte Festplattenspeicher auf <*Name des Geräts*> ist in Kürze erschöpft.|Warnung zum Festplattenspeicher.|Der bereitgestellte Festplattenspeicher ist in Kürze erschöpft. Erwägen Sie die Verschiebung von Workloads auf andere Volumes oder Freigaben oder das Löschen von Daten, um mehr Speicherplatz bereitzustellen.

### Warnungen zu Auftragsfehlern

|Warnungstext|Ereignis|Weitere Informationen/Empfohlene Maßnahmen|
|:---|:---|:---|
|Die Sicherung von <*Name des Geräts*> konnte nicht abgeschlossen werden.|Fehler beim Sicherungsauftrag.|Eine Sicherung konnte nicht erstellt werden. Erwägen Sie eine der folgenden Maßnahmen:<ul><li>Der Sicherungsvorgang kann aufgrund möglicher Konnektivitätsprobleme nicht erfolgreich abgeschlossen werden. Stellen Sie sicher, dass keine Konnektivitätsprobleme vorliegen. Weitere Informationen zur Behandlung von Verbindungsproblemen finden Sie auf der [lokalen Webbenutzeroberfläche](storsimple-ova-web-ui-admin.md) Ihres virtuellen Geräts.</li><li>Sie haben das verfügbare Speicherlimit erreicht. Erwägen Sie, nicht mehr benötigte Sicherungen zu löschen, um mehr Speicherplatz bereitzustellen.</li></ul> Beheben Sie die Probleme, löschen Sie die Warnung, und versuchen Sie, den Vorgang zu wiederholen.|
|Die Wiederherstellung von <*Name des Geräts*> konnte nicht abgeschlossen werden.|Fehler bei Wiederherstellungsauftrag.|Die Daten konnten nicht aus der Sicherung wiederhergestellt werden. Erwägen Sie eine der folgenden Maßnahmen:<ul><li>Unter Umständen ist Ihre Sicherungsliste nicht gültig. Aktualisieren Sie die Liste, um zu überprüfen, ob sie gültig ist.</li><li>Unter Umständen wird durch Verbindungsprobleme verhindert, dass der Wiederherstellungsvorgang erfolgreich abgeschlossen wird. Stellen Sie sicher, dass keine Konnektivitätsprobleme vorliegen.</li><li>Sie haben das verfügbare Speicherlimit erreicht. Erwägen Sie, nicht mehr benötigte Sicherungen zu löschen, um mehr Speicherplatz bereitzustellen.</li></ul>Lösen Sie die Probleme, löschen Sie die Warnung, und versuchen Sie, den Wiederherstellungsvorgang zu wiederholen.|
|Das Klonen von <*Name des Geräts*> konnte nicht abgeschlossen werden.|Fehler beim Klonauftrag.|Ein Klon konnte nicht erstellt werden. Erwägen Sie eine der folgenden Maßnahmen:<ul><li>Unter Umständen ist Ihre Sicherungsliste nicht gültig. Aktualisieren Sie die Liste, um zu überprüfen, ob sie gültig ist.</li><li>Unter Umständen wird durch Verbindungsprobleme verhindert, dass der Klonvorgang erfolgreich abgeschlossen wird. Stellen Sie sicher, dass keine Konnektivitätsprobleme vorliegen.</li><li>Sie haben das verfügbare Speicherlimit erreicht. Erwägen Sie, nicht mehr benötigte Sicherungen zu löschen, um mehr Speicherplatz bereitzustellen.</li></ul>Lösen Sie die Probleme, löschen Sie die Warnung, und versuchen Sie, den Vorgang zu wiederholen.|

### Leistungswarnungen

|Warnungstext|Ereignis|Weitere Informationen/Empfohlene Maßnahmen|
|:---|:---|:---|
|Bei der Datenübertragung kommt es zu unerwarteten Verzögerungen.|Langsame Datenübertragung.|Drosselungsfehler treten auf, wenn Sie die Skalierbarkeitsziele eines Speicherdiensts überschreiten. Der Speicherdienst tut dies, um sicherzustellen, dass kein Einzelclient oder Mandant diesen Dienst auf Kosten anderer verwenden kann. Weitere Informationen zur Problembehandlung für Ihr Azure-Speicherkonto finden Sie unter [Microsoft Azure-Speicher: Überwachung, Diagnose und Problembehandlung](storage-monitoring-diagnosing-troubleshooting.md).
|Der lokale reservierte Festplattenspeicher auf <*Name des Geräts*> ist in Kürze erschöpft.|Langsame Reaktion.|10 % des insgesamt bereitgestellten Speicherplatzes für <*Name des Geräts*> ist auf dem lokalen Gerät reserviert, und dieser reservierte Speicherplatz ist in Kürze erschöpft. Die Workload auf <*Name des Geräts*> generiert eine höhere Änderungsrate, oder Sie haben kürzlich eine große Menge von Daten migriert. Dies kann zu Leistungseinbußen führen. Wählen Sie eine der folgenden Vorgehensweisen, um dieses Problem zu lösen:<ul><li>Erhöhen Sie die Cloudbandbreite für dieses Gerät.</li><li>Reduzieren Sie die Workloads, oder verschieben Sie sie auf ein anderes Volume oder eine andere Freigabe.</li></ul>

### Sicherheitswarnungen

|Warnungstext|Ereignis|Weitere Informationen/Empfohlene Maßnahmen|
|:---|:---|:---|
|Das Kennwort für <*Name des Geräts*> läuft in <*Anzahl*> Tagen ab.|Kennwortwarnung.| Ihr Kennwort läuft in <Anzahl> Tagen ab. Sie sollten Ihr Kennwort ändern. Weitere Informationen finden Sie unter [Ändern des StorSimple Virtual Array-Geräteadministratorkennworts](storsimple-ova-change-device-admin-password.md).

### Updatewarnungen

|Warnungstext|Ereignis|Weitere Informationen/Empfohlene Maßnahmen|
|:---|:---|:---|
|Für Ihr Gerät sind neue Updates verfügbar.|Für das StorSimple Virtual Array sind Updates verfügbar.|Sie können neue Updates über die Seite **Wartung** installieren.|
|Auf <*Name des Geräts*> konnte nicht nach neuen Updates gesucht werden.|Fehler beim Update. |Beim Installieren neuer Updates ist ein Fehler aufgetreten. Sie können die Updates manuell installieren. Wenden Sie sich an den [Microsoft Support](storsimple-contact-microsoft-support.md), wenn das Problem weiterhin besteht.|


## Nächste Schritte

- [Erfahren Sie mehr über das StorSimple Virtual Array](storsimple-ova-overview.md).

<!----HONumber=AcomDC_0224_2016-->