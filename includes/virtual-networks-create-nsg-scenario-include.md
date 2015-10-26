## Szenario

Um zu veranschaulichen, wie Sie NSGs erstellen, wird in diesem Dokument das folgende Szenario verwendet.

![VNet-Szenario](./media/virtual-networks-create-nsg-scenario-include/figure1.png)

In diesem Szenario erstellen Sie eine NSG für jedes Subnetz im virtuellen Netzwerk **TestVNet**, wie unten beschrieben:

- **NSG-FrontEnd**. Die Front-End-NSG wird auf das Subnetz *FrontEnd* angewendet und enthält zwei Regeln:	
	- **rdp-rule**. Diese Regel ermöglicht RDP-Datenverkehr in das Subnetz *FrontEnd*.
	- **web-rule**. Diese Regel ermöglicht HTTP-Datenverkehr in das Subnetz *FrontEnd*.
- **NSG-BackEnd**. Die Back-End-NSG wird auf das Subnetz *BackEnd* angewendet und enthält zwei Regeln:	
	- **sql-rule**. Mit dieser Regel ist nur SQL-Verkehr aus dem Subnetz *FrontEnd* zulässig.
	- **web-rule**. Diese Regel verweigert dem gesamten Datenverkehr aus dem Subnetz *BackEnd* ins Internet.

Durch die Kombination dieser Regeln wird ein DMZ-ähnliches Szenario erstellt, in dem das Back-End-Subnetz nur eingehenden Datenverkehr für SQL-Datenverkehr aus dem Front-End-Subnetz empfangen kann und keinen Zugriff auf das Internet hat, während das Front-End-Subnetz mit dem Internet kommunizieren und nur eingehende HTTP-Anforderungen empfangen kann.
 

<!---HONumber=Oct15_HO3-->