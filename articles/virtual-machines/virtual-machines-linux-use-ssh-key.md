<properties 
	pageTitle="Verwenden von SSH zum Herstellen einer Verbindung zu virtuellen Linux-Computern in Azure" 
	description="Erfahren Sie, wie Sie SSH-Schlüssel mit einem virtuellen Linux-Computer auf Azure generieren und verwenden." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="szarkos" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/15/2015" 
	ms.author="szark"/>

#Gewusst wie: Verwenden von SSH mit Linux auf Azure

Die aktuelle Version des Azure-Verwaltungsportals akzeptiert nur öffentliche SSH-Schlüssel, die in einem X509-Zertifikat enthalten sind. Gehen Sie wie nachstehend beschrieben vor, um SSH-Schlüssel mit Azure zu erzeugen und zu verwenden.

## Erzeugen von Azure-kompatiblen Schlüsseln in Linux ##

1. Installieren Sie bei Bedarf das Hilfsprogramm `openssl`:

	**CentOS/Oracle Linux**

		# sudo yum install openssl

	**Ubuntu**

		# sudo apt-get install openssl

	**SLES und openSUSE**

		# sudo zypper install openssl


2. Verwenden Sie `openssl`, um ein X.509-Zertifikat mit einem 2048-Bit-RSA-Schlüsselpaar zu generieren. Beantworten Sie die Fragen, die `openssl` Ihnen stellt (oder lassen Sie die Felder leer). Der Inhalt dieser Felder wird nicht von der Plattform verwendet.

		# openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

3.	Ändern Sie die Berechtigungen für den privaten Schlüssel, um diesen zu sichern.

		# chmod 600 myPrivateKey.key

4.	Laden Sie die Datei `myCert.pem` hoch, während Sie den virtuellen Linux-Computer erstellen. Der Bereitstellungsvorgang installiert automatisch den öffentlichen Schlüssel in diesem Zertifikat in der Datei `authorized_keys` für den angegebenen Benutzer im virtuellen Computer.

5.	Wenn Sie die API direkt ohne das Verwaltungsportal verwenden möchten, konvertieren Sie die Datei `myCert.pem` in `myCert.cer` (DER-codiertes X509-Zertifikat). Verwenden Sie dafür den folgenden Befehl:

		# openssl  x509 -outform der -in myCert.pem -out myCert.cer


## Erzeugen eines Schlüssels aus einem vorhandenen OpenSSH-kompatiblen Schlüssel
Im vorherigen Beispiel wurde die Erstellung eines neuen Schlüssels zur Verwendung mit Azure beschrieben. In manchen Fällen verfügen Sie möglicherweise bereits über ein vorhandenes OpenSSH-kompatibles Schlüsselpaar (öffentlich und privat) und möchten dieselben Schlüssel in Azure verwenden.

Private OpenSSH-Schlüssel können direkt vom Hilfsprogramm `openssl` gelesen werden. Mit dem folgenden Befehl wird aus dem vorhandenen privaten SSH-Schlüssel (im Beispiel unten id\_rsa) der öffentliche Schlüssel `.pem` erstellt, der für Azure erforderlich ist:

	# openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem

Die Datei **myCert.pem** ist der öffentliche Schlüssel, der anschließend verwendet werden kann, um einen virtuellen Linux-Computer in Azure bereitzustellen. Während der Bereitstellung wird die Datei `.pem` in einen mit `openssh` kompatiblen öffentlichen Schlüssel übersetzt und in `~/.ssh/authorized_keys` platziert.


## Herstellen einer Verbindung mit einem virtuellen Azure-Computer über Linux

1. In manchen Fällen kann es sein, dass der SSH-Endpunkt eines virtuellen Linux-Computers für einen anderen Port als Port 22 konfiguriert ist. Sie finden die korrekte Portnummer auf dem Dashboard des virtuellen Computers im Verwaltungsportal (unter "SSH-Details").

2.	Stellen Sie die Verbindung mit dem virtuellen Linux-Computer mithilfe von `ssh` her. Beim ersten Anmelden werden Sie aufgefordert, den Fingerabdruck des öffentlichen Schlüssels des Hosts zu übernehmen.

		# ssh -i  myPrivateKey.key -p <port> username@servicename.cloudapp.net

3.	(Optional) Sie möchten möglicherweise `myPrivateKey.key` nach `~/.ssh/id_rsa` kopieren, sodass Ihr OpenSSH-Client dies automatisch auswählen kann, ohne dass die Option `-i` verwendet werden muss.

## Aufrufen von OpenSSL unter Windows ##

Es gibt eine Reihe von Dienstprogrammen, die `openssl`-Client für Windows enthalten. Nachstehend finden Sie einige Beispiele:

### Verwenden von Msysgit ###

1.	Laden Sie msysgit von folgendem Speicherort herunter, und installieren Sie es: [http://msysgit.github.com/](http://msysgit.github.com/)
2.	Führen Sie `msys` aus dem installierten Verzeichnis aus (Beispiel: c:\\msysgit\\msys.exe).
3.	Wechseln Sie zum Verzeichnis `bin`, indem Sie `cd bin` eingeben.


### Verwenden von GitHub für Windows ###

1.	Laden und installieren Sie GitHub für Windows von folgendem Speicherort: [http://windows.github.com/](http://windows.github.com/)
2.	Führen Sie die Git-Shell aus: Startmenü > Alle Programme > GitHub, Inc

	**Hinweis:** Beim Ausführen der `openssl`-Befehle tritt möglicherweise der folgende Fehler auf:

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


## Erstellen eines privaten Schlüssels in Windows ##

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

1. Download and install Puttygen from the following location: [http://www.chiark.greenend.org.uk/\~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

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

1.	Laden und installieren Sie Putty von folgendem Speicherort: [http://www.chiark.greenend.org.uk/\~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2.	Führen Sie putty.exe aus.
3.	Geben Sie den Hostnamen ein. Verwenden Sie dafür die IP aus dem Verwaltungsportal:

	![linuxputtyconfig](./media/virtual-machines-linux-use-ssh-key/linuxputtyconfig.png)

4.	Klicken Sie auf der Registerkarte "Verbindung" auf "SSH > Auth", um Ihren Schlüssel zu wählen, und anschließend auf **Öffnen**. Das auszufüllende Feld finden Sie auf dem Screenshot unten:

	![linuxputtyprivatekey](./media/virtual-machines-linux-use-ssh-key/linuxputtyprivatekey.png)

5.	Klicken Sie auf **Öffnen**, um die Verbindung mit Ihrem virtuellen Computer herzustellen.
 

<!---HONumber=August15_HO6-->