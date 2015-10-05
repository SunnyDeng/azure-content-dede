## Traffic Manager-Profil
Der Traffic Manager und seine untergeordnete Endpunkt-Ressource ermöglichen die Verteilung Ihres Datenverkehrs zu Endpunkten in und außerhalb von Azure. Eine solche Datenverkehrsverteilung wird durch Richtlinien bestimmt. Der Traffic Manager lässt auch eine Überwachung der Endpunkt-Integrität zu, sowie eine angemessene Umleitung des Datenverkehrs basierend auf der Integrität eines Endpunktes.

Zu den Schlüsseleigenschaften eines Traffic Manager zählen:

- **Datenverkehr-Routingmethode** - mögliche Werte sind *Leistung*, *Gewichtung* und *Priorität*.
- **DNS-Konfiguration** - FQDN für das Profil.
- **Protokoll** - Überwachungsprotokoll, mögliche Werte sind *HTTP* und *HTTPS*.
- **Port** - Überwachungsport. 
- **Pfad** - Überwachungspfad.
- **Endpunkte** - Container für Endpunkt-Ressourcen.

### Endpunkt 
Ein Endpunkt ist eine dem Traffic Manager-Profil untergeordnete Ressource. Er stellt einen Dienst- oder Webendpunkt dar, an den der Benutzerdatenverkehr anhand der in der Traffic Manager-Ressource konfigurierten Richtlinie verteilt wird.

Zu den Schlüsseleigenschaften eines Endpunktes zählen:
 
- **Typ** - der Typ des Endpunkts, mögliche Werte sind *Azure-Endpunkt*, *externer Endpunkt* und *geschachtelter Endpunkt*. 
- **Zielressourcen-ID** – öffentliche IP-Adresse eines Dienst- oder Webendpunktes. Dabei kann es sich um einen Azure- oder externen Endpunkt handeln.
- **Gewichtung** - Endpunkt-Gewichtung, die für die Datenverkehrsverwaltung verwendet wird. 
- **Priorität** - Priorität des Endpunktes, die zum Definieren einer Failover-Aktion verwendet wird. 

<!---HONumber=Sept15_HO4-->