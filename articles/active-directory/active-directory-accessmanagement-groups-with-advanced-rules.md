
<properties
	pageTitle="Verwenden von Attributen zum Erstellen erweiterter Regeln | Microsoft Azure"
	description="Erweiterte Vorgehensweisen zum Verwalten von Sicherheitsgruppen und Verwenden dieser Gruppen zum Verwalten des Zugriffs auf eine Ressource."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/15/2015" 
	ms.author="femila"/>


# Verwenden von Attributen zum Erstellen erweiterter Regeln
Das Azure-Verwaltungsportal bietet Ihnen die notwendige Flexibilität, um eine erweiterte Regel einzurichten, mit der Sie dynamische Mitgliedschaften für Gruppen aktivieren können.

**Erstellen der erweiterten Regel** Wählen Sie im Azure-Verwaltungsportal auf der Registerkarte **Konfigurieren** für die Gruppe das Optionsfeld **Erweiterte Regel** aus, und geben Sie die erweiterte Regel in das vorgesehene Textfeld ein. Sie können die erweiterte Regel mit den folgenden Informationen erstellen.

## Erstellen des Texts einer erweiterten Regel
Die erweiterte Regel, die Sie für die dynamischen Mitgliedschaften für Gruppen erstellen können, ist im Wesentlichen ein binärer Ausdruck, der aus drei Teilen besteht und ein wahres oder falsches Ergebnis liefert. Die drei Teile sind folgende:

- Linker Parameter
- Binärer Operator
- Rechte Konstante 

Eine vollständige erweiterte Regel sieht etwa wie folgt aus: (leftParameter binaryOperator "RightConstant"). Für den gesamten Ausdruck sind öffnende und schließende Klammern erforderlich, die rechte Konstante muss in doppelte gerade Anführungszeichen eingeschlossen werden, und die Syntax für den linken Parameter lautet "user.property". Eine erweiterte Regel kann aus mehreren binären Ausdrücke bestehen, die durch die logischen Operatoren "-and", "-or" und "-not" getrennt sind. Hier finden Sie Beispiele für eine richtig aufgebaute erweiterte Regel:

- (user.department -eq "Sales") -or (user.department -eq "Marketing") 
- (user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE") 

Eine vollständige Liste der unterstützten Parameter und Ausdrucksregeloperatoren finden Sie in den folgenden Abschnitten.

Die Gesamtlänge des Texts der erweiterten Regel darf 255 Zeichen nicht überschreiten.
> [AZURE.NOTE]Bei string- und regex-Vorgängen wird die Groß-und Kleinschreibung nicht beachtet. Sie können auch NULL-Prüfungen durchführen, indem Sie "$null" als Konstante verwenden, z. B.: user.department -eq $null. Zeichenfolgen mit Anführungszeichen (") sollten mit dem Escapezeichen ` maskiert werden, z. B.: user.department -eq "Sa`"les".

##Unterstützte Ausdrucksregeloperatoren
Die folgende Tabelle enthält alle Ausdrucksregeloperatoren und ihre Syntax zur Verwendung im Text der erweiterten Regel:

| Operator | Syntax |
|-----------------|----------------|
| Not Equals | -ne |
| Equals | -eq |
| Not Starts With | -notStartsWith |
| Starts With | -startsWith |
| Not Contains | -notContains |
| Contains | -contains |
| Not Match | -notMatch |
| Match | -match |


| Abfrageanalysefehler | Fehlerverwendung | Korrigierte Verwendung |
|----------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Fehler: Das Attribut nicht unterstützt. | (user.invalidProperty -eq "Value") | (user.department -eq "value")  
Die Eigenschaft sollte einer der unterstützten Eigenschaften aus der obigen Liste entsprechen. |
| Fehler: Der Operator wird für das Attribut nicht unterstützt. | (user.accountEnabled -contains true) | (user.accountEnabled -eq true)  
Die Eigenschaft weist den Typ "boolesch" auf. Verwenden Sie die unterstützten booleschen Operatoren (-eq oder -ne) aus der oben stehenden Liste. |
| Fehler: Abfragekompilierungsfehler. | (user.department -eq "Sales") -and (user.department -eq "Marketing")(user.userPrincipalName -match "*@domain.ext") | (user.department -eq "Sales") -and (user.department -eq "Marketing")  
Der logische Operator sollte einer der unterstützten Eigenschaften aus der obigen Liste entsprechen.  
(user.userPrincipalName -match ".*@domain.ext")  
oder  
(user.userPrincipalName -match "@domain.ext$")  
Fehler im regulären Ausdruck. |
| Fehler: Die Binärausdruck weist nicht das richtige Format auf. | (user.department –eq “Sales”) (user.department -eq "Sales")(user.department-eq"Sales") | (user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain")  
Abfrage enthält mehrere Fehler. Die Klammern befinden sich nicht an der richtigen Stelle. |
| Fehler: Unbekannter Fehler beim Einrichten dynamischer Mitgliedschaften. | (user.accountEnabled -eq "True" AND user.userPrincipalName -contains "alias@domain") | (user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain")  
Abfrage enthält mehrere Fehler. Die Klammern befinden sich nicht an der richtigen Stelle. |

##Unterstützte Parameter
Im Folgenden werden alle Benutzereigenschaften aufgelistet, die Sie in der erweiterten Regel verwenden können:

**Eigenschaften vom Typ "boolesch"**

Zulässige Operatoren

* -eq


* -ne


| Eigenschaften | Zulässige Werte | Verwendung |
|----------------|-----------------|--------------------------------|
| accountEnabled | true false | user.accountEnabled -eq true) |
| dirSyncEnabled | true false null | (user.dirSyncEnabled -eq true) |

**Eigenschaften vom Typ "string"**

Zulässige Operatoren

* -eq


* -ne


* -notStartsWith


* -StartsWith


* -contains


* -notContains


* -match


* -notMatch

| Eigenschaften | Zulässige Werte | Verwendung |
|----------------------------|-------------------------------------------------------------------------------------------------------|-----------------------------------------------------------|
| city | Jeder string-Wert oder $null. | (user.city -eq "value") |
| country | Jeder string-Wert oder $null. | (user.country -eq "value") |
| department | Jeder string-Wert oder $null. | (user.department -eq "value") |
| displayName | Jeder string-Wert. | (user.displayName -eq "value") |
| facsimileTelephoneNumber | Jeder string-Wert oder $null. | (user.facsimileTelephoneNumber -eq "value") |
| givenName | Jeder string-Wert oder $null. | (user.givenName -eq "value") |
| jobTitle | Jeder string-Wert oder $null. | (user.jobTitle -eq "value") |
| mail | Jeder string-Wert oder $null. SMTP-Adresse des Benutzers. | (user.mail -eq "value") |
| mailNickName | Jeder string-Wert. E-Mail-Alias des Benutzers. | (user.mailNickName -eq "value") |
| mobile | Jeder string-Wert oder $null. | (user.mobile -eq "value") |
| objectId | GUID des Benutzerobjekts. | (user.objectId -eq "1111111-1111-1111-1111-111111111111") |
| passwordPolicies | None DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword | (user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName | Jeder string-Wert oder $null. | (user.physicalDeliveryOfficeName -eq "value") |
| postalCode | Jeder string-Wert oder $null. | (user.postalCode -eq "value") |
| preferredLanguage | ISO 639-1 code | (user.preferredLanguage -eq "en-US") |
| sipProxyAddress | Jeder string-Wert oder $null. | (user.sipProxyAddress -eq "value") |
| state | Jeder string-Wert oder $null. | (user.state -eq "value") |
| streetAddress | Jeder string-Wert oder $null. | (user.streetAddress -eq "value") |
| surname | Jeder string-Wert oder $null. | (user.surname -eq "value") |
| telephoneNumber | Jeder string-Wert oder $null. | (user.telephoneNumber -eq "value") |
| usageLocation | Aus zwei Buchstaben bestehender Ländercode. | (user.usageLocation -eq "US") |
| userPrincipalName | Jeder string-Wert. | (user.userPrincipalName -eq "alias@domain") |
| userType | member guest $null | (user.userType -eq "Member") |

**Eigenschaften vom Typ "string collection"**

Zulässige Operatoren

* -contains


* -notContains

| Eigenschaften | Zulässige Werte | Verwendung |
|----------------|---------------------------------------|------------------------------------------------------|
| otherMails | Jeder string-Wert. | (user.otherMails -contains "alias@domain") |
| proxyAddresses | SMTP: alias@domain smtp: alias@domain | (user.proxyAddresses -contains "SMTP: alias@domain") |

Hier finden Sie einige Themen, die zusätzliche Informationen zu Azure Active Directory bieten:

* [Problembehandlung bei dynamischen Mitgliedschaften für Gruppen](active-directory-accessmanagement-troubleshooting.md)

* [Verwalten des Zugriffs auf Ressourcen mit Azure Active Directory-Gruppen](active-directory-manage-groups.md)

* [Was ist Azure Active Directory?](active-directory-whatis.md)

* [Integrieren lokaler Identitäten in Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=July15_HO4-->