## Szenario

Um zu veranschaulichen, wie Sie benutzerdefinierte Routen erstellen, wird in diesem Dokument das folgende Szenario verwendet.

![BILDBESCHREIBUNG](./media/virtual-network-create-udr-scenario-include/figure1.png)

In diesem Szenario erstellen Sie eine benutzerdefinierte Route für das *Front-End-Subnetz* und eine weitere für das *Back-End-Subnetz*, wie im Folgenden beschrieben:

- **UDR-FrontEnd**. Die benutzerdefinierte Route für das Front-End wird auf das Subnetz *FrontEnd* angewendet und enthält eine Route:	
	- **RouteToBackend**. Diese Route sendet den gesamten an das Back-End-Subnetz gerichteten Datenverkehr an den virtuellen Computer **FW1**.
- **UDR-BackEnd**. Die benutzerdefinierte Route für das Front-End wird auf das Subnetz *FrontEnd* angewendet und enthält eine Route:	
	- **RouteToBackend**. Diese Route sendet den gesamten an das Back-End-Subnetz gerichteten Datenverkehr an den virtuellen Computer **FW1**.

Durch die Kombination dieser Routen wird sichergestellt, dass der gesamte Datenverkehr von einem Subnetz zu einem anderen an den virtuellen Computer **FW1** weitergeleitet wird, der als virtuelles Gerät verwendet wird. Sie müssen auch die IP-Weiterleitung für diesen virtuellen Computer aktivieren, damit er den an andere virtuelle Computer gerichteten Datenverkehr empfangen kann.

<!---HONumber=Oct15_HO2-->