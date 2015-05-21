<properties
   pageTitle="Verwalten von StorSimple-Volumes"
   description="Erklärt, wie Sie StorSimple-Volumes hinzufügen, ändern und überwachen sowie ggf. offlineschalten können."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="04/13/2015"
   ms.author="v-sharos" />

#Verwalten von StorSimple-Volumes

##Übersicht

In diesem Lernprogramm wird erläutert, wie Sie den StorSimple-Manager-Dienst zum Erstellen und Verwalten von Volumes auf dem StorSimple-Gerät und dem virtuellen StorSimple-Gerät verwenden können.

Der StorSimple-Manager-Dienst ist eine Erweiterung des Azure-Verwaltungsportals, mit dem Sie Ihre StorSimple-Lösung über eine einzelne Webschnittstelle verwalten können. Zusätzlich zum Verwalten von Datenträgern können Sie mit dem StorSimple-Manager-Dienst StorSimple-Dienste erstellen und verwalten, Geräte anzeigen und verwalten, Warnungen anzeigen sowie Sicherungsrichtlinien und den Sicherungskatalog verwalten.

> [AZURE.NOTE]Mit Azure StorSimple können nur nach Bedarf bereitgestellte Volumes erstellt werden. In einem Azure StorSimple-System können keine vollständig oder teilweise bereitgestellten Volumes erstellt werden.
>
> Die Bereitstellung nach Bedarf ist eine Virtualisierungstechnologie, bei der der verfügbare Speicher scheinbar die physischen Ressourcen überschreitet. Anstatt vorab ausreichend Speicherplatz zu reservieren, ordnet Azure StorSimple bei der Bereitstellung nach Bedarf nur den für die aktuellen Anforderungen erforderlichen Speicherplatz zu. Die Flexibilität der Cloud-Speicherung ermöglicht diesen Ansatz, da Azure StorSimple diesen Cloud-Speicher erhöhen oder verringern kann, um die veränderten Anforderungen zu erfüllen.

##Die Seite "Volumes"

Auf der Seite **Volumes** können Sie die Speichervolumes verwalten, die auf dem Microsoft Azure StorSimple-Gerät für die Initiatoren \(Server\) bereitgestellt werden. Auf der Seite wird eine Liste der Volumes auf dem StorSimple-Gerät angezeigt.

 ![Seite "Volumes"](./media/storsimple-manage-volumes/HCS_VolumesPage.png)

Ein Volume umfasst eine Reihe von Attributen:

- **Name** – ein beschreibender Name, der eindeutig sein muss und den Datenträger identifiziert. Dieser Name wird auch in Überwachungsberichten zum Filtern nach einem bestimmten Volume verwendet.

- **Status** – online oder offline. Offline-Volumes sind für die Initiatoren \(Server\), die über Zugriff für die Verwendung des Volumes verfügen, nicht sichtbar.

- **Kapazität** – gibt an, wie groß das Volume aus Sicht des Initiators \(Servers\) ist. Die Kapazität entspricht der Gesamtmenge der Daten, die vom Initiator \(Server\) gespeichert werden können. Die Volumes verfügen über eine schlanke Speicherzuweisung, und die Daten werden dedupliziert. Dies bedeutet, dass Ihr Gerät die physische Speicherkapazität weder intern noch in der Cloud vorab anhand der konfigurierten Volumekapazität zuweist. Die Volumekapazität ist zugewiesen und wird bei Bedarf genutzt.

- **Zugriff** – gibt die Initiatoren \(Server\) an, die Zugriff auf dieses Volume haben. Initiatoren, die nicht Mitglieder des dem Volume zugeordneten Zugriffssteuerungsdatensatzes \(ACR\) sind, wird das Volume nicht angezeigt.

- **Überwachung** – gibt an, ob ein Volume überwacht wird. Beim Erstellen von Volumes wird die Überwachung standardmäßig aktiviert. Für einen Volumeklon wird die Überwachung jedoch deaktiviert. Befolgen Sie zum Überwachen eines Volumes die Anweisungen unter "Überwachen von Volumes".

Die häufigsten Aufgaben im Zusammenhang mit einem Volume sind:

- Hinzufügen eines Volumes 
- Ändern von Volumes 
- Löschen von Volumes 
- Offlineschalten von Volumes 
- Überwachen von Volumes 

##Hinzufügen eines Volumes

Sie haben beim Bereitstellen der StorSimple-Lösung [ein Volume erstellt](storsimple-deployment-walkthrough.md#step-6:-create-a-volume). Das Hinzufügen eines Volumes ist ein ähnliches Verfahren.

###So fügen Sie ein Volume hinzu

1. Wählen Sie auf der Seite **Geräte** das Gerät aus, doppelklicken Sie darauf, und klicken Sie dann auf die Registerkarte **Volumecontainer**.

2. Wählen Sie einen Volumecontainer aus, und klicken Sie auf den Pfeil in der entsprechenden Zeile, um auf die dem Container zugeordneten Volumes zuzugreifen.

3. Klicken Sie unten auf der Seite auf **Add**. Der Assistent "Volume hinzufügen" wird gestartet.

     ![Volume hinzufügen](./media/storsimple-manage-volumes/HCS_AddVolume1M.png)

4. Gehen Sie im Assistenten "Volume hinzufügen" unter **Grundlegende Einstellungen** folgendermaßen vor:

  1. Geben Sie den **Namen** für das Volume ein.
  2. Geben Sie die **Bereitgestellte Kapazität** für das Volume in GB oder TB an. Die Kapazität muss für ein physisches Gerät zwischen 1 GB und 64 TB liegen. Die maximale Kapazität, die für ein Volume auf einem virtuellen StorSimple-Gerät bereitgestellt werden kann, beträgt 30 TB.
  3. Wählen Sie aus der Dropdownliste den **Verwendungstyp** für das Volume aus. Wählen Sie bei der Arbeit mit weniger häufig abgerufenen Archivierungsdaten **Archivierungsvolume** aus. Wählen Sie für alle anderen Verwendungstypen **Primäres Volume** aus.
  5. Klicken Sie auf das Pfeilsymbol ![Pfeilsymbol](./media/storsimple-manage-volumes/HCS_ArrowIcon.png), um zur Seite **Weitere Einstellungen** zu wechseln.

     ![Volume hinzufügen](./media/storsimple-manage-volumes/HCs_AddVolume2M.png)
   
5. Fügen Sie unter **Weitere Einstellungen** einen neuen Zugriffssteuerungsdatensatz \(Access Control Record, ACR\) hinzu:
  
  1. Wählen Sie aus der Dropdownliste einen Zugriffssteuerungsdatensatz aus. Sie können auch einen neuen Zugriffssteuerungsdatensatz hinzufügen. Mithilfe von Zugriffssteuerungsdatensätzen wird festgelegt, welche Hosts auf die Volumes zugreifen dürfen, indem der Host-IQN mit dem im Datensatz aufgeführten abgeglichen wird.
  2. Es wird empfohlen, unter **Standardsicherung für dieses Volume?** die Standardsicherung zu aktivieren, indem Sie das Kontrollkästchen **Aktivieren** aktivieren.
   3. Klicken Sie auf das Häkchensymbol ![Häkchensymbol](./media/storsimple-manage-volumes/HCS_CheckIcon.png), um das Volume mit den angegebenen Einstellungen zu erstellen.

Das neue Volume kann nun verwendet werden.

##Ändern von Volumes

Zum Ändern eines Volumes müssen Sie dieses erweitern oder die Hosts ändern, die auf das Volume zugreifen.

> [AZURE.IMPORTANT]Wenn Sie die Größe des Volumes auf dem Gerät ändern, muss auch die Größe des Volumes auf dem Host geändert werden. Beachten Sie beim Ändern des Volumes auf dem Host die Anweisungen zu Ihrem Hostbetriebssystem.


###So ändern Sie ein Volume

1. Wählen Sie auf der Seite **Geräte** das Gerät aus, doppelklicken Sie darauf, und klicken Sie dann auf die Registerkarte **Volumecontainer**. Auf dieser Seite werden die dem Gerät zugeordneten Volumecontainer in Tabellenform aufgeführt.

2. Wählen Sie einen Volumecontainer aus, und klicken Sie auf diesen, um die Liste aller Volumes innerhalb des Containers anzuzeigen.

3. Wählen Sie auf der Seite **Volumes** ein Volume aus, und klicken Sie auf **Ändern**.

4. Gehen Sie im Assistenten "Ändern von Volume" unter **Grundlegende Einstellungen** folgendermaßen vor:

  - Ändern Sie **Name** und **Verwendungstyp**.
  - Erhöhen Sie die **Bereitgestellte Kapazität**. Die **Bereitgestellte Kapazität** kann nur erhöht werden. Ein Volume kann nach seiner Erstellung nicht mehr verkleinert werden.

    > [AZURE.NOTE]Sie können den Volumecontainer nicht ändern, nachdem dieser einem Volume zugeordnet wurde.

5. Unter **Weitere Einstellungen** können Sie folgende Aufgaben durchführen:

  - Ändern Sie die Zugriffssteuerungsdatensätze, sofern das Volume offline ist. Wenn das Volume online ist, müssen Sie dieses zunächst offline schalten. Lesen Sie vor dem Bearbeiten eines Zugriffssteuerungsdatensatzes die Schritte unter [Offlineschalten von Volumes](#take-a-volume-offline).
  - Ändern Sie die Liste der Zugriffssteuerungsdatensätze, nachdem das Volume offline ist.
 
    > [AZURE.NOTE]Die Option **Standardsicherung für dieses Volume?** kann für das Volume nicht geändert werden.

6. Speichern Sie die Änderungen, indem Sie auf das Häkchensymbol ![Häkchensymbol](./media/storsimple-manage-volumes/HCS_CheckIcon.png) klicken.

##Offlineschalten von Volumes

Möglicherweise müssen Sie ein Volume offline schalten, wenn Sie dieses ändern oder löschen möchten. Wenn ein Volume offline ist, ist es nicht für den Lese-/ Schreibzugriff verfügbar. Sie müssen das Volume sowohl auf dem Host als auch auf dem Gerät offline schalten. Führen Sie die folgenden Schritte durch, um ein Volume offline zu schalten.

###So schalten Sie ein Volume offline

1. Stellen Sie vor dem Offlineschalten sicher, dass das betreffende Volume nicht verwendet wird.

2. Schalten Sie das Volume zunächst auf dem Host offline. Dadurch werden potenzielle Schäden an den Daten auf dem Volume vermieden. Genaue Anweisungen finden Sie in den Anweisungen für das Hostbetriebssystem.

3. Wenn der Host offline ist, schalten Sie das Volume anhand der folgenden Schritte auf dem Gerät offline:

  1. Wählen Sie auf der Seite **Geräte** das Gerät aus, doppelklicken Sie darauf, und klicken Sie dann auf die Registerkarte **Volumecontainer**. Auf dieser Seite werden die dem Gerät zugeordneten **Volumecontainer** in Tabellenform aufgeführt.
  2. Wählen Sie einen Volumecontainer aus, und klicken Sie auf diesen, um die Liste aller Volumes innerhalb des Containers anzuzeigen.
  3. Wählen Sie ein Volume aus, und klicken Sie auf **Offline schalten**.
  4. Wenn Sie zur Bestätigung aufgefordert werden, klicken Sie auf **Ja**. Das Volume sollte jetzt offline sein.

    Wenn ein Volume offline geschaltet wurde, wird die Option **Online schalten** angezeigt.

> [AZURE.NOTE]Mit dem Befehl **Offline schalten** wird eine Anforderung an das Gerät gesendet, das Volume offline zu schalten. Wenn das Volume weiterhin von Hosts verwendet wird, führt dies zur Unterbrechung von Verbindungen, das Offlineschalten des Volumes schlägt jedoch nicht fehl.

##Löschen von Volumes

> [AZURE.IMPORTANT]Sie können ein Volume nur löschen, wenn dieses offline ist.

Führen Sie die folgenden Schritte durch, um ein Volume zu löschen.

###So löschen Sie ein Volume

1. Wählen Sie auf der Seite **Geräte** das Gerät aus, doppelklicken Sie darauf, und klicken Sie dann auf die Registerkarte **Volumecontainer**.

2. Wählen Sie den Volumecontainer mit dem zu löschenden Volume aus. Klicken Sie auf den Volumecontainer, um die Seite **Volumes** anzuzeigen.

3. Alle diesem Container zugeordneten Volumes werden in Tabellenform angezeigt. Überprüfen Sie den Status des zu löschenden Volumes. Wenn das zu löschende Volume nicht offline ist,schalten Sie es zunächst mithilfe der Schritte unter [Offlineschalten von Volumes](#Take-a-volume-offline) offline.

4. Nachdem das Volume offline ist, klicken Sie unten auf der Seite auf **Löschen**.

5. Wenn Sie zur Bestätigung aufgefordert werden, klicken Sie auf **Ja**. Das Volume wird nun gelöscht. Auf der Seite **Volumes** wird die aktualisierte Liste der Volumes innerhalb des Containers angezeigt.

##Überwachen von Volumes

Mit der Volumeüberwachung können Sie E/A-bezogene Statistiken für ein Volume erfassen. Die Überwachung wird standardmäßig für die ersten 32 von Ihnen erstellten Volumes aktiviert. Die Überwachung der zusätzlichen Volumes ist in der Standardeinstellung deaktiviert. Die Überwachung geklonter Volumes ist standardmäßig deaktiviert.

Führen Sie die folgenden Schritte durch, um die Überwachung eines Volumes zu aktivieren oder zu deaktivieren.

###So aktivieren oder deaktivieren Sie die Volumeüberwachung

1. Wählen Sie auf der Seite **Geräte** das Gerät aus, doppelklicken Sie darauf, und klicken Sie dann auf die Registerkarte **Volumecontainer**.

2. Wählen Sie den Volumecontainer mit dem Volume aus, und klicken Sie dann auf den Volumecontainer, um die Seite **Volumes** anzuzeigen.

3. Alle diesem Container zugeordneten Volumes werden in Tabellenform angezeigt. Klicken Sie zum Auswählen auf das Volume oder den Volumeklon.

4. Klicken Sie unten auf der Seite auf **Ändern**.

5. Klicken Sie im Assistenten "Ändern von Volume" unter **Grundlegende Einstellungen** in der Dropdownliste **Überwachen** auf **Aktivieren** oder **Deaktivieren**.

    ![Überwachen von Volumes](./media/storsimple-manage-volumes/HCS_MonitorVolumeM.png)

##Nächste Schritte

Erfahren Sie, wie Sie ein [StorSimple-Volume klonen](storsimple-clone-volume.md).



<!--HONumber=52-->
