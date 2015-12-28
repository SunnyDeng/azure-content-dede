#### So erstellen Sie einen virtuellen Computer

1.  Wechseln Sie im Azure-Portal zum **StorSimple Manager**-Dienst.

2. Wechseln Sie zur Seite **Geräte**. Klicken Sie auf der Seite **Geräte** unten auf **Virtuelles Gerät erstellen**.

3. Geben Sie im Dialogfeld **Virtuelles Gerät erstellen** folgende Details an:

     ![StorSimple – virtuelles Gerät erstellen](./media/storsimple-create-virtual-device-u2/CreatePremiumsva1.png)

	1. **Name** – ein eindeutiger Name für Ihr virtuelles Gerät.


	2. **Modell** – wählen Sie das Modell des virtuellen Geräts aus. Dieses Feld wird nur angezeigt, wenn Update 2 oder höher ausgeführt wird. Ein 8010-Gerätemodell bietet 30 TB Standardspeicher, und ein 8020-Modell bietet 64 TB Storage Premium-Speicher. Geben Sie 8010 an,
	3.  um Abrufszenarios auf Objektebene aus Sicherungen bereitzustellen. Wählen Sie 8020 aus, um Workloads mit hoher Leistung und geringer Latenz bereitzustellen oder um das Gerät als sekundäres Gerät für die Notfallwiederherstellung zu verwenden.
	 
	4. **Version** – wählen Sie die Version des virtuellen Geräts aus. Wenn ein 8020-Gerätemodell ausgewählt wurde, wird das Versionsfeld nicht für den Benutzer angezeigt. Diese Option ist nicht vorhanden, wenn alle für diesen Dienst registrierten physischen Geräte über Update 1 (oder höher) verfügen. Dieses Feld wird nur angezeigt, wenn Sie eine Kombination aus physischen Geräten ohne und mit Update 1 im gleichen Dienst registriert haben. Angesichts der Tatsache, dass die Version des virtuellen Geräts angibt, von welchem physischen Gerät Sie ein Failover ausführen oder klonen können, ist es wichtig, eine geeignete Version des virtuellen Geräts zu erstellen. Auswahl:

	   - Versionsupdate 0.3, wenn Sie ein Failover oder eine Notfallwiederherstellung von einem physischen Gerät mit Update 0.3 oder früher ausführen. 
	   - Versionsupdate 1, wenn Sie ein Failover oder einen Klonvorgang von einem physischen Gerät mit Update 1 (oder höher) ausführen. 
	   
	
	5. **Virtuelles Netzwerk** – geben Sie einen Namen für das virtuelle Netzwerk ein, der für dieses virtuelle Gerät verwendet werden soll. Wenn Sie Storage Premium-Speicher (Update 2 oder höher) verwenden, müssen Sie ein virtuelles Netzwerk auswählen, das vom Storage Premium-Konto unterstützt wird. Die nicht unterstützten virtuellen Netzwerke werden in der Dropdownliste abgeblendet. Sie erhalten eine Warnung, wenn Sie ein nicht unterstütztes virtuelles Netzwerk auswählen. 

	5. **Speicherkonto zum Erstellen des virtuellen Geräts** – wählen Sie das Speicherkonto aus, das bei der Bereitstellung zum Speichern des Images des virtuellen Geräts verwendet wird. Dieses Speicherkonto sollte sich in der gleichen Region wie das virtuelle Gerät und das virtuelle Netzwerk befinden. Es sollte weder vom physischen noch vom virtuellen Gerät zum Speichern von Daten verwendet werden. Hierfür wird standardmäßig ein neues Speicherkonto erstellt. Wenn Sie jedoch bereits über ein hierfür geeignetes Speicherkonto verfügen, können Sie dieses aus der Liste auswählen. Wenn Sie ein virtuelles Premium-Gerät erstellen, werden in der Dropdownliste nur Storage Premium-Speicherkonten angezeigt.

    	>[AZURE.NOTE]Das virtuelle Gerät funktioniert nur mit Azure-Speicherkonten. Andere Clouddienstanbieter, wie z. B. Amazon, HP und OpenStack (die vom physischen Gerät unterstützt werden), werden für das virtuelle StorSimple-Gerät nicht unterstützt.
	
	1. Klicken Sie auf das Häkchen, um zu bestätigen, dass die auf dem virtuellen Gerät gespeicherten Daten in einem Microsoft-Datencenter gehostet werden. Wenn Sie lediglich ein physisches Gerät verwenden, wird der Verschlüsselungsschlüssel auf Ihrem Gerät beibehalten. Daher kann Microsoft dieses nicht entschlüsseln.
	 
		Wenn Sie ein virtuelles Gerät verwenden, werden die Ver- und Entschlüsselungsschlüssel in Microsoft Azure gespeichert. Weitere Informationen finden Sie unter [Sicherheitsüberlegungen bei der Verwendung eines virtuellen Geräts](storsimple-security/#storsimple-virtual-device-security).
	2. Aktivieren Sie das Kontrollkästchen, um das virtuelle Gerät zu erstellen. Die Bereitstellung des Geräts kann etwa 30 Minuten dauern.

	![StorSimple – virtuelles Gerät in Erstellungsphase](./media/storsimple-create-virtual-device-u2/StorSimple_VirtualDeviceCreating1M.png)

    

<!---HONumber=AcomDC_1217_2015-->