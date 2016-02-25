<properties 
   pageTitle="Service Bus und Python mit AMQP 1.0 | Microsoft Azure"
   description="Verwenden von Service Bus aus Python mit AMQP"
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
   ms.date="02/08/2016"
   ms.author="sethm" />

# Verwenden von Service Bus aus Python mit AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

Proton-Python ist eine Python-Sprachbindung an Proton-C. Dies bedeutet, dass Proton-Python als Wrapper für ein Modul implementiert wird, das in C implementiert wurde.

## Herunterladen der Proton-Clientbibliothek

Sie können Proton-C und die dazugehörigen Bindungen (einschließlich Python) unter [http://qpid.apache.org/download.html](http://qpid.apache.org/download.html) herunterladen. Der Download erfolgt in Quellcodeform. Folgen Sie zum Erstellen des Codes den Anweisungen, die im heruntergeladenen Paket enthalten sind.

Beachten Sie, dass zum Zeitpunkt der Erstellung dieses Dokuments die SSL-Unterstützung in Proton-C nur für Linux-Betriebssysteme verfügbar ist. Da Azure Service Bus die Verwendung von SSL erfordert, können Proton-C (und die Sprachbindungen) derzeit nur zum Zugreifen auf Service Bus von Linux verwendet werden. Wir arbeiten daran, Proton-C mit SSL unter Windows zu ermöglichen. Achten Sie also auf Updates hierzu.

## Verwenden von Service Bus-Warteschlangen, -Themen und -Abonnements

Der folgende Code veranschaulicht, wie Sie Nachrichten aus einer Service Bus-Messagingentität senden und empfangen.

### Senden von Nachrichten mithilfe von Proton-Python

Der folgende Code veranschaulicht, wie Sie eine Nachricht an eine Service Bus-Messagingentität senden.

```
messenger = Messenger()
message = Message()
message.address = "amqps://[username]:[password]@[namespace].servicebus.windows.net/[entity]"

message.body = u"This is a text string"
messenger.put(message)
messenger.send()
```

### Empfangen von Nachrichten mit Proton-Python

Der folgende Code veranschaulicht, wie Sie eine Nachricht von einer Service Bus-Messagingentität empfangen.

```
messenger = Messenger()
address = "amqps://[username]:[password]@[namespace].servicebus.windows.net/[entity]"
messenger.subscribe(address)

messenger.start()
messenger.recv(1)
message = Message()
if messenger.incoming:
      messenger.get(message)
messenger.stop()
```

## Messaging zwischen .NET und Proton-Python

### Anwendungseigenschaften

#### Proton-Python an Service Bus .NET-APIs

Für Proton-Python-Nachrichten werden Anwendungseigenschaften der folgenden Typen unterstützt: **int**, **long**, **float**, **uuid**, **bool**, **string**. Der folgende Python-Code veranschaulicht, wie Sie Eigenschaften für eine Nachricht festlegen, indem Sie diese Eigenschaftentypen verwenden.

```
message.properties[u"TestString"] = u"This is a string"    
message.properties[u"TestInt"] = 1
message.properties[u"TestLong"] = 1000L
message.properties[u"TestFloat"] = 1.5    
message.properties[u"TestGuid"] = uuid.uuid1()    
```

In der Service Bus .NET-API werden die Anwendungseigenschaften von Nachrichten in der Sammlung **Properties** von [BrokeredMessage][] vorgehalten. Der folgende Code veranschaulicht, wie die Anwendungseigenschaften einer Nachricht gelesen werden, die von einem Python-Client empfangen wird.

```
if (message.Properties.Keys.Count > 0)
{
  foreach (string name in message.Properties.Keys)
  {
    Object value = message.Properties[name];
    Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
  }
  Console.WriteLine();
}
```

In der folgende Tabelle sind die Python-Eigenschaftentypen den .NET-Eigenschaftentypen zugeordnet.

| Python-Eigenschaftentyp | .NET-Eigenschaftentyp |
|----------------------|--------------------|
| int | int |
| float | double |
| lang | int64 |
| uuid | GUID |
| bool | bool |
| string | Zeichenfolge |

#### Service Bus .NET-APIs an Proton-Python

Der Typ [BrokeredMessage][] unterstützt Anwendungseigenschaften der folgenden Typen: **byte**, **sbyte**, **char**, **short**, **ushort**, **int**, **uint**, **long**, **ulong**, **float**, **double**, **decimal**, **bool**, **Guid**, **string**, **Uri**, **DateTime**, **DateTimeOffset** und **TimeSpan**. Der folgende .NET-Code veranschaulicht, wie Sie Eigenschaften für ein [BrokeredMessage][]-Objekt festlegen, indem Sie diese Eigenschaftentypen verwenden.

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
message.Properties["TestTimeSpan"] = TimeSpan.FromMinutes(60);
```

Der folgende Python-Code veranschaulicht, wie die Anwendungseigenschaften einer Nachricht gelesen werden, die von einem Service Bus-.NET-Client empfangen wird.

```
if message.properties != None:
   for k,v in message.properties.items():         
         print "--   %s : %s (%s)" % (k, str(v), type(v))         
```

In der folgende Tabelle sind die .NET-Eigenschaftentypen den Python-Eigenschaftentypen zugeordnet.

| .NET-Eigenschaftentyp | Python-Eigenschaftentyp | Hinweise |
|--------------------|----------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| byte | int | - |
| sbyte | int | - |
| char | char | Proton-Python-Klasse |
| short | int | - |
| ushort | int | - |
| int | int | - |
| uint | int | - |
| long | int | - |
| ulong | long | Proton-Python-Klasse |
| float | float | - |
| double | float | - |
| decimal | String | „decimal“ wird für Proton derzeit nicht unterstützt. |
| bool | bool | - |
| Guid | uuid | Proton-Python-Klasse |
| string | string | - |
| DateTime | timestamp | Proton-Python-Klasse |
| DateTimeOffset | DescribedType | DateTimeOffset.UtcTicks ist AMQP-Typ zugeordnet:<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type> |
| TimeSpan | DescribedType | Timespan.Ticks ist AMQP-Typ zugeordnet:<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type> |
| Uri | DescribedType | Uri.AbsoluteUri ist AMQP-Typ zugeordnet:<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type> |

### Standardeigenschaften

Die folgenden Tabellen enthalten die Zuordnung zwischen den Proton-Python-Standardnachrichteneigenschaften und den [BrokeredMessage][]-Standardnachrichteneigenschaften.

#### Proton-Python an Service Bus .NET-APIs

| Proton-Python | Service Bus .NET | Hinweise |
|----------------------|--------------------------|-----------------------------------------------------------|
| durable | – | Service Bus unterstützt nur dauerhafte Nachrichten. |
| priority | – | Service Bus unterstützt nur eine Nachrichtenpriorität. |
| Ttl | Message.TimeToLive | Konvertierung, Proton-Python-TTL-Wert wird in Millisekunden definiert. |
| first\_acquirer | n/a | - |
| delivery\_count | n/a | - |
| Id | Message.MessageID | - |
| user\_id | n/a | - |
| address | Message.To | - |
| subject | Message.Label | - |
| reply\_to | Message.ReplyTo | - |
| correlation\_id | Message.CorrelationID | - |
| content\_type | Message.ContentType | - |
| content\_encoding | n/a | - |
| expiry\_time | n/a | - |
| creation\_time | n/a | - |
| group\_id | Message.SessionId | - |
| group\_sequence | n/a | - |
| reply\_to\_group\_id | Message.ReplyToSessionId | - |
| format | n/a | - |

| Service Bus .NET | Proton | Hinweise |
|-------------------------|------------------------------|-----------------------------------------------------------|
| ContentType | Message.content\_type | - |
| CorrelationId | Message.correlation\_id | - |
| EnqueuedTimeUtc | n/a | - |
| Label | Message.subject | - |
| MessageId | Message.id | - |
| ReplyTo | Message.reply\_to | - |
| ReplyToSessionId | Message.reply\_to\_group\_id | - |
| ScheduledEnqueueTimeUtc | n/a | - |
| SessionId | Message.group\_id | - |
| TimeToLive | Message.ttl | Konvertierung, Proton-Python-TTL-Wert wird in Millisekunden definiert. |
| To | Message.address | - |

## Nächste Schritte

Möchten Sie mehr erfahren? Nutzen Sie die folgenden Links:

- [Übersicht über Service Bus AMQP]
- [AMQP in Service Bus für Windows Server]

[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
[AMQP in Service Bus für Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx

[Übersicht über Service Bus AMQP]: service-bus-amqp-overview.md

<!---HONumber=AcomDC_0211_2016-->