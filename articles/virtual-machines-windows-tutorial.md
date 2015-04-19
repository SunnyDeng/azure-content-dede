<properties 
	pageTitle="Erstellen eines virtuellen Computers unter Windows in Azure" 
	description="Erfahren Sie, wie Sie einen virtuellen Computer unter Windows in Azure erstellen, sich anschließend anmelden und einen Datenträger anfügen können." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/04/2015" 
	ms.author="kathydav"/>



# Erstellen eines virtuellen Computers unter Windows

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/virtual-machines-windows-tutorial/" title="Azure Portal" class="current">Azure-Portal</a><a href="/documentation/articles/virtual-machines-windows-tutorial-azure-preview/" title="Azure Preview Portal">Azure-Vorschauportal</a></div>

In diesem Lernprogramm erfahren Sie, wie einfach es ist, einen virtuellen Azure-Computer (Azure-VM) zu erstellen. In diesem Lernprogramm wird ein Windows Server-Image verwendet, aber dies ist nur eines der über Azure verfügbaren zahlreichen Images. Dies umfasst Windows-Betriebssysteme, Linux-basierte Betriebssysteme und Images mit vorinstallierten Anwendungen. Welche Images Sie auswählen können, hängt jeweils von der Art Ihres Abonnements ab. Beispielsweise können Desktop-Images für MSDN-Abonnenten verfügbar sein.


Sie können auch mithilfe [eigener Images als Vorlagen entsprechende virtuelle Windows-Computer erstellen](virtual-machines-create-upload-vhd-windows-server.md). Weitere Informationen zu virtuellen Azure-Computern finden Sie unter [Übersicht über Azure Virtual Machines](http://msdn.microsoft.com/library/azure/jj156143.aspx).

[AZURE.INCLUDE [free-trial-note](../includes/free-trial-note.md)]

## <a id="createvirtualmachine"> </a>Erstellen eines virtuellen Computers

In diesem Abschnitt erfahren Sie, wie Sie die Option **Aus Galerie** im Verwaltungsportal verwenden, um einen virtuellen Computer zu erstellen. Mit dieser Option erhalten Sie mehr Konfigurationsoptionen als mit der **Schnellerfassung**. Wenn Sie beispielsweise einen virtuellen Computer mit einem virtuellen Netzwerk verbinden möchten, sollten Sie die Option **Aus Galerie** verwenden.

> [AZURE.NOTE] Sie können auch das funktionsreichere, anpassbare [Azure-Vorschauportal](https://portal.azure.com) verwenden, um einen virtuellen Computer zu erstellen, die Bereitstellung von Anwendungsvorlagen für mehrere Computer zu automatisieren, erweiterte VM-Überwachungs- und Diagnosefeatures zu verwenden usw. Die verfügbaren VM-Konfigurationsoptionen in den beiden Portalen sind ähnlich, aber nicht identisch.  

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]

## <a id="logon"> </a>Gewusst wie: Anmelden bei einem virtuellen Computer nach dessen Erstellung 

In diesem Abschnitt lernen Sie, wie Sie sich bei dem von Ihnen erstellten virtuellen Computer anmelden, um sowohl dessen Einstellungen als auch die darauf ausgeführten Anwendungen zu verwalten.

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../includes/virtual-machines-log-on-win-server.md)]

## <a id="attachdisk"> </a>Gewusst wie: Einfügen eines Datenträgers an den neuen virtuellen Computer 

In diesem Abschnitt erfahren Sie, wie Sie einen leeren Datenträger an den virtuellen Computer anfügen. Weitere Informationen zum Anfügen eines Datenträgers, einschließlich zum Anfügen eines vorhandenen Datenträgers, finden Sie im [Lernprogramm zum Anfügen eines Datenträgers](http://azure.microsoft.com/documentation/articles/storage-windows-attach-disk/).

1. Melden Sie sich am [Azure-Verwaltungsportal](http://manage.windowsazure.com) an.

2. Klicken Sie auf **virtuelle Maschinen**, und wählen Sie dann die **MyTestVM** virtuellen Computer.

	![Select MyTestVM](./media/virtual-machines-windows-tutorial/selectvm.png)
	
3. Möglicherweise wird die Seite "Schnellstart" zuerst angezeigt. Wählen Sie in diesem Fall oben die Option **Dashboard**.

	![Select Dashboard](./media/virtual-machines-windows-tutorial/dashboard.png)

4. Klicken Sie in der Befehlsleiste auf **Anfügen**, und klicken Sie dann auf die Popup-Option **Leeren Datenträger anfügen**.

	![Select Attach from the command bar](./media/virtual-machines-windows-tutorial/commandbarattach.png)	

5. Der **Name des virtuellen Computers**, der **Speicherort**, der **Dateiname** und die **Host-Cache-Voreinstellungen** sind bereits ausgefüllt. Sie müssen lediglich eine Größe für den Datenträger angeben. Geben Sie z. B. **5** in das Feld **Größe** ein. Aktivieren Sie das Kontrollkästchen, um den Datenträger anzufügen.


	>[AZURE.NOTE] Alle Datenträger werden aus einer .vhd-Datei im Azure-Speicher erstellt. **Dateiname** dient für die Benennung der .vhd-Datei, die der Datenträger verwendet, nicht für die Benennung des Datenträgers. Azure weist dem Datenträger automatisch einen Namen zu. 

	![Specify the size of the empty disk](./media/virtual-machines-windows-tutorial/emptydisksize.png)	
	
	>[AZURE.NOTE] Die .vhd-Dateien werden als Seitenblobs im Azure-Speicher gespeichert. Außerhalb von Azure können virtuelle Festplatten entweder das VHD- oder ein VHDX-Format verwenden. Sie können auch fest, dynamisch erweiterbar oder differenzierend sein. Azure unterstützt das VHD-Format; feste Datenträger. Weitere Informationen finden Sie unter [Informationen zu VHDs in Azure](http://msdn.microsoft.com/library/azure/dn790344.aspx)  

6. Kehren Sie zum Dashboard zurück, um zu überprüfen, ob der leere Datenträger erfolgreich an den virtuellen Computer angefügt wurde. Er sollte in der Liste **Datenträger** nach dem BS-Datenträger angezeigt werden.

	![Attach empty disk](./media/virtual-machines-windows-tutorial/disklistwithdatadisk.png)

	Wenn Sie einen Datenträger anfügen, ist dieser offline und nicht initialisiert. Bevor Sie diesen zum Speichern von Daten verwenden können, müssen Sie sich bei dem virtuellen Computer anmelden und den Datenträger initialisieren.

7. Stellen Sie wie im vorherigen Abschnitt [Anmelden bei einem virtuellen Computer nach dessen Erstellung] beschrieben eine Verbindung zu dem virtuellen Computer her (#logon).

8. Öffnen Sie den **Server-Manager**, nachdem Sie sich auf dem virtuellen Computer angemeldet haben. Klicken Sie im linken Bereich auf **Datei- und Speicherdienste**.

	![Expand File and Storage Services in Server Manager](./media/virtual-machines-windows-tutorial/fileandstorageservices.png)

9. Wählen Sie im erweiterten Menü **Datenträger** aus.

	![Expand File and Storage Services in Server Manager](./media/virtual-machines-windows-tutorial/selectdisks.png)	
	
10.	Der Abschnitt **Datenträger** listet Datenträger 0, Datenträger 1 und Datenträger 2 auf. Datenträger 0 ist der Betriebssystem-Datenträger. Bei Datenträger 1 handelt es sich um einen temporären Ressourcendatenträger (der nicht für die Datenspeicherung verwendet werden sollte), und Datenträger 2 ist der Datenträger, den Sie an den virtuellen Computer angefügt haben. Der Datenträger hat eine Kapazität von 5 GB, je nachdem, was Sie beim Anfügen des Datenträgers angegeben haben. Klicken Sie mit der rechten Maustaste auf Datenträger 2, und  wählen Sie **Initialisieren** aus.

	![Start initialization](./media/virtual-machines-windows-tutorial/initializedisk.png)

11. Klicken Sie**Ja**.

	![Continue initialization](./media/virtual-machines-windows-tutorial/yesinitialize.png)

12. Klicken Sie erneut mit der rechten Maustaste auf Disk 2, und wählen Sie **Neues Volume** aus. 

	![Create the volume](./media/virtual-machines-windows-tutorial/initializediskvolume.png)

13. Schließen Sie den Assistenten unter Verwendung der angegebenen Standardwerte ab. Sobald der Assistent fertig ist, wird ein neues Volume im Bereich **Volumes** aufgelistet. Der Datenträger ist jetzt online und zum Speichern von Daten bereit. 

	![Create the volume](./media/virtual-machines-windows-tutorial/newvolumecreated.png)
	
## Nächste Schritte 

Weitere Informationen zur Konfiguration von virtuellen Windows-Computern auf Azure finden Sie in den folgenden Artikeln:

[Verbinden virtueller Computer mit einem virtuellen Netzwerk oder einem Cloud-Dienst](cloud-services-connect-virtual-machine.md)

[Erstellen und Hochladen einer Windows Server-VHD nach Azure](virtual-machines-create-upload-vhd-windows-server.md)

[Verwalten der Verfügbarkeit virtueller Computer](manage-availability-virtual-machines.md)

[Starten, Beenden und Löschen virtueller Computer](http://msdn.microsoft.com/library/azure/dn763935.aspx)

[VIDEO: Getting Started with VHDs - What's Really Happening](http://azure.microsoft.com/documentation/videos/getting-started-with-azure-virtual-machines) (Erste Schritte mit VHDs - was wirklich geschieht) (in englischer Sprache)

[VIDEO: FAQ mit Mark Russinovich - führt Microsoft Azure Windows aus?](http://azure.microsoft.com/documentation/videos/mark-russinovich-windows-on-azure)

[VIDEO: Adding a new virtual machine to a Web Farm by making reusable images](http://azure.microsoft.com/documentation/videos/adding-virtual-machines-web-farm) (Hinzufügen eines neuen virtuellen Computers zu einer Webfarm durch das Erstellen wiederverwendbarer Images) (in englischer Sprache)

[VIDEO: Adding Virtual Hard Drives, Storage Accounts, and Scaling Virtual Machines](http://azure.microsoft.com/documentation/videos/adding-drives-scaling-virtual-machines) (Hinzufügen von VHDs, Speicherkonten und skalierbaren virtuellen Computern) (in englischer Sprache)

[VIDEO: Scott Guthrie starts with Virtual Machines](http://azure.microsoft.com/documentation/videos/virtual-machines-scottgu) (Scott Guthrie beginnt mit der Verwendung von virtuellen Computern) (in englischer Sprache)

[VIDEO: Storage and Disk Basics with Azure Virtual Machines](http://azure.microsoft.com/documentation/videos/storage-and-disks-virtual-machines) (Speicher- und Datenträgergrundlagen bei virtuellen Azure-Computern) (in englischer Sprache)



[Informationen zu virtuellen Computern in Azure]: #virtualmachine
[Erstellen eines virtuellen Computers]: #custommachine
[Anmelden bei einem virtuellen Computer nach dessen Erstellung]: #logon
[Anschließen eines Datenträgers an den neuen virtuellen Computer]: #attachdisk
[Gewusst wie: Einrichten der Kommunikation mit dem virtuellen Computer]: #endpoints

<!--HONumber=47-->
