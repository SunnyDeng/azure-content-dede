<properties linkid="dev-nodejs-how-to-service-bus-queues" urlDisplayName="Queue Service" pageTitle="How to use the queue service (Node.js) | Microsoft Azure" metaKeywords="Azure Queue Service get messages Node.js" description="Learn how to use the Azure Queue service to create and delete queues, and insert, get, and delete messages. Samples written in Node.js." metaCanonical="" services="storage" documentationCenter="Node.js" title="How to Use the Queue Service from Node.js" authors="" solutions="" manager="" editor="" />

Verwenden des Warteschlangendiensts aus Node.js
===============================================

In diesem Leitfaden wird die Durchführung häufiger Szenarien mit dem Windows Azure-Warteschlangendienst demonstriert. Die Beispiele sind in C\# geschrieben und verwenden die Node.js-API. Zu den Szenarien gehören das **Einfügen**, **Einsehen**, **Abrufen** und **Löschen** von Warteschlangennachrichten sowie das **Erstellen und Löschen von Warteschlangen**. Weitere Informationen zu Warteschlangen finden Sie im Abschnitt [Nächste Schritte](#next-steps).

Inhaltsverzeichnis
------------------

-   [Was ist der Warteschlangendienst?](#what-is)
-   [Konzepte](#concepts)
-   [Erstellen eines Azure-Speicherkontos](#create-account)
-   [Erstellen einer Node.js-Anwendung](#create-app)
-   [Konfigurieren der Anwendung für den Speicherzugriff](#configure-access)
-   [Einrichten einer Azure-Speicherverbindungszeichenfolge](#setup-connection-string)
-   [Vorgehensweise: Erstellen einer Warteschlange](#create-queue)
-   [Vorgehensweise: Einfügen einer Nachricht in eine Warteschlange](#insert-message)
-   [Vorgehensweise: Einsehen der nächsten Nachricht](#peek-message)
-   [Vorgehensweise: Entfernen der nächsten Nachricht aus der Warteschlange](#get-message)
-   [Vorgehensweise: Ändern des Inhalts von Nachrichten in der Warteschlange](#change-contents)
-   [Vorgehensweise: Weitere Optionen für das Entfernen von Nachrichten aus der Warteschlange](#advanced-get)
-   [Vorgehensweise: Abrufen der Warteschlangenlänge](#get-queue-length)
-   [Vorgehensweise: Löschen einer Warteschlange](#delete-queue)
-   [Nächste Schritte](#next-steps)

Was ist der Warteschlangendienst?
---------------------------------

Der Warteschlangendienst in Azure ist ein Dienst zur Speicherung großer Mengen von Nachrichten, auf die von überall auf der Welt mit authentifizierten Anrufen über HTTP oder HTTPS zugegriffen werden kann. Eine einzelne Warteschlangennachricht kann bis zu 64KB groß sein, und eine Warteschlange kann Millionen von Nachrichten enthalten. Deren Anzahl ist nur durch die Gesamtkapazität von 100 TB des Speicherkontos begrenzt. Der Warteschlangendienst wird hauptsächlich für folgende Zwecke verwendet:

-   Erstellung eines Arbeits-Backlogs zur asynchronen Verarbeitung
-   Weiterleitung von Nachrichten von einer Azure-Webrolle an eine Workerrolle

Konzepte
--------

Der Warteschlangendienst umfasst die folgenden Komponenten:

![Queue1](./media/storage-nodejs-how-to-use-queues/queue1.png)

-   **URL-Format:** Warteschlangen sind über das folgende URL-Format adressierbar:

        http://storageaccount.queue.core.windows.net/queue  

    Mit der folgenden URL kann eine der Warteschlangen im Diagramm adressiert werden:

        http://myaccount.queue.core.windows.net/imagesToDownload

-   **Speicherkonto:** Der Zugriff auf Azure-Speicher erfolgt immer über ein Speicherkonto. Ein Speicherkonto ist die höchste Ebene des Namespace für den Zugriff auf Warteschlangen. Ein Speicherkonto kann bis zu 100 TB Blob-, Warteschlangen- und Tabellendaten enthalten.

-   **Warteschlange:** Eine Warteschlange enthält einen Satz von Nachrichten. Alle Nachrichten müssen sich in Warteschlangen befinden.

-   **Nachricht:** Eine Nachricht in einem beliebigen Format und mit einer Größe von bis zu 64 KB.

Erstellen eines Azure-Speicherkontos
------------------------------------

Für Speichervorgänge benötigen Sie ein Windows Azure-Speicherkonto. Mit den folgenden Schritten können Sie ein Speicherkonto erstellen. (Sie können ein Speicherkonto auch [mithilfe der REST-API](http://msdn.microsoft.com/en-us/library/windowsazure/hh264518.aspx) erstellen.)

1.  Melden Sie sich auf dem [Windows Azure-Verwaltungsportal](http://manage.windowsazure.com) an.

2.  Klicken Sie unten im Navigationsbereich auf **+NEW**.

    ![+Neu](./media/storage-nodejs-how-to-use-queues/plus-new.png)

3.  Klicken Sie auf **Speicherkonto** und anschließend auf **Schnellerfassung**.

    ![Dialogfeld "Schnellerfassung"](./media/storage-nodejs-how-to-use-queues/quick-storage.png)

4.  Geben Sie im Feld "URL" einen Unterdomänennamen ein, der im URI für das Speicherkonto verwendet werden soll. Der Eintrag kann drei bis 24 Kleinbuchstaben und Zahlen enthalten. Dieser Wert wird der Hostname im URI, der zum Adressieren von Blob-, Warteschlangen- oder Tabellenspeicherressourcen für das Abonnement verwendet wird.

5.  Wählen Sie eine Region/Affinitätsgruppe, in der sich der Speicher befinden soll. Wenn Sie Speicher aus Ihrer Windows Azure-Anwendung verwenden, wählen Sie die Region aus, in der Sie auch Ihre Anwendung bereitstellen.

6.  Klicken Sie auf **Speicherkonto erstellen**.

Erstellen einer Node.js-Anwendung
---------------------------------

Erstellen Sie eine leere Node.js-Anwendung. Hinweise zum Erstellen von Node.js-Anwendungen finden Sie unter [Erstellen und Bereitstellen einer Node.js-Anwendung auf einer Azure-Website](/en-us/develop/nodejs/tutorials/create-a-website-(mac)/), [Node.js Cloud Service]({localLink:2221} "Web App mit Express") (mit Windows PowerShell) oder [Website mit WebMatrix](/en-us/develop/nodejs/tutorials/web-site-with-webmatrix/).

Konfigurieren der Anwendung für den Speicherzugriff
---------------------------------------------------

Um Azure-Speicher verwenden zu können, müssen Sie das Node.js-Paket azure herunterladen und verwenden. Dieses Paket enthält eine Reihe von Bibliotheken, die mit den REST-Speicherdiensten kommunizieren.

### Verwenden von Node Package Manager (NPM) zum Abrufen des Pakets

1.  Verwenden Sie eine Befehlszeilenschnittstelle, z. B. **PowerShell** (Windows,) **Terminal** (Mac) oder **Bash** (Unix), und navigieren Sie zu dem Ordner, in dem Sie die Beispielanwendung erstellt haben.

2.  Geben Sie **npm install azure** in das Befehlsfenster ein. Die Ausgabe dieses Befehls sollte wie folgt aussehen:

        azure@0.7.5 node_modules\azure
        |-- dateformat@1.0.2-1.2.3
        |-- xmlbuilder@0.4.2
        |-- node-uuid@1.2.0
        |-- mime@1.2.9
        |-- underscore@1.4.4
        |-- validator@1.1.1
        |-- tunnel@0.0.2
        |-- wns@0.5.3
        |-- xml2js@0.2.7 (sax@0.5.2)
        |-- request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)

3.  Sie können den Befehl **ls** manuell ausführen, um zu überprüfen, ob der Ordner **node\_modules** erstellt wurde. Dieser Ordner enthält das **azure**-Paket mit den Bibliotheken, die Sie für den Speicherzugriff benötigen.

### Importieren des Pakets

Verwenden Sie Editor oder einen anderen Texteditor, um die folgende Zeile am Anfang der Datei **server.js** der Anwendung einzufügen, in der Sie den Speicher nutzen möchten:

    var azure = require('azure');

Einrichten einer Azure-Speicherverbindung
-----------------------------------------

Das Azure-Modul entnimmt den Umgebungsvariablen AZURE\_STORAGE\_ACCOUNT und AZURE\_STORAGE\_ACCESS\_KEY die Informationen, die zum Herstellen einer Verbindung mit Ihrem Azure-Speicherkonto benötigt werden. Falls diese Umgebungsvariablen nicht gesetzt sind, müssen Sie die Kontoinformationen beim Aufruf von **createQueueService** angeben.

Ein Beispiel zum Festlegen der Umgebungsvariablen in einer Konfigurationsdatei für einen Azure-Clouddienst finden Sie unter [Node.js-Clouddienst mit Speicher](/en-us/develop/nodejs/tutorials/web-app-with-storage/).

Ein Beispiel zum Festlegen der Umgebungsvariablen im Verwaltungsportal für eine Azure-Website finden Sie unter [Node.js-Webanwendung mit Speicher](/en-us/develop/nodejs/tutorials/web-site-with-storage/).

Vorgehensweise: Erstellen einer Warteschlange
---------------------------------------------

Durch folgenden Code wird ein **QueueService**-Objekt erstellt, das Ihnen das Arbeiten mit Warteschlangen ermöglicht.

    var queueService = azure.createQueueService();

Verwenden Sie die **createQueueIfNotExists**-Methode, die die angegebene Warteschlange zurückgibt, wenn sie bereits vorhanden ist, oder eine neue Warteschlange mit dem angegebenen Namen erstellt, sollte sie noch nicht vorhanden sein.

    queueService.createQueueIfNotExists(queueName, function(error){
        if(!error){
            // Warteschlange ist vorhanden
        }
    });

### Filter

Mit **QueueService** können Sie optionale Filteroperationen auf Operationen ausführen. Filtervorgänge können Protokollierung, automatische Wiederholung usw. umfassen. Filter sind Objekte, die eine Methode mit einer Signatur implementieren:

     function handle (requestOptions, next)

Nachdem die Vorverarbeitung der Anforderungsoptionen abgeschlossen ist, muss die Methode "next" aufrufen und hierbei eine Rückruffunktion mit der folgenden Signatur übergeben:

     function (returnObject, finalCallback, next)

Nachdem das returnObject-Objekt (die Antwort auf die an den Server gesendete Anforderung) verarbeitet wurde, muss in dieser Rückruffunktion entweder "next" aufgerufen werden, wenn die Tabelle vorhanden ist, um weitere Filter zu verarbeiten, oder andernfalls einfach "finallCallback" aufrufen, um den Dienstaufruf zu beenden.

Zwei Filter, die eine Wiederholungslogik implementieren, sind im Azure SDK für Node.js enthalten: **ExponentialRetryPolicyFilter** und **LinearRetryPolicyFilter**. Mit folgendem Code wird ein **QueueService**-Objekt erstellt, das **ExponentialRetryPolicyFilter** verwendet:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var queueService = azure.createQueueService().withFilter(retryOperations);

Vorgehensweise: Einfügen einer Nachricht in eine Warteschlange
--------------------------------------------------------------

Zum Einfügen einer Nachricht in eine Warteschlange verwenden Sie die **createMessage**-Methode, um eine neue Nachricht zu erstellen. Fügen Sie sie dann der Warteschlange hinzu.

    queueService.createMessage(queueName, "Hello world!", function(error){
        if(!error){
            // Nachricht wurde eingefügt
        }
    });

Vorgehensweise: Einsehen der nächsten Nachricht
-----------------------------------------------

Sie können einen Blick auf die Nachricht am Anfang einer Warteschlange werfen, ohne sie aus der Warteschlange zu entfernen, indem Sie die Methode **peekMessages** aufrufen. Standardmäßig wird von **peekMessages** jeweils nur eine Nachricht angeschaut.

    queueService.peekMessages(queueName, function(error, messages){
        if(!error){
            // Nachrichten eingesehen
            // Nachrichtentext ist in messages[0].messagetext verfügbar
        }
    });

> [WACOM.NOTE] Wenn Sie **peekMessage** verwenden und keine Nachrichten in der Warteschlange vorhanden sind, wird kein Fehler zurückgegeben, aber auch keine Nachrichten.

Vorgehensweise: Entfernen der nächsten Nachricht aus der Warteschlange
----------------------------------------------------------------------

Durch diesen Code wird eine Nachricht in zwei Schritten aus der Warteschlange entfernt. Wenn Sie **getMessages** aufrufen, wird standardmäßig die nächste Nachricht aus der Warteschlange abgerufen. Die für **getMessages** zurückgegebene Nachricht ist für andere Codes nicht mehr sichtbar, die Nachrichten aus dieser Warteschlange lesen. Standardmäßig bleibt die Nachricht 30 Sekunden lang unsichtbar. Um die Nachricht endgültig aus der Warteschlange zu entfernen, müssen Sie außerdem **deleteMessage** aufrufen. Dieser zweistufige Prozess zum Entfernen von Nachrichten stellt sicher, dass eine andere Codeinstanz dieselbe Nachricht erneut abrufen kann, falls die Verarbeitung aufgrund eines Hardware- oder Softwarefehlers fehlschlägt. Der Code ruft **deleteMessage** direkt nach der Verarbeitung der Nachricht auf.

    queueService.getMessages(queueName, function(error, messages){
        if(!error){
            // Nachricht in weniger als 30 Sekunden verarbeiten,
            // Nachrichtentext ist in messages[0].messagetext verfügbar 
            var message = messages[0]
            queueService.deleteMessage(queueName
                , message.messageid
                , message.popreceipt
                , function(error){
                    if(!error){
                        // Nachricht wurde gelöscht
                    }
                });
        }
    });

> [WACOM.NOTE] Wenn Sie **getMessages** verwenden und keine Nachrichten in der Warteschlange vorhanden sind, wird kein Fehler zurückgegeben, aber auch keine Nachrichten.

Vorgehensweise: Ändern des Inhalts von Nachrichten in der Warteschlange
-----------------------------------------------------------------------

Sie können den Inhalt einer Nachricht vor Ort in der Warteschlange ändern. Wenn die Nachricht eine Arbeitsaufgabe darstellt, können Sie diese Funktion verwenden, um den Status der Aufgabe zu aktualisieren. Im Code unten wird die **updateMessage**-Methode zum Aktualisieren einer Nachricht verwendet.

    queueService.getMessages(queueName, function(error, messages){
        if(!error){
            // Nachricht erhalten
            var message = messages[0];
            queueService.updateMessage(queueName
                , message.messageid
                , message.popreceipt
                , 10
                , { messagetext: 'in your message, doing stuff.' }
                , function(error){
                    if(!error){
                        // Nachricht erfolgreich aktualisiert
                    }
                });
        }
    });

Vorgehensweise: Weitere Optionen für das Entfernen von Nachrichten aus der Warteschlange
----------------------------------------------------------------------------------------

Es gibt zwei Möglichkeiten, wie Sie das Abrufen von Nachrichten aus der Warteschlange anpassen können. Erstens können Sie einen Nachrichtenstapel abrufen (bis zu 32). Zweitens können Sie das Unsichtbarkeits-Zeitlimit verkürzen oder verlängern, sodass der Code mehr oder weniger Zeit zur vollständigen Verarbeitung jeder Nachricht benötigt. Im folgenden Codebeispiel wird **getMessages** verwendet, um 15 Nachrichten mit einem Aufruf abzurufen. Anschließend wird jede Nachricht mithilfe einer for-Schleife verarbeitet. Außerdem wird das Unsichtbarkeits-Zeitlimit auf fünf Minuten pro Nachricht festgelegt.

    queueService.getMessages(queueName
        , {numofmessages: 15, visibilitytimeout: 5 * 60}
        , function(error, messages){
        if(!error){
            // Nachrichten abgerufen
            for(var index in messages){
                // Text ist in messages[index].messagetext verfügbar
                var message = messages[index];
                queueService.deleteMessage(queueName
                    , message.messageid
                    , message.popreceipt
                    , function(error){
                        if(!error){
                            // Nachricht wurde gelöscht
                        }
                    });
            }
        }
    });

Vorgehensweise: Abrufen der Warteschlangenlänge
-----------------------------------------------

Sie können die Anzahl der Nachrichten in einer Warteschlange schätzen lassen. Die **getQueueMetadata**-Methode fordert den Warteschlangendienst auf, Metadaten zur Warteschlange zurückzugeben. Die **approximatemessagecount**-Eigenschaft der Antwort enthält die Anzahl der Nachrichten in einer Warteschlange. Die Anzahl ist nur ein ungefährer Wert, da seit der Antwort des Warteschlangendienstes möglicherweise bereits Nachrichten hinzugefügt oder gelöscht wurden.

    queueService.getQueueMetadata(queueName, function(error, queueInfo){
        if(!error){
            // Warteschlangenlänge ist in queueInfo.approximatemessagecount verfügbar
        }
    });

Vorgehensweise: Löschen einer Warteschlange
-------------------------------------------

Zum Löschen einer Warteschlange und aller darin enthaltenen Nachrichten rufen Sie die Methode **deleteQueue** für das Warteschlangenobjekt auf.

    queueService.deleteQueue(queueName, function(error){
        if(!error){
            // Warteschlange wurde gelöscht
        }
    });

Nächste Schritte
----------------

Nachdem Sie sich nun mit den Grundlagen des Warteschlangenspeichers vertraut gemacht haben, folgen Sie diesen Links, um zu erfahren, wie komplexere Speicheraufgaben ausgeführt werden.

-   Weitere Informationen finden Sie in der MSDN-Referenz: [Speichern und Zugreifen auf Daten in Azure](http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx).
-   Besuchen Sie den [Blog des Azure-Speicherteams](http://blogs.msdn.com/b/windowsazurestorage/).
-   Besuchen Sie das [Azure SDK für Node](https://github.com/WindowsAzure/azure-sdk-for-node)-Repository auf GitHub.

