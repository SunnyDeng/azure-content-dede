<properties 
   pageTitle="Service Bus und PHP mit AMQP 1.0 | Microsoft Azure"
   description="Verwenden von Service Bus aus PHP mit AMQP"
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" /> 
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/26/2016"
   ms.author="sethm" />

# Verwenden von Service Bus aus PHP mit AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

Proton-PHP ist eine PHP-Sprachbindung an Proton-C. Dies bedeutet, dass Proton-PHP als Wrapper für ein Modul implementiert wird, das in C implementiert wurde.

## Herunterladen der Proton-Clientbibliothek

Sie können Proton-C und die dazugehörigen Bindungen (einschließlich PHP) unter [http://qpid.apache.org/download.html](http://qpid.apache.org/download.html) herunterladen. Der Download erfolgt in Quellcodeform. Folgen Sie zum Erstellen des Codes den Anweisungen, die im heruntergeladenen Paket enthalten sind.

> [AZURE.IMPORTANT] Zum Zeitpunkt der Erstellung dieses Dokuments ist die SSL-Unterstützung in Proton-C nur für Linux-Betriebssysteme verfügbar. Da Azure Service Bus die Verwendung von SSL erfordert, können Proton-C (und die Sprachbindungen) derzeit nur für den Zugriff auf Service Bus aus Linux verwendet werden. Wir arbeiten daran, Proton-C mit SSL unter Windows zu ermöglichen. Achten Sie also auf Updates hierzu.

## Verwenden von Service Bus-Warteschlangen, -Themen und -Abonnements aus PHP

Der folgende Code veranschaulicht, wie Sie Nachrichten aus einer Service Bus-Messagingentität senden und empfangen.

### Senden von Nachrichten mithilfe von Proton-PHP

Der folgende Code veranschaulicht, wie Sie eine Nachricht an eine Service Bus-Messagingentität senden.

```
$messenger = new Messenger();
$message = new Message();
$message->address = "amqps://[username]:[password]@[namespace].servicebus.windows.net/[entity]";

$message->body = "This is a text string";
$messenger->put($message);
$messenger->send();
```

### Empfangen von Nachrichten mit Proton-PHP

Der folgende Code veranschaulicht, wie Sie eine Nachricht von einer Service Bus-Messagingentität empfangen.

```
$messenger = new Messenger();
$address = "amqps://[username]:[password]@[namespace].servicebus.windows.net/[entity]";
$messenger->subscribe($address);

$messenger->start();
$messenger->recv(1);

if($messenger->incoming())
{
   $message = new Message();
   $messenger->get($message);      
}

$messenger->stop();
```

## Übertragen von Nachrichten zwischen .NET und Proton-PHP

### Anwendungseigenschaften

#### ProtonPHP zu .NET-APIs von Service Bus

Proton-PHP-Nachrichten unterstützen Anwendungseigenschaften der folgenden Typen: **integer**, **double**, **Boolean**, **string** und **object**. Der folgende PHP-Code veranschaulicht, wie Sie Eigenschaften für eine Nachricht festlegen, indem Sie diese Eigenschaftstypen verwenden.

```
$message->properties["TestInt"] = 1;    
$message->properties["TestDouble"] = 1.5;      
$message->properties["TestBoolean"] = False;
$message->properties["TestString"] = "Service Bus";    
$message->properties["TestObject"] = new UUID("1234123412341234");   
```

In den .NET-APIs von Service Bus sind die Nachrichtenanwendungseigenschaften in der **Properties**-Auflistung von [BrokeredMessage][] enthalten. Der folgende Code veranschaulicht, wie die Anwendungseigenschaften einer Nachricht gelesen werden, die von einem PHP-Client empfangen wird.

```
if (message.Properties.Keys.Count > 0)
{
  foreach (string name in message.Properties.Keys)
  {
    Object value = message.Properties[name];
    Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
  }
  Console.WriteLine();
}if (message.Properties.Keys.Count > 0)
{
foreach (string name in message.Properties.Keys)
{
  Object value = message.Properties[name];
  Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
}
Console.WriteLine();
}
```

In der folgende Tabelle sind die PHP-Eigenschaftstypen den .NET-Eigenschaftstypen zugeordnet.

| PHP-Eigenschaftstyp | .NET-Eigenschaftstyp |
|-------------------|--------------------|
| integer | int |
| double | double |
| boolean | bool |
| string | string |
| object | Object |

#### .NET-API von Service Bus zu PHP

Der [BrokeredMessage][]-Typ unterstützt Anwendungseigenschaften der folgenden Typen: **byte**, **sbyte**, **char**, **short**, **ushort**, **int**, **uint**, **long**, **ulong**, **float**, **double**, **decimal**, **bool**, **Guid**, **string**, **Uri**, **DateTime**, **DateTimeOffset** und **TimeSpan**. Der folgende .NET-Code veranschaulicht, wie Sie Eigenschaften für ein [BrokeredMessage][]-Objekt festlegen, indem Sie diese Eigenschaftstypen verwenden.

```
message.Properties["TestByte"] = (byte)128;
message.Properties["TestSbyte"] = (sbyte)-22;
message.Properties["TestChar"] = (char) 'X';
message.Properties["TestShort"] = (short)-12345;
message.Properties["TestUshort"] = (ushort)12345;
message.Properties["TestInt"] = (int)-100;
message.Properties["TestUint"] = (uint)100;
message.Properties["TestLong"] = (long)-12345;
message.Properties["TestUlong"] = (ulong)12345;
message.Properties["TestFloat"] = (float)3.14159;
message.Properties["TestDouble"] = (double)3.14159;
message.Properties["TestDecimal"] = (decimal)3.14159;
message.Properties["TestBoolean"] = true;
message.Properties["TestGuid"] = Guid.NewGuid();
message.Properties["TestString"] = "Service Bus";
message.Properties["TestUri"] = new Uri("http://www.bing.com");
message.Properties["TestDateTime"] = DateTime.Now;
message.Properties["TestDateTimeOffSet"] = DateTimeOffset.Now;
message.Properties["TestTimeSpan"] = TimeSpan.FromMinutes(60);
```

Der folgende PHP-Code veranschaulicht, wie die Anwendungseigenschaften einer Nachricht gelesen werden, die von einem .NET-Client von Service Bus empfangen wird.

```
if ($message->properties != null)
{
  foreach($message->properties as $key => $value)
  {
    printf("-- %s : %s (%s) \n", $key, $value, gettype($value));                       
  }         
}
```

In der folgende Tabelle sind die .NET-Eigenschaftstypen den PHP-Eigenschaftstypen zugeordnet.

| .NET-Eigenschaftstyp | PHP-Eigenschaftstyp | Hinweise |
|--------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| byte | integer | - | | sbyte | integer | - | | char | Char | Proton-PHP-Klasse | | short | integer | - | | ushort | integer | - | | int | integer | - | | uint | Integer | - | | long | integer | - | | ulong | integer | - | | float | double | - | | double | double | - | | decimal | string | "Decimal" wird derzeit nicht mit Proton unterstützt. | | bool | boolean | - | | Guid | UUID | Proton-PHP-Klasse | | string | string | - | | DateTime | integer | - | | DateTimeOffset | DescribedType | DateTimeOffset.UtcTicks zugeordnet zu AMQP-Typ:<type name="datetime-offset" class=restricted source="long"> <descriptor name="com.microsoft:datetime-offset" /></type> | | TimeSpan | DescribedType | Timespan.Ticks zugeordnet zu AMQP-Typ:<type name="timespan" class=restricted source="long"> <descriptor name="com.microsoft:timespan" /></type> | | Uri | DescribedType | Uri.AbsoluteUri zugeordnet zu AMQP-Typ:<type name="uri" class=restricted source="string"> <descriptor name="com.microsoft:uri" /></type> |

### Standardeigenschaften

Die folgenden Tabellen enthalten die Zuordnung zwischen den Proton-PHP-Standardnachrichteneigenschaften und den [BrokeredMessage][]-Standardnachrichteneigenschaften.

| Proton-PHP | Service Bus .NET | Hinweise |
|----------------------|--------------------------|----------------------------------------------------------|
| Durable | – | Service Bus unterstützt nur dauerhafte Nachrichten. |
| Priority | – | Service Bus unterstützt nur eine Nachrichtenpriorität. |
| Ttl | Message.TimeToLive | Konvertierung, Proton-Python-TTL-Wert wird in Millisekunden definiert. |
| first\_acquirer | - | - | | delivery\_count | - | - | | Id | Message.Id | - | | user\_id | - | - | | Address | Message.To | - | | Subject | Message.Label | - | | reply\_to | Message.ReplyTo | - | | correlation\_id | Message.CorrelationId | - | | content\_type | Message.ContentType | - | | content\_encoding | – | - | | expiry\_time | Message.ExpiresAtUTC | - | | creation\_time | – | - | | group\_id | Message.SessionId | - | | group\_sequence | - | - | | reply\_to\_group\_id | Message.ReplyToSessionId | - | | Format | – | -

#### .NET-APIs von Service Bus zu Proton-PHP

| Service Bus .NET | Proton-PHP | Hinweise |
|-------------------------|--------------------------------------------------------|--------------------------------------------------------|
| ContentType | Message->content\_type | - | | CorrelationId | Message->correlation\_id | - | | EnqueuedTimeUtc | Message->annotations[x-opt-enqueued-time] | - | | Label | Message->subject | - | | MessageId | Message->id | - | | ReplyTo | Message->reply\_to | - | | ReplyToSessionId | Message->reply\_to\_group\_id | - | | ScheduledEnqueueTimeUtc | Message->annotations ["x-opt-scheduled-enqueue-time"] | - | | SessionId | Message->group\_id | - | | TimeToLive | Message->ttl | Konvertierung, Proton-PHP-TTL-Wert wird in Millisekunden definiert. | | To | Message->address | - |

## Nächste Schritte

Möchten Sie mehr erfahren? Nutzen Sie die folgenden Links:

- [Übersicht über Service Bus AMQP]
- [AMQP in Service Bus für Windows Server]


[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
[AMQP in Service Bus für Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
[Übersicht über Service Bus AMQP]: service-bus-amqp-overview.md

<!---HONumber=AcomDC_0128_2016-->