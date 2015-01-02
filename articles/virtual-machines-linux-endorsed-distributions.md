<properties urlDisplayName="Endorsed distributions" pageTitle="Bestätigte Distributionen von Linux in Azure" metaKeywords="" description="Learn about Linux on Azure-endorsed distributions, including guidelines for Ubuntu, OpenLogic, and SUSE." metaCanonical="" services="virtual-machines" documentationCenter="" title="Linux on Azure-Endorsed Distributions" authors="szark" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="12/05/2014" ms.author="szark" />





#Linux auf Azure - unterstützte Distributionen

Die Distributionsimages im Azure-Katalog werden von den folgenden Partnern zur Verfügung gestellt. Außerdem arbeiten wir mit verschiedenen Linux-Communitys daran, noch mehr bestätigte Distributionen bereitzustellen. In der Zwischenzeit können Sie für Distributionen, die im Katalog nicht verfügbar sind, stets Ihre eigene Linux-Distribution bereitstellen ("Bring Your Own"), indem Sie die Richtlinien auf [dieser Seite] befolgen(../virtual-machines-linux-create-upload-vhd/).

## Canonical ##
 
[http://www.ubuntu.com/cloud/azure](http://www.ubuntu.com/cloud/azure)

Engineering und Open Community Governance von Canonical steigern den Erfolg von Ubuntu bei Clients, Servern und Cloud Computing, einschließlich persönlicher Cloud-Dienste für Endkunden. Canonicals Vision einer einheitlichen kostenlosen Plattform in Ubuntu, vom Telefon bis hin zur Cloud, mit einer Reihe kohärenter Schnittstellen für Telefon, Tablet, TV und Desktop macht Ubuntu zur ersten Wahl für vielfältige Institutionen - von öffentlichen Cloudanbietern bis hin zu Herstellern von Unterhaltungselektronik - sowie zu einem Favoriten für individuelle Techniker.

Mit Entwicklern und Engineering Centers überall auf der Welt ist Canonical hervorragend positioniert, um Partnerschaften mit Hardwareherstellern, Inhaltsanbietern und Softwareentwicklern einzugehen, um Ubuntu-Lösungen auf den Markt zu bringen - von PCs bis hin zu Servern und portablen Geräten.

## OpenLogic ##
 
[http://www.openlogic.com/azure](http://www.openlogic.com/azure)

OpenLogic ist ein führender Anbieter von Open Source-Lösungen für Unternehmen für die Cloud und das Rechenzentrum. OpenLogic hilft Hunderten führenden Unternehmen in zahlreichen Industriezweigen beim sicheren Erwerb, der Unterstützung sowie Steuerung von Open Source-Software. OpenLogic bietet handelsüblichen technischen Support und Schadenersatzleistungen für 600 von der OpenLogic Expert Community unterstützte Open Source-Pakete, einschließlich des professionellen Supports für CentOS, und ist zugleich der Launch-Partner für die Bereitstellung von CentOS-basierten Images unter Azure.

## Oracle##
[http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Die Strategie von Oracle besteht darin, ein weitgefasstes Portfolio von Lösungen für öffentliche und private Clouds anzubieten und den Kunden zugleich Wahlmöglichkeiten und Flexibilität bei der Art und Weise zu geben, wie sie Oracle-Software in Oracle-Clouds und anderen Clouds bereitstellen.  Aufgrund der Partnerschaft zwischen Oracle und Microsoft haben Kunden die Möglichkeit, Oracle-Software in öffentlichen und privaten Clouds von Microsoft bereitzustellen und können dabei auf die Zertifizierung und den Support von Oracle vertrauen.  Das Engagement und die Investition von Oracle in öffentliche und private Cloud-Lösungen von Oracle bleiben unverändert.

##SUSE##
 
[http://www.suse.com/suse-linux-enterprise-server-on-azure](http://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server unter Azure ist eine bewährte Plattform, die hervorragende Zuverlässigkeit und Sicherheit für Cloud Computing bietet. Die vielseitige Linux-Plattform SUSE lässt sich nahtlos in Azure-Cloud-Dienste integrieren, um eine einfach zu verwaltende Cloudumgebung bereitzustellen. Mit mehr als 9.200 zertifizierten Anwendungen von über 1.800 unabhängigen Softwareanbietern für SUSE Linux Enterprise Server stellt SUSE zudem sicher, dass vorhandene im Rechenzentrum unterstützte Arbeitsauslastungen vertrauensvoll unter Azure bereitgestellt werden können.

## Unterstützte Versionen #

Die folgende Tabelle enthält die verschiedenen Distributionsversionen, LIS-Treiber (Linux Integration Services) sowie Azure Linux Agent-Versionen, die für den Einsatz unter Azure getestet wurden. LIS-Treiber sind entweder standardmäßig im Kernel der Distribution integriert oder [hier] verfügbar(http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409). Linux-Agent-Versionen sind im Paketrepository der Distribution oder auf [Github] verfügbar(https://github.com/azure/walinuxagent).

In der Tabelle ist auch ein Lin zum [Linux-Kernel-Kompatibilitätspatch](http://go.microsoft.com/fwlink/?LinkID=403027&clcid=0x409) enthalten, den einige Distributions-/Kernelversionen für korrektes Arbeiten benötigen. 

<table border="1" width="600">
  <tr bgcolor="#E9E7E7">
		<th>Distribution</th>		
	    <th>Version</th>
	    <th>Treiber</th>
		<th>Kernel-Kompatibilitätspatch</th>
		<th>Agent</th>
			</tr>
	<tr>
		<th>  Ubuntu von Canonical </th>
		<td> Ubuntu 12.04.1+, 14.04 & 14.10 </td>
		<td>Im Kernel</td>
		<td><a href="http://go.microsoft.com/fwlink/?LinkID=275152&clcid=0x409">Erforderlich nur für 12.04 oder 12.04.01</a></td>
		<td>Paket: Im Paketrepository unter walinuxagent <br />
			Source: <a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a></td>
			</tr>
	<tr>
		<th> CentOS von OpenLogic </th>
		<td> CentOS 6.3+</td>
	    <td>CentOS 6.3: <a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409">LIS-Treiber</a>; CentOS 6.4+-Treiber: im Kernel</td>
		<td><a href="http://go.microsoft.com/fwlink/?LinkID=275153&clcid=0x409">Erforderlich nur für 6.3</a></td>
		<td>Paket: Im <a href="http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/">Open Logic-Paketrepository </a> unter walinuxagent<br />
			Source: <a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a></td>
 		
	</tr>
	<tr>
		<th> <a href="https://coreos.com/docs/running-coreos/cloud-providers/azure/">CoreOS</a> </th>
		<td> 494.4.0+ </td>
        <td> Im Kernel </td>
		<td> N/V </td>
		<td> Source: <a href="https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent">GitHub</a></td>
		
	</tr>
	<tr>
		<th> Oracle Linux </th>
		<td> 6.4+</td>
        <td>Im Kernel</td>
		<td>N/V</td>
		<td>Paket: Im Repository, Name: WALinuxAgent<br />
			Source: <a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a></td>
		
	</tr>
	<tr>
		<th> SUSE Linux Enterprise </th>
		<td> SLES 11 SP3+</td>
        <td>Im Kernel</td>
		<td>N/V</td>
		<td>Paket: Geben Sie in <a href="https://build.opensuse.org/project/show/Cloud:Tools" >Cloud:Tools</a> repo, name: WALinuxAgent<br />
			Source Code: <a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a></td>
		
	</tr>
	<tr>
		<th> openSUSE </th>
		<td> openSUSE 13.1+</td>
		<td>Im Kernel</td>
		<td>N/V</td>
		<td>Paket: Geben Sie in <a href="https://build.opensuse.org/project/show/Cloud:Tools" >Cloud:Tools</a> repo, name: WALinuxAgent<br />
			Source Code: <a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a></td>
		
	</tr>
</table>


<!--HONumber=35_1-->
