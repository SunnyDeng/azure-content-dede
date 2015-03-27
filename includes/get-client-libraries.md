Installation mithilfe von Composer

1. [Installieren von Git][install-git]. 

 
	> [AZURE.NOTE] Unter Windows muss die ausführbare Git-Datei zu Ihrer PATH-Umgebungsvariable hinzugefügt werden.

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

### Manuelle Installation

Gehen Sie folgendermaßen vor, um die PHP-Client-Bibliotheken für Azure manuell herunterzuladen:

1. Laden Sie von [GitHub][php-sdk-github] eine Zip-Datei herunter, die die Bibliotheken enthält. Alternativ können Sie auch das Repository verzweigen und auf dem lokalen Computer klonen. (Für die zweite Option sind ein GitHub-Konto und eine lokale Installation von Git erforderlich.)

	
	> [AZURE.NOTE] Für die PHP-Client-Bibliotheken für Azure besteht eine Abhängigkeit zu den PEAR-Paketen [HTTP_Request2](http://pear.php.net/package/HTTP_Request2), [Mail_mime](http://pear.php.net/package/Mail_mime) und [Mail_mimeDecode](http://pear.php.net/package/Mail_mimeDecode). Die empfohlene Methode zum Auflösen dieser Abhängigkeiten ist die Installation dieser Pakete mithilfe des [PEAR-Package-Managers](http://pear.php.net/manual/en/installation.php).


2. Kopieren Sie das Verzeichnis `WindowsAzure` der heruntergeladenen ZIP-Datei in Ihre Anwendungsverzeichnisstruktur.

Weitere Informationen zur Installation der PHP-Client-Bibliotheken für Azure (einschließlich Informationen zur Installation eines PEAR-Pakets) finden Sie unter [Herunterladen des Azure SDK für PHP][download-SDK-PHP].


[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: ../php-download-sdk/
[composer-phar]: http://getcomposer.org/composer.phar

<!--HONumber=47-->
