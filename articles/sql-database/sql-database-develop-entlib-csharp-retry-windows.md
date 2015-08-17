<properties 
	pageTitle="Codebeispiel: Wiederholungslogik von Enterprise Library in C# für das Herstellen einer Verbindung mit einer SQL-Datenbank | Microsoft Azure"
	description="Enterprise Library erleichtert das Einbinden einer Wiederholungslogik in die Clientprogramme, die auf Clouddienste zugreifen."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor="" />



<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="08/04/2015" 
	ms.author="genemi"/>



# Codebeispiel: Wiederholungslogik von Enterprise Library in C&#x23; für das Herstellen einer Verbindung mit einer SQL-Datenbank


Dieses Thema enthält ein vollständiges Codebeispiel, das die Enterprise Library (EntLib) veranschaulicht. EntLib erleichtert dem Programmierer das Einbinden einer Wiederholungslogik in den Client, der mit dem Clouddienst für die SQL-Datenbank von Microsoft Azure interagiert.


Enterprise Library 6 (EntLib60) ist die neueste Version.


Die folgenden Links werden empfohlen, um detaillierte Informationen zu EntLib zu erhalten:


- [Enterprise Library 6 – April 2013](http://msdn.microsoft.com/library/dn169621.aspx)<br/>
 Enthält zahlreiche Links zu weiteren Informationen.

- Kostenloses E-Book im PDF-Format von Microsoft:<br/>
[Developer's Guide to Microsoft Enterprise Library, 2nd Edition](http://www.microsoft.com/download/details.aspx?id=41145).


## Voraussetzungen


EntLib ist bei der Installation von Visual Studio und Microsoft .NET Framework nicht enthalten. Sie müssen eine separate Downloadaktion ausführen.


Das **NuGet**-System über Visual Studio erleichtert das Herunterladen. Beim Herunterladen wird ein Verzeichnis mit dem Namen "packages\" unter demselben Verzeichnis erstellt, in dem die SLN-Datei der Visual Studio-Lösung gespeichert ist.


Die DLL-Assemblydateien von EntLib werden in einem Unterverzeichnis installiert. Die beiden Assemblydateien haben die folgenden Namen:


- `Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.dll`
- `Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.Data.dll`


## Erläuterungen zu den C&#x23;-Codedateien


Das C#-Codebeispiel besteht aus drei CS-Dateien, deren Inhalt in die folgenden Abschnitte eingefügt wird. Die Dateinamen lauten:


- `Program.cs`
- `Custom_SqlDatabaseTransientErrorDetectionStrategy.cs`
- `ForcePersistent_SqlCommandTransientErrorDetectionStrategy.cs`


### Kürzere `Program.cs` ohne `try/catch`


Eine weitere bereitgestellte CS-Datei ist eine wesentlich kürzere Version von `Program.cs`. Der gesamte `try/catch`-Code wurde entfernt. Dadurch sind die EntLib-Aufrufe leichter zu sehen.



### Testen der CS-Datei


Das Testen der Wiederholungslogik ist schwierig, da es keine wirkliche Möglichkeit gibt, einen echten vorübergehenden Fehler zu erzeugen. Eine Testmöglichkeit ist die Verwendung von temporärem Code zu folgenden Zwecken:


1. Erzeugen eines vorgetäuschten vorübergehenden Fehlers
2. Beheben der Ursache des vorübergehenden Fehlers
3. Wiederholen der Verbindung oder Abfrage mit Erfolgserwartung


Als Hilfe beim Testen steht eine weitere CS-Datei bereit, die Sie als vorübergehenden Ersatz verwenden können:


- `Test2_TransientErrorDetectionStrategy`
 - Vorübergehender Ersatz für `Custom_SqlDatabaseTransientErrorDetectionStrategy.cs`.


Die Codestellen, an denen Änderungen für diesen Test vorgenommen werden, sind mit einem Kommentar gekennzeichnet, der die Zeichenfolge `TEST.PASSWORD_FIX` enthält.


## Kompilieren und Ausführen des Codebeispiels


Sie können das Beispiel mit den folgenden Schritten kompilieren:


1. Erstellen Sie in Visual Studio ein neues Projekt mit der Vorlage für die C#-Konsolenanwendung.

2. Klicken Sie mit der rechten Maustaste auf Ihr Projekt, und fügen Sie dann die CS-Dateien hinzu, für die in diesem Thema Quellcode angegeben ist.

3. Führen Sie in einem `cmd.exe`-Befehlsfenster das Programm wie unten gezeigt aus. Die tatsächliche Ausgabe einer Ausführung wird ebenfalls gezeigt:


		[C:\MyVS\ConsoleApplication1\ConsoleApplication1\bin\Debug]
		>> ConsoleApplication1.exe
		
		database_firewall_rules_table   245575913
		filestream_tombstone_2073058421 2073058421
		filetable_updates_2105058535    2105058535
		
		[C:\MyVS\ConsoleApplication1\ConsoleApplication1\bin\Debug]
		>>


Der C#-Quellcode für die CS-Dateien ist in den folgenden Abschnitten enthalten.


## Datei „Program.cs“


Die folgende Codedatei „Program.cs“ ist lang, da sie `try/catch`-Blöcke enthält, die nur ausgeführt werden, wenn ein Fehler auftritt. Gegen Ende dieses Themas ist eine wesentlich kürzere Version von `Program.cs` angegeben, bei der alle `try/catch`-Zeilen entfernt wurden.


Die `Main`-Methode ist in `Program.cs` enthalten. Die Aufrufliste wird wie folgt ausgeführt:


1. `Main` ruft `ConnectAndQuery` auf.

2. `ConnectAndQuery` ruft `InitializeFields` auf.

3. `ConnectAndQuery` ruft `EstablishConnection` auf.

4. `EstablishConnection` ruft `IssueQueryCommand` auf.


&nbsp;


	using     System;  // C#
	using X = System.Text;
	using D = System.Data;
	using C = System.Data.SqlClient;
	using E = Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling;
	
	namespace ConsoleApplication1 
	{
	    /// <summary>
	    /// Demo sample C# program that uses Microsoft's Enterprise Library 6 to robustly
	    /// connect to and interact with Azure SQL Database.
	    /// .
	    /// The sample chooses a strategy of discarding the Sql Connection whenever
	    /// a Sql Command fails, even for a transient reason. The 'for' loop
	    /// manages this choice. We are neither recommending nor disrecommending
	    /// this particular choice, just demonstrating flexibility.
	    /// .
	    /// For an optional test mechanism manually built into this program, find all:
	    /// 'TEST.PASSWORD_FIX' (related to connection).
	    /// </summary>
	    class Program
	    {
	        // const fields, so that SqlException objects thrown from Sql Commands
	        // are tested for transience by our custom logic.
	        private const string M_progressCreatingConnection = "CreatingConnection";
	        private const string M_progressIssuingCommand     = "IssuingCommand";
	
	        // Fields, shared among methods.
	        private string progressLocation;
	        private E.ReliableSqlConnection rsConnection;
	        private E.RetryPolicy connectionRetryPolicy;
	        private E.RetryPolicy commandRetryPolicy;
	        private C.SqlConnectionStringBuilder scsBuilder;
	        private Action EstablishConnection_action;
	        private Action IssueQueryCommand_action;
	        private string sqlSelectCode;
	        private ForcePersistent_SqlCommandTransientErrorDetectionStrategy
	            forcePersistent_SqlCommandTransientErrorDetectionStrategy;
	
	        // Also consider E.FixedInterval or E.Incremental.
	        private E.ExponentialBackoff exponentialBackoffStrategy;
	
	        // TEST.PASSWORD_FIX.  Replace with Test2_TransientErrorDetectionStrategy.
	        private Custom_SqlDatabaseTransientErrorDetectionStrategy
	            custom_SqlDatabaseTransientErrorDetectionStrategy;
	
	
	        static void Main(string[] args)
	        {
	            new Program().ConnectAndQuery();
	        }
	
	
	        /// <summary>
	        /// Called from Main.
	        /// Calls a method to establish a connection, which calls
	        /// another method to issue a query through the connection. 
	        /// </summary>
	        void ConnectAndQuery()
	        {
	            int maxCountTriesConnectAndQuery = 3;  // Adjustable.
	
	            this.InitializeFields();
	
	            //-------------------------------------------------------
	            // Preparations are complete.
	            // Next is a call .ExecuteAction to invoke the method EstablishConnection.
	            // Method EstablishConnection then calls another .ExecuteAction to
	            //     invoke the method IssueQueryCommand.
	            // Then control is returned to this method.
	
	            for (int cc = 1; cc <= maxCountTriesConnectAndQuery; cc++)
	            {
	                if (cc >= 2) { Console.WriteLine("-- Another iteration of outer loop, cc=={0}. --", cc); }
	
	                try
	                {
	                    // [A.1] Connect, which proceeds to issue a query command.
	                    this.connectionRetryPolicy.ExecuteAction(this.EstablishConnection_action);
	
	                    break; // [A.2] All has gone well, so let the program end.
	                }
	                catch (C.SqlException sqlExc)
	                {
	                    bool isTransientErrorNumber = false;
	
	                    // [A.3] Call our collection of transient error numbers.
	                    isTransientErrorNumber =
	                        Custom_SqlDatabaseTransientErrorDetectionStrategy // TEST.PASSWORD_FIX.
	                        .IsTransientStatic(sqlExc);
	
	                    if (isTransientErrorNumber == false)
	                    {
	                        Console.WriteLine();
	                        Console.WriteLine("Persistent error suffered, SqlException.Number = {0}."
	                            + "  Will terminate.", sqlExc.Number);
	                        Console.WriteLine(sqlExc.ToString());
	
	                        // [A.4] Either the connection attempt or the query command attempt
	                        //     suffered a persistent SqlException.
	                        // Break the query command loop, let the hopeless program end.
	                        break;
	                    }
	
	                    if (this.progressLocation == M_progressCreatingConnection)
	                    {
	                        // [A.5] The EntLib retry mechanisms already gave the Sql Connection
	                        //     enough retries, so this block will not give connection any more tries.
	                        // This block gives retries to certain Sql Command only.
	                        break;
	                    }
	
	                    // [A.6] The SqlException identified a transient error from an attempt
	                    //     to issue a query command.
	                    // So let this method reloop and try again. However, we recommend that the new
	                    // query attempt should start at the beginning and establish a new connection.
	                    Console.WriteLine();
	                    Console.WriteLine("Transient error encountered.  SqlException.Number = {0}."
	                        + "  Program might retry by itself.", sqlExc.Number);
	                    Console.WriteLine("{0} = Attempts so far. Might retry.", cc);
	                    Console.WriteLine(sqlExc.Message);
	                }
	                catch (Exception exc)
	                {
	                    Console.WriteLine();
	                    Console.WriteLine("Unexpected exception type caught.  Will terminate.");
	                    throw exc; // [A.7] The program must end, so re-throw the unrecognized exception.
	                }
	
	
	                // [A.8] Throw an exception if transient SqlExceptions caused us
	                // to exceed our self-imposed maximum of retries.
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
	            } // for cc
	            return;
	        } // method ConnectAndQuery
	
	
	        void EstablishConnection()
	        {
	            try
	            {
	                // [C.1]
	                using ( this.rsConnection = new E.ReliableSqlConnection(
	                    this.scsBuilder.ToString(),
	                    this.connectionRetryPolicy,
	                    this.commandRetryPolicy )
	                    )
	                {
	                    this.progressLocation = M_progressCreatingConnection;
	                    this.rsConnection.Open(); // [C.2] Open the newly constructed reliable connection.
	
	                    // [C.3] Prepare delegate, then issue a query command.
	                    this.commandRetryPolicy.ExecuteAction(this.IssueQueryCommand_action);
	                }
	            }
	            // TEST.PASSWORD_FIX.
	                // Step_1: In InitializeFields(), set password to a wrong value.
	                // Step_2: Uncomment the following 'catch' of sqlExc.
	            //catch (C.SqlException sqlExc)  // For TESTING only.  Usually comment out this one CATCH.
	            //{
	            //    if (sqlExc.Number == 18456)  // Could mean wrong password, could not connect.
	            //    {
	            //        this.scsBuilder["Password"] = "MyCorrectPassword";
	            //        Console.WriteLine("Testing.  Password now fixed in catch.");
	            //    }
	            //    throw sqlExc;
	            //}
	            catch (Exception exc)
	            {
	                throw exc;  // [C.4] Let caller assess any exception, SqlException or any kind.
	            }
	            return;
	        } // method EstablishConnection
	
	
	        void IssueQueryCommand()
	        {
	            D.IDataReader dReader = null;
	            D.IDbCommand dbCommand = null;
	
	            X.StringBuilder sBuilder = new X.StringBuilder(256);
	
	            try
	            {
	                // [D.1] Use the connection to create a query command.
	                using (dbCommand = this.rsConnection.CreateCommand())
	                {
	                    dbCommand.CommandText = this.sqlSelectCode;
	
	                    // [D.2] Issue the query command through the connection.
	                    this.progressLocation = M_progressIssuingCommand;
	                    using (dReader = this.rsConnection.ExecuteCommand<D.IDataReader>(dbCommand))
	                    {
	                        Console.WriteLine();
	                        // [D.3] Loop through all returned rows, writing the data to the console.
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
	                throw exc; // [D.4] Let caller assess any exception.
	            }
	            return;
	        } // method IssueQueryCommand
	
	
	        void InitializeFields()
	        {
	            // [B.1] Prepare the connection string to Azure SQL Database.
	            this.scsBuilder = new C.SqlConnectionStringBuilder();
	            // Change these values to your values.
	            this.scsBuilder["Server"] = "tcp:myazuresqldbserver.database.windows.net,1433";
	            this.scsBuilder["User ID"] = "MyLogin";  // @yourservername suffix sometimes.
	            this.scsBuilder["Password"] = "MyCorrectPassword";  // TEST.PASSWORD_FIX.
	            this.scsBuilder["Database"] = "MyDatabase";
	            // Leave these values as they are.
	            this.scsBuilder["Trusted_Connection"] = false;
	            this.scsBuilder["Integrated Security"] = false;
	            this.scsBuilder["Encrypt"] = true;
	            this.scsBuilder["Connection Timeout"] = 30; // Default is 15 seconds, too brief over Internet!
	
	            // [B.2] Prepare the Transact-SQL select statement.
	            this.sqlSelectCode =
	                @"SELECT TOP 3 name, CAST(object_id as nvarchar(32)) as [object_id]
	                  FROM sys.objects
	                  WHERE type='IT'
	                  ORDER BY name;";
	
	            // [B.3] Begin steps to create a retry policy for connecting.
	            this.exponentialBackoffStrategy = new E.ExponentialBackoff(
	                "exponentialBackoffStrategy",
	                2,                               // Maximum number of times to retry.
	                TimeSpan.FromMilliseconds(2000), // Minimum interval between retries.
	                TimeSpan.FromMilliseconds(8000), // Maximum interval between retries.
	                TimeSpan.FromMilliseconds(2000)  // Factor for random differences in interval between retries.
	                );
	
	            this.forcePersistent_SqlCommandTransientErrorDetectionStrategy =
	                new ForcePersistent_SqlCommandTransientErrorDetectionStrategy();
	
	            this.custom_SqlDatabaseTransientErrorDetectionStrategy =
	                new Custom_SqlDatabaseTransientErrorDetectionStrategy();// TEST.PASSWORD_FIX.
	
	            this.connectionRetryPolicy = new E.RetryPolicy(
	                this.custom_SqlDatabaseTransientErrorDetectionStrategy,
	                this.exponentialBackoffStrategy);
	            this.commandRetryPolicy = new E.RetryPolicy(
	                this.forcePersistent_SqlCommandTransientErrorDetectionStrategy,
	                this.exponentialBackoffStrategy);
	
	            this.EstablishConnection_action = delegate() { this.EstablishConnection(); };
	            this.IssueQueryCommand_action   = delegate() { this.IssueQueryCommand(); };
	
	            return;
	        } // method InitializeFields
	    } // class Program
	}


&nbsp;


## Datei `Custom_SqlDatabaseTransientErrorDetectionStrategy.cs`


EntLib60 verfügt über eine Klasse mit dem Namen `ReliableSqlConnection`. Sie können steuern, wie die Verbindungsinstanz entscheidet, ob eine Ausnahme ein vorübergehender Fehler ist. Dazu weisen Sie ihr eine Klasse zu, die `ITransientErrorDetectionStrategy` implementiert.

EntLib60 stellt die Klasse `SqlDatabaseTransientErrorDetectionStrategy` bereit. In diesem Thema wird jedoch eine eigene benutzerdefinierte Klasse `Custom_SqlDatabaseTransientErrorDetectionStrategy` implementiert und verwendet. Die benutzerdefinierte Klasse weist eine Positivliste von Werten für den Vergleich mit `SqlException.Number` auf.


	using     System;
	using G = System.Collections.Generic;
	using C = System.Data.SqlClient;
	using E = Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling;
	
	namespace ConsoleApplication1
	{
	    /// <summary>
	    /// A custom alternative to class SqlDatabaeTransientErrorDetectionStrategy.
	    /// </summary>
	    public class Custom_SqlDatabaseTransientErrorDetectionStrategy
	        : E.ITransientErrorDetectionStrategy
	    {
	        static private G.List<int> M_listTransientErrorNumbers;
	
	
	        /// <summary>
	        /// This method is required by ITransientErrorDetectionStrategy.
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
	                {4060, 10928, 10929, 40197, 40501, 40613};
	
	            M_listTransientErrorNumbers = new G.List<int>(arrayOfTransientErrorNumbers);
	        }
	    } // class Custom_SqlDatabaseTransientErrorDetectionStrategy
	}


&nbsp;


## Datei `ForcePersistent_SqlCommandTransientErrorDetectionStrategy.cs`


Diese benutzerdefinierte Klasse, die `ITransientErrorDetectionStrategy` implementiert, erzwingt, dass EntLib60 einen Abfragebefehlsfehler niemals als nur vorübergehend betrachtet. Stattdessen ist das Gesamtprogramm so ausgelegt, dass `SqlException.Number` in der eigenen benutzerdefinierten Logik bewertet wird.


Bei der Klasse in diesem Abschnitt wurde die Entwurfsoption gewählt, dass vom Programm eine Verbindung verworfen und eine neue Verbindung hergestellt wird, bevor ein fehlerhafter Abfragebefehl wiederholt wird, ganz gleich, ob es sich um einen vorübergehenden Fehler handelt oder nicht.


	using     System;
	using E = Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling;
	
	
	namespace ConsoleApplication1
	{
	    /// <summary>
	    /// Is a substitute for class E.SqlCommandTransientErrorDetectionStrategy.
	    /// Choosing to always discard the Sql Connection whenever the command fails,
	    /// regardless of whether command failure was transient.
	    /// Program try/catch and for-loop logic prefers to discard then re-construct
	    /// a new Sql Connection.
	    /// </summary>
	    public class ForcePersistent_SqlCommandTransientErrorDetectionStrategy
	        : E.ITransientErrorDetectionStrategy
	    {
	        public bool IsTransient(Exception exc)
	        {
	            return false;  // Force a failure, even if is transient.  No retry of sql command.
	        }
	    }
	}


&nbsp;


## Testen der Wiederholungslogik


Das Testen des EntLib60-Programms ist kompliziert. Es ist schwierig, einen echten vorübergehenden Fehler zu erzeugen und die jeweiligen Zeitpunkte zu steuern. Dieser Beispielcode beinhaltet ein einfaches Verfahren, das Sie zum Imitieren eines vorübergehenden Fehlers verwenden können.


Bei diesem Verfahren wird die benutzerdefinierte Erkennungsklasse `Test2_TransientErrorDetectionStrategy` anstelle der Klasse `Custom_SqlTransientErrorDetectionStrategy` verwendet. Auf diese Weise können Sie `ReliableSqlConnection` von EntLib60 zwingen, *alle* Vorkommen von `SqlException` als vorübergehend zu bewerten. Der Trick besteht darin, das Problem vor dem ersten Wiederholungsversuch vom Programm selbst beheben zu lassen.


### Trick: Falsch geschriebenes Kennwort


Ein einfacher Ansatz ist es, mit einem falsch geschriebenen Wert für das Verbindungskennwort zu beginnen. Wenn dadurch `SqlException` mit `sqlExc.Number==18456` verursacht wird, muss das Programm das Kennwort korrigieren. Bei der Wiederholung wird das Programm erfolgreich ausgeführt.


Um diesen Test im Demoprogramm zu implementieren, durchsuchen Sie den Quellcode nach allen Vorkommen von `TEST.PASSWORD_FIX`. Wenn Sie alle Vorkommen von `TEST.PASSWORD_FIX` gefunden und die Änderungen vorgenommen haben, die in den Kommentaren an den jeweiligen Stellen angegeben sind, haben Sie die folgenden Änderungen durchgeführt:


- Ändern Sie im Livecode in der `InitializeFields`-Methode den Kennwortwert in einen falschen Wert.

- Im `//`-kommentierten Code in der `EstablishConnection`-Methode gibt es einen ganzen `catch`-Block,<br/>
`//catch (SDSqlC.SqlException sqlExc)`,<br/>
der auskommentiert ist. Entfernen Sie die Kommentare für den gesamten Block.

- Weisen Sie im neuen unkommentierten `catch`-Block den richtigen Kennwortwert zu.

- An den Stellen, an denen im Code auf die Klasse `Custom_TransientErrorDetectionStrategy` verwiesen wird, ersetzen Sie den Verweis durch `Test2_TransientErrorDetectionStrategy`. Sie können den Variablennamen beibehalten.


### Datei `Test2_TransientErrorDetectionStrategy.cs`


	using     System;
	using E = Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling;
	
	namespace ConsoleApplication1
	{
	    /// <summary>
	    /// For testing, is a substitute for class E.SqlDatabaseTransientErrorDetectionStrategy.
	    /// Change the true/false assignment to returnValue for different tests.
	    /// </summary>
	    public class Test2_TransientErrorDetectionStrategy
	        : E.ITransientErrorDetectionStrategy
	    {
	        public bool IsTransient(Exception exc)
	        {
	            bool returnValue = Test2_TransientErrorDetectionStrategy.IsTransientStatic(exc);
	            Console.WriteLine("Inside Test2_TransientErrorDetectionStrategy .IsTransientStatic,"
	                + " returning {0}.", returnValue);
	            return returnValue;
	        }
	        static public bool IsTransientStatic(Exception exc)
	        {
	            return true;  //false;
	        }
	    }
	}


&nbsp;


## Kürzere `Program.cs` ohne `try/catch`


Der kürzere Code in diesem Abschnitt kompiliert, und würde bei Ausführung in der Regel funktionieren. Er ist jedoch nicht zur Ausführung in der Produktion gedacht.


Dieses kürzere `Program.cs`-Beispiel dient lediglich dazu, dieselben EntLib-Aufrufe, die auch in der tatsächlichen `Program.cs` enthalten sind, besser sichtbar zu machen. Durch das Entfernen aller `try/catch`-Blöcke sind die EntLib-Aufrufe leichter zu erkennen.


	using     System;  // C#
	using X = System.Text;
	using D = System.Data;
	using C = System.Data.SqlClient;
	using E = Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling;
	
	namespace ConsoleApplication1_Shorter_No_TryCatch
	{
	    class Program
	    {
	        private E.ReliableSqlConnection rsConnection;
	        private E.RetryPolicy connectionRetryPolicy;
	        private E.RetryPolicy commandRetryPolicy;
	        private E.ExponentialBackoff exponentialBackoffStrategy;
	        private C.SqlConnectionStringBuilder scsBuilder;
	        private Action EstablishConnection_action;
	        private Action IssueQueryCommand_action;
	        private string sqlSelectCode;
	        private Custom_SqlDatabaseTransientErrorDetectionStrategy
	            custom_SqlDatabaseTransientErrorDetectionStrategy;
	        private ForcePersistent_SqlCommandTransientErrorDetectionStrategy
	            forcePersistent_SqlCommandTransientErrorDetectionStrategy;
	
	        static void Main(string[] args)
	        {
	            new Program().ConnectAndQuery();
	        }
	
	        void ConnectAndQuery()
	        {
	            this.InitializeFields();
	            this.connectionRetryPolicy.ExecuteAction(this.EstablishConnection_action);
	        } // method ConnectAndQuery
	
	        void EstablishConnection()
	        {
	            using ( this.rsConnection = new E.ReliableSqlConnection(
	                this.scsBuilder.ToString(),
	                this.connectionRetryPolicy,
	                this.commandRetryPolicy )
	                    )
	            {
	                this.rsConnection.Open();
	                this.commandRetryPolicy.ExecuteAction(this.IssueQueryCommand_action);
	            }
	        } // method EstablishConnection
	
	
	        void IssueQueryCommand()
	        {
	            D.IDataReader dReader = null;
	            D.IDbCommand dbCommand = null;
	            X.StringBuilder sBuilder = new X.StringBuilder(256);
	
	            using (dbCommand = this.rsConnection.CreateCommand())
	            {
	                dbCommand.CommandText = this.sqlSelectCode;
	                using (dReader = this.rsConnection.ExecuteCommand<D.IDataReader>(dbCommand))
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
	
	        void InitializeFields()
	        {
	            this.scsBuilder = new C.SqlConnectionStringBuilder();
	            // Change these values to your values.
	            this.scsBuilder["Server"] = "tcp:myazuresqldbserver.database.windows.net,1433";
	            this.scsBuilder["User ID"] = "MyLogin";
	            this.scsBuilder["Password"] = "MyPassword";  // TEST.PASSWORD_FIX.
	            this.scsBuilder["Database"] = "MyDatabase";
	            // Leave these values as they are.
	            this.scsBuilder["Trusted_Connection"] = false;
	            this.scsBuilder["Integrated Security"] = false;
	            this.scsBuilder["Encrypt"] = true;
	            this.scsBuilder["Connection Timeout"] = 30;
	
	            this.sqlSelectCode =
	                @"SELECT TOP 3 name, CAST(object_id as nvarchar(32)) as [object_id]
	                  FROM sys.objects
	                  WHERE type='IT'
	                  ORDER BY name;";
	
	            this.exponentialBackoffStrategy = new E.ExponentialBackoff(
	                "exponentialBackoffStrategy",
	                2,
	                TimeSpan.FromMilliseconds(2000),
	                TimeSpan.FromMilliseconds(8000),
	                TimeSpan.FromMilliseconds(2000)
	                );
	
	            this.forcePersistent_SqlCommandTransientErrorDetectionStrategy =
	                new ForcePersistent_SqlCommandTransientErrorDetectionStrategy();
	            this.custom_SqlDatabaseTransientErrorDetectionStrategy =
	                new Custom_SqlDatabaseTransientErrorDetectionStrategy();
	
	            this.connectionRetryPolicy = new E.RetryPolicy(
	                this.custom_SqlDatabaseTransientErrorDetectionStrategy,
	                this.exponentialBackoffStrategy);
	            this.commandRetryPolicy = new E.RetryPolicy(
	                this.forcePersistent_SqlCommandTransientErrorDetectionStrategy,
	                this.exponentialBackoffStrategy);
	
	            this.EstablishConnection_action = delegate() { this.EstablishConnection(); };
	            this.IssueQueryCommand_action   = delegate() { this.IssueQueryCommand(); };
	        } // method InitializeFields
	    } // class Program
	}


## Verwandte Links


- [Namespace Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling](http://msdn.microsoft.com/library/microsoft.practices.enterpriselibrary.transientfaulthandling.aspx)

- [Enterprise Library 6-Klassenbibliothek](http://msdn.microsoft.com/library/dn170426.aspx)

- [Codebeispiel: Wiederholungslogik in C# für das Herstellen einer Verbindung mit einer SQL-Datenbank mithilfe von ADO.NET](sql-database-develop-csharp-retry-windows.md)

- [Clientcodebeispiele für die ersten Schritte mit SQL-Datenbank](sql-database-develop-quick-start-client-code-samples.md)

<!---HONumber=August15_HO6-->