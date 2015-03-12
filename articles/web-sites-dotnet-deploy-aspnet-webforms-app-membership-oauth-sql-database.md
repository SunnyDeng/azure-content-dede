<properties 
	pageTitle="Bereitstellen einer sicheren ASP.NET-Webformularanwendung mit Mitgliedschafts-, OAuth- und SQL-Datenbank für eine Azure-Website" 
	description="In diesem Lernprogramm wird gezeigt, wie Sie eine sichere ASP.NET 4.5 Web Forms-Anwendung erstellen, die eine SQL-Datenbank beinhaltet, und die Anwendung dann auf Azure bereitstellen." 
	services="web-sites" 
	documentationCenter=".net" 
	authors="Erikre" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/25/2014" 
	ms.author="erikre"/>


# Bereitstellen einer sicheren ASP.NET-Webformularanwendung mit Mitgliedschafts-, OAuth- und SQL-Datenbank für eine Azure-Website


In diesem Lernprogramm wird gezeigt, wie Sie eine sichere ASP.NET 4.5 Web Forms-Anwendung erstellen, die eine SQL-Datenbank beinhaltet, und die Anwendung dann auf Azure bereitstellen. 

>[AZURE.NOTE] 
Die MVC-Version dieses Lernprogramm finden Sie unter [Bereitstellen einer sicheren ASP.NET MVC 5-Anwendung mit Mitgliedschafts-, OAuth- und SQL-Datenbank für eine Azure-Website](http://azure.microsoft.com/documentation/articles/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/).

Sie können ein kostenloses Azure-Konto erstellen. Wenn Sie noch nicht über Visual Studio 2013 verfügen, wird Visual Studio 2013 für Web Express automatisch durch das SDK installiert. Sie können kostenlos mit der Entwicklung für Azure beginnen.

Bei diesem Lernprogramm wird davon ausgegangen, dass Sie noch keine Erfahrung mit der Verwendung von Microsoft Azure haben. Nach Abschluss dieses Lernprogramms verfügen Sie über eine Webanwendung, die unter Rückgriff auf eine Clouddatenbank in der Cloud ausgeführt wird.

Sie lernen Folgendes:

- Erstellen eines sicheren ASP.NET 4.5-Webformularprojekts und Veröffentlichen dieses Projekts auf einer Azure-Website
- Verwenden von OAuth und der ASP.NET-Mitgliedschaft zum Sichern der Anwendung
- Verwenden einer einzelnen Datenbank für Mitgliedschafts- und Anwendungsdaten
- Verwenden von Webformulargerüsten zum Erstellen von Webseiten, mit denen Sie Daten ändern können
- Verwenden der neuen Mitgliedschafts-API zum Hinzufügen von Benutzern und Rollen
- Verwenden einer SQL-Datenbank zum Speichern von Daten in Azure

Sie erstellen eine einfache Kontaktlisten-Webanwendung, die auf ASP.NET 4.5-Webformularen beruht und für den Datenbankzugriff Entity Framework verwendet. In der folgenden Abbildung wird die Web Forms-Seite dargestellt, die Lese- und Schreibzugriff auf die Datenbank ermöglicht:

![Contacts - Edit Page](./media/web-sites-dotnet-web-forms-secure/SecureWebForms00.png)

>[AZURE.NOTE] 
Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. Wenn Sie kein Konto haben, können Sie Ihre <a href="/de-de/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F" target="_blank">MSDN-Abonnentenvorteile aktivieren</a> oder <a href="/de-de/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">sich für eine kostenlose Testversion registrieren</a>. Wenn Sie Azure-Websites ausprobieren möchten, ehe Sie sich für ein Konto anmelden, besuchen Sie <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>. Auf dieser Seite können Sie sofort und kostenlos eine befristete ASP.NET Starter Site in Azure-Websites erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

Folgende Themen werden in diesem Lernprogramm behandelt:

- [Einrichten der Entwicklungsumgebung](#set-up-the-development-environment)
- [Einrichten der Azure-Umgebung](#Set-up-the-Azure-environment)
- [Erstellen einer ASP.NET-Webformularanwendung](#Create-an-ASP.NET-Web-Forms-Application)
- [Hinzufügen einer Datenbank zur Anwendung](#Add-a-Database-to-the-Application)
- [Aktivieren von SSL für das Projekt](#Enable-SSL-for-the-Project)
- [Hinzufügen eines OAuth 2.0-Anbieters](#Add-an-OAuth-2.0-Provider)
- [Verwenden der Mitgliedschafts-API zum Beschränken des Zugriffs](#Use-the-Membership-API-to-Restrict-Access)
- [Bereitstellen der Anwendung mit der Datenbank in Azure](#Deploy-the-Application-with-the-Database-to-Azure)
- [Überprüfen der Datenbank](#Review-the-Database)
- [Nächste Schritte](#Next-Steps)

##Einrichten der Entwicklungsumgebung 
Richten Sie zu Beginn Ihre Entwicklungsumgebung ein, indem Sie Visual Studio 2013 und das Azure SDK für .NET installieren.

1. Installieren Sie [Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkId=306566), falls es noch nicht vorhanden ist.  
2. Installieren Sie [Azure SDK für Visual Studio 2013](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409). Für dieses Lernprogramm ist Visual Studio 2013 erforderlich, bevor Sie das Azure SDK für Visual Studio 2013 installieren.  

	>[AZURE.NOTE]  
	Abhängig von der Anzahl an bereits bestehenden SDK-Abhängigkeiten auf Ihrem Computer kann der Installationsvorgang des SDK von mehreren Minuten bis hin zu einer halben Stunde oder länger dauern.  

3. Wenn Sie aufgefordert werden, die ausführbare Installationsdatei auszuführen oder zu speichern, klicken Sie auf **Ausführen**.
4. Klicken Sie im Fenster **Webplattform-Installer** auf **Installieren**, und fahren Sie mit der Installation fort.  
	![Web Platform Installer](./media/web-sites-dotnet-web-forms-secure/Intro-SecureWebForms-01.png)  

	>[AZURE.NOTE]  
	Wenn Sie das SDK bereits installiert haben, müssen 0 Elemente installiert werden. Die Anzahl der zu installierenden Elemente wird unten links im Fenster **Webplattform-Installer** angezeigt.  

5. Falls Sie nicht bereits über **Visual Studio Update 2** verfügen, laden Sie **[Visual Studio 2013 Update 2](http://www.microsoft.com/download/details.aspx?id=42666)** oder höher herunter, und installieren Sie es.  

	>[AZURE.NOTE]  
	Sie müssen Visual Studio 2013 Update 2 oder höher installieren, um Google OAuth 2.0 zu verwenden und um SSL lokal ohne Warnmeldungen verwenden zu können. Außerdem benötigen Sie Update 2 zur Verwendung des Web Forms-Gerüsts.  

Nach Abschluss der Installation haben Sie alles zur Hand, was Sie benötigen, um mit der Entwicklung zu beginnen.

##Einrichten der Azure-Umgebung
In diesem Abschnitt richten Sie die Azure-Umgebung ein, indem Sie eine Azure-Website und eine SQL-Datenbank in Azure erstellen.

###Erstellen einer Website und einer SQL-Datenbank in Azure 
In diesem Lernprogramm wird Ihre Azure-Website in einer freigegebenen Hostingumgebung und damit auf virtuellen Computern (VMs) ausgeführt, die für andere Azure-Websites freigegeben wurden. Eine freigegebene Hostingumgebung ist eine kostengünstige Möglichkeit, mit der Verwendung der Cloud zu beginnen. Später kann die Anwendung skaliert werden, sobald der Webdatenverkehr zunimmt, um die Anforderungen durch die Ausführung dedizierter virtueller Maschinen zu erfüllen. Wenn Sie eine komplexere Architektur benötigen, können Sie eine Migration zu einem Azure-Clouddienst ausführen. Clouddienste werden auf dedizierten VMs ausgeführt, die Sie Ihren Anforderungen entsprechend konfigurieren können. 

Die Azure SQL-Datenbank ist ein cloudbasierter relationaler Datenbankdienst auf Grundlage von SQL Server-Technologien. Die Tools und Anwendungen, die mit SQL Server verwendet werden können, sind auch für die SQL-Datenbank geeignet.

1. Klicken Sie im [Azure-Verwaltungsportal](https://manage.windowsazure.com/) auf der linken Registerkarte auf **Websites** und dann auf **Neu**.  
	![Web Platform Installer](./media/web-sites-dotnet-web-forms-secure/Intro-SecureWebForms-02.png)
2. Klicken Sie auf **Website** und dann auf **Benutzerdefiniert erstellen**.  
	![Custom Create](./media/web-sites-dotnet-web-forms-secure/Intro-SecureWebForms-03.png)  
	Der Assistent **Neue Website - benutzerdefiniert erstellen** wird angezeigt.  

3. Geben Sie im Schritt **Website erstellen** des Assistenten eine Zeichenfolge in das Feld **URL** ein, die als eindeutige URL der Anwendung dienen soll. Die vollständige URL besteht aus der hier eingegebenen Zeichenfolge und dem Suffix, das neben dem Textfeld aufgeführt wird. In der Abbildung ist eine URL dargestellt, die vermutlich bereits vergeben ist, sodass **eine andere URL ausgewählt werden muss**.  
	![Contacts - Create New Web Site](./media/web-sites-dotnet-web-forms-secure/Intro-SecureWebForms-04.png)  
4. Wählen Sie in der Dropdownliste "Webhosting-Plan" die am nächsten befindliche Region aus. Mit dieser Einstellung wird das Datencenter angegeben, in dem Ihr virtueller Computer ausgeführt wird.
5. Wählen Sie in der Dropdownliste **Datenbank** die Option **Eine kostenlose 20-MB-SQL-Datenbank erstellen** aus.
6. Behalten Sie im Feld **Name der Datenbankverbindungszeichenfolge** den Standardwert  *DefaultConnection* bei.
7. Klicken Sie unten im Feld auf den Pfeil. 
Der Assistent springt zum Schritt **Datenbankeinstellungen angeben**.
8. Geben Sie im Feld **Name** folgenden Namen ein: *`ContactDB`*.  
	![Database Settings](./media/web-sites-dotnet-web-forms-secure/Intro-SecureWebForms-05.png)  
9. Wählen Sie im Feld **Server** den Eintrag **Neuer SQL-Datenbankserver** aus.
Wenn Sie bereits zuvor eine SQL Server-Datenbank erstellt haben, können Sie alternativ diese SQL Server-Instanz aus der Dropdownliste auswählen.
10. Legen Sie **Region** auf dasselbe Gebiet fest wie bei der Erstellung der Website.
11. Geben Sie unter **Anmeldename** und **Kennwort** die Anmeldedaten eines Administrators ein. 
Wenn Sie **Neuer SQL-Datenbankserver** ausgewählt haben, geben Sie hier keinen vorhandenen Namen und kein vorhandenes Kennwort ein. Stattdessen definieren Sie jetzt einen neuen Namen und ein neues Kennwort zur späteren Verwendung beim Datenbankzugriff. Wenn Sie eine zuvor erstellte SQL Server-Version ausgewählt haben, werden Sie aufgefordert, das Kennwort für den zuvor erstellten SQL Server-Kontonamen einzugeben. Für dieses Lernprogramm wird das Feld **Erweitert** nicht aktiviert.
12. Klicken Sie auf das Häkchen unten rechts im Feld, um anzugeben, dass Sie fertig sind.

Sie kehren zur Seite **Websites** im **Azure-Verwaltungsportal** zurück, und in der Spalte **Status** wird angegeben, dass die Website erstellt wird. Kurz danach (normalerweise nach weniger als einer Minute) wird in der Spalte **Status** angegeben, dass die Website erfolgreich erstellt wurde. In der linken Navigationsleiste wird die Anzahl der Websites in Ihrem Konto neben dem Symbol **Websites** und die Anzahl der Datenbanken neben dem Symbol **SQL-Datenbanken** angegeben.
##Erstellen einer ASP.NET-Webformularanwendung 
Sie haben nun eine Azure-Website erstellt, diese enthält jedoch noch keinen Inhalt. Als Nächstes erstellen Sie die Visual Studio Web App, die Sie auf Azure veröffentlichen.
###Erstellen des Projekts 
1. Wählen Sie im Menü **Datei** in Visual Studio die Option **Neues Projekt** aus.  
	![File Menu - New Project](./media/web-sites-dotnet-web-forms-secure/SecureWebForms01.png)  
2. Wählen Sie die Vorlagengruppe **Templates** -> **Visual C#** -> **Web** auf der linken Seite aus. 
3. Wählen Sie in der mittleren Spalte die Vorlage **ASP.NET-Webanwendung** aus.
4. Benennen Sie Ihr Projekt *ContactManager*, und klicken Sie auf **OK**.  
	![New Project Dialog](./media/web-sites-dotnet-web-forms-secure/SecureWebForms02.png)  

	>[AZURE.NOTE]  
	Der Name des Projekts in diesem Lernprogramm ist **ContactManager**. Es wird empfohlen, dass Sie genau diesen Projektnamen verwenden, damit der im Lernprogramm angegebene Code wie erwartet funktioniert.  

5. Wählen Sie im Dialogfeld **Neues ASP.NET-Projekt** die Vorlage **Web Forms** aus. Deaktivieren Sie das Kontrollkästchen **Host in der Cloud**, wenn es aktiviert ist, und klicken Sie auf **OK**.  
	![Dialogfeld "Neues ASP.NET-Projekt"](./media/web-sites-dotnet-web-forms-secure/SecureWebForms03.png)  
	Die Web Forms-Anwendung wird erstellt.
###Aktualisieren der Masterseite
In ASP.NET Web Forms können Sie mit Masterseiten ein einheitliches Layout für die Seiten in Ihrer Anwendung erstellen. Eine einzige Masterseite definiert das Aussehen und das Standardverhalten, das Sie für alle Seiten (oder eine Gruppe von Seiten) in Ihrer Anwendung wünschen. Anschließend können Sie die einzelnen Inhaltsseiten mit dem anzuzeigenden Inhalt erstellen. Wenn Benutzer Inhaltsseiten anfordern, werden diese in ASP.NET mit der Masterseite zusammengeführt, um eine Ausgabe zu erstellen, in der das Layout der Masterseite mit dem Inhalt der Inhaltsseite kombiniert wird.
Für die neue Seite muss der Anwendungsname und der Link aktualisiert werden. Dieser Link verweist auf die Seite, welche die Kontaktdetails enthält. Um diese Änderungen vorzunehmen, ändern Sie das HTML auf der Masterseite. 

1. Öffnen Sie im **Projektmappen-Explorer** die Seite  *Site.Master*. 
2. Falls diese Seite sich in der **Entwurfsansicht** befindet, wechseln Sie zur **Quellansicht**.
3. Aktualisieren Sie die Masterseite, indem Sie die gelb hervorgehobenen Markups hinzufügen bzw. anpassen.

<pre class="prettyprint">
&lt;%@ Master Language=&quot;C#&quot; AutoEventWireup=&quot;true&quot; CodeBehind=&quot;Site.master.cs&quot; Inherits=&quot;ContactManager.SiteMaster&quot; %&gt;

&lt;!DOCTYPE html&gt;

&lt;html lang=&quot;en&quot;&gt;
&lt;head runat=&quot;server&quot;&gt;
    &lt;meta charset=&quot;utf-8&quot; /&gt;
    &lt;meta name=&quot;viewport&quot; content=&quot;width=device-width, initial-scale=1.0&quot; /&gt;
    &lt;title&gt;&lt;%: Page.Title %&gt; - <mark>Contact Manager</mark>&lt;/title&gt;

    &lt;asp:PlaceHolder runat=&quot;server&quot;&gt;
        &lt;%: Scripts.Render(&quot;~/bundles/modernizr&quot;) %&gt;
    &lt;/asp:PlaceHolder&gt;
    &lt;webopt:bundlereference runat=&quot;server&quot; path=&quot;~/Content/css&quot; /&gt;
    &lt;link href=&quot;~/favicon.ico&quot; rel=&quot;shortcut icon&quot; type=&quot;image/x-icon&quot; /&gt;

&lt;/head&gt;
&lt;body&gt;
    &lt;form runat=&quot;server&quot;&gt;
        &lt;asp:ScriptManager runat=&quot;server&quot;&gt;
            &lt;Scripts&gt;
                &lt;%--To learn more about bundling scripts in ScriptManager see http://go.microsoft.com/fwlink/?LinkID=301884 --%&gt;
                &lt;%--Framework Scripts--%&gt;
                &lt;asp:ScriptReference Name=&quot;MsAjaxBundle&quot; /&gt;
                &lt;asp:ScriptReference Name=&quot;jquery&quot; /&gt;
                &lt;asp:ScriptReference Name=&quot;bootstrap&quot; /&gt;
                &lt;asp:ScriptReference Name=&quot;respond&quot; /&gt;
                &lt;asp:ScriptReference Name=&quot;WebForms.js&quot; Assembly=&quot;System.Web&quot; Path=&quot;~/Scripts/WebForms/WebForms.js&quot; /&gt;
                &lt;asp:ScriptReference Name=&quot;WebUIValidation.js&quot; Assembly=&quot;System.Web&quot; Path=&quot;~/Scripts/WebForms/WebUIValidation.js&quot; /&gt;
                &lt;asp:ScriptReference Name=&quot;MenuStandards.js&quot; Assembly=&quot;System.Web&quot; Path=&quot;~/Scripts/WebForms/MenuStandards.js&quot; /&gt;
                &lt;asp:ScriptReference Name=&quot;GridView.js&quot; Assembly=&quot;System.Web&quot; Path=&quot;~/Scripts/WebForms/GridView.js&quot; /&gt;
                &lt;asp:ScriptReference Name=&quot;DetailsView.js&quot; Assembly=&quot;System.Web&quot; Path=&quot;~/Scripts/WebForms/DetailsView.js&quot; /&gt;
                &lt;asp:ScriptReference Name=&quot;TreeView.js&quot; Assembly=&quot;System.Web&quot; Path=&quot;~/Scripts/WebForms/TreeView.js&quot; /&gt;
                &lt;asp:ScriptReference Name=&quot;WebParts.js&quot; Assembly=&quot;System.Web&quot; Path=&quot;~/Scripts/WebForms/WebParts.js&quot; /&gt;
                &lt;asp:ScriptReference Name=&quot;Focus.js&quot; Assembly=&quot;System.Web&quot; Path=&quot;~/Scripts/WebForms/Focus.js&quot; /&gt;
                &lt;asp:ScriptReference Name=&quot;WebFormsBundle&quot; /&gt;
                &lt;%--Site Scripts--%&gt;
            &lt;/Scripts&gt;
        &lt;/asp:ScriptManager&gt;

        &lt;div class=&quot;navbar navbar-inverse navbar-fixed-top&quot;&gt;
            &lt;div class=&quot;container&quot;&gt;
                &lt;div class=&quot;navbar-header&quot;&gt;
                    &lt;button type=&quot;button&quot; class=&quot;navbar-toggle&quot; data-toggle=&quot;collapse&quot; data-target=&quot;.navbar-collapse&quot;&gt;
                        &lt;span class=&quot;icon-bar&quot;&gt;&lt;/span&gt;
                        &lt;span class=&quot;icon-bar&quot;&gt;&lt;/span&gt;
                        &lt;span class=&quot;icon-bar&quot;&gt;&lt;/span&gt;
                    &lt;/button&gt;
                    &lt;a class=&quot;navbar-brand&quot; runat=&quot;server&quot; <mark>id=&quot;ContactDemoLink&quot;</mark> href=&quot;~/<mark>Contacts/Default.aspx</mark>&quot;&gt;<mark>Contact Demo</mark>&lt;/a&gt;
                &lt;/div&gt;
                &lt;div class=&quot;navbar-collapse collapse&quot;&gt;
                    &lt;ul class=&quot;nav navbar-nav&quot;&gt;
                        &lt;li&gt;&lt;a runat=&quot;server&quot; href=&quot;~/&quot;&gt;Home&lt;/a&gt;&lt;/li&gt;
                        &lt;li&gt;&lt;a runat=&quot;server&quot; href=&quot;~/About&quot;&gt;About&lt;/a&gt;&lt;/li&gt;
                        &lt;li&gt;&lt;a runat=&quot;server&quot; href=&quot;~/Contact&quot;&gt;Contact&lt;/a&gt;&lt;/li&gt;
                    &lt;/ul&gt;
                    &lt;asp:LoginView runat=&quot;server&quot; ViewStateMode=&quot;Disabled&quot;&gt;
                        &lt;AnonymousTemplate&gt;
                            &lt;ul class=&quot;nav navbar-nav navbar-right&quot;&gt;
                                &lt;li&gt;&lt;a runat=&quot;server&quot; href=&quot;~/Account/Register&quot;&gt;Register&lt;/a&gt;&lt;/li&gt;
                                &lt;li&gt;&lt;a runat=&quot;server&quot; href=&quot;~/Account/Login&quot;&gt;Log in&lt;/a&gt;&lt;/li&gt;
                            &lt;/ul&gt;
                        &lt;/AnonymousTemplate&gt;
                        &lt;LoggedInTemplate&gt;
                            &lt;ul class=&quot;nav navbar-nav navbar-right&quot;&gt;
                                &lt;li&gt;&lt;a runat=&quot;server&quot; href=&quot;~/Account/Manage&quot; title=&quot;Manage your account&quot;&gt;Hello, &lt;%: Context.User.Identity.GetUserName()  %&gt; !&lt;/a&gt;&lt;/li&gt;
                                &lt;li&gt;
                                    &lt;asp:LoginStatus runat=&quot;server&quot; LogoutAction=&quot;Redirect&quot; LogoutText=&quot;Log off&quot; LogoutPageUrl=&quot;~/&quot; OnLoggingOut=&quot;Unnamed_LoggingOut&quot; /&gt;
                                &lt;/li&gt;
                            &lt;/ul&gt;
                        &lt;/LoggedInTemplate&gt;
                    &lt;/asp:LoginView&gt;
                &lt;/div&gt;
            &lt;/div&gt;
        &lt;/div&gt;
        &lt;div class=&quot;container body-content&quot;&gt;
            &lt;asp:ContentPlaceHolder ID=&quot;MainContent&quot; runat=&quot;server&quot;&gt;
            &lt;/asp:ContentPlaceHolder&gt;
            &lt;hr /&gt;
            &lt;footer&gt;
                &lt;p&gt;&amp;copy; &lt;%: DateTime.Now.Year %&gt; - <mark>Contact Manager</mark>&lt;/p&gt;
            &lt;/footer&gt;
        &lt;/div&gt;
    &lt;/form&gt;
&lt;/body&gt;
&lt;/html&gt;
</pre>  

Später in diesem Lernprogramm fügen Sie ein Web Forms-Gerüst hinzu. Das Gerüst erstellt die Seite, auf die der oben angegebene Link "Contact Demo" verweist.
###Lokales Ausführen der Anwendung 
1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf die Seite  *Default.aspx*, und wählen Sie **Als Startseite festlegen** aus. 
2. Drücken Sie **STRG+F5**, um die Anwendung auszuführen.  
	Die Standardseite der Anwendung wird im Standardbrowser angezeigt.
	![Contacts - Create New Web Site](./media/web-sites-dotnet-web-forms-secure/SecureWebForms04.png)  

Darüber hinaus müssen Sie keine weiteren Aktionen ausführen, um die Anwendung zu erstellen, die Sie für Azure bereitstellen. Später fügen Sie Datenbankfunktionalität sowie die erforderlichen Seiten zum Anzeigen und Bearbeiten der Kontaktdaten hinzu.
###Bereitstellen der Anwendung in Azure
Nachdem Sie nun die Anwendung erstellt und lokal ausgeführt haben, wird es Zeit, die Anwendung in Azure bereitzustellen.

1. Klicken Sie im **Projektmappen-Explorer** von Visual Studio mit der rechten Maustaste auf das Projekt, und wählen Sie im Kontextmenü **Veröffentlichen** aus.  
	![Select Publish](./media/web-sites-dotnet-web-forms-secure/SecureWebForms05.png)  
	Das Dialogfeld **Web veröffentlichen** wird angezeigt.  

2. Klicken Sie auf der Registerkarte **Profil** des Dialogfelds **Web veröffentlichen** auf **Azure-Websites**.  
	![Publish Web dialog box](./media/web-sites-dotnet-web-forms-secure/SecureWebForms06.png)  
3. Falls Sie nicht bereits angemeldet sind, klicken Sie im Dialogfeld **Select Existing Website** auf die Schaltfläche **Sign In**. Sobald Sie angemeldet sind, wählen Sie die Website aus, die Sie im ersten Teil dieses Lernprogramms erstellt haben. Klicken Sie auf **OK**, um fortzufahren.  
	![Select Existing Web Site dialog box](./media/web-sites-dotnet-web-forms-secure/SecureWebForms07.png)  
Visual Studio lädt Ihre Veröffentlichungseinstellungen herunter.
4. Klicken Sie im Dialogfeld **Web veröffentlichen** auf **Veröffentlichen**.  
	![Publish Web dialog box](./media/web-sites-dotnet-web-forms-secure/SecureWebForms08.png)  
Im Fenster **Web Publish Activity** in Visual Studio wird der gesamte Veröffentlichungsstatus angezeigt:  
	![Web Publishing Activity](./media/web-sites-dotnet-web-forms-secure/SecureWebForms09.png)  

Die erstellte Anwendung wird nun in der Cloud ausgeführt. Bei der nächsten Bereitstellung der Anwendung von Visual Studio aus werden nur die geänderten (oder neuen) Dateien bereitgestellt.  
	![App in Browser](./media/web-sites-dotnet-web-forms-secure/SecureWebForms10.png)  

>[AZURE.NOTE] 
Wenn bei der Veröffentlichung auf einer bereits eingerichteten Website ein Fehler auftritt, können Sie den Standort vor dem Hinzufügen der neuen Dateien löschen.  
Veröffentlichen Sie Ihre Anwendung erneut, wählen Sie im Dialogfeld **Web veröffentlichen** jedoch die Registerkarte **Einstellungen** aus. Stellen Sie anschließend die Konfiguration auf **Debug** ein, und wählen Sie die Option **Remove additional files at destination** aus. Verwenden Sie **Veröffentlichen**, um die Anwendung erneut bereitzustellen.  
	![Publish Web dialog box](./media/web-sites-dotnet-web-forms-secure/SecureWebForms11.png)  

##Hinzufügen einer Datenbank zur Anwendung 
Als Nächstes aktualisieren Sie die Web Forms-Anwendung, um Funktionen zum Anzeigen und Aktualisieren von Kontakten sowie zum Speichern der Daten in der Standarddatenbank hinzuzufügen. Beim Erstellen des Web Forms-Projekts wurde die Datenbank standardmäßig ebenfalls erstellt. Die Anwendung verwendet Entity Framework, um auf die Datenbank zuzugreifen und Daten in der Datenbank zu lesen und zu aktualisieren.
###Hinzufügen einer Datenmodellklasse 
Sie beginnen mit der Erstellung eines einfachen Datenmodells mithilfe von Code. Dieses Datenmodell befindet sich in einer Klasse namens `Contacts`. Der `Contacts`-Klassenname wurde ausgewählt, um einen Namenskonflikt mit der `Contact`-Klasse in der Datei "Contact.aspx.cs" zu vermeiden, die von der Web Forms-Vorlage erstellt wurde.  

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf  *Models* und anschließend auf **Hinzufügen** -> **Klasse**.  
	![Select Class](./media/web-sites-dotnet-web-forms-secure/SecureWebForms12.png)  
	Das Dialogfeld **Neues Element hinzufügen** wird angezeigt.  

2. Nennen Sie diese neue Klasse *Contacts.cs*.  
	![Add New Item dialog box](./media/web-sites-dotnet-web-forms-secure/SecureWebForms13.png)  
3. Ersetzen Sie den Standardcode durch den folgenden Code:  
	<pre class="prettyprint">
	using System.ComponentModel.DataAnnotations;
	using System.Globalization;
	
	namespace ContactManager.Models
	{
	    public class Contacts
	    {
	        [ScaffoldColumn(false)]
	        [Key]
	        public int ContactId { get; set; }
	        public string Name { get; set; }
	        public string Address { get; set; }
	        public string City { get; set; }
	        public string State { get; set; }
	        public string Zip { get; set; }
	        [DataType(DataType.EmailAddress)]
	        public string Email { get; set; }
	    }
	}
	</pre>

Mit der Klasse **Contacts** werden die Daten definiert, die für die einzelnen Kontakte gespeichert werden, sowie ein Primärschlüssel (`ContactID`), der von der Datenbank benötigt wird. Die Klasse **Contacts** enthält die anzuzeigenden Kontaktdaten. Jede Instanz eines Contacts-Objekts entspricht einer Zeile in einer relationalen Datenbanktabelle, und jede Eigenschaft der Contacts-Klasse ist einer Spalte in der relationalen Datenbanktabelle zugeordnet. Im Verlauf dieses Lernprogramms überprüfen Sie die in der Datenbank enthaltenen Kontaktdaten.

###Web Forms-Gerüst 
Sie haben die Modellklasse **Contacts** oben erstellt. Nun können Sie das Web Forms-Gerüst verwenden, um die Seiten *List*, *Insert*, *Edit* und *Delete* zu generieren, die beim Arbeiten mit diesen Daten verwendet werden. Der Web Forms-Gerüstbauer (Scaffolder) verwendet Entity Framework, Bootstrap und Dynamic Data. Standardmäßig wird der Web Forms-Gerüstbauer als Erweiterung zu Ihrem Projekt als Teil der Projektvorlage bei der Verwendung von Visual Studio 2013 installiert.

Mit den folgenden Schritten können Sie den Web Forms-Gerüstbauer verwenden.

1. Wählen Sie in der Menüleiste in Visual Studio **Extras** -> **Erweiterungen und Updates**.  
	Das Dialogfeld **Erweiterungen und Updates** wird angezeigt.
2. Wählen Sie im linken Bereich des Dialogfelds **Online** -> **Visual Studio Gallery** -> **Extras** -> **Gerüstbau** aus.
3. Falls 'Web Forms Scaffolding' nicht in der Liste angezeigt wird, geben Sie 'Web Forms Scaffolding' in das Suchfeld rechts im Dialogfeld ein.  
4. Wenn Web Forms Scaffolder nicht bereits installiert ist, wählen Sie **Herunterladen** aus, um 'Web Forms Scaffolding' herunterzuladen und zu installieren. Starten Sie Visual Studio ggf. neu. Speichern Sie Ihre Änderungen am Projekt, wenn Sie dazu aufgefordert werden.  
	![Extensions and Updates dialog box](./media/web-sites-dotnet-web-forms-secure/ExtensionsAndUpdatesDB.png)  
5. Erstellen Sie das Projekt **(STRG+UMSCHALT+B)**.  
	Sie müssen das Projekt vor der Verwendung des Gerüstmechanismus erstellen.  
6. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das *project*, und wählen Sie dann **Hinzufügen** -> **Neu erstelltes Element** aus.  
	Das Dialogfeld **Gerüst hinzufügen** wird angezeigt.
7. Wählen Sie im linken Fensterbereich **Web Forms** und im mittleren Fensterbereich **Web Forms Pages using Entity Framework** aus. Klicken Sie anschließend auf **Hinzufügen**.  
	![Add Scaffold dialog box](./media/web-sites-dotnet-web-forms-secure/SecureWebForms13a.png)  
	Das Dialogfeld **Add Web Forms Pages** wird angezeigt.  

8. Setzen Sie im Dialogfeld **Add Web Forms Pages** die **Modellklasse** auf  `Contact (ContactManager.Models)`. Setzen Sie **Datenkontextklasse** auf  `ApplicationDbContext (ContactManager.Models)`. Klicken Sie anschließend auf **Hinzufügen**. 
	![Add Web Forms Pages dialog box](./media/web-sites-dotnet-web-forms-secure/SecureWebForms13b.png)  

Der Web Forms Scaffolder fügt einen neuen Ordner mit den Seiten *Default.aspx*, *Delete.aspx*, *Edit.aspx* und *Insert.aspx* hinzu. Außerdem erstellt der Web Forms Scaffolder einen Ordner *DynamicData*, der die Ordner *EntityTemplates* und *FieldTemplates* enthält. Der `ApplicationDbContext`-Code wird für die Mitgliedschaftsdatenbank und die Kontaktdaten verwendet.

###Konfigurieren der Anwendung zur Verwendung des Datenmodells 
Die nächste Aufgabe besteht darin, das Feature Code First-Migrationen zu aktivieren, um die Datenbank basierend auf dem erstellten Datenmodell zu erstellen. Außerdem fügen Sie Beispieldaten und einen Dateninitialisierer hinzu.  

1. Wählen Sie im Menü **Extras** den Eintrag **NuGet Package Manager** und danach **Paket-Manager-Konsole** aus.  
	![Add Web Forms Pages dialog box](./media/web-sites-dotnet-web-forms-secure/SecureWebForms13c.png)  
2. Geben Sie im Fenster *Paket-Manager-Konsole* den folgenden Befehl ein:  
	<pre class="prettyprint">
	enable-migrations
	</pre>  
	Mit dem Befehl "enable-migrations" wird der Ordner *Migrations* erstellt und die Datei *Configuration.cs* darin abgelegt. Sie können diese Datei bearbeiten, um ein Seeding für die Datenbank auszuführen und Datenmigrationen zu konfigurieren.  
3. Geben Sie im Fenster **Paket-Manager-Konsole** den folgenden Befehl ein:  
	<pre class="prettyprint">
	add-migration Initial
	</pre>  
	Mit dem Befehl `add-migration Initial` wird eine Datei namens <date_stamp>"Initial" im Ordner *Migrations* generiert, mit der die Datenbank erstellt wird. Der erste Parameter (Initial) ist willkürlich und wird zum Erstellen des Dateinamens verwendet. Sie können die neue Klasse im **Projektmappen-Explorer** anzeigen. In der `Initial`-Klasse wird mit der `Up`-Methode die Tabelle `Contact` erstellt und mit der `Down`-Methode (wird verwendet, wenn Sie zum vorherigen Status zurückkehren möchten) wieder verworfen.  
4. Öffnen Sie die Datei *Migrations\Configuration.cs*. 
5. Fügen Sie den folgenden Namespace hinzu:  
	<pre class="prettyprint">
	using ContactManager.Models;
	</pre>  
6. Ersetzen Sie die `Seed`-Methode durch den folgenden Code:  
	<pre class="prettyprint">
	protected override void Seed(ContactManager.Models.ApplicationDbContext context)
	{
	    context.Contacts.AddOrUpdate(p => p.Name,
	       new Contacts
	       {
	           ContactId = 1,
	           Name = "Ivan Irons",
	           Address = "One Microsoft Way",
	           City = "Redmond",
	           State = "WA",
	           Zip = "10999",
	           Email = "ivani@wideworldimporters.com",
	       },
	       new Contacts
	        {
	            ContactId = 2,
	            Name = "Brent Scholl",
	            Address = "5678 1st Ave W",
	            City = "Redmond",
	            State = "WA",
	            Zip = "10999",
	            Email = "brents@wideworldimporters.com",
	        },
	        new Contacts
	        {
	            ContactId = 3,
	            Name = "Terrell Bettis",
	            Address = "9012 State St",
	            City = "Redmond",
	            State = "WA",
	            Zip = "10999",
	            Email = "terrellb@wideworldimporters.com",
	        },
	        new Contacts
	        {
	            ContactId = 4,
	            Name = "Jo Cooper",
	            Address = "3456 Maple St",
	            City = "Redmond",
	            State = "WA",
	            Zip = "10999",
	            Email = "joc@wideworldimporters.com",
	        },
	        new Contacts
	        {
	            ContactId = 5,
	            Name = "Ines Burnett",
	            Address = "7890 2nd Ave E",
	            City = "Redmond",
	            State = "WA",
	            Zip = "10999",
	            Email = "inesb@wideworldimporters.com",
	        }
	        );
	}
	</pre>
Mit diesem Code wird die Datenbank mit den Kontaktinformationen initialisiert (es wird ein Seeding dafür ausgeführt). Weitere Informationen zum Ausführen eines Seedings für die Datenbank finden Sie unter [Seeding and Debugging Entity Framework (EF) DBs](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx).  
7. Geben Sie im Fenster **Paket-Manager-Konsole** den folgenden Befehl ein:  
	<pre class="prettyprint">
	update-database
	</pre>  
Der `update-database`-Code führt die erste Migration aus, wodurch die Datenbank erstellt wird. Standardmäßig wird die Datenbank als SQL Server Express LocalDB-Datenbank erstellt.  
	![Package Manager Console](./media/web-sites-dotnet-web-forms-secure/SecureWebForms13d.png)  

###Lokales Ausführen der Anwendung und Anzeigen der Daten 
Führen Sie die Anwendung jetzt aus, um zu sehen, wie Sie die Kontakte anzeigen können.

1. Erstellen Sie zuerst das Projekt (**STRG+UMSCHALT+B**).  
2. Drücken Sie **STRG+F5**, um die Anwendung auszuführen.  
	Der Browser wird geöffnet und zeigt die Seite  *Default.aspx* an:
3. Wählen Sie den Link **Contact Demo** oben auf der Seite aus, um die Seite *Contact List* anzuzeigen.  
	![Contacts List Page](./media/web-sites-dotnet-web-forms-secure/SecureWebForms17.png)  

##Aktivieren von SSL für das Projekt 
Secure Sockets Layer (SSL) ist ein Protokoll, das Webservern und Webclients eine sichere Kommunikation durch Verschlüsselung ermöglicht. Wird kein SSL verwendet, können die zwischen Client und Server gesendeten Daten von jedem mit physischem Zugriff auf das Netzwerk per Paket-Sniffing ausgespäht werden. Außerdem sind verschiedene häufig verwendete Authentifizierungsschemas über einfaches HTTP nicht sicher. Insbesondere senden die einfache Authentifizierung und Formularauthentifizierung unverschlüsselte Anmeldeinformationen. Aus Sicherheitsgründen sollten diese Authentifizierungsschemas SSL verwenden.

1. Klicken Sie im **Projektmappen-Explorer** auf das Projekt **ContactManager**, und drücken Sie **F4**, um das Fenster** Eigenschaften** anzuzeigen. 
2. Ändern Sie **SSL-aktiviert** in `true`. 
3. Kopieren Sie die **SSL-URL**, damit Sie sie später verwenden können.  
	Die SSL-URL lautet `https://localhost:44300/`, sofern Sie zuvor noch keine SSL-Websites erstellt haben (wie unten dargestellt).
	![Project Properties](./media/web-sites-dotnet-web-forms-secure/SecureWebForms18.png)  
4. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **Contact Manager** und dann auf **Eigenschaften**.
5. Klicken Sie auf der linken Registerkarte auf **Web**.
6. Ändern Sie die **Projekt-URL** so, dass die zuvor gespeicherte **SSL-URL** verwendet wird.  
	![Project Web Properties](./media/web-sites-dotnet-web-forms-secure/SecureWebForms19.png)  
7. Drücken Sie **STRG+S**, um die Seite zu speichern.
8. Drücken Sie **STRG+F5**, um die Anwendung auszuführen.  
In Visual Studio wird eine Option angezeigt, mit der Sie SSL-Warnungen vermeiden können.  
9. Klicken Sie auf **Ja**, um dem IIS Express-SSL-Zertifikat zu vertrauen und fortzufahren.  
	![IIS Express SSL certificate details](./media/web-sites-dotnet-web-forms-secure/SecureWebForms20.png)  
	Es wird eine Sicherheitswarnung angezeigt.  

10. Klicken Sie auf **Ja**, um das Zertifikat auf Ihrem "localhost" zu installieren.  
	![Security Warning dialog box](./media/web-sites-dotnet-web-forms-secure/SecureWebForms21.png)  
	Das Browserfenster wird angezeigt.  

Sie können Ihre Webanwendung ganz einfach lokal mit SSL testen.



##Hinzufügen eines OAuth 2.0-Anbieters 
ASP.NET Web Forms bietet erweiterte Optionen für Mitgliedschaft und Authentifizierung. Diese Erweiterungen umfassen OAuth. OAuth ist ein offenes Protokoll, das die sichere Autorisierung in einer einfachen und Standardmethode von Web-, Mobil- und Desktopanwendungen ermöglicht. OAuth wird von der ASP.NET MVC-Internetvorlage verwendet, um Facebook, Twitter, Google und Microsoft als Authentifizierungsanbieter verfügbar zu machen. Auch wenn in diesem Lernprogramm nur Google als Authentifizierungsanbieter eingesetzt wird, können Sie den Code problemlos für die Verwendung einer der anderen Anbieter anpassen. Die Schritte zur Implementierung anderer Anbieter unterscheiden sich kaum von den Schritten in diesem Lernprogramm. 

Abgesehen von der Authentifizierung werden in diesem Lernprogramm auch Rollen verwendet, um die Autorisierung zu implementieren. Nur Benutzer, die Sie der Rolle `canEdit` hinzufügen, können Kontakte erstellen, bearbeiten oder löschen. 

Mit den folgenden Schritten können Sie einen Google-Authentifizierungsanbieter hinzufügen.

1. Öffnen Sie die Datei *App_Start\Startup.Auth.cs*. 
2. Entfernen Sie die Kommentarzeichen aus der `app.UseGoogleAuthentication()`-Methode, sodass sie wie folgt aussieht:  
	<pre class="prettyprint">
	            app.UseGoogleAuthentication(new GoogleOAuth2AuthenticationOptions()
	            {
	                ClientId = "",
	                ClientSecret = ""
	            });
	</pre>
3. Navigieren Sie zur [Google Developers Console](https://console.developers.google.com/). Außerdem müssen Sie sich mit Ihrem Google Entwickler-E-Mail-Konto anmelden (gmail.com). Wenn Sie nicht über ein Google-Konto verfügen, wählen Sie den Link **Konto erstellen** aus.  
	Als Nächstes wird die **Google-Entwickler-Konsole** angezeigt.
	![Google Developers Console](./media/web-sites-dotnet-web-forms-secure/SecureWebForms21a.png)  

4. Klicken Sie auf **Create Project**, und geben Sie einen Projektnamen und die ID ein (Sie können die Standardwerte verwenden). Klicken Sie anschließend in das **Vereinbarungskontrollkästchen**, und klicken Sie auf die Schaltfläche **Erstellen**.  
	![Google - New Project](./media/web-sites-dotnet-web-forms-secure/SecureWebForms21b.png)  
	Das neue Projekt wird in einigen Sekunden erstellt, und in Ihrem Browser wird die neue Projektseite angezeigt.
5. Klicken Sie auf der linken Registerkarte auf **APIs & auth** und dann auf **Credentials**.
6. Klicken Sie unter **OAuth** auf **Create New Client ID**.  
	Das Dialogfeld **Create Client ID** wird angezeigt.
	![Google - Create Client ID](./media/web-sites-dotnet-web-forms-secure/SecureWebForms21c.png)  
7. Behalten Sie im Dialogfeld **Create Client ID** den Standardwert **Web application** für den Anwendungstyp bei.  
8. Legen Sie die **Authorized JavaScript Origins** auf die SSL-URL fest, die Sie zuvor in diesem Lernprogramm verwendet haben (**https://localhost:44300/**, sofern Sie keine anderen SSL-Projekte erstellt haben).  
	Diese URL ist der Ursprung für Ihre Anwendung. In diesem Beispiel geben Sie nur die Test-URL "localhost" ein. Sie können jedoch mehrere URLs eingeben, um "localhost" und die Produktion einzubeziehen.  

9. Stellen Sie den **Authorized Redirect URI** wie folgt ein:  
	<pre class="prettyprint">  
	https://localhost:44300/signin-google  
	</pre>  
	Dieser Wert steht für den URI, den ASP.NET OAuth-Benutzer zur Kommunikation mit dem Google OAuth-Server verwenden. Geben Sie die oben verwendete SSL-URL ein (**https://localhost:44300/**, sofern Sie nicht andere SSL-Projekte erstellt haben).  
10. Klicken Sie auf die Schaltfläche **Create Client ID**.
11. Aktualisieren Sie in Visual Studio die `UseGoogleAuthentication`-Methode der Seite *Startup.Auth.cs*, indem Sie **AppId** und **App Secret** kopieren und in die Methode einfügen. Die unten dargestellten Werte für **AppId** und **App Secret** sind Beispiele und würden nicht funktionieren.  
	<pre class="prettyprint">  
	using System;
	using Microsoft.AspNet.Identity;
	using Microsoft.AspNet.Identity.EntityFramework;
	using Microsoft.AspNet.Identity.Owin;
	using Microsoft.Owin;
	using Microsoft.Owin.Security.Cookies;
	using Microsoft.Owin.Security.DataProtection;
	using Microsoft.Owin.Security.Google;
	using Owin;
	using ContactManager.Models;
	
	namespace ContactManager
	{
	    public partial class Startup {
	
	        // For more information on configuring authentication, please visit http://go.microsoft.com/fwlink/?LinkId=301883
	        public void ConfigureAuth(IAppBuilder app)
	        {
	            // Configure the db context and user manager to use a single instance per request
	            app.CreatePerOwinContext(ApplicationDbContext.Create);
	            app.CreatePerOwinContext<ApplicationUserManager>(ApplicationUserManager.Create);
	
	            // Enable the application to use a cookie to store information for the signed in user
	            // and to use a cookie to temporarily store information about a user logging in with a third party login provider
	            // Configure the sign in cookie
	            app.UseCookieAuthentication(new CookieAuthenticationOptions
	            {
	                AuthenticationType = DefaultAuthenticationTypes.ApplicationCookie,
	                LoginPath = new PathString("/Account/Login"),
	                Provider = new CookieAuthenticationProvider
	                {
	                    OnValidateIdentity = SecurityStampValidator.OnValidateIdentity<ApplicationUserManager, ApplicationUser>(
	                        validateInterval: TimeSpan.FromMinutes(20),
	                        regenerateIdentity: (manager, user) => user.GenerateUserIdentityAsync(manager))
	                }
	            });
	            // Use a cookie to temporarily store information about a user logging in with a third party login provider
	            app.UseExternalSignInCookie(DefaultAuthenticationTypes.ExternalCookie);
	
	            // Uncomment the following lines to enable logging in with third party login providers
	            //app.UseMicrosoftAccountAuthentication(
	            //    clientId: "",
	            //    clientSecret: "");
	
	            //app.UseTwitterAuthentication(
	            //   consumerKey: "",
	            //   consumerSecret: "");
	
	            //app.UseFacebookAuthentication(
	            //   appId: "",
	            //   appSecret: "");
	
	            app.UseGoogleAuthentication(new GoogleOAuth2AuthenticationOptions()
	            {
	                ClientId = "<mark>000000000000.apps.googleusercontent.com</mark>",
	                ClientSecret = "<mark>00000000000</mark>"
	            });
	        }
	    }
	}
	</pre>  
12. Drücken Sie **STRG+F5**, um die Anwendung zu erstellen und auszuführen. Klicken Sie auf den Link **Log in**.
13. Klicken Sie unter **Use another service to log in** auf **Google**.  
	![Log in](./media/web-sites-dotnet-web-forms-secure/SecureWebForms21d.png)  
14. Wenn Sie Ihre Anmeldeinformationen eingeben müssen, werden Sie zur Google-Website umgeleitet, wo Sie Ihre Anmeldeinformationen eingeben.  
	![Google - Sign in](./media/web-sites-dotnet-web-forms-secure/SecureWebForms21e.png)  
15. Nachdem Sie Ihre Anmeldeinformationen eingegeben haben, werden Sie aufgefordert, Berechtigungen für die soeben erstellten Webanwendung zu erteilen:  
	![Project Default Service Account](./media/web-sites-dotnet-web-forms-secure/SecureWebForms21f.png)  
16. Klicken Sie auf **Annehmen**. Sie werden nun zurück auf die Seite**Register** der Anwendung **ContactManager** geleitet, wo Sie Ihr Google-Konto registrieren können.
	![Register with your Google Account](./media/web-sites-dotnet-web-forms-secure/SecureWebForms21g.png)  
17. Sie können zwar den lokalen E-Mail-Registrierungsnamen in Ihr Gmail-Konto ändern, im Allgemeinen sollten Sie jedoch den Standard-E-Mail-Aliasnamen beibehalten (den Sie für die Authentifizierung verwendet haben). Klicken Sie auf **Log in**.

##Verwenden der Mitgliedschafts-API zum Beschränken des Zugriffs 
ASP.NET Identity ist das Mitgliedschaftssystem, das beim Erstellen einer ASP.NET-Webanwendung für die Authentifizierung verwendet wird. Damit ist es einfacher, benutzerspezifische Profildaten in die Anwendungsdaten zu integrieren. Außerdem können Sie im ASP.NET Identity das Persistenzmodell für die Benutzerprofile in Ihrer Anwendung auswählen. Sie können die Daten in einer SQL Server-Datenbank oder einem anderen Datenspeicher speichern, darunter auch *NoSQL*-Datenspeicher wie Azure-Speichertabellen.

Bei Verwendung der ASP.NET Web Forms-Standardvorlage haben Sie eine integrierte Mitgliedschaftsfunktion, die Sie sofort verwenden können, wenn die Anwendung ausgeführt wird. Sie verwenden ASP.NET Identity, um eine Administratorrolle hinzuzufügen und dieser Rolle einen Benutzer zuzuweisen. Anschließend erfahren Sie, wie Sie den Zugriff auf den Administrationsordner beschränken und welche Seiten in diesem Ordner verwendet werden, um die Kontaktdaten zu ändern.

###Hinzufügen eines Administrators 
Unter Verwendung von ASP.NET Identity können Sie eine Administratorrolle hinzufügen und mithilfe von Code dieser Rolle einen Benutzer zuweisen. 

1. Öffnen Sie im **Projektmappen-Explorer** die Datei *Configuration.cs* im Ordner *Migrations*.
2. Fügen Sie die folgenden `using`-Anweisungen im `ContactManger.Migrations`-Namespace hinzu:  
	<pre class="prettyprint">
	using Microsoft.AspNet.Identity;
	using Microsoft.AspNet.Identity.EntityFramework;
	</pre>
3. Fügen Sie der Klasse `Configuration` die folgende `AddUserAndRole`-Methode nach der `Seed`-Methode hinzu:  
	<pre class="prettyprint">
        public void AddUserAndRole(ContactManager.Models.ApplicationDbContext context)
        {
            IdentityResult IdRoleResult;
            IdentityResult IdUserResult;

            var roleStore = new RoleStore&lt;IdentityRole&gt;(context);
            var roleMgr = new RoleManager&lt;IdentityRole&gt;(roleStore);

            if (!roleMgr.RoleExists(&quot;canEdit&quot;))
            {
                IdRoleResult = roleMgr.Create(new IdentityRole { Name = &quot;canEdit&quot; });
            }

            //var userStore = new UserStore&lt;ApplicationUser&gt;(context);
            //var userMgr = new UserManager&lt;ApplicationUser&gt;(userStore);
            var userMgr = new UserManager&lt;ApplicationUser&gt;(new UserStore&lt;ApplicationUser&gt;(context));

            var appUser = new ApplicationUser
            {
                UserName = &quot;canEditUser@wideworldimporters.com&quot;,
                Email = &quot;canEditUser@wideworldimporters.com&quot;
            };
            IdUserResult = userMgr.Create(appUser, &quot;Pa$$word1&quot;);

            if (!userMgr.IsInRole(userMgr.FindByEmail(&quot;canEditUser@wideworldimporters.com&quot;).Id, &quot;canEdit&quot;))
            {
              //  IdUserResult = userMgr.AddToRole(appUser.Id, &quot;canEdit&quot;);
                IdUserResult = userMgr.AddToRole(userMgr.FindByEmail(&quot;canEditUser@wideworldimporters.com&quot;).Id, &quot;canEdit&quot;);
            }
        }
	</pre>
4. Fügen Sie zu Beginn der `Seed`-Methode einen Aufruf der `AddUserAndRole`-Methode hinzu. Beachten Sie, dass nur der Anfang der `Seed`-Methode angezeigt wird.  
	<pre class="prettyprint">
        protected override void Seed(ContactManager.Models.ApplicationDbContext context)
        {
            <mark>AddUserAndRole(context);</mark>
	</pre>  
5. Nachdem Sie alle Änderungen in der **Paket-Manager-Konsole** gespeichert haben, führen Sie den folgenden Befehl aus:  
	<pre class="prettyprint">
	Update-Database
	</pre>
	Mit diesem Code werden eine neue Rolle namens `canEdit` und ein neuer lokaler Benutzer mit der E-Mail-Adresse "canEditUser@wideworldimporters.com" erstellt. Anschließend fügt der Code "canEditUser@wideworldimporters.com" der Rolle `canEdit` hinzu. Weitere Informationen finden Sie auf der [ASP.NET Identity-Ressourcenseite](http://www.asp.net/identity).  

###Beschränken des Zugriffs auf den Administrationsordner 
Die Beispielanwendung **ContactManager** ermöglicht sowohl anonymen Benutzern als auch angemeldeten Benutzern das Anzeigen der Kontakte. Nachdem Sie diesen Abschnitt abgeschlossen haben, werden nur noch angemeldete Benutzer mit der Rolle "canEdit" in der Lage sein, die Kontakte zu ändern.

Sie erstellen einen Ordner namens *Admin*, auf den nur Benutzer zugreifen können, die der Rolle "canEdit" zugewiesen sind.

1. Fügen Sie im **Projektmappen-Explorer** einen Unterordner zum Ordner *Contacts* hinzu, und nennen Sie den neuen Unterordner *Admin*.
2. Verschieben Sie die folgenden Dateien aus dem Ordner *Contacts* in den Ordner *Contacts/Admin*:  
	- *Delete.aspx *und* Delete.aspx.cs*
	- *Edit.aspx *und* Edit.aspx.cs*
	- *Insert.aspx *und* Insert.aspx.cs*
3. Aktualisieren Sie die Linkverweise unter *Contacts/Default.aspx*, indem Sie "Admin/" vor den Seitenverweisen hinzufügen, die auf *Insert.aspx*, *Edit.aspx* und *Delete.aspx* verweisen:  
	<pre class="prettyprint">
	&lt;%@ Page Title=&quot;ContactsList&quot; Language=&quot;C#&quot; MasterPageFile=&quot;~/Site.Master&quot; CodeBehind=&quot;Default.aspx.cs&quot; Inherits=&quot;ContactManager.Contacts.Default&quot; ViewStateMode=&quot;Disabled&quot; %&gt;
	&lt;%@ Register TagPrefix=&quot;FriendlyUrls&quot; Namespace=&quot;Microsoft.AspNet.FriendlyUrls&quot; %&gt;
	
	&lt;asp:Content runat=&quot;server&quot; ContentPlaceHolderID=&quot;MainContent&quot;&gt;
	    &lt;h2&gt;Contacts List&lt;/h2&gt;
	    &lt;p&gt;
	        &lt;asp:HyperLink runat=&quot;server&quot; NavigateUrl=&quot;<mark>Admin/</mark>Insert.aspx&quot; Text=&quot;Create new&quot; /&gt;
	    &lt;/p&gt;
	    &lt;div&gt;
	        &lt;asp:ListView runat=&quot;server&quot;
	            DataKeyNames=&quot;ContactId&quot; ItemType=&quot;ContactManager.Models.Contacts&quot;
	            AutoGenerateColumns=&quot;false&quot;
	            AllowPaging=&quot;true&quot; AllowSorting=&quot;true&quot;
	            SelectMethod=&quot;GetData&quot;&gt;
	            &lt;EmptyDataTemplate&gt;
	                There are no entries found for Contacts
	            &lt;/EmptyDataTemplate&gt;
	            &lt;LayoutTemplate&gt;
	                &lt;table class=&quot;table&quot;&gt;
	                    &lt;thead&gt;
	                        &lt;tr&gt;
	                            &lt;th&gt;Name&lt;/th&gt;
	                            &lt;th&gt;Address&lt;/th&gt;
	                            &lt;th&gt;City&lt;/th&gt;
	                            &lt;th&gt;State&lt;/th&gt;
	                            &lt;th&gt;Zip&lt;/th&gt;
	                            &lt;th&gt;Email&lt;/th&gt;
	                            &lt;th&gt;&amp;nbsp;&lt;/th&gt;
	                        &lt;/tr&gt;
	                    &lt;/thead&gt;
	                    &lt;tbody&gt;
	                        &lt;tr runat=&quot;server&quot; id=&quot;itemPlaceholder&quot; /&gt;
	                    &lt;/tbody&gt;
	                &lt;/table&gt;
	            &lt;/LayoutTemplate&gt;
	            &lt;ItemTemplate&gt;
	                &lt;tr&gt;
	                    &lt;td&gt;
	                        &lt;asp:DynamicControl runat=&quot;server&quot; DataField=&quot;Name&quot; ID=&quot;Name&quot; Mode=&quot;ReadOnly&quot; /&gt;
	                    &lt;/td&gt;
	                    &lt;td&gt;
	                        &lt;asp:DynamicControl runat=&quot;server&quot; DataField=&quot;Address&quot; ID=&quot;Address&quot; Mode=&quot;ReadOnly&quot; /&gt;
	                    &lt;/td&gt;
	                    &lt;td&gt;
	                        &lt;asp:DynamicControl runat=&quot;server&quot; DataField=&quot;City&quot; ID=&quot;City&quot; Mode=&quot;ReadOnly&quot; /&gt;
	                    &lt;/td&gt;
	                    &lt;td&gt;
	                        &lt;asp:DynamicControl runat=&quot;server&quot; DataField=&quot;State&quot; ID=&quot;State&quot; Mode=&quot;ReadOnly&quot; /&gt;
	                    &lt;/td&gt;
	                    &lt;td&gt;
	                        &lt;asp:DynamicControl runat=&quot;server&quot; DataField=&quot;Zip&quot; ID=&quot;Zip&quot; Mode=&quot;ReadOnly&quot; /&gt;
	                    &lt;/td&gt;
	                    &lt;td&gt;
	                        &lt;asp:DynamicControl runat=&quot;server&quot; DataField=&quot;Email&quot; ID=&quot;Email&quot; Mode=&quot;ReadOnly&quot; /&gt;
	                    &lt;/td&gt;
	                    &lt;td&gt;
	                        &lt;a href=&quot;<mark>Admin/</mark>Edit.aspx?ContactId=&lt;%#: Item.ContactId%&gt;&quot;&gt;Edit&lt;/a&gt; | 
	                        &lt;a href=&quot;<mark>Admin/</mark>Delete.aspx?ContactId=&lt;%#: Item.ContactId%&gt;&quot;&gt;Delete&lt;/a&gt;
	                    &lt;/td&gt;
	                &lt;/tr&gt;
	            &lt;/ItemTemplate&gt;
	        &lt;/asp:ListView&gt;
	    &lt;/div&gt;
	&lt;/asp:Content&gt;
	</pre>
4. Aktualisieren Sie die sechs Verweise des Codes `Response.Redirect("Default.aspx")` auf `Response.Redirect("~/Contacts/Default.aspx")` für die folgenden drei Dateien:  
	- *Delete.aspx.cs*
	- *Edit.aspx.cs*
	- *Insert.aspx.cs*  

	Nun funktionieren alle Links korrekt, wenn Sie die Kontaktdaten anzeigen und aktualisieren.
5. Um den Zugriff auf den Ordner *Admin* zu beschränken, klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner *Admin*, und wählen Sie **Neues Element hinzufügen** aus.
6. Wählen Sie aus der Liste der Visual C# Web-Vorlagen **Webkonfigurationsdatei** aus der mittleren Liste aus, übernehmen Sie den Standardnamen *Web.config*, und klicken Sie dann auf **Hinzufügen**.
7. Ersetzen Sie den bestehenden XML-Inhalt in der Datei *Web.config* durch den folgenden Code:
	<pre class="prettyprint">
	&lt;?xml version=&quot;1.0&quot;?&gt;
	&lt;configuration&gt;
	  &lt;system.web&gt;
	    &lt;authorization&gt;
	      &lt;allow roles=&quot;canEdit&quot;/&gt;
	      &lt;deny users=&quot;*&quot;/&gt;
	    &lt;/authorization&gt;
	  &lt;/system.web&gt;
	&lt;/configuration&gt;
	</pre>
8. Speichern Sie die Datei *Web.config*. 
	Die Datei *Web.config* gibt an, dass nur Benutzer, die der Rolle "canEdit" zugewiesen wurden, die im Ordner *Admin* enthaltenen Seiten aufrufen können. 

Wenn ein Benutzer, der nicht zur Rolle "canEdit" gehört, versucht, die Daten zu ändern, wird er zur *Log in*-Seite umgeleitet.

##Bereitstellen der Anwendung mit der Datenbank in Azure 
Nachdem die Webanwendung nun vollständig ist, können Sie sie in Azure veröffentlichen.

###Veröffentlichen der Anwendung 
1. Erstellen Sie in Visual Studio das Projekt (**STRG+UMSCHALT+B**).
2. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen** aus.  
	![Publish menu option](./media/web-sites-dotnet-web-forms-secure/SecureWebForms22.png)  
	Das Dialogfeld **Web veröffentlichen** wird angezeigt.  
	![Publish Web dialog box](./media/web-sites-dotnet-web-forms-secure/SecureWebForms22a.png)  
3. Wählen Sie auf der Registerkarte **Profil** die Option **Azure-Websites** als Veröffentlichungsziel aus, wenn diese nicht bereits ausgewählt ist.  
	![Publish Web dialog box](./media/web-sites-dotnet-web-forms-secure/SecureWebForms23.png)  
4. Klicken Sie auf **Anmelden**, falls Sie nicht bereits angemeldet sind.
5. Wählen Sie aus dem Dropdownfeld **Vorhandene Websites** die vorhandene Website, die Sie zuvor in diesem Lernprogramm erstellt haben, aus, und klicken Sie auf **OK**.  
	![Select Existing Web Site dialog box](./media/web-sites-dotnet-web-forms-secure/SecureWebForms25.png)  
	Wenn Sie aufgefordert werden, Änderungen an dem Profil zu speichern, klicken Sie auf **Ja**.
6. Klicken Sie auf die Registerkarte **Einstellungen**.  
	![Select Existing Web Site dialog box](./media/web-sites-dotnet-web-forms-secure/SecureWebForms26.png)  
7. Legen Sie das Dropdownfeld **Konfiguration** auf **Debuggen** fest.
8. Klicken Sie auf den **Abwärtspfeil** neben **ApplicationDbContext**, und geben Sie **ContactDB** an.
9. Aktivieren Sie das Kontrollkästchen **Code First-Migrations ausführen**.  

	>[AZURE.NOTE]  
	In diesem Beispiel sollten Sie das Kontrollkästchen nur beim ersten Veröffentlichen der Anwendung aktivieren. Auf diese Weise wird die *Seed*-Methode in der Datei *Configuration.cs* nur einmal aufgerufen.  

10. Klicken Sie anschließend auf **Veröffentlichen**.  
	Die Anwendung wird in Azure veröffentlicht.

>[AZURE.NOTE]  
Wenn Sie Visual Studio geschlossen und nach dem Erstellen des Veröffentlichungsprofils wieder geöffnet haben, wird die Verbindungszeichenfolge möglicherweise nicht in der Dropdownliste angezeigt. Bearbeiten Sie in diesem Fall nicht das zuvor erstellte Veröffentlichungsprofil, sondern erstellen Sie ein neues Profil auf die gleiche Weise wie das vorherige Profil. Führen Sie dann die folgenden Schritte auf der Registerkarte **Einstellungen** aus.)  

###Überprüfen der Anwendung in Azure 
1. Klicken Sie im Browser auf den Link **Contact Demo**.  
	Die Kontaktliste wird angezeigt.
	![Contacts listed in Browser](./media/web-sites-dotnet-web-forms-secure/SecureWebForms27.png)  

2. Wählen Sie auf der Seite **Contact List** die Option **Create new** aus.  
	![Contacts listed in Browser](./media/web-sites-dotnet-web-forms-secure/SecureWebForms29.png)  
	Wie werden zur **Anmeldeseite** umgeleitet, das Sie sich nicht mit einem Konto angemeldet haben, das Kontakte bearbeiten kann.  
3. Nachdem Sie die unten angegebene E-Mail-Adresse und das Kennwort eingegebenen haben, klicken Sie auf **Log in**.  
	**E-Mail**: `canEditUser@wideworldimporters.com`  
	**Kennwort**: 'Pa$$word1'  
	![Log in Page](./media/web-sites-dotnet-web-forms-secure/SecureWebForms28.png)  

4. Geben Sie die neuen Daten für jedes Feld ein, und drücken Sie dann auf die Schaltfläche **Einfügen**.  
	![Add New Contact Page](./media/web-sites-dotnet-web-forms-secure/SecureWebForms30.png)  
	Die Seite *EditContactList.aspx* wird mit dem neuen Eintrag angezeigt.  
	![Add New Contact Page](./media/web-sites-dotnet-web-forms-secure/SecureWebForms31.png)  

5. Klicken Sie auf den Link **Log off**.

###Anhalten der Anwendung 
Um zu verhindern, dass sich andere Personen registrieren und Ihre Beispielanwendung verwenden, sollten Sie die Website anhalten.

1. Klicken Sie in Visual Studio im Menü **Ansicht** auf **Server-Explorer**. 
2. Navigieren Sie im **Server-Explorer** zu **Websites**.
3. Klicken Sie mit der rechten Maustaste auf jede Websiteinstanz, und wählen Sie **Website anhalten** aus.  
	![Stop Web Site menu item](./media/web-sites-dotnet-web-forms-secure/SecureWebForms26a.png)  

	Sie können die Website alternativ im Azure-Verwaltungsportal auswählen und dann unten auf der Seite auf das Symbol **Beenden** klicken.  
	![Add New Contact Page](./media/web-sites-dotnet-web-forms-secure/SecureWebForms26b.png)  

##Überprüfen der Datenbank 
Es ist wichtig zu wissen, wie Sie die Datenbank direkt anzeigen und ändern können. Durch die direkte Bearbeitung können Sie die Daten in der Datenbank überprüfen und wissen, wie die Daten in der Tabelle gespeichert sind.

###Überprüfen der SQL Azure-DB 
1. Navigieren Sie in Visual Studio im **Server-Explorer** zu **ContactDB**.
2. Klicken Sie mit der rechten Maustaste auf **ContactDB**, und wählen Sie **Öffnen in SQL Server-Objekt-Explorer** aus.  
	![Open in SQL Server Object Explorer menu item](./media/web-sites-dotnet-web-forms-secure/SecureWebForms32.png)  
3. Wenn das Dialogfeld **Firewallregel hinzufügen** angezeigt wird, wählen Sie **Firewallregel hinzufügen** aus.  

	>[AZURE.NOTE]  
	Wenn Sie **SQL-Datenbanken** nicht erweitern und **ContactDB** in Visual Studio nicht anzeigen können, müssen Sie die folgenden Anweisungen befolgen, um einen Firewallport oder einen Portbereich zu öffnen. Befolgen Sie dazu die Anweisungen unter **Einrichten von Azure-Firewallregeln** am Ende des [MVC-Lernprogramms](http://azure.microsoft.com/documentation/articles/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/). Alternativ können Sie auch die Daten der lokalen Datenbank überprüfen, indem Sie die Anwendung lokal erstellen, ausführen und Daten hinzufügen (**STRG+F5** in Visual Studio).  

4. Wenn das Dialogfeld **Mit Server verbinden** angezeigt wird, geben Sie das **Kennwort** ein, das Sie zu Beginn dieses Lernprogramms erstellt haben, und klicken Sie auf **Verbinden**.  

	>[AZURE.NOTE]  
	Falls Sie sich nicht mehr an das Kennwort erinnern, finden Sie es in Ihrer lokalen Projektdatei. Erweitern Sie im **Projektmappen-Explorer** den Ordner *Properties* und anschließend den Ordner *PublishProfiles*. Öffnen Sie die Datei *contactmanager.pubxml* (Ihr Dateiname lautet möglicherweise anders). Durchsuchen Sie die Datei nach dem Veröffentlichungskennwort.
5. Erweitern Sie die Datenbank **contactDB** und dann **Tabellen**.
6. Klicken Sie mit der rechten Maustaste auf die Tabelle **dbo.AspNetUsers**, und wählen Sie **Daten anzeigen** aus.  
	![View Data menu item](./media/web-sites-dotnet-web-forms-secure/SecureWebForms34.png)  
	Sie können die Daten anzeigen, die dem Benutzer "canEditUser@contoso.com" zugeordnet sind.  
	![ContactManager window](./media/web-sites-dotnet-web-forms-secure/SecureWebForms35.png)  

###Hinzufügen eines Benutzers zur Admin-Rolle durch Bearbeitung der Datenbank 
An früherer Stelle in diesem Lernprogramm haben Sie Code verwendet, um Benutzer der Rolle "canEdit" hinzuzufügen. Alternativ dazu können Sie die Daten direkt in den Mitgliedschaftstabellen bearbeiten. In den folgenden Schritten wird gezeigt, wie mit dieser alternativen Methode ein Benutzer zu einer Rolle hinzugefügt wird.

1. Klicken Sie im **SQL Server-Objekt-Explorer** mit der rechten Maustaste auf **dbo.AspNetUserRoles**, und wählen Sie **Daten anzeigen** aus.
	![AspNetUserRoles data](./media/web-sites-dotnet-web-forms-secure/SecureWebForms36.png)  
2. Kopieren Sie die *RoleId*, und fügen Sie sie in die leere (neue) Zeile ein.  
	![AspNetUserRoles data](./media/web-sites-dotnet-web-forms-secure/SecureWebForms37.png)  
3. Suchen Sie in der Tabelle **dbo.AspNetUsers** den Benutzer, den Sie der Rolle hinzufügen möchten, und kopieren Sie die Benutzer- *Id*.
4. Fügen Sie die kopierte  *Id* in das Feld **UserId** auf der neuen Zeile in der Tabelle **AspNetUserRoles** ein.  

>[AZURE.NOTE]  
Zurzeit wird ein Tool entwickelt, das die Verwaltung von Benutzern und Rollen vereinfacht.  

##Nächste Schritte
Weitere Informationen zu ASP.NET Web Forms finden Sie unter [Learn About ASP.NET Web Forms](http://www.asp.net/web-forms) auf der ASP.NET-Website sowie in den [Microsoft Azure-Lernprogrammen und -Anleitungen](http://azure.microsoft.com/documentation/services/web-sites/#net).

Dieses Lernprogramm basiert auf dem MVC-Lernprogramm [Bereitstellen einer sicheren ASP.NET MVC 5-Anwendung mit Mitgliedschafts-, OAuth- und SQL-Datenbank für eine Azure-Website](http://azure.microsoft.com/documentation/articles/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/) von Rick Anderson (Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT)), mit Unterstützung von Tom Dykstra und Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart)). 

Bitte teilen Sie uns mit, was Ihrer Meinung nach gelungen ist, bzw. verbessert werden kann, und zwar nicht nur bezüglich des Lernprogramms, sondern auch bezüglich der Produkte, die darin vorgestellt werden. Ihr Feedback unterstützt uns dabei, eine entsprechende Priorität für Verbesserungen festzulegen. Unter [Show Me How With Code](http://aspnet.uservoice.com/forums/228522-show-me-how-with-code) (in englischer Sprache) können Sie neue Themen anfordern oder eine Stimme dafür abgeben.






<!--HONumber=42-->
