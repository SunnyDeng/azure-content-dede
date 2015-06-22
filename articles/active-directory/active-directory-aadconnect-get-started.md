<properties 
	pageTitle="Erste Schritte mit Azure AD Connect" 
	description="Informationen zum Herunterladen, Installieren und Ausführen des Setup-Assistenten für Azure AD Connect." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="lisatoft"/>

<tags 
	ms.service="azure-active-directory-connect" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/02/2015" 
	ms.author="billmath"/>

# Erste Schritte mit Azure AD Connect


<div class="dev-center-tutorial-selector sublanding">
<a href="/de-de/documentation/articles/active-directory-aadconnect/" title="Worum handelt es sich?" class="current">Worum handelt es sich?</a> <a href="/de-de/documentation/articles/active-directory-aadconnect-how-it-works/" title="Funktionsweise">Funktionsweise</a> <a href="/de-de/documentation/articles/active-directory-aadconnect-get-started/" title="Erste Schritte">Erste Schritte</a> <a href="/de-de/documentation/articles/active-directory-aadconnect-whats-next/" title="Wie geht es weiter?">Wie geht es weiter?</a> <a href="/de-de/documentation/articles/active-directory-aadconnect-learn-more/" title="Weitere Informationen">Weitere Informationen</a>
</div>

Die folgende Dokumentation hilft Ihnen beim Einstieg mit Azure Active Directory Connect.

## Azure AD Connect herunterladen



Für die ersten Schritte mit Azure AD Connect können Sie die neuste Version wie folgt herunterladen: [Herunterladen der öffentlichen Vorschau von Azure AD Connect](http://connect.microsoft.com/site1164/program8612)

## Vor der Installation von Azure AD Connect
Vor der Installation von Azure AD Connect gibt es einige Dinge, die Sie benötigen.

- Ein Azure-Abonnement oder ein [Azure-Testabonnement](http://azure.microsoft.com/pricing/free-trial/)
- Azure AD Premium oder eine [Azure AD Premium-Testversion](http://aka.ms/aadptrial)
- Ein globales Azure AD-Administratorkonto für den Azure AD-Mandanten, den Sie integrieren möchten.
- Ein AD-Domänencontroller oder Mitgliedsserver mit Windows Server 2008 oder höher.
- Ein Enterprise-Administratorkonto für Ihr lokales Active Directory
- Optional: Ein Testbenutzerkonto zur Überprüfung der Synchronisierung. 

Wenn Sie das SSO mit AD FS-Option verwenden möchten, benötigen Sie auch Folgendes:

- Lokale Administratorrechte auf Ihren beabsichtigten Verbundservern.
- Lokale Administratorrechte für jeden Arbeitsgruppenserver (keiner Domäne angehörend), auf dem Sie die Webanwendungsproxy-Rolle bereitstellen möchten.
- Ein Windows Server 2012 R2-Server für den Verbundserver.
- Ein Windows Server 2012 R2-Server für den Webanwendungsproxy.
-  Eine PFX-Datei mit einem SSL-Zertifikat für den vorgesehenen Verbunddienstnamen, z. B. fs.contoso.com.
- Der Computer, auf dem Sie den Assistenten ausführen, muss eine Verbindung mit allen weiteren Computern herstellen können, auf denen Sie AD FS oder den Webanwendungsproxy über die Windows-Remoteverwaltung installieren möchten.


## Installieren von Azure AD Connect

Nachdem Sie Azure AD Connect heruntergeladen haben, installieren Sie es wie folgt.

1. Melden Sie sich auf dem Server an, auf dem Sie Azure AD Connect als Enterprise-Administrator installieren möchten.
2. Navigieren Sie zu AzureADConnect.msi und doppelklicken Sie darauf
3. Gehen Sie Schritt für Schritt durch den Assistenten mit den Express- oder den benutzerdefinierten Einstellungen
4. Optional: Verwenden Sie Ihr Test-Benutzerkonto, um sich bei einem Clouddienst wie Office 365 zum Testen anzumelden.

<center>![Welcome to Azure AD Connect](./media/active-directory-aadconnect-get-started/aadConnect_Welcome.png)</center>

## Optionale Konfigurationen für Synchronisierungsdienste
Bei der Installation der Synchronisierungsdienste können Sie den optionalen Konfigurationsabschnitt deaktiviert lassen und  Azure AD Connect wird alles automatisch einrichten. Dies umfasst das Einrichten einer SQL Server 2012 Express-Instanz und die Erstellung der entsprechenden Gruppen sowie das Zuweisen der Berechtigungen für die Gruppen. Wenn Sie die Standardeinstellungen ändern möchten, können Sie die unten stehende Tabelle verwenden, um sich die verfügbaren optionalen Konfigurationsoptionen anzusehen.

Optionale Konfiguration | Beschreibung 
------------- | ------------- |
SQL Server-Name |Ermöglicht Ihnen die Angabe des SQL-Servernamens und des Instanzennamens. Wählen Sie diese Option aus, wenn Sie bereits über einen Datenbankserver verfügen, den Sie verwenden möchten.
Dienstkonto |Standardmäßig erstellt Azure AD Connect ein Dienstkonto für die zu verwendenden Synchronisierungsdienste. Dabei ergibt sich das Problem, dass das Kennwort automatisch generiert und der Person unbekannt ist, die Azure AD Connect installiert. In den meisten Szenarien ist dies in Ordnung, wenn Sie jedoch erweiterte Konfigurationen erstellen möchten, wie z. B. das Festlegen des Gültigkeitsbereichs der zu synchronisierenden Organisationseinheiten, sollten Sie ein Konto erstellen und Ihr eigenes Kennwort wählen. |
Berechtigungen | Standardmäßig erstellt Azure AD Connect vier Gruppen, wenn die Synchronisierungsdienste installiert sind. Diese Gruppen sind: Administratorengruppe, Operatorengruppe, Durchsuchen-Gruppe und die Gruppe "Kennwort zurücksetzen". Wenn Sie eigene Gruppen angeben möchten, können Sie das hier tun.
Importieren von Einstellungen |Verwenden Sie diese Option, wenn Sie Konfigurationsinformationen von DirSync aus Azure AD Sync importieren.|



## Expressinstallation
Die Auswahl der Express-Einstellungen ist die Standardoption und eines der häufigsten Szenarios. Auf diese Weise stellt Azure AD Connect die Synchronisierung mit der Kennwort-Hash-Synchronisierungsoption bereit. Dies gilt nur für eine einzige Gesamtstruktur und ermöglicht den Benutzern die Verwendung ihres lokalen Kennworts beim Anmelden in der Cloud. Bei der Express-Installation wird automatisch eine Synchronisierung nach Abschluss der Installation gestartet. Mit dieser Option können Sie mit nur sechs kurzen Klicks Ihr lokales Verzeichnis auf die Cloud erweitern.

<center>![Express Installation](./media/active-directory-aadconnect-get-started/express.png)</center>

## Benutzerdefinierte Installation

Mit der benutzerdefinierten Installation können Sie mehrere verschiedene Optionen auswählen. Die folgende Tabelle beschreibt die Seiten des Assistenten, die nach Auswahl der benutzerdefinierten Installation verfügbar sind.

Seitenname | Beschreibung
-------------------    | ------------- | 
Benutzeranmeldung|Auf dieser Seite können Sie auswählen, ob die Kennwortsynchronisierung, der Verbund mit AD FS oder keines von beiden verwendet wird.
Verbinden mit den Verzeichnissen|Auf dieser Seite können Sie ein oder mehrere Verzeichnisse hinzufügen, mit denen eine Synchronisierung stattfinden soll.
Synchronisierungsfilterung| Hier können Sie bestimmen, ob Sie alle Benutzer und Gruppen synchronisieren möchten, oder ob Sie eine Gruppe pro Verzeichnis angeben und nur diese synchronisieren möchten.
Lokale Identitäten|Hier können Sie angeben, ob Benutzer entweder nur einmal in allen Verzeichnissen vorhanden sind, die in Connect zu Ihrer Verzeichnisseite hinzugefügt wurden, oder in mehreren Verzeichnissen. Wenn Benutzer in mehreren Verzeichnissen vorhanden sind, müssen Sie ein Attribut auswählen, das diese Benutzer in den Verzeichnissen eindeutig identifiziert. Zum Beispiel sind Mail-Attribut, ObjectSID oder SAMAccountName alle gängige Attribute zur eindeutigen Identifizierung von Benutzern.
Azure-Identitäten|Auf dieser Seite geben Sie den Quellanker für den Identitätsverbund an.
Optionale Features|In der unten stehenden Tabelle werden die optionalen Features, die Sie auswählen können, kurz beschrieben.

<center>![Express Installation](./media/active-directory-aadconnect-get-started/of.png)</center>


Optionale Features | Beschreibung
-------------------    | ------------- | 
Exchange-Hybridbereitstellung |Die Exchange-Hybridbereitstellungsfunktion ermöglicht die Koexistenz von Exchange-Postfächern sowohl lokal und als auch in Azure durch die Synchronisierung eines bestimmten Attributsatzes von Azure AD zurück in Ihr lokales Verzeichnis.
Azure AD-App- und Attributfilterung|Durch Aktivieren von Azure AD-App- und Attributfilterung kann die Sammlung der synchronisierten Attribute auf einer nachfolgenden Seite des Assistenten auf eine bestimmte Menge festgelegt werden. Zwei zusätzliche Konfigurationsseiten werden daraufhin im Assistenten geöffnet.  
Rückschreiben von Kennwörtern|Durch Aktivieren des Rückschreibens von Kennwörtern werden Kennwortänderungen aus Azure AD in Ihr lokales Verzeichnis zurückgeschrieben.
Rückschreiben von Benutzern|Durch Aktivieren des Rückschreibens von Benutzern werden in Azure AD erstellte Benutzer in Ihr lokales Verzeichnis zurückgeschrieben. Dadurch wird eine zusätzliche Konfigurationsseite im Assistenten geöffnet.  
Gerätesynchronisierung|Durch Aktivieren der Gerätesynchronisierung kann die Gerätekonfiguration in Azure AD geschrieben werden.
Verzeichniserweiterungen-Attributsynchronisierung|Durch Aktivieren der Verzeichniserweiterungen-Attributsynchronisierung werden angegebene Attribute mit Azure AD synchronisiert. Dadurch wird eine zusätzliche Konfigurationsseite im Assistenten geöffnet.  

Zusätzliche Konfigurationsoptionen, z. B. das Ändern der Standardkonfiguration, die Verwendung des Synchronisierungsregel-Editors und die deklarative Bereitstellung, finden Sie unter [Verwalten von Azure AD Connect](active-directory-aadconnect-whats-next.md)

## Azure AD Connect unterstützende Komponenten

Folgendes ist eine Liste der erforderlichen und unterstützenden Komponenten, die Azure AD Connect auf dem Server installiert, auf dem Azure AD Connect eingerichtet ist. Diese Liste ist für eine einfache Expressinstallation. Wenn Sie einen anderen SQL Server auf der Installationsseite für Synchronisierungsdienste verwenden möchten, werden die unten aufgeführten SQL Server 2012-Komponenten nicht installiert.

- Forefront Identity Manager – Azure Active Directory Connector
- Microsoft SQL Server 2012 – Befehlszeilenprogramme
- Microsoft SQL Server 2012 Native Client
- Microsoft SQL Server 2012 Express LocalDB
- Azure Active Directory-Modul für Windows PowerShell
- Microsoft Online Services-Anmeldeassistent für IT-Experten
- Microsoft Visual C++ 2013 Redistributionspaket


**Zusätzliche Ressourcen**

* [Verwenden Sie Ihre lokale Identitätsinfrastruktur in der Cloud](active-directory-aadconnect.md)
* [Funktionsweise von Azure AD Connect](active-directory-aadconnect-how-it-works.md)
* [Wie geht es weiter mit Azure AD Connect?](active-directory-aadconnect-whats-next.md)
* [Weitere Informationen](active-directory-aadconnect-learn-more.md)
* [Azure AD Connect auf MSDN](https://msdn.microsoft.com/library/azure/dn832695.aspx)

<!---HONumber=58--> 