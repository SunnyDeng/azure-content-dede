<properties 
   pageTitle="Verwalten von StorSimple-Bandbreitenvorlagen | Microsoft Azure"
   description="Beschreibt die Verwaltung von StorSimple-Bandbreitenvorlagen, mit denen Sie die Auslastung der Netzwerkbandbreite steuern."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/12/2015"
   ms.author="alkohli" />

# Verwalten von StorSimple-Bandbreitenvorlagen mithilfe des StorSimple Manager-Diensts

## Übersicht

Bandbreitenvorlagen ermöglichen Ihnen, mehrere Zeitpläne für bestimmte Tageszeiten einzurichten, um den Datenverkehr vom StorSimple-Gerät zur Cloud zu konfigurieren. Sie können diese Zeitpläne auch erstellen, ändern, löschen und als Vorlagen speichern. Diese Bandbreitenvorlagen können dann mehrere Volumecontainer übergreifend zur Steuerung der Bandbreite angewendet werden, die das StorSimple-Gerät zum Ausführen von Cloudvorgängen benötigt. Je nach Bandbreiten-Nutzungsmuster können Sie auch aus einer Liste von Standardvorlagen auswählen.

Mit Zeitplänen zur Bandbreiteneinschränkung können Sie folgende Aktionen ausführen:

- Geben Sie Zeitpläne an, die die Auslastung der Netzwerkbandbreite abhängig von der Workload anpassen.

- Zentralisieren Sie die Verwaltung und verwenden Sie die Zeitpläne wiederholt einfach und nahtlos auf mehreren Geräten.

Dieses Feature steht nur für physische StorSimple-Geräte und nicht für virtuelle Geräte zur Verfügung. Alle Bandbreitenvorlagen für den Dienst werden in einem Tabellenformat angezeigt und enthalten die folgenden Informationen:

- **Name:** der eindeutige Name für das Konto, der bei dessen Erstellung zugewiesen wurde.

- **Zeitplan:** die Anzahl der Zeitpläne in einer angegebenen Bandbreitenvorlage.

- **Used by:** die Anzahl der Datenträger, die das Speicherkonto verwenden.

Verwenden Sie die Seite **Konfigurieren** des StorSimple Manager-Diensts im Verwaltungsportal, um die Bandbreitenvorlagen zu verwalten. Im Folgenden werden die häufigsten auf dieser Seite ausgeführten Aufgaben im Zusammenhang mit Bandbreitenvorlagen aufgeführt:

- Hinzufügen einer Bandbreitenvorlage
- Bearbeiten einer Bandbreitenvorlage
- Löschen einer Bandbreitenvorlage
- Verwenden einer Standardbandbreitenvorlage
- Erstellen einer Bandbreitenvorlage für einen gesamten Tag, die zu einem bestimmten Zeitpunkt startet

Zusätzliche Informationen zur Konfiguration von Bandbreitenvorlagen finden Sie in den folgenden Ressourcen:

- Fragen und Antworten zu Bandbreitenvorlagen
- Bewährte Methoden für Bandbreitenvorlagen

## Hinzufügen einer Bandbreitenvorlage

Führen Sie die folgenden Schritte aus, um eine Bandbreitenvorlage zu erstellen.

#### So fügen Sie eine Bandbreitenvorlage hinzu

1. Klicken Sie auf der Seite **Konfigurieren** des StorSimple Manager-Diensts auf **Bandbreitenvorlage hinzufügen/bearbeiten**.

2. Im Dialogfeld **Bandbreitenvorlage hinzufügen/bearbeiten** führen Sie die folgenden Schritte aus:

   1. Wählen Sie aus der Dropdownliste **Vorlage** die Option **Neu erstellen** aus, um eine neue Bandbreitenvorlage hinzuzufügen.
   2. Geben Sie einen eindeutigen Namen für Ihre Bandbreitenvorlage an.

3. Definieren Sie einen **Bandbreitenzeitplan**. So erstellen Sie einen Zeitplan

   1. Wählen Sie in der Dropdownliste die Wochentage aus, für die der Zeitplan konfiguriert werden soll. Sie können mehrere Tage auswählen, indem Sie die Kontrollkästchen vor den jeweiligen Tagen in der Liste aktivieren.
   2. Wählen Sie die Option **Gesamter Tag** aus, wenn der Zeitplan für den ganzen Tag erzwungen werden soll. Wenn diese Option aktiviert ist, können Sie keine **Startzeit** oder **Endzeit** mehr angeben. Der Zeitplan wird von 0:00 Uhr bis 23:59 Uhr ausgeführt.
   3. Wählen Sie in der Dropdownliste eine **Startzeit** aus. Dies liegt den Beginn des Zeitplans fest.
   4. Wählen Sie in der Dropdownliste eine **Endzeit** aus. Dies liegt das Ende des Zeitplans fest.
   
         > [AZURE.NOTE] Overlapping schedules are not allowed. If the start and end times will result in an overlapping schedule, you will see an error message to that effect.

   5. Geben Sie die **Bandbreitenrate** an. Dies ist die Bandbreite in Megabit pro Sekunde (MBit/s), die Ihr StorSimple-Gerät bei Cloudvorgängen verwendet. Geben Sie eine Zahl zwischen 1 und 1000 in dieses Feld ein.
   
   6. Klicken Sie auf das Häkchensymbol ![Häkchensymbol](./media/storsimple-manage-bandwidth-templates/HCS_CheckIcon.png). Die Vorlage, die Sie erstellt haben, wird der Liste der Bandbreitenvorlagen auf der Seite **Konfigurieren** des Diensts hinzugefügt.

    ![Erstellen einer neuen Bandbreitenvorlage](./media/storsimple-manage-bandwidth-templates/HCS_CreateNewBT1.png)

4. Klicken Sie am unteren Rand der Seite auf **Speichern**, und klicken Sie dann auf **Ja**, wenn Sie zur Bestätigung aufgefordert werden. Dadurch werden die vorgenommenen Konfigurationsänderungen gespeichert.

## Bearbeiten einer Bandbreitenvorlage

Führen Sie die folgenden Schritte aus, um eine Bandbreitenvorlage zu bearbeiten.

### So bearbeiten Sie eine Bandbreitenvorlage

1. Klicken Sie auf **Bandbreitenvorlage hinzufügen/bearbeiten**.

2. Im Dialogfeld **Bandbreitenvorlage hinzufügen/bearbeiten** führen Sie die folgenden Schritte aus:

   1. Wählen Sie in der Dropdownliste **Vorlage** eine vorhandene Bandbreitenvorlage aus, die Sie ändern möchten.
   2. Nehmen Sie die Änderungen vor. (Sie können alle vorhandenen Einstellungen ändern.)
   3. Klicken Sie auf das Häkchensymbol ![Häkchensymbol](./media/storsimple-manage-bandwidth-templates/HCS_CheckIcon.png). Sie sehen die geänderte Vorlage in der Liste der Bandbreitenvorlagen auf der Konfigurationsseite des Diensts.

3. Klicken Sie unten auf der Seite auf **Speichern**, um die Änderungen zu speichern. Wenn Sie zur Bestätigung aufgefordert werden, klicken Sie auf **Ja**.

> [AZURE.NOTE]Sie können die Änderungen nicht speichern, wenn sich der bearbeitete Zeitplan mit einem vorhandenen Zeitplan in der Bandbreitenvorlage überschneidet, die Sie ändern.

## Löschen einer Bandbreitenvorlage

Führen Sie die folgenden Schritte aus, um eine Bandbreitenvorlage zu löschen.

#### So löschen Sie eine Bandbreitenvorlage

1. Wählen Sie die Vorlage, die Sie löschen möchten, in der Tabellenliste der Bandbreitenvorlagen für Ihren Dienst aus. Rechts neben der ausgewählten Vorlage wird ein Löschsymbol (**x**) angezeigt. Klicken Sie auf das Symbol **x**, um die Vorlage zu löschen.

2. Sie werden aufgefordert, diesen Schritt zu bestätigen. Klicken Sie zum Fortfahren auf **OK**.

Wenn die Vorlage von einem Volume verwendet wird, kann sie nicht gelöscht werden. Sie erhalten eine Fehlermeldung, dass die Vorlage verwendet wird. Ein Dialogfeld mit einer Fehlermeldung wird angezeigt, in der Sie darauf hingewiesen werden, alle Verweise auf die Vorlage zu entfernen.

Sie können alle Verweise auf die Vorlage löschen, indem Sie auf die Seite **Volumecontainer** zugreifen und die Volumecontainer bearbeiten, die diese Vorlage verwenden, sodass diese eine andere Vorlage oder eine benutzerdefinierte Einstellung bzw. unbegrenzte Bandbreite verwenden. Wenn alle Verweise entfernt wurden, können Sie die Vorlage löschen.

## Verwenden einer Standardbandbreitenvorlage

Eine Standardbandbreitenvorlage wird bereitgestellt, die standardmäßig von Volumecontainern verwendet wird, um Bandbreitensteuerung für den Cloudzugriff zu erzwingen. Die Standardvorlage dient auch als Referenz für Benutzer, die ihre eigenen Vorlagen erstellen. Im Folgenden sind Details dieser Standardvorlage aufgeführt:

- **Name:** unbegrenzte Nächte und Wochenenden

- **Zeitplan:** ein einzelner Zeitplan von Montag bis Freitag, der eine Bandbreite von 1 MBit/s zwischen 8:00 Uhr und 17:00 Uhr Gerätezeit festlegt. Die Bandbreite wird für den Rest der Woche auf "Unbegrenzt" festgelegt.

Die Standardvorlage kann bearbeitet werden. Die Verwendung dieser Vorlage (einschließlich bearbeiteter Versionen) wird nachverfolgt.

## Erstellen einer Bandbreitenvorlage für einen gesamten Tag, die zu einem bestimmten Zeitpunkt startet

Gehen Sie folgendermaßen vor, um einen Zeitplan zu erstellen, der zu einem angegebenen Zeitpunkt gestartet und über den gesamten Tag ausgeführt wird. Im Beispiel wird der Zeitplan um 9:00 Uhr gestartet und bis um 9:00 Uhr am nächsten Morgen ausgeführt. Beachten Sie, dass die Start- und Endzeiten für einen bestimmten Zeitplan beide im gleichen 24-Stunden-Zeitplan enthalten sein müssen und nicht über mehrere Tage ausgedehnt werden können. Wenn Sie Bandbreitenvorlagen benötigen, die mehrere Tage umfassen, müssen Sie mehrere Zeitpläne verwenden (wie im Beispiel gezeigt).

#### So erstellen Sie eine ganztägige Bandbreitenvorlage

1. Erstellen Sie einen Zeitplan, der um 9:00 Uhr beginnt und bis Mitternacht ausgeführt wird.

2. Fügen Sie einen weiteren Zeitplan hinzu. Konfigurieren Sie den zweiten Zeitplan von Mitternacht bis 9:00 Uhr.

3. Speichern Sie die Bandbreitenvorlage

Der zusammengesetzte Zeitplan wird zu einem Zeitpunkt Ihrer Wahl gestartet und ganztägig ausgeführt.

## Fragen und Antworten zu Bandbreitenvorlagen

**F**. Was geschieht mit der Bandbreitensteuerung zwischen den Zeitplänen? (Ein Zeitplan wurde beendet, und es hat noch kein anderer begonnen.)

**A**. In solchen Fällen wird keine Bandbreitensteuerung eingesetzt. Dies bedeutet, dass das Gerät uneingeschränkte Bandbreite für den Datenverkehr zur Cloud nutzen kann.

**F**. Können Bandbreitenvorlagen auf einem Gerät geändert werden, das offline ist?

**A**. Sie können keine Bandbreitenvorlagen für Volumecontainer ändern, wenn das entsprechende Gerät offline ist.

**F**. Lässt sich eine Bandbreitenvorlage bearbeiten, die einem Volumecontainer zugeordnet ist, wenn sich die damit verknüpften Volumes offline befinden?

**A**. Sie können eine Bandbreitenvorlage bearbeiten, die einem Volumecontainer zugeordnet ist, dessen verknüpfte Volumes offline sind. Beachten Sie, dass bei Volumes, die offline sind, keine Daten vom Gerät in die Cloud gesendet werden können.

**F**. Kann eine Standardvorlage gelöscht werden?

**A**. Obwohl Sie eine Standardvorlage löschen können, ist dies nicht empfehlenswert. Die Verwendung einer Standardvorlage (einschließlich bearbeiteter Versionen) wird nachverfolgt. Die Nachverfolgungsdaten werden analysiert und im Laufe der Zeit verwendet, um die Standardvorlage zu verbessern.

**F**. Wie stelle ich fest, dass eine Bandbreitenvorlagen geändert werden muss?

**A**. Eines der Anzeichen, dass eine Bandbreitenvorlage geändert werden muss, ist eine merkliche Verlangsamung des Netzwerks mehrmals am Tag. In diesem Fall sollten Sie die Speicher- und Netzwerknutzung anhand der E/A-Leistung und die Netzwerkdurchsatz-Diagramme überwachen.

Anhand des Netzwerkdaten-Durchsatzes können Sie die Tageszeit und die Volumecontainer ermitteln, in denen der Netzwerkengpass auftritt. Wenn dies bei der Übertragung von Daten in die Cloud auftritt (diese Informationen erhalten Sie über die E/A-Leistung für alle Volumecontainer vom Gerät zur Cloud), müssen Sie die den Volumecontainern zugeordneten Bandbreitenvorlagen ändern.

Nachdem die geänderten Vorlagen verwendet werden, müssen Sie das Netzwerk erneut auf bemerkbare Latenzen überwachen. Wenn Sie immer noch vorhanden sind, müssen Sie Bandbreitenvorlagen erneut ändern.

**F**. Was geschieht, wenn sich die Zeitpläne mehrerer Volumecontainer auf meinem Gerät überschneiden, jedoch verschiedene Einschränkungen für diese gelten?

**A**. Angenommen, Sie verfügen über ein Gerät mit 3 Volumecontainern. Die zugeordneten Zeitpläne dieser Container überlappen sich vollständig. Für diese Container gelten jeweils Bandbreiteneinschränkungen von 5, 10 und 15 MBit/s. Wenn gleichzeitig auf allen Containern E/A-Vorgänge stattfinden, gilt die niedrigste der drei Bandbreiteneinschränkungen: in diesem Fall 5 MBit/s, da die ausgehenden E/A-Anforderungen dieselbe Warteschlange nutzen.

## Bewährte Methoden für Bandbreitenvorlagen

Folgen Sie diesen Empfehlungen für Ihr StorSimple-Gerät:

- Konfigurieren Sie die Bandbreitenvorlagen auf dem Gerät für variable Drosselung des Netzwerkdurchsatzes zu unterschiedlichen Tageszeiten. Diese Bandbreitenvorlagen können in Verbindung mit Sicherungszeitplänen effizient zusätzliche Netzwerkbandbreite außerhalb der Spitzenzeiten für Cloudvorgänge nutzen.

- Berechnen Sie die tatsächliche Bandbreite für eine bestimmte Bereitstellung anhand der Größe der Bereitstellung und der erforderlichen Recovery Time Objective (RTO).

## Nächste Schritte

Weitere Informationen zum [Verwenden Ihres StorSimple-Geräts mithilfe des StorSimple Manager-Diensts](storsimple-manager-service-administration.md).

<!---HONumber=Oct15_HO3-->