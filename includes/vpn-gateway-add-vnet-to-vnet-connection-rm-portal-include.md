Stellen Sie beim Hinzufügen einer VNet-zu-VNet-Verbindung sicher, dass beide virtuellen Netzwerke ein virtuelles Netzwerkgateway enthalten und dass die Adressbereiche Ihrer virtuellen Netzwerke sich nicht überschneiden.

1. Finden Sie Ihr virtuelles Netzwerk auf dem Blatt **Virtuelle Netzwerke**, und klicken Sie darauf, um das Blatt zu öffnen. Auf dem Blatt wird Ihr Gateway als *Verbundenes Gerät* angezeigt. Sie können Einstellungen auch direkt von Ihrem virtuellen Netzwerkgateway aus konfigurieren, ohne zuerst das VNet zu erweitern.
2. Klicken Sie in den Einstellungen des virtuellen Netzwerkgateways auf **Verbindungen**, und klicken Sie dann auf **Hinzufügen**.
3. Geben Sie unter **Name** einen Namen für die Verbindung ein. 
4. Wählen Sie unter **Verbindungstyp** die Option **VNet-zu-VNet**
5. Für **Virtuelles Netzwerkgateway** ist der Wert festgelegt, da Sie von diesem Gateway aus die Verbindung herstellen.
6. Wählen Sie unter **Zweites virtuelles Netzwerkgateway** das Gateway aus, mit dem Sie von diesem Gateway aus eine Verbindung erstellen möchten.
8. Die verbleibenden Werte für **Abonnement**, **Ressourcengruppe** und **Speicherort** sind unveränderlich.
9. Klicken Sie auf **OK**, um die Verbindung zu erstellen. Auf dem Bildschirm blinkt der Hinweis *Verbindung wird erstellt*.
10. Wenn die Verbindung erstellt ist, wird sie auf dem Blatt **Verbindungen** für Ihr Gateway angezeigt.

<!---HONumber=AcomDC_0107_2016-->