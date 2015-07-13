
### Schritt 1: Ein Gerät autorisieren, um den Verschlüsselungsschlüssel für Dienstdaten im Verwaltungsportal zu ändern.

Üblicherweise fordert der Geräteadministrator den Dienstadministrator auf, ein Gerät zu autorisieren, um den Verschlüsselungsschlüssel für Dienstdaten zu ändern. Der Dienstadministrator autorisiert dann das Gerät, um den Schlüssel zu ändern.

Dieser Schritt wird im Verwaltungsportal ausgeführt. Der Dienstadministrator kann das Gerät aus einer Liste der für die Autorisierung zur Verfügung stehenden Geräte auswählen. Das Gerät wird dann autorisiert und der Vorgang zur Änderung des Verschlüsselungsschlüssels für Dienstdaten wird gestartet.

#### Welche Geräte können zur Änderung des Verschlüsselungsschlüssels für Dienstdaten autorisiert werden?

Ein Gerät muss die folgenden Kriterien erfüllen, bevor es für die Initiierung des Änderungsvorgangs des Verschlüsselungsschlüssels für Dienstdaten autorisiert werden kann:

- Für die Autorisierung zum Ändern des Verschlüsselungsschlüssels für Dienstdaten muss das Gerät online sein.

- Wurde die Änderung des Schlüssels nicht initiiert, können Sie dasselbe Gerät nach 30 Minuten erneut autorisieren.

- Wurde die Änderung des Schlüssel nicht durch das zuvor autorisierte Gerät initialisiert, können Sie auch ein anderes Gerät autorisieren. Nachdem das neue Gerät autorisiert wurde, kann die Änderung nicht durch das alte Gerät initiiert werden.

- Während des Rollover-Prozesses des Verschlüsselungsschlüssel für Dienstdaten können Sie kein Gerät autorisieren.

- Eine Geräte-Autorisierung ist möglich, sobald einige der beim Dienst registrierten Geräte den Verschlüsselungs-Rollover abgeschlossen haben. In diesen Fällen handelt es sich bei den verfügbaren Geräten um diejenigen, die die Änderung des Verschlüsselungsschlüssels für Dienstdaten bereits abgeschlossen haben.

> [AZURE.NOTE]Im Verwaltungsportal werden virtuelle StorSimple-Geräte nicht in der Liste der Geräte angezeigt, welche für die Initiierung der Schlüsseländerung durch eine Autorisierung zur Verfügung stehen.

Führen Sie die folgenden Schritte durch, um ein Gerät für die Initialisierung der Änderung des Verschlüsselungsschlüssels für Dienstdaten auszuwählen und zu autorisieren.

#### Geräteautorisierung zur Änderung des Schlüssels

1. Klicken Sie auf der Dashboard-Seite für Dienste auf **Verschlüsselungsschlüssel für Dienstdaten ändern**.

    ![Ändern des Dienstverschlüsselungsschlüssels](./media/storsimple-change-data-encryption-key/HCS_ChangeServiceDataEncryptionKey-include.png)

2. Wählen Sie im **Verschlüsselungsschlüssel für Dienstdaten ändern**-Dialogfeld ein Gerät und autorisieren Sie dieses, um die Änderung des Verschlüsselungsschlüssels für Dienstdaten zu initiieren. Die Dropdownliste enthält alle für die Autorisierung zur Verfügung stehenden Geräte.

3. Klicken Sie auf das Häkchensymbol ![Häkchensymbol](./media/storsimple-change-data-encryption-key/HCS_CheckIcon-include.png).

### Schritt 2: Verwenden Sie Windows PowerShell für StorSimple, um die Änderung des Verschlüsselungsschlüssels für Dienstdaten zu initialisieren.

Dieser Schritt wird in der Windows PowerShell für StorSimple-Schnittstelle auf dem autorisierten StorSimple-Gerät ausgeführt.

> [AZURE.NOTE]Bis zum Abschluss des Schlüssel-Rollovers kann im Verwaltungsportal des StorSimple Managers kein Vorgang ausgeführt werden.

Wenn Sie die Verbindung zur Windows PowerShell-Schnittstelle über die serielle Gerätekonsole herstellen, führen Sie die folgenden Schritte durch.

#### Initiierung der Änderung des Verschlüsselungsschlüssels für Dienstdaten

1. Wählen Sie Option 1, um sich mit Vollzugriff anzumelden.

2. Geben Sie an der Eingabeaufforderung Folgendes ein:

     `Invoke-HcsmServiceDataEncryptionKeyChange`

3. Nachdem das Cmdlet erfolgreich abgeschlossen wurde, erhalten Sie einen neuen Verschlüsselungsschlüssel für Dienstdaten. Kopieren und speichern Sie diesen Schlüssel, da Sie ihn für Schritt 3 dieses Prozesses benötigen. Dieser Schlüssel wird zur Aktualisierung aller Geräte benötigt, die beim StorSimple Manager-Dienst registriert sind.

    > [AZURE.NOTE]Dieser Prozess muss innerhalb von vier Stunden nach der Autorisierung eines StorSimple-Geräts initiiert werden.

    Der neue Schlüssel wird dann an den Dienst gesendet und an alle bei diesem Dienst registrierten Geräte übermittelt. Im Dienst-Dashboard wird eine Warnung angezeigt. Der Dienst deaktiviert alle Vorgänge auf den registrierten Geräten. Dann muss der Geräteadministrator den Verschlüsselungsschlüssel für Dienstdaten auf den anderen Geräten aktualisieren. E/A-Vorgänge (von Hosts an die Cloud übermittelte Daten) werden jedoch nicht unterbrochen.

    Wenn Sie nur ein Gerät beim Dienst registriert haben, ist der Rollover-Prozess nun abgeschlossen und Sie können den nächsten Schritt überspringen. Wenn Sie mehrere Geräte beim Dienst registriert haben, fahren Sie mit Schritt 3 fort.

### Schritt 3: Aktualisieren des Verschlüsselungsschlüssels für Dienstdaten auf anderen StorSimple-Geräten

Wenn Sie mehrere Geräte bei Ihrem StorSimple Manager-Dienst registriert haben, müssen Sie die folgenden Schritte in der Windows PowerShell-Schnittstelle Ihres StorSimple-Geräts durchführen. Verwenden Sie den Schlüssel, den Sie in Schritt 2 („Verwenden Sie Windows PowerShell für StorSimple, um die Änderung des Verschlüsselungsschlüssels für Dienstdaten zu initialisieren.“) erhalten haben, um alle verbleibenden, beim StorSimple Manager-Dienst registrierten StorSimple-Geräte zu aktualisieren.

Führen Sie die folgenden Schritte aus, um den Verschlüsselungsschlüssel für Dienstdaten auf Ihrem Gerät zu aktualisieren.

#### Aktualisieren des Verschlüsselungsschlüssels für Dienstdaten

1. Verwenden Sie Windows PowerShell für StorSimple für die Verbindung mit der Konsole. Wählen Sie Option 1, um sich mit Vollzugriff anzumelden.

2. Geben Sie an der Eingabeaufforderung Folgendes ein:

    `Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`

3. Geben Sie den Verschlüsselungsschlüssel für Dienstdaten ein, den Sie in [Schritt2: Verwenden Sie Windows PowerShell für StorSimple, um die Änderung des Verschlüsselungsschlüssels für Dienstdaten zu initialisieren.](##to-initiate-the-service-data-encryption-key-change) erhalten haben.

<!---HONumber=July15_HO1-->