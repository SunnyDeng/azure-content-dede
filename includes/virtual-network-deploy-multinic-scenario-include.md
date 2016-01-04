## Szenario

Dieses Dokument wird eine Bereitstellung schrittweise erläutert, in der in einem bestimmten Szenario mehrere NICs in virtuellen Computern verwendet werden. In diesem Szenario wird eine IaaS-Workload mit zwei Ebenen in Azure gehostet. Jede Ebene wird in einem eigenen Subnetz in einem virtuellen Netzwerk (VNet) bereitgestellt. Die Front-End-Ebene besteht aus mehreren Webservern, die in einem Load Balancer für hohe Verfügbarkeit gruppiert werden. Die Back-End-Ebene besteht aus mehreren Datenbankservern. Diese Datenbankserver werden mit jeweils zwei Netzwerkkarten bereitgestellt: eine Karte für den Datenbankzugriff und die andere Karte für die Verwaltung. Das Szenario umfasst auch Netzwerk-Sicherheitsgruppen (NSGs), die steuern, welcher Datenverkehr für jedes Subnetz und NIC in der Bereitstellung zulässig ist. Die folgende Abbildung zeigt die grundlegende Architektur dieses Szenarios.

![Multi-NIC-Szenario](./media/virtual-network-deploy-multinic-scenario-include/Figure1.png)

<!---HONumber=Nov15_HO4-->