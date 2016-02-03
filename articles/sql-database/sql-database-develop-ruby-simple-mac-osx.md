<properties
	pageTitle="Herstellen einer Verbindung mit der SQL-Datenbank mithilfe von Ruby mit TinyTDS unter Mac OS X (Yosemite)"
	description="Geben Sie ein Ruby-Codebeispiel an, das unter Mac OS X (Yosemite) zum Herstellen einer Verbindung mit Azure SQL-Datenbank ausgeführt werden kann."
	services="sql-database"
	documentationCenter=""
	authors="ajlam"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="sql-database"
	ms.tgt_pltfrm="na"
	ms.devlang="ruby"
	ms.topic="article"
	ms.date="12/17/2015"
	ms.author="andrela"/>


# Herstellen einer Verbindung mit SQL-Datenbank mithilfe von Ruby unter Mac OS X (Yosemite)


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Dieses Thema enthält ein Ruby-Codebeispiel, das auf einem Macintosh-Computer mit Yosemite für die Verbindung mit einer Datenbank in Azure SQL-Datenbank ausgeführt wird.

## Voraussetzungen

### Installieren der erforderlichen Module

Öffnen Sie das Terminal, und installieren Sie Folgendes:

**1) Homebrew**: Führen Sie den folgenden Befehl über das Terminal aus. Damit wird der Homebrew-Paket-Manager auf Ihren Computer heruntergeladen.

    ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

**2) FreeTDS:** Führen Sie den folgenden Befehl über das Terminal aus. Dadurch wird FreeTDS, das für das Funktionieren von TinyTDS erforderlich ist, auf Ihrem Computer installiert.

    brew install FreeTDS

**3) TinyTDS:** Führen Sie den folgenden Befehl über das Terminal aus. Dadurch wird TinyTDS auf Ihrem Computer installiert.

    gem install tiny_tds

### Eine SQL-Datenbank

Auf der [Seite für erste Schritte](sql-database-get-started.md) erhalten Sie Informationen zum Erstellen einer Beispieldatenbank. Sie sollten unbedingt die Anleitung zum Erstellen einer **AdventureWorks-Datenbankvorlage** befolgen. Die unten gezeigten Beispiele funktionieren nur mit dem **AdventureWorks-Schema**.


## Schritt 1: Abrufen der Verbindungsdetails

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## Schritt 2: Verbinden

Die [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds)-Funktion dient zum Verbinden mit einer SQL-Datenbank.

    require 'tiny_tds'
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword',
    host: 'yourserver.database.windows.net', port: 1433,
    database: 'AdventureWorks', azure:true

## Schritt 3: Ausführen einer Abfrage

Die Funktion [TinyTds::Result](https://github.com/rails-sqlserver/tiny_tds) wird verwendet, um ein Resultset aus einer Abfrage in einer SQL-Datenbank abzurufen. Diese Funktion akzeptiert eine Abfrage und gibt ein Resultset zurück. Das Resultset läuft mithilfe von [result.each do |row|](https://github.com/rails-sqlserver/tiny_tds) durch.

    require 'tiny_tds'  
    print 'test'     
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword',
    host: 'yourserver.database.windows.net', port: 1433,
    database: 'AdventureWorks', azure:true
    results = client.execute("select * from SalesLT.Product")
    results.each do |row|
    puts row
    end

## Schritt 4: Einfügen einer Zeile

In diesem Beispiel erfahren Sie, wie Sie eine [INSERT](https://msdn.microsoft.com/library/ms174335.aspx)-Anweisung sicher ausführen, Parameter zum Schutz Ihrer Anwendung vor einer [Einschleusung von SQL-Befehlen](https://technet.microsoft.com/library/ms161953(v=sql.105).aspx) übergeben und den automatisch generierten [Primärschlüsselwert](https://msdn.microsoft.com/library/ms179610.aspx) abrufen.


Bei der Verwendung von TinyTDS mit Azure wird empfohlen, dass Sie mehrere `SET`-Anweisungen ausführen, um zu ändern, wie die aktuelle Sitzung bestimmte Informationen behandelt. Empfohlene `SET`-Anweisungen werden im Codebeispiel bereitgestellt. Beispielsweise ermöglicht `SET ANSI_NULL_DFLT_ON` das Erstellen neuer Spalten zum Zulassen von NULL-Werten, selbst wenn der NULL-Zulässigkeitsstatus der Spalte nicht explizit angegeben ist.

Zum Ausrichten am [Datetime](http://msdn.microsoft.com/library/ms187819.aspx)-Format in Microsoft SQL Server verwenden Sie die Funktion [strftime](http://ruby-doc.org/core-2.2.0/Time.html#method-i-strftime), um in das entsprechende Datetime-Format umzuwandeln.

    require 'tiny_tds'
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword',
    host: 'yourserver.database.windows.net', port: 1433,
    database: 'AdventureWorks', azure:true
    results = client.execute("SET ANSI_NULLS ON")
    results = client.execute("SET CURSOR_CLOSE_ON_COMMIT OFF")
    results = client.execute("SET ANSI_NULL_DFLT_ON ON")
    results = client.execute("SET IMPLICIT_TRANSACTIONS OFF")
    results = client.execute("SET ANSI_PADDING ON")
    results = client.execute("SET QUOTED_IDENTIFIER ON")
    results = client.execute("SET ANSI_WARNINGS ON")
    results = client.execute("SET CONCAT_NULL_YIELDS_NULL ON")
    require 'date'
    t = Time.now
    curr_date = t.strftime("%Y-%m-%d %H:%M:%S.%L")
    results = client.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate)
    OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New', 'SQLEXPRESS New', 0, 0, '#{curr_date}' )")
    results.each do |row|
    puts row
    end

<!---HONumber=AcomDC_0107_2016-->