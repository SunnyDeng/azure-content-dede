<properties 
                pageTitle="Grundlegendes zum Zugriff auf Ressourcen in Azure" 
                description="In diesem Thema werden Konzepte zum Einsatz von Abonnementadministratoren erklärt, um den Zugriff auf Ressourcen im ganzen Azure-Portal zu steuern." 
                services="active-directory" 
                documentationCenter="" 
                authors="markusvi" 
                manager="swadhwa" 
                editor=""/>

<tags 
                ms.service="active-directory" 
                ms.workload="identity" 
                ms.tgt_pltfrm="na" 
                ms.devlang="na" 
                ms.topic="article" 
                ms.date="08/10/2015" 
                ms.author="markusvi"/>


# Grundlegendes zum Zugriff auf Ressourcen in Azure


> [AZURE.NOTE]In diesem Thema werden Konzepte zum Einsatz von Abonnementadministratoren erklärt, um den Zugriff auf Ressourcen im ganzen Azure-Portal zu steuern. Als Alternative bietet das Azure-Vorschauportal die [rollenbasierte Zugriffssteuerung](role-based-access-control-configure.md) für eine detailliertere Verwaltung der Azure-Ressourcen.

Im Oktober 2013 wurden das Azure-Verwaltungsportal und die Dienstverwaltungs-APIs in Azure Active Directory integriert, um die Benutzerfreundlichkeit bei der Verwaltung des Azure-Ressourcenzugriffs zu verbessern. Azure Active Directory bietet bereits leistungsstarke Funktionen wie Benutzerverwaltung, lokale Verzeichnissynchronisierung, mehrstufige Authentifizierung und Anwendungszugriffssteuerung. Diese Funktionen sollten natürlich auch für die allgemeine Verwaltung von Azure-Ressourcen zur Verfügung stehen.

Die Zugriffssteuerung in Azure unterliegt zunächst den Abrechnungsaspekten. Der Besitzer eines Azure-Kontos, auf das über das [Azure Accounts Center](https://account.windowsazure.com/subscriptions) zugegriffen wird, ist der Kontoadministrator (Account Administrator, AA). Abonnements sind ein Container für die Abrechnung, fungieren jedoch auch als Sicherheitsgrenze: Jedes Abonnement wird von einem Dienstadministrator (SA) verwaltet, der Azure-Ressourcen in diesem Abonnement über das [Azure-Verwaltungsportal](https://manage.windowsazure.com/) hinzufügen, entfernen und ändern kann. Der standardmäßige Dienstadministrator eines neuen Abonnements ist der Kontoadministrator. Der Kontoadministrator kann den Dienstadministrator jedoch über das Azure Accounts Center ändern.
 
<br><br>![Azure-Konten][1]

Abonnements sind außerdem einem Verzeichnis zugeordnet. Durch das Verzeichnis wird eine Gruppe von Benutzern definiert. Diese können Benutzer aus der Organisation (Unternehmen oder Schule) sein, die das Verzeichnis erstellt hat, oder externe Benutzer (also Microsoft-Konten). Auf die Abonnements kann eine Teilmenge dieser Verzeichnisbenutzer zugreifen, denen entweder die Funktion eines Dienstadministrators oder eines Co-Administrators (CA) zugewiesen wurde. Die einzige Ausnahme besteht darin, dass Microsoft-Konten (früher Windows Live ID) aus Gründen der Kompatibilität mit älteren Versionen als Dienstadministrator oder Co-Administrator zugewiesen werden können, ohne dass sie ihm Verzeichnis enthalten sein müssen.

<br><br>![Zugriffssteuerung in Azure][2]

 
Mithilfe der Funktionen im Azure-Verwaltungsportal können Dienstadministratoren, die über ein Microsoft-Konto angemeldet sind, das einem Abonnement zugeordnete Verzeichnis mit dem Befehl **Verzeichnis bearbeiten** unter **Einstellungen** auf der Seite **Abonnements** ändern. Beachten Sie, dass sich dieser Vorgang auf die Zugriffssteuerung dieses Abonnements auswirkt.



> [AZURE.NOTE]Der Befehl **Verzeichnis bearbeiten** im Azure-Verwaltungsportal steht nicht für Benutzer zur Verfügung, die über ein Geschäfts- oder Schulkonto angemeldet sind, weil sich diese Konten nur an dem Verzeichnis anmelden können, zu dem sie gehören.

<br><br>![Einfache Benutzeranmeldung − Ablauf][3]

Im einfachen Fall erzwingt eine Organisation (etwa Contoso), dass die Abrechnung und die Zugriffssteuerung für dieselbe Gruppe von Abonnements erfolgen. Dies bedeutet, dass das Verzeichnis Abonnements zugeordnet ist, die sich im Besitz eines einzigen Azure-Kontos befinden. Nach der erfolgreichen Anmeldung beim Azure-Verwaltungsportal sehen Benutzer zwei Ressourcenauflistungen (in der Abbildung oben orangefarben dargestellt):


- Verzeichnisse, in denen ihr Benutzerkonto enthalten ist (aus dem Verzeichnis selbst oder als Fremdprinzipal hinzugefügt). Da das zum Anmelden verwendete Verzeichnis für diese Berechnung nicht relevant ist, werden Ihre Verzeichnisse immer angezeigt, unabhängig davon, wo Sie sich angemeldet haben.

- Ressourcen, die den Abonnements angehören, die dem für die Anmeldung verwendeten Verzeichnis zugeordnet sind UND auf die der Benutzer zugreifen kann (d. h. für die er als Dienstadministrator oder Co-Administrator fungiert).


<br><br>![Benutzer mit mehreren Abonnements und Verzeichnissen][4]


Benutzer mit Abonnements, die mehrere Verzeichnisse umfassen, können den aktuellen Kontext des Azure-Verwaltungsportals mithilfe des Abonnementfilters wechseln. Im Hintergrund führt dies zu einer getrennten Anmeldung bei einem anderen Verzeichnis, allerdings ist durch einmaliges Anmelden (SSO) eine nahtlose Anmeldung gewährleistet.

Da die Abonnements in einer einzelnen Verzeichnisansicht dargestellt werden, können Vorgänge wie das Verschieben von Ressourcen zwischen Abonnements erschwert werden. Um die Ressourcen zu übertragen, müssen Sie die Abonnements u. U. zuerst mit dem Befehl **Verzeichnis bearbeiten** unter **Einstellungen** auf der Seite "Abonnements" demselben Verzeichnis zuordnen.



<!--Image references-->
[1]: ./media/active-directory-understanding-resource-access/IC707931.png
[2]: ./media/active-directory-understanding-resource-access/IC707932.png
[3]: ./media/active-directory-understanding-resource-access/IC707933.png
[4]: ./media/active-directory-understanding-resource-access/IC707934.png

<!---HONumber=Oct15_HO3-->