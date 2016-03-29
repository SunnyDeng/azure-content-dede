<properties
	pageTitle="EntLib-Wiederholungslogik zum Herstellen einer Verbindung mit SQL-Datenbank | Microsoft Azure"
	description="Enterprise Library wurde zur Vereinfachung verschiedener Aufgaben für Clientprogramme von Clouddiensten entwickelt. Dazu zählt u. a. die Integration von Wiederherstellungslogik für vorübergehende Fehler."
	services="sql-database"
	documentationCenter=""
	authors="annemill"
	manager="jhubbard"
	editor="" />


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/15/2016"
	ms.author="annemill"/>


# Codebeispiel: Wiederholungslogik aus Enterprise Library 6 in C&#x23; für das Herstellen einer Verbindung mit SQL-Datenbank

[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Dieses Thema enthält ein vollständiges Codebeispiel, das die Enterprise Library (EntLib) veranschaulicht. EntLib vereinfacht eine Reihe von Aufgaben für Clientprogramme, die mit Clouddiensten wie Azure SQL-Datenbank interagieren. Der Schwerpunkt unseres Beispiels liegt auf der wichtigen Aufgabe, Wiederholungslogik für vorübergehende Fehler zu integrieren.


Bei EntLib-Klassen wird zwischen zwei Kategorien von Laufzeitfehlern unterschieden:

- Fehler, die nicht von selbst behoben werden, z. B. ein falsch geschriebener Servername.
- Vorübergehende Fehler, z. B. Situationen, in denen der Server für einige Sekunden keine neuen Verbindungen akzeptiert, während das Azure-System einen Lastenausgleich ausführt.


Enterprise Library 6 (EntLib60) ist die aktuelle Version und wurde im April 2013 veröffentlicht.

- Microsoft stellt den Quellcode für die Öffentlichkeit zur Verfügung.
- Microsoft plant keine weitere Verwaltung des Quellcodes.


## Voraussetzungen


#### .NET Framework 4.0 oder höher


Installation von Microsoft .NET Framework 4.0 oder höher erforderlich. Zum Zeitpunkt der Verfassung dieses Artikels ist Version 4.6 verfügbar, und wir empfehlen die Verwendung dieser aktuellen Version.


#### Visual Studio Community-Edition (kostenlos)


Der Quellcode aus diesem Beispiel muss kompiliert werden. Ein Möglichkeit ist die Installation der [kostenlosen Microsoft Visual Studio *Community*-Edition](http://www.visualstudio.com/products/free-developer-offers-vs.aspx).


Möglicherweise müssen Sie sich mit Ihrer E-Mail-Adresse für MSDN registrieren. Dazu müssen in etwa die folgenden Schritte ausgeführt werden:


1. [Rufen Sie die MSDN-Website auf](http://msdn.microsoft.com/).
2. Klicken Sie im oberen Bildschirmbereich auf ** MSDN-Abonnements**.
3. Klicken Sie auf **Jetzt registrieren**.
4. Füllen Sie das Formular aus.
5. Klicken Sie im unteren Bildschirmbereich auf **Konto erstellen**.


#### Enterprise Library 6 (EntLib60)


Optionen für die Installation von EntLib60:


- Verwenden Sie das *NuGet*-Paket-Manager-Feature in Visual Studio:
 - Suchen Sie in NuGet nach **enterpriselibrary**.


- Navigieren Sie im [Startdokumentationsthema zu EntLib60](http://msdn.microsoft.com/library/dn169621.aspx) zur Zeile **Downloads**, und klicken Sie auf [Microsoft Enterprise Library 6](http://go.microsoft.com/fwlink/?linkid=290898), um die .DLL-Assemblybinärdateien herunterzuladen.


EntLib60 verfügt über verschiedene .DLL-Assemblydateien, deren Namen mit demselben Präfix beginnen (**Microsoft.Practices.EnterpriseLibrary.&#x2a;.dll**). Für dieses Codebeispiel sind jedoch lediglich die beiden folgenden Assemblys relevant:

- Microsoft.Practices.EnterpriseLibrary.**TransientFaultHandling**.dll
- Microsoft.Practices.EnterpriseLibrary.**TransientFaultHandling.Data**.dll


## Zusammenspiel von EntLib-Klassen


EntLib-Klassen werden zum Erstellen weiterer EntLib-Klassen verwendet. In diesem Codebeispiel wird folgende Konstruktions- und Nutzungssequenz verwendet:


1. Erstellen Sie ein **ExponentialBackoff**-Objekt.
2. Erstellen Sie ein **SqlDatabaseTransientErrorDetectionStrategy**-Objekt.
3. Erstellen Sie ein **RetryPolicy**-Objekt. Dabei werden folgende Eingabeparameter verwendet:
 - **ExponentialBackoff**-Objekt.
 - **SqlDatabaseTransientErrorDetectionStrategy**-Objekt.
4. Erstellen Sie ein **ReliableSqlConnection**-Objekt. Dabei werden folgende Eingabeparameter verwendet:
 - Ein **String**-Objekt – mit dem Servernamen und den übrigen Verbindungsinformationen.
 - **RetryPolicy**-Objekt.
5. Aufruf zum Herstellen der Verbindung (über die Methode **RetryPolicy .ExecuteAction**).
6. Rufen Sie die Methode **ReliableSqlConnection .CreateCommand** auf.
 - Gibt ein **System.SqlClient.Data.DbCommand**-Objekt zurück (Teil von ADO.NET).
7. Aufruf zum Ausführen einer Abfrage (über die Methode **RetryPolicy .ExecuteAction**).


## Kompilieren und Ausführen des Codebeispiels


Das Program.cs-Quellcodebeispiel finden Sie weiter unten in diesem Thema. Sie können das Beispiel über die folgenden Schritte ausführen und kompilieren:


1. Erstellen Sie in Visual Studio ein neues Projekt mit der Vorlage für die C#-Konsolenanwendung.

2. Bearbeiten Sie im Projektmappen-Explorer-Bereich die fast leere Datei "Program.cs", indem Sie den Starterinhalt durch den Program.cs-Code ersetzen, den Sie weiter unten in diesem Thema finden.

3. Klicken Sie in Visual Studio auf "Erstellen" > "Projektmappe erstellen", um Ihr Projekt zu kompilieren.

4. Führen Sie das Programm wie nachfolgend gezeigt in einem cmd.exe-Befehlsfenster aus. Die tatsächliche Ausgabe einer Ausführung wird ebenfalls gezeigt:


```
[C:\MyVS\EntLib60Retry\EntLib60Retry\bin\Debug]
>> EntLib60Retry.exe

database_firewall_rules_table   245575913
filestream_tombstone_2073058421 2073058421
filetable_updates_2105058535    2105058535

[C:\MyVS\EntLib60Retry\EntLib60Retry\bin\Debug]
>>
```


&nbsp;


## Quellcode für "Program.cs"


Der gesamte Quellcode für dieses EntLib-Beispiel ist in der folgenden Program.cs-Datei enthalten.


```
using     System;   // C#
using G = System.Collections.Generic;
using D = System.Data;
using C = System.Data.SqlClient;
using X = System.Text;
using H = System.Threading;
using Y = Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling;

namespace EntLib60Retry
{
   class Program
   {
      static void Main(string[] args)
      {
         Program program = new Program();

         if (program.Run(args) == false)
         {
            Console.WriteLine("Something was unable to complete.  :-( ");
         }
      }

      bool Run(string[] _args)
      {
         int retryIntervalSeconds = 10;
         bool returnBool = false;

         this.InitializeEntLibObjects();

         for (int tries = 1; tries <= 3; tries++)
         {
            if (tries > 1)
            {
               H.Thread.Sleep(1000 * retryIntervalSeconds);
               retryIntervalSeconds = Convert.ToInt32(retryIntervalSeconds * 1.5);
            }

            try
            {
               this.reliableSqlConnection = new Y.ReliableSqlConnection(
                  this.sqlConnectionSB.ToString(),
                  this.retryPolicy, null
                  );
               this.retryPolicy.ExecuteAction(this.OpenTheConnection_action);  // Open the connection.

               this.dbCommand = this.reliableSqlConnection.CreateCommand();
               this.dbCommand.CommandText = @"
SELECT TOP 3
      ob.name,
      CAST(ob.object_id as nvarchar(32)) as [object_id]
   FROM sys.objects as ob
   WHERE ob.type='IT'
   ORDER BY ob.name;";

               // We retry connection .Open after transient faults, but
               // we do not retry commands that use the connection.
               this.IssueTheQuery();  // Run the query, loop through results.

               returnBool = true;
               break;
            }

            catch (C.SqlException sqlExc)
            {
               if (false == this.sqlDatabaseTransientErrorDetectionStrategy.IsTransient(sqlExc))
               {
                  throw sqlExc;
               }
            }
         }
         return returnBool;
      }

      void OpenTheConnection()  // Called by .ExecuteAction.
      {
         this.reliableSqlConnection.Open();
      }

      void IssueTheQuery()      // Called by .ExecuteAction.
      {
         X.StringBuilder sBuilder = new X.StringBuilder(256);

         this.dataReader = this.dbCommand.ExecuteReader();

         while (this.dataReader.Read())
         {
            sBuilder.Length = 0;
            sBuilder.Append(this.dataReader.GetString(0));
            sBuilder.Append("\t");
            sBuilder.Append(this.dataReader.GetString(1));

            Console.WriteLine(sBuilder.ToString());
         }
      }

      void InitializeEntLibObjects()
      {
         this.InitializeSqlConnectionStringBuilder();

         this.exponentialBackoff = new Y.ExponentialBackoff(
            "exponentialBackoff",
             3,                    // Maximum number of times to retry, then let exception bubble up.
             TimeSpan.FromMilliseconds(10000), // Minimum interval between retries.
             TimeSpan.FromMilliseconds(30000), // Maximum interval between retries.
             TimeSpan.FromMilliseconds( 2000)  // For random differences in interval between retries.
            );
         this.sqlDatabaseTransientErrorDetectionStrategy =
            new Y.SqlDatabaseTransientErrorDetectionStrategy();
         this.retryPolicy = new Y.RetryPolicy(
               this.sqlDatabaseTransientErrorDetectionStrategy,
               this.exponentialBackoff
               );
         this.OpenTheConnection_action = delegate() { this.OpenTheConnection(); };
      }

      void InitializeSqlConnectionStringBuilder()
      {
         // Prepare the connection string to Azure SQL Database.
         this.sqlConnectionSB = new C.SqlConnectionStringBuilder();

         // Change these values to your values.
         this.sqlConnectionSB["Server"] = "tcp:myazuresqldbserver.database.windows.net,1433";
         this.sqlConnectionSB["User ID"] = "MyLogin";  // "@yourservername"  as suffix sometimes.
         this.sqlConnectionSB["Password"] = "MyPassword";
         this.sqlConnectionSB["Database"] = "MyDatabase";

         // Leave these values as they are.
         this.sqlConnectionSB["Trusted_Connection"] = false;
         this.sqlConnectionSB["Integrated Security"] = false;
         this.sqlConnectionSB["Encrypt"] = true;
         this.sqlConnectionSB["Connection Timeout"] = 30;
      }

      Program()   // Constructor.
      {
         int[] arrayOfTransientErrorNumbers =
                { 4060, 10928, 10929, 40197, 40501, 40613, 49918, 49919, 49920
                    //,11001   // 11001 for testing, pretend network error is transient.
                    //,18456   // 18456 for testing, purposely misspell database name.
                };
         listTransientErrorNumbers = new G.List<int>(arrayOfTransientErrorNumbers);
      }

      // Fields.
      private G.List<int> listTransientErrorNumbers;

      private Y.ReliableSqlConnection reliableSqlConnection;
      private Y.ExponentialBackoff exponentialBackoff;
      private Y.SqlDatabaseTransientErrorDetectionStrategy
                   sqlDatabaseTransientErrorDetectionStrategy;
      private Y.RetryPolicy retryPolicy;

      private Action OpenTheConnection_action;

      private C.SqlConnectionStringBuilder sqlConnectionSB;
      private D.IDbCommand dbCommand;
      private D.IDataReader dataReader;
   }
}
```


&nbsp;


## Verwandte Links


- Eine Liste zahlreicher Links zu weiteren Informationen finden Sie hier: [Enterprise Library 6 – April 2013](http://msdn.microsoft.com/library/dn169621.aspx) (in englischer Sprache)
 - Falls Sie den Quellcode anzeigen möchten, finden Sie im oberen Bereich dieses Themas eine Schaltfläche zum [Herunterladen des EntLib60-Quellcodes](http://go.microsoft.com/fwlink/p/?LinkID=290898).


- Kostenloses E-Book im PDF-Format von Microsoft: [Developer's Guide to Microsoft Enterprise Library, 2nd Edition](http://www.microsoft.com/download/details.aspx?id=41145) (in englischer Sprache)


- [Namespace Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling](http://msdn.microsoft.com/library/microsoft.practices.enterpriselibrary.transientfaulthandling.aspx)


- [Referenz zur Enterprise Library 6-Klassenbibliothek](http://msdn.microsoft.com/library/dn170426.aspx)


- [Codebeispiel: Wiederholungslogik in C# für das Herstellen einer Verbindung mit einer SQL-Datenbank mithilfe von ADO.NET](sql-database-develop-csharp-retry-windows.md)


- [Clientcodebeispiele für die ersten Schritte mit SQL-Datenbank](sql-database-develop-quick-start-client-code-samples.md)

<!---HONumber=AcomDC_0316_2016-->