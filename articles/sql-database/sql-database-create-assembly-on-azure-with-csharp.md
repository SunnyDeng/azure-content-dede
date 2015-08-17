<properties 
	pageTitle="CREATE ASSEMBLY in Azure SQL-Datenbank mit CSharp"
	description="C#-Quellcode zum Ausführen von &quot;CREATE ASSEMBLY&quot; in Azure SQL-Datenbank nach dem Codieren einer DLL-Datei in einer Zeichenfolge, die eine lange Hexadezimalzahl enthält." 
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
	ms.date="07/14/2015" 
	ms.author="genemi"/>


# CREATE ASSEMBLY in Azure SQL-Datenbank mit CSharp


<!--
GeneMi , Latest edit = 2015-March-25  Wednesday  10:22am
Converting plain text "CREATE ASSEMBLY" into a link to the MSDN topic, ms189524.aspx. And ms186755.aspx for "CREATE FUNCTION".
-->


In diesem Thema wird ein C#-Codebeispiel erläutert, mit dem Sie eine [CREATE ASSEMBLY](http://msdn.microsoft.com/library/ms189524.aspx)-Anweisung in Azure SQL-Datenbank ausführen können. Be einer SQL-Datenbank kann die FROM-Klausel das einfache Format eines Pfads auf dem lokalen Computer, der die Datenbank hostet, nicht übernehmen. Als Alternative können zunächst die Binärbits der Assembly-DLL in einer langen Zeichenfolge codiert werden, die eine Hexadezimalzahl enthält. In diesem Fall wird die Zeichenfolge als Wert für die FROM-Klausel angegeben.


### Voraussetzungen


Zum besseren Verständnis dieses Themas sollten Sie sich bereits mit dem folgenden Thema beschäftigt haben:


- [CLR-Tabellenwertfunktionen](http://msdn.microsoft.com/library/ms131103.aspx)<br/>Erläutert, wie die Transact-SQL-Anweisung "CREATE ASSEMBLY" mit anderen Anweisungen für den lokalen Microsoft SQL Server ausgeführt wird.


## A: Allgemeines Verfahren


1. Führen Sie ggf. DROP FUNCTION und DROP ASSEMBLY zum Bereinigen einer vorherigen Ausführung aus.
2. Merken Sie sich den Speicherort der Microsoft .NET Framework Assembly-DLL-Datei, die Sie aus Ihrem eigenen Code kompiliert haben. Den Speicherort geben Sie im nächsten Schritt an. 
3. Führen Sie die EXE-Datei aus, für die der C#-Quellcode in diesem Thema angegeben ist. Geben Sie für die EXE-Datei an, wo sich die DLL-Datei befindet.
 - Codiert die binäre DLL-Datei in einer langen Zeichenfolge, die eine Hexadezimalzahl enthält.
 - Führt eine CREATE ASSEMBLY-Anweisung mit der Hexadezimalzeichenfolge aus, die in der FROM-Klausel angegeben ist.
4. Verwenden Sie [CREATE FUNCTION](http://msdn.microsoft.com/library/ms186755.aspx) zum Verweisen auf eine Methode in der Assembly.
5. Verwenden Sie die T-SQL-Anweisung "SELECT" zum Aufrufen und Testen der Funktion.


In dieser Liste wird Folgendes nicht erwähnt ...<br/> **execute sp\_configure 'clr enabled', 1;**<br/> ... da dies zwar für Microsoft SQL Server, aber nicht für Azure SQL-Datenbank erforderlich ist.


Wenn dies für erneute Ausführungen notwendig ist, lautet der T-SQL-Code zum Löschen der Funktion und der Assembly wie folgt:


    DROP FUNCTION fnCompareStringsCaseSensitive;
    DROP ASSEMBLY CreateAssemblyFunctions3;


## B. Einfache Assembly-DLL für den Verweis auf die T-SQL-Funktion


Das einfache C#-Codebeispiel in diesem Abschnitt kann in einer Assembly-DLL-Datei kompiliert werden.


Dieses Codebeispiel enthält die **CompareCaseSensitiveNet**-Methode, auf die später in einer CREATE FUNCTION-T-SQL-Anweisung verwiesen wird. Beachten Sie, dass die Methode mit dem .NET-Attribut **SqlFunction** versehen ist. Eine Methode, die mit diesem Attribut versehen ist, kann von T-SQL als Funktion aufgerufen werden.


	using           System;   // C#
	using SDSqTyp = System.Data.SqlTypes;
	using MSqServ = Microsoft.SqlServer.Server;
	
	namespace CreateAssemblyFunctions3
	{
	    public class SqlFunctionMethodsForSprocs
	    {
	        /// <summary> This method is referenced
	        /// by a T-SQL CREATE FUNCTION statement. </summary>
	        [MSqServ.SqlFunction(IsDeterministic = true, IsPrecise = true)]
	        static public SDSqTyp.SqlInt32 CompareCaseSensitiveNet(string strA, string strB)
	        {
	            return String.Compare(strA, strB, false);
	        }
	    }
	}


## C. C&#x23;-Codebeispiel für die EXE-Datei zum Ausführen von "CREATE ASSEMBLY"


Wenn Sie die mit diesem C#-Beispiel erstellte EXE-Datei ausführen, ergibt sich die folgende Codesequenz:


1. Die Befehlszeilenausführung der EXE-Datei ruft die **Main**-Methode auf.
2. Die "Main"-Methode ruft die **ObtainHexStringOfAssembly**-Methode auf.
 - Die Methode gibt einen "SqlString" aus, der die Assembly als Hexadezimalzahl speichert.
3. Die "Main"-Methode ruft die **SubmitCreateAssemblyToAzureSqlDb**-Methode auf.
 - Die primäre Eingabe ist der "SqlString".
 - Die Ausgabe ist ein CREATE ASSEMBLY-Aufruf, der an die Azure SQL-Datenbank gesendet wird.


			using             System;   // C#
			using SDat      = System.Data;
			using SDSClient = System.Data.SqlClient;
			using SGlo      = System.Globalization;
			using SIo       = System.IO;
			using STex      = System.Text;
			
			namespace CreateAssemblyFromHexString6
			{
			    /// <summary>
			    /// Run the Main method on your local computer console, so it can issue a
			    /// a CREATE ASSEMBLY statement to your Azure SQL Database server:
			    /// </summary>
			    class Program
			    {
			        /// <summary>
			        /// Calls the methods in the proper sequence.
			        /// </summary>
			        /// <param name="args">
			        /// Parameters for the cmd.exe command line
			        ///    run of CreateAssemblyFromHexString6.exe:
			        /// args[0] = FullDirPathFileNameOfAssembly.
			        /// args[1] = AssemblyName.
			        ///    For the CREATE ASSEMBLY assemblyName statement.
			        /// args[2] = Azure SQL Database - ServerName, including a suffix like .database.windows.net .
			        /// args[3] = Azure SQL Database - DatabaseName.
			        /// args[4] = Azure SQL Database - LoginName.
			        /// args[5] = Azure SQL Database - Password for login.
			        ///    (Better if from .config file.)
			        /// </param>
			        static int Main(string[] args)
			        {
			            int returnCode = 1; // Only 0 (zero) means Good Success.
			            string
			                fullPathNameAssemblyFile,
			                assemblyName,
			                AzureSqlDbServerName,
			                AzureSqlDbDatabaseName,
			                AzureSqlDbLoginName,
			                AzureSqlDbPassword;
			
			            try
			            {
			                fullPathNameAssemblyFile = args[0];
			                assemblyName             = args[1];
			                AzureSqlDbServerName     = args[2];
			                AzureSqlDbDatabaseName   = args[3];
			                AzureSqlDbLoginName      = args[4];
			                AzureSqlDbPassword       = args[5];
			
			                string hexStringOfAssembly = Program
			                    .ObtainHexStringOfAssembly(fullPathNameAssemblyFile);
			
			                Program.SubmitCreateAssemblyToAzureSqlDb(
			                    hexStringOfAssembly,
			                    assemblyName,
			                    AzureSqlDbServerName,
			                    AzureSqlDbDatabaseName,
			                    AzureSqlDbLoginName,
			                    AzureSqlDbPassword);
			
			                returnCode = 0;
			            }
			            catch (Exception ex)
			            {
			                Console.WriteLine("Bad, Failure.");
			                throw ex;
			            }
			            Console.WriteLine("Good, Success.");
			            return returnCode;
			        }
			
			        /// <summary> Encodes the binary bits of the assembly DLL into a 
			        /// string containing a hexadecimal number. </summary>
			        /// <param name="fullPathToAssembly"
			        /// >Full directory path plus the file name, to the .DLL file.</param>
			        /// <returns>A string containing a hexadecimal number that encodes
			        /// the binary bits of the .DLL file.</returns>
			        static private string ObtainHexStringOfAssembly
			            (string fullPathToAssembly)
			        {
			            STex.StringBuilder sbuilder = new STex.StringBuilder("0x");
			            using (SIo.FileStream fileStream = new SIo.FileStream(
			                fullPathToAssembly,
			                SIo.FileMode.Open, SIo.FileAccess.Read, SIo.FileShare.Read)
			                )
			            {
			                int byteAsInt;
			                while (true)
			                {
			                    byteAsInt = fileStream.ReadByte();
			                    if (-1 >= byteAsInt) { break; }
			                    sbuilder.Append(byteAsInt.ToString
			                        ("X2", SGlo.CultureInfo.InvariantCulture));
			                }
			            }
			            return sbuilder.ToString();
			        }
			
			        /// <summary>
			        /// Sends a Transact-SQL CREATE ASSEMBLY FROM hexString.
			        /// </summary>
			        static private void SubmitCreateAssemblyToAzureSqlDb(
			            string hexStringOfAssembly,
			            string assemblyName,
			            string AzureSqlDbServerName,
			            string AzureSqlDbDatabaseName,
			            string AzureSqlDbLoginName,
			            string AzureSqlDbPassword)
			        {
			            string sqlCreateAssembly = "CREATE ASSEMBLY [" + assemblyName
			                + "] FROM " + hexStringOfAssembly + ";";
			            STex.StringBuilder sbuilderConnection = new STex.StringBuilder();
			
			            sbuilderConnection.Append("Server=tcp:");
			            sbuilderConnection.Append(AzureSqlDbServerName);
			            sbuilderConnection.Append(",1433;");
			
			            sbuilderConnection.Append("Database=");
			            sbuilderConnection.Append(AzureSqlDbDatabaseName);
			            sbuilderConnection.Append(";");
			
			            sbuilderConnection.Append("Trusted_Connection=False;");
			            sbuilderConnection.Append("Connection Timeout=30;");
			
			            sbuilderConnection.Append("User ID=");
			            sbuilderConnection.Append(AzureSqlDbLoginName);
			            sbuilderConnection.Append(";");
			
			            sbuilderConnection.Append("Password=");
			            sbuilderConnection.Append(AzureSqlDbPassword);
			            sbuilderConnection.Append(";");
			
			            using (SDSClient.SqlConnection sqlConnection =
			                new SDSClient.SqlConnection())
			            {
			                SDSClient.SqlCommand sqlCommand;
			                sqlConnection.ConnectionString = sbuilderConnection.ToString();
			                sqlCommand = sqlConnection.CreateCommand();
			                sqlCommand.CommandType = SDat.CommandType.Text;
			                sqlCommand.CommandText = sqlCreateAssembly;
			                sqlConnection.Open();
			                sqlCommand.ExecuteNonQuery();
			            }
			            return;
			        }
			    }
			}


### C.1 Referenzen und Versionen der Kompilierung


Zum Kompilieren und Testen des Beispielcodes für das EXE-Tool wurde Folgendes verwendet:


- Visual Studio 2013, Update 4
 - Als Projektvorlagentyp diente die einfache Konsolenanwendung.
- .NET Framework 4.5


Das Visual Studio-Projekt verwies für die Kompilierung auf die folgenden Assemblys:


- Microsoft.CSharp
- System
- System.Core
- System.Data
- System.Data.DataSetExtensions
- System.Xml
- System.Xml.Linq


### C.2 Befehlszeile zum Ausführen der EXE-Datei


Im folgenden Codeblock ist ein Beispiel für die Befehlszeile angegeben, die Sie zum Ausführen der EXE-Datei über die Konsole eingeben. Die Parameter in der Befehlszeile sind hier zur besseren Darstellung künstlich umbrochen.


	CreateAssemblyFromHexString6.exe
		C:\my\bin\debug\CreateAssemblyFunctions3.dll
		CreateAssemblyFunctions3
		myazuresqldbsvr2.database.windows.net
		myazuresqldbdab4
		myazurelogin
		Mypassword123


Der Einfachheit halber übergibt dieses Beispiel das Kennwort als Befehlszeilenparameter. Besser wäre es, wenn der C#-Code das Kennwort aus einer CONFIG-Datei abruft.


## D. Ausführen einer CREATE FUNCTION-Anweisung


Nach dem Speichern der Assembly auf dem Azure SQL-Datenbankserver müssen Sie eine CREATE FUNCTION-Transact-SQL-Anweisung ausführen, die auf die Methode in der Assembly verweist.


Der folgende Block des Transact-SQL-Codes umfasst mehrere unwesentliche SELECT-Anweisungen, die nachweisen, dass das Datenbanksystem über Datensätze für die Assembly und die Funktion verfügt. Schließlich wird die Funktion mit einer SELECT-Anweisung aufgerufen.


	SELECT a11.*, am2.*
		FROM           sys.assemblies       AS a11
		    INNER JOIN sys.assembly_modules AS am2 ON am2.assembly_id = a11.assembly_id
		WHERE a11.name = 'CreateAssemblyFunctions3';
	GO
	
	CREATE FUNCTION fnCompareStringsCaseSensitive
	    (@strA nvarchar(128), @strB nvarchar(128))
	    returns int
	    AS EXTERNAL NAME
	        CreateAssemblyFunctions3
	            .[CreateAssemblyFunctions3.SqlFunctionMethodsForSprocs]
	                .CompareCaseSensitiveNet;
	GO
	SELECT * FROM sys.objects WHERE name = 'fnCompareStringsCaseSensitive';
	
	 -- Use the new function.
	SELECT dbo.fnCompareStringsCaseSensitive('BLUE', 'blue') as returnedValue;
	GO


Dieser Transact-SQL-Codeblock endet mit einer SELECT-Anweisung, die die neue Funktion aufruft. Sie können die SELECT-Anweisung in einer gespeicherten Prozedur ablegen.


<!-- EndOfFile -->

<!---HONumber=August15_HO6-->