<properties 
	pageTitle="Verwenden des E-Mail-Diensts SendGrid (.NET) | Microsoft Azure" 
	description="Erfahren Sie, wie Sie E-Mails mit dem SendGrid-E-Mail-Dienst in Azure senden. Die Codebeispiele wurden in C# geschrieben und verwenden die .NET-API." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="thinkingserious" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="01/14/2016" 
	ms.author="team-pi@sendgrid.com"/>





# Senden von E-Mails in Azure mit SendGrid


## Übersicht

Dieser Leitfaden veranschaulicht die Ausführung allgemeiner Programmierungsaufgaben mit dem E-Mail-Dienst SendGrid in Azure. Die Beispiele sind in C# geschrieben und verwenden die .NET API. Beschrieben werden die Szenarien **Schreiber einer E-Mail**, **Senden einer E-Mail**, **Hinzufügen von Anhängen** und **Verwenden von Filtern**. Weitere Informationen zu SendGrid und zum Senden von E-Mails erhalten Sie im Abschnitt [Nächste Schritte][].

## Was ist der SendGrid-E-Mail-Dienst?

SendGrid ist ein [cloudbasierter E-Mail-Dienst], der zuverlässige [transaktionale E-Mail-Übermittlung], Skalierbarkeit und Echtzeitanalysen mit flexiblen APIs bietet, die die benutzerdefinierte Integration erleichtern. Häufige Verwendungsszenarien für SendGrid:

-   Automatisches Senden von Empfangsnachrichten an Kunden.
-   Verwaltung von Verteilerlisten zur monatlichen Versendung von E-Flyern und speziellen Angeboten.
-   Sammeln von Echtzeit-Kennzahlen für Sachverhalte wie gesperrte E-Mails oder Kundenreaktionsfähigkeit.
-   Erstellung von Berichten zur leichteren Identifikation von Trends.
-   Weiterleitung von Kundenanfragen.
-   Verarbeitung eingehender E-Mails.

Weitere Informationen finden Sie unter [https://sendgrid.com](https://sendgrid.com) oder in der [C#-Bibliothek][sendgrid-csharp].

## Erstellen eines SendGrid-Kontos

[AZURE.INCLUDE [sendgrid-sign-up](../../includes/sendgrid-sign-up.md)]

## Verweisen auf die SendGrid .NET-Klassenbibliothek

Der einfachste Weg, die SendGrid-API abzurufen und Ihre Anwendung mit allen Abhängigkeiten zu konfigurieren, ist das [SendGrid NuGet-Paket](https://www.nuget.org/packages/Sendgrid). NuGet ist eine Erweiterung von Microsoft Visual Studio 2015, die die Installation und die Aktualisierung von Bibliotheken und Tools erleichtert.

> [AZURE.NOTE] Wenn Sie NuGet installieren möchten und eine ältere Version von Visual Studio ausführen als Visual Studio 2015, besuchen Sie [http://www.nuget.org](http://www.nuget.org), und klicken Sie auf die Schaltfläche **Install NuGet**.

Gehen Sie folgendermaßen vor, um das SendGrid NuGet-Paket in Ihrer Anwendung zu installieren:

1.  Erstellen Sie ein neues Projekt.

    ![Erstellen eines neuen Projekts][create-new-project]

2.  Wählen Sie eine Vorlage aus.

    ![Vorlage auswählen][select-a-template]

3.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Verweise**, und klicken Sie dann auf **Manage NuGet Packages**.

4.  Suchen Sie nach **SendGrid**, und wählen Sie in der Ergebnisliste das Element **SendGrid** aus.

    ![SendGrid NuGet-Paket][SendGrid-NuGet-package]

5.  Klicken Sie auf **Installieren**, um die Installation abzuschließen, und schließen Sie dann das Dialogfeld.

Die .NET-Klassenbibliothek für SendGrid heißt **SendGridMail**. Sie enthält die folgenden Namespaces:

-   **SendGridMail** zur Erstellung und Bearbeitung von E-Mail-Elementen.
-   **SendGridMail.Transport** zum Senden von E-Mails entweder über das Protokoll **SMTP** oder über das Protokoll HTTP 1.1 mit **Web/REST**.

Fügen Sie zu Beginn aller C#-Dateien, in denen Sie programmgesteuert auf den E-Mail-Dienst SendGrid zugreifen möchten, die folgenden Namespace-Codedeklarationen hinzu: **System.Net** und **System.Net.Mail** sind .NET Framework-Namespaces, die enthalten sind, weil sie Typen enthalten, die üblicherweise mit den SendGrid-APIs verwendet werden.

    using System;
    using System.Net;
    using System.Net.Mail;
    using SendGrid;

## Vorgehensweise: Eine E-Mail erstellen

Mit dem **SendGridMessage**-Objekt können Sie eine E-Mail-Nachricht erstellen. Nachdem das Nachrichtenobjekt erstellt wurde, können Sie Eigenschaften und Methoden festlegen, beispielsweise den E-Mail-Absender, den E-Mail-Empfänger, den Betreff und den Text der E-Mail.

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

Weitere Informationen über die vom Typ **SendGrid** unterstützen Eigenschaften und Methoden finden Sie unter [sendgrid-csharp][] auf GitHub.

## Vorgehensweise: Eine E-Mail senden

Nach der Erstellung einer E-Mail können Sie diese über die von SendGrid bereitgestellte Web-API senden. Alternativ können Sie die [integrierte .NET-Bibliothek](https://sendgrid.com/docs/Code_Examples/csharp.html) verwenden.

Das Senden einer E-Mail erfordert die Angabe der Anmeldeinformationen für Ihr SendGrid-Konto (Benutzername und Kennwort) oder des SendGrid-API-Schlüssels. Der API-Schlüssel ist die bevorzugte Methode. Ausführliche Informationen zum Konfigurieren von API-Schlüsseln finden Sie in der [Dokumentation](https://sendgrid.com/docs/Classroom/Send/api_keys.html).

Sie können diese Anmeldeinformationen über das Azure-Portal speichern, indem Sie auf "Konfigurieren" klicken und die Schlüssel/Wert-Paare unter "App-Einstellungen" hinzufügen.

 ![App-Einstellungen in Azure][azure_app_settings]

 Anschließend können Sie wie folgt darauf zugreifen:
    
    var username = System.Environment.GetEnvironmentVariable("SENDGRID_USER"); 
    var pswd = System.Environment.GetEnvironmentVariable("SENDGRID_PASS");
    var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");

Mit Anmeldeinformationen:
    
    // Create network credentials to access your SendGrid account
    var username = "your_sendgrid_username";
    var pswd = "your_sendgrid_password";

    var credentials = new NetworkCredential(username, pswd);
    // Create an Web transport for sending email.
    var transportWeb = new Web(credentials);

Mit dem API-Schlüssel:

    var apiKey = "your_sendgrid_api_key";  
    // create a Web transport, using API Key
    var transportWeb = new Web(apiKey);


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

    // Send the email, which returns an awaitable task.
    transportWeb.DeliverAsync(myMessage);

    // If developing a Console Application, use the following
    // transportWeb.DeliverAsync(mail).Wait();

## Vorgehensweise: Einen Anhang hinzufügen

Anhänge können mithilfe der Methode **AddAttachment** und durch Angabe des Namens der hinzuzufügenden Datei und des Dateipfads eingefügt werden. Sie können mehrere Anhänge einfügen, indem Sie diese Methode für jede Datei aufrufen, die Sie anhängen möchten. Das folgende Beispiel veranschaulicht, wie ein Anhang in einer Nachricht eingefügt wird:

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.AddAttachment(@"C:\file1.txt");
    
Sie können Anhänge auch über den **Datenstrom** der Daten hinzufügen. Dazu rufen Sie dieselbe Methode wie oben auf, d. h. **AddAttachment**, übergeben jedoch den Datenstrom und den Dateinamen, der in der Nachricht angezeigt werden soll. In diesem Fall müssen Sie die System.IO-Bibliothek hinzufügen.

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    using (var attachmentFileStream = new FileStream(@"C:\file.txt", FileMode.Open))
    {
        myMessage.AddAttachment(attachmentFileStream, "My Cool File.txt");
    }


## Vorgehensweise: Verwenden von Apps zum Aktivieren von Fußzeilen sowie für Nachverfolgung und Analysen

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
    myMessage.Html = "<p><a href="http://www.example.com">Hello World Link!</a></p>";
    myMessage.Text = "Hello World!";
    
    // true indicates that links in plain text portions of the email 
    // should also be overwritten for link tracking purposes. 
    myMessage.EnableClickTracking(true);

## Vorgehensweise: Zusätzliche SendGrid-Dienste verwenden

SendGrid bietet webbasierte APIs und Webhooks, die Sie zur Nutzung zusätzlicher SendGrid-Funktionen aus Ihrer Azure-Anwendung einsetzen können. Ausführliche Informationen finden Sie in der [SendGrid-API-Dokumentation][].

## Nächste Schritte

Nachdem Sie nun mit den Grundlagen des E-Mail-Dienstes SendGrid vertraut sind, finden Sie unter diesen Links weitere Informationen.

*   SendGrid C#-Bibliotheksrepository: [sendgrid-csharp][]
*   SendGrid API-Dokumentation: <https://sendgrid.com/docs>
*   Spezielles SendGrid-Angebot für Azure-Kunden: [https://sendgrid.com](https://sendgrid.com)

  [Nächste Schritte]: #next-steps
  [What is the SendGrid Email Service?]: #whatis
  [Create a SendGrid Account]: #createaccount
  [Reference the SendGrid .NET Class Library]: #reference
  [How to: Create an Email]: #createemail
  [How to: Send an Email]: #sendemail
  [How to: Add an Attachment]: #addattachment
  [How to: Use Filters to Enable Footers, Tracking, and Analytics]: #usefilters
  [How to: Use Additional SendGrid Services]: #useservices
  
  [special offer]: https://www.sendgrid.com/windowsazure.html
  
  [create-new-project]: ./media/sendgrid-dotnet-how-to-send-email/create_new_project.png
  [select-a-template]: ./media/sendgrid-dotnet-how-to-send-email/select_a_template.png
  [SendGrid-NuGet-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid_nuget.png
  [azure_app_settings]: ./media/sendgrid-dotnet-how-to-send-email/app_settings.png
  [sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
  [SMTP vs. Web API]: https://sendgrid.com/docs/Integrate/index.html
  [App-Einstellungen]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
  [SendGrid-API-Dokumentation]: https://sendgrid.com/docs
  
  [cloudbasierter E-Mail-Dienst]: https://sendgrid.com/email-solutions
  [transaktionale E-Mail-Übermittlung]: https://sendgrid.com/transactional-email
 

<!---HONumber=AcomDC_0128_2016-->