<properties 
	pageTitle="Unterstützte Verteilungen von Linux in Azure" 
	description="Erfahren Sie mehr über unterstützte Linux-Verteilungen auf Azure, einschließlich Leitfäden für Ubuntu, OpenLogic und SUSE." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="szarkos" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="szark"/>



#Linux auf von Azure unterstützten Verteilungen

Die Linux-Images im Azure-Katalog werden von verschiedenen Partnern zur Verfügung gestellt. Außerdem arbeiten wir mit verschiedenen Linux-Communitys daran, die Liste der unterstützten Verteilungen noch vielseitiger zu gestalten. In der Zwischenzeit können Sie für Verteilungen, die im Katalog nicht verfügbar sind, Ihre eigene Linux-Images bereitstellen ("Bring Your Own"), indem Sie die Richtlinien [auf dieser Seite](virtual-machines-linux-create-upload-vhd.md) befolgen


## Unterstützte Verteilungen und Versionen ##

In der folgenden Tabelle finden Sie die auf Azure unterstützten Linux-Verteilungen und -Versionen.

Die Treiber für die Linux-Integrationsdienste (Linux Integration Services, LIS) für Hyper-V und Azure sind Kernelmodule, die Microsoft direkt für den Linux-Upstream-Kernel bereitstellt. Die LIS-Treiber sind entweder standardmäßig in den Kernel der Verteilung integriert oder stehen für ältere RHEL/CentOS-basierte Verteilungen [hier](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) als separater Download zur Verfügung. Weitere Informationen zu LIS-Treibern finden Sie [in diesem Artikel](virtual-machines-linux-create-upload-vhd-generic.md#linux-kernel-requirements).

Der Azure Linux-Agent ist bereits in den Images im Azure-Katalog vorinstalliert und i. d. R. im Paketrepository der Distribution verfügbar. Quellcode finden Sie unter [GitHub](https://github.com/azure/walinuxagent).

<table border="1" width="600">
  <tr bgcolor="#E9E7E7">
		<th>Verteilung</th>		
	    <th>Version</th>
	    <th>Treiber</th>
		<th>Agent</th>
			</tr>
	<tr>
		<th>  Ubuntu von Canonical </th>
		<td> Ubuntu 12.04, 14.04, 14.10 und 15.04 </td>
		<td>Im Kernel</td>
		<td>Paket: Im Repository unter "walinuxagent" <br />
			Quelle: <a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a></td>
			</tr>
	<tr>
		<th> CentOS von OpenLogic </th>
		<td> CentOS 6.3+, 7.0+</td>
	    <td> CentOS 6.3: <a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409">LIS-Download</a><br/>
	        CentOS 6.4+: Im Kernel</td>
		<td>Paket: Im <a href="http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/">Open Logic-Repository</a> unter "WALinuxAgent"<br />
			Quelle: <a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a></td>
 		
	</tr>
	<tr>
		<th> <a href="https://coreos.com/docs/running-coreos/cloud-providers/azure/">CoreOS</a> </th>
		<td> 494.4.0+ </td>
        <td> Im Kernel </td>
		<td> Quelle: <a href="https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent">GitHub</a></td>
		
	</tr>
	<tr>
		<th> Oracle Linux </th>
		<td> 6.4+, 7.0+ </td>
        <td>Im Kernel</td>
		<td>Paket: Im Repository unter "WALinuxAgent"<br />
			Quelle: <a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a></td>
		
	</tr>
	<tr>
		<th> SUSE Linux Enterprise </th>
		<td> SLES 11 SP3 oder höher, SLES 12 oder höher und <br /> SLES für SAP 11.3 oder höher </td>
        <td>Im Kernel</td>
		<td>Paket: Im <a href="https://build.opensuse.org/project/show/Cloud:Tools">Cloud:Tools</a>-Repository unter "WALinuxAgent"<br />
			Quelle: <a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a></td>
		
	</tr>
	<tr>
		<th> openSUSE </th>
		<td> openSUSE 13.1+</td>
		<td>Im Kernel</td>
		<td>Paket: Im <a href="https://build.opensuse.org/project/show/Cloud:Tools" >Cloud:Tools</a>-Repository unter "WALinuxAgent"<br />
			Quellcode: <a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a></td>
		
	</tr>
</table>

## Partner

### Canonical
[http://www.ubuntu.com/cloud/azure](http://www.ubuntu.com/cloud/azure)

Engineering und Open Community Governance von Canonical steigern den Erfolg von Ubuntu bei Clients, Servern und Cloud Computing, einschließlich persönlicher Clouddienste für Endkunden. Canonicals Vision einer einheitlichen kostenlosen Plattform in Ubuntu, vom Telefon bis hin zur Cloud, mit einer Reihe kohärenter Schnittstellen für Telefon, Tablet, TV und Desktop macht Ubuntu zur ersten Wahl für vielfältige Institutionen – von öffentlichen Cloudanbietern bis hin zu Herstellern von Unterhaltungselektronik – sowie zu einem Favoriten für individuelle Techniker.

Mit Entwicklern und Engineering Centers überall auf der Welt ist Canonical hervorragend positioniert, um Partnerschaften mit Hardwareherstellern, Inhaltsanbietern und Softwareentwicklern einzugehen, um Ubuntu-Lösungen auf den Markt zu bringen – von PCs bis hin zu Servern und portablen Geräten.


### CoreOS
[https://coreos.com/docs/running-coreos/cloud-providers/azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

Von der CoreOS-Website:

*CoreOS steht für Sicherheit, Konsistenz und Zuverlässigkeit. CoreOS installiert Paketen nicht über yum oder apt, sondern verwaltet Ihre Dienste mithilfe von Linux-Containern mit einem höheren Maß an Abstraktion. Der Code eines einzelnen Diensts und alle Abhängigkeiten sind in einem Container verpackt, der auf einem oder mehreren CoreOS-Computern ausgeführt werden kann.*


### OpenLogic
[http://www.openlogic.com/azure](http://www.openlogic.com/azure)

OpenLogic ist ein führender Anbieter von Open Source-Lösungen für Unternehmen für die Cloud und das Rechenzentrum. OpenLogic hilft Hunderten führenden Unternehmen in zahlreichen Industriezweigen beim sicheren Erwerb, der Unterstützung sowie Steuerung von Open Source-Software. OpenLogic bietet handelsüblichen technischen Support und Schadenersatzleistungen für 600 von der OpenLogic Expert Community unterstützte Open Source-Pakete, einschließlich professionellem Support für CentOS, und ist zugleich der Launch-Partner für die Bereitstellung von CentOS-Images unter Azure.


### Oracle
[http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Die Strategie von Oracle besteht darin, ein weitgefasstes Portfolio von Lösungen für öffentliche und private Clouds anzubieten und den Kunden zugleich Wahlmöglichkeiten und Flexibilität bei der Art und Weise zu geben, wie sie Oracle-Software in Oracle-Clouds und anderen Clouds bereitstellen. Aufgrund der Partnerschaft zwischen Oracle und Microsoft haben Kunden die Möglichkeit, Oracle-Software in öffentlichen und privaten Clouds von Microsoft bereitzustellen und können dabei auf die Zertifizierung und den Support von Oracle vertrauen. Das Engagement von Oracle und die Investition in öffentliche und private Cloudlösungen von Oracle bleibt unverändert.


### SUSE
[http://www.suse.com/suse-linux-enterprise-server-on-azure](http://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server unter Azure ist eine bewährte Plattform, die hervorragende Zuverlässigkeit und Sicherheit für Cloud Computing bietet. Die vielseitige Linux-Plattform SUSE lässt sich nahtlos in Azure-Cloud-Dienste integrieren, um eine einfach zu verwaltende Cloudumgebung bereitzustellen. Mit mehr als 9.200 zertifizierten Anwendungen von über 1.800 unabhängigen Softwareanbietern für SUSE Linux Enterprise Server stellt SUSE zudem sicher, dass vorhandene im Rechenzentrum unterstützte Arbeitsauslastungen vertrauensvoll unter Azure bereitgestellt werden können.

 

<!---HONumber=July15_HO1-->