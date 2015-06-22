<properties 
   pageTitle="Azure Automation-Webhooks"
   description="Ein Webhook, der es einem Client ermöglicht, ein Runbook in Azure Automation über einen HTTP-Aufruf zu starten. In diesem Artikel wird beschrieben, wie Sie einen Webhook erstellen und aufrufen, um ein Runbook zu starten."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/13/2015"
   ms.author="bwren" />

# Azure Automation-Webhooks

Mit einem *Webhook* können Sie ein bestimmtes Runbook in Azure Automation über eine einfache HTTP-Anforderung starten. Dadurch wird es externen Diensten wie Visual Studio Online, GitHub oder benutzerdefinierten Anwendungen ermöglicht, Runbooks zu starten, ohne eine vollständige Lösung unter Verwendung der Azure Automation API zu implementieren.

![Webhooks](media/automation-webhooks/webhooks-overview.png)

In [Starten eines Runbooks in Azure Automation](automation-starting-a-runbook.md) können Sie Webhooks mit anderen Methoden zum Starten eines Runbooks vergleichen.

## Details zu einem Webhook

Die folgende Tabelle beschreibt die Eigenschaften, die Sie für einen Webhook konfigurieren müssen.

| Eigenschaft | Beschreibung |
|:---|:---|
|Name | Sie können einem Webhook einen beliebigen Namen zuweisen, da er nicht für den Client verfügbar gemacht wird. Sie benötigen den Namen nur zur Identifizierung des Runbooks in Azure Automation. <br> Es empfiehlt sich, den Webhook entsprechend dem Client zu benennen, der ihn verwenden wird. |
|URL |Die URL des Webhooks ist die eindeutige Adresse, die ein Client mit einer HTTP POST-Anforderung aufruft, um das mit dem Webhook verknüpfte Runbook zu starten. Sie wird beim Erstellen des Webhooks automatisch generiert. Sie können keine benutzerdefinierte URL angeben. <br> <br> Die URL enthält ein Sicherheitstoken, das es ermöglicht, dass das Runbook ohne weitere Authentifizierung von einem Drittanbietersystem aufgerufen werden kann. Daher sollte sie wie ein Kennwort behandelt werden. Aus Sicherheitsgründen können Sie die URL im Azure-Vorschauportal nur zu dem Zeitpunkt anzeigen, zu dem der Webhook erstellt wird. Sie sollten die URL zur späteren Verwendung an einem sicheren Ort speichern. |
|Ablaufdatum | Ebenso wie ein Zertifikat verfügt jeder Webhook über ein Ablaufdatum, nach dem er nicht mehr verwendet werden kann. Das Ablaufdatum kann nach der Erstellung des Webhooks nicht geändert werden, und der Webhook kann nach Erreichen des Ablaufdatums nicht wieder aktiviert werden. In diesem Fall müssen Sie einen neuen Webhook erstellen, um den aktuellen zu ersetzen, und den Client aktualisieren, sodass er den neuen Webhook verwendet. |
| Aktiviert | Ein Runbook ist bei Erstellung standardmäßig aktiviert. Wenn Sie es auf "Deaktiviert" ändern, kann es von keinem Client mehr verwendet werden. Sie können die Eigenschaft **Aktiviert** beim Erstellen des Webhooks oder zu einem anderen Zeitpunkt nach der Erstellung festlegen. |


### Parameter
Ein Webhook kann Werte für Runbookparameter definieren, die verwendet werden, wenn das Runbook von diesem Webhook gestartet wird. Der Webhook muss Werte für alle obligatorischen Parameter des Runbooks einschließen und kann Werte für optionale Parameter einschließen. Mehrere mit einem einzelnen Runbook verknüpften Webhooks können verschiedene Parameterwerte verwenden.

>[AZURE.NOTE]Von einem Webhook festgelegte Parameterwerte können zurzeit nicht geändert werden, nachdem der Webhook erstellt wurde. Sie müssen einen anderen Webhook erstellen, der unterschiedliche Parameterwerte verwendet.

Wenn ein Client ein Runbook mithilfe eines Webhooks startet, können die im Webhook definierten Parameterwerte nicht überschrieben werden. Um Daten aus dem Client abzurufen, kann das Runbook nur einen Parameter namens **$WebhookData** vom Typ [object] akzeptieren, der Daten enthält, die der Client in die POST-Anforderung einschließt.

![Webhookdaten](media/automation-webhooks/webhookdata.png)

Das Objekt **$WebhookData** verfügt über folgende Eigenschaften:

| Eigenschaft | Beschreibung |
|:--- |:---|
| WebhookName | Der Name des Webhooks. |
| RequestHeader | Die Header der eingehenden POST-Anforderung. |
| RequestBody | Der Header der eingehenden POST-Anforderung. |

Zur Unterstützung des Parameters **$WebhookData** ist keine Konfiguration des Webhooks erforderlich, und das Runbook muss ihn nicht akzeptieren. Wenn das Runbook den Parameter nicht definiert, werden alle vom Client gesendeten Details der Anforderung ignoriert.

Wenn Sie beim Erstellen des Webhooks einen Wert für "$WebhookData" angeben, wird dieser Wert beim Starten des Runbooks durch den Webhook mit den Daten aus der POST-Anforderung des Clients überschrieben, selbst wenn der Anforderungstext des Clients keine Daten enthält. Wenn Sie ein Runbook, das einen "$WebhookData"-Wert enthält, mit einer anderen Methode als einem Webhook starten, können Sie einen Wert für "$WebhookData" bereitstellen, der vom Runbook erkannt wird. Bei diesem Wert sollte es sich um ein Objekt mit den gleichen Eigenschaften wie "$WebhookData" handeln, damit das Runbook damit ordnungsgemäß funktioniert.

>[AZURE.NOTE]Die Werte aller Eingabeparameter werden mit dem Runbookauftrag protokolliert. Dies bedeutet, dass alle vom Client bereitgestellten Eingaben mit "$WebhookData" protokolliert werden und jedem Benutzer mit Zugriff auf den Automatisierungsauftrag zur Verfügung stehen. Aus diesem Grund sollten Sie sorgfältig überlegen, welche vertraulichen Daten Sie in Webhookaufrufe einschließen.

## Sicherheit

Die Sicherheit eines Webhooks beruht auf dem Datenschutz seiner URL, die ein Sicherheitstoken enthält, mit dem es aufgerufen werden kann. Azure Automation führt keinerlei Authentifizierung der Anforderung durch, sofern diese über die richtige URL erfolgt. Aus diesem Grund sollten Webhooks nicht für Runbooks verwendet werden, die sehr vertrauliche Funktionen ausführen, ohne eine alternative Möglichkeit zur Überprüfung der Anforderung zu verwenden.

Sie können Logik in das Runbook integrieren, um zu ermitteln, ob es von einem Webhook aufgerufen wurde, indem Sie die Eigenschaft **WebhookName** des Parameters "$WebhookData" aktivieren. Das Runbook kann eine weitere Überprüfung durchführen, indem es in den Eigenschaften **RequestHeader** oder **RequestBody** nach bestimmten Informationen sucht.

Eine andere Strategie besteht darin, dass das Runbook eine externe Bedingung überprüft, wenn es eine Webhookanforderung empfängt. Betrachten Sie z. B. ein Runbook, das von GitHub aufgerufen wird, sobald ein neuer Commit für ein GitHub-Repository vorhanden ist. Das Runbook kann eine Verbindung mit GitHub herstellen, um zu überprüfen, ob tatsächlich gerade ein neuer Commit durchgeführt wurde, bevor es mit der Ausführung fortfährt.

## Erstellen eines Webhooks

Gehen Sie wie folgt vor, um einen neuen Webhook zu erstellen, der mit einem Runbook im Azure-Vorschauportal verknüpft ist

1. Klicken Sie im Blatt **Runbooks** im Azure-Vorschauportal auf das Runbook, das vom Webhook gestartet werden soll, um das Detailblatt des Runbooks anzuzeigen. 
3. Klicken Sie oben im Blatt auf **Webhook**, um das Blatt **Webhook hinzufügen** zu öffnen. <br> ![Schaltfläche "Webhooks"](media/automation-webhooks/webhooks-button.png)
4. Klicken Sie auf **Neuen Webhook erstellen**, um das Blatt **Webhook erstellen** zu öffnen.
5. Geben Sie einen **Namen** und ein **Ablaufdatum** für den Webhook an, und legen Sie fest, ob er aktiviert werden soll. Weitere Informationen zu diesen Eigenschaften finden Sie unter [Details zu einem Webhook](#details-of-a-webhook).
6. Klicken Sie auf das Symbol zum Kopieren, und drücken Sie STRG+C, um die URL des Webhooks zu kopieren. Speichern Sie die URL an einem sicheren Ort. **Nachdem Sie den Webhook erstellt haben, können Sie die URL nicht erneut abrufen.** <br> ![Webhook-URL](media/automation-webhooks/copy-webhook-url.png)
1. Klicken Sie auf **Erstellen**, um das Projekt zu erstellen.
3. Klicken Sie auf **Parameter**, um Werte für die Runbookparameter bereitzustellen. <br>
1. Klicken Sie auf **OK**, wenn Sie mit dem Konfigurieren des Webhooks fertig sind.


## Verwenden eines Webhooks

Um einen Webhook zu verwenden, muss Ihre Clientanwendung eine HTTP POST-Anforderung mit der URL für den Webhook ausgeben. Die Syntax des Webhooks weist folgendes Format auf.

	http://<Webhook Server>/token?=<Token Value>


Der Client empfängt einen der folgenden Codes als Antwort auf die POST-Anforderung.

| Code | Text | Beschreibung |
|:---|:----|:---|
| 202 | Zulässig | Die Anforderung wurde akzeptiert, und das Runbook wurde erfolgreich gestartet. |
| 400 | Ungültige Anforderung | Die Anforderung wurde aus einem der folgenden Gründe nicht akzeptiert. <ul> <li>Der Webhook ist abgelaufen.</li> <li>Der Webhook ist deaktiviert.</li> <li>Das Token in der URL ist ungültig.</li> </ul>|
| 500 | Interner Serverfehler | Die URL ist gültig, es ist jedoch ein Fehler aufgetreten. Senden Sie die Anforderung erneut. |

Der Client kann weder die Bestätigung, dass ein Runbookauftrag abgeschlossen wurde, noch den Abschlussstatus aus einem Webhook abrufen, da der Webhook keinen Bezeichner für den Runbookauftrag zurückgibt. Sie können nur überprüfen, ob die Anforderung erfolgreich übermittelt wurde.

### Beispiel

Im folgenden Beispiel wird ein Runbook, das einen Webhook verwendet, mit Windows PowerShell gestartet. Dieses Beispiel enthält Daten im Header und im Text, die vom Runbook verwendet werden können. Hinweis: In jeder Sprache, mit der eine HTTP-Anforderung erstellt werden kann, kann einen Webhook verwendet werden.

	$uri = "https://oaaswebhookcurrent.cloudapp.net/webhooks?token=8ud0dSrSo%2fvHWpYbklW%3c8s0GrOKJZ9Nr7zqcS%2bIQr4c%3d"
	$headers = @{"header1"="headerval1";"header2"="headerval2"}
	$body = "some request body"

	Invoke-RestMethod -Method Post -Uri $uri -Headers $headers -Body $body

Im folgenden Beispiel für ein Runbook wird die vorherige Anforderung akzeptiert und es werden die Daten aus dem Webhook abgerufen.

	workflow Sample-Webhook
	{
		param (	
				[object]$WebhookData
		)
	
		$WebhookName 	= 	$WebhookData.WebhookName
		$WebhookHeaders = 	$WebhookData.RequestHeader
		$WebhookBody 	= 	$WebhookData.RequestBody
	} 

	

## Verwandte Artikel

- [Starten eines Runbooks](automation-starting-a-runbook.md)
- [Anzeigen des Status eines Runbookauftrags](automation-viewing-the-status-of-a-runbook-job.md)

<!---HONumber=58--> 