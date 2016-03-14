<properties
	pageTitle="Identity Protection – Glossar | Microsoft Azure"
	description="Identity Protection-Glossar"
	services="active-directory"
	keywords="Azure Active Directory Identity Protection, Cloud App Discovery, Verwalten von Anwendungen"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/01/2016"
	ms.author="markvi"/>

# Identity Protection – Glossar 


### Gefährdet (Benutzer)	
Ein Benutzer, der über ein oder mehrere aktive Risikoereignisse verfügt.

### Atypischer Anmeldeort 	
Eine Anmeldung von einem geografischen Standort aus, der für den jeweiligen Benutzer, ähnliche Benutzer oder den Mandanten untypisch ist.

### Azure AD Identity Protection 	
Ein Sicherheitsmodul von Azure Active Directory, das eine umfassende Übersicht über Risikoereignisse und potenzielle Sicherheitsrisiken bietet, die für die Identitäten einer Organisation bestehen.

### Bedingter Zugriff 	
Eine Richtlinie zum Schützen des Zugriffs auf Ressourcen. Regeln für den bedingten Zugriff werden in Azure Active Directory gespeichert und von Azure AD ausgewertet, bevor Zugriff auf die Ressource gewährt wird. Beispiele für Regeln sind die Einschränkung des Zugriffs basierend auf dem Benutzerstandort, der Integrität des Geräts oder des Verfahrens für die Benutzerauthentifizierung.

### Anmeldeinformationen 	
Die sind die Informationen zur Identifizierung mit Identifikationsnachweis. Sie werden verwendet, um Zugriff auf lokale Ressourcen und Netzwerkressourcen zu erhalten. Beispiele für Anmeldeinformationen sind Benutzernamen und Kennwörter, Smartcards und Zertifikate.

### Ereignis 	
Ein Datensatz einer Aktivität in Azure Active Directory.

### Falsch positives Ergebnis (Risikoereignis) 
Ein Risikoereignisstatus, der von einem Identity Protection-Benutzer manuell festgelegt wird. Er gibt an, dass das Risikoereignis untersucht und dabei ermittelt wurde, dass es fälschlicherweise als Risikoereignis eingestuft wurde.

### Identität	
Eine Person oder Identität, die per Authentifizierung anhand von Kriterien, z. B. einem Kennwort oder Zertifikat, überprüft werden muss.

### Identitätsrisikoereignis 	
AAD-Ereignis, das von Identity Protection als anomal gekennzeichnet wurde und mit dem angegeben werden kann, dass eine Identität kompromittiert wurde.

### Ignoriert (Risikoereignis) 	
Ein Risikoereignisstatus, der von einem Identity Protection-Benutzer manuell festgelegt wurde und angibt, dass das Risikoereignis geschlossen wurde, ohne dass Maßnahmen ergriffen wurden.

### Unmöglicher Ortswechsel zu atypischen Orten 	
Dieses Risikoereignis wird ausgelöst, wenn zwei Anmeldungen für denselben Benutzer erkannt werden und Folgendes gilt: Mindestens eine davon stammt von einem atypischen Anmeldestandort, und der Zeitraum zwischen den Anmeldungen ist kürzer als der Mindestzeitraum, der für die physische Bewegung zwischen diesen Orten erforderlich ist.

###Untersuchung	
Der Prozess der Überprüfung von Aktivitäten, Protokollen und anderen relevanten Informationen zu einem Risikoereignis, um zu entscheiden, ob Schritte zur Korrektur oder Lösung erforderlich sind. Außerdem wird ermittelt, ob und wie die Identität kompromittiert und wie die kompromittierte Identität verwendet wurde.

### Kompromittierte Anmeldeinformationen 	

Dieses Risikoereignis wird ausgelöst, wenn unsere Ermittler herausfinden, dass die aktuellen Benutzeranmeldeinformationen (Benutzername und Kennwort) öffentlich im Dark Web gepostet werden.

### Lösung	
Eine Aktion, mit der für einen Angreifer die Möglichkeit eingeschränkt oder beseitigt wird, eine kompromittierte Identität oder ein Gerät auszunutzen, ohne für die Identität oder das Gerät wieder einen sicheren Zustand herzustellen. Bei einer Lösung werden keine vorherigen Risikoereignisse behoben, die der Identität oder dem Gerät zugeordnet sind.

### Multi-Factor Authentication 
Ein Authentifizierungsverfahren, für das mindestens zwei Authentifizierungsmethoden erforderlich sind: etwas, was sich im Besitz des Benutzers befindet, z. B. ein Zertifikat, etwas, was der Benutzer weiß, z. B. Benutzernamen, Kennwörter oder Passphrases, physische Attribute, z. B. einen Fingerabdruck, und persönliche Attribute, z. B. eine persönliche Signatur.

### Offlineerkennung 	
Die Erkennung von Anomalien und Bewertung des Risikos eines Ereignisses, z. B. ein verspäteter Anmeldeversuch für eine Veranstaltung, nachdem diese bereits vorüber ist.

### Richtlinienbedingung 	
Ein Teil einer Sicherheitsrichtlinie, mit der die Entitäten (Gruppen, Benutzer, Apps, Geräteplattformen, Gerätezustände, IP-Bereiche, Clienttypen) definiert werden, die in die Richtlinie eingeschlossen oder davon ausgeschlossen werden.

### Richtlinienregel 	
Der Teil einer Sicherheitsrichtlinie, mit der die Umstände beschrieben werden, unter denen die Richtlinie ausgelöst wird, sowie die bei Auslösung der Richtlinie auszuführenden Aktionen.

### Prävention 	
Eine Aktion, mit der Schaden für die Organisation verhindert werden soll, der durch den Missbrauch einer vermutlich oder mit Sicherheit kompromittierten Identität oder eines Geräts entsteht. Mit einer Präventionsaktion wird das Gerät bzw. die Identität nicht geschützt, und vorherige Risikoereignisse werden nicht gelöst.

### Privilegiert (Benutzer)
Ein Benutzer, der zum Zeitpunkt eines Risikoereignisses über dauerhafte oder vorübergehende Administratorberechtigungen für eine oder mehrere Ressourcen in Azure Active Directory verfügte, z. B. ein globaler Administrator, Abrechnungsadministrator, Dienstadministrator, Benutzeradministrator und Kennwortadministrator.

###Echtzeit 	
Siehe „Echtzeiterkennung“.

###Echtzeiterkennung 	
Die Erkennung von Anomalien und Bewertung des Risikos eines Ereignisses, z. B. ein Anmeldeversuch vor der Freigabe bzw. dem Start einer Veranstaltung.

### Bereinigt (Risikoereignis) 	
Ein Risikoereignis, das von Identity Protection automatisch festgelegt wird und angibt, dass das Risikoereignis mit der standardmäßigen Korrekturaktion für diese Art von Risikoereignis bereinigt wurde. Beim Zurücksetzen des Benutzerkennworts werden beispielsweise viele Risikoereignisse, die auf eine Kompromittierung des vorherigen Kennworts hinweisen, automatisch bereinigt.

### Korrektur 	
Eine Aktion zum Schützen einer Identität oder eines Geräts, die bzw. das zuvor wahrscheinlich oder mit Sicherheit kompromittiert war. Bei einer Korrekturaktion wird die Identität bzw. das Gerät wieder in einen sicheren Zustand versetzt, und vorherige Risikoereignisse, die der Identität oder dem Gerät zugeordnet sind, werden gelöst.

### Aufgelöst (Risikoereignis) 	
Ein Risikoereignisstatus, der von einem Identity Protection-Benutzer manuell festgelegt wird. Hiermit wird angegeben, dass der Benutzer außerhalb von Identity Protection eine geeignete Korrekturaktion durchgeführt hat und dass das Risikoereignis als geschlossen angesehen werden kann.

###Risikoereignisstatus 	
Eine Eigenschaft eines Risikoereignisses, mit der angegeben wird, ob das Ereignis aktiv ist. Wenn es geschlossen wurde, wird der Grund für das Schließen angegeben.

###Risikoereignistyp 	
Eine Kategorie für das Risikoereignis, mit der die Art der Anomalie angegeben wird, die zur Risikoeinstufung für das Ereignis geführt hat.

###Risikostufe (Risikoereignis) 	
Eine Einstufung (Hoch, Mittel oder Niedrig) des Schweregrads eines Risikoereignisses. So können Identity Protection-Benutzer die Aktionen priorisieren, die sie durchführen, um das Risiko für ihre Organisation zu mindern.

###Risikostufe (Anmeldung) 
Ein Einstufung (Hoch, Mittel oder Niedrig) der Wahrscheinlichkeit, dass bei einer bestimmten Anmeldung eine andere Person versucht, die Identität des Benutzers zu nutzen.

###Risikostufe (Benutzerkompromittierung) 
Eine Einstufung (Hoch, Mittel oder Niedrig) der Wahrscheinlichkeit, mit der eine Identität kompromittiert wurde.

### Risikostufe (Sicherheitsrisiko) 	
Eine Einstufung (Hoch, Mittel oder Niedrig) des Sicherheitsrisikos. Hiermit können Identity Protection-Benutzer die Aktionen priorisieren, die sie durchführen, um das Risiko für ihre Organisation zu mindern.

### Schützen (Identität)	
Das Durchführen einer Korrekturaktion, z. B. das Ändern eines Kennworts oder das Verwenden eines neuen Image für einen Computer, um für eine potenziell kompromittierte Identität wieder einen fehlerlosen Zustand herzustellen.

### Sicherheitsrichtlinie
Eine Auflistung von Regeln und Bedingungen einer Richtlinie. Eine Richtlinie kann auf Entitäten angewendet werden, z. B. Benutzer, Gruppen, Apps, Geräte, Geräteplattformen, Gerätestatus, IP-Bereiche und Auth2.0-Clienttypen. Wenn eine Richtlinie aktiviert ist, wird sie immer dann ausgewertet, wenn für eine in der Richtlinie enthaltene Entität ein Token für eine Ressource ausgegeben wird.

### Anmelden 
Das Authentifizieren mit einer Identität in Azure Active Directory.

### Anmeldung 
Der Prozess bzw. die Aktion zur Authentifizierung einer Identität in Azure Active Directory und das Ereignis, mit dem dieser Vorgang erfasst wird.

###Anmeldung von anonymer IP-Adresse 	
Ein Risikoereignis, das nach einer erfolgreichen Anmeldung über eine IP-Adresse ausgelöst wird, die als anonyme Proxy-IP-Adresse identifiziert wurde.

###Anmeldung von infiziertem Gerät 
Dieses Risikoereignis wird ausgelöst, wenn eine Anmeldung von einer IP-Adresse stammt, die bekanntermaßen von einem oder mehreren kompromittierten Geräten verwendet wird, und die Geräte aktiv versuchen, mit einem Botserver zu kommunizieren.

###Anmeldung von IP-Adresse mit verdächtiger Aktivität 
Dieses Risikoereignis wird ausgelöst, nachdem eine erfolgreiche Anmeldung von einer IP-Adresse mit einer hohen Zahl von fehlgeschlagenen Anmeldeversuchen unter Verwendung mehrerer Benutzerkonten innerhalb eines kurzen Zeitraums durchgeführt wurde.

###Anmeldung von unbekanntem Standort 
Dieses Risikoereignis wird ausgelöst, wenn sich ein Benutzer erfolgreich von einem neuen Standort (IP, Breiten-/Längengrad und ASN) aus anmeldet.

###Anmelderisiko 	
Siehe „Risikostufe (Anmeldung)“.

###Richtlinie zum Anmelderisiko 	
Eine bedingte Zugriffsrichtlinie, mit der das Risiko für eine bestimmte Anmeldung ausgewertet wird und Lösungen basierend auf vordefinierten Bedingungen und Regeln angewendet werden.

###Risiko einer Benutzerkompromittierung 	
Siehe „Risikostufe (Benutzerkompromittierung)“.

###Benutzerrisiko 	
Siehe „Risikostufe (Benutzerkompromittierung)“.

###Richtlinie zum Benutzerrisiko 	
Eine bedingte Zugriffsrichtlinie, mit der die Anmeldung berücksichtigt wird und Lösungen basierend auf vordefinierten Bedingungen und Regeln angewendet werden.

###Benutzer mit Risikokennzeichnung 	
Benutzer, die über Risikoereignisse verfügen (entweder „Aktiv“ oder „Bereinigt“).

###Sicherheitsrisiko 	
Eine Konfiguration oder ein Zustand in Azure Active Directory, durch die bzw. den das Verzeichnis anfällig für Angriffe oder Bedrohungen wird.


## Weitere Informationen

- [Azure Active Directory Identity Protection](active-directory-identityprotection.md)

<!---HONumber=AcomDC_0302_2016-->