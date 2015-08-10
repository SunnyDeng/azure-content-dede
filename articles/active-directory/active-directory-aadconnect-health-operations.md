<properties 
	pageTitle="Azure AD Connect Health-Vorgänge" 
	description="Auf dieser Seite zu Azure AD Connect Health werden zusätzliche Vorgänge beschrieben, die nach der Bereitstellung von Azure AD Connect Health ausgeführt werden können.." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/12/2015"
	ms.author="billmath"/>

# Azure AD Connect Health-Vorgänge

Das folgende Thema beschreibt die verschiedenen Vorgänge, die mithilfe von Azure AD Connect Health ausgeführt werden können.

## E-Mail-Benachrichtigungen
Sie können den Azure AD Connect Health-Dienst zum Senden von E-Mail-Benachrichtigungen konfigurieren, wenn Warnungen generiert werden, dass die Verbunddienstinfrastruktur nicht fehlerfrei ist. Benachrichtigungen werden gesendet, wenn eine Warnung generiert und wenn sie als gelöst markiert wird. Folgen Sie den unten stehenden Anweisungen, um E-Mail-Benachrichtigungen zu konfigurieren. Beachten Sie, dass E-Mail-Benachrichtigungen standardmäßig deaktiviert sind.


### So aktivieren Sie Azure AD Connect Health-E-Mail-Benachrichtigungen

1. Öffnen Sie das Blatt "Warnungen" für die Farm, für die Sie E-Mail-Benachrichtigungen senden möchten.
1. Klicken Sie in der Aktionsleiste auf die Schaltfläche "Benachrichtigungseinstellungen".
1. Aktivieren Sie die Option "E-Mail-Benachrichtigung".
1. Aktivieren Sie das Kontrollkästchen, um alle globalen Mandantenadministratoren zum Empfangen von E-Mail-Benachrichtigungen zu konfigurieren.
1. Wenn Sie E-Mail-Benachrichtigungen an andere E-Mail-Adressen senden lassen möchten, können Sie diese in den Feldern "Weitere E-Mail-Empfänger" angeben. Um eine E-Mail-Adresse aus dieser Liste zu entfernen, klicken Sie mit der rechten Maustaste auf den Eintrag, und wählen Sie "Löschen".
1. Klicken Sie zum Abschließen der Änderungen auf "Speichern". Alle Änderungen werden erst nach der Auswahl von "Speichern" wirksam.






## Löschen eines Servers aus einem Azure AD Connect Health-Dienst

In einigen Fällen möchten Sie möglicherweise einen Server aus der Überwachung entfernen. Folgen Sie den unten stehenden Anweisungen, um einen Server aus dem Azure AD Connect Health-Dienst zu entfernen.

Beim Löschen eines Servers sind folgende Punkte zu beachten:

- Diese Aktion BEENDET das Erfassen von jeglichen Daten von diesem Server. Dieser Server wird aus dem Überwachungsdienst entfernt. Nach dieser Aktion können Sie keine neuen Warnungen oder Überwachungs- bzw. Nutzungsanalysedaten für diesen Server anzeigen.
- Diese Aktion deinstalliert oder entfernt den Connect Health-Agent NICHT vom Server. Wenn Sie vor Ausführung dieses Schritts den Connect Health-Agent nicht deinstalliert haben, werden auf dem Server möglicherweise Fehlerereignisse in Zusammenhang mit dem Connect Health-Agent angezeigt.
- Diese Aktion löscht NICHT die Daten, die von diesem Server bereits erfasst wurden. Diese Daten werden gemäß der Microsoft Azure-Datenaufbewahrungsrichtlinie gelöscht. 
- Wenn Sie nach Ausführung dieser Aktion die Überwachung des gleichen Servers erneut starten möchten, müssen Sie den Connect Health-Agent von diesem Server deinstallieren und anschließend neu installieren. 


	### So löschen Sie einen Server aus dem Azure AD Connect Health-Dienst
<ol>
1. Öffnen Sie das Blatt "Server", indem Sie auf dem Blatt "Serverliste" den Namen des Servers auswählen, den Sie entfernen möchten. 
1. Klicken Sie auf der Aktionsleiste des Blatts "Server" auf die Schaltfläche "Löschen".
1. Bestätigen Sie die Aktion zum Löschen des Servers, indem Sie den Namen des Servers in das Bestätigungsfeld eingeben.
1. Klicken Sie auf die Schaltfläche "Löschen".







## Löschen einer Dienstinstanz aus dem Azure AD Connect Health-Dienst

In einigen Fällen möchten Sie möglicherweise eine Dienstinstanz entfernen. Folgen Sie den unten stehenden Anweisungen, um eine Dienstinstanz aus dem Azure AD Connect Health-Dienst zu entfernen.

Beim Löschen einer Dienstinstanz sind folgende Punkte zu beachten:

- Diese Aktion entfernt die aktuelle Dienstinstanz aus dem Überwachungsdienst. 
- Diese Aktion deinstalliert oder entfernt den Connect Health-Agent NICHT von einem der Server, die im Rahmen dieser Dienstinstanz überwacht wurden. Wenn Sie vor Ausführung dieses Schritts den Connect Health-Agent nicht deinstalliert haben, werden auf dem Server bzw. den Servern möglicherweise Fehlerereignisse in Zusammenhang mit dem Connect Health-Agent angezeigt. 
- Alle Daten aus dieser Dienstinstanz werden gemäß der Microsoft Azure-Datenaufbewahrungsrichtlinie gelöscht. 
- Wenn Sie nach Ausführung dieser Aktion die Überwachung dieses Diensts erneut starten möchten, deinstallieren Sie den Connect Health-Agent von allen zu überwachenden Servern, und installieren Sie ihn anschließend erneut. Wenn Sie nach Ausführung dieser Aktion die Überwachung des gleichen Servers erneut starten möchten, müssen Sie den Connect Health-Agent von diesem Server deinstallieren und anschließend neu installieren.







	### So löschen Sie eine Dienstinstanz aus dem Azure AD Connect Health-Dienst
<ol>
1. Öffnen Sie das Blatt "Dienst", indem Sie auf dem Blatt "Dienstliste" die ID des Diensts \(Name der Farm\) auswählen, den Sie entfernen möchten. 
1. Klicken Sie auf der Aktionsleiste des Blatts "Server" auf die Schaltfläche "Löschen".
1. Bestätigen Sie den Dienstnamen, indem Sie ihn in das Bestätigungsfeld eingeben \(Beispiel: sts.contoso.com\). 
1. Klicken Sie auf die Schaltfläche "Löschen".

<!---HONumber=July15_HO5-->