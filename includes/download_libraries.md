## Azure-Clientbibliotheken für Java – manueller Download

Die Azure-Bibliotheken für Java sind unter [Apache-Lizenz Version 2.0][license] erhältlich. Klicken Sie [hier][zip-download], um eine ZIP-Datei der Bibliotheken und aller Abhängigkeiten zu erhalten. Diese wird von Microsoft Open Technologies, Inc zur Verfügung gestellt. Informationen zur Lizenz und weitere Informationen finden Sie in den Dateien license.txt und ThirdPartyNotices.txt im ZIP.

## Azure-Bibliotheken für Java – Maven

Wenn Ihr Projekt bereits für die Verwendung von Maven für den Build-Prozess eingerichtet ist, fügen Sie die folgende Abhängigkeit zu Ihrer pom.xml-Datei hinzu:

	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management</artifactId>
	    <version>0.7.0</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-compute</artifactId>
	    <version>0.7.0</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-network</artifactId>
	    <version>0.7.0</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-sql</artifactId>
	    <version>0.7.0</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-storage</artifactId>
	    <version>0.7.0</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-websites</artifactId>
	    <version>0.7.0</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-media</artifactId>
	    <version>0.6.0</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-servicebus</artifactId>
	    <version>0.7.0</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-serviceruntime</artifactId>
	    <version>0.6.0</version>
	</dependency>


Ersetzen Sie die Versionsnummern aus diesem Beispiel innerhalb des Elements `<version>` mit gültigen Versionsnummern, die Sie im [Azure Libraries Repository on Maven](http://go.microsoft.com/fwlink/?LinkID=286274) (in englischer Sprache) erhalten.

[license]: http://www.apache.org/licenses/LICENSE-2.0.html
[zip-download]: http://go.microsoft.com/fwlink/?LinkId=253887

<!---HONumber=July15_HO2-->