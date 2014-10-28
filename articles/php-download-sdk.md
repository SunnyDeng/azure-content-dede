<properties title="Download the Azure SDK for PHP" pageTitle="Download the Azure SDK for PHP" metaKeywords="" description="Learn how to download and install the Azure SDK for PHP." documentationCenter="PHP" services="" solutions="web" authors="robmcm" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm"></tags>

# Herunterladen des Azure SDK für PHP

Das Azure SDK für PHP enthält Komponenten, mit deren Hilfe Sie PHP-Anwendungen für Azure entwickeln, bereitstellen und verwalten können. insbesondere enthält das Azure SDK für PHP Folgendes:

-   **Die PHP-Clientbibliotheken für Azure**. Diese Klassenbibliotheken bieten eine Schnittstelle für den Zugriff auf Azure-Funktionen wie Datenverwaltungs- und Cloud-Dienste.
-   **Die Azure-Befehlszeilentools für Mac und Linux**. Dies ist eine Gruppe von Befehlszeilentools zum Bereitstellen und Verwalten von Azure-Diensten wie Azure-Websites und virtuellen Azure-Computern. Diese Tools arbeiten auf jeder Plattform einschließlich Mac, Linux und Windows.
-   **Azure PowerShell (nur Windows)**. Dies ist eine Gruppe von PowerShell-Cmdlets zum Bereitstellen und Verwalten von Azure-Diensten wie Clouddiensten und virtuellen Computern.
-   **Die Azure-Emulatoren (nur Windows)**. Die Rechen- und Speicheremulatoren sind lokale Emulatoren von Datenverwaltungs- und Clouddiensten, mit denen Sie eine Anwendung lokal testen können. Die Azure-Emulatoren laufen nur unter Windows.

In den Abschnitten unten wird erläutert, wie Sie die oben beschriebenen Komponenten herunterladen und installieren.

Für die Anweisungen in diesem Thema wird vorausgesetzt, dass Sie [PHP][PHP] installiert haben.

> [WACOM.NOTE]
> Sie müssen PHP 5.3 oder höher besitzen, um die PHP-Clientbibliotheken für Azure verwenden zu können.

## PHP-Clientbibliotheken für Azure

Die PHP Clientbibliotheken für Azure bieten eine Schnittstelle zum Zugriff auf Azure-Funktionen wie Datenverwaltungs- und Clouddienste auf jedem Betriebssystem. Die Bibliotheken können über die Paketmanager Composer oder PEAR oder manuell installiert werden.

Informationen über die Verwendung der PHP-Clientbibliotheken für Azure finden Sie unter [Verwenden des Blob-Dienstes][Verwenden des Blob-Dienstes], [Verwenden des Tabellendienstes][Verwenden des Tabellendienstes] und [Verwenden des Warteschlangendienstes][Verwenden des Warteschlangendienstes].

### Installation mithilfe von Composer

1.  [Git installieren][Git installieren].

    > [WACOM.NOTE]
    > Unter Windows muss auch die ausführbare Git-Datei zur PATH-Umgebungsvariable hinzugefügt werden.

2.  Erstellen Sie im Stammverzeichnis Ihres Projekts eine Datei namens **composer.json**, und fügen Sie zu dieser den folgenden Code hinzu:

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

3.  Laden Sie **[composer.phar][composer.phar]** in Ihr Projektverzeichnis herunter.

4.  Öffnen Sie eine Eingabeaufforderung, und führen Sie in Ihrem Projektverzeichnis folgenden Befehl aus

        php composer.phar install

### Installation als PEAR-Paket

Gehen Sie folgendermaßen vor, um die PHP-Clientbibliotheken für Azure als PEAR-Paket zu installieren:

1.  [PEAR installieren][PEAR installieren].
2.  Richten Sie den Azure-PEAR-Kanal ein:

        pear channel-discover pear.windowsazure.com

3.  Installieren Sie das PEAR-Paket:

        pear install pear.windowsazure.com/WindowsAzure-0.3.1

Nach dem Abschluss der Installation können Sie in Ihrer Anwendung einen Verweis auf die Klassenbibliotheken anlegen.

### Manuelle Installation

Gehen Sie folgendermaßen vor, um die PHP-Client-Bibliotheken für Azure manuell herunterzuladen:

1.  Laden Sie von [GitHub][GitHub] eine Zip-Datei herunter, die die Bibliotheken enthält. Alternativ können Sie auch das Repository verzweigen und auf dem lokalen Computer klonen. (Für die zweite Option sind ein GitHub-Konto und eine lokale Installation von Git erforderlich.)

    > [WACOM.NOTE]
    > Für die PHP-Clientbibliotheken für Azure besteht eine Abhängigkeit zu den PEAR-Paketen [HTTP\_Request2][HTTP\_Request2], [Mail\_mime][Mail\_mime] und [Mail\_mimeDecode][Mail\_mimeDecode]. Die empfohlene Methode zum Auflösen dieser Abhängigkeiten ist die Installation dieser Pakete mithilfe des [PEAR-Package-Managers][PEAR-Package-Managers].

2.  Kopieren Sie das Verzeichnis `WindowsAzure` der heruntergeladenen ZIP-Datei in die Anwendungsverzeichnisstruktur, und legen Sie in der Anwendung einen Verweis auf die Klassenbibliotheken an.

## Azure PowerShell und Azure-Emulatoren

Azure PowerShell ist eine Gruppe von PowerShell-Cmdlets zum Bereitstellen und Verwalten von Azure-Diensten (wie Clouddiensten und virtuellen Computern). Die Azure-Emulatoren sind Emulatoren von Clouddiensten und Datenverwaltungsdiensten, mit denen Sie eine Anwendung lokal testen können. Diese Komponenten werden nur unter Windows unterstützt.

Die empfohlene Methode zur Installation von Azure PowerShell sowie der Azure-Emulatoren ist die Verwendung des [Microsoft-Webplattform-Installers][Microsoft-Webplattform-Installers]. Bitte beachten Sie, dass Sie auch noch weitere Entwicklungskomponenten wie PHP, SQL Server, die Microsoft-Treiber für SQL Server für PHP und WebMatrix installieren können.

Informationen zur Verwendung von Azure finden Sie unter [Verwenden von Azure PowerShell][Verwenden von Azure PowerShell].

## Azure-Befehlszeilen-Tools für Mac und Linux

Die Azure-Befehlszeilentools für Mac und Linux ermöglichen die Bereitstellung und Verwaltung von Azure-Diensten wie Azure-Websites und virtuellen Azure-Computern. In der folgenden Liste wird beschrieben, wie die Tools abhängig von Ihrem Betriebssystem installiert werden:

-   **Mac**: Laden Sie den Azure SDK Installer hier herunter: [][]<http://go.microsoft.com/fwlink/?LinkId=252249></a>. Öffnen Sie die heruntergeladene PKG-Datei, und führen Sie die Installationsschritte durch, zu denen Sie aufgefordert werden.

-   **Linux**: Installieren Sie die neueste Version von [Node.js][Node.js] (siehe [Install Node.js via Package Manager][Install Node.js via Package Manager] (Installieren von Node.js mithilfe des Paket-Managers, in englischer Sprache), und führen Sie dann den folgenden Befehl aus:

        npm install azure-cli -g

    > [WACOM.NOTE]
    > Möglicherweise müssen Sie diesen Befehl mit erhöhten Rechten ausführen: `sudo npm install azure-cli -g`

Informationen zur Verwendung der Azure-Befehlszeilen-Tools für Max und Linux erhalten Sie unter [Verwenden der Azure-Befehlszeilentools für Mac und Linux][Verwenden der Azure-Befehlszeilentools für Mac und Linux].

  [PHP]: http://www.php.net/manual/en/install.php
  [Verwenden des Blob-Dienstes]: http://go.microsoft.com/fwlink/?LinkId=252714
  [Verwenden des Tabellendienstes]: http://go.microsoft.com/fwlink/?LinkId=252715
  [Verwenden des Warteschlangendienstes]: http://go.microsoft.com/fwlink/?LinkId=252716
  [Git installieren]: http://git-scm.com/book/en/Getting-Started-Installing-Git
  [composer.phar]: http://getcomposer.org/composer.phar
  [PEAR installieren]: http://pear.php.net/manual/en/installation.getting.php
  [GitHub]: http://go.microsoft.com/fwlink/?LinkId=252719
  [HTTP\_Request2]: http://pear.php.net/package/HTTP_Request2
  [Mail\_mime]: http://pear.php.net/package/Mail_mime
  [Mail\_mimeDecode]: http://pear.php.net/package/Mail_mimeDecode
  [PEAR-Package-Managers]: http://pear.php.net/manual/en/installation.php
  [Microsoft-Webplattform-Installers]: http://go.microsoft.com/fwlink/?LinkId=253447
  [Verwenden von Azure PowerShell]: http://go.microsoft.com/fwlink/?LinkId=252718
  []: http://go.microsoft.com/fwlink/?LinkId=252249
  [Node.js]: http://nodejs.org/
  [Install Node.js via Package Manager]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
  [Verwenden der Azure-Befehlszeilentools für Mac und Linux]: http://go.microsoft.com/fwlink/?LinkId=252717
