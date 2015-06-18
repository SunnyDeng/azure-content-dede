<properties 
	pageTitle="Ändern des Datenträgerbuchstabens des temporären Windows-Datenträgers" 
	description="Beschreibt die erneute Zuordnung des temporären Datenträgers auf einem virtuellen Computer unter Windows in Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/15/2015" 
	ms.author="kathydav"/>

#Ändern des Datenträgerbuchstabens des temporären Windows-Datenträgers

Wenn Sie Laufwerk "D" zum Speichern der Daten verwenden müssen, befolgen Sie diese Anweisungen, um für den temporären Datenträger ein anderes Laufwerk zu verwenden. Verwenden Sie niemals das temporäre Laufwerk zum Speichern von Daten, die Sie behalten müssen.

Bevor Sie beginnen, benötigen Sie einen Datenträger, der an den virtuellen Computer angefügt ist, damit Sie die Windows-Auslagerungsdatei (pagefile.sys) während dieses Vorgangs speichern können. Weitere Informationen finden Sie bei Bedarf unter [Anfügen eines Datenträgers an einen virtuellen Windows-Computer]. Eine Anleitung dazu, wie Sie ermitteln können, welche Datenträger angefügt sind, finden Sie im Artikel [Informationen zu Datenträgern virtueller Computer in Azure] unter "Verwalten der Datenträger".

Wenn Sie einen vorhandenen Datenträger auf Laufwerk D verwenden möchten, stellen Sie sicher, dass Sie auch die virtuelle Festplatte in das Speicherkonto hochgeladen haben. Eine Anleitung finden Sie in Schritt 3 und 4 unter [Erstellen und Hochladen einer Windows Server VHD zu Azure].

> [AZURE.WARNING]Wenn Sie die Größe eines virtuellen Computers ändern und der virtuelle Computer hierdurch auf einen anderen Host verschoben wird, wird das temporäre Laufwerk wieder Laufwerk D geändert.

##Ändern des Datenträgerbuchstabens

1. Melden Sie sich beim virtuellen Computer an. 

2. Verschieben Sie pagefile.sys vom Laufwerk D auf ein anderes Laufwerk.

3. Starten Sie den virtuellen Computer neu.

4. 	Melden Sie sich wieder an und ändern Sie den Laufwerksbuchstaben von D auf E.

5.	Verbinden Sie über das [Azure-Verwaltungsportal](http://manage.windowsazure.com) einen vorhandenen oder leeren Datenträger.

6.	Melden Sie sich erneut am virtuellen Computer an, initialisieren Sie das Laufwerk und weisen Sie D als Laufwerksbuchstaben für das soeben angefügte Laufwerk zu.

7.	Stellen Sie sicher, dass E dem temporären Speicherlaufwerk zugeordnet ist.

8.	Verschieben Sie pagefile.sys vom anderen Laufwerk das Laufwerk E.

##Zusätzliche Ressourcen
[Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird]

[Trennen eines Datenträgers von einem virtuellen Computer]

[Informationen zu Azure-Speicherkonten]

<!--Link references-->
[Anfügen eines Datenträgers an einen virtuellen Windows-Computer]: storage-windows-attach-disk.md
[Informationen zu Datenträgern virtueller Computer in Azure]: ../dn790303.aspx
[Erstellen und Hochladen einer Windows Server VHD zu Azure]: virtual-machines-create-upload-vhd-windows-server.md
[Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird]: virtual-machines-log-on-windows-server.md
[Trennen eines Datenträgers von einem virtuellen Computer]: storage-windows-detach-disk.md
[Informationen zu Azure-Speicherkonten]: ../storage-whatis-account.md

<!---HONumber=58--> 