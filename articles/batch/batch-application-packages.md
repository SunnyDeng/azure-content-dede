<properties
	pageTitle="Einfache Anwendungsinstallation und -verwaltung in Azure Batch | Microsoft Azure"
	description="Verwenden Sie das Feature „Anwendungspakete“ von Azure Batch zur einfachen Verwaltung mehrerer Anwendungen und Versionen für die Installation auf Batch-Serverknoten."
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor="" />

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="03/14/2016"
	ms.author="marsma" />

# Anwendungsbereitstellung mit Azure Batch-Anwendungspaketen

Das Feature „Anwendungspakete“ von Azure Batch ermöglicht eine einfache Verwaltung und Bereitstellung von Anwendungen für die in Ihrem Pool benötigten Serverknoten. Mit Anwendungspaketen können Sie mehrere Versionen der von Ihren Aufgaben ausgeführten Anwendungen, einschließlich der Binärdaten und Unterstützungsdateien, hochladen und verwalten und daraufhin eine oder mehrere dieser Anwendungen für die Serverknoten in Ihrem Pool bereitstellen.

In diesem Artikel erfahren Sie, wie Sie Anwendungspakete mit dem Azure-Portal hochladen und verwalten und dann auf dem Serverknoten eines Pools mit der [Batch .NET][api_net]-Bibliothek installieren.

> [AZURE.NOTE] Das hier beschriebene Feature „Anwendungspakete“ ersetzt das Feature „Batch-Apps“, das in früheren Versionen des Diensts verfügbar war.

## Anforderungen für Anwendungspakete

Das in diesem Artikel beschriebene Feature „Anwendungspakete“ ist *nur* mit Batch-Pools kompatibel, die nach dem 10. März 2016 erstellt wurden. Anwendungspakete werden nicht für Serverknoten bereitgestellt, die sich in Pools befinden, die vor diesem Datum erstellt wurden.

Dieses Feature wurde in [Batch-REST-API][api_rest]-Version 2015-12-01.2.2 eingeführt, und die entsprechende [Batch .NET][api_net]-Bibliotheksversion ist 3.1.0. Sie sollten bei der Arbeit mit Batch immer die neueste Version der API verwenden.

## Informationen zu Anwendungen und Anwendungspaketen

In Azure Batch ist eine **Anwendung** ein Satz von Binärdateien mit Versionsangabe, die automatisch auf den Serverknoten in Ihrem Pool heruntergeladen werden können. Ein **Anwendungspaket** ist ein *bestimmter Satz* dieser Binärdateien und stellt eine bestimmte *Version* der Anwendung dar.

![Übersichtsdiagramm zu Anwendungen und Anwendungspaketen][1]

### Anwendungen

Eine Anwendung in Batch enthält ein oder mehrere Anwendungspakete. Sie legt die Konfigurationsoptionen für die Anwendung fest, z. B. die Standardversion des Anwendungspakets, die auf Serverknoten installiert werden soll, und gibt an, ob ihre Pakete aktualisiert oder gelöscht werden können.

### Anwendungspakete

Ein Anwendungspaket ist eine ZIP-Datei, die die Binärdateien der Anwendung und die unterstützenden Dateien enthält, die für die Ausführung Ihrer Aufgaben erforderlich sind. Jedes Anwendungspaket stellt eine bestimmte Version der Anwendung dar. Wenn Sie einen Pool im Batch-Dienst erstellen, können Sie eine oder mehrere dieser Anwendungen und (optional) eine Version angeben, und diese Anwendungspakete werden automatisch heruntergeladen und auf jedem Knoten extrahiert werden, wenn er dem Pool beitritt.

> [AZURE.IMPORTANT] Es gibt Einschränkungen für die Anzahl der Anwendungen und Anwendungspakete in einem Batch-Konto sowie bezüglich der maximalen Anwendungspaketgröße. Unter [Kontingente und Limits für den Azure Batch-Dienst](batch-quota-limit.md) finden Sie weitere Informationen zu diesen Einschränkungen.

### Vorteile von Anwendungspaketen

Anwendungspakete können sowohl den Code in Ihrer Batch-Lösung vereinfachen als auch den erforderlichen Aufwand bei der Verwaltung der Programme senken, die Ihre Aufgaben ausführen.

Mit Anwendungspaketen muss die Startaufgabe Ihres Pools keine lange Liste einzelner, auf den Knoten zu installierender Ressourcendateien angeben. Sie müssen nicht manuell mehrere Versionen dieser Dateien in Azure Storage oder auf Ihren Knoten verwalten. Und Sie müssen sich keine Gedanken über das Generieren von [SAS-URLs](../storage/storage-dotnet-shared-access-signature-part-1.md) für den Zugriff auf die Dateien in Ihrem Azure Storage-Konto machen.

Batch kümmert sich im Hintergrund um die Details für die Arbeit mit Azure Storage, um Anwendungspakete zu speichern und für Serverknoten bereitzustellen, damit sowohl Ihr Code als auch der Verwaltungsaufwand vereinfacht wird.

## Hochladen und Verwalten von Anwendungen

Mit dem Azure-Portal können Sie Anwendungspakete hinzufügen, aktualisieren und löschen sowie Standardversionen für jede Anwendung konfigurieren. Zu diesem Zeitpunkt werden diese Vorgänge nur im Azure-Portal unterstützt.

In den nächsten Abschnitten behandeln wir zuerst die Verknüpfung eines Storage-Kontos mit Ihrem Batch-Konto und überprüfen dann, welche Paketverwaltungsfunktionen im Azure-Portal verfügbar sind. Danach erfahren Sie, wie Sie diese Pakete mit der [Batch .NET][api_net]-Bibliothek für Serverknoten bereitstellen.

### Verknüpfen eines Storage-Kontos

Um Anwendungspakete zu verwenden, müssen Sie zuerst ein Azure Storage-Konto mit Ihrem Batch-Konto verknüpfen. Wenn Sie noch kein Storage-Konto für Ihr Batch-Konto konfiguriert haben, zeigt das Azure-Portal eine Warnung an, wenn Sie zum ersten Mal auf dem Batch-Kontoblatt auf die Kachel *Anwendungen* klicken.

![Warnung im Azure-Portal, dass kein Storage-Konto konfiguriert ist][9]

Der Batch-Dienst verwendet das zugeordnete Storage-Konto für Speicherung und Abruf von Anwendungspaketen. Nachdem Sie die beiden Konten verknüpft haben, kann Batch die im verknüpften Storage-Konto gespeicherten Pakete automatisch für Ihre Serverknoten bereitstellen. Klicken Sie auf dem Blatt *Warnung* auf **Speicherkontoeinstellungen**, dann auf dem Blatt *Speicherkonto* auf **Speicherkonto**, um ein vorhandenes Storage-Konto mit Ihrem Batch-Konto zu verknüpfen.

![Auswählen des Storage-Kontoblatts im Azure-Portal][10]

Sie können ein vorhandenes Storage-Konto in Ihrem Abonnement verwenden oder ein neues für Anwendungspaketspeicher erstellen. Weitere Informationen zum Erstellen eines Storage-Kontos finden Sie unter „Erstellen Sie ein Speicherkonto“ in [Informationen zu Azure-Speicherkonten](../storage/storage-create-storage-account.md). Wenn Sie ein Storage-Konto erstellt haben, können Sie es anschließend mithilfe des Blatts *Speicherkonto* mit dem Batch-Konto verknüpfen.

> [AZURE.WARNING] Da Batch Ihre Anwendungspakete mit Azure Storage speichert, werden Ihnen für die Blockblobdaten [normale Gebühren][storage_pricing] in Rechnung gestellt. Beachten Sie unbedingt Größe und Anzahl der Anwendungspakete, und entfernen Sie in regelmäßigen Abständen veraltete Pakete, um Kosten zu minimieren.

### Anzeigen aktueller Anwendungen

Um die Anwendungen in Ihrem Batch-Konto anzuzeigen, klicken Sie auf dem Batch-Kontoblatt auf die Kachel **Anwendungen**.

![Kachel „Anwendungen“][2]

Daraufhin wird das Blatt *Anwendungen* geöffnet:

![Auflisten von Anwendungen][3]

Das Blatt *Anwendungen* zeigt sowohl die ID jeder Anwendung in Ihrem Konto als auch die folgenden Eigenschaften an:

* **Pakete** – Die Anzahl der Versionen, die dieser Anwendung zugeordnet sind.
* **Standardversion** – Wenn Sie keine Version angeben, wenn Sie die Anwendung für einen Pool festlegen, wird diese Version installiert. Diese Einstellung ist optional.
* **Updates zulassen** – Wenn dieser Wert auf *Nein* gesetzt ist, sind Paketupdates und -löschungen für die Anwendung deaktiviert – nur neue Anwendungspaketversionen können hinzugefügt werden. Der Standardwert ist *Ja*.

### Anzeigen von Anwendungsdetails

Bei Klicken auf eine Anwendung auf dem Blatt *Anwendungen* wird das Detailsblatt für die Anwendung angezeigt.

![Anwendungsdetails][4]

Im Detailsblatt für die Anwendung können Sie die folgenden Einstellungen für Ihre Anwendung konfigurieren.

* **Updates zulassen** – Geben Sie an, ob ihre Anwendungspakete aktualisiert oder gelöscht werden können (siehe „Aktualisieren oder Löschen eines Anwendungspakets“ im Folgenden).
* **Standardversion** – Geben Sie ein Standardanwendungspaket an, das für Serverknoten bereitgestellt wird.
* **Anzeigename** – Dies ist ein Anzeigename, den Ihre Batch-Lösung zur Anzeige von Informationen über die Anwendung verwenden kann, z. B. in der Benutzeroberfläche eines Diensts, den Sie Ihren Kunden über Batch anbieten.

### Hinzufügen einer neuen Anwendung

Um eine neue Anwendung zu erstellen, fügen Sie ein Anwendungspaket mithilfe einer neuen, eindeutigen ID hinzu. Das erste Anwendungspaket, das Sie mithilfe der neuen Anwendungs-ID hinzufügen, erstellt auch die neue Anwendung.

Klicken Sie auf dem Blatt *Anwendungen* auf **Hinzufügen**, um das Blatt *Neue Anwendung* zu öffnen.

![Blatt „Neue Anwendung“ im Azure-Portal][5]

Das Blatt *Neue Anwendung* umfasst die folgenden Felder zur Angabe der Einstellungen der neuen Anwendung und des neuen Anwendungspakets.

**Metadaten**

Sie können die Metadaten für die Anwendung entweder manuell durch direkte Eingabe von Werten in die Textfelder **Anwendungs-ID** und **Version** angeben oder eine JSON-Datei mit diesen Metadaten hochladen. Um ID und Version der Anwendung manuell anzugeben, lassen Sie einfach die Dropdownlistenauswahl **Metadaten** auf **Metadaten eingeben** (Standard), und geben Sie manuell die Werte in die Textfelder **Anwendungs-ID** und **Version** ein.

Wählen Sie zum Angeben einer JSON-formatierten Metadatendatei mit ID und Version für ein Paket **Metadatendatei hochladen** aus der Dropdownliste **Metadaten**:

![Hochladen der Metadatendatei-Dropdownlistenauswahl][6]

Anschließend klicken Sie auf das Ordnersymbol neben dem angezeigten Textfeld **Metadatendatei**, und navigieren Sie zu der lokalen Datei mit den JSON-Daten. In diesem Beispiel wurde die Datei `litware_1.1001.2b.json` zum Hochladen ausgewählt, und die Textfelder **Anwendungs-ID** und **Version** wurden automatisch mit den Informationen in der Datei gefüllt:

![Metadatendatei-Auswahldetail][13]

Verwenden Sie das folgende JSON-Format, um die Anwendungspaket-Metadaten in einer Datei anzugeben:

```
{
    "id": "litware",
    "version": "1.1001.2b"
}
```

> [AZURE.NOTE] Wenn Sie eine JSON-Metadatendatei für ID und Version hochladen, müssen Sie *nicht* auch die Textfelder „Anwendungs-ID“ oder „Version“ bearbeiten – sie werden automatisch mit den Daten in der JSON-Datei gefüllt.

**Anwendungs-ID**

Dies gibt die ID Ihrer neuen Anwendung an, die den standardmäßigen Gültigkeitsprüfungsregeln von Azure Batch-IDs unterliegt:

* Sie kann eine beliebige Kombination von alphanumerischen Zeichen einschließlich Bindestrichen und Unterstrichen enthalten.
* Sie darf nicht mehr als 64 Zeichen enthalten.
* Sie muss innerhalb des Batch-Kontos eindeutig sein.
* Groß-/Kleinschreibung wird beibehalten und nicht unterschieden.

**Version**

Gibt die Version des Anwendungspakets an, das Sie hochladen. Für die Versionszeichenfolge gelten die folgenden Gültigkeitsprüfungsregeln:

* Sie kann eine beliebige Kombination von alphanumerischen Zeichen einschließlich Bindestrichen, Unterstrichen und Punkten enthalten.
* Sie darf nicht mehr als 64 Zeichen enthalten.
* Sie muss innerhalb der Anwendung eindeutig sein.
* Groß-/Kleinschreibung wird beibehalten und nicht unterschieden.

**Anwendungspaket**

Dies gibt die ZIP-Datei an, die die Binärdateien der Anwendung und jegliche unterstützenden Dateien enthält, die für die Ausführung der Anwendung erforderlich sind. Klicken Sie auf das Textfeld **Datei auswählen** oder das Ordnersymbol, um dahin zu navigieren und eine ZIP-Datei mit den Dateien der Anwendung auszuwählen.

Nachdem Sie eine Datei ausgewählt haben, klicken Sie auf **OK**, um das Hochladen in Azure Storage zu beginnen. Wenn das Hochladen abgeschlossen ist, werden Sie benachrichtigt, und das Blatt wird geschlossen. Beachten Sie, dass dieser Vorgang abhängig von der Größe der Datei, die Sie hochladen, und der Geschwindigkeit Ihrer Netzwerkverbindung einige Zeit dauern kann.

> [AZURE.WARNING] Schließen Sie das Blatt *Neue Anwendung* nicht, bevor das Hochladen abgeschlossen ist. Dies würde das Hochladen abbrechen.

### Hinzufügen eines neuen Anwendungspakets

Um eine neue Anwendungspaketversion für eine vorhandene Anwendung hinzuzufügen, wählen Sie eine Anwendung auf dem Blatt *Anwendungen* aus, klicken Sie auf **Pakete** und dann auf **Hinzufügen**, um das Blatt *Paket hinzufügen* anzuzeigen.

![Hinzufügen des Anwendungspaketblatts im Azure-Portal][8]

Wie Sie sehen können, entsprechen die Felder mit Ausnahme des deaktivierten Textfelds „Anwendungs-ID“ denen des Blatts *Neue Anwendung*. Geben Sie wie oben die **Version** für das neue Paket ein, navigieren Sie zu Ihrer **Anwendungspaket**-ZIP-Datei, und klicken Sie dann zum Hochladen des Pakets auf **OK**.

### Aktualisieren oder Löschen eines Anwendungspakets

Öffnen Sie zum Aktualisieren oder Löschen eines vorhandenen Anwendungspakets das Detailsblatt für die Anwendung, klicken Sie auf **Pakete**, um das Blatt *Pakete* anzuzeigen, klicken Sie auf die **Auslassungspunkte** in der Zeile des Anwendungspakets, das Sie ändern möchten, und wählen Sie die Aktion, die Sie ausführen möchten.

![Aktualisieren oder Löschen des Pakets im Azure-Portal][7]

**Aktualisieren**

Beim Klicken auf **Aktualisieren** wird das Blatt *Paket aktualisieren* angezeigt. Dieses Blatt ist vergleichbar mit dem Blatt *Neues Anwendungspaket*, doch nur das Paketauswahlfeld ist aktiviert, sodass Sie eine neue ZIP-Datei zum Hochladen angeben können.

![Aktualisieren des Paketblatts im Azure-Portal][11]

**Löschen**

Beim Klicken auf **Löschen** werden Sie aufgefordert, die Löschung der Paketversion zu bestätigen, und Batch löscht das Paket aus Azure Storage. Wenn Sie die Standardversion einer Anwendung löschen, wird die Standardversionseinstellung für die Anwendung entfernt.

![Löschen der Anwendung][12]

## Installieren von Anwendungen auf Serverknoten

Da wir nun das Hochladen und Verwalten von Anwendungspaketen mithilfe des Azure-Portals behandelt haben, können wir jetzt deren eigentliche Bereitstellung für Serverknoten und ihre Ausführung mit Batch-Aufgaben erörtern.

Um ein Anwendungspaket auf den Serverknoten in einem Pool zu installieren, geben Sie einen oder mehrere Anwendungspaket*verweise* für den Pool an. In Batch .NET fügen Sie hierzu einen oder mehrere [CloudPool][net_cloudpool].[ApplicationPackageReferences][net_cloudpool_pkgref] hinzu, wenn Sie den Pool erstellen, oder einem vorhandenen Pool hinzu.

Die [ApplicationPackageReference][net_pkgref]-Klasse gibt eine Anwendungs-ID und Version zum Installieren auf den Serverknoten eines Pools an.

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(poolId: "myPool",
                                          osFamily: "4",
                                          virtualMachineSize: "small",
                                          targetDedicated: "1");

// Specify the application and version to install on the compute nodes
myCloudPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "litware",
        Version = "1.1001.2b" }
};

// Commit the pool so that it's created in the Batch service. As the nodes join
// the pool, the specified application package will be installed on each.
await myCloudPool.CommitAsync();
```

## Ausführen der installierten Anwendungen

Da jeder Serverknoten einem Pool beitritt (oder neu gestartet bzw. ein Reimaging an ihm durchgeführt wird) werden die Pakete, die Sie angegeben haben, heruntergeladen und in ein benanntes Verzeichnis innerhalb von `AZ_BATCH_ROOT_DIR` auf dem Knoten extrahiert. Batch erstellt auch eine Umgebungsvariable für Ihre Aufgabenbefehlszeilen, die Sie beim Aufrufen der Binärdateien der Anwendung verwenden können – diese Variable entspricht folgendem Benennungsschema:

`AZ_BATCH_APP_PACKAGE_appid#version`

Wenn Sie beispielsweise angeben, dass Version 2.7 der Anwendung *blender* installiert werden soll, können Ihre Aufgaben über die folgende Umgebungsvariable in ihren Befehlszeilen auf die Binärdateien zugreifen:

`AZ_BATCH_APP_PACKAGE_BLENDER#2.7`

Wenn Ihre Anwendung eine Standardversion angibt, können Sie ohne Versionszeichenfolgen-Suffix auf die Umgebungsvariable zugreifen. Wenn Sie beispielsweise die Standardversion 2.7 für die Anwendung *Blender* im Azure-Portal angegeben hatten, können Ihre Aufgaben auf folgende Umgebungsvariable zugreifen:

`AZ_BATCH_APP_PACKAGE_BLENDER`

Der folgende Codeausschnitt zeigt, wie eine Aufgabe konfiguriert werden kann, wenn eine Standardversion für die Anwendung *blender* angegeben wurde.

```csharp
string taskId = "blendertask01";
string commandLine = @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -my -command -args";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [AZURE.TIP] Im Artikel [Übersicht über Azure Batch-Features](batch-api-basics.md) finden Sie unter „Umgebungseinstellungen für Aufgaben“ weitere Informationen zu Umgebungseinstellungen für Serverknoten.

## Aktualisieren der Anwendungspakete eines Pools

Wenn ein vorhandener Pool bereits mit einem Anwendungspaket konfiguriert wurde, können Sie ein neues Paket für den Pool angeben. Alle neuen Knoten, die dem Pool beitreten, installieren das neu angegebene Paket, wie jeder vorhandene Knoten, der neu gestartet bzw. an dem ein Reimaging durchgeführt wird. Serverknoten, die sich bereits im Pool befinden, wenn Sie die Paketverweise aktualisieren, werden nicht automatisch im neuen Anwendungspaket installiert.

In diesem Beispiel hat der vorhandene Pool Version 2.7 der Anwendung *blender* als eine seiner [CloudPool][net_cloudpool].[ApplicationPackageReferences][net_cloudpool_pkgref] konfiguriert. Um die Knoten des Pools mit Version 2.76b zu aktualisieren, geben Sie eine neue [ApplicationPackageReference][net_pkgref] mit der neuen Version ein, und committen Sie die Änderung.

```csharp
string newVersion = "2.76b";
CloudPool boundPool = await batchClient.PoolOperations.GetPoolAsync("myPool");
boundPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "blender",
        Version = newVersion }
};
await boundPool.CommitAsync();
```

Jetzt ist die neue Version konfiguriert, sodass jedem *neuen* Knoten, der dem Pool beitritt, Version 2.76b bereitgestellt wird. Um 2.76b auf den Knoten zu installieren, die sich *bereits* im Pool befinden, starten Sie sie neu (bzw. führen Sie ein Reimaging an ihnen durch) werden. Beachten Sie, dass neu gestartete Knoten die Dateien aus vorherigen Paketbereitstellungen beibehalten.

## Auflisten der Anwendungen in einem Batch-Konto

Sie können die Anwendungen und ihre Pakete mit der Methode [ApplicationOperations][net_appops].[ListApplicationSummaries][net_appops_listappsummaries] in einem Batch-Konto auflisten.

```csharp
// List the applications and their application packages in the Batch account.
List<ApplicationSummary> applications = await batchClient.ApplicationOperations.ListApplicationSummaries().ToListAsync();
foreach (ApplicationSummary app in applications)
{
    Console.WriteLine("ID: {0} | Display Name: {1}", app.Id, app.DisplayName);

    foreach (string version in app.Versions)
    {
        Console.WriteLine("  {0}", version);
    }
}
```

## Zusammenfassung

Mit Anwendungspaketen können Sie Ihren Kunden viel einfacher die Möglichkeit bieten, Anwendungen für ihre Aufträge auszuwählen, und genau die zu verwendende Version anzugeben, wenn sie Aufträge mit Ihrem Batch-fähigen Dienst verarbeiten. Sie könnten Ihren Kunden auch die Möglichkeit bieten, ihre eigenen Anwendungen in Ihren Dienst hochzuladen und dort zu verfolgen.

## Nächste Schritte

* Die [Batch-REST-API][api_rest] unterstützt auch die Arbeit mit Anwendungspaketen. Beachten Sie beispielsweise zum Angeben von Paketen für die Installation mithilfe der REST-API das Element [applicationPackageReferences][rest_add_pool_with_packages] in [Hinzufügen eines Pools mit einem Konto][rest_add_pool]. Unter [Applications][rest_applications] (Anwendungen) finden Sie ausführliche Informationen zum Abrufen von Anwendungsinformationen mit der Batch-REST-API.

* Erfahren Sie, wie Sie programmgesteuert [Azure Batch-Konten und -Kontingente mit Batch Management .NET](batch-management-dotnet.md) verwalten. Die [Batch Management .NET][api_net_mgmt]-Bibliothek kann Funktionen zum Erstellen und Löschen von Konten für Ihre Batch-Anwendung oder Ihren Dienst aktivieren.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[storage_pricing]: https://azure.microsoft.com/pricing/details/storage/
[net_appops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.aspx
[net_appops_listappsummaries]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.listapplicationsummaries.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.applicationpackagereferences.aspx
[net_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationpackagereference.aspx
[rest_applications]: https://msdn.microsoft.com/library/azure/mt643945.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_pool_with_packages]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_apkgreference

[1]: ./media/batch-application-packages/app_pkg_01.png "Übersichtsdiagramm für Anwendungspakete"
[2]: ./media/batch-application-packages/app_pkg_02.png "Kachel „Anwendungen“ im Azure-Portal"
[3]: ./media/batch-application-packages/app_pkg_03.png "Blatt „Anwendungen“ im Azure-Portal"
[4]: ./media/batch-application-packages/app_pkg_04.png "Blatt „Anwendungsdetails“ im Azure-Portal"
[5]: ./media/batch-application-packages/app_pkg_05.png "Blatt „Neue Anwendung“ im Azure-Portal"
[6]: ./media/batch-application-packages/app_pkg_06.png "Hochladen der Metadatendatei-Dropdownlistenauswahl"
[7]: ./media/batch-application-packages/app_pkg_07.png "Aktualisieren oder Löschen der Paketedropdownliste im Azure-Portal"
[8]: ./media/batch-application-packages/app_pkg_08.png "Blatt „Neues Anwendungspaket“ im Azure-Portal"
[9]: ./media/batch-application-packages/app_pkg_09.png "Warnung, dass kein verknüpftes Storage-Konto vorhanden ist"
[10]: ./media/batch-application-packages/app_pkg_10.png "Auswählen des Storage-Kontoblatts im Azure-Portal"
[11]: ./media/batch-application-packages/app_pkg_11.png "Aktualisieren des Paketblatts im Azure-Portal"
[12]: ./media/batch-application-packages/app_pkg_12.png "Dialogfeld zum Bestätigen der Paketlöschung im Azure-Portal"
[13]: ./media/batch-application-packages/app_pkg_13.png "Metadatendatei-Auswahldetail"

<!---HONumber=AcomDC_0316_2016-->