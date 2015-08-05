<properties
	pageTitle="Installieren und Konfigurieren von PostgreSQL auf einem virtuellen Microsoft Azure-Computer mit Linux"
	description="Erfahren Sie, wie Sie PostgreSQL auf einem virtuellen Ubuntu- oder CentOS-Computer in Azure installieren und konfigurieren."
	services="virtual-machines"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor=""
  tags=""/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="linux"
	ms.workload="infrastructure-services"
	ms.date="06/04/2015"
	ms.author="mingzhan"/>


#Installieren und Konfigurieren von PostgreSQL in Microsoft Azure

PostgreSQL ist eine erweiterte Open-Source-Datenbank, die Oracle und DB2 ähnlich ist. Sie umfasst Unternehmensfunktionen wie vollständige ACID-Kompatibilität, zuverlässige Transaktionsverarbeitung und Parallelitätssteuerung mit mehreren Versionen. Darüber hinaus unterstützt sie Standards wie ANSI SQL und SQL/MED (einschließlich Fremddatenwrappern für Oracle, MySQL, MongoDB und viele andere). Sie ist stark erweiterbar mit Unterstützung für mehr als 12 prozedurale Sprachen, GIN- und GIST-Indizes, Unterstützung für räumliche Daten und mehrere NoSQL-ähnliche Funktionen für JSON- oder Anwendungen auf Schlüssel-Wert-Basis.

In diesem Artikel erfahren Sie, wie Sie PostgreSQL auf einem virtuellen Azure-Computer, auf dem Linux ausgeführt wird, installieren und konfigurieren.

> [Azure.NOTE]Sie benötigen einen virtuellen Microsoft Azure-Computer mit Linux, um dieses Lernprogramm auszuführen. Bevor Sie fortfahren, sollten Sie die Informationen zum Erstellen und Einrichten eines virtuellen Linux-Computers unter [Erstellen eines virtuellen Linux-Computers](virtual-machines-linux-tutorial.md) lesen.

[Verwenden Sie in diesem Fall Port 1999 als PostgreSQL-Port.]

## Installieren von PostgreSQL

Stellen Sie über PuTTY eine Verbindung mit dem virtuellen Linux-Computer her. Wenn Sie den virtuellen Linux-Computer in Azure zum ersten Mal verwenden, finden Sie [hier](virtual-machines-linux-use-ssh-key.md) Informationen zum Herstellen einer Verbindung mit einer Linux-VM mit PuTTY.

1. Führen Sie den folgenden Befehl aus, um Root-Rechte (admin) zu erhalten:

		$ sudo su -

2. Für einige Distributionen gelten bestimmte Abhängigkeiten, die Sie vor der Installation von PostgreSQL installieren müssen. Suchen Sie Ihre Distribution in dieser Liste, und führen Sie den entsprechenden Befehl aus:

	- RedHat:

			# yum install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  

	- Debian:

 			# apt-get install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam libxslt-devel tcl-devel python-devel -y  

	- SUSE:

			# zypper install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  

3. Laden Sie PostgreSQL in das Stammverzeichnis, und entpacken Sie das Paket:

		# wget https://ftp.postgresql.org/pub/source/v9.3.5/postgresql-9.3.5.tar.bz2 -P /root/

		# tar jxvf  postgresql-9.3.5.tar.bz2

	Oben ist ein Beispiel aufgeführt. Sie finden [hier](https://ftp.postgresql.org/pub/source/) ein umfassenderes Downloadverzeichnis.

4. Um den Build zu starten, führen Sie folgende Befehle aus:

		# cd postgresql-9.3.5

		# ./configure --prefix=/opt/postgresql-9.3.5

5. Wenn Sie einen vollständigen Build, einschließlich der Dokumentation (HTML- und Handbuchseiten) sowie zusätzlicher Module (contrib), erstellen möchten, führen Sie stattdessen den folgenden Befehl aus:

		# gmake install-world

	Die folgende Bestätigungsmeldung wird angezeigt:

		PostgreSQL, contrib, and documentation successfully made. Ready to install.

## Konfigurieren von PostgreSQL

1. (Optional) Erstellen Sie eine symbolische Verknüpfung zum Verkürzen der PostgreSQL-Referenz, sodass die Versionsnummer nicht enthalten ist:

		# ln -s /opt/pgsql9.3.5 /opt/pgsql

2. Erstellen Sie ein Verzeichnis für die Datenbank:

		# mkdir -p /opt/pgsql_data

3. Erstellen Sie einen Benutzer ohne Root-Rechte, und ändern Sie das Profil des Benutzers. Wechseln Sie zu dem neuen Benutzer (in diesem Beispiel *Postgres*):

		# useradd postgres

		# chown -R postgres.postgres /opt/pgsql_data

		# su - postgres

    >[Azure.NOTE]Aus Gründen der Sicherheit verwendet PostgreSQL einen Benutzer ohne Root-Rechte zum Initialisieren, Starten oder Herunterfahren der Datenbank.


4. Bearbeiten Sie das *bash_profile*, indem Sie die folgenden Befehle eingeben. Diese Zeilen werden am Ende der Datei für das *bash_profile* hinzugefügt:

		cat >> ~/.bash_profile <<EOF
		export PGPORT=1999
		export PGDATA=/opt/pgsql_data
		export LANG=en_US.utf8
		export PGHOME=/opt/pgsql
		export PATH=\$PATH:\$PGHOME/bin
		export MANPATH=\$MANPATH:\$PGHOME/share/man
		export DATA=`date +"%Y%m%d%H%M"`
		export PGUSER=postgres
		alias rm='rm -i'
		alias ll='ls -lh'
		EOF

5. Führen Sie die *bash_profile*-Datei aus:

		$ source .bash_profile

6. Überprüfen Sie die Installation mit dem folgenden Befehl:

		$ which psql

	Wenn die Installation erfolgreich war, wird die folgende Antwort angezeigt:

		/opt/pgsql/bin/psql

7. Sie können auch die PostgreSQL-Version überprüfen:

		$ psql -V

8. Initialisieren Sie die Datenbank:

		$ initdb -D $PGDATA -E UTF8 --locale=C -U postgres -W

	Daraufhin sollten Sie folgende Ausgabe erhalten:

![image](./media/virtual-machines-linux-postgresql/no1.png)

## Einrichten von PostgreSQL

<!--	[postgres@ test ~]$ exit -->

Führen Sie die folgenden Befehle aus:

	# cd /root/postgresql-9.3.5/contrib/start-scripts

	# cp linux /etc/init.d/postgresql

Ändern Sie zwei Variablen in der Datei "/etc/init.d/postgresql". Das Präfix wird auf den Installationspfad von PostgreSQL festgelegt: **/opt/pgsql**. PGDATA wird auf den Datenspeicherpfad von PostgreSQL festgelegt: **/opt/pgsql_data**.

	# sed -i '32s#usr/local#opt#' /etc/init.d/postgresql

	# sed -i '35s#usr/local/pgsql/data#opt/pgsql_data#' /etc/init.d/postgresql

![image](./media/virtual-machines-linux-postgresql/no2.png)

Ändern Sie die Datei, um sie ausführbar zu machen:

	# chmod +x /etc/init.d/postgresql

Starten Sie PostgreSQL:

	# /etc/init.d/postgresql start

Überprüfen Sie, ob der PostgreSQL-Endpunkt aktiv ist:

	# netstat -tunlp|grep 1999

Die folgende Ausgabe wird angezeigt.

![image](./media/virtual-machines-linux-postgresql/no3.png)

## Herstellen einer Verbindung mit der Postgres-Datenbank

Wechseln Sie erneut zum Postgres-Benutzer:

	# su - postgres

Erstellen Sie eine Postgres-Datenbank:

	$ createdb events

Stellen Sie eine Verbindung mit der soeben erstellten Ereignisdatenbank her:

	$ psql -d events

## Erstellen und Löschen einer Postgres-Tabelle

Da jetzt eine Verbindung mit der Datenbank besteht, können wir Tabellen in ihr erstellen.

Erstellen Sie mit dem folgenden Befehl eine neue Postgres-Beispieltabelle:

	CREATE TABLE potluck (name VARCHAR(20),	food VARCHAR(30),	confirmed CHAR(1), signup_date DATE);

Wir haben nun eine Tabelle mit vier Spalten mit den folgenden Spaltennamen und Einschränkungen eingerichtet:

1. Die Spalte "Name" wurde mit dem VARCHAR-Befehl auf unter 20 Zeichen beschränkt.
2. Die Spalte "Food" gibt die Speisen an, die die einzelnen Personen mitbringen. VARCHAR beschränkt diesen Text auf unter 30 Zeichen.
3. Die Spalte "confirmed" zeichnet auf, ob die Person zugestimmt hat, einen Beitrag zum Buffet zu leisten. Die zulässigen Werte lauten "Y" und "N".
4. Die Spalte "date" wird angezeigt, wenn eine Person sich für das Ereignis angemeldet hat. Postgres erfordert, dass Datumsangaben im Format jjjj-mm-tt geschrieben werden

Wenn die Tabelle erfolgreich erstellt wurde, sollte folgende Ausgabe angezeigt werden:

![image](./media/virtual-machines-linux-postgresql/no4.png)

Sie können auch die Tabellenstruktur mit dem folgenden Befehl überprüfen:

![image](./media/virtual-machines-linux-postgresql/no5.png)

### Hinzufügen von Daten zu einer Tabelle

Fügen Sie zunächst Informationen in eine Zeile ein:

	INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('John', 'Casserole', 'Y', '2012-04-11');

Die folgende Ausgabe sollte angezeigt werden:

![image](./media/virtual-machines-linux-postgresql/no6.png)

Sie können einige weitere Personen zur Tabelle hinzufügen. Hier finden Sie einige Optionen, Sie können aber auch eigene erstellen:

	INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Sandy', 'Key Lime Tarts', 'N', '2012-04-14');

	INSERT INTO potluck (name, food, confirmed, signup_date) VALUES ('Tom', 'BBQ','Y', '2012-04-18');

	INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Tina', 'Salad', 'Y', '2012-04-18');

### Anzeigen von Tabellen

Verwenden Sie den folgenden Befehl zum Anzeigen einer Tabelle:

	select * from potluck;

Die Ausgabe ist:

![image](./media/virtual-machines-linux-postgresql/no7.png)

### Löschen von Daten in einer Tabelle

Mit dem folgenden Befehl können Sie Daten in einer Tabelle löschen:

	delete from potluck where name=’John’;

Dadurch werden alle erforderlichen Informationen in der Zeile "John" gelöscht. Die Ausgabe ist:

![image](./media/virtual-machines-linux-postgresql/no8.png)

### Aktualisieren von Daten in einer Tabelle

Mit dem folgenden Befehl können Sie Daten in einer Tabelle aktualisieren: In diesem Beispiel hat Sandy bestätigt, dass sie teilnimmt, sodass wir ihren Wert für "confirmed" von "N" in "Y" ändern können:

 	UPDATE potluck set confirmed = 'Y' WHERE name = 'Sandy';


##Weitere Informationen über PostgreSQL
Sie schließen jetzt die Installation von PostgreSQL auf dem virtuellen Linux-Computer in Microsoft Azure ab und beginnen mit der Verwendung in Microsoft Azure. Weitere Informationen über PostgreSQL finden Sie [hier](http://www.postgresql.org/)

<!---HONumber=July15_HO4-->