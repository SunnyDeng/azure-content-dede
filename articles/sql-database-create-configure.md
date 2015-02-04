<properties urlDisplayName="How to create and configure an Azure SQL DB" pageTitle="Erstellen und Konfigurieren einer Azure SQL-Datenbank - Azure-Lernprogramm" metaKeywords="Azure SQL-Datenbank erstellen, Azure SQL-Datenbank konfigurieren" description="Erstellen und Konfigurieren einer Azure SQL-Datenbank" metaCanonical="" services="sql-database" documentationCenter="" title="How to Create and Configure an Azure SQL Database" authors="sidneyh" solutions="" manager="jhubbard" editor="" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sidneyh" />

<h1><a id="configLogical"></a>Erstellen und Konfigurieren einer Azure SQL-Datenbank</h1>

In diesem Thema erstellen und konfigurieren Sie eine Azure SQL-Datenbank über das Azure-Verwaltungsportal. In diesem Workflow erstellen Sie zunächst den Server. Dieser Ansatz ist besser geeignet, wenn Sie bereits SQL Server-Datenbanken haben, die Sie hochladen möchten.

## Inhaltsverzeichnis
* [Gewusst wie: Erstellen eines logischen Servers](#createLogical)
* [Gewusst wie: Konfigurieren der Firewall für den logischen Server](#configFWLogical)

<h2><a id="createLogical"></a>Gewusst wie: Erstellen eines logischen Servers</h2>

1. Melden Sie sich beim [Verwaltungsportal](http://manage.windowsazure.com) an.

2. Klicken Sie unten auf der Seite auf **NEU**.

	![Click SQL Databases][1]

3. Klicken Sie auf **Datendienste**, klicken Sie auf **SQL-Datenbank** und dann auf **Schnellerstellung**.

	![Click New, Data Services, and Quick Create][2]
	 
5. Wählen Sie in den **SQL-Datenbankservereinstellungen** ein Abonnement aus.

	![Select a subscription][3]

6. Geben Sie in **SQL-Datenbankservereinstellungen** einen Anmeldenamen als ein Wort ohne Leerzeichen ein. 

	SQL-Datenbank verwendet SQL-Authentifizierung über eine verschlüsselte Verbindung. Es wird ein neuer SQL Server-Authentifizierungsanmeldename mit dem von Ihnen angegebenen Namen erstellt, welcher der festen Serverrolle sysadmin zugewiesen ist. 

	Der Anmeldename darf keine E-Mail-Adresse, kein Windows-Benutzerkonto und auch keine Windows Live ID sein. Ansprüche oder Windows-Authentifizierung wird in SQL-Datenbanken nicht unterstützt.
	
7. Geben Sie ein sicheres Kennwort mit mindestens acht Zeichen an, das eine Kombination aus Groß- und Kleinbuchstaben und Zahlen oder Symbolen enthält.

7. Wählen Sie eine Region aus. Die Region bestimmt den geografischen Standort des Servers. Die Region kann nicht einfach geändert werden, wählen Sie also einen sinnvollen Standort für den Server aus. Wählen Sie den nächstgelegenen Standort aus. Wenn Sie Ihre Azure-Anwendung und -Datenbank in derselben Region platzieren, sparen Sie Zugangskosten für Bandbreite und Datenlatenz.

8. Lassen Sie die Option **Ermöglichen Sie Windows Azure-Diensten den Zugriff auf den Server** aktiviert, damit Sie über das Verwaltungsportal, Speicherdienste und andere Dienste in Azure eine Verbindung zur Datenbank herstellen können. 

9. Klicken Sie abschließend auf das Häkchen unten auf der Seite.

### Automatisch generierter Servername

Beachten Sie, dass Sie keinen Servernamen angegeben haben. SQL Database generiert automatisch einen Servernamen, um doppelte DNS-Einträge zu verhindern. Der Servername ist eine alphanumerische Zeichenfolge aus zehn Zeichen. Sie können den Namen Ihres SQL Database-Servers nicht ändern.

Im nächsten Schritt konfigurieren Sie die Firewall, sodass Verbindungen von Anwendungen, die auf Ihrem Netzwerk ausgeführt werden, Zugriff erhalten.

## Gewusst wie: Konfigurieren der Firewall für den logischen Server

1. Klicken Sie im [Verwaltungsportal](http://manage.windowsazure.com) auf **SQL-Datenbanken**, und klicken Sie dann auf **Server**

	![Click Servers][4]
2. Klicken Sie in der Liste auf den soeben erstellten Server.

2. Klicken Sie auf **Konfigurieren**.

	![Click Configure][5]

3. Klicken Sie im Abschnitt **Zulässige IP-Adressen** auf **ZU DEN ZULÄSSIGEN IP-ADRESSEN HINZUFÜGEN**. Dies ist die IP-Adresse, die Ihr Router oder Proxyserver aktuell abhört. SQL Database erkennt die IP-Adresse, die von der aktuellen Verbindung verwendet wird, und erstellt eine Firewall-Regel zur Genehmigung der Verbindungsanforderungen von diesem Gerät. 
	![Click Add to the allowed IP addresses][6]

	Es wird ein Standardname für die Regel generiert. Bearbeiten Sie den Namen beliebig. 
	

4. Wenn Sie von einem anderen Computer aus eine Verbindung mit der Datenbank herstellen, müssen Sie eine neue Regel erstellen, um die Verbindung über dessen IP-Adresse zuzulassen. Verwenden Sie die Start- und End-Felder, um einen Bereich von IP-Adressen zu erstellen.

	Wenn Clientcomputer dynamisch zugewiesene IP-Adressen verwenden, können Sie einen Bereich angeben, der breit genug für die IP-Adressen ist, die den Computern im Netzwerk zugewiesen wurden. Beginnen Sie mit einem schmalen Bereich, und erweitern Sie diesen nur im Bedarfsfall.

7. Klicken Sie unten auf der Seite auf **Save**, um den Schritt abzuschließen. 

Sie haben jetzt einen logischen Server, eine Firewall-Regel, die eingehende Verbindungen von Ihrer IP-Adresse erlaubt, sowie einen Administratoranmeldenamen. 

**Hinweis:** Der logische Server, den Sie gerade erstellt haben, ist virtuell, und wird dynamisch auf physischen Servern in einem Datencenter gehostet. Das Löschen des Servers kann nicht rückgängig gemacht werden. Sichern Sie alle Datenbanken, die Sie nachträglich zum Server hochladen. 


<!--Image references-->
[1]: ./media/sql-database-create-configure/click-new.png
[2]: ./media/sql-database-create-configure/new-data-services-sql-storage-quick-create.png
[3]: ./media/sql-database-create-configure/server-settings.png
[4]: ./media/sql-database-create-configure/click-servers.png
[5]: ./media/sql-database-create-configure/click-configure.png
[6]: ./media/sql-database-create-configure/allowed-ip-addresses.png

