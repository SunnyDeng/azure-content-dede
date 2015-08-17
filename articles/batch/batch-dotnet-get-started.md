<properties
	pageTitle="Lernprogramm – Erste Schritte mit der Azure Batch-Bibliothek für .NET"
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
	ms.date="07/21/2015"
	ms.author="yidingz"/>

# Erste Schritte mit der Azure-Batch-Bibliothek für .NET  

In diesem Lernprogramm erfahren Sie, wie Sie eine Konsolenanwendung erstellen, die ein Programm mit den erforderlichen Dateien einrichtet, das auf mehreren Computeknoten in einem Azure Batch-Pool ausgeführt wird. Die in diesem Lernprogramm erstellten Aufgaben werten Text aus Dateien, die im Azure-Speicher enthalten sind, aus und geben die Wörter zurück, die am häufigsten verwendet werden. Die Beispiele sind in C# geschrieben und greifen auf die Azure Batch-Bibliothek für .NET zurück.

## Voraussetzungen

- Die Konten:

	- **Azure-Konto:** Sie können ein kostenloses Testkonto in wenigen Minuten erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/).

	- **Batch-Konto:** Informationen zur Erstellung finden Sie unter [Azure Batch – Technische Übersicht](batch-technical-overview.md) im Abschnitt **Batch-Konto**.

	- **Speicherkonto:** Weitere Informationen finden Sie im Abschnitt **Erstellen eines Speicherkontos** in [Informationen zu Azure-Speicherkonten](../storage-create-storage-account.md). In diesem Lernprogramm erstellen Sie einen Container in diesem Konto mit dem Namen **testcon1**.

- Ein Projekt für eine Visual Studio-Konsolenanwendung:

	1.  Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** und anschließend auf **Projekt**.

	2.	Klicken Sie in **Fenster** unter **Visual C#** auf **Konsolenanwendung**, weisen Sie dem Projekt den Namen **GettingStarted** und der Lösung den Namen **AzureBatch** zu, und klicken Sie dann auf **OK**.

- Die NuGet-Assemblys:

	1. Nachdem Sie Ihr Projekt in Visual Studio erstellt haben, klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **NuGet-Pakete verwalten** aus. Suchen Sie online nach **Azure.Batch**, und klicken Sie dann auf **Installieren**, um das Microsoft Azure Batch-Paket und die zugehörigen Abhängigkeiten zu installieren.

	2. Suchen Sie online nach **WindowsAzure.Storage**, und klicken Sie auf **Installieren**, um das Azure Storage-Paket und die zugehörigen Abhängigkeiten zu installieren.

## Schritt 1: Erstellen und Hochladen der erforderlichen Dateien

Zur Unterstützung der Anwendung wird in Azure Storage ein Container erstellt. Außerdem werden Textdateien erstellt, die dann in den Container hochgeladen werden.

### Einrichten der Storage-Verbindungszeichenfolge

1. Öffnen Sie die Datei "App.config" für das Projekt "GettingStarted", und fügen Sie dann in &lt;configuration&gt; das &lt;appSettings&gt;-Element hinzu.

		<?xml version="1.0" encoding="utf-8" ?>
		<configuration>
			<appSettings>
				<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[account-name];AccountKey=[account-key]"/>
			</appSettings>
		</configuration>

	Ersetzen Sie die folgenden Werte:

	- **[account-name]**: der Name des Speicherkontos, das Sie zuvor erstellt haben

	- **[account-key]**: der primäre Schlüssel für das Speicherkonto. Sie finden den primären Schlüssel im Verwaltungsportal auf der Seite "Storage"

2. Speichern Sie die Datei "App.config".

Weitere Informationen finden Sie unter [Konfigurieren von Azure-Verbindungszeichenfolgen](http://msdn.microsoft.com/library/windowsazure/ee758697.aspx).

### Erstellen des Speichercontainers

1. Fügen Sie im Projekt "GettingStarted" am Anfang der Datei "Program.cs" diese Namespacedeklarationen hinzu:

		using System.Configuration;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;

2. Fügen Sie diese Methode der Program-Klasse hinzu, die die Storage-Verbindungszeichenfolge abruft, den Container erstellt und die Berechtigungen festlegt:

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

3. Fügen Sie diesen Code der Main-Methode hinzu, um die soeben hinzugefügte Methode aufzurufen:

		CreateStorage();

4. Speichern Sie die Datei "Program.cs".

	> [AZURE.NOTE]In einer Produktionsumgebung wird empfohlen, dass Sie eine SAS (Shared Access Signature) verwenden.

Weitere Informationen finden Sie unter [Verwenden von Blob-Speicher aus .NET](../storage-dotnet-how-to-use-blobs.md).

### Erstellen des Verarbeitungsprogramms

1. Erstellen Sie im Projektmappen-Explorer ein neues Konsolenanwendungsprojekt namens **ProcessTaskData**.

2. Fügen Sie der Main-Methode diesen Code hinzu, der den Text aus den Dateien verarbeitet:

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

3. Speichern und erstellen Sie das Projekt "ProcessTaskData".

### Erstellen der Datendateien

1. Erstellen Sie im Projekt "GettingStarted" eine neue Textdatei mit dem Namen **taskdata1**, kopieren Sie den folgenden Text in diese Datei, und speichern Sie die Datei.

	Sie können virtuelle Azure-Computer verwenden, um bedarfsgesteuerte, skalierbare Serverinfrastruktur bereitzustellen, wenn für Ihre Geschäftsanforderungen flexible Ressourcen erforderlich sind. Über den Katalog können Sie virtuelle Computer erstellen, auf denen Windows-, Linux- und Unternehmensanwendungen wie SharePoint und SQL Server ausgeführt werden. Sie können auch eigene Images erfassen und verwenden, um benutzerdefinierte virtuelle Computer zu erstellen.

2. Erstellen Sie eine neue Textdatei mit dem Namen **taskdata2**, kopieren Sie den folgenden Text in diese Datei, und speichern Sie die Datei.

	Mit den Clouddiensten von Azure können Sie schnell leistungsstarke Anwendungen und Dienste bereitstellen und verwalten. Laden Sie einfach Ihre Anwendung hoch, und Azure kümmert sich um die Bereitstellungsdetails – von der Bereitstellung und dem Lastenausgleich über die Integritätsüberwachung bis hin zur kontinuierlichen Verfügbarkeit. Ihre Anwendung unterliegt dabei einer monatlichen SLA mit einem in der Branche führenden Wert von 99,95 Prozent. Sie konzentrieren sich nur auf die Anwendung, nicht auf die Infrastruktur.

3. Erstellen Sie eine neue Textdatei mit dem Namen **taskdata3**, kopieren Sie den folgenden Text in diese Datei, und speichern Sie die Datei.

	Azure-Websites bieten eine skalierbare, zuverlässige und benutzerfreundliche Umgebung für das Hosten von Webanwendungen. Wählen Sie aus einer Vielzahl von Frameworks und Vorlagen aus, um eine Website in Sekunden erstellen zu können. Verwenden Sie zum Entwickeln Ihrer Website mit .NET, PHP, Node.js oder Python beliebige Tools oder Betriebssysteme. Wählen Sie aus einer Vielzahl von Optionen für die Quellcodeverwaltung, einschließlich TFS, GitHub und BitBucket, um eine fortlaufende Integration einzurichten und als Team zu entwickeln. Erweitern Sie die Funktionalität der Website im Laufe der Zeit mithilfe zusätzlicher verwalteter Azure-Diensten wie Speicherung, CDN und SQL-Datenbank.

### Hochladen der Dateien in den Container

1. Öffnen Sie die Datei "Program.cs" des Projekts "GettingStarted", und fügen Sie diese Methode hinzu, die die Dateien hochlädt:

		static void CreateFiles()
		{
		  privateCloudStorageAccount storageAccount = CloudStorageAccount.Parse(
			ConfigurationManager.AppSettings["StorageConnectionString"]);
		  CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
		  CloudBlobContainer container = blobClient.GetContainerReference("testcon1");
		  CloudBlockBlob taskData1 = container.GetBlockBlobReference("taskdata1");
		  CloudBlockBlob taskData2 = container.GetBlockBlobReference("taskdata2");
		  CloudBlockBlob taskData3 = container.GetBlockBlobReference("taskdata3");
	  	CloudBlockBlob dataprocessor = container.GetBlockBlobReference("ProcessTaskData.exe");
	  	CloudBlockBlob storageassembly =
			container.GetBlockBlobReference("Microsoft.WindowsAzure.Storage.dll");
		  taskData1.UploadFromFile("..\\..\\taskdata1.txt", FileMode.Open);
		  taskData2.UploadFromFile("..\\..\\taskdata2.txt", FileMode.Open);
	  	taskData3.UploadFromFile("..\\..\\taskdata3.txt", FileMode.Open);
		  dataprocessor.UploadFromFile("..\\..\\..\\ProcessTaskData\\bin\\debug\\ProcessTaskData.exe", FileMode.Open);
		  storageassembly.UploadFromFile("Microsoft.WindowsAzure.Storage.dll", FileMode.Open);
		  Console.WriteLine("Uploaded the files. Press Enter to continue.");
		  Console.ReadLine();
		}

2. Speichern Sie die Datei "Program.cs".

## Schritt 2. Hinzufügen eines Pools zu Ihrem Konto

Der erste Satz von Ressourcen, den Sie zum Ausführen von Aufgaben erstellen müssen, ist ein Pool von Computeknoten.

1.	Fügen Sie im Projekt "GettingStarted" am Anfang der Datei "Program.cs" diese Namespacedeklarationen hinzu:

			using Microsoft.Azure.Batch;
			using Microsoft.Azure.Batch.Auth;
			using Microsoft.Azure.Batch.Common;

2. Fügen Sie der Main-Methode diesen Code hinzu, der die Anmeldeinformationen für die Aufrufe an den Azure Batch-Dienst einrichtet:

			BatchSharedKeyCredentials cred = new BatchSharedKeyCredentials("https://[account-name].[region].batch.azure.com", "[account-name]", "[account-key]");
			BatchClient client = BatchClient.Open(cred);

	Ersetzen Sie die folgenden Werte:

	- **[account-name]**: der Name des Batch-Kontos, das Sie zuvor erstellt haben
	- **[region]**: die Region, in der sich das Konto befindet. Informationen zum Ermitteln der verfügbaren Regionen finden Sie unter [Azure-Regionen](http://azure.microsoft.com/regions/).
	- **[account-key]**: der primäre Schlüssel für das Batch-Konto.

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

## Schritt 2: Hinzufügen eines Auftrags zu einem Konto

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

## Schritt 3: Hinzufügen von Aufgaben zum Auftrag.

Nachdem der Auftrag erstellt wurde, können Aufgaben hinzugefügt werden. Jede Aufgabe wird auf einem Serverknoten ausgeführt und verarbeitet eine Textdatei. In diesem Lernprogramm fügen Sie dem Auftrag drei Aufgaben hinzu.

1. Fügen Sie der Program-Klasse diese Methode hinzu, die dem Auftrag drei Aufgaben hinzufügt:

		static void AddTasks(BatchClient client)
		{
			CloudJob job = client.JobOperations.GetJob("testjob1");
			ResourceFile programFile = new ResourceFile(
				"https://[account-name].blob.azure.com/[]/ProcessTaskData.exe",
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

	**[account-name]**: muss durch den Namen des Speicherkontos ersetzt werden, das Sie zuvor erstellt haben Stellen Sie sicher, dass Sie alle vier Stellen erhalten.

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

## Schritt 4: Löschen der Ressourcen

Da Ihnen die Ressourcen in Azure in Rechnung gestellt werden, sollten Sie immer alle Ressourcen, die Sie nicht mehr benötigen, löschen.

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

2. Fügen Sie diesen Code der Main-Methode hinzu, um die soeben hinzugefügte Methode auszuführen:

		DeleteJob(client);

3. Speichern Sie die Datei "Program.cs".

### Löschen des Pools

1. Fügen Sie der Program-Klasse diese Methode hinzu, die den Pool löscht:

		static void DeletePool (BatchClient client)
		{
			client.PoolOperations.DeletePool("testpool1");
			Console.WriteLine("Pool was deleted.");
			Console.ReadLine();
		}

2. Fügen Sie diesen Code der Main-Methode hinzu, um die soeben hinzugefügte Methode auszuführen:

		DeletePool(client);

3. Speichern Sie die Datei "Program.cs".

## Schritt 5: Ausführen der Anwendung

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

8. An diesem Punkt können Sie zum Azure-Portal wechseln, um die erstellten Ressourcen anzuzeigen. Zum Löschen der Ressourcen drücken Sie die EINGABETASTE, bis das Programm beendet wird.

## Nächste Schritte

1. Nachdem Sie sich mit den Grundlagen der Ausführung von Aufgaben vertraut gemacht haben, können Sie etwas darüber erfahren, wie Computeknoten automatisch bei Änderungen der Anforderung an Ihre Anwendung skaliert werden. Informationen dazu finden Sie unter [Automatisches Skalieren von Computeknoten in einem Azure Batch-Pool](batch-automatic-scaling.md).

2. Einige Anwendungen erzeugen große Datenmengen, die nur schwer zu verarbeiten sind. Eine Möglichkeit, dies zu lösen, besteht in [effizienten Listenabfragen](batch-efficient-list-queries.md).

<!---HONumber=August15_HO6-->