<properties 
	pageTitle="Codebeispiel: Wiederholungslogik in C# für das Herstellen einer Verbindung mit einer SQL-Datenbank | Microsoft Azure"
	description="Das C#-Beispiel enthält die robuste Wiederholungslogik für die Interaktion mit Azure SQL-Datenbank."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor=""/>


<tags 
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/04/2015"
	ms.author="genemi"/>


# Codebeispiel: Wiederholungslogik in C# für das Herstellen einer Verbindung mit einer SQL-Datenbank


<!--
sql-database-develop-csharp-retry-windows.md  ,  NEW
mgm4f20150723retryfrommsdn68
. . . .
Old Titles on MSDN:  Code sample: Retry logic for connecting to Azure SQL Database with ADO.NET
.
How to: Connect to Azure SQL Database by using ADO.NET
.
ShortId on MSDN was:  ee336243.aspx
Dx  4d7936fd-341c-4a37-8796-25e385ae6c5b
-->


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Dieses Thema enthält ein C#-Codebeispiel, mit dem die benutzerdefinierte Wiederholungslogik veranschaulicht wird. Die Wiederholungslogik dient zum kontrollierten Verarbeiten von vorübergehenden Fehlern (auch: *Übergangsfehler*), die meist nicht mehr vorhanden sind, wenn das Programm einige Sekunden wartet und den Vorgang dann erneut ausführt.


ADO.NET-Klassen, die Sie zum Herstellen einer Verbindung mit dem lokalen Microsoft SQL Server verwenden, können ebenfalls eine Verbindung mit Azure SQL-Datenbank herstellen. Allein können die ADO.NET-Klassen aber nicht für die Robustheit und Zuverlässigkeit sorgen, die für die Verwendung in der Produktion erforderlich ist. In Ihrem Clientprogramm können vorübergehende Fehler auftreten, die vom Programm unbeaufsichtigt und kontrolliert behoben werden können. Beispiele für vorübergehende Fehler:


- Eine Verbindung über das Internet unterliegt kurzen Netzwerkausfällen, nach denen die Verbindung wiederhergestellt werden kann.

- Cloud Computing umfasst auch den Lastenausgleich, wobei Versuche, eine Verbindung herzustellen oder Abfragen durchzuführen, kurz blockiert werden können.


## Ermitteln von vorübergehenden Fehlern


Ihr Programm muss zwischen vorübergehenden Fehlern und beständigen Fehlern unterscheiden. Wenn im Programm der Name der Zieldatenbank falsch geschrieben ist, bleibt der Fehler der Art „Keine Datenbank gefunden“ bestehen und tritt immer auf, wenn Sie das Programm erneut ausführen.


Die Liste mit den Fehlernummern, die als vorübergehende Fehler kategorisiert werden, finden Sie unter:


- [Fehlermeldungen für SQL-Datenbank-Clientprogramme](sql-database-develop-error-messages.md#bkmk_connection_errors)
 - Siehe Abschnitt *Vorübergehende Fehler, Verbindungsabbruchfehler*.


## C#-Codebeispiel


Das C#-Codebeispiel in diesem Thema enthält eine benutzerdefinierte Erkennungs- und Wiederholungslogik zum Behandeln von vorübergehenden Fehlern.


Im Codebeispiel werden einige grundlegende Richtlinien oder Empfehlungen befolgt, die unabhängig davon gelten, welche Technologie Sie zum Interagieren mit Azure SQL-Datenbank verwenden. Allgemeine Empfehlungen finden Sie unter:


- [Verbindungen mit SQL-Datenbanken: Wichtige Empfehlungen](sql-database-connect-central-recommendations.md)


Das C#-Codebeispiel besteht aus den beiden CS-Dateien, deren Inhalt in die folgenden Abschnitte eingefügt wird. Die Dateinamen lauten:


- `Program.cs`
- `Custom_SqlDatabaseTransientErrorDetectionStrategy.cs`


## Kompilieren und Ausführen des Codebeispiels


Sie können das Beispiel mit den folgenden Schritten kompilieren:


1. Erstellen Sie in Visual Studio ein neues Projekt mit der Vorlage für die C#-Konsolenanwendung.

2. Klicken Sie mit der rechten Maustaste auf Ihr Projekt, und fügen Sie dann die CS-Datei hinzu, für die in diesem Thema Quellcode angegeben ist.

3. Führen Sie in einem `cmd.exe`-Befehlsfenster das Programm wie unten gezeigt aus. Die tatsächliche Ausgabe einer Ausführung wird ebenfalls angezeigt.


		[C:\MyVS\ConsoleApplication1\ConsoleApplication1\bin\Debug]
		>> ConsoleApplication1.exe
		database_firewall_rules_table   245575913
		filestream_tombstone_2073058421 2073058421
		filetable_updates_2105058535    2105058535
		
		[C:\MyVS\ConsoleApplication1\ConsoleApplication1\bin\Debug]
		>>


Der C#-Quellcode für die CS-Dateien ist in den folgenden Abschnitten enthalten.


## Datei „Program.cs“


Das Demoprogramm wurde wie folgt entworfen:


- Ein vorübergehender Fehler beim Versuch, eine Verbindung herzustellen, führt zu einer Wiederholung.

- Ein vorübergehender Fehler während eines Abfragebefehls bewirkt, dass das Programm die Verbindung verwirft und eine neue Verbindung herstellt, bevor der Abfragebefehl wiederholt wird.


Dieser Entwurf wird von Microsoft weder empfohlen, noch wird davon abgeraten. Das Demoprogramm soll lediglich die Entwurfsflexibilität veranschaulichen, die Ihnen zur Verfügung steht.


### Länge des Quellcodes


Die Datei `Program.cs` ist vor allem deshalb relativ lang, weil sie die Logik zum Abfangen von Ausnahmen enthält.


Eine [kürzere Version](#shorter_program_cs) der Datei `Program.cs`, in der die gesamte Wiederholungslogik und `Exception`-Behandlung entfernt wurde, finden Sie am Ende dieses Themas.


### Aufrufliste


Die `Main`-Methode ist in `Program.cs` enthalten. Die Aufrufliste wird wie folgt ausgeführt:


1. `Main` ruft `ConnectAndQuery` auf.

2. `ConnectAndQuery` ruft `EstablishConnection` auf.

3. `EstablishConnection` ruft `IssueQueryCommand` auf.


### Quellcode für „Program.cs“


	using     System;  // C#, pure ADO.NET, no Enterprise Library.
	using X = System.Text;
	using D = System.Data;
	using C = System.Data.SqlClient;
	using T = System.Threading;
	
	namespace ConsoleApplication1
	{
	    class Program
	    {
	        // Fields, shared among methods.
	        C.SqlConnection sqlConnection;
	        C.SqlConnectionStringBuilder scsBuilder;
	
	        static void Main(string[] args)
	        {
	            new Program().ConnectAndQuery();
	        }
	
	        /// <summary>
	        /// Prepares values for a connection. Then inside a loop, it calls a method
	        /// that opens a connection. The called method calls yet another method
	        /// that issues a query.
	        /// The loop reiterates only if a transient error is encountered.
	        /// </summary>
	        void ConnectAndQuery()
	        {
	            int connectionTimeoutSeconds = 30;  // Default of 15 seconds is too short over the Internet, sometimes.
	            int maxCountTriesConnectAndQuery = 3;  // You can adjust the various retry count values.
	            int secondsBetweenRetries = 6;  // Simple retry strategy.
	
	            // [A.1] Prepare the connection string to Azure SQL Database.
	            this.scsBuilder = new C.SqlConnectionStringBuilder();
	            // Change these values to your values.
	            this.scsBuilder["Server"] = "tcp:myazuresqldbserver.database.windows.net,1433";
	            this.scsBuilder["User ID"] = "MyLogin";  // @yourservername suffix sometimes.
	            this.scsBuilder["Password"] = "MyPassword";
	            this.scsBuilder["Database"] = "MyDatabase";
	            // Leave these values as they are.
	            this.scsBuilder["Trusted_Connection"] = false;
	            this.scsBuilder["Integrated Security"] = false;
	            this.scsBuilder["Encrypt"] = true;
	            this.scsBuilder["Connection Timeout"] = connectionTimeoutSeconds;
	
	            //-------------------------------------------------------
	            // Preparations are complete.
	
	            for (int cc = 1; cc <= maxCountTriesConnectAndQuery; cc++)
	            {
	                try
	                {
	                    // [A.2] Connect, which proceeds to issue a query command.
	                    this.EstablishConnection();
	
	                    // [A.3] All has gone well, so let the program end.
	                    break;
	                }
	                catch (C.SqlException sqlExc)
	                {
	                    bool isTransientError;
	
	                    // [A.4] Check whether sqlExc.Number is on the whitelist of transients.
	                    isTransientError = Custom_SqlDatabaseTransientErrorDetectionStrategy
	                        .IsTransientStatic(sqlExc);
	
	                    if (isTransientError == false)  // Is a persistent error...
	                    {
	                        Console.WriteLine();
	                        Console.WriteLine("Persistent error suffered, SqlException.Number=={0}.  Will terminate.",
	                            sqlExc.Number);
	                        Console.WriteLine(sqlExc.ToString());
	
	                        // [A.5] Either the connection attempt or the query command attempt suffered a persistent SqlException.
	                        // Break the loop, let the hopeless program end.
	                        break;
	                    }
	
	                    // [A.6] The SqlException identified a transient fault from an attempt to issue a query command.
	                    // So let this method reloop and try again. However, we recommend that the new query
	                    // attempt should start at the beginning and establish a new connection.
	                    Console.WriteLine();
	                    Console.WriteLine("Transient error encountered.  SqlException.Number=={0}."
	                        + "  Program might retry by itself.", sqlExc.Number);
	                    Console.WriteLine("{0} = Attempts so far. Might retry.", cc);
	                    Console.WriteLine(sqlExc.Message);
	                }
	                catch (Exception exc)
	                {
	                    Console.WriteLine();
	                    Console.WriteLine("Unexpected exception type caught in Main. Will terminate.");
	
	                    // [A.7] The program must end, so re-throw the unrecognized error.
	                    throw exc;
	                }
	
	                // [A.8] Throw an application exception if transient SqlExceptions caused us
	                // to exceed our self-imposed maximum count of retries.
	                if (cc > maxCountTriesConnectAndQuery)
	                {
	                    Console.WriteLine();
	                    string mesg = String.Format(
	                        "Transient errors suffered in too many retries ({0}). Will terminate.",
	                        cc - 1);
	                    Console.WriteLine(mesg);
	
	                    // [A.9] To end the program, throw a new exception of a different type.
	                    ApplicationException appExc = new ApplicationException(mesg);
	                    throw appExc;
	                }
	                // Else, can retry.
	
	                // A very simple retry strategy, a brief pause before looping.
	                T.Thread.Sleep(1000 * secondsBetweenRetries);
	            } // for cc
	            return;
	        } // method ConnectAndQuery
	
	
	        /// <summary>
	        /// Open a connection, then call a method that issues a query.
	        /// </summary>
	        void EstablishConnection()
	        {
	            try
	            {
	                // [B.1] The 'using' statement will .Dispose() the connection.
	                // If you are working with a connection pool, you might want instead
	                // to merely .Close() the connection.
	                using (this.sqlConnection = new C.SqlConnection(this.scsBuilder.ToString()))
	                {
	                    // [B.2] Open a connection.
	                    sqlConnection.Open();
	                    // [B.3]
	                    this.IssueQueryCommand();
	                }
	            }
	            catch (Exception exc)
	            {
	                // [B.4] This re-throw means we discard the connection whenever
	                // any error occurs during query command, even for a transient error.
	                throw exc;  // [B.5] Let caller assess any exception, SqlException or any kind.
	            }
	            return;
	        } // method EstablishConnection
	
	
	        /// <summary>
	        /// Issue a query, then write the result rows to the console.
	        /// </summary>
	        void IssueQueryCommand()
	        {
	            D.IDataReader dReader = null;
	            D.IDbCommand dbCommand = null;
	            X.StringBuilder sBuilder = new X.StringBuilder(512);
	
	            try
	            {
	                // [C.1] Use the connection to create a query command.
	                using (dbCommand = this.sqlConnection.CreateCommand())
	                {
	                    dbCommand.CommandText =
	                        @"SELECT TOP 3
	                              ob.name,
	                              CAST(ob.object_id as nvarchar(32)) as [object_id]
	                          FROM sys.objects as ob
	                          WHERE ob.type='IT'
	                          ORDER BY ob.name;";
	
	                    // [C.2] Issue the query command through the connection.
	                    using (dReader = dbCommand.ExecuteReader())
	                    {
	                        // [C.3] Loop through all returned rows, writing the data to the console.
	                        while (dReader.Read())
	                        {
	                            sBuilder.Length = 0;
	                            sBuilder.Append(dReader.GetString(0));
	                            sBuilder.Append("\t");
	                            sBuilder.Append(dReader.GetString(1));
	
	                            Console.WriteLine(sBuilder.ToString());
	                        }
	                    }
	                }
	            }
	            catch (Exception exc)
	            {
	                throw exc; // Let caller assess any exception.
	            }
	            return;
	        } // method IssueQueryCommand
	    } // class Program
	}


## Codedatei „Custom\_SqlDatabaseTransientErrorDetectionStrategy.cs“


[`SqlDatabaseTransientErrorDetectionStrategy`](http://msdn.microsoft.com/library/microsoft.practices.enterpriselibrary.transientfaulthandling.sqldatabasetransienterrordetectionstrategy.aspx) ist eine Klasse in der Enterprise Library (EntLib)-API. In diesem Codebeispiel wird die folgende benutzerdefinierte Klasse verwendet, bei der die Idee der EntLib-Klasse genutzt wird.


	using     System;
	using G = System.Collections.Generic;
	using C = System.Data.SqlClient;
	
	namespace ConsoleApplication1
	{
	    /// <summary>
	    /// A custom alternative to class SqlDatabaeTransientErrorDetectionStrategy.
	    /// </summary>
	    public class Custom_SqlDatabaseTransientErrorDetectionStrategy
	    {
	        static private G.List<int> M_listTransientErrorNumbers;
	
	
	        /// <summary>
	        /// This method happens to match ITransientErrorDetectionStrategy of EntLib60.
	        /// </summary>
	        public bool IsTransient(Exception exc)
	        {
	            return IsTransientStatic(exc);
	        }
	
	
	        /// <summary>
	        /// For general use beyond formal Enterprise Library classes.
	        /// </summary>
	        static public bool IsTransientStatic(Exception exc)
	        {
	            bool returnBool = false;  // Assume is a persistent error.
	            C.SqlException sqlExc;
	
	            if (exc is C.SqlException)
	            {
	                sqlExc = exc as C.SqlException;
	                if (M_listTransientErrorNumbers.Contains(sqlExc.Number) == true)
	                {
	                    returnBool = true;  // Error is transient, not persistent.
	                }
	            }
	            return returnBool;
	        }
	
	
	        /// <summary>
	        /// Lists the SqlException.Number values that are considered
	        /// to indicate transient errors.
	        /// </summary>
	        static Custom_SqlDatabaseTransientErrorDetectionStrategy()
	        {
	            int[] arrayOfTransientErrorNumbers =
	                {4060, 10928, 10929, 40197, 40501, 40613 };
	
	            M_listTransientErrorNumbers = new G.List<int>(arrayOfTransientErrorNumbers);
	        }
	    } // class Custom_SqlDatabaseTransientErrorDetectionStrategy
	}


<a id="shorter_program_cs" name="shorter_program_cs"></a>


&nbsp;


## Kurzversion der Datei „Program.cs“


Der Quellcode in diesem Abschnitt ist eine verkürzte Version der längeren Datei `Program.cs`, die oben angegeben ist. Die gesamte Wiederholungslogik und Ausnahmebehandlung wurde entfernt.


In der Kurzversion sind die ADO.NET-Aufrufe leichter zu sehen, und es ist bekannt, dass diese normalerweise funktionieren. In der Regel treten keine vorübergehenden Fehler auf, und es wird keine Ausnahme ausgelöst. Und normalerweise braucht ein Skydiver keinen Zweitschirm.


	using     System;  // C#, pure ADO.NET, no retry logic, no Exception handling.
	using X = System.Text;
	using D = System.Data;
	using C = System.Data.SqlClient;
	
	namespace ConsoleApplication1_dn864744
	{
	    class Program
	    {
	        C.SqlConnection sqlConnection;
	        C.SqlConnectionStringBuilder scsBuilder;
	
	        static void Main(string[] args)
	        {
	            new Program().ConnectAndQuery();
	        }
	
	        void ConnectAndQuery()
	        {
	            this.scsBuilder = new C.SqlConnectionStringBuilder();
	            // Change these values to your values.
	            this.scsBuilder["Server"] = "tcp:myazuresqldbserver.database.windows.net,1433";
	            this.scsBuilder["User ID"] = "MyLogin";
	            this.scsBuilder["Password"] = "MyPassword";
	            this.scsBuilder["Database"] = "MyDatabase";
	            this.scsBuilder["Trusted_Connection"] = false;
	            this.scsBuilder["Integrated Security"] = false;
	            this.scsBuilder["Encrypt"] = true;
	            this.scsBuilder["Connection Timeout"] = 30;
	
	            this.EstablishConnection();
	        } // method ConnectAndQuery
	
	        void EstablishConnection()
	        {
	            using (this.sqlConnection = new C.SqlConnection(this.scsBuilder.ToString()))
	            {
	                sqlConnection.Open();
	                this.IssueQueryCommand();
	            }
	        } // method EstablishConnection
	
	        void IssueQueryCommand()
	        {
	            D.IDataReader dReader = null;
	            D.IDbCommand dbCommand = null;
	            X.StringBuilder sBuilder = new X.StringBuilder(512);
	
	            using (dbCommand = this.sqlConnection.CreateCommand())
	            {
	                dbCommand.CommandText =
	                    @"SELECT TOP 3 ob.name, CAST(ob.object_id as nvarchar(32)) as [object_id]
	                        FROM sys.objects as ob
	                        WHERE ob.type='IT'
	                        ORDER BY ob.name;";
	
	                using (dReader = dbCommand.ExecuteReader())
	                {
	                    while (dReader.Read())
	                    {
	                        sBuilder.Length = 0;
	                        sBuilder.Append(dReader.GetString(0));
	                        sBuilder.Append("\t");
	                        sBuilder.Append(dReader.GetString(1));
	                        Console.WriteLine(sBuilder.ToString());
	                    }
	                }
	            }
	        } // method IssueQueryCommand
	    } // class Program
	}


## Verwandte Links


- [Clientcodebeispiele für die ersten Schritte mit SQL-Datenbank](sql-database-develop-quick-start-client-code-samples.md)

<!---HONumber=August15_HO9-->