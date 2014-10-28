<properties linkid="develop-net-tutorials-compute-intensive-task-on-a-virtual-machine" urlDisplayName="Compute Intensive .NET Task" pageTitle="Compute intensive .NET task on a virtual machine - Azure" metaKeywords="deploying compute .NET application, vm .NET application, Service Bus queue monitoring, remote monitoring" description="Learn how to deploy and run a compute-intensive .NET app on an Azure virtual machine and use Service Bus queues to monitor progress remotely." metaCanonical="" services="virtual-machines" documentationCenter=".NET" title="How to run a compute-intensive task in .NET on an Azure virtual machine" authors="wpickett" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="wpickett"></tags>

# Ausführen einer rechenintensiven Aufgabe in .NET auf einem virtuellen Azure-Computer

Mit Azure können Sie einen virtuellen Computer für rechenintensive Aufgaben verwenden. Ein virtueller Computer kann z. B. Aufgaben verarbeiten und die Ergebnisse an Clientcomputer oder mobile Anwendungen übermitteln. Nach Abschluss dieses Leitfadens wissen Sie, wie ein virtueller Computer erstellt wird, der eine rechenintensive .NET-Anwendung ausführt, die durch eine andere .NET-Anwendung überwacht werden kann.

Dieses Lernprogramm setzt voraus, dass Sie wissen, wie .NET-Konsolenanwendungen erstellt werden. Wissen über Azure ist keine Voraussetzung.

Sie erhalten Informationen zu folgenden Themen:

-   Erstellen eines virtuellen Computers
-   Remoteanmeldung an Ihrem virtuellen Computer
-   Erstellen eines Service Bus-Namespace
-   Erstellen einer .NET-Anwendung, die eine rechenintensive Aufgabe ausführt
-   Erstellen einer .NET-Anwendung, die den Fortschritt der rechenintensiven Aufgabe überwacht
-   Ausführen der .NET-Anwendungen
-   Anhalten der .NET-Anwendungen

Dieses Lernprogramm verwendet das "Traveling Salesman"-Problem für die rechenintensive Aufgabe. Es folgt ein Beispiel der .NET-Anwendung, in der die rechenintensive Aufgabe ausgeführt wird:

!["Traveling Salesman"-Problemlösung]["Traveling Salesman"-Problemlösung]

Es folgt ein Beispiel der .NET-Anwendung, welche die rechenintensive Aufgabe überwacht:

!["Traveling Salesman"-Problemclient]["Traveling Salesman"-Problemclient]

[WACOM.INCLUDE [create-account-and-vms-note][create-account-and-vms-note]]

## So erstellen Sie einen virtuellen Computer

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an.
2.  Klicken Sie auf **Neu**.
3.  Klicken Sie auf **Virtueller Computer**.
4.  Klicken Sie auf **Schnellerfassung**.
5.  Geben Sie auf dem Bildschirm **Virtuellen Computer erstellen** einen Wert für **DNS-Name** ein.
6.  Wählen Sie in der Dropdownliste **Image** ein Image aus, z. B. **Windows Server 2012**.
7.  Geben Sie im Feld **Benutzername** einen Namen für den Administrator ein. Merken Sie sich diesen Namen und das als nächstes eingegebene Kennwort. Sie benötigen diese Daten, wenn Sie sich von einem Remotestandort aus an dem virtuellen Computer anmelden.
8.  Geben Sie ein Kennwort in das Feld **Neues Kennwort** ein, und geben Sie das Kennwort dann erneut in das Feld **Kennwort bestätigen** ein.
9.  Wählen Sie in der Dropdownliste **Standort** den Datencenterstandort Ihres virtuellen Computers aus.
10. Klicken Sie auf **Virtuellen Computer erstellen**. Der virtuelle Computer wird jetzt erstellt. Sie können den Status im Abschnitt **Virtuelle Computer** des Verwaltungsportals überwachen. Wenn als Status des virtuellen Computers **Aktiv** angezeigt wird, können Sie sich am virtuellen Computer anmelden.

## So melden Sie sich von einem Remotestandort aus an Ihrem virtuellen Computer an

1.  Melden Sie sich am [Verwaltungsportal][Azure-Verwaltungsportal] an.
2.  Klicken Sie auf **Virtuelle Computer**.
3.  Klicken Sie auf den Namen des virtuellen Computers, an dem Sie sich anmelden möchten.
4.  Klicken Sie auf **Verbinden**.
5.  Befolgen Sie die Anweisungen, um eine Verbindung mit dem virtuellen Computer herzustellen. Wenn Sie zur Eingabe des Administratornamens und des Kennworts aufgefordert werden, verwenden Sie die Werte, die Sie beim Erstellen des virtuellen Computers bereitgestellt haben.

## Erstellen eines Service Bus-Namespace

Um mit der Verwendung von Servicebus-Warteschlangen in Azure beginnen zu können,
müssen Sie zuerst einen Dienstnamespace erstellen. Ein Dienstnamespace ist eine Bereichseinheit
zur Adressierung von Servicebus-Ressourcen innerhalb Ihrer Anwendung.

So erstellen Sie einen Dienstnamespace

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an.
2.  Klicken Sie im linken Navigationsbereich des Verwaltungsportals auf **Servicebus**.
3.  Klicken Sie im unteren Bereich des Verwaltungsportals auf **Erstellen**.

    ![Neuen Service Bus erstellen][Neuen Service Bus erstellen]

4.  Geben Sie im Dialogfeld **Namespace erstellen** einen Namen für den Namespace ein. Es wird sofort geprüft, ob der Name verfügbar ist, da der Name eindeutig sein muss.

    ![Dialogfeld "Namespace erstellen"][Dialogfeld "Namespace erstellen"]

5.  Nachdem Sie sichergestellt haben, dass der Namespacename verfügbar ist, wählen Sie die Region aus, in der der Namespace gehostet werden soll (verwenden Sie dieselbe Region, in der auch der virtuelle Computer gehostet wird).
    <div class="dev-callout"><br /><strong>Wichtig</strong><br /><p>W&auml;hlen Sie <strong>dieselbe Region</strong> aus, die Sie f&uuml;r den virtuellen Computer verwenden oder verwenden m&ouml;chten. Dies sorgt f&uuml;r die beste Leistung.</p><br /></div>

6.  Wenn Sie mehr als ein Azure-Abonnement für das Konto besitzen, mit dem Sie angemeldet sind, wählen Sie das Abonnement aus, das Sie für den Namespace verwenden möchten. (Wenn Sie nur ein Abonnement für das Konto haben, mit dem Sie angemeldet sind, wird keine Dropdownliste Ihrer Abonnements angezeigt.)
7.  Aktivieren Sie das Kontrollkästchen. Ihr Dienstnamespace wird nun erstellt und aktiviert. Eventuell müssen Sie einige Minuten warten, bis die Ressourcen für Ihr Konto bereitgestellt werden.

    ![Screenshot beim Klicken auf "Erstellen"][Screenshot beim Klicken auf "Erstellen"]

Der neue Namespace wird anschließend im Verwaltungsportal angezeigt und in kurzer Zeit aktiviert. Warten Sie, bis **Active** als Status angezeigt wird, bevor Sie mit dem nächsten Schritt fortfahren.

## Abrufen der Standard-Anmeldeinformationen für den Namespace

Um Verwaltungsvorgänge für den neuen Namespace auszuführen, wie zum
Beispiel das Erstellen einer Warteschlange, müssen Sie die Verwaltungsanmeldeinformationen
für den Namespace abrufen.

1.  Klicken Sie im linken Navigationsbereich auf den Knoten **Service Bus**,
    um die Liste verfügbarer Namespaces anzuzeigen:
    ![Screenshot der verfügbaren Namespaces][Screenshot der verfügbaren Namespaces]
2.  Wählen Sie in der angezeigten Liste den Namespace, den Sie gerade erstellt haben:
    ![Screenshot der Namespaceliste][Screenshot der Namespaceliste]
3.  Klicken Sie auf **Zugriffsschüssel**.
    ![Schaltfläche "Zugriffsschlüssel"][Schaltfläche "Zugriffsschlüssel"]
4.  Suchen Sie im Dialogfeld nach den Einträgen **Standardaussteller** und **Standardschlüssel**. Notieren Sie sich diese Werte, da Sie diese Informationen später benötigen, um Operationen mit dem Namespace durchzuführen.

## Erstellen einer .NET-Anwendung, die eine rechenintensive Aufgabe ausführt

1.  Laden Sie auf dem Entwicklungscomputer (der nicht mit dem erstellten virtuellen Computer identisch sein muss) das [Azure-SDK für .NET][Azure-SDK für .NET] herunter.
2.  Erstellen Sie eine .NET-Konsolenanwendung mit dem Projekt namens **TSPSolver**. Stellen Sie sicher, dass als Zielframework .**NET Framework 4** (nicht **.NET Framework 4 Client Profile**) eingestellt ist. Nach dem Erstellen eines Projekts kann das Zielframework folgendermaßen eingestellt werden: Klicken Sie im Menü von Visual Studio auf **Projekte**, klicken Sie auf **Eigenschaften**, klicken Sie auf die Registerkarte **Anwendung**, und legen Sie anschließend den Wert für **Zielframework** fest.
3.  Fügen Sie die Microsoft ServiceBus-Bibliothek hinzu. Klicken Sie im Projektmappen-Explorer von Visual Studio mit der rechten Maustaste auf **TSPSolver**, klicken Sie auf **Verweis hinzufügen**, klicken Sie auf die Registerkarte **Durchsuchen**, navigieren Sie zu **C:\\Programme\\Microsoft SDKs\\Windows Azure.NET SDK\\2012-06\\ref**, und wählen Sie **Microsoft.ServiceBus.dll** als Referenz aus.
4.  Fügen Sie die System.Runtime.Serialization-Bibliothek hinzu. Klicken Sie im Projektmappen-Explorer von Visual Studio mit der rechten Maustaste auf **TSPSolver**, klicken Sie auf **Verweis hinzufügen**, klicken Sie auf die Registerkarte **.NET**, und wählen Sie **System.Runtime.Serialization** als Verweis aus.
5.  Verwenden Sie das Codebeispiel am Ende dieses Abschnitts als Inhalt der Datei **Program.cs**.
6.  Ändern Sie die Platzhalter **your\_service\_bus\_namespace**, **your\_service\_bus\_owner** und **your\_service\_bus\_key** so, dass Ihre Service Bus-Werte für **Namespace**, **Standardaussteller** und **Standardschlüssel** verwendet werden.
7.  Kompilieren Sie die Anwendung. Damit wird **TSPSolver.exe** im Ordner **bin** des Projekts (entweder **bin\\release** oder **bin\\debug**, je nachdem, ob Sie als Ziel eine Releaseversion oder eine Debugversion vorgesehen haben) erstellt. Später kopieren Sie diese ausführbare Datei und Microsoft.ServiceBus.dll auf den virtuellen Computer.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.IO;

    using Microsoft.ServiceBus;
    using Microsoft.ServiceBus.Messaging;

    namespace TSPSolver
    {
        class Program
        {
            // Value specifying how often to provide an update to the console.
            private static long loopCheck = 100000000;
            private static long nTimes = 0, nLoops = 0;

            private static double[,] distances;
            private static String[] cityNames;
            private static int[] bestOrder;
            private static double minDistance;

            private static NamespaceManager namespaceManager;
            private static QueueClient queueClient;
            private static String queueName = "TSPQueue";

            private static void BuildDistances(String fileLocation, int numCities)
            {

                try
                {
                    StreamReader sr = new StreamReader(fileLocation);
                    String[] sep1 = { ", " };

                    double[,] cityLocs = new double[numCities, 2];

                    for (int i = 0; i < numCities; i++)
                    {
                        String[] line = sr.ReadLine().Split(sep1, StringSplitOptions.None);
                        cityNames[i] = line[0];
                        cityLocs[i, 0] = Convert.ToDouble(line[1]);
                        cityLocs[i, 1] = Convert.ToDouble(line[2]);
                    }
                    sr.Close();

                    for (int i = 0; i < numCities; i++)
                    {
                        for (int j = i; j < numCities; j++)
                        {
                            distances[i, j] = hypot(Math.Abs(cityLocs[i, 0] - cityLocs[j, 0]), Math.Abs(cityLocs[i, 1] - cityLocs[j, 1]));
                            distances[j, i] = distances[i, j];
                        }
                    }
                }
                catch (Exception e)
                {
                    throw e;
                }
            }

            private static double hypot(double x, double y)
            {
                return Math.Sqrt(x * x + y * y);
            }

            private static void permutation(List<int> startCities, double distSoFar, List<int> restCities)
            {
                try
                {

                    nTimes++;
                    if (nTimes == loopCheck)
                    {
                        nLoops++;
                        nTimes = 0;
                        DateTime dateTime = DateTime.Now;
                        Console.Write("Current time is {0}.", dateTime);
                        Console.WriteLine(" Completed {0} iterations of size of {1}.", nLoops, loopCheck);
                    }

                    if ((restCities.Count == 1) && ((minDistance == -1) || (distSoFar + distances[restCities[0], startCities[0]] + distances[restCities[0], startCities[startCities.Count - 1]] < minDistance)))
                    {
                        startCities.Add(restCities[0]);
                        newBestDistance(startCities, distSoFar + distances[restCities[0], startCities[0]] + distances[restCities[0], startCities[startCities.Count - 2]]);
                        startCities.Remove(startCities[startCities.Count - 1]);
                    }
                    else
                    {
                        for (int i = 0; i < restCities.Count; i++)
                        {
                            startCities.Add(restCities[0]);
                            restCities.Remove(restCities[0]);
                            permutation(startCities, distSoFar + distances[startCities[startCities.Count - 1], startCities[startCities.Count - 2]], restCities);
                            restCities.Add(startCities[startCities.Count - 1]);
                            startCities.Remove(startCities[startCities.Count - 1]);
                        }
                    }
                }
                catch (Exception e)
                {
                    throw e;
                }
            }

            private static void newBestDistance(List<int> cities, double distance)
            {
                try
                {
                    minDistance = distance;
                    String cityList = "Shortest distance is " + minDistance + ", with route: ";

                    for (int i = 0; i < bestOrder.Length; i++)
                    {
                        bestOrder[i] = cities[i];
                        cityList += cityNames[bestOrder[i]];
                        if (i != bestOrder.Length - 1)
                            cityList += ", ";
                    }
                    Console.WriteLine(cityList);
                    queueClient.Send(new BrokeredMessage(cityList));
                }
                catch (Exception e)
                {
                    throw e;
                }
            }

            static void Main(string[] args)
            {
                try
                {

                    String serviceBusNamespace = "your_service_bus_namespace";
                    String issuer = "your_service_bus_owner";
                    String key = "your_service_bus_key";

                    String connectionString = @"Endpoint=sb://" +
                           serviceBusNamespace +
                           @".servicebus.windows.net/;SharedSecretIssuer=" +
                           issuer + @";SharedSecretValue=" + key;

                    int numCities = 10; // Use as the default, if no value is specified
                    // at the command line.
                    if (args.Count() != 0)
                    {

                        if (args[0].ToLower().CompareTo("createqueue") == 0)
                        {
                            // No processing to occur other than creating the queue.
                            namespaceManager = NamespaceManager.CreateFromConnectionString(connectionString);
                            namespaceManager.CreateQueue(queueName);
                            Console.WriteLine("Queue named {0} was created.", queueName);
                            Environment.Exit(0);
                        }

                        if (args[0].ToLower().CompareTo("deletequeue") == 0)
                        {
                            // No processing to occur other than deleting the queue.
                            namespaceManager = NamespaceManager.CreateFromConnectionString(connectionString);
                            namespaceManager.DeleteQueue("TSPQueue");
                            Console.WriteLine("Queue named {0} was deleted.", queueName);
                            Environment.Exit(0);
                        }

                        // Neither creating or deleting a queue.
                        // Assume the value passed in is the number of cities to solve.
                        numCities = Convert.ToInt32(args[0]);
                    }

                    Console.WriteLine("Running for {0} cities.", numCities);

                    queueClient = QueueClient.CreateFromConnectionString(connectionString, "TSPQueue");

                    List<int> startCities = new List<int>();
                    List<int> restCities = new List<int>();

                    startCities.Add(0);
                    for (int i = 1; i < numCities; i++)
                    {
                        restCities.Add(i);
                    }
                    distances = new double[numCities, numCities];
                    cityNames = new String[numCities];
                    BuildDistances(@"c:\tsp\cities.txt", numCities);
                    minDistance = -1;
                    bestOrder = new int[numCities];
                    permutation(startCities, 0, restCities);
                    Console.WriteLine("Final solution found!");
                    queueClient.Send(new BrokeredMessage("Complete"));

                    queueClient.Close();
                    Environment.Exit(0);

                }
                catch (ServerBusyException serverBusyException)
                {
                    Console.WriteLine("ServerBusyException encountered");
                    Console.WriteLine(serverBusyException.Message);
                    Console.WriteLine(serverBusyException.StackTrace);
                    Environment.Exit(-1);
                }
                catch (ServerErrorException serverErrorException)
                {
                    Console.WriteLine("ServerErrorException encountered");
                    Console.WriteLine(serverErrorException.Message);
                    Console.WriteLine(serverErrorException.StackTrace);
                    Environment.Exit(-1);
                }
                catch (Exception exception)
                {
                    Console.WriteLine("Exception encountered");
                    Console.WriteLine(exception.Message);
                    Console.WriteLine(exception.StackTrace);
                    Environment.Exit(-1);
                }
            }
        }
    }

## Erstellen einer .NET-Anwendung, die den Fortschritt der rechenintensiven Aufgabe überwacht

1.  Erstellen Sie auf dem Entwicklungscomputer eine .NET-Konsolenanwendung mit **TSPClient** als Projektnamen. Stellen Sie sicher, dass als Zielframework .**NET Framework 4** (nicht **.NET Framework 4 Client Profile**) eingestellt ist. Nach dem Erstellen eines Projekts kann das Zielframework folgendermaßen eingestellt werden: Klicken Sie im Menü von Visual Studio auf **Projekte**, klicken Sie auf **Eigenschaften**, klicken Sie auf die Registerkarte **Anwendung**, und legen Sie anschließend den Wert für **Zielframework** fest.
2.  Fügen Sie die Microsoft ServiceBus-Bibliothek hinzu. Klicken Sie im Projektmappen-Explorer von Visual Studio mit der rechten Maustaste auf **TSPSolver**, klicken Sie auf **Verweis hinzufügen**, klicken Sie auf die Registerkarte **Durchsuchen**, navigieren Sie zu **C:\\Programme\\Microsoft SDKs\\Windows Azure.NET SDK\\2012-06\\ref**, und wählen Sie **Microsoft.ServiceBus.dll** als Referenz aus.
3.  Fügen Sie die System.Runtime.Serialization-Bibliothek hinzu. Klicken Sie im Projektmappen-Explorer von Visual Studio mit der rechten Maustaste auf **TSPClient**, klicken Sie auf **Verweis hinzufügen**, klicken Sie auf die Registerkarte **.NET**, und wählen Sie **System.Runtime.Serialization** als Verweis aus.
4.  Verwenden Sie das Codebeispiel am Ende dieses Abschnitts als Inhalt der Datei **Program.cs**.
5.  Ändern Sie die Platzhalter **your\_service\_bus\_namespace**, **your\_service\_bus\_owner** und **your\_service\_bus\_key** so, dass Ihre Service Bus-Werte für **Namespace**, **Standardaussteller** und **Standardschlüssel** verwendet werden.
6.  Kompilieren Sie die Anwendung. Damit wird **TSPClient.exe** im Ordner **bin** des Projekts (entweder **bin\\release** oder **bin\\debug**, je nachdem, ob Sie als Ziel eine Releaseversion oder eine Debugversion vorgesehen haben) erstellt. Sie können diesen Code auf dem Entwicklungscomputer ausführen oder diese ausführbare Datei und Microsoft.ServiceBus.dll auf einen Computer kopieren, auf dem die Clientanwendung ausgeführt wird (sie muss nicht auf den virtuellen Computer kopiert werden).

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.IO;

    using Microsoft.ServiceBus;
    using Microsoft.ServiceBus.Messaging;
    using System.Threading; // For Thread.Sleep

    namespace TSPClient
    {
        class Program
        {

            static void Main(string[] args)
            {

                try
                {

                    Console.WriteLine("Starting at {0}", DateTime.Now);

                    String serviceBusNamespace = "your_service_bus_namespace";
                    String issuer = "your_service_bus_owner";
                    String key = "your_service_bus_key";

                    String connectionString = @"Endpoint=sb://" +
                           serviceBusNamespace +
                           @".servicebus.windows.net/;SharedSecretIssuer=" +
                           issuer + @";SharedSecretValue=" + key;

                    QueueClient queueClient = QueueClient.CreateFromConnectionString(connectionString, "TSPQueue");

                    BrokeredMessage message;

                    int waitMinutes = 3;  // Use as the default, if no value
                    // is specified at command line.

                    if (0 != args.Length)
                    {
                        waitMinutes = Convert.ToInt16(args[0]);
                    }

                    String waitString;
                    waitString = (waitMinutes == 1) ? "minute" : waitMinutes.ToString() + " minutes";

                    while (true)
                    {
                        message = queueClient.Receive();

                        if (message != null)
                        {
                            try
                            {
                                string str = message.GetBody<string>();
                                Console.WriteLine(str);

                                // Remove message from queue
                                message.Complete();

                                if ("Complete" == str)
                                {
                                    Console.WriteLine("Finished at {0}.", DateTime.Now);
                                    break;
                                }
                            }
                            catch (Exception e)
                            {
                                // Indicates a problem. Unlock the message in the queue.
                                message.Abandon();
                                throw e;
                            }
                        }
                        else
                        {
                            // The queue is empty.
                            Console.WriteLine("Queue is empty. Sleeping for another {0}.", waitString);
                            System.Threading.Thread.Sleep(60000 * waitMinutes);
                        }
                    }
                    queueClient.Close();
                    Environment.Exit(0);
                }
                catch (ServerBusyException serverBusyException)
                {
                    Console.WriteLine("ServerBusyException encountered");
                    Console.WriteLine(serverBusyException.Message);
                    Console.WriteLine(serverBusyException.StackTrace);
                    Environment.Exit(-1);
                }
                catch (ServerErrorException serverErrorException)
                {
                    Console.WriteLine("ServerErrorException encountered");
                    Console.WriteLine(serverErrorException.Message);
                    Console.WriteLine(serverErrorException.StackTrace);
                    Environment.Exit(-1);
                }
                catch (Exception exception)
                {
                    Console.WriteLine("Exception encountered");
                    Console.WriteLine(exception.Message);
                    Console.WriteLine(exception.StackTrace);
                    Environment.Exit(-1);
                }
            }
        }
    }

## Ausführen der .NET-Anwendungen

Führen Sie die rechenintensive Anwendung aus, zunächst um die Warteschlage zu erstellen, dann um das "Traveling Salesman"-Problem zu lösen. Dadurch wird die aktuelle beste Route zur Service Bus-Warteschlange hinzugefügt. Während die rechenintensive Anwendung ausgeführt wird (oder danach), führen Sie den Client aus, um Ergebnisse aus der Service Bus-Warteschlange anzuzeigen.

### Ausführen der rechenintensiven Anwendung

1.  Melden Sie sich am virtuellen Computer an.
2.  Erstellen Sie einen Ordner mit dem Namen **c:\\TSP**. In diesem Ordner wird die Anwendung ausgeführt.
3.  Kopieren Sie die Dateien TSPSolver.exe und Microsoft.ServiceBus.dll, die sich im Ordner **bin** des Projekts TSPSolver befinden, nach **c:\\TSP**.
4.  Erstellen Sie eine Datei namens **c:\\TSP\\cities.txt** mit folgendem Inhalt:

        City_1, 1002.81, -1841.35
        City_2, -953.55, -229.6
        City_3, -1363.11, -1027.72
        City_4, -1884.47, -1616.16
        City_5, 1603.08, -1030.03
        City_6, -1555.58, 218.58
        City_7, 578.8, -12.87
        City_8, 1350.76, 77.79
        City_9, 293.36, -1820.01
        City_10, 1883.14, 1637.28
        City_11, -1271.41, -1670.5
        City_12, 1475.99, 225.35
        City_13, 1250.78, 379.98
        City_14, 1305.77, 569.75
        City_15, 230.77, 231.58
        City_16, -822.63, -544.68
        City_17, -817.54, -81.92
        City_18, 303.99, -1823.43
        City_19, 239.95, 1007.91
        City_20, -1302.92, 150.39
        City_21, -116.11, 1933.01
        City_22, 382.64, 835.09
        City_23, -580.28, 1040.04
        City_24, 205.55, -264.23
        City_25, -238.81, -576.48
        City_26, -1722.9, -909.65
        City_27, 445.22, 1427.28
        City_28, 513.17, 1828.72
        City_29, 1750.68, -1668.1
        City_30, 1705.09, -309.35
        City_31, -167.34, 1003.76
        City_32, -1162.85, -1674.33
        City_33, 1490.32, 821.04
        City_34, 1208.32, 1523.3
        City_35, 18.04, 1857.11
        City_36, 1852.46, 1647.75
        City_37, -167.44, -336.39
        City_38, 115.4, 0.2
        City_39, -66.96, 917.73
        City_40, 915.96, 474.1
        City_41, 140.03, 725.22
        City_42, -1582.68, 1608.88
        City_43, -567.51, 1253.83
        City_44, 1956.36, 830.92
        City_45, -233.38, 909.93
        City_46, -1750.45, 1940.76
        City_47, 405.81, 421.84
        City_48, 363.68, 768.21
        City_49, -120.3, -463.13
        City_50, 588.51, 679.33

5.  Wechseln Sie an einer Eingabeaufforderung zum Verzeichnis "c:\\TSP".
6.  Sie müssen die Service Bus-Warteschlange erstellen, bevor Sie die TSP-Solver-Permutationen ausführen. Führen Sie den folgenden Befehl aus, um die Service Bus-Warteschlange zu erstellen:

        TSPSolver createqueue

7.  Da die Warteschlange jetzt erstellt wurde, können Sie die TSP-Solver-Permutationen ausführen. Führen Sie beispielsweise folgenden Befehl aus, um den Solver für acht Städte auszuführen.

        TSPSolver 8

Falls Sie keine Zahl angeben, wird der Solver für zehn Städte ausgeführt. Wenn der Solver aktuelle kürzeste Routen findet, fügt er sie der Warteschlange hinzu.

Der Solver wird ausgeführt, bis alle Routen untersucht wurden.

> [WACOM.NOTE]
> Je größer die angegebene Zahl, desto länger wird der Solver ausgeführt. Die Ausführung für 14 Städte könnte zum Beispiel mehrere Minuten dauern, und die Ausführung für 15 Städte könnte mehrere Stunden dauern. Wird der Wert auf 16 oder darüber hinaus erhöht, könnte die Ausführung mehrere Tage (und schließlich Wochen, Monate und Jahre) dauern. Dies liegt an der schnellen Zunahme der Anzahl der Permutationen, die vom Solver analysiert werden, wenn die Anzahl der Städte zunimmt.

### Ausführen der überwachenden Clientanwendung

1.  Melden Sie sich an dem Computer an, auf dem die Clientanwendung ausgeführt wird. Dieser Computer muss nicht zwingend mit dem Computer identisch sein, auf dem die **TSPSolver**-Anwendung ausgeführt wird.
2.  Erstellen Sie einen Ordner, in dem die Anwendung ausgeführt wird. Zum Beispiel **c:\\TSP**.
3.  Kopieren Sie die Dateien **TSPClient.exe** und **Microsoft.ServiceBus.dll**, die sich im Ordner **bin** des Projekts TSPClient befinden, in den Ordner **c:\\TSP**.
4.  Wechseln Sie an einer Eingabeaufforderung zum Verzeichnis "c:\\TSP".
5.  Führen Sie den folgenden Befehl aus:

        TSPClient

    Geben Sie optional die Pause zwischen den Überprüfungen der Warteschlange in Minuten an, indem Sie ein Befehlszeilenargument übergeben. Die Standardpause beim Überprüfen der Warteschlange ist drei Minuten. Dieser Wert wird verwendet, wenn kein Befehlszeilenargument an **TSPClient** übergeben wird. Wenn Sie einen anderen Wert für das Intervall verwenden möchten, zum Beispiel eine Minute, führen Sie Folgendes aus:

        TSPClient 1

    Der Client wird ausgeführt, bis er die Warteschlangenmeldung "Complete" erhält. Beachten Sie, dass Sie beim Ausführen mehrerer Instanzen des Solvers ohne Ausführen des Clients den Client möglicherweise mehrfach ausführen müssen, um die Warteschlange vollständig zu leeren. Alternativ können Sie auch die Warteschlange löschen und dann neu erstellen. Um die Warteschlange zu löschen, führen Sie den folgenden **TSPSolver**-Befehl (nicht **TSPClient**-Befehl) aus:

        TSPSolver deletequeue

## Anhalten der .NET-Anwendungen

Für Solver- und Clientanwendungen können Sie **Strg+C** drücken, um die Anwendung vor dem normalen Abschluss zu beenden.

## Alternative zum Erstellen und Löschen der Warteschlange mit TSPSolver

Anstatt TSPSolver zum Erstellen oder Löschen der Warteschlange zu verwenden, können Sie die Warteschlange mit dem [Azure-Verwaltungsportal][Azure-Verwaltungsportal] erstellen oder löschen. Navigieren Sie zum Service Bus-Abschnitt des Verwaltungsportals, um auf die Funktionen zum Erstellen oder Löschen einer Warteschlange sowie zum Abrufen Abrufen von Verbindungszeichenfolge, Aussteller und Zugriffsschlüssel zuzugreifen. Sie können auch ein Dashboard der Service Bus-Warteschlangen anzeigen, sodass Sie Metriken für die ein- und ausgehenden Nachrichten sehen können.

  ["Traveling Salesman"-Problemlösung]: ./media/virtual-machines-dotnet-run-compute-intensive-task/WA_dotNetTSPSolver.png
  ["Traveling Salesman"-Problemclient]: ./media/virtual-machines-dotnet-run-compute-intensive-task/WA_dotNetTSPClient.png
  [create-account-and-vms-note]: ../includes/create-account-and-vms-note.md
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com
  [Neuen Service Bus erstellen]: ./media/virtual-machines-dotnet-run-compute-intensive-task/ServiceBusCreateNew.png
  [Dialogfeld "Namespace erstellen"]: ./media/virtual-machines-dotnet-run-compute-intensive-task/CreateNameSpaceDialog.png
  [Screenshot beim Klicken auf "Erstellen"]: ./media/virtual-machines-dotnet-run-compute-intensive-task/ClickCreate.png
  [Screenshot der verfügbaren Namespaces]: ./media/virtual-machines-dotnet-run-compute-intensive-task/AvailableNamespaces.png
  [Screenshot der Namespaceliste]: ./media/virtual-machines-dotnet-run-compute-intensive-task/NamespaceList.png
  [Schaltfläche "Zugriffsschlüssel"]: ./media/virtual-machines-dotnet-run-compute-intensive-task/AccessKey.png
  [Azure-SDK für .NET]: http://www.windowsazure.com/de-de/develop/net/
