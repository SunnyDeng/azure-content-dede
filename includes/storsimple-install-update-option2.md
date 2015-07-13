#### So installieren Sie Update 1 über das Azure-Verwaltungsportal

1. Wechseln Sie im Verwaltungsportal zur Seite **Geräte**, und wählen Sie Ihr Gerät aus.
 
2. Navigieren Sie zu **Geräte** > **Konfigurieren**.

3. Suchen Sie unter **Netzwerkschnittstellen** die Netzwerkschnittstelle, der ein Gateway zugewiesen wurde. Dies ist eine andere Netzwerkschnittstelle als DATA 0.

4. Löschen Sie die Gatewayeinstellung. Hinweis: Da Gatewayeinstellungen für eine cloudfähige Netzwerkschnittstelle erforderlich sind, müssen Sie den Cloudzugriff für diese Schnittstelle deaktivieren, um die Einstellung löschen zu können.

5. Wiederholen Sie Schritt 4 für alle anderen Netzwerkschnittstellen, denen ein Gateway zugewiesen ist (mit Ausnahme von DATA 0).

6. Speichern Sie die geänderte Konfiguration.

7. Sie können nun [das Verwaltungsportal zum Installieren von Update 1 verwenden](#use-the-management-portal-to-install-update-1).

<!---HONumber=62-->