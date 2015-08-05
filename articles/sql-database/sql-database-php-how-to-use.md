<properties 
	pageTitle="Verwenden von SQL-Datenbank (PHP) – Azure-Featureleitfaden" 
	description="Erfahren Sie, wie Sie eine Azure SQL-Datenbank in PHP erstellen und eine Verbindung zu dieser herstellen." 
	services="sql-database" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="jeffreyg" 
	editor="mollybos"/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="03/25/2015" 
	ms.author="tomfitz"/>

#Zugreifen auf Azure SQL-Datenbank aus PHP 

## Übersicht

In diesem Leitfaden werden die Grundlagen der Verwendung von SQL-Datenbank aus PHP erläutert. Die Beispiele sind in PHP geschrieben. Die behandelten Szenarien umfassen das **Erstellen einer SQL-Datenbank** und das **Herstellen einer Verbindung zu einer SQL-Datenbank**. Dieser Leitfaden beschreibt das Erstellen einer SQL-Datenbank im [Verwaltungsportal][management-portal]. Informationen zum Durchführen dieser Aufgaben im Produktionsportal finden Sie unter [Erste Schritte mit PHP und SQL-Datenbank][prod-portal-instructions]. Weitere Informationen finden Sie im Abschnitt [Nächste Schritte](#NextSteps).

##Was ist SQL-Datenbank?

SQL-Datenbank bietet ein Verwaltungssystem für relationale Datenbanken für Azure und basiert auf SQL Server-Technologie. Mit SQL-Datenbank können Sie relationale Datenbanklösungen für die Cloud bereitstellen und implementieren, und Sie profitieren von einem verteilten Datencenter, das Verfügbarkeit, Skalierbarkeit und Sicherheit für Unternehmen mit den Vorteilen von integriertem Datenschutz und Selbstreparatur bietet.

##<a id="Concepts"></a>Konzepte
Da SQL-Datenbank auf SQL Server-Technologien basiert, erfolgt der Zugriff auf SQL-Datenbank aus PHP ganz ähnlich wie der Zugriff auf SQL Server aus PHP. Sie können eine Anwendung lokal (mit SQL Server) entwickeln und dann eine Verbindung zu SQL-Datenbank herstellen, indem Sie lediglich die Verbindungszeichenfolge ändern. Es gibt jedoch einige Unterschiede zwischen SQL-Datenbank und SQL Server, die sich auf Ihre Anwendung auswirken können. Weitere Informationen finden Sie unter [Richtlinien für und Einschränkungen (SQL-Datenbanken)][limitations].

Der Zugriff auf SQL-Datenbank aus PHP sollte nach Möglichkeit unter Verwendung der [Microsoft-Treiber für PHP für SQL Server][download-drivers] erfolgen. (In den Beispielen dieses Artikels werden diese Treiber verwendet.) Die Microsoft-Treiber für PHP für SQL Server funktionieren nur unter Windows.

##<a id="Setup"></a>Vorgehensweise: Einrichten Ihrer Umgebung

Für die Einrichtung der Entwicklungsumgebung wird die Verwendung des [Microsoft-Webplattform-Installer][wpi-installer] empfohlen. Mit dem Webplattform-Installer können Sie Elemente Ihrer Webentwicklungsplattform auswählen und diese automatisch installieren und konfigurieren. Wenn Sie den Webplattform-Installer herunterladen und WebMatrix, PHP für WebMatrix und SQL Server Express zur Installation auswählen, wird eine vollständige Entwicklungsumgebung für Sie eingerichtet.

Alternativ können Sie die Umgebung auch manuell einrichten:

* Installieren von PHP und Konfigurieren von IIS: [http://php.net/manual/en/install.windows.iis7.php][manual-config].
* Herunterladen und Installieren von SQL Server Express: [http://www.microsoft.com/download/details.aspx?id=29062][install-sql-express]
* Laden Sie die [Microsoft-Treiber für PHP für SQL Server][download-drivers] herunter, und installieren Sie diese.

##<a id="CreateServer"></a>Vorgehensweise: Erstellen einer SQL-Datenbank

Führen Sie folgende Schritte durch, um eine Azure SQL-Datenbank zu erstellen:

1. Melden Sie sich am [Verwaltungsportal][management-portal] an.
2. Klicken Sie unten links im Portal auf **Neu**.

	![Neue Azure-Website erstellen][new-website]

3. Klicken Sie auf **DATENDIENSTE**, **SQL-DATENBANK** und dann auf **SCHNELLERFASSUNG**. Geben Sie einen Namen für die Datenbank ein, geben Sie an, ob ein vorhandener oder neuer Datenbankserver verwendet werden soll, und geben Sie eine Region, einen Administratornamen und ein Kennwort ein.

	![Eine neue SQL-Datenbank benutzerdefiniert erstellen][quick-create]


Klicken Sie im Verwaltungsportal auf **SQL-Datenbanken**, um Server- und Datenbankinformationen anzuzeigen. Sie können dann auf **DATENBANKEN** oder **SERVER** klicken, um die entsprechenden Informationen anzuzeigen.

![Server- und Datenbankinformationen anzeigen][sql-dbs-servers]

##<a id="ConnectionInfo"></a>Vorgehensweise: Abrufen von SQL-Datenbank-Verbindungsinformationen

Klicken Sie im Portal auf **SQL-DATENBANKEN** und dann auf den Namen der Datenbank, um SQL-Datenbank-Verbindungsinformationen abzurufen.

![Datenbankinformationen anzeigen][go-to-db-info]

Klicken Sie anschließend auf **SQL-Datenbank-Verbindungszeichenfolgen für ADO.NET, ODBC, PHP und JDBC anzeigen**.

![Verbindungszeichenfolgen anzeigen][show-connection-string]

Notieren Sie im PHP-Abschnitt des Ergebnisfensters die Werte für **SERVER**, **DATENBANK** und **BENUTZERNAME**. Ihr Kennwort ist das Kennwort, das Sie zum Erstellen der SQL-Datenbank verwendet haben.

##<a id="Connect"></a>Vorgehensweise: Herstellen einer Verbindung zu einer SQL-Datenbankinstanz

In den folgenden Beispielen wird gezeigt, wie Sie mit den Erweiterungen **SQLSRV** und **PDO_SQLSRV** eine Verbindung mit einer SQL-Datenbank namens `testdb` herstellen. Informationen zur **SQLSRV**- und **PDO_SQLSRV**-API finden Sie in der[Dokumentation für Microsoft-Treiber für PHP für SQL Server][driver-docs]. Sie benötigen die im vorstehenden Abschnitt abgerufenen Informationen. Ersetzen Sie `SERVER_ID` durch Ihre 10-stellige Server-ID (dies sind die ersten zehn Zeichen des im vorstehenden Abschnitt abgerufenen SERVER-Werts), und weisen Sie den Variablen `$user` und `$pwd` die entsprechenden Werte (Ihren Benutzernamen und Ihr Kennwort) zu.

#####SQLSRV

	$server = "tcp:<value of SERVER from section above>";
	$user = "<value of USERNAME from section above>"@SERVER_ID;
	$pwd = "password";
	$db = "testdb";

	$conn = sqlsrv_connect($server, array("UID"=>$user, "PWD"=>$pwd, "Database"=>$db));

	if($conn === false){
		die(print_r(sqlsrv_errors()));
	}

#####PDO_SQLSRV

	$server = "tcp:<value of SERVER from section above>";
	$user = "<value of USERNAME from section above>"@SERVER_ID;
	$pwd = "password";
	$db = "testdb";

	try{
		$conn = new PDO( "sqlsrv:Server= $server ; Database = $db ", $user, $pwd);
		$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
	}
	catch(Exception $e){
		die(print_r($e));
	}


##<a id="NextSteps"></a>Nächste Schritte
Wie bereits erwähnt, ähnelt die Verwendung von SQL-Datenbank sehr der Verwendung von SQL Server. Nachdem Sie eine Verbindung zu einer SQL-Datenbank hergestellt haben (wie oben beschrieben), können Sie die **SQLSRV**- oder **PDO_SQLSRV**-API zum Einfügen, Abrufen, Aktualisieren und Löschen von Daten verwenden. Es gibt jedoch einige Unterschiede zwischen SQL-Datenbank und SQL Server, die sich auf Ihre Anwendung auswirken können. Weitere Informationen finden Sie unter [Richtlinien für und Einschränkungen (SQL-Datenbanken)][limitations].

Ein Beispiel, das die Verwendung der SQL-Datenbank mit PHP auf Azure zeigt, ist auf <https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/tasklist-sqlazure> verfügbar.

[download-drivers]: http://www.microsoft.com/download/en/details.aspx?id=20098
[limitations]: http://msdn.microsoft.com/library/windowsazure/ff394102.aspx
[odbc-php]: http://www.php.net/odbc
[manual-config]: http://php.net/manual/en/install.windows.iis7.php
[install-drivers]: http://php.net/manual/en/sqlsrv.requirements.php
[driver-docs]: http://msdn.microsoft.com/library/dd638075(SQL.10).aspx
[access-php-odbc]: http://social.technet.microsoft.com/wiki/contents/articles/accessing-sql-azure-from-php.aspx
[install-sql-express]: http://www.microsoft.com/download/details.aspx?id=29062
[management-portal]: https://manage.windowsazure.com
[prod-portal-instructions]: http://blogs.msdn.com/b/brian_swan/archive/2010/02/12/getting-started-with-php-and-sql-azure.aspx
[new-website]: ./media/sql-database-php-how-to-use/plus-new.png
[custom-create]: ./media/sql-database-php-how-to-use-sql-database/create_custom_sql_db-2.png
[database-settings]: ./media/sql-database-php-how-to-use-sql-database/new-sql-db.png
[create-server]: ./media/sql-database-php-how-to-use-sql-database/db-server-settings.png
[sql-dbs-servers]: ./media/sql-database-php-how-to-use/sql-dbs-portal.png
[wpi-installer]: http://go.microsoft.com/fwlink/?LinkId=253447
[go-to-db-info]: ./media/sql-database-php-how-to-use/go-to-db-info.png
[show-connection-string]: ./media/sql-database-php-how-to-use/show-connection-string-2.png
[quick-create]: ./media/sql-database-php-how-to-use/create-new-sql.png
 

<!---HONumber=July15_HO4-->