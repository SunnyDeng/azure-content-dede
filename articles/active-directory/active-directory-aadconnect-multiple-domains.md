<properties
	pageTitle="Azure AD Connect, mehrere Domänen"
	description="In diesem Dokument wird das Einrichten und Konfigurieren mehrerer Domänen der obersten Ebene mit Office 365 und Azure AD beschrieben."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="billmath"/>

#Unterstützung für mehrere Domänen

Viele Benutzer haben gefragt, wie Sie mehrere Office 365- oder Azure AD-Domänen der obersten Ebene sowie Unterdomänen mit Verbund konfigurieren können. Im Prinzip ist dies ganz einfach, aber aufgrund einiger Vorgänge, die sozusagen hinter den Kulissen ablaufen, sollten Sie einige Tipps und Tricks kennen, um die folgenden Probleme zu vermeiden.

- Fehlermeldungen beim Versuch, zusätzliche Domänen für den Verbund zu konfigurieren
- Benutzer in untergeordneten Domänen können sich nicht mehr anmelden, nachdem mehrere Domänen der obersten Ebene für den Verbund konfiguriert wurden

## Mehrere Domänen der obersten Ebene
Betrachten wir das Setup der Beispielorganisation „contoso.com“, die eine zusätzliche Domäne namens „fabrikam.com“ benötigt.

Angenommen, in meinem lokalen System habe ich AD FS mit dem Verbunddienstnamen „fs.contoso100.com“ konfiguriert.

Wenn ich mich das erste Mal bei Office 365 oder Azure AD anmelde, konfiguriere ich „contoso.com“ als meine erste Anmeldedomäne. Das ist über Azure AD Connect oder Azure AD Powershell mithilfe von „New-MsolFederatedDomain“ möglich.

Sobald dies geschehen ist, sehen wir uns die Standardwerte für zwei der neuen Konfigurationseigenschaften der neuen Azure AD-Domäne an (diese können mithilfe von „Get-MsolDomainFederationSettings“ abgefragt werden):

| Eigenschaftenname | Wert | Beschreibung|
| ----- | ----- | -----|
|IssuerURI | http://fs.contoso100.com/adfs/services/trust| Diese Eigenschaft sieht zwar wie eine URL aus, ist aber eigentlich nur ein Name für das lokale Authentifizierungssystem, daher muss der Pfad nicht aufgelöst werden. Standardmäßig wird diese Eigenschaft von Azure AD auf den Wert des Verbunddienstbezeichners in meiner lokalen AD FS-Konfiguration festgelegt.
|PassiveClientSignInUrl|https://fs.contoso100.com/adfs/ls/|This ist der Standort, an den passive Anmeldeanforderungen gesendet werden. Es wird in mein tatsächliches AD FS-System aufgelöst. Tatsächlich gibt es mehrere *Url-Eigenschaften, aber wir müssen lediglich ein Beispiel betrachten, um den Unterschied zwischen dieser Eigenschaft und einem URI wie IssuerURI zu verdeutlichen.

Angenommen, ich füge nun meine zweite Domäne „fabrikam.com“ hinzu. Dazu führe ich den Azure AD Connect-Assistenten ein zweites Mal aus oder verwende PowerShell.

Wenn ich versuche, die zweite Domäne als Verbund mit Azure AD PowerShell hinzuzufügen, erhalte ich einen Fehler.

Der Grund dafür ist eine Einschränkung in Azure AD, durch die der IssuerURI nicht für mehrere Domänen den gleichen Wert aufweisen kann. Um diese Einschränkung zu umgehen, müssen Sie einen anderen IssuerURI für die neue Domäne verwenden. Und genau das bewirkt der SupportMultipleDomain-Parameter. Wird er mit den Cmdlets zum Konfigurieren eines Verbunds (New - Convert- und Update-MsolFederatedDomain) verwendet, bewirkt dieser Parameter, dass Azure AD den IssuerURI basierend auf den Namen der Domäne konfiguriert, die für Mandanten in Azure AD eindeutig sein muss und somit eindeutig sein sollte. Es gibt auch eine Änderung an den Anspruchsregeln, darauf werde ich jedoch später eingehen.

Wenn ich also in PowerShell „fabrikam.com“ mit dem SupportMultipleDomain-Parameter hinzufüge,

    PS C:\>New-MsolFederatedDomain -DomainName fabrikam.com –SupportMultipleDomain

erhalte ich die folgende Konfiguration in Azure AD:

- DomainName: fabrikam.com
- IssuerURI: http://fabrikam.com/adfs/services/trust
- PassiveClientSignInUrl: https://fs.contoso100.com/adfs/ls/

Beachten Sie, dass der IssuerURI zwar auf einen Wert auf Grundlage meiner Domäne festgelegt wurde und somit eindeutig ist, die Endpunkt-URL-Werte aber immer noch so konfiguriert sind, dass sie auf meinen Verbunddienst auf „fs.contoso100.com“ verweisen, genau wie bei der ursprünglichen Domäne „contoso.com“. Alle Domänen verweisen also weiterhin auf dasselbe AD FS-System.

Zweitens stellt SupportMultipleDomain sicher, dass das AD FS-System den richtigen Ausstellerwert in Token verwendet, die für Azure AD ausgestellt wurden. Dazu wird der Domänenteil des Benutzerprinzipalnamens (UPN) verwendet und als Domäne im IssuerURI festgelegt, d. h. https://{upn suffix}/adfs/services/trust. Während der Authentifizierung in Azure AD oder Office 365 wird daher das Issuer-Element im Token des Benutzers verwendet, um die Domäne in Azure AD zu finden. Wenn keine Übereinstimmung gefunden wird, schlägt die Authentifizierung fehl.

Wenn der UPN eines Benutzers z. B. johndoe@fabrikam.com lautet, wird das Issuer-Element in dem von AD FS ausgestellten Token auf http://fabrikam.com/adfs/services/trust festgelegt. Dies entspricht der Azure AD-Konfiguration und die Authentifizierung ist erfolgreich.

Im folgenden sehen Sie die angepasste Anspruchsregel, die diese Logik implementiert:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type =   "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));

In meinem Setup hatte ich „contoso.com“ zuerst ohne den supportMultipleDomains-Switch und mit dem Wert des Standard-IssuerURI registriert. Beim Hinzufügen von „fabrikam.com“ muss ich jetzt sicherstellen, dass auch „contoso.com“ so konfiguriert ist, dass der SupportMultipleDomains-Switch verwendet wird, da vom Anspruchsregelupdate nie mehr der Standard-IssuerURI gesendet wird und die Authentifizierung aufgrund des nicht übereinstimmenden IssuerURI fehlschlägt. Aber keine Sorge, Sie werden entsprechend gewarnt, bevor ihnen gestattet wird, den supportMultipleDomains-Switch in einer anderen Domäne zu verwenden.

Um dieses Problem zu vermeiden, müssen wir auch die Konfiguration für die Domäne „contoso.com“ aktualisieren. Der Azure AD Connect-Assistent erkennt recht gut, wann die oben genannten Schritte ausgeführt werden müssen, und ergreift entsprechende Maßnahmen, wenn Sie eine zweite Domäne hinzufügen. Wenn Sie sich beim ersten Durchlauf bereits in der SupportMultipleDomain-Konfiguration befinden, wird diese nicht außer Kraft gesetzt.

In PowerShell müssen Sie den SupportMultipleDomain-Switch manuell bereitstellen.

Nachfolgend finden Sie die detaillierten Schritte für den Übergang von einer einzelnen Domäne zu mehreren Domänen.

Anschließend verfügen wir über die Konfiguration für zwei Domänen in Azure AD:

- DomainName: contoso.com
- IssuerURI: http://contoso.com/adfs/services/trust
- PassiveClientSignInUrl: https://fs.contoso100.com/adfs/ls/
- DomainName: fabrikam.com
- IssuerURI: http://fabrikam.com/adfs/services/trust
- PassiveClientSignInUrl: https://fs.contoso100.com/adfs/ls/

Die Verbundanmeldung für Benutzer aus den Domänen „contoso.com“ und „fabrikam.com“ funktioniert jetzt. Nur ein Problem bleibt bestehen: die Anmeldung für Benutzer in untergeordneten Domänen.

##Untergeordnete Domänen
Angenommen, ich füge meine untergeordnete Domäne „sub.contoso.com“ zu Azure AD hinzu. Aufgrund der Art und Weise, wie Azure AD Domänen verwaltet, erbt die untergeordnete Domäne die Einstellungen der übergeordneten Domäne, in diesem Fall von „contoso.com“. Dies bedeutet, dass der IssuerURI für user@sub.contoso.com http://contoso.com/adfs/services/trust sein muss. Die oben implementierte Standardregel für

Azure AD generiert jedoch ein Token mit einem Aussteller http://sub.contoso.com/adfs/services/trust, der nicht dem erforderlichen Wert der Domäne entspricht, und die Authentifizierung schlägt fehl. Glücklicherweise gibt es eine Umgehung für dieses Problem, die jedoch nicht in unsere Tools integriert ist. Sie müssen AD FS-Vertrauensstellung für Microsoft Online manuell aktualisieren.

Sie müssen die benutzerdefinierte Anspruchsregel so konfigurieren, dass beim Erstellen des benutzerdefinierten Issuer-Werts alle Unterdomänen aus dem UPN-Suffix des Benutzers entfernt werden. Die genauen Schritte hierzu finden Sie weiter unten.

Zusammenfassend gesagt: Sie können über mehrere Domänen mit unterschiedlichen Namen sowie Unterdomänen verfügen, die alle mit dem gleichen AD FS-Server verbunden sind. Sie müssen lediglich einige zusätzliche Schritte ausführen, um sicherzustellen, dass die Issuer-Werte für alle Benutzer richtig festgelegt sind.

<!---HONumber=AcomDC_0128_2016-->