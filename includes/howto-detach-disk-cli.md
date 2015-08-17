
#Trennen eines Datenträgers von einem virtuellen Computer mithilfe der CLI

Wenn Sie einen Datenträger, der an einen virtuellen Computer angefügt ist, nicht mehr benötigen, können Sie ihn leicht trennen. Dadurch wird der Datenträger von dem virtuellen Computer entfernt, aber nicht aus dem Speicher. Wenn Sie die vorhandenen Daten erneut auf dem Datenträger verwenden möchten, können Sie ihn erneut an denselben virtuellen Computer oder an einen anderen anfügen.

> [AZURE.NOTE]Ein virtueller Computer in Azure verwendet verschiedene Arten von Datenträgern wie Betriebssystemfestplatten, lokale temporäre Festplatten und optionale Datenträger. Datenträger sind die empfohlene Möglichkeit zum Speichern von Daten für einen virtuellen Computer. Unter [Datenträger und Images verwalten](http://go.microsoft.com/fwlink/p/?LinkId=263439) finden Sie Details in Bezug auf Datenträger. Es ist derzeit nicht möglich, eine Betriebssystemfestplatte zu trennen.


1. Rufen Sie die Liste der Datenträger ab, die an Ihren virtuellen Computer angefügt sind:

        vm disk list <vm-name>

    Wenn Sie `<vm-name>` weglassen, erhalten Sie eine Liste aller Datenträger in Ihrem Abonnement.


2. Trennen eines Datenträgers:

        vm disk detach <vm-name> <lun>

    `lun` identifiziert den zu trennenden Datenträger und wird in Form einer Nummer angegeben, die Sie in der Datenträgerliste für Ihren virtuellen Computer finden.

Exemplarische Vorgehensweise einschließlich Terminalausgabe:

    ~$ azure vm disk list kmlinux
    info:    Executing command vm disk list
    + Fetching disk images
    + Getting virtual machines
    + Getting VM disks
    data:    Lun  Size(GB)  Blob-Name                               OS
    data:    ---  --------  --------------------------------------  -----
    data:         30        kmlinux-kmlinux-2015-02-05.vhd          Linux
    data:    1    5         kmlinux-f8ef0006ab182209.vhd
    data:    2    7         kmlinux-602362868dbb7439.vhd
    info:    vm disk list command OK
    ~$ azure vm disk detach kmlinux 2
    info:    Executing command vm disk detach
    + Getting virtual machines
    + Removing Data-Disk
    info:    vm disk detach command OK
    ~$ azure vm disk list kmlinux
    info:    Executing command vm disk list
    + Fetching disk images
    + Getting virtual machines
    + Getting VM disks
    data:    Lun  Size(GB)  Blob-Name                               OS
    data:    ---  --------  --------------------------------------  -----
    data:         30        kmlinux-kmlinux-2015-02-05.vhd          Linux
    data:    1    5         kmlinux-f8ef0006ab182209.vhd
    info:    vm disk list command OK

<!---HONumber=August15_HO6-->