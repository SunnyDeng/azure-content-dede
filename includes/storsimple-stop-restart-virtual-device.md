#### So beenden und starten Sie ein virtuelles Gerät
Um ein virtuelles Gerät zu beenden, klicken Sie auf dessen Namen und anschließend auf **Herunterfahren**. Während des Herunterfahrens des virtuellen Geräts wird der Status **Wird beendet** angezeigt. Nach dem Beenden des virtuellen Geräts ändert sich der Status in **Beendet**.

Verwenden Sie die folgenden Cmdlets zum Beenden und Starten eines virtuellen Geräts.

`Stop-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`


`Start-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`
    
#### So starten Sie ein virtuelles Gerät neu

Wenn ein virtuelles Gerät ausgeführt wird und Sie dieses neu starten möchten, klicken Sie auf den Namen und anschließend auf **Neu starten**. Während des Neustarts des virtuellen Geräts wird der Status **Wird neu gestartet** angezeigt. Wenn das virtuelle Gerät zur Verwendung bereit ist, wird der Status **Wird ausgeführt** angezeigt.

Verwenden Sie die folgenden Cmdlets für den Neustart eines virtuellen Geräts.

`Restart-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

<!---HONumber=AcomDC_1217_2015-->