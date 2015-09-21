<properties 
   pageTitle="SAS-Authentifizierung bei Service Bus | Microsoft Azure"
   description="Details zur SAS-Authentifizierung mit Service Bus."
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="" />
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/04/2015"
   ms.author="sethm" />

# SAS-Authentifizierung bei Service Bus

Die [SAS-Authentifizierung (Shared Access Signature)](service-bus-sas-overview.md) ermöglicht Anwendungen die Authentifizierung bei Service Bus mithilfe eines Zugriffsschlüssels, der für den Namespace oder für die Messagingentität (Warteschlange oder Thema) konfiguriert wird, welcher bestimmte Rechte zugeordnet sind. Sie können diesen Schlüssel zum Generieren eines SAS-Tokens verwenden, das Clients wiederum für die Authentifizierung bei Service Bus verwenden können.

Die Unterstützung der SAS-Authentifizierung ist im Azure SDK, Version 2.0 oder höher, enthalten. Weitere Informationen zur Service Bus-Authentifizierung finden Sie unter [Service Bus-Authentifizierung und -Autorisierung](service-bus-authentication-and-authorization.md).

## Konzepte

Die SAS-Authentifizierung in Service Bus umfasst die Konfiguration eines kryptografischen Schlüssels mit den zugehörigen Rechten für eine Service Bus-Ressource. Clients beanspruchen Zugriff auf Service Bus-Ressourcen, indem sie ein SAS-Token bereitstellen. Dieses Token besteht aus dem Ressourcen-URI, auf den zugegriffen wird, und einer Ablaufangabe, die mit dem konfigurierten Schlüssel signiert wird.

Sie können SAS-Autorisierungsregeln für Service Bus [Relays](service-bus-fundamentals-hybrid-solutions.md/#relays), -[Warteschlangen](service-bus-fundamentals-hybrid-solutions.md/#queues), -[Themen](service-bus-fundamentals-hybrid-solutions.md/#topics) und -[Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/) konfigurieren.

Die SAS-Authentifizierung verwendet die folgenden Elemente:

- [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx): ein primärer 256-Bit-Kryptografieschlüssel in Base64-Darstellung, ein optionaler sekundärer Schlüssel und ein Schlüsselname sowie zugehörige Rechte (eine Auflistung von Lausch-, Sende- oder Verwaltungsrechten, d. h. *Listen*, *Send* und *Manage*).

- [SharedAccessSignature](https://msdn.microsoft.com/library/azure/microsoft.servicebus.sharedaccesssignaturetokenprovider.sharedaccesssignature.aspx)-Token: Wird mithilfe des HMAC-SHA256-Codes einer Ressourcenzeichenfolge generiert und besteht aus dem URI der Ressource, auf die zugegriffen wird, sowie einer Ablaufangabe mit dem kryptografischen Schlüssel. Die Signatur und andere in den folgenden Abschnitten beschriebene Elemente werden als Zeichenfolge formatiert, um das [SharedAccessSignature](https://msdn.microsoft.com/library/azure/microsoft.servicebus.sharedaccesssignaturetokenprovider.sharedaccesssignature.aspx)-Token zu bilden.

## Konfiguration für SAS-Authentifizierung (Shared Access Signature)

Sie können die Regel [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) für Service Bus-Namespaces, -Warteschlangen oder -Themen konfigurieren. Die Konfiguration einer [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) für ein Service Bus-Abonnement wird zurzeit nicht unterstützt. Sie können die Regeln, die für einen Namespace oder ein Thema konfiguriert wurden, jedoch verwenden, um den Zugriff auf Abonnements abzusichern. Ein praktisches Beispiel für dieses Verfahren finden Sie unter [Using Shared Access Signature (SAS) authentication with Service Bus Subscriptions](http://code.msdn.microsoft.com/windowsazure/Using-Shared-Access-e605b37c) (in englischer Sprache).

Maximal zwölf solcher Regeln können für einen Service Bus-Namespace, eine Service Bus-Warteschlange oder ein Service Bus-Thema konfiguriert werden. Regeln, die für einen Service Bus-Namespace konfiguriert werden, gelten für alle Entitäten in dem jeweiligen Namespace.

![SAS](./media/service-bus-shared-access-signature-authentication/IC676272.gif)

In dieser Abbildung gelten die Autorisierungsregeln *manageRuleNS*, *sendRuleNS* und *listenRuleNS* sowohl für die Warteschlange "Q1" als auch für das Thema "T1", während *listenRuleQ* und *sendRuleQ* nur für die Warteschlange "Q1" gelten und *sendRuleT* nur für das Thema "T1" gilt.

Die folgenden Schlüsselparameter gelten für ein [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx)-Objekt:

|Parameter|Beschreibung|
|---|---|
|*KeyName*|Eine Zeichenfolge, die die Autorisierungsregel beschreibt.|
|*PrimaryKey*|Ein primärer Base64-codierter 256-Bit-Schlüssel zum Signieren und Überprüfen des SAS-Tokens.|
|*SecondaryKey*|Ein sekundärer Base64-codierter 256-Bit-Schlüssel zum Signieren und Überprüfen des SAS-Tokens.|
|*AccessRights*|Eine Liste der Zugriffsrechte, die von der Autorisierungsregel erteilt werden. Bei diesen Rechten kann es sich um eine beliebige Auflistung von Lausch-, Sende- und Verwaltungsrechten ("Listen", "Send" und "Manage") handeln.|

Wenn ein Service Bus-Namespace bereitgestellt wird, wird standardmäßig eine [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) erstellt, in der [KeyName](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.keyname.aspx) auf **RootManageSharedAccessKey** festgelegt ist. Zwei [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx)-Objekte werden ebenfalls für Notification Hubs konfiguriert: ein Objekt mit Lausch-, Sende- und Verwaltungsrechten und ein weiteres Objekt mit Lauschrechten.

## Erneutes Generieren und Widerrufen von Schlüsseln für SAS-Autorisierungsregeln

Es wird empfohlen, die im [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx)-Objekt verwendeten Schlüssel in regelmäßigen Abständen neu zu generieren. Anwendungen sollten im Allgemeinen den [PrimaryKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.primarykey.aspx) zum Generieren eines SAS-Tokens verwenden. Wenn die Schlüssel erneut generiert werden, sollten Sie den [SecondaryKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.secondarykey.aspx) durch den alten primären Schlüssel ersetzen und dann einen neuen Schlüssel als neuen primären Schlüssel generieren. Auf diese Weise können Sie Token weiterhin für die Autorisierung verwenden, die mit dem alten primären Schlüssel ausgestellt wurden und noch nicht abgelaufen sind.

Wenn ein Schlüssel gefährdet ist und Sie die Schlüssel widerrufen müssen, können Sie sowohl den [PrimaryKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.primarykey.aspx) als auch den [SecondaryKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.secondarykey.aspx) eines [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx)-Objekts neu generieren und so durch neue Schlüssel ersetzen. Durch dieses Verfahren werden alle Token, die mit den alten Schlüsseln signiert wurden, für ungültig erklärt.

## Generieren eines SAS-Tokens (Shared Access Signature)

Jeder Client, der Zugriff auf die in der SAS-Autorisierungsregel angegebenen Signaturschlüssel besitzt, kann das SAS-Token generieren. Dabei wird das folgende Format verwendet:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

Die **Signatur** für das SAS-Token wird anhand des HMAC-SHA256-Hashs einer Zeichenfolge für die Signatur mit der Eigenschaft [PrimaryKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.primarykey.aspx) einer Autorisierungsregel berechnet. Die Zeichenfolge für die Signatur besteht aus einem Ressourcen-URI und einem Ablaufwert im folgenden Format:

```
StringToSign = <resourceURI> + "\n" + expiry;
```

Beachten Sie, dass Sie den codierten Ressourcen-URI für diesen Vorgang verwenden sollten. Der Ressourcen-URI ist der vollständige URI der Service Bus-Ressource, auf die der Zugriff beansprucht wird. Beispiel: `http://<namespace>.servicebus.windows.net/<entityPath>` oder `sb://<namespace>.servicebus.windows.net/<entityPath>`, also `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3`.

Der Ablaufwert wird als die Anzahl der Sekunden seit dem 1. Januar 1970 um 00:00:00 UTC dargestellt.

Die zum Signieren verwendete SAS-Autorisierungsregel muss für die durch diesen URI angegebene Entität oder eines seiner hierarchisch übergeordneten Elemente konfiguriert werden. Beispiel: `http://contoso.servicebus.windows.net/contosoTopics/T1` oder `http://contoso.servicebus.windows.net` im vorherigen Beispiel.

Ein SAS-Token ist für alle Ressourcen unter dem `<resourceURI>` gültig, der in der Zeichenfolge für die Signatur verwendet wird.

Der [KeyName](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.keyname.aspx) im SAS-Token bezieht sich auf den **keyName** der SAS-Autorisierungsregel, die zum Generieren des Tokens verwendet wird.

*URL-encoded-resourceURI* muss mit dem URI identisch sein, der in der Zeichenfolge für die Signatur während der Berechnung der Signatur verwendet wird. Er sollte als [Prozentwert codiert](https://msdn.microsoft.com/library/4fkewx0t.aspx) sein.

## Verwenden der SAS-Authentifizierung mit Service Bus

Die folgenden Szenarien umfassen die Konfiguration von Autorisierungsregeln, das Generieren von SAS-Token und die Clientautorisierung.

Ein vollständiges praktisches Beispiel für eine Service Bus-Anwendung, die die Konfiguration veranschaulicht und die SAS-Autorisierung verwendet, finden Sie unter [SAS-Authentifizierung bei Service Bus](http://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8). Ein Beispiel, das die Verwendung von in Namespaces oder Themen konfigurierten SAS-Autorisierungsregeln zum Absichern von Service Bus-Abonnements veranschaulicht, finden Sie hier: [Using Shared Access Signature (SAS) authentication with Service Bus Subscriptions](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c) (in englischer Sprache).

## Zugreifen auf SAS-Autorisierungsregeln für einen Namespace

Vorgänge für den Service Bus-Namespacestamm erfordern eine Zertifikatauthentifizierung. Sie müssen ein Verwaltungszertifikat für Ihr Azure-Abonnement hochladen. Klicken Sie zum Hochladen eines Verwaltungszertifikats im linken Bereich des Azure-Portals auf **Einstellungen**. Weitere Informationen zu Azure-Verwaltungszertifikaten finden Sie unter [Erstellen eines Verwaltungszertifikats für Azure](https://msdn.microsoft.com/library/azure/gg551722.aspx).

Der Endpunkt für den Zugriff auf SAS-Autorisierungsregeln für einen Service Bus-Namespace lautet wie folgt:

```
https://management.core.windows.net/{subscriptionId}/services/ServiceBus/namespaces/{namespace}/AuthorizationRules/
```

Um ein [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx)-Objekt für einen Service Bus-Namespace zu erstellen, führen Sie einen POST-Vorgang für diesen Endpunkt mit den Regelinformationen aus, die als JSON oder XML serialisiert wurden. Beispiel:

```
// Base address for accessing authorization rules on a namespace
string baseAddress = @"https://management.core.windows.net/<subscriptionId>/services/ServiceBus/namespaces/<namespace>/AuthorizationRules/";

// Configure authorization rule with base64-encoded 256-bit key and Send rights
var sendRule = new SharedAccessAuthorizationRule("contosoSendAll",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Send });

// Operations on the Service Bus namespace root require certificate authentication.
WebRequestHandler handler = new WebRequestHandler
{
    ClientCertificateOptions = ClientCertificateOption.Manual
};
// Access the management certificate by subject name
handler.ClientCertificates.Add(GetCertificate(<certificateSN>));

HttpClient httpClient = new HttpClient(handler)
{
    BaseAddress = new Uri(baseAddress)
};
httpClient.DefaultRequestHeaders.Accept.Add(
    new MediaTypeWithQualityHeaderValue("application/json"));
httpClient.DefaultRequestHeaders.Add("x-ms-version", "2015-01-01");

// Execute a POST operation on the baseAddress above to create an auth rule
var postResult = httpClient.PostAsJsonAsync("", sendRule).Result;
```

Verwenden Sie in ähnlicher Weise einen GET-Vorgang für den Endpunkt, um die für den Namespace konfigurierten Autorisierungsregeln zu lesen.

Zum Aktualisieren oder Löschen einer bestimmten Autorisierungsregel verwenden Sie den folgenden Endpunkt:

```
https://management.core.windows.net/{subscriptionId}/services/ServiceBus/namespaces/{namespace}/AuthorizationRules/{KeyName}
```

## Zugreifen auf SAS-Autorisierungsregeln für eine Entität

Sie können auf ein [Microsoft.ServiceBus.Messaging.SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx)-Objekt, das für eine Service Bus-Warteschlange oder ein Service Bus-Thema konfiguriert ist, über die [AuthorizationRules](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.authorizationrules.aspx)-Auflistung im entsprechenden [QueueDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx)-, [TopicDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.aspx)- oder [NotificationHubDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.notifications.notificationhubdescription.aspx)-Objekt zugreifen.

Der folgende Code zeigt, wie Sie Autorisierungsregeln für eine Warteschlange hinzufügen.

```
// Create an instance of NamespaceManager for the operation
NamespaceManager nsm = NamespaceManager.CreateFromConnectionString( 
    <connectionString> );
QueueDescription qd = new QueueDescription( <qPath> );

// Create a rule with send rights with keyName as "contosoQSendKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoSendKey", 
    SharedAccessAuthorizationRule.GenerateRandomKey(), 
    new[] { AccessRights.Send }));

// Create a rule with listen rights with keyName as "contosoQListenKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQListenKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Listen }));

// Create a rule with manage rights with keyName as "contosoQManageKey"
// and add it to the queue description.
// A rule with manage rights must also have send and receive rights.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQManageKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send }));

// Create the queue.
nsm.CreateQueue(qd);
```

## Verwenden der SAS-Authentifizierung (Shared Access Signature)

Anwendungen, die das Azure .NET SDK mit den .NET-Bibliotheken von Service Bus nutzen, können die SAS-Autorisierung über die [SharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.sharedaccesssignaturetokenprovider.aspx)-Klasse verwenden. Der folgende Code veranschaulicht die Verwendung des Tokenanbieters zum Senden von Nachrichten an eine Service Bus-Warteschlange.

```
Uri runtimeUri = ServiceBusEnvironment.CreateServiceUri("sb", 
    <yourServiceNamespace>, string.Empty);
MessagingFactory mf = MessagingFactory.Create(runtimeUri, 
    TokenProvider.CreateSharedAccessSignatureTokenProvider(keyName, key));
QueueClient sendClient = mf.CreateQueueClient(qPath);

//Sending hello message to queue.
BrokeredMessage helloMessage = new BrokeredMessage("Hello, Service Bus!");
helloMessage.MessageId = "SAS-Sample-Message";
sendClient.Send(helloMessage);
```

Anwendungen können SAS auch zur Authentifizierung verwenden, indem sie eine SAS-Verbindungszeichenfolge in Methoden einsetzen, die Verbindungszeichenfolgen akzeptieren.

Beachten Sie, dass Sie zum Verwenden der SAS-Autorisierung mit Service Bus Relays SAS-Schlüssel nutzen können, die für den Service Bus-Namespace konfiguriert sind. Wenn Sie ein Relay explizit im Namespaceobjekt erstellen ([NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) mit [RelayDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.relaydescription.aspx)), können Sie die SAS-Regeln nur für dieses jeweilige Relay festlegen. Zum Verwenden der SAS-Autorisierung mit Service Bus-Abonnements können Sie SAS-Schlüssel nutzen, die für einen Service Bus-Namespace oder ein Thema konfiguriert sind.

## Erforderliche Rechte für Service Bus-Vorgänge

Die folgende Tabelle zeigt die Zugriffsrechte, die für verschiedene Vorgänge für Service Bus-Ressourcen erforderlich sind.

|Vorgang|Erforderlicher Anspruch|Anspruchsbereich|
|---|---|---|
|**Namespace**|||
|Konfigurieren einer Autorisierungsregel für einen Namespace|Verwalten|Jede Namespaceadresse|
|**Dienstregistrierung**|||
|Aufzählen privater Richtlinien|Verwalten|Jede Namespaceadresse|
|Relay|||
|Starten des Lauschvorgangs an einem Namespace|Empfangen|Jede Namespaceadresse|
|Senden von Nachrichten an einen Listener in einem Namespace|Send|Jede Namespaceadresse|
|**Warteschlange**|||
|Erstellen einer Warteschlange|Verwalten|Jede Namespaceadresse|
|Löschen einer Warteschlange|Verwalten|Beliebige gültige Warteschlangenadresse|
|Aufzählen von Warteschlangen|Verwalten|/$Resources/Queues|
|Abrufen der Warteschlangenbeschreibung|Verwalten oder Senden|Beliebige gültige Warteschlangenadresse|
|Konfigurieren einer Autorisierungsregel für eine Warteschlange|Verwalten|Beliebige gültige Warteschlangenadresse|
|Senden in die Warteschlange|Send|Beliebige gültige Warteschlangenadresse|
|Empfangen von Nachrichten aus einer Warteschlange|Empfangen|Beliebige gültige Warteschlangenadresse|
|Verwerfen oder Abschließen von Nachrichten nach dem Empfang der Nachricht im Peek/Lock-Modus|Empfangen|Beliebige gültige Warteschlangenadresse|
|Zurückstellen einer Nachricht für den späteren Abruf|Empfangen|Beliebige gültige Warteschlangenadresse|
|Platzieren einer Nachricht in die Warteschlange für unzustellbare Nachrichten|Empfangen|Beliebige gültige Warteschlangenadresse|
|Abrufen des einer Nachrichtenwarteschlangensitzung zugeordneten Status|Empfangen|Beliebige gültige Warteschlangenadresse|
|Festlegen des einer Nachrichtenwarteschlangensitzung zugeordneten Status|Empfangen|Beliebige gültige Warteschlangenadresse|
|**Thema**|||
|Erstellen eines Themas|Verwalten|Jede Namespaceadresse|
|Löschen eines Themas|Verwalten|Beliebige gültige Themenadresse|
|Auflisten von Themen|Verwalten|/$Resources/Topics|
|Abrufen der Themenbeschreibung|Verwalten oder Senden|Beliebige gültige Themenadresse|
|Konfigurieren einer Autorisierungsregel für ein Thema|Verwalten|Beliebige gültige Themenadresse|
|Senden an das Thema|Send|Beliebige gültige Themenadresse|
|**Abonnement**|||
|Erstellen eines Abonnements|Verwalten|Jede Namespaceadresse|
|Löschen eines Abonnements|Verwalten|../myTopic/Subscriptions/mySubscription|
|Aufzählen von Abonnements|Verwalten|../myTopic/Subscriptions|
|Abrufen der Abonnementbeschreibung|Verwalten oder Lauschen|../myTopic/Subscriptions/mySubscription|
|Verwerfen oder Abschließen von Nachrichten nach dem Empfang der Nachricht im Peek/Lock-Modus|Empfangen|../myTopic/Subscriptions/mySubscription|
|Zurückstellen einer Nachricht für den späteren Abruf|Empfangen|../myTopic/Subscriptions/mySubscription|
|Platzieren einer Nachricht in die Warteschlange für unzustellbare Nachrichten|Empfangen|../myTopic/Subscriptions/mySubscription|
|Abrufen des einer Themensitzung zugeordneten Status|Empfangen|../myTopic/Subscriptions/mySubscription|
|Festlegen des einer Themensitzung zugeordneten Status|Empfangen|../myTopic/Subscriptions/mySubscription|
|**Regel**|||
|Erstellen einer Regel|Verwalten|../myTopic/Subscriptions/mySubscription|
|Löschen einer Regel|Verwalten|../myTopic/Subscriptions/mySubscription|
|Aufzählen von Regeln|Verwalten oder Lauschen|../myTopic/Subscriptions/mySubscription/Rules|
|**Notification Hubs**|||
|Erstellen eines Notification Hubs|Verwalten|Jede Namespaceadresse|
|Erstellen oder Aktualisieren der Registrierung für ein aktives Gerät|Lauschen oder Verwalten|../notificationHub/tags/{tag}/registrations|
|Aktualisieren von PNS-Informationen|Lauschen oder Verwalten|../notificationHub/tags/{tag}/registrations/updatepnshandle|
|Senden an einen Notification Hub|Send|../notificationHub/messages|

## Nächste Schritte

Eine allgemeine Übersicht über SAS in Service Bus finden Sie unter [SAS (Shared Access Signatures)](service-bus-sas-overview.md).

Weitere Hintergrundinformationen zur Service Bus-Authentifizierung finden Sie unter [Service Bus-Authentifizierung und -Autorisierung](service-bus-authentication-and-authorization.md).

<!---HONumber=Sept15_HO2-->