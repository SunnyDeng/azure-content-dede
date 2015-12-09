<properties
	pageTitle="Tutorial – Erste Schritte mit der Azure Batch-Bibliothek für .NET | Microsoft Azure"
	description="Erfahren Sie mehr über grundlegende Konzepte zu Azure Batch, und lesen Sie, wie Sie den Batch-Dienst in einem einfachen Szenario für die Entwicklung einsetzen."
	services="batch"
	documentationCenter=".net"
	authors="yidingzhou"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.tgt_pltfrm="na"
	ms.workload="big-compute"
	ms.date="09/23/2015"
	ms.author="yidingz"/>

# Erste Schritte mit der Azure-Batch-Bibliothek für .NET  

Erstellen Sie zur Verwendung der Azure Batch-Bibliothek für .NET zunächst eine Konsolenanwendung, die Unterstützungsdateien und ein auf mehreren Computeknoten in einem Azure Batch-Pool ausgeführtes Programm einrichtet. Die in diesem Tutorial erstellten Aufgaben werten Text in Dateien aus, die in Azure Storage hochgeladen werden, und geben die in diesen Dateien am häufigsten verwendeten Wörter zurück. Die Beispiele sind in C# geschrieben und verwenden die [Azure Batch-Bibliothek für .NET](https://msdn.microsoft.com/library/azure/mt348682.aspx).

## Voraussetzungen

- Die Konten:

	- **Azure-Konto**: Sie können in wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/).

	- **Batch-Konto**: Siehe [Erstellen und Verwalten eines Batch Azure-Kontos](batch-account-create-portal.md).

	- **Speicherkonto**: Weitere Informationen finden Sie unter [Informationen zu Azure-Speicherkonten](../storage-create-storage-account.md) im Abschnitt **Erstellen eines Speicherkontos**. In diesem Tutorial erstellen Sie einen Container im Konto **testcon1**.

- Ein Projekt für eine Visual Studio-Konsolenanwendung:

	1.  Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** und anschließend auf **Projekt**.

	2.	Klicken Sie in **Fenster** unter **Visual C#** auf **Konsolenanwendung**, weisen Sie dem Projekt den Namen **GettingStarted** und der Lösung den Namen **AzureBatch** zu, und klicken Sie dann auf **OK**.

- Die NuGet-Assemblys:

	1. Nachdem Sie Ihr Projekt in Visual Studio erstellt haben, klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **NuGet-Pakete verwalten** aus. Suchen Sie online nach **Azure.Batch**, und klicken Sie dann auf **Installieren**, um das Microsoft Azure Batch-Paket und die zugehörigen Abhängigkeiten zu installieren.

	2. Suchen Sie online nach **WindowsAzure.Storage**, und klicken Sie auf **Installieren**, um das Azure Storage-Paket und die zugehörigen Abhängigkeiten zu installieren.

> [AZURE.TIP]In diesem Tutorial werden einige der grundlegenden Batch-Konzepte angewendet, die in der [Übersicht über Azure Batch-Features](batch-api-basics.md) erläutert werden. Sollten Sie noch nicht mit Batch gearbeitet haben, empfehlen wir dringend, zunächst dieses Thema zu lesen.

## Schritt 1: Erstellen und Hochladen der erforderlichen Dateien

Zur Unterstützung der Anwendung wird in Azure Storage ein Container erstellt. Außerdem werden Textdateien erstellt, die dann in den Container hochgeladen werden.

### Einrichten der Storage-Verbindungszeichenfolge

1. Öffnen Sie die Datei „App.config“ für das Projekt „GettingStarted“, und fügen Sie dann in *&lt;configuration&gt;* das *&lt;appSettings&gt;*-Element hinzu.

		<?xml version="1.0" encoding="utf-8" ?>
		<configuration>
			<appSettings>
				<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[account-name];AccountKey=[account-key]"/>
			</appSettings>
		</configuration>

	Ersetzen Sie die folgenden Werte:

	- **[account-name]**: Der Name des Speicherkontos, das Sie zuvor erstellt haben.

	- **[account-key]**: Der primäre Schlüssel des Speicherkontos. Den primären Schlüssel finden Sie im Azure-Portal auf der Seite „Storage“.

2. Speichern Sie die Datei "App.config".

Weitere Informationen zu Azure Storage-Verbindungszeichenfolgen finden Sie unter [Konfigurieren von Azure Storage-Verbindungszeichenfolgen](../storage/storage-configure-connection-string.md).

### Erstellen des Speichercontainers

1. Fügen Sie im Projekt „GettingStarted“ am Anfang der Datei „Program.cs“ die folgenden using-Direktiven hinzu:

		using System.Configuration;
		using System.IO;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;

2. Fügen Sie im **Projektmappen-Explorer** unter **Verweise** einen Verweis auf *System.Configuration* für das Projekt „GettingStarted“ hinzu.

3. Fügen Sie diese Methode der Program-Klasse hinzu, die die Storage-Verbindungszeichenfolge abruft, den Container erstellt und die Berechtigungen festlegt:

		static void CreateStorage()
		{
			// Get the storage connection string
			CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
				ConfigurationManager.AppSettings["StorageConnectionString"]);

			// Create the container
			CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
			CloudBlobContainer container = blobClient.GetContainerReference("testcon1");
			container.CreateIfNotExists();

			// Set permissions on the container
			BlobContainerPermissions containerPermissions = new BlobContainerPermissions();
			containerPermissions.PublicAccess = BlobContainerPublicAccessType.Blob;
			container.SetPermissions(containerPermissions);
			Console.WriteLine("Created the container. Press Enter to continue.");
			Console.ReadLine();
		}

4. Fügen Sie diesen Code der Main-Methode hinzu, um die soeben hinzugefügte Methode aufzurufen:

		CreateStorage();

5. Speichern Sie die Datei "Program.cs".

	> [AZURE.NOTE]In einer Produktionsumgebung wird die Verwendung einer [Shared Access Signature (SAS)](https://msdn.microsoft.com/library/azure/ee395415.aspx) empfohlen.

Weitere Informationen zu Blob-Speicher finden Sie unter [Verwenden des Blob-Speichers mit .NET](../storage/storage-dotnet-how-to-use-blobs.md).

### Erstellen des Verarbeitungsprogramms

1. Erstellen Sie im **Projektmappen-Explorer** ein neues Konsolenanwendungsprojekt mit dem Namen **ProcessTaskData**.

2. Nachdem Sie das Projekt in Visual Studio erstellt haben, klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **NuGet-Pakete verwalten** aus. Suchen Sie online nach **WindowsAzure.Storage**, und klicken Sie auf **Installieren**, um das Azure Storage-Paket und die zugehörigen Abhängigkeiten zu installieren.

3. Fügen Sie am Anfang von „Program.cs“ die folgende using-Direktive hinzu:

		using Microsoft.WindowsAzure.Storage.Blob;

4. Fügen Sie der Main-Methode diesen Code hinzu, der den Text aus den Dateien verarbeitet:

		string blobName = args[0];
		Uri blobUri = new Uri(blobName);
		int numTopN = int.Parse(args[1]);

		CloudBlockBlob blob = new CloudBlockBlob(blobUri);
		string content = blob.DownloadText();
		string[] words = content.Split(' ');
		var topNWords =
		  words.
			Where(word => word.Length > 0).
			GroupBy(word => word, (key, group) => new KeyValuePair<String, long>(key, group.LongCount())).
			OrderByDescending(x => x.Value).
			Take(numTopN).
			ToList();

		foreach (var pair in topNWords)
		{
			Console.WriteLine("{0} {1}", pair.Key, pair.Value);
		}

5. Speichern und erstellen Sie das Projekt "ProcessTaskData".

### Erstellen der Datendateien

1. Erstellen Sie im Projekt „GettingStarted“ eine neue Textdatei mit dem Namen **taskdata1.txt**, kopieren Sie den folgenden Text in diese Datei, und speichern Sie die Datei.

	Sie können virtuelle Azure-Computer verwenden, um bedarfsgesteuerte, skalierbare Serverinfrastruktur bereitzustellen, wenn für Ihre Geschäftsanforderungen flexible Ressourcen erforderlich sind. Über den Katalog können Sie virtuelle Computer erstellen, auf denen Windows-, Linux- und Unternehmensanwendungen wie SharePoint und SQL Server ausgeführt werden. Sie können auch eigene Images erfassen und verwenden, um benutzerdefinierte virtuelle Computer zu erstellen.

2. Erstellen Sie eine neue Textdatei mit dem Namen **taskdata2.txt**, kopieren Sie den folgenden Text in diese Datei, und speichern Sie die Datei.

	Mit den Clouddiensten von Azure können Sie schnell leistungsstarke Anwendungen und Dienste bereitstellen und verwalten. Laden Sie einfach Ihre Anwendung hoch, und Azure kümmert sich um die Bereitstellungsdetails – von der Bereitstellung und dem Lastenausgleich über die Integritätsüberwachung bis hin zur kontinuierlichen Verfügbarkeit. Ihre Anwendung unterliegt dabei einer monatlichen SLA mit einem in der Branche führenden Wert von 99,95 Prozent. Sie konzentrieren sich nur auf die Anwendung, nicht auf die Infrastruktur.

3. Erstellen Sie eine neue Textdatei mit dem Namen **taskdata3.txt**, kopieren Sie den folgenden Text in diese Datei, und speichern Sie die Datei.

	Azure-Websites bieten eine skalierbare, zuverlässige und benutzerfreundliche Umgebung für das Hosten von Webanwendungen. Wählen Sie aus einer Vielzahl von Frameworks und Vorlagen aus, um eine Website in Sekunden erstellen zu können. Verwenden Sie zum Entwickeln Ihrer Website mit .NET, PHP, Node.js oder Python beliebige Tools oder Betriebssysteme. Wählen Sie aus einer Vielzahl von Optionen für die Quellcodeverwaltung, einschließlich TFS, GitHub und BitBucket, um eine fortlaufende Integration einzurichten und als Team zu entwickeln. Erweitern Sie die Funktionalität der Website im Laufe der Zeit mithilfe zusätzlicher verwalteter Azure-Diensten wie Speicherung, CDN und SQL-Datenbank.

### Hochladen der Dateien in den Speichercontainer

1. Öffnen Sie die Datei „Program.cs“ des Projekts **GettingStarted**, und fügen Sie die folgende Methode zum Hochladen der Dateien hinzu:

		static void CreateFiles()
		{
		  CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
			ConfigurationManager.AppSettings["StorageConnectionString"]);
		  CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
		  CloudBlobContainer container = blobClient.GetContainerReference("testcon1");

		  CloudBlockBlob taskData1 = container.GetBlockBlobReference("taskdata1");
		  CloudBlockBlob taskData2 = container.GetBlockBlobReference("taskdata2");
		  CloudBlockBlob taskData3 = container.GetBlockBlobReference("taskdata3");
		  taskData1.UploadFromFile("..\\..\\taskdata1.txt", FileMode.Open);
		  taskData2.UploadFromFile("..\\..\\taskdata2.txt", FileMode.Open);
	  	taskData3.UploadFromFile("..\\..\\taskdata3.txt", FileMode.Open);

			CloudBlockBlob storageassembly = container.GetBlockBlobReference("Microsoft.WindowsAzure.Storage.dll");
			storageassembly.UploadFromFile("Microsoft.WindowsAzure.Storage.dll", FileMode.Open);

			CloudBlockBlob dataprocessor = container.GetBlockBlobReference("ProcessTaskData.exe");
		  dataprocessor.UploadFromFile("..\\..\\..\\ProcessTaskData\\bin\\debug\\ProcessTaskData.exe", FileMode.Open);

		  Console.WriteLine("Uploaded the files. Press Enter to continue.");
		  Console.ReadLine();
		}

2. Fügen Sie diesen Code der Main-Methode hinzu, um die soeben hinzugefügte Methode aufzurufen:

		CreateFiles();

3. Speichern Sie die Datei "Program.cs".

## Schritt 2. Hinzufügen eines Pools zum Batch-Konto

Der erste Satz von Ressourcen, den Sie zum Ausführen von Aufgaben erstellen müssen, ist ein Pool von Computeknoten.

1.	Fügen Sie im Projekt „GettingStarted“ am Anfang der Datei „Program.cs“ die folgenden using-Direktiven hinzu:

			using Microsoft.Azure.Batch;
			using Microsoft.Azure.Batch.Auth;
			using Microsoft.Azure.Batch.Common;

2. Fügen Sie der Main-Methode diesen Code hinzu, der die Anmeldeinformationen für die Aufrufe an den Azure Batch-Dienst einrichtet:

			BatchSharedKeyCredentials cred = new BatchSharedKeyCredentials("[account-url]", "[account-name]", "[account-key]");
			BatchClient client = BatchClient.Open(cred);

	Ersetzen Sie die Werte in Klammern durch diejenigen, die zu Ihrem Batch-Konto gehören. Sie finden diese im [Azure-Portal](https://portal.azure.com). Um diese Werte zu finden, melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und:

	- **[account-name]** – Klicken Sie auf **Batch-Konten**, und wählen Sie das zuvor erstellte Batch-Konto aus.
	- **[account-url]** – Klicken Sie auf dem Blatt des Batch-Kontos auf **Eigenschaften** > **URL**.
	- **[account-key]** – Klicken Sie auf dem Blatt des Batch-Kontos auf **Eigenschaften** > **Schlüssel** > **Primärer Zugriffsschlüssel**.

3.	Fügen Sie der Program-Klasse diese Methode hinzu, die den Pool erstellt:

			static void CreatePool(BatchClient client)
			{
			  CloudPool newPool = client.PoolOperations.CreatePool(
			    "testpool1",
			    "3",
			    "small",
			    3);
			  newPool.Commit();
			  Console.WriteLine("Created the pool. Press Enter to continue.");
			  Console.ReadLine();
		  	}

4. Fügen Sie diesen Code der Main-Methode hinzu, um die soeben hinzugefügte Methode aufzurufen:

		CreatePool(client);

5. Fügen Sie der Program-Klasse diese Methode hinzu, die die Pools im Konto auflistet: Damit können Sie überprüfen, ob der Pool erstellt wurde:

			static void ListPools(BatchClient client)
			{
				IPagedEnumerable<CloudPool> pools = client.PoolOperations.ListPools();
				foreach (CloudPool pool in pools)
				{
					Console.WriteLine("Pool name: " + pool.Id);
					Console.WriteLine("   Pool status: " + pool.State);
				}
				Console.WriteLine("Press enter to continue.");
				Console.ReadLine();
			}

6. Fügen Sie diesen Code der Main-Methode hinzu, um die soeben hinzugefügte Methode aufzurufen:

		ListPools(client);

7. Speichern Sie die Datei "Program.cs".

## Schritt 3: Hinzufügen eines Auftrags zum Konto

Erstellen Sie einen Auftrag, mit dem die im Pool ausgeführten Aufgaben verwaltet werden. Alle Aufgaben müssen einem Auftrag zugeordnet werden.

1. Fügen Sie der Program-Klasse diese Methode hinzu, die den Auftrag erstellt:

		static CloudJob CreateJob (BatchClient client)
		{
			CloudJob newJob = client.JobOperations.CreateJob();
			newJob.Id = "testjob1";
			newJob.PoolInformation = new PoolInformation() { PoolId = "testpool1" };
			newJob.Commit();
			Console.WriteLine("Created the job. Press Enter to continue.");
			Console.ReadLine();

			return newJob;
		}

2. Fügen Sie diesen Code der Main-Methode hinzu, um die soeben hinzugefügte Methode aufzurufen:

		CreateJob(client);

3. Fügen Sie der Program-Klasse diese Methode hinzu, die die Aufträge im Konto auflistet: Damit können Sie überprüfen, ob der Auftrag erstellt wurde:

		static void ListJobs (BatchClient client)
		{
			IPagedEnumerable<CloudJob> jobs = client.JobOperations.ListJobs();
			foreach (CloudJob job in jobs)
			{
				Console.WriteLine("Job id: " + job.Id);
				Console.WriteLine("   Job status: " + job.State);
			}
			Console.WriteLine("Press Enter to continue.");
			Console.ReadLine();
		}

4. Fügen Sie diesen Code der Main-Methode hinzu, um die soeben hinzugefügte Methode aufzurufen:

		ListJobs(client);

5. Speichern Sie die Datei "Program.cs".

## Schritt 4: Hinzufügen von Aufgaben zum Auftrag

Nachdem der Auftrag erstellt wurde, können Aufgaben hinzugefügt werden. Jede Aufgabe wird auf einem Serverknoten ausgeführt und verarbeitet eine Textdatei. In diesem Tutorial fügen Sie dem Auftrag drei Aufgaben hinzu.

1. Fügen Sie der Program-Klasse diese Methode hinzu, die dem Auftrag drei Aufgaben hinzufügt:

		static void AddTasks(BatchClient client)
		{
			CloudJob job = client.JobOperations.GetJob("testjob1");
			ResourceFile programFile = new ResourceFile(
				"https://[account-name].blob.core.windows.net/testcon1/ProcessTaskData.exe",
				"ProcessTaskData.exe");
      	  ResourceFile assemblyFile = new ResourceFile(
				"https://[account-name].blob.core.windows.net/testcon1/Microsoft.WindowsAzure.Storage.dll",
				"Microsoft.WindowsAzure.Storage.dll");
			for (int i = 1; i < 4; ++i)
			{
				string blobName = "taskdata" + i;
				string taskName = "mytask" + i;
				ResourceFile taskData = new ResourceFile("https://[account-name].blob.core.windows.net/testcon1/" +
				  blobName, blobName);
				CloudTask task = new CloudTask(taskName, "ProcessTaskData.exe https://[account-name].blob.core.windows.net/testcon1/" +
				  blobName + " 3");
				List<ResourceFile> taskFiles = new List<ResourceFile>();
				taskFiles.Add(taskData);
				taskFiles.Add(programFile);
				taskFiles.Add(assemblyFile);
				task.ResourceFiles = taskFiles;
				job.AddTask(task);
				job.Commit();
				job.Refresh();
			}

			client.Utilities.CreateTaskStateMonitor().WaitAll(job.ListTasks(),
        TaskState.Completed, new TimeSpan(0, 30, 0));
			Console.WriteLine("The tasks completed successfully.");
			foreach (CloudTask task in job.ListTasks())
			{
				Console.WriteLine("Task " + task.Id + " says:\n" + task.GetNodeFile(Constants.StandardOutFileName).ReadAsString());
			}
			Console.WriteLine("Press Enter to continue.");
			Console.ReadLine();
		}


	**[account-name]** muss durch den Namen des Speicherkontos ersetzt werden, das Sie zuvor erstellt haben. Aktualisieren Sie im vorangehenden Beispiel alle vier Instanzen von **[account-name]**.


2. Fügen Sie diesen Code der Main-Methode hinzu, um die soeben hinzugefügte Methode aufzurufen:

			AddTasks(client);

3. Fügen Sie der Programm-Klasse diese Methode hinzu, die die Aufgaben auflistet, die dem Auftrag zugeordnet sind:

		static void ListTasks(BatchClient client)
		{
			IPagedEnumerable<CloudTask> tasks = client.JobOperations.ListTasks("testjob1");
			foreach (CloudTask task in tasks)
			{
				Console.WriteLine("Task id: " + task.Id);
				Console.WriteLine("   Task status: " + task.State);
			  Console.WriteLine("   Task start: " + task.ExecutionInformation.StartTime);
			}
			Console.ReadLine();
		}

4. Fügen Sie diesen Code der Main-Methode hinzu, um die soeben hinzugefügte Methode aufzurufen:

		ListTasks(client);

5. Speichern Sie die Datei "Program.cs".

## Schritt 5: Löschen der Ressourcen

Da Ihnen Ressourcen in Azure in Rechnung gestellt werden, sollten Sie immer alle nicht mehr benötigten Ressourcen löschen.

### Löschen der Aufgaben

1.	Fügen Sie der Program-Klasse diese Methode hinzu, die die Aufgaben löscht:

			static void DeleteTasks(BatchClient client)
			{
				CloudJob job = client.JobOperations.GetJob("testjob1");
				foreach (CloudTask task in job.ListTasks())
				{
					task.Delete();
				}
				Console.WriteLine("All tasks deleted.");
				Console.ReadLine();
			}

2. Fügen Sie diesen Code der Main-Methode hinzu, um die soeben hinzugefügte Methode aufzurufen:

		DeleteTasks(client);

3. Speichern Sie die Datei "Program.cs".

### Löschen des Auftrags

1.	Fügen Sie der Program-Klasse diese Methode hinzu, die den Auftrag löscht:

			static void DeleteJob(BatchClient client)
			{
				client.JobOperations.DeleteJob("testjob1");
				Console.WriteLine("Job was deleted.");
				Console.ReadLine();
			}

2. Fügen Sie diesen Code der Main-Methode hinzu, um die soeben hinzugefügte Methode aufzurufen:

		DeleteJob(client);

3. Speichern Sie die Datei "Program.cs".

### Löschen des Pools

1. Fügen Sie der Program-Klasse die folgende Methode zum Löschen des Pools hinzu:

		static void DeletePool (BatchClient client)
		{
			client.PoolOperations.DeletePool("testpool1");
			Console.WriteLine("Pool was deleted.");
			Console.ReadLine();
		}

2. Fügen Sie diesen Code der Main-Methode hinzu, um die soeben hinzugefügte Methode aufzurufen:

		DeletePool(client);

3. Speichern Sie die Datei "Program.cs".

## Schritt 6: Ausführen der Anwendung

1. Starten Sie das GettingStarted-Projekt. Im Konsolenfenster sollte nach dem Erstellen des Containers Folgendes angezeigt werden:

		Created the container. Press Enter to continue.

2. Drücken Sie die EINGABETASTE. Daraufhin werden die Dateien erstellt und hochgeladen. Im Fenster sollte jetzt eine neue Zeile angezeigt werden:

		Uploaded the files. Press Enter to continue.

3. Drücken Sie die EINGABETASTE, um den Pool zu erstellen:

		Created the pool. Press Enter to continue.

4. Drücken Sie die EINGABETASTE. Es sollte diese Auflistung des neuen Pools angezeigt werden:

		Pool name: testpool1
			Pool status: Active
		Press Enter to continue.

5. Drücken Sie die EINGABETASTE, um den Auftrag zu erstellen:

		Created the job. Press Enter to continue.

6. Drücken Sie die EINGABETASTE. Es sollte diese Auflistung des neuen Auftrags angezeigt werden:

		Job id: testjob1
			Job status: Active
		Press Enter to continue.

7. Drücken Sie die EINGABETASTE, um die Aufgaben dem Projekt hinzuzufügen. Wenn die Aufgaben hinzugefügt werden, werden sie automatisch ausgeführt:

		The tasks completed successfully.
		Task mytask1 says:
		can 3
		you 3
		and 3

		Task mytask2 says:
		and 5
		application 3
		the 3

		Task mytask3 says:
		a 5
		and 5
		to 3

		Press Enter to continue.

7. Drücken Sie die EINGABETASTE. Es sollte eine Liste mit den Aufgaben mit deren Status angezeigt werden:

		Task id: mytask1
			Task status: Completed
			Task start: 7/17/2015 8:31:58 PM
		Task id: mytask2
			Task status: Completed
			Task start: 7/17/2015 8:31:57 PM
		Task id: mytask3
			Task status: Completed
			Task start: 7/17/2015 8:31:57 PM

8. Jetzt können Sie im Azure-Portal die erstellten Ressourcen überprüfen. Zum Löschen der Ressourcen drücken Sie die EINGABETASTE, bis das Programm beendet wird.

## Nächste Schritte

1. Nachdem Sie sich mit den Grundlagen der Ausführung von Aufgaben vertraut gemacht haben, können Sie sich nun damit befassen, wie Sie Computeknoten automatisch entsprechend den Anforderungen an Ihre Anwendung skalieren. Informationen dazu finden Sie unter [Automatisches Skalieren von Computeknoten in einem Azure Batch-Pool](batch-automatic-scaling.md).

2. Einige Anwendungen erzeugen große Datenmengen, die nur schwer zu verarbeiten sind. Eine Möglichkeit zur Lösung dieses Problems ist die Verwendung [effizienter Listenabfragen](batch-efficient-list-queries.md).

<!---HONumber=AcomDC_1203_2015-->