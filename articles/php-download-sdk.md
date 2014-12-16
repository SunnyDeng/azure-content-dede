<properties title="Download the Azure SDK for PHP" pageTitle="Herunterladen des Azure SDK für PHP" metaKeywords="" description="Learn how to download and install the Azure SDK for PHP." documentationCenter="PHP" services="" solutions="web" authors="tomfitz" manager="wpickett" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="10/21/2014" ms.author="tomfitz" />

#Herunterladen des Azure SDK für PHP

Das Azure SDK für PHP enthält Komponenten, mit deren Hilfe Sie PHP-Anwendungen für Azure entwickeln, bereitstellen und verwalten können. insbesondere enthält das Azure SDK für PHP Folgendes:

* **Die PHP-Clientbibliotheken für Azure**. Diese Basisklassenbibliotheken bieten eine Schnittstelle für den Zugriff auf Azure-Funktionen wie Datenverwaltungs- und Cloud-Dienste.  
* **Die Azure-Befehlszeilentools für Mac und Linux**. Dies ist eine Gruppe von Befehlszeilentools zum Bereitstellen und Verwalten von Azure-Diensten wie Azure-Websites und virtuellen Azure-Computern. Diese Tools arbeiten auf jeder Plattform einschließlich Mac, Linux und Windows.
* **Azure PowerShell (nur Windows)**. Dies ist eine Gruppe von PowerShell-Cmdlets zum Bereitstellen und Verwalten von Azure-Diensten wie Cloud-Diensten und virtuellen Computern.
* **Die Azure-Emulatoren (nur Windows)**. Die Rechen- und Speicheremulatoren sind lokale Emulatoren von Datenverwaltungs- und Cloud-Diensten, mit denen Sie eine Anwendung lokal testen können. Die Azure-Emulatoren laufen nur unter Windows.

In den Abschnitten unten wird erläutert, wie Sie die oben beschriebenen Komponenten herunterladen und installieren. 

Für die Anweisungen in diesem Thema wird vorausgesetzt, dass Sie [PHP][install-php] installiert haben.

> [WACOM.NOTE] 
> Sie müssen PHP 5.3 oder höher besitzen, um die PHP-Clientbibliotheken für Azure verwenden zu können. 

##PHP-Clientbibliotheken für Azure

Die PHP Clientbibliotheken für Azure bieten eine Schnittstelle zum Zugriff auf Azure-Funktionen wie Datenverwaltungs- und Cloud-Dienste auf jedem Betriebssystem. Die Bibliotheken können über die Paketmanager Composer oder PEAR oder manuell installiert werden.

Informationen über die Verwendung der PHP-Clientbibliotheken für Azure finden Sie unter[Verwenden des Blob-Dienstes][blob-service], [Verwenden des Tabellendienstes][table-service] und [Verwenden des Warteschlangendienstes][queue-service].

###Installation mithilfe von Composer

1. [Git installieren][install-git]. 


	> [WACOM.NOTE] 
	> Unter Windows muss die ausführbare Git-Datei zu Ihrer PATH-Umgebungsvariable hinzugefügt werden.

2. Erstellen Sie im Stammverzeichnis Ihres Projekts eine Datei namens **composer.json**, und fügen Sie zu dieser den folgenden Code hinzu:

		{
			"require": {
				"microsoft/windowsazure": "*"
			},			
			"repositories": [
				{
					"type": "pear",
					"url": "http://pear.php.net"
				}
			],
			"minimum-stability": "dev"
		}

3. Laden Sie **[composer.phar][composer-phar]** in Ihr Projektverzeichnis herunter.

4. Öffnen Sie eine Eingabeaufforderung, und führen Sie in Ihrem Projektverzeichnis folgenden Befehl aus

		php composer.phar install

###Installation als PEAR-Paket

Gehen Sie folgendermaßen vor, um die PHP-Clientbibliotheken für Azure als PEAR-Paket zu installieren:

1. [Installieren Sie PEAR][install-pear].
2. Richten Sie den Azure-PEAR-Kanal ein:

		pear channel-discover pear.windowsazure.com
3. Installieren Sie das PEAR-Paket:

		pear install pear.windowsazure.com/WindowsAzure-0.4.0

Nach dem Abschluss der Installation können Sie in Ihrer Anwendung einen Verweis auf die Klassenbibliotheken anlegen.

###Manuelle Installation

Gehen Sie folgendermaßen vor, um die PHP-Client-Bibliotheken für Azure manuell herunterzuladen:

1. Laden Sie von [GitHub][php-sdk-github] eine Zip-Datei herunter, die die Bibliotheken enthält. Alternativ können Sie auch das Repository verzweigen und auf dem lokalen Computer klonen. (Für die zweite Option sind ein GitHub-Konto und eine lokale Installation von Git erforderlich.)

	> [WACOM.NOTE] 
	> Für die PHP-Clientbibliotheken für Azure besteht eine Abhängigkeit zu den PEAR-Paketen [HTTP_Request2](http://pear.php.net/package/HTTP_Request2), [Mail_mime](http://pear.php.net/package/Mail_mime) und [Mail_mimeDecode](http://pear.php.net/package/Mail_mimeDecode). Die empfohlene Methode zum Auflösen dieser Abhängigkeiten ist die Installation dieser Pakete mithilfe des [PEAR-Package-Managers](http://pear.php.net/manual/en/installation.php).

2. Kopieren Sie das Verzeichnis "WindowsAzure" der heruntergeladenen ZIP-Datei in die Anwendungsverzeichnisstruktur, und legen Sie in der Anwendung einen Verweis auf die Klassenbibliotheken an.

##Azure PowerShell und Azure-Emulatoren

Azure PowerShell ist eine Gruppe von PowerShell-Cmdlets zum Bereitstellen und Verwalten von Azure-Diensten (wie Cloud-Diensten und virtuellen Computern). Die Azure-Emulatoren sind Emulatoren von Cloud-Diensten und Datenverwaltungsdiensten, mit denen Sie eine Anwendung lokal testen können. Diese Komponenten werden nur unter Windows unterstützt.

Die empfohlene Methode zur Installation von Azure PowerShell sowie der Azure-Emulatoren ist die Verwendung des [Microsoft-Webplattform-Installers][download-wpi]. Bitte beachten Sie, dass Sie auch noch weitere Entwicklungskomponenten wie PHP, SQL Server, die Microsoft-Treiber für SQL Server für PHP und WebMatrix installieren können.

Informationen zur Verwendung von Azure finden Sie unter [Verwenden von Azure PowerShell][powershell-tools].

##Azure-Befehlszeilen-Tools für Mac und Linux

Die Azure-Befehlszeilentools für Mac und Linux ermöglichen die Bereitstellung und Verwaltung von Azure-Diensten wie Azure-Websites und virtuellen Azure-Computern. In der folgenden Liste wird beschrieben, wie die Tools abhängig von Ihrem Betriebssystem installiert werden:

* **Mac**: Laden Sie den Azure SDK Installer hier herunter: [http://go.microsoft.com/fwlink/?LinkId=252249][mac-installer]. Öffnen Sie die heruntergeladene PKG-Datei, und führen Sie die Installationsschritte durch, zu denen Sie aufgefordert werden.

* **Linux**: Installieren Sie die neueste Version von [Node.js][nodejs-org] (siehe [Install Node.js via Package Manager][install-node-linux] (Installieren von Node.js mithilfe des Paket-Managers, in englischer Sprache)), und führen Sie dann den folgenden Befehl aus:

		npm install azure-cli -g

	> [WACOM.NOTE] 
	> Möglicherweise müssen Sie diesen Befehl mit erhöhten Rechten ausführen:  `sudo npm install azure-cli -g`


Informationen zur Verwendung der Azure-Befehlszeilen-Tools für Mac und Linux erhalten Sie unter [Verwenden der Azure-Befehlszeilentools für Mac und Linux][crossplat-tools].

[install-php]: http://www.php.net/manual/en/install.php
[composer-github]: https://github.com/composer/composer
[composer-phar]: http://getcomposer.org/composer.phar
[pear-net]: http://pear.php.net/
[http-request2-package]: http://pear.php.net/package/HTTP_Request2
[mail-mimedecode-package]: http://pear.php.net/package/Mail_mimeDecode
[mail-mime-package]: http://pear.php.net/package/Mail_mime
[install-pear]: http://pear.php.net/manual/en/installation.getting.php
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[download-wpi]: http://go.microsoft.com/fwlink/?LinkId=253447
[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[blob-service]: http://go.microsoft.com/fwlink/?LinkId=252714
[table-service]: http://go.microsoft.com/fwlink/?LinkId=252715
[queue-service]: http://go.microsoft.com/fwlink/?LinkId=252716
[crossplat-tools]: http://go.microsoft.com/fwlink/?LinkId=252717
[powershell-tools]: http://go.microsoft.com/fwlink/?LinkId=252718
[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
