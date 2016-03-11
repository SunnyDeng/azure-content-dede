<properties
   pageTitle="Verwenden der JavaScript-API-App in einer Logik-App | Microsoft Azure"
   description="JavaScript-API-App oder -Connector"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="stepsic-microsoft-com"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="02/22/2016"
   ms.author="stepsic"/>

#JavaScript-API-App

>[AZURE.NOTE] Diese Version des Artikels gilt für die Logik-Apps-Schemaversion 2014-12-01-preview.

Die JavaScript-API-App bietet Ihnen eine gute Möglichkeit, einfache JavaScript-Ausdrücke auszuführen, *während Ihre Logik-App ausgeführt wird*.

##Wann sollten Sie diese API-App verwenden?
Das Hauptszenario für diese API-App ist, wenn der Lebenszyklus des Codes, den Sie schreiben, mit der Logik-App identisch sein soll und der Code in *keinem* anderen Szenario aufgerufen werden soll.

Wenn Sie jedoch einen wiederverwendbaren Codeausschnitt erstellen möchten, der einen von der Logik-App unabhängigen Lebenszyklus aufweist, sollten Sie die WebJobs-API-App verwenden, um einfache Codeausdrücke zu schreiben und über Ihre Logik-App aufzurufen.

Wenn Sie zudem zusätzliche Pakete einschließen möchten, müssen Sie auch die WebJobs-API-App verwenden, da Sie mit der JavaScript-API-App keine Bibliotheken hinzufügen können.

Verwenden Sie die [C#-API-App](app-service-logic-cs-api.md), wenn Sie Ausdrücke lieber in C# schreiben möchten.

##Erstellen einer JavaScript-API-App
Um die JavaScript-API-App verwenden zu können, müssen Sie zunächst eine Instanz in der JavaScript-API-App erstellen. Dies kann entweder inline beim Erstellen einer Logik-App oder durch Auswählen der JavaScript-API-App aus dem Azure Marketplace erfolgen.

##Verwenden der JavaScript-API-App auf der Designeroberfläche von Logik-Apps
###Trigger
Sie können einen Trigger erstellen, der den Logik-App-Dienst abruft (in einem von Ihnen definierten Intervall). Wenn Inhalt zurückgegeben wird, wird die Logik-App ausgeführt, andernfalls wartet sie bis zur Überprüfung im nächsten Abrufintervall.

Die Eingaben für den Trigger sind: - **JavaScript-Ausdruck** - Ein Ausdruck, der ausgewertet wird. Er wird in einer Funktion aufgerufen und muss `false` zurückgeben, wenn die Logik-App nicht ausgeführt werden soll. Er kann beliebige Werte zurückgeben, wenn die Logik-App ausgeführt werden soll. Sie können den Inhalt der Antwort in den Aktionen der Logik-App verwenden. - **Kontextobjekt** - Ein optionales Objekt, das an den Trigger übergeben werden kann. Sie können beliebig viele Eigenschaften definieren, aber die Entität der obersten Ebene muss ein Objekt sein, z. B. `{ "bar" : 0}`.

Angenommen, Sie verfügen über einen einfachen Trigger, der die Logik-App nur zwischen :15 und :30 einer Stunde ausführt:

```
var d = new Date(); return (d.getMinutes() > 15) && (d.getMinutes() < 30);
```

###Aktion

Ebenso können Sie eine auszuführende Aktion bereitstellen.

Die Eingaben für die Aktion sind: - **JavaScript-Ausdruck** - Ein Ausdruck, der ausgewertet wird. Sie müssen die `return`-Anweisung aufnehmen, um Inhalte abzurufen. – **Kontextobjekt** – Ein optionales Objekt, das an den Trigger übergeben werden kann. Sie können beliebig viele Eigenschaften definieren, aber die Entität der obersten Ebene muss ein Objekt sein, z. B. `{ "bar" : 0}`.

Angenommen, Sie verwenden den Office 365-Trigger **Neue E-Mail**. Damit wird das folgende Objekt zurückgegeben: ```
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

```
return Attachments.map(function(obj){var a = obj.Content; a.FileName = obj.Name; return a;})
```

Die Aktion gibt das JSON-Objekt zurück, das Sie von Ihrer Funktion zurückgegeben haben. So können Sie in der Yammer-API-App auf `@body('javascriptapi')` für die **Attachments**-Eigenschaft verweisen.

## Mehr mit Ihrem Connector machen
Nachdem der Connector nun erstellt ist, können Sie ihn mit Logik-App in einem Geschäftsworkflow hinzufügen. Informationen finden Sie unter [Was sind Logik-Apps?](app-service-logic-what-are-logic-apps.md).

Sie können auch Leistungsstatistiken überprüfen und die Sicherheit zum Connector steuern. Informationen finden Sie unter [API-Apps und Connector verwalten und überwachen](../app-service-api/app-service-api-manage-in-portal.md).

<!--References -->

<!--Links -->
[Creating a Logic App]: app-service-logic-create-a-logic-app.md

<!---HONumber=AcomDC_0224_2016-->
