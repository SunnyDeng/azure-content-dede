## Senden von Nachrichten an Ereignis-Hubs
In diesem Abschnitt schreiben wir eine Java-Konsolenanwendung, um Ereignisse an den Ereignis-Hub zu senden. Wir verwenden den JMS AMQP-Anbieter aus dem [Apache Qpid-Projekt](http://qpid.apache.org/). Dies entspricht der Verwendung von Service Bus-Warteschlangen und -Themen mit AMQP über Java, wie [hier](../articles/service-bus/service-bus-java-how-to-use-jms-api-amqp.md) beschrieben. Weitere Informationen finden Sie in der [Qpid JMS-Dokumentation](http://qpid.apache.org/releases/qpid-0.30/programming/book/QpidJMS.html) und unter [Java Messaging Service](http://www.oracle.com/technetwork/java/jms/index.html).

1. Installieren Sie in Eclipse die [Azure Toolkit für Eclipse](https://msdn.microsoft.com/library/azure/hh690946.aspx). Diese beinhaltet die Qpid JMS AMQP-Clientbibliotheken.

2. Erstellen Sie in Eclipse ein neues Java-Projekt namens **Sender**.

3. Klicken Sie in "Eclipse Package Explorer" mit der rechten Maustaste auf das Projekt **Sender**, und wählen Sie **Properties** aus. Klicken Sie im linken Bereich des Dialogfelds auf **Java Build Path**. Klicken Sie dann auf die Registerkarte **Libraries** und anschließend auf die Schaltfläche **Add Library**. Wählen Sie **Package for Apache Qpid Client Libraries for JMS (von MS Open Tech)**. Klicken Sie auf **Next**, dann auf **Finish**.

	![][8]

4. Erstellen Sie im Stammverzeichnis des Projekts **Sender** eine Datei namens **servicebus.properties** mit folgendem Inhalt. Vergessen Sie nicht das Ersetzen der Werte für:
	- den Ereignis-Hub-Namen.
	- den Namespace-Namen (dieser lautet normalerweise `{event hub name}-ns`).
	- den URL-codierten **SendRule**-Schlüssel (diesen haben Sie sich bei der Erstellung des Ereignis-Hubs notiert). Die URL-Codierung können Sie [hier](http://www.w3schools.com/tags/ref_urlencode.asp) vornehmen.

			# servicebus.properties - sample JNDI configuration

			# Register a ConnectionFactory in JNDI using the form:
			# connectionfactory.[jndi_name] = [ConnectionURL]
			connectionfactory.SBCF = amqps://SendRule:{Send Rule key}@{namespace name}.servicebus.windows.net/?sync-publish=false

			# Register some queues in JNDI using the form
			# queue.[jndi_name] = [physical_name]
			# topic.[jndi_name] = [physical_name]
			queue.EventHub = {event hub name}

5. Erstellen Sie eine neue Klasse mit dem Namen **Sender**. Fügen Sie die folgenden `import`-Anweisungen ein:

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

6. Fügen Sie dann den folgenden Code hinzu:

		public static void main(String[] args) throws NamingException,
				JMSException, IOException, InterruptedException {
			// Configure JNDI environment
			Hashtable<String, String> env = new Hashtable<String, String>();
			env.put(Context.INITIAL_CONTEXT_FACTORY,
					"org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory");
			env.put(Context.PROVIDER_URL, "servicebus.properties");
			Context context = new InitialContext(env);

			ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");

			Destination queue = (Destination) context.lookup("EventHub");

			// Create Connection
			Connection connection = cf.createConnection();

			// Create sender-side Session and MessageProducer
			Session sendSession = connection.createSession(false,
					Session.AUTO_ACKNOWLEDGE);
			MessageProducer sender = sendSession.createProducer(queue);

			System.out.println("Press Ctrl-C to stop the sender process");
			System.out.println("Press Enter to start now");
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
[Azure Management Portal]: https://manage.windowsazure.com/


<!-- Images -->
[8]: ./media/service-bus-event-hubs-getstarted/create-sender-java1.png

<!---HONumber=July15_HO1-->