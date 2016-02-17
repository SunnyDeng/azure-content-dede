<properties
	pageTitle="Einrichten von Apache Tomcat auf einem virtuellen Linux-Computer | Microsoft Azure"
	description="Erfahren Sie, wie Sie Apache Tomcat7 unter Verwendung eines virtuellen Azure-Computers (VM) mit Linux einrichten können."
	services="virtual-machines"
	documentationCenter=""
	authors="NingKuang"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/15/2015"
	ms.author="ningk"/>

#Einrichten von Tomcat7 auf einem virtuellen Linux-Computer mit Microsoft Azure

Apache Tomcat (oder einfach Tomcat, früher auch Jakarta Tomcat) ist ein Open-Source-Webserver und Servlet-Container und wurde von der Apache Software Foundation (ASF) entwickelt. Tomcat implementiert das Java-Servlet und die JavaServer Pages (JSP)-Spezifikationen von Sun Microsystems und bietet eine reine Java-HTTP-Webserverumgebung zum Ausführen von Java-Code. In der einfachsten Konfiguration wird Tomcat in einem einzelnen Betriebssystem-Prozess ausgeführt. Dieser Prozess führt eine Java Virtual Machine (JVM) aus. Jede HTTP-Anforderung von einem Browser an Tomcat wird als separater Thread im Tomcat-Prozess verarbeitet.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Ressourcen-Manager-Modell.


In diesem Handbuch installieren Sie tomcat7 in einem Linux-Image und stellen dieses unter Microsoft Azure bereit.

Sie erhalten Informationen zu folgenden Themen:

-	Erstellen eines virtuellen Computers in Azure.
-	Gewusst wie: Vorbereiten des virtuellen Computers für tomcat7.
-	Gewusst wie: Installieren von tomcat7.

Es wird vorausgesetzt, dass der Leser bereits über ein Azure-Abonnement verfügt. Falls dies nicht der Fall ist, können Sie sich für eine kostenlose Testversion unter [http://azure.microsoft.com](https://azure.microsoft.com/) registrieren. Wenn Sie über ein MSDN-Abonnement verfügen, finden Sie weitere Informationen unter [Microsoft Azure-Sonderpreis: MSDN-, MPN- und Bizspark-Vorteile](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/?c=14-39). Weitere Informationen zu Azure finden Sie unter [Was ist Azure?](https://azure.microsoft.com/overview/what-is-azure/)

In diesem Artikel wird davon ausgegangen, dass Sie über allgemeine Kenntnisse zu tomcat und Linux verfügen.

##Phase 1: Erstellen eines Image
In dieser Phase erstellen Sie einen virtuellen Computer mit einem Linux-Image in Azure.

###Schritt 1: Generieren eines SSH-Authentifizierungsschlüssels
SSH ist ein wichtiges Tool für Systemadministratoren. Die Konfiguration der Zugriffssicherheit auf Grundlage eines vom Menschen bestimmten Kennworts ist jedoch keine bewährte Methode. Böswillige Benutzer können über einen Benutzernamen und ein unsicheres Kennwort in Ihr System eindringen.

Die gute Nachricht ist, dass es eine Möglichkeit gibt, wie Sie den Remote-Zugriff geöffnet lassen können, ohne sich über Kennwörter sorgen zu müssen. Die Methode besteht aus Authentifizierung mit asymmetrischer Kryptografie. Der private Schlüssel des Benutzers ist der Schlüssel, der die Authentifizierung vornimmt. Sie können sogar das Konto des Benutzers sperren, um Kennwortauthentifizierung vollständig zu untersagen.

Ein weiterer Vorteil dieser Methode ist, dass Sie nicht verschiedene Kennwörter benötigen, um sich auf verschiedenen Servern anzumelden. Sie können sich auf allen Servern mit dem persönlichen privaten Schlüssel authentifizieren. Dies erspart Ihnen auch, sich mehrere Kennwörter merken zu müssen.

Mit dieser Methode ist es auch möglich, sich mit einer Kennwortanforderung anzumelden.

Führen Sie die folgenden Schritte aus, um den SSH-Authentifizierungsschlüssel zu generieren.

1.	Laden und installieren Sie puttygen von folgendem Speicherort: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2.	Führen Sie "PUTTYGEN.EXE" aus.
3.	Klicken Sie auf **Generieren**, um die Schlüssel zu generieren. Während des Vorgangs können Sie den Zufallsfaktor durch Bewegen der Maus über den leeren Bereich im Fenster erhöhen. ![][1]
4.	Nach dem Generierungsvorgang zeigt "Puttygen.exe" den generierten Schlüssel an. Beispiel: ![][2]
5.	Wählen Sie den öffentlichen Schlüssel in **Schlüssel** aus, kopieren Sie diesen, und speichern Sie ihn in einer Datei namens "publicKey.pem". Klicken Sie nicht auf **Öffentlichen Schlüssel speichern**, weil sich das Dateiformat des gespeicherten öffentlichen Schlüssels vom gewünschten öffentlichen Schlüssel unterscheidet.
6.	Klicken Sie auf **Privaten Schlüssel speichern**, und speichern Sie den Schlüssel dann in einer Datei namens "privateKey.ppk".

###Schritt 2: Erstellen des Images im Azure-Portal
Klicken Sie im [Azure-Portal](https://portal.azure.com/) auf der Taskleiste auf **Neu**, und erstellen Sie ein Image. Wählen Sie dabei das Linux-Image aus, das Ihren Anforderungen gerecht wird. Dieses Beispiel verwendet das Ubuntu 14.04-Image. ![][3]

Geben Sie unter **Hostname** den Namen für die URL ein, die Sie und die Internetclients für den Zugriff auf diesen virtuellen Computer verwenden. Definieren Sie den letzten Teil des DNS-Namens (z. B. "tomcatdemo"), und Azure generiert die URL als "tomcatdemo.cloudapp.net".

Kopieren Sie für die Angabe **SSH Authentication Key** den Schlüsselwert aus der Datei **publicKey.pem**, die den von puttygen generierten öffentlichen Schlüssel enthält. ![][4]

Konfigurieren Sie weitere Einstellungen wie erforderlich, und klicken Sie dann auf "Erstellen".

##Phase 2: Vorbereiten des virtuellen Computers für Tomcat7
In dieser Phase konfigurieren Sie einen Endpunkt für Tomcat-Datenverkehr und stellen dann eine Verbindung mit dem neuen virtuellen Computer her.
###Schritt 1: Öffnen des HTTP-Ports, um Webzugriff zu erlauben
Endpunkte in Azure bestehen aus einem Protokoll (TCP oder UDP) sowie aus einem öffentlichen und einem privaten Port. Der private Port ist der Port, an dem der Dienst auf dem virtuellen Computer lauscht. Der öffentliche Port ist der Port, an dem der Azure-Clouddienst extern auf internetbasierten Datenverkehr lauscht.

TCP-Port 8080 ist die Standardportnummer, an der Tomcat lauscht. Wenn Sie diesen Port mit einem Azure-Endpunkt öffnen, können Sie und andere Internetclients auf die Tomcat-Seiten zugreifen.

1.	Klicken Sie im Azure-Portal auf **Durchsuchen** -> **Virtueller Computer**, und klicken Sie dann auf den virtuellen Computer, den Sie erstellt haben. ![][5]
2.	Klicken Sie zum Hinzufügen eines Endpunkts zu einem virtuellen Computer auf das Feld **Endpunkte**. ![][6]
3.	Klicken Sie auf **Hinzufügen**.  
	1.	Geben Sie für **Endpunkt** einen Endpunktnamen ein, und geben Sie dann unter **Öffentlicher Port** den Port "80" ein.  

		Wenn Sie den Port auf 80 festlegen, müssen Sie die Portnummer nicht in die URL einschließen, die Ihnen den Zugriff auf Tomcat ermöglicht. Beispiel: http://tomcatdemo.cloudapp.net.

		Wenn Sie den Port auf einen anderen Wert festlegen (z. B. Port 81), müssen Sie die Portnummer der URL für den Zugriff auf Tomcat hinzufügen. Beispiel: http://tomcatdemo.cloudapp.net:81/.
	2.	Geben Sie unter "Privater Port" den Port "8080" ein. Tomcat lauscht standardmäßig am TCP-Port 8080. Wenn Sie den Standardüberwachungsport von Tomcat geändert haben, sollten Sie den privaten Port so aktualisieren, dass er mit dem Tomcat-Überwachungsport übereinstimmt. ![][7]

4.	Klicken Sie auf **OK** , um den Endpunkt Ihrem virtuellen Computer hinzuzufügen.



###Schritt 2: Herstellen einer Verbindung mit dem Image, das Sie erstellt haben
Sie können ein beliebiges SSH-Tool für das Herstellen einer Verbindung mit Ihrem virtuellen Computer auswählen. In diesem Beispiel wird Putty verwendet.

Rufen Sie zunächst den DNS-Namen Ihres virtuellen Computers aus dem Azure-Portal ab. Klicken Sie auf **Durchsuchen** -> **Virtuelle Computer** -> Name Ihres virtuellen Computers -> **Eigenschaften**. Prüfen Sie dann das Feld **Domänenname** der Kachel **Eigenschaften**.

Rufen Sie die Portnummer für SSH-Verbindungen aus dem Feld **SSH** ab. Beispiel: ![][8]

Laden Sie Putty [hier](http://www.putty.org/) herunter.

Klicken Sie nach dem Download auf die ausführbare Datei "PUTTY.EXE". Konfigurieren Sie die grundlegenden Optionen mit dem Hostnamen und der Portnummer, die Sie aus den Eigenschaften Ihres virtuellen Computers abgerufen haben. Beispiel: ![][9]

Klicken Sie im linken Bereich auf **Verbindung** -> **SSH** -> **Authentifizierung**. Klicken Sie dann auf **Durchsuchen** , um den Speicherort der Datei **privateKey.ppk** anzugeben, die den von puttygen in Phase 1: "Erstellen eines Image" generierten privaten Schlüssel enthält: Beispiel: ![][10]

Klicken Sie auf **Öffnen**. Möglicherweise wird ein Meldungsfeld angezeigt. Wenn Sie den DNS-Namen und die Portnummer richtig konfiguriert haben, klicken Sie auf **Ja**. ![][11]


Daraufhin sollte Folgendes angezeigt werden: ![][12]

Geben Sie den Benutzernamen ein, den Sie beim Erstellen des virtuellen Computers in Phase 1: "Erstellen eines Image" angegeben haben: Folgendes sollte angezeigt werden: ![][13]





##Phase 3: Installieren von Software
In dieser Phase installieren Sie die Java-Laufzeitumgebung, Tomcat und andere Tomcat-Komponenten.

###Java-Laufzeitumgebung
Tomcat wird in Java geschrieben. Es gibt zwei Arten von Java Development Kits (JDKs), OpenJDK und Oracle-JDK, und Sie können das gewünschte Kit auswählen.

>AZURE.NOTE: Beide JDKs haben fast den gleichen Code für die Klassen in der Java-API, aber der Code für den virtuellen Computer ist unterschiedlich. Im Hinblick auf Bibliotheken verwendet OpenJDK tendenziell offene Bibliotheken, während Oracle eher geschlossene Bibliotheken verwendet. Oracle JDK hat hingegen mehr Klassen, und einige Fehler wurden behoben. Außerdem ist Oracle JDK stabiler als OpenJDK.

Mit den folgenden Befehlen werden die verschiedenen JDKs heruntergeladen.

open-jdk

	sudo apt-get update  
	sudo apt-get install openjdk-7-jre  

oracle-jdk

-	So laden Sie das JDK von der Oracle-Website herunter:  

		wget --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u5-b13/jdk-8u5-linux-x64.tar.gz  

-	So erstellen Sie ein Verzeichnis, dass die JDK-Dateien enthält:

		sudo mkdir /usr/lib/jvm  

-	So extrahieren Sie die JDK-Dateien in das Verzeichnis "/usr/lib/jvm/":

		sudo tar -zxf jdk-8u5-linux-x64.tar.gz  -C /usr/lib/jvm/  

-	So legen Sie das Oracle-JDK als Standard-JVM fest:

		sudo update-alternatives --install /usr/bin/java java /usr/lib/jvm/jdk1.8.0_05/bin/java 100  
		sudo update-alternatives --install /usr/bin/javac javac /usr/lib/jvm/jdk1.8.0_05/bin/javac 100  

####Test:
Sie können einen Befehl wie den folgenden verwenden, um zu testen, ob die Java-Laufzeitumgebung ordnungsgemäß installiert ist:

	java -version  

Wenn Sie open-jdk installiert haben, sollte sinngemäß die folgende Meldung angezeigt werden: ![][14]

Wenn Sie oracle-jdk installiert haben, sollte sinngemäß die folgende Meldung angezeigt werden: ![][15]

###Tomcat7
Geben Sie den folgenden Befehl für die Installation von Tomcat7 ein:

	sudo apt-get install tomcat7  

Wenn Sie Tomcat7 nicht verwenden, verwenden Sie die entsprechende Variante dieses Befehls.

####Test:

Wenn Sie überprüfen möchten, ob Tomcat7 erfolgreich installiert wurde, navigieren Sie zum DNS-Namen Ihres Apache-Servers (Beispiel-URL in diesem Artikel: http://tomcatexample.cloudapp.net/). Wenn sinngemäß die folgende Seite angezeigt wird, haben Sie Tomcat7 ordnungsgemäß installiert. ![][16]


###Installieren anderer Tomcat-Komponenten
Es gibt weitere optionale Tomcat-Komponenten, die Sie installieren können.

Verwenden Sie den Befehl **sudo apt-cache search tomcat7**, um alle verfügbaren Komponenten anzuzeigen. Die folgenden Befehle sind Beispiele für die Installation einiger nützlicher Komponenten.

	sudo apt-get install tomcat7-admin      #admin web applications
	sudo apt-get install tomcat7-user         #tools to create user instances  

##Phase 4: Konfigurieren von Tomcat
In dieser Phase verwalten Sie Tomcat.
###Starten und Beenden von Tomcat7
Der Tomcat7-Server wird bei der Installation von Tomcat7 automatisch gestartet. Sie können den Server auch selbst mit dem folgenden Befehl starten:

	sudo /etc/init.d/tomcat7 start

Beenden von Tomcat7：

	sudo /etc/init.d/tomcat7 stop

Anzeigen des Status von Tomcat7：

	sudo /etc/init.d/tomcat7 status

Neustarten von Tomcat-Diensten：

	sudo /etc/init.d/tomcat7 restart

###Tomcat-Verwaltung
Mit dem folgenden Befehl können Sie die Tomcat-Benutzerkonfigurationsdatei bearbeiten, um die Administratoranmeldeinformationen einzurichten:

	sudo vi  /etc/tomcat7/tomcat-users.xml   

Beispiel: ![][17]

>AZURE.NOTE: Erstellen Sie ein sicheres Kennwort für den Admin-Benutzernamen.

Nach der Bearbeitung dieser Datei sollten Sie Tomcat7-Dienste mit dem folgenden Befehl neu starten, um sicherzustellen, dass die Änderungen wirksam werden:

	sudo /etc/init.d/tomcat7 restart  

Öffnen Sie den Browser, und geben Sie die URL **http://<your tomcat server DNS name>/manager/html** ein. Das Beispiel in diesem Artikel verwendet die URL http://tomcatexample.cloudapp.net/manager/html.

Nachdem die Verbindung hergestellt wurde, müsste in etwa Folgendes angezeigt werden: ![][18]

##Häufige Probleme

###Auf den virtuellen Computer mit Tomcat und Moodle kann nicht über das Internet zugegriffen werden.

-	**Symptom** Tomcat wird ausgeführt, die Tomcat-Standardseite wird jedoch nicht in Ihrem Browser angezeigt.
-	**Mögliche Ursache**   
	1.	Der Tomcat-Überwachungsport ist nicht identisch mit dem privaten Port des Endpunkts des virtuellen Computers für Tomcat-Datenverkehr.  

		Überprüfen Sie die Endpunkteinstellungen des öffentlichen und des privaten Ports, und stellen Sie sicher, dass der private Port mit dem Tomcat-Überwachungsport identisch ist. Informationen zum Konfigurieren von Endpunkten für den virtuellen Computer finden Sie unter Phase 1: "Erstellen eines Image".

		Öffnen Sie zur Bestimmung des Tomcat-Überwachungsports "/etc/httpd/conf/httpd.conf" (Red Hat-Version) oder "/etc/tomcat7/server.xml" (Debian-Version). Standardmäßig ist der Tomcat-Überwachungsport 8080. Beispiel:

			<Connector port="8080" protocol="HTTP/1.1"  connectionTimeout="20000"  URIEncoding="UTF-8"            redirectPort="8443" />  

		Wenn Sie einen virtuellen Computer wie Debian oder Ubuntu verwenden und den Standard-Überwachungsport für Tomcat (z. B. 8081) ändern möchten, sollten Sie auch den Port für das Betriebssystem öffnen. Öffnen Sie zunächst das Profil:

			sudo vi /etc/default/tomcat7  

		Löschen Sie dann den Kommentar der letzten Zeile und ändern Sie "Nein" zu "Ja".

			AUTHBIND=yes

	2.	Die Firewall hat den Überwachungsport von Tomcat deaktiviert.

		Wenn Sie nur die Tomcat-Standardseite vom lokalen Host aus sehen können, besteht das Problem wahrscheinlich darin, dass der Port, an dem Tomcat lauscht, durch die Firewall blockiert ist. Sie können das Tool w3m verwenden, um die Webseite zu durchsuchen. Die folgenden Befehle installieren w3m und navigieren zur Apache-Standardseite:

			sudo yum  install w3m w3m-img
			w3m http://localhost:8080  

-	**Lösung**
	1. Wenn der Tomcat-Überwachungsport nicht mit dem privaten Port des Endpunkts für den Datenverkehr an den virtuellen Computer identisch ist, müssen Sie den privaten Port so ändern, dass dieser identisch mit dem Tomcat-Überwachungsport ist.   

	2.	Wenn das Problem durch die Firewall/iptables verursacht wird, fügen Sie "/etc/sysconfig/iptables" die folgenden Zeilen hinzu:

			-A INPUT -p tcp -m tcp --dport 80 -j ACCEPT
			-A INPUT -p tcp -m tcp --dport 443 -j ACCEPT  

		Beachten Sie, dass die zweite Zeile nur für HTTPS-Datenverkehr erforderlich ist.

		Stellen Sie sicher, dass sich diese Zeilen über allen Zeilen befinden, die den Zugriff ggf. global einschränken, z. B. über den Folgenden:

			-A INPUT -j REJECT --reject-with icmp-host-prohibited  

		Führen Sie den folgenden Befehl aus, um die iptables erneut zu laden:

			service iptables restart  

		Dies wurde unter CentOS 6.3 getestet.

###Beim Hochladen von Projektdateien in das Verzeichnis "/var/lib/tomcat7/webapps" wird der Zugriff verweigert  

-	**Symptom** Wenn Sie einen beliebigen SFTP-Client (z. B. FileZilla) zum Herstellen einer Verbindung mit Ihrem virtuellen Computer verwenden und zu "/var/lib/tomcat7/webapps/" navigieren, um Ihre Website zu veröffentlichen, erhalten Sie sinngemäß die folgende Fehlermeldung:  

		status:	Listing directory /var/lib/tomcat7/webapps
		Command:	put "C:\Users\liang\Desktop\info.jsp" "info.jsp"
		Error:	/var/lib/tomcat7/webapps/info.jsp: open for write: permission denied
		Error:	File transfer failed

-	**Mögliche Ursache** Sie haben keine Zugriffsberechtigungen für den Ordner "/var/lib/tomcat7/webapps".
-	**Lösung** Rufen Sie Berechtigungen aus dem root-Konto ab. Sie können den Besitzer dieses Ordners aus "root" in den Benutzernamen ändern, die Sie bei der Bereitstellung des Computers verwendet haben. Das folgende Beispiel zeigt den Kontonamen "azureuser":  

		sudo chown azureuser -R /var/lib/tomcat7/webapps

	Verwenden Sie die Option "-R", um die Berechtigungen auch auf alle Dateien in einem Verzeichnis anzuwenden.

	Beachten Sie, dass dieser Befehl auch für Verzeichnisse funktioniert. Die Option "-R" ändert die Berechtigungen für alle Dateien und Verzeichnisse innerhalb des Verzeichnisses. Beispiel:

		sudo chown -R username:group directory  

	Dieser Befehl ändert den Besitzer (Benutzer und Gruppe) für alle Dateien und Verzeichnisse im Verzeichnis und das Verzeichnis selbst.

	Der folgende Befehl ändert nur die Berechtigung des Ordnerverzeichnisses, betrifft jedoch nicht die Dateien und Ordner im Verzeichnis.

		sudo chown username:group directory



[1]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-01.png
[2]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-02.png
[3]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-03.png
[4]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-04.png
[5]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-05.png
[6]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-06.png
[7]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-07.png
[8]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-08.png
[9]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-09.png
[10]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-10.png
[11]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-11.png
[12]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-12.png
[13]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-13.png
[14]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-14.png
[15]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-15.png
[16]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-16.png
[17]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-17.png
[18]: ./media/virtual-machines-linux-setup-tomcat7-linux/virtual-machines-linux-setup-tomcat7-linux-18.png

<!---HONumber=AcomDC_0128_2016-->