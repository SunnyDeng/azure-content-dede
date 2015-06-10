<properties
	pageTitle="Erfassen eines Images des virtuellen Computers unter Linux"
	description="Erfahren Sie, wie Sie ein Image eines virtuellen Azure-Computers unter Linux erfassen können."
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
	ms.date="03/16/2015" 
	ms.author="kathydav"/>


# Gewusst wie: Erfassen eines virtuellen Linux-Computers, um ihn als Vorlage zu verwenden##

Dieser Artikel erläutert, wie Sie einen virtuellen Azure-Computer erfassen, auf dem Linux läuft, um ihn wie eine Vorlage zum Erstellen anderer virtueller Computer zu verwenden. Diese Vorlage umfasst den Betriebssystemdatenträger und alle an den virtuellen Computer angefügten Datenträger. Da die Vorlage keine Netzwerkkonfiguration enthält, müssen Sie die Konfiguration später vornehmen, wenn Sie die anderen virtuellen Computer erstellen, die auf dieser Vorlage basieren.

Azure behandelt diese Vorlage als ein Image und speichert es unter **Eigene Images**. Hier werden sämtliche Images abgelegt, die Sie hochladen. Weitere Informationen zu Images finden Sie unter [Informationen zu virtuellen Computern in Azure][].

##Voraussetzungen##

Diese Schritte setzen voraus, dass Sie bereits einen virtuellen Azure-Computer erstellt, das Betriebssystem konfiguriert und beliebige Datenträger angefügt haben. Falls dies noch nicht geschehen ist, finden Sie hier Anweisungen:

- [Erstellen eines benutzerdefinierten virtuellen Computers][]
- [Anfügen eines Datenträgers an einen virtuellen Computer][]

##Erfassen des virtuellen Computers##

1. Stellen Sie eine Verbindung mit dem virtuellen Computer her, indem Sie auf der Befehlsleiste auf **Verbinden** klicken. Ausführliche Informationen finden Sie unter [Anmelden bei einem virtuellen Computer, auf dem Linux ausgeführt wird][].

2. Geben Sie im SSH-Fenster den folgenden Befehl ein, und geben Sie anschließend das Kennwort für das Konto ein, das Sie auf dem virtuellen Computer erstellt haben. Beachten Sie, dass die Ausgabe von `waagent` je nach Version dieses Hilfsprogramms geringfügig abweichen kann:

	`sudo waagent -deprovision`

	![Virtuellen Computer zurücksetzen](./media/virtual-machines-linux-capture-image/LinuxDeprovision.png)


3. Geben Sie **y** ein, um fortzufahren.

	![Zurücksetzen des virtuellen Computers erfolgreich](./media/virtual-machines-linux-capture-image/LinuxDeprovision2.png)

4. Geben Sie **Exit** ein, um den SSH-Client zu schließen.

5. Wählen Sie im [Verwaltungsportal](http://manage.windowsazure.com) den virtuellen Computer aus, und klicken Sie anschließend auf **Herunterfahren**.

6. Wenn der virtuelle Computer beendet wird, klicken Sie in der Befehlsleiste auf **Erfassen**, um das Dialogfeld **Virtuellen Computer erfassen** zu öffnen.

7.	Geben Sie unter **Imagename** den Namen für das neue Image ein.

8.	Die Bereitstellung aller Linux-Images muss *aufgehoben* werden, indem der Befehl `waagent` mit der Option `-deprovision` ausgeführt wird. Geben Sie an, dass Sie "waagent-deprovision" auf dem virtuellen Computer ausgeführt haben, indem Sie auf die entsprechende Option klicken. So geben Sie an, dass das Betriebssystem für die Verwendung als Image vorbereitet wurde.

9.	Aktivieren Sie das Kontrollkästchen, um das Image zu erfassen.

	Das neue Image steht nun unter **Images** zur Verfügung. Der virtuelle Computer wird gelöscht, nachdem das Image erfasst wurde.

	![Image-Erfassung erfolgreich](./media/virtual-machines-linux-capture-image/VMCapturedImageAvailable.png)

##Nächste Schritte##
Das Image kann jetzt als Vorlage zum Erstellen virtueller Computer verwendet werden. Dazu erstellen Sie mithilfe der Methode **Aus Katalog** einen benutzerdefinierten virtuellen Computer und wählen das gerade erstellte Image aus. Anweisungen hierzu finden Sie unter [Erstellen eines benutzerdefinierten virtuellen Computers][].

[Anmelden bei einem virtuellen Computer, auf dem Linux ausgeführt wird]: virtual-machines-linux-how-to-log-on.md
[Informationen zu virtuellen Computern in Azure]: http://msdn.microsoft.com/library/azure/dn790290.aspx
[Erstellen eines benutzerdefinierten virtuellen Computers]: virtual-machines-create-custom.md
[Anfügen eines Datenträgers an einen virtuellen Computer]: storage-windows-attach-disk.md

<!---HONumber=58-->