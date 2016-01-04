<properties
	pageTitle="Hinzufügen eines eigenen Domänennamens zu Azure Active Directory | Microsoft Azure"
	description="Hier wird erläutert, wie Sie Ihren eigenen Domänennamen und zugehörige Informationen zu Azure Active Directory hinzufügen."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="12/01/2015"
	ms.author="curtand"/>

# Hinzufügen eines eigenen Domänennamens zu Azure Active Directory

Wenn Sie sich für einen Microsoft Clouddienst anmelden, wird Ihnen ein Domänenname im folgenden Format ausgegeben: contoso.onmicrosoft.com. Sie können diesen anfänglichen Domänennamen weiterhin verwenden, oder Sie können Ihren eigenen benutzerdefinierten Domänennamen zum Clouddienst hinzufügen. In diesem Thema wird erläutert, wie Sie Ihren eigenen Domänennamen und zugehörige Informationen zu Azure Active Directory (Azure AD) hinzufügen.

Für Office 365-Benutzer könnten folgende Themen ebenfalls von Interesse sein:

- [DNS-Grundlagen](https://support.office.com/article/854b6b2b-0255-4089-8019-b765cff70377)
- [Suchen Sie Ihre Domänenregistrierungsstelle oder Ihren DNS-Hostinganbieter](https://support.office.com/article/Find-your-domain-registrar-or-DNS-hosting-provider-b5b633ba-1e56-4a98-8ff5-2acaac63a5c8/)
- [Arbeiten mit Domänennamen in Office 365](https://support.office.com/article/Work-with-domain-names-in-Office-365-4f1bd681-337a-4bd3-a7b7-cf77b18d0870/)

## Zu Ihrer Domäne "onmicrosoft.com"

Sie können Ihre onmicrosoft.com-Domäne mit anderen Diensten verwenden. Beispielsweise können Sie die Domäne mit Exchange Online und Lync Online verwenden, um Verteilerlisten und Anmeldekonten zu erstellen, damit Benutzer auf SharePoint Online und Websitesammlungen zugreifen können.

Wenn Sie Ihren eigenen Domänennamen zu Ihrem Verzeichnis hinzufügen, können Sie weiterhin Ihre Domäne "onmicrosoft.com" verwenden.

Nachdem Sie bei der Registrierung den Namen für den Clouddienst ausgewählt haben, z. B. contoso.onmicrosoft.com, können Sie ihn nicht mehr ändern.

## Wie kann ich meine eigene Domäne hinzufügen?

Wenn Ihre Organisation bereits über einen benutzerdefinierten Domänennamen verfügt, können Sie ihn mit allen abonnierten Microsoft-Onlinediensten zu Ihrem Azure AD-Verzeichnis hinzufügen. Nachdem Sie Ihren Domänennamen in Azure AD hinzugefügt haben, können Sie die Zuordnung Ihres Domänennamens mit verschiedenen Clouddiensten starten.

Sie können Ihrem Azure AD-Verzeichnis bis zu 900 Domänennamen hinzufügen. Dazu verwenden Sie:

- Das klassische Azure-Portal, das Office 365-Portal oder das Microsoft Intune-Portal.
- Das Azure Active Directory-Modul für Windows PowerShell. Weitere Informationen zu den Cmdlets. die Sie dafür verwenden können, finden Sie unter [Verwalten von Domänen in Azure AD](https://msdn.microsoft.com/library/azure/dn919677.aspx).

Sie müssen bereits einen Domänennamen registriert und die Anmeldeinformationen für Ihre Domänennamen-Registrierungsstelle vorliegen haben (z. B. Go Daddy oder Register.com).

Sie können mehrere Domänen zu Ihrem Verzeichnis hinzufügen. Sie können jedoch nicht dieselbe Domäne zu verschiedenen Verzeichnissen hinzufügen. Wenn Sie also z. B. Ihre Domäne zu Ihrem Verzeichnis hinzufügen, können Sie kein weiteres Azure AD-Verzeichnis erstellen und den gleichen Domänennamen hinzufügen.

Wenn Sie einmaliges Anmelden mit dem Clouddienst verwenden möchten, empfehlen wir, für die Active Directory-Umgebung das Microsoft-Bereitstellungsvorbereitungsprogramm auszuführen. Dieses Tool untersucht Ihre Active Directory-Umgebung und erstellt einen Bericht mit Informationen darüber, ob Sie für die Einrichtung des einmaligen Anmeldens bereit sind. Wenn dies nicht der Fall ist, werden die notwendigen Änderungen zur Vorbereitung des einmaligen Anmeldens aufgelistet. Beispielsweise überprüft das Tool, ob Ihre Benutzer über UPNs verfügen und ob diese UPNs das richtige Format aufweisen. Informationen zum Herunterladen des Tools finden Sie unter [Microsoft-Bereitstellungsvorbereitungsprogramm](http://go.microsoft.com/fwlink/?linkid=235650).

> [AZURE.NOTE]Sie verwenden Office 365? Sobald Sie Ihre Domäne eingerichtet haben, können Sie mit dem Erstellen von E-Mail-Adressen, Lync Online-Konten und Verteilergruppen beginnen, die Ihren benutzerdefinierten Domänennamen verwenden. Sie können auch Ihren Domänennamen für eine öffentliche, auf SharePoint Online gehostete Website verwenden.

- [Hinzufügen und Überprüfen eines Domänennamens mithilfe des klassischen Azure-Portals](#add-and-verify-a-domain-using-the-azure-management-portal)
- [Bearbeiten Sie DNS-Einträge für Ihre Clouddienste](#edit-dns-records-for-your-cloud-services)
- [Überprüfen einer Domäne bei einer Domänennamen-Registrierungsstelle](#verify-a-domain-at-any-domain-name-registrar)

### Hinzufügen und Überprüfen eines Domänennamens mithilfe des klassischen Azure-Portals

1. Klicken Sie im klassischen Azure-Portal auf **Active Directory**, und klicken Sie dann auf den Namen des Verzeichnisses Ihrer Organisation. Sie können einen der folgenden Schritte ausführen:
    1. Klicken Sie auf der Standardverzeichnisseite auf **Domäne hinzufügen** im Abschnitt **Verbesserung der Anmeldeerfahrung für Benutzer**.
2. Klicken Sie auf **Domänen**, und klicken Sie dann auf **Hinzufügen eine Kundendomäne** oder auf die Schaltfläche **Hinzufügen**.
2. Auf der Seite **Domäne hinzufügen** geben Sie den Domänennamen ein, den Sie hinzufügen möchten, und führen Sie einen der folgenden Schritte aus:
    1. Wenn Sie Ihr lokales Active Directory nicht in Azure AD integrieren möchten, führen Sie folgende Schritte aus:
        1. Lassen Sie das Kontrollkästchen **Ich plane die Konfiguration dieser Domäne für einmaliges Anmelden mit meinem lokalen Active Directory** deaktiviert, und klicken Sie dann auf die Schaltfläche **Hinzufügen**.
        2. Nachdem die Meldung angezeigt wird, dass Ihre Domäne erfolgreich in Azure AD hinzugefügt wurde, klicken Sie auf den Pfeil, um zur nächsten Seite zu gelangen, damit Sie Ihre Domäne überprüfen können.
        3. Folgen Sie den Anweisungen auf der nächsten Seite, um sicherzustellen, dass der im vorherigen Schritt hinzugefügte Domänenname Ihnen gehört. Schrittweise Anleitungen finden Sie unter Überprüfen einer Domäne bei einer Domänennamen-Registrierungsstelle.
    2. Wenn Sie Ihr lokales Active Directory in Azure AD integrieren möchten, führen Sie folgende Schritte aus:
        1. Aktivieren Sie das Kontrollkästchen **Ich plane die Konfiguration dieser Domäne für einmaliges Anmelden mit meinem lokalen Active Directory**, und klicken Sie dann auf die Schaltfläche **Hinzufügen**.
        2. Nachdem die Meldung angezeigt wird, dass Ihre Domäne erfolgreich in Azure AD hinzugefügt wurde, klicken Sie auf den Pfeil, um zur nächsten Seite zu wechseln, und folgen dann den Anweisungen auf der Seite, um die für einmaliges Anmelden hinzugefügte Domäne zu konfigurieren.

> [AZURE.NOTE]Nachdem Sie Ihren Domänennamen in Azure AD hinzugefügt haben, können Sie den Standarddomänennamen für neue E-Mail-Adressen ändern. Weitere Informationen finden Sie unter [Wie kann ich den primären Domänennamen für Benutzer ändern?](#how-can-i-change-the-primary-domain-name-for-users?) Sie können auch das Profil für ein vorhandenes Benutzerkonto bearbeiten, um die E-Mail-Adresse zu aktualisieren (die auch Ihre Benutzer-ID ist), damit Sie Ihren benutzerdefinierten Domänennamen anstelle der Domäne "onmicrosoft.com" verwenden können.

### Bearbeiten Sie DNS-Einträge für Ihre Clouddienste

> [AZURE.NOTE]Verwenden Sie Microsoft Intune? Sie müssen keine DNS-Einträge für den Microsoft Intune-Clouddienst bearbeiten.

Nachdem Sie Ihren benutzerdefinierten Domänennamen hinzugefügt und überprüft haben, besteht der nächste Schritt darin, die DNS-Einträge bei Ihrer Domänenregistrierungsstelle oder Ihrem DNS-Hostinganbieter zu bearbeiten, die Datenverkehr auf den Clouddienst verweisen. Azure AD bietet die DNS-Informationen, die Sie benötigen.

Wenn Sie den Assistenten **Hinzufügen einer Domäne** gerade abgeschlossen haben, klicken Sie auf **DNS-Einträge konfigurieren**. Gehen Sie andernfalls folgendermaßen vor:

1. Klicken Sie im klassischen Azure-Portal im linken Bereich auf **Domänen**.
2. Abhängig vom verwendeten Portal klicken Sie auf den Domänennamen, den Sie einrichten möchten, und klicken Sie dann entweder auf **DNS-Einstellungen** oder **DNS-Einstellungen anzeigen**. Die Seite **DNS-Einstellungen** listet die DNS-Einträge für den Clouddienst auf.

    Wenn Sie einen Dienst konfigurieren möchten, den Sie auf der Registerkarte "DNS-Einstellungen" nicht sehen, überprüfen Sie Ihre Auswahl der Domänendienste, um sicherzugehen, dass Sie diesen Dienst für diesen Domänennamen ausgewählt haben. Informationen zum Ändern der Einstellungen, z. B. zum Hinzufügen von Lync Online, finden Sie unter Festlegen der Dienste, die Sie mit Ihrer Domäne verwenden.

3. Auf der Website Ihrer Domänennamen-Registrierungsstelle fügen Sie Ihrer DNS-Datei die erforderlichen Datensätze hinzu.

In der Regel dauert es ca. 15 Minuten, damit die Änderungen wirksam werden. Es kann jedoch bis zu 72 Stunden dauern, bis der erstellte DNS-Eintrag über das DNS-System weitergegeben wird. Wenn Sie diese Eintragseinstellungen erneut anzeigen möchten, klicken Sie auf der Seite **Domänen** auf die Domäne, und klicken Sie dann auf der Seite **Domäneneigenschaften** auf die Registerkarte **DNS-Einstellungen**.

Um den Status Ihrer Domäne zu überprüfen, klicken Sie auf der Seite **Domänen** auf die Domäne, und klicken Sie dann auf der Seite **Domäneneigenschaften** auf **Domänenproblembehandlung**.

### Überprüfen einer Domäne bei einer Domänennamen-Registrierungsstelle

Wenn Sie bereits eine Domäne bei einer Domänennamen-Registrierungsstelle registriert haben und sie so konfiguriert werden soll, dass sie mit Azure AD funktioniert, dann ist die Überprüfung der Domäne erforderlich, um zu bestätigen, dass Sie die Domäne besitzen. Zum Überprüfen Ihrer Domäne erstellen Sie einen DNS-Eintrag bei der Domänennamen-Registrierungsstelle (oder dort, wo Ihr DNS gehostet wird), und Azure AD verwendet diesen Datensatz, um zu bestätigen, dass Sie die Domäne besitzen.

Bevor Sie Ihre Domäne überprüfen können, müssen Sie eine benutzerdefinierte Domäne in Azure AD hinzufügen. Wenn Sie eine benutzerdefinierte Domäne hinzugefügt haben, diese aber noch nicht überprüft wurde, wird entweder der Status **Klicken Sie zum Überprüfen der Domäne** oder **Nicht überprüft** angezeigt.

#### Zusammentragen der Domäneninformationen

Je nach verwendetem Portal, das Sie zur Verwaltung Ihres Azure AD-Verzeichnisses verwenden, müssen Sie einige Informationen zu Ihrer Domäne sammeln, damit Sie später einen DNS-Eintrag erstellen können, der während der Überprüfung verwendet wird.

Wenn Sie Microsoft Intune oder das Azure-Kontoportal verwenden:

1. Suchen Sie in der Liste der Domänennamen auf der Seite **Domänen** die Domäne, die Sie überprüfen möchten. Klicken Sie in der Spalte **Status** auf **Klicken Sie zum Überprüfen der Domäne**.
2. Auf der Seite **Domäne überprüfen** wählen Sie in der Dropdownliste **Anweisungen zur Durchführung dieses Schritts mit:** Ihren DNS-Hostinganbieter aus. Wenn Ihr Anbieter nicht in der Liste angezeigt wird, wählen Sie **Allgemeine Anweisungen**.
3. In der Dropdownliste **Bestätigungsmethode wählen:** wählen Sie **TXT-Eintrag hinzufügen (bevorzugte Methode)** oder **MX-Datensatz hinzufügen (alternative Methode)**.

    Wenn Ihr DNS-Hostinganbieter das Erstellen von TXT-Datensätzen ermöglicht, empfehlen wir einen TXT-Eintrag zur Überprüfung. Warum? TXT-Einträge lassen sich einfach erstellen und bergen nicht die Gefahr, die E-Mail-Übermittlung zu beeinträchtigen, wenn versehentlich ein falscher Wert eingegeben wird.

4. Aus der Tabelle kopieren oder notieren Sie die Informationen **Ziele oder Verweise auf die Adresse**.

Wenn Sie das klassische Azure-Portal verwenden:

1. Klicken Sie im klassischen Azure-Portal auf **Active Directory**, klicken Sie auf den Namen des Verzeichnisses, und klicken Sie dann auf **Domänen**.
2. In der Liste der Domänennamen auf der Seite **Domänen** klicken Sie auf die Domäne, die Sie überprüfen möchten, und klicken Sie dann auf **Überprüfen**.
2. In der Dropdownliste **Datensatztyp** auf der Seite **Überprüfen** wählen Sie entweder **TXT-Eintrag** oder **MX-Eintrag**.
3. Kopieren oder notieren Sie die darunter angegebenen Informationen.

#### Hinzufügen eines DNS-Eintrags bei Ihrer Domänennamen-Registrierungsstelle

Azure AD verwendet einen DNS-Eintrag, den Sie bei Ihrer Domänennamen-Registrierungsstelle erstellen, um zu bestätigen, dass Sie die Domäne besitzen. Folgen Sie den Anweisungen unten, um entweder einen TXT- oder einen MX-Datensatztyp für eine Domäne zu erstellen, die bei Ihrer Registrierungsstelle registriert ist.

Wenn Ihre Domänenregistrierungsstelle kein '@' als Hostnamen akzeptiert, wenden Sie sich an die Stelle, um herauszufinden, wie das 'übergeordnete Element der aktuellen Zone' dargestellt werden soll.

Office 365 hat [spezifische Anweisungen für häufige Domänenregistrierungsstellen](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/).

Für allgemeine Anweisungen gehen Sie folgendermaßen vor, um einen TXT- oder MX-Datensatz hinzuzufügen:

1. Melden Sie sich bei der Website Ihrer Domänennamen-Registrierungsstelle an, und wählen Sie dann die Domäne, die Sie überprüfen möchten.
2. Im DNS-Verwaltungsbereich für Ihr Konto wählen Sie die Option zum Hinzufügen eines TXT- oder eines MX-Eintrags für Ihre Domäne.
3. Im Feld **TXT** oder **MX** für die Domäne geben Sie Folgendes ein: @
4. Im Feld **Vollständig qualifizierter Domänenname (FQDN)** oder **Verweist auf** geben oder fügen Sie die **Ziel- oder Verweisadresse** ein, die Sie im vorherigen Schritt notiert haben.
5. Für einen TXT-Eintrag werden **TTL**-Informationen abgefragt. Geben Sie **1** ein, um die Gültigkeitsdauer auf eine Stunde festzulegen.

    Für einen MX-Eintrag wird die Priorität (oder Einstellung) abgefragt. Geben Sie eine Zahl ein, die größer ist als die Zahl, die Sie für vorhandene MX-Einträge angegeben haben. Damit können Sie verhindern, dass der neue MX-Eintrag das E-Mail-Routing für die Domäne beeinträchtigt. Anstelle einer Priorität werden möglicherweise die folgenden Optionen angezeigt: **Niedrig**, **Mittel**, **Hoch**. In diesem Fall wählen Sie **Niedrig**.

6. Speichern Sie Ihre Änderungen, und melden Sie sich dann von der Website Ihrer Domänennamen-Registrierungsstelle ab.

Nachdem Sie entweder den TXT- oder den MX-Eintrag erstellt und sich von der Website abgemeldet haben, kehren Sie für die Überprüfung der Domain zum Clouddienst zurück. In der Regel dauert es ca. 15 Minuten, damit die Änderungen wirksam werden. Es kann jedoch bis zu 72 Stunden dauern, bis der erstellte Eintrag über das DNS-System weitergegeben wird.

#### Überprüfen der Domäne

Nachdem der für Ihre Domäne erstellte Eintrag erfolgreich über das DNS-System weitergegeben wurde, führen Sie Folgendes zum Beenden der Überprüfung Ihrer Domäne mit Azure AD aus.

1. Klicken Sie im klassischen Azure-Portal in Azure Active Directory auf **Domänen**.
2. Suchen Sie in der Liste **Domänen** die Domäne, die Sie überprüfen möchten, und klicken Sie dann je nach verwendetem Portal entweder auf **Klicken Sie zum Überprüfen der Domäne** oder auf **Überprüfen**.
3. Befolgen Sie die Anweisungen zum Abschließen des Überprüfungsprozesses.
    - Bei erfolgreicher Domänenüberprüfung werden Sie benachrichtigt, dass Ihre Domäne zu Ihrem Konto hinzugefügt wurde.
    - Wenn die Domänenüberprüfung fehlschlägt, benötigen die bei der Domänenregistrierungsstelle vorgenommenen Änderungen unter Umständen mehr Zeit für die Verteilung. Brechen Sie die Überprüfung ab und versuchen Sie es zu einem späteren Zeitpunkt erneut.

Wenn seit den Änderungen an Ihrer Domäne mehr als 72 Stunden vergangen sind, melden Sie sich bei der Website der Domänenregistrierungsstelle an, und überprüfen Sie, ob Sie die Aliasinformationen korrekt eingegeben haben. Wenn die Angaben falsch eingegeben wurden, müssen Sie den fehlerhaften DNS-Eintrag entfernen und einen neuen mit den richtigen Informationen erstellen, indem Sie wie in diesem Thema beschrieben vorgehen.

Nachdem Sie Ihre Domäne überprüft haben, können Sie sie so konfigurieren, dass sie mit Ihren Konten funktioniert.

## Wie kann ich den primären Domänennamen für Benutzer ändern?

Nachdem Sie Ihren Domänennamen in Azure AD hinzugefügt haben, können Sie den Domänennamen ändern, der standardmäßig bei der Erstellung eines neuen Benutzerkontos angezeigt wird. Gehen Sie dazu folgendermaßen vor:

1. Klicken Sie im klassischen Azure-Portal in der oberen linken Ecke auf den Namen Ihrer Organisation.
2. Klicken Sie auf **Bearbeiten**.
3. Wählen Sie einen neuen Standard-Domänennamen, wie z. B. den benutzerdefinierten Domänennamen, den Sie hinzugefügt haben.

## Wie kann ich eine Domäne entfernen?

Bevor Sie einen Domänennamen entfernen, wird empfohlen, dass Sie die folgende Informationen lesen:

- Der ursprüngliche Domänenname contoso.onmicrosoft.com, der bei der Registrierung für Ihr Verzeichnis bereitgestellt wurde, kann nicht entfernt werden.
- Jede Domäne der obersten Ebene, denen Unterdomänen zugeordnet sind, werden nicht entfernt, bis die Unterdomänen entfernt wurden. Beispielsweise können Sie adatum.com nicht entfernen, wenn Sie corp.adatum.com oder eine andere Unterdomäne vorliegen haben, die den Namen der Domäne der obersten Ebene verwendet. Weitere Informationen finden Sie in diesem [Support-Artikel](https://support.microsoft.com/kb/2787792/).
- Haben Sie die Verzeichnissynchronisierung aktiviert? Falls ja, wurde eine Domäne automatisch zu Ihrem Konto hinzugefügt. Sie sieht in etwa wie folgt aus: contoso.mail.onmicrosoft.com. Dieser Domänenname kann nicht entfernt werden.
- Bevor Sie einen Domänennamen entfernen können, müssen Sie zuerst den Domänennamen aus allen Benutzer- oder E-Mail-Konten entfernen, die mit der Domäne verbunden sind. Sie können alle Konten entfernen, oder Sie können mehrere Benutzerkonten gleichzeitig bearbeiten, um deren Domänenameninformationen und E-Mail-Adressen zu ändern. Weitere Informationen finden Sie unter [Erstellen oder Bearbeiten von Benutzern in Azure AD](active-directory-create-users.md).
- Wenn Sie eine SharePoint Online-Website auf einen Domänennamen hosten, der für eine SharePoint Online-Websitesammlung verwendet wird, müssen Sie die Websitesammlung löschen, bevor Sie den Domänennamen entfernen können.

So entfernen Sie einen Domänennamen:

1. Klicken Sie im klassischen Azure-Portal in Azure AD im linken Bereich auf **Domänen**.
2. Wählen Sie auf der Seite **Domänen** den Domänennamen, den Sie entfernen möchten, und klicken Sie dann auf **Domäne entfernen**.
3. Auf der Seite **Domäne entfernen** klicken Sie auf **Ja**.

Wenn Sie Ihren Domänennamen zu diesem Zeitpunkt nicht entfernen können, wird der Status für den Domänennamen als Ausstehende Entfernung auf der Seite Domänen angezeigt. Wenn dieser Status weiterhin angezeigt wird, versuchen Sie erneut, den Domänennamen zu entfernen.

## Behandlung von Problemen nach Änderung des Domänennamens

### Ich habe Änderungen an meiner Domäne vorgenommen, doch sie werden noch nicht angezeigt.

Aufgrund der Art und Weise, wie Aktualisierungen durch das Domain Name System (DNS) verschoben werden, dauert es bis zu 72 Stunden, bevor die vorgenommenen Änderungen bei einer Domänenregistrierungsstelle oder einem Hostinganbieter vollständig über das Internet übertragen sind und Sie Ihre Dienste mit dem Domänennamen verwenden können.

Darüber hinaus müssen die Änderungen, die Sie bei der Domänenregistrierungsstelle vornehmen, vollständig korrekt sein. Wenn Sie zur Behebung eines Fehlers zurückgehen, dauert es möglicherweise mehrere Tage, bist die aktualisierte Einstellung auf der Portalwebsite des Clouddienstes angezeigt wird.

Wie lange dauert das? Dies hängt zum Teil von der Gültigkeitsdauer-(TTL-)Einstellung ab, die Sie für den DNS-Eintrag angegeben haben, den Sie ersetzen oder aktualisieren. Bis die Gültigkeitsdauer (TTL) abgelaufen ist, werden Internetserver, die die vorherigen Daten im Cache gespeichert haben, den autorisierenden Nameserver nicht zum Anfordern des neuen Werts abfragen.

### Ich habe eine Domäne hinzugefügt, überprüft und die DNS-Einträge auf der Website der Registrierungsstelle konfiguriert. Warum erhalten neue E-Mail-Konten noch keine Nachrichten?

Wenn Sie das Hinzufügen oder Aktualisieren von DNS-Einträgen für Ihre Domäne abgeschlossen haben, dauert es bis zu 72 Stunden, bis die Änderungen wirksam werden.

Darüber hinaus müssen die Einstellungsinformationen auf der Website der Registrierungsstelle genau stimmen. Überprüfen Sie Ihre Einstellungen erneut, und stellen Sie sicher, dass Sie genügend Zeit für die Weitergabe der geänderten DNS-Datensätze durch das System zugelassen haben.

### Ich kann meinen Domänennamen nicht überprüfen. Wie kann ich herausfinden, was falsch ist?

Eine Möglichkeit zum Identifizieren von Problemen ist die Verwendung des Problembehandlungs-Assistenten für Domänen. Gehen Sie folgendermaßen vor, um den Assistenten zu starten: Klicken Sie im klassischen Azure-Portal auf der Administratorenseite auf **Domänen**, und doppelklicken Sie dann auf den Domänennamen, den Sie überprüfen möchten. Klicken Sie unter **Problembehandlung** auf **Domänenproblembehandlung**.

Der Assistent zur Problembehandlung fragt Sie ab, an welcher Stelle des Überprüfungsprozesses Sie sich befinden und gibt Ihnen dann Informationen, die Ihnen beim Abschließen der Überprüfung helfen.

### Ich habe meine Domäne hinzugefügt und überprüft, aber der neue Domänenname funktioniert nicht für vorhandene E-Mail-Adressen von Benutzern.

Wenn Sie Ihren benutzerdefinierten Domänennamen zum Clouddienst hinzufügen, nachdem Sie bereits Benutzerkonten hinzugefügt haben, müssen Sie möglicherweise Updates für den neuen Domänennamen durchführen. Beispielsweise müssen Sie die Konten Ihrer Benutzer so bearbeiten, dass ihre E-Mail-Adressen auf Ihre benutzerdefinierte Domäne festgelegt sind.

## Nächste Schritte

- [Azure AD-Forum](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD)
- [Stackoverflow](http://stackoverflow.com/questions/tagged/azure)
- [Als Organisation für Azure registrieren](sign-up-organization.md)
- [Verwalten von Domänen in Azure AD](https://msdn.microsoft.com/library/azure/dn919677.aspx)

<!---HONumber=AcomDC_1203_2015-->