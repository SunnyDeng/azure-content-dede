<properties linkid="dev-java-how-to-access-control" urlDisplayName="SendGrid Email Service" pageTitle="How to use the SendGrid email service (Java) - Azure" metaKeywords="Azure SendGrid, Azure email service, Azure SendGrid Java, Azure email Java" description="Learn how send email with the SendGrid email service on Azure. Code samples written in Java." metaCanonical="" services="" documentationCenter="Java" title="How to Send Email Using SendGrid from Java" authors="waltpo" solutions="" manager="" editor="mollybos" />

Senden von E-Mails mit SendGrid aus Java
========================================

Dieser Leitfaden veranschaulicht die Ausführung allgemeiner Programmierungsaufgaben mit dem E-Mail-Dienst SendGrid in Azure. Die Beispiele sind in Java geschrieben. Die behandelten Szenarien umfassen das **Erstellen einer E-Mail**, **Senden einer E-Mail**, **Hinzufügen von Anhängen**, **Verwenden von Filtern** und **Aktualisieren von Eigenschaften**. Weitere Informationen zu SendGrid und zum Senden von E-Mails erhalten Sie im Abschnitt [Nächste Schritte](#bkmk_NextSteps).

Inhaltsverzeichnis
------------------

-   [Was ist der SendGrid-E-Mail-Dienst?](#bkmk_WhatIsSendGrid)
-   [Erstellen eines SendGrid-Kontos](#bkmk_CreateSendGridAcct)
-   [Gewusst wie: Verwenden der javax.mail-Bibliotheken](#bkmk_HowToUseJavax)
-   [Gewusst wie: Erstellen einer E-Mail](#bkmk_HowToCreateEmail)
-   [Gewusst wie: Senden einer E-Mail](#bkmk_HowToSendEmail)
-   [Gewusst wie: Hinzufügen eines Anhangs](#bkmk_HowToAddAttachment)
-   [Gewusst wie: Verwenden von Filtern zur Aktivierung von Fußzeilen sowie für Nachverfolgungen und Analysen](#bkmk_HowToUseFilters)
-   [Gewusst wie: Aktualisieren von E-Mail-Eigenschaften](#bkmk_HowToUpdateEmail)
-   [Gewusst wie: Verwenden zusätzlicher SendGrid-Dienste](#bkmk_HowToUseAdditionalSvcs)
-   [Nächste Schritte](#bkmk_NextSteps)

Was ist der SendGrid-E-Mail-Dienst?
-----------------------------------

SendGrid ist ein [cloudbasierter E-Mail-Dienst](http://sendgrid.com/solutions), der zuverlässige [transaktionale E-Mail-Übermittlung](http://sendgrid.com/transactional-email), Skalierbarkeit und Echtzeitanalysen mit flexiblen APIs bietet, die die benutzerdefinierte Integration erleichtern. Häufige Verwendungsszenarien für SendGrid:

-   Automatisches Versenden von Empfangsbestätigungen an Kunden
-   Verwalten von Verteilungslisten zum Senden von monatlichen e-Flyern und speziellen Angeboten an Kunden
-   Sammeln von Echtzeit-Kennzahlen für Sachverhalte wie gesperrte E-Mails oder Kundenreaktionsfähigkeit
-   Generieren von Berichten zur Identifizierung von Trends
-   Weiterleiten von Kundenanfragen
-   E-Mail-Benachrichtigungen von Ihrer Anwendung aus

Weitere Informationen finden Sie unter &lt;http://sendgrid.com&gt;.

Erstellen eines SendGrid-Kontos
-------------------------------

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

Gewusst wie: Verwenden der javax.mail-Bibliotheken
--------------------------------------------------

Beschaffen Sie sich die javax.mail-Bibliotheken z. B. von &lt;http://www.oracle.com/technetwork/java/javamail&gt;, und importieren Sie diese in Ihren Code. Auf oberer Ebene dient der Prozess zur Verwendung der javax.mail-Bibliothek zum Senden von E-Mails per SMTP für Folgendes:

1.  Geben Sie die SMTP-Werte einschließlich des SMTP-Servers an, welcher bei SendGrid unter smtp.sendgrid.net geführt wird.

        public class MyEmailer {
           private static final String SMTP_HOST_NAME = "smtp.sendgrid.net";
           private static final String SMTP_AUTH_USER = "your_sendgrid_username";
           private static final String SMTP_AUTH_PWD = "your_sendgrid_password";
            
           public static void main(String[] args) throws Exception {
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

2.  Erweitern Sie die Klasse javax.mail.Authenticator. Geben Sie außerdem in der Implementierung der Methode getPasswordAuthentication den SendGrid-Benutzernamen und das -Kennwort an.

        private class SMTPAuthenticator extends javax.mail.Authenticator {
        public PasswordAuthentication getPasswordAuthentication() {
           String username = SMTP_AUTH_USER;
           String password = SMTP_AUTH_PWD;
           return new PasswordAuthentication(username, password);
        }

3.  Richten Sie eine authentifizierte E-Mail-Sitzung über ein javax.mail.Session-Objekt ein.

        Authenticator auth = new SMTPAuthenticator();
        Session mailSession = Session.getDefaultInstance(properties, auth);

4.  Erstellen Sie Ihre Nachricht, und ordnen Sie die Werte für **An**, **Von**, **Betreff** und die Inhalte zu. Dies wird im Abschnitt [Gewusst wie: Erstellen einer E-Mail](#bkmk_HowToCreateEmail) dargestellt.
5.  Versenden Sie eine Nachricht über ein javax.mail.Transport-Objekt. Dies wird im Abschnitt [Gewusst wie: Senden einer E-Mail](#bkmk_HowToSendEmail) dargestellt.

Gewusst wie: Erstellen einer E-Mail
-----------------------------------

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

Gewusst wie: Senden einer E-Mail
--------------------------------

Im Folgenden ist dargestellt, wie Sie eine E-Mail versenden.

    Transport transport = mailSession.getTransport();
    // Das Transportobjekt verbinden.
    transport.connect();
    // Die Nachricht senden.
    transport.sendMessage(message, message.getRecipients(Message.RecipientType.TO));
    // Die Verbindung schließen.
    transport.close();

Gewusst wie: Hinzufügen eines Anhangs
-------------------------------------

Der folgende Code zeigt, wie Sie einen Anhang hinzufügen.

    // Name und Pfad der lokalen Datei
    String attachmentName = "myfile.zip";
    String attachmentPath = "c:\myfiles\"; 
    MimeBodyPart attachmentPart = new MimeBodyPart();
    // Anzuhängende lokale Datei angeben.
    DataSource source = new FileDataSource(attachmentPath + attachmentName);
    attachmentPart.setDataHandler(new DataHandler(source));
    // In diesem Beispiel wird der Name der lokalen Datei als Anhangsname verwendet.
    // Die Namen können jedoch unterschiedlich sein, wenn Sie dies bevorzugen.
    attachmentPart.setFileName(attachmentName);
    multipart.addBodyPart(attachmentPart);

Gewusst wie: Verwenden von Filtern zur Aktivierung von Fußzeilen sowie für Nachverfolgungen und Analysen
--------------------------------------------------------------------------------------------------------

SendGrid bietet zusätzliche E-Mail-Funktionen durch die Verwendung von *Filtern*. Hierbei handelt es sich um Einstellungen, die zu einer E-Mail-Nachricht hinzugefügt werden können, um spezifische Funktionen wie etwa Aktivierung von Klickprotokollierung, Google Analytics, Abonnement-Tracking usw. zu ermöglichen. Eine vollständige Liste der verfügbaren Filter finden Sie unter [Filtereinstellungen](http://sendgrid.com/docs/API_Reference/Web_API/filter_settings.html).

-   Im Folgenden ist dargestellt, wie Sie einen Fußzeilen-Filter einfügen können. Dieser bewirkt, dass HTML-Text unten in einer zu versendenden E-Mail angezeigt wird.

        message.addHeader("X-SMTPAPI", 
            "{message.addHeader("X-SMTPAPI", 
            "{\"filters\": 
            {\"footer\": 
            {\"settings\": 
            {\"enable\":1,\"text/html\": 
            \"<html><b>Thank you</b> for your business.</html>\"}}}}");


-   Ein weiteres Beispiel für einen Filter ist die Klickprotokollierung. Angenommen, Ihr E-Mail-Text enthält einen Hyperlink wie etwa den folgenden, und Sie möchten die Klickrate nachverfolgen:

        messagePart.setContent(
            "Hello,
            <p>This is the body of the message. Visit 
            <a href='http://www.contoso.com'>http://www.contoso.com</a>.</p>
            Thank you.", 
            "text/html");

-   Verwenden Sie den folgenden Code, um die Klickprotokollierung zu aktivieren:

        message.addHeader("X-SMTPAPI", 
            "{message.addHeader("X-SMTPAPI", 
            "{\"filters\": 
            {\"clicktrack\": 
            {\"settings\": 
            {\"enable\":1}}}}");


Gewusst wie: Aktualisieren von E-Mail-Eigenschaften
---------------------------------------------------

Einige E-Mail-Eigenschaften können mit **set*Eigenschaft*** überschrieben oder mit **add*Eigenschaft*** angefügt werden.

Um z. B. die **ReplyTo**-Adressen anzugeben, können Sie folgenden Code verwenden:

    InternetAddress addresses[] = 
        { new InternetAddress("john@contoso.com"),
          new InternetAddress("wendy@contoso.com") };

    message.setReplyTo(addresses);

Um einen **Cc**-Empfänger hinzuzufügen, können Sie folgenden Code verwenden:

    message.addRecipient(Message.RecipientType.CC, new 
    InternetAddress("john@contoso.com"));

Gewusst wie: Verwenden zusätzlicher SendGrid-Dienste
----------------------------------------------------

SendGrid bietet webbasierte APIs, die Sie zur Nutzung zusätzlicher SendGrid-Funktionen in Ihrer Azure-Anwendung einsetzen können. Ausführliche Informationen finden Sie in der [SendGrid-API-Dokumentation](http://sendgrid.com/docs/API_Reference/index.html).

Nächste Schritte
----------------

Nachdem Sie nun mit den Grundlagen des SendGrid-E-Mail-Dienstes vertraut sind, finden Sie unter den folgenden Links weitere Informationen.

-   Beispiel, das die Verwendung von SendGrid in einer Azure-Bereitstellung darstellt: [Senden von E-Mails mit SendGrid aus Java in einer Azure-Bereitstellung](/en-us/develop/java/how-to-guides/sendgrid-sample/)
-   SendGrid Java-Informationen: &lt;http://sendgrid.com/docs/Code\_Examples/java.html&gt;
-   SendGrid-API-Dokumentation: &lt;http://sendgrid.com/docs/API\_Reference/index.html&gt;
-   Spezielles SendGrid-Angebot für Azure-Kunden: &lt;http://sendgrid.com/azure.html&gt;


