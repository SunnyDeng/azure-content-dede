<properties linkid="develop-net-tutorials-multi-tier-web-site-2-download-and-run" pageTitle="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" metaKeywords="Azure tutorial, Azure storage tutorial, Azure multi-tier tutorial, MVC Web Role tutorial, Azure worker role tutorial, Azure blobs tutorial, Azure tables tutorial, Azure queues tutorial" description="Learn how to create a multi-tier app using ASP.NET MVC and Azure. The app runs in a cloud service, with web role and worker roles, and uses Azure storage tables, queues, and blobs." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" authors="riande,tdykstra" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="cloud-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="riande,tdykstra" />

# Konfigurieren und Bereitstellen der Azure-E-Mail-Dienstanwendung - 2 von 5

Dies ist das zweite von fünf Lernprogrammen, in denen die Erstellung und Bereitstellung der Azure-E-Mail-Dienstanwendung beschrieben wird. Weitere Informationen zur Anwendung und den Lernprogrammen finden Sie im [ersten Lernprogramm der Serie][ersten Lernprogramm der Serie].

In diesem Lernprogramm lernen Sie Folgendes:

-   Ermöglichen der Azure-Entwicklung auf Ihrem Computer durch Installieren des Azure SDK.
-   Konfigurieren und Testen der Azure-E-Mail-Dienstanwendung auf Ihrem lokalen Computer.
-   Veröffentlichen der Anwendung in Azure.
-   Anzeigen und bearbeiten von Tabellen, Warteschlangen und Blobs in Azure mit dem Visual Studio-Server-Explorer.
-   Konfigurieren von Ablaufverfolgung und Anzeigen der Ablaufverfolgungsdaten.
-   Skalieren der Anwendung durch Einsatz zusätzlicher Workerrolleninstanzen.

### Teile des Lernprogramms

-   [Einrichten der Entwicklungsumgebung][Einrichten der Entwicklungsumgebung]
-   [Herunterladen und Ausführen der abgeschlossenen Lösung][Herunterladen und Ausführen der abgeschlossenen Lösung]
-   [Anzeigen des Entwicklungsspeichers in Visual Studio][Anzeigen des Entwicklungsspeichers in Visual Studio]
-   [Erstellen eines Azure-Speicherkontos][Erstellen eines Azure-Speicherkontos]
-   [Erstellen von Cloud-Diensten][Erstellen von Cloud-Diensten]
-   [Konfigurieren der Anwendung für den Azure-Speicher][Konfigurieren der Anwendung für den Azure-Speicher]
-   [Konfigurieren der Anwendung für SendGrid][Konfigurieren der Anwendung für SendGrid]
-   [Bereitstellen der Anwendung in Azure][Bereitstellen der Anwendung in Azure]
-   [Überführen der Anwendung von Staging zu Produktion][Überführen der Anwendung von Staging zu Produktion]
-   [Konfigurieren von Ablaufverfolgung und Anzeigen der Ablaufverfolgungsdaten][Konfigurieren von Ablaufverfolgung und Anzeigen der Ablaufverfolgungsdaten]
-   [Hinzufügen einer weiteren Workerrolle für zusätzliche Lastanforderungen][Hinzufügen einer weiteren Workerrolle für zusätzliche Lastanforderungen]

[WACOM.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

## <a name="downloadcnfg"></a><span class="short-header">Herunterladen und Ausführen</span>Herunterladen und Ausführen der abgeschlossenen Lösung

1.  Laden Sie die [abgeschlossene Lösung][abgeschlossene Lösung] herunter und entzippen Sie das Archiv.

2.  Starten Sie Visual Studio.

3.  Wählen Sie im Menü **Datei** die Option **Projekt öffnen**, navigieren Sie zu dem Ort, an dem Sie die Lösung gespeichert haben und öffnen Sie die Lösungsdatei.

4.  Drücken Sie STRG+UMSCH+B, um die Lösung zu erstellen.

    Standardmäßig stellt Visual Studio den Inhalt des NuGet-Pakets automatisch wieder her, das nicht in der *.zip*-Datei enthalten war. Wenn die Pakete nicht wiederhergestellt werden, installieren Sie diese manuell, indem Sie das Dialogfeld **NuGet-Pakete verwalten** öffnen und oben rechts auf **Wiederherstellen** klicken.

5.  Vergewissern Sie sich im **Projektmappen-Explorer**, dass **AzureEmailService** als Startprojekt ausgewählt ist.

6.  Drücken Sie STRG+F5, um die Anwendung auszuführen.

    Die Startseite der Anwendung wird in Ihrem Browser angezeigt.

7.  Klicken Sie in der Menüleiste auf **Mailinglisten**.

    (Die Screenshots zeigen den Webseitenstil aus Visual Studio 2012-Projektvorlagen. Der Inhalt ist jedoch in Visual Studio 2013 derselbe.)

    ![Ausführen der Anwendung.][Ausführen der Anwendung.]

8.  Klicken Sie auf **Neu erstellen**.

9.  Geben Sie einige Testdaten ein und klicken Sie auf **Create**.

    ![Ausführen der Anwendung.][1]

10. Erstellen Sie einige Mailinglisten-Einträge.

    ![Indexseite Adressenliste][Indexseite Adressenliste]

11. Klicken Sie auf **Subscribers** und fügen Sie einige Abonnenten hinzu. Setzen Sie den Wert von **Verified** auf `true`.

    ![Indexseite Empfänger][Indexseite Empfänger]

12. Bereiten Sie den Nachrichtenversand vor, indem Sie eine *.txt*-Datei mit dem Text einer zu verschickenden E-Mail erstellen. Erstellen Sie anschließend eine *.htm* mit demselben Text und zusätzlichem HTML-Code (Markieren Sie z. B. einige Worte als fett oder kursiv). Sie werden diese Dateien im nächsten Schritt verwenden.

13. Klicken Sie auf **Messages** und fügen Sie Nachrichten hinzu. Wählen Sie die im vorherigen Schritt erstellten Dateien aus. Der Standardwert für das Versanddatum ist eine Woche in der Zukunft. Ändern Sie dieses Datum nicht. Die Anwendung kann erst dann Nachrichten verschicken, wenn Sie SendGrid konfiguriert haben.

    ![Seite Nachricht erstellen][Seite Nachricht erstellen]

    ![Indexseite Nachrichten][Indexseite Nachrichten]

    Die Daten, die Sie eingegeben und angezeigt haben, werden vom Azure-Speicheremulator verwaltet. Der Speicheremulator verwendet eine lokale SQL Server Express LocalDB-Datenbank, um die Funktionsweise des Azure-Speichers in der Cloud zu emulieren. Die Anwendung verwendet den Speicheremulator, da das Projekt beim Herunterladen so vorkonfiguriert war. Diese Einstellung befindet sich in den *.cscfg*-Dateien im **AzureEmailService**-Projekt. Die *ServiceConfiguration.Local.cscfg*-Datei enthält Einstellungen für die lokale Ausführung in Visual Studio, und die *ServiceConfiguration.Cloud.cscfg*-Datei enthält Einstellungen für die Bereitstellung der Anwendung in der Cloud. Später werden Sie lernen, wie Sie die Anwendung für ein Azure-Speicherkonto konfigurieren können.

14. Schließen Sie den Browser.

## <a name="StorageExpVS"></a>Anzeigen des Entwicklungsspeichers in Visual Studio

Der **Azure-Speicher**-Browser im **Server-Explorer** bietet eine praktische Möglichkeit, um direkt mit Azure-Speicherressourcen zu arbeiten.

1.  Öffnen Sie das Menü **Ansicht** in Visual Studio und klicken Sie auf **Server-Explorer**.

2.  Erweitern Sie den Knoten **Azure**, dann den Knoten **Speicher** und zuletzt den Knoten **(Entwicklung)** unterhalb des Knotens **Azure-Speicher**.

    Falls Sie noch nicht in Visual Studio bei Azure angemeldet sind, werden Sie zur Eingabe Ihrer Azure-Anmeldeinformationen aufgefordert. Geben Sie die Anmeldeinformationen für das Konto ein, das das Abonnement enthält, in dem Sie den Clouddienst ausführen möchten.

3.  Erweitern Sie **Tabellen**, um die in den vorherigen Schritten erstellten Tabellen anzuzeigen.

    ![Server-Explorer][Server-Explorer]

4.  Doppelklicken Sie auf die Tabelle **MailingList**.

    ![VS-Speicher-Explorer][VS-Speicher-Explorer]

    Beachten Sie, dass das Fenster die verschiedenen Schemata der Tabelle anzeigt. `MailingList`-Entitäten haben `Description`- und `FromEmailAddress`-Eigenschaften, und `Subscriber`-Entitäten haben die `Verified`-Eigenschaft (plus `SubscriberGUID`, die nicht angezeigt wird, da das Bild nicht breit genug ist). Die Tabelle enthält Spalten für all diese Eigenschaften, und wenn die Entität in einer gegebenen Tabellenzeile keine solche Eigenschaft hat, dann ist das entsprechende Feld leer.

Alternativ können Sie für die Arbeit mit Azure-Speicherressourcen auch den [Azure-Speicher-Explorer][Azure-Speicher-Explorer] verwenden.

## <a name="createWASA"></a>Erstellen eines Azure-Speicherkontos

Bei der Ausführung der Beispielanwendung in Visual Studio haben Sie Zugriff auf Tabellen, Warteschlangen und Blobs im Azure-Speicheremulator oder in einem Azure-Speicherkonto in der Cloud. In diesem Abschnitt des Lernprogramms erstellen Sie das Azure-Speicherkonto, das Sie später im Lernprogramm in Visual Studio konfigurieren.

1.  Öffnen Sie das [Azure-Verwaltungsportal][Azure-Verwaltungsportal] in Ihrem Browser.

2.  Klicken Sie im [Azure-Verwaltungsportal][Azure-Verwaltungsportal] auf **Speicher** und dann auf **Neu**.

![Neuer Speicher][Neuer Speicher]

1.  Klicken Sie auf **Schnellerfassung**.

![Schnellerfassung][Schnellerfassung]

1.  Geben Sie ein URL-Präfix in das URL-Eingabefeld ein.

    Dieses Präfix zusammen mit dem Text unterhalb des Eingabefelds ergibt die eindeutige URL Ihres Speicherkontos. Falls das eingegebene Präfix bereits von einer anderen Person verwendet wird, erhalten Sie die Meldung "Der Speichername ist bereits vergeben" über dem Textfeld und müssen ein anderes Präfix auswählen.

2.  Wählen Sie die Region aus, in der Sie Ihre Anwendung bereitstellen möchten.

3.  Wählen Sie in der Dropdownliste **Replikation** den Wert **Lokal redundant** aus.

    Wenn Georeplikation für ein Speicherkonto aktiviert ist, werden dessen Inhalte an einen zweiten Ort repliziert, um im Katastrophenfall eine Failover-Instanz an diesem zweiten Standort zur Verfügung zu haben. Für die Georeplikation können zusätzliche Kosten anfallen. Für Test- und Entwicklungskonten macht es wenig Sinn, für Georeplikation zu bezahlen. Weitere Informationen finden Sie unter [How to Manage Storage Accounts][How to Manage Storage Accounts] (Verwalten von Speicherkonten, in englischer Sprache).

4.  Klicken Sie auf **Speicherkonto erstellen**.

    In der folgenden Abbildung wird ein Speicherkonto mit der URL `aestest3.core.windows.net` erstellt.

    ![Erstellen des Speichers mit URL-Präfix][Erstellen des Speichers mit URL-Präfix]

    Dieser Schritt kann mehrere Minuten in Anspruch nehmen. In der Wartezeit können Sie diese Schritte wiederholen und ein Produktions-Speicherkonto erstellen. Es macht Sinn, ein Test-Speicherkonto für die lokale Entwicklung, ein weiteres Testkonto für Tests in Azure und ein Produktions-Speicherkonto zu verwenden.

5.  Klicken Sie auf das zuvor erstellte Testkonto, und klicken Sie auf das Symbol **Zugriffsschlüssel verwalten**.

    ![Schlüssel verwalten][Schlüssel verwalten]

    ![Schlüssel-GUID][Schlüssel-GUID]

    Visual Studio konfiguriert die Verbindungszeichenfolgen automatisch mit einem dieser Schlüssel, wenn Sie das Speicherkonto auswählen. Sie können die Verbindungszeichenfolgen auch manuell bearbeiten.

    Sie können Ihren verwendeten Schlüssel periodisch wechseln, ohne eine Dienstunterbrechung in Ihrer Live-Anwendung zu verursachen. Dazu generieren Sie den nicht verwendeten Schlüssel neu und können anschließend die Verbindungszeichenfolge in Ihrer Anwendung ändern, sodass diese den neu generierten Schlüssel verwendet. Mit nur einem Schlüssel würde die Anwendung ihre Verbindung zum Speicherkonto verlieren, wenn Sie den Schlüssel erneut generieren. Die in der Abbildung gezeigten Schlüssel sind nicht mehr gültig, da sie nach der Erstellung der Abbildung erneut generiert wurden.

## <a name="createcloudsvc"></a><span class="short-header">Erstellen eines Cloud-Dienstes</span>Erstellen eines Cloud-Dienstes

1.  Klicken Sie im [Azure-Verwaltungsportal][Azure-Verwaltungsportal] auf **Clouddienste** und dann auf das Symbol **Neu**.

    ![Schnellerstellung Cloud][Schnellerstellung Cloud]

2.  Klicken Sie auf **Schnellerfassung**.

3.  Geben Sie ein URL-Präfix in das URL-Eingabefeld ein.

    Analog zur Speicher-URL muss auch diese URL eindeutig sein, und Sie erhalten eine Fehlermeldung, falls das eingegebene Präfix bereits von einer anderen Person verwendet wird.

4.  Wählen Sie die Region aus, in der Sie Ihre Anwendung bereitstellen möchten.

    Sie sollten den Cloud-Dienst in derselben Region erstellen wie Ihr Speicherkonto. Wenn sich Cloud-Dienst und Speicherkonto in unterschiedlichen Datencentern (d. h. unterschiedlichen Regionen) befinden, steigt die Latenz an und Sie müssen für die Bandbreite außerhalb des Datencenters bezahlen. Die Bandbreite innerhalb eines Datencenters ist kostenlos.

    Mit Azure-Affinitätsgruppen können Sie die Distanz zwischen Ressourcen in einem Datencenter und somit die Latenz minimieren. Dieses Lernprogramm verwendet keine Affinitätsgruppen. Anweisungen finden Sie unter [Erstellen einer Affinitätsgruppe in Azure][Erstellen einer Affinitätsgruppe in Azure].

5.  Klicken Sie auf **Clouddienst erstellen**.

    In der folgenden Abbildung wird ein Cloud-Dienst mit der URL aescloud.cloudapp.net erstellt.

    ![Erstellen des Speichers mit URL-Präfix][2]

## <a name="conf4azureStorage"></a><span class="short-header">Verwenden Ihres Speicherkontos</span>Konfigurieren Sie die Anwendung, um Ihr Azure-Speicherkonto zu verwenden

Sie werden nun die Anwendung konfigurieren, sodass diese bei der Ausführung in Visual Studio Ihr Azure-Speicherkonto anstatt des Entwicklungsspeichers verwendet.

1.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **MvcWebRole** unter **Rollen** im Projekt **AzureEmailService**, und klicken Sie auf **Eigenschaften**.

    ![Rechtsklick Eigenschaften][Rechtsklick Eigenschaften]

2.  Klicken Sie im Fenster **MvcWebRole [Role]** auf die Registerkarte **Einstellungen**.

3.  Wählen Sie in der Dropdownliste **Dienstkonfiguration** den Wert **Lokal** aus.

4.  Wenn Sie den Eintrag **StorageConnectionString** auswählen, sehen Sie eine elliptische Schaltfläche (**...**) am Ende der Zeile. Klicken Sie auf die elliptische Schaltfläche, um das Dialogfeld **Verbindungszeichenfolge für Speicherkonto** zu öffnen.

    ![Rechtsklick Eigenschaften][3]

5.  Klicken Sie im Dialogfeld **Verbindungszeichenfolge für Speicherkonto erstellen** auf **Ihr Abonnement** und wählen Sie Ihr **Abonnement** und den **Kontonamen** Ihres Speichers aus.

    ![Rechtsklick Eigenschaften][4]

6.  Wiederholen Sie die Prozedur, mit der Sie die `StorageConnectionString`-Verbindungszeichenfolge gesetzt haben, für die `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`-Verbindungszeichenfolge.

7.  Wiederholen Sie die Prozedur, mit der Sie die beiden Verbindungszeichenfolgen für die MvcWebRole-Rolle gesetzt haben, um die Verbindungszeichenfolgen für Workerrolle A und Workerrolle B zu setzen.

8.  Öffnen Sie die Datei **ServiceConfiguration.Local.cscfg** im **AzureEmailService**-Projekt.

    Im Element `Role` für `MvcWebRole` sehen Sie ein `ConfigurationSettings`-Element mit den Einstellungen, die Sie in der Visual Studio-Oberfläche vorgenommen haben.

          <Role name="MvcWebRole">
            <Instances count="1" />
            <ConfigurationSettings>
              <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[Key]" />
              <Setting name="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=aestest;AccountKey=[Key]" />
            </ConfigurationSettings>
          </Role>

    Die `Role`-Elemente für die beiden Workerrollen enthalten jeweils dieselbe Verbindungszeichenfolge.

    Sie können diese Dateien auch direkt bearbeiten, anstatt das Visual Studio-Fenster **[Rolle]** zu verwenden. Weitere Informationen zu den Konfigurationsdateien finden Sie unter [Konfigurieren eines Azure-Projekts][Konfigurieren eines Azure-Projekts].

### Testen Sie die nun zur Verwendung Ihres Speicherkontos konfigurierte Anwendung

1.  Drücken Sie STRG+F5, um die Anwendung auszuführen. Klicken Sie auf die Links **Mailing Lists**, **Subscribers** und **Messages** und geben Sie wie bereits zuvor einige Daten ein.

2.  Öffnen Sie den **Server-Explorer** in Visual Studio.

3.  Erweitern Sie den Knoten **Speicher** unter dem Knoten **Azure**, und erweitern Sie den Knoten des Speicherkontos, für das Sie die Anwendung konfiguriert haben.

4.  Erweitern Sie **Tabellen** und doppelklicken Sie auf die Tabelle `MailingList`, um die Daten anzuzeigen, die Sie in den Seiten **Mailingliste** und **Abonnent** der Anwendung eingegeben haben.

### Optionale Schritte, um den automatischen Start des Azure-Speicheremulators zu deaktivieren

Falls Sie den Azure-Speicheremulator nicht verwenden, können Sie den Projektstart beschleunigen und weniger lokale Ressourcen verwenden, indem Sie den automatischen Start für den Azure-Speicheremulator deaktivieren.

1.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Cloud-Projekt **AzureEmailService** und wählen Sie **Eigenschaften** aus.

    ![Auswählen der Cloud-Projekt-Eigenschaften][Auswählen der Cloud-Projekt-Eigenschaften]

2.  Öffnen Sie die Registerkarte **Entwicklung**.

3.  Setzen Sie den Wert für **Azure-Speicheremulator starten** auf **Falsch**.

    ![Deaktivieren des automatischen Starts des Azure-Speicheremulators][Deaktivieren des automatischen Starts des Azure-Speicheremulators]

    **Hinweis**: Deaktivieren Sie diese Option nur, wenn Sie den Speicheremulator nicht verwenden.

    In diesem Fenster können Sie auch die **Service Configuration**-Datei, die bei der lokalen Ausführung der Anwendung verwendet wird, von **Lokal** in**Cloud** ändern (von *ServiceConfiguration.Local.cscfg* in *ServiceConfiguration.Cloud.cscfg*).

4.  Klicken Sie in der Windows-Taskleiste mit der rechten Maustaste auf das Speicheremulatorsymbol und klicken Sie auf **Speicheremulator herunterfahren**.

    ![ASE][ASE]

## <a name="sendGrid"></a><span class="short-header">SendGrid</span>Konfigurieren der Anwendung für SendGrid

Diese Beispielanwendung verwendet SendGrid für den E-Mail-Versand. Um E-Mails mit SendGrid zu verschicken, müssen Sie zunächst ein SendGrid-Konto einrichten und anschließend eine Konfigurationsdatei mit Ihren SendGrid-Anmeldeinformationen aktualisieren.

<div class="note"><p><strong>Hinweis:</strong>Falls Sie SendGrid nicht verwenden k&ouml;nnen oder m&ouml;chten, k&ouml;nnen Sie stattdessen Ihren eigenen E-Mail-Dienst verwenden. Der Code f&uuml;r die Verwendung von SendGrid ist isoliert in zwei Methoden von Workerrolle B. In [Lernprogramm 5][tut5] erfahren Sie, welche &Auml;nderungen Sie f&uuml;r andere E-Mail-Versandmethoden vornehmen m&uuml;ssen. In diesem Fall k&ouml;nnen Sie diese Prozedur &uuml;berspringen und mit diesem Lernprogramm fortfahren. Alle sonstigen Teile der Anwendung (Webseiten, E-Mail-Planung usw.) werden funktionieren, mit Ausnahme des E-Mail-Versands.</p></div>

### SendGrid-Konto erstellen

1.  Folgen Sie den Anweisungen unter [How to Send Email Using SendGrid with Azure][How to Send Email Using SendGrid with Azure] (E-Mail-Versand mit SendGrid in Azure, in englischer Sprache), um ein kostenloses Konto zu erstellen.

### Aktualisieren der SendGrid-Anmeldeinformationen in den Workerrollen-Eigenschaften

In einem früheren Teil des Lernprogramms haben Sie die Speicherkonto-Anmeldeinformationen für die Webrolle und die beiden Workerrollen eingegeben. Dabei ist ihnen möglicherweise aufgefallen, dass Workerrolle B drei Einstellungen enthält, die in Webrolle und Workerrolle A nicht vorhanden sind. Mit derselben GUI können Sie nun diese drei Einstellungen konfigurieren (wählen Sie **Cloud** in der Dropdownliste **Dienstkonfiguration** aus).

Die folgenden Schritte beschreiben, wie Sie die Eigenschaften ändern können, indem Sie die Konfigurationsdatei bearbeiten.

1.  Bearbeiten Sie die Datei *ServiceConfiguration.Cloud.cscfg* im Projekt `AzureEmailService` und geben Sie die Werte für SendGrid-Benutzername und -Kennwort ein, die Sie im vorherigen Schritt aus dem `WorkerRoleB`-Element erhalten haben, das diese Einstellungen enthält. Der folgende Code zeigt das Workerrolle B-Element.

    ![SendGridSettings][SendGridSettings]

2.  Diese Datei enthält außerdem eine AzureMailServiceURL-Einstellung. Geben Sie hier den Wert ein, den Sie bei der Erstellung Ihres Azure-Cloud-Dienstes ausgewählt haben, z. B: "<http://aescloud.cloudapp.net>".

Bei der Bearbeitung der Cloud-Konfigurationsdatei konfigurieren Sie die Einstellungen, die bei der Ausführung der Anwendung in der Cloud verwendet werden. Falls die Anwendung auch bei der lokalen Ausführung E-Mails verschicken soll, müssen Sie auch die Daten *ServiceConfiguration.Local.cscfg* ändern.

## <a name="deployAz"></a><span class="short-header">Bereitstellen der Anwendung</span>Bereitstellen der Anwendung für Azure

Sie können die Anwendung bereitstellen, indem Sie entweder ein Paket in Visual Studio erstellen und dieses im Azure-Verwaltungsportal hochladen, oder Sie können das Projekt direkt in Visual Studio veröffentlichen. In diesem Lernprogramm lernen Sie die Veröffentlichungsmethode kennen.

Sie werden die Anwendung zunächst in der Stagingumgebung veröffentlichen und die Stagingumgebung später in Produktion überführen.

### Implementieren von IP-Einschränkungen

In der Stagingumgebung ist Ihre Anwendung öffentlich zugänglich für jeden, der die URL kennt. Daher sollten Sie zunächst IP-Einschränkungen implementieren, um unbefugten Zugriff zu verhindern. In Produktionsanwendungen würden Sie einen Authentifizierungs- und Autorisierungsmechanismus ähnlich der ASP.NET-Identität implementieren. Diese Funktionen wurden jedoch bei der Beispielanwendung ausgelassen, um Einrichtung, Bereitstellung und Tests einfach zu halten.

1.  Öffnen Sie die Datei *Web.Release.config*, die sich im Stammordner des Projekts `MvcWebRole` befindet, und ersetzen Sie den Attributwert **ipAddress** 127.0.0.1 durch Ihre IP-Adresse. (Sie müssen die Datei *Web.config* im **Projektmappen-Explorer** erweitern, um die Datei **Web.Release.config** sehen zu können.)

    Sie können Ihre IP-Adresse herausfinden, indem Sie "Find my IP" in[Bing][Bing] oder einer anderen Suchmaschine eingeben.

    Nach der Veröffentlichung der Anwendung werden die in der Datei *Web.release.config* angegebenen Transformationen angewendet, und die Elemente zur IP-Einschränkung werden in der *web.config*-Datei aktualisiert, die in der Cloud bereitgestellt wird. Sie können die transformierte *web.config*-Datei im Ordner *AzureEmailService\\MvcWebRole\\obj\\Release\\TransformWebConfig\\transformed* sehen, nachdem das Paket erstellt wurde.

### Konfigurieren der Anwendung zur Verwendung Ihres Speicherkontos bei Ausführung in der Cloud

Die Speicherkonto-Anmeldeinformationen, die Sie zuvor für Web- und Workerrollen eingegeben haben, bezogen sich auf die lokale Ausführung der Anwendung. Nun müssen Sie die Speicherkonto-Anmeldeinformationen für die Ausführung der Anwendung in der Cloud eingeben.

Für diesen Test verwenden wir dieselben Anmeldeinformationen für die Cloud, die Sie auch bereits lokal verwendet haben. Bei der Bereitstellung einer Produktionsanwendung würden Sie für Tests und Produktion normalerweise zwei unterschiedliche Konten verwenden. Außerdem ist es für Produktionsumgebungen empfehlenswert, für Diagnose- und Speicher-Verbindungszeichenfolge zwei unterschiedliche Konten zu verwenden. In diesem Test werden Sie jedoch dasselbe Konto verwenden.

Sie können die Verbindungszeichenfolgen in derselben GUI konfigurieren (achten Sie darauf, dass Sie **Cloud** in der Dropdownliste **Dienstkonfiguration** auswählen). Alternativ können Sie die Konfigurationsdatei bearbeiten, wie in den folgenden Schritten beschrieben.

1.  Öffnen Sie die Datei *ServiceConfiguration.Local.cscfg* im Projekt **AzureEmailService**, und kopieren Sie die `Setting`-Elemente für `StorageConnectionString` und `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`.

2.  Öffnen Sie die Datei *ServiceConfiguration.Cloud.cscfg* im Projekt **AzureEmailService**, und fügen Sie die kopierten Elemente in das `Configuration Settings`-Element für `MvcWebRole`, `WorkerRoleA` und `WorkerRoleB` ein. Ersetzen Sie dabei die vorhandenen `Setting`-Elemente.

3.  Vergewissern Sie sich, dass für Web- und beide Workerrollen jeweils dieselben Verbindungszeichenfolgen definiert sind.

### Veröffentlichen der Anwendung

1.  Falls noch nicht geschehen, öffnen Sie Visual Studio und öffnen Sie die **AzureEmailService**-Lösung.

2.  Klicken Sie mit der rechten Maustaste auf das Cloud-Projekt **AzureEmailService** und wählen Sie **Veröffentlichen** aus.

    ![Paket][Paket]

    Das Dialogfeld **Azure-Anwendung veröffentlichen** wird geöffnet.

    ![Cloud-Paket][Cloud-Paket]

3.  Falls Sie Ihre Speicherkonto-Anmeldeinformationen zuvor mit der automatischen Methode importiert haben, befindet sich Ihr Azure-Abonnement in der Dropdownliste. Wählen Sie den Eintrag aus und klicken Sie auf **Weiter**. Klicken Sie andernfalls auf **Anmelden, um Anmeldeinformationen herunterzuladen**und folgen Sie den Anweisungen unter [Konfigurieren der Anwendung für den Azure-Speicher][Konfigurieren der Anwendung für den Azure-Speicher], um Ihre Veröffentlichungseinstellungen herunterzuladen und zu importieren.

4.  Vergewissern Sie sich in der Registerkarte **Allgemeine Einstellungen**, dass Ihr Clouddienst in der Dropdownliste **Clouddienst** ausgewählt ist.

5.  Ändern Sie den Wert der Dropdownliste **Umgebung** von **Produktion** in **Staging**.

    ![Dashboard][Dashboard]

6.  Behalten Sie die Standardeinstellung **Release** **Buildkonfiguration** und **Cloud** für **Dienstkonfiguration** bei.

    Die Standardeinstellungen in der Registerkarte **Erweitert** brauchen für dieses Lernprogramm nicht geändert werden. Die Registerkarte **Erweitert** enthält einige hilfreiche Einstellungen für Entwicklung und Tests. Weitere Informationen zur Registerkarte "Erweitert" finden Sie unter [Assistent zum Veröffentlichen einer Azure-Anwendung][Assistent zum Veröffentlichen einer Azure-Anwendung].

7.  Klicken Sie auf **Weiter**.

8.  Klicken Sie im Schritt **Zusammenfassung** des Assistenten auf das **Speichern**-Symbol (das Diskettensymbol rechts neben der Dropdownliste für das Zielprofil), um die Veröffentlichungseinstellungen zu speichern.

    Bei der nächsten Veröffentlichung der Anwendung werden die gespeicherten Einstellungen verwendet, und Sie brauchen den Assistenten nicht erneut durchlaufen.

9.  Prüfen Sie Ihre Einstellungen und klicken Sie auf **Veröffentlichen**.

    ![pub][pub]

Das **Azure-Aktivitätsprotokoll** wird in Visual Studio geöffnet.

1.  Klicken Sie auf den Pfeil nach rechts, um die Bereitstellungsdetails zu erweitern.

    ![pub][5]
    ![pub][6]

    Die Bereitstellung kann 5 oder mehr Minuten in Anspruch nehmen.

2.  Klicken Sie nach Abschluss der Bereitstellung auf die **Website-URL**, um die Anwendung zu starten.

    ![Dashboard][7]

3.  Geben Sie einige Daten auf den Webseiten **Mailing Lists**, **Subscriber** und **Message** ein, um die Anwendung zu testen.

    **Hinweis**: Löschen Sie die Anwendung nach Abschluss Ihrer Tests, um Kosten für nicht genutzte Ressourcen zu vermeiden. Falls Sie ein [kostenloses Azure-Testkonto][kostenloses Azure-Testkonto] verwenden, werden die drei bereitgestellten Rollen Ihr monatliches Limit in etwa zwei Wochen verbrauchen. Um eine Bereitstellung im Azure-Verwaltungsportal zu löschen, klicken Sie am unteren Seitenrand auf **LÖSCHEN** und wählen Sie die entsprechende Staging- oder Produktionsumgebung aus.

    ![pub][8]

4.  Klicken Sie im Azure-Aktivitätsprotokoll in Visual Studio auf **In Server-Explorer öffnen**.

    Unter **Azure Compute** im **Server-Explorer** können Sie Ihre Bereitstellung überwachen. Falls Sie **Remotedesktop für alle Rollen aktivieren** im **Azure-Anwendung veröffentlichen**-Assistenten ausgewählt haben, können Sie mit der rechten Maustaste auf eine Rolleninstanz klicken und **Per Remotedesktop verbinden** auswählen.

    ![pub][9]

## <a name="swap"></a>Heraufstufen der Anwendung von Staging zu Produktion

1.  Klicken Sie im [Azure-Verwaltungsportal][Azure-Verwaltungsportal] im linken Bereich auf das Symbol **Clouddienste**, wählen Sie Ihren Cloud-Dienst aus und klicken Sie auf die Registerkarte **Dashboard**.

2.  Klicken Sie auf **Austauschen**.

3.  Klicken Sie auf **Ja**, um den VIP (virtuelle IP)-Tausch abzuschließen. Dieser Schritt kann mehrere Minuten in Anspruch nehmen.

    ![Dashboard][10]

4.  Blättern Sie nach Abschluss des Austauschs in der Registerkarte **Dashboard** nach unten zur **Produktions**-Bereitstellung und sehen Sie sich den Bereich **Schnellübersicht** im unteren rechten Bereich der Seite an. Beachten Sie, dass sich die **Website-URL** von einem GUID-Präfix zum Namen Ihres Cloud-Dienstes geändert hat.

    ![Dashboard][11]

5.  Klicken Sie auf den Link unter **Website-URL** oder kopieren Sie den Link und fügen Sie ihn in einem Browser ein, um die Anwendung in Produktion zu testen.

    Falls Sie die Speicherkonto-Einstellungen nicht geändert haben, werden bei der Ausführung der Anwendung in der Cloud die Daten angezeigt, die Sie beim Test der Anwendung in der Stagingumgebung eingegeben haben.

## <a name="trace"></a>Konfigurieren und Anzeigen der Ablaufverfolgung

Die Ablaufverfolgung ist ein unschätzbares Werkzeug zum Debuggen von Cloud-Anwendungen. In diesem Abschnitt des Lernprogramms lernen Sie, wie Sie die Ablaufverfolgungsdaten anzeigen können.

1.  Vergewissern Sie sich, dass die Diagnose-Verbindungszeichenfolge Ihr Azure-Speicherkonto und nicht den Entwicklungsspeicher verwendet.

    Beide sollten gleich sein, falls Sie den Anweisungen in diesem Lernprogramm gefolgt sind. Sie können dies entweder in der Visual Studio-GUI prüfen (Registerkarte **Einstellungen** in den **Eigenschaften** für die Rollen), oder in den *ServiceConfiguration.\*.cscfg*-Dateien.

**Hinweis:** Sie sollten nach Möglichkeit für Ablaufverfolgungsdaten und Produktionsdaten zwei unterschiedliche Speicherkonten verwenden. Der Einfachheit halber verwendet dieses Lernprogramm dasselbe Konto für beide Typen von Daten.

1.  Öffnen Sie *WorkerRoleA.cs* im Projekt **WorkerRoleA** in Visual Studio, suchen Sie nach `ConfigureDiagnostics`, und sehen Sie sich die Methode `ConfigureDiagnostics` an.

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

    Dieser Code legt fest, dass der `DiagnosticMonitor` bis zu 500 MB an Ablaufverfolgungsdaten speichert (beim Überschreiten von 500 MB werden die ältesten Daten überschrieben) und dass alle Ablaufverfolgungsnachrichten gespeichert werden sollen (LogLevel.Verbose). Die `ScheduledTransferPeriod` überträgt die Ablaufverfolgungsdaten jede Minute in den Speicher. Sie müssen die `ScheduledTransferPeriod` einstellen, um die Ablaufverfolgungsdaten zu speichern.

    Die Methode `ConfigureDiagnostics` in den einzelnen Worker- und Webrollen konfiguriert den Ablaufverfolgungs-Listener, sodass dieser Daten aufzeichnet, wenn Sie die Ablaufverfolgungs-API aufrufen. Weitere Informationen finden Sie unter [Using Trace in Azure Cloud Applications (Ablaufverfolgung in Azure-Cloudanwendungen, in englischer Sprache)][Using Trace in Azure Cloud Applications (Ablaufverfolgung in Azure-Cloudanwendungen, in englischer Sprache)].

2.  Doppelklicken Sie im **Server-Explorer** auf **WADLogsTable** (erweitern Sie **Azure** / **Speicher** / **NameIhresSpeicherkontos** / **Tabellen**) für das zuvor hinzugefügte Speicherkonto. Sie können einen [WCF Data Services-Filter][WCF Data Services-Filter] eingeben, um die angezeigten Entitäten einzuschränken. In der folgenden Abbildung werden nur Warnungs- und Fehlernachrichten angezeigt.

    ![Dashboard][12]

## <a name="addRole"></a>Hinzufügen einer weiteren Workerrolle für zusätzliche Lastanforderungen

Für die Skalierung der Ressourcen in Azure-Rollen existieren zwei Ansätze: Sie können entweder die [Größe der virtuellen Computer][Größe der virtuellen Computer] und/oder die Anzahl der laufenden virtuellen Computer anpassen.

Die Größe des virtuellen Computers wird im `vmsize`-Attribut des `WebRole` oder `WorkerRole`-Elements in der Datei *ServiceDefinition.csdef* angegeben. Mit der Standardeinstellung `Small` erhalten Sie einen Prozessorkern und 1,75 GB Arbeitsspeicher. Für Multithreading-Anwendungen, die viel Arbeitsspeicher, Speicherplatz und Bandbreite verbrauchen, können Sie diese Größe anheben, um deren Leistung zu steigern. Mit der Größe `ExtraLarge` erhalten Sie z. B. 8 Prozessorkerne und 14 GB Arbeitsspeicher. Die Erhöhung von Arbeitsspeicher, Prozessorkernen, Festplatte und Bandbreite in einem einzigen Computer bezeichnet man auch als *vertikale Skalierung*. Gute Kandidaten für vertikale Skalierung sind z. B. ASP.NET-Webanwendungen, die [asynchrone Methoden][asynchrone Methoden] verwenden. Unter [Größen virtueller Computer und Cloud-Dienste für Azure][Größe der virtuellen Computer] finden Sie eine Liste der Ressourcen für die einzelnen VM-Größen.

Workerrolle B ist in dieser Anwendung die einschränkende Komponente unter hoher Last, da diese Rolle für den Versand der E-Mails zuständig ist. (Workerrolle A erstellt lediglich Warteschlangen-Nachrichten und verbraucht daher weniger Ressourcen.) Da Workerrolle B nicht Multithreaded ist und relativ wenig Speicher verbraucht, macht vertikale Skalierung in diesem Fall wenig Sinn. Workerrolle B kann linear skalieren (d. h. die Leistung beinahe verdoppeln, indem Sie die Instanzen verdoppeln), indem Sie die Anzahl der Instanzen erhöhen. Die Erhöhung der Anzahl an Verarbeitungsinstanzen bezeichnet man als *horizontale Skalierung*. Jede Instanz ist mit Kosten verbunden. Sie sollten daher nur dann horizontal skalieren, wenn dies für Ihre Anwendung Sinn macht.

Sie können eine Web- oder Workerrolle horizontal skalieren, indem Sie die Einstellung in der Visual Studio-GUI ändern oder indem Sie die *ServiceConfiguration.\*.cscfg*-Dateien direkt bearbeiten. Die Anzahl der Instanzen ist in der Registerkarte **Konfiguration** im **Eigenschaften**-Fenster der Rolle und im `Instances`-Element in den *.cscfg*-Dateien festgelegt. Änderungen an diesen Einstellungen treten erst nach einer erneuten Bereitstellung der aktualisierten Konfigurationsdatei in Kraft. Alternativ können Sie für vorübergehende Lastspitzen die Anzahl der Rolleninstanzen im Azure-Verwaltungsportal ändern oder Azure so konfigurieren, dass die Anzahl der Instanzen nach frei definierbaren Kriterien automatisch geändert wird. Weitere Informationen zur automatischen Skalierung finden Sie im [letzten Lernprogramm in dieser Serie][letzten Lernprogramm in dieser Serie].

In diesem Abschnitt des Lernprogramms werden Sie Workerrolle B mithilfe des Verwaltungsportals horizontal skalieren. Zunächst lernen Sie allerdings, wie Sie dies in Visual Studio erledigen können.

In Visual Studio klicken Sie dazu mit der rechten Maustaste unter **Rollen** im Cloud-Projekt und wählen **Eigenschaften** aus.

![Rechtsklick Eigenschaften][Rechtsklick Eigenschaften]

Anschließend wählen Sie die Registerkarte **Konfiguration** auf der linken Seite aus und wählen **Cloud** in der Dropdownliste **Dienstkonfiguration** aus.

![Anzahl der Instanzen][Anzahl der Instanzen]

Beachten Sie, dass Sie in dieser Registerkarte auch die Größe der virtuellen Computer anpassen können.

Die folgenden Schritte beschreiben, wie Sie Ihre Anwendung mithilfe des Azure-Verwaltungsportals horizontal skalieren können.

1.  Wählen Sie im Azure-Verwaltungsportal Ihren Cloud-Dienst aus und klicken Sie auf **Skalieren**.

2.  Erhöhen Sie die Anzahl der Instanzen für Workerrolle B und klicken Sie auf **Speichern**.

    ![Zusätzliche Instanzen][Zusätzliche Instanzen]

    Die Bereitstellung der neuen virtuellen Computer kann einige Minuten in Anspruch nehmen.

3.  Öffnen Sie die Registerkarte **Instanzen**, um die einzelnen Rolleninstanzen Ihrer Anwendung anzuzeigen.

    ![Instanzen anzeigen][Instanzen anzeigen]

## <a name="nextsteps"></a>Nächste Schritte

Sie haben gelernt, wie Sie die fertige Anwendung konfigurieren, bereitstellen und skalieren können. In den weiteren Lernprogrammen lernen Sie, die Anwendung von Grund auf zu erstellen. Im [nächsten Lernprogramm][nächsten Lernprogramm] werden Sie die Webrolle erstellen.

Links zu weiteren Ressourcen für die Arbeit mit Azure-Speichertabellen, Warteschlangen und Blobs finden Sie im [letzten Lernprogramms in dieser Serie][letzten Lernprogramms in dieser Serie].

<div><a href="/de-de/develop/net/tutorials/multi-tier-web-site/3-web-role/" class="site-arrowboxcta download-cta">Lernprogramm 3</a></div>

  [ersten Lernprogramm der Serie]: /de-de/develop/net/tutorials/multi-tier-web-site/1-overview/
  [Einrichten der Entwicklungsumgebung]: #setupdevenv
  [Herunterladen und Ausführen der abgeschlossenen Lösung]: #downloadcnfg
  [Erstellen eines Azure-Speicherkontos]: #createWASA
  [Erstellen von Cloud-Diensten]: #createcloudsvc
  [Konfigurieren der Anwendung für den Azure-Speicher]: #conf4azureStorage
  [Konfigurieren der Anwendung für SendGrid]: #sendGrid
  [Bereitstellen der Anwendung in Azure]: #deployAz
  [Überführen der Anwendung von Staging zu Produktion]: #swap
  [Konfigurieren von Ablaufverfolgung und Anzeigen der Ablaufverfolgungsdaten]: #trace
  [Hinzufügen einer weiteren Workerrolle für zusätzliche Lastanforderungen]: #addRole
  [abgeschlossene Lösung]: http://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36
  [Ausführen der Anwendung.]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-mailinglist1.png
  [1]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create1.png
  [Indexseite Adressenliste]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-mailing-list-index-page.png
  [Indexseite Empfänger]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-subscribers-index-page.png
  [Seite Nachricht erstellen]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-message-create-page.png
  [Indexseite Nachrichten]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-message-index-page.png
  [Server-Explorer]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-serverExplorer.png
  [VS-Speicher-Explorer]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-wasVSdata.png
  [Azure-Speicher-Explorer]: http://azurestorageexplorer.codeplex.com/
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com
  [Neuer Speicher]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-portal-new-storage.png
  [Schnellerfassung]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-storage-quick.png
  [How to Manage Storage Accounts]: /de-de/manage/services/storage/how-to-manage-a-storage-account/
  [Erstellen des Speichers mit URL-Präfix]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create-storage-url-test.png
  [Schlüssel verwalten]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-manage-keys.png
  [Schlüssel-GUID]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-guid-keys.PNG
  [Schnellerstellung Cloud]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-new-cloud.png
  [Erstellen einer Affinitätsgruppe in Azure]: http://msdn.microsoft.com/de-de/library/jj156209.aspx
  [2]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create-cloud.png
  [Rechtsklick Eigenschaften]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-rt-prop.png
  [3]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-elip.png
  [4]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-enter.png
  [Konfigurieren eines Azure-Projekts]: http://msdn.microsoft.com/de-de/library/windowsazure/ee405486.aspx
  [Auswählen der Cloud-Projekt-Eigenschaften]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-aesp.png
  [Deaktivieren des automatischen Starts des Azure-Speicheremulators]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-1.png
  [ASE]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-se4.png
  [How to Send Email Using SendGrid with Azure]: http://www.windowsazure.com/de-de/develop/net/how-to-guides/sendgrid-email-service/ "SendGrid"
  [SendGridSettings]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-sg.png
  [Bing]: http://www.bing.com/search?q=find+my+IP&qs=n&form=QBLH&pq=find+my+ip&sc=8-10&sp=-1&sk= "find my IP"
  [Paket]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-6.png
  [Cloud-Paket]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-16.png
  [Dashboard]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-7.png
  [Assistent zum Veröffentlichen einer Azure-Anwendung]: http://msdn.microsoft.com/library/windowsazure/hh535756.aspx "pub wiz"
  [pub]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-8.png
  [5]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-11.png
  [6]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-9.png
  [7]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c55.png
  [kostenloses Azure-Testkonto]: http://www.windowsazure.com/de-de/pricing/free-trial/ "kostenloses Testkonto"
  [8]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-19.png
  [9]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-12.png
  [10]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c6.png
  [11]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c7.png
  [Using Trace in Azure Cloud Applications (Ablaufverfolgung in Azure-Cloudanwendungen, in englischer Sprache)]: http://blogs.msdn.com/b/windowsazure/archive/2012/10/24/using-trace-in-windows-azure-cloud-applications-1.aspx "Using Trace in Azure"
  [WCF Data Services-Filter]: http://msdn.microsoft.com/de-de/library/windowsazure/ff683669.aspx "WCF-Filter"
  [12]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-trc.png
  [Größe der virtuellen Computer]: http://msdn.microsoft.com/de-de/library/windowsazure/ee814754.aspx "VM-Größen"
  [asynchrone Methoden]: http://www.asp.net/mvc/tutorials/mvc-4/using-asynchronous-methods-in-aspnet-mvc-4 "Async MVC"
  [letzten Lernprogramm in dieser Serie]: /de-de/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/
  [Anzahl der Instanzen]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-instanceCnt.png
  [Zusätzliche Instanzen]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-in3.png
  [Instanzen anzeigen]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-in2.png
  [nächsten Lernprogramm]: /de-de/develop/net/tutorials/multi-tier-web-site/3-web-role/
  [letzten Lernprogramms in dieser Serie]: /de-de/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/#nextsteps
