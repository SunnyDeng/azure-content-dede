<properties urlDisplayName="Create a virtual machine" pageTitle="Erstellen eines virtuellen Computers unter Linux in Azure" metaKeywords="Azure Linux vm, Linux vm" description="Erfahren Sie, wie Sie mithilfe eines Images von Azure einen virtuellen Azure-Computer unter Linux erstellen. " metaCanonical="" services="virtual-machines" documentationCenter="" title="" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="10/9/2014" ms.author="kathydav" />

# Erstellen eines virtuellen Linux-Computers

Das Erstellen eines virtuellen Computers, auf dem Linux ausgeführt wird, ist einfach, wenn Sie die Image-Galerie im Azure Verwaltungsportal verwenden. Dieser Leitfaden zeigt Ihnen, wie Sie dazu vorgehen müssen. Es wird darin davon ausgegangen, dass Sie über keine Erfahrung in Bezug auf die Verwendung von Azure verfügen.

> [WACOM.NOTE] Sie benötigen zwar keine Erfahrungen mit virtuellen Azure Computern, um dieses Lernprogramm auszuführen, aber ein Azure-Konto. Sie können ein kostenloses Textkonto in wenigen Minuten erstellen. Ausführliche Informationen finden Sie unter [Create a Windows Azure account (in englischer Sprache)][Create a Windows Azure account (in englischer Sprache)].

In diesem Lernprogramm wird Folgendes behandelt:

-   [Informationen zu virtuellen Computern in Azure][Informationen zu virtuellen Computern in Azure]
-   [Erstellen eines virtuellen Computers][Erstellen eines virtuellen Computers]
-   [Anmelden bei einem virtuellen Computer nach dessen Erstellung][Anmelden bei einem virtuellen Computer nach dessen Erstellung]
-   [Anschließen eines Datenträgers an den neuen virtuellen Computer][Anschließen eines Datenträgers an den neuen virtuellen Computer]

**Wichtig**: In diesem Tutorial wird ein virtueller Computer erstellt, der nicht mit einem virtuellen Netzwerk verbunden ist. Wenn Ihr virtueller Computer ein virtuelles Netzwerk verwenden soll, müssen Sie das virtuelle Netzwerk beim Erstellen des virtuellen Computers angeben. Weitere Informationen über virtuelle Netzwerke erhalten Sie unter [Überblick über virtuelle Azure-Netzwerke][Überblick über virtuelle Azure-Netzwerke].

## <span id="virtualmachine"></span> </a>Informationen zu virtuellen Computern in Azure

Ein virtueller Computer in Azure ist ein Server in der Cloud, den Sie steuern und verwalten können. Nachdem Sie einen virtuellen Computer in Azure erstellt haben, können Sie ihn jederzeit löschen und neu erstellen, und Sie können auf den virtuellen Computer wie auf einen Server in Ihrem Büro zugreifen. Zum Erstellen eines virtuellen Computers werden Dateien für virtuelle Festplatten (Virtual Hard Disk File, VHD) verwendet. Die folgenden Typen von VHDs werden verwendet:

-   **Abbild** – Eine VHD, die als Vorlage zum Erstellen eines neuen virtuellen Computers verwendet wird. Ein Abbild ist eine Vorlage, da es nicht über spezifische Einstellungen verfügt wie ein ausgeführter virtueller Computer. Beispiele für diese Einstellungen sind Computername und Benutzerkontoeinstellungen. Wenn Sie einen virtuellen Computer mithilfe eines Abbilds erstellen, wird automatisch eine Betriebssystemfestplatte für den neuen virtuellen Computer erstellt.
-   **Festplatte** – Eine Festplatte ist eine VHD, die Sie als ausgeführte Version eines Betriebssystems starten und einbinden können. Sobald ein Abbild bereitgestellt wurde, wird es zu einer Festplatte. Es wird immer eine Festplatte erstellt, wenn Sie ein Abbild zum Erstellen eines virtuellen Computers verwenden. Jede VHD, die an virtualisierte Hardware angeschlossen ist und die im Rahmen eines Service ausgeführt wird, ist eine Festplatte.

Folgende Optionen sind verfügbar, um virtuelle Computer mithilfe von Abbildern zu erstellen:

-   Erstellen Sie einen virtuellen Computer unter Verwendung eines Abbilds, das in der Bildergalerie des Azure Verwaltungsportals bereitgestellt wurde.
-   Erstellen Sie eine VHD-Datei, die ein Abbild zu Azure enthält, und laden Sie diese hoch. Erstellen Sie dann einen virtuellen Computer mithilfe des Abbilds. Weitere Informationen zum Erstellen und Hochladen eines benutzerdefinierten Abbilds finden Sie unter [Erstellen und Hochladen einer virtuellen Festplatte mit Linux-Betriebssystem][Erstellen und Hochladen einer virtuellen Festplatte mit Linux-Betriebssystem].

Jeder virtuelle Computer befindet sich in einem Clouddienst, entweder alleine oder zusammen mit anderen virtuellen Computern. Sie können virtuelle Computer im selben Clouddienst platzieren, damit die virtuellen Computer miteinander kommunizieren können, und um einen Lastenausgleich zwischen den virtuellen Computern zu ermöglichen sowie hohe Verfügbarkeit der Computer sicherzustellen. Weitere Informationen zu Clouddiensten und virtuellen Computern finden Sie im Abschnitt über Ausführungsmodelle unter [Einführung in Azure][Einführung in Azure].

## <span id="custommachine"></span> </a>Erstellen eines virtuellen Computers

In diesem Lernprogramm wird die Methode **From Gallery** zum Erstellen eines virtuellen Computers verwendet, da Sie dadurch mehr Optionen haben als bei der Methode **Quick Create**. Sie können bei Bedarf verbundene Ressourcen, den DNS-Namen und die Netzwerkkonnektivität auswählen.

[WACOM.INCLUDE [virtual-machines-create-LinuxVM](../includes/virtual-machines-create-LinuxVM.md)]

[WACOM.INCLUDE [virtual-machines-Linux-tutorial-log-on-attach-disk](../includes/virtual-machines-Linux-tutorial-log-on-attach-disk.md)]

## Nächste Schritte

Weitere Informationen zu Linux auf Azure erhalten Sie in den folgenden Artikeln:

-   [Einführung in Linux auf Azure][Einführung in Linux auf Azure]

-   [Verwenden des Azure-Befehlszeilentools für Mac und Linux][Verwenden des Azure-Befehlszeilentools für Mac und Linux]

-   [Starten, Beenden und Löschen virtueller Computer][Starten, Beenden und Löschen virtueller Computer]

  [Create a Windows Azure account (in englischer Sprache)]: http://www.windowsazure.com/de-de/develop/php/tutorials/create-a-windows-azure-account/
  [Informationen zu virtuellen Computern in Azure]: #virtualmachine
  [Erstellen eines virtuellen Computers]: #custommachine
  [Anmelden bei einem virtuellen Computer nach dessen Erstellung]: #logon
  [Anschließen eines Datenträgers an den neuen virtuellen Computer]: #attachdisk
  [Überblick über virtuelle Azure-Netzwerke]: http://go.microsoft.com/fwlink/p/?LinkID=294063
  [Erstellen und Hochladen einer virtuellen Festplatte mit Linux-Betriebssystem]: /de-de/manage/linux/common-tasks/upload-a-vhd/
  [Einführung in Azure]: http://go.microsoft.com/fwlink/p/?LinkId=311926
  [Einführung in Linux auf Azure]: http://www.windowsazure.com/de-de/documentation/articles/introduction-linux/
  [Verwenden des Azure-Befehlszeilentools für Mac und Linux]: http://www.windowsazure.com/de-de/documentation/articles/xplat-cli/
  [Starten, Beenden und Löschen virtueller Computer]: http://msdn.microsoft.com/library/azure/dn763935.aspx
