<properties urlDisplayName="SendGrid Email Service" pageTitle="Verwenden des E-Mail-Diensts SendGrid (Java) - Azure" metaKeywords="Azure SendGrid, Azure-E-Mail-Dienst, Azure SendGrid Java, Azure-E-Mail Java" description="Erfahren Sie, wie Sie E-Mails mit dem SendGrid-E-Mail-Dienst in Azure versenden. Die Codebeispiele sind in Java geschrieben." metaCanonical="" services="" documentationCenter="Java" title="How to Send Email Using SendGrid from Java" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="10/30/2014" ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" />

# Senden von E-Mails mit SendGrid aus Java

Dieser Leitfaden veranschaulicht die Ausführung allgemeiner Programmierungsaufgaben mit dem E-Mail-Dienst SendGrid in Azure. Die Beispiele sind in Java geschrieben. Die behandelten Szenarien umfassen das **Erstellen einer E-Mail**, **Senden einer E-Mail**, **Hinzufügen von Anhängen**, **Verwenden von Filtern** und **Aktualisieren von Eigenschaften**. Weitere Informationen zu SendGrid und zum Senden von E-Mails erhalten Sie im Abschnitt [Nächste Schritte][].

## Inhaltsverzeichnis

-   [Was ist der E-Mail-Dienst SendGrid?][]
-   [Erstellen eines SendGrid-Kontos][]
-   [Vorgehensweise: Verwenden der javax.mail-Bibliotheken][]
-   [Vorgehensweise: Erstellen einer E-Mail][]
-   [Vorgehensweise: Senden einer E-Mail][]
-   [Vorgehensweise: Hinzufügen einer Anlage][]
-   [Vorgehensweise: Verwenden von Filtern zur Aktivierung von Fußzeilen sowie für Nachverfolgungen und Analysen][]
-   [Vorgehensweise: Aktualisieren von E-Mail-Eigenschaften][]
-   [Vorgehensweise: Verwenden zusätzlicher SendGrid-Dienste][]
-   [Nächste Schritte][]

## <a name="bkmk_WhatIsSendGrid"> </a>Was ist der E-Mail-Dienst SendGrid?

SendGrid ist ein [cloudbasierter E-Mail-Dienst], der zuverlässige [transaktionale E-Mail-Übermittlung], Skalierbarkeit und Echtzeitanalysen mit flexiblen APIs bietet, die die benutzerdefinierte Integration erleichtern. Häufige Verwendungsszenarien für SendGrid:

-   Automatisches Versenden von Empfangsbestätigungen an Kunden
-   Verwalten von Verteilungslisten zum Senden von monatlichen e-Flyern und speziellen Angeboten an Kunden
-   Sammeln von Echtzeit-Kennzahlen für Sachverhalte wie gesperrte E-Mails oder Kundenreaktionsfähigkeit
-   Generieren von Berichten zur Identifizierung von Trends
-   Weiterleiten von Kundenanfragen
- E-Mail-Benachrichtigungen von Ihrer Anwendung aus

Weitere Informationen finden Sie unter <http://sendgrid.com>.

## <a name="bkmk_CreateSendGridAcct"> </a>SendGrid-Konto erstellen

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="bkmk_HowToUseJavax"> </a>Vorgehensweise: Verwenden der javax.mail-Bibliotheken

Rufen Sie die javax.mail-Bibliotheken z. B. unter <http://www.oracle.com/technetwork/java/javamail> ab, und importieren Sie diese in Ihren Code. Auf oberer Ebene dient der Prozess zur Verwendung der javax.mail-Bibliothek zum Senden von E-Mails per SMTP für Folgendes:

1.  Geben Sie die SMTP-Werte einschließlich des SMTP-Servers an. Für
    SendGrid ist dies "smtp.sendgrid.net".
    
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
           	  // ...

2.  Erweitern Sie die Klasse <span class="auto-style1">"javax.mail.Authenticator", und geben Sie</span>     in Ihrer Implementierung der Methode     <span class="auto-style1">"getPasswordAuthentication"</span> Ihren SendGrid-Benutzernamen und das Kennwort zurück.  

        private class SMTPAuthenticator extends javax.mail.Authenticator {
        public PasswordAuthentication getPasswordAuthentication() {
           String username = SMTP_AUTH_USER;
           String password = SMTP_AUTH_PWD;
           return new PasswordAuthentication(username, password);
        }

3.  Richten Sie eine authentifizierte E-Mail-Sitzung über ein
    <span class="auto-style1">javax.mail.Session-Objekt</span> .  

        Authenticator auth = new SMTPAuthenticator();
        Session mailSession = Session.getDefaultInstance(properties, auth);

4.  Erstellen Sie Ihre Nachricht, und weisen Sie die Werte für **To**, **From**, **Subject** und den Inhalt zu. Dies wird im Abschnitt [Vorgehensweise: Erstellen einer E-Mail](#bkmk_HowToCreateEmail) gezeigt.
5.  Senden Sie die Nachricht über ein     <span class="auto-style1">javax.mail.Transport-Objekt</span> . Dies wird im Abschnitt [Vorgehensweise: Senden einer E-Mail][How to: Send an Email] gezeigt.

## <a name="bkmk_HowToCreateEmail"> </a>Vorgehensweise: Erstellen einer E-Mail

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

## <a name="bkmk_HowToSendEmail"> </a>Vorgehensweise: Senden einer E-Mail

Im Folgenden ist dargestellt, wie Sie eine E-Mail versenden.

    Transport transport = mailSession.getTransport();
    // Connect the transport object.
    transport.connect();
    // Send the message.
    transport.sendMessage(message, message.getAllRecipients());
    // Close the connection.
    transport.close();

## <a name="bkmk_HowToAddAttachment"> </a>Vorgehensweise: Hinzufügen einer Anlage

Der folgende Code zeigt, wie Sie eine Anlage hinzufügen.

    // Local file name and path.
    String attachmentName = "myfile.zip";
    String attachmentPath = "c:\\myfiles\\"; 
    MimeBodyPart attachmentPart = new MimeBodyPart();
    // Specify the local file to attach.
    DataSource source = new FileDataSource(attachmentPath + attachmentName);
    attachmentPart.setDataHandler(new DataHandler(source));
    // This example uses the local file name as the attachment name.
    // They could be different if you prefer.
    attachmentPart.setFileName(attachmentName);
    multipart.addBodyPart(attachmentPart);

## <a name="bkmk_HowToUseFilters"> </a>Vorgehensweise: Verwenden von Filtern zur Aktivierung von Fußzeilen sowie für Nachverfolgungen und Analysen

SendGrid bietet zusätzliche E-Mail-Funktionen durch die Verwendung von *Filtern*. Hierbei handelt es sich um Einstellungen, die zu einer E-Mail-Nachricht hinzugefügt werden können, um spezifische Funktionen wie etwa Aktivierung von Klickprotokollierung, Google Analytics, Abonnement-Tracking usw. zu ermöglichen. Eine vollständige Liste der Filter finden Sie unter [Filtereinstellungen][].

-   Das folgende Beispiel zeigt, wie ein Fußzeilenfilter eingefügt wird, der bewirkt, dass
    HTML-Text unten in der E-Mail angezeigt wird, die gesendet wird.

        message.addHeader("X-SMTPAPI", 
			"{\"filters\": 
			{\"footer\": 
			{\"settings\": 
        	{\"enable\":1,\"text/html\": 
			\"<html><b>Thank you</b> for your business.</html>\"}}}}");

-   Ein weiteres Beispiel für einen Filter ist die Klickprotokollierung. Angenommen, Ihr E-Mail-Text enthält einen Link (wie etwa den folgenden), und Sie möchten die Klickrate nachverfolgen:

        messagePart.setContent(
			"Hello,
			<p>This is the body of the message. Visit 
			<a href='http://www.contoso.com'>http://www.contoso.com</a>.</p>
			Thank you.", 
        	"text/html");

-   To enable the click tracking, use the following code:

        message.addHeader("X-SMTPAPI", 
			"{\"filters\": 
			{\"clicktrack\": 
			{\"settings\": 
        	{\"enable\":1}}}}");

## <a name="bkmk_HowToUpdateEmail"> </a>Vorgehensweise: Aktualisieren von E-Mail-Eigenschaften

Einige E-Mail-Eigenschaften können mithilfe von **set*Property*** überschrieben oder mithilfe von **add*Property*** angefügt werden.

Um z. B. die **ReplyTo**-Adressen anzugeben, können Sie folgenden Code verwenden:

    InternetAddress addresses[] = 
		{ new InternetAddress("john@contoso.com"),
          new InternetAddress("wendy@contoso.com") };
    
	message.setReplyTo(addresses);

Um einen **Cc**-Empfänger hinzuzufügen, können Sie folgenden Code verwenden:

    message.addRecipient(Message.RecipientType.CC, new 
    InternetAddress("john@contoso.com"));

## <a name="bkmk_HowToUseAdditionalSvcs"> </a>Vorgehensweise: Verwenden zusätzlicher SendGrid-Dienste

SendGrid bietet webbasierte APIs, mit denen Sie zusätzliche
SendGrid-Funktionen aus Ihrer Azure-Anwendung nutzen können. Ausführliche
Informationen finden Sie in der [Dokumentation zur SendGrid-API][].

## <a name="bkmk_NextSteps"> </a>Nächste Schritte

Nachdem Sie nun mit den Grundlagen des E-Mail-Diensts SendGrid vertraut sind, finden Sie unter
diesen Links weitere Informationen.

* Beispiel, das die Verwendung von SendGrid in einer Azure-Bereitstellung darstellt: [Senden von E-Mails mit SendGrid aus Java in einer Azure-Bereitstellung](store-sendgrid-java-how-to-send-email-example.md)
* SendGrid Java-SDK: <https://sendgrid.com/docs/Code_Examples/java.html>
* Dokumentation zur SendGrid-API: <https://sendgrid.com/docs/API_Reference/index.html>
* Spezielles SendGrid-Angebot für Azure-Kunden: <https://sendgrid.com/windowsazure.html>

  [Nächste Schritte]: #bkmk_NextSteps
  [Was ist der E-Mail-Dienst SendGrid?]: #bkmk_WhatIsSendGrid
  [Erstellen eines SendGrid-Kontos]: #bkmk_CreateSendGridAcct
  [Vorgehensweise: Verwenden der javax.mail-Bibliotheken]: #bkmk_HowToUseJavax
  [Vorgehensweise: Erstellen einer E-Mail]: #bkmk_HowToCreateEmail
  [Vorgehensweise: Senden einer E-Mail]: #bkmk_HowToSendEmail
  [How to: Send an Email]: #bkmk_HowToSendEmail
  [Vorgehensweise: Hinzufügen einer Anlage]: #bkmk_HowToAddAttachment
  [Vorgehensweise: Verwenden von Filtern zur Aktivierung von Fußzeilen sowie für Nachverfolgungen und Analysen]: #bkmk_HowToUseFilters
  [Vorgehensweise: Aktualisieren von E-Mail-Eigenschaften]: #bkmk_HowToUpdateEmail
  [Vorgehensweise: Verwenden zusätzlicher SendGrid-Dienste]: #bkmk_HowToUseAdditionalSvcs
  [http://sendgrid.com]: https://sendgrid.com
  [http://sendgrid.com/pricing.html]: http://sendgrid.com/pricing.html
  [http://www.sendgrid.com/azure.html]: https://www.sendgrid.com/windowsazure.html
  [http://sendgrid.com/features]: https://sendgrid.com/features
  [http://www.oracle.com/technetwork/java/javamail]: http://www.oracle.com/technetwork/java/javamail/index.html
  [Filtereinstellungen]: https://sendgrid.com/docs/API_Reference/Web_API/filter_settings.html
  [Dokumentation zur SendGrid-API]: https://sendgrid.com/docs/API_Reference/index.html
  [http://sendgrid.com/azure.html]: https://sendgrid.com/windowsazure.html
  [cloudbasierter E-Mail-Dienst]: https://sendgrid.com/email-solutions
  [transaktionale E-Mail-Übermittlung]: https://sendgrid.com/transactional-email

<!--HONumber=35.2-->
