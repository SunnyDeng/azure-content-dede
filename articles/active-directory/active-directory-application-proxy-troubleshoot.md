<properties
	pageTitle="Problembehandlung von Anwendungsproxys"
	description="Behandelt die Problembehandlung von Azure AD-Anwendungsproxys."
	services="active-directory"
	documentationCenter=""
	authors="rkarlin"
	manager="msStevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/09/2015"
	ms.author="rkarlin"/>



# Problembehandlung von Anwendungsproxys


> [AZURE.IMPORTANT]Das Feature "Anwendungsproxy" ist nur verfügbar, wenn Sie Azure Active Directory auf die Premium oder Basic Edition aktualisiert haben. Weitere Informationen finden Sie unter [Azure Active Directory-Editionen](active-directory-editions.md).

Wenn beim Zugriff auf eine veröffentlichte Anwendung oder beim Veröffentlichen von Anwendungen Fehler auftreten, überprüfen Sie die folgenden Optionen, um zu ermitteln, ob der Microsoft Azure AD-Anwendungsproxy ordnungsgemäß funktioniert:

- Öffnen Sie die Windows Services-Konsole, und vergewissern Sie sich, dass der Dienst "Microsoft AAD-Anwendungsproxy-Connector" aktiviert ist und ausgeführt wird. Sie können ggf. auch – wie in der folgenden Abbildung gezeigt – die Eigenschaftenseite des Anwendungsproxy-Diensts anzeigen:

![Screenshot von Microsoft AAD-Anwendungsproxy-Connector-Eigenschaften][connectorproperties.png]

- Öffnen Sie die Ereignisanzeige, und suchen Sie unter **Anwendungs- und Dienstprotokolle** > **Microsoft** > **AadApplicationProxy** > **Connector** > **Administrator** nach Ereignissen, die sich auf den Anwendungsproxy-Connector beziehen.
- Bei Bedarf sind ausführlichere Protokolle verfügbar, indem Sie die Analyse- und Debugprotokolle aktivieren und das Sitzungsprotokoll des Anwendungsproxy-Connectors aktivieren. Die folgende Abbildung zeigt dies:

![Screenshot von Anwendungsproxy-Connector-Sitzungsprotokoll][sessionlog.png]


## Allgemeine Fehler

Error | Beschreibung | Lösung
--- | --- | ---
Auf diese Unternehmens-App kann nicht zugegriffen werden. Sie haben keine Befugnis, auf diese Anwendung zuzugreifen. Fehler bei der Autorisierung. Stellen Sie sicher, dass Sie dem Benutzer Zugriff auf diese Anwendung zuweisen. | Möglicherweise haben Sie den Benutzer nicht der Anwendung zugewiesen. | Wechseln Sie zur Registerkarte "Anwendung", und weisen Sie unter "Benutzer und Gruppen" diesen Benutzer oder die Benutzergruppe dieser Anwendung zu.
Auf diese Unternehmens-App kann nicht zugegriffen werden. Sie haben keine Befugnis, auf diese Anwendung zuzugreifen. Fehler bei der Autorisierung. Stellen Sie sicher, dass der Benutzer eine Lizenz für Azure Active Directory Premium oder Basic hat. | Möglicherweise erhält der Benutzer diese Fehlermeldung beim Zugriff auf die von Ihnen veröffentlichte App, wenn ihm nicht explizit eine Lizenz für Premium/Basic vom Administrator des Abonnenten zugewiesen wurde. | Wechseln Sie zur Active Directory-Registerkarte **Lizenzen** des Abonnenten, und stellen Sie sicher, dass diesem Benutzer oder dieser Benutzergruppe eine Premium- oder Basic-Lizenz zugewiesen ist.


## Problembehandlung des Connectors
Wenn während der Connector-Installation durch den Assistenten ein Fehler bei der Registrierung auftritt, können Sie die Ursache des Fehlers im Ereignisprotokoll unter **Windows-Protokolle** > **Anwendung** oder durch Ausführen des folgenden Windows PowerShell-Befehls anzeigen.

    Get-EventLog application –source “Microsoft AAD Application Proxy Connector” –EntryType “Error” –Newest 1

| Error | Beschreibung | Lösung |
| --- | --- | --- |
| Fehler bei der Connector-Registrierung: Stellen Sie sicher, dass Sie den Anwendungsproxy im Azure-Verwaltungsportal aktiviert haben, und dass Sie Ihren Active Directory-Benutzernamen und das Kennwort richtig eingegeben haben. Fehler: "Mindestens ein Fehler." | Sie haben das Registrierungsfenster geschlossen, ohne die Anmeldung bei Azure AD auszuführen. | Führen Sie den Connector-Assistenten erneut aus, und registrieren Sie den Connector. |
| Fehler bei der Connector-Registrierung: Stellen Sie sicher, dass Sie den Anwendungsproxy im Azure-Verwaltungsportal aktiviert haben, und dass Sie Ihren Active Directory-Benutzernamen und das Kennwort richtig eingegeben haben. Fehler: "AADSTS50001: Die Ressource `https://proxy.cloudwebappproxy.net/registerapp` ist deaktiviert." | Anwendungsproxy ist deaktiviert. | Stellen Sie sicher, dass Sie den Anwendungsproxy im Azure AD-Portal aktivieren, bevor Sie versuchen, den Connector zu registrieren. Weitere Informationen zum Aktivieren des Anwendungsproxys finden Sie unter [Aktivieren von Anwendungsproxy-Diensten](active-directory-application-proxy-enable.md). |
| Fehler bei der Connector-Registrierung: Stellen Sie sicher, dass Sie den Anwendungsproxy im Azure-Verwaltungsportal aktiviert haben, und dass Sie Ihren Active Directory-Benutzernamen und das Kennwort richtig eingegeben haben. Fehler: "Mindestens ein Fehler." | Wenn das Registrierungsfenster geöffnet und dann sofort geschlossen wird, ohne dass Sie sich anmelden können, erhalten Sie ggf. diese Fehlermeldung. Dieser Fehler tritt auf, wenn in Ihrem System ein Netzwerkfehler vorliegt. | Stellen Sie sicher, dass es möglich ist, mit einem Browser eine Verbindung mit einer öffentlichen Website herzustellen, und dass die Ports wie unter [Voraussetzungen für den Anwendungsproxy](active-directory-application-proxy-enable.md) angegeben geöffnet sind. |
| Fehler bei der Connector-Registrierung: Vergewissern Sie sich, dass der Computer mit dem Internet verbunden ist. Fehler: "Unter `https://connector.msappproxy.net:9090/register/RegisterConnector` hat kein Endpunkt gelauscht, der die Nachricht akzeptieren konnte. Ursache dieses Fehlers ist häufig eine falsche Adresse oder SOAP-Aktion. Weitere Informationen können Sie, sofern vorhanden, der InnerException entnehmen." | Wenn Sie sich mithilfe Ihres Azure AD-Benutzernamens und -Kennworts anmelden und dann diese Fehlermeldung erhalten, sind ggf. alle Ports über 8081 blockiert. | Stellen Sie sicher, dass die erforderlichen Ports geöffnet sind. Weitere Informationen finden Sie unter [Voraussetzungen für den Anwendungsproxy](active-directory-application-proxy-enable.md). |
| Klartextfehler wird im Registrierungsfenster angezeigt. Der Vorgang kann nicht fortgesetzt werden – Fenster schließen. | Sie haben einen falschen Benutzernamen oder ein falsches Kennwort eingegeben. | Versuchen Sie es erneut. |
| Fehler bei der Connector-Registrierung: Stellen Sie sicher, dass Sie den Anwendungsproxy im Azure-Verwaltungsportal aktiviert haben, und dass Sie Ihren Active Directory-Benutzernamen und das Kennwort richtig eingegeben haben. Fehler: AADSTS50059: In der Anforderung oder in den bereitgestellten Anmeldeinformationen wurden keine Informationen gefunden, die den Mandanten identifizieren, und bei der Suche nach dem Dienstprinzipal-URI ist ein Fehler aufgetreten. | Sie versuchen, sich mithilfe eines Microsoft-Kontos und nicht mithilfe einer Domäne anzumelden, die Bestandteil der Organisations-ID des Verzeichnisses ist, auf das Sie versuchen, zuzugreifen. | Stellen Sie sicher, dass der Administrator Teil des gleichen Domänennamens wie die Mandantendomäne ist. Wenn die Azure AD-Domäne z. B. contoso.com ist, sollte der Administrator admin@contoso.com lauten. |
| Fehler beim Abrufen der aktuellen Ausführungsrichtlinie für die Ausführung von PowerShell-Skripts | Falls die Installation des Connectors nicht erfolgreich verläuft, stellen Sie sicher, dass die PowerShell-Ausführungsrichtlinie nicht deaktiviert ist. | Öffnen Sie den Gruppenrichtlinien-Editor. Gehen Sie zu "Computerkonfiguration > Administrative Vorlagen > Windows-Komponenten > Windows PowerShell", und doppelklicken Sie auf "Skriptausführung aktivieren". Diese kann entweder auf "Nicht konfiguriert" oder "Aktiviert" festgelegt sein. Bei der Einstellung "Aktiviert" stellen Sie sicher, dass die Ausführungsrichtlinie unter "Optionen" entweder auf **Lokale Skripts und remote signierte Skripts zulassen** oder **Alle Skripts zulassen** festgelegt ist. |
| Der Connector konnte die Konfiguration nicht herunterladen. | Das Clientzertifikat des Connectors, das für die Authentifizierung verwendet wird, ist abgelaufen. Dies kann auch auftreten, wenn Sie den Connector hinter einem Proxy installiert haben. In diesem Fall kann der Connector nicht auf das Internet zugreifen, und wird nicht in der Lage sein, Anwendungen für Remotebenutzer bereitzustellen. | Erneuern Sie die Vertrauensstellung manuell mithilfe des Cmdlets `Register-AppProxyConnector` in Windows PowerShell. Wenn sich der Connector hinter einem Proxy befindet, ist es notwendig, den Connector-Konten "Netzwerkdienste" und "Lokales System" Zugriff auf das Internet zu gewähren. Hierzu können sie entweder Zugriff auf den Proxy erhalten, oder sie werden zur Umgehung des Proxys eingestellt. |
| Fehler bei der Connector-Registrierung: Stellen Sie sicher, dass Sie ein globaler Administrator Ihres Active Directory-Verzeichnisses sind, um den Connector zu registrieren. Fehler: "Die Registrierungsanforderung wurde verweigert." | Der Alias, mit dem Sie versuchen, sich anzumelden, ist kein Administrator für diese Domäne. Ihr Connector wird immer für das Verzeichnis installiert, das Besitzer der Domäne des Benutzers ist. | Stellen Sie sicher, dass der Administrator, als der Sie sich anmelden möchten, über globale Berechtigungen für den Azure AD-Mandanten verfügt.|


## Kerberos-Fehler

| Error | Beschreibung | Lösung |
| --- | --- | --- |
| Fehler beim Abrufen der aktuellen Ausführungsrichtlinie für die Ausführung von PowerShell-Skripts | Falls die Installation des Connectors nicht erfolgreich verläuft, stellen Sie sicher, dass die PowerShell-Ausführungsrichtlinie nicht deaktiviert ist. | Öffnen Sie den Gruppenrichtlinien-Editor. Gehen Sie zu "Computerkonfiguration > Administrative Vorlagen > Windows-Komponenten > Windows PowerShell", und doppelklicken Sie auf "Skriptausführung aktivieren". Diese kann entweder auf "Nicht konfiguriert" oder "Aktiviert" festgelegt sein. Bei der Einstellung "Aktiviert" stellen Sie sicher, dass die Ausführungsrichtlinie unter "Optionen" entweder auf **Lokale Skripts und remote signierte Skripts zulassen** oder **Alle Skripts zulassen** festgelegt ist. |
| 12008 - Azure AD hat die maximale Anzahl von zulässigen Kerberos-Authentifizierungsversuchen beim Back-End-Server überschritten. | Dieses Ereignis deutet möglicherweise auf eine falsche Konfiguration zwischen Azure AD und dem Back-End-Anwendungsserver oder ein Problem bei der Konfiguration von Datum und Uhrzeit auf beiden Computern hin. | Der Back-End-Server hat das von Azure AD erstellte Kerberos-Ticket abgelehnt. Überprüfen Sie, ob die Konfiguration von Azure AD und des Back-End-Anwendungsservers korrekt ist. Stellen Sie sicher, dass die Konfiguration von Datum und Uhrzeit in Azure AD und auf dem Back-End-Anwendungsserver synchronisiert ist. |
| 13016 – Azure AD kann kein Kerberos-Ticket für den Benutzer abrufen, da kein UPN im Edgetoken oder im Zugriffscookie vorliegt. | Es gibt ein Problem mit der STS-Konfiguration. | Korrigieren Sie die UPN-Anspruchskonfiguration im STS. |
| 13019 – Azure AD kann aufgrund des folgenden allgemeinen API-Fehlers kein Kerberos-Ticket für den Benutzer abrufen. | Dieses Ereignis deutet möglicherweise auf eine falsche Konfiguration zwischen Azure AD und dem Domänencontrollerserver oder ein Problem bei der Konfiguration von Datum und Uhrzeit auf beiden Computern hin. | Der Domänencontroller hat das von Azure AD erstellte Kerberos-Ticket abgelehnt. Überprüfen Sie, ob die Konfiguration von Azure AD und des Back-End-Anwendungsservers korrekt ist, insbesondere die SPN-Konfiguration. Sorgen Sie dafür, dass Azure AD in dieselbe Domäne wie der Domänencontroller eingebunden ist, um sicherzustellen, dass der Domänencontroller eine Vertrauensstellung mit Azure AD herstellt. Stellen Sie sicher, dass die Konfiguration von Datum und Uhrzeit in Azure AD und auf dem Domänencontroller synchronisiert ist. |
| 13020 – Azure AD kann kein Kerberos-Ticket für den Benutzer abrufen, da der Back-End-Server-SPN nicht definiert ist. | Dieses Ereignis deutet möglicherweise auf eine falsche Konfiguration zwischen Azure AD und dem Domänencontrollerserver oder ein Problem bei der Konfiguration von Datum und Uhrzeit auf beiden Computern hin. | Der Domänencontroller hat das von Azure AD erstellte Kerberos-Ticket abgelehnt. Überprüfen Sie, ob die Konfiguration von Azure AD und des Back-End-Anwendungsservers korrekt ist, insbesondere die SPN-Konfiguration. Sorgen Sie dafür, dass Azure AD in dieselbe Domäne wie der Domänencontroller eingebunden ist, um sicherzustellen, dass der Domänencontroller eine Vertrauensstellung mit Azure AD herstellt. Stellen Sie sicher, dass die Konfiguration von Datum und Uhrzeit in Azure AD und auf dem Domänencontroller synchronisiert ist. |
| 13022 - Azure AD kann den Benutzer nicht authentifizieren, da der Back-End-Server auf Kerberos-Authentifizierungsversuche mit einem HTTP 401-Fehler reagiert. | Dieses Ereignis deutet möglicherweise auf eine falsche Konfiguration zwischen Azure AD und dem Back-End-Anwendungsserver oder ein Problem bei der Konfiguration von Datum und Uhrzeit auf beiden Computern hin. | Der Back-End-Server hat das von Azure AD erstellte Kerberos-Ticket abgelehnt. Überprüfen Sie, ob die Konfiguration von Azure AD und des Back-End-Anwendungsservers korrekt ist. Stellen Sie sicher, dass die Konfiguration von Datum und Uhrzeit in Azure AD und auf dem Back-End-Anwendungsserver synchronisiert ist. |
| Die Website kann die Seite nicht anzeigen. | Der Benutzer kann beim Versuch, auf die von Ihnen veröffentlichte App zuzugreifen, diese Fehlermeldung erhalten, wenn die Anwendung eine IWA-Anwendung ist. Der definierte SPN für diese Anwendung ist möglicherweise falsch. | Für IWA-Apps: Stellen Sie sicher, dass für diese Anwendung der richtige SPN konfiguriert ist. |
| Die Website kann die Seite nicht anzeigen. | Der Benutzer kann beim Versuch, auf die von Ihnen veröffentlichte App zuzugreifen, diese Fehlermeldung erhalten, wenn die Anwendung eine OWA-Anwendung ist. Folgende Ursachen kommen infrage: | Die Schritte zur Problembehebung in entsprechender Reihenfolge: |
| | Der definierte SPN für diese Anwendung ist falsch. | Stellen Sie sicher, dass für diese Anwendung der richtige SPN konfiguriert ist. |
| | Der Benutzer, der auf die Anwendung zugreifen möchte, verwendet für die Anmeldung ein Microsoft-Konto anstelle des richtigen Unternehmenskontos, oder der Benutzer ist ein Gastbenutzer. | Vergewissern Sie sich, dass Benutzer sich mithilfe ihres Unternehmenskontos anmelden, das der Domäne der veröffentlichten Anwendung entspricht. Microsoft-Konto- und Gastbenutzer können nicht auf IWA-Anwendungen zugreifen. |
| | Der Benutzer, der auf die Anwendung zugreifen möchte, ist für diese Anwendung auf der lokalen Seite nicht ordnungsgemäß definiert. | Stellen Sie sicher, dass dieser Benutzer über die entsprechenden Berechtigungen verfügt, wie für diese Back-End-Anwendung auf dem lokalen Computer definiert. |
| Auf diese Unternehmens-App kann nicht zugegriffen werden. Sie haben keine Befugnis, auf diese Anwendung zuzugreifen. Fehler bei der Autorisierung. Stellen Sie sicher, dass Sie dem Benutzer Zugriff auf diese Anwendung zuweisen. | Der Benutzer kann beim Versuch, auf die von Ihnen veröffentlichte App zuzugreifen, diese Fehlermeldung erhalten, wenn er für die Anmeldung ein Microsoft-Konto anstelle des richtigen Unternehmenskontos verwendet, oder ein Gastbenutzer ist. | Microsoft-Konto- und Gastbenutzer können nicht auf IWA-Anwendungen zugreifen. Vergewissern Sie sich, dass Benutzer sich mithilfe ihres Unternehmenskontos anmelden, das der Domäne der veröffentlichten Anwendung entspricht. |
| Auf diese Unternehmens-App kann momentan nicht zugegriffen werden. Versuchen Sie es später erneut. Der Connector hat das Zeitlimit überschritten. | Der Benutzer kann beim Versuch, auf die von Ihnen veröffentlichte App zuzugreifen, diese Fehlermeldung erhalten, wenn er für diese Anwendung auf der lokalen Seite nicht ordnungsgemäß definiert ist. | Stellen Sie sicher, dass dieser Benutzer über die entsprechenden Berechtigungen verfügt, wie für diese Back-End-Anwendung auf dem lokalen Computer definiert. |


## Weitere Informationen

[Mithilfe des Anwendungsproxys Anwendungen für sicheren Remotezugriff veröffentlichen](active-directory-application-proxy-configure.md)

[Aktivieren von Anwendungsproxy-Diensten](active-directory-application-proxy-enable.md)

<!--Image references-->
[1]: ./media/active-directory-application-proxy-troubleshoot/connectorproperties.png
[2]: ./media/active-directory-application-proxy-troubleshoot/sessionlog.png

<!---HONumber=Sept15_HO3-->