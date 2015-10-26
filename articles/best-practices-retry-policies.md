<properties
   pageTitle="NuGet-Pakete | Microsoft Azure"
   description="Anleitungen zu NuGet-Paketen für die Arbeit mit allgemeinen Wiederholungsrichtlinien."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/09/2015"
   ms.author="masashin"/>

# NuGet-Pakete

<p class="lead">Wenn mehrere Komponenten beginnen, miteinander zu kommunizieren, wird die Behandlung vorübergehende Fehler immer wichtiger. Die Fehlerbehandlung bei vorübergehenden Arbeit, die von den Wiederholungsrichtlinien im NuGet-Paket behandelt werden, helfen bei der Behandlung von Wiederholungen innerhalb einer einzigen Instanz.</p>

> Dieses Dokument basiert auf einem Entwurf als Machbarkeitsstudie. Es ist nicht die tatsächliche geprüfte Anleitung.

Der Patterns & Practices `TransientFaultHandling` Code wird die Arbeit allgemeiner Wiederholungsrichtlinien empfohlen.

```
Install-Package EnterpriseLibrary.WindowsAzure.TransientFaultHandling
```

## Konfiguration

Der Abschnitt enthält die Konfigurationsinformationen für die Wiederholfunktion:

Parameter | Beschreibung
-------------------- | ----------------------
MaximumExecutionTime | Maximale Ausführungszeit für die Anforderung, einschließlich aller möglichen Wiederholungen.
ServerTimeOut | Server-Timeout-Intervall für die Anforderung
RetryPolicy | Wiederholungsrichtlinie. Siehe Abschnitt „Richtlinien“ unten

```csharp
/// <summary>
/// An interface required for request option types.
/// </summary>
public interface IRequestOptions
{
    IRetryPolicy RetryPolicy { get; set; }

    TimeSpan? ServerTimeout { get; set; }

    TimeSpan? MaximumExecutionTime { get; set; }
}
```

Programmgesteuert:

- Die Unterstützung für Einstellungen auf dem Client.
- Aktivieren Sie das Überschreiben auf Vorgänge, die vom Client bereitgestellt werden

Konfigurationsdatei:

```xml
<RetryPolicyConfiguration defaultRetryStrategy="Fixed Interval Retry Strategy">
    <linearInterval name="Fixed Interval Retry Strategy"
	retryInterval="00:00:01" maxRetryCount="10" />
    <exponentialBackoff name="Backoff Retry Strategy" minBackoff="00:00:01"
        maxBackoff="00:00:30" deltaBackoff="00:00:10" maxRetryCount="10"
        fastFirst="false"/>
</RetryPolicyConfiguration>
```

## Richtlinien

### Exponentiell

Verwendet, um den Abstand wiederholter Versuche von Dienstaufrufen exponentiell zu vergrößern, um eine Drosselung des Dienstes zu vermeiden.

__Vorgehensweise:__

Das Backoff-Intervall zwischen nachfolgenden Versuchen exponentiell erhöhen. Hinzufügen von Zufallsgenerator (+/-20 %) zum Backoff-Intervall, damit nicht alle Clients gleichzeitig die Wiederholung durchführen.

__Konfiguration:__

Parameter | Beschreibung
-------------------- | -------------------------------------------------------
maxAttempt | Die Anzahl der Wiederholungsversuche.
deltaBackoff | Backoff-Intervall zwischen den Wiederholungen. Ein Vielfaches dieser Zeitspanne wird für nachfolgende Wiederholungen verwendet.
MinBackoff | Zu allen Wiederholungsintervallen hinzugefügt, die aus dem DeltaBackoff berechnet wurden.
FastFirst | Sofortiger erster Wiederholungsversuch
MaxBackoff | MaxBackoff wird verwendet, wenn das berechnete Wiederholungsintervall größer als MaxBackoff ist. Dieser Wert kann nicht geändert werden.

__Implementierungslogik:__

```csharp
if(!ExponentialRetry.FastFirst){
    Random r = new Random();
    double increment = (Math.Pow(2, currentRetryCount) - 1) * r.Next((int)(this.deltaBackoff.TotalMilliseconds * 0.8), (int)(this.deltaBackoff.TotalMilliseconds * 1.2));
    retryInterval = (increment < 0) ? ExponentialRetry.MaxBackoff :
    TimeSpan.FromMilliseconds(Math.Min(ExponentialRetry.MaxBackoff.TotalMilliseconds, ExponentialRetry.MinBackoff.TotalMilliseconds + increment));
} else {
    retryInterval = TimeSpan.Zero;
}
```

## Linear

Verwendet, um den Abstand wiederholter Versuche von Dienstaufrufen linear zu vergrößern, um eine Drosselung des Dienstes zu vermeiden.

__Vorgehensweise:__

Führen Sie eine angegebene Anzahl von Wiederholungen durch, mit einem angegebenen festen Zeitintervall zwischen den Wiederholungen. Hinzufügen von Zufallsgenerator (+/-20 %) zum Backoff-Intervall, damit nicht alle Clients gleichzeitig die Wiederholung durchführen.

__Konfiguration:__

Parameter | Beschreibung
-------------------- | -------------------------------------------------------
maxAttempt | Die Anzahl der Wiederholungsversuche.
deltaBackoff | Backoff-Intervall zwischen den Wiederholungen.
FastFirst | Sofortiger erster Wiederholungsversuch

__Implementierungslogik:__

```csharp
if(!ExponentialRetry.FastFirst) {
    Random r = new Random();
    retryInterval = TimeSpan.FromMilliseconds(r.Next((int)(
    this.deltaBackoff.TotalMilliseconds * 0.8), (int)(this.deltaBackoff.TotalMilliseconds * 1.2)));
} else {
    retryInterval = TimeSpan.Zero;
}
```

## Adaptiv

Verwendet, um den Abstand wiederholter Versuche von Dienstaufrufen basierend auf dem übergebenen Fehlercode/den übergebenen Metadaten vom Dienst im Antwortheader zu vergrößern.

__Vorgehensweise:__

Eine angegebene Anzahl von Wiederholungen ausführen, mit einem Backoff-Intervall, das basierend auf vom Dienst übergebenem Fehlercode/übergebenen Metadaten im Antwortheader berechnet wird


__Konfiguration:__

Nicht konfigurierbar

__Implementierungslogik:__

Basierend auf dem übergebenen Fehlercode/den übergebenen Metadaten, die vom Dienst im Antwortheader übergeben wurden

__Unterbrechung der Verbindung:__

Basierend auf dem [Trennschalter](http://msdn.microsoft.com/library/dn589784.aspx)

## Erweiterbarkeit

Öffentliche Schnittstelle, die für die Bereitstellung von benutzerdefinierten Wiederholungsversuchsrichtlinien implementiert werden kann

```csharp
public interface IRetryPolicy
{
    /// <summary>
    /// Generates a new retry policy for the current request attempt.
    /// </summary>
    IRetryPolicy CreateInstance();

    /// <summary>
    /// Determines whether the operation should be retried and the interval until the next retry.
    /// </summary>
    /// <param name="currentRetryCount">An integer specifying the number of retries for the given operation. A value of zero signifies this is the first error encountered.</param>
    /// <param name="statusCode">An integer containing the status code for the last operation.</param>
    /// <param name="retryInterval">A <see cref="TimeSpan"/> indicating the interval to wait until the next retry.</param>
    /// <returns><c>true</c> if the operation should be retried; otherwise, <c>false</c>.</returns>
    bool ShouldRetry(int currentRetryCount, int statusCode, out TimeSpan retryInterval);
}
```

## Telemetrie

Protokollieren Sie Wiederholungen als ETW-Ereignisse mit einer EventSource. Dies sind die Felder, die für jeden Wiederholungsversuch protokolliert werden sollten

Parameter | Beschreibung
-------------------- | -------------------------------------------------------
requestId | ""
policyType | "RetryExponential"
Vorgang | "Get:https://retry-guidance-tests.servicebus.windows.net/TestQueue/?api-version=2014-05"
operationStartTime | "9/5/2014 10:00:13 PM"
operationStartTime | "9/5/2014 10:00:14 PM"
Iteration | "0"
iterationSleep | "00:00:00.1000000"
lastExceptionType | "Microsoft.ServiceBus.Messaging.MessagingCommunicationException"
exceptionMessage | "Der Remotename konnte nicht aufgelöst werden: 'retry-guidance-tests.servicebus.windows.net'.TrackingId:6a26f99c-dc6d-422e-8565-f89fdd0d4fe3,TimeStamp:9/5/2014 10:00:13 PM"

<!---HONumber=Oct15_HO3-->