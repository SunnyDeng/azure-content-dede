<properties
	pageTitle="Erste Schritte in Azure AD Java | Microsoft Azure"
	description="Erfahren Sie, wie Sie eine Java-Befehlszeilen-App erstellen, die Benutzer für den Zugriff auf eine API anmeldet."
	services="active-directory"
	documentationCenter="java"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
  ms.tgt_pltfrm="na"
	ms.devlang="java"
	ms.topic="article"
	ms.date="02/19/2016"
	ms.author="brandwe"/>


# Verwenden der Java-Befehlszeilen-App für den Zugriff auf eine API mit Azure AD

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Azure AD erleichtert die Auslagerung der Identitätsverwaltung für Webanwendungen, indem es eine einmalige An- und Abmeldung (Single Sign-on und Single Sign-out) mit nur wenigen Codezeilen ermöglicht. Für Java-Web-Apps erreichen Sie das Gleiche durch die Microsoft-Implementierung des Community-gestützten ADAL4J.

  Hier verwenden wir ADAL4J für Folgendes:
- Anmelden von Benutzern an der App mit Azure AD als Identitätsanbieter
- Anzeigen einiger Informationen zum Benutzer
- Abmelden des Benutzers von der App

Dazu müssen Sie folgende Schritte ausführen:

1. Registrieren einer Anwendung in Azure AD
2. Legen Sie fest, dass Ihre App die Bibliothek „ADAL4J“ verwendet.
3. Verwenden Sie die Bibliothek „ADAL4J“ zur Ausgabe von An- und Abmeldeanforderungen für Azure AD.
4. Ausdrucken von Informationen zum Benutzer

Beginnen Sie, indem Sie [das Anwendungsgerüst](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect/archive/skeleton.zip) oder [das vollständige Beispiel herunterladen](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect\/archive/complete.zip). Außerdem benötigen Sie einen Azure AD-Mandanten, bei dem Sie Ihre Anwendung registrieren. Wenn Sie noch keinen Mandanten haben, [erfahren Sie hier, wie Sie einen erhalten](active-directory-howto-tenant.md).

## 1\. Registrieren einer Anwendung in Azure AD
Damit Ihre Anwendung Benutzer authentifizieren kann, müssen Sie zunächst in Ihrem Mandanten eine neue Anwendung registrieren.

- Melden Sie sich beim Azure-Verwaltungsportal an.
- Klicken Sie in der linken Navigationsleiste auf **Active Directory**.
- Wählen Sie den Mandanten aus, in dem die Beispielanwendung registriert werden soll.
- Klicken Sie auf die Registerkarte **Anwendungen** und dann in der unteren Schublade auf „Hinzufügen“.
- Folgen Sie den Bildschirmaufforderungen, und erstellen Sie eine neue **Webanwendung und/oder Web-API**.
    - Am **Namen** der Anwendung sollten die Endbenutzer die Funktion der Anwendung ablesen können.
    - Die **Anmelde-URL** ist die Basis-URL Ihrer Anwendung. Der Standardwert des Gerüsts lautet `http://localhost:8080/adal4jsample/`.
    - Die **App-ID-URI** ist eine eindeutige Kennung für die Anwendung. Üblicherweise wird `https://<tenant-domain>/<app-name>` verwendet, zum Beispiel: `http://localhost:8080/adal4jsample/`
- Nach Abschluss der Registrierung weist AAD Ihrer Anwendung eine eindeutige Client-ID zu. Diesen Wert benötigen Sie in den nächsten Abschnitten, weswegen Sie ihn aus der Registerkarte „Konfigurieren“ kopieren sollten.

Erstellen Sie im Portal für Ihre App einen **geheimen Schlüssel** für Ihre Anwendung, und notieren Sie ihn sich. Sie benötigen ihn später.


## 2\. Festlegen, dass Ihre App die Bibliothek „ADAL4J“ verwendet und Bestimmen der Voreinstellungen für Maven
Hier konfigurieren wir ADAL4J für die Verwendung des OpenID Connect-Authentifizierungsprotokolls. ADAL4J wird unter anderem für die Ausgabe von Anmelde- und Abmeldeanforderungen, für die Verwaltung der Benutzersitzungen und für das Abrufen von Benutzerinformationen verwendet.

-	Öffnen oder erstellen Sie `pom.xml` im Stammverzeichnis des Projekts, suchen Sie `// TODO: provide dependencies for Maven`, und ersetzen Sie es durch Folgendes:

```Java
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>com.microsoft.azure</groupId>
	<artifactId>public-client-adal4j-sample</artifactId>
	<packaging>jar</packaging>
	<version>0.0.1-SNAPSHOT</version>
	<name>public-client-adal4j-sample</name>
	<url>http://maven.apache.org</url>
	<properties>
		<spring.version>3.0.5.RELEASE</spring.version>
	</properties>

	<dependencies>
		<dependency>
			<groupId>com.microsoft.azure</groupId>
			<artifactId>adal4j</artifactId>
			<version>1.1.2</version>
		</dependency>
		<dependency>
			<groupId>com.nimbusds</groupId>
			<artifactId>oauth2-oidc-sdk</artifactId>
			<version>4.5</version>
		</dependency>
		<dependency>
			<groupId>org.json</groupId>
			<artifactId>json</artifactId>
			<version>20090211</version>
		</dependency>
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>javax.servlet-api</artifactId>
			<version>3.0.1</version>
			<scope>provided</scope>
		</dependency>
		<dependency>
			<groupId>org.slf4j</groupId>
			<artifactId>slf4j-log4j12</artifactId>
			<version>1.7.5</version>
		</dependency>
</dependencies>
	<build>
		<finalName>public-client-adal4j-sample</finalName>
		<plugins>
		        <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>exec-maven-plugin</artifactId>
            <version>1.2.1</version>
            <configuration>
                <mainClass>PublicClient</mainClass>
            </configuration>
        </plugin>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-compiler-plugin</artifactId>
				<configuration>
					<source>1.7</source>
					<target>1.7</target>
					<encoding>UTF-8</encoding>
				</configuration>
			</plugin>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-dependency-plugin</artifactId>
				<executions>
					<execution>
						<id>install</id>
						<phase>install</phase>
						<goals>
							<goal>sources</goal>
						</goals>
					</execution>
				</executions>
			</plugin>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-resources-plugin</artifactId>
				<version>2.5</version>
				<configuration>
					<encoding>UTF-8</encoding>
				</configuration>
			</plugin>
			<plugin>
        <artifactId>maven-assembly-plugin</artifactId>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>single</goal>
            </goals>
          </execution>
        </executions>
        <configuration>
          <descriptorRefs>
            <descriptorRef>jar-with-dependencies</descriptorRef>
          </descriptorRefs>
        </configuration>
      </plugin>
      <plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-assembly-plugin</artifactId>
  <configuration>
    <archive>
      <manifest>
        <mainClass>PublicClient</mainClass>
      </manifest>
    </archive>
  </configuration>
</plugin>
		</plugins>
	</build>

</project>


```



## 3\. Erstellen der Java-PublicClient-Datei

Wie bereits erwähnt, wird Graph-API zum Abrufen von Daten über den angemeldeten Benutzer verwendet. Das ist einfacher, wenn wir sowohl eine Datei erstellen, die ein **Verzeichnisobjekt** darstellt, als auch eine separate Datei, die den **Benutzer** darstellt. So kann das OO-Muster von Java genutzt werden.

1. Erstellen Sie eine Datei namens `DirectoryObject.java`, die grundlegende Informationen zu jedem DirectoryObject speichert (dies kann später für beliebige andere Graph-Abfragen verwendet werden). Fügen Sie mit Ausschneiden/Einfügen Folgendes ein:

```Java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

import javax.naming.ServiceUnavailableException;

import com.microsoft.aad.adal4j.AuthenticationContext;
import com.microsoft.aad.adal4j.AuthenticationResult;

public class PublicClient {

    private final static String AUTHORITY = "https://login.microsoftonline.com/common/";
    private final static String CLIENT_ID = "2a4da06c-ff07-410d-af8a-542a512f5092";

    public static void main(String args[]) throws Exception {

        try (BufferedReader br = new BufferedReader(new InputStreamReader(
                System.in))) {
            System.out.print("Enter username: ");
            String username = br.readLine();
            System.out.print("Enter password: ");
            String password = br.readLine();

            AuthenticationResult result = getAccessTokenFromUserCredentials(
                    username, password);
            System.out.println("Access Token - " + result.getAccessToken());
            System.out.println("Refresh Token - " + result.getRefreshToken());
            System.out.println("ID Token - " + result.getIdToken());
        }
    }

    private static AuthenticationResult getAccessTokenFromUserCredentials(
            String username, String password) throws Exception {
        AuthenticationContext context = null;
        AuthenticationResult result = null;
        ExecutorService service = null;
        try {
            service = Executors.newFixedThreadPool(1);
            context = new AuthenticationContext(AUTHORITY, false, service);
            Future<AuthenticationResult> future = context.acquireToken(
                    "https://graph.windows.net", CLIENT_ID, username, password,
                    null);
            result = future.get();
        } finally {
            service.shutdown();
        }

        if (result == null) {
            throw new ServiceUnavailableException(
                    "authentication result was null");
        }
        return result;
    }
}


```


##Kompilieren und Ausführen des Beispiels

Wechseln Sie zurück zum Stammverzeichnis, und führen Sie den folgenden Befehl aus, um mit `maven` das gerade zusammengestellte Beispiel zu erstellen. Dabei wird die Datei `pom.xml` verwendet, die Sie für Abhängigkeiten erstellt haben.

`$ mvn package`

In Ihrem Verzeichnis `/targets` sollte sich nun die Datei `adal4jsample.war` befinden. Diese können Sie in Ihrem Tomcat-Container bereitstellen und die URL besuchen.

`http://localhost:8080/adal4jsample/`


> [AZURE.NOTE] 
Mit den aktuellen Tomcat-Servern können WAR-Dateien einfach bereitgestellt werden. Navigieren Sie einfach zu `http://localhost:8080/manager/`, und führen Sie die Schritte zum Hochladen der Datei `adal4jsample.war` aus. Sie wird automatisch mit dem richtigen Endpunkt bereitgestellt.

##Nächste Schritte

Glückwunsch! Sie haben nun eine funktionierende Java-Anwendung, die Benutzer authentifizieren, Web-APIs über OAuth 2.0 sicher aufrufen und grundlegende Benutzerinformationen abfragen kann. Sofern nicht bereits geschehen, ist es nun an der Zeit, Ihren Mandanten mit Benutzern zu füllen.

Als Referenz stellen wir das vollständige Beispiel (ohne Ihre Konfigurationswerte) [hier als ZIP-Datei bereit](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect/archive/complete.zip). Sie können es alternativ aus GitHub klonen:

```git clone --branch complete https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect.git```

<!---HONumber=AcomDC_0302_2016-->