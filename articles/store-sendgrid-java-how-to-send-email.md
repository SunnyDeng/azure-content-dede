<properties 
	pageTitle="Verwenden des SendGrid-E-Mail-Diensts (Java) – Azure" 
	description="Erfahren Sie, wie Sie E-Mails mit dem SendGrid-E-Mail-Dienst in Azure senden. Die Codebeispiele wurden in Java geschrieben." 
	services="" 
	documentationCenter="java" 
	authors="thinkingserious" 
	manager="sendgrid" 
	editor="mollybos"/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="10/30/2014" 
	ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork"/>

# Senden von E-Mails mit SendGrid aus Java

Dieser Leitfaden veranschaulicht die Ausführung allgemeiner Programmierungsaufgaben mit dem E-Mail-Dienst SendGrid in Azure. Die Beispiele sind in Java geschrieben. Die behandelten Szenarien umfassen das **Erstellen einer E-Mail**, **Senden einer E-Mail**, **Hinzufügen von Anhängen**, **Verwenden von Filtern** und **Aktualisieren von Eigenschaften**. Weitere Informationen zu SendGrid und zum Senden von E-Mails erhalten Sie im Abschnitt [Nächste Schritte][].

## Inhaltsverzeichnis

-   [Was ist der SendGrid-E-Mail-Dienst?][]
-   [Erstellen von SendGrid-Konten][]
-   [Verwenden der javax.mail-Bibliotheken][]
-   [Erstellen einer E-Mail][]
-   [Senden von E-Mails][]
-   [Hinzufügen von Anlagen][]
-   [Verwenden von Filtern zur Aktivierung von Fußzeilen sowie für Nachverfolgungen und Analysen][]
-   [Aktualisieren von E-Mail-Eigenschaften][]
-   [Verwenden zusätzlicher SendGrid-Dienste][]
-   [Nächste Schritte][]

## <a name="bkmk_WhatIsSendGrid"> </a>Was ist der SendGrid-E-Mail-Dienst?

SendGrid ist ein [cloudbasierter E-Mail-Dienst], der zuverlässige [transaktionale E-Mail-Übermittlung], Skalierbarkeit und Echtzeitanalysen mit flexiblen APIs bietet, die die benutzerdefinierte Integration erleichtern. Häufige Verwendungsszenarien für SendGrid:

-   Automatisches Versenden von Empfangsbestätigungen an Kunden
-   Verwalten von Verteilungslisten zum Senden von monatlichen e-Flyern und speziellen Angeboten an Kunden
-   Sammeln von Echtzeit-Kennzahlen für Sachverhalte wie gesperrte E-Mails oder Kundenreaktionsfähigkeit
-   Generieren von Berichten zur Identifizierung von Trends
-   Weiterleiten von Kundenanfragen
- E-Mail-Benachrichtigungen von Ihrer Anwendung aus

Weitere Informationen finden Sie unter <http://sendgrid.com>.

## <a name="bkmk_CreateSendGridAcct"> </a>Erstellen von SendGrid-Konten

[AZURE.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="bkmk_HowToUseJavax"> </a>Verwenden der javax.mail-Bibliotheken

Rufen Sie die javax.mail-Bibliotheken z. B. unter <http://www.oracle.com/technetwork/java/javamail> ab, und importieren Sie diese in Ihren Code. Auf oberer Ebene dient der Prozess zur Verwendung der javax.mail-Bibliothek zum Senden von E-Mails per SMTP für Folgendes:

1.  Geben Sie die SMTP-Werte einschließlich des SMTP-Servers an, welcher bei SendGrid unter smtp.sendgrid.net geführt wird.
    
        import java.util.Properties;
        import javax.activation.*;
        import javax.mail.*;
        import javax.mail.internet.*;

        public class MyEmailer {
	       private static final String SMTP_HOST_NAME = "smtp.sendgrid.net";
	       private static final String SMTP_AUTH_USER = "your_sendgrid_username";
           private static final String SMTP_AUTH_PWD = "your_sendgrid_password";
        
		   public static void main(String[] args) throws Exception{
         	  new MyEmailer().SendMail();
           }
        
		   public void SendMail() throws Exception
           {
              Properties properties = new Properties();
           	  properties.put("mail.transport.protocol", "smtp");
           	  properties.put("mail.smtp.host", SMTP_HOST_NAME);
           	  properties.put("mail.smtp.port", 587);
           	  properties.put("mail.smtp.auth", "true");
           	  // …

2.  Erweitern Sie die Klasse <span class="auto-style1">javax.mail.Authenticator</span>. Geben Sie außerdem in der Implementierung der Methode <span class="auto-style1">getPasswordAuthentication</span> den SendGrid-Benutzernamen und das dazugehörige Kennwort an.

        private class SMTPAuthenticator extends javax.mail.Authenticator {
        public PasswordAuthentication getPasswordAuthentication() {
           String username = SMTP_AUTH_USER;
           String password = SMTP_AUTH_PWD;
           return new PasswordAuthentication(username, password);
        }

3.  Richten Sie eine authentifizierte E-Mail-Sitzung über ein Objekt vom Typ <span class="auto-style1">javax.mail.Session</span> ein.

        Authenticator auth = new SMTPAuthenticator();
        Session mailSession = Session.getDefaultInstance(properties, auth);

4.  Erstellen Sie Ihre Nachricht, und ordnen Sie die Werte für **An**, **Von**, **Betreff** und die Inhalte zu. Die entsprechende Vorgehensweise wird im Abschnitt [Erstellen einer E-Mail](#bkmk_HowToCreateEmail) erläutert.
5.  Senden Sie die Nachricht über ein Objekt vom Typ <span class="auto-style1">javax.mail.Transport</span>. Die entsprechende Vorgehensweise wird im Abschnitt [Senden von E-Mails][How to: Send an Email] erläutert.

## <a name="bkmk_HowToCreateEmail"> </a>Erstellen einer E-Mail

Im Folgenden ist dargestellt, wie Sie Werte für eine E-Mail angeben.

    MimeMessage message = new MimeMessage(mailSession);
    Multipart multipart = new MimeMultipart("alternative");
    BodyPart part1 = new MimeBodyPart();
    part1.setText("Hello, Your Contoso order has shipped. Thank you, John");
    BodyPart part2 = new MimeBodyPart();
    part2.setContent(
		"<p>Hello,</p>
		<p>Your Contoso order has <b>shipped</b>.</p>
		<p>Thank you,<br>John</br></p>",
		"text/html");
    multipart.addBodyPart(part1);
    multipart.addBodyPart(part2);
    message.setFrom(new InternetAddress("john@contoso.com"));
    message.addRecipient(Message.RecipientType.TO,
       new InternetAddress("someone@example.com"));
    message.setSubject("Your recent order");
    message.setContent(multipart);

## <a name="bkmk_HowToSendEmail"> </a>Senden von E-Mails

Im Folgenden ist dargestellt, wie Sie eine E-Mail versenden.

    Transport transport = mailSession.getTransport();
    // Connect the transport object.
    transport.connect();
    // Send the message.
    transport.sendMessage(message, message.getAllRecipients());
    // Close the connection.
    transport.close();

## <a name="bkmk_HowToAddAttachment"> </a>Hinzufügen von Anlagen

Der folgende Code zeigt, wie Sie eine Anlage hinzufügen.

    // Local file name and path.
    String attachmentName = "myfile.zip";
    String attachmentPath = "c:\\myfiles\"; 
    MimeBodyPart attachmentPart = new MimeBodyPart();
    // Specify the local file to attach.
    DataSource source = new FileDataSource(attachmentPath + attachmentName);
    attachmentPart.setDataHandler(new DataHandler(source));
    // This example uses the local file name as the attachment name.
    // They could be different if you prefer.
    attachmentPart.setFileName(attachmentName);
    multipart.addBodyPart(attachmentPart);

## <a name="bkmk_HowToUseFilters"> </a>Verwenden von Filtern zur Aktivierung von Fußzeilen sowie für Nachverfolgungen und Analysen

SendGrid bietet zusätzliche E-Mail-Funktionen durch die Verwendung von *Filtern*. Hierbei handelt es sich um Einstellungen, die zu einer E-Mail-Nachricht hinzugefügt werden können, um spezifische Funktionen wie etwa Aktivierung von Klickprotokollierung, Google Analytics, Abonnement-Tracking usw. zu ermöglichen. Eine vollständige Liste der verfügbaren Filter finden Sie unter [Filtereinstellungen][].

-   Im Folgenden ist dargestellt, wie Sie einen Fußzeilen-Filter einfügen können. Dieser bewirkt, dass HTML-Text unten in einer zu versendenden E-Mail angezeigt wird.

        message.addHeader("X-SMTPAPI", 
			"{"filters": 
			{"footer": 
			{"settings": 
        	{"enable":1,"text/html": 
			"<html><b>Thank you</b> for your business.</html>"}}}}");

-   Ein weiteres Beispiel für einen Filter ist die Klickprotokollierung. Angenommen, Ihr E-Mail-Text enthält einen Link (wie etwa den folgenden), und Sie möchten die Klickrate nachverfolgen:

        messagePart.setContent(
			"Hello,
			<p>This is the body of the message. Visit 
			<a href='http://www.contoso.com'>http://www.contoso.com</a>.</p>
			Thank you.", 
        	"text/html");

-   Verwenden Sie den folgenden Code, um die Klickprotokollierung zu aktivieren:

        message.addHeader("X-SMTPAPI", 
			"{"filters": 
			{"clicktrack": 
			{"settings": 
        	{"enable":1}}}}");

## <a name="bkmk_HowToUpdateEmail"> </a>Aktualisieren von E-Mail-Eigenschaften

Einige E-Mail-Eigenschaften können mithilfe von **set*Property*** überschrieben oder mithilfe von **add*Property*** angefügt werden.

Um z. B. die **ReplyTo**-Adressen anzugeben, können Sie folgenden Code verwenden:

    InternetAddress addresses[] = 
		{ new InternetAddress("john@contoso.com"),
          new InternetAddress("wendy@contoso.com") };
    
	message.setReplyTo(addresses);

Um einen **Cc**-Empfänger hinzuzufügen, können Sie folgenden Code verwenden:

    message.addRecipient(Message.RecipientType.CC, new 
    InternetAddress("john@contoso.com"));

## <a name="bkmk_HowToUseAdditionalSvcs"> </a>Verwenden zusätzlicher SendGrid-Dienste

SendGrid bietet webbasierte APIs, die Sie zur Nutzung zusätzlicher SendGrid-Funktionen aus Ihrer Azure-Anwendung einsetzen können. Ausführliche Informationen finden Sie in der [SendGrid-API-Dokumentation][].

## <a name="bkmk_NextSteps"> </a>Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen des SendGrid-E-Mail-Dienstes vertraut gemacht haben, finden Sie unter diesen Links weitere Informationen.

* Ein Beispiel, das die Verwendung von SendGrid in einer Azure-Bereitstellung darstellt finden Sie unter [Senden von E-Mails mit SendGrid aus Java in einer Azure-Bereitstellung](store-sendgrid-java-how-to-send-email-example.md).
* SendGrid-Java-SDK: <https://sendgrid.com/docs/Code_Examples/java.html>
* SendGrid API-Dokumentation: <https://sendgrid.com/docs/API_Reference/index.html>
* Spezielles SendGrid-Angebot für Azure-Kunden: <https://sendgrid.com/windowsazure.html>

  [Nächste Schritte]: #bkmk_NextSteps
  [Was ist der SendGrid-E-Mail-Dienst?]: #bkmk_WhatIsSendGrid
  [Erstellen von SendGrid-Konten]: #bkmk_CreateSendGridAcct
  [Verwenden der javax.mail-Bibliotheken]: #bkmk_HowToUseJavax
  [Erstellen einer E-Mail]: #bkmk_HowToCreateEmail
  [How to: Send an Email]: #bkmk_HowToSendEmail
  [Senden von E-Mails]: #bkmk_HowToSendEmail
  [Hinzufügen von Anlagen]: #bkmk_HowToAddAttachment
  [Verwenden von Filtern zur Aktivierung von Fußzeilen sowie für Nachverfolgungen und Analysen]: #bkmk_HowToUseFilters
  [Aktualisieren von E-Mail-Eigenschaften]: #bkmk_HowToUpdateEmail
  [Verwenden zusätzlicher SendGrid-Dienste]: #bkmk_HowToUseAdditionalSvcs
  [http://sendgrid.com]: https://sendgrid.com
  [http://sendgrid.com/pricing.html]: http://sendgrid.com/pricing.html
  [http://www.sendgrid.com/azure.html]: https://www.sendgrid.com/windowsazure.html
  [http://sendgrid.com/features]: https://sendgrid.com/features
  [http://www.oracle.com/technetwork/java/javamail]: http://www.oracle.com/technetwork/java/javamail/index.html
  [Filtereinstellungen]: https://sendgrid.com/docs/API_Reference/Web_API/filter_settings.html
  [SendGrid-API-Dokumentation]: https://sendgrid.com/docs/API_Reference/index.html
  [http://sendgrid.com/azure.html]: https://sendgrid.com/windowsazure.html
  [cloudbasierter E-Mail-Dienst]: https://sendgrid.com/email-solutions
  [transaktionale E-Mail-Übermittlung]: https://sendgrid.com/transactional-email

<!---HONumber=August15_HO6-->