<properties linkid="" urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Developing Single Tenant Applications with Azure Active Directory" authors="" solutions="" manager="" editor="" />

Entwickeln von Anwendungen für einzelne Mandanten mit Azure Active Directory
============================================================================

Einführung
----------

In diesem Lernprogramm erfahren Sie, wie Sie das Azure Active Directory nutzen, um Verzeichnisbenutzer zu authentifizieren und deren Daten aus dem Verzeichnis zu lesen. Sie lernen Folgendes:

-   Bereitstellung der Webanwendung im Mandanten eines Kunden
-   Schutz der Anwendung mit WS-Federation
-   Zugriff auf das Verzeichnis mit dem Graph-API

### Voraussetzungen

Für diese Anleitung ist die folgende Entwicklungsumgebung erforderlich:

-   Visual Studio 2010 SP1
-   Microsoft .NET Framework 4.0
-   [ASP.NET MVC 3](http://www.microsoft.com/en-us/download/details.aspx?id=4211)
-   [Windows Identity Foundation 1.0 Runtime](http://www.microsoft.com/en-us/download/details.aspx?id=17331)
-   [Windows Identity Foundation 3.5 SDK](http://www.microsoft.com/en-us/download/details.aspx?id=4451)
-   [WCF Data Services für OData V3](http://www.microsoft.com/download/en/details.aspx?id=29306)
-   Internet Information Services (IIS) 7.5 mit aktiviertem SSL
-   Windows PowerShell
-   [Office 365 PowerShell Commandlets](http://onlinehelp.microsoft.com/en-us/office365-enterprises/ff652560.aspx)

### Inhaltsverzeichnis

-   [Einführung](#introduction)
-   [Schritt 1: Erstellen einer ASP.NET MVC-Anwendung](#createapp)
-   [Schritt 2: Bereitstellen der Anwendung in einem Directory-Mandanten eines Unternehmens](#provisionapp)
-   [Schritt 3: Schutz der Anwendung mit WS-Federation zur Mitarbeiteranmeldung](#protectapp)
-   [Schritt 4: Lesen der Verzeichnisdaten mit der Graph-API](#readdata)
-   [Zusammenfassung](#summary)

Schritt 1: Erstellen einer ASP.NET MVC-Anwendung
------------------------------------------------

In diesem Schritt wird die Erstellung einer einfachen ASP.NET MVC-Anwendung beschrieben, die eine geschützte Ressource darstellt. Der Zugriff auf diese Ressource erfolgt über eine Verbundauthentifizierung, die vom STS des Unternehmens verwaltet wird. Dieser wird später in diesem Lernprogramm beschrieben.

1.  Öffnen Sie Visual Studio als Administrator.
2.  Klicken Sie im Menü **Datei** auf **Neues Projekt**.
3.  Wählen Sie im Vorlagenmenü auf der linken Seite **Web** aus, und klicken Sie anschließend auf **ASP.NET MVC 3-Webanwendung**. Nennen Sie das Projekt *OrgIdFederationSample*.
4.  Wählen Sie im Dialogfeld **Neues ASP.NET MVC 3-Projekt** die Vorlage **Leer** aus, und klicken Sie auf **OK**.
5.  Wenn das neue Projekt in Visual Studio erstellt wurde, klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner **Controller**, und klicken Sie dann auf **Hinzufügen** sowie auf **Controller**.
6.  Geben Sie dem neuen Controller im Dialogfeld **Controller hinzufügen** den Namen *HomeController.cs*, und klicken Sie auf **Hinzufügen**.
7.  Die Datei **HomeController.cs** wird erstellt und geöffnet. Klicken Sie in der Codedatei mit der rechten Maustaste auf die ***Index()***-Methode, und klicken Sie dann auf **Ansicht hinzufügen...**.
8.  Klicken Sie im Dialogfeld **Ansicht hinzufügen** auf **OK**. Die Datei **Index.cshtml** wird in einem neuen Ordner namens **Home** erstellt.
9.  Klicken Sie mit der rechten Maustaste im **Projektmappen-Explorer** auf das Projekt **OrgIdFederationSample**, und klicken Sie dann auf **Eigenschaften**.
10. Klicken Sie im linken Menü des Fensters "Eigenschaften" auf **Web**, und wählen Sie dann **Lokalen IIS-Webserver verwenden** aus. Eventuell werden Sie in einem Dialogfeld aufgefordert, ein virtuelles Verzeichnis für das Projekt zu erstellen. Klicken Sie dann in diesem Dialogfeld auf **Ja**.
11. Erstellen Sie die Anwendung, und führen Sie sie aus. Die Indexseite Ihres HomeControllers wird angezeigt.

Schritt 2: Bereitstellen der Anwendung in einem Directory-Mandanten eines Unternehmens
--------------------------------------------------------------------------------------

In diesem Schritt wird beschrieben, wie ein Administrator eines Azure Active Directory-Kunden die MVC-Anwendung im Mandanten dieses Kunden bereitstellt und eine einmalige Anmeldung konfiguriert. Wenn dieser Schritt ausgeführt ist, können die Mitarbeiter dieses Kunden sich über ihre Office 365-Konten auf der Webanwendung anmelden.

Der Bereitstellungsvorgang beginnt mit dem Erstellen eines neuen Dienstprinzipals für die Anwendung. Dienstprinzipale werden von Azure Active Directory verwendet, um Anwendungen im Verzeichnis zu registrieren und zu authentifizieren.

1.  Laden Sie die Office 365 PowerShell Commandlets herunter, und installieren Sie sie, falls dies noch nicht geschehen ist.
2.  Führen Sie im Menü **Start** die Konsole **Microsoft Online Services-Modul für Windows PowerShell** aus. Diese Konsole bietet eine Befehlszeilenumgebung zur Konfiguration von Attributen Ihres Office 365-Mandanten, z. B. zum Erstellen und Ändern von Dienstprinzipalen.
3.  Importieren Sie das erforderliche **MSOnlineExtended**-Modul, indem Sie den folgenden Befehl eingeben und die Eingabetaste drücken:

         Import-Module MSOnlineExtended -Force

4.  Um eine Verbindung mit Ihrem Office 365-Verzeichnis herzustellen, müssen Sie die Anmeldedaten des Unternehmensadministrators eingeben. Geben Sie den folgenden Befehl ein, drücken Sie die Eingabetaste, und geben Sie dann an der Eingabeaufforderung Ihre Anmeldedaten ein:

         Connect-MsolService

5.  Nun erstellen Sie einen neuen Dienstprinzipal für die Anwendung. Geben Sie den folgenden Befehl ein, und drücken Sie die Eingabetaste:

         New-MsolServicePrincipal -ServicePrincipalNames @("OrgIdFederationSample/localhost") -DisplayName "Federation Sample Web Site" -Type Symmetric -Usage Verify -StartDate "12/01/2012" -EndDate "12/01/2013" 

    Die in diesem Schritt ausgegebenen Informationen sehen in etwa folgendermaßen aus:

         The following symmetric key was created as one was not supplied qY+Drf20Zz+A4t2w e3PebCopoCugO76My+JMVsqNBFc=
         DisplayName           : Federation Sample Web Site
         ServicePrincipalNames : {OrgIdFederationSample/localhost}
         ObjectId              : 59cab09a-3f5d-4e86-999c-2e69f682d90d
         AppPrincipalId        : 7829c758-2bef-43df-a685-717089474505
         TrustedForDelegation  : False
         AccountEnabled        : True
         KeyType               : Symmetric
         KeyId                 : f1735cbe-aa46-421b-8a1c-03b8f9bb3565
         StartDate             : 12/01/2012 08:00:00 a.m.
         EndDate               : 12/01/2013 08:00:00 a.m.
         Usage                 : Verify 

    **Hinweis**

    Sie sollten diese Ausgabe, besonders den generierten symmetrischen Schlüssel, speichern. Dieser Schlüssel wird Ihnen nur während der Erstellung des Dienstprinzipals angezeigt, und Sie können ihn in Zukunft nicht mehr abrufen. Die anderen Werte werden zur Verwendung der Graph-API benötigt, um Informationen im Verzeichnis zu lesen und zu schreiben.

6.  Im letzten Schritt wird die Antwort-URL für Ihre Anwendung eingerichtet. An die Antwort-URL werden Antworten nach Authentifizierungsversuchen gesendet. Geben Sie die folgenden Befehle ein, und drücken Sie die Eingabetaste:

         $replyUrl = New-MsolServicePrincipalAddresses -Address "https://localhost/OrgIdFederationSample" 

         Set-MsolServicePrincipal -AppPrincipalId "7829c758-2bef-43df-a685-717089474505" -Addresses $replyUrl 

Die Webanwendung wurde nun im Verzeichnis bereitgestellt und kann zur einmaligen Anwendung durch Unternehmensmitarbeiter verwendet werden.

Schritt 3: Schutz der Anwendung mit WS-Federation zur Mitarbeiteranmeldung
--------------------------------------------------------------------------

In diesem Schritt erfahren Sie, wie Sie mit Windows Identity Foundation (WIF) Unterstützung für eine Verbundanmeldung hinzufügen. Außerdem fügen Sie eine Anmeldeseite hinzu und konfigurieren eine Vertrauensstellung zwischen der Anwendung und dem Directory-Mandanten.

1.  Klicken Sie in Visual Studio mit der rechten Maustaste auf das Projekt **OrgIdFederationSample**, und wählen Sie **STS-Verweis hinzufügen...** aus. Diese Kontextmenü-Option wurde bei der Installation des WIF SDK hinzugefügt.
2.  Geben Sie im Dialogfeld **Federation Utility** unter **Anwendungs-URI** den URI im folgenden Format ein:

         spn:<Your AppPrincipalId>@<Your Directory Domain>

    **spn** bedeutet, dass der URI ein Dienstprinzipalname ist, **Your AppPrincpalId** ist der GUID-Wert für **AppPrincipalId**, der bei der Erstellung eines Dienstprinzipals generiert wird, und **Your Directory Domain** ist die \*.onmicrosoft.com-Domäne für den Directory-Mandanten. Für diese Beispielanwendung wird wie unten gezeigt ein kompletter Anwendungs-URI-Wert angegeben:

         spn:7829c758-2bef-43df-a685-717089474505@awesomecomputers.onmicrosoft.com

    Wenn Sie den Anwendungs-URI eingegeben haben, klicken Sie auf **Weiter**.

3.  Die Warnung, dass die Anwendung nicht über eine sichere https-Verbindung gehostet wird, wird ausgegeben. Diese Warnung wird dadurch erzeugt, dass die Federation Utility das **spn:**-Format nicht erkennt, aber die Anwendung verwendet dennoch eine sichere https-Verbindung. Klicken Sie auf **Ja**, um fortzufahren.

4.  Wählen Sie auf der nächsten Seite in der Federation Utility die Option **Vorhandenen STS verwenden** aus, und geben Sie dann unter **Speicherort des STS WS-Verbundmetadatendokuments** die URL für das WS-Federation-Metadatendokument ein. Die URL wird im folgenden Format angegeben:

         https://accounts.accesscontrol.windows.net/<Domain Name or Tenant ID>/FederationMetadata/2007-06/FederationMetadata.xml 

    Für diese Anwendung wird der Speicherort der WS-Federation-Metadaten folgendermaßen angegeben:

         https://accounts.accesscontrol.windows.net/fabrikam.onmicrosoft.com/FederationMetadata/2007-06/FederationMetadata.xml 

    Wenn Sie den Speicherort für die Metadaten eingegeben haben, klicken Sie auf **Weiter**.

5.  Wählen Sie auf der nächsten Seite der Federation Utility die Option **Überprüfung der Zertifikatskette deaktivieren**, und klicken Sie dann auf **Weiter**.

6.  Wählen Sie auf der nächsten Seite der Federation Utility die Option **Keine Verschlüsselung** aus, und klicken Sie dann auf **Weiter**.

7.  Auf der nächsten Seite der Federation Utility werden die vom STS bereitgestellten Ansprüche angezeigt. Prüfen Sie die Ansprüche, und klicken Sie dann auf **Weiter**.

8.  Als Nächstes konfigurieren Sie Internet Information Services (IIS) zur Unterstützung von SSL für Ihre Entwicklungsumgebung. Sie können in der Eingabeaufforderung **Ausführen** die Zeichenfolge *inetmgr* eingeben, um den IIS-Manager zu öffnen.

9.  Erweitern Sie im linken Menü des IIS-Managers den Ordner **Sites**, und klicken Sie dann auf **Default Web Site Home**. Klicken Sie rechts im Menü **Aktionen** auf **Bindungen...**.

10. Klicken Sie im Dialogfeld **Sitebindungen** auf **Hinzufügen**. Ändern Sie im Dialogfeld **Sitebindung hinzufügen** den **Typ** in ***https***, und wählen Sie dann unter **SSL-Zertifikat** die Option **IIS Express Development Certificate**. Klicken Sie auf **OK**.

11. Klicken Sie im linken Menü des IIS-Managers auf **Anwendungspools**, wählen Sie in der Liste den Eintrag **ASP.NET v4.0** aus, und klicken Sie rechts im Menü **Aktionen** auf **Erweiterte Einstellungen**.

12. Setzen Sie im Dialogfeld **Erweiterte Einstellungen** die Eigenschaft **Benutzerprofil laden** auf **true**. Klicken Sie auf **OK**.

13. Öffnen Sie in Visual Studio im Stammverzeichnis des Projekts im **Projektmappen-Explorer** die Datei **Web.config**.

14. Suchen Sie in der Datei **Web.config** nach dem Abschnitt **wsFederation**, und fügen Sie ein **reply**-Attribut mit demselben Wert wie in der Variable **\$replyUrl** hinzu, die Sie beim Erstellen des Dienstprinzipals angegeben haben. Beispiel:

        <wsFederation passiveRedirectEnabled="true" issuer="https://accounts.accesscontrol.windows.net/v2/wsfederation" realm="spn: 7829c758-2bef-43df-a685-717089474505" requireHttps="false" reply="https://localhost/OrgIdFederationSample" /> 

15. Fügen Sie im Abschnitt **system.web** einen **httpRuntime**-Knoten hinzu. Setzen Sie dafür das Attribut **requestValidationMode** auf **2.0**. Beispiel:

        <system.web>
            <httpRuntime requestValidationMode="2.0" /> 
            ...

    Speichern Sie die Datei **Web.config**.

16. Nachdem Sie nun die Authentifizierung für die Webanwendung aktiviert haben, sollte die Seite **Index** so geändert werden, dass sie die Ansprüche eines authentifizierten Benutzers anzeigt. Öffnen Sie die Datei **Index.cshtml** im Ordner **Home** unter dem Ordner **Views**.

17. Fügen Sie den folgenden Codeausschnitt zur Datei **Index.cshtml** hinzu, und speichern Sie diese:

        <p>
            @if (User.Identity.IsAuthenticated)
            {
            <ul>
                @foreach (string claim in ((Microsoft.IdentityModel.Claims.IClaimsIdentity)this.User.Identity).Claims.Select(c => c.ClaimType + " : " + c.Value))
                {
                    <li>@claim</li>
                }
            </ul>
            }
        </p> 

18. Drücken Sie nach dem Speichern der Änderungen in der Datei **Index.cshtml** die Taste **F5**, um die Anwendung auszuführen. Sie werden dann auf die Seite "Office 365-Identitätsanbieter" weitergeleitet, auf der Sie sich mit den Anmeldedaten für Ihren Directory-Mandanten anmelden können. Beispiel: *john.doe@awesomecomputers.onmicrosoft.com*.

19. Nachdem Sie sich mit Ihren Anmeldedaten angemeldet haben, werden Sie zur Indexseite Ihres HomeControllers weitergeleitet, wo die Ansprüche Ihres Kontos angezeigt werden. Dies zeigt dem Benutzer, dass er sich erfolgreich über eine von Azure Active Directory bereitgestellte einmalige Anmeldung in einer Anwendung angemeldet hat.

Schritt 4: Lesen der Verzeichnisdaten mit der Graph-API
-------------------------------------------------------

In diesem Schritt wird dargestellt, wie Sie die Graph-API verwenden, um eine Verbindung zum Azure Active Directory-Mandanten herzustellen und Daten zu lesen. Damit Sie mit der Graph-API beginnen können, laden Sie die folgende ASP.NET-Anwendung herunter: [Beispielanwendung mit Schreibunterstützung für die Graph-API](http://code.msdn.microsoft.com/Write-Sample-App-for-79e55502). Diese Anwendung enthält Helfermethoden, welche die Authentifizierung und Erstellung von Anforderungen gegenüber der Graph-API erleichtern.

Sie können außerdem Berechtigungen zum Dienstprinzipal der von Ihnen in Schritt 2 erstellten Anwendung hinzufügen. Zum Hinzufügen dieser Berechtigungen benötigen Sie den Wert "AppPrincipalId".

1.  Laden Sie die Beispielanwendung herunter, und extrahieren Sie sie im gewünschten Ordner.
2.  Bevor Sie den Beispielcode verwenden können, müssen Sie dem Dienstprinzipal zusätzliche Berechtigungen erteilen. Dank dieser Berechtigungen ist es dem Dienstprinzipal möglich, Daten mithilfe der Graph-API zu lesen. Führen Sie im Menü **Start** die Konsole **Microsoft Online Services-Modul für Windows PowerShell** aus.
3.  Sie erteilen dem Dienstprinzipal Leseberechtigungen, indem Sie ihn zur Service Support Administrator-Rolle hinzufügen. Weitere Informationen über das Zuweisen von Rollen zum Dienstprinzipal finden Sie unter [Rollenbasierte Zugriffssteuerung für Graph-API](http://msdn.microsoft.com/en-us/library/hh974466.aspx). Geben Sie den folgenden Befehl ein, und drücken Sie die Eingabetaste:

         Add-MsolRoleMember -RoleMemberType "ServicePrincipal" -RoleName "Service Support Administrator" -RoleMemberObjectId $appPrincipal.ObjectId 

4.  Die **\$appPrincipal.ObjectId**-Variable stellt die objectId Ihres Dienstprinzipals dar. Sie erhalten diese, indem Sie den folgenden Befehl eingeben und die Eingabetaste drücken:

         Get-MsolServicePrincipal -AppPrincipalId 7829c758-2bef-43df-a685-717089474505 

    **Hinweis**

    Der oben angegebene Wert **AppPrincipalId wurde zurückgegeben, als Sie den Dienstprinzipal im Schritt 2 erstellt haben.

5.  Wenn Sie die Rolle zu Ihrem Dienstprinzipal hinzugefügt haben, starten Sie Visual Studio, und öffnen Sie die Stammdatei **Web.config** der Beispielanwendung.

6.  Suchen Sie den Bereich *&lt;configuration\>* in der **Web.config**. Navigieren Sie anschließend zum Bereich *&lt;appSettings\>*. Ändern Sie die Werte für die Schlüssel *TenantDomainName*, *AppPrincipalId* und *SymmetricKey* in die Werte für Ihren Dienstprinzipal. Beispiel:

         <appSettings> 
             ...
             <add key="TenantDomainName" value="awesomecomputers.onmicrosoft.com"/> 
             ...
             <add key="AppPrincipalId" value="7829c758-2bef-43df-a685-717089474505"/>     
             ...
             <add key="SymmetricKey" value="qY+Drf20Zz+A4t2w e3PebCopoCugO76My+JMVsqNBFc=" /> 
             ...
         </appSettings>

7.  Speichern Sie die Datei **Web.config**. Drücken Sie danach **F5**, um die Anwendung auszuführen.

8.  Die Beispielanwendung zeigt ein Menü zum Zugreifen auf alle Ihre Benutzer, Unternehmensadministratoren und weitere an. Wenn Sie auf einen der Links klicken, werden die in Ihrem Mandanten gespeicherten Informationen mithilfe der Graph-API zurückgegeben.

Zusammenfassung
---------------

In diesem Lernprogramm haben Sie erfahren, wie Sie eine Anwendung für einen einzelnen Mandanten erstellen und konfigurieren können, die die Möglichkeiten einer einmaligen Anwendung mit Azure Active Directory nutzt. Außerdem haben Sie mithilfe der Graph-API auf die Verzeichnisdaten des Mandanten zugegriffen. Es wird empfohlen, dass Sie sich mit der Beispielanwendung vertraut machen, um zu erfassen, wie Sie aus der Graph-API in Ihrer eigenen Anwendung Nutzen ziehen können.

Weitere Informationen zur Graph-API erhalten Sie, wenn [Sie die betreffenden Themen unter MSDN lesen](http://msdn.microsoft.com/en-us/library/hh974476.aspx). Sie können auch Anwendungen für mehrere Mandanten in Azure Active Directory erstellen. Lesen Sie dafür das Lernprogramm [Entwickeln von Cloud-Anwendungen für mehrere Mandanten mit Azure Active Directory](http://g.microsoftonline.com/0AX00en/121).

