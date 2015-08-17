<properties
   pageTitle="C#-API-App"
   description="C#-API-App"
   services="app-service\logic"
   documentationCenter=".net"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="07/16/2015"
   ms.author="jehollan"/>

#C#-API-App
Die C#-API-App bietet Ihnen eine gute Möglichkeit, einfache C#-Ausdrücke auszuführen, *während Ihre Logik-App ausgeführt wird*.

##Wann sollten Sie diese API-App verwenden?
Das Hauptszenario für diese API-App ist, wenn der Lebenszyklus des Codes, den Sie schreiben, mit der Logik-App identisch sein soll und der Code in *keinem*anderen Szenario aufgerufen werden soll.

Wenn Sie jedoch einen wiederverwendbaren Codeausschnitt erstellen möchten, der einen von der Logik-App unabhängigen Lebenszyklus aufweist, sollten Sie die WebJobs-API-App verwenden, um einfache Codeausdrücke zu schreiben und über Ihre Logik-App aufzurufen.

Wenn Sie zudem zusätzliche Pakete einschließen möchten, müssten Sie die Assembly (DLL) als eine mit Base64 codierte binäre Zeichenfolge an den Connector übergeben (wie die Ausgabe aus dem Blobspeicher). Ist mehr Flexibilität für Pakete und Assemblys gewünscht, wäre WebJob wahrscheinlich die bessere Option.

Verwenden Sie die [JavaScript-API-App](app-service-logic-javascript-api.md), wenn Sie Ausdrücke lieber in JS schreiben möchten.

##Erstellen einer C#-API-App
Um die C#-API-App verwenden zu können, müssen Sie zunächst eine Instanz in der C#-API-App erstellen. Dies kann entweder inline beim Erstellen einer Logik-App oder durch Auswählen der C#-API-App aus dem Azure Marketplace erfolgen.

##Verwenden der C#-API-App auf der Designeroberfläche von Logik-Apps
###Trigger
Sie können einen Trigger erstellen, der den Logik-App-Dienst abruft (in einem von Ihnen definierten Intervall). Wenn nicht `false` zurückgegeben wird, wird die Logik-App ausgeführt, andernfalls wartet sie bis zur Überprüfung im nächsten Abrufintervall.

Die Eingaben für den Trigger sind: – **C#-Ausdruck** – Ein Ausdruck, der ausgewertet wird. Er wird in einer Funktion aufgerufen und muss `false` zurückgeben, wenn die Logik-App nicht ausgeführt werden soll. Er kann beliebige Werte zurückgeben, wenn die Logik-App ausgeführt werden soll. Sie können den Inhalt der Antwort in Aktionen der Logik-App verwenden.

Angenommen, Sie verfügen über einen einfachen Trigger, der die Logik-App nur zwischen :15 und :30 einer Stunde ausführt:

```
var d = new DateTime.Now; return (d.Minute > 15) && (d.Minute < 30);
```

###Aktion

Ebenso können Sie eine auszuführende Aktion bereitstellen.

Die Eingaben für die Aktion sind: – **C#-Ausdruck** – Ein Ausdruck, der ausgewertet wird. Sie müssen die `return`-Anweisung aufnehmen, um Inhalte abzurufen. – **Kontextobjekt(e)** – Ein optionales Kontextobjekt, das an den Trigger übergeben werden kann. Sie können beliebig viele Eigenschaften definieren, aber die Basis muss ein JObject `{ ... }` sein. Auf Objekte kann im Skript über den Schlüsselnamen verwiesen werden (der Wert wird als JToken, das dem Namen entspricht, übergeben). – **Bibliotheken** – Ein optionales Array von DLL-Dateien, die zum Kompilieren des Skripts aufgenommen werden. Das Array verwendet die folgende Struktur und funktioniert am besten neben einem Blobspeicherconnector mit der DLL-Datei als Ausgabe:

```javascript
[{"filename": "name.dll", "assembly": {Base64StringFromConnector}, "usingstatment": "using Library.Reference;"}]
```

Angenommen, Sie verwenden den Office 365-Trigger **Neue E-Mail**. Damit wird das folgende Objekt zurückgegeben:

```javascript
{
	...
	"Attachments" : [
		{
			"name" : "Picture.png",
			"content" : {
				"ContentData" : "iVBORw0KGgoAAAANSUhEUgAAAAUAAAAFAQMAAAC3obSmAAAABGdBTUEAALGPC/xhBQAAAAFzUkdCAK7OHOkAAAAGUExURf///wAAAFXC034AAAASSURBVAjXY2BgCGBgYOhgKAAABEIBSWDJEbYAAAAASUVORK5CYII=",
				"ContentType" : "image/png",
				"ContentTransferEncoding" : "Base64"
			}
		},	
		{
			"name" : "File.txt",
			"content" : {
				"ContentData" : "Don't worry, be happy!",
				"ContentType" : "text/plain",
				"ContentTransferEncoding" : "None"
			}
		}	
	]
}
```

Aber diese Anlagen sollen in einen Yammer-Beitrag hochgeladen werden. Leider ist das Schema für Yammer-Anlagen geringfügig anders. Dies kann nun innerhalb Ihrer Logik-App analysiert werden. Übergeben Sie für das Kontextobjekt: `@triggerBody()`, und übergeben Sie als Ausdruck:

```javascript
JArray YammerAttachments = new JObject();
foreach(var obj in (JArray)Attachments)
{
	JObject att = new JObject();
	att["Content"] = obj["content"];
	att["FileName"] = obj["Name"];
	YammerAttachments.Add(att);	
}
return YammerAttachments;
```

Die Aktion gibt das Objekt zurück, das Sie von Ihrer Funktion in einem Ergebnisobjekt zurückgegeben haben. So können Sie in der Yammer-API-App auf `@body('csapi').results` für die **Attachments**-Eigenschaft verweisen.

## Mehr mit Ihrem Connector machen
Nachdem der Connector nun erstellt ist, können Sie ihn mit Logik-App in einem Geschäftsworkflow hinzufügen. Informationen finden Sie unter [Was sind Logik-Apps?](app-service-logic-what-are-logic-apps.md).

Sie können auch Leistungsstatistiken überprüfen und die Sicherheit zum Connector steuern. Informationen finden Sie unter [API-Apps und Connector verwalten und überwachen](../app-service-api/app-service-api-manage-in-portal.md).

<!--References -->

<!--Links -->
[Creating a Logic App]: app-service-logic-create-a-logic-app.md

<!---HONumber=August15_HO6-->