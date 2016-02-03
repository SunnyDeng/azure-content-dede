## Szenario

In diesem Dokument wird eine Bereitstellung veranschaulicht, die eine statische öffentliche IP-Adresse verwendet, die einer virtuellen Maschine (VM) zugeordnet ist. In diesem Szenario besitzen Sie eine einzelne VM mit ihrer eigenen statischen öffentlichen IP-Adresse. Die VM ist Teil des Subnetzes **FrontEnd** und verfügt zudem über eine statische private IP-Adresse (**192.168.1.101**) in diesem Subnetz.

Sie benötigen unter Umständen eine statische IP-Adresse für Webserver, für die SSL-Verbindungen erforderlich sind, bei denen das SSL-Zertifikat mit einer IP-Adresse verknüpft ist.

![BILDBESCHREIBUNG](./media/virtual-network-deploy-static-pip-scenario-include/figure1.png)

Sie können die folgenden Schritte ausführen, um die in der Abbildung oben gezeigte Umgebung bereitzustellen.

<!---HONumber=AcomDC_0114_2016-->