<properties 
	pageTitle="Verwenden von SSH unter Windows zum Herstellen einer Verbindung mit virtuellen Linux-Computern | Microsoft Azure" 
description="Erfahren Sie, wie Sie SSH-Schlüssel auf einem Windows-Computer erstellen und verwenden, um Verbindungen mit virtuellen Linux-Computern in Azure herzustellen." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="squillace" 
	manager="timlt" 
	editor=""
	tags="azure-service-management,azure-resource-manager" />

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/04/2016" 
	ms.author="rasquill"/>

#Verwenden von SSH mit Windows in Azure

> [AZURE.SELECTOR]
- [Windows](../articles/virtual-machines/virtual-machines-windows-use-ssh-key.md)
- [Linux/Mac](../articles/virtual-machines/virtual-machines-linux-use-ssh-key.md)

In diesem Thema wird beschrieben, wie Sie **ssh-rsa** und öffentliche und private Schlüsseldateien im **PEM**-Format unter Windows erstellen und verwenden, um damit mit dem Befehl **ssh** Verbindungen mit Ihren Linux-VMs in Azure herzustellen. Wenn Sie bereits **PEM**-Dateien erstellt haben, können diese verwenden, um virtuelle Linux-Computer zu erstellen, zu denen Sie mit **ssh** eine Verbindung herstellen können. Viele weitere Befehle verwenden das **SSH**-Protokoll und Schlüsseldateien für das sichere Ausführen von Aufgaben, insbesondere **scp** oder [Secure Copy](https://en.wikipedia.org/wiki/Secure_copy), das Dateien sicher zwischen Computern kopieren kann, die **SSH**-Verbindungen unterstützen.


## Erforderliche SSH- und Schlüsselerstellungsprogramme

**SSH** &#8212; oder [Secure Shell](https://en.wikipedia.org/wiki/Secure_Shell) &#8212; ist ein Protokoll für verschlüsselte Verbindungen, das die sichere Anmeldung über ungesicherte Verbindungen ermöglicht. Es ist das Standardprotokoll für die Verbindung mit virtuellen Linux-Computern, die in Azure gehostet werden, sofern Sie nicht andere Verbindungsmechanismen für Ihre virtuellen Linux-Computer konfigurieren haben. Windows-Benutzer können mit **ssh**-Clientimplementierung auch Verbindungen mit Linux-VMs in Azure herstellen und diese verwalten, Windows-Computer bieten i. d. R. aber standardmäßig keinen **ssh**-Client, sodass Sie einen auswählen müssen.

Folgende häufig verwendete Clients können Sie installieren:

- [puTTY und puTTYgen]((http://www.chiark.greenend.org.uk/~sgtatham/putty/)
- [MobaXterm](http://mobaxterm.mobatek.net/)
- [Cygwin](https://cygwin.com/)
- [Git für Windows](https://git-for-windows.github.io/) bietet schon im Lieferumfang die Umgebung und Tools

Wenn Sie über etwas mehr Erfahrungen verfügen, können Sie auch den [neuen Port des **OpenSSH**-Toolsets für Windows](http://blogs.msdn.com/b/powershell/archive/2015/10/19/openssh-for-windows-update.aspx) testen. Beachten Sie jedoch, dass sich dieser Code zurzeit noch in der Entwicklung befindet, deshalb sollten Sie die Codebasis überprüfen, bevor Sie sie für Produktionssysteme verwenden.

> [AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## Erforderliche Schlüsseldateien

Ein grundlegendes SSH-Setup für Azure umfasst ein öffentliches und privates **ssh-rsa**-Schlüsselpaar mit 2048 Bit (**ssh-keygen** speichert diese Dateien standardmäßig unter **~/.ssh/id\_rsa** und **~/.ssh/id-rsa.pub**, sofern Sie die Standardeinstellungen nicht ändern) sowie eine `.pem`-Datei, die zur Verwendung mit dem klassischen Bereitstellungsmodell des klassischen Portals aus der privaten **id\_rsa**-Schlüsseldatei generiert wird.

Im Folgenden sind die Dateitypen für die unterschiedlichen Bereitstellungsszenarien aufgeführt:

1. **SSH-RSA**-Schlüssel sind unabhängig vom Bereitstellungsmodell für alle Bereitstellungen mithilfe des [Azure-Portals](https://portal.azure.com) erforderlich.
2. PEM-Dateien sind erforderlich, um VMs mithilfe des [klassischen Portals](https://manage.windowsazure.com) zu erstellen. PEM-Dateien werden auch in klassischen Bereitstellungen mit der [Azure-Befehlszeilenschnittstelle](../xplat-cli-install.md) unterstützt.

> [AZURE.NOTE] Wenn Sie vorhaben, mit dem klassischen Bereitstellungsmodell bereitgestellte Dienste zu verwalten, können Sie auch eine **CER**-Datei erstellen. Dies erfordert jedoch weder die Verwendung von **ssh** noch das Herstellen einer Verbindung mit Linux-VMs und ist daher nicht Gegenstand dieses Artikels. Geben Sie zum Erstellen dieser Dateien unter Linux oder auf einem Mac Folgendes ein:

## Abrufen von ssh-keygen und openssl unter Windows ##

[Dieser Abschnitt](#What-SSH-and-key-creation-programs-do-you-need) oben listet verschiedene Dienstprogramme auf, die `ssh-keygen` und `openssl` für Windows enthalten. Nachstehend finden Sie einige Beispiele:

### Verwenden von Msysgit ###

1.	Laden Sie msysgit von folgendem Speicherort herunter, und installieren Sie es: [http://msysgit.github.com/](http://msysgit.github.com/)
2.	Führen Sie `msys` aus dem installierten Verzeichnis aus (Beispiel: c:\\msysgit\\msys.exe).
3.	Wechseln Sie zum Verzeichnis `bin`, indem Sie `cd bin` eingeben.


### Verwenden von GitHub für Windows ###

1.	Laden und installieren Sie GitHub für Windows von folgendem Speicherort: [http://windows.github.com/](http://windows.github.com/)
2.	Führen Sie die Git-Shell aus: Startmenü > Alle Programme > GitHub, Inc

> [AZURE.NOTE] Beim Ausführen der obigen `openssl`-Befehle tritt möglicherweise der folgende Fehler auf:

        Unable to load config info from /usr/local/ssl/openssl.cnf

Die einfachste Lösung ist, die Umgebungsvariable `OPENSSL_CONF` festzulegen. Der Prozess zum Festlegen dieser Variable variieren abhängig von der Shell, die Sie in Github konfiguriert haben:

**Powershell:**

        $Env:OPENSSL_CONF="$Env:GITHUB_GIT\ssl\openssl.cnf"

**CMD:**

        set OPENSSL_CONF=%GITHUB_GIT%\ssl\openssl.cnf

**Git Bash:**

        export OPENSSL_CONF=$GITHUB_GIT/ssl/openssl.cnf
	

###Verwenden von Cygwin###

1.	Laden und installieren Sie Cygwin von folgendem Speicherort: [http://cygwin.com/](http://cygwin.com/)
2.	Stellen Sie sicher, dass das OpenSSL-Paket und alle seine Abhängigkeiten installiert sind.
3.	Führen Sie `cygwin` aus.

## Erstellen eines privaten Schlüssels##

1.	Befolgen Sie eine der obigen Anweisungen, um `openssl.exe` auszuführen.
2.	Geben Sie den folgenden Befehl ein:

		# openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

3.	Ihr Bildschirm sollte nun folgendermaßen aussehen:

	![linuxwelcomegit](./media/virtual-machines-linux-use-ssh-key/linuxwelcomegit.png)

4.	Beantworten Sie die Fragen, die Ihnen gestellt werden.
5.	Es wären zwei Dateien erstellt worden: `myPrivateKey.key` und `myCert.pem`.
6.	Wenn Sie die API direkt ohne das Verwaltungsportal verwenden möchten, konvertieren Sie die Datei `myCert.pem` in `myCert.cer` (DER-codiertes X509-Zertifikat). Verwenden Sie dafür den folgenden Befehl:

		# openssl.exe  x509 -outform der -in myCert.pem -out myCert.cer

## Erstellen eines PPK für Putty ##

1. Download and install Puttygen from the following location: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

2. Puttygen ist möglicherweise nicht in der Lage, den zuvor erstellten privaten Schlüssel (`myPrivateKey.key`) zu lesen. Führen Sie den folgenden Befehl aus, um ihn in einen privaten RSA-Schlüssel zu übersetzen, den Puttygen verstehen kann:

		# openssl rsa -in ./myPrivateKey.key -out myPrivateKey_rsa
		# chmod 600 ./myPrivateKey_rsa

	Der obige Befehl sollte den neuen privaten Schlüssel "myPrivateKey\_rsa" generieren.

3. Führen Sie `puttygen.exe` aus.

4. Wählen Sie die Befehlsfolge "Datei > Privaten Schlüssel laden".

5. Suchen Sie den privaten Schlüssel, den wir oben als `myPrivateKey_rsa` benannt haben. Ändern Sie den Dateienfilter, so dass er **Alle Dateien (*.*)** anzeigt.

6. Klicken Sie auf **Öffnen**. Sie erhalten eine Aufforderung, die ungefähr folgendermaßen aussieht:

	![linuxgoodforeignkey](./media/virtual-machines-linux-use-ssh-key/linuxgoodforeignkey.png)

7. Klicken Sie auf **OK**.

8. Klicken Sie auf **Privaten Schlüssel speichern**. Diese Option ist im Screenshot unten hervorgehoben:

	![linuxputtyprivatekey](./media/virtual-machines-linux-use-ssh-key/linuxputtygenprivatekey.png)

9. Speichern Sie die Datei als PPK.


## Verwenden von Putty für eine Verbindung mit einem Linux-Computer ##

1.	Laden und installieren Sie Putty von folgendem Speicherort: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2.	Führen Sie putty.exe aus.
3.	Geben Sie den Hostnamen ein. Verwenden Sie dafür die IP aus dem Verwaltungsportal:

	![linuxputtyconfig](./media/virtual-machines-linux-use-ssh-key/linuxputtyconfig.png)

4.	Klicken Sie auf der Registerkarte "Verbindung" auf "SSH > Auth", um Ihren Schlüssel zu wählen, und anschließend auf **Öffnen**. Das auszufüllende Feld finden Sie auf dem Screenshot unten:

	![linuxputtyprivatekey](./media/virtual-machines-linux-use-ssh-key/linuxputtyprivatekey.png)

5.	Klicken Sie auf **Öffnen**, um die Verbindung mit Ihrem virtuellen Computer herzustellen.
 

<!---HONumber=AcomDC_0204_2016-->