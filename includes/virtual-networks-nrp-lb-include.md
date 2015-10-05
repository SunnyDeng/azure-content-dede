## Lastenausgleichsmodul
Ein Lastenausgleichsmodul wird verwendet, wenn Sie Ihre Anwendungen skalieren möchten. Zu den typischen Bereitstellungsszenarios zählen Anwendungen, die auf mehreren VM-Instanzen ausgeführt werden. Den VM-Instanzen ist ein Lastenausgleichsmodul vorgelagert, das hilft den Netzwerkdatenverkehr an die verschiedenen Instanzen zu verteilen.

![NICs auf einem einzelnen virtuellen Computer](./media/resource-groups-networking/Figure5.png)

Lastenausgleichsmodule enthalten die folgenden untergeordneten Ressourcen:

- **Front-End-IP-Konfiguration** – ein Lastenausgleichsmodul kann ein oder mehrere Front-End-IP-Adressen umschließen, auch als virtuelle IPs (VIPs) bekannt. Diese IP-Adressen dienen als Eingang für den Datenverkehr. 
- **Back-End-Adresspool** – IP-Adressen, die den VM NICs zugeordnet sind, an die Last verteilt wird.
- **Lastenausgleichsregeln** – eine Regeleigenschaft ordnet eine bestimmte Front-End-IP-/Port-Kombination einer Back-End-IP-Adressen-/Port-Kombination zu. Mit einer einzelnen Definition eines Lastenausgleichsmodul können Sie mehrere Lastenausgleichsregeln definieren, von denen jede eine Kombination aus Front-End-IP und Port sowie Back-End-IP und Port ist, die dem virtuellen Computer zugeordnet ist. 
- **Überprüfungen** – Mit Überprüfungen können Sie die Integrität der VM-Instanzen nachverfolgen. Schlägt eine Integritätsüberprüfung fehl, wird die VM-Instanz automatisch aus der Rotation entfernt.
- **Eingehende NAT-Regeln** – NAT-Regeln definieren den Eingangsdatenverkehr, der durch die Front-End-IP fließt und an die Back-End-IP verteilt wird.

<!---HONumber=Sept15_HO4-->