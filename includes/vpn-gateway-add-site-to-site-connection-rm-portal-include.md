Beim Hinzufügen einer Standort-zu-Standort-Verbindung mit dem virtuellen Netzwerkgateway müssen Sie zuerst ein lokales Netzwerkgateway erstellen, um von Ihrer Konfiguration aus darauf zu verweisen. Stellen Sie sicher, dass Sie ein lokales Netzwerkgateway konfiguriert haben. Sie können über **Durchsuchen** nach lokalen Netzwerkgateways suchen und über **Lokale Netzwerkgateways** filtern.

1. Finden Sie Ihr virtuelles Netzwerk auf dem Blatt **Virtuelle Netzwerke**, und klicken Sie darauf, um das Blatt zu öffnen. Auf dem Blatt wird Ihr Gateway als *Verbundenes Gerät* angezeigt.
2. Klicken Sie auf den ***Namen des virtuellen Netzwerkgateways*** -> **Virtuelles Netzwerkgateway** -> **Einstellungen** -> **Verbindungen**, und klicken Sie dann auf **Hinzufügen**.
3. Geben Sie unter **Name** einen Namen für die Verbindung ein. In diesem Beispiel verwenden wir *GW1S2S*
4. Wählen Sie als **Verbindungstyp** die Option **Standort-zu-Standort (IPSec)** aus.
5. Für **Virtuelles Netzwerkgateway** ist der Wert festgelegt, da Sie von diesem Gateway aus die Verbindung herstellen.
6. Klicken Sie unter **Lokales Netzwerkgateway** auf **Ein lokales Netzwerkgateway auswählen**, und wählen Sie das lokale Netzwerkgateway aus, das Sie verwenden möchten. In diesem Beispiel verwenden wir *GW1LocalNet*.
7. Unter **Gemeinsam verwendeter Schlüssel** müssen die hier angegebenen Werte mit denen übereinstimmen, die für Ihr lokales VPN-Gerät vorliegen. Wenn Ihr VPN-Gerät im lokalen Netzwerk keinen gemeinsam verwendeten Schlüssel bereitstellt, können Sie einen erfinden und sowohl hier als auch auf Ihrem lokalen Gerät eingeben. Wichtig ist, dass beide übereinstimmen.
8. Die verbleibenden Werte für **Abonnement**, **Ressourcengruppe** und **Speicherort** sind unveränderlich.
9. Klicken Sie auf **OK**, um die Verbindung zu erstellen. Auf dem Bildschirm blinkt der Hinweis *Verbindung wird erstellt*.
10. Wenn die Verbindung erstellt ist, wird sie auf dem Blatt **Verbindungen** für Ihr Gateway angezeigt.

<!---HONumber=AcomDC_0107_2016-->