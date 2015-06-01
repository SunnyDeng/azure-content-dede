1. Melden Sie sich am [klassischen Azure-Portal](http://manage.windowsazure.com) an. Testen Sie das [kostenlose Testkonto](http://azure.microsoft.com/pricing/free-trial/), wenn Sie noch kein Abonnement haben.

2. Klicken Sie in der Befehlsleiste am unteren Ende des Fensters auf **Neu**.

3. Unter **Compute** klicken Sie auf **Virtueller Computer** und anschließend auf **From Gallery**.

	![Navigieren Sie zu From Gallery in der Befehlsleiste](./media/virtual-machines-create-WindowsVM/fromgallery.png)
	
4. Im ersten Bildschirm können Sie für Ihren virtuellen Computer ein **Image** aus einer Liste in der Image-Galerie auswählen. (Die verfügbaren Abbilder können sich je nach dem von Ihnen verwendeten Abonnement unterscheiden.) Klicken Sie auf den Pfeil, um fortzufahren.

	![Wählen Sie ein Image aus](./media/virtual-machines-create-WindowsVM/chooseimage.png)

5. Im zweiten Bildschirm wählen Sie einen Computernamen, Größe sowie Benutzername und Passwort des Administrators aus. Wenn Sie den virtuellen Azure-Computer nur ausprobieren möchten, füllen Sie die Felder wie im Bild unten angegeben aus. Andernfalls wählen Sie die benötigte Schicht und die Größe, um Ihre Anwendung oder Ihren Leistungsumfang auszuführen. Nachfolgend finden Sie detaillierte Informationen, die Ihnen beim Ausfüllen helfen:
	
	- **Neuer Benutzername** bezieht sich auf das Administratorkonto, dass Sie zur Verwaltung des Servers verwenden. Geben Sie für dieses Konto ein individuelles Passwort an und stellen Sie sicher, dass Sie es nicht vergessen. **Sie brauchen den Benutzernamen und das Passwort, um sich am virtuellen Computer anzumelden**. 

	- Die Größe eines virtuellen Computers wirkt sich auf seine Betriebskosten sowie auf die Konfigurationsoptionen, wie die Anzahl der Datenträger, die Sie anschließen können, aus. Detaillierte Informationen finden Sie unter [Größen virtueller Computer und Clouddienste für Azure](http://go.microsoft.com/fwlink/p/?LinkId=466520).

	![Konfigurieren der Eigenschaften des virtuellen Computers](./media/virtual-machines-create-WindowsVM/vmconfiguration.png)



6. Im dritten Bildschirm können Sie Ressourcen für Netzwerkerstellung, Speicherung und Verfügbarkeit konfigurieren. Nachfolgend finden Sie detaillierte Informationen, die Ihnen beim Ausfüllen helfen: 
	

	- Der DNS-Name des Cloud-Diensts ist der globale DNS-Name, der Teil des URIs wird, der zum Kontaktieren des virtuellen Computers verwendet wird. Sie müssen einen eigenen Namen für den Cloud-Dienst eingeben, da dieser in Azure eindeutig sein muss. Clouddienste sind wichtig für Szenarios, die verwenden [mehrere virtuelle Computer](../articles/cloud-services-connect-virtual-machine.md).
 
	- Bei **Region/Affinitätsgruppe/Virtuelles Netzwerk** geben Sie eine Region an, die zu Ihrem Ort passt. Sie können stattdessen auch ein virtuelles Netzwerk angeben.
 
	>[AZURE.NOTE] Wenn der virtuelle Computer ein virtuelles Netzwerk verwenden soll, müssen Sie das virtuelle Netzwerk beim Erstellen des virtuellen Computers angeben.Sie können den virtuellen Computer nicht zu einem virtuellen Netzwerk hinzufügen, nachdem Sie ihn erstellt haben. Weitere Informationen finden Sie unter [Azure Virtual Network Overview](http://go.microsoft.com/fwlink/p/?LinkID=294063).

	- Detaillierte Informationen über die Konfiguration von Endpunkten finden Sie unter [Einrichten von Endpunkten für einen virtuellen Computer](../articles/virtual-machines-set-up-endpoints-classic-portal.md).

	![Configure the connected resources of the virtual machine](./media/virtual-machines-create-WindowsVM/resourceconfiguration.png)

7. Im vierten Konfigurationsbildschirm können Sie den VM-Agent und einige der verfügbaren Erweiterungen konfigurieren. Aktivieren Sie das Kontrollkästchen, um den virtuellen Computer zu erstellen.


	![Configure VM Agent and extensions for the virtual machine](./media/virtual-machines-create-WindowsVM/agent-and-extensions.png)

	>[AZURE.NOTE] Der VM-Agent stellt Ihnen die Umgebung für das Installieren von Erweiterungen bereit, die Ihnen bei der Interaktion mit dem virtuellen Computer oder bei dessen Verwaltung helfen können. Einzelheiten finden Sie unter [Using Extensions](http://go.microsoft.com/FWLink/p/?LinkID=390493).  
    
8. Nach der Erstellung des virtuellen Computers wird der neue virtuelle Computer im klassischen Portal unter **Virtuelle Computer** aufgelistet. In diesen Abschnitten werden auch der entsprechende Clouddienst und das Speicherkonto erstellt und aufgelistet. Der virtuelle Computer und der Clouddienst werden automatisch gestartet. Das Verwaltungsportal zeigt ihren Status als **Laufend** an. 

	![Konfiguration des VM-Agents und der Endpunkte des virtuellen Computers](./media/virtual-machines-create-WindowsVM/vmcreated.png) 
<!--HONumber=52-->
