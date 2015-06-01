
1. Führen Sie zum Eskalieren von Berechtigungen Folgendes aus:

		sudo -s
	
	Geben Sie das Kennwort ein.

2. Führen Sie den folgenden Befehl aus, um die MySQL Community Server-Edition zu installieren:

		# zypper install mysql-community-server

	Warten Sie, bis MySQL heruntergeladen und installiert wurde.
3. Führen Sie den folgenden Befehl aus, um das System so einzurichten, dass beim Start MySQL gestartet wird:

		# insserv mysql
4. Jetzt können Sie den MySQL-Daemon (mysqld) mit dem folgenden Befehl manuell starten:

		# rcmysql start

	Führen Sie Folgendes aus, um den Status des MySQL-Daemons zu prüfen:

		# rcmysql status

	Führen Sie den folgenden Befehl aus, um den MySQL-Daemon zu stoppen:

		# rcmysql stop

5. Warnung! Nach der Installation ist das MySQL-Stammkennwort standardmäßig leer.  Es wird empfohlen, das Skript **mysql_secure_installation** auszuführen, das bei der Sicherung von MySQL hilft. Beim Ausführen von **mysql_secure_installation** werden Sie aufgefordert, das MySQL-Stammkennwort zu ändern, anonyme Benutzerkonten zu entfernen, remote Stammanmeldedaten zu deaktivieren, Testdatenbanken zu entfernen und die Berechtigungstabelle erneut zu laden. Es wird empfohlen, für alle diese Optionen mit "Ja" zu antworten und das Stammkennwort zu ändern. Führen Sie den folgenden Befehl aus, um das Skript auszuführen:

		$ mysql_secure_installation

6. Nach der Ausführung können Sie sich bei MySQL anmelden:

		$ mysql -u root -p

	Geben Sie das MySQL-Stammkennwort ein, das Sie im vorherigen Schritt geändert haben. Daraufhin wird eine Eingabeaufforderung angezeigt, über die Sie SQL-Anweisungen starten können, um mit der Datenbank zu interagieren.

7. Führen Sie zum Erstellen eines neuen MySQL-Benutzers bei der **mysql>**-Eingabeaufforderung den folgenden Befehl aus:

		mysql> CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';

	Beachten Sie, dass das Semikolon (;) am Ende der Zeilen entscheidend für den Abschluss der Befehle ist.

8. Geben Sie die folgenden Befehle aus, um eine Datenbank zu erstellen und um dafür die  `mysqluser`-Benutzerberechtigungen zu gewähren:

		mysql> CREATE DATABASE testdatabase;
		mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';

	Beachten Sie, dass die Benutzernamen und Kennwörter der Datenbank nur von Skripten verwendet werden, die eine Verbindung zur Datenbank herstellen.  Benutzernamen für Datenbankkonten sind nicht notwendigerweise tatsächliche Benutzerkonten im System.

9. Führen Sie den folgenden Befehl aus, um sich von einem anderen Computer aus anzumelden:

		mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'<ip-address>' IDENTIFIED BY 'password';

	wobei  `ip-address` die IP-Adresse des Computers ist, von dem aus Sie eine Verbindung mit MySQL herstellen möchten.
	
10. Geben Sie den folgenden Befehl ein, um das Verwaltungsdienstprogramm für die MySQL-Datenbank zu beenden:

		quit

11. Nachdem MySQL installiert wurde, müssen Sie einen Endpunkt konfigurieren, damit ein Remotezugriff auf MySQL möglich ist. Melden Sie sich beim [Azure-Verwaltungsportal][AzurePreviewPortal] an. Klicken Sie im Azure-Portal auf **Virtuelle Computer**, klicken Sie auf den Namen des neuen virtuellen Computers und danach auf **Endpunkte**.

	![Endpoints][Image7]

12. Klicken Sie am unteren Seitenrand auf **Hinzufügen**.
	![Endpoints][Image8]

13. Fügen Sie einen Endpunkt mit dem Namen "MySQL" hinzu, und wählen Sie als Protokoll **TCP** aus. Setzen Sie die beiden Ports **Öffentlich** und **Privat** auf "3306". Dies ermöglicht den Zugriff auf MySQL.
	![Endpoints][Image9]

14. Führen Sie den folgenden Befehl auf Ihrem lokalen Computer aus, um in Azure remote eine Verbindung zu MySQL herzustellen, das auf Ihrem virtuellen OpenSUSE-Computer ausgeführt wird:

		mysql -u mysqluser -p -h <yourservicename>.cloudapp.net

	Wenn Sie den in diesem Lernprogramm erstellten virtuellen Computer verwenden, lautet der Befehl folgendermaßen:

		mysql -u mysqluser -p -h testlinuxvm.cloudapp.net

15. Sie haben MySQL erfolgreich konfiguriert und dazu eine Datenbank sowie einen neuen Benutzer erstellt.  Weitere Informationen zu MySQL finden Sie in der [MySQL-Dokumentation][MySQLDocs].	

[MySQLDocs]: http://dev.mysql.com/doc/
[AzurePreviewPortal]: http://manage.windowsazure.com

[Image9]: ./media/install-and-run-mysql-on-opensuse-vm/LinuxVmAddEndpointMySQL.png

<!--HONumber=45--> 
