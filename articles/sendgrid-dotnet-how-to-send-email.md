<properties linkid="dev-net-how-to-sendgrid-email-service" urlDisplayName="SendGrid Email Service" pageTitle="How to use the SendGrid email service (.NET) - Azure" metaKeywords="Azure SendGrid, Azure email service, Azure SendGrid .NET, Azure email .NET, Azure SendGrid C#, Azure email C#" description="Learn how send email with the SendGrid email service on Azure. Code samples written in C# and use the .NET API." metaCanonical="" services="" documentationCenter=".NET" title="How to Send Email Using SendGrid with Azure" authors="" solutions="" manager="" editor="" />

Senden von E-Mails in Azure mit SendGrid
========================================

Dieser Leitfaden veranschaulicht die Ausführung allgemeiner Programmierungsaufgaben mit dem E-Mail-Dienst SendGrid in Azure. Die Beispiele sind in C\# geschrieben und verwenden die .NET API. Beschrieben werden die Szenarien **Schreiber einer E-Mail**, **Senden einer E-Mail**, **Hinzufügen von Anhängen** und **Verwenden von Filtern**. Weitere Informationen zu SendGrid und zum Senden von E-Mails erhalten Sie im Abschnitt [Nächste Schritte](#nextsteps).

Inhaltsverzeichnis
------------------

[Was ist der SendGrid-E-Mail-Dienst?](#whatis)
 [Erstellen eines SendGrid-Kontos](#createaccount)
 [Anlegen eines Verweises auf die SendGrid .NET-Klassenbibliothek](#reference)
 [Vorgehensweise: Erstellen einer E-Mail](#createemail)
 [Vorgehensweise: Senden einer E-Mail](#sendemail)
 [Vorgehensweise: Hinzufügen eines Anhangs](#addattachment)
 [Vorgehensweise: Verwenden von Filtern zur Aktivierung von Fußzeilen sowie für Nachverfolgungen und Analysen](#usefilters)
 [Vorgehensweise: Verwenden zusätzlicher SendGrid-Dienste](#useservices)
 [Nächste Schritte](#nextsteps)

Was ist der SendGrid-E-Mail-Dienst?Was ist der SendGrid-E-Mail-Dienst?
----------------------------------------------------------------------

SendGrid ist ein [cloudbasierter E-Mail-Dienst](http://sendgrid.com/solutions), der zuverlässige [transaktionale E-Mail-Übermittlung](http://sendgrid.com/transactional-email), Skalierbarkeit und Echtzeitanalysen mit flexiblen APIs bietet, die die benutzerdefinierte Integration erleichtern. Häufige Verwendungsszenarien für SendGrid:

-   Automatisches Senden von Empfangsnachrichten an Kunden.
-   Verwaltung von Verteilerlisten zur monatlichen Versendung von E-Flyern und speziellen Angeboten.
-   Erfassung von Echzeitmessdaten für Elemente wie blockierte E-Mails und Kundenreaktionszeit.
-   Erstellung von Berichten zur leichteren Identifikation von Trends.
-   Weiterleitung von Kundenanfragen.

Weitere Informationen finden Sie unter <http://sendgrid.com>.

Erstellen eines SendGrid-KontosErstellen eines SendGrid-Kontos
--------------------------------------------------------------

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

Anlegen eines Verweises auf die SendGrid .NET-Klassenbibliothek Anlegen eines Verweises auf die SendGrid .NET-Klassenbibliothek
-------------------------------------------------------------------------------------------------------------------------------

Der einfachste Weg, die SendGrid-API abzurufen und Ihre Anwendung mit allen Abhängigkeiten zu konfigurieren, ist das SendGrid NuGet-Paket. NuGet ist eine Erweiterung von Microsoft Visual Studio 2012, die die Installation und die Aktualisierung von Bibliotheken und Tools erleichtert.

**Hinweis**

Wenn Sie NuGet installieren möchten und eine ältere Version von Visual Studio ausführen als Visual Studio 2012, besuchen Sie <http://www.nuget.org>, und klicken Sie auf die Schaltfläche **Install NuGet**.

Gehen Sie folgendermaßen vor, um das SendGrid NuGet-Paket in Ihrer Anwendung zu installieren:

1.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Verweise**, und klicken Sie dann auf **Manage NuGet Packages**.

2.  Klicken Sie im linken Bereich des Dialogfelds **NuGet-Pakete verwalten** auf **Online**.

3.  Suchen Sie nach **SendGrid**, und wählen Sie in der Ergebnisliste das Element **SendGrid** aus.

    ![SendGrid NuGet-Paket](./media/sendgrid-dotnet-how-to-send-email/sendgrid01.png)

4.  Klicken Sie auf **Installieren**, um die Installation abzuschließen, und schließen Sie dann das Dialogfeld.

Die .NET-Klassenbibliothek für SendGrid heißt **SendGridMail**. Sie enthält die folgenden Namespaces:

-   **SendGridMail** zur Erstellung und Bearbeitung von E-Mail-Elementen.
-   **SendGridMail.Transport** zum Senden von E-Mails entweder über das Protokoll **SMTP** oder über das Protokoll HTTP 1.1 mit **Web/REST**.

Fügen Sie zu Beginn aller C\#-Dateien, in denen Sie programmgesteuert auf den E-Mail-Dienst SendGrid zugreifen möchten, die folgenden Namespace-Codedeklarationen hinzu: **System.Net** und **System.Net.Mail** sind .NET Framework-Namespaces, die enthalten sind, weil sie Typen enthalten, die üblicherweise mit den SendGrid-APIs verwendet werden.

    using System.Net;
    using System.Net.Mail;
    using SendGridMail;
    using SendGridMail.Transport;

Vorgehensweise: Erstellen einer E-MailVorgehensweise: Erstellen einer E-Mail
----------------------------------------------------------------------------

Verwenden Sie das statische Verfahren **SendGrid.GetInstance**, um eine E-Mail-Nachricht des Typs **SendGrid** zu erstellen. Wenn die Nachricht erstellt ist, können Sie die Eigenschaften und Verfahren von **SendGrid** verwenden, um Werte wie den E-Mail-Absender, den E-Mail-Empfänger, den Betreff und den Text der E-Mail festzulegen.

Das folgende Beispiel zeigt, wie ein vollständig ausgefülltes E-Mail-Objekt erstellt wird:

    // E-Mail-Eigenschaften einrichten.
    var from = new MailAddress("john@contoso.com");
    var to   = new MailAddress[] { new MailAddress("jeff@contoso.com") };
    var cc   = new MailAddress[] { new MailAddress("anna@contoso.com") };
    var bcc  = new MailAddress[] { new MailAddress("peter@contoso.com") };
    var subject = "Testing the SendGrid Library";
    var html    = "<p>Hello World!</p>";
    var text = "Hello World plain text!";
    var transport = SendGridMail.TransportType.SMTP;

    // E-Mail erstellen und die acht Eigenschaften als Argumente einfügen.
    SendGrid myMessage = SendGrid.GetInstance(from, to, cc, bcc, subject, html, text, transport);

Das folgende Beispiel zeigt, wie ein leeres E-Mail-Objekt erstellt wird:

    // Zuerst das E-Mail-Objekt erstellen, dann die Eigenschaften hinzufügen.
    SendGrid myMessage = SendGrid.GetInstance();
     
    // Nachrichteneigenschaften hinzufügen.
    MailAddress sender = new MailAddress(@"John Smith <john@contoso.com>");
    myMessage.From = sender;
     
    // Mehrere Adressen in das Feld "To" einfügen.
    List<String> recipients = new List<String>
    {
        @"Jeff Smith <jeff@contoso.com>",
        @"Anna Lidman <anna@contoso.com>",
        @"Peter Saddow <peter@contoso.com>"
    };
     
    foreach (string recipient in recipients)
    {
        myMessage.AddTo(recipient);
    }
     
    // Nachrichtentext in HTML-Format einfügen.
    myMessage.Html = "<p>Hello World!</p>";

    // Betreff einfügen.
    myMessage.Subject = "Testing the SendGrid Library";

Weitere Informationen über die vom Typ **SendGrid** unterstützen Eigenschaften und Methoden finden Sie unter [sendgrid-csharp](https://github.com/sendgrid/sendgrid-csharp) auf GitHub.

Vorgehensweise: Senden einer E-MailVorgehensweise: Senden einer E-Mail
----------------------------------------------------------------------

Nach der Erstellung einer E-Mail-Nachricht können sie diese entweder über SM'TP oder über die von SendGrid bereitgestellte Web-API senden. Genaue Informationen zu den Vor- und Nachteilen jeder API finden Sie in der SendGrid-Dokumentation unter [SMTP vs. Web API](http://docs.sendgrid.com/documentation/get-started/integrate/examples/smtp-vs-rest/).

Das Senden einer E-Mail über eines der Protokolle erfordert die Eingabe der Anmeldeinformationen für Ihr SendGrid-Konto (Benutzername und Kennwort). Der folgende Code veranschaulicht, wie Sie Ihre Anmeldeinformationen in einem **NetworkCredential**-Objekt verpacken:

    // Netzwerk-Anmeldeinformationen zum Zugriff auf Ihr SendGrid-Konto erstellen.
    var username = "your_sendgrid_username";
    var pswd = "your_sendgrid_password";

    var credentials = new NetworkCredential(username, pswd);

Verwenden Sie zum Senden einer E-Mail die Methode **Übermitteln** in der Klasse **SMTP**, die das Protokoll SMTP verwendet, oder in der Transportklasse **REST**, die die Web-API für SendGrid aufruft. Die folgenden Beispiele zeigen, wie Sie eine Nachricht sowohl über SMTP als auch über die Web-API senden.

### SMTP

    // Zuerst das E-Mail-Objekt erstellen, dann die Eigenschaften hinzufügen.
    SendGrid myMessage = SendGrid.GetInstance();
    myMessage.AddTo("anna@contoso.com");
    myMessage.From = new MailAddress("john@contoso.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Anmeldeinformationen durch Angabe von Benutzernamen und Kennwort erstellen.
    var credentials = new NetworkCredential("username", "password");

    // SMTP-Transport zum Senden von E-Mails erstellen.
    var transportSMTP = SMTP.GetInstance(credentials);

    // E-Mail senden.
    transportSMTP.Deliver(myMessage);

### Web-API

    // Zuerst das E-Mail-Objekt erstellen, dann die Eigenschaften hinzufügen.
    SendGrid myMessage = SendGrid.GetInstance();
    myMessage.AddTo("anna@contoso.com");
    myMessage.From = new MailAddress("john@contoso.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Anmeldeinformationen durch Angabe von Benutzernamen und Kennwort erstellen.
    var credentials = new NetworkCredential("username", "password");

    // REST-Transport zum Senden von E-Mails erstellen.
    var transportREST = Web.GetInstance(credentials);

    // E-Mail senden.
    transportREST.Deliver(myMessage);

Vorgehensweise: Hinzufügen eines AnhangsVorgehensweise: Hinzufügen eines Anhangs
--------------------------------------------------------------------------------

Anhänge können mithilfe der Methode **AddAttachment** und durch Angabe des Namens der hinzuzufügenden Datei und des Dateipfads eingefügt werden. Sie können mehrere Anhänge einfügen, indem Sie diese Methode für jede Datei aufrufen, die Sie anhängen möchten. Das folgende Beispiel veranschaulicht, wie ein Anhang in einer Nachricht eingefügt wird:

    SendGrid myMessage = SendGrid.GetInstance();
    myMessage.AddTo("anna@contoso.com");
    myMessage.From = new MailAddress("john@contoso.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.AddAttachment(@"C:\file1.txt");

Vorgehensweise: Verwenden von Filtern zur Aktivierung von Fußzeilen sowie für Nachverfolgungen und AnalysenVorgehensweise: Verwenden von Filtern zur Aktivierung von Fußzeilen sowie für Nachverfolgungen und Analysen
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

SendGrid bietet zusätzliche E-Mail-Funktionen durch die Verwendung von Filtern. Dies sind Einstellungen, die zu einer E-Mail-Nachricht hinzugefügt werden können, um bestimmte Funktionen wie Klickprotokollierung, Google Analytics, Abonnementsverfolgung usw. zu aktivieren. Eine vollständige Liste der verfügbaren Filter finden Sie unter [Filtereinstellungen](http://docs.sendgrid.com/documentation/api/smtp-api/filter-settings/).

Filter können durch als Teil der Klasse **SendGrid** implementierte Methoden auf **SendGrid**-E-Mail-Nachrichten angewendet werden. Bevor Sie Filter für eine E-Mail-Nachricht aktivieren können, müssen Sie zuerst die Liste verfügbarer Filter initialisieren, indem Sie die Methode **InitalizeFilters** aufrufen.

Die folgenden Beispiele veranschaulichen die Filter für die Fußzeile und die Klickprotokollierung:

### Fußzeile

    // Zuerst das E-Mail-Objekt erstellen, dann die Eigenschaften hinzufügen.
    SendGrid myMessage = SendGrid.GetInstance();
    myMessage.AddTo("anna@contoso.com");
    myMessage.From = new MailAddress("john@contoso.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.InitializeFilters();
    // Eine Fußzeile zur Nachricht hinzufügen.
    myMessage.EnableFooter("PLAIN TEXT FOOTER", "<p><em>HTML FOOTER</em></p>");

### Klickprotokollierung

	// Zuerst das E-Mail-Objekt erstellen, dann die Eigenschaften hinzufügen.
	SendGrid myMessage = SendGrid.GetInstance();
	myMessage.AddTo("anna@contoso.com");
	myMessage.From = new MailAddress("john@contoso.com", "John Smith");
	myMessage.Subject = "Testing the SendGrid Library";
	myMessage.Html = "<p data-morhtml="true"><a data-morhtml="true" href=\"http://www.windowsazure.com\">Hello World Link!</a></p>";
	myMessage.Text = "Hello World!";
	
	myMessage.InitializeFilters();
	// "true" zeigt an, dass Links in Nur-Text-Teilen der Mail 
	// ebenfalls für die Überwachung verteilter Links überschrieben werden sollen. 
	myMessage.EnableClickTracking(true);

Vorgehensweise: Verwenden zusätzlicher SendGrid-DiensteVorgehensweise: Verwenden zusätzlicher SendGrid-Dienste
--------------------------------------------------------------------------------------------------------------

SendGrid bietet webbasierte APIs, die Sie zur Nutzung zusätzlicher SendGrid-Funktionen aus Ihrer Azure-Anwendung einsetzen können. Ausführliche Informationen finden Sie in der [SendGrid-API-Dokumentation](http://docs.sendgrid.com/documentation/api/).

Nächste SchritteNächste Schritte
--------------------------------

Nachdem Sie nun mit den Grundlagen des E-Mail-Dienstes SendGrid vertraut sind, finden Sie unter diesen Links weitere Informationen.

-   Repo für die SendGrid C\#-Bibliothek: [sendgrid-csharp](https://github.com/sendgrid/sendgrid-csharp)
-   SendGrid-API-Dokumentation: <http://docs.sendgrid.com/documentation/api/>
-   Spezielles SendGrid-Angebot für Azure-Kunden: <http://sendgrid.com>

