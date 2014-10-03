### Installation mithilfe von Composer

1.  [Git installieren][].

    <div class="dev-callout"> 
<b>Hinweis</b> 
<p>Unter Windows muss die ausf&uuml;hrbare Git-Datei zu Ihrer PATH-Umgebungsvariable hinzugef&uuml;gt werden.</p>
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

3.  Laden Sie **[composer.phar][]** in Ihr Projektverzeichnis herunter.

4.  Öffnen Sie eine Eingabeaufforderung, und führen Sie in Ihrem Projektverzeichnis folgenden Befehl aus

        php composer.phar install

### Manuelle Installation

Gehen Sie folgendermaßen vor, um die PHP-Client-Bibliotheken für Azure manuell herunterzuladen:

1.  Laden Sie von [GitHub][] eine Zip-Datei herunter, die die Bibliotheken enthält. Alternativ können Sie auch das Repository verzweigen und auf dem lokalen Computer klonen. (Für die zweite Option sind ein GitHub-Konto und eine lokale Installation von Git erforderlich.)

    <div class="dev-callout"> 
<b>Hinweis</b> 
<p>F&uuml;r die PHP-Client-Bibliotheken f&uuml;r Azure besteht eine Abh&auml;ngigkeit zu den PEAR-Paketen <a href="http://pear.php.net/package/HTTP_Request2">HTTP_Request2</a>, <a href="http://pear.php.net/package/Mail_mime">Mail_mime</a> und <a href="http://pear.php.net/package/Mail_mimeDecode">Mail_mimeDecode</a>. Die empfohlene Methode zum Aufl&ouml;sen dieser Abh&auml;ngigkeiten ist die Installation dieser Pakete mithilfe des <a href="http://pear.php.net/manual/en/installation.php">PEAR-Package-Managers</a>.</p> 
</div>

2.  Kopieren Sie das Verzeichnis `WindowsAzure` der heruntergeladenen Zip-Datei in Ihre Anwendungsverzeichnisstruktur.

Weitere Informationen zur Installation der PHP-Client-Bibliotheken für Azure (einschließlich Informationen zur Installation eines PEAR-Pakets) finden Sie unter [Herunterladen des Azure SDK für PHP][].

  [Git installieren]: http://git-scm.com/book/en/Getting-Started-Installing-Git
  [composer.phar]: http://getcomposer.org/composer.phar
  [GitHub]: http://go.microsoft.com/fwlink/?LinkId=252719
  [HTTP\_Request2]: http://pear.php.net/package/HTTP_Request2
  [Mail\_mime]: http://pear.php.net/package/Mail_mime
  [Mail\_mimeDecode]: http://pear.php.net/package/Mail_mimeDecode
  [PEAR-Package-Managers]: http://pear.php.net/manual/en/installation.php
  [Herunterladen des Azure SDK für PHP]: ../php-download-sdk/
