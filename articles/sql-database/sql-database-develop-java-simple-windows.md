<properties 
	pageTitle="Herstellen von Verbindungen mit der SQL-Datenbank mithilfe von Java mit JDBC unter Windows" 
	description="Zeigt ein Java-Codebeispiel zum Herstellen einer Verbindung mit Azure SQL-Datenbank. Das Beispiel verwendet JDBC und wird auf einem Clientcomputer unter Windows ausgeführt."
	services="sql-database" 
	documentationCenter="" 
	authors="LuisBosquez" 
	manager="jeffreyg" 
	editor="genemi"/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="05/20/2015" 
	ms.author="lbosq"/>


# Herstellen von Verbindungen mit der SQL-Datenbank mithilfe von Java mit JDBC unter Windows


Dieses Thema enthält ein Java-Codebeispiel zum Herstellen einer Verbindung mit Azure SQL-Datenbank. Das Java-Beispiel beruht auf dem Java Development Kit (JDK), Version 1.8. Das Beispiel stellt mithilfe des JDBC-Treibers eine Verbindung mit Azure SQL-Datenbank her.


## Anforderungen


- [Microsoft JDBC-Treiber für SQL Server – SQL JDBC 4](http://www.microsoft.com/download/details.aspx?displaylang=en&id=11774).
- Eine Betriebssystemplattform, auf der [Java Development Kit 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) ausgeführt wird.
- Eine bestehende Datenbank auf Azure SQL. Im Thema [Erste Schritte](sql-database-get-started.md) erhalten Sie Informationen zum Erstellen einer Beispieldatenbank und zum Abrufen der Verbindungszeichenfolge.


## Testumgebung


Beim Java-Codebeispiel in diesem Thema wird davon ausgegangen, dass die folgende Testtabelle in der Azure SQL-Datenbank bereits enthalten ist.


<!--
Could this instead be a #tempPerson table, so that the Java code sample could be fully self-sufficient and be runnable (with automatic cleanup)?
-->


	CREATE TABLE Person
	(
		id         INT    PRIMARY KEY    IDENTITY(1,1),
		firstName  VARCHAR(32),
		lastName   VARCHAR(32),
		age        INT
	);


## Verbindungszeichenfolge für die SQL-Datenbank


Das Codebeispiel erstellt mithilfe einer Verbindungszeichenfolge ein `Connection`-Objekt. Die Verbindungszeichenfolge finden Sie mithilfe des [Azure-Vorschauportals](http://portal.azure.com/). Weitere Informationen zum Ermitteln der Verbindungszeichenfolge finden Sie unter [Erstellen der ersten Azure SQL-Datenbank](sql-database-get-started.md).


## Java-Codebeispiel


Der Abschnitt enthält den größten Teil des Java-Codebeispiels. Die enthaltenen Kommentare geben an, wohin Sie die kleineren Java-Ausschnitte aus den folgenden Abschnitten kopieren. Das Beispiel in diesem Abschnitt kann auch ohne Kopieren und Einfügen in der Nähe der Kommentare kompiliert und ausgeführt werden. In diesem Fall würde aber nur eine Verbindung hergestellt und wieder getrennt werden. Folgende Kommentare sind enthalten:


1. `// INSERT two rows into the table.`
2. `// TRANSACTION and commit for an UPDATE.`
3. `// SELECT rows from the table.`


Es folgt der Hauptteil des Java-Codebeispiels. Das Beispiel umfasst die `main`-Funktion der `SQLDatabaseTest`-Klasse.


	import java.sql.*;
	import com.microsoft.sqlserver.jdbc.*;
	
	public class SQLDatabaseTest {
	
		public static void main(String[] args) {
			String connectionString =
				"jdbc:sqlserver://your_server.database.windows.net:1433;" 
				+ "database=your_database;"
				+ "user=your_user@your_server;"
				+ "password={your_password};"
				+ "encrypt=true;"
				+ "trustServerCertificate=false;"
				+ "hostNameInCertificate=*.database.windows.net;"
				+ "loginTimeout=30;"; 
	
			// Declare the JDBC objects.
			Connection connection = null;
			Statement statement = null;
			ResultSet resultSet = null;
			PreparedStatement prepsInsertPerson = null;
			PreparedStatement prepsUpdateAge = null;
	
			try {
				connection = DriverManager.getConnection(connectionString);
	
				// INSERT two rows into the table.
				// ...
	
				// TRANSACTION and commit for an UPDATE.
				// ...
	
				// SELECT rows from the table.
				// ...
			}
			catch (Exception e) {
				e.printStackTrace();
			}
			finally {
				// Close the connections after the data has been handled.
				if (prepsInsertPerson != null) try { prepsInsertPerson.close(); } catch(Exception e) {}
				if (prepsUpdateAge != null) try { prepsUpdateAge.close(); } catch(Exception e) {}
				if (resultSet != null) try { resultSet.close(); } catch(Exception e) {}
				if (statement != null) try { statement.close(); } catch(Exception e) {}
				if (connection != null) try { connection.close(); } catch(Exception e) {}
			}
		}
	}


Um das gezeigte Java-Codebeispiel tatsächlich auszuführen, müssen Sie natürlich auch die tatsächlichen Werte in die Verbindungszeichenfolge einfügen und die folgenden Platzhalter ersetzen:


- your_server
- your_database
- your_user
- your_password


## INSERT two rows into the table (Einfügen von zwei Zeilen in die Tabelle mithilfe einer INSERT-Anweisung)


Dieser Java-Codeausschnitt übergibt eine Transact-SQL-INSERT-Anweisung für das Einfügen von zwei Zeilen in die Tabelle "Person". Die allgemeine Abfolge lautet wie folgt:


1. Generieren eines `PreparedStatement`-Objekts mithilfe des `Connection`-Objekts.
 - Wir fügen den Parameter `Statement.RETURN_GENERATED_KEYS` ein, um damit später den automatisch generierten **id**-Schlüsselwert abzurufen.
2. Rufen Sie die `execute`-Methode für das `PreparedStatement`-Objekt auf.
3. Rufen Sie mithilfe des `PreparedStatement`-Objekts den numerischen Wert ab, der automatisch für den Primärschlüssel generiert wurde.
 - Dies bezieht sich auf die AUTO_INCREMENT-Spezifikation in der **id**-Spalte in der Tabelle "Person"


Kopieren Sie diesen kurzen Java-Codeausschnitt, und fügen Sie ihn beim Kommentar `// INSERT two rows into the table.` in das Hauptcodebeispiel ein.


	// Create and execute an INSERT SQL prepared statement.
	String insertSql = "INSERT INTO Person (firstName, lastName, age) VALUES "
		+ "('Bill', 'Gates', 59), "
		+ "('Steve', 'Ballmer', 59);";
	
	prepsInsertPerson = connection.prepareStatement(
		insertSql,
		Statement.RETURN_GENERATED_KEYS);
	prepsInsertPerson.execute();
	// Retrieve the generated key from the insert.
	resultSet = prepsInsertPerson.getGeneratedKeys();
	// Iterate through the set of generated keys.
	while (resultSet.next()) {
		System.out.println("Generated: " + resultSet.getString(1));
	}


## TRANSACTION and commit for an UPDATE (TRANSACTION und COMMIT für ein UPDATE)


Der folgende Java-Codeausschnitt übergibt eine Transact-SQL-UPDATE-Anweisung zum Erhöhen des `age`-Werts für jede Zeile in der Tabelle "Person". Die allgemeine Abfolge lautet wie folgt:


1. Die `setAutoCommit`-Methode wird aufgerufen, um Updates per automatischem Commit in der Datenbank zu verhindern.
2. Die `executeUpdate`-Methode wird aufgerufen, um das UPDATE im Kontext der Transaktion auszuführen.
3. Die `commit`-Methode wird aufgerufen, um explizit einen Commit für die Transaktion auszuführen.


Kopieren Sie diesen kurzen Java-Codeausschnitt, und fügen Sie ihn beim Kommentar `// TRANSACTION and commit for an UPDATE.` in das Hauptcodebeispiel ein.


	// Set AutoCommit value to false to execute a single transaction at a time.
	connection.setAutoCommit(false);
	
	// Write the SQL Update instruction and get the PreparedStatement object.
	String transactionSql = "UPDATE Person SET Person.age = Person.age + 1;";
	prepsUpdateAge = connection.prepareStatement(transactionSql);
	
	// Execute the statement.
	prepsUpdateAge.executeUpdate();
	
	//Commit the transaction.
	connection.commit();
	
	// Return the AutoCommit value to true.
	connection.setAutoCommit(true);


## SELECT rows from a table (Auswählen von Zeilen in einer Tabelle)


Dieser Java-Codeausschnitt führt eine Transact-SQL-SELECT-Anweisung aus, um alle aktualisierten Zeilen aus der Tabelle "Person" anzuzeigen. Die allgemeine Abfolge lautet wie folgt:


1. Generieren eines `Statement`-Objekts mithilfe des `Connection`-Objekts.
2. Generieren eines `ResultSet`-Objekts mithilfe des `Statement`-Objekts.
3. Schleife um einen Aufruf von `resultSet.next` zum Durchlaufen aller zurückgegebenen Zeilen.


Kopieren Sie diesen kurzen Java-Codeausschnitt, und fügen Sie ihn beim Kommentar `// SELECT rows from a table.` in das Hauptcodebeispiel ein.


	// Create and execute a SELECT SQL statement.
	String selectSql = "SELECT firstName, lastName, age FROM dbo.Person";
	statement = connection.createStatement();
	resultSet = statement.executeQuery(selectSql);
	
	// Iterate through the result set and print the attributes.
	while (resultSet.next()) {
		System.out.println(resultSet.getString(2) + " "
			+ resultSet.getString(3));
	}

<!---HONumber=58--> 