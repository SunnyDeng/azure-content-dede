<!--author=SharS last changed: 11/16/15-->

#### So installieren Sie Update 1.2 über das Azure-Portal

1. Wechseln Sie im Azure-Portal zur Seite **Geräte**, und wählen Sie Ihr Gerät aus.
 
2. Navigieren Sie zu **Geräte** > **Konfigurieren**.

3. Vergewissern Sie sich unter **Netzwerkschnittstellen** zunächst, dass Sie über mindestens eine Netzwerkschnittstelle verfügen, für die iSCSI aktiviert ist. Suchen Sie dann die Netzwerkschnittstelle (nicht DATA 0), der ein Gateway zugewiesen wurde.

4. Deaktivieren Sie die Netzwerkschnittstelle, der ein Gateway zugeordnet ist, und speichern Sie die geänderte Konfiguration. Beachten Sie, dass die Netzwerkschnittstellen-Einstellungen beibehalten werden. Wenn Sie also diese Netzwerkschnittstelle später wieder aktivieren, stellt das Portal wieder die ursprünglichen Einstellungen her.

7. Sie können nun [das Azure-Portal zum Installieren von Update 1.2 verwenden](#install-update-12-via-the-azure-portal). Befolgen Sie die Anleitung beginnend mit Schritt 3 dieses Verfahrens. Nachdem Sie alle Updates installiert haben, können Sie die deaktivierte Netzwerkschnittstelle wieder aktivieren.

<!---HONumber=Nov15_HO4-->