<properties
   pageTitle="Erste Schritte: Verbinden mit Azure SQL Data Warehouse | Microsoft Azure"
   description="Erste Schritte zum Herstellen von Verbindungen mit SQL Data Warehouse für das Ausführen von Abfragen."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/01/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Erste Schritte: Verbinden mit Azure SQL Data Warehouse
In diesem Artikel für den Schnelleinstieg ist dargestellt, wie Sie mit zwei verschiedenen Tools eine Verbindung mit einer bereitgestellten Instanz von SQL Data Warehouse herstellen und Abfragen an diese durchführen:

- **Visual Studio** – Der integrierte Code-Editor und Debugger von Visual Studio, SQL Server Data Tools (SSDT), ist vollständig kompatibel mit SQL Data Warehouse, sodass Sie problemlos Verbindungen herstellen, Abfragen ausführen und SQL Data Warehouse verwalten können.  

> [AZURE.NOTE]SQL Data Warehouse setzt mindestens SSDT Preview, Version 12.0.50623 oder höher, voraus.

- **sqlcmd** – sqlcmd ist ein Befehlszeilentool, das einfache Verbindungs- und Abfragefunktionen bietet.  

Im Laufe dieses Artikels werden folgende Aktionen ausgeführt:

1. Installieren und Aktualisieren von Visual Studio 2013
2. Herstellen einer Verbindung mit SQL Data Warehouse in SSDT
3. Ausführen von Abfragen an die SQL Data Warehouse-Datenbank

>[AZURE.NOTE]Es wird vorausgesetzt, dass Sie entweder die Bereitstellungsanweisungen abgeschlossen haben oder über ein SQL Data Warehouse verfügen. Wenn Sie das SQL Data Warehouse nicht bereitgestellt haben, lesen Sie die [Ersten Schritte zur Bereitstellung](sql-data-warehouse-get-started-provision.md).

## Einrichten von Visual Studio für die Entwicklung##
Das SQL Data Warehouse-Team empfiehlt, Visual Studio 2013 oder höher in Verbindung mit SQL Server Data Tools für die Entwicklung zu verwenden. Im Folgenden wird beschrieben, wie Sie Visual Studio 2013 herunterladen und aktualisieren, wenn Sie nicht bereits eine geeignete Version von Visual Studio installiert haben.

Weitere Informationen sowie Antworten auf allgemeine Fragen zu SSDT finden Sie in der [Vollständigen SSDT-Dokumentation](https://msdn.microsoft.com/library/azure/hh272686.aspx).

### Herunterladen von Visual Studio 2013 ###
Auf der Visual Studio 2013-Website auf können Sie eine Kopie von Visual Studio herunterladen und installieren. Die kostenlosen Editionen von SQL Data Warehouse sind mehr als ausreichend. Wählen Sie die für Ihre Anforderungen geeignete Edition.

<a href="https://www.visualstudio.com/downloads/download-visual-studio-vs#DownloadFamilies_5" target="blank">Herunterladen von Visual Studio 2013</a>

### Aktualisieren von Visual Studio 2013 ###
Visual Studio 2013 ist bereits installiert? Prima. Um sicherzustellen, dass Sie die aktuelle Version nutzen, führen die folgenden Schritte aus. Sie können mit dem nächsten Schritt fortfahren.

1. Öffnen Sie Visual Studio 2013.
2. Klicken Sie im Menü **Extras** auf **Erweiterungen und Updates**.
3. Navigieren Sie durch die Strukturansicht zu **Updates** und **Produktupdates**.
4. Wenn kein Update verfügbar ist, können Sie das Fenster **Erweiterungen und Updates** schließen und mit der nächsten Aufgabe in dieser Schnellstartanleitung fortfahren.

Wenn jedoch ein Update für Visual Studio selbst vorhanden ist, klicken Sie auf die Schaltfläche **Aktualisieren**. Hierdurch wird der Download des aktuellen Updates für Visual Studio 2013 gestartet.

Schließen Sie das Fenster **Erweiterungen und Updates** und Visual Studio 2013, bevor Sie fortfahren.

5. Klicken Sie auf die Schaltfläche **Ausführen**, um das aktuelle Update von Visual Studio 2013 zu installieren.

6. Akzeptieren Sie die Lizenzbedingungen, und klicken Sie auf die Schaltfläche **Installieren**. Bestätigen Sie alle Meldungen der Benutzerkontensteuerung.

7. Klicken Sie auf die Schaltfläche **Starten**, um die Installation abzuschließen.

Dies schließt das Update von Visual Studio 2013 ab.

### Aktualisieren von SSDT
> [AZURE.IMPORTANT]SQL Data Warehouse setzt mindestens SSDT Preview, Version 12.0.50623 oder höher, voraus.

Die SSDT-Entwickler aktualisieren ihr Plug-In regelmäßig mit neuen Features, sodass von Zeit zu Zeit eine Aktualisierung ausgeführt werden muss. Dies ist ebenfalls ein sehr einfacher Vorgang. Um zu überprüfen, ob Sie SSDT aktualisieren müssen, führen Sie die folgenden Schritte aus:

1. Öffnen Sie Visual Studio 2013.  
2. Klicken Sie im Menü **Extras** dann auf **Erweiterungen und Updates**.
3. Navigieren Sie durch die Strukturansicht zu **Updates** und **Produktupdates**.
4. Wenn kein Update verfügbar ist, können Sie das Fenster **Erweiterungen und Updates** schließen und mit der nächsten Aufgabe in dieser Schnellstartanleitung fortfahren.

Wenn jedoch ein Update namens "Microsoft SQL Server Update for database tooling" vorhanden ist, klicken Sie auf die Schaltfläche **Aktualisieren**. Hierdurch wird der Download der aktuellen SSDT-Version gestartet.

5. Klicken Sie auf die Schaltfläche **Ausführen**, um die aktuelle Version von SSDT zu installieren.

6. Akzeptieren Sie die Lizenzbedingungen, und klicken Sie dann auf die Schaltfläche **Installieren**.

7. Klicken Sie auf die Schaltfläche **Schließen**, um das SSDT-Update abzuschließen

8. Schließen Sie das Fenster **Erweiterungen und Updates**.

Sie verfügen nun auf Ihrem Desktopcomputer über eine aktuelle Version von Visual Studio 2013 mit einer aktuellen SSDT-Erweiterung.

> [AZURE.NOTE]Derzeit empfehlen wir die [SSDT Preview for Visual Studio 2013, version 12.0.50623 oder höher](http://go.microsoft.com/fwlink/?LinkID=616714&clcid=0x409) (in englischer Sprache).

## Verbinden mit Visual Studio 2013
Wenn Sie die gewünschte Version von Visual Studio verwenden, können Sie auf zwei verschiedene Arten eine Verbindung mit SQL Data Warehouse herstellen.

### Herstellen einer Verbindung von Visual Studio aus
Zum Herstellen einer Verbindung müssen Sie lediglich den SQL Server-Objekt-Explorer öffnen und die Verbindungsinformationen eingeben.

1. Öffnen Sie Visual Studio.
2. Wählen Sie das Menü **Ansicht** und dann **SQL Server-Objekt-Explorer** aus der Dropdownliste aus.

> [AZURE.NOTE]Stellen Sie sicher, dass Sie den SQL Server-Objekt-Explorer und *nicht* den Server-Explorer auswählen. Die Namen klingen ähnlich, es sind jedoch sehr unterschiedliche Steuerelemente. Sie befinden sich direkt nebeneinander. Achten Sie also darauf, den richtigen Eintrag zu wählen.

Der SQL Server-Objekt-Explorer wird angezeigt:


3. Klicken Sie im SQL Server-Objekt-Explorer auf die Schaltfläche **Server hinzufügen**.

4. Füllen Sie das Dialogfeld **Verbindung mit Server** mit den Werten auf, die Sie beim Erstellen des logischen Servers ausgewählt haben. Klicken Sie außerdem auf die Schaltfläche "Optionen", und geben Sie vor dem Herstellen einer Verbindung die Datenbank an, zu der Sie eine Verbindung herstellen (Ihre SQL Data Warehouse-Instanz).

Sie können bei Bedarf das Kontrollkästchen **Kennwort speichern** aktivieren. Sie sparen so natürlich Zeit, aber denken Sie daran, dass dadurch jede Person mit physischem Zugriff auf Ihr Profil über dieses Konto Abfragen ausführen kann.

> [AZURE.NOTE]Beachten Sie, dass der Servername vollqualifiziert sein muss. Aus diesem Grund sollte der Wert für den Servernamen der folgenden Konvention entsprechen: *ServerName*.database.windows.net, wobei *ServerName* für den Namen des logischen Servers steht.

Klicken Sie auf **Verbinden**, nachdem Sie die Anmeldeinformationen vollständig ausgefüllt haben.

Sie haben nun eine Verbindung hergestellt und Ihren logischen SQL Data Warehouse-Server in SQL Server-Objekt-Explorer registriert.

### Herstellen einer Verbindung aus dem Azure-Portal
Wechseln Sie direkt aus dem Azure-Portal zu Visual Studio.

1. Melden Sie sich beim [Azure-Portal](http://manage.windowsazure.com/) an.
2. Wählen Sie die gewünschte SQL Data Warehouse-Instanz auf dem Blatt **Durchsuchen** aus.
3. Wählen Sie am oberen Rand des SQL Data Warehouse-Blatts **In Visual Studio öffnen** aus.
4. Wenn Sie die Firewall nicht mit der IP-Adresse des Clientcomputers konfiguriert haben, wählen Sie **Firewall konfigurieren** aus.

	a. Geben Sie einen **Regelnamen**, die **Start-IP-Adresse** und die **End-IP-Adresse** ein.

	b. Klicken Sie oben auf dem Blatt auf **Speichern**.
5. Klicken Sie auf **In Visual Studio öffnen**.
6. Visual Studio wird geöffnet, und Sie werden aufgefordert, Ihre Anmeldeinformationen einzugeben.
7. Nachdem Sie Ihre Anmeldeinformationen eingegeben und eine Verbindung hergestellt haben, werden die Server- und Data Warehouse-Instanzen im SQL Server-Objekt-Explorer angezeigt.  

## Verbinden mit SQL Data Warehouse per sqlcmd

Sie können auch mit dem Befehlszeilenprogramm [sqlcmd](https://msdn.microsoft.com/library/azure/ms162773.aspx), das in SQL Server oder in [Microsoft Befehlszeilenprogramme 11 für SQL Server](http://go.microsoft.com/fwlink/?LinkId=321501) enthalten ist, eine Verbindung mit SQL Data Warehouse herstellen. Mit dem Hilfsprogramm sqlcmd können Sie Transact-SQL-Anweisungen, Systemprozeduren und Skriptdateien an der Eingabeaufforderung eingeben.

Für die Verbindung mit einer bestimmten Instanz von SQL Data Warehouse müssen Sie bei Verwendung von sqlcmd die Eingabeaufforderung öffnen und **sqlcmd** gefolgt von der Verbindungszeichenfolge für die SQL Data Warehouse-Datenbank eingeben. Die Verbindungszeichenfolge muss die folgenden Parameter enthalten:

+ **Benutzer (-U)**: Serverbenutzer in Form von `<`Benutzer`>`@`<`Servername`>`.database.windows.net.
+ **Kennwort (-P)**: Das Kennwort des Benutzers.
+ **Server (-S)**: Server in Form von `<`Servername`>`.database.windows.net.
+ **Datenbank (-D)**: Datenbankname.
+ **Bezeichner in Anführungszeichen aktivieren (-I)**: Bezeichner müssen in Anführungszeichen eingeschlossen sein, um die Verbindung mit einer SQL Data Warehouse-Instanz herzustellen.

Zur Verbindung mit einer SQL Data Warehouse-Instanz geben Sie daher Folgendes ein:

```
C:\>sqlcmd -S <Server Name>.database.windows.net -d <Database> -U <User> -P <Password> -I
```

Nach dem Herstellen der Verbindung können Sie alle unterstützten Transact-SQL-Anweisungen für die Instanz ausgeben. Die folgende Anweisung nutzt beispielsweise die [CREATE TABLE](https://msdn.microsoft.com/library/azure/dn268335.aspx)-Anweisung zum Erstellen einer neuen Tabelle.

```
C:\>sqlcmd -S <Server Name>.database.windows.net -d <Database> -U <User> -P <Password> -I
1> CREATE TABLE table1 (Col1 int, Col2 varchar(20));
2> GO
3> QUIT
```

Weitere Informationen zu sqlcmd finden Sie in der [sqlcmd-Dokumentation](https://msdn.microsoft.com/library/azure/ms162773.aspx).

<!--NOTE: SQL DB does not support the -z/-Z parameters for changing users password with SQLCMD.  Do we support this? -->

## Durchführen von Beispielabfragen ##

Da wir jetzt unseren Server registriert haben, fahren wir mit dem Schreiben einer Abfrage fort.

1. Klicken Sie auf die Benutzerdatenbank in SSDT.

2. Klicken Sie auf die Schaltfläche **Neue Abfrage**. Ein neues Fenster wird geöffnet.

3. Schreiben Sie eine Abfrage. Geben Sie den folgenden Code in das Abfragefenster ein.

	```
	SELECT  COUNT(*)
	FROM    sys.tables;
	```

4. Führen Sie die Abfrage aus.

	Zum Ausführen der Abfrage klicken Sie unten auf den grünen Pfeil, oder Sie verwenden die folgende Tastenkombination: `CTRL`+`SHIFT`+`F5`.

## Nächste Schritte ##
Versuchen Sie nun, da Sie eine Verbindung herstellen und Abfragen senden können, [das Laden von Beispieldaten][].

[das Laden von Beispieldaten]: ./sql-data-warehouse-get-started-load-samples.md
[developing code]: ./articles/sql-data-warehouse-overview-develop/

<!---HONumber=Oct15_HO2-->