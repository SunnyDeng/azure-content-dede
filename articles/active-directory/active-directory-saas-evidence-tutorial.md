<properties
	pageTitle="Tutorial: Azure Active Directory-Integration mit Evidence.com | Microsoft Azure"
	description="Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Evidence.com konfigurieren."
	services="active-directory"
	documentationCenter=""
	authors="asmalser-msft"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/23/2016"
	ms.author="asmalser"/>


# Tutorial: Azure Active Directory-Integration mit Evidence.com

In diesem Tutorial wird erläutert, wie einmaliges Anmelden für Azure Active Directory (AAD) und Evidence.com eingerichtet wird. Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:
	
* Ein gültiges Microsoft Azure-Abonnement
* Ein Evidence.com-Abonnement mit aktiviertem einmaligem Anmelden (Senden Sie eine E-Mail an earlyaccess@evidence.com, wenn SAML-basiertes einmaliges Anmelden nicht aktiviert ist.)

Nach Abschluss dieses Tutorials können sich die AAD-Benutzer, denen Sie Evidence.com-Zugriff zugewiesen haben, mit dem AAD-Zugriffsbereich per einmaligem Anmelden an der Anwendung anmelden.

## Hinzufügen von Evidence.com zu Ihrem Verzeichnis

In diesem Abschnitt wird beschrieben, wie Sie Evidence.com als integrierte Anwendung in Azure Active Directory hinzufügen.

**So aktivieren Sie die Anwendungsintegration für Evidence**

1.	Klicken Sie im linken Navigationsbereich des [klassischen Azure-Portals](https://manage.windowsazure.com) auf **Active Directory**.

2.	Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3.	Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

4.	Klicken Sie zum Öffnen des Anwendungskatalogs auf **Hinzufügen** und dann auf **Anwendung aus dem Katalog hinzufügen**.

5.	Geben Sie im Suchfeld **Evidence.com** ein.

6.	Wählen Sie im Ergebnisbereich **Evidence.com** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.


## Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Konto in Azure Active Directory bei Evidence.com zu authentifizieren.

**So konfigurieren Sie einmaliges Anmelden**

1.	Klicken Sie nach dem Hinzufügen von Evidence.com im klassischen Azure-Portal auf **Einmaliges Anmelden konfigurieren**. 
 
2.	Wählen Sie auf dem nächsten Bildschirm die Option **Azure AD – einmaliges Anmelden**, und klicken Sie auf **Weiter**.

3.	Geben Sie auf dem Bildschirm „App-URL konfigurieren“ die URL ein, mit der sich Benutzer an Ihrem Evidence.com-Mandanten anmelden können (Beispiel: https://yourtenant.evidence.com), und klicken Sie dann auf **Weiter**.

4.	Klicken Sie auf den Link **Zertifikat herunterladen**, und speichern Sie es auf der lokalen Festplatte. Dieses Zertifikat und die Metadaten-URLs (Entitäts-ID, Anmelde-URL für SSO und Abmelde-URL) werden verwendet, um SSO auf der Evidence.com-Website einzurichten.

5.	Melden Sie sich in einem separaten Webbrowserfenster an Ihrem Evidence.com-Mandanten als Administrator an, und navigieren Sie zur Registerkarte **Admin**.
      
6.	Klicken Sie auf **Agentur – einmaliges Anmelden**.
 
7.	Wählen Sie die Option **SAML-basiertes einmaliges Anmelden**.
 
8.	Kopieren Sie die Werte für **Aussteller-URL**, **Einmaliges Anmelden** und **Einmaliges Abmelden**, die im klassischen Azure-Portal angezeigt werden, in die entsprechenden Felder von Evidence.com.

9.	Öffnen Sie das Zertifikat, das Sie in Schritt 4 heruntergeladen haben, mit einem Text-Editor wie „Notepad.exe“, kopieren Sie den Inhalt, und fügen Sie ihn in das Feld **Sicherheitszertifikat** ein.

10. Speichern Sie die Konfiguration in Evidence.com.
 
11.	Aktivieren Sie im klassischen Azure-Portal die Option **Bestätigen Sie, dass Sie die einmalige Anmeldung wie oben beschrieben konfiguriert haben**. Bei Aktivierung kann das aktuelle Zertifikat für diese Anwendung arbeiten.
 
12.	Klicken Sie auf der Seite „Bestätigung zur einmaligen Anmeldung“ auf **Fertig stellen**.


## Erstellen eines Evidence.com-Testbenutzers

Damit sich Azure AD-Benutzer anmelden können, müssen sie für den Zugriff innerhalb der Evidence.com-Anwendung bereitgestellt werden. In diesem Abschnitt wird beschrieben, wie in Evidence.com Azure AD-Benutzerkonten erstellt werden.

**So stellen Sie in Evidence.com ein Benutzerkonto bereit**

1.	Melden Sie sich in einem Webbrowserfenster bei der Evidence.com-Unternehmenswebsite als Administrator an.

2.	Navigieren Sie zur Registerkarte **Admin**.

3.	Klicken Sie auf **Benutzer hinzufügen**.

4.	Klicken Sie auf die Schaltfläche **Hinzufügen**.

5.  Die **E-Mail-Adresse** des hinzugefügten Benutzers muss mit dem Benutzernamen des Benutzers in Azure AD übereinstimmen, dem Sie Zugriff gewähren möchten. Wenn der Benutzername und die E-Mail-Adresse in Ihrem Unternehmen nicht identisch sind, können Sie im klassischen Azure-Portal den Abschnitt **Evidence.com > Attribute > Einmaliges Anmelden** verwenden, um das an Evidence.com gesendete nameidenitifier-Element zu ändern und als E-Mail-Adresse zu verwenden.


## Zuweisen von Benutzern zu Evidence.com

Damit bereitgestellten AAD-Benutzern Evidence.com im Zugriffsbereich angezeigt wird, muss ihnen im klassischen Azure-Portal der Zugriff zugewiesen werden.

**So weisen Sie Evidence.com Benutzer zu**

1.	Klicken Sie auf der Schnellstartseite für Evidence.com im klassischen Azure-Portal auf **Zuweisen von Benutzern zu Evidence.com**.
 
2.	Wählen Sie im Menü **Einblenden**, ob Sie Evidence.com einen Benutzer oder eine Gruppe zuweisen möchten, und klicken Sie auf die Schaltfläche mit dem Häkchen.
 
3.	Wählen Sie in der Liste **Benutzer** die zu gruppierenden Benutzer aus, denen Sie Evidence.com zuweisen möchten.
 
4.	Klicken Sie in der Fußzeile auf die Schaltfläche **Zuweisen**.

<!---HONumber=AcomDC_0224_2016-->