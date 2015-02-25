<properties pageTitle="Ändern des Datenträgerbuchstabens des temporären Windows-Datenträgers" description="Beschreibt die erneute Zuordnung des temporären Datenträgers auf einem virtuellen Computer unter Windows in Azure." services="virtual-machines" documentationCenter="" authors="KBDAzure" manager="timlt" editor=""/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/15/2015" ms.author="kathydav"/>

#Ändern des Datenträgerbuchstabens des temporären Windows-Datenträgers

Wenn Sie Laufwerk D zum Speichern von Daten verwenden müssen, befolgen Sie diese Anweisungen, um ein anderes Laufwerk für den temporären Datenträger zu verwenden. Verwenden Sie niemals das temporäre Laufwerk zum Speichern von Daten, die Sie behalten müssen.

Bevor Sie beginnen, benötigen Sie einen Datenträger, der an den virtuellen Computer angefügt ist, damit Sie die Windows-Auslagerungsdatei (pagefile.sys) während dieses Vorgangs speichern können. Weitere Informationen finden Sie bei Bedarf unter [Anfügen eines Datenträgers an einen virtuellen Windows-Computer]. Eine Anleitung dazu, wie Sie ermitteln können, welche Datenträger angefügt sind, finden Sie im Artikel [Informationen zu Datenträgern virtueller Computer in Azure] unter "Verwalten der Datenträger".

Wenn Sie einen vorhandenen Datenträger auf Laufwerk D verwenden möchten, stellen Sie sicher, dass Sie auch die virtuelle Festplatte in das Speicherkonto hochgeladen haben. Anweisungen finden Sie in den Schritten 3 und 4 in [Erstellen und Hochladen einer Windows Server-VHD nach Azure].

> [AZURE.WARNING] Wenn Sie die Größe eines virtuellen Computers ändern und der virtuelle Computer hierdurch auf einen anderen Host verschoben wird, wird das temporäre Laufwerk wieder Laufwerk D geändert.

##Ändern des Datenträgerbuchstabens

1. Melden Sie sich beim virtuellen Computer an. 

2. Verschieben Sie "pagefile.sys" vom Datenträger D zu einem anderen Datenträger.

3. Starten Sie den virtuellen Computer neu.

4. 	Melden Sie sich erneut an, und ändern Sie den Datenträgerbuchstaben von D zu E.

5.	Fügen Sie im [Azure-Verwaltungsportal](http://manage.windowsazure.com) einen vorhandenen Datenträger oder einen leeren Datenträger an.

6.	Melden Sie sich erneut am virtuellen Computer an, initialisieren Sie den Datenträger, und weisen Sie D als Datenträgerbuchstaben für den von Ihnen angefügten Datenträger zu.

7.	Stellen Sie sicher, dass E dem temporären Speicherdatenträger zugeordnet ist.

8.	Verschieben Sie "pagefile.sys" von dem anderen Datenträger zum Datenträger E.

##Zusätzliche Ressourcen
[Anmelden bei einem virtuellen Computer unter Windows Server]

[Trennen eines Datenträgers von einem virtuellen Computer]

[Über Azure-Speicherkonten]

<!--Link references-->
[Anfügen eines Datenträgers an einen virtuellen Windows-Computer]: ../storage-windows-attach-disk
[Informationen zu Datenträgern von virtuellen Computern in Azure]: ../http://msdn.microsoft.com/de-de/library/azure/dn790303.aspx
[Erstellen und Hochladen einer Windows Server-VHD nach Azure]: ../virtual-machines-create-upload-vhd-windows-server/
[Anmelden bei einem virtuellen Computer unter Windows Server]: ../virtual-machines-log-on-windows-server/
[Trennen eines Datenträgers von einem virtuellen Computer]: ../storage-windows-detach-disk/
[Über Azure-Speicherkonten]: ../storage-whatis-account/






<!--HONumber=42-->
