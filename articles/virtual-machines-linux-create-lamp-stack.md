<properties
	pageTitle="Erstellen eines LAMP-Stapels mit Microsoft Azure"
	description="Erfahren Sie, wie Sie einen LAMP-Stapel mit Microsoft Azure mithilfe von virtuellen Azure-Computern erstellen, auf denen Linux ausgeführt wird."
	services="virtual-machines"
	documentationCenter=""
	authors="NingKuang"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="2/10/2015"
	ms.author="ningk"/>

#Erstellen eines LAMP-Stapels mit Microsoft Azure

Ein "LAMP"-Stapel ist eine Gruppe von Open-Source-Software, die in der Regel zusammen installiert wird, um einem Server das Hosten dynamischer Websites und Webanwendungen zu ermöglichen. Dieser Begriff ist ein Akronym (Linux, Apache, MySQL-Datenbank, PHP), das das Linux-Betriebssystem mit dem Apache-Webserver darstellt. Die Websitedaten werden in einer MySQL-Datenbank gespeichert, und dynamische Inhalte werden von PHP verarbeitet.  

In diesem Handbuch installieren Sie einen LAMP-Stapel in einem Linux-Image und stellen dieses unter Microsoft Azure bereit.  

Sie erhalten Informationen zu folgenden Themen:  

-	Gewusst wie: Erstellen eines virtuellen Azure-Computers
-	Gewusst wie: Vorbereiten des virtuellen Computers für den LAMP-Stapel
-	Gewusst wie: Installieren von Software, die von Ihrem LAMP-Server auf dem virtuellen Computer benötigt wird

Es wird vorausgesetzt, dass der Leser bereits über ein Azure-Abonnement verfügt.  Falls dies nicht der Fall ist, können Sie sich für eine kostenlose Testversion unter [http://azure.microsoft.com](http://azure.microsoft.com) registrieren. Wenn Sie über ein MSDN-Abonnement verfügen, finden Sie weitere Informationen unter [Microsoft Azure-Sonderpreis: MSDN-, MPN- und Bizspark-Vorteile](http://azure.microsoft.com/pricing/member-offers/msdn-benefits/?c=14-39). Weitere Informationen zu Azure finden Sie unter [Was ist Azure?](http://azure.microsoft.com/overview/what-is-azure/)

Wenn Sie bereits einen virtuellen Computer verwenden und nur Informationen zu den Grundlagen der Installation eines LAMP-Stapels auf unterschiedlichen Linux-Verteilungen benötigen, lesen Sie zusätzlich zu diesem Thema [Installieren des LAMP-Stapels auf einem virtuellen Linux-Computer in Azure](../virtual-machines-linux-install-lamp-stack/).

Sie können auch vorkonfigurierte LAMP-Images aus dem Azure Marketplace bereitstellen. Das folgende 10-minütige Video enthält eine Einführung zur Bereitstellung vorgefertigter LAMP-Images aus dem Azure Marketplace:  

> [AZURE.VIDEO lamp-stack-on-azure-vms-with-guy-bowerman]

##Phase 1: Erstellen eines Images
In dieser Phase erstellen Sie einen virtuellen Computer mit einem Linux-Image in Azure.  

###Schritt 1: Generieren eines SSH-Authentifizierungsschlüssels
SSH ist ein wichtiges Tool für Systemadministratoren. Es ist jedoch aus Sicherheitsgründen nicht immer ratsam, ein Kennwort zu verwenden, das eine Person festgelegt hat. Mithilfe eines starken SSH-Schlüssels können Sie den Remotezugriff geöffnet lassen, ohne sich Gedanken über Kennwörter machen zu müssen. Die Methode besteht aus Authentifizierung mit asymmetrischer Kryptografie. Der private Schlüssel des Benutzers ist der Schlüssel, der die Authentifizierung vornimmt. Sie können sogar das Konto des Benutzers sperren, um Kennwortauthentifizierung vollständig zu untersagen.

Führen Sie die folgenden Schritte aus, um den SSH-Authentifizierungsschlüssel zu generieren.

-	Laden und installieren Sie puttygen von folgendem Speicherort: [http://www.chiark.greenend.org.uk/~sgtatham/](http://www.chiark.greenend.org.uk/~sgtatham/)putty/download.html
-	Führen Sie "putty.exe" aus.
-	Klicken Sie auf **Generieren**, um die Schlüssel zu generieren. Während des Vorgangs können Sie die Zufälligkeit durch Bewegen der Maus über den leeren Bereich im Fenster erhöhen.  
![][1]
-	Nach dem Generierungsvorgang zeigt "Puttygen.exe" den generierten Schlüssel an. Beispiel:  
![][2]
-	Wählen und kopieren Sie den öffentlichen Schlüssel in **Schlüssel**, und speichern Sie ihn in einer Datei namens **publicKey.pem**. Klicken Sie nicht auf **Öffentlichen Schlüssel speichern**, weil sich das Dateiformat des gespeicherten öffentlichen Schlüssels vom gewünschten öffentlichen Schlüssel unterscheidet.
-	Klicken Sie auf **Privaten Schlüssel speichern**, und speichern Sie den Schlüssel dann in einer Datei namens **privateKey.ppk**.

###Schritt 2: Erstellen des Images im Azure-Vorschauportal
Klicken Sie im [Azure-Vorschauportal](https://portal.azure.com/) auf der Taskleiste auf **Neu**, und erstellen Sie ein Image anhand dieser Anleitungen. Wählen Sie dabei das Linux-Image basierend auf Ihren Anforderungen aus. Dieses Beispiel verwendet das Ubuntu-Image 14.04.

![][3]

Geben Sie unter **Hostname** den Namen für die URL ein, die Sie und die Internetclients für den Zugriff auf diesen virtuellen Computer verwenden. Definieren Sie den letzten Teil des DNS-Namens (z. B. "LAMPDemo"), und Azure generiert die URL als "Lampdemo.cloudapp.net".

Wählen Sie für **Benutzername** einen Namen aus, den Sie später für die Anmeldung am virtuellen Computer verwenden.

Kopieren Sie für **SSH-Authentifizierungsschlüssel** den Schlüsselwert aus der Datei **publicKey.pem**, die den von Puttygen generierten öffentlichen Schlüssel enthält.  

![][4]

Konfigurieren Sie weitere Einstellungen wie erforderlich, und klicken Sie dann auf **Erstellen**.

##Phase 2: Vorbereiten des virtuellen Computers für den LAMP-Stapel
In dieser Phase konfigurieren Sie einen Endpunkt für Webdatenverkehr und stellen dann eine Verbindung mit dem neuen virtuellen Computer her.

###Schritt 1: Öffnen des HTTP-Ports, um Webzugriff zu erlauben
Endpunkte in Azure bestehen aus einem Protokoll (TCP oder UDP) sowie aus einem öffentlichen und einem privaten Port. Der private Port ist der Port, an dem der Dienst auf dem virtuellen Computer lauscht. Der öffentliche Port ist der Port, an dem der Azure-Clouddienst extern auf internetbasierten Datenverkehr lauscht. In einigen Fällen handelt es sich um die gleiche Portnummer.  

TCP-Port 80 ist die Standardportnummer, an der Apache lauscht. Wenn Sie diesen Port mit einem Azure-Endpunkt öffnen, können Sie und andere Internetclients auf den Apache-Webserver zugreifen.  

Klicken Sie im Azure-Vorschauportal auf **Durchsuchen -> Virtueller Computer**, und klicken Sie dann auf den virtuellen Computer, den Sie erstellt haben.

![][5]

Klicken Sie zum Hinzufügen eines Endpunkts zu einem virtuellen Computer auf das Feld **Endpunkte**.

![][6]

Klicken Sie auf **Hinzufügen**. Bei der Bereitstellung eines neuen virtuellen Computers können Sie Endpunkte nach Bedarf aktivieren oder deaktivieren.

Konfigurieren Sie den Endpunkt:  

1.	Geben Sie einen Namen für den Endpunkt unter **Endpunkt** ein.
2.	Geben Sie "80" unter **Öffentlicher Port** ein. Wenn Sie den Standardlauschport von Apache geändert haben, sollten Sie den privaten Port so aktualisieren, dass er mit dem Apache-Lauschport übereinstimmt.
3.	Geben Sie "80" unter **Öffentlicher Port** ein. HTTP-Datenverkehr verwendet standardmäßig Port 80.
Wenn Sie den Port auf 80 festlegen, müssen Sie die Portnummer nicht in die URL einschließen, die Ihnen den Zugriff auf den Apache-Webdienst ermöglicht. Beispiel: "http://lampdemo.cloudapp.net".
Wenn Sie den Apache-Lauschport auf einen anderen Wert festlegen (z. B. Port 81), müssen Sie die Portnummer der URL für den Zugriff auf den Apache-Webdienst hinzufügen. Beispiel: "http://lampdemo.cloudapp.net:81/".

![][7]

Klicken Sie auf **OK**, um den Endpunkt Ihrem virtuellen Computer hinzuzufügen.




###Schritt 2: Herstellen einer Verbindung mit dem Image, das Sie erstellt haben
Sie können ein beliebiges SSH-Tool für das Herstellen einer Verbindung mit Ihrem neuen virtuellen Computer auswählen. In diesem Beispiel wird Putty verwendet.  

Rufen Sie zunächst den DNS-Namen Ihres virtuellen Computers aus dem Azure-Vorschauportal ab. Klicken Sie auf **Durchsuchen -> Virtuelle Computer** Name Ihres virtuellen Computers **-> Eigenschaften**, und untersuchen Sie dann das Feld **Domänenname** der Kachel **Eigenschaften**.

Rufen Sie die Portnummer für SSH-Verbindungen aus dem Feld **SSH** ab.   Beispiel:  

![][8]

Laden Sie Putty [hier](http://www.putty.org/) herunter.  

Klicken Sie nach dem Download auf die ausführbare Datei "PUTTY.EXE". Konfigurieren Sie die grundlegenden Optionen mit dem Hostnamen und der Portnummer, die Sie aus den Eigenschaften Ihres virtuellen Computers abgerufen haben. Beispiel:

![][9]

Klicken Sie im linken Bereich auf **Verbindung -> SSH -> Authentifizierung**, und klicken Sie dann auf **Durchsuchen**, um den Speicherort der Datei **privateKey.ppk** anzugeben, die den privaten Schlüssel enthält, der von puttygen in Phase 1 generiert wurde: Erstellen Sie ein Image. Beispiel:  

![][10]

Klicken Sie auf **Öffnen**. Möglicherweise wird ein Meldungsfeld angezeigt. Wenn Sie den DNS-Namen und die Portnummer richtig konfiguriert haben, klicken Sie auf **Ja**.

![][11]


Daraufhin sollte Folgendes angezeigt werden.

![][12]

Geben Sie den Benutzernamen ein, den Sie beim Erstellen des virtuellen Computers in Phase 1 angegeben haben: Erstellen Sie ein Image. Folgendes sollte angezeigt werden:  

![][13]

##Phase 3: Installieren des LAMP-Stapels

Abhängig von der zum Erstellen des virtuellen Computers verwendeten Linux-Verteilung wird der LAMP-Stapel auf verschiedene Weise installiert. Die folgenden Abschnitte enthalten die typischen Schritte für einige gängige Linux-Betriebssysteme.

###Red Hat auf CentOS-Basis

####Installieren von Apache
Öffnen Sie zum Installieren von Apache das Terminal, und führen Sie den folgenden Befehl aus:  

	sudo yum install httpd

Starten Sie Apache nach der Installation mit dem folgenden Befehl:  

	sudo service httpd start
####Testen von Apache
Wenn Sie überprüfen möchten, ob Apache erfolgreich installiert wurde, navigieren Sie zum DNS-Namen Ihres Apache-Servers (für die Beispiel-URL in diesem Artikel "http://lampdemo.cloudapp.net/"). Die Seite sollte den Text "It works!" anzeigen.
![][14]

####Problembehandlung
Wenn Apache ausgeführt wird, die oben abgebildete Apache-Standardseite jedoch nicht angezeigt wird, müssen Sie Folgendes überprüfen:  

-	Lauschadresse/Port des Apache-Webdiensts
	-	Überprüfen Sie die Endpunkteinstellung für Ihren virtuellen Azure-Computer. Stellen Sie sicher, dass die Konfiguration des Endpunkts ordnungsgemäß ist. Siehe Phase 1: Anleitungen zum "Erstellen eines Images" in diesem Artikel.
	-	Öffnen Sie "/etc/httpd/conf/httpd.conf", und suchen Sie dann nach der Zeichenfolge "Listen". Stellen Sie sicher, dass der Apache-Lauschport mit dem privaten Port identisch ist, den Sie für Ihren Endpunkt konfiguriert haben. Der Standardport für Apache ist 80. Beispiel:  

			......
			......
				# prevent Apache from glomming onto all bound IP addresses (0.0.0.0)
				#
				#Listen 12.34.56.78:80
				Listen 80
				#
				# Dynamic Shared Object (DSO) Support
			......
			......  

-	Firewall, iptables-Konfiguration  
Wenn Sie die Apache-Standardseite vom lokalen Host aus sehen können, besteht das Problem möglicherweise darin, dass der Port, an dem Apache lauscht, durch die Firewall blockiert ist. Sie können das Tool w3m verwenden, um die Apache-Webseite zu durchsuchen. Mit den folgenden Befehle installieren Sie w3m und navigieren zur Apache-Standardseite:  

		sudo yum  install w3m w3m-img  
		w3m http://localhost

	Wenn das Problem durch die Firewall oder iptables verursacht wird, fügen Sie "/etc/sysconfig/iptables" die folgenden Zeilen hinzu:  

		-A INPUT -p tcp -m tcp --dport 80 -j ACCEPT
		-A INPUT -p tcp -m tcp --dport 443 -j ACCEPT

	Beachten Sie, dass die zweite Zeile nur für HTTPS-Datenverkehr erforderlich ist.  

	Stellen Sie sicher, dass sich diese Zeilen über allen Zeilen befinden, die den Zugriff ggf. global einschränken, z. B. über den folgenden:  

		-A INPUT -j REJECT --reject-with icmp-host-prohibited  

	Damit die neuen Einstellungen wirksam werden, verwenden Sie den folgenden Befehl:  

		service iptables restart

####Installieren von MySQL
Öffnen Sie zum Installieren von MySQL das Terminal, und führen Sie dann die folgenden Befehle aus:  

	sudo yum install mysql-server
	sudo service mysqld start

Während der Installation fragt MySQL zwei Mal nach Ihrer Berechtigung. Nachdem Sie auf beide Abfragen mit "Ja" geantwortet haben, wird MySQL installiert.

####Konfigurieren von MySQL
Nachdem die Installation erfolgt ist, können Sie ein root-Kennwort für MySQL mit dem folgenden Befehl festlegen:  

	sudo /usr/bin/mysql_secure_installation  

Die Eingabeaufforderung fordert Sie zur Eingabe Ihres aktuellen root-Kennworts auf.

Da Sie MySQL soeben installiert haben, verfügen Sie wahrscheinlich nicht über ein solches Kennwort. Lassen Sie das Kennwort also leer, und drücken Sie die EINGABETASTE.  

	Enter current password for root (enter for none):
	OK, successfully used password, moving on...  

Sie werden aufgefordert, ein root-Kennwort festzulegen. Fahren Sie fort, wählen Sie "J" aus, und folgen Sie dann den Anweisungen.  

CentOS automatisiert das Einrichten von MySQL und stellt eine Reihe von Fragen, die mit "Ja" oder "Nein" beantwortet werden müssen. Diese Fragen werden unten gezeigt. Wählen Sie "J" oder "N" für Ihre Konfiguration aus. Nach Abschluss dieses Vorgangs wird MySQL erneut geladen, und die neuen Änderungen werden implementiert.  

	By default, a MySQL installation has an anonymous user, allowing anyone
	to log into MySQL without having to have a user account created for
	them.  This is intended only for testing, and to make the installation
	go a bit smoother.  You should remove them before moving into a
	production environment.

	Remove anonymous users? [Y/n] y
	 ... Success!

	Normally, root should only be allowed to connect from 'localhost'.  This
	ensures that someone cannot guess at the root password from the network.

	Disallow root login remotely? [Y/n] y
	... Success!

	By default, MySQL comes with a database named 'test' that anyone can
	access.  This is also intended only for testing, and should be removed
	before moving into a production environment.

	Remove test database and access to it? [Y/n] y
	 - Dropping test database...
	 ... Success!
	 - Removing privileges on test database...
	 ... Success!

	Reloading the privilege tables will ensure that all changes made so far
	will take effect immediately.

	Reload privilege tables now? [Y/n] y
	 ... Success!

	Cleaning up...

	All done!  If you've completed all of the above steps, your MySQL
	installation should now be secure.

	Thanks for using MySQL!  

####Installieren von PHP
PHP ist eine Open-Source-Webskriptsprache, die häufig verwendet wird, um dynamische Webseiten zu erstellen.  

Öffnen Sie zum Installieren von PHP auf Ihrem virtuellen Computer das Terminal, und führen Sie dann den folgenden Befehl aus:  

	sudo yum install php php-mysql  

Antworten Sie mit "j", um Softwarepakete herunterzuladen. Antworten Sie mit "j" auf "Importing GPG key 0xE8562897 "CentOS-5 Key (CentOS 5 Official Signing Key)". PHP wird installiert.

	warning: rpmts_HdrFromFdno: Header V3 DSA signature: NOKEY, key ID e8562897
	updates/gpgkey                                                                                                                                                                       | 1.5 kB     00:00
	Importing GPG key 0xE8562897 "CentOS-5 Key (CentOS 5 Official Signing Key) <centos-5-key@centos.org>" from /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-5
	Is this ok [y/N]: y  

###Debian, Ubuntu-Basis
Dies wurde unter Ubuntu 14.04 getestet.  

Ubuntu basiert auf Debian. Sie können den LAMP-Stapel auf die gleiche Weise wie in der Red Hat-Serie installieren. Verwenden Sie das Tool Tasksel, um diese Schritte zu vereinfachen.

Tasksel ist ein Debian/Ubuntu-Tool, das mehrere in Beziehung stehende Pakete als koordinierten Task auf Ihrem System installiert. Weitere Informationen finden Sie unter [Tasksel - Communityhilfe-Wiki](https://help.ubuntu.com/community/Tasksel).

Verwenden Sie Tasksel zum Installieren der erforderlichen Software für den LAMP-Stapel.

- Gehen Sie folgendermaßen vor, um die Paketlisten aus den Repositorys herunterzuladen und zu aktualisieren, um Informationen zu den neuesten Versionen der Pakete und ihren Abhängigkeiten abzurufen:  

		sudo apt-get update
-	So installieren Sie den Ubuntu-LAMP-Stapel mithilfe von Tasksel:  

		sudo apt-get install tasksel
		sudo tasksel install lamp-server

Führen Sie im nächsten Schritt den Assistenten aus, und wählen Sie Ihr **MySQL-Root-Kennwort** aus.

![][15]


##Testen von LAMP auf Ihrem Server
Sie können das LAMP-System durch Erstellen einer schnellen PHP-Infoseite testen.

Erstellen Sie zunächst eine neue Datei:  

	sudo nano /var/www/html/info.php  

Fügen Sie die folgende Zeile hinzu:  

	<?php
	phpinfo();
	?>  

Speichern Sie die Datei, und beenden Sie die Anwendung.  

Starten Sie Apache neu, damit alle Änderungen auf Ihrem Computer wirksam werden. Wenn das Betriebssystem Ihres virtuellen Computers CentOS ist, verwenden Sie den folgenden Befehl, um Apache neu zu starten:  

	sudo service httpd restart

Wenn das Betriebssystem Ihres virtuellen Computers Ubuntu ist, verwenden Sie den folgenden Befehl, um Apache neu zu starten:  

	sudo service apache2 restart  

Schließen Sie den Test ab, indem Sie zu Ihrer PHP-Infoseite navigieren. (Für den Beispielwebserver in diesem Thema lautet die URL "http://lampdemo.cloudapp.net/info.php".)  

Ihr Browser sollte ähnlich wie die folgende Abbildung aussehen:

![][16]

##Zusätzliche Schritte

Als übliches Vorgehen ändern Sie einige Standardeinstellungen, um die Webanwendungsbereitstellung vorzubereiten.  

###Zulassen von Remotezugriff auf MySQL
Wenn Sie mehrere virtuelle Computer mit MySQL installiert haben, die Daten austauschen müssen, sollten Sie MySQL-Remotezugriff aktivieren und die entsprechenden Berechtigungen erteilen.  

**Befehlsreferenzformat:**

	grant [authority] on [databaseName].[tableName] to [username]@[login host] identified by "[passwd]"  

**Beispiel:**  

	grant select,insert,update,delete on studentManage.student to user1@"%" Identified by "abc";

Sie sollten auch das Profil "/etc/mysql/my.cnf" ändern. Wenn Zeilen vorhanden sind, die diesen Zeilen ähneln:  

	skip-networking
	bind-address = 127.0.0.1  

Sie sollten sie auskommentieren (# am Anfang der Zeilen hinzufügen) und dann MySQL neu starten.  

Informationen zum Hinzufügen eines Endpunkts, um Remotezugriff zu erlauben, finden Sie in den Anleitungen in Phase 1: Erstellen Sie ein Image, um einen neuen Endpunkt zu erstellen. Die TCP-Standardportnummer für Remotezugriff von MySQL ist 3306. Beispiel:

![][17]

###Bereitstellen von Webanwendungen auf dem Apache-Server
Nachdem Sie den LAMP-Stapel erfolgreich eingerichtet haben, können Sie Ihre vorhandene Webanwendung auf dem Apache-Webserver (Ihrem virtuellen Computer) bereitstellen. Dabei werden die gleichen Schritte wie bei der Bereitstellung einer vorhandenen Webanwendung auf einem physischen Server ausgeführt.

-	Der Stamm des Webservers befindet sich unter **/var/www/html**. Sie sollten den Benutzern, die Dateien in diesen Ordner hochladen müssen, Berechtigungen gewähren. Das folgende Beispiel zeigt, wie einer Gruppe namens "lampappgroup" Schreibberechtigungen hinzugefügt werden und der Benutzername "azureuser" dieser Gruppe zugewiesen wird:  

		sudo groupadd lampappgroup                      # Create a group
		sudo gpasswd -a azureuser lampappgroup    # Add azureuser to lampappgroup
		sudo chgrp lampappgroup /var/www/html/  # Change the ownership to group lampappgroup
		sudo chmod g+w /var/www/html/                 # grant write permission to group lampappgroup

	>[AZURE.NOTE] Möglicherweise müssen Sie sich erneut anmelden, wenn Sie eine Datei in "/var/www/html/" ändern möchten.
-	Verwenden Sie zum Herstellen einer Verbindung mit dem DNS-Namen Ihres virtuellen Computers (z. B. "lampdemo.cloudapp.net") einen beliebigen SFTP-Client (z. B. FileZilla), und navigieren Sie dann zu /**var/www/html**, um Ihre Website zu veröffentlichen.  
![][18]



##Häufig auftretende Probleme und Problembehandlung

###Auf einen virtuellen Computer kann mit Apache und Moodle nicht aus dem Internet zugegriffen werden

-	**Symptom**  
Apache wird ausgeführt, die Apache-Standardseite wird jedoch nicht in Ihrem Browser angezeigt.
-	**Mögliche Ursache**
	1.	Der Apache-Lauschport ist nicht identisch mit den privaten Port des Endpunkts des virtuellen Computers für Webdatenverkehr.</br>
	Überprüfen Sie die Endpunkteinstellungen des öffentlichen und des privaten Ports, und stellen Sie sicher, dass der private Port mit dem Apache-Lauschport identisch ist. In Phase 1: Erstellen Sie ein Image für die Anweisungen zum Konfigurieren von Endpunkten für den virtuellen Computer.</br>
	Öffnen Sie zum Ermitteln des Lauschports von Apache "/etc/httpd/conf/httpd.conf" (Red Hat-Version) oder "/etc/apache2/ports.conf" (Debian-Version). Suchen Sie nach der Zeichenfolge "Listen". Der Standardport ist 80.

	2.	Die Firewall hat den Lauschport von Apache deaktiviert.</br>  
	Wenn Sie die Apache-Standardseite vom lokalen Host aus sehen können, besteht das Problem wahrscheinlich darin, dass der Port, an dem Apache lauscht, durch die Firewall blockiert ist. Sie können das Tool w3m verwenden, um die Webseite zu durchsuchen. Mit den folgenden Befehle installieren Sie w3m und navigieren zur Apache-Standardseite:  

			sudo yum  install w3m w3m-img
			w3m http://localhost

-	**Lösung**

	1.	Wenn der Apache-Lauschport nicht identisch mit dem privaten Port des Endpunkts für Webdatenverkehr auf dem virtuellen Computer ist, müssen Sie den privaten Port des Endpunkts so ändern, dass er mit dem Apache-Lauschport identisch ist.
	2.	Wenn das Problem durch die Firewall/iptables verursacht wird, fügen Sie "/etc/sysconfig/iptables" die folgenden Zeilen hinzu:  

			-A INPUT -p tcp -m tcp --dport 80 -j ACCEPT
			-A INPUT -p tcp -m tcp --dport 443 -j ACCEPT

		Beachten Sie, dass die zweite Zeile nur für HTTPS-Datenverkehr erforderlich ist.

		Stellen Sie sicher, dass sich diese Zeilen über allen Zeilen befinden, die den Zugriff ggf. global einschränken, z. B. über den folgenden:  

			-A INPUT -j REJECT --reject-with icmp-host-prohibited  

		Führen Sie den folgenden Befehl aus, um die iptables erneut zu laden:  

			service iptables restart  

		Dies wurde unter CentOS 6.3 getestet.

###Berechtigung verweigert beim Hochladen Ihrer Projektdateien in "/var/www/html/"  

-	**Symptom**  
Wenn Sie einen beliebigen SFTP-Client (z. B. FileZilla) zum Herstellen einer Verbindung mit Ihrem virtuellen Computer verwenden und zu "/var/www/html" navigieren, um Ihre Website zu veröffentlichen, erhalten Sie eine Fehlermeldung ähnlich der folgenden Meldung:  

		status:	Listing directory /var/www/html
		Command:	put "C:\Users\liang\Desktop\info.php" "info.php"
		Error:	/var/www/html/info.php: open for write: permission denied
		Error:	File transfer failed

-	**Mögliche Ursache**
Sie sind nicht berechtigt, auf den Ordner "/var/www/html" zuzugreifen.  
-	**Lösung**  
Sie müssen Berechtigungen aus dem root-Konto abrufen. Sie können den Besitzer dieses Ordners aus "root" in den Benutzernamen ändern, den Sie bei der Bereitstellung des Computers verwendet haben. Das folgende Beispiel zeigt den Kontonamen "azureuser":  

		sudo chown azureuser -R /var/www/html  

	Verwenden Sie die Option "-R", um die Berechtigungen auch auf alle Dateien in einem Verzeichnis anzuwenden.  

	Beachten Sie, dass dieser Befehl auch für Verzeichnisse funktioniert. Die Option "-R" ändert die Berechtigungen für alle Dateien und Verzeichnisse innerhalb des Verzeichnisses. Beispiel:  

		sudo chown -R username:group directory  

	Dieser Befehl ändert den Besitzer (Benutzer und Gruppe) für alle Dateien und Verzeichnisse im Verzeichnis und das Verzeichnis selbst.  

	Der folgende Befehl ändert nur die Berechtigung des Ordnerverzeichnisses, betrifft jedoch nicht die Dateien und Ordner im Verzeichnis.  

		sudo chown username:group directory  

###Der vollqualifizierte Domänenname des Servers konnte nicht zuverlässig ermittelt werden.

-	**Symptom**  
Wenn Sie den Apache-Server mithilfe eines der folgenden Befehle neu starten:  

		sudo /etc/init.d/apache2 restart  # Debian release  

	oder

		sudo /etc/init.d/httpd restart   # Red Hat release  

	Sie erhalten den folgenden Fehler:  

		Restarting web server apache2
		apache2: Could not reliably determine the server's fully qualified domain name, using 127.0.1.1 for ServerName
		... waiting apache2:
		Could not reliably determine the server's fully qualified domain name, using 127.0.1.1 for ServerName  

-	**Mögliche Ursache**
	Sie haben den Servernamen von Apache nicht festgelegt.

-	**Lösung**  
	Fügen Sie eine Zeile "ServerName localhost" in "httpd.conf" (Red Hat-Version) oder "apache2.conf" (Debian Version) in "/etc/apache2" ein, und starten Sie Apache dann neu. Die Meldung wird nicht mehr angezeigt.



[1]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-01.png
[2]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-02.png
[3]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-03.png
[4]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-04.png
[5]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-05.png
[6]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-06.png
[7]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-07.png
[8]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-08.png
[9]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-09.png
[10]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-10.png
[11]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-11.png
[12]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-12.png
[13]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-13.png
[14]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-14.png
[15]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-15.png
[16]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-16.png
[17]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-17.png
[18]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-18.jpg

<!--HONumber=45--> 
