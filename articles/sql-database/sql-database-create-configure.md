<properties 
	pageTitle="Erstellen und Konfigurieren einer Azure SQL-Datenbank - Azure-Lernprogramm" 
	description="Erstellen und Konfigurieren einer Azure SQL-Datenbank" 
	services="sql-database" 
	documentationCenter="" 
	authors="sidneyh" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/21/2015" 
	ms.author="sidneyh"/>

# So wird‘s gemacht: Erstellen und Konfigurieren einer Azure SQL-Datenbank

In diesem Thema erfahren Sie, wie Sie eine neue Azure SQL-Datenbank mit der Option **SCHNELLERFASSUNG** des Azure-Verwaltungsportals erstellen und konfigurieren. Dieses Verfahren veranschaulicht, wie Sie eine SQL-Datenbank mit einem vorhandenen Server erstellen und bei Bedarf auch einen neuen Server erstellen.

> [AZURE.NOTE]Beim Erstellen einer SQL-Datenbank mit der SCHNELLERFASSUNG wird eine Standarddatenbank (S0) bereitgestellt. SQL-Datenbanken einer anderen Dienst- und Leistungsebene können mit der Option **BENUTZERDEFINIERT ERSTELLEN** erstellt werden. Ausführliche Informationen zum Erstellen einer Azure SQL-Datenbank mit **BENUTZERDEFINIERT ERSTELLEN** finden Sie unter [Erste Schritte mit Microsoft Azure SQL-Datenbank](sql-database-get-started.md).

## So wird‘s gemacht: Erstellen einer Azure SQL-Datenbank

1. Melden Sie sich im [Verwaltungsportal](https://portal.azure.com/) an.

2. Klicken Sie am unteren Seitenrand auf **NEU**.

	![Klicken Sie auf „SQL-Datenbanken“.][1]

3. Klicken Sie auf **DATA SERVICES**, auf **SQL-DATENBANK** und anschließend auf **SCHNELLERFASSUNG**.

	![Klicken Sie auf „Neu“ > „Data Services“ > „Schnellerfassung“.][2]
	 
5. Geben Sie unter **SCHNELLERFASSUNG** einen Namen für die neue Datenbank ein, wählen Sie ein Abonnement aus, und wählen Sie dann aus in der Liste **SERVER** einen Server aus (oder erstellen Sie mithilfe des folgenden Schritts einen neuen Server).

	![Erstellen Sie eine neue SQL-Datenbank auf einem vorhandenen Server.][7]

	Optional: Klicken Sie auf **Neuer SQL-Datenbankserver**, um einen neuen Server zu erstellen. ![Erstellen Sie eine neue SQL-Datenbank und einen neuen Server.][8]

	1. Wählen Sie eine Region aus. Die Region bestimmt den geografischen Standort des Servers. Die Region kann nicht einfach geändert werden, wählen Sie also einen sinnvollen Standort für den Server aus. Wählen Sie den nächstgelegenen Standort aus. Wenn Sie Ihre Azure-Anwendung und -Datenbank in derselben Region platzieren, sparen Sie Zugangskosten für Bandbreite und Datenlatenz.
	2. Geben Sie einen Anmeldenamen (in einem Wort ohne Leerzeichen) ein. 

		SQL-Datenbank verwendet SQL-Authentifizierung über eine verschlüsselte Verbindung. Es wird ein neuer SQL Server-Authentifizierungsanmeldename mit dem von Ihnen angegebenen Namen erstellt, welcher der festen Serverrolle sysadmin zugewiesen ist.

		Der Anmeldename darf keine E-Mail-Adresse, kein Windows-Benutzerkonto und auch keine Windows Live ID sein. Ansprüche oder Windows-Authentifizierung wird in SQL Database nicht unterstützt. 
	3. Geben Sie ein sicheres Kennwort mit mindestens acht Zeichen an, das eine Kombination aus Groß- und Kleinbuchstaben und Zahlen oder Symbolen enthält.

	


9. Wenn Sie abschließend am unteren Seitenrand auf das Häkchen für **SQL-DATENBANK ERSTELLEN**.

### Automatisch generierter Servername

Beachten Sie, dass Sie keinen Servernamen angegeben haben, falls Sie einen neuen Server erstellt haben. SQL-Datenbank generiert automatisch einen Servernamen, um doppelte DNS-Einträge zu verhindern. Der Servername ist eine alphanumerische Zeichenfolge aus zehn Zeichen. Sie können den Namen Ihres SQL Database-Servers nicht ändern.

Im nächsten Schritt konfigurieren Sie die Firewall, sodass Verbindungen von Anwendungen, die in Ihrem Netzwerk ausgeführt werden, Zugriff erhalten.

<a id="configFWLogical"></a>

## So wird‘s gemacht: Konfigurieren der Firewall für den logischen Server

1. Klicken Sie im [Verwaltungsportal](http://manage.windowsazure.com) auf **SQL-Datenbanken** und anschließend auf **Server**.

	![Klicken Sie auf „Server“.][4]
2. Klicken Sie in der Liste auf den soeben erstellten Server.

2. Klicken Sie auf **Konfigurieren**.

	![Klicken Sie auf „Konfigurieren“.][5]

3. Klicken Sie im Abschnitt **Zulässige IP-Adressen** auf **ZU DEN ZULÄSSIGEN IP-ADRESSEN HINZUFÜGEN**. Dies ist die IP-Adresse, die Ihr Router oder Proxyserver aktuell abhört. Die SQL-Datenbank erkennt die IP-Adresse, die von der aktuellen Verbindung verwendet wird, und erstellt eine Firewallregel, um Verbindungsanforderungen von diesem Gerät zuzulassen. ![Klicken Sie auf „Zu den zulässigen IP-Adressen hinzufügen“.][6]

	Es wird ein Standardname für die Regel generiert. Bearbeiten Sie den Namen beliebig.
	

4. Wenn Sie von einem anderen Computer aus eine Verbindung mit der Datenbank herstellen, müssen Sie eine neue Regel erstellen, um die Verbindung über dessen IP-Adresse zuzulassen. Verwenden Sie die Start- und End-Felder, um einen Bereich von IP-Adressen zu erstellen.

	Wenn Clientcomputer dynamisch zugewiesene IP-Adressen verwenden, können Sie einen Bereich angeben, der breit genug für die IP-Adressen ist, die den Computern im Netzwerk zugewiesen wurden. Beginnen Sie mit einem engen Bereich, und erweitern Sie diesen nur im Bedarfsfall.

7. Klicken Sie unten auf der Seite auf **Save**, um den Schritt abzuschließen.

Sie verfügen jetzt über eine SQL-Datenbank, einen logischen Server, eine Firewallregel, die eingehende Verbindungen von Ihrer IP-Adresse erlaubt, sowie einen Administratoranmeldenamen.

**Hinweis:** Der logische Server, den Sie soeben erstellt haben, ist virtuell und wird dynamisch auf physischen Servern in einem Datencenter gehostet. Das Löschen des Servers kann nicht rückgängig gemacht werden. Sichern Sie alle Datenbanken, die Sie nachträglich zum Server hochladen.


<!--Image references-->
[1]: ./media/sql-database-create-configure/click-new.png
[2]: ./media/sql-database-create-configure/new-data-services-sql-storage-quick-create.png
[3]: ./media/sql-database-create-configure/server-settings.png
[4]: ./media/sql-database-create-configure/click-servers.png
[5]: ./media/sql-database-create-configure/click-configure.png
[6]: ./media/sql-database-create-configure/allowed-ip-addresses.png
[7]: ./media/sql-database-create-configure/quick-create-existing-server.png
[8]: ./media/sql-database-create-configure/quick-create-new-server.png



 

<!---HONumber=August15_HO6-->