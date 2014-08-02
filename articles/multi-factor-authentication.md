<properties linkid="" urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="What is Azure Multi-Factor Authentication?" authors="billmath" solutions="" manager="terrylan" editor="lisatoft" />

Was ist Azure Multi-Factor Authentication?
==========================================

Multi-Factor oder Two-Factor Authentication ist eine Authentifizierungsmethode, welche die Verwendung von mehr als einer Verifizierungsmethode erfordert und eine wichtige zweite Sicherheitsebene für Benutzeranmeldungen und Transaktionen darstellt. Dies funktioniert durch das Anfordern von zwei oder mehr der folgenden Verifizierungsmethoden:

-   Etwas, das Sie wissen (normalerweise ein Kennwort)
-   Etwas, das Sie haben (ein vertrautes Gerät, das nicht leicht dupliziert werden kann, wie ein Telefon)
-   Etwas, das Sie sind (biometrisch)

Die Sicherheit der Multi-Factor Authentication liegt im Ebenenansatz. Die Faktoren der mehrfachen Authentifizierung zu überwinden stellt eine große Herausforderung für Angreifer dar. Selbst wenn ein Angreifer das Kennwort des Benutzers herausfinden kann, ist dies nutzlos, wenn er das vertraute Gerät nicht besitzt. Auch wenn der Benutzer das Gerät verliert, kann es der Finder des Geräts nicht verwenden, wenn er nicht gleichzeitig auch das Kennwort des Benutzers kennt. Azure Multi-Factor Authentication ist der Multi-Factor Authentication-Dienst, der Benutzer zusätzlich zur Verifizierung der Anmeldung über eine Mobil-App, einen Telefonanruf oder eine Textnachricht auffordert. Es kann mit Azure Active Directory verwendet werden, um lokale Ressourcen mit dem Azure Multi-Factor Authentication-Server zu sichern, sowie für benutzerdefinierte Anwendungen und Verzeichnisse, die das SDK verwenden.

![Azure Multi-Factor Authentication](./media/multi-factor-authentication/WAMFA1.png)

### Sichern von Cloud-Azure Active Directory

Die Aktivierung von Multi-Factor Authentication für Azure AD-Identitäten sorgt dafür, dass Benutzer bei der nächsten Anmeldung aufgefordert werden, eine zusätzliche Verifizierung festzulegen. Verwenden Sie Multi-Factor Authentication, um ohne zusätzliche Einrichtung den Zugriff auf Azure, Microsoft Online Services wie Office 365 und Dynamics CRM Online sowie zu Clouddiensten von Drittanbietern zu sichern, die Azure AD integrieren. Multi-Factor Authentication kann schnell für eine große Anzahl an globalen Benutzern und Anwendungen aktiviert werden. [Mehr Infos](http://msdn.microsoft.com/en-us/library/dn249466.aspx)

### Sichern von lokalen Ressourcen und Active Directory

Aktivieren Sie Multi-Factor Authentication für Ihre eigenen lokalen Ressourcen wie IIS und Active Directory mithilfe des Azure Multi-Factor Authentication-Server. Azure Multi-Factor Authentication-Server ermöglicht dem Administrator die Integration mit IIS-Authentifizierung, um Microsoft IIS-Webanwendungen, RADIUS-Authentifizierung, LDAP-Authentifizierung und Windows-Authentifizierung zu sichern. [Mehr Infos](http://msdn.microsoft.com/en-us/library/dn249467.aspx)

### Sichern von benutzerdefinierten Anwendungen

Ein SDK ermöglicht die direkte Integration mit Clouddiensten. Integrieren Sie Active Authentication-Verifizierung per Telefonanruf oder Textnachricht in die Anmelde- oder Transaktionsprozesse von Anwendungen, und nutzen Sie die vorhandene Benutzerdatenbank der Anwendung. [Mehr Infos](http://msdn.microsoft.com/en-us/library/dn249464.aspx)

### Multi-Factor Authentication für Office 365

Multi-Factor Authentication für Office 365 durch Azure Multi-Factor Authentication funktioniert exklusiv mit Office 365-Anwendungen und wird über das Office 365-Portal verwaltet. Dadurch können Administratoren jetzt Office 365-Ressourcen mit Multi-Factor Authentication sichern. [Mehr Infos](http://msdn.microsoft.com/en-us/library/dn383636.aspx)

### Multi-Factor Authentication für Azure-Administratoren

Dieselbe Teilmenge von Multi-Factor Authentication-Funktionen für Office 365 wird allen Azure-Administratoren kostenlos zur Verfügung gestellt. Jedes Administratorenkonto eines Azure-Abonnements kann jetzt durch die Aktivierung dieser wichtigen Multi-Factor Authentication-Funktion zusätzlichen Schutz erhalten. Somit kann ein Administrator, der auf das Azure-Portal zugreifen möchte, um einen virtuellen Computer oder eine Website zu erstellen bzw. Speicher, Mobile Services oder einen anderen Azure-Dienst zu verwalten, Multi-Factor Authentication zu seinem Administratorkonto hinzufügen. [Mehr Infos](http://msdn.microsoft.com/en-us/library/dn249471.aspx)

### Funktionsvergleiche für Multi-Factor Authentication

Im Folgenden sehen Sie die verfügbaren Multi-Factor Authentication-Versionen sowie eine kurze Zusammenfassung deren Funktionen. Sie können dies verwenden, um herauszufinden, welche Multi-Factor Authentication-Version die richtige für Sie ist. [Mehr Infos](http://msdn.microsoft.com/en-us/library/dn249471.aspx)

![Funktionsvergleiche für Multi-Factor Authentication](./media/multi-factor-authentication/mfacomparison1.png)

**Zusätzliche Ressourcen**

-   [Anmelden bei Azure als Organisation](/en-us/manage/services/identity/organizational-account/)
-   [Azure-Identität](/en-us/manage/windows/fundamentals/identity/)
-   [Azure Multi-Factor Authentication-Bibliothek](http://technet.microsoft.com/en-us/library/dn249471.aspx)

