<properties
 pageTitle="Einschränkungen, Standardwerte und Fehlercodes für Scheduler"
 description=""
 services="scheduler"
 documentationCenter=".NET"
 authors="krisragh"
 manager="dwrede"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="07/28/2015"
 ms.author="krisragh"/>

# Einschränkungen, Standardwerte und Fehlercodes für Scheduler

## Kontingente, Einschränkungen, Standardwerte und Drosselungen für Scheduler

[AZURE.INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## Der Header „x-ms-request-id“

Jede Anforderung für den Scheduler-Dienst gibt einen Antwortheader namens **x-ms-request-id** zurück. Dieser Header enthält einen opaken Wert, der die Anforderung eindeutig identifiziert.

Wenn bei einer Anforderung kontinuierlich ein Fehler auftritt, obwohl die Anforderung ordnungsgemäß formuliert ist, können Sie den Fehler unter Angabe dieses Werts an Microsoft melden. Geben Sie in Ihrem Bericht den Wert von „x-ms-request-id“, die ungefähre Anforderungszeit, die ID des Abonnements, den Clouddienst, die Auftragssammlung und/oder den Auftrag sowie die Art des Vorgangs an, den die Anforderung ausführen sollte.

## Status- und Fehlercodes für Scheduler

Neben den standardmäßigen HTTP-Statuscodes gibt die Azure Scheduler-REST-API erweiterte Fehlercodes und Fehlermeldungen zurück. Die erweiterten Codes sind nicht als Ersatz für die standardmäßigen HTTP-Statuscodes gedacht, sondern liefern hilfreiche Zusatzinformationen, die zusammen mit den standardmäßigen HTTP-Statuscodes verwendet werden können.

Ein HTTP-404-Fehler kann beispielsweise zahlreiche Ursachen haben. Hier können die Zusatzinformationen aus der erweiterten Meldung hilfreich sein. Weitere Informationen zu den standardmäßigen HTTP-Codes, die von der REST-API zurückgegeben werden, finden Sie unter [Dienstverwaltungsstatus und Fehlercodes](https://msdn.microsoft.com/library/windowsazure/ee460801.aspx). REST-API-Vorgänge für die Dienstverwaltungs-API geben standardmäßige HTTP-Statuscodes gemäß [Statuscodedefinitionen für HTTP/1.1](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) zurück. Die folgende Tabelle enthält allgemeine Fehler, die unter Umständen vom Dienst zurückgegeben werden.

|Fehlercode|HTTP-Statuscode|Meldung für den Benutzer|
|----|----|----|
|MissingOrIncorrectVersionHeader|Unzulässige Anforderung (400)|Der Versionsverwaltungsheader wurde nicht oder falsch angegeben.|
|InvalidXmlRequest|Unzulässige Anforderung (400)|Das XML des Anforderungstexts war ungültig oder wurde nicht richtig angegeben.|
|MissingOrInvalidRequiredQueryParameter|Unzulässige Anforderung (400)|Ein erforderlicher Abfrageparameter für diese Anforderung wurde nicht angegeben oder falsch angegeben.|
|InvalidHttpVerb|Unzulässige Anforderung (400)|Das angegebene HTTP-Verb wurde vom Server nicht erkannt oder ist für diese Ressource ungültig.|
|AuthenticationFailed|Verboten (403)|Fehler des Servers beim Authentifizieren der Anforderung. Stellen Sie sicher, dass das Zertifikat gültig und diesem Abonnement zugeordnet ist.|
|ResourceNotFound|Nicht gefunden (404)|Die angegebene Ressource ist nicht vorhanden.|
|InternalError|Interner Serverfehler (500)|Auf dem Server ist ein interner Fehler aufgetreten. Versuchen Sie die Anforderung erneut.|
|OperationTimedOut|Interner Serverfehler (500)|Der Vorgang konnte nicht innerhalb der zulässigen Zeit abgeschlossen werden.|
|ServerBusy|Dienst nicht verfügbar (503)|Der Server (oder eine interne Komponente) ist zurzeit nicht verfügbar, um Anforderungen zu empfangen. Bitte wiederholen Sie die Anforderung.|
|SubscriptionDisabled|Verboten (403)|Das Abonnement weist einen deaktivierten Status auf.|
|BadRequest|Unzulässige Anforderung (400)|Ein Parameter war falsch.|
|ConflictError|Konflikt (409)|Es ist ein Konflikt aufgetreten, um den Abschluss des Vorgangs zu verhindern.|
|TemporaryRedirect|Temporäre Umleitung (307)|Das angeforderte Objekt ist nicht verfügbar. Ein temporärer URI für den neuen Speicherort des Objekts steht in der Antwort im Feld „Speicherort“ zur Verfügung. Die ursprüngliche Anforderung kann mit dem neuen URI wiederholt werden.|

API-Vorgänge können ebenfalls zusätzliche Fehlerinformationen zurückgeben, die vom Verwaltungsdienst definiert werden. Diese zusätzlichen Fehlerinformationen werden im Antworttext zurückgegeben. Für den Text der Fehlerantwort wird das folgende grundlegende Format verwendet:

		<?xml version="1.0" encoding="utf-8"?>  
		<Error>  
			<Code>string-code</Code>  
			<Message>detailed-error-message</Message>  
		</Error>  

## Siehe auch

 [Scheduler Concepts, Terminology, and Entity Hierarchy](scheduler-concepts-terms.md) (in englischer Sprache)

 [Get Started Using Scheduler in the Management Portal](scheduler-get-started-portal.md) (in englischer Sprache)

 [Plans and Billing in Azure Scheduler](scheduler-plans-billing.md) (in englischer Sprache)

 [How to Build Complex Schedules and Advanced Recurrence with Azure Scheduler](scheduler-advanced-complexity.md) (in englischer Sprache)

 [Zeitplanungsmodul-REST-API – Referenz](https://msdn.microsoft.com/library/dn528946)

 [Scheduler High-Availability and Reliability](scheduler-high-availability-reliability.md) (in englischer Sprache)

 [Scheduler Outbound Authentication](scheduler-outbound-authentication.md) (in englischer Sprache)

<!---HONumber=Oct15_HO3-->