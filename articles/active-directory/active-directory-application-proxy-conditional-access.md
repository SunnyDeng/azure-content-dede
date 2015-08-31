<properties
	pageTitle="Bedingter Zugriff für Anwendungen, die mit dem Azure AD-Anwendungsproxy veröffentlicht wurden"
	description="Stellt Informationen dazu bereit, wie der bedingte Zugriff für veröffentlichte Anwendungen eingerichtet wird, auf die mit dem Azure AD-Anwendungsproxy remote zugegriffen wird. "
	services="active-directory"
	documentationCenter=""
	authors="rkarlin"
	manager="msStevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/09/2015"
	ms.author="rkarlin"/>

# Arbeiten mit bedingtem Zugriff
> [AZURE.NOTE]Das Feature "Anwendungsproxy" ist nur verfügbar, wenn Sie Azure Active Directory auf die Premium oder Basic Edition aktualisiert haben. Weitere Informationen finden Sie unter [Azure Active Directory-Editionen](https://msdn.microsoft.com/library/azure/dn532272.aspx).

Sie können ab sofort Zugriffsregeln aktivieren, mit denen Benutzern und Gruppen bedingter Zugriff auf Anwendungen gewährt wird, die mit dem Anwendungsproxy veröffentlicht werden. Dies ermöglicht Folgendes: – Anforderung von Multi-Factor Authentication auf Anwendungsebene – Anforderung von Multi-Factor Authentication nur dann, wenn Benutzer nicht bei der Arbeit sind – Blockieren des Anwendungszugriffs, wenn Benutzer nicht bei der Arbeit sind

Diese Regeln können auf alle Benutzer und Gruppen oder lediglich auf bestimmte Benutzer und Gruppen angewendet werden. Standardmäßig gilt die Regel für alle Benutzer, die Zugriff auf die Anwendung haben. Die Regel kann jedoch auch auf Benutzer beschränkt werden, die Mitglied einer angegebenen Sicherheitsgruppe sind. Zugriffsregeln werden ausgewertet, wenn ein Benutzer auf eine Verbundanwendung zugreift, die OAuth 2.0, OpenID Connect, SAML oder WS-Federation verwendet. Darüber hinaus werden Zugriffsregeln ausgewertet, wenn von OAuth 2.0 und OpenID Connect ein Aktualisierungstoken zum Abrufen eines Zugriffstokens verwendet wird.

## Voraussetzungen für den bedingten Zugriff

- Azure Active Directory Premium-Abonnement 
- Ein Verbundmandant oder verwalteter Azure Active Directory-Mandant 
- Verbundmandanten erfordern die Aktivierung von Multi-Factor Authentication (MFA) 

![](http://i.imgur.com/rv28onQ.png)

## Konfigurieren von Multi-Factor Authentication pro Anwendung
1. Melden Sie sich als Administrator beim Azure-Verwaltungsportal an.
2. Gehen Sie zu Active Directory, und wählen Sie das Verzeichnis aus, in dem Sie den Anwendungsproxy aktivieren möchten.
3. Klicken Sie auf **Anwendungen**, und führen Sie einen Bildlauf nach unten zum Abschnitt **Zugriffsregeln** durch. Der Abschnitt mit den Zugriffsregeln wird nur für Anwendungen angezeigt, die mit dem Anwendungsproxy veröffentlicht wurden und die Verbundauthentifizierung nutzen.
4. Aktivieren Sie die Regeln, indem Sie **Zugriffsregeln aktivieren** auf **Ein** festlegen.
5. Geben Sie die Benutzer und Gruppen an, auf die die Regeln angewendet werden sollen. Verwenden Sie die Schaltfläche **Gruppe hinzufügen**, um eine oder mehrere Gruppen auszuwählen, für die die Zugriffsregel gelten soll. Dieses Dialogfeld kann auch zum Entfernen ausgewählter Gruppen verwendet werden. Bei Anwendung der ausgewählten Regeln auf Gruppen werden die Zugriffsregeln nur für Benutzer erzwungen, die einer der angegebenen Sicherheitsgruppen angehören. <br> Um Sicherheitsregeln explizit von der Regel auszuschließen, aktivieren Sie die Option **Ausgenommen** und geben eine oder mehrere Gruppen an. Benutzer, die Mitglieder einer der in der Ausnahmeliste aufgeführten Gruppen sind, müssen keine mehrstufige Authentifizierung durchführen. <br> Wenn ein Benutzer unter Verwendung der Multi-Factor Authentication-Funktion auf Benutzerebene konfiguriert wurde, hat diese Einstellung Vorrang vor den Multi-Factor Authentication-Regeln auf Anwendungsebene. Dies bedeutet, dass Benutzer, die auf Benutzerebene für die mehrstufige Authentifizierung konfiguriert wurden, auch dann eine mehrstufige Authentifizierung durchlaufen müssen, wenn sie auf Anwendungsebene von den Multi-Factor Authentication-Regeln ausgenommen wurden. [Erfahren Sie mehr über die Multi-Factor Authentication-Funktion und Einstellungen auf Benutzerebene](../multi-factor-authentication/multi-factor-authentication.md). 
6. Wählen Sie die Zugriffsregel, die Sie festlegen möchten:
- **Erfordert mehrstufige Authentifizierung**: Benutzer, auf die Zugriffsregeln angewendet werden, müssen eine mehrstufige Authentifizierung durchlaufen, bevor sie auf die Anwendung zugreifen können, für die die Regel gilt.
- **Erfordert mehrstufige Authentifizierung, wenn nicht bei der Arbeit**: Benutzer, die über eine vertrauenswürdige IP-Adresse auf die Anwendung zugreifen, werden nicht zur Ausführung einer mehrstufigen Authentifizierung aufgefordert. Die Bereiche für vertrauenswürdige IP-Adressen können auf den Einstellungsseiten für die mehrstufige Authentifizierung konfiguriert werden.
- **Zugriff blockieren, wenn nicht gearbeitet wird**: Benutzer, die versuchen, sich von außerhalb des Unternehmensnetzwerks zu verbinden, können nicht auf die Anwendung zugreifen.


## Konfigurieren von MFA für Verbunddienste
Für Verbundmandanten kann die mehrstufige Authentifizierung von Azure Active Directory oder den lokalen AD FS-Server durchgeführt werden. Standardmäßig wird MFA auf allen Seiten durchgeführt, die von Azure Active Directory gehostet werden. Führen Sie zur lokalen Konfiguration von MFA Windows PowerShell aus, und verwenden Sie die –SupportsMFA-Eigenschaft zum Festlegen des Azure AD-Moduls. Das folgende Beispiel zeigt, wie Sie MFA lokal aktivieren, indem Sie das [Set-MsolDomainFederationSettings-Cmdlet](https://msdn.microsoft.com/library/azure/dn194088.aspx) im Mandanten "contoso.com" verwenden: `Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true ` Zusätzlich zum Festlegen dieses Flags muss die AD FS-Instanz des Verbundmandanten für die Ausführung der mehrstufigen Authentifizierung konfiguriert werden. Folgen Sie hierzu den Anweisungen unter [Lokales Bereitstellen von Microsoft Azure Multi-Factor Authentication](http://technet.microsoft.com/library/dn280946.aspx).
## Zusätzliche Ressourcen

* [Als Organisation für Azure registrieren](..sign-up-organization.md)
* [Azure-Identität](..fundamentals-identity.md)

<!---HONumber=August15_HO8-->