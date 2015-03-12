1. Melden Sie sich am [Azure-Verwaltungsportal](http://manage.windowsazure.com) an. Testen Sie die [kostenlose Testversion](http://azure.microsoft.com/pricing/free-trial/), wenn Sie noch nicht über ein Abonnement verfügen.

2. Klicken Sie unten im Fenster auf der Befehlsleiste auf **Neu**.

3. Klicken Sie unter **Berechnen** auf **Virtueller Computer** und anschließend auf **Aus Galerie**.

	![Navigate to From Gallery in the Command Bar](./media/virtual-machines-create-WindowsVM/fromgallery.png)
	
4. Auf dem ersten Bildschirm können Sie die Option **Image auswählen** für Ihren virtuellen Computer aus einer der Listen in der Image-Galerie auswählen. (Die verfügbaren Abbilder können sich je nach dem von Ihnen verwendeten Abonnement unterscheiden.) Klicken Sie auf den Pfeil, um fortzufahren.

	![Choose an image](./media/virtual-machines-create-WindowsVM/chooseimage.png)

5. Auf dem zweiten Bildschirm können Sie einen Computernamen, die Größe sowie den Administratorbenutzernamen und das -kennwort auswählen. Wenn Sie Azure Virtual Machines einfach testen möchten, füllen Sie die Felder aus, wie dies im folgenden Bild gezeigt wird. Wählen Sie ansonsten eine Stufe und die Größe aus, die erforderlich sind, damit Ihre App oder Ihre Arbeitsauslastung ausgeführt werden kann. Im Folgenden finden Sie einige Details, die Ihnen beim Ausfüllen helfen: 
	
	- **Neuer Benutzername** bezieht sich auf das Administratorkonto, das Sie zum Verwalten des Servers verwenden. Erstellen Sie ein eindeutiges Kennwort für dieses Konto, und merken Sie sich das Kennwort. **Sie benötigen Benutzername und Kennwort, um sich am virtuellen Computer anzumelden**. 

	- Die Größe eines virtuellen Computers wirkt sich auf die Kosten für dessen Verwendung sowie auf die Konfigurationsoptionen wie die Anzahl der von Ihnen anfügbaren Datenträger aus. Detaillierte Informationen finden Sie unter [Größen virtueller Computer und Cloud-Dienste für Azure](http://go.microsoft.com/fwlink/p/?LinkId=466520).

	![Configure the properties of the virtual machine](./media/virtual-machines-create-WindowsVM/vmconfiguration.png)

6. Auf dem dritten Bildschirm können Sie die Ressourcen für Netzwerk, Speicher und Verfügbarkeit konfigurieren. Im Folgenden finden Sie einige Details, die Ihnen beim Ausfüllen helfen: 

	- Der **DNS-Name des Cloud-Diensts** ist der globale DNS-Name, der Teil des URIs wird, der zum Kontaktieren des virtuellen Computers verwendet wird. Sie müssen einen eigenen Namen für den Cloud-Dienst eingeben, da dieser in Azure eindeutig sein muss. Cloud-Dienste sind wichtig für Szenarien mit [mehreren virtuellen Computern](http://azure.microsoft.com/documentation/articles/cloud-services-connect-virtual-machine/).
 
	- Verwenden Sie für **Region/Affinitätsgruppe/Virtuelles Netzwerk** eine Region, die für Ihren Standort angemessen ist. Sie können stattdessen ein virtuelles Netzwerk angeben.
 
	>[AZURE.NOTE] Wenn der virtuelle Computer ein virtuelles Netzwerk verwenden soll, **müssen** Sie das virtuelle Netzwerk beim Erstellen des virtuellen Computers angeben. Sie können den virtuellen Computer nicht mit einem virtuellen Netzwerk verknüpfen, nachdem Sie den virtuellen Computer erstellt haben. Weitere Informationen finden Sie unter [Überblick über Azure Virtual Network](http://go.microsoft.com/fwlink/p/?LinkID=294063).

	- Unter [Gewusst wie: Einrichten von Endpunkten für einen virtuellen Computer](http://azure.microsoft.com/documentation/articles/virtual-machines-set-up-endpoints/) finden Sie Einzelheiten über das Konfigurieren von Endpunkten.

	![Configure the connected resources of the virtual machine](./media/virtual-machines-create-WindowsVM/resourceconfiguration.png)

7. Auf dem vierten Konfigurationsbildschirm können Sie den VM-Agent und einige der verfügbaren Erweiterungen konfigurieren. Aktivieren Sie das Kontrollkästchen, um den virtuellen Computer zu erstellen.

	![Configure VM Agent and extensions for the virtual machine](./media/virtual-machines-create-WindowsVM/agent-and-extensions.png)

	>[AZURE.NOTE] Der VM-Agent stellt Ihnen die Umgebung für das Installieren von Erweiterungen bereit, die Ihnen bei der Interaktion mit dem virtuellen Computer oder bei dessen Verwaltung helfen können. Weitere Informationen erhalten Sie unter [Verwenden von Erweiterungen](http://go.microsoft.com/FWLink/p/?LinkID=390493).  
    
8. Nach der Erstellung des virtuellen Computers führt das Verwaltungsportal den neuen virtuellen Computer unter **Virtuelle Computer** auf. Der entsprechende Cloud-Dienst bzw. das Speicherkonto werden ebenfalls in diesen Abschnitten erstellt und aufgeführt. Der virtuelle Computer und der Cloud-Dienst werden automatisch gestartet, und das Verwaltungsportal zeigt ihren Status als **Wird ausgeführt** an. 

	![Configure VM Agent and the endpoints of the virtual machine](./media/virtual-machines-create-WindowsVM/vmcreated.png)

<!--HONumber=42-->
