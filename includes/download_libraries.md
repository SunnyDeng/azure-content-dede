#Herunterladen des Azure SDK für Java

##Azure-Clientbibliotheken für Java - manueller Download

Die Azure-Bibliotheken für Java sind unter [Apache-Lizenz Version 2.0][license] erhältlich. Klicken Sie [hier][zip-download], um eine ZIP-Datei der Bibliotheken und aller Abhängigkeiten zu erhalten.  Diese wird von Microsoft Open Technologies, Inc zur Verfügung gestellt. Informationen zur Lizenz und weitere Informationen finden Sie in den Dateien license.txt und ThirdPartyNotices.txt im ZIP.

##Azure-Bibliotheken für Java - Maven

Wenn Ihr Projekt bereits für die Verwendung von Maven für den Build-Prozess eingerichtet ist, fügen Sie die folgende Abhängigkeit zu Ihrer pom.xml-Datei hinzu:

	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-compute</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-network</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-sql</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-storage</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-websites</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-media</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-servicebus</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-serviceruntime</artifactId>
	    <version>n.n.n</version>
	</dependency>



Ersetzen Sie innerhalb des Elements `<version>`  *n.n.n* durch eine gültige Versionsnummer, die Sie im [Azure-Bibliothekenrepository auf Maven](http://go.microsoft.com/fwlink/?LinkID=286274) erhalten.

[license]: http://www.apache.org/licenses/LICENSE-2.0.html
[zip-download]:  http://go.microsoft.com/fwlink/?LinkId=253887

<!--HONumber=42-->
