<!--author=SharS last changed: 9/16/15-->

#### Hinzufügen eines Volumecontainers

1. Wählen Sie auf der Seite **Geräte** das Gerät aus, doppelklicken Sie darauf, und klicken Sie dann auf die Registerkarte **Volumecontainer**.

2. Klicken Sie unten auf der Seite auf **Hinzufügen**. Führen Sie im Dialogfeld **Volumecontainer erstellen** die folgenden Schritte aus:

  1. Geben Sie einen eindeutigen **Namen** für den Volumecontainer an. Dieser Name darf maximal 32 Zeichen enthalten.
  2. Wählen Sie ein **Speicherkonto** aus, das diesem Volumecontainer zugeordnet werden soll. Sie können mithilfe der Option **Weitere hinzufügen** ein vorhandenes Speicherkonto innerhalb desselben Abonnements oder ein Speicherkonto aus einem anderen Abonnement auswählen. Sie können auch das Speicherkonto auswählen, das beim Erstellen des Diensts zum ersten Mal generiert wurde.
  3. Geben Sie als Bandbreite **Unbegrenzt** an, wenn Sie die gesamte verfügbare Bandbreite nutzen möchten, oder **Benutzerdefiniert**, um eine Bandbreitenkontrolle zu implementieren. Geben Sie einen Wert zwischen 1 und 1000 MBit/s als benutzerdefinierte Bandbreite an. Mithilfe von **Bandbreitenvorlage auswählen** können Sie einen Zeitplan für die Bandbreitenzuweisung anwenden.
  4. Es wird empfohlen, dass Sie immer **Cloud-Speicherverschlüsselung aktivieren** zum Verschlüsseln der in der Cloud gespeicherten Daten verwenden. Deaktivieren Sie die Verschlüsselung nur dann, wenn Sie andere Verfahren zum Verschlüsseln Ihrer Daten anwenden.
  5. Geben Sie einen **Verschlüsselungsschlüssel für Cloudspeicher** an, der zwischen 8 und 32 Zeichen enthält. Das Gerät verwendet diesen Schlüssel für den Zugriff auf die verschlüsselten Daten. Geben Sie im Feld **Cloudspeicher-Verschlüsselungsschlüssel bestätigen** den Cloudverschlüsselungsschlüssel erneut an, um diesen zu bestätigen.
  6. Klicken Sie auf den Pfeil, um zur nächsten Seite zu wechseln.

    ![Erstellen eines Volumecontainers mit Bandbreitenvorlage 1](./media/storsimple-add-volume-container/HCS_CreateVCBT1-include.png)

3. Wenn Sie **Bandbreitenvorlage auswählen** angegeben haben, wählen Sie aus der Dropdownliste der vorhandenen Bandbreitenvorlagen eine Vorlage aus. Überprüfen Sie die Zeitplaneinstellungen, und klicken Sie auf das Häkchensymbol ![Häkchensymbol](./media/storsimple-configure-new-storage-account/HCS_CheckIcon-include.png).

    ![Erstellen eines Volumecontainers mit Bandbreitenvorlage 2](./media/storsimple-add-volume-container/HCS_CreateVCBT2-include.png)

Der Volumecontainer wird gespeichert, und der neu erstellte Volumecontainer wird auf der Seite **Volumecontainer** aufgeführt.
 

<!---HONumber=AcomDC_1203_2015-->