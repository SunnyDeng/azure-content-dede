<properties
	pageTitle="Azure AD Connect Health-Vorgänge"
	description="In diesem Artikel werden zusätzliche Vorgänge beschrieben, die nach der Bereitstellung von Azure AD Connect Health ausgeführt werden können."
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
	ms.date="08/14/2015"
	ms.author="billmath"/>

# Azure AD Connect Health-Vorgänge

Das folgende Thema beschreibt die verschiedenen Vorgänge, die mithilfe von Azure AD Connect Health ausgeführt werden können.

## Aktivieren von E-Mail-Benachrichtigungen
Sie können Azure AD Connect Health zum Senden von E-Mail-Benachrichtigungen konfigurieren, die beim Generieren von Warnungen gesendet werden und angeben, dass die Identitätsinfrastruktur nicht fehlerfrei ist. Benachrichtigungen werden gesendet, wenn eine Warnung generiert und wenn sie als gelöst markiert wird. Folgen Sie den unten stehenden Anweisungen, um E-Mail-Benachrichtigungen zu konfigurieren. Beachten Sie, dass E-Mail-Benachrichtigungen standardmäßig deaktiviert sind.


### So aktivieren Sie Azure AD Connect Health-E-Mail-Benachrichtigungen

1. Öffnen Sie das Blatt "Warnungen" für den Dienst, für den Sie E-Mail-Benachrichtigungen empfangen möchten.
2. Klicken Sie in der Aktionsleiste auf die Schaltfläche "Benachrichtigungseinstellungen".
3. Aktivieren Sie die Option "E-Mail-Benachrichtigung".
4. Aktivieren Sie das Kontrollkästchen, um alle globalen Administratoren zum Empfangen von E-Mail-Benachrichtigungen zu konfigurieren.
5. Wenn Sie E-Mail-Benachrichtigungen an andere E-Mail-Adressen senden lassen möchten, können Sie diese im Feld "Weitere E-Mail-Empfänger" angeben. Um eine E-Mail-Adresse aus dieser Liste zu entfernen, klicken Sie mit der rechten Maustaste auf den Eintrag, und wählen Sie "Löschen".
6. Klicken Sie zum Abschließen der Änderungen auf "Speichern". Alle Änderungen werden erst nach der Auswahl von "Speichern" wirksam.






## Löschen eines Servers aus einem Azure AD Connect Health-Dienst

In einigen Fällen möchten Sie möglicherweise einen Server aus der Überwachung entfernen. Folgen Sie den unten stehenden Anweisungen, um einen Server aus dem Azure AD Connect Health-Dienst zu entfernen.

Beim Löschen eines Servers sind folgende Punkte zu beachten:

- Diese Aktion BEENDET das Erfassen von jeglichen Daten von diesem Server. Dieser Server wird aus dem Überwachungsdienst entfernt. Nach dieser Aktion können Sie keine neuen Warnungen oder Überwachungs- bzw. Nutzungsanalysedaten für diesen Server anzeigen.
- Diese Aktion deinstalliert oder entfernt den Connect Health-Agent NICHT vom Server. Wenn Sie vor Ausführung dieses Schritts den Connect Health-Agent nicht deinstalliert haben, werden auf dem Server möglicherweise Fehlerereignisse in Zusammenhang mit dem Connect Health-Agent angezeigt.
- Diese Aktion löscht NICHT die Daten, die von diesem Server bereits erfasst wurden. Diese Daten werden gemäß der Microsoft Azure-Datenaufbewahrungsrichtlinie gelöscht.
- Wenn Sie nach Ausführung dieser Aktion die Überwachung des gleichen Servers erneut starten möchten, müssen Sie den Connect Health-Agent von diesem Server deinstallieren und anschließend neu installieren.


### So löschen Sie einen Server aus dem Azure AD Connect Health-Dienst

1. Öffnen Sie das Blatt "Server", indem Sie auf dem Blatt "Serverliste" den Namen des Servers auswählen, den Sie entfernen möchten.
2. Klicken Sie auf der Aktionsleiste des Blatts "Server" auf die Schaltfläche "Löschen".
3. Bestätigen Sie die Aktion zum Löschen des Servers, indem Sie den Namen des Servers in das Bestätigungsfeld eingeben.
4. Klicken Sie auf die Schaltfläche "Löschen".







## Löschen einer Dienstinstanz aus dem Azure AD Connect Health-Dienst

In einigen Fällen möchten Sie möglicherweise eine Dienstinstanz entfernen. Folgen Sie den unten stehenden Anweisungen, um eine Dienstinstanz aus dem Azure AD Connect Health-Dienst zu entfernen.

Beim Löschen einer Dienstinstanz sind folgende Punkte zu beachten:

- Diese Aktion entfernt die aktuelle Dienstinstanz aus dem Überwachungsdienst.
- Diese Aktion deinstalliert oder entfernt den Connect Health-Agent NICHT von einem der Server, die im Rahmen dieser Dienstinstanz überwacht wurden. Wenn Sie vor Ausführung dieses Schritts den Connect Health-Agent nicht deinstalliert haben, werden auf dem Server bzw. den Servern möglicherweise Fehlerereignisse in Zusammenhang mit dem Connect Health-Agent angezeigt.
- Alle Daten aus dieser Dienstinstanz werden gemäß der Microsoft Azure-Datenaufbewahrungsrichtlinie gelöscht.
- Wenn Sie nach Ausführung dieser Aktion die Überwachung dieses Diensts erneut starten möchten, deinstallieren Sie den Connect Health-Agent von allen zu überwachenden Servern, und installieren Sie ihn anschließend erneut. Wenn Sie nach Ausführung dieser Aktion die Überwachung des gleichen Servers erneut starten möchten, müssen Sie den Connect Health-Agent von diesem Server deinstallieren und anschließend neu installieren.


### So löschen Sie eine Dienstinstanz aus dem Azure AD Connect Health-Dienst

1. Öffnen Sie das Blatt "Dienst", indem Sie auf dem Blatt "Dienstliste" die ID des Diensts (Name der Farm) auswählen, den Sie entfernen möchten.
2. Klicken Sie auf der Aktionsleiste des Blatts "Server" auf die Schaltfläche "Löschen".
3. Bestätigen Sie den Dienstnamen, indem Sie ihn in das Bestätigungsfeld eingeben (Beispiel: sts.contoso.com).
4. Klicken Sie auf die Schaltfläche "Löschen".

## Aktivieren der Überwachung für AD FS

Um mithilfe der Nutzungsanalyse Daten zu sammeln und zu analysieren, benötigt der Azure AD Connect Health-Agent die in den AD FS-Überwachungsprotokollen enthaltenen Informationen. Diese Protokolle sind nicht standardmäßig aktiviert. Dies gilt nur für AD FS-Verbundserver. Das Aktivieren der Überwachung ist auf AD FS-Proxyservern oder Webanwendungsproxyservern nicht erforderlich. Führen Sie die nachstehenden Anweisungen aus, um die AD FS-Überwachung zu aktivieren und die AD FS-Überwachungsprotokolle zu ermitteln.

#### So aktivieren Sie die Überwachung für AD FS 2.0

1. Klicken Sie auf **Start**, zeigen Sie auf **Programme** und dann auf **Verwaltung**, und klicken Sie anschließend auf **Lokale Sicherheitsrichtlinie**.
2. Navigieren Sie zum Ordner **Sicherheitseinstellungen\\Lokale Richtlinien\\Zuweisen von Benutzerrechten**, und doppelklicken Sie dann auf "Generieren von Sicherheitsüberwachungen".
3. Stellen Sie auf der Registerkarte **Lokale Sicherheitseinstellung** sicher, dass das AD FS 2.0-Dienstkonto aufgeführt wird. Wenn es nicht vorhanden ist, klicken Sie auf **Benutzer oder Gruppe hinzufügen** und fügen es der Liste hinzu. Klicken Sie dann auf **OK**.
4. Öffnen Sie ein Eingabeaufforderungsfenster mit erhöhten Rechten, und führen Sie den folgenden Befehl zum Aktivieren der Überwachung aus: <code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable</code>.
5. Schließen Sie "Lokale Sicherheitsrichtlinie", und öffnen Sie dann das Verwaltungs-Snap-In. Klicken Sie zum Öffnen des Verwaltungs-Snap-Ins auf **Start**, zeigen Sie auf **Programme** und dann auf **Verwaltung**, und klicken Sie anschließend auf "AD FS 2.0-Verwaltung".
6. Klicken Sie im Bereich "Aktionen" auf "Verbunddiensteigenschaften bearbeiten".
7. Klicken Sie im Dialogfeld **Verbunddiensteigenschaften** auf die Registerkarte **Ereignisse**.
8. Aktivieren Sie die Kontrollkästchen **Erfolgsüberwachungen** und **Fehlerüberwachungen**.
9. Klicken Sie auf **OK**.

#### So aktivieren Sie die Überwachung für AD FS unter Windows Server 2012 R2

1. Öffnen Sie **Lokale Sicherheitsrichtlinie**, indem Sie den **Server-Manager** auf dem Startbildschirm oder über die Taskleiste auf dem Desktop öffnen, und klicken Sie dann auf **Tools/Lokale Sicherheitsrichtlinie**.
2. Navigieren Sie zum Ordner **Sicherheitseinstellungen\\Lokale Richtlinien\\Zuweisen von Benutzerrechten**, und doppelklicken Sie dann auf **Generieren von Sicherheitsüberwachungen**.
3. Stellen Sie auf der Registerkarte **Lokale Sicherheitseinstellung** sicher, dass das AD FS-Dienstkonto aufgeführt wird. Wenn es nicht vorhanden ist, klicken Sie auf **Benutzer oder Gruppe hinzufügen** und fügen es der Liste hinzu. Klicken Sie dann auf **OK**.
4. Öffnen Sie ein Eingabeaufforderungsfenster mit erhöhten Rechten, und führen Sie den folgenden Befehl zum Aktivieren der Überwachung aus: <code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable</code>.
5. Schließen Sie **Lokale Sicherheitsrichtlinie**, und öffnen Sie dann das **AD FS-Verwaltungs**-Snap-In (klicken Sie hierzu im Server-Manager auf "Tools", und wählen Sie dann "AD FS-Verwaltung" aus).
6. Klicken Sie im Bereich "Aktionen" auf **Verbunddiensteigenschaften bearbeiten**.
7. Klicken Sie im Dialogfeld "Verbunddiensteigenschaften" auf die Registerkarte **Ereignisse**.
8. Aktivieren Sie die Kontrollkästchen **Erfolgsüberwachungen und Fehlerüberwachungen**. Klicken Sie anschließend auf **OK**.






#### So ermitteln Sie die AD FS-Überwachungsprotokolle


1. Öffnen Sie die **Ereignisanzeige**.
2. Wechseln Sie zu "Windows-Protokolle", und wählen Sie **Sicherheit** aus.
3. Klicken Sie auf der rechten Seite auf **Aktuelle Protokolle filtern**.
4. Wählen Sie unter "Ereignisquelle" die **AD FS-Überwachung** aus.

![AD FS-Überwachungsprotokolle](./media/active-directory-aadconnect-health-requirements/adfsaudit.png)

> [AZURE.WARNING]Wenn eine Gruppenrichtlinie implementiert ist, die die AD FS-Überwachung deaktiviert, kann der Azure AD Connect Health-Agent keine Informationen sammeln. Stellen Sie sicher, dass die Überwachung nicht durch eine Gruppenrichtlinie deaktiviert wird.


## Verwandte Links

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Azure AD Connect Health Agent-Installation für AD FS](active-directory-aadconnect-health-agent-install-adfs.md)
* [Verwenden von Azure AD Connect Health mit AD FS](active-directory-aadconnect-health-adfs.md)
* [Azure AD Connect Health – FAQ](active-directory-aadconnect-health-faq.md)

<!---HONumber=Oct15_HO2-->