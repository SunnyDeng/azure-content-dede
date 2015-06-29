<properties 
	pageTitle="Erste Schritte mit Azure AD Connect" 
	description="Informationen zum Herunterladen, Installieren und Ausführen des Setup-Assistenten für Azure AD Connect." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="billmath"/>

# Erste Schritte mit Azure AD Connect

<div class="dev-center-tutorial-selector sublanding">
<a href="../active-directory-aadconnect/" title="Worum handelt es sich?">Worum handelt es sich?</a> <a href="../active-directory-aadconnect-how-it-works/" title="Funktionsweise">Funktionsweise</a> <a href="../active-directory-aadconnect-get-started/" title="Erste Schritte" class="current">Erste Schritte</a> <a href="../active-directory-aadconnect-whats-next/" title="Nächste Schritte">Nächste Schritte</a> <a href="../active-directory-aadconnect-learn-more/" title="Weitere Informationen">Weitere Informationen</a>
</div>


Die folgende Dokumentation hilft Ihnen beim Einstieg mit Azure Active Directory Connect. In dieser Dokumentation wird die Verwendung der Expressinstallation für Azure AD Connect behandelt. Informationen zur benutzerdefinierten Installation finden Sie unter [Custom installation of Azure AD Connect](active-directory-aadconnect-get-started-custom.md) (in englischer Sprache). Informationen zum Aktualisieren von DirSync auf Azure AD Connect finden Sie unter [Upgrading DirSync to Azure Active Directory Connect](active-directory-aadconnect-dirsync-upgrade-get-started.md) (in englischer Sprache).

## Azure AD Connect herunterladen



Für Ihre ersten Schritte mit Azure AD Connect können Sie die neuste Version über den folgenden Link herunterladen: [Download Azure AD Connect](http://go.microsoft.com/fwlink/?LinkID=615771) (in englischer Sprache)

## Vor der Installation von Azure AD Connect
Vor der Installation von Azure AD Connect mit Express-Einstellungen benötigen Sie Folgendes.


 
- Ein Azure-Abonnement oder ein [Azure-Testabonnement](http://azure.microsoft.com/pricing/free-trial/) – Dies ist nur erforderlich für den Zugriff auf das Azure-Portal und nicht für die Verwendung von Azure AD Connect. Bei Verwendung von PowerShell oder Office 365 benötigen Sie für Azure AD Connect kein Azure-Abonnement.
- Ein globales Azure AD-Administratorkonto für den Azure AD-Mandanten, den Sie integrieren möchten.
- Ein AD-Domänencontroller oder Mitgliedsserver mit Windows Server 2008 oder höher.
- Ein Enterprise-Administratorkonto für Ihr lokales Active Directory
- Optional: Ein Testbenutzerkonto zur Überprüfung der Synchronisierung. 


Informationen zu weiteren Anforderungen für benutzerdefinierte Optionen, z. B. für mehrere Gesamtstrukturen oder die Verbundanmeldung, finden Sie [hier](active-directory-aadconnect-get-started-custom.md).


## Expressinstallation von Azure AD Connect
Die Auswahl der Express-Einstellungen ist die Standardoption und eines der häufigsten Szenarios. Auf diese Weise stellt Azure AD Connect die Synchronisierung mit der Kennwort-Hash-Synchronisierungsoption bereit. Dies gilt nur für eine einzige Gesamtstruktur und ermöglicht den Benutzern die Verwendung ihres lokalen Kennworts beim Anmelden in der Cloud. Bei Verwendung der Express-Einstellungen wird nach Abschluss der Installation automatisch eine Synchronisierung gestartet (dies können Sie jedoch auch deaktivieren). Mit dieser Option können Sie mit nur wenigen kurzen Klicks Ihr lokales Verzeichnis auf die Cloud erweitern.

<center>![Willkommen bei Azure&#160;AD Connect](./media/active-directory-aadconnect-get-started/welcome.png)</center>

### So installieren Sie Azure AD Connect mit Express-Einstellungen
--------------------------------------------------------------------------------------------

1. Melden Sie sich als Enterprise-Administrator an dem Server an, auf dem Sie Azure AD Connect installieren möchten. Dies sollte der Server sein, der als Synchronisierungsserver verwendet werden soll.
2. Navigieren Sie zu AzureADConnect.msi und doppelklicken Sie darauf
3. Aktivieren Sie auf der Willkommensseite das Kontrollkästchen zum Zustimmen zu den Lizenzbedingungen, und klicken Sie auf **Weiter**.
4. Klicken Sie im Bildschirm "Express-Einstellungen" auf **Express-Einstellungen verwenden**.
<center>![Willkommen bei Azure AD Connect](./media/active-directory-aadconnect-get-started/express.png)</center>
6. Geben Sie im Bildschirm "Mit Azure AD verbinden" den Benutzernamen und das Kennwort eines globalen Azure-Administrators für Azure AD ein. Klicken Sie auf **Weiter**.
8. Geben Sie im Bildschirm "Mit AD DS verbinden" den Benutzernamen und das Kennwort für ein Enterprise-Administratorkonto ein. Klicken Sie auf **Weiter**.
<center>![Willkommen bei Azure&#160;AD Connect](./media/active-directory-aadconnect-get-started/install4.png)</center>
9. Klicken Sie im Bildschirm "Bereit zur Konfiguration" auf **Installieren**.
	- Optional können Sie auf der Seite "Bereit zur Konfiguration" das Kontrollkästchen **Starten Sie den Synchronisierungsvorgang, sobald die Anfangskonfiguration abgeschlossen wurde** deaktivieren. Der Assistent konfiguriert dann zwar die Synchronisierung, die Aufgabe bleibt jedoch deaktiviert, sodass sie erst ausgeführt wird, wenn Sie sie in der Aufgabenplanung manuell aktivieren. Sobald die Aufgabe aktiviert ist, wird die Synchronisierung alle drei Stunden ausgeführt.
	- Durch Aktivieren des entsprechenden Kontrollkästchens können Sie wahlweise auch die Konfiguration von Synchronisierungsdiensten für die **Exchange-Hybridbereitstellung** festlegen. Wenn Sie Exchange-Postfächer nicht gleichzeitig lokal und in der Cloud bereitstellen möchten, müssen Sie diese Option nicht aktivieren.

<center>![Willkommen bei Azure&#160;AD Connect](./media/active-directory-aadconnect-get-started/readyinstall.png)</center>
8. Klicken Sie nach Abschluss der Installation auf **Beenden**.


<br> <br>

Im Folgenden können Sie sich ein Video zur Expressinstallation ansehen:

<center>[AZURE.VIDEO azure-active-directory-connect-express-settings]</center>



## Überprüfen der Installation

Nach der erfolgreichen Installation von Azure AD Connect können Sie überprüfen, ob die Synchronisierung ausgeführt wird, indem Sie sich beim Azure-Portal anmelden und die letzte Synchronisierungszeit prüfen.

1.  Melden Sie sich beim Azure-Portal an.
2.  Wählen Sie im linken Bereich "Active Directory" aus.
3.  Doppelklicken Sie auf das Verzeichnis, das Sie soeben zum Einrichten von Azure AD Connect verwendet haben.
4.  Wählen Sie im oberen Bereich "Verzeichnisintegration" aus. Prüfen Sie die letzte Synchronisierungszeit.

<center>![Expressinstallation](./media/active-directory-aadconnect-get-started/verify.png)</center>

## Weitere Vorgehensweise
Nachdem Sie nun Azure AD Connect installiert haben, können Sie auf diesen Link [hier](active-directory-aadconnect-whats-next.md) klicken, um verschiedene Aufgaben auszuführen, z. B. Zuweisen von Benutzern zu Azure AD Premium- oder Enterprise Mobility-Lizenzen oder Konfigurieren zusätzlicher Optionen.

 

<!---HONumber=58_postMigration-->