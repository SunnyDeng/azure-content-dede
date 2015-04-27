<properties 
	pageTitle="Erstellen eines virtuellen Computers unter Linux in Azure" 
	description="Erfahren Sie, wie Sie mithilfe eines Images von Azure einen virtuellen Azure-Computer unter Linux erstellen." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="1/19/2015" 
	ms.author="kathydav"/>

#Erstellen eines virtuellen Linux-Computers 

Das Erstellen eines virtuellen Computers, auf dem Linux ausgeführt wird, ist einfach, wenn Sie den Image-Katalog im Azure-Verwaltungsportal verwenden. Dieser Leitfaden zeigt Ihnen, wie Sie dazu vorgehen müssen. Es wird darin davon ausgegangen, dass Sie über keine Erfahrung in Bezug auf die Verwendung von Azure verfügen.

> [AZURE.NOTE] Sie benötigen zwar keine Erfahrungen mit virtuellen Azure-Computern, um dieses Lernprogramm auszuführen, aber ein Azure-Konto. Sie können ein kostenloses Testkonto in wenigen Minuten erstellen. Ausführliche Informationen finden Sie unter [Erstellen eines Azure-Kontos](http://azure.microsoft.com/develop/php/tutorials/create-a-windows-azure-account/). 

In diesem Lernprogramm wird Folgendes behandelt:

- [Informationen zu virtuellen Computern in Azure] []
- [Gewusst wie: Erstellen des virtuellen Computers] []
- [Gewusst wie: Anmelden bei einem virtuellen Computer nach dessen Erstellung] []
- [Gewusst wie: Anfügen eines Datenträgers an den neuen virtuellen Computer] []

**Wichtig**: In diesem Lernprogramm wird ein virtueller Computer erstellt, der nicht mit einem virtuellen Netzwerk verbunden ist. Wenn Ihr virtueller Computer ein virtuelles Netzwerk verwenden soll, müssen Sie das virtuelle Netzwerk beim Erstellen des virtuellen Computers angeben. Weitere Informationen über virtuelle Netzwerke erhalten Sie unter [Überblick über virtuelle Azure-Netzwerke](http://go.microsoft.com/fwlink/p/?LinkID=294063)

## <a id="virtualmachine"> </a>Informationen zu virtuellen Computern in Azure ##

Ein virtueller Computer in Azure ist ein Server in der Cloud, den Sie steuern und verwalten können. Nachdem Sie einen virtuellen Computer in Azure erstellt haben, können Sie ihn jederzeit löschen und neu erstellen, und Sie können auf den virtuellen Computer wie auf einen Server in Ihrem Büro zugreifen. Zum Erstellen eines virtuellen Computers werden Dateien für virtuelle Festplatten (Virtual Hard Disk File, VHD) verwendet. Die folgenden Typen von VHDs werden verwendet:

- **Image** - Eine VHD, die als Vorlage zum Erstellen eines neuen virtuellen Computers verwendet wird. Ein Image ist eine Vorlage, da es nicht über spezifische Einstellungen verfügt wie ein ausgeführter virtueller Computer. Beispiele für diese Einstellungen sind Computername und Benutzerkontoeinstellungen. Wenn Sie einen virtuellen Computer mithilfe eines Image erstellen, wird automatisch eine Betriebssystemfestplatte für den neuen virtuellen Computer erstellt.
- **Festplatte** - Eine Festplatte ist eine VHD, die Sie als ausgeführte Version eines Betriebssystems starten und einbinden können. Sobald ein Image bereitgestellt wurde, wird es zu einer Festplatte. Es wird immer eine Festplatte erstellt, wenn Sie ein Image zum Erstellen eines virtuellen Computers verwenden. Jede VHD, die an virtualisierte Hardware angeschlossen ist und die im Rahmen eines Service ausgeführt wird, ist eine Festplatte.

Folgende Optionen sind verfügbar, um virtuelle Computer mithilfe von Images zu erstellen:

- Erstellen Sie einen virtuellen Computer unter Verwendung eines Image, das im Image-Katalog des Azure-Verwaltungsportals bereitgestellt wurde.
- Erstellen Sie eine VHD-Datei, die ein Image zu Azure enthält, und laden Sie diese hoch. Erstellen Sie dann einen virtuellen Computer mithilfe des Image. Weitere Informationen zum Erstellen und Hochladen eines benutzerdefinierten Image finden Sie unter [Erstellen und Hochladen einer virtuellen Festplatte, die das Linux-Betriebssystem enthält](/de-de/manage/linux/common-tasks/upload-a-vhd/).

Jeder virtuelle Computer befindet sich in einem Clouddienst, entweder alleine oder zusammen mit anderen virtuellen Computern. Sie können virtuelle Computer im selben Clouddienst platzieren, damit die virtuellen Computer miteinander kommunizieren können, und um einen Lastenausgleich zwischen den virtuellen Computern zu ermöglichen sowie hohe Verfügbarkeit der Computer sicherzustellen. Weitere Informationen zu Clouddiensten und virtuellen Computern finden Sie im Abschnitt über "Ausführungsmodelle" unter [Einführung in Azure](http://go.microsoft.com/fwlink/p/?LinkId=311926).

## <a id="custommachine"> </a>Gewusste wie: Erstellen eines virtuellen Computers ##

In diesem Lernprogramm wird die Methode **Aus Katalog** zum Erstellen eines virtuellen Computers verwendet, da Sie dadurch mehr Optionen haben als bei der Methode **Schnellerfassung**. Sie können bei Bedarf verbundene Ressourcen, den DNS-Namen und die Netzwerkkonnektivität auswählen.

[AZURE.INCLUDE [virtual-machines-create-LinuxVM](../includes/virtual-machines-create-LinuxVM.md)]

[AZURE.INCLUDE [virtual-machines-Linux-tutorial-log-on-attach-disk](../includes/virtual-machines-Linux-tutorial-log-on-attach-disk.md)]

> [AZURE.NOTE] Sie können die Verbindung zum virtuellen Linux-Computer auch mithilfe eines SSH-Schlüssels für die Identifikation herstellen. Weitere Informationen zum Konfigurieren und Verbinden mit einem SSH-Schlüssel finden Sie unter [Gewusst wie: Verwenden von SSH mit Linux auf Azure](virtual-machines-linux-use-ssh-key).

##Nächste Schritte 

Weitere Informationen zu Linux auf Azure erhalten Sie in den folgenden Artikeln:

- [Einführung in Linux auf Azure](http://azure.microsoft.com/documentation/articles/introduction-linux/)

- [Gewusst wie: Verwenden des Azure-Befehlszeilentools für Mac und Linux](http://azure.microsoft.com/documentation/articles/xplat-cli/)

- [Informationen zu den Azure-VM-Konfigurationseinstellungen](http://msdn.microsoft.com/library/azure/dn763935.aspx)

- [Die Docker-Erweiterung für virtuelle Linux-Computer auf Azure](virtual-machines-docker-vm-extension.md)


[Nächste Schritte]: #next
[Informationen zu virtuellen Computern in Azure]: #virtualmachine
[Gewusst wie: Erstellen eines virtuellen Computers]: #custommachine
[Gewusst wie: Anmelden bei einem virtuellen Computer nach dessen Erstellung]: #logon
[Gewusst wie: Einfügen eines Datenträgers an den neuen virtuellen Computer]: #attachdisk

<!--HONumber=45--> 
