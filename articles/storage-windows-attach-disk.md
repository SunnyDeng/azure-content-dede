<properties linkid="manage-linux-howto-attach-a-disk" urlDisplayName="Attach a disk" pageTitle="Attach a disk to a virtual machine | Azure" metaKeywords="Azure Windows virtual machine, Azure attach disk, Azure initialize disk" description="Learn how to attach a data disk to an Azure virtual machine and initialize it so it's ready for use." metaCanonical="" services="virtual-machines,storage" documentationCenter="" title="How to Attach a Data Disk to a Virtual Machine" authors="kathydav" solutions="" manager="dongill" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav" />	

# Anfügen eines Datenträgers an einen virtuellen Windows-Computer

Sie können sowohl leere Datenträger als auch Datenträger mit Daten anfügen. In beiden Fällen sind die Datenträger eigentlich VHD-Dateien, die sich in einem Azure-Speicherkonto befinden. Außerdem müssen Sie in beiden Fällen den Datenträger nach dem Anfügen initialisieren, damit er verwendet werden kann.

> [WACOM.NOTE] Es empfiehlt sich, einen oder mehrere separate Datenträger zu verwenden, um die Daten eines virtuellen Computers zu speichern. Wenn Sie einen virtuellen Azure-Computer erstellen, hat dieser einen Datenträger für das Betriebssystem, der Laufwerk C zugeordnet ist, und einen temporären Datenträger, der Laufwerk D zugeordnet ist. **Verwenden Sie Laufwerk D nicht zum Speichern von Daten.** Wie der Name schon sagt, bietet er nur temporäre Speicherung. Er ermöglicht keine Redundanz oder Sicherung, da er sich nicht im Azure-Speicher befindet.

-   [Gewusst wie: Anfügen eines leeren Datenträgers][]
-   [Gewusst wie: Anfügen eines vorhandenen Datenträgers][]
-   [Gewusst wie: Initialisieren eines neues Datenträgers unter Windows Server][]

[WACOM.INCLUDE [howto-attach-disk-windows-linux][]]

## <span id="initializeinWS"></span></a>Gewusst wie: Initialisieren eines neues Datenträgers unter Windows Server

1.  Stellen Sie eine Verbindung mit dem virtuellen Computer her. Eine Beschreibung der dafür erforderlichen Schritte finden Sie unter [Anmeldung auf einem virtuellen Windows Server-Computer][].

2.  Öffnen Sie nach der Anmeldung im linken Bereich **Server-Manager**, erweitern Sie **Speicherung**, und klicken Sie anschließend auf **Datenträgerverwaltung**.

    ![Server-Manager öffnen][]

3.  Klicken Sie mit der rechten Maustaste auf **Datenträger 2**, anschließend auf **Datenträger initialisieren** und dann auf **OK**.

    ![Datenträger initialisieren][]

4.  Klicken Sie mit der rechten Maustaste auf den Speicherplatzzuordnungsbereich für Datenträger 2, klicken Sie auf **Neues einfaches Volume**, und beenden Sie anschließend den Assistenten mit den Standardwerten.

    ![Volume initialisieren][]

[logon]: ../virtual-machines-log-on-windows-server/


<!-- below text in english, which needs to be localize -->

	The disk is now online and ready to use with a new drive letter.



    ![Volume successfully initialized](./media/storage-windows-attach-disk/InitializeSuccess.png)

  [Gewusst wie: Anfügen eines leeren Datenträgers]: #attachempty
  [Gewusst wie: Anfügen eines vorhandenen Datenträgers]: #attachexisting
  [Gewusst wie: Initialisieren eines neues Datenträgers unter Windows Server]: #initializeinWS
  [howto-attach-disk-windows-linux]: ../includes/howto-attach-disk-windows-linux.md
  [Anmeldung auf einem virtuellen Windows Server-Computer]: ../virtual-machines-log-on-windows-server/
  [Server-Manager öffnen]: ./media/storage-windows-attach-disk/ServerManager.png
  [Datenträger initialisieren]: ./media/storage-windows-attach-disk/InitializeDisk.png
  [Volume initialisieren]: ./media/storage-windows-attach-disk/InitializeDiskVolume.png
