<properties 
	pageTitle="Verwenden des E-Mail-Diensts SendGrid (.NET) – Azure" 
	description="Erfahren Sie, wie Sie E-Mails mit dem SendGrid-E-Mail-Dienst in Azure senden. Die Codebeispiele wurden in C# geschrieben und verwenden die .NET-API."
	services="" 
	documentationCenter=".net" 
	authors="thinkingserious" 
	manager="sendgrid" 
	editor="erikre"/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="10/29/2014" 
	ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork"/>





# Senden von E-Mails in Azure mit SendGrid

Letzte Aktualisierung: Montag, 27. Oktober 2014

Dieser Leitfaden veranschaulicht die Ausführung allgemeiner Programmierungsaufgaben mit dem E-Mail-Dienst SendGrid in Azure. Die Beispiele sind in C\# geschrieben und verwenden die .NET-API. Beschrieben werden die Szenarien **Schreiben einer E-Mail**, **Senden einer E-Mail**, **Hinzufügen von Anhängen** und **Verwenden von Filtern**. Weitere Informationen zu SendGrid und zum Senden von E-Mails finden Sie im Abschnitt [Nächste Schritte][].

<h2><a name="toc"></a>Inhaltsverzeichnis</h2>

[Was ist der SendGrid-E-Mail-Dienst?][]   
[Erstellen eines SendGrid-Kontos][]
[Verweis auf die .NET-Klassenbibliothek für SendGrid][]
[Gewusst wie: Erstellen einer E-Mail][]   
[Gewusst wie: Senden einer E-Mail][]   
[Gewusst wie: Hinzufügen eines Anhangs][]   
[Gewusst wie: Verwenden von Apps zum Aktivieren von Fußzeilen sowie für Nachverfolgung und Analysen][]   
[Gewusst wie: Verwenden zusätzlicher SendGrid-Dienste][]   
[Nächste Schritte][]

<h2><a name="whatis"></a><span  class="short-header">Was ist der SendGrid-E-Mail-Dienst?</span></h2>

SendGrid ist ein [cloudbasierter E-Mail-Dienst], der zuverlässige [transaktionsbezogene E-Mail-Übermittlung], Skalierbarkeit und Echtzeitanalysen mit flexiblen APIs bietet, die die benutzerdefinierte Integration erleichtern. Häufige Verwendungsszenarien für SendGrid:

-   Automatisches Senden von Empfangsnachrichten an Kunden.
-   Verwaltung von Verteilerlisten zur monatlichen Versendung von E-Flyern und speziellen Angeboten.
-   Sammeln von Echtzeit-Kennzahlen für Sachverhalte wie gesperrte E-Mails oder Kundenreaktionsfähigkeit.
-   Erstellung von Berichten zur leichteren Identifikation von Trends.
-   Weiterleitung von Kundenanfragen.
-   Verarbeitung eingehender E-Mails.

Weitere Informationen finden Sie unter [https://sendgrid.com](https://sendgrid.com).

<h2><a name="createaccount"></a>SendGrid-Konto erstellen</h2>

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

<h2><a name="reference"></a>Verweis auf die .NET-Klassenbibliothek für SendGrid</h2>

Der einfachste Weg, die SendGrid-API abzurufen und Ihre Anwendung mit allen Abhängigkeiten zu konfigurieren, ist das [SendGrid NuGet-Paket](https://www.nuget.org/packages/Sendgrid). NuGet ist eine Erweiterung von Microsoft Visual Studio 2012, die die Installation und die Aktualisierung von Bibliotheken und Tools erleichtert. 

<div class="dev-callout">
<b>Hinweis</b>
<p>Wenn
Sie NuGet installieren möchten und eine ältere Visual Studio-Version vor Visual Studio 2012 verwenden, besuchen Sie  <a href="http://www.nuget.org">http://www.nuget.org</a>, und klicken Sie auf die Schaltfläche <b>Installation
NuGet</b> .</p>
</div>

Gehen Sie folgendermaßen vor, um das SendGrid NuGet-Paket in Ihrer Anwendung zu installieren:

1.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Verweise**, und klicken Sie dann auf **NuGet-Pakete verwalten**.

2.  Klicken Sie im linken Bereich des Dialogfelds **NuGet-Pakete verwalten** auf **Online**.

3.  Suchen Sie nach **SendGrid**, und wählen Sie das Element **SendGrid** in der Ergebnisliste aus (die aktuelle Version ist 5.0.0).

    ![SendGrid NuGet package][SendGrid-NuGet-package]

4.  Klicken Sie auf **Installieren**, um die Installation abzuschließen, und schließen Sie dann das Dialogfeld.

Die .NET-Klassenbibliothek für SendGrid heißt **SendGridMail**. Sie enthält die folgenden Namespaces:

-   **SendGridMail** zur Erstellung und Bearbeitung von E-Mail-Elementen.
-   **SendGridMail.Transport** zum Senden von E-Mails entweder über das Protokoll **SMTP** oder über das Protokoll **HTTP 1.1** mit **Web/REST**.

Fügen Sie zu Beginn aller C\#-Dateien, in denen Sie programmgesteuert auf den E-Mail-Dienst SendGrid zugreifen möchten, die folgenden Namespace-Codedeklarationen hinzu: **System.Net*** und **System.Net.Mail** sind .NET Framework-Namespaces und enthalten, weil sie Typen enthalten, die üblicherweise mit den SendGrid-APIs verwendet werden.

    using System;
    using System.Net;
    using System.Net.Mail;
    using SendGrid;

<h2><a name="createemail"></a>Gewusst wie: Erstellen einer E-Mail</h2>

Verwenden Sie die statische **SendGrid.GetInstance**-Methode, um eine E-Mail des Typs **SendGrid** zu erstellen. Nachdem die Nachricht erstellt wurde, können Sie die Eigenschaften und Methoden von **SendGrid** verwenden, um Werte wie den E-Mail-Absender, den E-Mail-Empfänger, den Betreff und den Text der E-Mail festzulegen.

Das folgende Beispiel zeigt, wie ein vollständig ausgefülltes E-Mail-
Objekt erstellt wird:

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

Weitere Informationen zu allen Eigenschaften und Methoden, die vom Typ **SendGrid** unterstützt werden, finden Sie unter [sendgrid-csharp][] auf GitHub.

<h2><a name="sendemail"></a>Gewusst wie: Senden einer E-Mail</h2>

Nach der Erstellung einer E-Mail können Sie diese über die von SendGrid bereitgestellte Web-API senden. Alternativ können Sie die [integrierte .NET-Bibliothek verwenden](https://sendgrid.com/docs/Code_Examples/csharp.html).

Beim Senden von E-Mails müssen Sie Ihre
SendGrid-Konto-Anmeldeinformationen (Benutzername und Kennwort) angeben. Der folgende Code veranschaulicht, wie Sie Ihre Anmeldeinformationen in ein **NetworkCredential**-Objekt einbinden:
    
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

Die folgenden Beispiele zeigen, wie Sie eine Nachricht über die Web-API senden.

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

Anhänge können mithilfe der Methode **AddAttachment** und durch Angabe von Namen und Pfad der anzuhängenden Datei hinzugefügt werden. Sie können mehrere Anhänge einfügen, indem Sie diese Methode für jede Datei aufrufen, die Sie anhängen möchten. Das folgende Beispiel veranschaulicht, wie ein Anhang in einer Nachricht eingefügt wird:

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.AddAttachment(@"C:\file1.txt");
    
Sie können Anhänge auch über den **Datenstrom** der Daten hinzufügen. Dazu rufen Sie dieselbe Methode wie oben auf, d. h. **AddAttachment**, übergeben jedoch den Datenstrom und den Dateinamen, der in der Nachricht angezeigt werden soll. In diesem Fall müssen Sie die System.IO-Bibliothek hinzufügen.

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    using (var attachmentFileStream = new FileStream(@"C:\file.txt", FileMode.Open))
    {
        myMessage.AddAttachment(attachmentFileStream, "My Cool File.txt");
    }


<h2><a name="usefilters"></a><span  class="short-header">Gewusst wie: Verwenden von Apps zum Aktivieren von Fußzeilen sowie für Nachverfolgung und Analysen</span></h2>

SendGrid bietet zusätzliche E-Mail-Funktionen durch die Verwendung von Apps. Dies sind Einstellungen, die zu einer E-Mail-Nachricht hinzugefügt werden können, um bestimmte Funktionen wie Klickprotokollierung, Google Analytics, Abonnementsverfolgung usw. zu aktivieren. Eine vollständige Liste der Apps finden Sie unter [App-Einstellungen][].

Apps können mithilfe von Methoden, die als Teil der **SendGrid**-Klasse implementiert wurden, auf **SendGrid**-E-Mails angewendet werden.

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

SendGrid bietet webbasierte APIs und Webhooks, die Sie zur Nutzung zusätzlicher SendGrid-Funktionen aus Ihrer Azure-Anwendung einsetzen können. Ausführliche Informationen finden Sie in der [SendGrid-API-Dokumentation][].

<h2><a name="nextsteps"></a>Nächste Schritte</h2>

Nachdem Sie nun mit den Grundlagen des E-Mail-Dienstes SendGrid vertraut sind, finden Sie unter den folgenden Links weitere Informationen.

* Repo für die SendGrid C\#-Bibliothek: [sendgrid-csharp][]
*   SendGrid-API-Dokumentation: <https://sendgrid.com/docs>
*   Spezielles SendGrid-Angebot für Azure-Kunden: [https://sendgrid.com](https://sendgrid.com)

  [Nächste Schritte]: #nextsteps
  [Was ist der SendGrid-E-Mail-Dienst?]: #whatis
  [Erstellen eines SendGrid-Kontos]: #createaccount
  [Verweis auf die .NET-Klassenbibliothek für SendGrid]: #reference
  [Gewusst wie: Erstellen einer E-Mail]: #createemail
  [Gewusst wie: Senden einer E-Mail]: #sendemail
  [Gewusst wie: Hinzufügen eines Anhangs]: #addattachment
  [Gewusst wie: Verwenden von Filtern zur Aktivierung von Fußzeilen sowie für Nachverfolgungen und Analysen]: #usefilters
  [Gewusst wie: Verwenden zusätzlicher SendGrid-Dienste]: #useservices
  
  
  [Sonderangebot]: https://www.sendgrid.com/windowsazure.html
  
  
  
  [SendGrid NuGet-Paket]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid01.png
  [sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
  [SMTP- und Web-API]: https://sendgrid.com/docs/Integrate/index.html
  [App-Einstellungen]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
  [SendGrid-API-Dokumentation]: https://sendgrid.com/docs
  
  [Cloud-basierter E-Mail-Dienst]: https://sendgrid.com/email-solutions
  [Transaktionsbezogene E-Mail-Übermittlung]: https://sendgrid.com/transactional-email

<!--HONumber=35.2-->

<!--HONumber=46--> 
