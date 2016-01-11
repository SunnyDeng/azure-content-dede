<properties
	pageTitle="Verwenden von REST zum Sichern und Wiederherstellen von App Service-Apps"
	description="Sie erfahren, wie Sie RESTful-API-Aufrufe zum Sichern und Wiederherstellen einer App in Azure App Service verwenden."
	services="app-service"
	documentationCenter=""
	authors="nking92"
	manager="edlauare"
    editor="" />

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/18/2015"
	ms.author="nicking"/>

# Verwenden von REST zum Sichern und Wiederherstellen von App Service-Apps
[App Service-Apps](https://azure.microsoft.com/services/app-service/web/) können als Blobs in Azure Storage gesichert werden. Das Backup kann auch die Datenbanken der App enthalten. Falls die App versehentlich gelöscht wird oder falls die App auf eine vorherige Version zurückgesetzt werden muss, ist die Wiederherstellung aus einem früheren Backup möglich. Sicherungen können jederzeit bedarfsgesteuert durchgeführt oder in geeigneten Abständen geplant werden.

In diesem Artikel wird erläutert, wie Sie eine App mit RESTful-API-Anforderungen sichern und wiederherstellen. Wenn Sie App-Backups grafisch über das Azure-Portal erstellen und verwalten möchten, helfen Ihnen die Informationen unter [Sichern von Web-Apps in Azure App Service](web-sites-backup.md) weiter.

<a name="gettingstarted"></a>
## Erste Schritte
Um REST-Anforderungen senden zu können, müssen Sie **Name**, **Ressourcengruppe** und **Abonnement-ID** Ihrer App kennen. Sie erhalten diese Informationen, indem Sie im [Azure-Portal](https://portal.azure.com) auf dem Blatt **App Service** auf Ihre App klicken. Für die Beispiele in diesem Artikel konfigurieren wir die Website `backuprestoreapiexamples.azurewebsites.net`. Sie ist in der Ressourcengruppe „Default-Web-WestUS“ gespeichert und wird unter einem Abonnement mit der ID 00001111-2222-3333-4444-555566667777 ausgeführt.

![Beispielwebsite-Informationen][SampleWebsiteInformation]

<a name="backup-restore-rest-api"></a>
## Sichern und Wiederherstellen einer REST-API
Es folgen mehrere Beispiele dafür, wie Sie die REST-API zum Sichern und Wiederherstellen einer App verwenden. Jedes Beispiel enthält eine URL und einen HTTP-Anforderungstext. Die Beispiel-URL enthält Platzhalter, die in geschweiften Klammern stehen, z. B. {subscriptionId}. Ersetzen Sie diese Platzhalter durch die entsprechenden Informationen für Ihre App. Zu Referenzzwecken werden hier kurz die Platzhalter beschrieben, die in den Beispiel-URLs verwendet werden.

* subscriptionId – ID des Azure-Abonnements, in dem die App enthalten ist
* resourceGroupName – Name der Ressourcengruppe, in der die App enthalten ist
* sitename – Name der App
* backupId – ID des App-Backups

Die vollständige Dokumentation der API, einschließlich verschiedener optionaler Parameter, die in der HTTP-Anforderung enthalten sein können, finden Sie unter [Azure-Ressourcen-Explorer](https://resources.azure.com/).

<a name="backup-on-demand"></a>
## Bedarfsgesteuertes Sichern einer App
Wenn Sie eine App sofort sichern möchten, senden Sie eine **POST**-Anforderung an `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{sitename}/backup/`.

Hier ist dargestellt, wie die URL für unsere Beispielwebsite aussieht. `https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backup/`

Sie müssen im Text der Anforderung ein JSON-Objekt bereitstellen, um anzugeben, welches Speicherkonto zum Speichern des Backups verwendet werden soll. Das JSON-Objekt muss eine Eigenschaft mit dem Namen **storageAccountUrl** aufweisen, die über eine [SAS-URL](../storage/storage-dotnet-shared-access-signature-part-1.md) mit Gewährung des Schreibzugriffs auf den Azure Storage-Container verfügt, in dem das Backup-Blob enthalten sein soll. Wenn Sie Ihre Datenbanken sichern möchten, müssen Sie auch eine Liste mit den Namen, Typen und Verbindungszeichenfolgen der zu sichernden Datenbanken angeben.

```
{
    "properties":
    {
        "storageAccountUrl": “https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl”,
        “databases”: [
            {
                “databaseType”: “SqlAzure”,
                “name”: “MyDatabase1”,
                "connectionString": "<your database connection string>"
            }
        ]
    }
}
```

Ein Backup der App beginnt sofort, nachdem die Anforderung empfangen wurde. Der Backupprozess kann unter Umständen sehr lange dauern. Die HTTP-Antwort enthält eine ID, die Sie in einer anderen Anforderung verwenden können, um den Status des Backups anzuzeigen. Dies ist ein Beispiel für den Text der HTTP-Antwort auf die Backupanforderung.

```
{
    "id": "/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples",
    "name": " backuprestoreapiexamples ",
    "type": "Microsoft.Web/sites",
    "location": "WestUS",
    "properties":    {
        "id": 1,
        "storageAccountUrl": “https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl”,
        "blobName": “backup_201509291825.zip”,
        "name": "backup_201509291825",
        "status": 4,
        "sizeInBytes": 0,
        "created": "2015-09-29T18:25:26.3992707Z",
        "log": null,
        "databases": [
            {
                "databaseType": "SqlAzure",
                "name": "MyDatabase1",
                "connectionString": "<your database connection string>"
            }
        ],
        "scheduled": false,
        "correlationId": "ea730f4d-dd06-4f7f-a090-9aa09k662h36",
    }
}
```

>[AZURE.NOTE]Die Fehlermeldungen finden Sie in der log-Eigenschaft der HTTP-Antwort.

<a name="schedule-automatic-backups"></a>
## Planen von automatischen Backups
Sie können eine App nicht nur bedarfsgesteuert sichern, sondern auch planen, dass ein Backup automatisch durchgeführt wird.

### Einrichten eines neuen Zeitplans für automatische Backups
Um einen Sicherungszeitplan einzurichten, senden Sie eine **PUT**-Anforderung an `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{name}/config/backup`.

Hier ist dargestellt, wie die URL für unsere Beispielwebsite aussieht. `https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/config/backup`

Der Anforderungstext muss ein JSON-Objekt enthalten, mit dem die Konfiguration des Backups angegeben wird. Es folgt ein Beispiel mit allen erforderlichen Parametern.

```
{
    "location": "WestUS",
    "properties": // Represents an app restore request
    {
        "backupSchedule": { // Required for automatically scheduled backups
            "frequencyInterval": "7",
            "frequencyUnit": "Day",
            "keepAtLeastOneBackup": "True",
            "retentionPeriodInDays": "10",
        },
        "enabled": "True", // Must be set to true to enable automatic backups
        "name": “mysitebackup”,
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl"
    }
}
```

Im Beispiel wird die App so konfiguriert, dass alle sieben Tage ein automatisches Backup durchgeführt wird. Mit den Parametern **frequencyInterval** und **frequencyUnit** wird bestimmt, wie häufig die Backups erstellt werden sollen. Gültige Werte für **frequencyUnit** sind **hour** und **day**. Wenn eine App beispielsweise alle zwölf Stunden gesichert werden soll, legen Sie „frequencyInterval“ auf 12 und „frequencyUnit“ auf eine Stunde fest.

Alte Backups werden automatisch aus dem Speicherkonto entfernt. Sie können steuern, wie alt die Backups sein sollen, indem Sie den Parameter **retentionPeriodInDays** festlegen. Wenn unabhängig vom Alter immer mindestens ein Backup gespeichert sein soll, legen Sie **keepAtLeastOneBackup** auf „true“ fest.

### Abrufen des automatischen Sicherungszeitplans
Um die Backupkonfiguration einer App abzurufen, senden Sie eine **POST**-Anforderung an die URL ` https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{name}/config/backup/list`.

Die URL für unsere Beispielwebsite lautet `https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/config/backup/list`.

<a name="get-backup-status"></a>
## Abrufen des Status eines Backups
Je nachdem, wie groß die App ist, kann die Erstellung eines Backups einige Zeit dauern. Für Backups können auch Fehler oder eine Zeitüberschreitung auftreten, oder sie können teilweise erfolgreich sein. Um den Status aller Backups einer App anzuzeigen, senden Sie eine **GET**-Anforderung an die URL `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{name}/backups`.

Um den Status eines bestimmten Backups anzuzeigen, senden Sie eine GET-Anforderung an die URL `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{name}/backups/{backupId}`.

Hier ist dargestellt, wie die URL für unsere Beispielwebsite aussieht. `https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1`

Der Antworttext enthält ein ähnliches JSON-Objekt wie in diesem Beispiel.

```
{
    "properties":  {
        "id": 1,
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl",
        "blobName": "backup_201509291734.zip",
        "name": "backup_201509291734",
        "status": 2,
        "sizeInBytes": 151973,
        "created": "2015-09-29T17:34:57.2091605",
        "scheduled": false,
        "lastRestoreTimeStamp": null,
        "finishedTimeStamp": "2015-09-29T17:35:11.3293602",
        "correlationId": "2379fc13-418a-4b9c-920d-d06e73c5028d",
        "websiteSizeInBytes": 209920
    }
}
```

Der Status eines Backups ist ein enumerierter Typ. Hier sind alle möglichen Status aufgeführt.

* 0 – InProgress: Das Backup wurde gestartet, aber es wurde noch nicht abgeschlossen.
* 1 – Failed: Das Backup war nicht erfolgreich.
* 2 – Succeeded: Das Backup war erfolgreich.
* 3 – TimedOut: Das Backup konnte nicht rechtzeitig abgeschlossen werden und wurde abgebrochen.
* 4 – Created: Die Backupanforderung wurde in die Warteschlange eingereiht, aber sie wurde noch nicht gestartet.
* 5 – Skipped: Das Backup wurde nicht durchgeführt, weil von einem Zeitplan zu viele Backups ausgelöst wurden.
* 6 – PartiallySucceeded: Das Backup war erfolgreich, aber einige Dateien wurden nicht gesichert, da sie nicht gelesen werden konnten. Dies tritt normalerweise auf, weil die Dateien mit einer exklusiven Sperre belegt wurden.
* 7 – DeleteInProgress: Es wurde angefordert, dass das Backup gelöscht wird, aber es wurde noch nicht gelöscht.
* 8 – DeleteFailed: Das Backup konnte nicht gelöscht werden. Dies kann auftreten, wenn die SAS-URL, die zum Erstellen des Backups verwendet wurde, abgelaufen ist.
* 9 – Deleted: Das Backup wurde erfolgreich gelöscht.

<a name="restore-app"></a>
## Wiederherstellen einer App aus einem Backup
Wenn die App gelöscht wurde oder wenn Sie die App auf eine vorherige Version zurücksetzen möchten, können Sie die App aus einem Backup wiederherstellen. Um eine Wiederherstellung aufzurufen, senden Sie eine **POST**-Anforderung an die URL `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{name}/backups/{id}/restore`.

Hier ist dargestellt, wie die URL für unsere Beispielwebsite aussieht. `https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1/restore`

Senden Sie im Anforderungstext ein JSON-Objekt, das die Eigenschaften für den Wiederherstellungsvorgang enthält. Hier ist ein Beispiel mit allen erforderlichen Eigenschaften angegeben:

```
{
    "location": "WestUS",
    "properties":
    {
        "blobName": "backup_201509280431.zip",
        "databases": [ // Not required unless you’re restoring databases
            {
                “databaseType”: “SqlAzure”,
                “name”: “MyDatabase1”
        }],
        "overwrite": "true",
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl" // SAS URL to storage container containing your website backup
    }
}
```

### Wiederherstellen als neue App
Es kann vorkommen, dass Sie beim Wiederherstellen eines Backups eine neue App erstellen möchten, anstatt eine bereits vorhandene App zu überschreiben. Ändern Sie hierfür die Anforderungs-URL so, dass sie auf die neue App zeigt, die Sie erstellen möchten. Ändern Sie außerdem die **overwrite**-Eigenschaft im JSON-Code in **false**.

<a name="delete-app-backup"></a>
## Löschen eines App-Backups
Wenn Sie ein Backup löschen möchten, senden Sie eine **DELETE**-Anforderung an die URL `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{name}/backups/{backupId}`.

Hier ist dargestellt, wie die URL für unsere Beispielwebsite aussieht. `https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1`

<a name="manage-sas-url"></a>
## Verwalten der SAS-URL eines Backups
Azure App Service versucht, Ihr Backup aus Azure Storage zu löschen. Dazu wird die beim Erstellen des Backups angegebene SAS-URL verwendet. Wenn diese SAS-URL nicht mehr gültig ist, kann das Backup nicht über die REST-API gelöscht werden. Aber Sie können die SAS-URL, die einem Backup zugeordnet ist, aktualisieren, indem Sie eine **POST**-Anforderung an die URL `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{name}/backups/{id}/list` senden.

Hier ist dargestellt, wie die URL für unsere Beispielwebsite aussieht. `https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1/list`

Senden Sie im Anforderungstext ein JSON-Objekt, das die neue SAS-URL enthält. Beispiel:

```
{
    "properties":
    {
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl"
    }
}
```

>[AZURE.NOTE]Aus Sicherheitsgründen wird die einem Backup zugeordnete SAS-URL nicht zurückgegeben, wenn eine GET-Anforderung für ein bestimmtes Backup gesendet wird. Wenn Sie die SAS-URL anzeigen möchten, die einem Backup zugeordnet ist, senden Sie eine POST-Anforderung an die obige URL und fügen einfach ein leeres JSON-Objekt in den Anforderungstext ein. Die Antwort des Servers enthält alle Informationen zum Backup, einschließlich der SAS-URL.

<!-- IMAGES -->
[SampleWebsiteInformation]: ./media/websites-csm-backup/01siteconfig.png

<!---HONumber=AcomDC_1223_2015-->