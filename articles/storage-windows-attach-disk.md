<properties 
	pageTitle="Anfügen eines Datenträgers an einen virtuellen Computer | Azure" 
	description="Erfahren Sie, wie Sie einen Datenträger an einen virtuellen Azure-Computer anfügen und initialisieren können, damit er betriebsbereit ist." 
	services="virtual-machines, storage" 
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
	ms.date="01/20/2015" 
	ms.author="kathydav"/>

#Anfügen eines Datenträgers an einen virtuellen Windows-Computer

Sie können sowohl leere Datenträger als auch Datenträger mit Daten anfügen. In beiden Fällen sind die Datenträger eigentlich VHD-Dateien, die sich in einem Azure-Speicherkonto befinden. Außerdem müssen Sie in beiden Fällen den Datenträger nach dem Anfügen initialisieren, damit er verwendet werden kann. 

> [AZURE.NOTE] Es empfiehlt sich, einen oder mehrere separate Datenträger zu verwenden, um die Daten eines virtuellen Computers zu speichern. Wenn Sie einen virtuellen Azure-Computer erstellen, hat dieser einen Datenträger für das Betriebssystem, der Laufwerk C zugeordnet ist, und einen temporären Datenträger, der Laufwerk D zugeordnet ist. **Verwenden Sie Laufwerk D nicht zum Speichern von Daten.** Wie der Name schon sagt, bietet dieser Datenträger nur temporäre Speicherung. Er ermöglicht keine Redundanz oder Sicherung, da er sich nicht im Azure-Speicher befindet.

- [Vorgehensweise: Anfügen eines leeren Datenträgers](#attachempty)
- [Vorgehensweise: Anfügen eines vorhandenen Datenträgers](#attachexisting)
- [Vorgehensweise: Initialisieren eines neues Datenträgers unter Windows Server](#initializeinWS)


[AZURE.INCLUDE [howto-attach-disk-windows-linux](../includes/howto-attach-disk-windows-linux.md)]

##<a id="initializeinWS"></a>Vorgehensweise: Initialisieren eines neues Datenträgers unter Windows Server

1. Stellen Sie eine Verbindung mit dem virtuellen Computer her. Ausführliche Informationen finden Sie unter [Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird][logon].

2. Öffnen Sie nach der Anmeldung im linken Bereich **Server-Manager**, erweitern Sie **Speicherung**, und klicken Sie anschließend auf **Datenträgerverwaltung**.



	![Open Server Manager](./media/storage-windows-attach-disk/ServerManager.png)



3. Klicken Sie mit der rechten Maustaste auf **Datenträger 2**, anschließend auf **Datenträger initialisieren** und dann auf **OK**.



	![Initialize the disk](./media/storage-windows-attach-disk/InitializeDisk.png)


4. Klicken Sie mit der rechten Maustaste auf den Speicherplatzzuordnungsbereich für Datenträger 2, klicken Sie auf **Neues einfaches Volume**, und beenden Sie anschließend den Assistenten mit den Standardwerten.
 

	![Initialize the volume](./media/storage-windows-attach-disk/InitializeDiskVolume.png)


[logon]: ../virtual-machines-log-on-windows-server/



	Der Datenträger ist nun online und betriebsbereit mit einem neuen Laufwerksbuchstaben.



	![Volume successfully initialized](./media/storage-windows-attach-disk/InitializeSuccess.png)

> [AZURE.NOTE] Die Anzahl der Datenträger, die Sie an einen virtuellen Computer anfügen können, hängt von der Größe des virtuellen Computers ab. So können Sie beispielsweise nur 4 Datenträger beim Standard A2 anfügen, aber 32 Datenträger beim Standard D14 und 64 Datenträger beim Standard G5. Nähere Informationen zur Anzahl der Datenträger, die pro virtuellem Computer angefügt werden können, finden Sie [hier](https://msdn.microsoft.com/de-de/library/azure/dn197896.aspx).


<!--HONumber=42-->
