<properties title="How To Change the Drive Letter of the Windows Temporary Disk" pageTitle="How To Change the Drive Letter of the Windows Temporary Disk" description="Describes how to remap the temporary disk on a Windows VM in Azure" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav" />

# So ändern Sie den Laufwerksbuchstaben des temporären Windows-Laufwerks

Sie können den Laufwerksbuchstaben des temporären Laufwerks ändern, wenn Sie das Laufwerk D für einen anderen Zweck benötigen. Das ist wahrscheinlich dann der Fall, wenn Sie eine Anwendung oder einen Dienst unterstützen möchten, der das Laufwerk D als festen Speicherort verwendet.

Ehe Sie beginnen, sollten Sie für Folgendes sorgen:

-   Ein verbundener Datenträger, der zum Speichern der Windows page-Datei (pagefile.sys) während dieses Vorgangs verwendet werden kann. Eine Anleitung finden Sie unter [Anfügen eines Datenträgers an einen virtuellen Windows-Computer][Anfügen eines Datenträgers an einen virtuellen Windows-Computer].
-   Eine hochgeladene VHD im Speicherkonto, wenn Sie eine virtuelle Festplatte eines vorhandenen Datenträgers auf dem Laufwerk D verwenden möchten. Eine Anleitung finden Sie in Schritt 3 und 4 unter [Erstellen und Hochladen einer Windows Server VHD zu Azure][Erstellen und Hochladen einer Windows Server VHD zu Azure].

## Ändern des Laufwerkbuchstabens

1.  Melden Sie sich beim virtuellen Computer an.

2.  Verschieben Sie pagefile.sys vom Laufwerk D auf ein anderes Laufwerk.

3.  Starten Sie den virtuellen Computer neu.

4.  Melden Sie sich wieder an und ändern Sie den Laufwerksbuchstaben von D auf E.

5.  Verbinden Sie über das [Azure-Verwaltungsportal][Azure-Verwaltungsportal] einen vorhandenen oder leeren Datenträger.

6.  Melden Sie sich erneut am virtuellen Computer an, initialisieren Sie das Laufwerk und weisen Sie D als Laufwerksbuchstaben für das soeben angefügte Laufwerk zu.

7.  Stellen Sie sicher, dass E dem temporären Speicherlaufwerk zugeordnet ist.

8.  Verschieben Sie pagefile.sys vom anderen Laufwerk das Laufwerk E.

## Zusätzliche Ressourcen

[Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird][Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird]

[Trennen eines Datenträgers von einem virtuellen Computer][Trennen eines Datenträgers von einem virtuellen Computer]

[Was ist ein Speicherkonto?][Was ist ein Speicherkonto?]

<!--Link references-->

  [Anfügen eines Datenträgers an einen virtuellen Windows-Computer]: ../storage-windows-attach-disk
  [Erstellen und Hochladen einer Windows Server VHD zu Azure]: ../virtual-machines-create-upload-vhd-windows-server/
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com
  [Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird]: ../virtual-machines-log-on-windows-server/
  [Trennen eines Datenträgers von einem virtuellen Computer]: ../storage-windows-detach-disk/
