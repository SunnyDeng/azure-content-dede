<properties
	pageTitle="Erfassen eines Images eines virtuellen Linux-Computers | Microsoft Azure"
	description="Erfahren Sie, wie Sie ein Image eines mit dem klassischen Bereitstellungsmodell erstellten virtuellen Azure-Computers unter Linux erfassen können."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/14/2015"
	ms.author="dkshir"/>


# Gewusst wie: Erfassen eines virtuellen Linux-Computers, um ihn als Vorlage zu verwenden

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Ressourcen-Manager-Modell.


Dieser Artikel erläutert, wie Sie einen virtuellen Azure-Computer erfassen, auf dem Linux läuft, um ihn wie eine Vorlage zum Erstellen anderer virtueller Computer zu verwenden. Diese Vorlage umfasst den Betriebssystem-Datenträger und die an den virtuellen Computer angefügten Datenträger. Da die Vorlage keine Netzwerkkonfiguration enthält, müssen Sie die Konfiguration später vornehmen, wenn Sie die anderen virtuellen Computer erstellen, die auf dieser Vorlage basieren.

Azure behandelt diese Vorlage als lokales Image und speichert es unter**Images**. Hier werden sämtliche Images abgelegt, die Sie hochladen. Weitere Informationen zu Images finden Sie unter [Informationen zu virtuellen Computern in Azure][].

## Voraussetzungen

Diese Schritte setzen voraus, dass Sie bereits mithilfe des klassischen Bereitstellungsmodells einen virtuellen Azure-Computer erstellt, das Betriebssystem konfiguriert und beliebige Datenträger angefügt haben. Falls dies noch nicht geschehen ist, finden Sie hier Anweisungen:

- [Erstellen eines virtuellen Linux-Computers][]


## Erfassen des virtuellen Computers

1. Stellen Sie mit einem SSH-Client Ihrer Wahl eine Verbindung mit dem virtuellen Computer her. Ausführliche Informationen finden Sie unter [Anmelden bei einem virtuellen Computer, auf dem Linux ausgeführt wird][].

2. Geben Sie im SSH-Fenster den folgenden Befehl ein. Beachten Sie, dass die Ausgabe von `waagent` je nach Version dieses Hilfsprogramms geringfügig abweichen kann:

	`sudo waagent -deprovision`

	Dieser Befehl versucht, das System zu bereinigen und für eine erneute Bereitstellung vorzubereiten. Bei diesem Vorgang werden die folgenden Aufgaben ausgeführt:

	- Entfernen von SSH-Hostschlüsseln (sofern "Provisioning.RegenerateSshHostKeyPair" in der Konfigurationsdatei auf "y" festgelegt ist)
	- Löschen der Namenserverkonfiguration in "/etc/resolv.conf"
	- Entfernen des Kennworts des `root`-Benutzers aus "/etc/shadow" (sofern "Provisioning.DeleteRootPassword" in der Konfigurationsdatei auf "y" festgelegt ist)
	- Entfernen von zwischengespeicherten DHCP-Clientleases
	- Setzt den Hostnamen auf "localhost.localdomain" zurück
	- Löschen des zuletzt bereitgestellten Benutzerkontos (aus "/var/lib/waagent" abgerufen) **und der zugehörigen Daten**

	>[AZURE.NOTE]Beim Aufheben der Bereitstellung werden Dateien und Daten gelöscht, um das Image zu "verallgemeinern". Führen Sie diesen Befehl nur auf virtuellen Computern aus, die Sie als neue Imagevorlage aufzeichnen möchten. Dies garantiert nicht, dass alle vertraulichen Informationen aus dem Image gelöscht werden oder dass es für eine erneute Verteilung an Dritte genutzt werden kann.


3. Geben Sie **y** ein, um fortzufahren. Sie können den Parameter `-force` hinzufügen, um diesen Bestätigungsschritt zu vermeiden.

4. Geben Sie **Exit** ein, um den SSH-Client zu schließen.


	>[AZURE.NOTE]Bei den nächsten Schritten wird davon ausgegangen, dass Sie [die Azure-Befehlszeilenschnittstelle auf dem Clientcomputer installiert haben](../xplat-cli-install.md). Die folgenden Schritte können Sie auch im [Verwaltungsportal][] ausführen.

5. Öffnen Sie auf dem Clientcomputer die Azure-CLI, und melden Sie sich bei Ihrem Azure-Abonnement an. Weitere Informationen hierzu finden Sie unter [Herstellen einer Verbindung mit einem Azure-Abonnement von der Azure-CLI](../xplat-cli-connect.md).

6. Stellen Sie sicher, dass der Dienstverwaltungsmodus aktiviert ist:

	`azure config mode asm`

7. Beenden Sie den virtuellen Computer, dessen Bereitstellung mit den oben stehenden Schritten bereits aufgehoben wurde:

	`azure vm shutdown <your-virtual-machine-name>`

	>[AZURE.NOTE]Mit `azure vm list` können Sie alle in Ihrem Abonnement erstellten virtuellen Computer ermitteln.

8. Wenn der virtuelle Computer beendet wird, erfassen Sie das Image mit dem folgenden Befehl:

	`azure vm capture -t <your-virtual-machine-name> <new-image-name>`

	Geben Sie für _new-image-name_ den gewünschten Imagenamen ein. Dieser Befehl erstellt ein generalisiertes Betriebssystemimage. Der Unterbefehl `-t` löscht den ursprünglichen virtuellen Computer.

9.	Das neue Image ist jetzt in der Liste der Images verfügbar, die zum Konfigurieren neuer virtueller Computer verwendet werden können. Sie können es mit dem folgenden Befehl anzeigen:

	`azure vm image list`

	Im [Verwaltungsportal][] wird es in der Liste **IMAGES** angezeigt.

	![Image-Erfassung erfolgreich](./media/virtual-machines-linux-capture-image/VMCapturedImageAvailable.png)


## Nächste Schritte
Das Image kann jetzt als Vorlage zum Erstellen virtueller Computer verwendet werden. Sie können den Azure-CLI-Befehl `azure vm create` verwenden und den Namen des Images angeben, das Sie gerade erstellt haben. Weitere Informationen zu dem Befehl finden Sie unter [Verwenden der Azure-Befehlszeilenschnittstelle mit der Dienstverwaltungs-API](virtual-machines-command-line-tools.md). Alternativ können Sie einen benutzerdefinierten virtuellen Computer über das [Verwaltungsportal][] erstellen, indem Sie die Methode **Aus Katalog** verwenden und das Image auswählen, das Sie gerade erstellt haben. Weitere Informationen finden Sie unter [Erstellen eines benutzerdefinierten virtuellen Computers][].

**Siehe auch**: [Benutzerhandbuch für Azure Linux-Agent](virtual-machines-linux-agent-user-guide.md)

[Verwaltungsportal]: http://manage.windowsazure.com
[Anmelden bei einem virtuellen Computer, auf dem Linux ausgeführt wird]: virtual-machines-linux-how-to-log-on.md
[Informationen zu virtuellen Computern in Azure]: virtual-machines-images.md
[Erstellen eines benutzerdefinierten virtuellen Computers]: virtual-machines-linux-create-custom.md
[How to Attach a Data Disk to a Virtual Machine]: storage-windows-attach-disk.md
[Erstellen eines virtuellen Linux-Computers]: virtual-machines-linux-tutorial.md

<!---HONumber=Oct15_HO4-->