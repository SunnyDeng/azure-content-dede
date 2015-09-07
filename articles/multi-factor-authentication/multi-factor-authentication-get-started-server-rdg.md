<properties 
	pageTitle="Remotedesktop-Gateway und Azure Multi-Factor Authentication-Server mithilfe von RADIUS"
	description="Dies ist die Azure Multi-Factor Authentication-Seite, die bei der Bereitstellung Remotedesktop-Gateways (RD) und des Azure Multi-Factor Authentication-Servers mithilfe von RADIUS Unterstützung bietet."
	services="multi-factor-authentication"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2015"
	ms.author="billmath"/>

# Remotedesktop-Gateway und Azure Multi-Factor Authentication-Server mithilfe von RADIUS

In vielen Fällen verwendet das Remotedesktop-Gateway den lokalen Netzwerkrichtlinenserver (NPS) zum Authentifizieren von Benutzern. Dieses Dokument beschreibt, wie RADIUS-Anforderungen aus dem Remotedesktop-Gateway (über den lokalen NPS) an den Multi-Factor Authentication-Server weitergeleitet werden.

Der Multi-Factor Authentication-Server sollte auf einem separaten Server installiert werden, wodurch dann die RADIUS-Anforderung per Proxy zurück an den NPS auf dem Remotedesktop-Gatewayserver zurückgegeben wird. Nachdem der NPS den Benutzernamen und das Kennwort überprüft hat, wird eine Antwort an den Multi-Factor Authentication-Server zurückgegeben, der die zweite Stufe der Authentifizierung durchführt, bevor ein Ergebnis an das Gateway zurückgegeben wird.





## Konfigurieren des RD-Gateways

Das RD-Gateway muss zum Senden von RADIUS-Authentifizierungen an einen Azure Multi-Factor Authentication-Server konfiguriert werden. Sobald das RD-Gateway installiert, konfiguriert und funktionstüchtig ist, rufen Sie die Eigenschaften des RD-Gateway auf. Wechseln Sie zur Registerkarte "RD-CAP-Speicher", und ändern sie die Einstellung auf einen zentralen Server mit NPS statt einem lokalen Server mit NPS. Fügen Sie einen oder mehrere Azure Multi-Factor Authentication-Server als RADIUS-Server hinzu, und geben Sie einen gemeinsamen geheimen Schlüssel für jeden Server an.





## Konfigurieren des Netzwerkrichtlinienservers

Das RD-Gateway verwendet den NPS, um die RADIUS-Anforderung an Azure Multi-Factor Authentication zu senden. Ein Timeout-Wert muss geändert werden, um zu verhindern, dass das RD-Gateway in ein Timeout eintritt, bevor die mehrstufige Authentifizierung abgeschlossen ist. Verwenden Sie das folgende Verfahren, um den NPS konfigurieren.

1. Erweitern Sie im NPS das Menü "RADIUS-Clients und Server" in der linken Spalte, und klicken Sie auf "RADIUS-Remoteservergruppen". Öffnen Sie die Eigenschaften der Gruppe "TS-GATEWAY GROUP". Bearbeiten Sie den/die angezeigten RADIUS-Server, und wechseln Sie zur Registerkarte "Lastenausgleich". Ändern Sie die "Anzahl der Sekunden ohne Antwort, bevor die Anforderung als abgebrochen gilt" und die "Anzahl der Sekunden zwischen Anforderungen, wenn der Server als nicht verfügbar identifiziert wird" auf 30 bis 60 Sekunden. Klicken Sie auf die Registerkarte "Authentifizierung/Konto", und stellen Sie sicher, dass die angegebenen RADIUS-Ports den Ports entsprechen, die der Multi-Factor Authentication-Server überwacht.
2. Außerdem muss der NPS zum Empfangen von RADIUS-Authentifizierungen vom Azure Multi-Factor Authentication-Server konfiguriert werden. Klicken Sie im linken Menü auf "RADIUS-Clients". Fügen Sie den Azure Multi-Factor Authentication-Server als RADIUS-Client hinzu. Wählen Sie einen Anzeigenamen und geben Sie einen gemeinsamen geheimen Schlüssel an.
3. Erweitern Sie den Abschnitt "Richtlinien" im linken Navigationsbereich, und klicken Sie auf "Verbindungsanforderungsrichtlinien". Es sollte eine Verbindungsanforderungsrichtlinie namens "TS GATEWAY AUTHORIZATION POLICY" geben, die beim Konfigurieren des RD-Gateways erstellt wurde. Diese Richtlinie leitet RADIUS-Anforderungen an den Multi-Factor Authentication-Server weiter.
4. Kopieren Sie diese Richtlinie, um eine neue zu erstellen. Fügen Sie in der neuen Richtlinie eine Bedingung hinzu, in der der Anzeigename des Clients mit dem oben in Schritt 2 festgelegten Anzeigenamen für den Azure Multi-Factor Authentication-Server RADIUS-Client übereinstimmt. Ändern Sie den Authentifizierungsanbieter auf dem lokalen Computer. Mit dieser Richtlinie können Sie sicherstellen, dass beim Empfang einer RADIUS-Anforderung vom Azure Multi-Factor Authentication-Server die Authentifizierung lokal erfolgt, anstatt eine RADIUS-Anforderung an den Azure Multi-Factor Authentication-Server zurückzusenden, was zu einer Schleifenbedingung führen würde. Um die Schleifenbedingung zu verhindern, muss diese neue Richtlinie ÜBER die Originalrichtlinie treten, die an den Multi-Factor Authentication-Server weiterleitet.

## Konfigurieren von Azure Multi-Factor Authentication


--------------------------------------------------------------------------------



Der Azure Multi-Factor Authentication-Server wird als RADIUS-Proxy zwischen dem RD-Gateway und NPS konfiguriert. Er sollte auf einem Domänen-verbundenen Server installiert werden, der vom Remotedesktop-Gatewayserver getrennt ist. Verwenden Sie das folgende Verfahren, um den Azure Multi-Factor Authentication-Server zu konfigurieren.

1. Öffnen Sie den Azure Multi-Factor Authentication-Server, und klicken Sie auf das Symbol "RADIUS-Authentifizierung". Aktivieren Sie das Kontrollkästchen für die RADIUS-Authentifizierung.
2. Stellen Sie auf der Registerkarte "Clients" sicher, dass die Ports mit der Konfiguration in NPS übereinstimmen, und klicken Sie auf die Schaltfläche "Hinzufügen". Fügen Sie die IP-Adresse des RD-Gateway-Servers, den Anwendungsnamen (optional) und einen gemeinsamen geheimen Schlüssel hinzu. Der gemeinsame geheime Schlüssel muss auf dem Azure Multi-Factor Authentication-Server und dem RD-Gateway identisch sein.
3. Klicken Sie auf die Registerkarte "Ziel", und wählen Sie das Optionsfeld für den/die RADIUS-Server.
4. Klicken Sie auf die Schaltfläche "Hinzufügen". Geben Sie die IP-Adresse, den gemeinsamen geheimen Schlüssel und die Ports des NPS-Servers ein. Sofern Sie keinen zentralen NPS nutzen, sind der RADIUS-Client und das RADIUS-Ziel identisch. Der gemeinsame geheime Schlüssel muss mit der Einrichtung im Bereich "RADIUS-Client" des NPS-Servers übereinstimmen. 

![RADIUS-Authentifizierung](./media/multi-factor-authentication-get-started-server-rdg/radius.png)

<!---HONumber=August15_HO9-->