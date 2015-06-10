<properties
	pageTitle="Installieren von MySQL in Azure"
	description="Erfahren Sie, wie Sie den MySQL-Stack auf einem virtuellen Linux-Computer in Azure installieren. Sie können die Installation auf Ubuntu oder CentOS vornehmen."
	services="virtual-machines"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/12/2015"
	ms.author="mingzhan"/>


#Installieren von MySQL in Azure


Bei diesem Thema wird vorausgesetzt, dass der Leser bereits über ein Azure-Konto verfügt. Andernfalls wird empfohlen, sich anmelden bei [Azure](http://azure.microsoft.com).



##Erstellen eines VM-Images in Microsoft Azure.
Hier erstellen wir eine neue VM vom Microsoft Azure-Verwaltungsportal.
###Generieren eines "SSH-Authentifizierungsschlüssels"
Wir benötigen den SSH-Schlüssel, um auf das Azure-Portal zuzugreifen.


- Laden und installieren Sie puttygen von [hier](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html). 
- Führen Sie "putty.exe" aus.
- Klicken Sie auf die Schaltfläche "Generieren", um den Schlüssel zu generieren.


 ![image](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p01.png)
 
- Nach Generierung des Schlüssel wird dieser wie unten angezeigt. 
 
 ![image](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p02.png)

- Kopieren Sie den öffentlichen Schlüssel und speichern Sie ihn in einer Datei namens "publicKey.key". Klicken Sie nicht auf die Schaltfläche "Öffentlichen Schlüssel speichern", weil sich das Dateiformat des gespeicherten öffentlichen Schlüssels vom gewünschten öffentlichen Schlüssel unterscheidet.
- Klicken Sie auf die Schaltfläche "Privaten Schlüssel speichern", und speichern Sie den Schlüssel dann unter "privateKey.ppk". 

###Anmeldung im Azure-Portal

Wechseln Sie zu https://portal.azure.com/, und melden Sie sich.

###Erstellen eines virtuellen Linux-Computers

Klicken Sie auf "NEU" links unten, erstellen Sie ein Image, indem Sie den Anweisungen folgen, und wählen Sie das Linux-Image gemäß Ihren Anforderungen aus. Hier wähle ich Ubuntu 14.04 als Beispiel.

  ![image](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p03.png)

###Einrichten von HOSTNAME

Für "HOSTNAME": Dies ist die URL, die Sie zum Zugriff auf Ihren virtuellen Computer verwenden können. Sie müssen nur den DNS-Namen angeben, z. B. "mysqlnode1", und Azure generiert die URL als "mysqlnode1.cloudapp.net"; "SSH-AUTHENTIFIZIERUNGSSCHLÜSSEL": Dies ist der öffentliche Schlüssel, der von Puttygen generiert wird. Sie müssen ihn aus dem Inhalt der Datei "publicKey.key" kopieren.

  ![image](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p04.png)
  

##Öffnen Sie den MySQL-Standardport
Endpunkte in Microsoft Azure bestehen aus einem Protokoll, zusammen mit einem öffentlichen und privaten Port. Der private Port ist der Port, die der Dienst auf dem lokalen Computer überwacht. Der öffentliche Port ist der Port, an dem der Dienst extern lauscht. Port 3306 ist die Standard-Portnummer, an der MySQL lauscht. Klicken Sie auf "DURCHSUCHEN" ⇒ "VIRTUELLER COMPUTER", und klicken Sie dann auf das Image, das Sie erstellt haben.
 
   ![image](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p05.png)


##Herstellen einer Verbindung mit dem Image, das Sie erstellt haben
Sie können ein beliebiges SSH-Tool für das Herstellen einer Verbindung mit Ihrem virtuellen Computer auswählen. In diesem Beispiel wird Putty verwendet.
 

- Zunächst laden Sie Putty herunter. Dies ist die Download-URL von Putty .
- Klicken Sie nach dem Download von Putty auf die ausführbare Datei "PUTTY.EXE". Wie folgt festlegen.


     Der "Hostname“ (bzw. die IP-Adresse) ist beim Erstellen eines Image die URL als "DNS-NAME".
     
     Als "Port" kann 22 gewählt werden. Dies ist der Standardport des SSH-Dienstes.

   ![image](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p06.png)
 
- Bevor Sie "Öffnen" auswählen, klicken Sie auf die Registerkarte "Verbindung > SSH > Auth", um Ihre von Puttygen generierte Datei zu durchsuchen, die den privaten Schlüssel enthält. Das auszufüllende Feld finden sie auf dem Screenshot unten.

   ![image](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p07.png)
 
- Klicken Sie dann auf "Öffnen". Es erscheint möglicherweise ein Meldungsfeld mit der Information, dass der Computer, mit dem Sie eine Verbindung herstellen, möglicherweise nicht der richtige ist. Wenn Sie den DNS-Namen und die Portnummer richtig konfiguriert haben, klicken Sie auf "Ja".
  
 ![image](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p08.png)

- Sie sehen nun Folgendes. 
 
 ![image](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p09.png)


##Installieren von MySQL auf dem virtuellen Computer
Drei Installationsmethoden werden für MySQL unterstützt: binäre Pakete, Rpm-Pakete und Quellpakete. Aus Leistungsgründen verwenden wir ein Rpm-Paket für MySQL 5.6 als Beispiel für diesen Artikel. Die MySQL5.6-Leistung ist erheblich besser als die von MySQL5.5. Weitere Informationen finden Sie [hier](http://www.mysqlperformanceblog.com/2013/02/18/is-mysql-5-6-slower-than-mysql-5-5/).


###Installieren Sie MySQL5.6 auf Ubuntu oder Debian
Wir verwenden Ubuntu 14.04 LTS als Beispiel für diesen Artikel.

- Schritt 1: Installieren von MySQL Server 5.6

    Installieren von MySQL-Server 5.6 mit dem apt-get-Befehl

              # azureuser@mysqlnode1:~$ sudo apt-get update
              # azureuser@mysqlnode1:~$ sudo apt-get -y install mysql-server-5.6

    Während der Installation sehen Sie ein Popup-Dialogfeld, in dem Sie um Festlegung des MySQL Stammkennworts gebeten werden. Sie müssen das neue MySQL-Benutzer-Stammkennwort angeben. Unten sehen Sie den Screenshot.

 ![image](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p10.png)

    Confirm the password again when it is asked.

 ![image](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p11.png)
 
- Schritt 2: Anmeldung in MySQL-Server

    Der MySQL-Dienst wird automatisch gestartet, sobald die MySQL Server-Installation abgeschlossen ist. Sie können sich mit dem Benutzerstamm in MySQL-Server anmelden. Um sich in MySQL-Server anzumelden, verwenden Sie untenstehenden Befehl. Sie werden nach dem Stammkennwort für MySQL gefragt, das Sie während der Installation von MySQL-Server eingerichtet haben.

             # azureuser@mysqlnode1:~$ mysql -uroot -p

- Schritt 3: Überprüfen Sie den MySQL-Dienst auf dem virtuellen Computer
    
    Stellen Sie nach der Anmeldung sicher, dass der MySQL-Dienst ausgeführt wird. Sie können folgende Befehle zum Starten/Neustarten des Dienstes benutzen.

    (a) Um den Status vom MySQL-Dienst zu erhalten.

             #sudo service mysql status

    (b) Um den MySQL-Dienst zu starten.

             #sudo service mysql start

    (c) Um den MySQL-Dienst anzuhalten.

             #sudo service mysql stop

    (d) Um den MySQL-Dienst neu zu starten.

             #sudo service mysql restart


###Installieren von MySQL auf der Redhat-Betriebssystemfamilie oder Oracle Linux
- Schritt 1: Hinzufügen des MySQL-Yum-Repositorys. Um Stammberechtigungen zu erhalten, führen Sie diesen Befehl aus: 

            #sudo su -
            #[root@azureuser ~]# wget http://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm 
            #[root@azureuser ~]# yum localinstall -y mysql-community-release-el6-5.noarch.rpm 

- Schritt 2: Auswählen einer Versionsreihe
 
            #[root@azureuser ~]# vim /etc/yum.repos.d/mysql-community.repo

    Dies ist ein typischer Eintrag für ein Subrepository der Versionsreihe in der Datei:

        # *Enable to use MySQL 5.6*

        [mysql56-community]
        name=MySQL 5.6 Community Server

        baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/6/$basearch/

        enabled=1

        gpgcheck=1

        gpgkey=file:/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql

- Schritt 3: Installieren von MySQL mit Yum. Installieren Sie MySQL mit folgendem Befehl:

           #[root@azureuser ~]#yum install mysql-community-server 

    Das Paket für den MySQL-Server sowie andere erforderliche Pakete werden installiert.

- Schritt 4: Überprüfen Sie den Ausführungsstatus von MySQL

    Sie können den Status des MySQL-Servers mit dem folgenden Befehl überprüfen:
   
           #[root@azureuser ~]#service mysqld status

    Sie können überprüfen, ob der Standardport des MySQL-Servers ausgeführt wird:

           #[root@azureuser ~]#netstat  –tunlp|grep 3306

- Schritt 5: Starten und Beenden des MySQL-Servers

    Starten Sie den MySQL-Server mit dem folgenden Befehl:

           #[root@azureuser ~]#service mysqld start

    Beenden Sie den MySQL-Server mit dem folgenden Befehl:

           #[root@azureuser ~]#service mysqld stop

    Führen Sie den folgenden Befehl aus, um das System so einzurichten, dass beim Start MySQL gestartet wird:

           #[root@azureuser ~]#chkconfig mysqld on


###Installieren von MySQL in Suse Linux

- Schritt 1: Installieren von MySQL Server

    Führen Sie zum Erweitern der Berechtigungen folgenden Befehl aus:

           #sudo su -

    Installieren Sie MySQL, indem Sie den folgenden Befehl ausführen:

           #mysql-test:~ # zypper update

           #mysql-test:~ # zypper install mysql-server mysql-devel mysql

- Schritt 2: Überprüfen Sie den Ausführungsstatus von MySQL

    Sie können den Status des MySQL-Servers mit dem folgenden Befehl überprüfen:

           #mysql-test:~ # rcmysql status

    Sie können überprüfen, ob der Standardport des MySQL-Servers ausgeführt wird;

           #mysql-test:~ # netstat  –tunlp|grep 3306

- Schritt 3: Starten und Beenden des MySQL-Servers

    Starten Sie den MySQL-Server mit dem folgenden Befehl:

           #mysql-test:~ # rcmysql start

    Beenden Sie den MySQL-Server mit dem folgenden Befehl:

           #mysql-test:~ # rcmysql stop

    Führen Sie den folgenden Befehl aus, um das System so einzurichten, dass beim Start MySQL gestartet wird:

           #mysql-test:~ # insserv mysql

<!---HONumber=58-->