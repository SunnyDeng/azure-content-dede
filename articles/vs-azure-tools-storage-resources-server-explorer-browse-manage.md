<properties 
   pageTitle="Durchsuchen und Verwalten von Speicherressourcen mit dem Server-Explorer"
	description="Durchsuchen und Verwalten von Speicherressourcen mit dem Server-Explorer"
	services="visual-studio-online"
	documentationCenter="na"
	authors="kempb"
	manager="douge"
	editor="tlee"/>
<tags 
   ms.service="storage"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="08/24/2015"
	ms.author="kempb"/>

# Durchsuchen und Verwalten von Speicherressourcen mit dem Server-Explorer

Wenn Sie die Azure Tools für Microsoft Visual Studio installiert haben, können Sie Blob-, Warteschlangen- und Tabellendaten von Ihren Speicherkonten für Azure aus anzeigen. Der Knoten für Azure Storage im Server-Explorer zeigt Daten, die sich in Ihrem lokalen Speicheremulatorkonto und Ihren anderen Azure-Speicherkonten befinden.

Wählen Sie zum Anzeigen des Server-Explorers in Visual Studio in der Menüleiste nacheinander die Optionen **Ansicht** und **Server-Explorer**. Der Speicherknoten zeigt alle Speicherkonten an, die unter jedem Azure-Abonnement/-Zertifikat vorhanden sind, mit dem Sie verbunden sind. Wird das Speicherkonto nicht angezeigt, befolgen Sie die Anweisungen am [Ende des Themas](#add-storage-accounts-by-using-server-explorer), um das Konto hinzuzufügen.

Ab Azure SDK 2.7 können Sie auch den neuen Cloud-Explorer zum Anzeigen und Verwalten von Azure-Ressourcen verwenden. Weitere Informationen finden Sie unter [Verwalten von Azure-Ressourcen mit dem Cloud-Explorer](https://msdn.microsoft.com/library/azure/mt185741.aspx).


## Anzeigen und Verwalten von Speicherressourcen in Visual Studio

Der Server-Explorer zeigt automatisch eine Liste von Blobs, Warteschlangen und Tabellen in Ihrem Speicheremulatorkonto an. Das Speicheremulatorkonto wird als der Knoten **Entwicklung** unter dem Knoten "Storage" im Server-Explorer angezeigt.

Erweitern Sie zum Anzeigen der Ressourcen des Speicheremulatorkontos den Knoten **Entwicklung**. Wenn der Speicheremulator nicht gestartet wurde, wird er automatisch gestartet, wenn Sie den Knoten **Entwicklung** erweitern. Dies kann mehrere Sekunden dauern. Sie können weiterhin in anderen Bereichen von Visual Studio arbeiten, während der Speicheremulator gestartet wird.

Erweitern Sie zum Anzeigen von Ressourcen in einem Speicherkonto den Knoten des Speicherkontos im Server-Explorer. Die folgenden Unterknoten werden angezeigt:

- Blobs (in englischer Sprache)

- Warteschlangen

- Tabellen

## Arbeiten mit Blobressourcen

Der Knoten "Blobs" zeigt eine Liste der Container für das ausgewählte Speicherkonto an. Blobcontainer enthalten Blobdateien, und Sie können diese Blobs in Ordnern und Unterordnern organisieren. Weitere Informationen finden Sie unter [Verwenden von BLOB-Speicher aus .NET](..storage/storage-dotnet-how-to-use-blobs/).

###So erstellen Sie einen Blobcontainer

1. Öffnen Sie das Kontextmenü für den Knoten **Blobs**, und wählen Sie dann **Blobcontainer erstellen**.

1. Geben Sie den Namen des neuen Containers im Dialogfeld **Blobcontainer erstellen** ein, und wählen Sie dann **OK**. ![Hinzufügen eines neuen Blobcontainers](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC744153.bmp)

    >[AZURE.NOTE]Der Blobcontainername muss mit einer Zahl (0-9) oder einem Kleinbuchstaben (a-z) beginnen.

###So löschen Sie einen Blobcontainer

- Öffnen Sie das Kontextmenü für den Blobcontainer, den Sie entfernen möchten, und wählen Sie **Löschen**.

###So zeigen Sie eine Liste der in einem Blobcontainer enthaltenen Elemente an

- Öffnen Sie das Kontextmenü für einen Blobcontainernamen in der Liste aus, und wählen Sie dann **Blobcontainer anzeigen**.

    Wenn Sie den Inhalt eines Blobcontainers anzeigen, wird er auf einer Registerkarte angezeigt, die als Blobcontaineransicht bezeichnet wird.

    ![VST\_SE\_BlobDesigner](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC749016.png)

    Sie können die folgenden Vorgänge für Blobs mithilfe der Schaltflächen in der rechten oberen Ecke der Blobcontaineransicht ausführen:


    - Eingeben und Anwenden eines Filterwerts

    - Aktualisieren der Liste der Blobs im Container

    - Hochladen einer Datei

    - Löschen eines Blobs


        >[AZURE.NOTE]Beim Löschen einer Datei aus einem Blobcontainer wird nicht die zugrunde liegende Datei gelöscht; sie wird lediglich aus dem Blobcontainer entfernt.


    - Öffnen eines Blobs

    - Speichern eines Blobs auf dem lokalen Computer

###So erstellen Sie einen Ordner oder Unterordner in einem Blobcontainer 

1. Wählen Sie den Blobcontainer im Server-Explorer. Wählen Sie im Fenster "Container" die Schaltfläche **Blob hochladen**.

    ![Hochladen einer Datei in einen Blobordner](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766037.png)

1. Wählen Sie im Dialogfeld **Neue Datei hochladen** die Schaltfläche **Durchsuchen**, um die Datei anzugeben, die Sie hochladen möchten. Geben Sie dann einen Ordnernamen im Feld **Ordner (optional)** ein.

    Sie können den Containerordnern Unterordner hinzufügen, indem Sie dieselben Schritten anwenden. Wenn Sie keinen Ordnernamen angeben, wird die Datei auf der obersten Ebene des Blobcontainers hochgeladen. Die Datei wird im angegebenen Ordner im Container angezeigt.

    ![Ordner, der einem Blobcontainer hinzugefügt wurde](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766038.png)

1. Doppelklicken Sie auf den Ordner, oder drücken Sie die EINGABETASTE, um den Inhalt des Ordners anzuzeigen. Wenn Sie sich im Ordner des Containers befinden, können Sie in das Stammverzeichnis des Containers zurück wechseln, indem Sie die Schaltfläche **Übergeordnetes Verzeichnis öffnen** (Pfeil nach oben) wählen.

###So löschen Sie einen Containerordner

 - Löschen aller Dateien im Ordner 

    >[AZURE.NOTE]Da es sich bei Ordnern in Blobcontainern um virtuelle Ordner handelt, ist es nicht möglich, einen leeren Ordner zu erstellen, noch können Sie einen Ordner löschen, um dessen Dateiinhalt zu löschen. Sie müssen den gesamten Inhalt eines Ordners löschen, um den Ordner zu löschen.

### So filtern Sie Blobs in einem Container

Sie können die angezeigten Blobs filtern, indem Sie ein gemeinsames Präfix angeben.

Wenn Sie beispielsweise das Präfix `hello` in das Filtertextfeld eingeben und dann die Schaltfläche **Ausführen** (**!**) wählen, werden nur Blobs angezeigt, die mit "hello" beginnen.

![VST\_SE\_FilterBlobs](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC519076.png)


>[AZURE.NOTE]Beim Filterfeld muss die Groß-/Kleinschreibung beachtet werden. Filtervorgänge mit Platzhalterzeichen werden nicht unterstützt. Blobs können nur nach Präfix gefiltert werden. Das Präfix schließt möglicherweise ein Trennzeichen ein, wenn Sie Blobs in einer virtuellen Hierarchie mithilfe eines Trennzeichens organisieren. Zum Beispiel werden durch Filtern nach dem Präfix "HelloFabric/" alle Blobs zurückgegeben, die mit dieser Zeichenfolge beginnen.

### So laden Sie Blobdaten herunter

- Öffnen Sie im Server-Explorer das Kontextmenü für mindestens ein Blob, und wählen Sie **Öffnen** aus, oder wählen Sie den Blobnamen, und wählen Sie dann die Schaltfläche **Öffnen**, oder doppelklicken Sie auf den Blobnamen.

    Der Status eines Blobdownloads wird im Fenster **Azure-Aktivitätsprotokoll** angezeigt.

    Das Blob wird im Standard-Editor für diesen Dateityp geöffnet. Wird der Dateityp vom Betriebssystem erkannt, wird die Datei in einer lokal installierten Anwendung geöffnet. Andernfalls werden Sie zur Auswahl einer Anwendung aufgefordert, die für den Dateityp des Blobs geeignet ist. Die beim Herunterladen von Blobs erstellte lokale Datei ist als schreibgeschützt gekennzeichnet.

    Blobdaten werden lokal zwischengespeichert und im Blobdienst mit der Uhrzeit der letzten Änderung des Blobs verglichen. Wenn das Blob seit dem letzten Herunterladen aktualisiert wurde, wird es erneut heruntergeladen. Andernfalls wird das Blob vom lokalen Datenträger geladen. Standardmäßig wird ein Blob in ein temporäres Verzeichnis heruntergeladen. Um Blobs in ein bestimmtes Verzeichnis herunterzuladen, öffnen Sie das Kontextmenü für die ausgewählten Blobnamen, und wählen Sie **Speichern unter**. Wenn Sie ein Blob auf diese Weise speichern, wird die Blobdatei nicht geöffnet, und die lokale Datei wird mit Lese-/Schreibattributen erstellt.

### So laden Sie Blobs hoch

- Wählen Sie die Schaltfläche **Blob hochladen** aus, wenn der Container für die Anzeige in der Blobcontaineransicht geöffnet ist.

    Sie können eine oder mehrere Dateien eines beliebigen Typs zum Hochladen auswählen. Vom **Azure-Aktivitätsprotokoll** wird der Uploadstatus angezeigt. Weitere Informationen zum Arbeiten mit Blobdaten finden Sie unter [Verwenden des Azure-Blobspeicherdiensts in .NET](http://go.microsoft.com/fwlink/p/?LinkId=267911).

### So zeigen Sie an Blobs übertragene Protokolle an

- Wenn Sie mithilfe der Azure-Diagnose Daten von der Azure-Anwendung protokollieren und Protokolle an das Speicherkonto übertragen haben, werden Container angezeigt, die von Azure für diese Protokolle erstellt wurden. Das Anzeigen dieser Protokolle im Server-Explorer ist eine einfache Möglichkeit, um Probleme mit Ihrer Anwendung zu erkennen, insbesondere wenn sie für Azure bereitgestellt wurde. Weitere Informationen zur Azure-Diagnose finden Sie unter [Sammeln von Protokollierungsdaten mit der Azure-Diagnose](https://msdn.microsoft.com/library/azure/gg433048.aspx).

### So rufen Sie die URL für ein Blob ab

- Öffnen Sie das Kontextmenü des Blobs, und wählen Sie dann **URL kopieren** aus.

### So bearbeiten Sie ein Blob

- Wählen Sie das Blob und anschließend die Schaltfläche **Blob öffnen** aus.

    Die Datei wird an einen temporären Speicherort heruntergeladen und auf dem lokalen Computer geöffnet. Sie müssen das Blob nach dem Ändern erneut hochladen.

### Arbeiten mit Warteschlangenressourcen

Verwenden Sie die in Azure-Speicherkonten gehosteten Speicherdienstwarteschlangen, um die Kommunikation der Clouddienstrollen untereinander und mit anderen Diensten über einen Nachrichtenübergabemechanismus zu ermöglichen. Greifen Sie auf die Warteschlange programmgesteuert über einen Clouddienst und einen Webdienst für externe Clients zu. Oder greifen Sie mit dem Server-Explorer in Visual Studio direkt auf die Warteschlange zu.

Wenn Sie einen Clouddienst entwickeln, in dem Warteschlangen verwendet werden, können Sie Warteschlangen mit Visual Studio erstellen und interaktiv mit ihnen arbeiten, während Sie Ihren Code entwickeln und testen.

Im Server-Explorer können Sie die Warteschlangen in einem Speicherkonto anzeigen, Warteschlangen erstellen und löschen, eine Warteschlange zum Anzeigen von Nachrichten öffnen und einer Warteschlange Nachrichten hinzufügen. Ist eine Warteschlange zum Anzeigen geöffnet, können Sie die einzelnen Nachrichten anzeigen. Mit den Schaltflächen in der linken oberen Ecke können Sie die folgenden Aktionen für die Warteschlange ausführen:

- Aktualisieren der Ansicht der Warteschlange

- Hinzufügen einer Nachricht zu einer Warteschlange

- Entfernen der obersten Nachricht aus der Warteschlange

- Löschen der gesamten Warteschlange

Die folgende Abbildung zeigt eine Warteschlange, die zwei Nachrichten enthält.

![Anzeigen einer Warteschlange](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC651470.png)

Weitere Informationen über Speicherdienstwarteschlangen finden Sie unter [Verwenden des Warteschlangenspeicherdiensts](http://go.microsoft.com/fwlink/?LinkID=264702). Weitere Informationen zum Webdienst für Speicherdienstwarteschlangen finden Sie unter [Konzepte des Warteschlangendiensts](http://go.microsoft.com/fwlink/?LinkId=264788). Informationen dazu, wie Nachrichten mithilfe von Visual Studio an eine Speicherdienstwarteschlange gesendet werden, finden Sie unter [Senden von Nachrichten an eine Speicherdienstwarteschlange](https://msdn.microsoft.com/library/azure/jj649344.aspx).

>[AZURE.NOTE]Speicherdienstwarteschlangen unterscheiden sich von Service Bus-Warteschlangen. Weitere Informationen zu Service Bus-Warteschlangen finden Sie unter "Service Bus-Warteschlangen, -Themen und -Abonnements".

### Arbeiten mit Tabellenressourcen

Der Azure Tabellenspeicherdienst erlaubt die Speicherung großer Mengen von strukturierten Daten. Der Dienst ist ein NoSQL-Datenspeicher zur Annahme authentifizierter Anrufe von innerhalb und außerhalb der Azure-Cloud. Azure-Tabellen sind hervorragend zur Speicherung strukturierter nicht relationaler Daten geeignet.

### Erstellen einer Tabelle

1. Wählen Sie im Server-Explorer den Knoten **Tabellen** des Speicherkontos aus, und wählen Sie dann **Tabelle erstellen**.

1. Geben Sie im Dialogfeld **Tabelle erstellen** einen Namen für die Tabelle ein.

### So zeigen Sie Tabellendaten an

1. Öffnen Sie im Server-Explorer den Knoten **Azure**, und öffnen Sie dann den Knoten **Storage**.

1. Öffnen Sie den für Sie relevanten Speicherkontoknoten, und öffnen Sie dann den Knoten **Tabellen**, um eine Liste der Tabellen für das Speicherkonto anzuzeigen.

1. Öffnen Sie das Kontextmenü für eine Tabelle, und wählen Sie dann **Tabelle anzeigen**.

    
    ![Eine Azure-Tabelle im Projektmappen-Explorer](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC744165.png)

Die Tabelle ist in Entitäten (Anzeige in Zeilen) und Eigenschaften (Anzeige in Spalten) organisiert. Die folgende Abbildung zeigt z. B. Entitäten, die im **Tabellen-Designer** aufgeführt sind:


### So bearbeiten Sie Tabellendaten

1. Öffnen Sie im **Tabellen-Designer** das Kontextmenü für eine Entität (eine einzelne Zeile) oder eine Eigenschaft (eine einzelne Zelle), und wählen Sie dann **Bearbeiten** aus.

    ![Hinzufügen oder Bearbeiten einer Tabellenentität](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC656238.png)

    Entitäten in einer einzelnen Tabelle müssen nicht denselben Eigenschaftensatz (Spalten) aufweisen. Beachten Sie beim Anzeigen und Bearbeiten von Tabellendaten folgende Einschränkungen:


    - Binäre Daten (vom Typ "Byte") können zwar nicht angezeigt oder bearbeitet, aber in einer Tabelle gespeichert werden.

    - Die **PartitionKey**- oder **RowKey**-Werte können nicht bearbeitet werden, da dieser Vorgang vom Tabellenspeicher in Azure nicht unterstützt wird.

    - Eine Eigenschaft mit dem Namen "Timestamp" kann nicht erstellt werden, da von den Azure Storage-Diensten eine Eigenschaft mit diesem Namen verwendet wird.

    - DateTime-Werte müssen den Regions- und Spracheinstellungen des Computers (Beispiel: TT.MM.JJJJ HH:MM:SS für Deutschland) entsprechend eingegeben werden.

### So fügen Sie Entitäten hinzu

1. Wählen Sie im **Tabellen-Designer** die Schaltfläche **Entität hinzufügen** aus, die sich in der Nähe der oberen rechten Ecke der Tabellenansicht befindet.

    ![Entität hinzufügen](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655336.png)

1. Geben Sie im Dialogfeld **Entität hinzufügen** die Werte der **PartitionKey**- und **RowKey**-Eigenschaften ein.

    ![Dialogfeld "Entität hinzufügen"](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655335.png)

    Gehen Sie bei der Eingabe der Werte mit Bedacht vor, da sie nach dem Schließen des Dialogfelds nicht mehr geändert werden können. Zum Ändern muss die Entität gelöscht und erneut hinzugefügt werden.

## So filtern Sie Entitäten

Mit dem Abfrage-Generator können Sie den Satz von Entitäten anpassen, der in einer Tabelle angezeigt wird.

1. Öffnen Sie zum Öffnen des Abfrage-Generators eine Tabelle für die Anzeige.

1. Wählen Sie in der Symbolleiste der Tabellenansicht die Schaltfläche aus, die sich ganz rechts befindet.

    Das Dialogfeld **Abfrage-Generator** wird angezeigt. Im Folgenden ist eine mit dem Abfrage-Generator erstellte Abfrage abgebildet.

    ![Abfrage-Generator](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC652231.png)

1. Schließen Sie das Dialogfeld, wenn Sie die Abfrage erstellt haben. Die resultierende Textform der Abfrage wird in einem Textfeld als WCF Data Services-Filter angezeigt.

1. Klicken Sie auf das grüne Dreieckssymbol, um die Abfrage auszuführen.

    Sie können im **Tabellen-Designer** angezeigte Entitätsdaten filtern, indem Sie im Filterfeld direkt eine WCF Data Services-Filterzeichenfolge eingeben. Diese Art von Zeichenfolge ist mit einer SQL-WHERE-Klausel vergleichbar, wird jedoch als HTTP-Anforderung an den Server gesendet. Informationen zum Erstellen von Filterzeichenfolgen finden Sie unter [Erstellen von Filterzeichenfolgen für den Tabellen-Designer](https://msdn.microsoft.com/library/azure/ff683669.aspx).

    Die folgende Abbildung zeigt ein Beispiel für eine gültige Filterzeichenfolge:

    ![VST\_SE\_TableFilter](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655337.png)

## Aktualisieren von Speicherdaten

Wenn der Server-Explorer eine Verbindung mit einem Speicherkonto herstellt oder Daten daraus abruft, kann es bis zu einer Minute dauern, bis der Vorgang abgeschlossen ist. Wenn Sie keine Verbindung herstellen können, führt dies möglicherweise zu einer Zeitüberschreitung des Vorgangs. Während Daten abgerufen werden, können Sie weiterhin in anderen Bereichen von Visual Studio arbeiten. Wenn der Vorgang zu lange dauert, klicken Sie zum Abbrechen in der Symbolleiste des Server-Explorers auf die Schaltfläche **Aktualisieren beenden**.

### So aktualisieren Sie Blobcontainerdaten

- Wählen Sie unter **Storage** den Knoten **Blobs** und dann in der Symbolleiste des Server-Explorers die Schaltfläche **Aktualisieren** aus.

- Um die angezeigte Liste der Blobs zu aktualisieren, wählen Sie die Schaltfläche **Ausführen**.

### So aktualisieren Sie Tabellendaten

- Wählen Sie unter **Storage** den Knoten **Tabellen** und dann die Schaltfläche **Aktualisieren** aus. 

- Um die Liste der Entitäten zu aktualisieren, die im **Tabellen-Designer** angezeigt wird, klicken Sie im **Tabellen-Designer** auf die Schaltfläche **Ausführen**.

### So aktualisieren Sie Warteschlangendaten

- Wählen Sie den Knoten **Warteschlangen** und dann die Schaltfläche **Aktualisieren** aus.

### So aktualisieren Sie alle Elemente in einem Speicherkonto

- Wählen Sie den Kontonamen und dann die Schaltfläche **Aktualisieren** in der Symbolleiste des Server-Explorers aus.

## Hinzufügen von Speicherkonten mithilfe des Server-Explorers

Es gibt zwei Möglichkeiten, Speicherkonten mithilfe des Server-Explorers hinzuzufügen. Sie können in Ihrem Azure-Abonnement ein neues Speicherkonto erstellen, oder Sie können ein vorhandenes Speicherkonto anfügen.

### So erstellen Sie ein neues Speicherkonto mit dem Server-Explorer

1. Öffnen Sie im Server-Explorer das Kontextmenü für den Knoten "Speicher", und wählen Sie dann "Speicherkonto erstellen".

    ![Erstellen eines neuen Azure-Speicherkontos](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC744166.png)

1. Wählen Sie im Dialogfeld **Speicherkonto erstellen** die folgenden Informationen aus, oder geben Sie sie ein.

    - Das Azure-Abonnement, dem Sie das Speicherkonto hinzufügen möchten

    - Den Namen, den Sie für das neue Speicherkonto verwenden möchten

    - Die Region oder Affinitätsgruppe (z. B. "USA, Westen" oder "Ostasien")

    - Den Typ der Replikation, den Sie für das Speicherkonto verwenden möchten, zum Beispiel "Georedundant"

1. Wählen Sie **Erstellen**.

    Das neue Speicherkonto wird im Projektmappen-Explorer in der Liste **Speicher** angezeigt.

### So fügen Sie ein vorhandenes Speicherkonto mithilfe des Server-Explorers an

1. Öffnen Sie im Server-Explorer das Kontextmenü für den Azure-Speicherknoten, und wählen Sie dann **Externen Speicher anfügen** aus.

    ![Hinzufügen eines vorhandenen Speicherkontos](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766039.png)

1. Wählen Sie im Dialogfeld **Speicherkonto erstellen** die folgenden Informationen aus, oder geben Sie sie ein.

    - Den Namen des vorhandenen Speicherkontos, das Sie anfügen möchten. Sie können einen Namen eingeben oder ihn aus der Liste auswählen.

    - Den Schlüssel für das ausgewählte Speicherkonto. Dieser Wert wird für gewöhnlich bereitgestellt, wenn Sie ein Speicherkonto auswählen. Wenn Visual Studio den Speicherkontoschlüssel speichern soll, wählen Sie das Feld "Kontoschlüssel speichern" aus.

    - Das Protokoll, das zum Herstellen einer Verbindung mit dem Speicherkonto verwendet wird, beispielsweise HTTP, HTTPS oder ein benutzerdefinierter Endpunkt. Unter [Konfigurieren von Verbindungszeichenfolgen](https://msdn.microsoft.com/library/azure/ee758697.aspx) finden Sie weitere Informationen über benutzerdefinierte Endpunkte.

### So zeigen Sie die sekundären Endpunkte an 

- Wenn Sie ein Speicherkonto mithilfe der Replikationsoption **Read-Access Geo Redundant** erstellt haben, können Sie dessen sekundäre Endpunkte anzeigen. Öffnen Sie das Kontextmenü für den Kontonamen, und wählen Sie dann **Eigenschaften** aus.

    ![Sekundäre Speicherendpunkte](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766040.png)

### So entfernen Sie ein Speicherkonto aus dem Server-Explorer

- Öffnen Sie im Server-Explorer das Kontextmenü für den Kontonamen, und wählen Sie **Löschen**. Wenn Sie ein Speicherkonto löschen, werden auch alle gespeicherten Schlüsselinformationen für dieses Konto entfernt.

>[AZURE.NOTE]Das Löschen eines Speicherkontos aus dem Server-Explorer besitzt keine Auswirkungen auf das Speicherkonto oder die darin enthaltenen Daten. Es wird lediglich der Verweis aus dem Server-Explorer entfernt. Verwenden Sie das Azure-Verwaltungsportal, um ein Speicherkonto dauerhaft zu löschen.

## Siehe auch

[Zugreifen auf Azure Storage Services](https://msdn.microsoft.com/library/azure/ee405490.aspx)

<!---HONumber=August15_HO9-->