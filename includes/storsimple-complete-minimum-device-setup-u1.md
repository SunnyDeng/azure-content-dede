<!--author=alkohli last changed: 9/17/15-->

#### So führen Sie die mindestens erforderliche StorSimple-Gerätekonfiguration aus

1. Wählen Sie das Gerät aus, und klicken Sie dann auf **Schnellstart**. Klicken Sie auf **Complete device setup**, um den Assistenten zum Konfigurieren von Geräten zu starten.

2. Gehen Sie im Assistenten zum Konfigurieren von Geräten im Dialogfeld **Grundlegende Einstellungen** folgendermaßen vor:
  1. Geben Sie einen **Anzeigenamen** für Ihr Gerät an. Der Standardgerätename enthält Informationen zum Gerätemodell und zur Seriennummer. Sie können einen Anzeigenamen zuweisen, der bis zu 64 Zeichen lang ist, um Ihr Gerät zu verwalten.
  2. Legen Sie die **Zeitzone** basierend auf dem geografischen Standort fest, an dem das Gerät bereitgestellt wird. Ihr Gerät verwendet diese Zeitzone für alle geplanten Vorgänge.
  3. Geben Sie unter **DNS-Einstellungen** eine Adresse für die Angabe **Sekundärer DNS-Server** an. Wenn Sie IPv6 verwenden, wird das Feld basierend auf dem IPv6-Präfix ausgefüllt, das in der Windows PowerShell-Benutzeroberfläche angegeben wurde. Wenn der sekundäre DNS-Server nicht konfiguriert ist, können Sie Ihre Gerätekonfiguration nicht speichern.
  4. Aktivieren Sie unter "Aktivierte iSCI Schnittstellen" mindestens ein Netzwerk für iSCSI. Mindestens eine Netzwerkschnittstelle muss cloudfähig sein, und eine Schnittstelle muss iSCSI-aktiviert sein. DATA 0 ist automatisch cloudfähig.
 
      ![Grundlegende Einstellungen für die minimale Konfiguration von StorSimple-Geräten](./media/storsimple-complete-minimum-device-setup-u1/HCS_MinDeviceSetupBasicSettings1-include.png)

3. Klicken Sie auf das Pfeilsymbol ![StorSimple, Pfeilsymbol](./media/storsimple-complete-minimum-device-setup/HCS_ArrowIcon-include.png).

4. Geben Sie im Dialogfeld **Netzwerkschnittstellen** die festen IP-Adressen für Controller 0 und Controller 1 an. **Die festen IP-Adressen des Controllers müssen freie IP-Adressen in dem Subnetz sein, auf das die IP-Adresse des Geräts zugreifen kann.** Wenn die DATA 0-Schnittstelle für IPv4 konfiguriert wurde, müssen die festen IP-Adressen im IPv4-Format angegeben werden. Wenn Sie ein Präfix für die IPv6-Konfiguration angegeben haben, werden die festen IP-Adressen in diesen Feldern automatisch mit Daten aufgefüllt.


    ![Netzwerkschnittstellen für die minimale Konfiguration von StorSimple-Geräten](./media/storsimple-complete-minimum-device-setup-u1/HCS_MinDeviceSetupNetworkInterfaces2-include.png)

    Die festen IP-Adressen für den Controller werden dazu verwendet, die Updates für das Gerät vorzunehmen. Daher ist es erforderlich, dass die festen IP-Adressen routingfähig und für Verbindungen mit dem Internet geeignet sind. Mit dem Cmdlet [Test-HcsmConnection][Test] können Sie feststellen, ob die festen IP-Adressen Ihres Controllers routingfähig sind. Im folgenden Beispiel sind feste Controller-IP-Adressen dargestellt, die ins Internet weitergeleitet werden und auf die Microsoft Update-Server zugreifen können.

     ![Test-HcsmConnection mit Darstellung routingfähiger IP-Adressen](./media/storsimple-complete-minimum-device-setup-u1/Test-HcsmConnectionOutputRegisteredDevice.png)

5. Klicken Sie auf das Häkchensymbol ![StorSimple, Häkchensymbol](./media/storsimple-complete-minimum-device-setup/HCS_CheckIcon-include.png). Sie gelangen zurück auf die Seite **Schnellstart** des Geräts.

 >[AZURE.NOTE]Alle anderen Geräteeinstellungen können über die Seite **Konfigurieren** geändert werden.

<!--Link reference-->
[Test]: https://technet.microsoft.com/library/dn715782(v=wps.630).aspx

<!---HONumber=Oct15_HO3-->