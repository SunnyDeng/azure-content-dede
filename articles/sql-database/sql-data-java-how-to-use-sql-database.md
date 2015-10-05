<properties 
	pageTitle="Verwenden Azure SQL (Java) | Microsoft Azure" 
	description="Erfahren Sie, wie Sie eine Azure SQL-Datenbank mit Java-Code verwenden." 
	services="sql-database" 
	documentationCenter="java" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor="jimbe"/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="09/22/2015" 
	ms.author="genemi"/>

# Verwenden der Azure SQL-Datenbank in Java

Die folgenden Schritte zeigen Ihnen, wie Sie die Azure SQL-Datenbank mit Java verwenden können. Zur Vereinfachung werden Befehlszeilenbeispiele gegeben, aber für Webanwendungen (lokal, in Azure oder in anderen Umgebungen gehostet) können beinahe dieselben Schritte verwendet werden. Dieser Leitfaden beschreibt das Erstellen eines Servers und einer Datenbank im [Azure-Verwaltungsportal](https://windows.azure.com).

## Was ist Azure SQL-Datenbank?

Azure SQL-Datenbank bietet ein Verwaltungssystem für relationale Datenbanken für Azure und basiert auf SQL Server-Technologie. Mit einer SQL-Datenbankinstanz können Sie relationale Datenbanklösungen für die Cloud bereitstellen und implementieren, und Sie profitieren von einem verteilten Datencenter, das Verfügbarkeit, Skalierbarkeit und Sicherheit für Unternehmen mit den Vorteilen von integriertem Datenschutz und Selbstreparatur bietet.



## Konzepte
Da Azure SQL-Datenbank auf SQL Server-Technologien basiert, erfolgt der Zugriff auf SQL-Datenbank aus Java ganz ähnlich wie der Zugriff auf SQL Server aus Java. Sie können eine Anwendung lokal (mit SQL Server) entwickeln und dann eine Verbindung zu SQL-Datenbank herstellen, indem Sie lediglich die Verbindungszeichenfolge ändern. Sie können einen SQL Server JDBC-Treiber für Ihre Anwendung verwenden. Es gibt jedoch einige Unterschiede zwischen SQL-Datenbank und SQL Server, die sich auf Ihre Anwendung auswirken können. Weitere Informationen finden Sie unter [Richtlinien für und Einschränkungen (SQL-Datenbanken)](http://msdn.microsoft.com/library/windowsazure/ff394102.aspx).

Weitere Informationen zu SQL-Datenbank finden Sie im Abschnitt [Nächste Schritte][].

## Voraussetzungen

In den folgenden Schritten werden die Voraussetzungen für die Verwendung von SQL-Datenbank in Java beschrieben.

* Ein Java Developer Kit (JDK), Version 1.6 oder höher.
* Ein Azure-Abonnement, das von <http://www.microsoft.com/windowsazure/offers/> bezogen werden kann.
* Falls Sie Eclipse verwenden, brauchen Sie die Eclipse IDE für Java EE-Entwickler, Indigo oder höher. Dies kann von <http://www.eclipse.org/downloads/> heruntergeladen werden. Außerdem brauchen Sie das Azure-Plug-In für Eclipse mit Java (von Microsoft Open Technologies). Stellen Sie bei der Installation des Plug-Ins sicher, dass der Microsoft JDBC-Treiber 4.0 für SQL Server enthalten ist. Weitere Informationen finden Sie unter [Installieren des Azure-Plug-Ins für Eclipse mit Java (von Microsoft Open Technologies)](http://msdn.microsoft.com/library/windowsazure/hh690946.aspx).
* Wenn Sie nicht mit Eclipse arbeiten, benötigen Sie den Microsoft JDBC-Treiber 4.0 für SQL Server, den Sie von <http://www.microsoft.com/download/details.aspx?id=11774> herunterladen können.

## Erstellen einer Azure SQL-Datenbank

Bevor Sie Azure SQL-Datenbank in Ihrem Java-Code verwenden können, müssen Sie einen Azure SQL-Datenbankserver erstellen.

1. Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com) an.
2. Klicken Sie auf **Neu**.

    ![Neue SQL-Datenbank erstellen][create_new]

3. Klicken Sie auf **SQL-Datenbank** und dann auf **Benutzerdefiniert erstellen**.

    ![Benutzerdefinierte SQL-Datenbank erstellen][create_new_sql_db]

4. Geben Sie im Dialogfeld **Datenbankeinstellungen** den Namen Ihrer Datenbank an. In diesem Lernprogramm wird **gettingstarted** als Datenbankname verwendet.
5. Wählen Sie im Feld **Server** die Option **New SQL Database Server**. Übernehmen Sie in den restlichen Feldern die Standardwerte.

    ![Einstellungen der SQL-Datenbank][create_database_settings]

6. Klicken Sie auf den Weiter-Pfeil.
7. Geben Sie im Dialogfeld **Servereinstellungen** einen Anmeldenamen für den SQL Server an. In diesem Lernprogramm wird **MySQLAdmin** verwendet. Geben Sie ein Kennwort ein und bestätigen Sie es anschließend. Wählen Sie eine Region aus und stellen Sie sicher, dass das Kontrollkästchen **Ermöglichen Sie Windows Azure-Diensten den Zugriff auf den Server** aktiviert ist.

    ![Einstellungen des SQL Servers][create_server_settings]

8. Klicken Sie auf die Schaltfläche zum Fertigstellen.

## Bestimmung der Verbindungszeichenfolge für die SQL-Datenbank

1. Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com) an.
2. Klicken Sie auf **SQL Databases**.
3. Klicken Sie die gewünschte Datenbank an.
4. Klicken Sie auf **Show connection strings**.
5. Markieren Sie den Inhalt de **JDBC**-Verbindungszeichenfolge.

    ![Ermitteln der JDBS-Verbindungszeichenfolge][get_jdbc_connection_string]

6. Klicken Sie mit der rechten Maustaste auf den Inhalt der **JDBC**-Verbindungszeichenfolge und klicken Sie auf **Copy**.
7. Sie können diesen Wert nun in Ihrem Code einfügen, um eine Verbindungszeichenfolge mit dem folgenden Format zu erstellen. Ersetzen Sie *your\_server* (an zwei Stellen) durch den im vorherigen Schritt kopierten Text, und ersetzen Sie *your\_password* durch das Kennwort, das Sie bei der Erstellung Ihres SQL-Datenbank-Kontos festgelegt haben. (Ersetzen Sie außerdem die Werte **database=** und **user=**, falls Sie nicht **gettingstarted** und **MySQLAdmin** verwenden.) 

    String connectionString = "jdbc:sqlserver://*your_server*.database.windows.net:1433" + ";" + "database=gettingstarted" + ";" + "user=MySQLAdmin@*your\_server*" + ";" + "password=*your\_password*" + ";" + "encrypt=true" + ";" + "hostNameInCertificate=*.int.mscds.com" + ";" + "loginTimeout=30";

Wir werden diese Zeichenfolge später in dieser Anleitung verwenden. Momentan ist es nur wichtig, dass Sie mit der Bestimmung der Verbindungszeichenfolge vertraut sind. Je nach Anforderungen Ihrer Anwendung brauchen Sie die Einstellungen **encrypt** und **hostNameInCertificate** möglicherweise nicht. Außerdem können Sie die Einstellung **loginTimeout** ändern.

## Erlauben eines Bereichs zulässiger IP-Adressen

1. Melden Sie sich am [Verwaltungsportal](https://manage.windowsazure.com) an.
2. Klicken Sie auf **SQL Databases**.
3. Klicken Sie auf **Servers**.
4. Klicken Sie den gewünschten Server an.
5. Klicken Sie auf **Manage**.
6. Klicken Sie auf **Konfigurieren**.
7. Geben Sie unter **Allowed IP addresses** einen Namen für die neue IP-Regel ein. Geben Sie Anfang und Ende des IP-Adressbereichs ein. Die IP-Adresse des aktuellen Clients wird hier ebenfalls angezeigt. Das folgende Beispiel erlaubt nur eine einzige Client-IP-Adresse (Sie haben eine andere IP-Adresse).

    ![Dialogfeld "Zulässige IP-Adressen"][allowed_ips_dialog]

8. Klicken Sie auf die Schaltfläche zum Fertigstellen. Die angegebenen IP-Adressen können nun auf Ihren Datenbankserver zugreifen.

## Verwenden der Azure SQL-Datenbank in Java

1. Erstellen Sie ein Java-Projekt. Nennen Sie das Projekt für dieses Lernprogramm **HelloSQLAzure**.
2. Fügen Sie eine Java-Klassendatei namens **HelloSQLAzure.java** zum Projekt hinzu.
3. Fügen Sie den **Microsoft JDBC-Treiber für SQL Server** zu Ihrem Buildpfad hinzu.

   Falls Sie Eclipse verwenden:

    1. Within Eclipse's Project Explorer, right-click the **HelloSQLAzure** project and click **Properties**.
    2. In the left-hand pane of the **Properties** dialog, click **Java Build Path**.
    3. Click the **Libraries** tab, and then click **Add Library**.
    4. In the **Add Library** dialog, select **Microsoft JDBC Driver 4.0 for SQL Server**, click **Next**, and then click **Finish**.
    5. Click **OK** to close the **Properties** dialog.

    If you are not using Eclipse, add the Microsoft JDBC Driver 4.0 for SQL Server JAR to your class path. For related information, see [Using the JDBC Driver](http://msdn.microsoft.com/library/ms378526.aspx).

4. Fügen Sie in Ihrem Code **HelloSQLAzure.java** `import` Anweisungen hinzu, wie nachfolgend dargestellt:

        import java.sql.*;
        import com.microsoft.sqlserver.jdbc.*;

5. Geben Sie die Verbindungszeichenfolge an. Dies ist ein Beispiel. Ersetzen Sie wie oben *your\_server* (an zwei Stellen), *your\_user* und *your\_password* mit den entsprechenden Werten für Ihren SQL-Datenbankserver.

        String connectionString =
        	"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
        		"database=master" + ";" + 
        		"user=your_user@your_server" + ";" +  
        		"password=your_password";

Nun können Sie den Code hinzufügen, der mit Ihrem SQL-Datenbankserver kommunizieren wird.

## Kommunizieren mit der Azure SQL-Datenbank in Ihrem Code

Der Rest dieses Artikels enthält Beispiele für Folgendes:

1. Herstellen einer Verbindung mit dem SQL-Datenbankserver.
2. Definieren einer SQL-Anweisung, z. B. zum Erstellen oder Löschen einer Tabelle, Einfügen/Auswählen/Löschen von Zeilen usw.
3. Ausführen der SQL-Anweisung entweder durch Aufrufen von **executeUpdate** oder **executeQuery**.
4. Anzeigen der Abfrageergebnisse falls zutreffend.

Die folgenden Abschnitte sollten in der Reihenfolge gelesen werden. Der erste Codeabschnitt ist ein komplettes Beispiel; die restlichen Beispiele verwenden Teile des Frameworks im vollständigen Beispiel, z. B. die **import**-Anweisungen, **class** und **main** Deklarationen, Fehlerbehandlung und Schließen von Ressourcen.

## Erstellen einer Tabelle

Der folgende Code zeigt, wie Sie eine Tabelle mit dem Namen **Person** erstellen.

	import java.sql.*;
	import com.microsoft.sqlserver.jdbc.*;
	
	public class HelloSQLAzure {
	
	    public static void main(String[] args) 
	    {
	
			// Connection string for your SQL Database server.
			// Change the values assigned to your_server, 
			// your_user@your_server,
			// and your_password.
			String connectionString = 
				"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
					"database=gettingstarted" + ";" + 
					"user=your_user@your_server" + ";" +  
					"password=your_password";
			
			// The types for the following variables are
			// defined in the java.sql library.
			Connection connection = null;  // For making the connection
			Statement statement = null;    // For the SQL statement
			ResultSet resultSet = null;    // For the result set, if applicable
			
			try
			{
			    // Ensure the SQL Server driver class is available.
			    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
			
			    // Establish the connection.
			    connection = DriverManager.getConnection(connectionString);
			
			    // Define the SQL string.
			    String sqlString = 
					"CREATE TABLE Person (" + 
			        	"[PersonID] [int] IDENTITY(1,1) NOT NULL," +
			            "[LastName] [nvarchar](50) NOT NULL," + 
			            "[FirstName] [nvarchar](50) NOT NULL)";
			
			    // Use the connection to create the SQL statement.
			    statement = connection.createStatement();
			
			    // Execute the statement.
			    statement.executeUpdate(sqlString);
			
			    // Provide a message when processing is complete.
			    System.out.println("Processing complete.");
			
			}
			// Exception handling
	        catch (ClassNotFoundException cnfe)  
	        {
	            
	            System.out.println("ClassNotFoundException " +
	                               cnfe.getMessage());
	        }
	        catch (Exception e)
	        {
	            System.out.println("Exception " + e.getMessage());
	            e.printStackTrace();
	        }
	        finally
	        {
	            try
	            {
	                // Close resources.
	                if (null != connection) connection.close();
	                if (null != statement) statement.close();
	                if (null != resultSet) resultSet.close();
	            }
	            catch (SQLException sqlException)
	            {
	                // No additional action if close() statements fail.
	            }
	        }
	        
	    }
	
	}
	

## Erstellen eines Index in einer Tabelle

Der folgende Code zeigt, wie Sie einen Index mit dem Namen **index1** für die Tabelle **Person** erstellen und dabei die Spalte **PersonID** verwenden.

	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
			"database=gettingstarted" + ";" + 
			"user=your_user@your_server" + ";" +  
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	    // Define the SQL string.
	    String sqlString = 
			"CREATE CLUSTERED INDEX index1 " + "ON Person (PersonID)";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	
	    // Execute the statement.
	    statement.executeUpdate(sqlString);
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}
	// Exception handling and resource closing not shown...



## Einfügen von Zeilen

Der folgende Code zeigt, wie Sie Zeilen in die Tabelle **Person** einfügen.

	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
			"database=gettingstarted" + ";" + 
			"user=your_user@your_server" + ";" +  
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	    // Define the SQL string.
	    String sqlString = 
			"SET IDENTITY_INSERT Person ON " + 
	        	"INSERT INTO Person " + 
	            "(PersonID, LastName, FirstName) " + 
	            "VALUES(1, 'Abercrombie', 'Kim')," + 
	            	  "(2, 'Goeschl', 'Gerhard')," + 
	                  "(3, 'Grachev', 'Nikolay')," + 
	                  "(4, 'Yee', 'Tai')," + 
	                  "(5, 'Wilson', 'Jim')";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	
	    // Execute the statement.
	    statement.executeUpdate(sqlString);
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}
	// Exception handling and resource closing not shown...

 
## Abrufen von Zeilen

Der folgende Code zeigt, wie Sie Zeilen aus der Tabelle **Person** abrufen.

	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
			"database=gettingstarted" + ";" + 
			"user=your_user@your_server" + ";" +  
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	    // Define the SQL string.
	    String sqlString = "SELECT TOP 10 * FROM Person";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	
	    // Execute the statement.
	    resultSet = statement.executeQuery(sqlString);
	
	    // Loop through the results
	    while (resultSet.next())
	    {
	        // Print out the row data
	        System.out.println(
	        	"Person with ID " + 
	        	resultSet.getString("PersonID") + 
	        	" has name " +
	        	resultSet.getString("FirstName") + " " +
	       		resultSet.getString("LastName"));
	        }
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}
	// Exception handling and resource closing not shown...

 Der folgende Code zeigt, wie Sie die ersten 10 Zeilen aus der Tabelle **Person** abrufen. Verändern Sie die SQL-Anweisung wie folgt, um alle Zeilen zurückzugeben:

	String sqlString = "SELECT * FROM Person";

 
## Abrufen von Zeilen mit einer WHERE-Klausel

Erweitern Sie die SQL-Anweisung im oben gezeigten Code um eine Klausel, um Zeilen mit einer Klausel abzurufen. Die folgende SQL-Anweisung enthält eine Klausel für Zeilen, deren Wert in **FirstName** gleich **Jim** ist.

	// Define the SQL string.
	String sqlString = "SELECT * FROM Person WHERE FirstName='Jim'";
	
WHERE-Klauseln können auch beim Abrufen von Anzahlen sowie bei Änderungen und Löschungen von Zeilen verwendet werden.

## Abrufen einer Zeilenanzahl

Der folgende Code zeigt, wie Sie die Zeilenanzahl der Tabelle **Person** abrufen.
 
	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
			"database=gettingstarted" + ";" + 
			"user=your_user@your_server" + ";" +  
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	// Define the SQL string.
	    String sqlString = "SELECT COUNT (PersonID) FROM Person";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	
	    // Execute the statement.
	    resultSet = statement.executeQuery(sqlString);
	
	    // Print out the returned number of rows.
	    while (resultSet.next())
	    {
	        System.out.println("There were " + 
	                         resultSet.getInt(1) +
	                         " rows returned.");
	    }
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}
	// Exception handling and resource closing not shown...

## Aktualisieren von Zeilen

Der folgende Code zeigt, wie Sie Zeilen ändern können. In diesem Beispiel wird der Wert von **LastName** zu **Kim** geändert für alle Zeilen, deren Wert für **FirstName** gleich **Jim** ist.

	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
			"database=gettingstarted" + ";" + 
			"user=your_user@your_server" + ";" +  
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	    // Define the SQL string.
	    String sqlString = 
			"UPDATE Person " + "SET LastName = 'Kim' " + "WHERE FirstName='Jim'";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	    
	    // Execute the statement.
	    statement.executeUpdate(sqlString);
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}// Exception handling and resource closing not shown...

 

## Löschen von Zeilen

Der folgende Code zeigt, wie Sie Zeilen löschen können. Dieses Beispiel löscht alle Zeilen, deren Wert für **FirstName** gleich **Jim** ist.

	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
			"database=gettingstarted" + ";" + 
			"user=your_user@your_server" + ";" +  
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	    // Define the SQL string.
	    String sqlString = 
			"DELETE from Person " + 
				"WHERE FirstName='Jim'";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	
	    // Execute the statement.
	    statement.executeUpdate(sqlString);
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}
	// Exception handling and resource closing not shown...
	
 
## Prüfen, ob eine Tabelle vorhanden ist

Der folgende Code zeigt, wie Sie feststellen können, ob eine Tabelle existiert.

	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
			"database=gettingstarted" + ";" + 
			"user=your_user@your_server" + ";" +  
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	    // Define the SQL string.
	    String sqlString = 
			"IF EXISTS (SELECT 1 " +
	        	"FROM sysobjects " + 
	            "WHERE xtype='u' AND name='Person') " +
	            "SELECT 'Person table exists.'" +
	            "ELSE  " +
	            "SELECT 'Person table does not exist.'";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	
	    // Execute the statement.
	    resultSet = statement.executeQuery(sqlString);
	
	    // Display the result.
	    while (resultSet.next())
	    {
	        System.out.println(resultSet.getString(1));
	    }
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}
	// Exception handling and resource closing not shown...

## Löschen eines Index

Der folgende Code zeigt, wie Sie einen Index mit dem Namen **index1** für die Tabelle **Person** löschen können.

	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +
			"database=gettingstarted" + ";" +
			"user=your_user@your_server" + ";" +
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	    // Define the SQL string.
	    String sqlString = 
			"DROP INDEX index1 " + 
	        	"ON Person";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	
	    // Execute the statement.
	    statement.executeUpdate(sqlString);
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}
	// Exception handling and resource closing not shown...

 
## Löschen einer Tabelle

Der folgende Code zeigt, wie Sie eine Tabelle mit dem Namen **Person** löschen.

	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
			"database=gettingstarted" + ";" + 
			"user=your_user@your_server" + ";" +  
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	    // Define the SQL string.
	    String sqlString = "DROP TABLE Person";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	
	    // Execute the statement.
	    statement.executeUpdate(sqlString);
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}
	// Exception handling and resource closing not shown...

## Verwenden einer SQL-Datenbank in einer Azure-Bereitstellung

Um SQL-Datenbanken in Java mit einer Azure-Bereitstellung zu verwenden, müssen Sie nicht nur den Microsoft JDBC-Treiber 4.0 für SQL Server als Bibliothek in Ihren Classpath integrieren, sondern diesen auch mit Ihrer Bereitstellung verpacken.


**Verpacken des Microsoft JDBC-Treibers 4.0 für SQL Server, falls Sie Eclipse verwenden**

1. Klicken Sie im Projekt-Explorer in Eclipse mit der rechten Maustaste auf Ihr Projekt und anschließend auf **Properties**.
2. Klicken Sie im linken Bereich des **Properties**-Dialogfelds auf **Deployment Assembly** und anschließend auf **Add**.
3. Klicken Sie im Dialogfeld **New Assembly Directive** auf **Java Build Path Entries** und anschließend auf **Next**.
4. Wählen Sie **Microsoft JDBC Driver 4.0 for SQL Server** aus und klicken Sie auf **Finish**.
5. Klicken Sie auf **OK**, um das Dialogfeld **Properties** zu schließen.
6. Exportieren Sie die WAR-Datei Ihres Projekts in Ihren approot-Ordner und erstellen Sie Ihr Azure-Projekt neu. Siehe dazu die Schritte unter [Erstellen einer Hello World-Anwendung mithilfe des Azure-Plug-Ins für Eclipse with Java (von Microsoft Open Technologies)](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx). Dieser Artikel beschreibt außerdem, wie Sie Ihre Anwendung im Serveremulator und in Azure ausführen können.

**Verpacken des Microsoft JDBC-Treibers 4.0 für SQL Server, falls Sie Eclipse nicht verwenden**

* Stellen Sie sicher, dass die Bibliothek des Microsoft JDBC-Treibers 4.0 SQL Server in derselben Azure-Rolle enthalten ist wie Ihre Java-Anwendung und dass Sie diese zum Classpath Ihrer Anwendung hinzugefügt haben.

## Nächste Schritte

Weitere Informationen zum Microsoft JDBC-Treiber für SQL Server finden Sie unter [Übersicht über den JDBC-Treiber](http://msdn.microsoft.com/library/ms378749.aspx). Weitere Informationen zur SQL-Datenbank finden Sie unter [Übersicht über die SQL-Datenbank](http://msdn.microsoft.com/library/windowsazure/ee336241.aspx).

Weitere Informationen finden Sie außerdem im [Java Developer Center](/develop/java/).

[Concepts]: #concepts
[Prerequisites]: #prerequisites
[Creating an Azure SQL Database]: #create_db
[Determining the SQL Database connection string]: #determine_connection_string
[To allow access to a range of IP addresses]: #specify_allowed_ips
[To use Azure SQL Database in Java]: #use_sql_azure_in_java
[Communicating with Azure SQL Database from your code]: #communicate_from_code
[To create a table]: #to_create_table
[To create an index on a table]: #to_create_index
[To insert rows]: #to_insert_rows
[To retrieve rows]: #to_retrieve_rows
[To retrieve rows using a WHERE clause]: #to_retrieve_rows_using_where
[To retrieve a count of rows]: #to_retrieve_row_count
[To update rows]: #to_update_rows
[To delete rows]: #to_delete_rows
[To check whether a table exists]: #to_check_table_existence
[To drop an index]: #to_drop_index
[To drop a table]: #to_drop_table
[Using SQL Database in Java within an Azure Deployment]: #using_in_azure
[Nächste Schritte]: #nextsteps
[create_new]: ./media/sql-data-java-how-to-use-sql-database/WA_New.png
[create_new_sql_db]: ./media/sql-data-java-how-to-use-sql-database/WA_SQL_DB_Create.png
[create_database_settings]: ./media/sql-data-java-how-to-use-sql-database/WA_CustomCreate_1.png
[create_server_settings]: ./media/sql-data-java-how-to-use-sql-database/WA_CustomCreate_2.png
[get_jdbc_connection_string]: ./media/sql-data-java-how-to-use-sql-database/WA_SQL_JDBC_ConnectionString.png
[allowed_ips_dialog]: ./media/sql-data-java-how-to-use-sql-database/WA_Allowed_IPs.png
 

<!---HONumber=Sept15_HO4-->