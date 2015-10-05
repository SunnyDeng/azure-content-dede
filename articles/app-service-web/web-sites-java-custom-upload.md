<properties 
	pageTitle="Hochladen einer benutzerdefinierten Java Web-App in Azure" 
	description="In diesem Tutorial erfahren Sie, wie Sie eine benutzerdefinierte Java Web-App in Azure App Service-Web-Apps hochladen." 
	services="app-service\web" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="08/31/2015" 
	ms.author="robmcm"/>

# Hochladen einer benutzerdefinierten Java Web-App in Azure

In diesem Thema wird erläutert, wie Sie eine benutzerdefinierte Java Web-App in [Azure App Service] Web-Apps hochladen. Es enthält Informationen, die für alle Java-Websites oder Web-Apps gelten, und einige Beispiele für spezifische Anwendungen.

Beachten Sie, dass Azure die Möglichkeit bietet, Java Web-Apps mithilfe der Konfigurationsbenutzeroberfläche des Azure-Vorschauportals und des Azure- Marketplace zu erstellen. Informationen hierzu finden Sie unter [Erstellen einer Java Web-App in Azure App Service](web-sites-java-get-started.md). Dieses Tutorial eignet sich für Szenarien, in denen Sie nicht die Portal-Konfigurationsbenutzeroberfläche oder Azure Marketplace verwenden möchten.

## Konfigurationsrichtlinien

Im Folgenden werden die Einstellungen beschrieben, die für benutzerdefinierte Java Web-Apps auf Azure erwartet werden.

- Der vom Java-Prozess verwendete HTTP-Port wird dynamisch zugewiesen. Der Prozess muss den Port der Umgebungsvariable `HTTP_PLATFORM_PORT` verwenden.
- Bis auf den einzelnen HTTP-Listener sollten alle Überwachungsports deaktiviert sein. In Tomcat umfassen diese die Shutdown-, HTTPS- und AJP-Ports.
- Der Container darf nur für IPv4-Verkehr konfiguriert sein.
- Der Befehl für den **Start** der Anwendung muss in der Konfiguration festgelegt sein.
- Anwendungen, die Verzeichnisse mit Schreibberechtigungen benötigen, müssen im Inhaltsverzeichnis der Azure-Web-App abgelegt werden, d.h. unter **D:\\home**. Die Umgebungsvariable `HOME` verweist auf "D:\\home".  

Sie können Umgebungsvariablen wie in der web.config-Datei erforderlich festlegen.

## httpPlatform-Konfiguration mit web.config

Im Folgenden wird das Format **httpPlatform** innerhalb der web.config beschrieben.
                                 
**arguments** (Default=""). Argumente für das ausführbare Programm oder das Skript, die in der Einstellung **processPath** festgelegt sind.

Beispiele (mit **processPath**):

    processPath="%HOME%\site\wwwroot\bin\tomcat\bin\catalina.bat"
    arguments="start"
    
    processPath="%JAVA_HOME\bin\java.exe"
    arguments="-Djava.net.preferIPv4Stack=true -Djetty.port=%HTTP\_PLATFORM\_PORT% -Djetty.base=";%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115"; -jar ";%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115\start.jar";"


**processPath** - Pfad zum ausführbaren Programm oder Skript, das einen Prozess startet, der HTTP-Anfragen abhört.

Beispiele:


    processPath="%JAVA_HOME%\bin\java.exe"

    processPath="%HOME%\site\wwwroot\bin\tomcat\bin\startup.bat"

    processPath="%HOME%\site\wwwroot\bin\tomcat\bin\catalina.bat"
                                                                                       
**rapidFailsPerMinute** (Default=10.) Gibt an, wie oft der unter **processPath** festgelegte Prozess pro Minute abstürzen darf. Falls diese Grenze überschritten wird, startet **HttpPlatformHandler** diesen Prozess innerhalb der verbleibenden Zeit der Minute nicht mehr.
                                    
**requestTimeout** (Default="00:02:00".) Gibt an, wie lange **HttpPlatformHandler** auf eine Antwort von dem Prozess wartet, der `%HTTP_PLATFORM_PORT%` abhört.

**startupRetryCount** (Default=10.) Gibt an, wie oft **HttpPlatformHandler** versuchen wird, den unter **processPath** angegebenen Prozess zu starten. Weitere Details erhalten Sie unter **startupTimeLimit**.

**startupTimeLimit** (Default=10 seconds.) Gibt an, wie lange **HttpPlatformHandler** wartet, bis das ausführbare Programm/Skript einen Prozess zum Abhören des Ports startet. Wenn diese Zeitbeschränkung überschritten wird, beendet **HttpPlatformHandler** den Prozess und versucht, ihn so oft erneut zu starten, wie unter **startupRetryCount** angegeben.
                                                                                      
**stdoutLogEnabled** (Default="true".) Falls dies zutrifft, werden **stdout** und **stderr**, die für den Prozess in der Einstellung **processPath** festgelegt sind, an die Datei weitergeleitet, die unter **stdoutLogFile** angegeben ist (siehe Abschnitt **stdoutLogFile**).
                                    
**stdoutLogFile** (Default="d:\\home\\LogFiles\\httpPlatformStdout.log".) Absoluter Dateipfad, für den **stdout** und **stderr** von dem unter **processPath** angegebenen Prozess protokolliert werden.
                                    
> [AZURE.NOTE]`%HTTP_PLATFORM_PORT%` ist ein spezieller Platzhalter, der entweder als Teil von **arguments** oder als Teil der Liste **httpPlatform** **environmentVariables** angegeben wird. Dieser wird von **HttpPlatformHandler** durch einen intern generierten Port ersetzt, damit der von **processPath** festgelegte Prozess diesen Port abhören kann.

## Bereitstellung

Java-basierte Web-Apps können problemlos auf die gleiche Art und Weise bereitgestellt werden wie Internet Information Services (IIS)-basierte Webanwendungen. FTP, Git und Kudu werden ebenso als Bereitstellungsmechanismen unterstützt wie die integrierte SCM-Funktion für Web-Apps. WebDeploy fungiert als Protokoll. Da Java jedoch nicht in Visual Studio entwickelt wird, lässt sich WebDeploy nicht für Java Web-App-Bereitstellungen verwenden.

## Beispiele für die Anwendungskonfiguration

Für die folgenden Anwendungen werden eine web.config-Datei und die Anwendungskonfiguration als Beispiele herangezogen, um zu zeigen, wie Sie Ihre Java-Anwendung in App Service-Web-Apps aktivieren.

### Tomcat
Es gibt zwei Variationen von Tomcat, die mit App Service-Web-Apps bereitgestellt werden. Es können jedoch auch weiterhin benutzerspezifische Instanzen hochgeladen werden. Im Folgenden finden Sie ein Beispiel einer Tomcat-Installation mit einer anderen Java Virtual Machine (JVM).

	<?xml version="1.0" encoding="UTF-8"?>
	<configuration>
	  <system.webServer>
	    <handlers>
	      <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
	    </handlers>
	    <httpPlatform processPath="%HOME%\site\wwwroot\bin\tomcat\bin\startup.bat" 
	        arguments="">
	      <environmentVariables>
	        <environmentVariable name="CATALINA_OPTS" value="-Dport.http=%HTTP_PLATFORM_PORT%" />
	        <environmentVariable name="CATALINA_HOME" value="%HOME%\site\wwwroot\bin\tomcat" />
	        <environmentVariable name="JRE_HOME" value="%HOME%\site\wwwroot\bin\java" /> <!-- optional, if not specified, this will default to %programfiles%\Java -->
	        <environmentVariable name="JAVA_OPTS" value="-Djava.net.preferIPv4Stack=true" />
	      </environmentVariables>
	    </httpPlatform>
	  </system.webServer>
	</configuration>

Bei Tomcat müssen einige Änderungen an der Konfiguration vorgenommen werden. Die server.xml-Datei muss folgendermaßen geändert werden:

-	Shutdown port = -1
-	HTTP connector port = ${port.http}
-	HTTP connector address = "127.0.0.1"
-	Kommentieren der HTTPS- und AJP-Konnektoren
-	Die IPv4-Einstellung kann auch in der catalina.properties-Datei festgelegt werden, wo Sie `java.net.preferIPv4Stack=true` hinzufügen können.
    
Direct3D-Aufrufe werden in App Service-Web-Apps nicht unterstützt. Falls Ihre Anwendung solche Aufrufe durchführt, fügen Sie die folgende Java-Option hinzu, um diese Aufrufe zu deaktivieren: `-Dsun.java2d.d3d=false`

### Jetty

Wie bei Tomcat, können Kunden auch für Jetty ihre eigenen Instanzen hochladen. Falls Sie die vollständige Installation von Jetty ausführen, würde die Konfiguration folgendermaßen aussehen:

	<?xml version="1.0" encoding="UTF-8"?>
	<configuration>
	  <system.webServer>
	    <handlers>
	      <add name="httppPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
	    </handlers>
	    <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" 
	         arguments="-Djava.net.preferIPv4Stack=true -Djetty.port=%HTTP_PLATFORM_PORT% -Djetty.base=";%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115"; -jar ";%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115\start.jar";"
	        startupTimeLimit="20"
		  startupRetryCount="10"
		  stdoutLogEnabled="true">
	    </httpPlatform>
	  </system.webServer>
	</configuration>

Die Jetty-Konfiguration muss in der start.ini-Datei geändert werden, um `java.net.preferIPv4Stack=true` festzulegen.

### Hudson

In unserem Test verwendeten wir die Hudson 3.1.2-WAR-Datei und die Standardinstanz Tomcat 7.0.50, jedoch ohne die UI für die Einrichtung zu verwenden. Da es sich bei Hudson um ein softwarebasiertes Tool handelt, empfehlen wir die Installation auf dedizierten Instanzen, auf denen das Kennzeichen **AlwaysOn** für die Web-App festgelegt werden kann.

1. Erstellen Sie im Stammverzeichnis Ihrer Web-App, also **d:\\home\\site\\wwwroot**, ein Verzeichnis namens **webapps** (falls nicht bereits vorhanden), und speichern Sie die Hudson.war-Datei unter **d:\\home\\site\\wwwroot\\webapps**.
2. Laden Sie Apache Maven 3.0.5 herunter (mit Hudson kompatibel), und speichern Sie es unter **d:\\home\\site\\wwwroot**.
3. Erstellen Sie unter **d:\\home\\site\\wwwroot** eine web.conifg-Datei, und kopieren Sie die folgenden Inhalte hinein:
	
		<?xml version="1.0" encoding="UTF-8"?>
		<configuration>
		  <system.webServer>
		    <handlers>
		      <add name="httppPlatformHandler" path="*" verb="*" 
		modules="httpPlatformHandler" resourceType="Unspecified" />
		    </handlers>
		    <httpPlatform processPath="%AZURE_TOMCAT7_HOME%\bin\startup.bat"
		startupTimeLimit="20"
		startupRetryCount="10">
		<environmentVariables>
		  <environmentVariable name="HUDSON_HOME" 
		value="%HOME%\site\wwwroot\hudson_home" />
		  <environmentVariable name="JAVA_OPTS" 
		value="-Djava.net.preferIPv4Stack=true -Duser.home=%HOME%/site/wwwroot/user_home -Dhudson.DNSMultiCast.disabled=true" />
		</environmentVariables>            
		    </httpPlatform>
		  </system.webServer>
		</configuration>

    Nun kann die Web-App neu gestartet werden, um die Änderungen zu übernehmen. Stellen Sie eine Verbindung mit http://yourwebapp/hudson her, um Hudson zu starten.

4. Nachdem Hudson sich selbst konfiguriert hat, sollte die folgende Anzeige zu sehen sein:

    ![Hudson](./media/web-sites-java-custom-upload/hudson1.png)
    
5. Zugriff auf die Hudson-Konfigurationsseite: Klicken Sie auf **Hudson verwalten** und dann auf **System konfigurieren**.
6. Konfigurieren Sie das JDK wie unten gezeigt:

	![Hudson configuration](./media/web-sites-java-custom-upload/hudson2.png)

7. Konfigurieren Sie Maven wie unten gezeigt:

	![Maven configuration](./media/web-sites-java-custom-upload/maven.png)

8. Speichern Sie die Einstellungen. Hudson sollte nun konfiguriert und einsatzbereit sein.

Weitere Informationen zu Hudson finden Sie unter [http://hudson-ci.org](http://hudson-ci.org).

### Liferay

Liferay wird in App Service-Web-Apps unterstützt. Da Liferay möglicherweise erheblichen Speicherplatz benötigt, muss die Web-App auf einem mittleren oder großen dedizierten Worker ausgeführt werden, der genügend Speicherplatz bietet. Der Startvorgang von Liferay nimmt einige Minuten in Anspruch. Aus diesem Grund empfiehlt es sich, die Einstellung **Always On** für die Web-App zu verwenden.

Bei der Verwendung von Liferay 6.1.2 Community Edition GA3 in Kombination mit Tomcat wurden die folgenden Dateien nach dem Herunterladen von Liferay bearbeitet:

**Server.xml**

- Legen Sie für den Shutdown-Port "-1" fest.
- Legen Sie für den HTTP-Konnektor `<Connector port="${port.http}" protocol="HTTP/1.1" connectionTimeout="600000" address="127.0.0.1" URIEncoding="UTF-8" />` fest.
- Kommentieren Sie den AJP-Konnektor.

Erstellen Sie im Ordner **liferay\\tomcat-7.0.40\\webapps\\ROOT\\WEB-INF\\classes** eine Datei namens **portal-ext.properties**. Die Datei muss die folgende Zeile enthalten:

    liferay.home=%HOME%/site/wwwroot/liferay

Erstellen Sie auf der Verzeichnisebene des Ordners "tomcat-7.0.40" eine Datei namens **web.config** mit folgendem Inhalt:

	<?xml version="1.0" encoding="UTF-8"?>
	<configuration>
	  <system.webServer>
	    <handlers>
	<add name="httpPlatformHandler" path="*" verb="*"
	     modules="httpPlatformHandler" resourceType="Unspecified" />
	    </handlers>
	    <httpPlatform processPath="%HOME%\site\wwwroot\tomcat-7.0.40\bin\catalina.bat" 
	                  arguments="run" 
	                  startupTimeLimit="10" 
	                  requestTimeout="00:10:00" 
	                  stdoutLogEnabled="true">
	      <environmentVariables>
	  <environmentVariable name="CATALINA_OPTS" value="-Dport.http=%HTTP_PLATFORM_PORT%" />
	  <environmentVariable name="CATALINA_HOME" value="%HOME%\site\wwwroot\tomcat-7.0.40" />
	        <environmentVariable name="JRE_HOME" value="D:\Program Files\Java\jdk1.7.0_51" /> 
	        <environmentVariable name="JAVA_OPTS" value="-Djava.net.preferIPv4Stack=true" />
	      </environmentVariables>
	    </httpPlatform>
	  </system.webServer>
	</configuration>

Im Block **httpPlatform** ist **requestTimeout** auf "00:10:00" festgelegt. Dies kann auch reduziert werden, allerdings werden Ihnen dann möglicherweise einige Zeitüberschreitungsfehler beim Bootstrapping von Liferay angezeigt. Wenn dieser Wert geändert wird, sollte auch **connectionTimeout** in der server.xml-Datei von Tomcat geändert werden.

Es sollte darauf hingewiesen werden, dass die JRE\_HOME-Umgebungsvariable in der oben genannten web.config-Datei festgelegt wurde, um auf das 64-Bit-JDK zu verweisen. Der Standardwert ist 32-Bit, aber da Liferay möglicherweise mehr Speicher benötigt, wird empfohlen, das 64-Bit-JDK zu verwenden.

Sobald Sie diese Änderungen vorgenommen haben, starten Sie Ihre Web-App neu mit Liferay, und öffnen Sie dann http://yourwebapp. Das Liferay-Portal ist über das Web-App-Stammverzeichnis verfügbar.

## Nächste Schritte

Weitere Informationen über Liferay finden Sie unter [http://www.liferay.com](http://www.liferay.com).

Weitere Informationen zu Java finden Sie im [Java Developer Center](/develop/java/).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 
 
<!-- External Links -->
[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714

<!---HONumber=Sept15_HO4-->