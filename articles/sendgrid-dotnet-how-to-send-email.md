<properties linkid="dev-net-how-to-sendgrid-email-service" urlDisplayName="SendGrid Email Service" pageTitle="How to use the SendGrid email service (.NET) - Azure" metaKeywords="Azure SendGrid, Azure email service, Azure SendGrid .NET, Azure email .NET, Azure SendGrid C#, Azure email C#" description="Learn how send email with the SendGrid email service on Azure. Code samples written in C# and use the .NET API." metaCanonical="" services="" documentationCenter=".NET" title="How to Send Email Using SendGrid with Azure" authors="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" solutions="" manager="carolz" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork"></tags>

# Senden von E-Mails in Azure mit SendGrid

Letzte Aktualisierung: 21.08.14

Dieser Leitfaden veranschaulicht die Ausführung allgemeiner Programmierungsaufgaben
mit dem E-Mail-Dienst SendGrid in Azure. Die Beispiele sind in C# geschrieben
und verwenden die .NET-API. Die Szenarien behandeln die Themen **Erstellen
einer E-Mail**, **Senden einer E-Mail**, **Hinzufügen von Anhängen** und **Verwenden von
Filtern**. Weitere Informationen zu SendGrid und zum Senden von E-Mails erhalten Sie im Abschnitt
[Nächste Schritte][Nächste Schritte].

## <a name="toc"></a>Inhaltsverzeichnis

[Was ist der SendGrid-E-Mail-Dienst?][Was ist der SendGrid-E-Mail-Dienst?]
[Erstellen eines SendGrid-Kontos][Erstellen eines SendGrid-Kontos]
[Anlegen eines Verweises auf die SendGrid .NET-Klassenbibliothek][Anlegen eines Verweises auf die SendGrid .NET-Klassenbibliothek]
[Vorgehensweise: Erstellen einer E-Mail][Vorgehensweise: Erstellen einer E-Mail]
[Vorgehensweise: Senden einer E-Mail][Vorgehensweise: Senden einer E-Mail]
[Vorgehensweise: Hinzufügen eines Anhangs][Vorgehensweise: Hinzufügen eines Anhangs]
[Vorgehensweise: Verwenden von Filtern zur Aktivierung von Fußzeilen sowie für Nachverfolgungen und Analysen][Vorgehensweise: Verwenden von Filtern zur Aktivierung von Fußzeilen sowie für Nachverfolgungen und Analysen]
[Vorgehensweise: Verwenden zusätzlicher SendGrid-Dienste][Vorgehensweise: Verwenden zusätzlicher SendGrid-Dienste]
[Nächste Schritte][Nächste Schritte]

## <a name="whatis"></a><span class="short-header">Was ist der SendGrid-E-Mail-Dienst?</span>

SendGrid ist ein [cloudbasierter E-Mail-Dienst][cloudbasierter E-Mail-Dienst], der zuverlässige
[transaktionale E-Mail-Übermittlung][transaktionale E-Mail-Übermittlung], Skalierbarkeit und Echtzeitanalysen mit flexiblen APIs
 bietet, die die benutzerdefinierte Integration erleichtern. Häufige Verwendungsszenarien für
SendGrid:

-   Automatisches Senden von Empfangsnachrichten an Kunden.
-   Verwalten von Verteilungslisten zum Senden von monatlichen
    e-Flyern und speziellen Angeboten an Kunden
-   Sammeln von Echtzeit-Kennzahlen für Sachverhalte wie gesperrte E-Mails und
    Kundenreaktionszeit
-   Erstellung von Berichten zur leichteren Identifikation von Trends.
-   Weiterleitung von Kundenanfragen.

Weitere Informationen finden Sie unter [][]<http://sendgrid.com></a>.

## <a name="createaccount"></a><span class="short-header">SendGrid-Konto erstellen</span>

[WACOM.INCLUDE [sendgrid-sign-up][sendgrid-sign-up]]

## <a name="reference"></a><span class="short-header">Anlegen eines Verweises auf die SendGrid .NET-Klassenbibliothek </span>Anlegen eines Verweises auf die SendGrid .NET-Klassenbibliothek

Der einfachste Weg, die SendGrid-API abzurufen und Ihre Anwendung mit allen
Abhängigkeiten zu konfigurieren, ist das [SendGrid NuGet-Paket][SendGrid NuGet-Paket]. NuGet ist
eine Erweiterung von Microsoft Visual Studio 2012, die die Installation und die Aktualisierung von Bibliotheken
und Tools erleichtert.

<div class="dev-callout">
<b>Hinweis</b>
<p>Wenn
Sie NuGet installieren m&ouml;chten und eine &auml;ltere Visual Studio-Version vor Visual Studio 2012 verwenden, besuchen Sie <a href="http://www.nuget.org">http://www.nuget.org</a>, und klicken Sie auf die Schaltfl&auml;che <b>Install
NuGet</b>.</p>
</div>

Gehen Sie folgendermaßen vor, um das SendGrid NuGet-Paket in Ihrer Anwendung zu installieren:

1.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Verweise**, und klicken Sie dann auf
    **Manage NuGet Packages**.

2.  Klicken Sie im linken Bereich des Dialogfelds **NuGet-Pakete verwalten** auf **Online**.

3.  Suchen Sie nach **SendGrid**, und wählen Sie in der Ergebnisliste das
    Element **SendGrid** aus.

    ![SendGrid NuGet-Paket][1]

4.  Klicken Sie auf **Installieren**, um die Installation abzuschließen, und
    schließen Sie dann das Dialogfeld.

Die .NET-Klassenbibliothek für SendGrid heißt **SendGridMail**. Sie enthält
die folgenden Namespaces:

-   **SendGridMail** zur Erstellung und Bearbeitung von E-Mail-Elementen.
-   **SendGridMail.Transport** zum Senden von E-Mails entweder über das Protokoll
    **SMTP** oder über das Protokoll HTTP 1.1 mit
    **Web/REST**.

Fügen Sie am Anfang aller C#-Dateien, in denen Sie programmgesteuert
auf den E-Mail-Dienst SendGrid zugreifen möchten, die folgenden Namespace-Codedeklarationen hinzu.
**System.Net** und **System.Net.Mail** sind .NET Framework-Namespaces,
die enthalten sind, weil sie Typen umfassen,
die üblicherweise mit den SendGrid-APIs verwendet werden.

    using System;
    using System.Net;
    using System.Net.Mail;
    using SendGrid;

## <a name="createemail"></a><span class="short-header">Gewusst wie: Erstellen einer E-Mail</span>Vorgehensweise: Erstellen einer E-Mail

Verwenden Sie die statische **SendGrid.GetInstance**-Methode, um eine
E-Mail des Typs **SendGrid** zu erstellen. Nachdem die Nachricht erstellt wurde,
können Sie die Eigenschaften und Methoden von **SendGrid** verwenden, um
Werte wie den E-Mail-Absender, den E-Mail-Empfänger, den Betreff und den
Text der E-Mail festzulegen.

Das folgende Beispiel zeigt, wie ein vollständig ausgefülltes E-Mail-Objekt
erstellt wird:

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

Weitere Informationen über die vom Typ
**SendGrid** unterstützen Eigenschaften und Methoden finden Sie unter [sendgrid-csharp][sendgrid-csharp] auf GitHub.

## <a name="sendemail"></a><span class="short-header">Gewusst wie: Senden einer E-Mail</span>

Nach der Erstellung einer E-Mail können Sie
diese über die von SendGrid bereitgestellte Web-API senden. Alternativ können Sie die [integrierte .NET-Bibliothek][integrierte .NET-Bibliothek] verwenden.

Das Senden einer E-Mail erfordert die Angabe
der Anmeldeinformationen für Ihr SendGrid-Konto (Benutzername und Kennwort). Der folgende Code
veranschaulicht, wie Sie Ihre Anmeldeinformationen in einem **NetworkCredential**
-Objekt umschließen:

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

## <a name="addattachment"></a><span class="short-header">Gewusst wie: Hinzufügen eines Anhangs</span>Vorgehensweise: Hinzufügen eines Anhangs

Anhänge können mithilfe der Methode **AddAttachment**
 und durch Angabe des Namens der anzufügenden Datei und des Dateipfads hinzugefügt werden.
Sie können mehrere Anhänge einfügen, indem Sie
diese Methode für jede Datei aufrufen, die Sie anfügen möchten. Das folgende Beispiel veranschaulicht, wie
ein Anhang einer Nachricht hinzugefügt wird:

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.AddAttachment(@"C:\file1.txt");

Sie können Anhänge auch über den **Datenstrom** der Daten hinzufügen. Dazu rufen Sie dieselbe Methode wie oben auf, d. h. **AddAttachment**, übergeben jedoch den Datenstrom und den Dateinamen, der in der Nachricht angezeigt werden soll.

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    using (var attachmentFileStream = new FileStream(@"C:\file.txt", FileMode.Open))
    {
        myMessage.AddAttachment(attachmentFileStream, "My Cool File.txt");
    }

## <a name="usefilters"></a><span class="short-header">Gewusst wie: Verwenden von Filtern zur Aktivierung von Fußzeilen sowie für Nachverfolgungen und Analysen</span>

SendGrid bietet zusätzliche E-Mail-Funktionen durch die Verwendung von
Filtern. Dies sind Einstellungen, die einer E-Mail hinzugefügt
werden können, um bestimmte Funktionen wie Klickprotokollierung, Google
Analytics, Abonnementsverfolgung usw. zu aktivieren. Eine vollständige Liste der Filter finden Sie unter
[Filtereinstellungen][Filtereinstellungen].

Filter können mithilfe von Methoden auf **SendGrid**-E-Mails angewendet werden,
die als Teil der **SendGrid**-Klasse implementiert sind.

Die folgenden Beispiele veranschaulichen die Filter für die Fußzeile und
die Klickprotokollierung:

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

## <a name="useservices"></a><span class="short-header">Gewusst wie: Verwenden zusätzlicher SendGrid-Dienste</span>

SendGrid bietet webbasierte APIs, die Sie zur Nutzung zusätzlicher
SendGrid-Funktionen aus Ihrer Azure-Anwendung einsetzen können. Ausführliche
Informationen finden Sie in der [SendGrid-API-Dokumentation][SendGrid-API-Dokumentation].

## <a name="nextsteps"></a><span class="short-header">Nächste Schritte</span>Nächste Schritte

Nachdem Sie nun mit den Grundlagen des E-Mail-Diensts SendGrid vertraut sind, finden Sie unter
diesen Links weitere Informationen.

-   Repo für die SendGrid C#-Bibliothek: [sendgrid-csharp][sendgrid-csharp]
-   SendGrid-API-Dokumentation: <http://docs.sendgrid.com/documentation/api/>
-   Spezielles SendGrid-Angebot für Azure-Kunden: [][]<http://sendgrid.com></a>

  [Nächste Schritte]: #nextsteps
  [Was ist der SendGrid-E-Mail-Dienst?]: #whatis
  [Erstellen eines SendGrid-Kontos]: #createaccount
  [Anlegen eines Verweises auf die SendGrid .NET-Klassenbibliothek]: #reference
  [Vorgehensweise: Erstellen einer E-Mail]: #createemail
  [Vorgehensweise: Senden einer E-Mail]: #sendemail
  [Vorgehensweise: Hinzufügen eines Anhangs]: #addattachment
  [Vorgehensweise: Verwenden von Filtern zur Aktivierung von Fußzeilen sowie für Nachverfolgungen und Analysen]: #usefilters
  [Vorgehensweise: Verwenden zusätzlicher SendGrid-Dienste]: #useservices
  [cloudbasierter E-Mail-Dienst]: http://sendgrid.com/solutions
  [transaktionale E-Mail-Übermittlung]: http://sendgrid.com/transactional-email
  []: http://sendgrid.com
  [sendgrid-sign-up]: ../includes/sendgrid-sign-up.md
  [SendGrid NuGet-Paket]: https://www.nuget.org/packages/Sendgrid
  [1]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid01.png
  [sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
  [integrierte .NET-Bibliothek]: https://sendgrid.com/docs/Code_Examples/csharp.html
  [Filtereinstellungen]: http://docs.sendgrid.com/documentation/api/smtp-api/filter-settings/
  [SendGrid-API-Dokumentation]: http://docs.sendgrid.com/documentation/api/
