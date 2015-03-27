<properties 
	pageTitle="Erstellen und Konfigurieren einer Azure SQL-Datenbank - Azure-Lernprogramm" 
	description="Erstellen und Konfigurieren einer Azure SQL-Datenbank" 
	services="sql-database" 
	documentationCenter="" 
	authors="sidneyh" 
	manager="jhubbard" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/19/2014" 
	ms.author="sidneyh"/>

# Erstellen und Konfigurieren einer Azure SQL-Datenbank

In diesem Thema erstellen und konfigurieren Sie eine neue Azure SQL-Datenbank mit der Option **SCHNELLERFASSUNG** des Azure-Verwaltungsportals. Dieses Verfahren veranschaulicht, wie Sie eine SQL-Datenbank mit einem vorhandenen Server erstellen und bei Bedarf auch einen neuen Server erstellen.

> [AZURE.NOTE] Beim Erstellen einer SQL-Datenbank mit der **SCHNELLERFASSUNG** wird eine Standarddatenbank (S0) bereitgestellt. Zum Erstellen einer SQL-Datenbank auf einer anderen Dienstebene und Leistungsebene als der Standardebene (S0) verwenden Sie **BENUTZERDEFINIERT ERSTELLEN**. Ausführliche Informationen zum Erstellen einer Azure SQL-Datenbank mit **BENUTZERDEFINIERT ERSTELLEN** finden Sie unter [Erste Schritte mit Microsoft Azure SQL-Datenbank](http://azure.microsoft.com/documentation/articles/sql-database-get-started/).

## Gewusst wie: Erstellen einer Azure SQL-Datenbank

1. Melden Sie sich im [Verwaltungsportal](https://portal.azure.com/) an.

2. Klicken Sie unten auf der Seite auf **NEU**.

	![Click SQL Databases][1]

3. Klicken Sie auf **DATA SERVICES**, auf **SQL-DATENBANK** und dann auf **SCHNELLERFASSUNG**.

	![Click New, Data Services, and Quick Create][2]
	 
5. Geben Sie in **SCHNELLERFASSUNG** einen Namen für die neue Datenbank ein, wählen Sie ein Abonnement, und wählen Sie dann aus der Liste **SERVER** einen Server aus (gehen Sie zum nächsten Schritt zum Erstellen eines neuen Servers über).

	![Create a new SQL Database in an existing server][7]

	Optional können Sie einen neuen Server erstellen, indem Sie **Neuer SQL-Datenbankserver** auswählen.
    ![Create a new SQL Database and a new server][8]

	1. Wählen Sie eine Region aus. Die Region bestimmt den geografischen Standort des Servers. Die Region kann nicht einfach geändert werden. Wählen Sie also einen sinnvollen Standort für diesen Server aus. Wählen Sie den nächstgelegenen Standort aus. Wenn Sie Ihre Azure-Anwendung und -Datenbank in derselben Region platzieren, sparen Sie Zugangskosten für Bandbreite und Datenlatenz.
	2. Geben Sie einen Anmeldenamen (in einem Wort ohne Leerzeichen) ein. 

		SQL-Datenbank verwendet SQL-Authentifizierung über eine verschlüsselte Verbindung. Es wird ein neuer SQL Server-Authentifizierungsanmeldename mit dem von Ihnen angegebenen Namen erstellt, welcher der festen Serverrolle sysadmin zugewiesen ist. 

		Der Anmeldename darf keine E-Mail-Adresse, kein Windows-Benutzerkonto und auch keine Windows Live ID sein. Ansprüche oder Windows-Authentifizierung wird in SQL Database nicht unterstützt. 
	3. Geben Sie ein sicheres Kennwort mit mindestens acht Zeichen an, das eine Kombination aus Groß- und Kleinbuchstaben und Zahlen oder Symbolen enthält.

	


9. Wenn Sie fertig sind, klicken Sie unten auf der Seite auf das Häkchen **SQL-DATENBANK ERSTELLEN**.

### Automatisch generierter Servername

Beachten Sie, dass Sie keinen Servernamen angegeben haben, falls Sie einen neuen Server erstellt haben. SQL-Datenbank generiert automatisch einen Servernamen, um doppelte DNS-Einträge zu verhindern. Der Servername ist eine alphanumerische Zeichenfolge aus zehn Zeichen. Sie können den Namen Ihres SQL Database-Servers nicht ändern.

Im nächsten Schritt konfigurieren Sie die Firewall, sodass Verbindungen von Anwendungen, die in Ihrem Netzwerk ausgeführt werden, Zugriff erhalten.

<a id="configFWLogical"></a>

## Gewusst wie: Konfigurieren der Firewall für den logischen Server

1. Klicken Sie im [Verwaltungsportal](http://manage.windowsazure.com) auf **SQL-Datenbanken**, und klicken Sie dann auf **Server**

	![Click Servers][4]
2. Klicken Sie in der Liste auf den soeben erstellten Server.

2. Klicken Sie auf **Configure**.

	![Click Configure][5]

3. Klicken Sie im Abschnitt **Zulässige IP-Adressen** auf **ZU DEN ZULÄSSIGEN IP-ADRESSEN HINZUFÜGEN**. Dies ist die IP-Adresse, die Ihr Router oder Proxyserver aktuell abhört. SQL Database erkennt die IP-Adresse, die von der aktuellen Verbindung verwendet wird, und erstellt eine Firewall-Regel zur Genehmigung der Verbindungsanforderungen von diesem Gerät. 
	![Click Add to the allowed IP addresses][6]

	Es wird ein Standardname für die Regel generiert. Bearbeiten Sie den Namen beliebig. 
	

4. Wenn Sie von einem anderen Computer aus eine Verbindung mit der Datenbank herstellen, müssen Sie eine neue Regel erstellen, um die Verbindung über dessen IP-Adresse zuzulassen. Verwenden Sie die Start- und End-Felder, um einen Bereich von IP-Adressen zu erstellen.

	Wenn Clientcomputer dynamisch zugewiesene IP-Adressen verwenden, können Sie einen Bereich angeben, der breit genug für die IP-Adressen ist, die den Computern im Netzwerk zugewiesen wurden. Beginnen Sie mit einem engen Bereich, und erweitern Sie diesen nur im Bedarfsfall.

7. Klicken Sie unten auf der Seite auf **Speichern**, um diesen Schritt abzuschließen. 

Sie verfügen jetzt über eine SQL-Datenbank, einen logischen Server, eine Firewallregel, die eingehende Verbindungen von Ihrer IP-Adresse erlaubt, sowie einen Administratoranmeldenamen. 

**Hinweis:** Der gerade erstellte logische Server ist virtuell und wird dynamisch auf physischen Servern in einem Rechenzentrum gehostet. Das Löschen des Servers kann nicht mehr rückgängig gemacht werden. Achten Sie darauf, alle Datenbanken, die Sie nachfolgend auf den Server hochladen, zu sichern. 


<!--Image references-->
[1]: ./media/sql-database-create-configure/click-new.png
[2]: ./media/sql-database-create-configure/new-data-services-sql-storage-quick-create.png
[3]: ./media/sql-database-create-configure/server-settings.png
[4]: ./media/sql-database-create-configure/click-servers.png
[5]: ./media/sql-database-create-configure/click-configure.png
[6]: ./media/sql-database-create-configure/allowed-ip-addresses.png
[7]: ./media/sql-database-create-configure/quick-create-existing-server.png
[8]: ./media/sql-database-create-configure/quick-create-new-server.png




<!--HONumber=47-->
