<properties
   pageTitle="Was ist Microsoft Azure Active Directory-Lizenzierung? | Microsoft Azure"
   description="Beschreibung der Microsoft Azure AD-Lizenzierung, der Funktionsweise, der ersten Schritte und der bewährten Methoden, einschließlich Office 365, Microsoft Intune und Azure Active Directory Premium und Basic"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="07/16/2015"
   ms.author="curtand"/>

# Was ist Microsoft Azure Active Directory-Lizenzierung?

##Beschreibung
Azure Active Directory (AD) ist die IDaaS-Lösung (Identity as a Service) und -Plattform von Microsoft. Azure AD wird in einer Reihe von funktionalen und technischen Versionen angeboten. Diese reichen von Azure AD Free, die mit anderen Microsoft-Diensten wie Office 365, Dynamics, Microsoft Intune und Azure (Azure AD generiert keine Nutzungsgebühren in diesem Modus) verfügbar ist, bis hin zu kostenpflichtigen Versionen von Azure AD wie Enterprise Mobility Suite (EMS), Azure AD Premium und Basic sowie Azure Multi-Factor Authentication (MFA). Wie bei vielen der Microsoft-Onlinedienste werden die meisten kostenpflichtigen Versionen von Azure AD über Berechtigungen pro Benutzer bereitgestellt, wie auch in Office 365, Microsoft Intune und Azure AD. In diesen Fällen entspricht der Erwerb des Diensts einem oder mehreren Abonnements, und jedes Abonnement umfasst die Vorausbezahlung einer Anzahl von Lizenzen in Ihrem Mandanten. Berechtigungen pro Benutzer werden durch Lizenzzuweisung erreicht und erstellen eine Verknüpfung zwischen dem Benutzer und dem Produkt, sodass die Komponenten des Diensts für den Benutzer aktiviert werden und eine der vorausbezahlten Lizenzen nutzt wird.

Das Azure AD-Verwaltungsportal ist Teil des Azure-Verwaltungsportals. Zwar ist für die Verwendung von Azure AD der Erwerb von Azure nicht erforderlich, für den Zugriff auf dieses Portal wird aber ein aktives Azure-Abonnement oder ein [Azure-Testabonnement](http://azure.microsoft.com/pricing/free-trial/) benötigt.

Einen umfassenden Überblick über die Azure AD-Dienstfunktionen finden Sie unter [Was ist Azure AD?](active-directory-whatis.md).

> [AZURE.NOTE]Nutzungsbasierte Abonnements von Azure sind anders: Sie werden zwar auch in Ihrem Verzeichnis dargestellt, diese Abonnements ermöglichen jedoch das Erstellen von Azure-Ressourcen und deren Abstimmung auf Ihre Zahlungsweise. In diesem Fall ist keine Lizenzanzahl mit dem Abonnement verknüpft. Die Zuordnung von Benutzern zum Abonnement und der Benutzerzugriff zum Verwalten von Abonnementressourcen erfolgt durch die Erteilung von Berechtigungen zum Verarbeiten von Azure-Ressourcen, die dem Abonnement zugeordnet sind.

> [Erfahren Sie mehr über Azure AD-Servicelevel](http://azure.microsoft.com/support/legal/sla/)

##Wie funktioniert die Lizenzierung von Azure AD?

Berechtigungs- oder lizenzbasierte Azure AD-Dienste funktionieren durch die Aktivierung eines Abonnements im Azure AD-Verzeichnis/-Dienstmandanten. Sobald das Abonnement aktiv ist, können Dienstfunktionen von Verzeichnis-/Dienstadministratoren verwaltet und von lizenzierten Benutzern verwendet werden.

Wenn Sie Enterprise Mobility Suite, Azure AD Premium oder Azure AD Basic erwerben oder aktivieren, wird das Verzeichnis mit dem Abonnement aktualisiert, einschließlich der Gültigkeitsdauer und der vorausbezahlten Lizenzen. Ihre Abonnementinformationen, einschließlich Status, nächstes Lebenszyklusereignis und Anzahl der zugewiesenen oder verfügbaren Lizenzen, stehen über das Azure AD-Verwaltungsportal auf der Registerkarte "Lizenzen" für das jeweilige Verzeichnis zur Verfügung. Dies ist auch der beste Ort für die Verwaltung Ihrer Lizenzzuweisungen.

Jedes Abonnement umfasst einen oder mehrere Dienstpläne, die jeweils die enthaltene Funktionsebene des Diensttyps darstellen, beispielsweise Azure AD, Azure MFA, Microsoft Intune, Exchange Online und SharePoint Online. Für die Azure AD-Lizenzverwaltung ist keine Verwaltung auf Dienstplanebene erforderlich. Dies ist ein Unterschied zu Office 365, für das dieser erweiterte Konfigurationsmodus zum Verwalten des Zugriffs auf die enthaltenen Dienste benötigt wird. Bei Azure AD erfolgt die Konfiguration im Dienst, um Funktionen zu aktivieren und einzelne Berechtigungen zu verwalten.

Im Allgemeinen werden Azure AD-Abonnementinformationen über das Azure-Verwaltungsportal auf der Registerkarte "Lizenzen" für das jeweilige Verzeichnis verwaltet. Azure AD-Abonnements, mit Ausnahme von Azure AD Premium, werden nicht im Office-Portal angezeigt.

> [AZURE.IMPORTANT]Abonnements von Azure AD Premium und Basic sowie Enterprise Mobility Suite sind auf ihre bereitgestellten Verzeichnisse/Mandanten beschränkt. Abonnements können nicht zwischen Verzeichnissen aufgeteilt oder verwendet werden, um Benutzern Berechtigungen für andere Verzeichnissen zu erteilen. Das Verschieben eines Abonnements zwischen Verzeichnissen ist möglich, erfordert jedoch das Einreichen eines Supporttickets oder im Fall von direkten Käufen die Kündigung und den erneuten Erwerb.

Kostenpflichtige Azure AD-Funktionen decken das gesamte Verzeichnis ab. Beispiele: – Gruppenbasierte Zuweisung zu Anwendungen, die für die jeweilige Anwendung aktiviert wird, die Sie verwalten. – Erweiterte und Self-Service-Gruppenverwaltungsfunktionen stehen in der Verzeichniskonfiguration oder in den jeweiligen Gruppen zur Verfügung. – Premium-Sicherheitsberichte finden Sie auf der Registerkarte "Berichterstellung". – Die Ermittlung von Cloudanwendungen befindet sich im Azure-Vorschauportal unter "Identität".

###Zuweisen von Lizenzen
Zwar ist nur der Erwerb eines Abonnements erforderlich, um kostenpflichtige Funktionen zu konfigurieren, die Verwendung Ihrer kostenpflichtigen Azure AD-Funktionen erfordert jedoch die Verteilung von Lizenzen an die richtigen Personen. Im Allgemeinen muss allen Benutzern, die Zugriff haben sollen oder die über kostenpflichtige Azure AD-Funktionen verwaltet werden, eine Lizenz zugewiesen werden. Die Lizenzzuweisung ist eine Zuordnung zwischen einem Benutzer und einem erworbenen Dienst wie Azure AD Premium, Basic oder Enterprise Mobility Suite.

Es ist einfach zu verwalten, welche Benutzer im Verzeichnis eine Lizenz erhalten sollen. Dies kann durch die Zuweisung zu einer Gruppe erreicht werden, um Zuweisungsregeln über das Azure AD-Verwaltungsportal zu erstellen, oder durch die direkte Zuweisung von Lizenzen an die richtigen Personen über das Portal, PowerShell oder APIs. Wenn einer Gruppe Lizenzen zugewiesen werden, wird allen Mitgliedern der Gruppe eine Lizenz zugewiesen. Wenn Benutzer der Gruppe hinzugefügt oder aus der Gruppe entfernt werden, werden sie der entsprechenden Lizenz zugewiesen oder davon entfernt. Für die Gruppenzuweisung kann jede verfügbare Gruppenverwaltung genutzt werden, die Ihnen zur Verfügung steht und die mit der gruppenbasierten Zuweisung zu Anwendungen konsistent ist. Durch diesen Ansatz können Sie Regeln so einrichten, dass alle Benutzer in Ihrem Verzeichnis automatisch zugewiesen werden, dass sichergestellt ist, dass jeder in der entsprechenden Position über eine Lizenz verfügt, oder dass die Entscheidung an andere Manager in der Organisation delegiert wird.

Mit der gruppenbasierten Lizenzzuweisung erben Benutzer, denen ein Verwendungsspeicherort fehlt, den Verzeichnisspeicherort während der Zuweisung. Dieser Speicherort kann jederzeit vom Administrator geändert werden. In Fällen, in denen die automatische Zuweisung aufgrund von Fehlern fehlschlägt, spiegeln die Benutzerinformationen unter diesem Lizenztyp den Status wider.

##Erste Schritte mit der Azure AD-Lizenzierung

Die ersten Schritte mit Azure AD sind einfach. Sie können das Verzeichnis im Rahmen der Anmeldung bei einer kostenlosen Testversion von Azure erstellen. [Erfahren Sie mehr über die Anmeldung als Organisation](sign-up-organization.md). Mit Folgendem können Sie sicherstellen, dass das Verzeichnis optimal auf andere Microsoft-Dienste, die Sie möglicherweise nutzen oder zu nutzen planen, und auf Ihre Ziele beim Erwerb des Diensts abgestimmt ist.

Dies sind einige bewährte Methoden: – Wenn Sie bereits Organisationsdienste von Microsoft verwenden, verfügen Sie schon über ein Azure AD-Verzeichnis. In diesem Fall sollten Sie dasselbe Verzeichnis für andere Dienste verwenden, damit die zentrale Identitätsverwaltung, einschließlich Bereitstellung und Hybrid-SSO, von den Diensten genutzt werden kann. Ihre Benutzer müssen sich nur einmal anmelden und können umfangreichere Funktionen der Dienste nutzen. Wenn Sie einen kostenpflichtigen Azure AD-Dienst für Ihre Mitarbeiter erwerben möchten, empfehlen wir, dass Sie dafür dasselbe Verzeichnis verwenden. – Wenn Sie beabsichtigen, Azure AD für andere Benutzer (Partner, Kunden usw.) zu verwenden oder wenn Sie Azure AD-Dienste auswerten möchten und dies isoliert vom Produktionsdienst erledigen möchten oder wenn Sie eine Sandkastenumgebung für Ihre Dienste einrichten möchten, empfehlen wir, dass Sie zuerst ein neues Verzeichnis über das Azure-Verwaltungsportal erstellen. [Erfahren Sie mehr über das Erstellen eines neuen Azure AD-Verzeichnisses im Azure-Verwaltungsportal](active-directory-licensing-directory-independence.md). Das neue Verzeichnis wird mit Ihrem Konto als externer Benutzer mit globalen Administratorberechtigungen erstellt. Wenn Sie sich beim Azure-Verwaltungsportal mit diesem Konto anmelden, wird dieses Verzeichnis angezeigt, und Sie haben Zugriff auf alle Verwaltungsaufgaben für das Verzeichnis. Es wird empfohlen, dass Sie ein lokales Konto mit entsprechenden Berechtigungen zum Verwalten von anderen Microsoft-Diensten (auf die nicht über das Azure-Verwaltungsportal zugegriffen werden kann) erstellen. [Erfahren Sie mehr über das Erstellen von Benutzerkonten in Azure AD](active-directory-create-users.md).

> [AZURE.NOTE]Azure AD unterstützt "externe Benutzer". Dies sind Benutzerkonten in einer Instanz von Azure AD, die mit einem Microsoft-Konto (MSA) oder einer Azure AD-Identität aus einem anderen Verzeichnis erstellt wurden. Während diese Funktion in alle Organisationsdienste von Microsoft implementiert werden soll, werden diese Konten noch nicht in allen Diensten unterstützt. Beispielsweise unterstützt das Office 365-Verwaltungsportal derzeit diese Benutzer nicht. Daher können externe Benutzer mit Microsoft-Konten nicht auf das Office 365-Verwaltungsportal zugreifen, während externe Benutzer aus anderen Azure AD-Verzeichnissen ignoriert werden. Im letzteren Fall kann nur auf das lokale Konto des Benutzers, das Azure AD- oder Office 365-Verzeichnis, in dem der Benutzer ursprünglich erstellt wurde, über diese Dienste zugegriffen werden.

Wie erwähnt, gibt es für Azure AD verschiedene kostenpflichtige Versionen. Diese Versionen weisen geringfügige Unterschiede in ihrer Kaufverfügbarkeit auf:


| Produkt | EA/VL | Öffnen | 	CSP | 	Nutzungsrechte für MPN | 	Direktkauf | Testversion |
|---|---|---|---|---|---|---|
| Enterprise Mobility Suite |	X |	X |	X |	X | |	 	X |
| Azure AD Premium | X | X | X | | X | X |
| Azure AD Basic | X | X | X | X | <br /> | <br /> |

###Auswählen von mindestens einer Lizenztestversion
 In allen Fällen können Sie ein Testabonnement von Azure AD Premium oder Enterprise Mobility Suite aktivieren, indem Sie die gewünschte Testversion auf der Registerkarte "Lizenzen" in Ihrem Verzeichnis auswählen. Eine Testversion enthält ein 30-Tage-Abonnement mit 100 Lizenzen.

![Azure Active Directory-Testlizenzpläne](./media/active-directory-licensing-what-is/trial_plans.png)

![Enterprise Mobility Suite-Testlizenzpläne](./media/active-directory-licensing-what-is/EMS_trial_plan.png)

![Aktive Testlizenzpläne](./media/active-directory-licensing-what-is/active_license_trials.png)

###Zuweisen von Lizenzen
Sobald die Testversion aktiv ist, sollten Sie sich selbst eine Lizenz zuweisen und den Browser aktualisieren, um sicherzustellen, dass alle Funktionen angezeigt werden. Der nächste Schritt ist, den Benutzern Lizenzen zuzuweisen, die auf kostenpflichtige Azure AD-Funktionen zugreifen oder darin enthalten sein müssen. Wie oben unter "Zuweisen von Lizenzen" erwähnt, ist die beste Möglichkeit hierzu, die Gruppe zu bestimmen, die die gewünschte Zielgruppe darstellt, und ihr die Lizenz zuzuweisen. Auf diese Weise werden Benutzer, die während ihres gesamten Lebenszyklus der Gruppe hinzugefügt oder daraus entfernt werden, der Lizenz zugewiesen oder davon entfernt.

Um eine Lizenz für eine Gruppe oder einzelne Benutzer zuzuweisen, wählen Sie den gewünschten Lizenzplan aus, und klicken Sie auf der Befehlsleiste auf **Zuweisen**.

![Aktive Testlizenzpläne](./media/active-directory-licensing-what-is/assign_licenses.png)

Im Zuweisungsdialogfeld für den ausgewählten Plan können Sie Benutzer auswählen und der Spalte **Zuweisen** auf der rechten Seite hinzufügen. Sie können durch die Benutzerliste blättern oder mit der Lupe oben rechts in der Benutzertabelle nach bestimmten Personen suchen. Wählen Sie zum Zuweisen von Gruppen im Menü **Anzeigen** die Option "Gruppen" aus, und klicken Sie dann auf das Häkchen auf der rechten Seite, um die angezeigten Zuweisungen zu aktualisieren.

![Zuweisen von Lizenzen zu Gruppen](./media/active-directory-licensing-what-is/assign_licenses_to_groups.png)

Sie können jetzt Gruppen suchen oder durchblättern und sie der Spalte **Zuweisen** auf die gleiche Weise hinzufügen. Sie können auch eine Kombination von Benutzern und Gruppen in einem einzigen Vorgang zuweisen. Klicken Sie auf das Häkchen unten rechts auf der Seite, um den Vorgang abzuschließen.

![Statusmeldung bei der Lizenzzuweisung](./media/active-directory-licensing-what-is/license_assignment_progress_message.png)

Wenn eine Gruppe zugewiesen wurde, erben ihre Mitglieder die Lizenzen innerhalb von 30 Minuten, aber in der Regel innerhalb von 1 bis 2 Minuten.

Zuweisungsfehler können im Azure AD-Portal bei der Lizenzzuweisung auftreten, sind jedoch relativ selten. Potenzielle Zuweisungsfehler sind beschränkt auf: – Zuweisungskonflikte – Wenn einem Benutzer zuvor eine Lizenz zugewiesen war, die nicht mit der aktuellen Lizenz kompatibel ist. In diesem Fall ist für das Zuweisen der neuen Lizenz das Entfernen der vorherigen erforderlich. – Überschrittene verfügbare Lizenzen – Wenn die Anzahl der Benutzer in zugewiesenen Gruppen die Anzahl der verfügbaren Lizenzen überschreitet, gibt der Zuweisungsstatus für den Benutzer einen Zuweisungsfehler aufgrund fehlender Lizenzen an.

###Anzeigen zugewiesener Lizenzen

Eine zusammenfassende Darstellung der zugewiesenen Lizenzen, einschließlich der verfügbaren und zugewiesenen Lizenzen sowie des nächsten Lebenszyklusereignisses im Abonnement, werden auf der Registerkarte **Lizenzen** angezeigt.

![Anzeigen der Anzahl der zugewiesenen Lizenzen](./media/active-directory-licensing-what-is/view_assigned_licenses.png)

Eine ausführliche Liste der zugewiesenen Benutzer und Gruppen, einschließlich des Zuweisungsstatus und -pfads (direkt oder geerbt von einer oder mehreren Gruppen) ist verfügbar, wenn Sie in einem Lizenzplan navigieren.

![Detailanzeige von zugewiesenen Lizenzen für einen Lizenzplan](./media/active-directory-licensing-what-is/assigned_licenses_detail.png)

Das Entfernen von Lizenzen ist ebenso einfach wie das Zuweisen. Bei einem direkt zugewiesenem Benutzer oder bei einer zugewiesenen Gruppe können Sie die Lizenz entfernen, indem Sie den Lizenztyp auswählen, dann **Entfernen** auswählen, den Benutzer oder die Gruppe der Liste "Entfernen" hinzufügen und dann die Aktion bestätigen. Alternativ können Sie einen Lizenztyp öffnen, den Benutzer oder die Gruppe auswählen und auf der Befehlsleiste auf **Entfernen** tippen. Um die Vererbung einer Lizenz aus einer Gruppe an einen Benutzer zu beenden, entfernen Sie den Benutzer einfach aus der Gruppe.

###Erweitern von Testversionen

Erweiterungen von Testversionen für Kunden sind als Self-Service über das Office 365-Portal verfügbar. Ein Administrator des Kunden kann zum [Office-Portal](https://portal.office.com/#Billing) navigieren (der Zugriff hängt von Berechtigungen für das Office-Portal ab) und Ihre Azure AD Premium-Testversion auswählen. Klicken Sie auf den Link **Testversion erweitern**, und folgen Sie den Anweisungen. Sie müssen eine Kreditkarte angeben, aber es wird nichts berechnet.

![Erweitern einer Lizenztestversion im Office-Portal](./media/active-directory-licensing-what-is/extend_license_trial.png)

Kunden können auch über eine Supportanforderung eine Erweiterung der Testversion anfordern. Ein Administrator des Kunden kann zur [Supportseite](http://aka.ms/extendAADtrial) im Office-Portal navigieren (der Zugriff hängt von Berechtigungen für das Office-Portal ab). Wählen Sie auf dieser Seite "Abonnements und Testversionen" unter "Features" und "Fragen zur Testversion" unter "Symptom" aus. Geben Sie abschließend Informationen über die Umstände ein.

![Erweitern einer Lizenztestversion mithilfe einer Supportanforderung](./media/active-directory-licensing-what-is/alternate_office_aad_trial_extension.png)

## Nächste Schritte

Jetzt sind Sie möglicherweise bereit, einige Funktionen von Azure AD Premium zu konfigurieren und zu verwenden.

- [Self-Service-Kennwortzurücksetzung](active-directory-manage-passwords.md)
- [Self-Service-Gruppenverwaltung](active-directory-accessmanagement-self-service-group-management.md)
- [Azure AD Connect-Zustand](active-directory-aadconnect-health.md)
- [Gruppenzuweisung zu Anwendungen](active-directory-manage-groups.md)
- [Azure Multi-Factor Authentication](multi-factor-authentication.md)
- [Direkter Erwerb von Azure AD Premium-Lizenzen](http://aka.ms/buyaadp)

<!---HONumber=July15_HO4-->