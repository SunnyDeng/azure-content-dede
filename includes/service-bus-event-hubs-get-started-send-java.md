## Senden von Nachrichten an Ereignis-Hubs
In diesem Abschnitt schreiben wir eine Java-Konsolenanwendung, um Ereignisse an den Ereignis-Hub zu senden. Wir nutzen den JMS AMQP-Anbieter aus dem [Apache Qpid-Projekt](http://qpid.apache.org/). Dies entspricht der Verwendung von Servicebuswarteschlangen und -Themen mit AMQP aus Java, wie [hier](http://azure.microsoft.com/documentation/articles/service-bus-java-how-to-use-jms-api-amqp/) gezeigt. Weitere Informationen finden Sie in der [Qpid JMS-Dokumentation](http://qpid.apache.org/releases/qpid-0.30/programming/book/QpidJMS.html) und [Java Messaging Service](http://www.oracle.com/technetwork/java/jms/index.html).

1. Erstellen Sie in Eclipse ein neues Java-Projekt namens **Sender**.

2. Laden Sie die neueste Version der Bibliothek **Qpid JMS AMQP 1.0** von [hier](http://qpid.apache.org/components/qpid-jms/index.html) herunter.

3. Extrahieren Sie die Dateien aus dem Archiv, und kopieren Sie die folgenden JAR-Dateien aus dem Archivverzeichnis `qpid-amqp-1-0-client-jms\<version>\lib` in Ihr Eclipse-Projekt **Sender**.

4. Klicken Sie in Eclipse Package Explorer mit der rechten Maustaste auf das Projekt **Sender**, und wählen Sie **Properties**. Klicken Sie im linken Bereich des Dialogfelds auf **Java Build Path**, klicken Sie dann auf die Registerkarte **Libraries** und anschließend auf die Schaltfläche **Add JARs**. Wählen Sie all zuvor kopierten JAR-Dateien aus, und klicken Sie auf **OK**.

	![][8]

5. Erstellen Sie im Stammverzeichnis des Projekts **Sender** eine Datei namens **servicebus.properties** mit folgendem Inhalt. Vergessen Sie nicht, den Wert für den Namen von Ereignis-Hub und Namespace (letzterer lautet in der Regel `{event hub name}-ns`) zu ersetzen. Sie müssen auch eine URL-codierte Version des Schlüssels für die zuvor erstellte **SendRule** einsetzen. Die URL-Codierung können Sie [hier](http://www.w3schools.com/tags/ref_urlencode.asp) vornehmen.

		# servicebus.properties - sample JNDI configuration

		# Register a ConnectionFactory in JNDI using the form:
		# connectionfactory.[jndi_name] = [ConnectionURL]
		connectionfactory.SBCF = amqps://SendRule:{Send Rule key}@{namespace name}.servicebus.windows.net/?sync-publish=false

		# Register some queues in JNDI using the form
		# queue.[jndi_name] = [physical_name]
		# topic.[jndi_name] = [physical_name]
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
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/


<!-- Images -->
[8]: ./media/service-bus-event-hubs-getstarted/create-sender-java1.png
<!--HONumber=47-->
