<properties urlDisplayName="SQL Database" pageTitle="Verwenden von SQL Azure (Java) - Leitfaden zu Azure-Features" metaKeywords="" description="Learn how to use the Azure SQL Database from Java code. " metaCanonical="" services="sql-database" documentationCenter="Java" title="How to Use Azure SQL Database in Java" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="09/25/2014" ms.author="robmcm" />

# Verwenden der Azure SQL-Datenbank in Java

In den folgenden Schritten wird gezeigt, wie Sie Azure SQL-Datenbank mit Java verwenden können. Zur Vereinfachung werden Befehlszeilenbeispiele gegeben, aber für Webanwendungen (lokal, in Azure oder in anderen Umgebungen gehostet) können beinahe dieselben Schritte verwendet werden. Dieser Leitfaden beschreibt das Erstellen eines Servers und einer Datenbank im [Azure-Verwaltungsportal](https://windows.azure.com).

## Was ist Azure SQL-Datenbank?

Azure SQL-Datenbank bietet ein Verwaltungssystem für relationale Datenbanken für Azure und basiert auf SQL Server-Technologie. Mit einer SQL-Datenbankinstanz können Sie relationale Datenbanklösungen für die Cloud bereitstellen und implementieren, und Sie profitieren von einem verteilten Rechenzentrum, das Verfügbarkeit, Skalierbarkeit und Sicherheit für Unternehmen mit integriertem Datenschutz und Selbstreparatur bietet.

## Inhaltsverzeichnis

-   [Konzepte][]
-   [Voraussetzungen][]
-   [Erstellen einer Azure SQL-Datenbank][]
-   [Bestimmung der Verbindungszeichenfolge für die SQL-Datenbank][]
-   [Erlauben eines Bereichs zulässiger IP-Adressen][]
-   [Verwenden der Azure SQL-Datenbank in Java][]
-   [Kommunizieren mit der Azure SQL-Datenbank in Ihrem Code][]
-   [Erstellen einer Tabelle][]
-   [Erstellen eines Index in einer Tabelle][]
-   [Einfügen von Zeilen][]
-   [Abrufen von Zeilen][]
-   [Abrufen von Zeilen mit einer WHERE-Klausel][]
-   [Abrufen einer Zeilenanzahl][]
-   [Aktualisieren von Zeilen][]
-   [Löschen von Zeilen][]
-   [Prüfen, ob eine Tabelle vorhanden ist][]
-   [Löschen eines Index][]
-   [Löschen einer Tabelle][]
-   [Verwenden einer SQL-Datenbank in einer Azure-Bereitstellung][]
-   [Nächste Schritte][]

<h2><a id="concepts"></a>Konzepte</h2>
Da Azure SQL-Datenbank auf SQL Server-Technologien basiert, erfolgt der Zugriff auf SQL-Datenbank aus Java ganz ähnlich wie der Zugriff auf SQL Server aus Java. Sie können eine Anwendung lokal (mit SQL Server) entwickeln und dann eine Verbindung zu SQL-Datenbank herstellen, indem Sie lediglich die Verbindungszeichenfolge ändern. Sie können einen SQL Server JDBC-Treiber für Ihre Anwendung verwenden. Es gibt jedoch einige Unterschiede zwischen SQL-Datenbank und SQL Server, die sich auf Ihre Anwendung auswirken können. Weitere Informationen finden Sie unter [Richtlinien und Einschränkungen (SQL-Datenbanken)](http://msdn.microsoft.com/en-us/library/windowsazure/ff394102.aspx).

Weitere Informationen zu SQL-Datenbank finden Sie im Abschnitt [Nächste Schritte][].

<h2><a id="prerequisites"></a>Voraussetzungen</h2>

In den folgenden Schritten werden die Voraussetzungen für die Verwendung von SQL-Datenbank in Java beschrieben.

* Ein Java Developer Kit (JDK), Version 1.6 oder höher.
* Ein Azure-Abonnement, das von <http://www.microsoft.com/windowsazure/offers/> bezogen werden kann.
* Wenn Sie Eclipse verwenden, brauchen Sie die Eclipse-IDE für Java EE-Entwickler, Indigo oder höher. Dies kann von <http://www.eclipse.org/downloads/> heruntergeladen werden. Außerdem brauchen Sie das Azure-Plug-In für Eclipse mit Java (von Microsoft Open Technologies). Stellen Sie bei der Installation des Plug-Ins sicher, dass der Microsoft JDBC-Treiber 4.0 für SQL Server enthalten ist. Weitere Informationen finden Sie unter [Installieren des Azure-Plug-Ins für Eclipse mit Java (von Microsoft Open Technologies)](http://msdn.microsoft.com/en-us/library/windowsazure/hh690946.aspx).
* Wenn Sie nicht mit Eclipse arbeiten, benötigen Sie den Microsoft JDBC-Treiber 4.0 für SQL Server, den Sie von <http://www.microsoft.com/en-us/download/details.aspx?id=11774> herunterladen können.

<h2><a id="create_db"></a>Erstellen einer Azure SQL-Datenbank</h2>

Bevor Sie Azure SQL-Datenbank in Ihrem Java-Code verwenden können, müssen Sie einen Azure SQL-Datenbankserver erstellen.

1. Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com) an.
2. Klicken Sie auf **Neu**.

    ![Create new SQL database][create_new]

3. Klicken Sie auf **SQL-Datenbank** und anschließend auf **Benutzerdefiniert erstellen**.

    ![Create custom SQL database][create_new_sql_db]

4. Geben Sie im Dialogfeld **Datenbankeinstellungen** den Namen der Datenbank an. In diesem Lernprogramm wird **gettingstarted** als Datenbankname verwendet.
5. Wählen Sie für **Server** die Option **Neuer SQL-Datenbankserver**. Übernehmen Sie die Standardwerte für die anderen Felder.

    ![SQL database settings][create_database_settings]

6. Klicken Sie auf den Pfeil für "Weiter".	
7. Geben Sie im Dialogfeld **Servereinstellungen** einen Anmeldenamen für den SQL Server an. In diesem Lernprogramm wird **MySQLAdmin** verwendet. Geben Sie ein Kennwort ein und bestätigen Sie es anschließend. Wählen Sie eine Region aus, und stellen Sie sicher, dass die Option **Allow Azure Services to access the server** aktiviert ist.

    ![SQL server settings][create_server_settings]

8. Klicken Sie auf die Schaltfläche "Completion".

<h2><a id="determine_connection_string"></a>Bestimmung der Verbindungszeichenfolge für die SQL-Datenbank</h2>

1. Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com) an.
2. Klicken Sie auf **SQL-Datenbanken**.
3. Klicken Sie die gewünschte Datenbank an.
4. Klicken Sie auf **Verbindungszeichenfolgen anzeigen**.
5. 	Markieren Sie den Inhalt der **JDBC**-Verbindungszeichenfolge.

    ![Determine JDBC connection string][get_jdbc_connection_string]

6. 	Klicken Sie mit der rechten Maustaste auf den markierten Inhalt der **JDBC**-Verbindungszeichenfolge, und klicken Sie dann auf **Kopieren**.
7. Sie können diesen Wert nun in Ihrem Code einfügen, um eine Verbindungszeichenfolge im folgenden Format zu erstellen. Ersetzen Sie *your_server* (an zwei Stellen) durch den im vorherigen Schritt kopierten Text, und ersetzen Sie *your_password* durch das Kennwort, das Sie bei der Erstellung Ihres SQL-Datenbank-Kontos festgelegt haben. (Ersetzen Sie außerdem die Werte **database=** und **user=**, falls Sie nicht **gettingstarted** und **MySQLAdmin** verwenden.) 

    String connectionString =
		"jdbc:sqlserver://*your_server*.database.windows.net:1433" + ";" +  
    	"database=gettingstarted" + ";" + 
    	"user=MySQLAdmin@*your_server*" + ";" +  
    	"password=*your_password*" + ";" +  
        "encrypt=true" + ";" +
        "hostNameInCertificate=*.int.mscds.com" + ";" +  
        "loginTimeout=30";

Wir werden diese Zeichenfolge später in dieser Anleitung verwenden. Momentan ist es nur wichtig, dass Sie wissen, wie die Verbindungszeichenfolge ermittelt wird. Abhängig von den Anforderungen Ihrer Anwendung brauchen Sie u. U. die Einstellungen**encrypt** und **hostNameInCertificate** nicht zu verwenden und die Einstellung **loginTimeout** nicht ändern.

<h2><a id="specify_allowed_ips"></a>Erlauben eines Bereichs zulässiger IP-Adressen</h2>
1. Melden Sie sich beim [Verwaltungsportal](https://manage.windowsazure.com) an.
2. Klicken Sie auf **SQL-Datenbanken**.
3. Klicken Sie auf **Server**.
4. Klicken Sie den gewünschten Server an.
5. Klicken Sie auf **Verwalten**.
6. Klicken Sie auf **Konfigurieren**.
7. Geben Sie unter **Zulässige IP-Adressen ** den Namen einer neuen IP-Regel ein. Geben Sie Anfang und Ende des IP-Adressbereichs ein. Die IP-Adresse des aktuellen Clients wird hier ebenfalls angezeigt. Im folgenden Beispiel wird nur eine einzige Client-IP-Adresse zugelassen (Sie haben eine andere IP-Adresse).

    ![Allowed IP addresses dialog][allowed_ips_dialog]

8. Klicken Sie auf die Schaltfläche zum Fertigstellen. Die angegebenen IP-Adressen können nun auf Ihren Datenbankserver zugreifen.

<h2><a id="use_sql_azure_in_java"></a>Verwenden der Azure SQL-Datenbank in Java</h2>

1. Erstellen Sie ein Java-Projekt. Nennen Sie das Projekt für dieses Lernprogramm **HelloSQLAzure**.
2. Fügen Sie dem Projekt eine Java-Klassendatei namens **HelloSQLAzure.java** hinzu.
3. Fügen Sie dem Buildpfad **Microsoft JDBC-Treiber für SQL Server** hinzu.

   Falls Sie Eclipse verwenden:

    1. Klicken Sie im Projekt-Explorer in Eclipse mit der rechten Maustaste auf **HelloSQLAzure** project and click **Properties**.
    2. Klicken Sie im linken Bereich des Dialogfelds **Properties** auf **Java Build Path**.
    3. Klicken Sie auf die Registerkarte **Libraries** und anschließend auf **Add Library**.
    4. Wählen Sie im Dialogfeld **Add Library** die Option **Microsoft JDBC Driver 4.0 für SQL Server** aus, klicken Sie auf **Next** und anschließend auf **Finish**.
    5. Klicken Sie auf **OK**, um das Dialogfeld **Properties** zu schließen.

    Wenn Sie nicht mit Eclipse arbeiten, fügen Sie die JAR-Datei mit dem Microsoft JDBC-Treiber 4.0 für SQL Server dem Klassenpfad hinzu. Weitere Informationen hinzu finden Sie unter [Verwenden des JDBC-Treibers](http://msdn.microsoft.com/en-us/library/ms378526.aspx).

4. Fügen Sie dem Code in Ihrer **HelloSQLAzure.java**-Datei die folgenden `import`-Anweisungen hinzu:

        import java.sql.*;
        import com.microsoft.sqlserver.jdbc.*;

5. Geben Sie die Verbindungszeichenfolge an. Dies ist ein Beispiel. Ersetzen Sie wie oben *your_server* (an zwei Stellen), *your_user* und *your_password* durch die entsprechenden Werten für Ihren SQL-Datenbankserver.

        String connectionString =
        	"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
        		"database=master" + ";" + 
        		"user=your_user@your_server" + ";" +  
        		"password=your_password";

Nun können Sie den Code hinzufügen, der mit Ihrem SQL-Datenbankserver kommunizieren wird.

<h2><a id="communicate_from_code"></a>Kommunizieren mit der Azure SQL-Datenbank in Ihrem Code</h2>

Der Rest dieses Artikels enthält Beispiele für Folgendes:

1. Herstellen einer Verbindung mit dem SQL-Datenbankserver.
2. Definieren einer SQL-Anweisung, z. B. zum Erstellen oder Löschen einer Tabelle, Einfügen/Auswählen/Löschen von Zeilen usw.
3. Führen Sie die SQL-Anweisung entweder durch Aufrufen von **executeUpdate** oder **executeQuery** aus.
4. Anzeigen der Abfrageergebnisse falls zutreffend.

Die folgenden Abschnitte sollten in der angegebenen Reihenfolge gelesen (und bearbeitet) werden. Der erste Codeabschnitt ist ein komplettes Beispiel. In den restlichen Beispielen werden Teile des Frameworks aus dem vollständigen Beispiel verwendet, z. B. die **import**-Anweisungen, die **class**- und **main**-Deklarationen, die Fehlerbehandlung und das Schließen von Ressourcen.

<h2><a id="to_create_table"></a>Erstellen einer Tabelle</h2>

Der folgende Code zeigt, wie Sie eine Tabelle namens **Person** erstellen.

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
	

<h2><a id="to_create_index"></a>Erstellen eines Index in einer Tabelle</h2>

Der folgende Code zeigt, wie Sie einen Index namens **index1** für die Tabelle **Person** erstellen und hierzu die Spalte **PersonID** verwenden.

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



<h2><a id="to_insert_rows"></a>Einfügen von Zeilen</h2>

Der folgende Code zeigt, wie Sie der Tabelle **Person** Zeilen hinzufügen.

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

 
<h2><a id="to_retrieve_rows"></a>Abrufen von Zeilen</h2>

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

 Im obigen Code wurden die ersten 10 Zeilen in der Tabelle **Person** ausgewählt. Ändern Sie die SQL-Anweisung wie folgt ab, um alle Zeilen zurückzugeben:

	String sqlString = "SELECT * FROM Person";

 
<h2><a id="to_retrieve_rows_using_where"></a>Abrufen von Zeilen mit einer WHERE-Klausel</h2>

Um Zeilen mit einer Klausel abzurufen, erweitern Sie die SQL-Anweisung im oben gezeigten Code um eine Klausel. Die folgende SQL-Anweisung enthält eine Klausel für Zeilen, deren Wert in **FirstName** gleich **Jim** ist.

	// SQL-Anweisung definieren.
	String sqlString = "SELECT * FROM Person WHERE FirstName='Jim'";
	
WHERE-Klauseln können auch beim Abrufen von Anzahlen sowie bei Änderungen und Löschungen von Zeilen verwendet werden.

<h2><a id="to_retrieve_row_count"></a>Abrufen einer Zeilenanzahl</h2>

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

<h2><a id="to_update_rows"></a>Aktualisieren von Zeilen</h2>

Der folgende Code zeigt, wie Sie Zeilen aktualisieren. In diesem Beispiel wird der Wert von **LastName** in allen Zeilen, in denen der Wert von **FirstName** gleich **Jim** ist, in **Kim** geändert.

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

 

<h2><a id="to_delete_rows"></a>Löschen von Zeilen</h2>

Der folgende Code zeigt, wie Sie Zeilen löschen. In diesem Beispiel werden alle Zeilen gelöscht, in denen der Wert von **FirstName** gleich **Jim** ist.

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
	
 
<h2><a id="to_check_table_existence"></a>Prüfen, ob eine Tabelle vorhanden ist</h2>

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

<h2><a id="to_drop_index"></a>Löschen eines Index</h2>

Der folgende Code zeigt, wie Sie einen Index namens **index1** aus der Tabelle **Person** löschen.

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

 
<h2><a id="to_drop_table"></a>Löschen einer Tabelle</h2>

Der folgende Code zeigt, wie Sie eine Tabelle namens **Person** löschen.

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

<h2><a id="using_in_azure"></a>Verwenden einer SQL-Datenbank in einer Azure-Bereitstellung</h2>

Um SQL-Datenbanken in Java mit einer Azure-Bereitstellung zu verwenden, müssen Sie nicht nur den Microsoft JDBC-Treiber 4.0 für SQL Server als Bibliothek in Ihren Classpath integrieren, sondern diesen auch mit Ihrer Bereitstellung verpacken.


**Verpacken des Microsoft JDBC-Treibers 4.0 für SQL Server, falls Sie Eclipse verwenden**

1. Klicken Sie im Projekt-Explorer in Eclipse mit der rechten Maustaste auf Ihr Projekt und anschließend auf **Properties**.
2. Klicken Sie im linken Bereich des Dialogfelds **Properties** auf **Deployment Assembly** und anschließend auf **Add**.
3. Klicken Sie im Dialogfeld **New Assembly Directive** auf **Java Build Path Entries** und anschließend auf **Next**.
4. Wählen Sie **Microsoft JDBC Driver 4.0 SQL Server** aus, und klicken Sie dann auf **Finish**.
5. Klicken Sie auf **OK**, um das Dialogfeld **Properties** zu schließen.
6. 	Exportieren Sie die WAR-Datei Ihres Projekts in Ihren approot-Ordner, und erstellen Sie das Azure-Projekt neu. Gehen Sie dazu wie unter [Erstellen einer Hello World-Anwendung mithilfe des Azure-Plug-Ins für Eclipse mit Java (von Microsoft Open Technologies)](http://msdn.microsoft.com/en-us/library/windowsazure/hh690944.aspx) beschrieben vor. Dieser Artikel beschreibt außerdem, wie Sie Ihre Anwendung im Serveremulator und in Azure ausführen.

**Verpacken des Microsoft JDBC-Treibers 4.0 für SQL Server, falls Sie Eclipse nicht verwenden**

* Stellen Sie sicher, dass die Bibliothek des Microsoft JDBC-Treibers 4.0 SQL Server in derselben Azure-Rolle enthalten ist wie Ihre Java-Anwendung und dass Sie diese zum Classpath Ihrer Anwendung hinzugefügt haben.

<h2><a id="nextsteps"></a>Nächste Schritte</h2>

Weitere Informationen zum Microsoft JDBC-Treiber für SQL Server finden Sie unter [Übersicht über den JDBC-Treiber](http://msdn.microsoft.com/en-us/library/ms378749.aspx). Weitere Informationen zu SQL-Datenbank finden Sie unter [Übersicht über die SQL-Datenbank](http://msdn.microsoft.com/en-us/library/windowsazure/ee336241.aspx).

[Konzepte]:#concepts
[Voraussetzungen]:#prerequisites
[Erstellen einer Azure SQL-Datenbank]:#create_db
[Bestimmung der Verbindungszeichenfolge für die SQL-Datenbank]:#determine_connection_string
[Erlauben eines Bereichs zulässiger IP-Adressen]:#specify_allowed_ips
[Verwenden der Azure SQL-Datenbank in Java]:#use_sql_azure_in_java
[Kommunizieren mit der Azure SQL-Datenbank in Ihrem Code]:#communicate_from_code
[Erstellen einer Tabelle]:#to_create_table
[Erstellen eines Index in einer Tabelle]:#to_create_index
[Einfügen von Zeilen]:#to_insert_rows
[Abrufen von Zeilen]:#to_retrieve_rows
[Abrufen von Zeilen mit einer WHERE-Klausel]:#to_retrieve_rows_using_where
[Abrufen einer Zeilenanzahl]:#to_retrieve_row_count
[Aktualisieren von Zeilen]:#to_update_rows
[Löschen von Zeilen]:#to_delete_rows
[Prüfen, ob eine Tabelle vorhanden ist]:#to_check_table_existence
[Löschen eines Index]:#to_drop_index
[Löschen einer Tabelle]:#to_drop_table
[Verwenden einer SQL-Datenbank in einer Azure-Bereitstellung]:#using_in_azure
[Nächste Schritte]:#nextsteps
[create_new]: ./media/sql-data-java-how-to-use-sql-database/WA_New.png
[create_new_sql_db]: ./media/sql-data-java-how-to-use-sql-database/WA_SQL_DB_Create.png
[create_database_settings]: ./media/sql-data-java-how-to-use-sql-database/WA_CustomCreate_1.png
[create_server_settings]: ./media/sql-data-java-how-to-use-sql-database/WA_CustomCreate_2.png
[get_jdbc_connection_string]: ./media/sql-data-java-how-to-use-sql-database/WA_SQL_JDBC_ConnectionString.png
[allowed_ips_dialog]: ./media/sql-data-java-how-to-use-sql-database/WA_Allowed_IPs.png
