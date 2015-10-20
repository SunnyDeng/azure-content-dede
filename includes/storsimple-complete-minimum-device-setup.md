<!--author=alkohli last changed: 9/17/15-->

#### So führen Sie die mindestens erforderliche StorSimple-Gerätekonfiguration aus

1. Wählen Sie auf der Seite **Geräte** das Gerät aus, klicken Sie auf den Pfeil neben dem Gerätenamen, um zur Seite für dieses Gerät zu wechseln. 

	![Geräteseite mit online geschaltetem Gerät](./media/storsimple-complete-minimum-device-setup/HCS_DevicesPageM-include.png)

2. Klicken Sie auf das Schnellstartsymbol ![Schnellstart-Symbol](./media/storsimple-complete-minimum-device-setup/HCS_QuickStartIcon-include.png), um auf die Schnellstartseite für das Gerät zuzugreifen. Klicken Sie auf **Geräteinstallation abschließen**, um den **Gerät konfigurieren**-Assistenten zu starten.

	![Schnellstartseite für das Gerät](./media/storsimple-complete-minimum-device-setup/Device_Quick_Start_page_1M.png)

2. Gehen Sie auf der Seite **Grundeinstellungen** folgendermaßen vor:
  1. Geben Sie einen **Anzeigenamen** für Ihr Gerät an. Der Standardgerätename enthält Informationen zum Gerätemodell und zur Seriennummer. Sie können einen Anzeigenamen zuweisen, der bis zu 64 Zeichen lang ist, um Ihr Gerät zu verwalten.
  2. Legen Sie die **Zeitzone** basierend auf dem geografischen Standort fest, an dem das Gerät bereitgestellt wird. Ihr Gerät verwendet diese Zeitzone für alle geplanten Vorgänge.
  3. Geben Sie unter **DNS-Einstellungen** eine Adresse für die Angabe **Sekundärer DNS-Server** an. Wenn Sie IPv6 verwenden, wird das Feld basierend auf dem IPv6-Präfix ausgefüllt, das in der Windows PowerShell-Benutzeroberfläche angegeben wurde. Wenn der sekundäre DNS-Server nicht konfiguriert ist, können Sie Ihre Gerätekonfiguration nicht speichern.
  4. Aktivieren Sie unter "Aktivierte iSCI Schnittstellen" mindestens ein Netzwerk für iSCSI. Mindestens eine Netzwerkschnittstelle muss cloudfähig sein, und eine Schnittstelle muss iSCSI-aktiviert sein. DATA 0 ist automatisch cloudfähig.
 
      ![Grundlegende Einstellungen für die minimale Konfiguration von StorSimple-Geräten](./media/storsimple-complete-minimum-device-setup/HCS_MinDeviceSetupBasicSettings1-include.png)

3. Klicken Sie auf das Pfeilsymbol ![StorSimple, Pfeilsymbol](./media/storsimple-complete-minimum-device-setup/HCS_ArrowIcon-include.png).

4. Geben Sie auf der Seite **Netzwerkschnittstellen** die festen IP-Adressen für Controller 0 und Controller 1 an. Wenn die DATA 0-Schnittstelle für IPv4 konfiguriert wurde, müssen die festen IP-Adressen im IPv4-Format angegeben werden. Wenn Sie ein Präfix für die IPv6-Konfiguration angegeben haben, werden die festen IP-Adressen in diesen Feldern automatisch mit Daten aufgefüllt.


	> [AZURE.NOTE]
 	> 
 	> - Die festen IP-Adressen des Controllers müssen freie IP-Adressen in dem Subnetz sein, auf das die IP-Adresse des Geräts zugreifen kann.
 	> - Die festen IP-Adressen für den Controller werden dazu verwendet, die Updates für das Gerät vorzunehmen. Daher ist es erforderlich, dass die festen IP-Adressen routingfähig und für Verbindungen mit dem Internet geeignet sind.

    ![Netzwerkschnittstellen für die minimale Konfiguration von StorSimple-Geräten](./media/storsimple-complete-minimum-device-setup/HCS_MinDeviceSetupNetworkInterfaces2-include.png)

5. Klicken Sie auf das Häkchensymbol ![StorSimple, Häkchensymbol](./media/storsimple-complete-minimum-device-setup/HCS_CheckIcon-include.png). Sie gelangen zurück auf die Seite **Schnellstart** des Geräts.

 >[AZURE.NOTE]Alle anderen Geräteeinstellungen können über die Seite **Konfigurieren** geändert werden.

![Video verfügbar](./media/storsimple-complete-minimum-device-setup/Video_icon.png) **Video verfügbar**

Sie können sich [hier](http://azure.microsoft.com/documentation/videos/minimum-storsimple-device-setup/) ein Video anschauen, in dem die mindestens erforderliche Gerätekonfiguration demonstriert wird.

<!---HONumber=Oct15_HO3-->