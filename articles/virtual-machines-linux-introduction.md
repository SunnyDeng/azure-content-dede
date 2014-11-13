<properties urlDisplayName="Intro to Linux" pageTitle="Einf&uuml;hrung zu Linux in Azure &ndash; Azure-Lernprogramm" metaKeywords="Azure Linux vm, Linux vm" description="Erfahren Sie, wie Sie virtuelle Linux-Computer auf Azure verwenden." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Einf&uuml;hrung in Linux auf Azure" authors="szark" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="szark" />

# Einführung in Linux auf Azure

Dieses Thema bietet einen Überblick über einige Aspekte der Verwendung virtueller Linux-Computer in der Azure-Cloud. Die Bereitstellung eines virtuellen Linux-Computers ist ein unkomplizierter Prozess, wenn sie mithilfe eines bereits in der Galerie vorhandenen Images erfolgt.

## Inhaltsverzeichnis

-   [Authentifizierung: Benutzernamen, Kennwörter und SSH-Schlüssel.][Authentifizierung: Benutzernamen, Kennwörter und SSH-Schlüssel.]
-   [Erzeugen und Verwenden von SSH-Schlüsseln für die Anmeldung auf virtuellen Linux-Computern][Erzeugen und Verwenden von SSH-Schlüsseln für die Anmeldung auf virtuellen Linux-Computern]
-   [Erlangen von Superuser-Berechtigungen mit sudo][Erlangen von Superuser-Berechtigungen mit sudo]
-   [Firewall-Konfiguration][Firewall-Konfiguration]
-   [Hostnamen-Änderungen][Hostnamen-Änderungen]
-   [Image-Erfassung virtueller Computer][Image-Erfassung virtueller Computer]
-   [Anfügen von Datenträgern][Anfügen von Datenträgern]

## <span id="authentication"></span></a>Authentifizierung: Benutzernamen, Kennwörter und SSH-Schlüssel

Wenn Sie mithilfe des Azure-Verwaltungsportals einen virtuellen Linux-Computer erstellen, werden Sie aufgefordert, einen Benutzernamen, ein Kennwort und (optional) einen öffentlichen SSH-Schlüssel anzugeben. Die Auswahl eines Benutzernamens zur Bereitstellung eines virtuellen Computers auf Azure ist den folgenden Beschränkungen unterworfen: Namen von bereits auf dem virtuellen Computer vorhandenen Systemkonten (UID \<100) – wie „root“ – sind nicht erlaubt.

-   Siehe [Gewusst wie: Verwenden von SSH mit Linux auf Azure][Gewusst wie: Verwenden von SSH mit Linux auf Azure].

### <span id="keygeneration"></span></a>Erzeugen eines SSH-Schlüssels

Die aktuelle Version des Verwaltungsportals akzeptiert nur öffentliche SSH-Schlüssel, die in einem X.509-Zertifikat enthalten sind. Gehen Sie wie nachstehend beschrieben vor, um SSH-Schlüssel mit Azure zu erzeugen und zu verwenden.

1.  Verwenden Sie openssl, um ein X.509-Zertifikat mit einem 2048-bit-RSA-Schlüsselpaar zu erzeugen.

        openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

    Bitte beantworten Sie die Fragen, die openssl Ihnen stellt (oder lassen Sie die Felder leer). Der Inhalt dieser Felder wird nicht von der Plattform verwendet.

2.  Ändern Sie die Berechtigungen auf dem privaten Schlüssel, um diesen zu sichern.

        chmod 600 myPrivateKey.key

3.  Konvertieren Sie `myCert.pem` zu `myCert.cer` (DER-codiertes X.509-Zertifikat).

        openssl  x509 -outform der -in myCert.pem -out myCert.cer

4.  Laden Sie das `myCert.cer` hoch, während Sie den virtuellen Linux-Computer erstellen. Der Bereitstellungsvorgang installiert automatisch den öffentlichen Schlüssel in diesem Zertifikat in der Datei `~/.ssh/authorized_keys` für den angegebenen Benutzer im virtuellen Computer.

5.  Stellen Sie die Verbindung mit dem virtuellen Linux-Computer mithilfe von ssh her.

        ssh -i  myPrivateKey.key -p port  username@servicename.cloudapp.net

    Beim ersten Anmelden werden Sie aufgefordert, den Fingerabdruck des öffentlichen Schlüssels des Hosts zu übernehmen.

6.  Sie möchten möglicherweise optional `myPrivateKey.key` zu `~/.ssh/id_rsa` kopieren, sodass Ihr OpenSSH-Client dies automatisch auswählen kann, ohne dass die Option „-i“ verwendet werden muss.
     Alternativ können Sie `~/.ssh/config` so ändern, dass sie einen danach einen Abschnitt für Ihren virtuellen Computer enthält:

        Host servicename.cloudapp.net
          IdentityFile %d/.ssh/myPrivateKey.key

### Erzeugen eines Schlüssels aus einem vorhandenen OpenSSH-kompatiblen Schlüssel

Im vorherigen Beispiel wurde die Erstellung eines neuen Schlüssels zur Verwendung mit Azure beschrieben. In manchen Fällen können Benutzer bereits über ein vorhandenes OpenSSH-kompatibles Schlüsselpaar (öffentlich und privat) verfügen und vorhaben, diese Schlüssel in Azure zu verwenden.

Private OpenSSH-Schlüssel können direkt vom Hilfsprogramm `openssl` gelesen werden. Mit dem folgenden Befehl wird aus dem vorhandenen privaten SSH-Schlüssel (im Beispiel unten id\_rsa) der öffentliche Schlüssel `.pem` erstellt, der für Microsoft Azure erforderlich ist:

    # openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem

Die Datei **myCert.pem** ist der öffentliche Schlüssel, der anschließend verwendet werden kann, um einen virtuellen Linux-Computer auf Azure bereitzustellen. Während der Bereitstellung wird die Datei `.pem` in einen `openssh`-kompatiblen öffentlichen Schlüssel übersetzt und in `~/.ssh/authorized_keys` platziert.

## <span id="superuserprivileges"></span></a>Erlangen von Superuser-Berechtigungen mit `sudo`

Das Benutzerkonto, das während der Bereitstellung der virtuellen Computerinstanz auf Azure angegeben wird, ist ein Konto mit lokal weit reichenden Berechtigungen. Dieses Konto wird vom Azure Linux-Agent konfiguriert, damit die Berechtigungen mithilfe des `sudo`-Hilfsprogramms auf „root“ (Superuser-Konto) erweitert werden können. Sobald Sie über dieses Benutzerkonto angemeldet sind, können Sie mit dieser Befehlssyntax Befehle als „root“ ausführen.

    # sudo <COMMAND>

Optional können Sie über **sudo -s** eine Root-Shell abrufen.

-   Siehe [Verwenden von Stammberechtigungen auf virtuellen Linux-Computern in Azure][Verwenden von Stammberechtigungen auf virtuellen Linux-Computern in Azure].

## <span id="firewallconfiguration"></span></a>Firewall-Konfiguration

Azure bietet einen integrierten Paketfilter, der die Konnektivität auf die im Verwaltungsportal angegebenen Ports beschränkt. Standardmäßig ist SSH der einzige erlaubte Port. Sie können den Zugriff auf zusätzliche Ports auf Ihrem virtuellen Linux-Computer aktivieren, indem Sie im Verwaltungsportal Endpunkte konfigurieren.

-   Siehe: [Einrichten von Endpunkten für einen virtuellen Computer][Einrichten von Endpunkten für einen virtuellen Computer]

Die Linux-Images in der Azure-Galerie aktivieren die Firewall *iptables* nicht standardmäßig. Bei Bedarf kann die Firewall für die Bereitstellung einer zusätzlichen Filterung konfiguriert werden.

## <span id="hostnamechanges"></span></a>Hostnamen-Änderungen

Wenn Sie eine Instanz eines Linux-Images zum ersten Mal bereitstellen, müssen Sie einen Hostnamen für den virtuellen Computer bereitstellen. Wenn der virtuelle Computer läuft, wird dieser Hostname auf den Plattform-DNS-Servern veröffentlicht, so dass mehrere miteinander verbundene virtuelle Computer IP-Adresssuchen anhand von Hostnamen durchführen können.

Wenn nach der Bereitstellung eines virtuellen Computers Hostnamenänderungen gewünscht werden, verwenden Sie den folgenden Befehl:

    # sudo hostname <newname>

Der Azure Linux-Agent enthält eine Funktion zur automatischen Erkennung dieser Namensänderung und zur entsprechenden Konfiguration des virtuellen Computers für eine Beibehaltung dieser Änderung und für ihre Veröffentlichung auf DNS-Plattformservern.

-   [Benutzerhandbuch für Azure Linux-Agent][Benutzerhandbuch für Azure Linux-Agent]

### Ubuntu-Images

Ubuntu-Images nutzen cloud-init, was zusätzliche Funktionalitäten für das Bootstrapping eines virtuellen Computers bietet.

-   Siehe [Custom Data and Cloud-Init on Microsoft Azure][Custom Data and Cloud-Init on Microsoft Azure] (Benutzerdefinierte Daten und Cloud-Init auf Microsoft Azure) (in englischer Sprache).

## <span id="virtualmachine"></span></a>Image-Erfassung virtueller Computer

Azure bietet die Möglichkeit, den Status eines vorhandenen virtuellen Computers in einem Image zu erfassen, das danach verwendet kann, um zusätzliche virtuelle Computerinstanzen bereitzustellen. Der Azure Linux Agent kann verwendet werden, um einige während des Bereitstellungsprozesses durchgeführten Anpassungen zurückzusetzen. Sie können die Schritte unten ausführen, um einen virtuellen Computer als Image zu erfassen.

1.  Führen Sie **waagent -deprovision** aus, um bei Bereitstellungen durchgeführte Anpassungen rückgängig zu machen. Oder optional **waagent -deprovision+user**, um das während der Bereitstellung angegebene Benutzerkonto und alle damit zusammenhängenden Daten zu löschen.

2.  Schalten Sie den virtuellen Computer aus.

3.  Klicken Sie im Verwaltungsportal auf *Aufnehmen*, oder verwenden Sie die PowerShell oder Befehlszeilenschnittstellen-Tools zum Erfassen des virtuellen Computers als ein Image.

-   Siehe: [Erfassen eines virtuellen Linux-Computers, um ihn als Vorlage zu verwenden][Erfassen eines virtuellen Linux-Computers, um ihn als Vorlage zu verwenden]

## <span id="attachingdisks"></span></a>Anfügen von Datenträgern

An jeden virtuellen Computer ist ein temporärer lokaler *Ressourcendatenträger* angefügt. Da die Daten auf einem Ressourcendatenträger bei Neustarts verloren gehen können, werden Ressourcendatenträger oft von Anwendungen und Prozessen zur vorübergehenden und **temporären** Datenspeicherung auf dem virtuellen Computer verwendet. Außerdem dienen sie zur Speicherung von Auslagerungsdateien für das Betriebssystem.

Unter Linux wird der Ressourcendatenträger normalerweise vom Azure Linux Agent verwaltet und automatisch an **/mnt/resource** (oder auf Ubuntu-Images an **/mnt**) angefügt.

    >[WACOM.NOTE] Note that the resource disk is a **temporary** disk, and might be deleted and reformatted when the VM is rebooted.

Der Datenträger unter Linux kann vom Kernel den Namen `/dev/sdc` erhalten, und die Benutzer müssen diese Ressource partitionieren, formatieren und bereitstellen. Dies wird im folgenden Lernprogramm Schritt für Schritt erläutert: [Gewusst wie: Anfügen eines Datenträgers an einen virtuellen Computer][Gewusst wie: Anfügen eines Datenträgers an einen virtuellen Computer].

-   Weitere Informationen: [Konfigurieren des Software-RAID unter Linux][Konfigurieren des Software-RAID unter Linux]

  [Authentifizierung: Benutzernamen, Kennwörter und SSH-Schlüssel.]: #authentication
  [Erzeugen und Verwenden von SSH-Schlüsseln für die Anmeldung auf virtuellen Linux-Computern]: #keygeneration
  [Erlangen von Superuser-Berechtigungen mit sudo]: #superuserprivileges
  [Firewall-Konfiguration]: #firewallconfiguration
  [Hostnamen-Änderungen]: #hostnamechanges
  [Image-Erfassung virtueller Computer]: #virtualmachine
  [Anfügen von Datenträgern]: #attachingdisks
  [Gewusst wie: Verwenden von SSH mit Linux auf Azure]: ../linux-use-ssh-key/
  [Verwenden von Stammberechtigungen auf virtuellen Linux-Computern in Azure]: ../virtual-machines-linux-use-root-privileges/
  [Einrichten von Endpunkten für einen virtuellen Computer]: ../virtual-machines-set-up-endpoints/
  [Benutzerhandbuch für Azure Linux-Agent]: ../virtual-machines-linux-agent-user-guide/
  [Custom Data and Cloud-Init on Microsoft Azure]: http://azure.microsoft.com/blog/2014/04/21/custom-data-and-cloud-init-on-windows-azure/
  [Erfassen eines virtuellen Linux-Computers, um ihn als Vorlage zu verwenden]: ../virtual-machines-linux-capture-image/
  [Gewusst wie: Anfügen eines Datenträgers an einen virtuellen Computer]: ../virtual-machines-linux-how-to-attach-disk/
  [Konfigurieren des Software-RAID unter Linux]: ../virtual-machines-linux-configure-raid/
