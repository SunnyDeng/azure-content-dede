<properties authors="kathydav" editor="tysonn" manager="timlt" /> 


#Verbinden virtueller Computer mit einem virtuellen Netzwerk oder einem Cloud-Dienst

Virtuelle Computer müssen mit einem Cloud-Dienst verbunden sein, der als Container fungiert und einen eindeutigen öffentlichen DNS-Namen, eine öffentliche IP-Adresse und einen Satz von Endpunkten bereitstellt, um auf den virtuellen Computer über das Internet zugreifen zu können. Der Cloud-Dienst kann optional auch in einem virtuellen Netzwerk sein. 

Wenn der Cloud-Dienst nicht in einem virtuellen Netzwerk ist, können die virtuellen Computer in diesem Cloud-Dienst mit anderen virtuellen Computern nur über die öffentlichen DNS-Namen der anderen virtuellen Computer kommunizieren und der Datenverkehr läuft über das Internet. Wenn ein Cloud-Dienst mit einem virtuellen Netzwerk verbunden ist, können die virtuellen Computer in diesem Cloud-Dienst mit allen anderen virtuellen Computern in dem virtuellen Netzwerk kommunizieren, ohne dass Daten über das Internet gesendet werden.

Wenn Sie Ihre virtuellen Computer im gleichen eigenständigen Cloud-Dienst platzieren, können Sie Lastenausgleich und Verfügbarkeitsgruppen nutzen. Weitere Informationen finden Sie unter [Lastenausgleich virtueller Computer](../../articles/load-balance-virtual-machines/) und [Verwalten der Verfügbarkeit virtueller Computern](../../articles/manage-availability-virtual-machines/). Sie können die virtuellen Computer jedoch nicht in Subnetzen organisieren oder einen eigenständigen Cloud-Dienst mit Ihrem lokalen Netzwerk verbinden. Beispiel:

![Virtual machines in a standalone cloud service](./media/howto-connect-vm-cloud-service/CloudServiceExample.png)
 
Wenn Sie Ihre virtuellen Computer in einem virtuellen Netzwerk platzieren, können Sie entscheiden, wie viele Cloud-Dienste Sie verwenden möchten, um Lastenausgleich und Verfügbarkeitsgruppen zu nutzen. Darüber hinaus können Sie die virtuellen Computer in Subnetzen auf die gleiche Weise organisieren wie Ihr lokales Netzwerk und das virtuelle Netzwerk mit Ihrem lokalen Netzwerk verbinden. Beispiel:

![Virtual machines in a virtual network](./media/howto-connect-vm-cloud-service/VirtualNetworkExample.png)

Virtuelle Netzwerke sind das empfohlene Verfahren zum Verbinden virtueller Computern in Azure. Die bewährte Methode ist es, jede Ebene der Anwendung in einem separaten Cloud-Dienst zu konfigurieren. Dies ermöglicht eine Delegierung erweiterter Benutzerrechte über die rollenbasierte Zugriffssteuerung (Role Based Access Control, RBAC). Weitere Informationen finden Sie unter [Rollenbasierte Zugriffssteuerung im Azure-Vorschauportal](../../articles//role-based-access-control-configure/). Möglicherweise müssen Sie jedoch einige virtuelle Computer aus verschiedenen Anwendungsebenen im gleichen Cloud-Dienst kombinieren, um die Höchstzahl von 200 Cloud-Diensten pro Abonnement nicht zu überschreiten.

So verbinden Sie virtuelle Computer in einem virtuellen Netzwerk:

1.	Erstellen Sie das virtuelle Netzwerk im Azure-Verwaltungsportal. Weitere Informationen finden Sie unter [Aufgaben bei der Konfiguration virtueller Netzwerke](https://msdn.microsoft.com/library/azure/jj156206.aspx).
2.	Erstellen Sie die Gruppe von Cloud-Diensten für Ihre Bereitstellung im Azure-Verwaltungsportal entsprechend Ihrem Entwurf für Verfügbarkeitsgruppen und Lastenausgleich mit **Neu > Berechnen > Cloud-Dienst > Benutzerdefiniert erstellen**.
3.	Geben Sie beim Erstellen jedes neuen virtuellen Computers den richtigen Cloud-Dienst und Ihr virtuelles Netzwerk an. Wenn der Cloud-Dienst zuvor mit Ihrem virtuellen Netzwerk verbunden wurde, ist der Name dieses Diensts bereits für Sie ausgewählt.

Hier ein Beispiel unter Verwendung des Azure-Verwaltungsportals.

![Selecting a cloud service for a virtual machine](./media/howto-connect-vm-cloud-service/VMConfig1.png)
 
So verbinden Sie virtuelle Computer in einem eigenständigen Cloud-Dienst:

1.	Erstellen Sie den Cloud-Dienst für Ihre Bereitstellung im Azure-Verwaltungsportal mit **Neu > Berechnen > Cloud-Dienst > Benutzerdefiniert erstellen**.
2.	Geben Sie beim Erstellen des virtuellen Computers den Namen des in Schritt 1 erstellten Cloud-Diensts an. 
Alternativ können Sie beim Erstellen des ersten virtuellen Computers den Cloud-Dienst für Ihre Bereitstellung erstellen.

Hier ein Beispiel unter Verwendung des Azure-Verwaltungsportals für den vorhandenen Cloud-Dienst mit dem Namen EndpointTest.
 
![Add a virtual machine to an existing cloud service](./media/howto-connect-vm-cloud-service/Connect-VM-to-CS.png)

##Ressourcen
[Lastenausgleich virtueller Computer](../../articles/load-balance-virtual-machines/)

[Verwalten der Verfügbarkeit virtueller Computer](../../articles/manage-availability-virtual-machines/)

[Konfigurationsaufgaben für virtuelle Netzwerke](https://msdn.microsoft.com/library/azure/jj156206.aspx)

Nachdem Sie einen virtuellen Computer erstellt haben, empfiehlt es sich, einen Datenträger hinzuzufügen, damit ein Speicherort für die Daten der Dienste und Arbeitsauslastungen verfügbar ist. Weitere Informationen finden Sie unter einer der folgenden Optionen:

[Gewusst wie: Anfügen eines Datenträgers an einen virtuellen Linux-Computer](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-how-to-attach-disk/)

[Gewusst wie: Anfügen eines Datenträgers an einen Windows-Computer](http://azure.microsoft.com/documentation/articles/storage-windows-attach-disk/)


<!--HONumber=45--> 
