<properties 
	pageTitle="Herstellen von Verbindungen mit SQL-Datenbanken mithilfe von Ruby mit TinyTDS unter Ubuntu" 
	description="Geben Sie ein Ruby-Codebeispiel, das wie ein Client unter Ubuntu Linux zum Herstellen einer Verbindung mit Azure SQL-Datenbanken ausgeführt werden kann."
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
	ms.date="09/21/2015" 
	ms.author="andrela"/>


# Herstellen von Verbindungen mit SQL-Datenbanken mithilfe von Ruby unter Ubuntu Linux

[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

Dieses Thema zeigt ein Ruby-Codebeispiel, das auf einem Ubuntu Linux-Clientcomputer ausgeführt wird, um eine Verbindung mit einer Azure SQL-Datenbank herzustellen.

## Installieren der erforderlichen Module

Öffnen Sie das Terminal und installieren Sie FreeTDS, wenn Sie es nicht auf Ihrem Computer haben.
	
    sudo apt-get --assume-yes update 
    sudo apt-get --assume-yes install freetds-dev freetds-bin

Nachdem Ihr Computer mit FreeTDS konfiguriert ist, installieren Sie Ruby, wenn Sie es nicht auf Ihrem Computer haben.
    
    sudo apt-get install libgdbm-dev libncurses5-dev automake libtool bison libffi-dev 
    curl -L https://get.rvm.io | bash -s stable

Wenn Sie Probleme mit Signaturen haben, führen Sie den folgenden Befehl aus.

    command curl -sSL https://rvm.io/mpapis.asc | gpg --import - 

Wenn keine Probleme mit Signaturen vorliegen, führen Sie die folgenden Befehle aus.

    source ~/.rvm/scripts/rvm 
    rvm install 2.1.2 
    rvm use 2.1.2 --default 
    ruby -v 

Stellen Sie sicher, dass Sie Version 2.1.2 ausführen oder den virtuellen Ruby-Computer.

Installieren Sie anschließend TinyTDS.

    gem install tiny_tds

## Erstellen Sie eine Datenbank und rufen Sie die Verbindungszeichenfolge ab.

Das Ruby-Beispiel basiert auf der AdventureWorks-Beispieldatenbank. Wenn Sie nicht bereits über AdventureWorks verfügen, können Sie in folgendem Thema nachlesen, wie es erstellt wird:[Erstellen Ihrer ersten Azure SQL-Datenbank](sql-database-get-started.md).

Im Thema wird auch erklärt, wie die Verbindungszeichenfolge der Datenbank abgerufen wird.

## Herstellen von Verbindungen mit der SQL-Datenbank

Die Funktion [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) dient zum Verbinden mit einer SQL-Datenbank.

    require 'tiny_tds' 
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword', 
    host: 'yourserver.database.windows.net', port: 1433, 
    database: 'AdventureWorks', azure:true 

## Ausführen einer SELECT-Anweisung und Abrufen des Resultsets

Die Funktion [TinyTds::Result](https://github.com/rails-sqlserver/tiny_tds) wird verwendet, um ein Resultset aus einer Abfrage in einer SQL-Datenbank abzurufen. Diese Funktion akzeptiert eine Abfrage und gibt ein Resultset zurück. Das Resultset läuft mithilfe von [result.each führen |row|](https://github.com/rails-sqlserver/tiny_tds) durch.

    require 'tiny_tds'  
    print 'test'     
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword', 
    host: 'yourserver.database.windows.net', port: 1433, 
    database: 'AdventureWorks', azure:true 
    results = client.execute("select * from SalesLT.Product") 
    results.each do |row| 
    puts row 
    end 

## Einfügen von Zeilen, Übergeben von Parametern und Abrufen der generierten Primärschlüsselwerte

Das Codebeispiel:

- Übergibt Parameter für die Werte, die in eine Zeile eingefügt werden sollen.
- Fügt die Zeile ein.
- Ruft den Wert ab, der für den Primärschlüssel generiert wurde.

In SQL-Datenbanken können die [IDENTITY](http://msdn.microsoft.com/library/ms186775.aspx)-Eigenschaft und das [SEQUENCE](http://msdn.microsoft.com/library/ff878058.aspx)-Objekt zum automatischen Generieren von Werten für [Primärschlüssel](http://msdn.microsoft.com/library/ms179610.aspx) verwendet werden.

Bei der Verwendung von TinyTDS mit Azure wird empfohlen, dass Sie mehrere `SET`-Anweisungen ausführen, damit geändert werden kann, wie die aktuelle Sitzung spezifische Informationen behandelt. Empfohlene `SET`-Anweisungen werden im Codebeispiel bereitgestellt. Beispielsweise ermöglicht `SET ANSI_NULL_DFLT_ON` das Erstellen neuer Spalten zum Zulassen von Null-Werten, selbst wenn der NULL-Zulässigkeitsstatus der Spalte nicht explizit angegeben ist.

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

<!---HONumber=Nov15_HO2-->