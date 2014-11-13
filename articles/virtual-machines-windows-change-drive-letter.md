<properties title="&Auml;ndern des Datentr&auml;gerbuchstabens des tempor&auml;ren Windows-Datentr&auml;gers" pageTitle="&Auml;ndern des Datentr&auml;gerbuchstabens des tempor&auml;ren Windows-Datentr&auml;gers" description="Beschreibt die erneute Zuordnung des tempor&auml;ren Datentr&auml;gers auf einem virtuellen Computer unter Windows in Azure." metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav"  manager="timlt" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="07/09/2014" ms.author="kathydav" />

# Ändern des Datenträgerbuchstabens des temporären Windows-Datenträgers

Sie können den Datenträgerbuchstaben des temporären Datenträgers ändern, wenn Sie den Datenträger D für einen anderen Zweck verwenden müssen. Am wahrscheinlichsten würden Sie dies umsetzen, um eine Anwendung oder einen Dienst zu unterstützen, die bzw. der den Datenträger D als einen dauerhaften Speicherort verwendet.

Stellen Sie zunächst sicher, dass Sie über Folgendes verfügen:

-   Ein angefügter Datenträger, den Sie zum Speichern der Windows-Auslagerungsdatei („pagefile.sys“) während dieser Prozedur verwenden können. Anweisungen dazu finden Sie unter [Gewusst wie: Anfügen eines Datenträgers an einen virtuellen Windows-Computer][Gewusst wie: Anfügen eines Datenträgers an einen virtuellen Windows-Computer].
-   Eine virtuelle Festplatte im Speicherkonto, wenn Sie eine virtuelle Festplatte des vorhandenen Datenträgers auf dem Datenträger D verwenden möchten. Anweisungen finden Sie in den Schritten 3 und 4 in [Erstellen und Hochladen einer virtuellen Festplatte, die das Windows Server-Betriebssystem enthält][Erstellen und Hochladen einer virtuellen Festplatte, die das Windows Server-Betriebssystem enthält].

## Ändern des Datenträgerbuchstabens

1.  Melden Sie sich am virtuellen Computer an.

2.  Verschieben Sie „pagefile.sys“ vom Datenträger D zu einem anderen Datenträger.

3.  Starten Sie den virtuellen Computer neu.

4.  Melden Sie sich erneut an, und ändern Sie den Datenträgerbuchstaben von D zu E.

5.  Fügen Sie im [Azure-Verwaltungsportal][Azure-Verwaltungsportal] einen vorhandenen Datenträger oder einen leeren Datenträger an.

6.  Melden Sie sich erneut am virtuellen Computer an, initialisieren Sie den Datenträger, und weisen Sie D als Datenträgerbuchstaben für den von Ihnen angefügten Datenträger zu.

7.  Stellen Sie sicher, dass E dem temporären Speicherdatenträger zugeordnet ist.

8.  Verschieben Sie „pagefile.sys“ von dem anderen Datenträger zum Datenträger E.

## Zusätzliche Ressourcen

[Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird][Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird]

[Trennen eines Datenträgers von einem virtuellen Computer][Trennen eines Datenträgers von einem virtuellen Computer]

[Was ist ein Speicherkonto?][Was ist ein Speicherkonto?]

<!--Link references-->

  [Gewusst wie: Anfügen eines Datenträgers an einen virtuellen Windows-Computer]: ../storage-windows-attach-disk
  [Erstellen und Hochladen einer virtuellen Festplatte, die das Windows Server-Betriebssystem enthält]: ../virtual-machines-create-upload-vhd-windows-server/
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com
  [Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird]: ../virtual-machines-log-on-windows-server/
  [Trennen eines Datenträgers von einem virtuellen Computer]: ../storage-windows-detach-disk/
  [Was ist ein Speicherkonto?]: ../storage-whatis-account/
