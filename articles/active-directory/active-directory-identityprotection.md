<properties
	pageTitle="Azure Active Directory Identity Protection | Microsoft Azure"
	description="Erfahren Sie, wie Sie mit Azure AD Identity Protection für Angreifer die Möglichkeit einschränken können, eine kompromittierte Identität oder ein Gerät auszunutzen, und wie Sie eine Identität oder ein Gerät schützen, das zuvor vermutlich oder mit Sicherheit kompromittiert war."
	services="active-directory"
	keywords="Azure Active Directory Identity Protection, Cloud App Discovery, Verwalten von Anwendungen, Sicherheit, Risiko, Risikostufe, Sicherheitsrisiko, Sicherheitsrichtlinie"
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
	ms.date="03/02/2016"
	ms.author="markvi"/>

#Azure Active Directory Identity Protection 

Azure Active Directory Identity Protection ist ein Sicherheitsdienst, der eine umfassende Übersicht über Risikoereignisse und potenzielle Sicherheitsrisiken bietet, die für die Identitäten Ihrer Organisation bestehen. Microsoft beschäftigt sich schon seit mehr als einem Jahrzehnt mit dem Schutz cloudbasierter Identitäten, und mit Azure AD Identity Protection stellt Microsoft diese Schutzsysteme jetzt für Unternehmenskunden zur Verfügung. Für Identity Protection werden die vorhandenen Azure AD-Funktionen zur Erkennung von Anomalien genutzt (über die Berichte zu anomalen Aktivitäten von Azure AD), und es werden neue Risikoereignistypen eingeführt, mit denen Anomalien in Echtzeit erkannt werden können.

> [AZURE.NOTE] Die Vorschauversion von Identity Protection ist derzeit nur für Azure AD-Mandanten verfügbar, die in den USA bereitgestellt werden.
 

Die allermeisten Sicherheitsverletzungen kommen vor, wenn Angreifer Zugriff auf eine Umgebung erhalten, indem sie die Identität eines Benutzers stehlen. Angreifer gehen beim Nutzen von Drittanbieter-Sicherheitsverletzungen immer effektiver vor und starten professionelle Phishingangriffe. Nachdem ein Angreifer Zugriff auf ein mit geringen Rechten ausgestattetes Benutzerkonto erhalten hat, ist es für ihn relativ einfach, per Seitwärtsbewegung Zugriff auf wichtige Ressourcen des Unternehmens zu erlangen. Daher ist es wichtig, alle Identitäten zu schützen und bei einer Kompromittierung einer Identität proaktiv zu verhindern, dass die kompromittierte Identität missbraucht wird.

Die Ermittlung von kompromittierten Identitäten ist keine einfache Aufgabe. Glücklicherweise erhalten Sie hierbei Unterstützung von Identity Protection: Identity Protection nutzt adaptive Machine Learning-Algorithmen und heuristische Verfahren, um Anomalien und Risikoereignisse zu erkennen, die auf die Kompromittierung einer Identität hindeuten können.
 
Mit diesen Daten generiert Identity Protection Berichte und Warnungen, damit Sie diese Risikoereignisse untersuchen und entsprechende Aktionen zur Korrektur oder Lösung durchführen können.
 
Azure Active Directory Identity Protection ist aber viel mehr als ein Tool für die Überwachung und Berichterstellung. Basierend auf Risikoereignissen berechnet Identity Protection eine Benutzerrisikostufe für jeden Benutzer, sodass Sie risikobasierte Richtlinien konfigurieren können, um die Identitäten Ihrer Organisation automatisch zu schützen. Mit diesen risikobasierten Richtlinien in Verbindung mit anderen Kontrollelementen für den bedingten Zugriff von Azure Active Directory und EMS können adaptive Korrekturaktionen blockiert oder bereitgestellt werden. Hierzu gehören Kennwortzurücksetzungen und die Durchsetzung der Multi-Factor Authentication.

####Funktionen von Identity Protection 

**Erkennen von Risikoereignissen und gefährdeten Konten:**

- Erkennen von sechs Risikoereignistypen per Machine Learning und heuristischen Regeln 

- Berechnen von Benutzerrisikostufen

- Bereitstellen von benutzerdefinierten Empfehlungen zur Verbesserung der allgemeinen Sicherheit, indem Sicherheitsrisiken aufgedeckt werden

<br>

**Untersuchen von Risikoereignissen:**

- Senden von Benachrichtigungen für Risikoereignisse

- Untersuchen von Risikoereignissen mit relevanten und kontextbezogenen Informationen

- Bereitstellen grundlegender Workflows zum Nachverfolgen von Untersuchungen

- Bereitstellen des einfachen Zugriffs auf Korrekturaktionen, z. B. Kennwortzurücksetzung

<br>

**Risikobasierte Richtlinien für bedingten Zugriff:**

- Richtlinie für Abwehrmaßnahmen bei risikobehafteten Anmeldungen, indem Anmeldungen blockiert oder die Multi-Factor Authentication erzwungen wird.

- Richtlinie zum Blockieren oder Schützen risikobehafteter Benutzerkonten

- Richtlinie zur Durchsetzung der Registrierung von Benutzern für die Multi-Factor Authentication


## Erkennung und Risiko

### Risikoereignisse

Risikoereignisse sind Ereignisse, die von Identity Protection als verdächtig gekennzeichnet wurden und mit denen angegeben wird, dass eine Identität unter Umständen kompromittiert wurde. Eine vollständige Liste mit Risikoereignissen finden Sie unter [Arten von Risikoereignissen](#types-of-risk-events). Einige dieser Risikoereignisse sind über die Berichte zu anomalen Aktivitäten von Azure AD im Azure-Verwaltungsportal verfügbar. Die Tabelle unten enthält die verschiedenen Risikoereignistypen und den entsprechenden **Azure AD-Bericht zur anomalen Aktivitäten**. Microsoft investiert weiter in diesen Bereich und plant, die Erkennungsgenauigkeit von vorhandenen Risikoereignissen ständig weiter zu verbessern und fortlaufend neue Risikoereignistypen hinzuzufügen.



| Typ des Identity Protection-Risikoereignisses | Zugehöriger Azure AD-Bericht zu anomalen Aktivitäten |
| :-- | :-- |
| Kompromittierte Anmeldeinformationen | Benutzer mit kompromittierten Anmeldeinformationen |
| Unmöglicher Ortswechsel zu atypischen Orten |	Irreguläre Anmeldeaktivitäten |
| Anmeldungen von infizierten Geräten | Anmeldungen von möglicherweise infizierten Geräten |
| Anmeldungen von anonymen IP-Adressen | Anmeldungen von unbekannten Quellen |
| Anmeldungen von IP-Adressen mit verdächtigen Aktivitäten |	Anmeldungen von IP-Adressen mit verdächtigen Aktivitäten |
| Anmeldungen von unbekannten Standorten | - | | Sperr-Ereignisse (nicht Teil der öffentlichen Vorschau) | - |

Die folgenden Azure AD-Berichte zu anomalen Aktivitäten sind nicht als Risikoereignisse in Azure AD Identity Protection enthalten und daher auch nicht über Identity Protection verfügbar. Diese Berichte sind im Azure-Verwaltungsportal noch enthalten, aber sie werden nach und nach eingestellt, da sie durch Risikoereignisse in Identity Protection ersetzt werden.

- Anmeldungen nach mehreren Fehlern
- Anmeldungen aus mehreren geografischen Regionen

### Risikostufe

Die Risikostufe für ein Risikoereignis ist ein Hinweis (Hoch, Mittel oder Niedrig) auf den Schweregrad des Risikoereignisses. Die Risikostufe dient Identity Protection-Benutzern als Hilfe beim Priorisieren der Aktionen, die sie durchführen müssen, um das Risiko für ihre Organisation zu mindern. Der Schweregrad des Risikoereignisses steht für die Stärke des Signals, das zum Vorhersagen der Kompromittierung einer Identität dient, kombiniert mit dem damit üblicherweise verbundenen „Rauschen“.

- **Hoch**: Risikoereignis mit hohem Vertraulichkeitsgrad und hohem Schweregrad. Ereignisse dieser Art sind starke Indikatoren dafür, dass die Identität des Benutzers kompromittiert wurde, und für alle betroffenen Benutzerkonten sollten sofort Korrekturmaßnahmen eingeleitet werden.

- **Mittel**: Risikoereignis mit hohem Schweregrad, aber niedrigerem Vertraulichkeitsgrad (oder umgekehrt). Diese Ereignisse sind potenziell risikobehaftet, und für alle betroffenen Benutzerkonten sollten Korrekturmaßnahmen eingeleitet werden.

- **Niedrig**: Risikoereignis mit niedrigem Vertraulichkeitsgrad und Schweregrad. Bei diesem Ereignis ist unter Umständen keine sofortige Aktion erforderlich, aber zusammen mit anderen Risikoereignissen kann auch dies ein starker Hinweis dafür sein, dass die Identität kompromittiert wurde.


![Risikostufe](./media/active-directory-identityprotection/01.png "Risikostufe")

 

Risikoereignisse werden entweder in **Echtzeit** oder während der Nachbearbeitung identifiziert, nachdem das Risikoereignis bereits stattgefunden hat (offline). Derzeit werden die meisten Risikoereignisse in Identity Protection offline berechnet und innerhalb von zwei bis vier Stunden in Identity Protection angezeigt. Die Echtzeit-Risikoereignisse werden zwar in Echtzeit ausgewertet, aber sie werden in der Identity Protection-Konsole erst nach fünf bis zehn Minuten angezeigt.

Einige ältere Clients unterstützen derzeit keine Erkennung und Prävention von Risikoereignissen in Echtzeit. Daher können Anmeldungen von diesen Clients nicht in Echtzeit erkannt oder verhindert werden.

### Arten von Risikoereignissen

Dieser Abschnitt enthält eine Übersicht über die verfügbaren Arten von Risikoereignissen.

#### Kompromittierte Anmeldeinformationen

Kompromittierte Anmeldeinformationen sind Anmeldeinformationen, für die von unseren Microsoft-Sicherheitsexperten herausgefunden wird, dass sie öffentlich im Dark Web gepostet werden. Diese Anmeldeinformationen sind dort normalerweise in Klartext zu finden. Sie werden mit Azure AD-Anmeldeinformationen verglichen. Wenn sich eine Übereinstimmung ergibt, werden sie in Identity Protection als „Kompromittierte Anmeldeinformationen“ gemeldet.

Risikoereignisse vom Typ „Kompromittierte Anmeldeinformationen“ werden als Risikoereignisse mit dem Schweregrad „Hoch“ eingestuft, da es sich hierbei um einen eindeutigen Hinweis darauf handelt, dass der Benutzername und das Kennwort für Angreifer zur Verfügung stehen.

#### Unmöglicher Ortswechsel zu atypischen Orten

Mit dieser Art von Risikoereignis werden zwei Anmeldungen identifiziert, die von weit entfernten Orten durchgeführt wurden und bei denen mindestens einer der Orte aufgrund des bisherigen Verhaltens atypisch für den Benutzer ist. Außerdem ist der Zeitraum zwischen den beiden Anmeldungen kürzer als der Zeitraum, der für die Reise des Benutzers vom ersten zum zweiten Ort erforderlich wäre. Dies deutet darauf hin, dass ein anderer Benutzer die gleichen Anmeldeinformationen verwendet.

Bei diesem Machine Learning-Algorithmus werden offensichtliche „*falsch positive Ergebnisse*“ ignoriert, die zum Zustand des unmöglichen Ortswechsels beitragen, z. B. VPNs und regelmäßig von anderen Benutzern der Organisation verwendete Orte. Das System verfügt über einen anfänglichen Lernzeitraum von 14 Tagen, in dem das Anmeldeverhalten des neuen Benutzers erlernt wird.

Der unmögliche Ortswechsel ist normalerweise ein zuverlässiger Hinweis darauf, dass sich ein Hacker erfolgreich anmelden konnte. Es kann aber zu falsch positiven Ergebnissen kommen, wenn ein Benutzer mit einem neuen Gerät unterwegs ist oder eine VPN-Verbindung nutzt, die von anderen Personen der Organisation normalerweise nicht verwendet wird. Eine weitere Quelle für falsch positive Ergebnisse sind Anwendungen, von denen Server-IPs fälschlicherweise als Client-IPs übergeben werden. Hierbei kann der Eindruck entstehen, dass Anmeldungen über das Rechenzentrum durchgeführt werden, in dem das Back-End der Anwendung gehostet wird (häufig sind dies Microsoft-Rechenzentren, die den Eindruck entstehen lassen, dass Anmeldungen über im Besitz von Microsoft befindliche IP-Adressen abgewickelt werden). Aufgrund dieser falsch positiven Ergebnisse ergibt sich als Risikostufe für das Risikoereignis „Mittel“.

####Anmeldungen von infizierten Geräten

Bei dieser Art von Risikoereignis werden Anmeldungen von Geräten identifiziert, die mit Schadsoftware infiziert sind und für die bekannt ist, dass sie aktiv mit einem Botserver kommunizieren. Dies wird ermittelt, indem IP-Adressen den Benutzergeräts mit IP-Adressen korreliert werden, die in Kontakt mit einem Botserver gestanden haben.

Bei diesem Risikoereignis werden IP-Adressen identifiziert, keine Benutzergeräte. Wenn sich hinter einer einzelnen IP-Adresse mehrere Geräte verbergen und nur einige davon von einem Botnetzwerk kontrolliert werden, kann es sein, dass dieses Ereignis bei Anmeldungen von anderen Geräten fälschlicherweise ausgelöst wird. Dies der Grund dafür, warum dieses Risikoereignis die Einstufung „Niedrig“ erhält.

Sie sollten den Benutzer kontaktieren und alle Geräte des Benutzers überprüfen. Es ist auch möglich, dass das persönliche Gerät eines Benutzers infiziert ist oder dass (wie bereits erwähnt) eine andere Person unter der gleichen IP-Adresse wie der Benutzer ein infiziertes Gerät verwendet hat. Infizierte Geräte sind häufig mit Schadsoftware infiziert, die von Antivirensoftware noch nicht identifiziert wurde. Dies kann auch auf schlechte Angewohnheiten des Benutzers hinweisen, die dazu geführt haben, dass das Gerät infiziert wurde.

Weitere Informationen dazu, wie Sie Infektionen durch Schadsoftware behandeln können, finden Sie im [Microsoft-Center zum Schutz vor Schadsoftware](http://go.microsoft.com/fwlink/?linkid=335773&clcid=0x409).


#### Anmeldungen von anonymen IP-Adressen

Bei dieser Art von Risikoereignis werden Benutzer identifiziert, die sich erfolgreich unter einer IP-Adresse angemeldet haben, die als anonyme Proxy-IP-Adresse identifiziert wurde. Diese Proxys werden von Personen verwendet, die die IP-Adresse ihres Geräts verbergen möchten, und können in böswilliger Absicht eingesetzt werden.

Es wird empfohlen, dass Sie sich sofort an den Benutzer wenden, um zu überprüfen, ob dieser die anonyme IP-Adresse verwendet hat. Die Risikostufe für diese Art von Risikoereignis lautet „Mittel“, da eine anonyme IP-Adresse kein starker Hinweis auf eine Kompromittierung eines Kontos ist.

#### Anmeldungen von IP-Adressen mit verdächtigen Aktivitäten

Bei dieser Art von Risikoereignis werden IP-Adressen identifiziert, für die eine hohe Zahl fehlgeschlagener Anmeldeversuche beobachtet wurde, und zwar mit mehreren Benutzerkonten innerhalb eines kurzen Zeitraums. Dies entspricht Datenverkehrsmustern von IP-Adressen, die von Angreifern verwendet werden, und es ist ein starker Hinweis darauf, dass Konten entweder bereits kompromittiert sind oder dass dies kurz bevorsteht. Dies ist ein Machine Learning-Algorithmus, bei dem offensichtliche „*falsch positive Ergebnisse*“ ignoriert werden, z. B. IP-Adressen, die regelmäßig von anderen Benutzern in der Organisation verwendet werden. Das System benötigt einen anfänglichen Lernzeitraum von 14 Tagen, in dem es das Anmeldeverhalten eines neuen Benutzers und neuen Mandanten erlernt.

Es wird empfohlen, sich an den Benutzer zu wenden und zu überprüfen, ob dieser sich tatsächlich mit einer IP-Adresse angemeldet hat, die als verdächtig gekennzeichnet war. Die Risikostufe für diesen Ereignistyp lautet „Mittel“, da sich hinter einer IP-Adresse mehrere Geräte verbergen können, von denen nur einige Geräte für die verdächtige Aktivität verantwortlich sind.


#### Anmeldungen von unbekannten Standorten

Bei dieser Art von Risikoereignis handelt es sich um ein Verfahren zur Echtzeitauswertung von Anmeldungen, bei dem die letzten Standorte (IP, Breiten-/Längengrad und ASN) berücksichtigt werden, um neue oder unbekannte Orte zu ermitteln. Im System werden Informationen zu den vorherigen Standorten gespeichert, die von einem Benutzer genutzt wurden, und diese werden als „vertraute“ Standorte angesehen. Das Risikoereignis wird ausgelöst, wenn die Anmeldung von einem Standort aus erfolgt, der in der Liste der vertrauten Standorte noch nicht enthalten ist. Das System benötigt einen anfänglichen Lernzeitraum von 14 Tagen, in dem neue Standorte nicht als unbekannte Orte gekennzeichnet werden. Außerdem ignoriert das System Anmeldungen von vertrauten Geräten und von Standorten aus, die geografisch nahe an einem bekannten Speicherort liegen. <br> Unbekannte Standorte können ein starker Hinweis darauf sein, dass ein Angreifer versucht, eine gestohlene Identität zu verwenden. Zu falsch positiven Ergebnissen kann es kommen, wenn ein Benutzer auf Reisen ist, ein neues Gerät ausprobiert oder eine neue VPN-Verbindung verwendet. Aufgrund dieser falsch positiven Ergebnisse ergibt sich als Risikostufe für diese Art von Ereignis „Mittel“.

### Sicherheitsrisiken

Bei Sicherheitsrisiken handelt es sich um Schwachstellen in Ihrer Umgebung, die von einem Angreifer ausgenutzt werden können. Es wird empfohlen, dass Sie diese Sicherheitsrisiken entschärfen, um den Sicherheitsstatus Ihrer Organisation zu verbessern und Angreifer daran zu hindern, diese Schwachstellen auszunutzen.

#### Multi-Factor Authentication-Registrierung nicht konfiguriert 

Mit diesem Sicherheitsrisiko können Sie die Bereitstellung der Azure Multi-Factor Authentication in Ihrer Organisation besser steuern.

Azure Multi-Factor Authentication dient als zweite Sicherheitsebene für die Benutzerauthentifizierung. Sie hilft beim Schutz des Zugriffs auf Daten und Anwendungen und erfüllt gleichzeitig die Anforderungen von Benutzern an ein einfaches Anmeldeverfahren. Sie bietet leistungsfähige Authentifizierung mittels einiger einfacher Überprüfungsoptionen – Telefonanruf, SMS oder per Benachrichtigung bzw. Überprüfungscode in einer mobilen Anwendung sowie OATH-Tokens von Drittanbietern.

Es wird empfohlen, dass Sie die Multi-Factor Authentication für Anmeldungen von Benutzern obligatorisch machen. Multi-Factor Authentication spielt eine wichtige Rolle in Bezug auf risikobasierte Richtlinien für bedingten Zugriff, die unter Identity Protection verfügbar sind.

Weitere Informationen finden Sie unter [Was ist Azure Multi-Factor Authentication?](../multi-factor-authentication/multi-factor-authentication.md).


#### Nicht verwaltete Cloud-Apps

Mit diesem Sicherheitsrisiko können Sie in Ihrer Organisation nicht verwaltete Cloud-Apps identifizieren.
 
In modernen Unternehmen sind den IT-Abteilungen häufig nicht alle Cloudanwendungen bekannt, die die Benutzer der Organisation für ihre Arbeit verwenden. Es ist verständlich, warum Administratoren sich Sorgen um den unberechtigten Zugriff auf Unternehmensdaten, mögliche Datenlecks und andere Sicherheitsrisiken machen.

Es wird empfohlen, dass Sie in Ihrer Organisation die Cloud-App-Ermittlung einsetzen, um nicht verwaltete Cloudanwendungen zu ermitteln und diese Anwendungen mit Azure Active Directory zu verwalten.

Ausführliche Informationen finden Sie unter [Suchen nach nicht verwalteten Cloudanwendungen per Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md).



####Sicherheitswarnungen von Privileged Identity Management

Dieses Sicherheitsrisiko hilft Ihnen beim Ermitteln und Bereinigen von Warnungen zu privilegierten Identitäten in Ihrer Organisation.

Damit Benutzer privilegierte Vorgänge ausführen können, müssen Organisationen ihren Benutzern einen vorübergehenden oder dauerhaften privilegierten Zugriff auf Azure AD, Azure- oder Office 365-Ressourcen oder andere SaaS-Apps gewähren. Mit jedem dieser privilegierten Benutzer vergrößert sich die Angriffsfläche Ihrer Organisation. Dieses Sicherheitsrisiko dient Ihnen als Unterstützung beim Identifizieren von Benutzern mit nicht erforderlichem privilegiertem Zugriff und beim Treffen geeigneter Maßnahmen, um das damit verbundene Risiko zu mindern oder zu beseitigen.

Wir empfehlen Ihrer Organisation die Verwendung von Azure AD Privileged Identity Management, um privilegierte Identitäten und deren Zugriff auf Ressourcen in Azure AD und anderen Microsoft-Onlinediensten wie Office 365 oder Microsoft Intune verwalten, steuern und überwachen zu können.

Ausführliche Informationen finden Sie unter [Azure AD Privileged Identity Management](active-directory-privileged-identity-management-configure.md).

## Untersuchung
Ihre Navigation durch Identity Protection beginnt normalerweise mit dem Identity Protection-Dashboard.

<br><br> ![Korrektur](./media/active-directory-identityprotection/29.png "Korrektur") <br>

Über das Dashboard haben Sie Zugriff auf folgende Komponenten:
 
- Berichte, z. B. **Benutzer mit Risikokennzeichnung**, **Risikoereignisse** und **Sicherheitsrisiken**
- Einstellungen, z. B. die Konfiguration von **Sicherheitsrichtlinien**, **Benachrichtigungen** und **Multi-Factor Authentication-Registrierung**
 

Hier beginnen Sie in der Regel mit dem Untersuchungsprozess. Dies ist der Prozess der Überprüfung von Aktivitäten, Protokollen und anderen relevanten Informationen zu einem Risikoereignis, um zu entscheiden, ob Schritte zur Fehlerbeseitigung oder Lösung erforderlich sind. Außerdem wird ermittelt, ob und wie die Identität kompromittiert und wie die kompromittierte Identität verwendet wurde.


### Benachrichtigungen

Von Azure AD Identity Protection werden zwei Arten von automatisierten Benachrichtigungs-E-Mails gesendet, die der Verwaltung von Benutzerrisiko- und Risikoereignissen dienen:

- E-Mail als Warnung vor kompromittiertem Benutzer

- Wöchentliche E-Mail mit Übersicht

#### E-Mail als Warnung vor kompromittiertem Benutzer

Eine E-Mail als Warnung vor einem kompromittiertem Benutzer wird generiert, wenn ein Konto von Azure AD Identity Protection als kompromittiert erkannt wird. Die E-Mail enthält einen Link zum Bericht vom Typ „Benutzer mit Risikokennzeichnung“ in der Identity Protection-Konsole. Es wird empfohlen, dass Benachrichtigungen über betroffene Benutzer sofort untersucht werden.


#### Wöchentliche E-Mail mit Übersicht

Die wöchentliche E-Mail mit einer Übersicht enthält eine Zusammenfassung der neuen Risikoereignisse.<br> Sie hat folgenden Inhalt:
- Gefährdete Benutzer
- Gemeldete Risikoereignisse
- Erkannte Sicherheitsrisiken
- Links zu verwandten Berichten in Identity Protection


<br> ![Korrektur](./media/active-directory-identityprotection/400.png "Korrektur") <br>

Standardmäßig wird die Warnung vor dem kompromittierten Benutzer an alle Azure Active Directory-Administratoren gesendet. Sie können die Empfänger wie folgt anpassen:

- Folgen Sie dem Link „Empfänger verwalten“ unten in der Warnung.

- Klicken Sie in den Identity Protection-Einstellungen auf „Benachrichtigungen“.
 
<br> ![Benutzerrisiken](./media/active-directory-identityprotection/62.png "Benutzerrisiken") <br>
 



## Was ist eine Benutzerrisikostufe?

Eine Benutzerrisikostufe ist ein Hinweis (Hoch, Mittel oder Niedrig) auf die Wahrscheinlichkeit, mit der die Identität des Benutzers kompromittiert wurde. Sie wird basierend auf den Benutzerrisikoereignissen berechnet, die der Identität des Benutzers zugeordnet sind.

Der Status eines Risikoereignisses lautet entweder **Aktiv** oder **Geschlossen**. Nur Risikoereignisse, die den Status **Aktiv** haben, fließen in die Berechnung des Benutzerrisikos ein.

Die Benutzerrisikostufe wird anhand der folgenden Eingaben berechnet:

- Aktive Risikoereignisse des Benutzers
- Risikostufe dieser Ereignisse 
- Durchgeführte Korrekturaktionen 

<br> ![Benutzerrisiken](./media/active-directory-identityprotection/86.png "Benutzerrisiken") <br>



Sie können die Benutzerrisikostufen verwenden, um Richtlinien für den bedingten Zugriff zu erstellen. So können risikobehaftete Benutzer an der Anmeldung gehindert werden, oder es kann das sichere Ändern des Kennworts erzwungen werden.


## Manuelles Schließen von Risikoereignissen

In den meisten Fällen führen Sie Korrekturaktionen durch, z. B. das sichere Zurücksetzen des Kennworts, um Risikoereignisse automatisch zu schließen. Allerdings ist dies unter Umständen nicht immer möglich. <br> Da Risikoereignisse, die den Status **Aktiv** haben, in die Berechnung des Benutzerrisikos einfließen, müssen Sie eine Risikostufe ggf. manuell herabsetzen, indem Sie Risikoereignisse manuell schließen. <br> Während der Untersuchung können Sie die folgenden Aktionen durchführen, um den Status eines Risikoereignisses zu ändern:

<br> ![Aktionen](./media/active-directory-identityprotection/34.png "Aktionen") <br>

- **Lösen:** Wenn Sie nach dem Untersuchen eines Risikoereignisses außerhalb von Identity Protection eine geeignete Korrekturaktion durchgeführt haben und der Meinung sind, dass das Risikoereignis als geschlossen angesehen werden kann, können Sie das Ereignis als „Aufgelöst“ kennzeichnen. Für Ereignisse mit dem Status „Aufgelöst“ wird der Status auf „Geschlossen“ festgelegt, und das Risikoereignis fließt nicht mehr in das Benutzerrisiko ein.

- **Als falsch positives Ergebnis kennzeichnen:** In einigen Fällen kann die Untersuchung eines Risikoereignisses ergeben, dass es fälschlicherweise als risikobehaftet gekennzeichnet wurde. Sie können zur Reduzierung der Anzahl solcher Vorfälle beitragen, indem Sie das Risikoereignis als falsch positives Ergebnis kennzeichnen. Auf diese Weise können die Machine Learning-Algorithmen die Klassifizierung ähnlicher Ereignisse in Zukunft verbessern. Der Status der falsch positiven Ereignisse wird auf **Geschlossen** festgelegt, und sie fließen nicht mehr in die Berechnung des Benutzerrisikos ein.

- **Ignorieren:** Falls Sie keine Korrekturaktion durchgeführt haben und das Risikoereignis trotzdem aus der Liste „Aktiv“ entfernen möchten, können Sie ein Risikoereignis mit „Ignorieren“ kennzeichnen. Der Ereignisstatus wird dann in „Geschlossen“ geändert. Ignorierte Ereignisse gehen nicht in das Benutzerrisiko ein. Diese Option sollte nur unter ungewöhnlichen Umständen verwendet werden.

- **Reaktivieren:** Risikoereignisse, die manuell geschlossen wurden (durch Auswahl von **Lösen**, **Falsch positiv** oder **Ignorieren**) können reaktiviert werden. Hierfür wird der Ereignisstatus zurück auf **Aktiv** gesetzt. Reaktivierte Risikoereignisse fließen in die Berechnung des Benutzerrisikos ein. Risikoereignisse, die per Korrekturmaßnahme (z. B. das sichere Zurücksetzen des Kennworts) geschlossen werden, können nicht reaktiviert werden.



## Bereinigen von Benutzerrisikoereignissen

Eine Korrektur bzw. Bereinigung ist eine Aktion zum Schützen einer Identität oder eines Geräts, die bzw. das zuvor wahrscheinlich oder mit Sicherheit kompromittiert war. Bei einer Korrekturaktion wird die Identität bzw. das Gerät wieder in einen sicheren Zustand versetzt, und vorherige Risikoereignisse, die der Identität oder dem Gerät zugeordnet sind, werden gelöst.

Sie können wie folgt vorgehen, um Benutzerrisikoereignisse zu bereinigen:

- Durchführen einer sicheren Kennwortzurücksetzung, um Benutzerrisikoereignisse manuell zu bereinigen 

- Konfigurieren einer Sicherheitsrichtlinie für das Benutzerrisiko, um Benutzerrisikoereignisse automatisch zu lösen oder zu bereinigen

- Wiederherstellen des infizierten Geräts durch ein neues Image


### Sichere Kennwortzurücksetzung

Eine sichere Kennwortzurücksetzung ist eine effektive Korrektur für viele Risikoereignisse. Wenn sie durchgeführt wird, werden diese Risikoereignisse automatisch geschlossen, und die Benutzerrisikoebene wird neu berechnet. Sie können die Identity Protection-Konsole verwenden, um für einen risikobehafteten Benutzer eine Kennwortzurücksetzung zu initiieren.

Die Konsole bietet zwei Optionen zum Zurücksetzen eines Kennworts:

**Kennwort zurücksetzen:** Aktivieren Sie die Option **Benutzer zur Kennwortzurücksetzung auffordern**, damit Benutzer die Wiederherstellung selbst durchführen können, wenn sie sich für die Multi-Factor Authentication registriert haben. Während der nächsten Anmeldung des Benutzers muss der Benutzer einen Multi-Factor Authentication-Ablauf erfolgreich bewältigen und wird dann gezwungen, das Kennwort zu ändern. Diese Option ist nicht verfügbar, wenn das Benutzerkonto noch nicht für Multi-Factor Authentication registriert ist.

**Temporäres Kennwort:** Wählen Sie die Option **Generiert ein temporäres Kennwort**, um das vorhandene Kennwort sofort ungültig zu machen und ein neues temporäres Kennwort für den Benutzer zu erstellen. Senden Sie das neue temporäre Kennwort an eine andere E-Mail-Adresse des Benutzers oder an den Manager des Benutzers. Da das Kennwort temporär ist, wird der Benutzer aufgefordert, das Kennwort bei der Anmeldung zu ändern.

<br> ![Richtlinie](./media/active-directory-identityprotection/71.png "Richtlinie") <br>



## Benutzerrisiko-Sicherheitsrichtlinie

Eine Benutzerrisiko-Sicherheitsrichtlinie ist eine Richtlinie für bedingten Zugriff, mit der die Risikostufe für einen bestimmten Benutzer ausgewertet wird und Korrektur- und Lösungsmaßnahmen basierend auf vordefinierten Bedingungen und Regeln angewendet werden. <br><br> ![Benutzerrisiko-Richtlinie](./media/active-directory-identityprotection/500.png "Benutzerrisiko-Richtlinie") <br>

Azure AD Identity Protection dient Ihnen als Unterstützung beim Verwalten der Korrektur- und Lösungsmaßnahmen für Benutzer, die als risikobehaftet eingestuft sind. Sie haben folgende Möglichkeiten:

- Legen Sie die Benutzer und Gruppen fest, für die die Richtlinie gilt. <br><br> ![Benutzerrisiko-Richtlinie](./media/active-directory-identityprotection/501.png "Benutzerrisiko-Richtlinie") <br>

- Legen Sie den Schwellenwert für die Benutzerrisikostufe (Niedrig, Mittel oder Hoch) fest, mit der eine Kennwortänderung ausgelöst wird. <br><br> ![Benutzerrisiko-Richtlinie](./media/active-directory-identityprotection/502.png "Benutzerrisiko-Richtlinie") <br>

- Legen Sie den Schwellenwert für die Benutzerrisikostufe (Niedrig, Mittel oder Hoch) fest, mit der die Blockierung eines Benutzers ausgelöst wird. <br><br> ![Benutzerrisiko-Richtlinie](./media/active-directory-identityprotection/503.png "Benutzerrisiko-Richtlinie") <br>

- Überprüfen und bewerten Sie die Auswirkungen einer Änderung, bevor Sie sie aktivieren. <br><br> ![Benutzerrisiko-Richtlinie](./media/active-directory-identityprotection/504.png "Benutzerrisiko-Richtlinie") <br>


Wenn Sie für den Schwellenwert **Hoch** wählen, wird die Richtlinie weniger häufig ausgelöst, und die Auswirkungen für Benutzer verringern sich. Benutzer mit der Einstufung **Niedrig** und **Mittel**, die als risikobehaftet gekennzeichnet sind, werden aber aus der Richtlinie ausgeschlossen. Dies trägt unter Umständen nicht zum Schutz von Identitäten oder Geräten bei, die zuvor vermutlich oder mit Sicherheit kompromittiert waren.

Gehen Sie beim Festlegen der Richtlinie wie folgt vor:

- Schließen Sie Benutzer aus, für die voraussichtlich viele falsch positive Ergebnisse generiert werden (Entwickler, Sicherheitsanalysten).

- Schließen Sie Benutzer mit Gebietsschemas aus, für die die Aktivierung der Richtlinie keinen praktischen Nutzen hat (z. B. kein Zugriff auf den Helpdesk).

- Verwenden Sie während des anfänglichen Rollouts der Richtlinie den Schwellenwert **Hoch**, oder wenn Sie die Auswirkungen für Endbenutzer verringern möchten.

- Verwenden Sie einen Schwellenwert mit der Einstellung **Niedrig**, falls für Ihre Organisation eine höhere Sicherheitsebene erforderlich ist. Wenn Sie für den Schwellenwert **Niedrig** auswählen, erhöht sich der Aufwand für die Benutzer bei der Anmeldung, aber die Sicherheit wird erhöht.

Die empfohlene Standardvorgehensweise für die meisten Organisationen ist die Konfiguration einer Regel für den Schwellenwert **Mittel**, um eine gute Balance zwischen Benutzerfreundlichkeit und Sicherheit zu erzielen.



### Lösen von Benutzerrisikoereignissen
Administratoren können eine Sicherheitsrichtlinie für das Benutzerrisiko festlegen, um Benutzer je nach Risikostufe bei der Anmeldung zu blockieren.

Das Sperren einer Anmeldung bewirkt Folgendes:
 
- Verhindert die Generierung neuer Benutzerrisikoereignisse für den betroffenen Benutzer

- Ermöglicht Administratoren die Bereinigung der Risikoereignisse, die die Identität des Benutzers beeinträchtigen, und die Wiederherstellung eines sicheren Zustands





### Korrektur- und Lösungsabläufe für Benutzerrisiken  

Die folgenden Abschnitte enthalten eine Übersicht über die Benutzerumgebung, die für die Korrektur- und Lösungsabläufe für Benutzerrisiken verwendet wird.

#### Wiederherstellungsablauf für kompromittierte Konten

Wenn eine Sicherheitsrichtlinie für das Benutzerrisiko konfiguriert wurde, müssen Benutzer, für die die in der Richtlinie angegebene Benutzerrisikostufe erfüllt ist (und daher als kompromittiert angesehen werden), den Wiederherstellungsablauf für kompromittierte Benutzer durchlaufen, bevor sie sich anmelden können.

Der Wiederherstellungsablauf für kompromittierte Benutzer umfasst drei Schritte:

1. Der Benutzer wird darüber informiert, dass die Sicherheit des Kontos aufgrund von verdächtigen Aktivitäten oder kompromittierten Anmeldeinformationen gefährdet ist.

<br> ![Korrektur](./media/active-directory-identityprotection/101.png "Korrektur") <br>

2.	Der Benutzer muss seine Identität nachweisen, indem er eine Sicherheitsabfrage richtig beantwortet. Wenn der Benutzer für Multi-Factor Authentication registriert ist, kann er die Wiederherstellung selbst durchführen. Hierfür muss der Benutzer mit einem Sicherheitscode einen Roundtripvorgang für seine Telefonnummer durchführen. 

<br> ![Korrektur](./media/active-directory-identityprotection/110.png "Korrektur") <br>


3.	Am Ende des Vorgangs wird der Benutzer gezwungen, sein Kennwort zu ändern, da eine andere Person unter Umständen Zugriff auf sein Konto hatte. Screenshots dieser Benutzeroberfläche finden Sie weiter unten.
 
<br> ![Korrektur](./media/active-directory-identityprotection/111.png "Korrektur") <br>







 
#### Kennwort zurücksetzen
Wenn die Anmeldung für einen Benutzer gesperrt ist, kann ein Administrator ein temporäres Kennwort für ihn generieren. Der Benutzer muss sein Kennwort dann bei der nächsten Anmeldung ändern. Mit einer Kennwortänderung werden die meisten Arten von Risikoereignissen für den Benutzer gelöst und geschlossen.

<br> ![Korrektur](./media/active-directory-identityprotection/160.png "Korrektur") <br>


### Entschärfung der Benutzerrisikoebene

### Kompromittiertes Konto: Blockiert 

Um die Blockierung für einen Benutzer aufzuheben, der aufgrund einer Benutzerrisiko-Sicherheitsrichtlinie blockiert wurde, muss sich der Benutzer an einen Administrator oder den Helpdesk wenden. Die selbst durchgeführte Wiederherstellung per Multi-Factor Authentication ist in diesem Fall nicht möglich.

<br> ![Korrektur](./media/active-directory-identityprotection/104.png "Korrektur") <br>


## Lösen von Anmelderisikoereignissen 
Eine Lösung ist eine entschärfende Aktion, mit der für einen Angreifer die Möglichkeit eingeschränkt wird, eine kompromittierte Identität oder ein Gerät auszunutzen, ohne für die Identität oder das Gerät wieder einen sicheren Zustand herzustellen. Bei einer Lösung werden keine vorherigen Anmelderisikoereignisse behoben, die der Identität oder dem Gerät zugeordnet sind.

Sie können den bedingten Zugriff in Azure AD Identity Protection verwenden, um Anmelderisikoereignisse automatisch zu lösen. Mit diesen Richtlinien berücksichtigen Sie die Risikostufe des Benutzers oder der Anmeldung, um risikobehaftete Anmeldungen zu blockieren oder den Benutzer zur Durchführung der Multi-Factor Authentication zu zwingen. Mit diesen Aktionen kann unter Umständen verhindert werden, dass ein Angreifer eine gestohlene Identität nutzt, um Schaden anzurichten. Außerdem haben Sie ggf. Zeit, um die Identität zu schützen.


## Anmelderisiko-Sicherheitsrichtlinie

Eine Anmelderisiko-Sicherheitsrichtlinie ist eine bedingte Zugriffsrichtlinie, mit der das Risiko für eine bestimmte Anmeldung ausgewertet wird und Lösungen basierend auf vordefinierten Bedingungen und Regeln angewendet werden. <br><br> ![Richtlinie zum Anmelderisiko](./media/active-directory-identityprotection/700.png "Richtlinie zum Anmelderisiko") <br>

Azure AD Identity Protection unterstützt Sie beim Verwalten der Entschärfung risikobehafteter Anmeldungen, indem Folgendes ermöglicht wird:

- Legen Sie die Benutzer und Gruppen fest, für die die Richtlinie gilt. <br><br> ![Richtlinie zum Anmelderisiko](./media/active-directory-identityprotection/701.png "Richtlinie zum Anmelderisiko") <br>

- Legen Sie den Schwellenwert für die Anmelderisikostufe (Niedrig, Mittel oder Hoch) fest, mit dem eine Multi-Factor Authentication-Abfrage für die betroffenen Anmeldungen ausgelöst wird. <br><br> ![Richtlinie zum Anmelderisiko](./media/active-directory-identityprotection/702.png "Richtlinie zum Anmelderisiko") <br>

- Legen Sie den Schwellenwert für die Anmelderisikostufe (Niedrig, Mittel oder Hoch) fest, mit dem die betroffenen Anmeldungen blockiert werden. <br><br> ![Richtlinie zum Anmelderisiko](./media/active-directory-identityprotection/703.png "Richtlinie zum Anmelderisiko") <br>

- Überprüfen und bewerten Sie die Auswirkungen einer Änderung, bevor Sie sie aktivieren. <br><br> ![Richtlinie zum Anmelderisiko](./media/active-directory-identityprotection/704.png "Richtlinie zum Anmelderisiko") <br>

 
Wenn Sie für den Schwellenwert **Hoch** wählen, wird die Richtlinie weniger häufig ausgelöst, und die Auswirkungen für Benutzer verringern sich.<br> Hierbei werden aber Anmeldungen mit dem Status **Niedrig** und **Mittel**, die als risikobehaftet gekennzeichnet sind, aus der Richtlinie ausgeschlossen. Für einen Angreifer wird das Ausnutzen einer kompromittierten Identität so ggf. nicht blockiert.

Gehen Sie beim Festlegen der Richtlinie wie folgt vor:

- Schließen Sie Benutzer aus, die nicht über Multi-Factor Authentication verfügen bzw. für die dies nicht möglich ist.

- Schließen Sie Benutzer mit Gebietsschemas aus, für die die Aktivierung der Richtlinie keinen praktischen Nutzen hat (z. B. kein Zugriff auf den Helpdesk).

- Schließen Sie Benutzer aus, für die voraussichtlich viele falsch positive Ergebnisse generiert werden (Entwickler, Sicherheitsanalysten).

- Verwenden Sie während des anfänglichen Rollouts der Richtlinie den Schwellenwert **Hoch**, oder wenn Sie die Auswirkungen für Endbenutzer verringern möchten.

- Verwenden Sie einen Schwellenwert mit der Einstellung **Niedrig**, falls für Ihre Organisation eine höhere Sicherheitsebene erforderlich ist. Wenn Sie für den Schwellenwert **Niedrig** auswählen, erhöht sich der Aufwand für die Benutzer bei der Anmeldung, aber die Sicherheit wird erhöht.

Die empfohlene Standardvorgehensweise für die meisten Organisationen ist die Konfiguration einer Regel für den Schwellenwert **Mittel**, um eine gute Balance zwischen Benutzerfreundlichkeit und Sicherheit zu erzielen.

 
Für die Richtlinie zum Anmelderisiko gilt Folgendes:

- Sie wird auf den gesamten Browserdatenverkehr und alle Anmeldungen angewendet, für den bzw. die eine moderne Authentifizierung genutzt wird.
- Sie wird nicht auf Anwendungen angewendet, für die ältere Sicherheitsprotokolle verwendet werden, indem der WS-Trust-Endpunkt beim Verbund-IDP, z. B. ADFS, deaktiviert wird.

Auf der Seite **Risikoereignisse** in der Identity Protection-Konsole werden alle Ereignisse aufgeführt:

- Ereignisse, auf die die Richtlinie angewendet wurde
- Sie können die Aktivität überprüfen und ermitteln, ob die Aktion geeignet war 




## Multi-Factor Authentication-Registrierungsrichtlinie

Multi-Factor Authentication wird verwendet, um sich zusätzliche Sicherheit über die Identität eines Benutzers zu verschaffen.<br> Die Registrierung für die Multi-Factor Authentication ist ein wichtiger Schritt zur Vorbereitung Ihrer Organisation auf den Schutz und die Wiederherstellung nach der Kompromittierung von Konten. <br> ![MFA-Registrierung](./media/active-directory-identityprotection/600.png "MFA-Registrierung") <br>

Azure AD Identity Protection unterstützt Sie beim Verwalten des Rollouts der Multi-Factor Authentication-Registrierung, indem Folgendes ermöglicht wird:

- Legen Sie die Benutzer und Gruppen fest, für die die Richtlinie gilt. <br><br> ![MFA-Registrierung](./media/active-directory-identityprotection/601.png "MFA-Registrierung") <br>

- Definieren Sie, wie lange Benutzer die Registrierung überspringen dürfen. <br><br> ![MFA-Registrierung](./media/active-directory-identityprotection/602.png "MFA-Registrierung") <br>

- Zeigen Sie den aktuellen Registrierungsstatus der betroffenen Benutzer an. <br><br> ![MFA-Registrierung](./media/active-directory-identityprotection/603.png "MFA-Registrierung") <br>


##Lösungsabläufe für Anmelderisiken 

#### Wiederherstellungsablauf für risikobehaftete Anmeldungen

Wenn ein Administrator eine Richtlinie für Anmelderisiken konfiguriert hat, werden die betroffenen Benutzer benachrichtigt, sobald sie versuchen, sich anzumelden.

Der Ablauf für risikobehaftete Anmeldungen besteht aus zwei Schritten:

1. Der Benutzer wird informiert, dass im Zusammenhang mit seiner Anmeldung eine Unregelmäßigkeit erkannt wurde, z. B. das Anmelden von einem neuen Standort, mit einem neuen Gerät oder mit einer neuen App. <br> ![Korrektur](./media/active-directory-identityprotection/120.png "Korrektur") <br>

2. Der Benutzer muss seine Identität nachweisen, indem er eine Sicherheitsabfrage richtig beantwortet. Wenn der Benutzer für die Multi-Factor Authentication registriert ist, muss er mit einem Sicherheitscode einen Roundtripvorgang für seine Telefonnummer durchführen. Da es sich nur um eine risikobehaftete Anmeldung und nicht um ein kompromittiertes Konto handelt, muss der Benutzer sein Kennwort bei diesem Ablauf nicht ändern. <br> ![Korrektur](./media/active-directory-identityprotection/121.png "Korrektur") <br>
 
#### Risikobehaftete Anmeldung blockiert
Administratoren können auch eine Richtlinie für Anmelderisiken festlegen, um Benutzer bei der Anmeldung je nach Risikostufe zu blockieren. Um die Blockierung aufzuheben, müssen Endbenutzer sich an einen Administrator oder den Helpdesk wenden, oder sie können versuchen, sich von einem vertrauten Standort oder mit einem vertrauten Gerät anzumelden. Die selbst durchgeführte Wiederherstellung per Multi-Factor Authentication ist in diesem Fall nicht möglich.

<br> ![Korrektur](./media/active-directory-identityprotection/130.png "Korrektur") <br>
 
#### Registrierung für Multi-Factor Authentication

Die Benutzerfreundlichkeit ist für den Wiederherstellungsablauf für kompromittierte Konten und den Ablauf für risikobehaftete Anmeldungen jeweils am höchsten, wenn der Benutzer die Wiederherstellung selbst durchführen kann. Wenn ein Benutzer für die Multi-Factor Authentication registriert ist, ist seinem Konto bereits eine Telefonnummer zugeordnet, die für die erfolgreiche Beantwortung von Sicherheitsabfragen verwendet werden kann. Es ist keine Beteiligung des Helpdesks oder eines Administrators erforderlich, um für ein kompromittiertes Konto die Wiederherstellung durchzuführen. Daher ist es dringend zu empfehlen, dass Sie für Ihre Benutzer die Registrierung für Multi-Factor Authentication vornehmen.

Administratoren haben folgende Möglichkeiten:

- Festlegen einer Richtlinie, die es für Benutzer erforderlich macht, ihre Konten für die zusätzliche Sicherheitsüberprüfung einzurichten 
- Zulassen des Überspringens der Multi-Factor Authentication-Registrierung für bis zu 30 Tage, falls Benutzern vor dem Registrieren eine Schonfrist eingeräumt werden soll

 
 <br> ![Korrektur](./media/active-directory-identityprotection/140.png "Korrektur") <br> <br> ![Korrektur](./media/active-directory-identityprotection/141.png "Korrektur") <br> <br> ![Korrektur](./media/active-directory-identityprotection/142.png "Korrektur") <br>

 

#### Multi-Factor Authentication-Registrierung während einer risikobehafteten Anmeldung

Es ist wichtig, dass sich Benutzer für Multi-Factor Authentication registrieren, damit sie vorbereitet sind und richtig auf Sicherheitsabfragen reagieren können. Wenn ein Benutzer nicht für Multi-Factor Authentication registriert ist, dies für die Richtlinie aber erforderlich ist, kann er während einer risikobehafteten Anmeldung zum Registrieren aufgefordert werden. Dies bedeutet, dass ein Angreifer anstelle des richtigen Benutzers zum Hinzufügen einer Telefonnummer aufgefordert wird. <br> Zur Vermeidung dieser Situation sollten Sie für Benutzer die Registrierung für Multi-Factor Authentication so bald wie möglich obligatorisch machen. Die Telefonnummer ist dem Konto dann bereits zugeordnet, wenn es ggf. kompromittiert wird. Alternativ hierzu können Administratoren kompromittierte Benutzer, die nicht für Multi-Factor Authentication registriert sind, auch vollständig blockieren.

 <br> ![Korrektur](./media/active-directory-identityprotection/150.png "Korrektur") <br> <br> ![Korrektur](./media/active-directory-identityprotection/151.png "Korrektur") <br>






 
## Playbook

### Simulieren von Risikoereignissen

Dieser Abschnitt enthält eine Übersicht über die Simulation der folgenden Arten von Risikoereignissen:

- Anmeldungen von anonymen IP-Adressen (einfach)

- Anmeldungen von unbekannten Standorten (mittelschwer)

- Unmöglicher Ortswechsel zu atypischen Orten (schwierig)

Andere Risikoereignisse können nicht auf sichere Weise simuliert werden.


#### Anmeldungen von anonymen IP-Adressen

Bei dieser Art von Risikoereignis werden Benutzer identifiziert, die sich erfolgreich unter einer IP-Adresse angemeldet haben, die als anonyme Proxy-IP-Adresse identifiziert wurde. Diese Proxys werden von Personen verwendet, die die IP-Adresse ihres Geräts verbergen möchten, und können in böswilliger Absicht eingesetzt werden.

Führen Sie die folgenden Schritte aus, um eine Anmeldung über eine anonyme IP-Adresse zu simulieren:

1.	Laden Sie den [Tor Browser](https://www.torproject.org/projects/torbrowser.html.en) herunter.
2.	Navigieren Sie mit dem Tor Browser zu https://myapps.microsoft.com.   
3.	Geben Sie die Anmeldeinformationen des Kontos ein, das im Bericht „Anmeldungen von anonymen IP-Adressen“ enthalten sein soll.

Fertig! Die Anmeldung sollte innerhalb von fünf Minuten in Identity Protection angezeigt werden.


####Anmeldungen von unbekannten Standorten
Beim Risiko vom Typ „Unbekannter Standort“ handelt es sich um ein Verfahren zur Echtzeitauswertung von Anmeldungen, bei dem die letzten Standorte (IP, Breiten-/Längengrad und ASN) berücksichtigt werden, um neue oder unbekannte Orte zu ermitteln. Das System speichert vorherige IPs, Breiten-/Längengrad und ASNs eines Benutzers und stuft diese als „vertraute“ Orte ein. Ein Anmeldestandort wird als nicht vertraut angesehen, wenn er nicht mit einem der vertrauten Standorte identisch ist. Das System benötigt einen anfänglichen Lernzeitraum von 14 Tagen, in dem neue Standorte nicht als unbekannte Orte gekennzeichnet werden. Außerdem ignoriert das System Anmeldungen von vertrauten Geräten und von Standorten aus, die geografisch nahe an einem vorhandenen bekannten Speicherort liegen.

Um unbekannte Speicherorte zu simulieren, müssen Sie sich von einem Ort und mit einem Gerät anmelden, der bzw. das für das Konto noch nicht verwendet wurde. Schritt für Schritt:

1.	Wählen Sie ein Konto aus, das bereits über einen Anmeldeverlauf von 14 Tagen verfügt. 

2.	Entscheiden Sie sich für eine der folgenden Optionen:
	
    a. Navigieren Sie bei Verwendung eines VPN zu [https://myapps.microsoft.com](https://myapps.microsoft.com), und geben Sie die Anmeldeinformationen des Kontos ein, für das Sie das Risikoereignis simulieren möchten.

    b. Bitten Sie eine Person an einem anderen Standort sich mit den Anmeldeinformationen für das Konto anzumelden (nicht zu empfehlen).

Fertig! Die Anmeldung sollte innerhalb von fünf Minuten in Identity Protection angezeigt werden.
 
#### Unmöglicher Ortswechsel zu atypischen Orten
Das Simulieren des unmöglichen Ortswechsels ist schwierig, da der Algorithmus einen Machine Learning-Ansatz nutzt, um falsch positive Ergebnisse auszusieben, z. B. der unmögliche Ortswechsel vertrauter Geräte oder Anmeldungen über VPNs, die von anderen Benutzern im Verzeichnis verwendet werden. Außerdem ist für den Algorithmus ein Anmeldeverlauf von 3 bis 14 Tagen des Benutzers erforderlich, bevor mit dem Generieren von Risikoereignissen begonnen wird.

1.	Navigieren Sie mit Ihrem Standardbrowser zu [https://myapps.microsoft.com](https://myapps.microsoft.com).  

2.	Geben Sie die Anmeldeinformationen des Kontos ein, für das Sie ein Risikoereignis vom Typ „Unmöglicher Ortswechsel“ generieren möchten.

3.	Ändern Sie nun Ihren Benutzer-Agent. Sie können den Benutzer-Agent in Internet Explorer über die Entwicklertools ändern. Ändern Sie den Benutzer-Agent in Firefox oder Chrome mit einem geeigneten Add-On (User-Agent Switcher).

4.	Ändern Sie nun Ihre IP-Adresse. Sie können die IP-Adresse ändern, indem Sie ein VPN oder ein Tor-Add-On verwenden oder unter Azure einen neuen Computer in einem anderen Rechenzentrum einrichten.

5.	Melden Sie sich unter [https://myapps.microsoft.com](https://myapps.microsoft.com) mit den gleichen Anmeldeinformationen wie vorher und nur wenige Minuten nach der vorherigen Anmeldung an.

Die Anmeldung sollte innerhalb von zwei bis vier Stunden in Identity Protection angezeigt werden. Aufgrund der beteiligten komplexen Machine Learning-Modelle ist es auch möglich, dass dieser Vorgang nicht erfasst wird. Es kann ratsam sein, diese Schritte für mehrere Azure AD-Konten zu replizieren.


#### Simulieren von Sicherheitsrisiken 
Sicherheitsrisiken sind Schwachstellen in einer Azure AD-Umgebung, die von einem Angreifer ausgenutzt werden können. Derzeit werden in Azure AD Identity Protection drei Arten von Sicherheitsrisiken erfasst, für die andere Features von Azure AD genutzt werden. Diese Sicherheitsrisiken werden in Identity Protection automatisch angezeigt, nachdem diese Features eingerichtet wurden.

-	Azure AD [Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)
-	Azure AD [Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md)
-	Azure AD [Privileged Identity Management](active-directory-privileged-identity-management-configure.md) 



###Risikobasierte Richtlinien für bedingten Zugriff
####Risiko einer Benutzerkompromittierung

**Führen Sie die folgenden Schritte aus, um das Risiko einer Benutzerkompromittierung zu testen**:

1.	Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) mit den Anmeldeinformationen vom Typ „Globaler Administrator“ für Ihren Mandanten an.

2.	Navigieren Sie zu **Identity Protection**.

3.	Klicken Sie im Hauptblatt von Azure AD Identity Protection auf **Einstellungen**.

4.	Klicken Sie im Blatt **Portaleinstellungen** unter **Sicherheitsregeln** auf **Risiko einer Benutzerkompromittierung**.

5.	Deaktivieren Sie im Blatt **Anmelderisiko** die Option **Regel aktivieren**, und klicken Sie dann auf **Einstellungen speichern**.

6.	Simulieren Sie für ein bestimmtes Benutzerkonto ein Risikoereignis vom Typ „Unbekannte Standorte“ oder „Anonyme IP-Adresse“. Die Benutzerrisikostufe wird für den Benutzer hierdurch auf **Mittel** angehoben.

7.	Warten Sie einige Minuten, und vergewissern Sie sich, dass die Stufe des Benutzers **Mittel** lautet.

8.	Wechseln Sie zum Blatt **Portaleinstellungen**.

9.	Wählen Sie im Blatt **Risiko einer Benutzerkompromittierung** unter **Regel aktivieren** die Option **Ein**.

10.	Wählen Sie eine der folgenden Optionen:

    a. Wählen Sie unter **Anmeldung blockieren** die Option **Mittel**, um die Blockierung durchzuführen.

    b. Wählen Sie zum Erzwingen der Änderung in ein sicheres Kennwort unter **Erfordert mehrstufige Authentifizierung** die Option **Mittel**.

13.	Klicken Sie auf **Speichern**.

14. Sie können den risikobasierten bedingten Zugriff jetzt testen, indem Sie sich mit einem Benutzer anmelden, der über eine erhöhte Risikostufe verfügt. Falls das Benutzerrisiko „Mittel“ lautet, wird Ihre Anmeldung blockiert, oder die Änderung des Kennworts wird erzwungen. Dies hängt von der von Ihnen festgelegten Richtlinie ab. <br><br> ![Playbook](./media/active-directory-identityprotection/201.png "Playbook") <br>

 
####Anmelderisiko

 


Führen Sie die folgenden Schritte aus, um das Anmelderisiko zu testen:

1.	Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) mit den Anmeldeinformationen vom Typ „Globaler Administrator“ für Ihren Mandanten an.

2.	Navigieren Sie zu **Identity Protection**.

3.	Klicken Sie im Hauptblatt von **Azure AD Identity Protection** auf **Einstellungen**.

4.	Klicken Sie im Blatt **Portaleinstellungen** unter **Sicherheitsregeln** auf **Anmelderisiko**.

5.	Wählen Sie im Blatt „Anmelderisiko“ unter **Regel aktivieren** die Option **Ein**.

7.	Wählen Sie eine der folgenden Optionen:

    a. Wählen Sie unter **Anmeldung blockieren** die Option **Mittel**, um die Blockierung durchzuführen.

    b. Wählen Sie zum Erzwingen der Änderung in ein sicheres Kennwort unter **Erfordert mehrstufige Authentifizierung** die Option **Mittel**.

8.	Wählen Sie unter „Anmeldung blockieren...“ die Option „Mittel“, um die Blockierung durchzuführen.

9.	Wählen Sie zum Erzwingen von Multi-Factor Authentication unter „Erfordert mehrstufige Authentifizierung…“ die Option „Mittel“.

10.	Klicken Sie auf **Speichern**.

11.	Sie können den risikobehafteten bedingten Zugriff nun testen, indem Sie das Risikoereignis „Unbekannte Standorte“ oder „Anonyme IP-Adresse“ simulieren. Beide werden als Risikoereignisse der Stufe „Mittel“ angesehen.

<br> ![Playbook](./media/active-directory-identityprotection/200.png "Playbook") <br>


## Weitere Informationen

 - [Azure AD und Identity Show: Identity Protection (Vorschau)](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)
 - [Identity Protection – Glossar](active-directory-identityprotection-glossary.md)

<!---HONumber=AcomDC_0302_2016-->