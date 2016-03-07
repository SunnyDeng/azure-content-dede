<properties
   pageTitle="Konfigurieren der Rollen für einen Azure-Clouddienst mit Visual Studio | Microsoft Azure"
   description="Erfahren Sie, wie Rollen für Azure-Clouddienste mit Visual Studio eingerichtet und konfiguriert werden."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="02/24/2016"
   ms.author="tarcher" />

# Konfigurieren der Rollen für einen Azure-Clouddienst mit Visual Studio

Ein Azure-Clouddienst kann einen oder mehrere Worker- oder Webrollen aufweisen. Für jede Rolle müssen Sie definieren, wie diese Rolle eingerichtet ist, und konfigurieren, wie diese Rolle ausgeführt wird. Weitere Informationen zu Rollen in Clouddiensten erhalten Sie im Video [Introduction to Azure Cloud Services](https://channel9.msdn.com/Series/Windows-Azure-Cloud-Services-Tutorials/Introduction-to-Windows-Azure-Cloud-Services) (in englischer Sprache). Die Informationen für Ihren Clouddienst werden in den folgenden Dateien gespeichert:

- **ServiceDefinition.csdef**

    Die Dienstdefinitionsdatei definiert die Laufzeiteinstellungen für Ihren Clouddienst, u. a. welche Rollen erforderlich sind, Endpunkte und Größe des virtuellen Computers. Die in dieser Datei gespeicherten Daten können während der Ausführung der Rolle nicht geändert werden.

- **ServiceConfiguration.cscfg**

    Die Dienstkonfigurationsdatei konfiguriert, wie viele Instanzen einer Rolle ausgeführt werden, und die Werte der für eine Rolle definierten Einstellungen. Die in dieser Datei gespeicherten Daten können während der Ausführung der Rolle geändert werden.

Um verschiedene Werte für die Einstellungen zur Ausführungsweise der Rolle speichern zu können, können Sie mehrere Dienstkonfigurationen besitzen. Sie können unterschiedliche Dienstkonfigurationen für die einzelnen Bereitstellungsumgebungen verwenden. Beispielsweise können Sie festlegen, dass die Verbindungszeichenfolge des Speicherkontos den lokalen Azure-Speicheremulator in einer lokalen Dienstkonfiguration verwendet, und eine weitere Dienstkonfiguration erstellen, die den Azure-Speicher in der Cloud verwendet.

Beim Erstellen eines neuen Azure-Clouddiensts in Visual Studio werden standardmäßig zwei Dienstkonfigurationen erstellt. Diese Konfigurationen werden dem Azure-Projekt hinzugefügt. Sie heißen:

- ServiceConfiguration.Cloud.cscfg

- ServiceConfiguration.Local.cscfg

## Konfigurieren eines Azure-Clouddiensts

Sie können einen Azure-Clouddienst vom Projektmappen-Explorer in Visual Studio aus konfigurieren, wie in der folgenden Abbildung dargestellt.

![Konfigurieren eines Clouddiensts](./media/vs-azure-tools-configure-roles-for-cloud-service/IC713462.png)

### So konfigurieren Sie einen Azure-Clouddienst

1. Öffnen Sie zum Konfigurieren der einzelnen Rollen im Azure-Projekt im **Projektmappen-Explorer** das Kontextmenü für die Rolle im Azure-Projekt, und wählen Sie dann **Eigenschaften** aus.

    Eine Seite mit dem Namen der Rolle wird im Visual Studio-Editor angezeigt. Die Seite zeigt die Felder für die Registerkarte **Konfiguration** an.

1. Wählen Sie in der Liste **Dienstkonfiguration** den Namen der Dienstkonfiguration aus, die Sie bearbeiten möchten.

    Wenn Sie alle Dienstkonfigurationen für diese Rolle ändern möchten, können Sie **Alle Konfigurationen** auswählen.

    >[AZURE.IMPORTANT] Wenn Sie eine bestimmte Dienstkonfiguration auswählen, sind einige Eigenschaften deaktiviert, da sie nur für alle Konfigurationen festgelegt werden können. Um diese Eigenschaften zu bearbeiten, müssen Sie "Alle Konfigurationen" auswählen.

    Sie können jetzt eine Registerkarte auswählen, um alle aktivierten Eigenschaften in dieser Ansicht zu aktualisieren.

## Ändern der Anzahl von Rolleninstanzen

Um die Leistung des Clouddiensts zu verbessern, können Sie die Anzahl der ausgeführten Instanzen einer Rolle basierend auf der Anzahl der Benutzer oder der erwarteten Auslastung für eine Rolle ändern. Wenn der Clouddienst in Azure ausgeführt wird, wird ein separater virtueller Computer für jede Instanz einer Rolle erstellt. Dies wirkt sich auf die Abrechnung für die Bereitstellung dieses Clouddiensts aus. Weitere Informationen zur Abrechnung finden Sie unter [Informationen zu Ihrer Rechnung für Microsoft Azure](billing-understand-your-bill.md).

### So ändern Sie die Anzahl der Instanzen für eine Rolle

1. Wählen Sie die Registerkarte **Konfiguration** aus.

1. Wählen Sie in der Liste **Dienstkonfiguration** die Dienstkonfiguration aus, die Sie aktualisieren möchten.

    >[AZURE.NOTE] Sie können die Instanzenanzahl für eine bestimmte Dienstkonfiguration oder für alle Dienstkonfigurationen festlegen.

1. Geben Sie im Textfeld **Instanzenanzahl** die Anzahl der Instanzen ein, die Sie für diese Rolle starten möchten.

    >[AZURE.NOTE] Jede Instanz wird auf einem separaten virtuellen Computer ausgeführt, wenn Sie Ihren Clouddienst in Azure veröffentlichen.

1. Wählen Sie auf der Symbolleiste die Schaltfläche **Speichern** aus, um diese Änderungen in der Dienstkonfigurationsdatei zu speichern.

## Verwalten von Verbindungszeichenfolgen für Speicherkonten

Sie können Verbindungszeichenfolgen für Ihre Dienstkonfigurationen hinzufügen, entfernen oder ändern. Möglicherweise möchten Sie verschiedene Verbindungszeichenfolgen für verschiedene Dienstkonfigurationen verwenden. Beispielsweise möchten Sie eine lokale Verbindungszeichenfolge für eine lokale Dienstkonfiguration mit dem Wert `UseDevelopmentStorage=true` festlegen. Sie können auch eine Clouddienstkonfiguration konfigurieren, die ein Speicherkonto in Azure verwendet.

>[AZURE.WARNING] Wenn Sie die Schlüsselinformationen eines Azure-Speicherkontos für eine Speicherkonto-Verbindungszeichenfolge eingeben, werden diese Informationen lokal in der Dienstkonfigurationsdatei gespeichert. Diese Informationen werden jedoch derzeit nicht als verschlüsselter Text gespeichert.

Wenn Sie verschiedene Werte für die einzelnen Dienstkonfigurationen verwenden, müssen Sie nicht verschiedene Verbindungszeichenfolgen im Clouddienst verwenden oder Ihren Code ändern, wenn Sie Ihren Clouddienst in Azure veröffentlichen. Im Code können Sie denselben Namen für die Verbindungszeichenfolge verwenden. Der Wert unterscheidet sich basierend auf der Dienstkonfiguration, die Sie auswählen, wenn Sie einen Clouddienst erstellen oder veröffentlichen.

### So verwalten Sie Verbindungszeichenfolgen für Speicherkonten

1. Wählen Sie die Registerkarte **Einstellungen** aus.

1. Wählen Sie in der Liste **Dienstkonfiguration** die Dienstkonfiguration aus, die Sie aktualisieren möchten.

    >[AZURE.NOTE] Sie können Verbindungszeichenfolgen für eine bestimmte Dienstkonfiguration aktualisieren. Wenn Sie jedoch eine Verbindungszeichenfolge hinzufügen oder löschen möchten, müssen Sie "Alle Konfigurationen" auswählen.

1. Wenn Sie eine Verbindungszeichenfolge hinzufügen möchten, wählen Sie die Schaltfläche **Einstellung hinzufügen** aus. Der Liste wird ein neuer Eintrag hinzugefügt.

1. Geben Sie im Textfeld **Name** den Namen ein, den Sie für die Verbindungszeichenfolge verwenden möchten.

1. Wählen Sie in der Dropdownliste **Typ** den Eintrag **Verbindungszeichenfolge** aus.

1. Um den Wert für die Verbindungszeichenfolge zu ändern, klicken Sie auf die Schaltfläche mit den Auslassungspunkten (...). Das Dialogfeld **Verbindungszeichenfolge für den Speicher erstellen** wird angezeigt.

1. Um den lokalen Speicherkontoemulator zu verwenden, wählen Sie das Optionsfeld **Microsoft Azure-Speicheremulator** aus, und klicken Sie dann auf die Schaltfläche **OK**.

1. Um ein Speicherkonto in Azure zu verwenden, wählen Sie das Optionsfeld **Ihr Abonnement** aus, und wählen Sie dann das gewünschte Speicherkonto aus.

1. Wählen Sie das Optionsfeld **Manuell eingegebene Anmeldeinformationen** aus, um benutzerdefinierte Anmeldeinformationen zu verwenden. Geben Sie den Speicherkontonamen und dann entweder den primären oder sekundären Schlüssel ein. Informationen zur Erstellung eines Speicherkontos und zur Eingabe der Details für das Speicherkonto im Dialogfeld **Verbindungszeichenfolge für den Speicher erstellen** finden Sie unter [Veröffentlichen und Bereitstellen einer Azure-Anwendung in Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md).

1. Um eine Verbindungszeichenfolge zu löschen, wählen Sie die Verbindungszeichenfolge aus, und wählen Sie dann die Schaltfläche **Einstellung entfernen** aus.

1. Wählen Sie auf der Symbolleiste das Symbol **Speichern** aus, um diese Änderungen in der Dienstkonfigurationsdatei zu speichern.

1. Für den Zugriff auf die Verbindungszeichenfolge in der Dienstkonfigurationsdatei müssen Sie den Wert der Konfigurationseinstellung abrufen. Der folgende Code zeigt ein Beispiel, in dem Blob-Speicher erstellt wird und Daten mithilfe einer Verbindungszeichenfolge `MyConnectionString` aus der Dienstkonfigurationsdatei hochgeladen werden, wenn ein Benutzer auf der Seite "Default.aspx" in der Webrolle für einen Azure-Clouddienst **Button1** auswählt: Fügen Sie die folgenden using-Anweisungen zu "Default.aspx.cs" hinzu:

    ```
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. Öffnen Sie "Default.aspx.cs" in der Entwurfsansicht, und fügen Sie eine Schaltfläche aus der Toolbox hinzu. Fügen Sie der `Button1_Click`-Methode den folgenden Code hinzu. Dieser Code verwendet `GetConfigurationSettingValue`, um den Wert aus der Dienstkonfigurationsdatei für die Verbindungszeichenfolge abzurufen. Anschließend wird ein Blob im Speicherkonto erstellt, auf das in der Verbindungszeichenfolge `MyConnectionString` verwiesen wird, und schließlich fügt das Programm dem Blob Text hinzu.

    ```
    protected void Button1_Click(object sender, EventArgs e)
    {
        // Setup the connection to Azure Storage
        var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("MyConnectionString"));
        var blobClient = storageAccount.CreateCloudBlobClient();
        // Get and create the container
        var blobContainer = blobClient.GetContainerReference("quicklap");
        blobContainer.CreateIfNotExists();
        // upload a text blob
        var blob = blobContainer.GetBlockBlobReference(Guid.NewGuid().ToString());
        blob.UploadText("Hello Azure");

    }
    ```

## Hinzufügen benutzerdefinierter Einstellungen zur Verwendung in Ihrem Azure-Clouddienst

Benutzerdefinierte Einstellungen in der Dienstkonfigurationsdatei ermöglichen das Hinzufügen eines Namens und eines Werts für eine Zeichenfolge für eine bestimmte Dienstkonfiguration. Sie können diese Einstellung verwenden, um ein Feature in Ihrem Clouddienst zu konfigurieren, indem Sie den Wert der Einstellung lesen und diesen Wert zum Steuern der Logik im Code verwenden. Sie können diese Dienstkonfigurationswerte ohne erneute Erstellung des Dienstpakets oder während der Ausführung des Clouddiensts ändern. Der Code kann auf Benachrichtigungen bzgl. Änderungen einer Einstellung prüfen. Weitere Informationen finden Sie unter [RoleEnvironment.Changing-Ereignis](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changing.aspx).

Sie können benutzerdefinierte Einstellungen für Ihre Dienstkonfigurationen hinzufügen, entfernen oder ändern. Möglicherweise möchten Sie verschiedene Werte für diese Zeichenfolgen für verschiedene Dienstkonfigurationen verwenden.

Wenn Sie verschiedene Werte für die einzelnen Dienstkonfigurationen verwenden, müssen Sie nicht verschiedene Zeichenfolgen im Clouddienst verwenden oder Ihren Code ändern, wenn Sie Ihren Clouddienst in Azure veröffentlichen. Im Code können Sie denselben Namen für die Zeichenfolge verwenden. Der Wert unterscheidet sich basierend auf der Dienstkonfiguration, die Sie auswählen, wenn Sie einen Clouddienst erstellen oder veröffentlichen.

### So fügen Sie benutzerdefinierte Einstellungen zur Verwendung in Ihrem Azure-Clouddienst hinzu

1. Wählen Sie die Registerkarte **Einstellungen** aus.

1. Wählen Sie in der Liste **Dienstkonfiguration** die Dienstkonfiguration aus, die Sie aktualisieren möchten.

    >[AZURE.NOTE] Sie können Zeichenfolgen für eine bestimmte Dienstkonfiguration aktualisieren. Wenn Sie jedoch eine Zeichenfolge hinzufügen oder löschen möchten, müssen Sie **Alle Konfigurationen** auswählen.

1. Wenn Sie eine Zeichenfolge hinzufügen möchten, wählen Sie die Schaltfläche **Einstellung hinzufügen** aus. Der Liste wird ein neuer Eintrag hinzugefügt.

1. Geben Sie im Textfeld **Name** den Namen ein, den Sie für die Zeichenfolge verwenden möchten.

1. Wählen Sie in der Dropdownliste **Typ** den Eintrag **Zeichenfolge** aus.

1. Geben Sie zum Hinzufügen oder Ändern des Werts für die Zeichenfolge den neuen Wert ins Textfeld **Wert** ein.

1. Um eine Zeichenfolge zu löschen, wählen Sie die Zeichenfolge aus, und wählen Sie dann die Schaltfläche **Einstellung entfernen** aus.

1. Wählen Sie auf der Symbolleiste die Schaltfläche **Speichern** aus, um diese Änderungen in der Dienstkonfigurationsdatei zu speichern.

1. Für den Zugriff auf die Zeichenfolge in der Dienstkonfigurationsdatei müssen Sie den Wert der Konfigurationseinstellung abrufen.

    Sie müssen sicherstellen, dass die folgenden using-Anweisungen bereits wie im vorherigen Verfahren zu "Default.aspx.cs" hinzugefügt wurden:

    ```
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. Fügen Sie den folgenden Code zur `Button1_Click`-Methode hinzu, um auf diese Zeichenfolge auf die gleiche Weise zuzugreifen wie auf eine Verbindungszeichenfolge. Ihr Code kann dann bestimmten Code basierend auf dem Wert der Einstellungszeichenfolge für die verwendete Dienstkonfigurationsdatei ausführen.

    ```
    var settingValue = RoleEnvironment.GetConfigurationSettingValue("MySetting");
    if (settingValue == “ThisValue”)
    {
    // Perform these lines of code
    }
    ```

## Verwalten von lokalem Speicher für jede Rolleninstanz

Sie können lokalen Dateisystemspeicher für jede Instanz einer Rolle hinzufügen. Hier können Sie die lokalen Daten speichern, auf die nicht von anderen Rollen zugegriffen werden muss. Alle Daten, die nicht im Tabellen-, Blob- oder SQL-Datenbankspeicher gespeichert werden müssen, können hier abgelegt werden. Beispielsweise können Sie diesen lokalen Speicher zum Zwischenspeichern von Daten verwenden, die möglicherweise erneut verwendet werden müssen. Auf diese gespeicherten Daten kann nicht von anderen Instanzen einer Rolle zugegriffen werden. Weitere Informationen zu lokalen Speicherressourcen finden Sie unter [Konfigurieren lokaler Speicherressourcen](cloud-services/cloud-services-configure-local-storage-resources.md).

Die Einstellungen des lokalen Speichers gelten für alle Dienstkonfigurationen. Sie können lokalen Speicher nur für alle Dienstkonfigurationen hinzufügen, entfernen oder ändern.

### So verwalten Sie lokalen Speicher für jede Rolleninstanz

1. Wählen Sie die Registerkarte **Lokaler Speicher** aus.

1. Wählen Sie in der Liste **Dienstkonfiguration** den Eintrag **Alle Konfigurationen** aus.

1. Um einen lokalen Speichereintrag hinzuzufügen, wählen Sie die Schaltfläche **Lokalen Speicher hinzufügen** aus. Der Liste wird ein neuer Eintrag hinzugefügt.

1. Geben Sie im Textfeld **Name** den Namen ein, den Sie für diesen lokalen Speicher verwenden möchten.

1. Geben Sie im Textfeld **Größe** die Größe in MB ein, die Sie für diesen lokalen Speicher benötigen.

1. Um die Daten in diesem lokalen Speicher zu entfernen, wenn der virtuelle Computer für diese Rolle wiederverwendet wird, aktivieren Sie das Kontrollkästchen **Bei Wiederverwendung der Rolle bereinigen**.

1. Um einen vorhandenen lokalen Speichereintrag zu bearbeiten, wählen Sie die Zeile, die Sie aktualisieren möchten. Dann können Sie die Felder bearbeiten, wie in den vorherigen Schritten beschrieben.

1. Um einen lokalen Speichereintrag zu löschen, wählen Sie den Speichereintrag in der Liste aus, und wählen Sie dann die Schaltfläche **Lokalen Speicher entfernen** aus.

1. Wählen Sie auf der Symbolleiste das Symbol **Speichern** aus, um diese Änderungen in den Dienstkonfigurationsdateien zu speichern.

1. Um auf den lokalen Speicher zuzugreifen, den Sie in der Dienstkonfigurationsdatei hinzugefügt haben, müssen Sie den Wert der Konfigurationseinstellung der lokalen Ressource abrufen. Verwenden Sie die folgenden Codezeilen für den Zugriff auf diesen Wert, erstellen Sie eine Datei mit dem Namen **MyStorageTest.txt**, und schreiben Sie eine Zeile mit Testdaten in diese Datei. Sie können diesen Code der `Button_Click`-Methode hinzufügen, die Sie in den vorherigen Verfahren verwendet haben:

1. Sie müssen sicherstellen, dass die folgenden using-Anweisungen der Datei "Default.aspx.cs" hinzugefügt werden:

    ```
    using System.IO;
    using System.Text;
    ```

1. Fügen Sie der `Button1_Click`-Methode den folgenden Code hinzu. Dadurch wird die Datei im lokalen Speicher erstellt, und die Testdaten werden in diese Datei geschrieben.

    ```
    // Retrieve an object that points to the local storage resource
    LocalResource localResource = RoleEnvironment.GetLocalResource("LocalStorage1");

    //Define the file name and path
    string[] paths = { localResource.RootPath, "MyStorageTest.txt" };
    String filePath = Path.Combine(paths);

    using (FileStream writeStream = File.Create(filePath))
    {
          Byte[] textToWrite = new UTF8Encoding(true).GetBytes("Testing Web role storage");
          writeStream.Write(textToWrite, 0, textToWrite.Length);
    }
    ```

1. (Optional) Um die Datei anzuzeigen, die beim lokalen Ausführen des Clouddiensts erstellt wurde, führen Sie die folgenden Schritte aus:

  1. Führen Sie die Webrolle aus, und klicken Sie auf **Button1**, um sicherzustellen, dass der Code in `Button1_Click` aufgerufen wird.

  1. Öffnen Sie im Infobereich das Kontextmenü für das Azure-Symbol, und wählen Sie **Serveremulator-Benutzeroberfläche anzeigen** aus. Das Dialogfeld **Azure-Serveremulator** wird angezeigt.

  1. Wählen Sie die Webrolle aus:

  1. Wählen Sie in der Menüleiste **Extras**, **Lokalen Speicher öffnen** aus. Ein Windows Explorer-Fenster wird angezeigt.

  1. Geben Sie in der Menüleiste im Textfeld **Suchen** den Text **MyStorageTest.txt** ein, und drücken Sie dann die **EINGABETASTE**, um die Suche zu starten.

    Die Datei wird in den Suchergebnissen angezeigt.

  1. Um den Inhalt der Datei anzuzeigen, öffnen Sie das Kontextmenü für die Datei, und wählen Sie **Öffnen** aus.

## Erfassen von Clouddienst-Diagnoseinformationen

Sie können Diagnosedaten für den Azure-Clouddienst sammeln. Diese Daten werden einem Speicherkonto hinzugefügt. Möglicherweise möchten Sie verschiedene Verbindungszeichenfolgen für verschiedene Dienstkonfigurationen verwenden. Beispielsweise möchten Sie ein lokales Speicherkonto für eine lokale Dienstkonfiguration mit dem Wert "UseDevelopmentStorage=true" einrichten. Sie können auch eine Clouddienstkonfiguration konfigurieren, die ein Speicherkonto in Azure verwendet. Weitere Informationen zur Azure-Diagnose finden Sie unter "Sammeln von Protokollierungsdaten mit der Azure-Diagnose".

>[AZURE.NOTE] Die lokale Dienstkonfiguration ist bereits für die Verwendung lokaler Ressourcen konfiguriert. Wenn Sie die Clouddienstkonfiguration für die Veröffentlichung Ihres Azure-Clouddienst verwenden, wird die Verbindungszeichenfolge, die Sie beim Veröffentlichen angeben, auch für die Diagnoseverbindungszeichenfolge verwendet, es sei denn, Sie haben eine Verbindungszeichenfolge angegeben. Wenn Sie Ihren Clouddienst mithilfe von Visual Studio paketieren, wird die Verbindungszeichenfolge in der Dienstkonfiguration nicht geändert.

### So erfassen Sie Clouddienst-Diagnoseinformationen

1. Wählen Sie die Registerkarte **Konfiguration** aus.

1. Wählen Sie in der Liste **Dienstkonfiguration** die Dienstkonfiguration aus, die Sie aktualisieren möchten, oder wählen Sie **Alle Konfigurationen** aus.

    >[AZURE.NOTE] Sie können das Speicherkonto für eine bestimmte Dienstkonfiguration aktualisieren. Wenn Sie jedoch die Diagnose aktivieren oder deaktivieren möchten, müssen Sie "Alle Konfigurationen" auswählen.

1. Um die Diagnose zu aktivieren, aktivieren Sie das Kontrollkästchen **Diagnose aktivieren**.

1. Um den Wert für das Speicherkonto zu ändern, klicken Sie auf die Schaltfläche mit den Auslassungspunkten (...).

    Das Dialogfeld **Verbindungszeichenfolge für den Speicher erstellen** wird angezeigt.

1. Um eine lokale Verbindungszeichenfolge zu verwenden, wählen Sie die Option für den Azure-Speicheremulator aus, und klicken Sie dann auf **OK**.

1. Um ein Speicherkonto zu verwenden, das dem Azure-Abonnement zugeordnet ist, wählen Sie die Option **Ihr Abonnement** aus.

1. Um ein Speicherkonto für die lokale Verbindungszeichenfolge zu verwenden, wählen Sie die Option **Manuell eingegebene Anmeldeinformationen** aus.

    Weitere Informationen zur Erstellung eines Speicherkontos und zur Eingabe der Details für das Speicherkonto im Dialogfeld **Verbindungszeichenfolge für den Speicher erstellen** finden Sie unter [Veröffentlichen und Bereitstellen einer Azure-Anwendung in Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md).

1. Wählen Sie unter **Kontoname** das Speicherkonto aus, das Sie verwenden möchten.

    Wenn Sie die Anmeldeinformationen für das Speicherkonto manuell eingeben, muss der Primärschlüssel in **Kontoschlüssel** kopiert oder eingegeben werden. Der Schlüssel kann vom Azure-Verwaltungsportal kopiert werden. Führen Sie zum Kopieren dieses Schlüssels folgende Schritte in der Ansicht **Speicherkonten** im Azure-Verwaltungsportal aus:

  1. Wählen Sie das Speicherkonto, das Sie für Ihren Clouddienst verwenden möchten.

  1. Wählen Sie die Schaltfläche **Zugriffsschlüssel verwalten** am unteren Bildschirmrand aus. Das Dialogfeld **Zugriffsschlüssel verwalten** wird angezeigt.

  1. Um den Zugriffsschüssel zu kopieren, wählen Sie die Schaltfläche **In Zwischenablage kopieren** aus. Sie können jetzt diesen Schlüssel in das Feld **Kontoschlüssel** einfügen.

1. Wenn Sie das von Ihnen angegebene Speicherkonto beim Veröffentlichen des Clouddiensts in Azure als Verbindungszeichenfolge für die Diagnose (und die Zwischenspeicherung) verwenden möchten, aktivieren Sie das Kontrollkästchen **Verbindungszeichenfolgen für Entwicklungsspeicher zur Diagnose und zum Zwischenspeichern beim Veröffentlichen in Microsoft Azure mit Anmeldeinformationen für Microsoft Azure-Speicherkonto aktualisieren**.

1. Wählen Sie auf der Symbolleiste die Schaltfläche **Speichern** aus, um diese Änderungen in der Dienstkonfigurationsdatei zu speichern.

## Ändern der Größe des virtuellen Computers, der für die einzelnen Rollen verwendet wird

Sie können die Größe der virtuellen Computer für die einzelnen Rollen festlegen. Sie können diese Größe nur für alle Dienstkonfigurationen festlegen. Wenn Sie eine geringere Computergröße auswählen, werden weniger CPU-Kerne, weniger Arbeitsspeicher und weniger lokaler Festplattenspeicher zugewiesen. Die zugewiesene Bandbreite ist ebenfalls kleiner. Weitere Informationen zu diesen Größen und den zugewiesenen Ressourcen finden Sie unter [Größen für Clouddienste](cloud-services/cloud-services-sizes-specs.md).

Die für die einzelnen virtuellen Computer in Azure erforderlichen Ressourcen wirken sich auf die Kosten der Ausführung Ihres Clouddiensts in Azure aus. Weitere Informationen zu Azure-Abrechnungen finden Sie unter [Informationen zu Ihrer Rechnung für Microsoft Azure](billing-understand-your-bill.md).

### So ändern Sie die Größe des virtuellen Computers

1. Wählen Sie die Registerkarte **Konfiguration** aus.

1. Wählen Sie in der Liste **Dienstkonfiguration** den Eintrag **Alle Konfigurationen** aus.

1. Um die Größe des virtuellen Computers für diese Rolle auszuwählen, wählen Sie die entsprechende Größe aus der Liste **Größe des virtuellen Computers** aus.

1. Wählen Sie auf der Symbolleiste die Schaltfläche **Speichern** aus, um diese Änderungen in der Dienstkonfigurationsdatei zu speichern.

## Verwalten von Endpunkten und Zertifikaten für Ihre Rollen

Sie konfigurieren Netzwerkendpunkte durch die Angabe des Protokolls, der Portnummer und, für HTTPS, der SSL-Zertifikatsinformationen. Versionen vor Juni 2012 unterstützen HTTP, HTTPS und TCP. Von der Juni 2012-Version werden diese Protokolle und UDP unterstützt. Sie können UDP nicht für Eingabeendpunkte im Serveremulator verwenden. Dieses Protokoll kann nur für interne Endpunkte verwendet werden.

Zum Verbessern der Sicherheit Ihres Azure-Clouddiensts können Sie Endpunkte erstellen, die das HTTPS-Protokoll verwenden. Wenn Sie z. B. einen Clouddienst besitzen, der von Kunden für Bestellungen verwendet wird, möchten Sie durch Verwendung von SSL sicherstellen, dass die Informationen der Kunden sicher sind.

Sie können außerdem Endpunkte hinzufügen, die intern oder extern verwendet werden können. Externe Endpunkten werden Eingabeendpunkte genannt. Ein Eingabeendpunkt ermöglicht einen anderen Zugriffspunkt für Benutzer Ihres Clouddiensts. Wenn Sie einen WCF-Dienst haben, möchten Sie möglicherweise einen internen Endpunkt für eine Webrolle verfügbar machen, den sie zum Zugreifen auf diesen Dienst verwenden soll.

>[AZURE.IMPORTANT] Sie können Endpunkte nur für alle Dienstkonfigurationen aktualisieren.

Wenn Sie HTTPS-Endpunkte hinzufügen, müssen Sie ein SSL-Zertifikat verwenden. Dazu können Sie Ihrer Rolle Zertifikate für alle Dienstkonfigurationen zuordnen und diese für Ihre Endpunkte verwenden.

>[AZURE.IMPORTANT] Diese Zertifikate werden nicht mit dem Dienst paketiert. Sie müssen die Zertifikate über das Azure Platform-Verwaltungsportal gesondert in Azure hochladen.

Alle Verwaltungszertifikate, die Sie den Dienstkonfigurationen zuordnen, gelten nur, wenn der Clouddienst in Azure ausgeführt wird. Wenn der Clouddienst in der lokalen Entwicklungsumgebung ausgeführt wird, wird ein Standardzertifikat verwendet, das vom Azure-Serveremulator verwaltet wird.

### So fügen Sie einer Rolle ein Zertifikat hinzu

1. Wählen Sie die Registerkarte **Zertifikate** aus.

1. Wählen Sie in der Liste **Dienstkonfiguration** den Eintrag **Alle Konfigurationen** aus.

    >[AZURE.NOTE] Sie müssen zum Hinzufügen oder Entfernen von Zertifikaten "Alle Konfigurationen" auswählen. Sie können den Namen und den Fingerabdruck für eine bestimmte Dienstkonfiguration aktualisieren, wenn es erforderlich ist.

1. Um ein Zertifikat für diese Rolle hinzuzufügen, wählen Sie die Schaltfläche **Zertifikat hinzufügen** aus. Der Liste wird ein neuer Eintrag hinzugefügt.

1. Geben Sie im Textfeld **Name** den Namen für das Zertifikat ein.

1. Wählen Sie in der Liste **Speicherort** den Speicherort für das Zertifikat aus, das Sie hinzufügen möchten.

1. Wählen Sie in der Liste **Speichername** den Speicher aus, in dem Sie das Zertifikat auswählen möchten.

1. Um das Zertifikat hinzuzufügen, wählen Sie die Schaltfläche mit den Auslassungspunkten (...). Das Dialogfeld **Windows-Sicherheit** wird angezeigt.

1. Wählen Sie in der Liste das Zertifikat aus, das Sie verwenden möchten, und klicken Sie dann auf die Schaltfläche **OK**.

    >[AZURE.NOTE] Wenn Sie ein Zertifikat aus dem Zertifikatspeicher hinzufügen, werden den Konfigurationseinstellungen automatisch alle Zwischenzertifikate hinzugefügt. Diese Zwischenzertifikate müssen auch in Azure hochgeladen werden, um den Dienst ordnungsgemäß für SSL zu konfigurieren.

1. Wählen Sie zum Löschen eines Zertifikats das Zertifikat aus, und klicken Sie dann auf die Schaltfläche **Zertifikat entfernen**.

1. Wählen Sie auf der Symbolleiste das Symbol **Speichern** aus, um diese Änderungen in den Dienstkonfigurationsdateien zu speichern.

### So verwalten Sie Endpunkte für eine Rolle

1. Wählen Sie die Registerkarte **Endpunkte** aus.

1. Wählen Sie in der Liste **Dienstkonfiguration** den Eintrag **Alle Konfigurationen** aus.

1. Um einen Endpunkt hinzuzufügen, wählen Sie die Schaltfläche **Endpunkt hinzufügen** aus. Der Liste wird ein neuer Eintrag hinzugefügt.

1. Geben Sie im Textfeld **Name** den Namen ein, den Sie für den Endpunkt verwenden möchten.

1. Wählen Sie den Typ des erforderlichen Endpunkts in der Liste **Typ** aus.

1. Wählen Sie das Protokoll für den erforderlichen Endpunkt in der Liste **Protokoll** aus.

1. Wenn es ein Eingabeendpunkt ist, geben Sie im Textfeld **Öffentlicher Port** den zu verwendenden öffentlichen Port ein.

1. Geben Sie im Textfeld **Privater Port** den zu verwendenden privaten Port ein.

1. Wenn der Endpunkt das HTTPS-Protokoll benötigt, wählen Sie in der Liste **SSL-Zertifikatname** das zu verwendende Zertifikat aus.

    >[AZURE.NOTE] Diese Liste enthält die Zertifikate, die Sie für diese Rolle auf der Registerkarte **Zertifikate** hinzugefügt haben.

1. Wählen Sie auf der Symbolleiste die Schaltfläche **Speichern** aus, um diese Änderungen in den Dienstkonfigurationsdateien zu speichern.

## Nächste Schritte
Unter [Konfigurieren eines Azure-Projekts](vs-azure-tools-configuring-an-azure-project.md) erhalten Sie weitere Informationen zu Azure-Projekten in Visual Studio. Informationen zum Clouddienstschema finden Sie unter [Schemareferenz](https://msdn.microsoft.com/library/azure/dd179398).

<!---HONumber=AcomDC_0224_2016-->