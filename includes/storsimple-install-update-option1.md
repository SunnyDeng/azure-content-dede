<!--author=SharS last changed: 03/17/2016-->

#### So laden Sie Hotfixes herunter

Führen Sie die folgenden Schritte aus, um das Softwareupdate herunterzuladen.

1. Starten Sie Internet Explorer, und navigieren Sie zu [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).

2. Falls Sie den Microsoft Update-Katalog zum ersten Mal auf diesem Computer verwenden, klicken Sie auf **Installieren**, wenn Sie zum Installieren des Add-Ons für den Microsoft Update-Katalog aufgefordert werden. ![Katalog installieren](./media/storsimple-install-update-option-1/HCS_InstallCatalog-include.png)

3. Geben Sie im Suchfeld des Microsoft Update-Katalogs die KB-Nummer des Hotfixes ein, den Sie herunterladen möchten (beispielsweise **3063418**), und klicken Sie anschließend auf **Suchen**.

4. Das Paket **StorSimple Update 1.2 Appliance Update** wird angezeigt. Klicken Sie auf **Hinzufügen**. Das Update wird dem Warenkorb hinzugefügt.

5. Suchen Sie nach weiteren Hotfixes aus der obigen Tabelle (**3043005** und **3063416**), und fügen Sie sie jeweils dem Warenkorb hinzu.

5. Klicken Sie auf **Warenkorb anzeigen**.

    ![Warenkorb anzeigen](./media/storsimple-install-update-option-1/HCS_InstallBasket-include.png)

6. Klicken Sie auf **Download**. Geben Sie einen lokalen Speicherort an, oder klicken Sie auf **Durchsuchen**, und navigieren Sie zum gewünschten lokalen Speicherort für den Download. Die Updates werden heruntergeladen und am angegebenen Speicherort in einem nach dem Update benannten Unterordner gespeichert. Der Ordner kann auch in eine Netzwerkfreigabe kopiert werden, auf die vom Gerät aus zugegriffen werden kann.

>   [AZURE.NOTE]
Der Zugriff auf die Hotfixes muss über beide Controller möglich sein, um die Erkennung von Fehlermeldungen des Peercontrollers zu ermöglichen.

#### So installieren und überprüfen Sie Hotfixes für den normalen Modus
Führen Sie zum Installieren und Überprüfen der Hotfixes für den normalen Modus die folgenden Schritte aus. Falls Sie sie bereits über das Azure-Portal installiert haben, fahren Sie mit [Installieren und Überprüfen von Wartungsmodus-Hotfixes](#to-install-and-verify-maintenance-mode-hotfixes) fort.

1. Greifen Sie zum Installieren des Softwareupdates über die serielle Konsole Ihres StorSimple-Geräts auf die Windows PowerShell-Benutzeroberfläche zu. Ausführliche Anweisungen dazu finden Sie unter [Verwenden von PuTTy zum Herstellen einer Verbindung mit der seriellen Konsole](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console). Drücken Sie bei der Eingabeaufforderung die **Eingabetaste**.

4. Wählen Sie **Option 1**, um sich mit Vollzugriff beim Gerät anzumelden.

5. Geben Sie zum Installieren des Updatepakets Folgendes an der Eingabeaufforderung ein:

    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`

    Verwenden Sie die IP-Adresse anstelle von DNS im Freigabepfad des obigen Befehls. Der Anmeldeinformationsparameter wird nur verwendet, wenn Sie auf eine authentifizierte Freigabe zugreifen.

	Es wird empfohlen, den Parameter für die Anmeldeinformationen zu verwenden, um auf Freigaben zuzugreifen. Sogar Freigaben, die für "jeden" offen sind, sind dies in der Regel nicht für nicht authentifizierte Benutzer.

    Nachfolgend sehen Sie eine Beispielausgabe.

        ````
        Controller0>Start-HcsHotfix -Path \\10.100.100.100\share
        \hcsmdssoftwareupdate.exe -Credential contoso\John

        Confirm

        This operation starts the hotfix installation and could reboot one or
        both of the controllers. If the device is serving I/Os, these will not
        be disrupted. Are you sure you want to continue?
        [Y] Yes [N] No [?] Help (default is "Y"): Y

        ````

6. Geben Sie **Y** ein, um die Hotfixinstallation zu bestätigen.

7. Überwachen Sie das Update mithilfe des `Get-HcsUpdateStatus`-Cmdlets.

    Die folgende Beispielausgabe zeigt den Status des Updates. Der Parameter `RunInprogress` ist `True`, wenn das Update ausgeführt wird.

        ````
        Controller0>Get-HcsUpdateStatus
        RunInprogress       : True
        LastHotfixTimestamp : 9/02/2015 10:36:13 PM
        LastUpdateTimestamp : 9/02/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   :
        ````

     Die folgende Beispielausgabe zeigt an, dass das Update abgeschlossen ist. Der Parameter `RunInProgress` ist `False`, wenn das Update abgeschlossen ist.

        ````
        Controller1>Get-HcsUpdateStatus

        RunInprogress       : False
        LastHotfixTimestamp : 9/02/2015 10:56:13 PM
        LastUpdateTimestamp : 9/02/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   :

        ````

	> [AZURE.NOTE] In manchen Fällen gibt das Cmdlet `False` zurück, wenn das Update noch ausgeführt wird. Um sicherzustellen, dass das Update abgeschlossen ist, warten Sie einige Minuten, führen Sie diesen Befehl erneut aus, und überprüfen Sie, ob `RunInProgress` gleich `False` ist. Wenn dies der Fall ist, wurde das Hotfix abgeschlossen.

8. Überprüfen Sie nach dem Abschließen des Softwareupdates die Versionen der Systemsoftware. Geben Sie den folgenden Befehl ein:

    `Get-HcsSystem`

    Daraufhin sollten die folgenden Versionen angezeigt werden.

    - HcsSoftwareVersion: 6.3.9600.17584
    - CisAgentVersion: 1.0.9049.0
    - MdsAgentVersion: 26.0.4696.1433

	Wenn die Versionsnummern nach dem Anwenden des Updates nicht geändert werden, bedeutet dies, dass der Hotfix nicht angewendet werden konnte. Sollte dies der Fall sein, wenden Sie sich an den [Microsoft Support](storsimple-contact-microsoft-support.md), um weitere Unterstützung zu erhalten.

9. Wiederholen Sie die Schritte 3 bis 5, um den verbleibenden Hotfix für den normalen Modus (KB3043005) zu installieren.

#### So installieren und überprüfen Sie Wartungsmodus-Hotfixes

Verwenden Sie KB3063416, um die Datenträger-Firmwareupdates zu installieren. Es handelt sich dabei um beeinträchtigende Updates mit einer Dauer von etwa 30–45 Minuten. Sie können diese während eines geplanten Wartungsfensters durch Herstellen einer Verbindung mit der seriellen Konsole des Geräts installieren.

Folgen Sie den Anweisungen unten, um die Datenträger-Firmwareupdates zu installieren.

1. Versetzen Sie das Gerät in den Wartungsmodus. Beachten Sie, dass Sie kein Windows PowerShell-Remoting verwenden sollten, wenn Sie für ein Gerät im Wartungsmodus eine Verbindung herstellen. Sie müssen dieses Cmdlet auf dem Gerätecontroller ausführen, wenn eine Verbindung über die serielle Konsole des Geräts besteht. Geben Sie Folgendes ein:

    `Enter-HcsMaintenanceMode`

	Nachfolgend sehen Sie eine Beispielausgabe.

		Controller0>Enter-HcsMaintenanceMode
		Checking device state...

		In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
		[Y] Yes [N] No (Default is "Y"): Y

		-----------------------MAINTENANCE MODE------------------------
		Microsoft Azure StorSimple Appliance Model 8100
		Name: Update1-8100-SHG0997879L76YD
		Software Version: 6.3.9600.17584
		Copyright (C) 2014 Microsoft Corporation. All rights reserved.
		You are connected to Controller0 - Passive
		---------------------------------------------------------------
		Serial Console Menu
		[1] Log in with full access
		[2] Log into peer controller with full access
		[3] Connect with limited access
		[4] Change language
		Please enter your choice>

	Daraufhin werden beide Controller im Wartungsmodus gestartet.

3. Geben Sie Folgendes ein, um das Datenträger-Firmwareupdate zu installieren:

    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`

    Nachfolgend sehen Sie eine Beispielausgabe.

        Controller1>Start-HcsHotfix -Path \\10.100.100.100\share\DiskFirmwarePackage.exe -Credential contoso\john
    	Enter Password:
    	WARNING: In maintenance mode, hotfixes should be installed on each controller sequentially. After the hotfix is installed on this controller, install it on the peer controller.
    	Confirm
    	This operation starts a hotfix installation and could reboot one or both of the controllers. Are you sure you want to continue?
    	[Y] Yes [N] No (Default is "Y"): Y
    	WARNING: Installation is currently in progress. This operation can take several minutes to complete.

1.  Überwachen Sie den Installationsstatus mit dem Befehl `Get-HcsUpdateStatus`. Das Update ist abgeschlossen, wenn `RunInProgress` in `False` geändert wird.

2.  Nachdem die Installation abgeschlossen ist, wird der Controller, auf dem der Hotfix für den Wartungsmodus installiert wurde, neu gestartet. Verwenden Sie für die Anmeldung Option 1 mit Vollzugriff, und überprüfen Sie die Firmwareversion des Datenträgers. Geben Sie Folgendes ein:

	`Get-HcsFirmwareVersion`

    Die erwarteten Datenträger-Firmwareversionen lauten:

    `XMGG, XGEE, KZ50, F6C2, VR08`

    Führen Sie den Befehl `Get-HcsFirmwareVersion` auf dem zweiten Controller aus, um zu überprüfen, ob die Softwareversion aktualisiert wurde. Anschließend können Sie den Wartungsmodus beenden. Geben Sie den folgenden Befehl für jeden Gerätecontroller ein:

    `Exit-HcsMaintenanceMode`

1. Beim Beenden des Wartungsmodus werden die Controller neu gestartet. Nachdem die Firmwareupdates für Datenträger erfolgreich installiert wurden und das Gerät den Wartungsmodus verlassen hat, kehren Sie zum klassischen Azure-Portal zurück. Beachten Sie, dass es bis zu 24 Stunden dauern kann, bis die Installation der Wartungsmodus-Updates im Portal angezeigt wird.

<!---HONumber=AcomDC_0323_2016-->