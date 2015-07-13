#### So installieren Sie Update 1 über Windows PowerShell für StorSimple

1. Führen Sie die folgenden Schritte aus, um das Softwareupdate herunterzuladen.

    1. Starten Sie Internet Explorer, und navigieren Sie zu [http://catalog.update.microsoft.com/v7/site/Home.aspx](http://catalog.update.microsoft.com/v7/site/Home.aspx).
    2. Wenn Sie Erstbenutzer sind, werden Sie aufgefordert, den Microsoft Update-Katalog zu installieren. Klicken Sie auf **Installieren**.
    
        ![Katalog installieren](./media/storsimple-install-update-option-1/HCS_InstallCatalog-include.png)

    3. Ein Katalogsuchbildschirm wird angezeigt. Geben Sie im Suchfeld **3063418** ein, und klicken Sie auf **Suchen**.

        ![Katalog durchsuchen](./media/storsimple-install-update-option-1/HCS_SearchCatalog-include.png)

    4. Das Paket **StorSimple Update 1.0-Anwendungsupdate** wird angezeigt. Klicken Sie auf **Hinzufügen**. Das Update wird dem Warenkorb hinzugefügt.

        ![Updatepaket](./media/storsimple-install-update-option-1/HCS_UpdateBundle-include.png)

    5. Klicken Sie auf **Warenkorb anzeigen**.
 
        ![Warenkorb anzeigen](./media/storsimple-install-update-option-1/HCS_InstallBasket-include.png)

    6. Klicken Sie auf **Download**. Geben Sie einen lokalen Speicherort an, oder navigieren Sie zu einem lokalen Speicherort, an dem der Download angezeigt werden soll. Das Update ("all-hcsmdssoftwareupdate_288da2cc8cd2e3c3958b603a79346cb586fb8fe3.exe") wird am ausgewählten Speicherort im Ordner "StorSimple Update 1.0-Anwendungsupdate-Paket (KB3063418)" heruntergeladen. Der Ordner kann auch in eine Netzwerkfreigabe kopiert werden, auf die vom Gerät aus zugegriffen werden kann.
        
2. Greifen Sie zum Installieren des Softwareupdates über die serielle Konsole Ihres StorSimple-Geräts auf die Windows PowerShell-Benutzeroberfläche zu. Ausführliche Anweisungen dazu finden Sie unter [Verwenden von PuTTy zum Herstellen einer Verbindung mit der seriellen Konsole](#use-putty-to-connect-to-the-serial-console).

3. Drücken Sie an der Eingabeaufforderung die EINGABETASTE.

4. Wählen Sie **Option 1**, um sich mit Vollzugriff beim Gerät anzumelden.

5. Geben Sie zum Installieren des Updatepakets Folgendes an der Eingabeaufforderung ein:

    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`

    Der Anmeldeinformationsparameter wird nur verwendet, wenn Sie auf eine authentifizierte Freigabe zugreifen.

    Nachfolgend sehen Sie eine Beispielausgabe.

        ````
        Controller0>Start-HcsHotfix -Path \10.100.100.100\share
        \hcsmdssoftwareupdate.exe -Credential contoso\John
      
        Confirm

        This operation starts the hotfix installation and could reboot one or
        both of the controllers. If the device is serving I/Os, these will not 
        be disrupted. Are you sure you want to continue?
        [Y] Yes [N] No [?] Help (default is "Y"): Y

        ````
 
6. Geben Sie **Y** ein, um die Hotfixinstallation zu bestätigen.

7. Überwachen Sie das Update mit dem Cmdlet "Get-HcsUpdateStatus".

    Die folgende Beispielausgabe zeigt den Status des Updates.

        ````
        Controller0>Get-HcsUpdateStatus
        RunInprogress       : True
        LastHotfixTimestamp : 4/13/2015 10:56:13 PM
        LastUpdateTimestamp : 4/13/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   : 
        ````
 
     Die folgende Beispielausgabe zeigt an, dass das Update abgeschlossen ist.

        ````
        Controller1>Get-HcsUpdateStatus

        RunInprogress       : False
        LastHotfixTimestamp : 4/13/2015 10:56:13 PM
        LastUpdateTimestamp : 4/13/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   :

        ````
 
8. Navigieren Sie nach Abschluss des Softwareupdates im Verwaltungsportal zur Seite "Wartung". Suchen Sie nach verfügbaren Updates. Ihnen sollten nun weitere verfügbare Softwareupdates angezeigt werden.

9. Klicken Sie auf **Updates installieren**, um alle verfügbaren Softwareupdates aus dem Portal anzuwenden.

10. Überprüfen Sie nach dem Abschließen des Softwareupdates die Systemsoftware, die Treiber und die Firmwareversionen. Geben Sie den folgenden Befehl ein:

    `Get-HcsSystem`

    Daraufhin sollten die folgenden Versionen angezeigt werden.

    - HcsSoftwareVersion: 6.3.9600.17491
    - CisAgentVersion: 1.0.9037.0
    - MdsAgentVersion: 26.0.4696.1433 
 
11. Um sicherzustellen, dass die Firmware korrekt aktualisiert wurde, geben Sie Folgendes ein:

    `Start-HcsFirmwareCheck`

    Der Firmwarestatus sollte **UpToDate** lauten.

<!---HONumber=62-->