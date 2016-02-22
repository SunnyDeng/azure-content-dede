<!--author=SharS last changed: 02/04/2016-->

#### So erstellen Sie ein Volume

1. Klicken Sie auf dem Gerät auf der Seite **Schnellstart** auf **Volume hinzufügen**. Dadurch wird der Assistent "Volume hinzufügen" gestartet.

2. Geben Sie im Assistenten "Volume hinzufügen" unter **Grundeinstellungen**

	4. den **Namen** für das Volume ein.
	5. Wählen Sie aus der Dropdownliste den **Verwendungstyp** für das Volume aus. Für Workloads, die lokale Garantien, niedrige Latenzzeiten und höhere Leistung benötigen, wählen Sie ein **lokales** Volume aus. Wählen Sie für alle anderen Daten ein **mehrstufiges** Volume aus. Wenn Sie dieses Volume für archivierte Daten verwenden, aktivieren Sie das Kontrollkästchen **Verwenden Sie dieses Volume für Archivdaten, auf die Sie seltener zugreifen**. 
	
		Ein lokales Volume wird mit vollständiger Speicherzuweisung (Thick Provisioning) bereitgestellt und stellt sicher, dass die primären Daten auf dem Volume lokal auf dem Gerät verbleiben und nicht in die Cloud übergehen. Wenn Sie ein lokales Volume erstellen, überprüft das Gerät den verfügbaren Speicherplatz auf den lokalen Ebenen, um das Volume in der angeforderten Größe bereitzustellen. Bei der Erstellung eines lokalen Volumes werden vorhandene Daten eventuell vom Gerät in die Cloud übertragen, und das Erstellen des Volumes kann längere Zeit in Anspruch nehmen. Die gesamte Zeit hängt von der Größe des bereitgestellten Volumes, von der verfügbaren Netzwerkbandbreite und von den Daten auf Ihrem Gerät ab.

		Ein mehrstufiges Volume wird mit schlanker Speicherzuweisung bereitgestellt und kann sehr schnell erstellt werden. Bei Verwendung des mehrstufigen Volumes für archivierte Daten wird durch Auswahl der Option **Verwenden Sie dieses Volume für Archivdaten, auf die Sie seltener zugreifen** die Deduplizierungblockgröße für das Volume in 512 KB geändert. Wenn dieses Feld nicht aktiviert ist, verwendet das entsprechende mehrstufige Volume eine Blockgröße von 64 KB. Bei Verwendung größerer Deduplizierungsblöcke kann das Gerät die Übertragung umfangreicher Archivdaten in die Cloud beschleunigen.

	3. Geben Sie die **Bereitgestellte Kapazität** für das Volume an. Notieren Sie die Kapazität, die basierend auf dem ausgewählten Volumetyp verfügbar ist. Die angegebene Volumegröße darf den verfügbaren Speicherplatz nicht überschreiten.

		Sie können lokale Volumes von bis zu 9 TB oder mehrstufige Volumes von bis zu 200 TB auf dem 8100-Gerät bereitstellen. Auf dem größeren 8600-Gerät können Sie lokale Volumes von bis zu 24 TB oder mehrstufige Volumes von bis zu 500 TB bereitstellen. Da für das Hosten des Arbeitssatzes mehrstufiger Volumes lokaler Speicher auf dem Gerät erforderlich ist, wirkt sich die Erstellung lokaler Volumes auf den Speicherplatz aus, der für die Bereitstellung mehrstufiger Volumes zur Verfügung steht. Wenn Sie daher ein lokales Volume erstellen, verringert sich dadurch der verfügbare Speicherplatz für die Erstellung mehrstufiger Volumes. Wenn Sie ein mehrstufiges Volume erstellen, reduziert sich in gleicher Weise der verfügbare Speicherplatz für die Erstellung lokaler Volumes.

		Wenn Sie ein lokales Volume von 9 TB (maximal zulässige Größe) auf dem 8100-Gerät bereitstellen, haben Sie damit den gesamten lokalen Speicherplatz erschöpft, der auf dem Gerät verfügbar ist. Ab diesem Zeitpunkt können Sie keine mehrstufigen Volumes mehr erstellen, da auf dem Gerät kein lokaler Speicherplatz zum Hosten des Arbeitssatzes mehrstufiger Volumes mehr vorhanden ist. Vorhandene mehrstufige Volumes wirken sich ebenfalls auf den verfügbaren Speicherplatz aus. Wenn Sie beispielsweise ein 8100-Gerät verwenden, auf dem bereits mehrstufige Volumes von 100 TB vorhanden sind, stehen nur noch 4,5 TB Speicherplatz für lokale Volumes zur Verfügung.

        Die folgende Abbildung zeigt das Dialogfeld **Grundeinstellungen** für ein lokales Volume.

         ![Hinzufügen eines lokalen Volumes](./media/storsimple-create-volume-u2/add-local-volume-include.png)

        Die folgende Abbildung zeigt das Dialogfeld **Grundeinstellungen** für ein mehrstufiges Volume.

         ![Hinzufügen eines lokalen Volumes](./media/storsimple-create-volume-u2/add-tiered-volume-include.png)

   4. Klicken Sie auf den Pfeil ![Pfeilsymbol](./media/storsimple-create-volume-u2/HCS_ArrowIcon-include.png), um zur nächsten Seite zu wechseln.


3. Fügen Sie im Dialogfeld **Zusätzliche Einstellungen** einen neuen Datensatz für die Zugriffssteuerung (Access Control Record, ACR) hinzu:

	1. Geben Sie einen **Namen** für den ACR ein.
	2. Geben Sie unter **iSCSI-Initiatorname** den qualifizierten iSCSI-Namen (IQN) des Windows-Hosts an. Wenn Sie den IQN nicht kennen, fahren Sie mit [Abrufen des IQNs eines Windows Server-Hosts](#get-the-iqn-of-a-windows-server-host) fort.
	3. Aktivieren Sie unter **Standardsicherung für dieses Volume?** das Kontrollkästchen **Aktivieren**. Die Standardsicherung erstellt eine Richtlinie, die jeden Tag um 22:30 Uhr ausgeführt wird (Uhrzeit des Geräts) und eine Cloud-Momentaufnahme dieses Volumes erstellt.
	 
     > [AZURE.NOTE] Nachdem die Sicherung hier aktiviert wurde, kann sie nicht zurückgesetzt werden. Sie müssen das Volume bearbeiten, um diese Einstellung zu ändern.

     ![Volume hinzufügen](./media/storsimple-create-volume-u2/AddVolumeAdditionalSettings1.png)

4. Klicken Sie auf das Häkchensymbol ![Häkchensymbol](./media/storsimple-create-volume-u2/HCS_CheckIcon-include.png). Ein Volume wird mit den angegebenen Einstellungen erstellt.

<!---HONumber=AcomDC_0211_2016-->