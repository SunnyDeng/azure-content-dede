<properties urlDisplayName="SendGrid Email Service" pageTitle="Gewusst wie: Verwenden des E-Mail-Diensts SendGrid (.NET) - Azure" metaKeywords="Azure SendGrid, Azure email service, Azure SendGrid .NET, Azure email .NET, Azure SendGrid C#, Azure email C#" description="Learn how send email with the SendGrid email service on Azure. Code samples written in C# and use the .NET API." metaCanonical="" services="" documentationCenter=".NET" title="How to Send Email Using SendGrid with Azure" authors="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" solutions="" manager="wpickett" editor="erikre" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/24/2014" ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" />





# Senden von E-Mails in Azure mit SendGrid

Letzte Aktualisierung: 24. Oktober 2014

Dieser Leitfaden veranschaulicht die Ausführung allgemeiner Programmierungsaufgaben mit dem E-Mail-Dienst SendGrid in Azure. Die Beispiele sind in C\# geschrieben und verwenden die .NET API. Beschrieben werden die Szenarien **Schreiber einer E-Mail**, **Senden einer E-Mail**, **Hinzufügen von Anhängen** und **Verwenden von Filtern**. Weitere Informationen zu SendGrid und zum Senden von E-Mails erhalten Sie im Abschnitt [Nächste Schritte][].

<h2><a name="toc"></a>Inhaltsverzeichnis</h2>

[Was ist der SendGrid-E-Mail-Dienst?][]   
[Erstellen eines SendGrid-Kontos][] 
[Verweisen auf die SendGrid .NET-Klassenbibliothek][] 
[Gewusst wie: Erstellen einer E-Mail][] 
[Gewusst wie: Senden einer E-Mail][] 
[Gewusst wie: Hinzufügen eines Anhangs][] 
[Gewusst wie: Verwenden von Filtern zur Aktivierung von Fußzeilen sowie für Nachverfolgungen und Analysen][] 
[Gewusst wie: Verwenden zusätzlicher SendGrid-Dienste][] 
[Nächste Schritte][]

<h2><a name="whatis"></a>Was ist der SendGrid-E-Mail-Dienst?</h2>

SendGrid ist ein [cloudbasierter E-Mail-Dienst], der zuverlässige
[transaktionale E-Mail-Übermittlung], Skalierbarkeit und Echtzeitanalysen mit flexiblen APIs bietet,
die benutzerdefinierte Integration vereinfachen. Häufige Verwendungsszenarien für SendGrid
umfassen:

-   Automatisches Senden von Empfangsnachrichten an Kunden.
-   Verwalten von Verteilungslisten zum Senden von monatlichen
    E-Flyern und speziellen Angeboten an Kunden.
-   Sammeln von Echtzeit-Kennzahlen für Sachverhalte wie gesperrte E-Mails oder
    Kundenreaktionen.
-   Erstellung von Berichten zur leichteren Identifikation von Trends.
-   Weiterleitung von Kundenanfragen.

Weitere Informationen finden Sie unter [https://sendgrid.com](https://sendgrid.com).

<h2><a name="createaccount"></a>SendGrid-Konto erstellen</h2>

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

<h2><a name="reference"></a>Verweisen auf die SendGrid .NET-Klassenbibliothek</h2>

Der einfachste Weg, die SendGrid-API abzurufen und Ihre Anwendung mit allen Abhängigkeiten zu konfigurieren, ist das SendGrid NuGet-Paket (https://www.nuget.org/packages/Sendgrid). NuGet ist eine Erweiterung von Microsoft Visual Studio 2012, die die Installation und die Aktualisierung von Bibliotheken und Tools erleichtert. 

<div class="dev-callout">
<b>Hinweis</b>
<p>Wenn
Sie NuGet installieren möchten und eine ältere Visual Studio-Version vor Visual Studio 2012 verwenden, besuchen Sie <a href="http://www.nuget.org">http://www.nuget.org</a>, und klicken Sie auf die Schaltfläche <b>Install
NuGet</b>.</p>
</div>

Gehen Sie folgendermaßen vor, um das SendGrid NuGet-Paket in Ihrer Anwendung zu installieren:

1.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Verweise**, und klicken Sie dann auf
    **NuGet-Pakete verwalten**.

2.  Klicken Sie im linken Bereich des Dialogfelds **NuGet-Pakete verwalten** auf **Online**.

3.  Suchen Sie nach **SendGrid**, und wählen Sie das Element **SendGrid** in der
    Liste der Suchergebnisse aus.

    ![SendGrid NuGet package][SendGrid-NuGet-package]

4.  Klicken Sie auf **Installieren**, um die Installation abzuschließen, und schließen Sie dann das
    Dialogfeld.

Die .NET-Klassenbibliothek für SendGrid heißt **SendGridMail**. Sie enthält
die folgenden Namespaces:

-   **SendGridMail** zur Erstellung und Bearbeitung von E-Mail-Elementen.
-   **SendGridMail.Transport** zum Senden von E-Mails entweder mit dem
    **SMTP**-Protokoll oder dem HTTP 1.1-Protokoll mit **Web/REST**.

Fügen Sie zu Beginn aller C#-Dateien, in denen Sie programmgesteuert auf den E-Mail-Dienst SendGrid zugreifen möchten, die folgenden Namespace-Codedeklarationen hinzu: **System.Net** und **System.Net.Mail** sind .NET Framework-Namespaces, die enthalten sind, weil sie Typen enthalten, die üblicherweise mit den SendGrid-APIs verwendet werden.

    using System;
    using System.Net;
    using System.Net.Mail;
    using SendGrid;

<h2><a name="createemail"></a>Gewusst wie: Erstellen einer E-Mail</h2>

Verwenden Sie die statische **SendGrid.GetInstance**-Methode, um eine E-Mail des Typs **SendGrid** zu erstellen. Wenn die Nachricht erstellt ist, können Sie die Eigenschaften und Verfahren von **SendGrid** verwenden, um Werte wie den E-Mail-Absender, den E-Mail-Empfänger, den Betreff und den Text der E-Mail festzulegen.

Das folgende Beispiel zeigt, wie ein vollständig ausgefülltes E-Mail-Objekt erstellt wird:

    // Create the email object first, then add the properties.
    var myMessage = new SendGridMessage();

    // Add the message properties.
    myMessage.From = new MailAddress("john@example.com");

    // Add multiple addresses to the To field.
    List<String> recipients = new List<String>
    {
        @"Jeff Smith <jeff@example.com>",
        @"Anna Lidman <anna@example.com>",
        @"Peter Saddow <peter@example.com>"
    };

    myMessage.AddTo(recipients);

    myMessage.Subject = "Testing the SendGrid Library";

    //Add the HTML and Text bodies
    myMessage.Html = "<p>Hello World!</p>";
    myMessage.Text = "Hello World plain text!";

FWeitere Informationen über die vom Typ **SendGrid** unterstützen Eigenschaften und Methoden finden Sie unter [sendgrid-csharp][] auf GitHub.

<h2><a name="sendemail"></a>Gewusst wie: Senden einer E-Mail</h2>

Nach der Erstellung einer E-Mail können Sie diese über die von SendGrid bereitgestellte Web-API senden. Alternativ können Sie die integrierte .NET-Bibliothek](https://sendgrid.com/docs/Code_Examples/csharp.html) verwenden.

Das Senden einer E-Mail erfordert die Angabe der Anmeldeinformationen für Ihr SendGrid-Konto (Benutzername und Kennwort). Der folgende Code veranschaulicht, wie Sie Ihre Anmeldeinformationen in einem **NetworkCredential**-Objekt verpacken:
    
    // Create network credentials to access your SendGrid account
    var username = "your_sendgrid_username";
    var pswd = "your_sendgrid_password";

    /* Alternatively, you may store these credentials via your Azure portal
       by clicking CONFIGURE and adding the key/value pairs under "app settings".
       Then, you may access them as follows: 
       var username = System.Environment.GetEnvironmentVariable("SENDGRID_USER"); 
       var pswd = System.Environment.GetEnvironmentVariable("SENDGRID_PASS");
       assuming you named your keys SENDGRID_USER and SENDGRID_PASS */

    var credentials = new NetworkCredential(username, pswd);

The following examples show how to send a message using the Web API.

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Create credentials, specifying your user name and password.
    var credentials = new NetworkCredential("username", "password");

    // Create an Web transport for sending email.
    var transportWeb = new Web(credentials);

    // Send the email.
    // You can also use the **DeliverAsync** method, which returns an awaitable task.
    transportWeb.Deliver(myMessage);

<h2><a name="addattachment"></a>Gewusst wie: Hinzufügen eines Anhangs</h2>

Anhänge können mithilfe der Methode **AddAttachment** und durch Angabe des Namens der hinzuzufügenden Datei und des Dateipfads eingefügt werden. Sie können mehrere Anhänge einfügen, indem Sie diese Methode für jede Datei aufrufen, die Sie anhängen möchten. Das folgende Beispiel veranschaulicht, wie ein Anhang in einer Nachricht eingefügt wird:

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.AddAttachment(@"C:\file1.txt");
    
Sie können Anhänge auch über den **Datenstrom** der Daten hinzufügen. Dazu rufen Sie dieselbe Methode wie oben auf, d. h. **AddAttachment**, übergeben jedoch den Datenstrom und den Dateinamen, der in der Nachricht angezeigt werden soll.

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    using (var attachmentFileStream = new FileStream(@"C:\file.txt", FileMode.Open))
    {
        myMessage.AddAttachment(attachmentFileStream, "My Cool File.txt");
    }

<h2><a name="usefilters"></a>Gewusst wie: Verwenden von Filtern zur Aktivierung von Fußzeilen sowie für Nachverfolgungen und Analysen</h2>

SendGrid bietet zusätzliche E-Mail-Funktionen durch die Verwendung von Filtern. Dies sind Einstellungen, die zu einer E-Mail-Nachricht hinzugefügt werden können, um bestimmte Funktionen wie Klickprotokollierung, Google Analytics, Abonnementsverfolgung usw. zu aktivieren. Eine vollständige Liste der verfügbaren Filter finden Sie unter [Filtereinstellungen][].

Filter können mithilfe von Methoden auf **SendGrid**-E-Mails angewendet werden, die als Teil der **SendGrid**Klasse implementiert sind.

Die folgenden Beispiele veranschaulichen die Filter für die Fußzeile und die Klickprotokollierung:

### Fußzeile

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Add a footer to the message.
    myMessage.EnableFooter("PLAIN TEXT FOOTER", "<p><em>HTML FOOTER</em></p>");

### Klickprotokollierung

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Html = "<p><a href=\"http://www.example.com\">Hello World Link!</a></p>";
    myMessage.Text = "Hello World!";
    
    // true indicates that links in plain text portions of the email 
    // should also be overwritten for link tracking purposes. 
    myMessage.EnableClickTracking(true);

<h2><a name="useservices"></a>Gewusst wie: Verwenden zusätzlicher SendGrid-Dienste</h2>

SendGrid bietet webbasierte APIs, die Sie zur Nutzung zusätzlicher SendGrid-Funktionen aus Ihrer Azure-Anwendung einsetzen können. Ausführliche Informationen finden Sie in der [SendGrid-API-Dokumentation][].

<h2><a name="nextsteps"></a>Nächste Schritte</h2>

Nachdem Sie nun mit den Grundlagen des E-Mail-Dienstes SendGrid vertraut sind, finden Sie unter diesen Links weitere Informationen.

* Repository für die SendGrid C#-Bibliothek: [sendgrid-csharp][]
*   SendGrid-API-Dokumentation: <https://sendgrid.com/docs>
*   Spezielles SendGrid-Angebot für Azure-Kunden: [https://sendgrid.com](https://sendgrid.com)

  [Nächste Schritte]: #nextsteps
  [Was ist der SendGrid-E-Mail-Dienst?]: #whatis
  [Erstellen eines SendGrid-Kontos]: #createaccount
  [Verweisen auf die SendGrid .NET-Klassenbibliothek]: #reference
  [Gewusst wie: Erstellen einer E-Mail]: #createemail
  [Gewusst wie: Senden einer E-Mail]: #sendemail
  [Gewusst wie: Hinzufügen eines Anhangs]: #addattachment
  [Gewusst wie: Verwenden von Filtern zur Aktivierung von Fußzeilen sowie für Nachverfolgungen und Analysen]: #usefilters
  [Gewusst wie: Verwenden zusätzlicher SendGrid-Dienste]: #useservices
  
  
  [Sonderangebot]: https://www.sendgrid.com/windowsazure.html
  
  
  
  [SendGrid-NuGet-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid01.png
  [sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
  [SMTP vs. Web API]: https://sendgrid.com/docs/Integrate/index.html
  [Filter Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
  [SendGrid API documentation]: https://sendgrid.com/docs
  
  [cloud-based email service]: https://sendgrid.com/email-solutions
  [transactional email delivery]: https://sendgrid.com/transactional-email
