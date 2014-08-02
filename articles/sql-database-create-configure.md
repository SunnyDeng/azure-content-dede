<properties linkid="manage-services-how-to-configure-a-sqldb" urlDisplayName="How to configure" pageTitle="How to configure a SQL Database - Azure" metaKeywords="Azure creating SQL Server, Azure configuring SQL Server" description="Learn how to create and configure a logical server using SQL Server in Azure." metaCanonical="" services="sql-database" documentationCenter="" title="How to Create and Configure SQL Database" authors="" solutions="" manager="" editor="" />

Erstellen und Konfigurieren einer SQL-Datenbank
===============================================

In diesem Thema wird die Erstellung und Konfiguration logischer Server behandelt. Im neuen Azure-Verwaltungsportal (Vorschau) können Sie mit überarbeiteten Workflows eine Datenbank und anschließend einen Server erstellen.

In diesem Thema wird jedoch zuerst der Server erstellt. Dieser Ansatz ist besser geeignet, wenn Sie bereits SQL Server-Datenbanken haben, die Sie hochladen möchten.

Inhaltsverzeichnis
------------------

-   [Gewusst wie: Erstellen eines logischen Servers](#createLogical)
-   [Gewusst wie: Konfigurieren der Firewall für den logischen Server](#configFWLogical)

Gewusst wie: Erstellen eines logischen Servers
----------------------------------------------

1.  Melden Sie sich beim [Verwaltungsportal](http://manage.windowsazure.com) an.

2.  Klicken Sie auf der Startseite der SQL-Datenbanken auf **SQL-Datenbank** und dann auf **SERVER**.

3.  Klicken Sie unten auf der Seite auf **Hinzufügen**.

4.  Geben Sie in den Servereinstellungen einen Administratornamen (in einem Wort ohne Leerzeichen) ein.

    Die SQL-Datenbank verwendet SQL-Authentifizierung über eine verschlüsselte Verbindung. Es wird ein neuer SQL Server-Authentifizierungsanmeldename mit dem von Ihnen angegebenen Namen erstellt, welcher der festen Serverrolle sysadmin zugewiesen ist.

    Der Anmeldename darf keine E-Mail-Adresse, kein Windows-Benutzerkonto und auch keine Windows Live ID sein. Ansprüche oder Windows-Authentifizierung wird in SQL-Datenbanken nicht unterstützt.

5.  Geben Sie ein sicheres Kennwort mit mindestens acht Zeichen an, das eine Kombination aus Groß- und Kleinbuchstaben und Zahlen oder Symbolen enthält.

6.  Wählen Sie eine Region aus. Die Region bestimmt den geografischen Standort des Servers. Die Region kann nicht einfach geändert werden, wählen Sie also einen sinnvollen Standort für den Server aus. Wählen Sie den nächstgelegenen Standort aus. Wenn Sie Ihre Azure-Anwendung und -Datenbank in derselben Region platzieren, sparen Sie Zugangskosten für Bandbreite und Datenlatenz.

7.  Lassen Sie **Allow Services** aktiviert, damit Sie über das Verwaltungsportal für die SQL-Datenbank, Speicherdienste und andere Dienste in Azure eine Verbindung zur Datenbank herstellen können.

8.  Klicken Sie abschließend auf das Häkchen unten auf der Seite.

Beachten Sie, dass Sie keinen Servernamen angegeben haben. Die SQL-Datenbank generiert automatisch einen Servernamen, um doppelte DNS-Einträge zu verhindern. Der Servername ist eine alphanumerische Zeichenfolge aus zehn Zeichen. Sie können den Namen Ihres SQL-Datenbankservers nicht ändern.

Im nächsten Schritt konfigurieren Sie die Firewall, sodass Verbindungen von Anwendungen, die auf Ihrem Netzwerk ausgeführt werden, Zugriff erhalten.

Gewusst wie: Konfigurieren der Firewall für den logischen Server
----------------------------------------------------------------

1.  Klicken Sie im [Verwaltungsportal](http://manage.windowsazure.com) auf **SQL-Datenbanken**, klicken Sie auf **Server**, und klicken Sie dann auf den gerade erstellten Server.

2.  Klicken Sie auf **Konfigurieren**.

3.  Kopieren Sie die aktuelle Client-IP-Adresse. Wenn Sie eine Verbindung von einem Netzwerk aus herstellen, ist dies die IP-Adresse, die Ihr Router oder Proxyserver abhört. Die SQL-Datenbank erkennt die IP-Adresse, die von der aktuellen Verbindung verwendet wird, sodass Sie eine Firewall-Regel zur Genehmigung der Verbindungsanforderungen von diesem Gerät erstellen können.

4.  Fügen Sie die IP-Adresse in Bereichsanfang und -ende ein. Wenn später Verbindungsfehler auftreten, die angeben, dass der Bereich zu eng ist, können Sie diese Regel bearbeiten und den Bereich erweitern.

    Wenn Clientcomputer dynamisch zugewiesene IP-Adressen verwenden, müssen Sie einen Bereich angeben, der breit genug für die IP-Adressen ist, die den Computern im Netzwerk zugewiesen wurden. Beginnen Sie mit einem schmalen Bereich, und erweitern Sie diesen nur im Bedarfsfall.

5.  Geben Sie einen Namen für die Firewall-Regel ein, z. B. den Namen Ihres Computers oder der Firma.

6.  Klicken Sie auf das Häkchen, um die Regel zu speichern.

7.  Klicken Sie unten auf der Seite auf **Save**, um den Schritt abzuschließen. Falls **Save** nicht angezeigt wird, aktualisieren Sie die Browserseite.

Sie haben jetzt einen logischen Server, eine Firewall-Regel, die eingehende Verbindungen von Ihrer IP-Adresse erlaubt, sowie einen Administratoranmeldenamen. Im nächsten Schritt wechseln Sie zum lokalen Computer, um die verbleibenden Konfigurationsschritte abzuschließen.

**Hinweis:** Der logische Server, den Sie gerade erstellt haben, ist vorübergehend, und wird dynamisch auf physikalischen Servern in einem Datencenter gehostet. Wenn Sie den Server löschen, kann dies nicht mehr rückgängig gemacht werden. Sichern Sie alle Datenbanken, die Sie nachträglich zum Server hochladen.

