Um ein lokales Netzwerkgateway zu erstellen, führen Sie die folgenden Schritte aus:

1. Verwenden Sie **Durchsuchen**, und filtern Sie, um das Blatt **Lokale Netzwerkgateways** zu finden. Klicken Sie dann auf **Hinzufügen**.
2. Geben Sie auf dem Blatt **Lokale Netzwerkgateways erstellen** unter **Name** einen Namen für Ihr lokales Netzwerkgateway-Objekt ein. In diesem Beispiel nennen wir das lokale Netzwerkgateway *GW1LocalNet*.
3. Konfigurieren Sie eine **IP-Adresse** für das Gateway. Dies ist die IP-Adresse des externen VPN-Geräts, mit dem Sie eine Verbindung herstellen möchten. Sie darf sich nicht hinter einer NAT befinden und muss für Azure erreichbar sein. Dies ist die IP-Adresse des Geräts, mit dem Ihr Azure-Gateway eine Verbindung herstellen soll.
4. **Adressraum** bezieht sich auf die Adressbereiche für das lokale Netzwerk. Sie können mehrere Adressraumbereiche hinzufügen. Die hier eingegebenen Bereiche dürfen sich nicht mit den Adressraumbereichen für virtuelle Netzwerke überschneiden, die über das Gateway kommunizieren sollen. Sie müssen sie mit Ihrer lokalen Konfiguration sowie mit den Adressräumen des virtuellen Azure-Netzwerks koordinieren. 
5. Stellen Sie unter **Abonnement** sicher, dass das richtige Abonnement angezeigt wird.
6. Wählen Sie unter **Ressourcengruppe** die Ressourcengruppe aus, die Sie verwenden möchten. Sie können entweder eine neue Ressourcengruppe erstellen oder eine auswählen, die Sie bereits erstellt haben. Um eine neue Ressourcengruppe zu erstellen, geben Sie den Namen in das Feld ein. Um eine bereits erstellte Ressourcengruppe auszuwählen, klicken Sie auf **Ressourcengruppe**, um das Blatt **Ressourcengruppe** zu öffnen, und wählen Sie dann die gewünschte Ressourcengruppe aus.
7. Stellen Sie unter **Standort** sicher, dass der Standort mit dem virtuellen Netzwerkgateway identisch ist, mit dem die Zuordnung erfolgt.
8. Lassen Sie „An Dashboard anheften“ ausgewählt, wenn Sie dieses lokale Netzwerkgateway ganz bequem im Dashboard vorfinden möchten.
9. Klicken Sie auf **Erstellen**, um das lokale Netzwerkgateway zu erstellen. Im Dashboard wird „Lokales Netzwerkgateway wird bereitgestellt“ angezeigt. Die Erstellung sollte nicht sehr lange dauern.

<!---HONumber=AcomDC_0107_2016-->