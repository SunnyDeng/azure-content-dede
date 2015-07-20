
Weitere Informationen zu Datenträgern finden Sie unter [Informationen zu virtuellen Computerdatenträgern in Azure](http://go.microsoft.com/fwlink/p/?LinkId=403697).

##<a id="cliattachempty"></a>Vorgehensweise: Anfügen eines leeren Datenträgers
Das Anfügen eines leeren Datenträgers ist die einfachere Methode zum Hinzufügen eines Datenträgers. Führen Sie den folgenden Befehl aus, um einen neuen, leeren Datenträger anzufügen:

    vm disk attach-new <vm-name> <size-in-gb> [blob-url]

Ersetzen Sie `vm-name` durch den Namen Ihres virtuellen Computers, und `size-in-gb` durch die Größe des neuen Datenträgers. Sie können optional als letzten Parameter eine Blob-URL anfügen, um den Zielblob explizit anzugeben. Wenn Sie keine Blob-URL angeben, wird ein Blob-Objekt automatisch generiert.

Führen Sie den folgenden Befehl aus, um sicherzustellen, dass der Datenträger erstellt wurde:

    vm disk list <vm-name>

Nachfolgend sehen Sie eine exemplarische Vorgehensweise zur Ausführung der oben genannten Befehle einschließlich Terminalausgabe:

    ~$ azure vm disk attach-new pinkylinux 20 http://pinkylinux.blob.core.windows.net/vhds/pinkydisk1.vhd
    info:   Executing command vm disk attach-new
    + Getting virtual machines
    + Adding Data-Disk
    info:   vm disk attach-new command OK
    ~$ azure vm disk list pinkylinux
    info:    Executing command vm disk list
    + Fetching disk images
    + Getting virtual machines
    + Getting VM disks
    data:    Lun  Size(GB)  Blob-Name                               OS
    data:    ---  --------  --------------------------------------  -----
    data:         30        pinkylinux-pinkylinux-2015-02-05.vhd    Linux
    data:    0    5         pinkydisk1.vhd
    data:    1    20        pinkylinux-f8ef0006ab182209.vhd
    info:    vm disk list command OK

<!---HONumber=July15_HO2-->