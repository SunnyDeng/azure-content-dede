<properties
   pageTitle="Erstellen eines virtuellen Azure-Computers mit der xplat-cli"
   description="In diesem Thema wird beschrieben, wie Sie die xplat-cli auf einer beliebigen Plattform installieren, diese zur Verbindungsherstellung mit Ihrem Azure-Konto verwenden und eine VM aus der xplat-cli erstellen."
   services="virtual-machines"
   documentationCenter="virtual-machines"
   authors="squillace"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="command-line-interface"
   ms.workload="infrastructure-services"
   ms.date="02/20/2015"
   ms.author="rasquill"/>

# Erstellen einer VM mit der plattformübergreifenden Azure-Befehlszeilenschnittstelle (xplat-cli)
Die plattformübergreifende Azure-Befehlszeilenschnittstelle (xplat-cli) ist eine hervorragende Methode, um Ihre Azure-Infrastruktur von einer beliebigen Plattform aus zu verwalten.

Sie müssen lediglich die xplat-cli installieren und über ein Azure-Abonnement verfügen, um sofort eine VM zu erstellen. Führen wir diese Schritte also aus. Wenn Sie noch kein Azure-Konto besitzen, erwerben Sie ein [kostenloses Abonnement](http://azure.microsoft.com/pricing/free-trial/).

## Installieren der xplat-cli

Folgen Sie diesen Anweisungen, um die [xplat-cli](http://azure.microsoft.com/documentation/articles/xplat-cli/#install) zu installieren.

## Verbindungsherstellung mit Azure über die xplat-cli

Sie können Ihre xplat-cli-Installation mit einem persönlichen Azure-Konto oder einem Arbeits- oder Schulkonto für Azure verbinden. Die Unterschiede zwischen diesen Konten werden unter [Verbinden mit Ihrem Azure-Abonnement](http://azure.microsoft.com/documentation/articles/xplat-cli/#configure) erläutert.

## Erstellen einer VM und Verbinden mit Azure

Das Erstellen einer VM beginnt mit der Auswahl (oder dem Hochladen) eines Images und der Verwendung des `azure vm create`-Befehls.

1. Um ein Image über die Befehlszeile auszuwählen, können Sie die verfügbaren VM-Images mit dem Befehl `azure vm image list` auflisten. Da es sehr viele Images gibt, sollten Sie die Ergebnisse mithilfe von `more` in Seiten einteilen oder über `grep` (Linux) oder `findstr` (Windows) filtern. Wenn Sie beispielsweise nach Ubuntu-Images unter Linux suchen, verwenden Sie einen Befehl wie diesen:

        azure vm image list | grep Ubuntu

    Die daraufhin angezeigte Imageliste ist weiterhin lang, deshalb können Sie eine weitere Filterung nach Version vornehmen:

        azure vm image list | grep Ubuntu-14_10

    Aus den Ergebnissen können Sie ein Image auswählen und den Befehl `show` verwenden, um die zugehörigen Eigenschaften im Detail anzuzeigen:

        azure vm image show b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-de-de-30GB

2. Erstellen Sie Ihre VM.

    Nachdem Sie ein VM-Image ausgewählt haben, verwenden Sie den Befehl `vm create`, um das Image zu erstellen. Dieser Befehl umfasst viele Optionen, die Sie mit dem Hilfebefehl auflisten können:

        vm create --help

    Gemeinsam mit dem Image aus Schritt 1 sind die wichtigsten Argumente zum Erstellen einer VM der Speicherort, der DNS-Name und der Benutzername.

    Für die Authentifizierung können Sie ein Kennwort angeben (an der Befehlszeile oder interaktiv) oder die Authentifizierung mit einem Zertifikat durchführen. Wenn Sie sich für ein Kennwort entscheiden, muss dieses mindestens 8 Zeichen umfassen, sowohl Groß- als auch Kleinbuchstaben sowie ein Sonderzeichen (d. h. eines dieser Zeichen: !@#$%^&+=) enthalten. Das Kennwort sollte in Anführungszeichen gesetzt und Sonderzeichen mit Escapezeichen versehen werden, wenn Sie es an der Befehlszeile übergeben.

    Um sich für einen Speicherort zu entscheiden, können Sie mit dem Befehl `vm location list` eine Region in Ihrer Nähe auswählen.

  Der gewählte DNS-Name muss eindeutig sein (er wird  `dnsname.cloudapp.net` zugeordnet) und entspricht dem Namen des virtuellen Computers, wenn Sie an der Befehlszeile keinen separaten VM-Namen eingeben.  

    Im nachstehenden Linux-Beispiel wird eine VM in der Region "West US" erstellt, der SSH-Standardport 22 geöffnet (Argument "-e") und ein Benutzer namens `myadminuser` erstellt:

        azure vm create -e -l "West US"  my-new-cli-vm b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-de-de-30GB "myadminuser" "myAdm1n@passwd"

## Nächste Schritte

Führen wir nun einige Aufgaben mit Ihrer VM aus. 

Da im obigen Beispiel der SSH-Standardport geöffnet wurde, kann auf einfache Weise eine Verbindung mit der VM hergestellt und deren ordnungsgemäßer Betrieb überprüft werden. Von einer Linux-Befehlszeile:

    ssh myadminuser@my-new-cli-vm.cloudapp.net

Weitere Beispiele zur Verwendung der xplat-cli zum Verwalten Ihrer Azure-Infrastruktur finden Sie unter [Azure-Befehlszeilentool für Mac und Linux](http://azure.microsoft.com/documentation/articles/virtual-machines-command-line-tools/)

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png

<!--HONumber=47-->
