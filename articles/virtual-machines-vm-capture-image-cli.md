<properties
	pageTitle="Erfassen eines Images für einen virtuellen Linux-Computer mithilfe der CLI"
	description="Erfahren Sie, wie Sie ein Image eines virtuellen Azure-Computers unter Linux erfassen können."
	services="virtual-machines"
	documentationCenter=""
	authors="karthmut"
	manager="madhana"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/20/2015"
	ms.author="karthmut"/>




# Erfassen eines virtuellen Linux-Computers mit der CLI, um ihn als Vorlage zu verwenden##



Dieser Artikel erläutert, wie Sie einen virtuellen Azure-Computer erfassen, auf dem Linux läuft, um ihn wie eine Vorlage zum Erstellen anderer virtueller Computer zu verwenden. Diese Vorlage umfasst den Betriebssystemdatenträger und alle an den virtuellen Computer angefügten Datenträger. Da die Vorlage keine Netzwerkkonfiguration enthält, müssen Sie die Konfiguration später vornehmen, wenn Sie die anderen virtuellen Computer erstellen, die auf dieser Vorlage basieren.



Azure behandelt diese Vorlage als Image und speichert es in der Liste Ihrer Images. Hier werden sämtliche Images abgelegt, die Sie hochladen. Weitere Informationen zu Images finden Sie unter [Informationen zu Images virtueller Computer in Azure][].



##Voraussetzungen##



Diese Schritte setzen voraus, dass Sie bereits einen virtuellen Azure-Computer erstellt, das Betriebssystem konfiguriert und beliebige Datenträger angefügt haben. Falls dies noch nicht geschehen ist, finden Sie hier Anweisungen:



- [Erstellen eines benutzerdefinierten virtuellen Computers] []

- [Gewusst wie: Anfügen eines Datenträgers an einen virtuellen Computer] []



##Erfassen des virtuellen Computers##



1. Stellen Sie eine Verbindung mit dem virtuellen Computer her. Ausführliche Informationen finden Sie unter [Anmelden bei einem virtuellen Computer, auf dem Linux ausgeführt wird][].



2. Die VM kann nur erfasst werden, wenn sie den Status **StoppedDeallocated** aufweist. Geben Sie im SSH-Fenster den folgenden Befehl ein, um die VM herunterzufahren:



        vm shutdown [options] <vm-name>



    Beachten Sie, dass für `vm shutdown` u. a. die Option `-p` verfügbar ist, mit der die Rechenressourcen beim Herunterfahren beibehalten werden. Aktivieren Sie diese Option **nicht**, da die Bereitstellung der VM aufgehoben werden muss, um sie zu erfassen.



3. Führen Sie den folgenden Befehl aus, um Ihr VM-Image zu erfassen:



        vm capture <vm-name> <target-image-name> --deleted



    Die VM muss gelöscht werden, um das Image zu erfassen. Sie können hierzu entweder "--deleted" oder "-t" verwenden.



4. Bestätigen Sie, dass Ihr Image erfasst wurde, indem Sie Ihre VM-Imageliste überprüfen:



        vm image list | grep <target-image-name>



    Der Abschnitt `| grep <target-image-name>` ist optional, aber unterstützt Sie beim leichteren Auffinden Ihres Images in der Liste.



Nachfolgend sehen Sie eine exemplarische Vorgehensweise zum Erfassen eines virtuellen Computers einschließlich Terminalausgabe:


    ~$ azure vm list

    info:    Executing command vm list

    + Getting virtual machines

    data:    Name         Status            Location  DNS Name                  IP Address

    data:    -----------  ----------------  --------  ------------------------  ------------

    data:    kmorig       RoleStateUnknown  West US   kmorig.cloudapp.net       100.92.56.16

    info:    vm list command OK

    ~$ azure vm shutdown kmorig

    info:    Executing command vm shutdown

    + Getting virtual machines

    + Shutting down VM

    info:    vm shutdown command OK

    ~$ azure vm list

    info:    Executing command vm list

    + Getting virtual machines

    data:    Name         Status              Location  DNS Name                  IP Address

    data:    -----------  ------------------  --------  ------------------------  ----------

    data:    kmorig       StoppedDeallocated  West US   kmorig.cloudapp.net

    info:    vm list command OK

    ~$ azure vm capture kmorig kmcaptured --deleted

    info:    Executing command vm capture

    + Getting virtual machines

    + Checking image with name kmcaptured exists

    + Capturing VM

    info:    vm capture command OK

    ~$ azure vm image list | grep kmcaptured

    data:    kmcaptured



Weitere Informationen und zusätzliche Befehlen finden Sie in der [Azure-CLI-Dokumentation][].


[Azure-CLI-Dokumentation]: ../virtual-machines-command-line-tools/

[Anmelden bei einem virtuellen Computer unter Linux]: ../virtual-machines-linux-how-to-log-on/

[Informationen zu Images von virtuellen Computern in Azure]: http://msdn.microsoft.com/library/azure/dn790290.aspx

[Erstellen eines benutzerdefinierten virtuellen Computers]: ../virtual-machines-create-custom/

[Gewusst wie: Anfügen eines Datenträgers an einen virtuellen Computer]: ../storage-windows-attach-disk/

<!--HONumber=47-->
