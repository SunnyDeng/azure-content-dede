## Senden von Nachrichten an Ereignis-Hubs
In diesem Abschnitt schreiben wir eine Java-Konsolenanwendung, um Ereignisse an den Ereignis-Hub zu senden. Wir verwenden den JMS AMQP-Anbieter aus dem [Apache Qpid-Projekt](http://qpid.apache.org/). Dies entspricht der Verwendung von Service Bus-Warteschlangen und -Themen mit AMQP über Java, wie [hier](../articles/service-bus-java-how-to-use-jms-api-amqp.md) beschrieben. Weitere Informationen finden Sie in der [Qpid JMS-Dokumentation](http://qpid.apache.org/releases/qpid-0.30/programming/book/QpidJMS.html) und unter [Java Messaging Service](http://www.oracle.com/technetwork/java/jms/index.html).

1. Erstellen Sie in Eclipse ein neues Java-Projekt namens **Sender**.

2. Laden Sie die neueste Version der Bibliothek **Qpid JMS AMQP 1.0** [hier](http://qpid.apache.org/components/qpid-jms/index.html) herunter.

3. Extrahieren Sie die Dateien aus dem Archiv, und kopieren Sie die folgenden JAR-Dateien aus dem Archivverzeichnis `qpid-amqp-1-0-client-jms\<version>\lib` in Ihr Eclipse-Projekt **Sender**.

4. Klicken Sie in Eclipse Package Explorer mit der rechten Maustaste auf das Projekt **Sender**, und wählen Sie **Properties**. Klicken Sie im linken Bereich des Dialogfelds auf **Java Build Path**, klicken Sie dann auf die Registerkarte **Libraries** und anschließend auf die Schaltfläche **Add JARs**. Wählen Sie all zuvor kopierten JAR-Dateien aus, und klicken Sie auf **OK**.

	![][8]

5. Erstellen Sie im Stammverzeichnis des Projekts **Sender** eine Datei namens **servicebus.properties** mit folgendem Inhalt. Vergessen Sie nicht, den Wert für den Namen von Ereignis-Hub und Namespace (letzterer lautet in der Regel `{event hub name}-ns`) zu ersetzen. Sie müssen auch eine URL-codierte Version des Schlüssels für die zuvor erstellte **SendRule** einsetzen. Die URL-Codierung können Sie [hier](http://www.w3schools.com/tags/ref_urlencode.asp) vornehmen.

		# servicebus.properties - JNDI-Beispielkonfiguration

		# Registrieren einer ConnectionFactory in JNDI in folgender Form:
		# connectionfactory.[jndi_name] = [Verbindungs-URL]
		connectionfactory.SBCF = amqps://SendRule:{Send Rule key}@{namespace name}.servicebus.windows.net/?sync-publish=false

		# Registrieren von Warteschlangen JNDI in folgender Form
		# queue.[jndi_name] = [physischer_Name]
		# topic.[jndi_name] = [physischer_Name]
		queue.EventHub = {event hub name}

5. Erstellen Sie eine neue Klasse namens **Sender**. Fügen Sie die folgenden `import`-Anweisungen ein:

		import java.io.BufferedReader;
		import java.io.IOException;
		import java.io.InputStreamReader;
		import java.io.UnsupportedEncodingException;
		import java.util.Hashtable;
		
		import javax.jms.BytesMessage;
		import javax.jms.Connection;
		import javax.jms.ConnectionFactory;
		import javax.jms.Destination;
		import javax.jms.JMSException;
		import javax.jms.MessageProducer;
		import javax.jms.Session;
		import javax.naming.Context;
		import javax.naming.InitialContext;
		import javax.naming.NamingException; 

8. Fügen Sie dann den folgenden Code hinzu:

		public static void main(String[] args) throws NamingException,
				JMSException, IOException, InterruptedException {
			// JNDI-Umgebung konfigurieren
			Hashtable<String, String> env = new Hashtable<String, String>();
			env.put(Context.INITIAL_CONTEXT_FACTORY,
					"org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory");
			env.put(Context.PROVIDER_URL, "servicebus.properties");
			Context context = new InitialContext(env);
	
			ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
	
			Destination queue = (Destination) context.lookup("EventHub");
	
			// Verbindung erstellen
			Connection connection = cf.createConnection();
	
			// Sitzung und MessageProducer senderseitig erstellen
			Session sendSession = connection.createSession(false,
					Session.AUTO_ACKNOWLEDGE);
			MessageProducer sender = sendSession.createProducer(queue);
	
			System.out.println("Drücken Sie STRG+C, um das Senden abzubrechen.");
			System.out.println("Drücken Sie zum Starten die EINGABETASTE.");
			BufferedReader commandLine = new java.io.BufferedReader(
					new InputStreamReader(System.in));
			commandLine.readLine();
	
			while (true) {
				sendBytesMessage(sendSession, sender);
				Thread.sleep(200);
			}
		}
		
		private static void sendBytesMessage(Session sendSession, MessageProducer sender) throws JMSException, UnsupportedEncodingException {
	        BytesMessage message = sendSession.createBytesMessage();
	        message.writeBytes("Test AMQP message from JMS".getBytes("UTF-8"));
	        sender.send(message);
	        System.out.println("Sent message");
	    }



<!-- Links -->
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/


<!-- Images -->
[8]: ./media/service-bus-event-hubs-getstarted/create-sender-java1.png


<!--HONumber=52--> 
