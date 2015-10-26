<properties 
	pageTitle="Herstellen von Verbindungen mit Azure SQL-Datenbanken mithilfe von Ruby mit TinyTDS unter Windows" 
	description="Geben Sie ein Ruby-Codebeispiel an, das unter Windows zum Herstellen einer Verbindung mit Azure SQL-Datenbank ausgeführt werden kann."
	services="sql-database" 
	documentationCenter="" 
	authors="meet-bhagdev" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="08/04/2015" 
	ms.author="mebha"/>


# Herstellen von Verbindungen mit SQL-Datenbanken mithilfe von Ruby unter Windows

[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

Dieses Thema enthält ein Ruby-Codebeispiel, das auf einem Windows-Computer mit Windows 8.1 für die Verbindung mit einer Datenbank in Azure SQL-Datenbank ausgeführt wird.

## Installieren der erforderlichen Module

Öffnen Sie das Terminal, und installieren Sie Folgendes:

**1) Ruby:** Falls Ruby auf Ihrem Computer nicht vorhanden ist, installieren Sie es. Neuen Benutzern von Ruby empfehlen wir, die Ruby 2.1.X-Installationsprogramme zu verwenden. Diese bieten eine stabile Sprache und eine umfassende Liste von Paketen (Gems), die kompatibel sind und aktualisiert werden. [Wechseln Sie zur Ruby-Downloadseite](http://rubyinstaller.org/downloads/), und laden Sie das entsprechende 2.1.x-Installationsprogramm herunter. Wenn Sie beispielsweise einen 64-Bit-Computer nutzen, laden Sie das Installationsprogrammr **Ruby 2.1.6 (x 64)** herunter. <br/><br/>Nachdem das Installationsprogramm heruntergeladen wurde, führen Sie folgende Schritte aus:


- Doppelklicken Sie auf die Datei, um das Installationsprogramm zu starten.

- Wählen Sie Ihre Sprache aus, und akzeptieren Sie die Nutzungsbedingungen.

- Aktivieren Sie auf dem Bildschirm mit den Installationseinstellungen die Kontrollkästchen neben *Add Ruby executables to your PATH* und *Associate .rb and .rbw files with this Ruby installation*.


**2) DevKit:** Laden Sie DevKit von der Seite [RubyInstaller](http://rubyinstaller.org/downloads/) herunter.

Nachdem der Download abgeschlossen ist, führen Sie folgende Schritte aus:


- Doppelklicken Sie auf die Datei. Sie werden gefragt, wo Sie die Dateien extrahieren möchten.

- Klicken Sie auf die Schaltfläche "...", und wählen Sie "C:\\DevKit" aus. Sie müssen wahrscheinlich diesen Ordner zunächst erstellen, indem Sie auf "Neuen Ordner erstellen" klicken.

- Klicken Sie auf "OK" und dann auf "Extrahieren", um die Dateien zu extrahieren.


Öffnen Sie dann eine Eingabeaufforderung, und geben Sie die folgenden Befehle ein:

	> chdir C:\DevKit
	> ruby dk.rb init
	> ruby dk.rb install

Sie verfügen jetzt über ein voll funktionsfähiges Ruby und RubyGems!


**3) TinyTDS:** Navigieren Sie zu "C:\\DevKit", und führen Sie den folgenden Befehl in Ihrem Terminal aus. Dadurch wird TinyTDS auf Ihrem Computer installiert.

	gem inst tiny_tds --pre

## Erstellen einer Datenbank und Abrufen der Verbindungszeichenfolge

Das Ruby-Beispiel basiert auf der `AdventureWorks`-Beispieldatenbank. Wenn Sie nicht bereits über `AdventureWorks` verfügen, können Sie in folgendem Thema nachlesen, wie es erstellt wird:[Erstellen Ihrer ersten Azure SQL-Datenbank](sql-database-get-started.md).

Im Thema wird auch erklärt, wie die Verbindungszeichenfolge der Datenbank abgerufen wird.

## Herstellen von Verbindungen mit der SQL-Datenbank

Die [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds)-Funktion dient zum Verbinden mit einer SQL-Datenbank.

    require 'tiny_tds' 
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword', 
    host: 'yourserver.database.windows.net', port: 1433, 
    database: 'AdventureWorks', azure:true 

## Ausführen einer SELECT-Anweisung und Abrufen des Resultsets

Kopieren Sie den folgenden Code, und fügen Sie ihn in eine leere Datei ein. Nennen Sie sie "test.rb". Führen Sie sie anschließend durch Eingeben des folgenden Befehls über die Befehlszeile aus:

	ruby test.rb

Im Codebeispiel wird die Funktion [TinyTds::Result](https://github.com/rails-sqlserver/tiny_tds) verwendet, um ein Resultset aus einer Abfrage in einer SQL-Datenbank abzurufen. Diese Funktion akzeptiert eine Abfrage und gibt ein Resultset zurück. Das Resultset läuft mithilfe von [result.each do |row|](https://github.com/rails-sqlserver/tiny_tds) durch.

    require 'tiny_tds'  
    print 'test'     
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword', 
    host: 'yourserver.database.windows.net', port: 1433, 
    database: 'AdventureWorks', azure:true 
    results = client.execute("SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC") 
    results.each do |row| 
    puts row 
    end 

## Einfügen von Zeilen, Übergeben von Parametern und Abrufen der generierten Primärschlüsselwerte

Das Codebeispiel:

- Übergibt Parameter für die Werte, die in eine Zeile eingefügt werden sollen.
- Fügt die Zeile ein.
- Ruft den Wert ab, der für den Primärschlüssel generiert wurde.

In Azure SQL-Datenbanken können die [IDENTITY](http://msdn.microsoft.com/library/ms186775.aspx)-Eigenschaft und das [SEQUENCE](http://msdn.microsoft.com/library/ff878058.aspx)-Objekt zum automatischen Generieren von Werten für [Primärschlüssel](http://msdn.microsoft.com/library/ms179610.aspx) verwendet werden.

Bei der Verwendung von TinyTDS mit Azure wird empfohlen, dass Sie mehrere `SET`-Anweisungen ausführen, um zu ändern, wie die aktuelle Sitzung bestimmte Informationen behandelt. Empfohlene `SET`-Anweisungen werden im Codebeispiel bereitgestellt. Beispielsweise ermöglicht `SET ANSI_NULL_DFLT_ON` das Erstellen neuer Spalten zum Zulassen von NULL-Werten, selbst wenn der NULL-Zulässigkeitsstatus der Spalte nicht explizit angegeben ist.

Zum Ausrichten am [Datetime](http://msdn.microsoft.com/library/ms187819.aspx)-Format in Microsoft SQL Server verwenden Sie die [strftime](http://ruby-doc.org/core-2.2.0/Time.html#method-i-strftime)-Funktion, um in das entsprechende "Datetime"-Format umzuwandeln.

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

<!---HONumber=Oct15_HO3-->