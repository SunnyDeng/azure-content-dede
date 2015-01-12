### Installation mithilfe von Composer

1. [Git installieren][Git installieren].

	<div class="dev-callout">
	<b>Hinweis</b>
	<p>Unter Windows muss die ausführbare Git-Datei zu Ihrer PATH-Umgebungsvariable hinzugefügt werden.</p>
	</div>

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

### Manuelle Installation

Gehen Sie folgendermaßen vor, um die PHP-Client-Bibliotheken für Azure manuell herunterzuladen:

1.  Laden Sie von [GitHub][GitHub] eine Zip-Datei herunter, die die Bibliotheken enthält. Alternativ können Sie auch das Repository verzweigen und auf dem lokalen Computer klonen. (Für die zweite Option sind ein GitHub-Konto und eine lokale Installation von Git erforderlich.)

	<div class="dev-callout">
	<b>Hinweis</b>
	<p>Für die PHP-Client-Bibliotheken für Azure besteht eine Abhängigkeit zu den PEAR-Paketen <a href="http://pear.php.net/package/HTTP_Request2">HTTP\_Request2</a>, <a href="http://pear.php.net/package/Mail_mime">Mail\_mime</a> und <a href="http://pear.php.net/package/Mail_mimeDecode">Mail\_mimeDecode</a>. Die empfohlene Methode zum Auflösen dieser Abhängigkeiten ist die Installation dieser Pakete mithilfe des <a href="http://pear.php.net/manual/en/installation.php">PEAR-Package-Managers</a>.</p>
	</div>


2.  Kopieren Sie das Verzeichnis `WindowsAzure` der heruntergeladenen Zip-Datei in Ihre Anwendungsverzeichnisstruktur.

Weitere Informationen zur Installation der PHP-Client-Bibliotheken für Azure (einschließlich Informationen zur Installation eines PEAR-Pakets) finden Sie unter [Herunterladen des Azure SDK für PHP][Herunterladen des Azure SDK für PHP].


[Git installieren]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[composer.phar]: http://getcomposer.org/composer.phar
[GitHub]: http://go.microsoft.com/fwlink/?LinkId=252719
[Herunterladen des Azure SDK für PHP]: ../php-download-sdk/
