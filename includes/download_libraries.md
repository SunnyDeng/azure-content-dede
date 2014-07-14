
# Herunterladen des Azure SDK für Java

## Azure-Clientbibliotheken für Java -- manueller Download

Die Azure-Bibliotheken für Java sind unter [Apache-Lizenz Version 2.0][1] erhältlich. Klicken Sie [hier][2], um eine ZIP-Datei der Bibliotheken und aller Abhängigkeiten zu erhalten. Diese wird von Microsoft Open Technologies, Inc zur Verfügung gestellt. Informationen zur Lizenz und weitere Informationen finden Sie in den Dateien license.txt und ThirdPartyNotices.txt im ZIP.

## Azure-Bibliotheken für Java -- Maven

Wenn Ihr Projekt bereits für die Verwendung von Maven für den Build-Prozess eingerichtet ist, fügen Sie die folgende Abhängigkeit zu Ihrer pom.xml-Datei hinzu:

    <dependency>
    	<groupId>com.microsoft.windowsazure</groupId>
    	<a rtifactId>microsoft-windowsazure-api</artifactId>
    	<version>n.n.n</version>
    </dependency>

Ersetzen Sie innerhalb des Elements `<version>`
*n.n.n* mit einer gültigen Versionsnummer, die Sie unter [Azure
Libraries Repository on Maven][3] (Azure-Bibliotheken-Repository auf Maven) erhalten.



[1]: http://www.apache.org/licenses/LICENSE-2.0.html
[2]: http://go.microsoft.com/fwlink/?LinkId=253887
[3]: http://go.microsoft.com/fwlink/?LinkID=286274