<properties
   pageTitle="Verwalten von StorSimple-Volumes (U2) | Microsoft Azure"
   description="Erläutert, wie Sie StorSimple-Volumes hinzufügen, ändern, überwachen und löschen sowie ggf. offline schalten können."
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

# Verwalten von Volumes mithilfe des StorSimple Manager-Diensts (Update 2)

[AZURE.INCLUDE [storsimple-version-selector-manage-volumes](../../includes/storsimple-version-selector-manage-volumes.md)]

## Übersicht

In diesem Tutorial wird erläutert, wie Sie den StorSimple-Manager-Dienst zum Erstellen und Verwalten von Volumes auf dem StorSimple-Gerät und dem virtuellen StorSimple-Gerät mit installiertem Update 2 verwenden können.

Der StorSimple-Manager-Dienst ist eine Erweiterung des klassischen Azure-Portals, mit der Sie Ihre StorSimple-Lösung über eine einzelne Weboberfläche verwalten können. Zusätzlich zum Verwalten von Datenträgern können Sie mit dem StorSimple-Manager-Dienst StorSimple-Dienste erstellen und verwalten, Geräte anzeigen und verwalten, Warnungen anzeigen sowie Sicherungsrichtlinien und den Sicherungskatalog verwalten.

## Volumetypen

Folgende StorSimple-Volumes sind möglich:

- **Lokale Volumes**: Daten in diesen Volumes verbleiben jederzeit auf dem lokalen StorSimple-Gerät.
- **Mehrstufige Volumes**: Daten in diesen Volumes können in die Cloud überlaufen.

Ein Archivierungsvolume ist ein mehrstufiger Volumetyp. Die für Archivierungsvolumes verwendeten größeren Deduplizierungsblöcke ermöglichen dem Gerät die Übertragung größerer Datensegmente in die Cloud.

Nach Bedarf können Sie den Volumetyp von lokal in mehrstufig ändern oder umgekehrt. Weitere Informationen finden Sie unter [Ändern des Volumetyps](#change-the-volume-type).

### Lokale Volumes

Lokale Volumes werden vollständig bereitgestellt und lagern keine Daten in die Cloud aus. So gewährleisten sie lokale Garantien für primäre Daten, unabhängig von der Cloudkonnektivität. Daten auf lokalen Volumes werden nicht dedupliziert und komprimiert. Allerdings werden Momentaufnahmen von lokal angehefteten Volumes dedupliziert.

Lokale Volumes werden vollständig bereitgestellt. Aus diesem Grund müssen Sie bei deren Erstellung über genügend Speicherplatz auf dem Gerät verfügen. Sie können lokale Volumes mit einer Größe von maximal 9 TB auf dem StorSimple 8100-Gerät und 24 TB auf dem 8600-Gerät bereitstellen. StorSimple reserviert den verbleibenden lokalen Speicherplatz auf dem Gerät für Momentaufnahmen, Metadaten und die Datenverarbeitung. Sie können die Größe eines lokalen Volumes auf den maximal verfügbaren Speicherplatz erhöhen, jedoch können Sie die Größe eines Volumes nach der Erstellung nicht mehr verringern.

Wenn Sie ein lokales Volume erstellen, verringert sich dadurch der verfügbare Speicherplatz für die Erstellung mehrstufiger Volumes. Das Gegenteil trifft ebenfalls zu: Wenn Sie mehrstufige Volumes verwenden, ist der Speicherplatz für die Erstellung lokaler Volumes geringer als die oben genannten Obergrenzen.

### Mehrstufige Volumes

Mehrstufige Volumes sind Volumes mit schlanker Speicherzuweisung, in denen häufig verwendete Daten lokal auf dem Gerät bleiben und weniger häufig verwendete Daten automatisch in die Cloud ausgelagert werden. Schlanke Speicherzuweisung ist eine Virtualisierungstechnologie, bei der der verfügbare Speicher die physischen Ressourcen zu überschreiten scheint. Anstatt ausreichend Speicher im Voraus zu reservieren, verwendet StorSimple die schlanke Bereitstellung, um nur eben genug Speicher zum Erfüllen der aktuellen Anforderungen zuzuweisen. Die Elastizität von Cloudspeicher ermöglicht diesen Ansatz, weil StorSimple den Cloudspeicher vergrößern oder verkleinern kann, um sich ändernde Anforderungen zu erfüllen.

Bei Verwendung des mehrstufigen Volumes für archivierte Daten wird durch Aktivieren des Kontrollkästchens **Verwenden Sie dieses Volume für Archivdaten, auf die Sie seltener zugreifen** die Deduplizierungblockgröße für das Volume in 512 KB geändert. Wenn dieses Feld nicht aktiviert ist, verwendet das entsprechende mehrstufige Volume eine Blockgröße von 64 KB. Bei Verwendung größerer Deduplizierungsblöcke kann das Gerät die Übertragung umfangreicher Archivdaten in die Cloud beschleunigen.

>[AZURE.NOTE] Archivierungsvolumes, die mit einer StorSimple-Version vor Update 2 erstellt werden, werden als mehrstufig mit aktiviertem Archivierungskontrollkästchen importiert.

### Bereitgestellte Kapazität

In der folgenden Tabelle finden Sie die bereitgestellte Maximalkapazität für die einzelnen Geräte und Volumetypen. (Beachten Sie, dass lokale Volumes nicht auf einem virtuellen Gerät verfügbar sind.)

| | Maximale Größe mehrstufiger Volumes | Maximale Größe lokaler Volumes |
|-------------|----------------------------|------------------------------------|
| **Physische Geräte** | | |
| 8100 | 64 TB | 9 TB |
| 8600 | 64 TB | 24 TB |
| **Virtuelle Geräte** | | |
| 8010 | 30 TB | – |
| 8020 | 64 TB | – | 

## Die Seite "Volumes"

Auf der Seite **Volumes** können Sie die Speichervolumes verwalten, die auf dem Microsoft Azure StorSimple-Gerät für die Initiatoren (Server) bereitgestellt werden. Auf der Seite wird eine Liste der Volumes auf dem StorSimple-Gerät angezeigt.

 ![Seite „Volumes“](./media/storsimple-manage-volumes-u2/VolumePage.png)

Ein Volume umfasst eine Reihe von Attributen:

- **Volumename**: Ein beschreibender Name, der eindeutig sein muss und das Volume identifiziert. Dieser Name wird auch in Überwachungsberichten zum Filtern nach einem bestimmten Volume verwendet.

- **Status** – online oder offline. Offline-Volumes sind für die Initiatoren (Server), die über Zugriff für die Verwendung des Volumes verfügen, nicht sichtbar.

- **Kapazität**: Gibt die Gesamtmenge der Daten an, die vom Initiator (Server) gespeichert werden kann. Lokale Volumes werden vollständig bereitgestellt und befinden sich auf dem StorSimple-Gerät. Mehrstufige Volumes werden mit schlanker Speicherzuweisung bereitgestellt, und die Daten werden dedupliziert. Bei Volumes mit schlanker Speicherzuweisung weist Ihr Gerät die physische Speicherkapazität weder intern noch in der Cloud anhand der konfigurierten Volumekapazität vorab zu. Die Volumekapazität wird nach Bedarf zugewiesen und genutzt.

- **Typ**: Gibt an, ob das Volume **mehrstufig** (Standard) oder **lokal** ist.

- **Sicherung**: Gibt an, ob eine Standardsicherungsrichtlinie für das Volume vorhanden ist.

- **Zugriff** – gibt die Initiatoren (Server) an, die Zugriff auf dieses Volume haben. Initiatoren, die nicht Mitglieder des dem Volume zugeordneten Zugriffssteuerungsdatensatzes (ACR) sind, wird das Volume nicht angezeigt.

- **Überwachung** – gibt an, ob ein Volume überwacht wird. Beim Erstellen von Volumes wird die Überwachung standardmäßig aktiviert. Für einen Volumeklon wird die Überwachung jedoch deaktiviert. Befolgen Sie zum Überwachen eines Volumes die Anweisungen unter [Überwachen von Volumes](#monitor-a-volume).

Verwenden Sie die Anweisungen in diesem Tutorial, um die folgenden Aufgaben auszuführen:

- Hinzufügen eines Volumes 
- Ändern von Volumes 
- Ändern des Volumetyps
- Löschen von Volumes 
- Offlineschalten von Volumes 
- Überwachen von Volumes 

## Hinzufügen eines Volumes

Sie haben beim Bereitstellen der StorSimple-Lösung [ein Volume erstellt](storsimple-deployment-walkthrough-u2.md#step-6-create-a-volume). Das Hinzufügen eines Volumes ist ein ähnliches Verfahren.

#### So fügen Sie ein Volume hinzu

1. Wählen Sie auf der Seite **Geräte** das Gerät aus, doppelklicken Sie darauf, und klicken Sie dann auf die Registerkarte **Volumecontainer**.

2. Wählen Sie einen Volumecontainer aus der Liste aus, und doppelklicken Sie darauf, um auf die dem Container zugeordneten Volumes zuzugreifen.

3. Klicken Sie unten auf der Seite auf **Hinzufügen**. Der Assistent "Volume hinzufügen" wird gestartet.

     ![Assistent zum Hinzufügen von Volumes – Grundeinstellungen](./media/storsimple-manage-volumes-u2/TieredVolEx.png)

4. Gehen Sie im Assistenten "Volume hinzufügen" unter **Grundlegende Einstellungen** folgendermaßen vor:

  1. Geben Sie den **Namen** für das Volume ein.
  2. Wählen Sie einen **Verwendungstyp** aus der Dropdownliste aus. Wählen Sie für Workloads, für die eine ständige Verfügbarkeit der Daten auf dem lokalen Gerät erforderlich ist, **Lokal** aus. Wählen Sie für alle anderen Datentypen **Mehrstufig** aus. (**Mehrstufig** ist die Standardeinstellung.)
  3. Wenn Sie in Schritt 2 **Mehrstufig** ausgewählt haben, können Sie das Kontrollkästchen **Verwenden Sie dieses Volume für Archivdaten, auf die Sie seltener zugreifen** aktivieren, um ein Archivierungsvolume zu konfigurieren.
  4. Geben Sie die **Bereitgestellte Kapazität** für das Volume in GB oder TB an. Unter [Bereitgestellte Kapazität](#provisioned-capacity) finden Sie die maximalen Größen der einzelnen Geräte und Volumetypen. Sehen Sie sich die **Verfügbare Kapazität** an, um zu ermitteln, wie viel Speicher tatsächlich auf dem Gerät verfügbar ist.

5. Klicken Sie auf das Pfeilsymbol ![Pfeilsymbol](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png). Wenn Sie ein lokales Volume konfigurieren, wird die folgende Meldung angezeigt.

    ![Nachricht zum Ändern des Volumetyps](./media/storsimple-manage-volumes-u2/LocalVolEx.png)
   
5. Klicken Sie erneut auf das Pfeilsymbol ![Pfeilsymbol](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png), um zur Seite **Weitere Einstellungen** zu wechseln.

    ![Assistent zum Hinzufügen von Volumes – Zusätzliche Einstellungen](./media/storsimple-manage-volumes-u2/AddVolume2.png)<br>

6. Fügen Sie unter **Weitere Einstellungen** einen neuen Zugriffssteuerungsdatensatz (Access Control Record, ACR) hinzu:
  
  1. Wählen Sie aus der Dropdownliste einen Zugriffssteuerungsdatensatz aus. Sie können auch einen neuen Zugriffssteuerungsdatensatz hinzufügen. Mithilfe von Zugriffssteuerungsdatensätzen wird festgelegt, welche Hosts auf die Volumes zugreifen dürfen, indem der Host-IQN mit dem im Datensatz aufgeführten abgeglichen wird. Wenn Sie keinen ACR angeben, wird die folgende Meldung angezeigt.

        ![Specify ACR](./media/storsimple-manage-volumes-u2/SpecifyACR.png)

  2. Es wird empfohlen, das Kontrollkästchen **Standardsicherung für dieses Volume aktivieren** zu aktivieren.
  3. Klicken Sie auf das Häkchensymbol ![Häkchensymbol](./media/storsimple-manage-volumes-u2/HCS_CheckIcon.png), um das Volume mit den angegebenen Einstellungen zu erstellen.

Das neue Volume kann nun verwendet werden.

>[AZURE.NOTE] Wenn Sie ein lokales Volume und unmittelbar danach ein weiteres lokales Volume erstellen, werden die Aufträge zur Volumeerstellung nacheinander ausgeführt. Der erste Auftrag zur Volumeerstellung muss abgeschlossen sein, bevor der nächste Auftrag zur Volumeerstellung beginnt.

## Ändern von Volumes

Zum Ändern eines Volumes müssen Sie dieses erweitern oder die Hosts ändern, die auf das Volume zugreifen.

> [AZURE.IMPORTANT] 
>
> - Wenn Sie die Größe des Volumes auf dem Gerät ändern, muss auch die Größe des Volumes auf dem Host geändert werden. 
> - Die hier beschriebenen hostseitigen Schritte gelten für Windows Server 2012 (2012R2). Vorgehensweisen für Linux oder andere Hostbetriebssysteme können davon abweichen. Beachten Sie beim Ändern des Volumes auf einem Host mit einem anderen Betriebssystem die Anweisungen zu Ihrem Hostbetriebssystem. 

#### So ändern Sie ein Volume

1. Wählen Sie auf der Seite **Geräte** das Gerät aus, doppelklicken Sie darauf, und klicken Sie dann auf die Registerkarte **Volumecontainer**.

2. Wählen Sie einen Volumecontainer aus der Liste aus, und doppelklicken Sie darauf, um die dem Container zugeordneten Volumes anzuzeigen.

3. Wählen Sie ein Volume aus, und klicken Sie unten auf der Seite auf **Ändern**. Der Assistent zum Ändern von Volumes wird gestartet.

4. Gehen Sie im Assistenten "Ändern von Volume" unter **Grundlegende Einstellungen** folgendermaßen vor:

  - Bearbeiten Sie den **Namen**.
  - Konvertieren Sie den **Verwendungstyp** von lokal in mehrstufig bzw. von mehrstufig in lokal. (Weitere Informationen finden Sie unter [Ändern des Volumetyps](#change-the-volume-type).)
  - Erhöhen Sie die **Bereitgestellte Kapazität**. Die **Bereitgestellte Kapazität** kann nur erhöht werden. Ein Volume kann nach seiner Erstellung nicht mehr verkleinert werden.

5. Unter **Zusätzliche Einstellungen** können Sie den ACR ändern, vorausgesetzt, dass das Volume offline ist. Wenn das Volume online ist, müssen Sie dieses zunächst offline schalten. Lesen Sie vor dem Bearbeiten eines Zugriffssteuerungsdatensatzes die Schritte unter [Offlineschalten von Volumes](#take-a-volume-offline).

    > [AZURE.NOTE] Die Option **Standardsicherung für dieses Volume?** kann für das Volume nicht geändert werden.

6. Speichern Sie die Änderungen, indem Sie auf das Häkchensymbol ![Häkchensymbol](./media/storsimple-manage-volumes-u2/HCS_CheckIcon.png) klicken. Das klassische Azure-Portal zeigt eine Meldung zur Aktualisierung des Volumes an. Eine Erfolgsmeldung wird angezeigt, wenn das Volume aktualisiert wurde.

7. Wenn Sie ein Volume erweitern, führen Sie folgende Schritte auf dem Windows-Hostcomputer aus:

   1. Wechseln Sie zu **Verwaltung** -> **Datenträgerverwaltung**.
   2. Klicken Sie mit der rechten Maustaste auf **Datenträgerverwaltung**, und wählen Sie **Datenträger neu einlesen** aus.
   3. Wählen Sie in der Liste der Datenträger die aktualisierten Volumes aus und dann **Volume erweitern**. Der Assistent zum Erweitern von Volumes wird gestartet. Klicken Sie auf **Weiter**.
   4. Schließen Sie den Assistenten unter Verwendung der angegebenen Standardwerte ab. Nach Abschluss des Assistenten sollte für das Volume die höhere Speichergröße angezeigt werden.

    >[AZURE.NOTE] Wenn Sie ein lokales Volume und unmittelbar danach ein weiteres lokales Volume erweitern, werden die Aufträge zur Volumeerweiterung nacheinander ausgeführt. Der erste Auftrag zur Volumeerweiterung muss abgeschlossen sein, bevor der nächste Auftrag zur Volumeerweiterung beginnt.

![Video verfügbar](./media/storsimple-manage-volumes-u2/Video_icon.png) **Video verfügbar**

Sie können sich [hier](https://azure.microsoft.com/documentation/videos/expand-a-storsimple-volume/) ein Video anschauen, in dem das Erweitern eines Volumes demonstriert wird.

## Ändern des Volumetyps

Sie können den Volumetyp von mehrstufig in lokal ändern oder umgekehrt. Normalerweise handelt es sich dabei um kleine vorhandene Volumes, auf die Sie häufig zugreifen möchten. Diese Konvertierung sollte jedoch nicht häufig vorkommen. Für die Konvertierung eines Volumes von mehrstufig in lokal gibt es folgende Gründe:

- Lokale Garantien in Bezug auf Datenverfügbarkeit und Leistung
- Beseitigung von Cloudlatenzzeiten und Cloudverbindungsproblemen.

Ein lokales Volume wird bei seiner Erstellung vollständig bereitgestellt. Wenn Sie ein mehrstufiges Volume in ein lokales Volume konvertieren, stellt StorSimple vor der Konvertierung sicher, dass auf dem Gerät genügend Speicherplatz verfügbar ist. Wenn Sie nicht über genügend Speicherplatz verfügen, erhalten Sie eine Fehlermeldung und der Vorgang wird abgebrochen.

> [AZURE.NOTE] Bevor Sie eine Konvertierung von mehrstufig in lokal starten, berücksichtigen Sie unbedingt den Platzbedarf der anderen Workloads.

Möglicherweise empfiehlt es sich, ein lokales Volume in ein mehrstufiges Volume zu ändern, wenn Sie zusätzlichen Speicherplatz zur Bereitstellung anderer Volumes benötigen. Bei der Konvertierung des lokalen Volumes in ein mehrstufiges erhöht sich die auf dem Gerät verfügbare Kapazität um die Größe der freigegebenen Kapazität. Wenn Verbindungsprobleme die Konvertierung eines Volumes vom lokalen in den mehrstufigen Typ verhindern, zeigt das lokale Volume Eigenschaften eines mehrstufigen Volumes, bis die Konvertierung abgeschlossen ist. Dies liegt daran, dass einige Daten möglicherweise in die Cloud übergegangen sind. Diese übergelaufenen Daten belegen weiterhin lokalen Speicherplatz auf dem Gerät, der erst freigegeben werden kann, wenn der Vorgang neu gestartet wird und abgeschlossen ist.

>[AZURE.NOTE] Das Konvertieren eines Volumes kann einige Zeit dauern, und eine Konvertierung kann nach ihrem Start nicht abgebrochen werden. Das Volume bleibt während der Konvertierung online, und Sie können Sicherungen erstellen. Sie können das Volume jedoch nicht erweitern oder wiederherstellen, solange die Konvertierung stattfindet.

#### So ändern Sie den Volumetyp

1. Wählen Sie auf der Seite **Geräte** das Gerät aus, doppelklicken Sie darauf, und klicken Sie dann auf die Registerkarte **Volumecontainer**.

2. Wählen Sie einen Volumecontainer aus der Liste aus, und doppelklicken Sie darauf, um die dem Container zugeordneten Volumes anzuzeigen.

3. Wählen Sie ein Volume aus, und klicken Sie unten auf der Seite auf **Ändern**. Der Assistent zum Ändern von Volumes wird gestartet.

4. Ändern Sie auf der Seite **Grundeinstellungen** den Verwendungstyp, indem Sie den neuen Typ aus der Dropdownliste **Verwendungstyp** auswählen.

    - Wenn Sie den Typ in **Lokal** ändern, prüft StorSimple, ob genügend Kapazität vorhanden ist.
    - Wenn Sie den Typ in **Mehrstufig** ändern und dieses Volume für archivierte Daten verwendet werden soll, aktivieren Sie das Kontrollkästchen **Verwenden Sie dieses Volume für Archivdaten, auf die Sie seltener zugreifen**.

        ![Archivierungskontrollkästchen](./media/storsimple-manage-volumes-u2/ModifyTieredVolEx.png)

5. Klicken Sie auf das Pfeilsymbol ![Pfeilsymbol](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png), um zur Seite **Weitere Einstellungen** zu wechseln. Wenn Sie ein lokales Volume konfigurieren, wird die folgende Meldung angezeigt.

    ![Nachricht zum Ändern des Volumetyps](./media/storsimple-manage-volumes-u2/ModifyLocalVolEx.png)

6. Klicken Sie erneut auf das Pfeilsymbol ![Pfeilsymbol](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png), um fortzufahren.

7. Klicken Sie auf das Häkchensymbol ![Häkchensymbol](./media/storsimple-manage-volumes-u2/HCS_CheckIcon.png), um die Konvertierung zu starten. Das Azure-Portal zeigt eine Meldung zur Aktualisierung des Volumes an. Eine Erfolgsmeldung wird angezeigt, wenn das Volume aktualisiert wurde.

## Offlineschalten von Volumes

Möglicherweise müssen Sie ein Volume offline schalten, wenn Sie dieses ändern oder löschen möchten. Wenn ein Volume offline ist, ist es nicht für den Lese-/ Schreibzugriff verfügbar. Sie müssen das Volume sowohl auf dem Host als auch auf dem Gerät offline schalten.

#### So schalten Sie ein Volume offline

1. Stellen Sie vor dem Offlineschalten sicher, dass das betreffende Volume nicht verwendet wird.

2. Schalten Sie das Volume zunächst auf dem Host offline. Dadurch werden potenzielle Schäden an den Daten auf dem Volume vermieden. Genaue Anweisungen finden Sie in den Anweisungen für das Hostbetriebssystem.

3. Wenn der Host offline ist, schalten Sie das Volume anhand der folgenden Schritte auf dem Gerät offline:

  1. Wählen Sie auf der Seite **Geräte** das Gerät aus, doppelklicken Sie darauf, und klicken Sie dann auf die Registerkarte **Volumecontainer**. Auf dieser Seite werden die dem Gerät zugeordneten **Volumecontainer** in Tabellenform aufgeführt.
  2. Wählen Sie einen Volumecontainer aus, und klicken Sie auf diesen, um die Liste aller Volumes innerhalb des Containers anzuzeigen.
  3. Wählen Sie ein Volume aus, und klicken Sie auf **Offline schalten**.
  4. Wenn Sie zur Bestätigung aufgefordert werden, klicken Sie auf **Ja**. Das Volume sollte jetzt offline sein.

    Wenn ein Volume offline geschaltet wurde, wird die Option **Online schalten** angezeigt.

> [AZURE.NOTE] Mit dem Befehl **Offline schalten** wird eine Anforderung an das Gerät gesendet, das Volume offline zu schalten. Wenn das Volume weiterhin von Hosts verwendet wird, führt dies zur Unterbrechung von Verbindungen, das Offlineschalten des Volumes schlägt jedoch nicht fehl.

## Löschen von Volumes

> [AZURE.IMPORTANT] Sie können ein Volume nur löschen, wenn dieses offline ist.

Führen Sie die folgenden Schritte durch, um ein Volume zu löschen.

#### So löschen Sie ein Volume

1. Wählen Sie auf der Seite **Geräte** das Gerät aus, doppelklicken Sie darauf, und klicken Sie dann auf die Registerkarte **Volumecontainer**.

2. Wählen Sie den Volumecontainer mit dem zu löschenden Volume aus. Klicken Sie auf den Volumecontainer, um die Seite **Volumes** anzuzeigen.

3. Alle diesem Container zugeordneten Volumes werden in Tabellenform angezeigt. Überprüfen Sie den Status des zu löschenden Volumes. Wenn das zu löschende Volume nicht offline ist,schalten Sie es zunächst mithilfe der Schritte unter [Offlineschalten von Volumes](#take-a-volume-offline) offline.

4. Nachdem das Volume offline ist, klicken Sie unten auf der Seite auf **Löschen**.

5. Wenn Sie zur Bestätigung aufgefordert werden, klicken Sie auf **Ja**. Das Volume wird nun gelöscht. Auf der Seite **Volumes** wird die aktualisierte Liste der Volumes innerhalb des Containers angezeigt.

    >[AZURE.NOTE] Wenn Sie ein lokales Volume und unmittelbar danach ein weiteres lokales Volume löschen, werden die Aufträge zur Volumelöschung nacheinander ausgeführt. Der erste Auftrag zur Volumelöschung muss abgeschlossen sein, bevor der nächste Auftrag zur Volumelöschung beginnt.
 
## Überwachen von Volumes

Mit der Volumeüberwachung können Sie E/A-bezogene Statistiken für ein Volume erfassen. Die Überwachung wird standardmäßig für die ersten 32 von Ihnen erstellten Volumes aktiviert. Die Überwachung der zusätzlichen Volumes ist in der Standardeinstellung deaktiviert. Die Überwachung geklonter Volumes ist ebenfalls standardmäßig deaktiviert.

Führen Sie die folgenden Schritte durch, um die Überwachung eines Volumes zu aktivieren oder zu deaktivieren.

#### So aktivieren oder deaktivieren Sie die Volumeüberwachung

1. Wählen Sie auf der Seite **Geräte** das Gerät aus, doppelklicken Sie darauf, und klicken Sie dann auf die Registerkarte **Volumecontainer**.

2. Wählen Sie den Volumecontainer mit dem Volume aus, und klicken Sie dann auf den Volumecontainer, um die Seite **Volumes** anzuzeigen.

3. Alle diesem Container zugeordneten Volumes werden in Tabellenform angezeigt. Klicken Sie zum Auswählen auf das Volume oder den Volumeklon.

4. Klicken Sie unten auf der Seite auf **Ändern**.

5. Klicken Sie im Assistenten "Ändern von Volume" unter **Grundlegende Einstellungen** in der Dropdownliste **Überwachen** auf **Aktivieren** oder **Deaktivieren**.

## Nächste Schritte

- Erfahren Sie, wie Sie ein [StorSimple-Volume klonen](storsimple-clone-volume.md).

- Erfahren Sie, wie Sie [Ihr StorSimple-Gerät mithilfe des StorSimple Manager-Diensts verwalten](storsimple-manager-service-administration.md).

 

<!---HONumber=AcomDC_0224_2016-->