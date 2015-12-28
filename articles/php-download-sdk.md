<properties
	pageTitle="Herunterladen des Azure SDK für PHP"
	description="Erfahren Sie mehr über das Herunterladen und Installieren von Azure SDK für PHP."
	documentationCenter="php"
	services="app-service\web"
	authors="tfitzmac"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="PHP"
	ms.topic="article"
	ms.date="12/16/2015"
	ms.author="tomfitz"/>

#Herunterladen des Azure SDK für PHP

## Übersicht

Das Azure SDK für PHP enthält Komponenten, mit deren Hilfe Sie PHP-Anwendungen für Azure entwickeln, bereitstellen und verwalten können. insbesondere enthält das Azure SDK für PHP Folgendes:

* **Die PHP-Clientbibliotheken für Azure**. Diese Basisklassenbibliotheken bieten eine Schnittstelle für den Zugriff auf Azure-Funktionen wie Datenverwaltungs- und Clouddienste.  
* **Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows (Azure CLI)** Dies ist eine Gruppe von Befehlen zum Bereitstellen und Verwalten von Azure-Diensten wie Azure-Websites und virtuellen Azure-Computern. Die Azure-Befehlszeilenschnittstelle funktioniert auf allen Plattformen, z. B. Mac, Linux und Windows.
* **Azure PowerShell (nur Windows)**. Dies ist eine Gruppe von PowerShell-Cmdlets zum Bereitstellen und Verwalten von Azure-Diensten wie Clouddiensten und virtuellen Computern.
* **Die Azure-Emulatoren (nur Windows)**. Die Rechen- und Speicheremulatoren sind lokale Emulatoren von Datenverwaltungs- und Clouddiensten, mit denen Sie eine Anwendung lokal testen können. Die Azure-Emulatoren laufen nur unter Windows.

In den Abschnitten unten wird erläutert, wie Sie die oben beschriebenen Komponenten herunterladen und installieren.

Für die Anweisungen in diesem Thema wird vorausgesetzt, dass Sie [PHP][install-php] installiert haben.

> [AZURE.NOTE]Sie müssen PHP 5.3 oder höher besitzen, um die PHP-Clientbibliotheken für Azure verwenden zu können.

##PHP-Clientbibliotheken für Azure

Die PHP Clientbibliotheken für Azure bieten eine Schnittstelle zum Zugriff auf Azure-Funktionen wie Datenverwaltungs- und Clouddienste auf jedem Betriebssystem. Die Bibliotheken können über die Paketmanager Composer oder PEAR oder manuell installiert werden.

Informationen über die Verwendung der PHP-Clientbibliotheken für Azure finden Sie unter [Verwenden des Blob-Dienstes][blob-service], [Verwenden des Tabellendienstes][table-service] und [Verwenden des Warteschlangendienstes][queue-service].

###Installation mithilfe von Composer

1. [Git installieren][install-git].


	> [AZURE.NOTE]Unter Windows muss die ausführbare Git-Datei zu Ihrer PATH-Umgebungsvariable hinzugefügt werden.

2. Erstellen Sie im Stammverzeichnis Ihres Projekts eine Datei namens **composer.json**, und fügen Sie zu dieser den folgenden Code hinzu:

        {
            "repositories": [
                {
                    "type": "pear",
                    "url": "http://pear.php.net"
                }
            ],
            "require": {
                "pear-pear.php.net/mail_mime" : "*",
                "pear-pear.php.net/http_request2" : "*",
                "pear-pear.php.net/mail_mimedecode" : "*",
                "microsoft/windowsazure": "*"
            }
        }

3. Laden Sie **[composer.phar][composer-phar]** in Ihr Projektverzeichnis herunter.

4. Öffnen Sie eine Eingabeaufforderung, und führen Sie in Ihrem Projektverzeichnis folgenden Befehl aus

		php composer.phar install

###Installation als PEAR-Paket

Gehen Sie folgendermaßen vor, um die PHP-Clientbibliotheken für Azure als PEAR-Paket zu installieren:

1. [PEAR installieren][install-pear].
2. Richten Sie den Azure-PEAR-Kanal ein:

		pear channel-discover pear.windowsazure.com
3. Installieren Sie das PEAR-Paket:

		pear install pear.windowsazure.com/WindowsAzure-0.4.0

Nach dem Abschluss der Installation können Sie in Ihrer Anwendung einen Verweis auf die Klassenbibliotheken anlegen.

###Manuelle Installation

Gehen Sie folgendermaßen vor, um die PHP-Client-Bibliotheken für Azure manuell herunterzuladen:

1. Laden Sie von [GitHub][php-sdk-github] eine Zip-Datei herunter, die die Bibliotheken enthält. Alternativ können Sie auch das Repository verzweigen und auf dem lokalen Computer klonen. (Für die zweite Option sind ein GitHub-Konto und eine lokale Installation von Git erforderlich.)

	> [AZURE.NOTE]Für die PHP-Client-Bibliotheken für Azure besteht eine Abhängigkeit zu den PEAR-Paketen [HTTP\_Request2](http://pear.php.net/package/HTTP_Request2), [Mail\_mime](http://pear.php.net/package/Mail_mime) und [Mail\_mimeDecode](http://pear.php.net/package/Mail_mimeDecode). Die empfohlene Methode zum Auflösen dieser Abhängigkeiten ist die Installation dieser Pakete mithilfe des [PEAR-Package-Managers](http://pear.php.net/manual/en/installation.php).

2. Kopieren Sie das Verzeichnis `WindowsAzure` der heruntergeladenen ZIP-Datei in die Anwendungsverzeichnisstruktur, und legen Sie in der Anwendung einen Verweis auf die Klassenbibliotheken an.

##Azure PowerShell und Azure-Emulatoren

Azure PowerShell ist eine Gruppe von PowerShell-Cmdlets zum Bereitstellen und Verwalten von Azure-Diensten (wie Clouddiensten und virtuellen Computern). Die Azure-Emulatoren sind Emulatoren von Clouddiensten und Datenverwaltungsdiensten, mit denen Sie eine Anwendung lokal testen können. Diese Komponenten werden nur unter Windows unterstützt.

Die empfohlene Methode zur Installation von Azure PowerShell sowie der Azure-Emulatoren ist die Verwendung des [Microsoft-Webplattform-Installers][download-wpi]. Bitte beachten Sie, dass Sie auch noch weitere Entwicklungskomponenten wie PHP, SQL Server, die Microsoft-Treiber für SQL Server für PHP und WebMatrix installieren können.

Informationen zur Verwendung von Azure finden Sie unter [Verwenden von Azure PowerShell][powershell-tools].

##Azure-Befehlszeilenschnittstelle

Die Azure-Befehlszeilenschnittstelle ist eine Gruppe von Befehlen zum Bereitstellen und Verwalten von Azure-Diensten wie Azure-Websites und virtuellen Azure-Computern. Informationen zum Installieren der Azure-Befehlszeilenschnittstelle finden Sie unter [Installieren der Azure-Befehlszeilenschnittstelle](xplat-cli-install.md).

## Nächste Schritte

Weitere Informationen finden Sie im [PHP Developer Center](/develop/php/).


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
[azure cli]: http://go.microsoft.com/fwlink/?LinkId=252717
[powershell-tools]: http://go.microsoft.com/fwlink/?LinkId=252718
[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git

<!---HONumber=AcomDC_1217_2015-->