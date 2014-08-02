<properties linkid="develop-java-how-to-guides-web-sso" urlDisplayName="Web SSO" pageTitle="Single sign-on with Azure Active Directory (Java)" metaKeywords="Azure Java web app, Azure single sign-on, Azure Java Active Directory" description="Learn how to create a Java web application that uses single sign-on with Azure Active Directory." metaCanonical="" services="active-directory" documentationCenter="Java" title="Web Single Sign-On with Java and Azure Active Directory" authors="" solutions="" manager="" editor="" />

Einmalige Webanmeldung mit Java und Azure Active Directory
==========================================================

Einführung
----------

In diesem Lernprogramm erfahren Java-Entwickler, wie sie Azure Active Directory zur einmaligen Anwendung von Office 365-Benutzern einsetzen können. Sie erhalten Informationen zu folgenden Themen:

-   Bereitstellung der Webanwendung im Mandanten eines Kunden
-   Schutz der Anwendung mit WS-Federation

### Voraussetzungen

Für dieses Lernprogramm wird ein bestimmter Anwendungsserver verwendet, aber wenn Sie ein erfahrener Java-Entwickler sind, können Sie den unten beschriebenen Vorgang auch auf andere Umgebungen anwenden. Für dieses Lernprogramm ist die folgende Entwicklungsumgebung erforderlich:

-   [Java-Beispielcode für Azure Active Directory](https://github.com/WindowsAzure/azure-sdk-for-java-samples/tree/master/WAAD.WebSSO.JAVA)
-   [Java Runtime Environment 1.6](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
-   [JBoss Application Server Version 7.1.1.Final](http://www.jboss.org/jbossas/downloads/)
-   [JBoss Developer Studio IDE](https://devstudio.jboss.com/earlyaccess/)
-   Internet Information Services (IIS) 7.5 mit aktiviertem SSL
-   Windows PowerShell
-   [Office 365 PowerShell Commandlets](http://onlinehelp.microsoft.com/en-us/office365-enterprises/ff652560.aspx)

### Inhaltsverzeichnis

-   [Einführung](#introduction)
-   [Schritt 1: Erstellen einer Java-Anwendung](#createapp)
-   [Schritt 2: Bereitstellen der Anwendung in einem Directory-Mandanten eines Unternehmens](#provisionapp)
-   [Schritt 3: Schutz der Anwendung mit WS-Federation zur Mitarbeiteranmeldung](#protectapp)
-   [Zusammenfassung](#summary)

Schritt 1: Erstellen einer Java-Anwendung
-----------------------------------------

Dieser Schritt beschreibt die Erstellung einer einfachen Java-Anwendung, die eine geschützte Ressource darstellt. Der Zugriff auf diese Ressource erfolgt über eine Verbundauthentifizierung, die vom STS des Unternehmens verwaltet wird. Dieser wird später in diesem Lernprogramm beschrieben.

1.  Öffnen Sie eine neue Instanz von JBoss Developer Studio.
2.  Klicken Sie im Menü **File** auf **New** und anschließend auf **Project**.
3.  Erweitern Sie im Dialogfeld **New Project** den Ordner **Maven**, klicken Sie auf **Maven Project**, und klicken Sie dann auf **Next**.
4.  Aktivieren Sie im Dialogfeld **New Maven Project** die Option **Create a simple project (skip archetype selection)**, und klicken Sie auf **Next**.
5.  Geben Sie auf der nächsten Seite des Dialogfelds **New Maven Project** in den Textfeldern **Group Id** und **Artifact Id** den Text *sample* ein. Wählen Sie dann im Dropdown-Menü **Packaging** die Option **war**, und klicken Sie auf **Finish**.
6.  Das neue Maven-Projekt wird erstellt. Erweitern Sie im Menü **Project Explorer** auf der linken Seite das Projekt **sample**, klicken Sie mit der rechten Maustaste auf die Datei **pom.xml**, und klicken Sie dann auf **Open With** sowie auf **Text Editor**.
7.  Fügen Sie in der Datei **pom.xml** den folgenden XML-Text in den Abschnitt *&lt;project\>* ein:

         <repositories>
             <repository>
                 <id>jboss-public-repository-group</id>
                 <name>JBoss Public Maven Repository Group</name>
                 <url>https://repository.jboss.org/nexus/content/groups/public-jboss/</url>
                 <layout>default</layout>
                 <releases>
                     <enabled>true</enabled>
                     <updatePolicy>never</updatePolicy>
                 </releases>
                 <snapshots>
                     <enabled>true</enabled>
                     <updatePolicy>never</updatePolicy>
                 </snapshots>
             </repository>
         </repositories>
         <pluginRepositories>
             <pluginRepository>
                 <id>jboss-public-repository-group</id>
                 <name>JBoss Public Maven Repository Group</name>
                 <url>https://repository.jboss.org/nexus/content/groups/public-jboss/</url>
                 <layout>default</layout>
                 <releases>
                     <enabled>true</enabled>
                     <updatePolicy>always</updatePolicy>
                 </releases>
                 <snapshots>
                     <enabled>true</enabled>
                     <updatePolicy>always</updatePolicy>
                 </snapshots>
             </pluginRepository>
         </pluginRepositories>
         <build>
             <plugins>
                 <plugin>
                     <groupId>org.apache.maven.plugins</groupId>
                     <artifactId>maven-compiler-plugin</artifactId>
                     <version>2.0.2</version>
                     <configuration>
                         <source>1.6</source>
                         <target>1.6</target>
                     </configuration>
                 </plugin>
             </plugins>
         </build> 

    Speichern Sie nach dem Einfügen dieses XML-Texts die Datei **pom.xml**.

8.  Klicken Sie mit der rechten Maustaste auf das Projekt **sample**, und klicken Sie auf **Maven** sowie auf **Update Maven Project**. Klicken Sie im Dialogfeld **Update Maven Project** auf **OK**. In diesem Schritt wird Ihr Projekt durch die Änderungen in der Datei **pom.xml** aktualisiert.

9.  Klicken Sie mit der rechten Maustaste auf das Projekt **sample**, und klicken Sie dann auf **New** sowie auf **JSP File**.

10. Ändern Sie im Dialogfeld **New JSP File** den Pfad für die neue Datei in *sample/src/main/webapp*. Geben Sie der Datei dann den Namen **index.jsp**, und klicken Sie auf **Finish**.

11. Die neue Datei **index.jsp** wird automatisch geöffnet. Ersetzen Sie den automatisch generierten Code durch den folgenden, und speichern sie dann die Datei:

        <%@ page language="java" contentType="text/html; charset=ISO-8859-1"  pageEncoding="ISO-8859-1"%>
        <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
        <html>
        <head>
            <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
            <title>Index</title>
        </head>
        <body>
            <h3>Index Page</h3>
        </body>
        </html> 

12. Klicken Sie mit der rechten Maustaste auf das Projekt **sample**, und klicken Sie auf **Run As** sowie auf **Run On Server**.

13. Aktivieren Sie im Dialogfeld **Run On Server** die Option **JBoss Enterprise Application Platform 6.x**, und klicken Sie auf **Finished**.

Schritt 2: Bereitstellen der Anwendung in einem Directory-Mandanten eines Unternehmens
--------------------------------------------------------------------------------------

In diesem Schritt wird beschrieben, wie ein Administrator eines Azure Active Directory-Kunden die Java-Anwendung im Mandanten dieses Kunden bereitstellt und eine einmalige Anwendung konfiguriert. Wenn dieser Schritt ausgeführt ist, können die Mitarbeiter dieses Kunden sich über ihre Office 365-Konten auf der Webanwendung anmelden.

Der Bereitstellungsvorgang beginnt mit dem Erstellen eines neuen Dienstprinzipals für die Anwendung. Dienstprinzipale werden von Azure Active Directory verwendet, um Anwendungen im Verzeichnis zu registrieren und zu authentifizieren.

1.  Laden Sie die Office 365 PowerShell Commandlets herunter, und installieren Sie sie, falls dies noch nicht geschehen ist.
2.  Führen Sie im Menü **Start** die Konsole **Microsoft Online Services-Modul für Windows PowerShell** aus. Diese Konsole bietet eine Befehlszeilenumgebung zur Konfiguration von Attributen Ihres Office 365-Mandanten, z. B. zum Erstellen und Ändern von Dienstprinzipalen.
3.  Importieren Sie das erforderliche **MSOnlineExtended**-Modul, indem Sie den folgenden Befehl eingeben und die Eingabetaste drücken:

         Import-Module MSOnlineExtended -Force
4.  Um eine Verbindung mit Ihrem Office 365-Verzeichnis herzustellen, müssen Sie die Anmeldedaten des Unternehmensadministrators eingeben. Geben Sie den folgenden Befehl ein, drücken Sie die Eingabetaste, und geben Sie dann an der Eingabeaufforderung Ihre Anmeldedaten ein:

         Connect-MsolService

5.  Nun erstellen Sie einen neuen Dienstprinzipal für die Anwendung. Geben Sie den folgenden Befehl ein, und drücken Sie die Eingabetaste:

         New-MsolServicePrincipal -ServicePrincipalNames @("javaSample/localhost") -DisplayName "Federation Sample Web Site" -Type Symmetric -Usage Verify -StartDate "12/01/2012" -EndDate "12/01/2013" 

    Die in diesem Schritt ausgegebenen Informationen sehen in etwa folgendermaßen aus:

         The following symmetric key was created as one was not supplied qY+Drf20Zz+A4t2w e3PebCopoCugO76My+JMVsqNBFc=
         DisplayName           : Federation Sample Java Web Site
         ServicePrincipalNames : {javaSample/localhost}
         ObjectId              : 59cab09a-3f5d-4e86-999c-2e69f682d90d
         AppPrincipalId        : 7829c758-2bef-43df-a685-717089474505
         TrustedForDelegation  : False
         AccountEnabled        : True
         KeyType               : Symmetric
         KeyId                 : f1735cbe-aa46-421b-8a1c-03b8f9bb3565
         StartDate             : 12/01/2012 08:00:00 a.m.
         EndDate               : 12/01/2013 08:00:00 a.m.
         Usage                 : Verify 

    > [WACOM.NOTE]
    > Sie sollten diese Ausgabe, besonders den generierten symmetrischen Schlüssel, speichern. Dieser Schlüssel wird Ihnen nur während der Erstellung des Dienstprinzipals angezeigt, und Sie können ihn in Zukunft nicht mehr abrufen. Die anderen Werte werden zur Verwendung der Graph-API benötigt, um Informationen im Verzeichnis zu lesen und zu schreiben.

6.  Im letzten Schritt wird die Antwort-URL für Ihre Anwendung eingerichtet. An die Antwort-URL werden Antworten nach Authentifizierungsversuchen gesendet. Geben Sie die folgenden Befehle ein, und drücken Sie die Eingabetaste:

         $replyUrl = New-MsolServicePrincipalAddresses -Address "https://localhost:8443/sample" 

         Set-MsolServicePrincipal -AppPrincipalId "7829c758-2bef-43df-a685-717089474505" -Addresses $replyUrl 

Die Webanwendung wurde nun im Verzeichnis bereitgestellt und kann zur einmaligen Anwendung durch Unternehmensmitarbeiter verwendet werden.

Schritt 3: Schutz der Anwendung mit WS-Federation zur Mitarbeiteranmeldung
--------------------------------------------------------------------------

In diesem Schritt erfahren Sie, wie Sie mit Windows Identity Foundation (WIF) und der **waad-federation**-Bibliothek, die Sie zu Beginn als Teil des Beispielcode-Pakets heruntergeladen haben, Unterstützung für eine Verbundanmeldung hinzufügen. Außerdem fügen Sie eine Anmeldeseite hinzu und konfigurieren eine Vertrauensstellung zwischen der Anwendung und dem Directory-Mandanten.

1.  Klicken Sie in JBoss Developer Studio auf **File** und auf **Import**.

2.  Erweitern Sie im Dialogfeld **Import** den Ordner **Maven**, klicken Sie auf **Existing Maven Projects**, und klicken Sie dann auf **Next**.

3.  Legen Sie im Dialogfeld **Import Maven Projects** den Pfad zum **Root Directory** auf den Speicherort fest, in den Sie die **waad-federation**-Bibliothek im Beispielcode heruntergeladen haben. Aktivieren Sie dann das Kontrollkästchen neben der Datei **pom.xml** im Projekt **waad-federation**, und klicken Sie auf **Finish**.

4.  Erweitern Sie das Projekt **sample**, klicken Sie mit der rechten Maustaste auf **pom.xml**, klicken Sie auf **Open With** und anschließend auf **Text Editor**.

5.  Fügen Sie in der Datei **pom.xml** den folgenden XML-Text in den Abschnitt *&lt;project\>* ein, und speichern Sie die Datei:

         <dependencies>
             <dependency>
                 <groupId>javax.servlet</groupId>
                 <artifactId>servlet-api</artifactId>
                 <version>3.0-alpha-1</version>
             </dependency>
             <dependency>
                 <groupId>com.microsoft.samples.waad.federation</groupId>
                 <artifactId>waad-federation</artifactId>
                 <version>0.0.1-SNAPSHOT</version>
             </dependency>
         </dependencies> 

6.  Klicken Sie mit der rechten Maustaste auf das Projekt **sample**, und klicken Sie auf **Maven** sowie auf **Update Project**. Klicken Sie im Dialogfeld **Update Maven Project** auf **OK**. In diesem Schritt wird Ihr Projekt durch die Änderungen in der Datei **pom.xml** aktualisiert.

7.  Klicken Sie mit der rechten Maustaste auf das Projekt **sample**, klicken Sie auf **New** und dann auf **Filter**.

8.  Geben Sie im Dialogfeld **Create Filter** unter dem Eintrag **Class name** den Text *FederationFilter* ein, und klicken Sie auf **Finish**.

9.  Die automatisch generierte Datei **FederationFilter.java** wird geöffnet. Ersetzen Sie deren Code durch den folgenden Code, und speichern Sie die Datei:

         import java.io.IOException;
         import javax.servlet.Filter;
         import javax.servlet.FilterChain;
         import javax.servlet.FilterConfig;
         import javax.servlet.ServletException;
         import javax.servlet.ServletRequest;
         import javax.servlet.ServletResponse;
         import javax.servlet.http.HttpServletRequest;
         import javax.servlet.http.HttpServletResponse;
         import java.util.regex.*;
         import com.microsoft.samples.federation.FederatedLoginManager; import com.microsoft.samples.federation.URLUTF8Encoder;

         public class FederationFilter implements Filter {
             private String loginPage;
             private String allowedRegex;
             public void init(FilterConfig config) throws ServletException {
                 this.loginPage = config.getInitParameter("login-page-url");
                 this.allowedRegex = config.getInitParameter("allowed-regex");
             }
             public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
                     if (request instanceof HttpServletRequest) { 
                         HttpServletRequest httpRequest = (HttpServletRequest) request;
                         if (!httpRequest.getRequestURL().toString().contains(this.loginPage)) {
                             FederatedLoginManager loginManager = FederatedLoginManager.fromRequest(httpRequest);
                             boolean allowedUrl = Pattern.compile(this.allowedRegex).matcher(httpRequest.getRequestURL().toString()).find();
                             if (!allowedUrl && !loginManager.isAuthenticated()) {
                                 HttpServletResponse httpResponse = (HttpServletResponse) response;
                                 String encodedReturnUrl = URLUTF8Encoder.encode(httpRequest.getRequestURL().toString());
                                 httpResponse.setHeader("Location", this.loginPage + "ReturnUrl=" + encodedReturnUrl);
                                 httpResponse.setStatus(302);
                                 return;
                             }
                         }
                     }
                 chain.doFilter(request, response);
             }
             public void destroy() {
             }
         } 

10. Erweitern Sie im **Project Explorer** die Ordner **src**, **main** und **webapp**. Klicken Sie mit der rechten Maustaste auf die Datei **web.xml**, und klicken Sie dann auf **Open With** sowie auf **Text Editor**.

11. Fügen Sie in der Datei **web.xml** einen Filter ein, um mit gesicherten und ungesicherten Seiten umzugehen. Dieser Filter leitet nicht authentifizierte Benutzer zur Anmeldeseite weiter (die als Filterparameter **login-page-url** festgelegt ist). Wenn eine URL allerdings mit dem im Filterparameter **allowed-regex** festgelegten regulären Ausdruck übereinstimmt, wird sie nicht gefiltert. Fügen Sie im Abschnitt *&lt;web-app\>* den folgenden XML-Text ein, und speichern Sie dann die Datei **web.xml**.

        <filter>
            <filter-name>FederationFilter</filter-name>
            <filter-class>FederationFilter</filter-class>
            <init-param>
                <param-name>login-page-url</param-name>
                <param-value>/sample/login.jsp</param-value>
            </init-param>
            <init-param>
                <param-name>allowed-regex</param-name>
                <param-value>(\/sample\/login.jsp|\/sample\/wsfed-saml|\/sample\/oauth)</param-value>
            </init-param>
        </filter>
        <filter-mapping>
            <filter-name>FederationFilter</filter-name>
            <url-pattern>/*</url-pattern>
        </filter-mapping> 

12. Klicken Sie zum Erstellen einer Anmeldeseite mit der rechten Maustaste auf das Projekt **sample**, und klicken Sie dann auf **New** sowie auf **JSP File**.

13. Ändern Sie im Dialogfeld **New JSP File** den Pfad für die neue Datei in *sample/src/main/webapp*. Geben Sie der Datei den Namen **login.jsp**, und klicken Sie auf **Finish**.

14. Die neue Datei **login.jsp** wird automatisch geöffnet. Ersetzen Sie den automatisch generierten Code durch den folgenden, und speichern sie dann die Datei:

        <%@ page language="java" contentType="text/html; charset=ISO-8859-1"  pageEncoding="ISO-8859-1"%>
        <%@ page import="com.microsoft.samples.federation.*"%>
        <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
        <html>
        <head>
            <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1"> 
            <title>Login Page</title>
        </head>
        <body>
            <h3>Login Page</h3>
            <a href="<%=FederatedLoginManager.getFederatedLoginUrl(request.getParameter("returnUrl"))%>"><%=FederatedConfiguration.getInstance().getStsFriendlyName()%></a>
        </body>
        </html> 

15. Erweitern Sie im **Project Explorer** den Ordner **/src/main** des Projekts **sample**. Klicken Sie mit der rechten Maustaste auf den Ordner **resources**, und klicken Sie dann auf **New** sowie auf **Other**.

16. Erweitern Sie im Dialogfeld **New** den Ordner **JBoss Tools Web**, und klicken Sie dann auf **Properties File** sowie auf **Next**.

17. Benennen Sie die Datei im Dialogfeld **New File Properties** in **federation** um, und klicken Sie auf **Finish**.

18. Erweitern Sie im **Project Explorer** den Ordner **/src/mainresources** des Projekts **sample**. Klicken Sie mit der rechten Maustaste auf die Datei **federation.properties**, und klicken Sie dann auf **Open With** sowie auf **Text Editor**.

19. Fügen Sie in der Datei **federation.properties** die folgenden Konfigurationseinträge hinzu, und speichern Sie dann die Datei:

        federation.trustedissuers.issuer=https://accounts.accesscontrol.windows.net/v2/wsfederation
        federation.trustedissuers.thumbprint=qY+Drf20Zz+A4t2we3PebCopoCugO76My+JMVsqNBFc=
        federation.trustedissuers.friendlyname=Fabrikam
        federation.audienceuris=spn:7829c758-2bef-43df-a685-717089474505
        federation.realm=spn:7829c758-2bef-43df-a685-717089474505
        federation.reply=https://localhost:8443/sample/wsfed-saml 

    > [WACOM.NOTE]
    > Den Werten **audienceuris** und **realm** muss die Zeichenfolge "spn:" vorausgehen.

20. Nun müssen Sie ein neues Servlet erstellen. Klicken Sie mit der rechten Maustaste auf das Projekt **sample**, und klicken Sie dann auf **New**, auf **Other** sowie auf **Servlet**.

21. Geben Sie im Dialogfeld **Create Servlet** unter **Class name** den Text *FederationServlet* ein, und klicken Sie auf **Finish**.

22. Die Datei **FederationServlet.java** wird automatisch geöffnet. Ersetzen Sie deren Inhalt durch den folgenden Code, und speichern Sie die Datei:

        import java.io.IOException;
        import javax.servlet.ServletException;
        import javax.servlet.http.HttpServlet;
        import javax.servlet.http.HttpServletRequest;
        import javax.servlet.http.HttpServletResponse;
        import com.microsoft.samples.federation.FederatedAuthenticationListener;
        import com.microsoft.samples.federation.FederatedLoginManager;
        import com.microsoft.samples.federation.FederatedPrincipal;
        import com.microsoft.samples.federation.FederationException;
            
        public class FederationServlet extends HttpServlet {
            private static final long serialVersionUID = 1L;

            protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
                String token = request.getParameter("wresult").toString();

                if (token == null) {
                    response.sendError(400, "You were supposed to send a wresult parameter with a token");
                }
                FederatedLoginManager loginManager = FederatedLoginManager.fromRequest(request, new SampleAuthenticationListener());

                try {
                    loginManager.authenticate(token, response);
                } catch (FederationException e) {
                    response.sendError(500, "Oops! and error occurred.");
                }
            }

            private class SampleAuthenticationListener implements FederatedAuthenticationListener {
                @Override
                public void OnAuthenticationSucceed(FederatedPrincipal principal) {
                    // ***
                    // do whatever you want with the principal object that contains the token's claims
                    // ***
                }
            }
        } 

23. Erweitern Sie im **Project Explorer** den Ordner **src/main/webapp/WEB-INF**. Klicken Sie mit der rechten Maustaste auf die Datei **web.xml**, und klicken Sie dann auf **Open With** sowie auf **Text Editor**.

24. Ersetzen Sie in der Datei **web.xml** die Einstellung **/FederationServlet** im Abschnitt *&lt;url-pattern\>* durch **/ws-saml**. Beispiel:

        <servlet>
            <description></description>
            <display-name>FederationServlet</display-name>
            <servlet-name>FederationServlet</servlet-name>
            <servlet-class>FederationServlet</servlet-class>
        </servlet>
        <servlet-mapping>
            <servlet-name>FederationServlet</servlet-name>
            <url-pattern>/wsfed-saml</url-pattern>
        </servlet-mapping> 

25. Erweitern Sie im **Project Explorer** den Ordner **src/main/webapp**. Klicken Sie mit der rechten Maustaste auf die Datei **index.jsp**, und klicken Sie dann auf **Open With** sowie auf **Text Editor**.

26. Ersetzen Sie in der Datei **index.jsp** den vorhandenen Code durch den folgenden, und speichern Sie die Datei:

        <%@ page language="java" contentType="text/html; charset=ISO-8859-1" pageEncoding="ISO-8859-1"%>
        <%@ page import="com.microsoft.samples.federation.*"%>
        <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
        <html>
        <head>
            <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
            <title>Index Page</title>
        </head>
        <body>
            <h3>Welcome <strong><%=FederatedLoginManager.fromRequest(request).getPrincipal().getName()%></strong>!</h3>
            <h2>Claim list:</h2>
            <ul> 
                <% for (Claim claim : FederatedLoginManager.fromRequest(request).getClaims()) { %>
                <li><%= claim.toString()%></li>
                <%  } %>
            </ul>
        </body>
        </html> 

27. Erweitern Sie im **Project Explorer** den Ordner **src/main/webapp/WEB-INF**. Klicken Sie mit der rechten Maustaste auf die Datei **web.xml**, und klicken Sie dann auf **Open With** sowie auf **Text Editor**.

28. Nun aktivieren wir SSL für die Anwendung. Geben Sie in der Datei **web.xml** den folgenden Abschnitt *&lt;security-constraint\>* in den Abschnitt *&lt;web-app\>* ein, und speichern Sie die Datei:

        <security-constraint>
            <web-resource-collection>
                <web-resource-name>SSL Forwarding</web-resource-name>
                <url-pattern>/*</url-pattern>
                <http-method>POST</http-method>
                <http-method>GET</http-method>
            </web-resource-collection>
            <user-data-constraint>
                <transport-guarantee>CONFIDENTIAL</transport-guarantee>
            </user-data-constraint>
        </security-constraint> 

    > [WACOM.NOTE]
    > Bevor Sie fortfahren, stellen Sie sicher, dass JBoss Server bereits für eine SSL-Unterstützung konfiguriert ist.

29. Nun können wir die Anwendung durchgängig ausführen. Klicken Sie mit der rechten Maustaste auf das Projekt **sample**, und klicken Sie auf **Run As** sowie auf **Run On Server**. Übernehmen Sie die Werte, die Sie zuvor festgelegt haben, und klicken Sie auf **Finish**.

30. Der JBoss-Browser öffnet die Anmeldeseite. Wenn Sie auf den Link **Awesome Computers** klicken, werden Sie auf die Seite "Office 365-Identiitäsanbieter" weitergeleitet, auf der Sie sich mit den Anmeldeinformationen Ihres Directory-Mandanten anmelden können. Beispiel: *john.doe@fabrikam.onmicrosoft.com*.

31. Nach der Anmeldung werden Sie als authentifizierter Benutzer wieder zur gesicherten Seite (**sample/index.jsp**) weitergeleitet.

Zusammenfassung
---------------

In diesem Lernprogramm haben Sie erfahren, wie Sie eine Java-Anwendung für einen einzelnen Mandanten-erstellen und konfigurieren können, die die Möglichkeiten einer einmaligen Anwendung mit Azure Active Directory nutzt.

[Introduction]: #introduction
[Step 1: Create a Java Application]: #createapp
[Step 2: Provision the Application in a Company's Directory Tenant]: #provisionapp
[Step 3: Protect the Application Using WS-Federation for Employee Sign In]: #protectapp
[Summary]: #summary
[Developing Multi-Tenant Cloud Applications with Azure Active Directory]: http://g.microsoftonline.com/0AX00en/121
[Windows Identity Foundation 3.5 SDK]: http://www.microsoft.com/en-us/download/details.aspx?id=4451
[Windows Identity Foundation 1.0 Runtime]: http://www.microsoft.com/en-us/download/details.aspx?id=17331
[Office 365 Powershell Commandlets]: http://onlinehelp.microsoft.com/en-us/office365-enterprises/ff652560.aspx
[ASP.NET MVC 3]: http://www.microsoft.com/en-us/download/details.aspx?id=4211
[Java Runtime Environment 1.6]: http://www.oracle.com/technetwork/java/javase/downloads/index.html
[Java Sample Code for Azure Active Directory]: https://github.com/WindowsAzure/azure-sdk-for-java-samples/tree/master/WAAD.WebSSO.JAVA
[JBoss Application Server version 7.1.1.Final]: http://www.jboss.org/jbossas/downloads/
[JBoss Developer Studio IDE]: https://devstudio.jboss.com/earlyaccess/
