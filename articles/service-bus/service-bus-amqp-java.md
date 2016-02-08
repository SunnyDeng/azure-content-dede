<properties 
   pageTitle="Service Bus und Java mit AMQP 1.0 | Microsoft Azure"
   description="Verwenden von Service Bus aus Java mit AMQP"
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

# Verwenden von Service Bus aus Java mit AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

Der Java Message Service (JMS) ist eine Standard-API für das Arbeiten mit nachrichtenbasierter Middleware auf der Java-Plattform. Microsoft Azure Service Bus wurde mit der auf AMQP 1.0 basierenden JMS-Clientbibliothek getestet, die im Rahmen des Apache Qpid-Projekts entwickelt wurde. Diese Bibliothek unterstützt die vollständige JMS 1.1-API und kann mit jedem mit AMQP 1.0 kompatiblen Messagingdienst verwendet werden. Dieses Szenario wird auch in [Service Bus für Windows Server](https://msdn.microsoft.com/library/dn282144.aspx) (lokaler Service Bus) unterstützt. Weitere Informationen finden Sie unter [AMQP in Service Bus für Windows Server][].

## Herunterladen der Apache Qpid AMQP 1.0 JMS-Clientbibliothek

Informationen zum Herunterladen der neuesten Version der Apache Qpid JMS AMQP 1.0-Clientbibliothek finden Sie unter: [http://people.apache.org/~rgodfrey/qpid-java-amqp-1-0-client-jms.html](http://people.apache.org/~rgodfrey/qpid-java-amqp-1-0-client-jms.html).

Folgende vier JAR-Dateien müssen aus dem Apache Qpid JMS AMQP 1.0-Verteilungsarchiv zu dem Java-KLASSENPFAD hinzugefügt werden, wenn JMS-Anwendungen mit Service Bus erstellt und ausgeführt werden:

-   geronimo-jms\_1.1\_spec-[version].jar

-   qpid-amqp-1-0-client-[version].jar

-   qpid-amqp-1-0-client-jms-[version].jar

-   qpid-amqp-1-0-common-[version].jar

## Verwenden von Service Bus-Warteschlangen, -Themen und -Abonnements aus JMS

### JNDI (Java Naming and Directory Interface; Java Benennungs- und Verzeichnisschnittstelle)

JMS verwendet die Java Naming and Directory Interface (JNDI), um eine Trennung zwischen logischen und physischen Namen umzusetzen. Zwei Typen von JMS-Objekten werden mit JNDI aufgelöst: **ConnectionFactory** und **Destination**. JNDI verwendet ein Anbietermodell, das Sie mit verschiedenen Verzeichnisdiensten verbinden können, um Namensauflösungsfunktionen zu implementieren. Die Apache Qpid JMS AMQP 1.0-Bibliothek enthält einen einfachen dateibasierten JNDI-Anbieter, der mithilfe einer Textdatei konfiguriert wird.

Der JNDI-Anbieter der Qpid-Eigenschaftendatei wird mithilfe einer Eigenschaftendatei im folgenden Format konfiguriert:

```
# servicebus.properties – sample JNDI configuration

# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCONNECTIONFACTORY = amqps://[username]:[password]@[namespace].servicebus.windows.net

# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
topic.TOPIC = topic1
queue.QUEUE = queue1
```

#### Konfigurieren der Verbindungsfactory

Der Eintrag zum Definieren einer **ConnectionFactory** in der Qpid Properties-Datei für den JNDI-Anbieter hat das folgende Format:

```
connectionfactory.[jndi_name] = [ConnectionURL]
```

`[jndi\_name]` und `[ConnectionURL]` haben die folgende Bedeutung:

| Name | Bedeutung | | | | |
|-----------------|--------------------------------------------------------------------------------------------------------------------------------------------|---|---|---|---|
| `[jndi\_name]` | Der logische Name der Verbindungsfactory. Dieser Name wird in der Java-Anwendung mithilfe der JNDI-Methode `IntialContext.lookup()` aufgelöst. | | | | |
| `[ConnectionURL]` | Diese URL liefert der JMS-Bibliothek die vom AMQP-Broker benötigten Informationen. | | | | |

Das Format der Verbindungs-URL ist wie folgt:

```
amqps://[username]:[password]@[namespace].servicebus.windows.net
```

`[namespace]`, `[username]` und `[password]` haben die folgende Bedeutung:

| Name | Bedeutung | | | | |
|---------------|--------------------------------------------------------------------------------|---|---|---|---|
| `[namespace]` | Der Service Bus-Namespace, der aus dem [klassischen Azure-Portal][] abgerufen wird. | | | | |
| `[username]` | Der Service Bus-Ausstellername, der aus dem [klassischen Azure-Portal][] abgerufen wird. | | | | |
| `[password]` | Die URL-codierte Form des Service Bus-Ausstellerschlüssels, der aus dem [klassischen Azure-Portal][] abgerufen wird. | | | | |

> [AZURE.NOTE] Sie müssen das Kennwort manuell URL-codieren. Ein nützliches URL-Codierungshilfsprogramm ist unter [http://www.w3schools.com/tags/ref\_urlencode.asp](http://www.w3schools.com/tags/ref_urlencode.asp) verfügbar.

Angenommen, die aus dem Azure-Portal abgerufenen Informationen lauten wie folgt:

| Namespace: | test.servicebus.windows.net |
|--------------|----------------------------------------------|
| Ausstellername: | owner |
| Ausstellerschlüssel: | abcdefg |

Mit der folgenden Konfigurationszeichenfolge können Sie in diesem Fall ein **ConnectionFactory**-Objekt mit dem Namen `SBCONNECTIONFACTORY` definieren:

```
connectionfactory.SBCONNECTIONFACTORY = amqps://owner:abcdefg@test.servicebus.windows.net
```

#### Konfigurieren von Zielen

Der Eintrag zum Definieren eines Ziels in der "Properties"-Datei von Qpid für den JNDI-Anbieter hat das folgende Format:

```
queue.[jndi_name] = [physical_name]
topic.[jndi_name] = [physical_name]
```

`[jndi\_name]` und `[physical\_name]` haben die folgende Bedeutung:

| Name | Bedeutung |
|-------------------|------------------------------------------------------------------------------------------------------------------------------------------|
| `[jndi\_name]` | Der logische Name des Ziels. Dieser Name wird in der Java-Anwendung mithilfe der JNDI-Methode `IntialContext.lookup()` aufgelöst. |
| `[physical\name]` | Der Name der Service Bus-Entität, mit der die Anwendung Nachrichten austauscht. |

Beachten Sie Folgendes:

- Der `[physical\name]`-Wert kann eine Service Bus-Warteschlange oder ein Service Bus-Thema sein.
- Beim Empfang von einem Service Bus-Themenabonnement sollte der in JNDI angegebene physische Name dem Themennamen entsprechen. Der Abonnementname wird bei der Erstellung des Abonnements im JMS-Anwendungscode angegeben.
- Es ist auch möglich, ein Service Bus-Themenabonnement als JMS-Warteschlange zu behandeln. Dieser Ansatz hat mehrere Vorteile: für Warteschlangen und Themenabonnements kann derselbe Empfängercode verwendet werden, und alle Adressinformationen (die Namen von Themen und Abonnements) werden in die "Properties"-Datei offengelegt.
- Damit ein Service Bus-Themenabonnement als eine JMS-Warteschlange behandelt wird, muss der Eintrag in der "Properties"-Datei dieses Format haben: `queue.[jndi\_name] = [topic\_name]/Subscriptions/[subscription\_name]`.|

Zum Definieren eines logischen JMS-Ziels namens "TOPIC", das dem Service Bus-Thema namens "topic1" zugeordnet ist, muss der Eintrag in der "Properties"-Datei wie folgt aussehen:

```
topic.TOPIC = topic1
```

### Senden von Nachrichten mithilfe von JMS

Der folgende Code veranschaulicht, wie Sie eine Nachricht an ein Service Bus-Thema senden. Es wird davon ausgegangen, dass `SBCONNECTIONFACTORY` und `TOPIC`, wie im vorherigen Abschnitt beschrieben, in der Konfigurationsdatei **servicebus.properties** definiert sind.

```
Hashtable<String, String> env = new Hashtable<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, 
        "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties"); 
 
InitialContext context = new InitialContext(env); 
 
ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCONNECTIONFACTORY");
Topic topic = (Topic) context.lookup("TOPIC");
Connection connection = cf.createConnection();
Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
MessageProducer producer = session.createProducer(topic);
TextMessage message = session.createTextMessage("This is a text string"); 
producer.send(message);
```

### Empfangen von Nachrichten mithilfe von JMS

Der folgende Code veranschaulicht, wie Sie eine Nachricht von einem Service Bus-Themenabonnement empfangen. Es wird davon ausgegangen, dass `SBCONNECTIONFACTORY` und TOPIC, wie im vorherigen Abschnitt beschrieben, in der Konfigurationsdatei **servicebus.properties** definiert sind. Außerdem wird vorausgesetzt, dass der Abonnementname `subscription1` lautet.

```
Hashtable<String, String> env = new Hashtable<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, 
        "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties"); 
 
InitialContext context = new InitialContext(env);

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCONNECTIONFACTORY");
Topic topic = (Topic) context.lookup("TOPIC");
Connection connection = cf.createConnection();
Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
TopicSubscriber subscriber = session.createDurableSubscriber(topic, "subscription1");
connection.start();
Message message = messageConsumer.receive();
```

### Leitlinien für das Erstellen stabiler Anwendungen

Die JMS-Spezifikation definiert, wie der Ausnahmevertrag der API-Methoden und Anwendungscode geschrieben werden muss, um solche Ausnahmen zu behandeln. Hier sind einige andere Punkte, die in Bezug auf die Behandlung von Ausnahmen zu berücksichtigen sind:

-   Registrieren Sie einen **ExceptionListener** mit der JMS-Verbindung mithilfe von **connection.setExceptionListener**. Dadurch kann ein Client asynchron über ein Problem benachrichtigt werden. Diese Benachrichtigung ist besonders wichtig für Verbindungen, die nur Nachrichten nutzen, da sie ansonsten keine andere Möglichkeit haben zu erfahren, dass ihre Verbindung ausgefallen ist. Der **ExceptionListener** wird aufgerufen, wenn es ein Problem mit der zugrunde liegenden AMQP-Verbindung, Sitzung oder dem Link gibt. In diesem Fall muss die Anwendung die Objekte **JMS Connection**, **Session**, **MessageProducer** und **MessageConsumer** von Grund auf neu erstellen.

-   Um dafür zu sorgen, dass eine Nachricht vom einem **MessageProducer** erfolgreich an eine Service Bus-Entität gesendet wurde, stellen Sie sicher, dass die Anwendung mit festgelegter Systemeigenschaft **sync\_publish** konfiguriert wurde. Starten Sie dazu das Programm mit der Java VM-Option **-Dqpid.sync\_publish=true**, die Sie beim Starten der Anwendung über die Befehlszeile festlegen. Durch Festlegen dieser Option wird die Bibliothek so konfiguriert, dass keine Rückgabe aus dem Sendeaufruf erfolgt, bis die Bestätigung empfangen wird, dass die Nachricht von Service Bus akzeptiert wurde. Wenn ein Problem während des Sendevorgangs auftritt, wird eine **JMSException** ausgelöst. Es gibt zwei mögliche Ursachen:
	1. Wenn das Problem vorliegt, weil Service Bus die bestimmte gesendete Nachricht zurückweist, wird eine **MessageRejectedException**-Ausnahme ausgelöst. Dieser Fehler ist entweder vorübergehend oder beruht auf einem Problem mit der Nachricht. Die empfohlene Vorgehensweise ist, den Vorgang mit einer Backofflogik zu wiederholen. Wenn das Problem weiterhin besteht, sollte die Nachricht mit einem lokal protokollierten Fehler verworfen werden. In dieser Situation besteht keine Notwendigkeit zum erneuten Erstellen der Objekte **JMS Connection**, **Session** oder **MessageProducer**. 
	2. Wenn das Problem dadurch verursacht wird, dass Service Bus den AMQP-Link schließt, wird eine **InvalidDestinationException**-Ausnahme ausgelöst. Der Grund kann ein vorübergehendes Problem oder das Löschen der Nachrichtenentität sein. In beiden Fällen müssen die Objekte **JMS Connection**, **Session** und **MessageProducer** neu erstellt werden. Wenn die Fehlerbedingung vorübergehend war, wird dieser Vorgang schließlich erfolgreich sein. Wenn die Entität gelöscht wurde, ist der Fehler permanent.

## Übertragen von Nachrichten zwischen .NET und JMS

### Nachrichtentext

JMS definiert fünf verschiedene Nachrichtentypen: **BytesMessage**, **MapMessage**, **ObjectMessage**, **StreamMessage** und **TextMessage**. Die .NET-API von Service Bus hat mit [BrokeredMessage][] nur einen Nachrichtentyp.

#### JMS zur .NET-API von Service Bus

Die folgenden Abschnitte zeigen, wie Nachrichten der einzelnen JMS-Nachrichtentypen in .NET genutzt werden. Ein Beispiel für **ObjectMessage** wurde noch nicht hinzugefügt, da der Text einer **ObjectMessage** ein serialisierbares Objekt in der Programmiersprache Java enthält, das eine .NET-Anwendung nicht interpretieren kann.

##### BytesMessage

Der folgende Code zeigt, wie der Text eines **BytesMessage**-Objekts mithilfe der .NET-APIs von Service Bus genutzt werden kann.

```
Stream stream = message.GetBody<Stream>();
int streamLength = (int)stream.Length;

byte[] byteArray = new byte[streamLength];
stream.Read(byteArray, 0, streamLength);

Console.WriteLine("Length = " + streamLength);
for (int i = 0; i < stream.Length; i++)
{
  Console.Write("[" + (sbyte) byteArray[i] + "]");
}
```

##### MapMessage

Der folgende Code zeigt, wie der Text eines **MapMessage**-Objekts mithilfe der .NET-APIs von Service Bus genutzt werden kann. Dieser Code durchläuft die Elemente der Zuordnung und zeigt den Namen und Wert der einzelnen Elemente an.

```
Dictionary<String, Object> dictionary = message.GetBody<Dictionary<String, Object>>();

foreach (String mapItemName in dictionary.Keys)
{
  Object mapItemValue = null;
  if (dictionary.TryGetValue(mapItemName, out mapItemValue))
  {
    Console.WriteLine(mapItemName + ":" + mapItemValue);
  }
}
```

##### StreamMessage

Der folgende Code zeigt, wie der Text eines **StreamMessage**-Objekts mithilfe der .NET-APIs von Service Bus genutzt werden kann. Dieser Code listet alle Elemente im Datenstrom mit ihrem Typ auf.

```
List<Object> list = message.GetBody<List<Object>>();

foreach (Object item in list)
{
  Console.WriteLine(item + " (" + item.GetType() + ")");
}
```

##### TextMessage

Der folgende Code zeigt, wie der Text eines **TextMessage**-Objekts mithilfe der .NET-APIs von Service Bus genutzt werden kann. Dieser Code zeigt die Textzeichenfolge, die im Text der Nachricht enthalten ist.

```
Console.WriteLine("Text: " + message.GetBody<String>());
```

#### .NET-API von Service Bus zu JMS

Die folgenden Abschnitte zeigen, wie eine .NET-Anwendung eine Nachricht erstellen kann, die in JMS in den einzelnen JMS-Nachrichtentypen empfangen wird. Ein Beispiel für **ObjectMessage** wurde noch nicht hinzugefügt, da der Text einer **ObjectMessage** ein serialisierbares Objekt in der Programmiersprache Java enthält, das eine .NET-Anwendung nicht interpretieren kann.

##### BytesMessage

Der folgende Code zeigt, wie Sie in .NET ein [BrokeredMessage][]-Objekt erstellen, das von einem JMS-Client als **BytesMessage** empfangen wird.

```
byte[] bytes = { 33, 12, 45, 33, 12, 45, 33, 12, 45, 33, 12, 45 };
message = new BrokeredMessage(bytes);
```

##### StreamMessage

Der folgende Code zeigt, wie Sie in .NET ein [BrokeredMessage][]-Objekt erstellen, das von einem JMS-Client als **StreamMessage** empfangen wird.

```
List<Object> list = new List<Object>();
list.Add("String 1");
list.Add("String 2");
list.Add("String 3");
list.Add((double)3.14159);
message = new BrokeredMessage(list);
```

##### TextMessage

Der folgende Code zeigt, wie der Text einer **TextMessage**mithilfe der .NET-API von Service Bus genutzt werden kann. Dieser Code zeigt die Textzeichenfolge, die im Text der Nachricht enthalten ist.

```
message = new BrokeredMessage("this is a text string");
```

### Anwendungseigenschaften

####JMS zu .NET-APIs von Service Bus

JMS-Nachrichten unterstützen Anwendungseigenschaften der folgenden Typen: **boolean**, **byte**, **short**, **int**, **long**, **float**, **double** und **String**. Der folgende Java-Code veranschaulicht, wie Sie Eigenschaften für eine Nachricht festlegen, indem Sie diese Eigenschaftstypen verwenden.

```
message.setBooleanProperty("TestBoolean", true); 
message.setByteProperty("TestByte", (byte) 33); 
message.setDoubleProperty("TestDouble", 3.14159D); 
message.setFloatProperty("TestFloat", 3.13159F); 
message.setIntProperty("TestInt", 100); 
message.setStringProperty("TestString", "Service Bus");
```

In den .NET-APIs von Service Bus sind die Nachrichtenanwendungseigenschaften in der **Properties**-Auflistung von [BrokeredMessage][] enthalten. Der folgende Code veranschaulicht, wie die Anwendungseigenschaften einer Nachricht gelesen werden, die von einem JMS-Client empfangen wird.

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

Die folgende Tabelle zeigt, wie die JMS-Eigenschaftstypen den .NET-Eigenschaftstypen zugeordnet sind.

| JMS-Eigenschaftstyp | .NET-Eigenschaftstyp |
|-------------------|--------------------|
| Byte | sbyte |
| Integer | int |
| Float | float |
| Double | double |
| Boolean | bool |
| String | string |

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

Der folgende Java-Code veranschaulicht, wie die Anwendungseigenschaften einer Nachricht gelesen werden, die von einem .NET-Client von Service Bus empfangen wird.

```
Enumeration propertyNames = message.getPropertyNames(); 
while (propertyNames.hasMoreElements()) 
{ 
  String name = (String) propertyNames.nextElement(); 
  Object value = message.getObjectProperty(name); 
  System.out.println(name + ": " + value + " (" + value.getClass() + ")"); 
}
```

Die folgende Tabelle zeigt, wie die .NET-Eigenschaftstypen den JMS-Eigenschaftstypen zugeordnet sind.

| .NET-Eigenschaftstyp | JMS-Eigenschaftstyp | Hinweise |
|--------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| byte | UnsignedByte | - | 
| sbyte | Byte | - |
| char | Character | - |
| short | Short | - |
| ushort | UnsignedShort | - |
| int | Integer | - |
| uint | UnsignedInteger | - |
| long | Long | - |
| ulong | UnsignedLong | - |
| float | Float | - |
| double | Double | - |
| decimal | BigDecimal | - |
| bool | Boolean | - |
| Guid | UUID | - |
| string | String | - |
| DateTime | Date | - |
 | DateTimeOffset | DescribedType | DateTimeOffset.UtcTicks zugeordnet zum AMQP-Typ:<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type> |
| TimeSpan | DescribedType | Timespan.Ticks zugeordnet zum AMQP-Typ:<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type> |
| Uri | DescribedType | Uri.AbsoluteUri zugeordnet zum AMQP-Typ:<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type> |

### Standardheader

Die folgenden Tabellen zeigen, wie die JMS-Standardheader und [BrokeredMessage][]-Standardeigenschaften mithilfe von AMQP 1.0 zugeordnet werden.

#### JMS zu .NET-APIs von Service Bus

| JMS | Service Bus .NET | Hinweise |
|------------------|--------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| JMSCorrelationID | Message.CorrelationID | - |
| JMSDeliveryMode | Derzeit nicht verfügbar | Service Bus unterstützt nur dauerhafte Nachrichten. z. B. DeliveryMode.PERSISTENT, unabhängig davon, was angegeben wird. | 
| JMSDestination | Message.To | - | 
| JMSExpiration | Message. TimeToLive | Conversion | 
| JMSMessageID | Message.MessageID | Standardmäßig wird die JMSMessageID in binärer Form in der AMQP-Nachricht codiert. Beim Empfang einer binären Nachrichten-ID wird die .NET-Clientbibliothek basierend auf den Unicode-Werten der Bytes in eine Zeichenfolgendarstellung konvertiert. Damit die JMS-Bibliothek Zeichenfolgennachrichten-IDs verwendet, fügen Sie die Zeichenfolge "binary-messageid=false" an die Abfrageparameter von "JNDI ConnectionURL" an. Beispiel: “amqps://[username]:[password]@[namespace].servicebus.windows.net? binary-messageid=false”. |
| JMSPriority | Derzeit nicht verfügbar. | Service Bus unterstützt keine Nachrichtenpriorität. |
| JMSRedelivered | Derzeit nicht verfügbar | - |
| JMSReplyTo | Message. ReplyTo | - |
| JMSTimestamp | Message.EnqueuedTimeUtc | Conversion |
| JMSType | Message.Properties[“jms-type”] | - |

#### .NET-API von Service Bus zu JMS

| Service Bus .NET | JMS | Hinweise |
|-------------------------|------------------|-------------------------|
| ContentType | - | Derzeit nicht verfügbar | 
| CorrelationId | JMSCorrelationID | - |
| EnqueuedTimeUtc | JMSTimestamp | Conversion |
| Label | – | Derzeit nicht verfügbar |
| MessageId | JMSMessageID | - |
| ReplyTo | JMSReplyTo | - |
| ReplyToSessionId | – | Derzeit nicht verfügbar |
| ScheduledEnqueueTimeUtc | – | Derzeit nicht verfügbar |
| SessionId | – | Derzeit nicht verfügbar |
| TimeToLive | JMSExpiration | Conversion |
| To | JMSDestination | - |

## Nicht unterstützte Funktionen und Einschränkungen

Bei der Verwendung von JMS über AMQP 1.0 mit Service Bus gelten die folgenden Einschränkungen:

-   Pro Sitzung ist nur ein **MessageConsumer** oder **MessageProducer** erlaubt. Falls Sie mehrere **MessageProducer**- oder **MessageConsumer**-Objekte in einer Anwendung benötigen, müssen Sie für diese jeweils eine Sitzung erstellen.

-   Flüchtige Themenabonnements werden momentan nicht unterstützt.

-   **MessageSelector**-Objekte werden nicht unterstützt.

-   Temporäre Ziele, z. B. **TemporaryQueue** und **TemporaryTopic**, werden ebenso wie die **QueueRequestor**- und **TopicRequestor**-APIs, die diese verwenden, nicht unterstützt.

-   Von Transaktionen betroffene Sitzungen werden nicht unterstützt.

-   Verteilte Transaktionen werden nicht unterstützt.

## Nächste Schritte

Möchten Sie mehr erfahren? Nutzen Sie die folgenden Links:

- [Übersicht über Service Bus AMQP]
- [AMQP in Service Bus für Windows Server]

[AMQP in Service Bus für Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx

[Übersicht über Service Bus AMQP]: service-bus-amqp-overview.md
[klassischen Azure-Portal]: http://manage.windowsazure.com

<!---HONumber=AcomDC_0128_2016-->