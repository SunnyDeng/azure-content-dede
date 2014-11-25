<properties umbracoNaviHide="0" pageTitle="How To Configure Cloud Services" metaKeywords="Azure cloud services, cloud service, configure cloud service" description="Learn how to configure Azure cloud services." linkid="manage-windows-how-to-guide-storage-accounts" urlDisplayName="How to: storage accounts" headerExpose="" footerExpose="" disqusComments="1" title="Sign up for Azure as an organization" authors="terrylan" manager="terrylan" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="terrylan" />

# Als Unternehmen für Azure registrieren

[WACOM.INCLUDE [Haftungsausschluss](../includes/disclaimer.md)]

Bis vor Kurzem war es nur mit einem Microsoft-Konto (Windows Live-ID) möglich, sich für ein neues Azure-Abonnement anzumelden. Nun unterstützt Azure auch eine der beiden folgenden Kontomethoden zur Registrierung:

-   **Microsoft-Konten** (von Ihnen für den privaten Gebrauch erstellt) - Bieten Zugriff auf alle für den normalen Computerbenutzer entwickelten Microsoft-Produkte und Clouddienste wie Outlook (Hotmail), Messenger, SkyDrive, MSN, Xbox LIVE oder Office Live. Durch eine Anmeldung bei Outlook wird automatisch ein Microsoft-Konto mit einer @Outlook.com-Adresse erstellt. Sobald es erstellt ist, kann ein Microsoft-Konto für den Zugriff auf für den normalen Computerbenutzer entwickelte Microsoft-Clouddienste und/oder Azure verwendet werden. [Weitere Informationen][Weitere Informationen]

-   **Organisationskonten** (von einem Administrator für geschäftliche/akademische Zwecke ausgegeben) - Bieten Zugriff auf alle Microsoft-Clouddienste für kleine, mittlere und große Unternehmen wie Azure, Windows Intune oder Office 365. Wenn Sie sich bei einem dieser Dienste als Organisation anmelden, wird automatisch in Azure Active Directory ein cloudbasierter Mandant bereitgestellt, der diese Organisation repräsentiert. [Weitere Informationen][1]

    Nach der Erstellung dieses Mandanten kann ein Administrator Organisationskonten an alle seine Mitarbeiter/Studenten ausgeben und diesen Konten Lizenzen für die Clouddienst-Abonnements zuweisen, auf die sie zugreifen müssen, z. B. Azure.

    *Möchten Sie sich als Organisation bei Azure anmelden?* **Jetzt anmelden**

## Was ist Azure Active Directory?

Active Directory ist ein Dienst, den Kunden zum Verwalten ihres lokalen Verzeichnisses über das Windows Server-Betriebssystem nutzen können. Ähnlich dazu ist Azure Active Directory (Azure AD) ein Dienst, der über Azure zur Verfügung gestellt wird, sodass Sie das cloudbasierte Verzeichnis Ihres Unternehmens verwalten können. [Weitere Informationen][2]

Da es sich um das Cloud-Verzeichnis Ihres Unternehmens handelt, entscheiden Sie, welche Benutzer es gibt, welche Informationen in der Cloud gespeichert werden, wer diese Informationen nutzen oder verwalten kann und welche Anwendungen oder Dienste auf die Informationen zugreifen dürfen. Wenn Sie Azure AD verwenden, ist es die Aufgabe von Microsoft, Active Directory in der Cloud bereitzustellen und eine hohe Skalierbarkeit, Hochverfügbarkeit und integrierte Notfallwiederherstellung zu bieten sowie Ihre Anforderungen an Vertraulichkeit und Sicherheit der Informationen Ihres Unternehmens zu erfüllen.

### Integration mit lokalem Active Directory

Wenn in Ihrem Unternehmen bereits das lokale Active Directory verwendet wird, können Sie die Integrationsfunktionen von Azure AD nutzen, wie etwa Verzeichnissynchronisierung und einmaliges Anmelden, um die Reichweite Ihrer vorhandenen lokalen Identitäten für eine optimierte Benutzerfreundlichkeit für Administratoren und Benutzer weiter auf die Cloud auszudehnen. [Weitere Informationen][3]

  [Weitere Informationen]: http://windows.microsoft.com/de-de/windows-live/sign-in-what-is-microsoft-account
  [1]: http://technet.microsoft.com/de-de/library/jj573650
  [2]: http://technet.microsoft.com/de-de/library/hh967619
  [3]: http://technet.microsoft.com/de-de/library/jj573653
