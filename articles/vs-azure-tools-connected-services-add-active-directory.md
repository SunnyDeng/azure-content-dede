<properties 
   pageTitle="Hinzufügen von Azure Active Directory mithilfe von verbundenen Diensten in Visual Studio | Microsoft Azure"
	description="Fügen Sie Azure Active Directory mithilfe des Dialogfelds Verbundene Dienste hinzufügen in Visual Studio hinzu"
	services="visual-studio-online"
	documentationCenter="na"
	authors="patshea123"
	manager="douge"
	editor="tlee"/>

<tags  ms.service="visual-studio-online"" ms.devlang="multiple" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="visual-studio-online" ms.date="08/12/2015" ms.author="patshea"/>

# Hinzufügen von Azure Active Directory mithilfe von verbundenen Diensten in Visual Studio 

##Übersicht
Mithilfe von Azure Active Directory (Azure AD) können Sie einmaliges Anmelden (Single Sign-On, SSO) für ASP.NET MVC-Webanwendungen oder AD-Authentifizierung in Web-API-Diensten unterstützen. Mit der Azure AD-Authentifizierung können Benutzer ihre Konten in Azure AD verwenden, um eine Verbindung mit Webanwendungen herzustellen. Die Vorteile der Azure AD-Authentifizierung mit Web-API sind u. a. eine verbesserte Sicherheit, wenn eine API über eine Webanwendung verfügbar gemacht wird. Mit Azure AD benötigen Sie kein separates Authentifizierungssystem mit eigenem Konto und Benutzerverwaltung.

## Unterstützte Projekttypen

Mit dem Dialogfeld "Verbundene Dienste" können Sie in den folgenden Projekttypen eine Verbindung mit Azure AD herstellen.

- ASP.NET MVC-Projekte

- ASP.NET-Web-API-Projekte


### Herstellen einer Verbindung mit Azure AD mithilfe des Dialogfelds "Verbundene Dienste"

1. Stellen Sie sicher, dass Sie über ein Azure-Konto verfügen. Wenn Sie nicht über ein Azure-Konto verfügen, können Sie sich für eine [kostenlose Testversion](http://go.microsoft.com/fwlink/?LinkId=518146) registrieren.

1. Öffnen Sie in Visual Studio das Kontextmenü für den Knoten **Verweise** in Ihrem Projekt, und wählen Sie **Verbundene Dienste hinzufügen** aus.
1. Wählen Sie **Azure AD-Authentifizierung** und dann **Konfigurieren** aus.

    ![Wählen Sie "Azure AD-Authentifizierung hinzufügen" aus](./media/vs-azure-tools-connected-services-add-active-directory/connected-services-add-active-directory.png)

1. Aktivieren Sie auf der ersten Seite von **Azure-Authentifizierung konfigurieren** das Kontrollkästchen **Einmaliges Anmelden mit Azure AD konfigurieren**.

    Wenn Ihr Projekt mit einer anderen Authentifizierungskonfiguration konfiguriert ist, warnt Sie der Assistent, dass die vorherige Konfiguration deaktiviert wird, wenn Sie fortfahren.

    ![Konfigurieren Sie Azure AD im Assistenten](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-1.png)

1.  Wählen Sie auf der zweiten Seite eine Domäne aus der Dropdownliste **Domäne** aus. Die Liste der Domänen enthält alle Domänen, auf die mit den im Dialogfeld "Kontoeinstellungen" aufgeführten Konten zugegriffen werden kann. Alternativ können Sie einen Domänennamen eingeben, wenn Sie den gewünschten nicht finden, beispielsweise "mydomain.onmicrosoft.com". Sie können die Option zum Erstellen einer neuen Azure AD-App auswählen oder die Einstellungen einer vorhandenen Azure AD-App verwenden.

    ![Konfigurieren Sie Azure AD im Assistenten](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-2.png)


1. Stellen Sie auf der dritten Seite des Assistenten sicher, dass **Verzeichnisdaten lesen** aktiviert ist. Der Assistent füllt das Feld **Geheimer Clientschlüssel** aus.

    ![Konfigurieren Sie Azure AD im Assistenten](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-3.png)

1. Klicken Sie auf die Schaltfläche **Fertig stellen**. Das Dialogfeld fügt den erforderlichen Konfigurationscode und Verweise hinzu, um das Projekt für die Azure AD-Authentifizierung zu aktivieren. Die AD-Domäne wird im Azure-Portal angezeigt.

1. Überprüfen Sie die Seite "Erste Schritte", die in Ihrem Browser angezeigt wird, um mögliche nächste Schritte zu ermitteln, und die Seite "Was ist passiert", um zu sehen, wie Ihr Projekt geändert wurde. Wenn Sie überprüfen möchten, ob alles funktioniert hat, öffnen Sie die geänderten Konfigurationsdateien, und stellen Sie sicher, dass die Einstellungen, die unter "Was ist passiert" genannt werden, vorhanden sind. Beispiel: Der wichtigsten web.config-Datei in einem ASP.NET MVC-Projekt werden die folgenden Einstellungen hinzugefügt:

        <appSettings> 
            <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
            <add key="ida:AADInstance" value="https://login.windows.net/" />
            <add key="ida:Domain" value="Your selected domain" />
            <add key="ida:TenantId" value="The Id of your selected Azure AD Tenant" />
            <add key="ida:PostLogoutRedirectUri" value="The default redirect URI from the project" />
        </appSettings>

## Änderungen am Projekt

Beim Ausführen des Assistenten fügt Visual Studio Ihrem Projekt Azure AD und entsprechende Verweise hinzu. Zudem werden Konfigurationsdateien und Codedateien im Projekt geändert, um Unterstützung für Azure AD hinzuzufügen. Die genauen Änderungen, die von Visual Studio vorgenommen werden, hängen vom Projekttyp ab. Ausführliche Informationen dazu, wie ASP.NET MVC-Projekte geändert werden, finden Sie unter [Was ist passiert – MVC-Projekte](http://go.microsoft.com/fwlink/p/?LinkID=513809). Informationen zu Web-API-Projekten finden Sie unter [Was ist passiert – Web-API-Projekte](http://go.microsoft.com/fwlink/p/?LinkId=513810).

##Nächste Schritte

Stellen Sie Fragen, und holen Sie sich Hilfe.

 - [MSDN-Forum: Azure AD](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD)

 - [Azure AD-Dokumentation](http://azure.microsoft.com/documentation/services/active-directory/)

 - [Blogbeitrag: Intro to Azure AD](http://blogs.msdn.com/b/brunoterkaly/archive/2014/03/03/introduction-to-windows-azure-active-directory.aspx) (in englischer Sprache)

<!---HONumber=August15_HO9-->