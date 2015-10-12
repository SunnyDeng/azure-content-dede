<properties
	pageTitle="Ändern des Datenträgerbuchstabens des temporären Datenträgers | Microsoft Azure"
	description="Erfahren Sie, wie Sie den Laufwerkbuchstaben des temporären Datenträgers auf einem mit dem klassischen Bereitstellungsmodell erstellten virtuellen Windows-Computer ändern."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn
"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/27/2015"
	ms.author="cynthn"/>

#Ändern des Laufwerkbuchstabens des temporären Windows-Datenträgers auf einem mit dem klassischen Bereitstellungsmodell erstellten virtuellen Computer

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Dieser Artikel behandelt das Erstellen von Ressourcen mit dem klassischen Bereitstellungsmodell.

Wenn Sie Laufwerk "D" zum Speichern der Daten verwenden müssen, befolgen Sie diese Anweisungen, um für den temporären Datenträger ein anderes Laufwerk zu verwenden. Verwenden Sie niemals den temporären Datenträger zum Speichern von Daten, die Sie behalten müssen.

Bevor Sie beginnen, benötigen Sie einen Datenträger, der an den virtuellen Computer angefügt ist, damit Sie die Windows-Auslagerungsdatei (pagefile.sys) während dieses Vorgangs speichern können. Weitere Informationen finden Sie bei Bedarf unter [Anfügen eines Datenträgers an einen virtuellen Windows-Computer][Attach]. Anweisungen dazu, wie Sie herausfinden, welche Datenträger angeschlossen sind, finden Sie unter "Suchen des Datenträgers" in [Trennen eines Datenträgers von einem virtuellen Windows-Computer][Detach].

Wenn Sie einen vorhandenen Datenträger auf Laufwerk D verwenden möchten, stellen Sie sicher, dass Sie auch die virtuelle Festplatte in das Speicherkonto hochgeladen haben. Eine Anleitung finden Sie in den Schritten 3 und 4 unter [Erstellen und Hochladen einer Windows Server VHD nach Azure][VHD].

> [AZURE.WARNING]Wenn Sie die Größe eines virtuellen Computers ändern oder diesen "Beenden (freigeben)", kann dies das Verschieben des virtuellen Computers auf einen neuen Hypervisor auslösen. Ein geplantes oder ungeplantes Wartungsereignis kann ebenfalls diese Neuanordnung auslösen. In diesem Szenario wird dem temporären Datenträger der erste verfügbare Laufwerkbuchstabe zugewiesen. Wenn keine Ihrer Anwendungen speziell das Laufwerk "D:" erfordert, müssen Sie nach dem Verschieben der Auslagerungsdatei einen neuen permanenten Datenträger zuweisen und diesem den Buchstaben "D" zuweisen. Azure verwendet den Buchstaben "D" dann nicht wieder.

> [AZURE.WARNING]Wenn Sie die Größe eines virtuellen Computers nach dem manuellen Verschieben der Auslagerungsdatei ändern, tritt beim Start möglicherweise ein Fehler auf, wenn der temporäre Datenträger für den neuen virtuellen Computer nicht groß genug für die Auslagerungsdatei des ursprünglichen virtuellen Computers ist. Dieser Fehler kann auch auftreten, wenn das temporäre Laufwerk nicht auf den nächsten verfügbaren Laufwerkbuchstaben festgelegt wurde, sodass Windows in der Konfiguration der Auslagerungsdatei auf einen ungültigen Laufwerkbuchstaben verweist, während Azure das temporäre Laufwerk mit dem nächsten verfügbaren Laufwerksbuchstaben erstellt.

##Ändern des Datenträgerbuchstabens

1. Melden Sie sich beim virtuellen Computer an. Weitere Informationen finden Sie unter [Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird][Logon].

2. Verschieben Sie pagefile.sys vom Laufwerk D auf ein anderes Laufwerk.

3. Starten Sie den virtuellen Computer neu.

4. Melden Sie sich wieder an, und ändern Sie den Laufwerkbuchstaben von D auf E.

5. Fügen Sie über das [Azure-Portal](http://manage.windowsazure.com) einen vorhandenen oder leeren Datenträger an.

6.	Melden Sie sich erneut am virtuellen Computer an, initialisieren Sie den Datenträger, und weisen Sie D als Laufwerkbuchstaben für den soeben angefügten Datenträger zu.

7.	Stellen Sie sicher, dass E dem temporären Datenträger zugeordnet ist.

8.	Verschieben Sie pagefile.sys vom anderen Laufwerk das Laufwerk E.

9.	Starten Sie den virtuellen Computer neu.



## Zusätzliche Ressourcen
[Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird][Logon]

[Trennen eines Datenträgers von einem virtuellen Windows-Computer][Detach]

[Informationen zu Azure-Speicherkonten][Storage]

<!--Link references-->
[Attach]: storage-windows-attach-disk.md



[VHD]: virtual-machines-create-upload-vhd-windows-server.md

[Logon]: virtual-machines-log-on-windows-server.md

[Detach]: storage-windows-detach-disk.md

[Storage]: ../storage-whatis-account.md

<!---HONumber=Oct15_HO1-->