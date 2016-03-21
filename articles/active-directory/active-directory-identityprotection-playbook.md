<properties
	pageTitle="Azure Active Directory Identity Protection-Playbook| Microsoft Azure"
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
	ms.date="03/08/2016"
	ms.author="markvi"/>

#Azure Active Directory Identity Protection-Playbook 

Dieses Playbook hilft Ihnen:

- Daten in der Identity Protection Umgebung durch das Simulieren von Risikoereignissen und Sicherheitsrisiken aufzufüllen
- Richtlinien für den risikobasierten bedingten Zugriff einzurichten und die Auswirkungen dieser Richtlinien zu testen


## Simulieren von Risikoereignissen

Dieser Abschnitt bietet Ihnen die Schritte zum Simulieren der folgenden Risikoereignistypen:

- Anmeldungen von anonymen IP-Adressen (einfach)
- Anmeldungen von unbekannten Standorten (mittelschwer)
- Unmöglicher Ortswechsel zu atypischen Orten (schwierig)

Andere Risikoereignisse können nicht auf sichere Weise simuliert werden.


### Anmeldungen von anonymen IP-Adressen

Bei dieser Art von Risikoereignis werden Benutzer identifiziert, die sich erfolgreich unter einer IP-Adresse angemeldet haben, die als anonyme Proxy-IP-Adresse identifiziert wurde. Diese Proxys werden von Personen verwendet, die die IP-Adresse ihres Geräts verbergen möchten, und können in böswilliger Absicht eingesetzt werden.

**Führen Sie die folgende Schritte aus, um eine Anmeldung über eine anonyme IP-Adresse zu simulieren,**:

1.	Laden Sie den [Tor Browser](https://www.torproject.org/projects/torbrowser.html.en) herunter.
2.	Navigieren Sie mit dem Torbrowser zu [https://myapps.microsoft.com](https://myapps.microsoft.com).   
3.	Geben Sie die Anmeldeinformationen des Kontos ein, das im Bericht **Anmeldungen von anonymen IP-Adressen** enthalten sein soll.

Die Anmeldung wird auf dem Identity Protection Dashboard innerhalb von fünf Minuten angezeigt.


###Anmeldungen von unbekannten Standorten

Beim Risiko vom Typ „Unbekannter Standort“ handelt es sich um ein Verfahren zur Echtzeitauswertung von Anmeldungen, bei dem die letzten Standorte (IP, Breiten-/Längengrad und ASN) berücksichtigt werden, um neue oder unbekannte Orte zu ermitteln. Das System speichert vorherige IPs, Breiten-/Längengrad und ASNs eines Benutzers und stuft diese als „vertraute“ Orte ein. Ein Anmeldestandort wird als nicht vertraut angesehen, wenn er nicht mit einem der vertrauten Standorte identisch ist.

Azure Active Directory Identity Protection:

 - benötigt einen anfänglichen Lernzeitraum von 14 Tagen, in dem neue Standorte nicht als unbekannte Orte gekennzeichnet werden.
 - ignoriert Anmeldungen von vertrauten Geräten und von Standorten aus, die geografisch nahe an einem vorhandenen bekannten Speicherort liegen.

Um unbekannte Standorte zu simulieren, müssen Sie sich von einem Ort und mit einem Gerät anmelden, der bzw. das für das Konto noch nicht verwendet wurde.


**Gehen Sie folgendermaßen vor, um eine Anmeldung von einem unbekannten Ort zu simulieren**:

1.	Wählen Sie ein Konto aus, das bereits über einen Anmeldeverlauf von mindestens 14 Tagen verfügt. 

2.	Entscheiden Sie sich für eine der folgenden Optionen:
	
    a. Navigieren Sie bei Verwendung eines VPN zu [https://myapps.microsoft.com](https://myapps.microsoft.com), und geben Sie die Anmeldeinformationen des Kontos ein, für das Sie das Risikoereignis simulieren möchten.

    b. Bitten Sie eine Person an einem anderen Standort sich mit den Anmeldeinformationen für das Konto anzumelden (nicht empfehlenswert).

Die Anmeldung wird auf dem Identity Protection Dashboard innerhalb von fünf Minuten angezeigt.
 
### Unmöglicher Ortswechsel zu atypischen Orten
Das Simulieren des unmöglichen Ortswechsels ist schwierig, da der Algorithmus einen Machine Learning-Ansatz nutzt, um falsch positive Ergebnisse auszusieben, z. B. der unmögliche Ortswechsel vertrauter Geräte oder Anmeldungen über VPNs, die von anderen Benutzern im Verzeichnis verwendet werden. Außerdem ist für den Algorithmus ein Anmeldeverlauf von 3 bis 14 Tagen des Benutzers erforderlich, bevor mit dem Generieren von Risikoereignissen begonnen wird.

**Führen Sie die folgenden Schritte aus, um einen unmöglichen Ortswechsel zu einen atypischen Ort zu simulieren**:

1.	Navigieren Sie mit Ihrem Standardbrowser zu [https://myapps.microsoft.com](https://myapps.microsoft.com).  

2.	Geben Sie die Anmeldeinformationen des Kontos ein, für das Sie ein Risikoereignis vom Typ „Unmöglicher Ortswechsel“ generieren möchten.

3.	Ändern Sie Ihren Benutzer-Agent. Sie können den Benutzer-Agent in Internet Explorer über die Entwicklertools ändern. Ändern Sie den Benutzer-Agent in Firefox oder Chrome mit einem geeigneten Add-On (User-Agent Switcher).

4.	Ändern Sie Ihre IP-Adresse. Sie können die IP-Adresse ändern, indem Sie ein VPN oder ein Tor-Add-On verwenden oder unter Azure einen neuen Computer in einem anderen Rechenzentrum einrichten.

5.	Melden Sie sich unter [https://myapps.microsoft.com](https://myapps.microsoft.com) mit den gleichen Anmeldeinformationen wie vorher und innerhalb von wenigen Minuten nach der vorherigen Anmeldung an.

Die Anmeldung wird im Identity Protection Dashboard innerhalb der nächsten 2 bis 4 Stunden angezeigt.<br> Aufgrund der beteiligten komplexen Machine Learning-Modelle ist es auch möglich, dass dieser Vorgang nicht erfasst wird.<br> Eventuell möchten Sie, diese Schritte für mehrere Azure AD-Konten replizieren.


## Simulieren von Sicherheitsrisiken 
Sicherheitsrisiken sind Schwachstellen in einer Azure AD-Umgebung, die von einem Angreifer ausgenutzt werden können. Derzeit werden in Azure AD Identity Protection drei Arten von Sicherheitsrisiken erfasst, für die andere Features von Azure AD genutzt werden. Diese Sicherheitsrisiken werden im Identity Protection Dashboard automatisch angezeigt, nachdem diese Features eingerichtet wurden.

-	Azure AD [Multi-Factor Authentication?](../multi-factor-authentication/multi-factor-authentication.md)
-	Azure AD [Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md)
-	Azure AD [Privileged Identity Management](active-directory-privileged-identity-management-configure.md) 



##Risiko einer Benutzerkompromittierung

**Führen Sie die folgenden Schritte aus, um das Risiko einer Benutzerkompromittierung zu testen**:

1.	Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) mit den Anmeldeinformationen für „Globaler Administrator“ für Ihren Mandanten an.

2.	Navigieren Sie zu **Identity Protection**.

3.	Klicken Sie im Hauptblatt von **Azure AD Identity Protection** auf **Einstellungen**.

4.	Klicken Sie im Blatt **Portaleinstellungen** unter **Sicherheitsregeln** auf **Risiko einer Benutzerkompromittierung**.

5.	Deaktivieren Sie im Blatt **Anmelderisiko** die Option **Regel aktivieren**, und klicken Sie dann auf **Einstellungen speichern**.

6.	Simulieren Sie für ein bestimmtes Benutzerkonto ein Risikoereignis vom Typ „Unbekannte Standorte“ oder „Anonyme IP-Adresse“. Die Benutzerrisikostufe wird für diesen Benutzer hierdurch auf **Mittel** angehoben.

7.	Warten Sie einige Minuten, und vergewissern Sie sich dann, dass die Stufe Ihres Benutzers **Mittel** lautet.

8.	Gehen Sie zum Blatt **Portaleinstellungen**.

9.	Wählen Sie im Blatt **Risiko einer Benutzerkompromittierung** unter **Regel aktivieren** die Option **Ein**.

10.	Wählen Sie eine der folgenden Optionen:

    a. Wählen Sie unter **Anmeldung blockieren** die Option **Mittel**, um die Blockierung durchzuführen.

    b. Wählen Sie zum Erzwingen der Änderung in ein sicheres Kennwort unter **Erfordere mehrstufige Authentifizierung** die Option **Mittel**.

13.	Klicken Sie auf **Speichern**.

14. Sie können den risikobasierten bedingten Zugriff jetzt testen, indem Sie sich mit einem Benutzer anmelden, der über eine erhöhte Risikostufe verfügt. Ist das Benutzerrisiko „Mittel“ ist die Anmeldung entweder, abhängig von der Konfiguration Ihrer Richtlinie, blockiert, oder Sie sind gezwungen, Ihr Kennwort zu ändern. <br><br> ![Playbook](./media/active-directory-identityprotection-playbook/201.png "Playbook") <br>

 
##Anmelderisiko

 
**Führen Sie die folgenden Schritte aus, um das Anmelderisiko zu testen:**

1.	Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) mit den Anmeldeinformationen für „Globaler Administrator“ für Ihren Mandanten an.

2.	Navigieren Sie zu **Identity Protection**.

3.	Klicken Sie im Hauptblatt von **Azure AD Identity Protection** auf **Einstellungen**.

4.	Klicken Sie im Blatt **Portaleinstellungen** unter **Sicherheitsregeln** auf **Anmelderisiko**.

5.	Wählen Sie im Blatt **Anmelderisiko** unter **Regel aktivieren** die Option **Ein**.

7.	Wählen Sie eine der folgenden Optionen:

    a. Wählen Sie unter **Anmeldung blockieren** die Option **Mittel**, um die Blockierung durchzuführen.

    b. Wählen Sie zum Erzwingen der Änderung in ein sicheres Kennwort unter **Erfordere mehrstufige Authentifizierung** die Option **Mittel**.

8.	Wählen Sie unter „Anmeldung blockieren“ die Option „Mittel“, um die Blockierung durchzuführen.

9.	Wählen Sie zum Erzwingen von mehrstufiger Authentifizierung unter **Erfordere mehrstufige Authentifizierung** die Option **Mittel**.

10.	Klicken Sie auf **Speichern**.

11.	Sie können den risikobasierten bedingten Zugriff nun testen, indem Sie die Risikoereignisse „Unbekannte Standorte“ oder „Anonyme IP-Adresse“ simulieren, da beide als Risikoereignisse der Stufe **Mittel** angesehen werden.

<br> ![Playbook](./media/active-directory-identityprotection-playbook/200.png "Playbook") <br>


## Weitere Informationen

 - [Azure Active Directory Identity Protection](active-directory-identityprotection.md)

<!---HONumber=AcomDC_0309_2016-->