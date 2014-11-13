<properties urlDisplayName="Use SSH" pageTitle="Verwenden von SSH zum Herstellen einer Verbindung zu virtuellen Linux-Computern in Azure" metaKeywords="Azure SSH keys Linux, Linux vm SSH" description="Erfahren Sie, wie Sie SSH-Schl&uuml;ssel mit einem virtuellen Linux-Computer auf Azure generieren und verwenden." metaCanonical="" services="virtual-machines" documentationCenter="" title="Verwenden von SSH mit Linux auf Azure" authors="timlt" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timlt" />

# Verwenden von SSH mit Linux auf Azure

In diesem Thema werden die Schritte zur Erzeugung von mit Azure kompatiblen SSH-Schlüsseln beschrieben.

Die aktuelle Version des Azure-Verwaltungsportals akzeptiert nur öffentliche SSH-Schlüssel, die in einem X509-Zertifikat enthalten sind. Gehen Sie wie nachstehend beschrieben vor, um SSH-Schlüssel mit Azure zu erzeugen und zu verwenden.

## Erzeugung von Azure-kompatiblen Schlüsseln in Linux

1.  Installieren Sie bei Bedarf das Hilfsprogramm `openssl`:

    **CentOS / Oracle Linux**

        # sudo yum install openssl

    **Ubuntu**

        # sudo apt-get install openssl

    **SLES & openSUSE**

        # sudo zypper install openssl

2.  Verwenden Sie `openssl`, um ein X.509-Zertifikat mit einem 2048-bit-RSA-Schlüsselpaar zu generieren. Beantworten Sie die Fragen, die `openssl` Ihnen stellt (oder lassen Sie die Felder leer). Der Inhalt dieser Felder wird nicht von der Plattform verwendet.

        # openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

3.  Ändern Sie die Berechtigungen auf dem privaten Schlüssel, um diesen zu sichern.

        # chmod 600 myPrivateKey.key

4.  Laden Sie das `myCert.pem` hoch, während Sie den virtuellen Linux-Computer erstellen. Der Bereitstellungsvorgang installiert automatisch den öffentlichen Schlüssel in diesem Zertifikat in der Datei `authorized_keys` für den angegebenen Benutzer im virtuellen Computer.

5.  Wenn Sie die API direkt ohne das Verwaltungsportal verwenden möchten, wandeln Sie die Datei `myCert.pem` in `myCert.cer` (DER-codiertes X509-Zertifikat) um. Verwenden Sie dafür den folgenden Befehl:

        # openssl  x509 -outform der -in myCert.pem -out myCert.cer

## Erzeugen eines Schlüssels aus einem vorhandenen OpenSSH-kompatiblen Schlüssel

Im vorherigen Beispiel wurde die Erstellung eines neuen Schlüssels zur Verwendung mit Azure beschrieben. In manchen Fällen verfügen Sie möglicherweise bereits über ein vorhandenes OpenSSH-kompatibles Schlüsselpaar (öffentlich und privat) und möchten diese Schlüssel in Azure verwenden.

Private OpenSSH-Schlüssel können direkt vom Hilfsprogramm `openssl` gelesen werden. Mit dem folgenden Befehl wird aus dem vorhandenen privaten SSH-Schlüssel (im Beispiel unten id\_rsa) der öffentliche Schlüssel `.pem` erstellt, der für Microsoft Azure erforderlich ist:

    # openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem

Die Datei **myCert.pem** ist der öffentliche Schlüssel, der anschließend verwendet werden kann, um einen virtuellen Linux-Computer auf Azure bereitzustellen. Während der Bereitstellung wird die Datei `.pem` in einen `openssh`-kompatiblen öffentlichen Schlüssel übersetzt und in `~/.ssh/authorized_keys` platziert.

## Verbindung mit einem virtuellen Azure-Computer über Linux

1.  In einigen Fällen wird der SSH-Endpunkt für einen virtuellen Linux-Computer möglicherweise für einen Port konfiguriert, der nicht dem Standardport 22 entspricht. Sie finden die richtige Portnummer auf dem Dashboard für den virtuellen Computer im Verwaltungsportal (unter „SSH-Details“).

2.  Stellen Sie die Verbindung mit dem virtuellen Linux-Computer mithilfe von `ssh` her. Beim ersten Anmelden werden Sie aufgefordert, den Fingerabdruck des öffentlichen Schlüssels des Hosts zu übernehmen.

        # ssh -i  myPrivateKey.key -p <port> username@servicename.cloudapp.net

3.  (Optional) Sie möchten möglicherweise `myPrivateKey.key` zu `~/.ssh/id_rsa` kopieren, sodass Ihr OpenSSH-Client dies automatisch auswählen kann, ohne dass die Option `-i` verwendet werden muss.

## OpenSSL unter Windows aufrufen

### Verwenden von msysgit

1.  Laden und installieren Sie msysgit von folgendem Speicherort: [][]<http://msysgit.github.com/></a>
2.  Führen Sie im installierten Verzeichnis `msys` aus (Beispiel: c:\\msysgit\\msys.exe)
3.  Nehmen Sie eine Änderung zum Verzeichnis `bin` vor, indem Sie `cd bin` eingeben.

### Verwenden von GitHub für Windows

1.  Laden und installieren Sie GitHub für Windows von folgendem Speicherort: [][1]<http://windows.github.com/></a>
2.  Führen Sie die Git-Shell aus: Startmenü \> Alle Programme \> GitHub, Inc

### Verwenden von cygwin

1.  Laden und installieren Sie Cygwin von folgendem Speicherort: [][2]<http://cygwin.com/></a>
2.  Stellen Sie sicher, dass das OpenSSL-Paket und alle seine Abhängigkeiten installiert sind.
3.  Führen Sie `cygwin` aus.

## Erstellen eines privaten Schlüssels in Windows

1.  Befolgen Sie eine der obigen Anweisungen, um `openssl.exe` auszuführen.
2.  Geben Sie den folgenden Befehl ein:

        # openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

3.  Ihr Bildschirm sollte nun folgendermaßen aussehen:

    ![linuxwelcomegit][linuxwelcomegit]

4.  Beantworten Sie die Fragen, die Ihnen gestellt werden.
5.  Es sind zwei Dateien erstellt worden: `myPrivateKey.key` und `myCert.pem`.
6.  Wenn Sie die API direkt ohne das Verwaltungsportal verwenden möchten, wandeln Sie die Datei `myCert.pem` in `myCert.cer` (DER-codiertes X509-Zertifikat) um. Verwenden Sie dafür den folgenden Befehl:

        # openssl.exe  x509 -outform der -in myCert.pem -out myCert.cer

## Erstellen eines PPK für Putty

1.  Laden und installieren Sie Puttygen von folgendem Speicherort: [][3]<http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html></a>

2.  Puttygen ist möglicherweise nicht in der Lage, den zuvor (`myPrivateKey.key`) erstellten privaten Schlüssel zu lesen. Führen Sie den folgenden Befehl aus, um ihn in einen privaten RSA-Schlüssel zu übersetzen, den Puttygen verstehen kann:

        # openssl rsa -in ./myPrivateKey.key -out myPrivateKey_rsa
        # chmod 600 ./myPrivateKey_rsa

    Der obige Befehl sollte den neuen privaten Schlüssel „myPrivateKey\_rsa“ generieren.

3.  Führen Sie `puttygen.exe` aus.

4.  Klicken Sie auf das Menü: Datei \> Privaten Schlüssel laden

5.  Suchen Sie nach dem privaten Schlüssel, den wir oben als `myPrivateKey_rsa` benannt haben. Ändern Sie den Dateienfilter, so dass er **Alle Dateien (\*.\*)** anzeigt.

6.  Klicken Sie auf **Öffnen**. Sie erhalten eine Aufforderung, die ungefähr folgendermaßen aussieht:

    ![linuxgoodforeignkey][linuxgoodforeignkey]

7.  Klicken Sie auf **OK**.

8.  Klicken Sie auf **Privaten Schlüssel speichern**. Diese Option ist im Screenshot unten hervorgehoben:

    ![linuxputtyprivatekey][linuxputtyprivatekey]

9.  Speichern Sie die Datei als PPK.

## Verwenden von Putty für eine Verbindung mit einem Linux-Computer

1.  Laden und installieren Sie putty von folgendem Speicherort: [][3]<http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html></a>
2.  Führen Sie putty.exe aus.
3.  Geben Sie den Hostnamen ein. Verwenden Sie dafür die IP aus dem Verwaltungsportal:

    ![linuxputtyconfig][linuxputtyconfig]

4.  Klicken Sie auf der Registerkarte "Verbindung" auf "SSH \> Auth", um Ihren Schlüssel zu wählen, und anschließend auf **Öffnen**. Das auszufüllende Feld finden Sie auf dem Screenshot unten:

    ![linuxputtyprivatekey][4]

5.  Klicken Sie auf **Öffnen**, um die Verbindung mit Ihrem virtuellen Computer herzustellen.

  []: http://msysgit.github.com/
  [1]: http://windows.github.com/
  [2]: http://cygwin.com/
  [linuxwelcomegit]: ./media/virtual-machines-linux-use-ssh-key/linuxwelcomegit.png
  [3]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
  [linuxgoodforeignkey]: ./media/virtual-machines-linux-use-ssh-key/linuxgoodforeignkey.png
  [linuxputtyprivatekey]: ./media/virtual-machines-linux-use-ssh-key/linuxputtygenprivatekey.png
  [linuxputtyconfig]: ./media/virtual-machines-linux-use-ssh-key/linuxputtyconfig.png
  [4]: ./media/virtual-machines-linux-use-ssh-key/linuxputtyprivatekey.png
