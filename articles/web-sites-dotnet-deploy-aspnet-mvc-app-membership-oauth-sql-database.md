<properties linkid="dev-net-tutorials-web-app-with-sql-azure-vs2013" urlDisplayName="Website with SQL Database" pageTitle="Deploy a Secure ASP.NET MVC app with Membership, OAuth, and SQL Database to an Azure Website" metaKeywords="Azure hello world tutorial, Azure getting started tutorial, SQL Database tutorial, Azure .NET hello world tutorial, Azure C# hello world tutorial, SQL Azure C# tutorial" description="Learn how to develop an ASP.NET MVC 5 website with a SQL Database back-end deploy it to Azure." metaCanonical="" services="web-sites,sql-database" documentationCenter=".NET" title="Deploy a Secure ASP.NET MVC 5 app with Membership, OAuth, and SQL Database to an Azure Website" authors="riande"  solutions="" writer="riande" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="riande" />

# Bereitstellen einer sicheren ASP.NET MVC 5-Anwendung mit Mitgliedschafts-, OAuth- und SQL-Datenbank für eine Azure-Website

***Aktualisiert am 2. April 2014.***

In diesem Lernprogramm wird die Erstellung einer sicheren ASP.NET MVC 5-Webanwendung erläutert, mit der Benutzer sich mithilfe der Anmeldeinformationen von Facebook oder Google anmelden können. Darüber hinaus wird die Anwendung für Azure bereitgestellt.

Sie können ein kostenloses Azure-Konto erstellen. Wenn Sie noch nicht über Visual Studio 2013 verfügen, wird Visual Studio 2013 für Web Express automatisch durch das SDK installiert. Sie können kostenlos mit der Entwicklung für Azure beginnen. Wenn Sie Visual Studio 2012 verwenden möchten, arbeiten Sie das [vorherige Lernprogramm][vorherige Lernprogramm] durch. Diese Version des Lernprogramms ist wesentlich einfacher als die vorherige Version.

Bei diesem Lernprogramm wird davon ausgegangen, dass Sie noch keine Erfahrung mit der Verwendung von Azure haben. Nach Abschluss dieses Lernprogramms verfügen Sie über eine sichere datengesteuerte Webanwendung, die unter Rückgriff auf eine Clouddatenbank in der Cloud ausgeführt wird.

Sie lernen Folgendes:

-   Erstellen eines sicheren ASP.NET MVC 5-Projekts und Veröffentlichen dieses Projekts auf einer Azure-Website
-   Verwenden von [OAuth][OAuth], [OpenID][OpenID] und der ASP.NET-Mitgliedschaftsdatenbank zum Sichern der Anwendung
-   Verwenden der neuen Mitgliedschafts-API zum Hinzufügen von Benutzern und Rollen
-   Verwenden einer SQL-Datenbank zum Speichern von Daten in Azure

Sie erstellen eine einfache Kontaktlisten-Webanwendung, die auf ASP.NET MVC 5 beruht und für den Datenbankzugriff ADO.NET Entity Framework verwendet. In der folgenden Abbildung wird die Anmeldeseite der fertiggestellten Anwendung dargestellt:

![Anmeldeseite][Anmeldeseite]

> [WACOM.NOTE] Um dieses Lernprogramm abzuschließen, benötigen Sie ein Microsoft Azure-Konto. Wenn Sie kein Konto haben, können Sie [Ihre MSDN-Abonnementenvorteile aktivieren][Ihre MSDN-Abonnementenvorteile aktivieren] oder [sich für eine kostenlose Testversion registrieren][sich für eine kostenlose Testversion registrieren].

Dieses Lernprogramm umfasst folgende Punkte:

-   [Einrichten der Entwicklungsumgebung][Einrichten der Entwicklungsumgebung]
-   [Einrichten der Azure-Umgebung][Einrichten der Azure-Umgebung]
-   [Erstellen einer ASP.NET MVC 5-Anwendung][Erstellen einer ASP.NET MVC 5-Anwendung]
-   [Bereitstellen der Anwendung in Azure][Bereitstellen der Anwendung in Azure]
-   [Hinzufügen einer Datenbank zur Anwendung][Hinzufügen einer Datenbank zur Anwendung]
-   [Hinzufügen eines OAuth-Anbieters][Hinzufügen eines OAuth-Anbieters]
-   [Verwenden der Mitgliedschafts-API][Verwenden der Mitgliedschafts-API]
-   [Bereitstellen der Anwendung in Azure][1]
-   [Nächste Schritte][Nächste Schritte]

[WACOM.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

Sie müssen [Visual Studio 2013 Update 2 RC][Visual Studio 2013 Update 2 RC] oder höher installieren, um das neue SSL-Zertifikat für den Localhost zu verwenden.

## <a name="bkmk_setupwindowsazure"></a>Einrichten der Azure-Umgebung

Richten Sie als Nächstes die Azure-Umgebung ein, indem Sie eine Azure-Website und eine SQL-Datenbank erstellen.

### Erstellen einer Website und einer SQL-Datenbank in Azure

Ihre Azure-Website wird in einer freigegebenen Hostingumgebung und damit auf virtuellen Computern (VMs) ausgeführt, die für andere Azure-Clients freigegeben wurden. Eine freigegebene Hostingumgebung ist eine kostengünstige Möglichkeit, mit der Verwendung der Cloud zu beginnen. Später kann die Anwendung skaliert werden, sobald der Webdatenverkehr zunimmt, um die Anforderungen durch die Ausführung dedizierter virtueller Maschinen zu erfüllen. Wenn Sie eine komplexere Architektur benötigen, können Sie eine Migration zu einem Azure-Clouddienst ausführen. Clouddienste werden auf dedizierten VMs ausgeführt, die Sie Ihren Anforderungen entsprechend konfigurieren können.

Die Azure SQL-Datenbank ist ein cloudbasierter relationaler Datenbankdienst auf Grundlage von SQL Server-Technologien. Die Tools und Anwendungen, die mit SQL Server verwendet werden können, sind auch für die SQL-Datenbank geeignet.

1.  Klicken Sie im [Azure-Verwaltungsportal][Azure-Verwaltungsportal] auf der linken Registerkarte auf **Websites** und dann auf **Neu**.

    ![Schaltfläche "Neu" im Verwaltungsportal][Schaltfläche "Neu" im Verwaltungsportal]

2.  Klicken Sie auf **Website** und dann auf **Custom Create**.

    ![Erstellung über Datenbanklink im Verwaltungsportal][Erstellung über Datenbanklink im Verwaltungsportal]

    Der Assistent **New Web Site - Custom Create** wird geöffnet.

3.  Geben Sie im Schritt **Website erstellen** des Assistenten eine Zeichenfolge in das Feld **URL** ein, die als eindeutige URL der Anwendung dienen soll. Die vollständige URL besteht aus der hier eingegebenen Zeichenfolge und dem Suffix, das neben dem Textfeld aufgeführt wird. In der Abbildung wird eine URL dargestellt, die vermutlich bereits vergeben ist, sodass eine andere URL ausgewählt werden muss.

    ![Erstellung über Datenbanklink im Verwaltungsportal][2]

4.  Wählen Sie in der Dropdownliste **Datenbank** die Option **Create a free SQL database** aus.

5.  Wählen Sie in der Dropdownliste **Region** dieselbe Region aus, die Sie für die Website ausgewählt haben.
    Mit dieser Einstellung wird das Datencenter angegeben, in dem Ihr virtueller Computer ausgeführt wird.
6.  Ändern Sie im Feld **DB Connection String Name** den Standardwert *DefaultConnection* nicht.
7.  Klicken Sie auf den Pfeil, der nach rechts unten im Feld weist.
    Der Assistent springt zum Schritt **Specify database settings**.

8.  Geben Sie in das Feld **Name** die Zeichenfolge *ContactDB* ein. (Siehe Abbildung unten.)
9.  Wählen Sie im Feld **Server** die Option **New SQL Database server**. (Siehe Abbildung unten.) Wenn Sie bereits zuvor eine SQL Server-Datenbank erstellt haben, können Sie alternativ diese SQL Server-Instanz aus der Dropdownliste auswählen.
10. Legen Sie für **Region** dasselbe Gebiet fest wie bei der Erstellung der Website.
11. Geben Sie unter **Anmeldename** und **Kennwort** die Anmeldedaten eines Administrators ein. Wenn Sie **New SQL Database server** ausgewählt haben, geben Sie hier keinen vorhandenen Namen und kein vorhandenes Kennwort ein. Stattdessen definieren Sie jetzt einen neuen Namen und ein neues Kennwort zur späteren Verwendung beim Datenbankzugriff. Wenn Sie eine zuvor erstellte SQL Server-Version ausgewählt haben, werden Sie aufgefordert, das Kennwort für den zuvor erstellten SQL Server-Kontonamen einzugeben. Für dieses Lernprogramm wird das Feld **Advanced** nicht aktiviert. Bei einer kostenlosen Datenbank können Sie nur die Sammlung festlegen.
12. Klicken Sie auf das Häkchen unten rechts im Feld, um anzugeben, dass Sie fertig sind.

    ![Schritt "Datenbankeinstellungen" im Assistenten "New Web Site - Create with Database"][Schritt "Datenbankeinstellungen" im Assistenten "New Web Site - Create with Database"]

    Im folgenden Bild wird die Verwendung einer vorhandenen SQL Server-Version und -Anmeldung gezeigt.

    ![Schritt "Datenbankeinstellungen" im Assistenten "New Web Site - Create with Database"][3]

    Sie kehren zur Seite **Websites** im Verwaltungsportal zurück, und in der Spalte **Status** wird angegeben, dass die Website erstellt wird. Nach einer Weile (normalerweise weniger als eine Minute) wird in der Spalte **Status** angegeben, dass die Website erfolgreich erstellt wurde. In der linken Navigationsleiste wird die Anzahl der Websites in Ihrem Konto neben dem Symbol **Websites** angegeben und die Anzahl der Datenbanken neben dem Symbol **SQL-Datenbanken**.

## <a name="bkmk_createmvc4app"></a>Erstellen einer ASP.NET MVC 5-Anwendung

Sie haben nun eine Azure-Website erstellt, diese enthält jedoch noch keinen Inhalt. Als Nächstes erstellen Sie die Visual Studio Web App, die Sie auf Azure veröffentlichen.

### Erstellen des Projekts

1.  Klicken Sie im Menü **Datei** auf **Neues Projekt**.

	!["Neues Projekt" im Menü "Datei"]["Neues Projekt" im Menü "Datei"]

1.  Erweitern Sie im Dialogfeld **Neues Projekt** den Eintrag **C\#**, und wählen Sie unter **Installierte Vorlagen** die Option **Web** aus. Wählen Sie dann **ASP.NET-Webanwendung** aus.

2.  Nennen Sie die Anwendung **ContactManager**, und klicken Sie auf **OK**.

	![Dialogfeld "Neues Projekt"][Dialogfeld "Neues Projekt"]

	**Hinweis:** Im Bild wird als Name "MyExample" angezeigt, Sie müssen jedoch "ContactManager" eingeben. In den Codeblöcken, die Sie später kopieren, wird davon ausgegangen, dass der Projektname "ContactManager" lautet.

1.  Wählen Sie im Dialogfeld **New ASP.NET Project** die Vorlage **MVC** aus, **deaktivieren** Sie das Kontrollkästchen **Create remote resources**, und klicken Sie dann auf **OK**. (Das Kontrollkästchen kann **Host in the cloud** anstelle von **Create remote resources** lauten.)

![Dialogfeld "Neues ASP.NET-Projekt"][Dialogfeld "Neues ASP.NET-Projekt"]

### Einrichten der Seitenkopf- und -fußzeile

1.  Öffnen Sie im **Projektmappen-Explorer** die Datei *Layout.cshtml* im Ordner *Views\\Shared*.

    ![\_Layout.cshtml im Projektmappen-Explorer][\_Layout.cshtml im Projektmappen-Explorer]

2.  Ersetzen Sie die zwei Vorkommen von "My ASP.NET MVC Application" durch "Contact Manager".
3.  Ersetzen Sie "Application name" durch "CM Demo".

4.  Aktualisieren Sie den ersten Aktionslink, und ersetzen Sie *Home* durch *Cm*, um den Controller *Cm* zu verwenden.

    ![Codeänderungen][Codeänderungen]

### Lokales Ausführen der Anwendung

1.  Drücken Sie STRG+F5, um die Anwendung auszuführen.

    Die Startseite der Anwendung wird im Standardbrowser angezeigt.

    ![Lokal ausgeführte Website][Lokal ausgeführte Website]

Darüber hinaus müssen Sie keine weiteren Aktionen ausführen, um die Anwendung zu erstellen, die Sie für Azure bereitstellen. Zu einem späteren Zeitpunkt fügen Sie Datenbankfunktionalität hinzu.

## <a name="bkmk_deploytowindowsazure1"></a>Bereitstellen der Anwendung in Azure

1.  Klicken Sie im Projektmappen-Explorer von Visual Studio mit der rechten Maustaste auf das Projekt, und wählen Sie im Kontextmenü **Veröffentlichen** aus.

	!["Veröffentlichen" im Kontextmenü des Projekts]["Veröffentlichen" im Kontextmenü des Projekts]

	Der Assistent **Web veröffentlichen** wird geöffnet.

1.  Klicken Sie im Assistenten **Web veröffentlichen** auf der Registerkarte **Profil** auf **Azure Web Sites**.

	![Veröffentlichungseinstellungen importieren][Veröffentlichungseinstellungen importieren]

1.  Klicken Sie auf die Schaltfläche **Anmelden**, und melden Sie sich beim Azure-Portal an.

	![Anmelden][Anmelden]

	Nach der Anmeldung wird das Dialogfeld **Select Existing Web Site** geöffnet.

1.  Wählen Sie die Website aus, die Sie im ersten Teil dieses Lernprogramms erstellt haben, und klicken Sie dann auf **OK**.

	![Website auswählen][Website auswählen]

1.  Klicken Sie im Dialogfeld **Web veröffentlichen** auf **Veröffentlichen**.

    ![Publish][Publish]

    Die erstellte Anwendung wird nun in der Cloud ausgeführt. Bei der nächsten Bereitstellung der Anwendung werden nur die geänderten (oder neuen) Dateien bereitgestellt.

    ![In Cloud ausgeführt][In Cloud ausgeführt]

## <a name="bkmk_addadatabase"></a>Hinzufügen einer Datenbank zur Anwendung

Als Nächstes aktualisieren Sie die MVC-Anwendung, um Funktionen zum Anzeigen und Aktualisieren von Kontakten sowie Speichern der Daten in einer Datenbank hinzuzufügen. Die Anwendung verwendet Entity Framework, um die Datenbank zu erstellen und Daten in der Datenbank zu lesen und zu aktualisieren.

### Hinzufügen von Datenmodellklassen für die Kontakte

Sie beginnen mit der Erstellung eines einfachen Datenmodells in Code.

1.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner **Models**, und klicken Sie dann auf **Hinzufügen** \> **Klasse**.

    ![Klasse hinzufügen im Kontextmenü des Ordners "Models"][Klasse hinzufügen im Kontextmenü des Ordners "Models"]

2.  Geben Sie der neuen Klassendatei im Dialogfeld **Neues Element hinzufügen** den Namen *Contact.cs*, und klicken Sie dann auf **Hinzufügen**.

    ![Dialogfeld "Neues Element hinzufügen"][Dialogfeld "Neues Element hinzufügen"]

3.  Ersetzen Sie den Inhalt der Datei "Contacts.cs" durch den folgenden Code.

        using System.ComponentModel.DataAnnotations;
        using System.Globalization;
        namespace ContactManager.Models
        {
            public class Contact
            {
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

    Mit der Klasse **Contacts** werden die Daten definiert, die für die einzelnen Kontakte gespeichert werden, sowie ein Primärschlüssel (*ContactID*), der von der Datenbank benötigt wird.

### Erstellen von Webseiten, die Anwendungsbenutzern das Arbeiten mit den Kontakten ermöglichen

Mit dem ASP.NET MVC-Gerüstfeature kann automatisch Code generiert werden, der Erstellungs-, Lese-, Aktualisierungs- und Löschaktionen (CRUD) durchführt.

## <a name="bkmk_addcontroller"></a>Hinzufügen eines Controllers und einer Ansicht für die Daten

1.  Erstellen Sie das Projekt **(STRG+UMSCHALT+B)**. (Sie müssen das Projekt vor der Verwendung des Gerüstmechanismus erstellen.)
2.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner "Controllers", und klicken Sie dann auf **Hinzufügen** \> **Controller**.

    ![Controller hinzufügen im Kontextmenü des Ordners "Controllers"][Controller hinzufügen im Kontextmenü des Ordners "Controllers"]

3.  Wählen Sie im Dialogfeld **Add Scaffold** die Option **MVC 5 Controller with views, using EF** aus, und klicken Sie dann auf **Hinzufügen**.

    ![Dialogfeld "Add Scaffold"][Dialogfeld "Add Scaffold"]

4.  Wählen Sie im Dropdownfeld **Modellklasse** die Option **Contact (ContactManager.Models)** aus. (Siehe Abbildung unten.)
5.  Wählen Sie unter **Datenkontextklasse** die Option **ApplicationDbContext (ContactManager.Models)** aus. **ApplicationDbContext** wird für die Mitgliedschaftsdatenbank und die Kontaktdaten verwendet.
6.  Geben Sie im Texteingabefeld **Controllername** die Zeichenfolge **CmController** als Controllernamen ein.

    ![Kontextdialogfeld "Neue Daten"][Kontextdialogfeld "Neue Daten"]

7.  Klicken Sie auf **Hinzufügen**.

	In Visual Studio werden Controllermethoden und Ansichten für CRUD-Datenbankvorgänge für **Contact**-Objekte erstellt.

## Aktivieren von Migrationen, Erstellen der Datenbank, Hinzufügen von Beispieldaten und eines Dateninitialisierers

Die nächste Aufgabe besteht darin, das Feature [Code First-Migrationen][Code First-Migrationen] zu aktivieren, um die Datenbank basierend auf dem erstellten Datenmodell zu erstellen.

1.  Wählen Sie im Menü **Extras** den Eintrag **NuGet Package Manager** und danach **Paket-Manager-Konsole** aus.
    !["Paket-Manager-Konsole" im Menü "Extras"]["Paket-Manager-Konsole" im Menü "Extras"]

2.  Geben Sie im Fenster **Paket-Manager-Konsole** den folgenden Befehl ein:

        enable-migrations

    Mit dem Befehl **enable-migrations** wird der Ordner *Migrations* erstellt und die Datei *Configuration.cs* darin abgelegt. Sie können diese Datei bearbeiten, um ein Seeding für die Datenbank auszuführen und *Migrations* zu konfigurieren.

3.  Geben Sie im Fenster **Paket-Manager-Konsole** den folgenden Befehl ein:

        add-migration Initial

    Mit dem Befehl **add-migration-initial** wird eine Datei namens **\<Zeitstempel\>Initial** im Ordner *Migrations* generiert, mit der die Datenbank erstellt wird. Der erste Parameter (**Initial**) ist willkürlich und wird zum Erstellen des Dateinamens verwendet. Sie können die neue Klasse im **Projektmappen-Explorer** anzeigen.
    In der **Initial**-Klasse wird mit der **Up**-Methode die Tabelle "Contacts" erstellt und mit der **Down**-Methode (wird verwendet, wenn Sie zum vorherigen Status zurückkehren möchten) wieder verworfen.

4.  Öffnen Sie die Datei *Migrations\\Configuration.cs*.
5.  Fügen Sie den folgenden Namespace hinzu.

         using ContactManager.Models;

6.  Ersetzen Sie die *Seed*-Methode durch den folgenden Code:

        protected override void Seed(ContactManager.Models.ApplicationDbContext context)
        {
            context.Contacts.AddOrUpdate(p => p.Name,
               new Contact
               {
                   Name = "Debra Garcia",
                   Address = "1234 Main St",
                   City = "Redmond",
                   State = "WA",
                   Zip = "10999",
                   Email = "debra@example.com",
               },
                new Contact
                {
                    Name = "Thorsten Weinrich",
                    Address = "5678 1st Ave W",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "thorsten@example.com",
                },
                new Contact
                {
                    Name = "Yuhong Li",
                    Address = "9012 State st",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "yuhong@example.com",
                },
                new Contact
                {
                    Name = "Jon Orton",
                    Address = "3456 Maple St",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "jon@example.com",
                },
                new Contact
                {
                    Name = "Diliana Alexieva-Bosseva",
                    Address = "7890 2nd Ave E",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "diliana@example.com",
                }
                );
        }

    Mit diesem Code wird die Datenbank mit den Kontaktinformationen initialisiert (es wird ein Seeding dafür ausgeführt). Weitere Informationen zum Ausführen eines Seedings für die Datenbank finden Sie unter [Seeding and Debugging Entity Framework (EF) DBs][Seeding and Debugging Entity Framework (EF) DBs] (in englischer Sprache).

7.  Geben Sie im Fenster **Paket-Manager-Konsole** den folgenden Befehl ein:

        update-database

    ![Befehle in der Paket-Manager-Konsole][Befehle in der Paket-Manager-Konsole]

    Mit **update-database** wird die erste Migration ausgeführt, wodurch die Datenbank erstellt wird. Standardmäßig wird die Datenbank als SQL Server Express LocalDB-Datenbank erstellt.

8.  Drücken Sie STRG+F5, um die Anwendung auszuführen, und klicken Sie dann auf den Link **CM Demo**, oder navigieren Sie zu [http://localhost:(port\#)/Cm][http://localhost:(port\#)/Cm].

    In der Anwendung werden die Seeddaten angezeigt und Links zum Bearbeiten und Löschen und zu Details bereitgestellt. Sie können Daten erstellen, bearbeiten, löschen und anzeigen.

    ![MVC-Datenansicht][MVC-Datenansicht]

## <a name="addOauth"></a><span class="short-header">OAuth</span>Hinzufügen eines OAuth2- und OpenID-Anbieters

[OAuth][OAuth] ist ein offenes Protokoll, das die sichere Autorisierung in einer einfachen und Standardmethode von Web-, Mobil- und Desktopanwendungen ermöglicht. OAuth und [OpenID][OpenID] werden von der ASP.NET MVC-Internetvorlage verwendet, um Facebook, Twitter, Google und Microsoft als Authentifizierungsanbieter verfügbar zu machen. Auch wenn in diesem Lernprogramm nur Google als Authentifizierungsanbieter eingesetzt wird, können Sie den Code problemlos für die Verwendung einer der anderen Anbieter anpassen. Die Schritte zur Implementierung anderer Anbieter unterscheiden sich kaum von den Schritten in diesem Lernprogramm. Informationen zum Verwenden von Facebook als Authentifizierungsanbieter finden Sie im Lernprogramm [Create an ASP.NET MVC 5 App with Facebook and Google OAuth2 and OpenID Sign-on][Create an ASP.NET MVC 5 App with Facebook and Google OAuth2 and OpenID Sign-on].

Abgesehen von der Authentifizierung werden in diesem Lernprogramm auch Rollen verwendet, um die Autorisierung zu implementieren. Nur Benutzer, die Sie der Rolle *canEdit* hinzufügen, können Daten ändern (d. h. Kontakte erstellen, bearbeiten oder löschen).

1.  Folgen Sie den Anweisungen im Lernprogramm [Create an ASP.NET MVC 5 App with Facebook and Google OAuth2 and OpenID Sign-on][Create an ASP.NET MVC 5 App with Facebook and Google OAuth2 and OpenID Sign-on] unter **Creating a Google app for OAuth 2 to set up a Google app for OAuth2**.
2.  Öffnen Sie die Datei *App\_Start\\Startup.Auth.cs*. Entfernen Sie die Kommentarzeichen aus der *app.UseGoogleAuthentication()*-Methode, und geben Sie **clientId** und **clientSecret** ein.

<!-- -->

             public void ConfigureAuth(IAppBuilder app)
             {
                // Anwendung kann Cookie zum Speichern von Infos für angemeldeten Benutzer verwenden
                app.UseCookieAuthentication(new CookieAuthenticationOptions
                {
                   AuthenticationType = DefaultAuthenticationTypes.ApplicationCookie,
                   LoginPath = new PathString("/Account/Login")
                });
                // Cookie verwenden, um Infos über externen Anmeldeanbieter angemeldeten Benutzer temporär zu speichern 
                app.UseExternalSignInCookie(DefaultAuthenticationTypes.ExternalCookie);

                app.UseGoogleAuthentication(
                   clientId: "000-000.apps.googleusercontent.com",
                   clientSecret: "00000000000");
             } 

1.  Führen Sie die Anwendung aus, und klicken Sie auf den Link **Anmelden**.
2.  Klicken Sie unter **Use another service to log in** auf die Schaltfläche **Google**.

    ![Google-Anmeldung][Google-Anmeldung]

3.  Geben Sie Ihre Anmeldeinformationen ein.
4.  Sie werden vom Google-Authentifizierungsserver gefragt, ob die App Ihre E-Mail-Adresse und grundlegende Informationen zu Ihrem Konto anzeigen darf. Klicken Sie auf **Zustimmen**.
    ![GOOGLE Zustimmungsabfrage][GOOGLE Zustimmungsabfrage]
5.  Sie werden zur Registrierungsseite weitergeleitet. Als Benutzername wird standardmäßig der E-Mail-Aliasname festgelegt, mit dem Sie sich registriert haben. Sie können diese Einstellung ändern. Klicken Sie auf **Registrieren**.

    ![Registrieren][Registrieren]

## <a name="mbrDB"></a><span class="short-header">Mitgliedschaftsdatenbank</span>Verwenden der Mitgliedschafts-API

In diesem Abschnitt fügen Sie der Mitgliedschaftsdatenbank einen lokalen Benutzer und die Rolle *canEdit* hinzu. Nur Benutzer mit der Rolle *canEdit* können Daten bearbeiten. Eine bewährte Methode besteht darin, Rollen nach den Aktionen zu benennen, die damit ausgeführt werden können. Entsprechend ist *canEdit* einer Rolle mit dem Namen *admin* vorzuziehen. Mit fortschreitender Fertigstellung der Anwendung können Sie neue Rollen wie *canDeleteMembers* anstelle von weniger aussagekräftigen Rollen wie *superAdmin* hinzufügen.

1.  Öffnen Sie die Datei *migrations\\configuration.cs*, und fügen Sie die folgenden `using`-Anweisungen hinzu:

        using Microsoft.AspNet.Identity;
        using Microsoft.AspNet.Identity.EntityFramework;

2.  Fügen Sie der Klasse die folgende **AddUserAndRole**-Methode hinzu:

         bool AddUserAndRole(ContactManager.Models.ApplicationDbContext context)
         {
            IdentityResult ir;
            var rm = new RoleManager<IdentityRole>
                (new RoleStore<IdentityRole>(context));
            ir = rm.Create(new IdentityRole("canEdit"));
            var um = new UserManager<ApplicationUser>(
                new UserStore<ApplicationUser>(context));
            var user = new ApplicationUser()
            {
               UserName = "user1@contoso.com",
            };
            ir = um.Create(user, "P_assw0rd1");
            if (ir.Succeeded == false)
               return ir.Succeeded;
            ir = um.AddToRole(user.Id, "canEdit");
            return ir.Succeeded;
         }

3.  Rufen Sie die neue Methode über die **Seed**-Methode auf:
	<pre>
        protected override void Seed(ContactManager.Models.ApplicationDbContext context)
        {
            <mark>AddUserAndRole(context);</mark>
            context.Contacts.AddOrUpdate(p => p.Name,
                // Code removed for brevity
        }
	</pre>  
    <span></span>
    In den folgenden Abbildungen werden die Änderungen an der *Seed*-Methode gezeigt:

    ![Codebild][Codebild]

   Mit diesem Code werden eine neue Rolle namens *canEdit* und ein neuer lokaler Benutzer *user1@contoso.com* erstellt und *user1@contoso.com* wird der Rolle *canEdit* hinzugefügt. Weitere Informationen finden Sie auf der [ASP.NET Identity-Ressourcenseite][ASP.NET Identity-Ressourcenseite].

## Verwenden von temporärem Code zum Hinzufügen neuer Benutzer mit Anmeldung zu sozialen Netzwerken zur Rolle "canEdit"

In diesem Abschnitt ändern Sie die **ExternalLoginConfirmation**-Methode im Kontocontroller vorübergehend, um neue Benutzer hinzuzufügen, die sich mit einem OAuth- oder OpenID-Anbieter bei der Rolle *canEdit* registrieren. Die **ExternalLoginConfirmation**-Methode wird automatisch geändert, um einer Administratorrolle neue Benutzer hinzuzufügen. Bis ein Tool zum Hinzufügen und Verwalten von Rollen bereitgestellt wird, verwenden wir den unten dargestellten Code zur temporären automatischen Registrierung. Es ist geplant, in Zukunft ein [WSAT][WSAT] vergleichbares Tool bereitzustellen, mit dem Benutzerkonten und -rollen erstellt und bearbeitet werden können. An einer späteren Stelle in diesem Lernprogramm wird das Hinzufügen von Benutzern zu Rollen im Server-Explorer demonstriert.

1.  Öffnen Sie die Datei **Controllers\\AccountController.cs**, und navigieren Sie zur **ExternalLoginConfirmation**-Methode.
2.  Fügen Sie **AddToRoleAsync** unmittelbar vor dem Aufruf **SignInAsync** den folgenden Aufruf hinzu.

                await UserManager.AddToRoleAsync(user.Id, "canEdit");

	Mit dem oben dargestellten Code wird der neu registrierte Benutzer der Rolle "canEdit" hinzugefügt, sodass er auf Aktionsmethoden zugreifen kann, die das Ändern (Bearbeiten) von Daten erlauben. Nachfolgend wird die Codeänderung dargestellt:

	![Code][Code]

An späterer Stelle in diesem Lernprogramm stellen Sie die Anwendung für Azure bereit. Die Anmeldung erfolgt dann über Google oder einen anderen Authentifizierungsdrittanbieter. Dadurch wird Ihr neu registriertes Konto der Rolle *canEdit* hinzugefügt. Jede Person, die die URL Ihrer Website findet und über eine Google-ID verfügt, kann sich dann registrieren und die Datenbank aktualisieren. Sie können die Website beenden, um dies zu verhindern. Durch eine Untersuchung der Datenbank können Sie überprüfen, wer der Rolle *canEdit* angehört.

Drücken Sie in der Paket-Manager-Konsole die NACH-OBEN-TASTE, um den folgenden Befehl aufzurufen:

        Update-Database

Führen Sie den Befehl **Update-Database** aus, mit dem die **Seed**-Methode ausgeführt wird. Daraufhin wird die gerade hinzugefügte **AddUserAndRole**-Methode hinzugefügt. Mit **AddUserAndRole** wird der Benutzer *user1@contoso.com* erstellt und der Rolle *canEdit* hinzugefügt.

## Schützen der Anwendung durch SSL und das Attribut "Authorize"

In diesem Abschnitt wenden Sie das [Authorize][Authorize]-Attribut an, um den Zugriff auf die Aktionsmethoden einzuschränken. Anonyme Benutzer können nur die **Index**-Aktionsmethode des Controllers **Home** anzeigen. Registrierten Benutzern werden die Kontaktdaten (die Seiten **Index** und **Details** des Controllers **Cm**) sowie die Info- und Kontaktseiten angezeigt. Nur Benutzer mit der Rolle *canEdit* können auf Aktionsmethoden zugreifen, mit denen Daten geändert werden.

1.  Fügen Sie der Anwendung die Filter [Authorize][Authorize] und [RequireHttps][RequireHttps] hinzu. Alternativ können Sie das [Authorize][Authorize]-Attribut und das [RequireHttps][RequireHttps]-Attribut jedem Controller hinzufügen, es wird jedoch als Best Practice im Bereich Sicherheit betrachtet, die Attribute der gesamten Anwendung hinzuzufügen. Auf diese Weise wird jeder neu hinzugefügte Controller und jede neu hinzugefügte Aktionsmethode automatisch geschützt und Sie müssen nicht daran denken, die Attribute anzuwenden. Weitere Informationen finden Sie unter [Securing your ASP.NET MVC App and the new AllowAnonymous Attribute][Securing your ASP.NET MVC App and the new AllowAnonymous Attribute] (in englischer Sprache). Öffnen Sie die Datei *App\_Start\\FilterConfig.cs*, und ersetzen Sie die *RegisterGlobalFilters*-Methode durch den folgenden Code (damit werden die zwei Filter hinzugefügt):
		<pre>
        public static void
        RegisterGlobalFilters(GlobalFilterCollection filters)
        {
            filters.Add(new HandleErrorAttribute());
            <mark>filters.Add(new System.Web.Mvc.AuthorizeAttribute());
            filters.Add(new RequireHttpsAttribute());</mark>
        }
		</pre>
<span></span>
    Der geänderte Code ist im folgenden Bild dargestellt:

    ![Codebild][4]

    Der im obigen Code angewendete Filter [Authorize][Authorize] verhindert, dass anonyme Benutzer auf Methoden in der Anwendung zugreifen. Sie verwenden das [AllowAnonymous][Securing your ASP.NET MVC App and the new AllowAnonymous Attribute]-Attribut, um die Autorisierungsanforderung in einigen Methoden zu deaktivieren, sodass anonyme Benutzer sich anmelden und die Startseite anzeigen können. [RequireHttps][RequireHttps] erfordert, dass der gesamte Zugriff auf die Webanwendung durch HTTPS erfolgen muss.

2.  Fügen Sie der **Index**-Methode des Controllers **Home** das [AllowAnonymous][Securing your ASP.NET MVC App and the new AllowAnonymous Attribute]-Attribut hinzu. Mit dem [AllowAnonymous][Securing your ASP.NET MVC App and the new AllowAnonymous Attribute]-Attribut lassen Sie die Methoden zu, die von der Autorisierung ausgenommen werden sollen. Im Folgenden wird ein Bild eines Teils des HomeController angezeigt:

    ![Code][5]

3.  Führen Sie eine globale Suche nach *AllowAnonymous* durch. Sie werden feststellen, dass es in den Anmelde- und Registrierungsmethoden des Kontocontrollers verwendet wird.
4.  Fügen Sie in der Datei *CmController.cs* den Code `[Authorize(Roles = "canEdit")]` zu den **HttpGet**- und **HttpPost**-Methoden hinzu, mit denen Daten im Controller *Cm* geändert werden (Erstellen, Bearbeiten, Löschen, jede Aktionsmethode abgesehen von **Index** und **Details**). Im Folgenden wird ein Teil des fertigen Codes angezeigt:

    ![Bild des Codes][Bild des Codes]

## Aktivieren von SSL für das Projekt

1.  Aktivieren Sie SSL. Klicken Sie im Projektmappen-Explorer auf das Projekt **ContactManager**, und drücken Sie dann F4, um das Eigenschaftsdialogfeld aufzurufen. Ändern Sie **SSL-aktiviert** in true. Kopieren Sie die **SSL-URL**. Die SSL-URL lautet https://localhost:44300/, sofern Sie nicht zuvor SSL-Websites erstellt haben.

    ![SSL aktivieren][SSL aktivieren]

2.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **Contact Manager** und dann auf **Eigenschaften**.
3.  Klicken Sie auf der linken Registerkarte auf **Web**.
4.  Ändern Sie die **Projekt-URL** zur Verwendung der **SSL-URL**, und speichern Sie die Seite (STRG+S).

    ![SSL aktivieren][6]

5.  Stellen Sie sicher, dass Internet Explorer von Visual Studio gestartet wird, wie im folgenden Bild dargestellt:

    ![Standardbrowser][Standardbrowser]

    In der Browserauswahl können Sie den Browser angeben, der von Visual Studio gestartet wird.

	![Browserauswahl][Browserauswahl]

    You can select multiple browsers and have Visual Studio update each browser when you make changes. For more information see [Using Browser Link in Visual Studio 2013](http://www.asp.net/visual-studio/overview/2013/using-browser-link).

1.  Drücken Sie STRG+F5, um die Anwendung auszuführen. Befolgen Sie die Anweisungen, um das von IIS Express generierte selbstsignierte Zertifikat als vertrauenswürdig einzustufen.

    ![Anweisungen, um das von IIS Express generierte selbstsignierte Zertifikat als vertrauenswürdig einzustufen][Anweisungen, um das von IIS Express generierte selbstsignierte Zertifikat als vertrauenswürdig einzustufen]

2.  Lesen Sie das Dialogfeld **Sicherheitswarnung**, und klicken Sie dann auf **Ja**, wenn Sie das Zertifikat installieren möchten, das **localhost** darstellt.

	![IIS Express-Zertifikatwarnung für Localhost][IIS Express-Zertifikatwarnung für Localhost]

1.  Die Seite *Home* wird in IE angezeigt, und es gibt keine SSL-Warnungen.

    ![IE mit Localhost-SSL, keine Warnungen][IE mit Localhost-SSL, keine Warnungen]

    Google Chrome akzeptiert das Zertifikat ebenfalls und zeigt HTTPS-Inhalt ohne Warnung an. Firefox verwendet einen eigenen Zertifikatspeicher, deshalb wird eine Warnung angezeigt. Bei unserer Anwendung können Sie unbesorgt auf **Ich kenne das Risiko** klicken.

    ![Firefox-Zertifikatwarnung][Firefox-Zertifikatwarnung]

2.  Wenn Sie immer noch von einer vorherigen Sitzung angemeldet sind, klicken Sie auf den Link **Abmelden**.
3.  Klicken Sie auf den Link **About** oder **Contact**. Sie werden zur Anmeldeseite weitergeleitet, da anonyme Benutzer diese Seiten nicht anzeigen können.
4.  Klicken Sie auf den Link **Register as a new user**, und fügen Sie einen lokalen Benutzer mit der E-Mail-Adresse *joe@contoso.com* hinzu. Stellen Sie sicher, dass *Joe* die Seiten **Home**, **About** und **Contact** anzeigen kann.

    ![Anmeldung][Anmeldung]

5.  Klicken Sie auf den Link *CM Demo*, und überprüfen Sie, ob die Daten sichtbar sind.
6.  Klicken Sie auf einen Bearbeitungslink auf der Seite. Sie werden zur Anmeldeseite weitergeleitet (da kein neuer lokaler Benutzer zur Rolle *canEdit* hinzugefügt wurde).
7.  Melden Sie sich als *user1@contoso.com* mit dem Kennwort "P\_assw0rd1" an (bei "0" in "word" handelt es sich um eine Null). Sie werden zu der Bearbeitungsseite weitergeleitet, die Sie zuvor ausgewählt haben.

   Wenn Sie sich nicht mit diesem Konto und Kennwort anmelden können, versuchen Sie, das Kennwort aus dem Quellcode zu kopieren und einzufügen. Sollten Sie sich dennoch nicht anmelden können, überprüfen Sie in der Tabelle **AspNetUsers** in der Spalte **UserName**, ob *user1@contoso.com* wirklich hinzugefügt wurde.

1.  Stellen Sie sicher, dass Sie Daten ändern können.

## <a name="bkmk_deploytowindowsazure11"></a>Bereitstellen der Anwendung in Azure

1.  Klicken Sie im Projektmappen-Explorer von Visual Studio mit der rechten Maustaste auf das Projekt, und wählen Sie im Kontextmenü **Veröffentlichen** aus.

    !["Veröffentlichen" im Kontextmenü des Projekts][7]

    Der Assistent **Web veröffentlichen** wird geöffnet.

2.  Klicken Sie im Dialogfeld **Web veröffentlichen** auf der linken Seite auf **Einstellungen**. Klicken Sie auf das Symbol **v**, um für **ApplicationDbContext** die **Remoteverbindungszeichenfolge** auszuwählen, und wählen Sie dann **ContactDB** aus.

    ![Einstellungen][Einstellungen]

3.  Wählen Sie unter **ContactManagerContext** die Option **Execute Code First Migrations** aus.

    ![Einstellungen][8]

4.  Klicken Sie auf **Veröffentlichen**.

5.  Melden Sie sich als *user1@contoso.com* an, und stellen Sie sicher, dass Sie Daten bearbeiten können.

6.  Melden Sie sich ab.

7.  Melden Sie sich über Google oder Facebook an. Damit wird das Google- oder Facebook-Konto der Rolle **canEdit** hinzugefügt.

### Beenden der Website, um die Registrierung anderer Personen zu verhindern

1.  Navigieren Sie im **Server-Explorer** zu **Websites**.
2.  Klicken Sie mit der rechten Maustaste auf jede Websiteinstanz, und wählen Sie **Website anhalten** aus.

    ![Website anhalten][Website anhalten]

    Sie können die Website alternativ im Azure-Verwaltungsportal auswählen und dann auf das Symbol **Beenden** unten auf der Seite klicken.

    ![Website anhalten][9]

### Entfernen von "AddToRoleAsync", "Publish" und "Test"

1.  Kommentieren Sie den folgenden Code aus der **ExternalLoginConfirmation**-Methode im Kontocontroller aus, oder entfernen Sie ihn:
     `await UserManager.AddToRoleAsync(user.Id, "canEdit");`
2.  Erstellen Sie das Projekt (die Dateiänderungen werden dabei gespeichert und es wird sichergestellt, dass keine Kompilierungsfehler vorhanden sind).
3.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen** aus.

    !["Veröffentlichen" im Kontextmenü des Projekts]["Veröffentlichen" im Kontextmenü des Projekts]

4.  Klicken Sie auf die Schaltfläche **Vorschau starten**. Es werden nur die Dateien bereitgestellt, die aktualisiert werden müssen.
5.  Starten Sie die Website in Visual Studio oder über das Portal. **Eine Veröffentlichung ist bei angehaltener Website nicht möglich**.

    ![Website starten][Website starten]

6.  Kehren Sie zu Visual Studio zurück, und klicken Sie auf **Veröffentlichen**.
7.  Die Azure-Anwendung wird im Standardbrowser geöffnet. Wenn Sie angemeldet sind, melden Sie sich ab, sodass Sie die Startseite als anonymer Benutzer anzeigen können.
8.  Klicken Sie auf den Link **About**. Sie werden zur Anmeldeseite weitergeleitet.
9.  Klicken Sie auf den Link **Registrieren** auf der Anmeldeseite, und erstellen Sie ein lokales Konto. Dieses lokale Konto wird verwendet, um zu überprüfen, ob Sie auf die schreibgeschützten Seiten zugreifen können, jedoch nicht auf die Seiten mit Datenänderungen (die durch die Rolle *canEdit* geschützt sind). An späterer Stelle in diesem Lernprogramm wird der lokale Kontozugriff entfernt.

    ![Registrieren][10]

10. Stellen Sie sicher, dass Sie zu den Seiten *About* und *Contact* navigieren können.

    ![Abmelden][Abmelden]

11. Klicken Sie auf den Link **CM Demo**, um zum Controller **Cm** zu navigieren. Alternativ können Sie *Cm* an die URL anhängen.

    ![CM-Seite][CM-Seite]

12. Klicken Sie auf einen Bearbeitungslink. Sie werden zur Anmeldeseite weitergeleitet. Klicken Sie unter **Use another service to log in** auf Google oder Facebook, und melden Sie sich mit dem zuvor registrierten Konto an. (Wenn Sie schnell arbeiten und das Sitzungscookie noch nicht abgelaufen ist, werden Sie automatisch mit dem zuvor verwendeten Google- oder Facebook-Konto angemeldet.)
13. Stellen Sie sicher, dass Sie Daten bearbeiten können, während Sie beim Konto angemeldet sind.
    **Hinweis:** Sie können sich in dieser Anwendung nicht bei Google abmelden und mit demselben Browser bei einem anderen Google-Konto anmelden. Wenn Sie einen einzigen Browser verwenden, müssen Sie zu Google navigieren und sich abmelden. Sie können sich über den Drittanbieterauthentifikator (wie Google) mithilfe eines anderen Browsers anmelden.

Falls Sie Ihren Vornamen und Nachnamen im Google-Konto nicht ausgefüllt haben, tritt ein NullReferenceException-Ausnahmefehler auf.

## Überprüfen der SQL Azure-DB

1.  Navigieren Sie im Server-Explorer zu **ContactDB**.
2.  Klicken Sie mit der rechten Maustaste auf **ContactDB**, und wählen Sie **Open in SQL Server Object Explorer** aus.

    ![In SSOX öffnen][In SSOX öffnen]

**Hinweis:** Wenn Sie **SQL Databases** nicht erweitern und **ContactDB** in Visual Studio *nicht* anzeigen können, müssen Sie die folgenden Anweisungen befolgen, um einen Firewallport oder einen Portbereich zu öffnen. Befolgen Sie die Anweisungen unter **Set up Azure firewall rules**. Nach dem Hinzufügen der Firewallregel müssen Sie möglicherweise einige Minuten warten.

1.  Klicken Sie mit der rechten Maustaste auf die Tabelle **AspNetUsers**, und wählen Sie **Daten anzeigen** aus.

    ![CM-Seite][11]

2.  Beachten Sie, dass sich die ID des Google-Kontos, bei dem Sie sich registriert haben, und die ID von *user1@contoso.com* in der Rolle **canEdit** befinden. Dies sollten die einzigen Benutzer in der Rolle **canEdit** sein. (Wird im nächsten Schritt überprüft.)

    ![CM-Seite][12]

3.  Klicken Sie im SQL Server-Objekt-Explorer mit der rechten Maustaste auf **AspNetUserRoles**, und wählen Sie **Daten anzeigen** aus.

    ![CM-Seite][13]

Stellen Sie sicher, dass die **UserIds** von *user1@contoso.com* und dem registrierten Google-Konto stammen.

## Einrichten von Azure-Firewallregeln

Führen Sie die Schritte in diesem Abschnitt aus, wenn Sie in Visual Studio keine Verbindung mit SQL Azure herstellen können oder die Fehlermeldung "Cannot open server" erhalten.

![Firewallfehler][Firewallfehler]

Sie müssen Ihre IP-Adresse den zulässigen IPs hinzufügen.

1.  Wählen Sie im Azure-Portal auf der linken Registerkarte **SQL Databases** aus.

    ![SQL auswählen][SQL auswählen]

2.  Klicken Sie auf **ContactDB**.

3.  Klicken Sie auf den Link **Set up Azure firewall rules for this IP address**.

    ![Firewallregeln][Firewallregeln]

4.  Wenn die Frage "The current IP address xxx.xxx.xxx.xxx is not included in existing firewall rules. Do you want to update the firewall rules?" angezeigt wird, klicken Sie auf **Ja**. Das Hinzufügen dieser Adresse ist hinter einigen Unternehmensfirewalls oft nicht ausreichend, Sie müssen einen IP-Adressbereich hinzufügen.

Im nächsten Schritt wird ein Bereich zulässiger IP-Adressen hinzugefügt.

1.  Klicken Sie im Azure-Portal auf **SQL Databases**.
2.  Wählen Sie die Registerkarte **Server** aus, und klicken Sie dann auf den Server, den Sie konfigurieren möchten.

    ![Registerkarte "Server" in Azure][Registerkarte "Server" in Azure]

3.  Klicken Sie auf die Registerkarte **Configure**.

4.  Fügen Sie einen Regelnamen sowie die Start- und End-IP-Adresse hinzu.

    ![IP-Bereich][IP-Bereich]

5.  Klicken Sie unten auf der Seite auf **Speichern**.
6.  Bitte hinterlassen Sie Feedback und teilen Sie mir mit, ob Sie einen IP-Adressbereich hinzufügen mussten, um die Verbindung herzustellen.

Schließlich können Sie über SQL Server Object Explorer (SSOX) die Verbindung mit der SQL-Datenbank herstellen.

1.  Klicken Sie im Menü **Ansicht** auf **SQL Server-Objekt-Explorer**.
2.  Klicken Sie mit der rechten Maustaste auf **SQL Server**, und wählen Sie **Add SQL Server** aus.
3.  Legen Sie im Dialogfeld **Connect to Server** für **Authentifizierung** die Option **SQL Server Authentication** fest. Sie erhalten den Servernamen und die Anmeldung aus dem Azure-Portal.
4.  Navigieren Sie im Browser zum Portal, und wählen Sie **SQL Databases** aus.
5.  Wählen Sie **ContactDB** aus, und klicken Sie dann auf **View SQL Database connection strings**.
6.  Kopieren Sie auf der Seite **Connection Strings** den **Server** und die **Benutzer-ID**.

    ![Verbindungszeichenfolge][Verbindungszeichenfolge]

7.  Geben Sie die Server- und Benutzer-ID-Werte in das Dialogfeld **Connect to Server** in Visual Studio ein. Der Wert **Benutzer-ID** wird in den Eintrag **Anmeldung** eingefügt. Geben Sie das Kennwort ein, das Sie zum Erstellen der SQL-Datenbank verwendet haben.

    ![Dialogfeld "Connect to Server"][Dialogfeld "Connect to Server"]

Sie können nun mithilfe der oben genannten Anweisungen zur Kontaktdatenbank navigieren.

## So fügen Sie der Rolle "canEdit" durch die Bearbeitung von Datenbanktabellen einen Benutzer hinzu

An früherer Stelle in diesem Lernprogramm haben Sie Code verwendet, um Benutzer der Rolle **canEdit** hinzuzufügen. Alternativ dazu können Sie die Daten direkt in den Mitgliedschaftstabellen bearbeiten. In den folgenden Schritten wird gezeigt, wie mit dieser alternativen Methode ein Benutzer zu einer Rolle hinzugefügt wird.

1.  Klicken Sie im SQL Server-Objekt-Explorer mit der rechten Maustaste auf **AspNetUserRoles**, und wählen Sie **Daten anzeigen** aus.

    ![CM-Seite][13]

2.  Kopieren Sie die *RoleId*, und fügen Sie sie in die leere (neue) Zeile ein.

    ![CM-Seite][14]

3.  Suchen Sie in der Tabelle **AspNetUsers** den Benutzer, den Sie in die Rolle versetzen möchten, kopieren Sie die *Id* des Benutzers, und fügen Sie sie in die Spalte **UserId** der Tabelle **AspNetUserRoles** ein.

Zurzeit wird ein Tool entwickelt, das die Verwaltung von Benutzern und Rollen vereinfacht.

## Überlegungen zur lokalen Registrierung

Die aktuelle ASP.NET-Mitgliedschaftsregistrierung im Projekt umfasst keine Unterstützung von Kennwortzurücksetzungen und es wird nicht überprüft, ob eine Person sich registriert (z. B. durch [CAPTCHA][CAPTCHA]). Nachdem sich ein Benutzer über einen der Drittanbieter authentifiziert hat, kann er sich registrieren. Führen Sie die folgenden Schritte aus, wenn Sie die lokale Registrierung deaktivieren möchten:

1.  Entfernen Sie im Kontocontroller das *[AllowAnonymous]*-Attribut aus den GET- und POST-*Register*-Methoden. Auf diese Weise können sich weder Robots noch anonyme Benutzer registrieren.
2.  Entfernen Sie im Ordner *Views\\Shared* in der Datei *_LoginPartial.cshtml* den Aktionslink für die Registrierung.
3.  Entfernen Sie in der Datei *Views\\Account\\Login.cshtml* den Aktionslink für die Registrierung.
4.  Stellen Sie die Anwendung bereit.

## <a name="nextsteps"></a><span class="short-header">Nächste Schritte</span>Nächste Schritte

Anweisungen zum Hinzufügen von Profildaten zur Benutzerregistrierungsdatenbank und ausführliche Informationen zum Verwenden von Facebook als Authentifizierungsanbieter finden Sie in meinem Lernprogramm [Create an ASP.NET MVC 5 App with Facebook and Google OAuth2 and OpenID Sign-on][Create an ASP.NET MVC 5 App with Facebook and Google OAuth2 and OpenID Sign-on].

Informationen zum Aktivieren der Anmeldeschaltflächen für soziale Netzwerke, die oben in diesem Lernprogramm dargestellt sind, finden Sie unter [Pretty social login buttons for ASP.NET MVC 5][Pretty social login buttons for ASP.NET MVC 5] (in englischer Sprache).

Ein guter Ausgangspunkt für weitere Informationen zu ASP.NET MVC ist mein Lernprogramm [Getting Started with ASP.NET MVC 5][Getting Started with ASP.NET MVC 5] (in englischer Sprache). Im ausgezeichneten Artikel [Getting Started with EF and MVC][Getting Started with EF and MVC] von Tom Dykstra erhalten Sie einen Einblick in die fortgeschrittene MVC- und EF-Programmierung.

Dieses Lernprogramm und die Beispielanwendung wurden von [Rick Anderson][Rick Anderson] (Twitter [@RickAndMSFT][@RickAndMSFT]) sowie Tom Dykstra und Barry Dorrans (Twitter [@blowdart][@blowdart]) geschrieben.

Bitte teilen Sie uns mit, was Ihrer Meinung nach gelungen ist, bzw. verbessert werden kann, und zwar nicht nur bezüglich des Lernprogramms, sondern auch bezüglich der Produkte, die darin vorgestellt werden. Ihr Feedback unterstützt uns dabei, eine entsprechende Priorität für Verbesserungen festzulegen. Unter [Show Me How With Code][Show Me How With Code] (in englischer Sprache) können Sie neue Themen anfordern oder eine Stimme dafür abgeben.

<!-- bookmarks --> 

  [vorherige Lernprogramm]: /de-de/develop/net/tutorials/web-site-with-sql-database-vs2012/
  [OAuth]: http://oauth.net/ "http://oauth.net/"
  [OpenID]: http://openid.net/
  [Anmeldeseite]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxb.png
  [Ihre MSDN-Abonnementenvorteile aktivieren]: /de-de/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
  [sich für eine kostenlose Testversion registrieren]: /de-de/pricing/free-trial/?WT.mc_id=A261C142F
  [Einrichten der Entwicklungsumgebung]: #setupdevenv
  [Einrichten der Azure-Umgebung]: #bkmk_setupwindowsazure
  [Erstellen einer ASP.NET MVC 5-Anwendung]: #bkmk_createmvc4app
  [Bereitstellen der Anwendung in Azure]: #bkmk_deploytowindowsazure1
  [Hinzufügen einer Datenbank zur Anwendung]: #bkmk_addadatabase
  [Hinzufügen eines OAuth-Anbieters]: #addOauth
  [Verwenden der Mitgliedschafts-API]: #mbrDB
  [1]: #bkmk_deploytowindowsazure11
  [Nächste Schritte]: #nextsteps
  [install-sdk-2013-only]: ../includes/install-sdk-2013-only.md
  [Visual Studio 2013 Update 2 RC]: http://go.microsoft.com/fwlink/?LinkId=390521
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com

<!-- images-->
  [Schaltfläche "Neu" im Verwaltungsportal]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxWSnew2.png
  [Erstellung über Datenbanklink im Verwaltungsportal]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxCreateWSwithDB.png
  [2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr1.png
  [Schritt "Datenbankeinstellungen" im Assistenten "New Web Site - Create with Database"]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-setup-azure-site-004.png
  [3]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxPrevDB.png
  ["Neues Projekt" im Menü "Datei"]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/gs13newproj.png
  [Dialogfeld "Neues Projekt"]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13newprojdb.png
  [Dialogfeld "Neues ASP.NET-Projekt"]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss1.PNG
  [Codeänderungen]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs3.png
  [Lokal ausgeführte Website]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr2.png
  ["Veröffentlichen" im Kontextmenü des Projekts]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13publish.png
  [Veröffentlichungseinstellungen importieren]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss2.PNG
  [Anmelden]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss22.PNG
  [Website auswählen]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss3.png
  [Publish]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr3.png
  [In Cloud ausgeführt]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss4.PNG
  [Klasse hinzufügen im Kontextmenü des Ordners "Models"]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr5.png
  [Dialogfeld "Neues Element hinzufügen"]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-adddatabase-002.png
  [Controller hinzufügen im Kontextmenü des Ordners "Controllers"]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-controller-add-context-menu.png
  [Dialogfeld "Add Scaffold"]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr6.png
  [Kontextdialogfeld "Neue Daten"]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss5.PNG
  [Code First-Migrationen]: http://msdn.microsoft.com/library/hh770484.aspx
  ["Paket-Manager-Konsole" im Menü "Extras"]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/SS6.png
  [Befehle in der Paket-Manager-Konsole]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-migrations-package-manager-console.png
  [MVC-Datenansicht]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx2.png
  [Create an ASP.NET MVC 5 App with Facebook and Google OAuth2 and OpenID Sign-on]: http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on
  [Google-Anmeldung]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss23.PNG
  [GOOGLE Zustimmungsabfrage]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss7.PNG
  [Registrieren]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss8.PNG
  [Codebild]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss24.PNG
  [ASP.NET Identity-Ressourcenseite]: http://curah.microsoft.com/55636/aspnet-identity
  [WSAT]: http://msdn.microsoft.com/de-de/library/ms228053.aspx
  [Code]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss9.PNG
  [Authorize]: http://msdn.microsoft.com/de-de/library/system.web.mvc.authorizeattribute.aspx
  [RequireHttps]: http://msdn.microsoft.com/de-de/library/system.web.mvc.requirehttpsattribute.aspx
  [Securing your ASP.NET MVC App and the new AllowAnonymous Attribute]: http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx
  [4]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss10.PNG
  [5]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss11.PNG
  [Bild des Codes]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr11.png
  [SSL aktivieren]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxSSL.png
  [6]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr1.png
  [Standardbrowser]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss12.PNG
  [Browserauswahl]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss13.png
  [Anweisungen, um das von IIS Express generierte selbstsignierte Zertifikat als vertrauenswürdig einzustufen]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss26.PNG
  [IIS Express-Zertifikatwarnung für Localhost]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss27.PNG
  [IE mit Localhost-SSL, keine Warnungen]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss28.PNG
  [Firefox-Zertifikatwarnung]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss30.PNG
  [Anmeldung]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss14.PNG
  [7]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-deploy1-publish-001.png
  [Einstellungen]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrc2.png
  [8]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrc3.png
  [Website anhalten]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr2.png
  [9]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr3.png
  [Website starten]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss15.png
  [10]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss16.PNG
  [Abmelden]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss17.PNG
  [CM-Seite]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr4.png
  [In SSOX öffnen]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr12.png
  [11]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr8.png
  [12]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr9.png
  [13]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs1.png
  [Firewallfehler]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx5.png
  [SQL auswählen]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx6.png
  [Firewallregeln]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx7.png
  [Registerkarte "Server" in Azure]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss25.PNG
  [IP-Bereich]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx9.png
  [Verbindungszeichenfolge]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss21.PNG
  [Dialogfeld "Connect to Server"]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rss1.png
  [14]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs2.png
  [CAPTCHA]: http://www.asp.net/web-pages/tutorials/security/16-adding-security-and-membership
  [Pretty social login buttons for ASP.NET MVC 5]: http://www.beabigrockstar.com/pretty-social-login-buttons-for-asp-net-mvc-5/
  [Getting Started with ASP.NET MVC 5]: http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started
  [Getting Started with EF and MVC]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application
  [Rick Anderson]: http://blogs.msdn.com/b/rickandy/
  [@RickAndMSFT]: https://twitter.com/RickAndMSFT
  [@blowdart]: https://twitter.com/blowdart
  [Show Me How With Code]: http://aspnet.uservoice.com/forums/228522-show-me-how-with-code
