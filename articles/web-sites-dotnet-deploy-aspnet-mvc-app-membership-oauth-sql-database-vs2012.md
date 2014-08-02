<properties linkid="" urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title=" OAuth" authors="riande" solutions="" manager="wpickett" editor="mollybos" />

Bereitstellen einer sicheren ASP.NET MVC-Anwendung mit Mitgliedschafts-, OAuth- und SQL-Datenbank für eine Azure-Website
========================================================================================================================

***Von [Rick Anderson](https://twitter.com/RickAndMSFT) und Tom Dykstra. Aktualisiert am 15. Oktober 2013.***

[Visual Studio 2013](/en-us/develop/net/tutorials/web-site-with-sql-database/ "Visual Studio 2013")[Visual Studio 2012](/en-us/develop/net/tutorials/web-site-with-sql-database-vs2012/ "Visual Studio 2012")

**Hinweis**

Es ist eine [aktuellere Version dieses Lernprogramms](/en-us/develop/net/tutorials/web-site-with-sql-database/) verfügbar. Sie können diese Version weiterhin nutzen, wenn Sie Visual Studio 2012 verwenden, die neue Version ist jedoch viel benutzerfreundlicher.

In diesem Lernprogramm wird die Erstellung einer sicheren ASP.NET MVC 4-Webanwendung erläutert, mit der Benutzer sich mithilfe der Anmeldeinformationen von Facebook, Yahoo oder Google anmelden können. Darüber hinaus wird die Anwendung für Azure bereitgestellt.

Sie können ein kostenloses Azure-Konto erstellen. Wenn Sie noch nicht über Visual Studio 2012 verfügen, wird Visual Studio 2012 für das Web Express automatisch durch das SDK installiert. Sie können kostenlos mit der Entwicklung für Azure beginnen.

Bei diesem Lernprogramm wird davon ausgegangen, dass Sie noch keine Erfahrung mit der Verwendung von Azure haben. Nach Abschluss dieses Lernprogramms verfügen Sie über eine sichere datengesteuerte Webanwendung, die unter Rückgriff auf eine Clouddatenbank in der Cloud ausgeführt wird.

Sie lernen Folgendes:

-   Ermöglichen der Azure-Entwicklung auf Ihrem Computer durch Installieren des Azure SDK.
-   Erstellen eines sicheren ASP.NET MVC 4-Projekts und Veröffentlichen dieses Projekts auf einer Azure-Website
-   Verwenden von OAuth und der ASP.NET-Mitgliedschaftsdatenbank zum Sichern der Anwendung
-   Bereitstellung einer Mitgliedschaftsdatenbank in Azure
-   Verwenden einer SQL-Datenbank zum Speichern von Daten in Azure
-   Verwenden von Visual Studio zur Aktualisierung und Verwaltung der Mitgliedschaftsdatenbank

Sie erstellen eine einfache Kontaktlisten-Webanwendung, die auf ASP.NET MVC 4 beruht und für den Datenbankzugriff ADO.NET Entity Framework verwendet. In der folgenden Abbildung wird die Anmeldeseite der fertiggestellten Anwendung dargestellt:

![Anmeldeseite](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxb.png)

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

Dieses Lernprogramm umfasst folgende Punkte:

-   [Einrichten der Entwicklungsumgebung](#bkmk_setupdevenv)
-   [Einrichten der Azure-Umgebung](#bkmk_setupwindowsazure)
-   [Erstellen einer ASP.NET MVC 4-Anwendung](#bkmk_createmvc4app)
-   [Bereitstellen der Anwendung in Azure](#bkmk_deploytowindowsazure1)
-   [Hinzufügen einer Datenbank zu der Anwendung](#bkmk_addadatabase)
-   [Hinzufügen eines OAuth-Anbieters](#addOauth)
-   [Hinzufügen von Rollen zur Mitgliedschaftsdatenbank](#mbrDB)
-   [Erstellen eines Datenbereitstellungsskripts](#ppd)
-   [Bereitstellen der Anwendung in Azure](#bkmk_deploytowindowsazure11)
-   [Aktualisieren der Mitgliedschaftsdatenbank](#ppd2)
-   [Nächste Schritte](#nextsteps)

Einrichten der Entwicklungsumgebung
-----------------------------------

Richten Sie zu Beginn Ihre Entwicklungsumgebung ein, indem Sie das Azure SDK für .NET Framework installieren.

1.  Klicken Sie auf den folgenden Link, um das Azure SDK für .NET zu installieren. Wenn Sie Visual Studio 2012 noch nicht installiert haben, wird es über den Link installiert. Für dieses Lernprogramm ist Visual Studio 2012 erforderlich. [Azure SDK für Visual Studio 2012](http://go.microsoft.com/fwlink/?LinkId=254364)
2.  Wenn Sie aufgefordert werden, die ausführbare Installationsdatei auszuführen oder zu speichern, klicken Sie auf **Ausführen**.
3.  Klicken Sie im Fenster "Webplattform-Installer" auf **Installieren**, und setzen Sie die Installation fort.

![Webplattform-Installer – Azure SDK für .NET](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/WebPIAzureSdk20NetVS12.png)

Nach Abschluss der Installation haben Sie alles zur Hand, was Sie benötigen, um mit der Entwicklung zu beginnen.

Einrichten der Azure-Umgebung
-----------------------------

Richten Sie als Nächstes die Azure-Umgebung ein, indem Sie eine Azure-Website und eine SQL-Datenbank erstellen.

### Erstellen einer Website und einer SQL-Datenbank in Azure

Ihre Azure-Website wird in einer freigegebenen Hostingumgebung und damit auf virtuellen Computern (VMs) ausgeführt, die für andere Azure-Clients freigegeben wurden. Eine freigegebene Hostingumgebung ist eine kostengünstige Möglichkeit, mit der Verwendung der Cloud zu beginnen. Später kann die Anwendung skaliert werden, sobald der Webdatenverkehr zunimmt, um die Anforderungen durch die Ausführung dedizierter virtueller Maschinen zu erfüllen. Wenn Sie eine komplexere Architektur benötigen, können Sie eine Migration zu einem Azure-Clouddienst ausführen. Clouddienste werden auf dedizierten VMs ausgeführt, die Sie Ihren Anforderungen entsprechend konfigurieren können.

Die Azure SQL-Datenbank ist ein cloudbasierter relationaler Datenbankdienst auf Grundlage von SQL Server-Technologien. Die Tools und Anwendungen, die mit SQL Server verwendet werden können, sind auch für die SQL-Datenbank geeignet.

1.  Klicken Sie im [Azure-Verwaltungsportal](https://manage.windowsazure.com) auf der linken Registerkarte auf **Websites** und dann auf **Neu**.

	![Schaltfläche "Neu" im Verwaltungsportal](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxWSnew2.png)

1.  Klicken Sie auf **CUSTOM CREATE**.

    ![Erstellung über Datenbanklink im Verwaltungsportal](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxCreateWSwithDB.png)

Der Assistent **New Web Site - Custom Create** wird geöffnet.

1.  Geben Sie im Schritt **Neue Website** des Assistenten eine Zeichenfolge in das Feld **URL** ein, die als eindeutige URL der Anwendung dienen soll. Die vollständige URL besteht aus der hier eingegebenen Zeichenfolge und dem Suffix, das neben dem Textfeld aufgeführt wird. In der Abbildung wird die URL "contactmgr2" dargestellt. Diese URL wird möglicherweise bereits verwendet, und Sie müssen eine andere URL wählen.

    ![Erstellung über Datenbanklink im Verwaltungsportal](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxCreateWSwithDB_2.png)

2.  Wählen Sie in der Dropdownliste **Datenbank** die Option **Create a new SQL database** aus.

3.  Wählen Sie in der Dropdownliste **Region** dieselbe Region aus, die Sie für die Website ausgewählt haben. Mit dieser Einstellung wird das Rechenzentrum angegeben, in dem Ihr virtueller Computer ausgeführt wird. Geben Sie im Feld **DB CONNECTION STRING NAME** *connectionString1* ein.

    ![Erstellen eines neuen Website-Schritts einer neuen Website – Mit Datenbankassistent erstellen](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxCreateWSwithDB_2.png)

4.  Klicken Sie auf den Pfeil, der nach rechts unten im Feld weist. Der Assistent springt zum Schritt **Datenbankeinstellungen**.

5.  Geben Sie in das Feld **Name** die Zeichenfolge *ContactDB* ein.

6.  Wählen Sie im Feld **Server** die Option **New SQL Database server**. Wenn Sie bereits zuvor eine SQL Server-Datenbank erstellt haben, können Sie alternativ diese SQL Server-Instanz aus der Dropdownliste auswählen.

7.  Geben Sie unter **ANMELDENAME** und **KENNWORT** die Anmeldedaten eines Administrators ein. Wenn Sie **New SQL Database server** ausgewählt haben, geben Sie hier keinen vorhandenen Namen und kein vorhandenes Kennwort ein. Stattdessen definieren Sie jetzt einen neuen Namen und ein neues Kennwort zur späteren Verwendung beim Datenbankzugriff. Wenn Sie eine zuvor erstellte SQL Server-Version ausgewählt haben, werden Sie aufgefordert, das Kennwort für den zuvor erstellten SQL Server-Kontonamen einzugeben. Für dieses Lernprogramm wird das Feld **Advanced** nicht aktiviert. In dem Feld **Advanced** können Sie die Datenbankgröße (die Standardgröße beträgt 1 GB, es sind jedoch bis zu 150 GB verfügbar) und die Sortierung festlegen.

8.  Klicken Sie auf das Häkchen unten im Feld, um anzugeben, dass Sie fertig sind.

    ![Schritt "Datenbankeinstellungen" im Assistenten "New Web Site - Create with Database"](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-setup-azure-site-004.png)

    Im folgenden Bild wird die Verwendung einer vorhandenen SQL Server-Version und -Anmeldung gezeigt. ![Schritt "Datenbankeinstellungen" im Assistenten "New Web Site - Create with Database"](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxPrevDB.png)

    Sie kehren zur Seite Websites im Verwaltungsportal zurück, und in der Spalte **Status** wird angegeben, dass die Website erstellt wird. Nach einer Weile (normalerweise weniger als eine Minute) wird in der Spalte **Status** angegeben, dass die Website erfolgreich erstellt wurde. In der linken Navigationsleiste wird die Anzahl der Websites in Ihrem Konto neben dem Symbol **Websites** angegeben und die Anzahl der Datenbanken neben dem Symbol **SQL-Datenbanken**.

Erstellen einer ASP.NET MVC 4-Anwendung
---------------------------------------

Sie haben nun eine Azure-Website erstellt, diese enthält jedoch noch keinen Inhalt. Zunächst erstellen Sie ein Visual Studio-Webanwendungsprojekt, das Sie auf Azure veröffentlichen.

### Erstellen des Projekts

1.  Starten Sie Visual Studio 2012.
2.  Klicken Sie im Menü **Datei** auf **Neues Projekt**.
3.  Erweitern Sie im Dialogfeld **Neues Projekt** den Eintrag **Visual C\#**, und wählen Sie die Option **Web** unter **Installierte Vorlagen** aus. Wählen Sie dann **ASP.NET MVC 4-Webanwendung aus**. Behalten Sie die Standardeinstellung **.NET Framework 4.5** bei. Nennen Sie die Anwendung **ContactManager**, und klicken Sie auf **OK**.

    ![Dialogfeld "Neues Projekt"](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-createapp-002.png)

4.  Wählen Sie im Dialogfeld **Neues ASP.NET MVC 4-Projekt** die Vorlage **Internetanwendung** aus. Behalten Sie das standardmäßige Razor-**Ansichtsmodul** bei, und klicken Sie dann auf **OK**.

    ![Dialogfeld "Neues ASP.NET MVC 4-Projekt"](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxb2.png)

### Einrichten der Seitenkopf- und -fußzeile

1.  Erweitern Sie im **Projektmappen-Explorer** den Ordner "Views\\Shared", und öffnen Sie die Datei *\_Layout.cshtml*.

    ![\_Layout.cshtml im Projektmappen-Explorer](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-createapp-004.png)

2.  Ersetzen Sie die Vorkommnisse von "My ASP.NET MVC Application" durch "Contact Manager".
3.  Ersetzen Sie "your logo here" durch "CM Demo".

### Lokales Ausführen der Anwendung

1.  Drücken Sie STRG+F5, um die Anwendung auszuführen. Die Startseite der Anwendung wird im Standardbrowser angezeigt. ![Homepage To-Do-Liste](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxa.png)

Darüber hinaus müssen Sie keine weiteren Aktionen ausführen, um die Anwendung zu erstellen, die Sie für Azure bereitstellen. Zu einem späteren Zeitpunkt fügen Sie Datenbankfunktionalität hinzu.

Bereitstellen der Anwendung in Azure
------------------------------------

1.  Öffnen Sie das [Azure Verwaltungsportal](http://manage.windowsazure.com "portal") in Ihrem Browser.

2.  Klicken Sie auf der Registerkarte **Websites** auf den Namen der zuvor erstellten Seite.

    ![Die Anwendung "Contact Manager" in der Registerkarte "Websites" des Verwaltungsportals](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-setup-azure-site-006.png)

3.  Klicken Sie auf der rechten Seite des Fensters auf **Veröffentlichungsprofil herunterladen**.

    ![Schnellstartregisterkarte und Schaltfläche "Veröffentlichungsprofil herunterladen"](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-deploy1-download-profile.png)

    In diesem Schritt laden Sie eine Datei herunter, die alle Einstellungen enthält, die Sie zur Bereitstellung der Anwendung auf Ihrer Website benötigen. Sie importieren diese Datei in Visual Studio, um die Informationen nicht manuell eingeben zu müssen.

4.  Speichern Sie die Datei *publishsettings* in einem Ordner, auf den Sie aus Visual Studio zugreifen können.

    ![Speichern der .publishsettings-Datei](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-deploy1-save-profile.png)

    [WACOM.INCLUDE [publishsettingsfilewarningchunk](../includes/publishsettingsfilewarningchunk.md)]

5.  Klicken Sie im **Projektmappen-Explorer** von Visual Studio mit der rechten Maustaste auf das Projekt, und wählen Sie im Kontextmenü **Veröffentlichen** aus.

    !["Veröffentlichen" im Kontextmenü des Projekts](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/PublishVSSolution.png)

    Der Assistent **Web veröffentlichen** wird geöffnet.

6.  Klicken Sie im Assistenten **Web veröffentlichen** auf der Registerkarte **Profil** auf **Importieren**.

    ![Veröffentlichungseinstellungen importieren](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/ImportPublishSettings.png)

    Das Dialogfeld **Veröffentlichungsprofil importieren** wird geöffnet.

7.  Wenn Sie Ihr Azure-Abonnement noch nicht in Visual Studio hinzugefügt haben, führen Sie die folgenden Schritte aus. Mit diesen Schritten fügen Sie Ihr Abonnement hinzu, sodass Ihre Website in der Dropdownliste **Aus einer Azure-Website importieren** enthalten ist.

    a. Klicken Sie im Dialogfeld **Veröffentlichungsprofil importieren** auf **Add Azure subscription**.

    ![Azure-Abo hinzufügen](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rzAddWAsub.png)

    b. Klicken Sie im Dialogfeld **Import Azure Subscriptions** auf **Download subscription file**.

    ![Abo herunterladen](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rzDownLoad.png)

    c. Speichern Sie im Browserfenster die Datei *.publishsettings*.

    ![Veröffentlichungsdatei herunterladen](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rzDown2.png)

    > [WACOM.NOTE] Die Datei ".publishsettings" enthält Ihre Anmeldeinformationen (unverschlüsselt), die verwendet werden, um Ihre Azure-Abonnements und -Dienste zu verwalten. Die bewährte Sicherheitsmethode für diese Datei besteht im vorübergehenden Speichern außerhalb Ihrer Quellcodeverzeichnisse (beispielsweise im Ordner "Libraries\\Documents"). Löschen Sie sie nach Abschluss des Importvorgangs. Böswillige Benutzer, die Zugriff auf die .publishsettings-Datei erlangen, können Ihre Azure-Dienste bearbeiten, erstellen und löschen.

    d. Klicken Sie im Dialogfeld **Import Azure Subscriptions** auf **Durchsuchen** und navigieren Sie zur *.publishsettings*-Datei.

    ![Abo herunterladen](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rzDownLoad.png)

    e. Klicken Sie auf **Importieren**.

    ![Importieren](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rzImp.png)

8.  Wählen Sie im Dialogfeld **Veröffentlichungsprofil importieren** die Option **Aus einer Azure-Website importieren** und dann Ihre Website in der Dropdownliste aus, und klicken Sie auf **OK**.

    ![Import Publish Profile](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/ImportPublishProfile.png)

    Die erstellte Anwendung wird nun in der Cloud ausgeführt. Bei der nächsten Bereitstellung der Anwendung werden nur die geänderten (oder neuen) Dateien bereitgestellt.

    ![To-Do-List-Homepage in Azure](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/newapp005.png)

Hinzufügen einer Datenbank zu der Anwendung
-------------------------------------------

Als Nächstes aktualisieren Sie die MVC-Anwendung, um Funktionen zum Anzeigen und Aktualisieren von Kontakten sowie Speichern der Daten in einer Datenbank hinzuzufügen. Die Anwendung verwendet Entity Framework, um die Datenbank zu erstellen und Daten in der Datenbank zu lesen und zu aktualisieren.

### Hinzufügen von Datenmodellklassen für die Kontakte

Sie beginnen mit der Erstellung eines einfachen Datenmodells in Code.

1.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner "Models", und klicken Sie dann auf **Hinzufügen** \> **Klasse**.

	![Klasse hinzufügen im Kontextmenü des Ordners "Models"](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-adddatabase-001.png)

1.  Geben Sie der neuen Klassendatei im Dialogfeld **Neues Element hinzufügen** den Namen *Contact.cs*, und klicken Sie dann auf **Hinzufügen**.

	![Dialogfeld "Neues Element hinzufügen"](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-adddatabase-002.png)

1.  Ersetzen Sie den Inhalt der Datei Contacts.cs durch den folgenden Code.

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

Hinzufügen eines Controllers und einer Ansicht für die Daten
------------------------------------------------------------

1.  Erstellen Sie das Projekt **(STRG+UMSCHALT+B)**. (Sie müssen das Projekt vor der Verwendung des Gerüstmechanismus erstellen.)
2.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner "Controllers", und klicken Sie dann auf **Hinzufügen** \> **Controller**.

    ![Controller hinzufügen im Kontextmenü des Ordners "Controllers"](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-controller-add-context-menu.png)

3.  Geben Sie im Dialogfeld **Controller hinzufügen** die Zeichenfolge "HomeController" als Controllernamen ein.
4.  Stellen Sie für die Vorlage **Scaffolding options** **MVC Controller with read/write actions and views, using Entity Framework** ein.
5.  Wählen Sie **Contact** als Modellklasse und **&lt;Neuer Datenkontext...\>** als Datenkontextklasse.

    ![Dialogfeld "Controller" hinzufügen](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-controller-add-controller-dialog.png)

6.  Übernehmen Sie im Dialogfeld **Neuer Datenkontext** den Standardwert *ContactManager.Models.ContactManagerContext*. ![Dialogfeld "Controller" hinzufügen](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxNewCtx.png)

7.  Klicken Sie auf **OK**, und klicken Sie dann im Dialogfeld **Controller hinzufügen** auf **Hinzufügen**.
8.  Stellen Sie im Überschreibungsdialog **Controller hinzufügen** sicher, dass alle Optionen aktiviert sind, und klicken Sie auf **OK**.

    ![Meldungsfeld "Controller hinzufügen"](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxOverwrite.png)

In Visual Studio werden Controllermethoden und Ansichten für CRUD-Datenbankvorgänge für **Contact**-Objekte erstellt.

Aktivieren von Migrationen, Erstellen der Datenbank, Hinzufügen von Beispieldaten und eines Dateninitialisierers
----------------------------------------------------------------------------------------------------------------

Die nächste Aufgabe besteht darin, das Feature [Code First-Migrationen](http://msdn.microsoft.com/library/hh770484.aspx) zu aktivieren, um die Datenbank basierend auf dem erstellten Datenmodell zu erstellen.

1.  Wählen Sie im Menü **Extras** den Eintrag **Paketverwaltung** und danach **Paket-Manager-Konsole** aus.

    !["Paket-Manager-Konsole" im Menü "Extras"](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-migrations-package-manager-menu.png)

2.  Geben Sie im Fenster **Paket-Manager-Konsole** den folgenden Befehl ein:

         enable-migrations -ContextTypeName ContactManagerContext

    ![enable-migrations](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxE.png) 
	Sie müssen den Kontexttypnamen angeben (**ContactManagerContext**), da das Projekt zwei aus [DbContext](http://msdn.microsoft.com/en-us/library/system.data.entity.dbcontext(v=VS.103).aspx) abgeleitete Klassen enthält: den gerade hinzugefügten **ContactManagerContext** und den **UsersContext**, der für die Mitgliedschaftsdatenbank verwendet wird. Die Klasse **ContactManagerContext** wurde vom Gerüstassistenten von Visual Studio hinzugefügt.

    Mit dem Befehl **enable-migrations** wird der Ordner *Migrations* erstellt und die Datei *Configuration.cs* darin abgelegt. Sie können diese Datei bearbeiten, um "Migrations" zu konfigurieren.

3.  Geben Sie im Fenster **Paket-Manager-Konsole** den folgenden Befehl ein:

         add-migration Initial

    Mit dem Befehl **add-migration-initial** wird eine Datei namens **&lt;Zeitstempel\>Initial** im Ordner *Migrations* generiert, mit der die Datenbank erstellt wird. Der erste Parameter (**Initial**) ist willkürlich und wird zum Erstellen des Dateinamens verwendet. Sie können die neue Klasse im **Projektmappen-Explorer** anzeigen.

    In der **Initial**-Klasse wird mit der **Up**-Methode die Tabelle Contacts erstellt und mit der **Down**-Methode (wird verwendet, wenn Sie zum vorherigen Status zurückkehren möchten) wieder verworfen.

4.  Öffnen Sie die Datei *Migrations\\Configuration.cs*.
5.  Fügen Sie die folgenden Namespaces hinzu.

    	using ContactManager.Models;

6.  Ersetzen Sie die *Seed*-Methode durch den folgenden Code:

         protected override void Seed(ContactManager.Models.ContactManagerContext context)
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

    Mit diesem Code wird die Datenbank mit den Kontaktinformationen initialisiert. Weitere Informationen zum Ausführen eines Seedings für die Datenbank finden Sie unter [Seeding and Debugging Entity Framework (EF) DBs](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx) (in englischer Sprache).

7.  Geben Sie im Fenster **Paket-Manager-Konsole** den folgenden Befehl ein:

         update-database

    ![Befehle in der Paket-Manager-Konsole](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-migrations-package-manager-console.png)

    Mit **update-database** wird die erste Migration ausgeführt, wodurch die Datenbank erstellt wird. Standardmäßig wird die Datenbank als SQL Server Express LocalDB-Datenbank erstellt. (Es sei denn, Sie haben SQL Server Express installiert. In diesem Fall wird die Datenbank mit der SQL Server Express-Instanz erstellt.)

8.  Drücken Sie STRG+F5, um die Anwendung auszuführen.

In der Anwendung werden die Seeddaten angezeigt und Links zum Bearbeiten und Löschen und zu Details bereitgestellt.

![MVC-Datenansicht](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx2.png)

OAuthHinzufügen eines OAuth-Anbieters
-------------------------------------

[OAuth](http://oauth.net/ "http://oauth.net/") ist ein offenes Protokoll, das die sichere Autorisierung in einer einfachen und Standardmethode von Web-, Mobil- und Desktopanwendungen ermöglicht. OAuth wird von der ASP.NET MVC-Internetvorlage verwendet, um Facebook, Twitter, Google, Yahoo und Microsoft als Authentifizierungsanbieter verfügbar zu machen. Auch wenn in diesem Lernprogramm nur Facebook, Google und Yahoo als Authentifizierungsanbieter eingesetzt werden, können Sie den Code problemlos für die Verwendung eines der anderen Anbieter anpassen. Die Schritte zur Implementierung anderer Anbieter unterscheiden sich kaum von den Schritten in diesem Lernprogramm.

Abgesehen von der Authentifizierung werden in diesem Lernprogramm auch Rollen verwendet, um die Autorisierung zu implementieren. Nur Benutzer, die Sie der Rolle "canEdit" hinzufügen, können Kontakte erstellen, bearbeiten oder löschen.

Registrieren mit einem externen Anbieter
----------------------------------------

Wenn Sie Benutzer mit Anmeldeinformationen von externen Anbietern authentifizieren möchten, müssen Sie Ihre Website bei diesem Anbieter registrieren, und einen Schlüssel sowie einen geheimen Verbindungsschlüssel erhalten. Bei Google und Yahoo müssen Sie keine Registrierung vornehmen und keine Schlüssel erhalten.

Dieses Lernprogramm enthält nicht alle Schritte, die zur Registrierung bei diesen Anbietern erforderlich sind. Diese Schritte sind meist einfach. Befolgen Sie die Anweisungen auf diesen Websites, um eine erfolgreiche Registrierung Ihrer Website durchzuführen. Sie können Ihre Website auf den Entwicklerwebsites der folgenden Anbieter registrieren:

-   [Facebook](http://developers.facebook.com/)
-   [Microsoft](http://go.microsoft.com/fwlink/?LinkID=144070)
-   [Twitter](http://dev.twitter.com/)

Navigieren Sie zu [https://developers.facebook.com/apps](https://developers.facebook.com/apps/), und melden Sie sich gegebenenfalls an. Klicken Sie auf die Schaltfläche **Register as a Developer**, und schließen Sie den Registrierungsprozess ab. Wenn Sie die Registrierung abgeschlossen haben, klicken Sie auf **Create New App**. Geben Sie einen Namen für die Anwendung ein. Sie müssen keinen Namespace für die Anwendung eingeben.

![Neue FB-Anwendung erstellen](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxFBapp.png)

Geben Sie localhost als **App Domain** und http://localhost/ als **Site URL** ein. Klicken Sie bei **Sandbox Mode** auf **Enabled**, und klicken Sie dann auf **Save Changes**.

Sie benötigen die **Anwendungs-ID** und den **geheimen Anwendungsschlüssel**, um OAuth in dieser Anwendung zu implementieren. ![Neue FB-Anwendung](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxFB.png)

Testbenutzer erstellen
----------------------

Klicken Sie im linken Bereich unter **Settings** auf **Developer Roles**. Klicken Sie auf den Link **Create** in der Spalte **Test Users** (nicht in der Spalte **Testers**).

![FB-Tester](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxFBt.png)

Klicken Sie auf den Link **Modify**, um eine E-Mail für Testbenutzer zu erhalten (mit der Sie sich bei der Anwendung anmelden). Klicken Sie auf den Link **See More**, und klicken Sie dann auf **Edit**, um das Kennwort für den Testbenutzer festzulegen.

Hinzufügen der Anwendungs-ID und des geheimen Schlüssels vom Anbieter
---------------------------------------------------------------------

Öffnen Sie die Datei *App\_Start\\BundleConfig.cs*. Entfernen Sie die Kommentarzeichen aus der *RegisterFacebookClient*-Methode, und fügen Sie die Anwendungs-ID und den geheimen Schlüssel hinzu. Verwenden Sie die Werte, die Sie erhalten haben. Die unten genannten Werte funktionieren nicht. Entfernen Sie die Kommentarzeichen aus dem *OAuthWebSecurity.RegisterGoogleClient*-Aufruf, und fügen Sie *OAuthWebSecurity.RegisterYahooClient* hinzu, wie im Folgenden gezeigt. Bei den Anbietern Google und Yahoo müssen Sie keine Registrierung vornehmen und keine Schlüssel erhalten. Warnung: Halten Sie Ihre Anwendungs-ID und den geheimen Schlüssel geheim. Böswillige Benutzer, die Zugriff auf Ihre Anwendungs-ID und den geheimen Schlüssel erlangen, können vorgeben, die Anwendung zu sein.

     public static void RegisterAuth()
        {
            OAuthWebSecurity.RegisterFacebookClient(
                appId: "numerischen Schlüssel hier eingeben",
                appSecret: "numerischen geheimen Schlüssel hier eingeben");

            OAuthWebSecurity.RegisterGoogleClient();
            OAuthWebSecurity.RegisterYahooClient();
        }

1.  Führen Sie die Anwendung aus, und klicken Sie auf den Link **Anmelden**.
2.  Klicken Sie auf die Schaltfläche **Facebook**.
3.  Geben Sie Ihre Anmeldeinformationen oder die eines Testbenutzers für Facebook ein.
4.  Klicken Sie auf **Okay**, um der Anwendung zu erlauben, auf Ihre Facebook-Ressourcen zuzugreifen.
5.  Sie werden zur Registrierungsseite weitergeleitet. Wenn Sie mit einem Testkonto angemeldet sind, können Sie den **Benutzernamen** in einen kürzeren ändern, beispielsweise "Bill FB Test". Klicken Sie auf die Schaltfläche **Register**, um Benutzernamen und E-Mail-Alias in der Mitgliedschaftsdatenbank zu speichern.
6.  Registrieren Sie einen weiteren Benutzer. Aktuell sorgt ein Fehler im Anmeldesystem dafür, dass Sie sich nicht beim selben Anbieter abmelden und mit einer anderen Benutzeridentität anmelden können. (Das bedeutet, Sie können sich nicht mit Ihrem Facebook-Konto abmelden und sich danach mit einem anderen Facebook-Konto wieder anmelden.) Navigieren Sie mit einem anderen Browser zur Website und registrieren Sie einen weiteren Benutzer. So umgehen Sie das Problem. Ein Benutzer wird der Verwaltungsrolle hinzugefügt und kann die Anwendung bearbeiten. Der andere Benutzer hat nur Zugriff auf Methoden auf der Website, mit denen keine Bearbeitung möglich ist. Anonyme Benutzer haben nur Zugriff auf die Startseite.
7.  Registrieren Sie einen weiteren Benutzer über einen anderen Anbieter.
8.  **Optional**: Sie können auch ein lokales Konto erstellen, das mit keinem der Anbieter verbunden ist. Später in diesem Lernprogramm wird die Möglichkeit zum Erstellen lokaler Konten entfernt. Klicken Sie auf den Link **Abmelden** (wenn Sie angemeldet sind), und klicken Sie dann auf den Link **Registrieren**, um ein lokales Konto zu erstellen. Sie sollten ein lokales Konto, das mit keinem externen Authentifizierungsanbieter verbunden ist, zu Administrationszwecken erstellen.

Mitgliedschaftsdatenbank Hinzufügen von Rollen zur Mitgliedschaftsdatenbank
---------------------------------------------------------------------------

In diesem Abschnitt fügen Sie der Mitgliedschaftsdatenbank die Rolle *canEdit* hinzu. Nur Benutzer mit der Rolle "canEdit" können Daten bearbeiten. Eine bewährte Methode besteht darin, Rollen nach den Aktionen zu benennen, die damit ausgeführt werden können. Entsprechend ist *canEdit* einer Rolle mit dem Namen *admin* vorzuziehen. Mit fortschreitender Fertigstellung der Anwendung können Sie neue Rollen wie *canDeleteMembers* anstelle von Rollen wie *superAdmin* hinzufügen.

1.  Klicken Sie im Menü **Ansicht** auf **Server-Explorer**.

2.  Erweitern Sie in **Server-Explorer** **DefaultConnection**, und erweitern Sie dann **Tabellen**.

3.  Klicken Sie mit der rechten Maustaste auf **Benutzerprofil**, und klicken Sie dann auf **Show Table Data**.

    ![Show Table Data](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSTD.png)

4.  Zeichnen Sie die **Benutzer-ID** für den Benutzer auf, der die Rolle "canEdit" innehaben soll. In der folgenden Abbildung besitzt der Benutzer *ricka* mit der **Benutzer-ID** 2 die Rolle "canEdit" für die Website.

    ![Benutzer-IDs](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxUid.png)

5.  Klicken Sie mit der rechten Maustaste auf **webpages\_Roles**, und klicken Sie dann auf **Show Table Data**.
6.  Geben Sie in die Zelle **RoleName** **canEdit** ein. Die **RoleId** ist 1, wenn Sie jetzt zum ersten Mal eine Rolle hinzugefügt haben. Zeichnen Sie die RoleID auf. Stellen Sie sicher, dass der Name kein Leerzeichen am Ende enthält. "canEdit " in der Rollentabelle entspricht nicht "canEdit" im Controllercode.

    ![roleID](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxRoleID.png)

7.  Klicken Sie mit der rechten Maustaste auf **webpages UsersInRoles**, und klicken Sie dann auf **Show Table Data**. Geben Sie die **Benutzer-ID** für den Benutzer ein, der *canEdit*-Zugriff und die **RoleId** erhalten soll.

    ![Benutzerrolle ID Tabelle](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxUR.png)

Die Tabelle **webpages\_OAuthMembership** enthält den OAuth-Anbieter, dessen Benutzer-ID und die Benutzer-ID für jeden registrierten OAuth-Benutzer. Die Tabelle **webpages-Membership** enthält die ASP.NET-Mitgliedschaftstabelle. Sie können Benutzer über den Link "Registrieren" zu dieser Tabelle hinzufügen. Es wird empfohlen, einen Benutzer mit der *canEdit*-Rolle hinzuzufügen, der nicht mit Facebook oder einem anderen Drittanbieter für Autorisierung verbunden ist. So verfügen Sie immer über *canEdit*-Zugriff, selbst wenn der Authentifizierungsdrittanbieter nicht verfügbar ist Später in diesem Lernprogramm deaktivieren Sie die ASP.NET-Mitgliedschaftsregistrierung.

Schützen der Anwendung durch das Attribut "Authorize"
-----------------------------------------------------

In diesem Abschnitt wenden Sie das [Authorize](http://msdn.microsoft.com/en-us/library/system.web.mvc.authorizeattribute(v=vs.100).aspx)-Attribut an, um den Zugriff auf die Aktionsmethoden einzuschränken. Anonyme Benutzer können nur die Startseite ansehen. Registrierte Benutzer können Kontaktdetails sowie die Seiten "About" und "Contacts" ansehen. Nur Benutzer mit der Rolle *canEdit* können auf Aktionsmethoden zugreifen, mit denen Daten geändert werden.

1.  Fügen Sie der Anwendung die Filter [Authorize](http://msdn.microsoft.com/en-us/library/system.web.mvc.authorizeattribute(v=vs.100).aspx) und [RequireHttps](http://msdn.microsoft.com/en-us/library/system.web.mvc.requirehttpsattribute(v=vs.108).aspx) hinzu. Alternativ können Sie das [Authorize](http://msdn.microsoft.com/en-us/library/system.web.mvc.authorizeattribute(v=vs.100).aspx)-Attribut und das [RequireHttps](http://msdn.microsoft.com/en-us/library/system.web.mvc.requirehttpsattribute(v=vs.108).aspx)-Attribut jedem Controller hinzufügen, es wird jedoch als Best Practice im Bereich Sicherheit betrachtet, die Attribute der gesamten Anwendung hinzuzufügen. Auf diese Weise wird jeder neu hinzugefügte Controller und jede neu hinzugefügte Aktionsmethode automatisch geschützt und Sie müssen nicht daran denken, die Attribute anzuwenden. Weitere Informationen finden Sie unter [Securing your ASP.NET MVC 4 App and the new AllowAnonymous Attribute](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) (in englischer Sprache). Öffnen Sie die Datei *App\_Start\\FilterConfig.cs*, und ersetzen Sie die *RegisterGlobalFilters*-Methode durch den folgenden Code.

         public static void
         RegisterGlobalFilters(GlobalFilterCollection filters)
         {
             filters.Add(new HandleErrorAttribute());
             filters.Add(new System.Web.Mvc.AuthorizeAttribute());
             filters.Add(new RequireHttpsAttribute());
         }

2.  Fügen Sie das Attribut [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) zur **Index**-Methode hinzu. Mit dem [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx)-Attribut lassen Sie die Methoden zu, die von der Autorisierung ausgenommen werden sollen.
3.  Fügen Sie [Authorize(Roles = "canEdit")] zu den Get- und Post-Methoden hinzu, die Daten ändern (Create, Edit, Delete).
4.  Fügen Sie die Methoden *About* und *Contact* hinzu. Im Folgenden wird ein Teil des fertigen Codes angezeigt.

         public class HomeController : Controller
         {
             private ContactManagerContext db = new ContactManagerContext();
             [AllowAnonymous]
             public ActionResult Index()
             {
                 return View(db.Contacts.ToList());
             }

             public ActionResult About()
             {
                 return View();
             }

             public ActionResult Contact()
             {
                 return View();
             }

             [Authorize(Roles = "canEdit")]
             public ActionResult Create()
             {
                 return View();
             }
             // Methoden aus Gründen der Übersichtlichkeit verschoben und weggelassen.
         }

5.  Entfernen Sie die ASP.NET-Mitgliedschaftsregistrierung. Die aktuelle ASP.NET-Mitgliedschaftsregistrierung im Projekt umfasst keine Unterstützung von Kennwortzurücksetzungen und es wird nicht überprüft, ob eine Person sich registriert (z. B. durch [CAPTCHA](http://www.asp.net/web-pages/tutorials/security/16-adding-security-and-membership)). Nachdem sich ein Benutzer über einen der Drittanbieter authentifiziert hat, kann er sich registrieren. Entfernen Sie im Kontocontroller das *[AllowAnonymous]*-Attribut aus den Get- und Post-*Register*-Methoden. Auf diese Weise können sich weder Robots noch anonyme Benutzer registrieren.
6.  Entfernen Sie in der Datei *Views\\Shared\_LoginPartial.cshtml* den Aktionslink für die Registrierung.
7.  Aktivieren Sie SSL. Klicken Sie im Projektmappen-Explorer auf das Projekt **ContactManager**, und drücken Sie dann F4, um das Eigenschaftsdialogfeld aufzurufen. Ändern Sie **SSL-aktiviert** in true. Kopieren Sie die **SSL-URL**.

    ![SSL aktivieren](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSSL.png)

8.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **Contact Manager** und dann auf **Eigenschaften**.
9.  Klicken Sie auf der linken Registerkarte auf **Web**.
10. Passen Sie die **Projekt-URL** so an, dass die **SSL-URL** verwendet wird.
11. Klicken Sie auf **Virtuelles Verzeichnis erstellen**.

    ![SSL aktivieren](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxS2.png)

12. Drücken Sie STRG+F5, um die Anwendung auszuführen. Im Browser wird eine Zertifikatwarnung angezeigt. Bei dieser Anwendung können Sie sicher auf den Link **Laden dieser Website fortsetzen** klicken. Prüfen Sie, ob nur Benutzer mit der *canEdit*-Rolle Daten ändern können. Prüfen Sie, ob anonyme Benutzer nur die Startseite sehen können.

    ![Zertifikatwarnung](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxNOT.png)

    ![Zertifikatwarnung](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxNOT2.png)

Azure-Websites enthalten ein gültiges Sicherheitszertifikat, daher wird diese Warnung bei der Bereitstellung in Azure nicht angezeigt.

Vorbereiten der DatenbankErstellen eines Datenbereitstellungsskripts
--------------------------------------------------------------------

Die Mitgliedschaftsdatenbank wird nicht von Entity Framework Code First verwaltet, daher können Sie keine Migrationen verwenden, um sie bereitzustellen. Verwenden Sie den [dbDacFx](http://msdn.microsoft.com/en-us/library/dd394698.aspx)-Anbieter, um das Datenbankschema bereitzustellen. Konfigurieren Sie außerdem das Veröffentlichungsprofil so, dass ein Skript ausgeführt wird, das erste Mitgliedschaftsdaten in Mitgliedschaftstabellen einträgt

In diesem Lernprogramm wird SQL Server Management Studio (SSMS) zur Erstellung von Datenbereitstellungskripts verwendet.

Installieren Sie SSMS aus dem [Microsoft SQL Server 2012 Express Download Center](http://www.microsoft.com/en-us/download/details.aspx?id=29062):

-   [ENU\\x64\\SQLManagementStudio\_x64\_ENU.exe](http://download.microsoft.com/download/8/D/D/8DD7BDBA-CEF7-4D8E-8C16-D9F69527F909/ENU/x64/SQLManagementStudio_x64_ENU.exe) für 64-Bit-Systeme
-   [ENU\\x86\\SQLManagementStudio\_x86\_ENU.exe](http://download.microsoft.com/download/8/D/D/8DD7BDBA-CEF7-4D8E-8C16-D9F69527F909/ENU/x86/SQLManagementStudio_x86_ENU.exe) für 32-Bit-Systeme

	Wenn Sie die falsche Version für Ihr Betriebssystem ausgewählt haben, schlägt die Installation fehl, und Sie können es mit der anderen Version versuchen.

(Beachten Sie, dass es sich hierbei um einen Download mit 600 Megabyte Umfang handelt. Möglicherweise dauert die Installation lange, und eventuell ist ein Systemneustart des Computers erforderlich.)

Klicken Sie auf der ersten Seite des SQL Server-Installationscenters auf **Neue eigenständige SQL Server-Installation oder Hinzufügen von Features zu einer vorhandenen Installation**, und befolgen Sie die Anweisungen. Übernehmen Sie hierbei die Standardeinstellungen. Die folgende Abbildung enthält den Schritt zur Installation von SSMS.

![SQL-Installation](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSS.png)

### Erstellen des Skripts für die Entwicklungsdatenbank

1.  Führen Sie SSMS aus.
2.  Geben Sie im Dialogfeld **Verbindung mit Server herstellen** *(localdb)\\v11.0* als Servernamen ein, behalten Sie die Einstellung **Windows-Authentifizierung** für **Authentifizierung** bei, und klicken Sie dann auf **Verbinden**. Wenn Sie SQL Express installiert haben, geben Sie **.\\SQLEXPRESS** ein.

    ![Verbindung zum Server-Dialog](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxC2S.png)

3.  Erweitern Sie im Fenster **Object-Explorer** **Datenbanken**. Klicken Sie mit der rechten Maustaste auf **aspnet-ContactManager**. Klicken Sie auf **Tasks**, und klicken Sie dann auf **Skripts generieren**.

    ![Skripts generieren](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxGenScripts.png)

4.  Klicken Sie im Dialogfeld **Skripts generieren und veröffentlichen** auf **Skripterstellungsoptionen festlegen**. Sie können den Schritt **Objekte auswählen** überspringen, da die Standardeinstellung "Skripterstellung für gesamte Datenbank und alle Datenbankobjekte" lautet und das in diesem Fall die korrekte Einstellung ist.

5.  Klicken Sie auf **Erweitert**.

    ![Skripterstellungsoptionen festlegen](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx11.png)

6.  Blättern Sie im Dialogfeld **Erweiterte Skripterstellungsoptionen** nach unten zu **Datentypen, für die ein Skript erstellt wird.** Klicken Sie in der Dropdownliste auf die Option **Data only**. (Siehe Abbildung unterhalb des nächsten Schrittes.)

7.  Ändern Sie das **Skript USE DATABASE** in **False**. USE-Anweisungen sind für die Azure-SQL-Datenbank nicht gültig und für die Bereitstellung für SQL Server Express in der Testumgebung nicht erforderlich.

    ![Skripterstellungsoptionen festlegen](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxAdv.png)

8.  Klicken Sie auf **OK**.
9.  Im Dialogfeld **Skripts generieren und veröffentlichen** legt das Feld **Dateiname** fest, wo das Skript erstellt werden soll. Ändern Sie den Pfad so, dass er zum Projektordner führt (der Ordner, der die Datei *Contacts.sln* enthält), und ändern Sie den Dateinamen zu *aspnet-data-membership.sql*.
10. Klicken Sie auf **Weiter**, um zur Registerkarte **Zusammenfassung** zu gehen, und klicken Sie dann erneut auf **Weiter**, um das Skript zu erstellen.

    ![Speichern oder veröffentlichen](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx1.png)

11. Klicken Sie auf **Fertig stellen**.

Bereitstellen der Anwendung in Azure
------------------------------------

1.  Öffnen Sie die Anwendungs-Stammdatei *Web.config*. Suchen Sie das Markup *DefaultConnection*, kopieren Sie es, und fügen Sie es unter der Markupzeile *DefaultConnection* ein. Benennen Sie das kopierte Element in *DefaultConnectionDeploy* um. Sie benötigen diese Verbindungszeichenfolge, um Benutzerdaten in der Mitgliedschaftsdatenbank bereitstellen zu können. 
    ![3 Zeichenfolgen](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxd.png)

2.  Erstellen Sie die Anwendung.
3.  Klicken Sie im **Projektmappen-Explorer** von Visual Studio mit der rechten Maustaste auf das Projekt, und wählen Sie im Kontextmenü **Veröffentlichen** aus.

    !["Veröffentlichen" im Kontextmenü des Projekts](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-deploy1-publish-001.png)

Der Assistent **Web veröffentlichen** wird geöffnet.

1.  Klicken Sie auf die Registerkarte **Einstellungen**. Klicken Sie auf das Symbol **v**, um für **ContactManagerContext** und **DefaultConnectionDeploy** die **Remoteverbindungszeichenfolge** auszuwählen. Die drei aufgeführten Datenbanken verwenden alle die gleiche Remoteverbindungszeichenfolge. In der Datenbank **ContactManagerContext** werden die Kontakte gespeichert. **DefaultConnectionDeploy** wird nur verwendet, um die Benutzerkontendaten für die Mitgliedschaftsdatenbank bereitzustellen. **UsersContext** ist die Mitgliedschaftsdatenbank.

    ![Einstellungen](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxD2.png)

2.  Wählen Sie unter **ContactManagerContext** die Option **Execute Code First Migrations** aus.

    ![Einstellungen](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSettings.png)

3.  Aktivieren Sie unter **DefaultConnectionDeploy** **Update database**, und klicken Sie dann auf den Link **Configure database updates**.
4.  Klicken Sie auf den Link **Add SQL Script**, und navigieren Sie zur Datei *aspnet-data-membership.sql*. Dies ist nur einmalig erforderlich. Bei der nächsten Bereitstellung deaktivieren Sie **Update database**, da Sie die Benutzerdaten nicht zu den Mitgliedschaftstabellen hinzufügen müssen.

    ![SQL hinzufügen](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxAddSQL2.png)

5.  Klicken Sie auf **Veröffentlichen**.
6.  Navigieren Sie zur Seite [https://developers.facebook.com/apps](https://developers.facebook.com/apps/), und ändern Sie die Einstellungen für **App Domains** und **Site URL** zur Azure-URL.
7.  Testen Sie die Anwendung. Prüfen Sie, ob nur Benutzer mit der *canEdit*-Rolle Daten ändern können. Prüfen Sie, ob anonyme Benutzer nur die Startseite sehen können. Prüfen Sie, ob authentifizierte Benutzer zu allen Links navigieren können, bei denen keine Daten geändert werden können.
8.  Wenn Sie die Anwendung das nächste Mal veröffentlichen, deaktivieren Sie **Update database** unter **DefaultConnectionDeploy**.

    ![Einstellungen](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSettings.png)

Aktualisieren der DatenbankAktualisieren der Mitgliedschaftsdatenbank
---------------------------------------------------------------------

Wenn die Website in Azure bereitgestellt wurde, und Sie über mehr registrierte Benutzer verfügen, sollten Sie einigen Benutzern die *canEdit*-Rolle zuweisen. In diesem Abschnitt verwenden Sie Visual Studio für die Verbindung zur SQL-Datenbank sowie zum Hinzufügen von Benutzern zur *canEdit*-Rolle.

![Einstellungen](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSettings.png)

1.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und klicken Sie dann auf **Veröffentlichen**. ![Veröffentlichen](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxP.png)

2.  Klicken Sie auf die Registerkarte **Einstellungen**.
3.  Kopieren Sie die Verbindungszeichenfolge. Die Verbindungszeichenfolge aus diesem Beispiel lautet beispielsweise: Data Source=tcp:d015leqjqx.database.windows.net,1433; Initial Catalog=ContactDB2;User Id=ricka0@d015lxyze;Password=xyzxyz
4.  Schließen Sie das Dialogfeld "Veröffentlichen".
5.  Klicken Sie im Menü **Ansicht** auf **Server-Explorer**.

6.  Klicken Sie auf das Symbol **Connect to Database**.

    ![Veröffentlichen](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxc.png)

7.  Wenn Sie nach der Datenquelle gefragt werden, klicken Sie auf **Microsoft SQL Server**. 
    ![Veröffentlichen](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx3.png)

8.  Kopieren Sie den **Servernamen**, der mit *tcp* beginnt (siehe Abbildung unten), und fügen Sie ihn ein.
9.  Klicken Sie auf **Use SQL Server Authentication**
10. Geben Sie Ihren **Benutzernamen** und Ihr **Kennwort** ein, die in der kopierten Verbindungszeichenfolge enthalten sind.
11. Geben Sie den Datenbanknamen ein (ContactDB oder die Zeichenfolge nach "Initial Catalog=" in der Datenbank, wenn Sie nicht den Namen ContactDB gewählt haben). Wenn eine Fehlermeldung angezeigt wird, siehe nächster Abschnitt.
12. Klicken Sie auf **Test Connection**. Wenn eine Fehlermeldung angezeigt wird, siehe nächster Abschnitt. 
   ![Dialog Verbindung hinzufügen](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx4.png)

Anmeldefehler "Cannot open server"
----------------------------------

Wenn eine Fehlermeldung angezeigt wird, die mit "Cannot open server" beginnt, müssen Sie Ihre IP-Adresse zu den zulässigen IP-Adressen hinzufügen.

![Firewallfehler](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx5.png)

1.  Wählen Sie im Azure-Portal auf der linken Registerkarte **SQL Databases** aus.

    ![SQL auswählen](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx6.png)

2.  Wählen Sie die Datenbank aus, die Sie öffnen möchten.

3.  Klicken Sie auf den Link **Set up Azure firewall rules for this IP address**.

    ![Firewallregeln](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx7.png)

4.  Wenn die Frage "The current IP address xxx.xxx.xxx.xxx is not included in existing firewall rules. Do you want to update the firewall rules?" angezeigt wird, klicken Sie auf **Ja**. Das Hinzufügen dieser Adresse ist oft nicht ausreichend, Sie müssen einen IP-Adressbereich hinzufügen.

Hinzufügen eines Bereichs zulässiger IP-Adressen
------------------------------------------------

1.  Klicken Sie im Azure-Portal auf **SQL Databases**.
2.  Klicken Sie auf den **Server**, auf dem die Datenbank gehostet wird.

    ![DB-Server](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx8.png)

3.  Klicken Sie oben auf der Seite auf **Konfigurieren**.
4.  Fügen Sie einen Regelnamen sowie die Start- und End-IP-Adresse hinzu. 
    ![IP-Bereich](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx9.png)

5.  Klicken Sie unten auf der Seite auf **Speichern**.
6.  Sie können jetzt mit den zuvor gezeigten Schritten die Mitgliedschaftsdatenbank bearbeiten.

Nächste SchritteNächste Schritte
--------------------------------

Dieses Lernprogramm und die Beispielanwendung wurden von [Rick Anderson](http://blogs.msdn.com/b/rickandy/) (Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT)) sowie Tom Dykstra, Tom FitzMacken und Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart)) geschrieben.

Bitte teilen Sie uns mit, was Ihrer Meinung nach gelungen ist, bzw. verbessert werden kann, und zwar nicht nur bezüglich des Lernprogramms, sondern auch bezüglich der Produkte, die darin vorgestellt werden. Ihr Feedback unterstützt uns dabei, eine entsprechende Priorität für Verbesserungen festzulegen. Wir interessieren uns vor allem dafür, wie groß das Interesse an weiteren Automatisierungen in Bezug auf die Konfiguration und Bereitstellung der Mitgliedsdatenbank ist.

Wie Sie die farbigen Anmeldeschaltflächen für Facebook, Google und Yahoo erhalten, erfahren Sie im Blogeintrag [Customizing External Login Buttons in ASP.NET MVC 4](http://www.beabigrockstar.com/customizing-external-login-buttons-in-asp-net-mvc-4/) (Anpassen von externen Anmeldeschaltflächen in ASP.NET MVC 4, in englischer Sprache). Informationen zur Verwendung der Windows-Authentifizierung finden Sie unter:

-   [Azure-Authentifizierung](http://www.asp.net/vnext/overview/fall-2012-update/windows-azure-authentication)
-   [So erstellen Sie eine Intranetsite mit ASP.NET MVC](http://msdn.microsoft.com/en-us/library/gg703322(v=vs.98).aspx)

Daten können außerdem über den Azure-Speicher in einer Azure-Anwendung gespeichert werden. Dieser Speicher bietet nichtrelationale Datenspeicherung in Form von Blobs und Tabellen. Die folgenden Links bieten weitere Informationen zu ASP.NET MVC und Azure.

-   [.NET-Anwendungen mit mehreren Ebenen mithilfe von Speichertabellen, Warteschlangen und Blobs](http://www.windowsazure.com/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/)
-   [Einleitung zu ASP.NET MVC 4](http://www.asp.net/mvc/tutorials/mvc-4/getting-started-with-aspnet-mvc4/intro-to-aspnet-mvc-4)
-   [Erste Schritte in Entity Framework mit MVC](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application)
-   [OAuth 2.0 and Sign-In (OAuth 2.0 und Anmeldung, in englischer Sprache)](http://blogs.msdn.com/b/vbertocci/archive/2013/01/02/oauth-2-0-and-sign-in.aspx)

Sie haben erfahren, wie eine einfache Webanwendung auf einer Azure-Website bereitgestellt wird. Weitere Informationen zur Konfiguration, Verwaltung und Skalierung von Azure-Websites finden Sie auf der Seite [Common Tasks](http://www.windowsazure.com/en-us/develop/net/common-tasks/) in den "Vorgehensweise"-Themen.

Informationen zum Debuggen von Azure-Websites finden Sie unter [Problembehandlung von Azure-Websites in Visual Studio](/en-us/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/).

Informationen zum Bereitstellen einer Anwendung für einen Azure-Clouddienst finden Sie in [der Clouddienstversion dieses Lernprogramms](http://www.windowsazure.com/en-us/develop/net/tutorials/cloud-service-with-sql-database/) sowie unter [Entwickeln von Webanwendungen mit Azure](http://msdn.microsoft.com/en-us/library/Hh674484). Gründe, weshalb Sie die ASP.NET-Webanwendung in einem Azure-Clouddienst statt über eine Azure-Website ausführen sollten, sind unter anderem:

-   Sie benötigen Administratorberechtigungen auf dem Webserver, auf dem die Anwendung ausgeführt wird.
-   Sie möchten auf den Webserver, auf dem die Anwendung ausgeführt wird, über eine Remotedesktopverbindung zugreifen.
-   Ihre Anwendung ist mehrschichtig, und Sie möchten die Arbeitslast auf mehrere virtuelle Server (Web und Worker) verteilen.

Weitere Informationen zu SQL-Datenbank und Azure-Speicher finden Sie unter [Data Storage Offerings on Azure](http://social.technet.microsoft.com/wiki/contents/articles/data-storage-offerings-on-the-windows-azure-platform.aspx) (Datenspeicherangebote in Windows Azure, in englischer Sprache).

Weitere Informationen zur Verwendung von SQL-Datenbank finden Sie unter [Arbeiten mit Windows Azure SQL-Datenbank](http://go.microsoft.com/fwlink/p/?LinkId=282414#ssdb).

Weitere Informationen zu Entity Framework und Code First-Migrationen finden Sie in den folgenden Ressourcen:

-   [Erste Schritte in Entity Framework mit MVC](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application)
-   [Code First Migrations (Code First-Migrationen, in englischer Sprache)](http://msdn.microsoft.com/en-us/library/hh770484)

