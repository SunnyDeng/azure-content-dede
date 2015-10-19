Obwohl durch die Verwendung von Systemrouten der Datenverkehr für Ihre Bereitstellung automatisch ermöglicht wird, gibt es Fälle, in denen Sie das Routing von Paketen über ein virtuelles Gerät steuern möchten. Hierzu können Sie benutzerdefinierte Routen erstellen, die den nächsten Hop für Pakete in ein bestimmtes Subnetz angeben, und festlegen, dass die Pakete stattdessen an Ihre virtuelle Anwendung gesendet werden sollen. Außerdem können Sie die IP-Weiterleitung für den virtuellen Computer aktivieren, der als virtuelles Gerät ausgeführt wird.

Beispiele für Fälle, in denen virtuelle Geräte verwendet werden können:

- Überwachen des Netzwerkverkehrs mit einem Intrusion Detection System (IDS)
- Steuerung des Datenverkehrs mit einer Firewall

Weitere Informationen zu UDR und IP-Weiterleitung finden Sie unter [Benutzerdefinierte Routen und IP-Weiterleitung](./virtual-networks-udr-overview.md).

<!---HONumber=Oct15_HO2-->