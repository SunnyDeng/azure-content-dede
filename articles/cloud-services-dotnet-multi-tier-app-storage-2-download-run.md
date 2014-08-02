<properties linkid="develop-net-tutorials-multi-tier-web-site-2-download-and-run" urlDisplayName="Step 2: Download and Run" pageTitle="ASP.NET Multi-tier Web Application with Azure - Step 2: Download and run" metaKeywords="Azure tutorial, deploying email service app, publishing email service" description="The second tutorial in a series that teaches how to configure your computer for Azure development and deploy the Email Service app." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Configuring and Deploying the Azure Email Service application - 2 of 5" authors="riande,tdykstra" solutions="" manager="wpickett" editor="mollybos" />

Konfigurieren und Bereitstellen der Azure-E-Mail-Dienstanwendung - 2 von 5
==========================================================================

Dies ist das zweite von fünf Lernprogrammen, in denen die Erstellung und Bereitstellung der Azure-E-Mail-Dienstanwendung beschrieben wird. Weitere Informationen zur Anwendung und den Lernprogrammen finden Sie im [ersten Lernprogramm der Serie](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/).

Dieses Lernprogramm zeigt, wie Sie Ihren Computer für die Azure-Entwicklung konfigurieren und die Azure-E-Mail-Dienstanwendung mit einem der folgenden Produkte in einem Azure-Cloud-Dienst bereitstellen können:

-   Visual Studio 2012
-   Visual Studio 2012 Express für das Web
-   Visual Studio 2010
-   Visual Web Developer Express 2010.

> [WACOM.NOTE] Nach der Erstellung dieses Lernprogramms wurde Visual Studio 2013 veröffentlicht, und Azure-Verwaltungsportal und SDK wurden aktualisiert. Sie finden diese Art von Hinweisen an Punkten, in denen sich die Anweisungen für Visual Studio 2013 und das neueste SDK unterscheiden. Diese Hinweise wurden im März 2014 geschrieben, und die geänderten Prozeduren wurden mit der Version 2.3 des SDK getestet.

Sie können ein kostenloses Azure-Konto erstellen. Wenn Sie noch nicht über Visual Studio 2013 verfügen, wird Visual Studio 2013 für das Web Express automatisch durch das SDK installiert. Auf diese Weise können Sie völlig kostenlos mit der Entwicklung für Azure beginnen.

In diesem Lernprogramm lernen Sie Folgendes:

-   Ermöglichen der Azure-Entwicklung auf Ihrem Computer durch Installieren des Azure SDK.
-   Konfigurieren und Testen der Azure-E-Mail-Dienstanwendung auf Ihrem lokalen Computer.
-   Veröffentlichen der Anwendung in Azure.
-   Anzeigen und bearbeiten von Tabellen, Warteschlangen und Blobs in Azure mithilfe von Visual Studio oder des Azure-Speicher-Explorers.
-   Konfigurieren von Ablaufverfolgung und Anzeigen der Ablaufverfolgungsdaten.
-   Skalieren der Anwendung durch Einsatz zusätzlicher Workerrolleninstanzen.

<div class="dev-callout"><strong>Hinweis</strong></div>

Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. Sie können ein [kostenloses Testkonto](/en-us/pricing/free-trial/) erstellen oder [Ihre MSDN-Abonnementenvorteile aktivieren](/en-us/pricing/member-offers/msdn-benefits/).
<br />


### Teile des Lernprogramms

-   [Einrichten der Entwicklungsumgebung](#setupdevenv)
-   [Einrichten eines kostenlosen Azure-Kontos](#setupwindowsazure)
-   [Erstellen eines Azure-Speicherkontos](#createWASA)
-   [Installieren des Azure-Speicher-Explorers](#installASE)
-   [Erstellen von Cloud-Diensten](#createcloudsvc)
-   [Herunterladen und Ausführen der abgeschlossenen Lösung](#downloadcnfg)
-   [Anzeigen des Entwicklerspeichers in Visual Studio](#StorageExpVS)
-   [Konfigurieren der Anwendung für den Azure-Speicher](#conf4azureStorage)
-   [Bereitstellen der Anwendung in Azure](#deployAz)
-   [Überführen der Anwendung von Staging zu Produktion](#swap)
-   [Konfigurieren der Anwendung für SendGrid](#sendGrid)
-   [Konfigurieren von Ablaufverfolgung und Anzeigen der Ablaufverfolgungsdaten](#trace)
-   [Hinzufügen einer weiteren Workerrolle für zusätzliche Lastanforderungen](#addRole)

> [WACOM.NOTE] Der korrekte Link für die Installation des SDK im folgenden Abschnitt ist <http://go.microsoft.com/fwlink/?LinkID=324322>, falls Sie Visual Studio 2013 verwenden.

[WACOM.INCLUDE [install-sdk-2012-only](../includes/install-sdk-2012-only.md)]

Erstellen eines SpeicherkontosErstellen eines Azure-Speicherkontos
------------------------------------------------------------------

Bei der Ausführung der Beispielanwendung in Visual Studio haben Sie Zugriff auf Tabellen, Warteschlangen und Blobs im Azure-Entwicklungsspeicher oder in einem Azure-Speicherkonto in der Cloud. Der Entwicklungsspeicher verwendet eine lokale SQL Server Express LocalDB-Datenbank, um die Funktionsweise des Azure-Speichers in der Cloud zu emulieren. In diesem Lernprogramm verwenden Sie zunächst den Entwicklungsspeicher und lernen anschließend, wie Sie die Anwendung in Visual Studio für die Verwendung eines Cloud-Speicherkontos konfigurieren können. In diesem Abschnitt des Lernprogramms erstellen Sie das Azure-Speicherkonto, das Sie später im Lernprogramm in Visual Studio konfigurieren.

1.  Öffnen Sie das [Azure Verwaltungsportal](http://manage.windowsazure.com) in Ihrem Browser.

2.  Klicken Sie im [Azure-Verwaltungsportal](http://manage.windowsazure.com) auf **Speicher** und dann auf **Neu**.

    ![Neuer Speicher](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-portal-new-storage.png)

3.  Klicken Sie auf **Schnellerfassung**.

    ![Schnellerfassung](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-storage-quick.png)

4.  Geben Sie ein URL-Präfix in das URL-Eingabefeld ein.

    Dieses Präfix zusammen mit dem Text unterhalb des Eingabefelds ergibt die eindeutige URL Ihres Speicherkontos. Falls das eingegebene Präfix bereits von einer anderen Person verwendet wird, erhalten Sie die Meldung "Der Speichername ist bereits vergeben" über dem Textfeld und müssen ein anderes Präfix auswählen.

5.  Wählen Sie die Region aus, in der Sie Ihre Anwendung bereitstellen möchten.

6.  Wählen Sie in der Dropdownliste **Replikation** den Wert **Lokal redundant** aus.

    Wenn Georeplikation für ein Speicherkonto aktiviert ist, werden dessen Inhalte an einen zweiten Ort repliziert, um im Katastrophenfall eine Failover-Instanz an diesem zweiten Standort zur Verfügung zu haben. Für die Georeplikation können zusätzliche Kosten anfallen. Für Test- und Entwicklungskonten macht es wenig Sinn, für Georeplikation zu bezahlen. Weitere Informationen finden Sie unter [How to Manage Storage Accounts](/en-us/manage/services/storage/how-to-manage-a-storage-account/) (Verwalten von Speicherkonten, in englischer Sprache).

7.  Klicken Sie auf **Speicherkonto erstellen**.

    In der folgenden Abbildung wird ein Speicherkonto mit der URL `aestest3.core.windows.net` erstellt.

    ![Erstellen des Speichers mit URL-Präfix](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create-storage-url-test.png)

    Dieser Schritt kann mehrere Minuten in Anspruch nehmen. In der Wartezeit können Sie diese Schritte wiederholen und ein Produktions-Speicherkonto erstellen. Es macht Sinn, ein Test-Speicherkonto für die lokale Entwicklung, ein weiteres Testkonto für Tests in Azure und ein Produktions-Speicherkonto zu verwenden.

8.  Klicken Sie auf das zuvor erstellte Testkonto, und klicken Sie auf das Symbol **Zugriffsschlüssel verwalten**.

    ![Schlüssel verwalten](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-manage-keys.png)

    ![Schlüssel-GUID](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-guid-keys.PNG)

    Sie benötigen den **primären Zugriffsschlüssel** oder den **sekundären Zugriffsschlüssel** im gesamten Verlauf dieses Lernprogramms. Beide Schlüssel können in Speicher-Verbindungszeichenfolgen verwendet werden.

    Sie können Ihren verwendeten Schlüssel periodisch wechseln, ohne eine Dienstunterbrechung in Ihrer Live-Anwendung zu verursachen. Dazu generieren Sie den nicht verwendeten Schlüssel neu und können anschließend die Verbindungszeichenfolge in Ihrer Anwendung ändern, sodass diese den neu generierten Schlüssel verwendet. Mit nur einem Schlüssel würde die Anwendung ihre Verbindung zum Speicherkonto verlieren, wenn Sie den Schlüssel erneut generieren. Die in der Abbildung gezeigten Schlüssel sind nicht mehr gültig, da sie nach der Erstellung der Abbildung erneut generiert wurden.

9.  Kopieren Sie einen dieser Schlüssel für den nächsten Abschnitt in die Zwischenablage.

Installieren des ASEInstallieren des Azure-Speicher-Explorers
-------------------------------------------------------------

Mit dem **Azure-Speicher-Explorer** (ASE) können Sie Azure-Speichertabellen, Warteschlangen und Blobs abfragen und aktualisieren. Sie werden den ASE in diesem Lernprogramm verwenden, um zu prüfen, ob Ihre Daten korrekt aktualisiert wurden und um Testdaten zu erstellen.

1.  Installieren des [Azure-Speicher-Explorers](http://azurestorageexplorer.codeplex.com/).

    Die Screenshots zeigen ASE 4, Sie können jedoch auch ASE 5 verwenden.

2.  Starten Sie den **Azure-Speicher-Explorer** und klicken Sie auf **Konto hinzufügen**.

    ![ASE-Konto hinzufügen](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-ase-add.png)

3.  Geben Sie den Namen des Testkontos ein und fügen Sie den zuvor kopierten Schlüssel ein.

4.  Klicken Sie auf **Speicherkonto hinzufügen**.

    ![ASE-Konto hinzufügen](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-ase-add2.png)

Für die Arbeit mit Azure-Speicher stehen noch andere Tools zur Verfügung. Weitere Informationen finden Sie unter [Azure Storage Explorers (2014)](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx) (Azure-Speicher-Explorer (2014), in englischer Sprache).

Erstellen eines Cloud-DienstesErstellen eines Cloud-Dienstes
------------------------------------------------------------

1.  Öffnen Sie das [Azure Verwaltungsportal](http://manage.windowsazure.com) in Ihrem Browser.

2.  Klicken Sie auf **Cloud-Dienste** und anschließend auf das Symbol **Neu**.

    ![Schnellerstellung Cloud](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-new-cloud.png)

3.  Klicken Sie auf **Schnellerfassung**.

4.  Geben Sie ein URL-Präfix in das URL-Eingabefeld ein.

    Analog zur Speicher-URL muss auch diese URL eindeutig sein, und Sie erhalten eine Fehlermeldung, falls das eingegebene Präfix bereits von einer anderen Person verwendet wird.

5.  Wählen Sie die Region aus, in der Sie Ihre Anwendung bereitstellen möchten.

    Sie sollten den Cloud-Dienst in derselben Region erstellen wie Ihr Speicherkonto. Wenn sich Cloud-Dienst und Speicherkonto in unterschiedlichen Datencentern (d. h. unterschiedlichen Regionen) befinden, steigt die Latenz an und Sie müssen für die Bandbreite außerhalb des Datencenters bezahlen. Die Bandbreite innerhalb eines Datencenters ist kostenlos.

    Mit Azure-Affinitätsgruppen können Sie die Distanz zwischen Ressourcen in einem Datencenter und somit die Latenz minimieren. Dieses Lernprogramm verwendet keine Affinitätsgruppen. Anweisungen finden Sie unter [Erstellen einer Affinitätsgruppe in Azure](http://msdn.microsoft.com/en-us/library/jj156209.aspx).

6.  Klicken Sie auf **Cloud-Dienst erstellen**.

    In der folgenden Abbildung wird ein Cloud-Dienst mit der URL aescloud.cloudapp.net erstellt.

    ![Erstellen des Speichers mit URL-Präfix](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create-cloud.png)

    Sie können mit dem nächsten Schritt fortfahren, ohne auf den Abschluss dieses Schritts zu warten.

Herunterladen und AusführenHerunterladen und Ausführen der abgeschlossenen Lösung
---------------------------------------------------------------------------------

1.  Laden Sie die [abgeschlossene Lösung](http://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36) herunter und entzippen Sie das Archiv.

2.  Starten Sie Visual Studio mit erhöhten Rechten.

    Der Serveremulator, mit dem sich Azure-Projekte lokal ausführen lassen, benötigt erhöhte Rechte.

    > [WACOM.NOTE] Mit SDK 2.3 und später benötigt der Azure-Serveremulator keine erhöhten Rechte mehr. Das Projekt ist allerdings immer noch für den Original-Serveremulator konfiguriert und benötigt daher erhöhte Rechte.

3.  Wiederherstellen der NuGet-Pakete.

    Die abgeschlossene Lösung enthält keine Assemblys oder andere Inhalte für die installierten NuGet-Pakete, um die Downloadgröße möglichst klein zu halten. Beim Öffnen und Erstellen der Lösung lädt NuGet die kompletten Paketinhalte automatisch herunter. Dazu müssen Sie allerdings die NuGet-Option zum Wiederherstellen von Paketen in Visual Studio aktivieren. Führen Sie die folgenden Schritte aus, falls Sie diese NuGet-Option noch nicht aktiviert haben.

    > [WACOM.NOTE] In Visual Studio 2013 ist die automatische Paketwiederherstellung standardmäßig aktiviert. Da Sie jedoch ein Visual Studio 2012-Projekt öffnen, erfolgt die Wiederherstellung nicht automatisch. Öffnen Sie zunächst die Lösung, und öffnen Sie anschließend das Dialogfeld **Manage NuGet Packages** wie unten beschrieben. Oben rechts sehen Sie die Schaltfläche **Restore**: Klicken Sie auf diese Schaltfläche, um die Pakete wiederherzustellen.

         1. Klicken Sie im Menü **Tools** auf **Library Package Manager** und anschließend auf **Manage NuGet Packages for Solution**. 

         2. Klicken Sie in der unteren linken Ecke des Dialogfelds **Manage NuGet Packages** auf **Settings**.

         3. Wählen Sie im linken Bereich des Dialogfelds **Options** die Option **General** unter **Package Manager** aus.

         4. Wählen Sie **Allow NuGet to download missing packages during build** aus.

    ![Aktivieren der NuGet-Paketwiederherstellung](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/NuGetPkgRestore.png)

4.  Wählen Sie im Menü **Datei** die Option **Projekt öffnen**, navigieren Sie zu dem Ort, an dem Sie die Lösung gespeichert haben und öffnen Sie die Lösungsdatei.

5.  Vergewissern Sie sich im **Projektmappen-Explorer**, dass **AzureEmailService** als Startprojekt ausgewählt ist.

6.  Drücken Sie STRG+F5, um die Anwendung auszuführen.

    > [WACOM.NOTE] Sie müssen Verweise zu den aktuellen SDK-Assemblys hinzufügen, um die Lösung erstellen zu können. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das MvcWebRole-Projekt und anschließend auf **Hinzufügen** -- **Verweis**. Klicken Sie unter **Assemblys** auf **Erweiterungen**. Wählen Sie *Microsoft.WindowsAzure.Diagnostics* und *Microsoft.WindowsAzure.ServiceRuntime* aus, und klicken Sie auf **OK**. Wiederholen Sie den Vorgang für die Projekte WorkerRoleA und WorkerRoleB.

    Die Startseite der Anwendung wird in Ihrem Browser angezeigt.

    ![Ausführen der Anwendung.](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-mailinglist1.png)

7.  Klicken Sie auf **Neu erstellen**.

8.  Geben Sie einige Testdaten ein und klicken Sie auf **Create**.

    ![Ausführen der Anwendung.](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create1.png)

9.  Erstellen Sie einige Mailinglisten-Einträge.

    ![Indexseite Adressenliste](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-mailing-list-index-page.png)

10. Klicken Sie auf **Subscribers** und fügen Sie einige Abonnenten hinzu. Setzen Sie den Wert von **Verified** auf `wahr`.

    ![Indexseite Empfänger](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-subscribers-index-page.png)

11. Bereiten Sie den Nachrichtenversand vor, indem Sie eine *.txt*-Datei mit dem Text einer zu verschickenden E-Mail erstellen. Erstellen Sie anschließend eine *.htm* mit demselben Text und zusätzlichem HTML-Code (Markieren Sie z. B. einige Worte als fett oder kursiv). Sie werden diese Dateien im nächsten Schritt verwenden.

12. Klicken Sie auf **Messages** und fügen Sie Nachrichten hinzu. Wählen Sie die im vorherigen Schritt erstellten Dateien aus. Der Standardwert für das Versanddatum ist eine Woche in der Zukunft. Ändern Sie dieses Datum nicht. Die Anwendung kann erst dann Nachrichten verschicken, wenn Sie SendGrid konfiguriert haben.

   ![Erstellungsseite für Nachrichten](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-message-create-page.png)
	<br/><br/>
   ![Indexseite für Nachrichten](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-message-index-page.png)

Die Daten, die Sie eingegeben und angezeigt haben, werden im Azure-Entwicklungsspeicher gespeichert. Der Entwicklungsspeicher verwendet eine lokale SQL Server Express LocalDB-Datenbank, um die Funktionsweise des Azure-Speichers in der Cloud zu emulieren. Die Anwendung verwendet den Entwicklungsspeicher, da das Projekt beim Herunterladen so vorkonfiguriert war. Diese Einstellung befindet sich in den *.cscfg*-Dateien im **AzureEmailService**-Projekt. Die *ServiceConfiguration.Local.cscfg*-Datei enthält Einstellungen für die lokale Ausführung in Visual Studio, und die *ServiceConfiguration.Cloud.cscfg*-Datei enthält Einstellungen für die Bereitstellung der Anwendung in der Cloud. Später werden Sie lernen, wie Sie die Anwendung für Ihr zuvor erstelltes Azure-Speicherkonto konfigurieren können.

EntwicklungsspeicherAnzeigen des Entwicklungsspeichers in Visual Studio
-----------------------------------------------------------------------

Der **Azure-Speicher-Browser** im **Server-Explorer** bietet eine praktische schreibgeschützte Ansicht von Azure-Speicherressourcen.

1.  Öffnen Sie das Menü **Ansicht** in Visual Studio und klicken Sie auf **Server-Explorer**.

2.  Erweitern Sie den Knoten **(Entwicklung)** unterhalb des Knotens **Azure-Speicher**.

    > [WACOM.NOTE] Mit dem aktuellen SDK müssen Sie **Windows Azure** / **Speicher** / **(Entwicklung)** erweitern.

3.  Erweitern Sie **Tabellen**, um die in den vorherigen Schritten erstellten Tabellen anzuzeigen.

    ![Server-Explorer](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-serverExplorer.png)

4.  Doppelklicken Sie auf die Tabelle **MailingList**.

    ![VS-Speicher-Explorer](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-wasVSdata.png)

    Beachten Sie, dass das Fenster die verschiedenen Schemata der Tabelle anzeigt. `MailingList`-Entitäten haben die Eigenschaften `Description` und `FromEmailAddress`, und `Subscriber`-Entitäten haben die Eigenschaft `Verified` (plus `SubscriberGUID`, dieser Wert wird allerdings nicht angezeigt, da das Fenster nicht groß genug ist). Die Tabelle enthält Spalten für all diese Eigenschaften, und wenn die Entität in einer gegebenen Tabellenzeile keine solche Eigenschaft hat, dann ist das entsprechende Feld leer.

Mit dem Speicher-Browser in Visual Studio können Sie keine Azure-Speicherressourcen aktualisieren oder löschen. Mit dem [Azure-Speicher-Explorer](http://azurestorageexplorer.codeplex.com/) können Sie Entwicklungs-Speicherressourcen aktualisieren oder löschen. (Klicken Sie auf das Kontrollkästchen **Entwicklungsspeicher** im Dialogfeld **Speicherkonto hinzufügen**, um einen Entwicklungsspeicher im Azure-Speicher-Explorer zu verwenden.)

> [WACOM.NOTE] Mit dem neuesten SDK können Sie im **Server Explorer** auch Entwicklungsspeicher aktualisieren.

Verwenden Ihres SpeicherkontosKonfigurieren Sie die Anwendung, um Ihr Azure-Speicherkonto zu verwenden
------------------------------------------------------------------------------------------------------

Sie werden nun die Anwendung konfigurieren, sodass diese bei der Ausführung in Visual Studio Ihr Azure-Speicherkonto anstatt des Entwicklungsspeichers verwendet. Seit der Version 1.8 des SDK gibt es hierfür eine neue Methode. Bei der älteren Methode müssen Sie Einstellungen aus dem Azure-Verwaltungsportal kopieren und einfügen. Die folgenden Schritte beschreiben den neueren Weg zum Konfigurieren der Einstellungen für das Azure-Speicherkonto.

1.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **MvcWebRole** unter **Rollen** im Projekt **AzureEmailService**, und klicken Sie auf **Eigenschaften**.

    ![Rechtsklick Eigenschaften](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-rt-prop.png)

2.  Klicken Sie auf die Registerkarte **Einstellungen**. Wählen Sie in der Dropdownliste **Dienstkonfiguration** den Wert **Lokal** aus.

3.  Wenn Sie den Eintrag **StorageConnectionString** auswählen, sehen Sie eine elliptische Schaltfläche (**...**) am Ende der Zeile. Klicken Sie auf die elliptische Schaltfläche, um das Dialogfeld **Verbindungszeichenfolge für Speicherkonto** zu öffnen.

    ![Rechtsklick Eigenschaften](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-elip.png)

4.  Klicken Sie im Dialogfeld **erbindungszeichenfolge für Speicherkonto** auf **Ihr Abonnement** und anschließend auf **Veröffentlichungseinstellungen herunterladen**.

    > [WACOM.NOTE] Mit dem neuesten SDK können Sie sich in diesem Dialogfeld an Azure anmelden und anschließend das gewünschte Speicherkonto auswählen.

    ![Rechtsklick Eigenschaften](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-enter.png)

    Visual Studio startet eine neue Instanz Ihres Standardbrowsers mit der Seite zum Herunterladen von Veröffentlichungseinstellungen im Azure-Portal. Falls Sie nicht am Portal angemeldet sind, werden Sie zur Anmeldung aufgefordert. Nach der Anmeldung können Sie die Veröffentlichungseinstellungen mit Ihrem Browser speichern. Notieren Sie sich den Speicherort der Datei.

   ![Veröffentlichungseinstellungen](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-3.png)

5.  Klicken Sie im Dialog **Verbindungszeichenfolge für Speicherkonto** auf **Importieren** und navigieren Sie zu den Veröffentlichungseinstellungen, die Sie im vorherigen Schritt gespeichert haben.

6.  Wählen Sie Abonnement und Speicherkonto aus und klicken Sie auf **OK**.

    ![Speicherkonto auswählen](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-5.png)

7.  Wiederholen Sie die Prozedur, mit der Sie die `StorageConnectionString`-Verbindungszeichenfolge gesetzt haben, für die `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`-Verbindungszeichenfolge.

    Sie müssen die Einstellungsveröffentlichungsdatei nicht erneut herunterladen. Wenn Sie auf die Ellipse für die `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`-Verbindungszeichenfolge klicken, werden Sie feststellen, dass sich das Dialogfeld **Verbindungszeichenfolge für Speicherkonto** Ihre Abonnementdaten gemerkt hat. Wenn Sie auf das Optionsfeld **Ihr Abonnement** klicken, brauchen Sie nur noch die bereits zuvor ausgewählten Werte für **Abonnement** und **Kontoname** auswählen und auf **OK** klicken.

8.  Wiederholen Sie die Prozedur, mit der Sie die beiden Verbindungszeichenfolgen für die MvcWebRole-Rolle gesetzt haben, um die Verbindungszeichenfolgen für Workerrolle A und Workerrolle B zu setzen.

### Manuelle Methode zum Konfigurieren der Speicherkonto-Anmeldeinformationen

Die folgende Prozedur beschreibt den manuellen Weg zum Konfigurieren der Einstellungen für das Azure-Speicherkonto. Falls Sie die zuvor beschriebene automatische Methode verwendet haben, können Sie diese Prozedur überspringen. Sie können diese Prozedur natürlich auch lesen, um zu erfahren, was bei der automatischen Methode hinter den Kulissen geschehen ist.

1.  Öffnen Sie das [Azure Verwaltungsportal](http://manage.windowsazure.com) in Ihrem Browser.

2.  Klicken Sie in der Registerkarte **Speicher** auf das zuvor erstellte Testkonto und klicken Sie auf das Symbol **Zugriffsschlüssel verwalten**.

   ![Schlüssel verwalten](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-manage-keys.png)

   ![Schlüssel-GUID](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-guid-keys.PNG)

3.  Kopieren Sie den primären oder den sekundären Zugriffsschlüssel.

4.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **MvcWebRole** unter **Rollen** im Projekt **AzureEmailService**, und klicken Sie auf **Eigenschaften**.

   ![Rechtsklick Eigenschaften](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-rt-prop.png)

5.  Klicken Sie auf die Registerkarte **Einstellungen**. Wählen Sie in der Dropdownliste **Dienstkonfiguration** den Wert **Lokal** aus.

6.  Wenn Sie den Eintrag **StorageConnectionString** auswählen, sehen Sie eine elliptische Schaltfläche (**...**) am Ende der Zeile. Klicken Sie auf die elliptische Schaltfläche, um das Dialogfeld **Verbindungszeichenfolge für Speicherkonto** zu öffnen.

   ![Rechtsklick Eigenschaften](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-elip.png)

7.  Wählen Sie im Dialogfeld **Verbindungszeichenfolge für Speicherkonto** das Optionsfeld **Manuell eingegebene Anmeldeinformationen** aus. Geben Sie den Namen Ihres Speicherkontos und den im vorherigen Schritt kopierten primären bzw. sekundären Zugriffsschlüssel ein.

8.  Klicken Sie auf **OK**.

Sie können die Einstellungen für die Workerrollen entweder mit derselben Prozedur konfigurieren, oder alternativ die Konfigurationsdatei bearbeiten, um die Einstellungen der Webrolle an die Workerrollen weiterzugeben. In den folgenden Schritten lernen Sie, wie Sie die Konfigurationsdatei bearbeiten können. (Dies ist immer noch Teil der manuellen Methode zum Einstellen der Speicher-Anmeldeinformationen und kann übersprungen werden, falls Sie die Einstellungen mithilfe der automatischen Methode an die Workerrollen weitergegeben haben.)

1.  Öffnen Sie die Datei **ServiceConfiguration.Local.cscfg** im **AzureEmailService**-Projekt.

    Im Element `Role` für `MvcWebRole` sehen Sie ein `ConfigurationSettings`-Element mit den Einstellungen, die Sie in der Visual Studio-Oberfläche vorgenommen haben.

           <Role name="MvcWebRole">
             <Instances count="1" />
             <ConfigurationSettings>
               <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[Key]" />
		       <Setting name="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=aestest;AccountKey=[Key]" />
             </ConfigurationSettings>
           </Role>

    Die `Role`-Elemente für die beiden Workerrollen enthalten jeweils dieselbe Verbindungszeichenfolge.

2.  Löschen Sie die `Setting`-Elemente für diese beiden Verbindungszeichenfolgen aus den Elementen `WorkerRoleA` und `WorkerRoleB`, und fügen Sie stattdessen die `Setting`-Elements aus dem `MvcWebRole`-Element ein.

Weitere Informationen zu den Konfigurationsdateien finden Sie unter [Konfigurieren eines Azure-Projekts](http://msdn.microsoft.com/en-us/library/windowsazure/ee405486.aspx).

### Testen Sie die nun zur Verwendung Ihres Speicherkontos konfigurierte Anwendung

1.  Drücken Sie STRG+F5, um die Anwendung auszuführen. Klicken Sie auf die Links **Mailing Lists**, **Subscribers** und **Messages** und geben Sie wie bereits zuvor einige Daten ein.

Sie können nun entweder **Azure-Speicher-Explorer** oder **Server-Explorer** verwenden, um die von der Anwendung in Azure-Tabellen gespeicherten Daten anzuzeigen.

### Verwenden des Azure-Speicher-Explorers, um die Daten in Ihrem Speicherkonto anzuzeigen

1.  Öffnen Sie den **Azure-Speicher-Explorer**.

2.  Wählen Sie das Speicherkonto aus, dessen Anmeldeinformationen Sie zuvor eingegeben haben.

3.  Wählen Sie **Tabellen** unter **Speichertyp** aus.

4.  Wählen Sie die Tabelle `MailingList` aus und klicken Sie auf **Abfrage**, um die von Ihnen auf den Seiten **Mailing List** und **Subscriber** der Anwendung eingegebenen Daten anzuzeigen.

   ![ASE](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-ase1.png)<br/>

### Verwenden des Server-Explorers, um die Daten in Ihrem Speicherkonto anzuzeigen

1.  Klicken Sie im **Server-Explorer** (oder **Datenbank-Explorer**) mit der rechten Maustaste auf **Azure-Seoicher**, und klicken Sie auf **Neues Speicherkonto hinzufügen**.

2.  Führen Sie dieselben Schritte wie zuvor beim Einrichten der Anmeldeinformationen für Ihr Speicherkonto aus.

3.  Erweitern Sie den neuen Knoten unter **Azure-Speicher**, um die in Ihrem Azure-Speicherkonto gespeicherten Daten anzuzeigen.

    ![ASE](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-se3.png)

### Optionale Schritte, um den automatischen Start des Azure-Speicheremulators zu deaktivieren

Falls Sie den Azure-Speicheremulator nicht verwenden, können Sie den Projektstart beschleunigen und weniger lokale Ressourcen verwenden, indem Sie den automatischen Start für den Azure-Speicheremulator deaktivieren.

1.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Cloud-Projekt **AzureEmailService** und wählen Sie **Eigenschaften** aus.

    ![Auswählen der Cloud-Projekt-Eigenschaften](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-aesp.png)

2.  Öffnen Sie die Registerkarte **Entwicklung**.

3.  Setzen Sie den Wert für **Azure-Speicheremulator starten** auf **Falsch**.

    ![Deaktivieren des automatischen Starts des Azure-Speicheremulators](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-1.png)

    **Hinweis**: Deaktivieren Sie diese Option nur, wenn Sie den Speicheremulator nicht verwenden.

    In diesem Fenster können Sie auch die **Service Configuration**-Datei, die bei der lokalen Ausführung der Anwendung verwendet wird, von **Lokal** in**Cloud** ändern (von *ServiceConfiguration.Local.cscfg* in *ServiceConfiguration.Cloud.cscfg*).

4.  Klicken Sie in der Windows-Taskleiste mit der rechten Maustaste auf das Speicheremulatorsymbol und klicken Sie auf **Speicheremulator herunterfahren**.

    ![ASE](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-se4.png)

SendGridKonfigurieren der Anwendung für SendGrid
------------------------------------------------

Diese Beispielanwendung verwendet SendGrid für den E-Mail-Versand. Um E-Mails mit SendGrid zu verschicken, müssen Sie zunächst ein SendGrid-Konto einrichten und anschließend eine Konfigurationsdatei mit Ihren SendGrid-Anmeldeinformationen aktualisieren.

 **Hinweis:** Falls Sie SendGrid nicht verwenden können oder möchten, können Sie stattdessen Ihren eigenen E-Mail-Dienst verwenden. Der Code für die Verwendung von SendGrid ist isoliert in zwei Methoden von Workerrolle B. In [Lernprogramm 5][tut5] erfahren Sie, welche Änderungen Sie für andere E-Mail-Versandmethoden vornehmen müssen. In diesem Fall können Sie diese Prozedur überspringen und mit diesem Lernprogramm fortfahren. Alle sonstigen Teile der Anwendung (Webseiten, E-Mail-Planung usw.) werden funktionieren, mit Ausnahme des E-Mail-Versands.

### SendGrid-Konto erstellen

1.  Folgen Sie den Anweisungen unter [How to Send Email Using SendGrid with Azure](http://www.windowsazure.com/en-us/develop/net/how-to-guides/sendgrid-email-service/ "SendGrid") (E-Mail-Versand mit SendGrid in Azure, in englischer Sprache), um ein kostenloses Konto zu erstellen.

### Aktualisieren der SendGrid-Anmeldeinformationen in den Workerrollen-Eigenschaften

In einem früheren Teil des Lernprogramms haben Sie die Speicherkonto-Anmeldeinformationen für die Webrolle und die beiden Workerrollen eingegeben. Dabei ist ihnen möglicherweise aufgefallen, dass Workerrolle B drei Einstellungen enthält, die in Webrolle und Workerrolle A nicht vorhanden sind. Mit derselben GUI können Sie nun diese drei Einstellungen konfigurieren (wählen Sie **Cloud** in der Dropdownliste **Dienstkonfiguration** aus).

Die folgenden Schritte beschreiben, wie Sie die Eigenschaften ändern können, indem Sie die Konfigurationsdatei bearbeiten.

1.  Bearbeiten Sie die Datei *ServiceConfiguration.Cloud.cscfg* im Projekt `AzureEmailService` und geben Sie im entsprechenden `WorkerRoleB`-Element für SendGrid-Benutzername und -Passwort die Werte ein, die Sie im vorherigen Schritt erhalten haben. Der folgende Code zeigt das Workerrolle B-Element.

    ![SendGridSettings](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-sg.png)

2.  Diese Datei enthält außerdem eine AzureMailServiceURL-Einstellung. Geben Sie hier den Wert ein, den Sie bei der Erstellung Ihres Azure-Cloud-Dienstes ausgewählt haben, z. B: "http://aescloud.cloudapp.net".

Bei der Bearbeitung der Cloud-Konfigurationsdatei konfigurieren Sie die Einstellungen, die bei der Ausführung der Anwendung in der Cloud verwendet werden. Falls die Anwendung auch bei der lokalen Ausführung E-Mails verschicken soll, müssen Sie auch die Daten *ServiceConfiguration.Local.cscfg* ändern.

Bereitstellen der AnwendungBereitstellen der Anwendung für Azure
----------------------------------------------------------------

Sie können die Anwendung bereitstellen, indem Sie entweder ein Paket in Visual Studio erstellen und dieses im Azure-Verwaltungsportal hochladen, oder Sie können das Projekt direkt in Visual Studio veröffentlichen. In diesem Lernprogramm lernen Sie die Veröffentlichungsmethode kennen.

Sie werden die Anwendung zunächst in der Stagingumgebung veröffentlichen und die Stagingumgebung später in Produktion überführen.

### Implementieren von IP-Einschränkungen

In der Stagingumgebung ist Ihre Anwendung öffentlich zugänglich für jeden, der die URL kennt. Daher sollten Sie zunächst IP-Einschränkungen implementieren, um unbefugten Zugriff zu verhindern. In Produktionsanwendungen würden Sie einen Authentifizierungs- und Autorisierungsmechanismus ähnlich des ASP.NET-Mitgliedschaftssystems implementieren. Diese Funktionen wurden jedoch bei der Beispielanwendung ausgelassen, um Einrichtung, Bereitstellung und Tests einfach zu halten.

1.  Öffnen Sie die Datei *Web.Release.config* im Stammordner des `MvcWebRole`Projekts und ersetzen Sie den Wert 127.0.0.1 des Attributs **ipAddress** mit Ihrer IP-Adresse. (Sie müssen die Datei *Web.config* im **Projektmappen-Explorer** erweitern, um die Datei **Web.Release.config** sehen zu können.)

    Sie können Ihre IP-Adresse herausfinden, indem Sie "Find my IP" in[Bing](http://www.bing.com/search?q=find+my+IP&qs=n&form=QBLH&pq=find+my+ip&sc=8-10&sp=-1&sk= "find my IP") oder einer anderen Suchmaschine eingeben.

    Nach der Veröffentlichung der Anwendung werden die in der Datei *Web.release.config* angegebenen Transformationen angewendet, und die Elemente zur IP-Einschränkung werden in der *web.config*-Datei aktualisiert, die in der Cloud bereitgestellt wird. Sie können die transformierte *web.config*-Datei im Ordner *AzureEmailService\\MvcWebRole\\obj\\Release\\TransformWebConfig\\transformed* sehen, nachdem das Paket erstellt wurde.

### Konfigurieren der Anwendung zur Verwendung Ihres Speicherkontos bei Ausführung in der Cloud

Die Speicherkonto-Anmeldeinformationen, die Sie zuvor für Web- und Workerrollen eingegeben haben, bezogen sich auf die lokale Ausführung der Anwendung. Nun müssen Sie die Speicherkonto-Anmeldeinformationen für die Ausführung der Anwendung in der Cloud eingeben.

Für diesen Test verwenden wir dieselben Anmeldeinformationen für die Cloud, die Sie auch bereits lokal verwendet haben. Bei der Bereitstellung einer Produktionsanwendung würden Sie für Tests und Produktion normalerweise zwei unterschiedliche Konten verwenden. Außerdem ist es für Produktionsumgebungen empfehlenswert, für Diagnose- und Speicher-Verbindungszeichenfolge zwei unterschiedliche Konten zu verwenden. In diesem Test werden Sie jedoch dasselbe Konto verwenden.

Sie können die Verbindungszeichenfolgen in derselben GUI konfigurieren (achten Sie darauf, dass Sie **Cloud** in der Dropdownliste **Dienstkonfiguration** auswählen). Alternativ können Sie die Konfigurationsdatei bearbeiten, wie in den folgenden Schritten beschrieben.

1.  Öffnen Sie die Datei *ServiceConfiguration.Local.cscfg* im Projekt **AzureEmailService** und kopieren Sie die `Setting`-Elemente für `StorageConnectionString` und `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`.

2.  Öffnen Sie die Datei *ServiceConfiguration.Cloud.cscfg* im Projekt **AzureEmailService** und fügen Sie die kopierten Elemente in das the `Configuration Settings`-Element für `MvcWebRole`, `WorkerRoleA` und `WorkerRoleB` ein. Ersetzen Sie dabei die bereits vorhandenen `Setting`-Elemente.

3.  Vergewissern Sie sich, dass für Web- und beide Workerrollen jeweils dieselben Verbindungszeichenfolgen definiert sind.

### Veröffentlichen der Anwendung

1.  Falls noch nicht geschehen, öffnen Sie Visual Studio als Administrator und öffnen Sie die **AzureEmailService**-Lösung.

2.  Klicken Sie mit der rechten Maustaste auf das Cloud-Projekt **AzureEmailService** und wählen Sie **Veröffentlichen** aus.

    ![Paket](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-6.png)

    Das Dialogfeld **Azure-Anwendung veröffentlichen** wird geöffnet.

    ![Cloud-Paket](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-16.png)

3.  Falls Sie Ihre Speicherkonto-Anmeldeinformationen zuvor mit der automatischen Methode importiert haben, befindet sich Ihr Azure-Abonnement in der Dropdownliste. Wählen Sie den Eintrag aus und klicken Sie auf **Weiter**. Klicken Sie andernfalls auf **Anmelden, um Anmeldeinformationen herunterzuladen** und folgen Sie den Anweisungen unter [Konfigurieren der Anwendung für den Azure-Speicher](#conf4azureStorage), um Ihre Veröffentlichungseinstellungen herunterzuladen und zu importieren.

4.  Prüfen Sie die Einstellung in der Dropdownliste **Cloud-Dienst** in der Registerkarte **Allgemeine Einstellungen**.

5.  Ändern Sie den Wert der Dropdownliste **Umgebung** von **Produktion** in **Staging**.

    ![Dashboard](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-7.png)

6.  Behalten Sie die Standardeinstellung **Release** **Buildkonfiguration** und **Cloud** für **Dienstkonfiguration** bei.

    Die Standardeinstellungen in der Registerkarte **Erweitert** brauchen für dieses Lernprogramm nicht geändert werden. Die Registerkarte **Erweitert** enthält einige hilfreiche Einstellungen für Entwicklung und Tests. Weitere Informationen zur Registerkarte "Erweitert" finden Sie unter [Assistent zum Veröffentlichen einer Azure-Anwendung](http://msdn.microsoft.com/library/windowsazure/hh535756.aspx "pub wiz").

7.  Klicken Sie auf **Weiter**.

8.  Klicken Sie im Schritt **Zusammenfassung** des Assistenten auf das **Speichern**-Symbol (das Diskettensymbol rechts neben der Dropdownliste für das Zielprofil), um die Veröffentlichungseinstellungen zu speichern.

    Bei der nächsten Veröffentlichung der Anwendung werden die gespeicherten Einstellungen verwendet, und Sie brauchen den Assistenten nicht erneut durchlaufen.

9.  Prüfen Sie Ihre Einstellungen und klicken Sie auf **Veröffentlichen**.

    ![pub](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-8.png)

    Das **Azure-Aktivitätsprotokoll** wird in Visual Studio geöffnet.

10. Klicken Sie auf den Pfeil nach rechts, um die Bereitstellungsdetails zu erweitern.

    ![pub](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-11.png)
	<br/><br/>
     ![pub](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-9.png)

    Die Bereitstellung kann 5 oder mehr Minuten in Anspruch nehmen.

11. Klicken Sie nach Abschluss der Bereitstellung auf die **Website-URL**, um die Anwendung zu starten.

    ![Dashboard](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c55.png)

12. Geben Sie einige Daten auf den Webseiten **Mailing Lists**, **Subscriber** und **Message** ein, um die Anwendung zu testen.

    **Hinweis**: Löschen Sie die Anwendung nach Abschluss Ihrer Tests, um Kosten für nicht genutzte Ressourcen zu vermeiden. Falls Sie ein [kostenloses Azure-Testkonto](http://www.windowsazure.com/en-us/pricing/free-trial/ "kostenloses Testkonto") verwenden, werden die drei bereitgestellten Rollen Ihr monatliches Limit in etwa zwei Wochen verbrauchen. Um eine Bereitstellung im Azure-Verwaltungsportal zu löschen, klicken Sie am unteren Seitenrand auf **LÖSCHEN** und wählen Sie die entsprechende Staging- oder Produktionsumgebung aus.

    ![pub](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-19.png)

13. Klicken Sie im Azure-Aktivitätsprotokoll in Visual Studio auf **In Server-Explorer öffnen**.

    Unter **Azure Compute** im **Server-Explorer** können Sie Ihre Bereitstellung überwachen. Falls Sie **Remotedesktop für alle Rollen aktivieren** im **Azure-Anwendung veröffentlichen**-Assistenten ausgewählt haben, können Sie mit der rechten Maustaste auf eine Rolleninstanz klicken und **Per Remotedesktop verbinden** auswählen.

    ![pub](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-12.png)

ProduktionÜberführen der Anwendung von Staging zu Produktion
------------------------------------------------------------

1.  Klicken Sie im [Azure-Verwaltungsportal](http://manage.windowsazure.com) im linken Bereich auf das Symbol **Cloud-Dienste** und wählen Sie Ihren Cloud-Dienst aus.

2.  Klicken Sie auf **Tauschen**.

3.  Klicken Sie auf **Ja**, um den VIP (virtuelle IP)-Tausch abzuschließen. Dieser Schritt kann mehrere Minuten in Anspruch nehmen.

    ![Dashboard](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c6.png)

4.  Klicken Sie nach Abschluss des Tauschs auf das Symbol **Cloud-Dienste** im linken Bereich und wählen Sie Ihren Cloud-Dienst aus.

5.  Blättern Sie in der Registerkarte **Dashboard** nach unten zur **Produktions-**-Bereitstellung und sehen Sie sich den Bereich **Schnellübersicht** im unteren rechten Bereich der Seite an. Beachten Sie, dass sich die **Website-URL** von einem GUID-Präfix zum Namen Ihres Cloud-Dienstes geändert hat.

    ![Dashboard](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c7.png)

6.  Klicken Sie auf den Link unter **Website-URL** oder kopieren Sie den Link und fügen Sie ihn in einem Browser ein, um die Anwendung in Produktion zu testen.

    Falls Sie die Speicherkonto-Einstellungen nicht geändert haben, werden bei der Ausführung der Anwendung in der Cloud die Daten angezeigt, die Sie beim Test der Anwendung in der Stagingumgebung eingegeben haben.

AblaufverfolgungKonfigurieren von Ablaufverfolgung und Anzeigen der Ablaufverfolgungsdaten
------------------------------------------------------------------------------------------

Die Ablaufverfolgung ist ein unschätzbares Werkzeug zum Debuggen von Cloud-Anwendungen. In diesem Abschnitt des Lernprogramms lernen Sie, wie Sie die Ablaufverfolgungsdaten anzeigen können.

1.  Vergewissern Sie sich, dass die Diagnose-Verbindungszeichenfolge Ihr Azure-Speicherkonto und nicht den Entwicklungsspeicher verwendet.

    Beide sollten gleich sein, falls Sie den Anweisungen in diesem Lernprogramm gefolgt sind. Sie können dies entweder in der Visual Studio-GUI prüfen (Registerkarte **Einstellungen** in den **Eigenschaften** für die Rollen), oder in den *ServiceConfiguration.\*.cscfg*-Dateien.

    **Hinweis:** Sie sollten nach Möglichkeit für Ablaufverfolgungsdaten und Produktionsdaten zwei unterschiedliche Speicherkonten verwenden. Der Einfachheit halber verwendet dieses Lernprogramm dasselbe Konto für beide Typen von Daten.

2.  Öffnen Sie *WorkerRoleA.cs* im Projekt **WorkerRoleA** in Visual Studio, suchen Sie nach `ConfigureDiagnostics` und sehen Sie sich die Methode `ConfigureDiagnostics` an.

         private void ConfigureDiagnostics()
         {
             DiagnosticMonitorConfiguration config = DiagnosticMonitor.GetDefaultInitialConfiguration();
             config.ConfigurationChangePollInterval = TimeSpan.FromMinutes(1d);
             config.Logs.BufferQuotaInMB = 500;
             config.Logs.ScheduledTransferLogLevelFilter = LogLevel.Verbose;
             config.Logs.ScheduledTransferPeriod = TimeSpan.FromMinutes(1d);

             DiagnosticMonitor.Start(
                 "Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString",
                 config);
         }

    Dieser Code legt fest, dass der `DiagnosticMonitor` bis zu 500 MB an Ablaufverfolgungsdaten speichert (beim Überschreiten von 500 MB werden die ältesten Daten überschrieben) und dass alle Ablaufverfolgungsnachrichten gespeichert werden sollen (LogLevel.Verbose). `ScheduledTransferPeriod` überträgt die Ablaufverfolgungsdaten jede Minute in den Speicher. Sie müssen `ScheduledTransferPeriod` konfigurieren, um Ablaufverfolgungsdaten zu speichern.

    Die Methode `ConfigureDiagnostics` in den einzelnen Worker- und Webrollen konfiguriert den Ablaufverfolgungs-Listener, sodass dieser Daten aufzeichnet, wenn Sie die Ablaufverfolgungs-API aufrufen. Weitere Informationen finden Sie unter [Using Trace in Azure Cloud Applications (Ablaufverfolgung in Azure-Cloudanwendungen, in englischer Sprache)](http://blogs.msdn.com/b/windowsazure/archive/2012/10/24/using-trace-in-windows-azure-cloud-applications-1.aspx "Using Trace in Azure").

3.  Doppelklicken Sie im **Server-Explorer** auf **WADLogsTable** (erweitern Sie **Storage** / **NameIhresSpeicherkontos** / **Tables**) für das zuvor hinzugefügte Speicherkonto. Sie können einen [WCF Data Services-Filter](http://msdn.microsoft.com/en-us/library/windowsazure/ff683669.aspx "WCF-Filter") eingeben, um die angezeigten Entitäten einzuschränken. In der folgenden Abbildung werden nur Warnungs- und Fehlernachrichten angezeigt.

    ![Dashboard](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-trc.png)

Hinzufügen einer RolleninstanzHinzufügen einer weiteren Workerrolle für zusätzliche Lastanforderungen
-----------------------------------------------------------------------------------------------------

Für die Skalierung der Ressourcen in Azure-Rollen existieren zwei Ansätze: Sie können entweder die [Größe der virtuellen Computer](http://msdn.microsoft.com/en-us/library/windowsazure/ee814754.aspx "VM-Größen") und/oder die Anzahl der laufenden virtuellen Computer anpassen.

Die Größe eines virtuellen Computers ist im `vmsize`-Attribut des `WebRole-` bzw. `WorkerRole`-Elements in der Datei *ServiceDefinition.csdef* festgelegt. Mit der Standardeinstellung `Small` erhalten Sie einen Prozessorkern und 1,75 GB Arbeitsspeicher. Für Multithreading-Anwendungen, die viel Arbeitsspeicher, Speicherplatz und Bandbreite verbrauchen, können Sie diese Größe anheben, um deren Leistung zu steigern. Mit der Größe `ExtraLarge` erhalten Sie z. B. 8 Prozessorkerne und 14 GB Arbeitsspeicher. Die Erhöhung von Arbeitsspeicher, Prozessorkernen, Festplatte und Bandbreite in einem einzigen Computer bezeichnet man auch als *vertikale Skalierung*. Gute Kandidaten für vertikale Skalierung sind z. B. ASP.NET-Webanwendungen, die [asynchrone Methoden](http://www.asp.net/mvc/tutorials/mvc-4/using-asynchronous-methods-in-aspnet-mvc-4 "Async MVC") verwenden. Unter [Größen virtueller Computer und Cloud-Dienste für Azure](http://msdn.microsoft.com/en-us/library/windowsazure/ee814754.aspx "VM-Größen") finden Sie eine Liste der Ressourcen für die einzelnen VM-Größen.

Workerrolle B ist in dieser Anwendung die einschränkende Komponente unter hoher Last, da diese Rolle für den Versand der E-Mails zuständig ist. (Workerrolle A erstellt lediglich Warteschlangen-Nachrichten und verbraucht daher weniger Ressourcen.) Da Workerrolle B nicht Multithreaded ist und relativ wenig Speicher verbraucht, macht vertikale Skalierung in diesem Fall wenig Sinn. Workerrolle B kann linear skalieren (d. h. die Leistung beinahe verdoppeln, indem Sie die Instanzen verdoppeln), indem Sie die Anzahl der Instanzen erhöhen. Die Erhöhung der Anzahl an Verarbeitungsinstanzen bezeichnet man als *horizontale Skalierung*. Jede Instanz ist mit Kosten verbunden. Sie sollten daher nur dann horizontal skalieren, wenn dies für Ihre Anwendung Sinn macht.

Sie können eine Web- oder Workerrolle horizontal skalieren, indem Sie die Einstellung in der Visual Studio-GUI ändern oder indem Sie die *ServiceConfiguration.\*.cscfg*-Dateien direkt bearbeiten. Die Anzahl der Instanzen ist in der Registerkarte **Konfiguration** im **Eigenschaften**-Fenster der Rolle und im `Instanzen`-Element in den *.cscfg*-Dateien festgelegt. Änderungen an diesen Einstellungen treten erst nach einer erneuten Bereitstellung der aktualisierten Konfigurationsdatei in Kraft. Alternativ können Sie für vorübergehende Lastspitzen die Anzahl der Rolleninstanzen im Azure-Verwaltungsportal ändern. Sie können die Anzahl der Instanzen auch mithilfe der Azure-Verwaltungs-API ändern. Außerdem können Sie den [Autoscaling Application Block](/en-us/develop/net/how-to-guides/autoscaling/) verwenden, um Ihre Anwendung bei erhöhter Last automatisch horizontal zu skalieren. Weitere Informationen zur automatischen Skalierung finden Sie in den Links am Ende des [letzten Lernprogramms in dieser Serie](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/).

In diesem Abschnitt des Lernprogramms werden Sie Workerrolle B mithilfe des Verwaltungsportals horizontal skalieren. Zunächst lernen Sie allerdings, wie Sie dies in Visual Studio erledigen können.

In Visual Studio klicken Sie dazu mit der rechten Maustaste unter **Rollen** im Cloud-Projekt und wählen **Eigenschaften** aus.

![Rechtsklick Eigenschaften](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-rt-prop.png)

Anschließend wählen Sie die Registerkarte **Konfiguration** auf der linken Seite aus und wählen **Cloud** in der Dropdownliste **Dienstkonfiguration** aus.

![Anzahl der Instanzen](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-instanceCnt.png)

Beachten Sie, dass Sie in dieser Registerkarte auch die Größe der virtuellen Computer anpassen können.

Die folgenden Schritte beschreiben, wie Sie Ihre Anwendung mithilfe des Azure-Verwaltungsportals horizontal skalieren können.

1.  Wählen Sie im Azure-Verwaltungsportal Ihren Cloud-Dienst aus und klicken Sie auf **Skalieren**.

2.  Erhöhen Sie die Anzahl der Instanzen für Workerrolle B und klicken Sie auf **Speichern**.

    ![Zusätzliche Instanzen](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-in3.png)

    Die Bereitstellung der neuen virtuellen Computer kann einige Minuten in Anspruch nehmen.

3.  Öffnen Sie die Registerkarte **Instanzen**, um die einzelnen Rolleninstanzen Ihrer Anwendung anzuzeigen.

    ![Instanzen anzeigen](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-in2.png)

Nächste SchritteNächste Schritte
--------------------------------

Sie haben gelernt, wie Sie die fertige Anwendung konfigurieren, bereitstellen und skalieren können. In den weiteren Lernprogrammen lernen Sie, die Anwendung von Grund auf zu erstellen. Im [nächsten Lernprogramm](/en-us/develop/net/tutorials/multi-tier-web-site/3-web-role/) werden Sie die Webrolle erstellen.

Links zu weiteren Ressourcen für die Arbeit mit Azure-Speichertabellen, Warteschlangen und Blobs finden Sie am Ende des [letzten Lernprogramms in dieser Serie](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/).
[Lernprogramm 3](/en-us/develop/net/tutorials/multi-tier-web-site/3-web-role/)

