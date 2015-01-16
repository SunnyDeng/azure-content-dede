<properties urlDisplayName="SendGrid Email Service" pageTitle="Verwenden des SendGrid-E-Mail-Dienstes (Java) - Azure"," metaKeywords="Azure SendGrid, Azure-E-Mail-Dienst, Azure SendGrid Java, Azure E-Mail Java" description="Erfahren Sie, wie Sie E-Mails mit dem SendGrid-E-Mail-Dienst in Azure senden. Die Codebeispiele wurden in Java geschrieben." metaCanonical="" services="" documentationCenter="Java" title="How to Send Email Using SendGrid from Java" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="10/30/2014" ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" />

# Senden von E-Mails mit SendGrid aus Java

Dieser Leitfaden veranschaulicht die Durchführung allgemeiner Programmierungsaufgaben mit dem SendGrid-E-Mail-Dienst in Azure. Die Beispiele sind in Java geschrieben. Die behandelten Szenarien umfassen das **Erstellen einer E-Mail**, **Senden einer E-Mail**, **Hinzufügen von Anhängen**, **Verwenden von Filtern** und **Aktualisieren von Eigenschaften**. Weitere Informationen zu SendGrid und zum Senden von E-Mails erhalten Sie im Abschnitt [Nächste Schritte][].

## Inhaltsverzeichnis

-   [Was ist der SendGrid-E-Mail-Dienst?][]
-   [SendGrid-Konto erstellen][]
-   [Gewusst wie: Verwenden der javax.mail-Bibliotheken][]
-   [Gewusst wie: Erstellen einer E-Mail][]
-   [Gewusst wie: Senden einer E-Mail][]
-   [Gewusst wie: Hinzufügen eines Anhangs][]
-   [Gewusst wie: Verwenden von Filtern zur Aktivierung von Fußzeilen sowie für Nachverfolgungen und Analysen][]
-   [Gewusst wie: Aktualisieren von E-Mail-Eigenschaften][]
-   [Gewusst wie: Verwenden zusätzlicher SendGrid-Dienste][]
-   [Nächste Schritte][]

## <a name="bkmk_WhatIsSendGrid"> </a>Was ist der SendGrid-E-Mail-Dienst?

SendGrid ist ein [cloudbasierter E-Mail-Dienst], der zuverlässige [transaktionale E-Mail-Übermittlung], Skalierbarkeit und Echtzeitanalysen mit flexiblen APIs bietet, die eine benutzerdefinierte Integration erleichtern. Häufige Verwendungsszenarien für SendGrid:

-   Automatisches Versenden von Empfangsbestätigungen an Kunden
-   Verwalten von Verteilerlisten, um Kunden monatlich
    E-Flyer und spezielle Angebote zu senden
-   Sammeln von Echtzeit-Kennzahlen für Sachverhalte wie gesperrte E-Mails und
    Kundenreaktionsfähigkeit
-   Generieren von Berichten zur Identifizierung von Trends
-   Weiterleiten von Kundenanfragen
- E-Mail-Benachrichtigungen von Ihrer Anwendung aus

Weitere Informationen finden Sie unter <http://sendgrid.com>.

## <a name="bkmk_CreateSendGridAcct"> </a>SendGrid-Konto erstellen

[WACOM.INCLUDE [SendGrid-Anmeldung](../includes/sendgrid-sign-up.md)]

## <a name="bkmk_HowToUseJavax"> </a>Gewusst wie: Verwenden der javax.mail-Bibliotheken

Rufen Sie die javax.mail-Bibliotheken z. B. von
<http://www.oracle.com/technetwork/java/javamail> ab und importieren Sie sie in
Ihren Code. Der Prozess zur Verwendung der javax.mail-Bibliothek
zum Senden von E-Mails mithilfe von SMTP sieht im Allgemeinen folgendermaßen aus:

1.  Geben Sie die SMTP-Werte an, einschließlich des SMTP-Servers, der für
    SendGrid smtp.sendgrid.net ist.

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

2.  Erweitern Sie die Klasse <span class="auto-style1">javax.mail.Authenticator</span>
    und geben Sie in der Implementierung der
    <span class="auto-style1">getPasswordAuthentication</span>-Methode
    Ihren SendGrid-Benutzernamen und das Kennwort zurück.  

        private class SMTPAuthenticator extends javax.mail.Authenticator {
        public PasswordAuthentication getPasswordAuthentication() {
           String username = SMTP_AUTH_USER;
           String password = SMTP_AUTH_PWD;
           return new PasswordAuthentication(username, password);
        }

3.  Erstellen Sie eine authentifizierte E-Mail-Sitzung über ein
    <span class="auto-style1">javax.mail.Session</span>-Objekt.  

        Authenticator auth = new SMTPAuthenticator();
        Session mailSession = Session.getDefaultInstance(properties, auth);

4.  Erstellen Sie Ihre Nachricht und weisen Sie **An**, **Von**, **Betreff** und
    "Inhalt" Werte zu. Dies wird im Abschnitt [Vorgehensweise: Erstellen einer E-Mail](#bkmk_HowToCreateEmail) dargestellt.
5.  Senden Sie die Nachricht über ein
    <span class="auto-style1">javax.mail.Transport</span>-Objekt. Dies
    wird im Abschnitt [Vorgehensweise: Senden einer E-Mail][How to: Send an Email]
    dargestellt.

## <a name="bkmk_HowToCreateEmail"> </a>Gewusst wie: Erstellen einer E-Mail

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

## <a name="bkmk_HowToSendEmail"> </a>Gewusst wie: Senden einer E-Mail

Im Folgenden ist dargestellt, wie Sie eine E-Mail versenden.

    Transport transport = mailSession.getTransport();
    // Connect the transport object.
    transport.connect();
    // Send the message.
    transport.sendMessage(message, message.getRecipients(Message.RecipientType.TO));
    // Close the connection.
    transport.close();

## <a name="bkmk_HowToAddAttachment"> </a>Gewusst wie: Hinzufügen eines Anhangs

Der folgende Code zeigt, wie Sie einen Anhang hinzufügen.

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

## <a name="bkmk_HowToUseFilters"> </a>Gewusst wie: Verwenden von Filtern zur Aktivierung von Fußzeilen sowie für Nachverfolgungen und Analysen

SendGrid bietet zusätzliche E-Mail-Funktionen durch die Verwendung von "Filtern". Hierbei handelt es sich um Einstellungen, die zu einer E-Mail-Nachricht hinzugefügt werden können, um spezifische Funktionen wie etwa Aktivierung von Klickprotokollierung, Google Analytics, Abonnement-Tracking usw. zu ermöglichen. Eine vollständige Liste der verfügbaren Filter finden Sie unter [Filtereinstellungen][].

-   Das folgende Beispiel zeigt, wie Sie einen Fußzeilenfilter einfügen können, sodass
    HTML-Text am unteren Rand der zu sendenden E-Mail angezeigt wird.

        message.addHeader("X-SMTPAPI", 
			"{\"filters\": 
			{\"footer\": 
			{\"settings\": 
        	{\"enable\":1,\"text/html\": 
			\"<html><b>Thank you</b> for your business.</html>\"}}}}");

-   Ein weiteres Beispiel für einen Filter ist die Klickprotokollierung. Angenommen, Ihr E-Mail-Text enthält einen Hyperlink, wie etwa den folgenden, und Sie möchten die Klickrate nachverfolgen: 

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

## <a name="bkmk_HowToUpdateEmail"> </a>Gewusst wie: Aktualisieren von E-Mail-Eigenschaften

Einige E-Mail-Eigenschaften können mithilfe von **set*Property*** überschrieben oder mit **add*Property*** angehängt werden.

Um z. B. die **ReplyTo**-Adressen anzugeben, können Sie folgenden Code verwenden:

    InternetAddress addresses[] = 
		{ new InternetAddress("john@contoso.com"),
          new InternetAddress("wendy@contoso.com") };
    
	message.setReplyTo(addresses);

Um einen **Cc**-Empfänger hinzuzufügen, können Sie folgenden Code verwenden:

    message.addRecipient(Message.RecipientType.CC, new 
    InternetAddress("john@contoso.com"));

## <a name="bkmk_HowToUseAdditionalSvcs"> </a>Gewusst wie: Verwenden zusätzlicher SendGrid-Dienste

SendGrid bietet webbasierte APIs, die Sie zur Nutzung zusätzlicher SendGrid-Funktionen aus Ihrer Azure-Anwendung einsetzen können. Ausführliche Informationen finden Sie in der [SendGrid-API-Dokumentation][].

## <a name="bkmk_NextSteps"> </a>Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen des SendGrid-E-Mail-Dienstes vertraut gemacht haben, finden Sie unter diesen Links weitere Informationen.

* Beispiel, das die Verwendung von SendGrid in einer Azure-Bereitstellung darstellt: [Senden von E-Mails mit SendGrid aus Java in einer Azure-Bereitstellung](../store-sendgrid-java-how-to-send-email-example/)
* SendGrid Java-SDK: <https://sendgrid.com/docs/Code_Examples/java.html>
* SendGrid-API-Dokumentation: <https://sendgrid.com/docs/API_Reference/index.html>
* Spezielles SendGrid-Angebot für Azure-Kunden: <https://sendgrid.com/windowsazure.html>

  [Nächste Schritte]: #bkmk_NextSteps
  [Was ist der SendGrid-E-Mail-Dienst?]: #bkmk_WhatIsSendGrid
  [Erstellen eines SendGrid-Kontos]: #bkmk_CreateSendGridAcct
  [Gewusst wie: Verwenden der javax.mail-Bibliotheken]: #bkmk_HowToUseJavax
  [Gewusst wie: Erstellen einer E-Mail]: #bkmk_HowToCreateEmail
  [Gewusst wie: Senden einer E-Mail]: #bkmk_HowToSendEmail
  [Gewusst wie: Hinzufügen eines Anhangs]: #bkmk_HowToAddAttachment
  [Gewusst wie: Verwenden von Filtern zur Aktivierung von Fußzeilen sowie für Nachverfolgungen und Analysen]: #bkmk_HowToUseFilters
  [Gewusst wie: Aktualisieren von E-Mail-Eigenschaften]: #bkmk_HowToUpdateEmail
  [Gewusst wie: Verwenden zusätzlicher SendGrid-Dienste]: #bkmk_HowToUseAdditionalSvcs
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
