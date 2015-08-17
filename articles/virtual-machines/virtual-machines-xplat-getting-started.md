<properties
   pageTitle="Erstellen eines virtuellen Azure-Computers mit der Azure-Befehlszeilenschnittstelle | Microsoft Azure"
   description="In diesem Thema erfahren Sie, wie Sie die Azure-Befehlszeilenschnittstelle auf einer beliebigen Plattform installieren, eine Verbindung mit Ihrem Azure-Konto herstellen und über die Azure-Befehlszeilenschnittstelle einen virtuellen Computer erstellen."
   services="virtual-machines"
   documentationCenter="virtual-machines"
   authors="dlepow"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="command-line-interface"
   ms.workload="infrastructure-services"
   ms.date="06/09/2015"
   ms.author="danlep"/>

# Erstellen eines virtuellen Computers mithilfe der Azure-Befehlszeilenschnittstelle (Azure-CLI)
Die Azure-Befehlszeilenschnittstelle ist eine hervorragende Methode, um Ihre Azure-Infrastruktur von einer beliebigen Plattform aus zu verwalten.

Wenn Sie die Azure-Befehlszeilenschnittstelle installiert haben und über ein Azure-Abonnement verfügen, können Sie sofort einen virtuellen Computer erstellen. Führen wir diese Schritte also aus. Wenn Sie noch kein Azure-Konto besitzen, erhalten Sie [hier](http://azure.microsoft.com/pricing/free-trial/) ein kostenloses Abonnement.

## Installieren der Azure-Befehlszeilenschnittstelle

Befolgen Sie die Anweisungen für das [Installieren der Azure-Befehlszeilenschnittstelle](../xplat-cli.md#install).

## Herstellen einer Verbindung mit Azure unter Verwendung der Azure-Befehlszeilenschnittstelle

Sie können die Installation der Azure-Befehlszeilenschnittstelle mit einem persönlichen Azure-Konto oder einem Arbeits- oder Schulkonto für Azure verbinden. Die Unterschiede zwischen diesen Konten werden unter [Verbinden mit Ihrem Azure-Abonnement](../xplat-cli.md#configure) erläutert.

## Erstellen eines virtuellen Computers und Herstellen einer Verbindung mit Azure

Das Erstellen eines virtuellen Computers beginnt mit der Auswahl (oder dem Hochladen) eines Image und der Verwendung des Befehls `azure vm create`.

1. Beim Auswählen eines Images über die Befehlszeile können Sie mithilfe des Befehls `azure vm image list` die verfügbaren VM-Images auflisten. Da sehr viele Images zur Auswahl stehen, sollten Sie die Ergebnisse mithilfe von `more` in Seiten unterteilen oder über `grep` (Linux) oder `findstr` (Windows) filtern. Wenn Sie beispielsweise unter Linux nach Ubuntu-Images suchen, verwenden Sie einen Befehl wie diesen:

        azure vm image list | grep Ubuntu

    Die daraufhin angezeigte Imageliste ist weiterhin lang, deshalb können Sie eine weitere Filterung nach Version vornehmen:

        azure vm image list | grep Ubuntu-14_10

    Aus den Ergebnissen können Sie ein Image auswählen und den Befehl `show` verwenden, um die zugehörigen Eigenschaften im Detail anzuzeigen:

        azure vm image show b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-de-de-30GB

2. Nachdem Sie ein VM-Image ausgewählt haben, verwenden Sie den Befehl `vm create`, um das Image zu erstellen. Dieser Befehl umfasst viele Optionen, die Sie mit dem Befehl `help` auflisten können:

        vm create --help

    Gemeinsam mit dem Image aus Schritt 1 sind die wichtigsten Argumente zum Erstellen einer VM der Speicherort, der DNS-Name und der Benutzername.

    Für die Authentifizierung können Sie ein Kennwort angeben (an der Befehlszeile oder interaktiv) oder die Authentifizierung mit einem Zertifikat durchführen. Wenn Sie sich für ein Kennwort entscheiden, muss dieses mindestens 8 Zeichen umfassen, sowohl Groß- als auch Kleinbuchstaben sowie ein Sonderzeichen (d. h. eines dieser Zeichen: !@#$%^&+=) enthalten. Das Kennwort sollte in Anführungszeichen gesetzt und Sonderzeichen mit Escapezeichen versehen werden, wenn Sie es an der Befehlszeile übergeben.

    Bei der Wahl eines geeigneten Speicherorts können Sie mit dem Befehl `vm location list` eine Region in Ihrer Nähe auswählen.

  Der gewählte DNS-Name muss eindeutig sein (er wird `dnsname.cloudapp.net` zugeordnet) und entspricht dem Namen des virtuellen Computers, wenn Sie an der Befehlszeile keinen separaten VM-Namen eingeben.

    The Linux example below creates a VM in West US, opens the default SSH port 22 (the -e argument), and creates a user called `myadminuser`:

        azure vm create -e -l "West US"  my-new-cli-vm b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-de-de-30GB "myadminuser" "myAdm1n@passwd"

## Nächste Schritte

Führen wir nun einige Aufgaben mit Ihrer VM aus.

Da im obigen Beispiel der SSH-Standardport geöffnet wurde, kann auf einfache Weise eine Verbindung mit der VM hergestellt und deren ordnungsgemäßer Betrieb überprüft werden. Von einer Linux-Befehlszeile:

    ssh myadminuser@my-new-cli-vm.cloudapp.net

Weitere Beispiele zur Verwendung der Azure-Befehlszeilenschnittstelle zum Verwalten Ihrer Azure-Infrastruktur finden Sie auf der [Befehlsreferenzseite für die Azure-Befehlszeilenschnittstelle](../virtual-machines-command-line-tools.md).

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png

<!---HONumber=August15_HO6-->