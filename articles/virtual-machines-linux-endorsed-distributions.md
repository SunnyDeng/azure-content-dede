<properties linkid="manage-linux-other-resources-endorsed-distributions" urlDisplayName="Endorsed distributions" pageTitle="Endorsed distributions of Linux in Azure" metaKeywords="" description="Learn about Linux on Azure-endorsed distributions, including guidelines for Ubuntu, OpenLogic, and SUSE." metaCanonical="" services="virtual-machines" documentationCenter="" title="Linux on Azure-Endorsed Distributions" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Linux auf von Azure unterstützten Distributionen

Die Distributionsimages im Azure-Katalog werden von den folgenden Partnern zur Verfügung gestellt. Außerdem arbeiten wir mit den verschiedenen Linux-Communitys daran, weitere unterstützte Distributionen bereitzustellen. In der Zwischenzeit können Sie bei Distributionen, die nicht in der Galerie verfügbar sind, stets Ihre eigene Linux-Distribution bereitstellen ("Bring Your Own"), indem Sie die Richtlinien auf [dieser Seite][dieser Seite] befolgen.

## Canonical

[][]<http://www.ubuntu.com/cloud/azure></a>

Engineering und Open Community Governance von Canonical steigern den Erfolg von Ubuntu bei Clients, Servern und Cloud Computing, einschließlich persönlicher Cloud-Dienste für Endkunden. Canonicals Vision einer einheitlichen kostenlosen Plattform in Ubuntu, vom Telefon bis hin zur Cloud, mit einer Reihe kohärenter Schnittstellen für Telefon, Tablet, TV und Desktop macht Ubuntu zur ersten Wahl für vielfältige Institutionen – von öffentlichen Cloudanbietern bis hin zu Herstellern von Unterhaltungselektronik – sowie zu einem Favoriten für individuelle Techniker.

Mit Entwicklern und Engineering Centers überall auf der Welt ist Canonical hervorragend positioniert, um Partnerschaften mit Hardwareherstellern, Inhaltsanbietern und Softwareentwicklern einzugehen, um Ubuntu-Lösungen auf den Markt zu bringen – von PCs bis hin zu Servern und portablen Geräten.

## OpenLogic

[][1]<http://www.openlogic.com/azure></a>

OpenLogic ist ein führender Anbieter von Open Source-Lösungen für Unternehmen für die Cloud und das Rechenzentrum. OpenLogic hilft Hunderten führenden Unternehmen in zahlreichen Industriezweigen beim sicheren Erwerb, der Unterstützung sowie Steuerung von Open Source-Software. OpenLogic bietet handelsüblichen technischen Support und Schadenersatzleistungen für 600 von der OpenLogic Expert Community unterstützte Open Source-Pakete, einschließlich professionellem Support für CentOS, und ist zugleich der Launch-Partner für die Bereitstellung von CentOS-Images unter Azure.

## Oracle

[][2]<http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html></a>

Die Strategie von Oracle besteht darin, ein weitgefasstes Portfolio von Lösungen für öffentliche und private Clouds anzubieten und den Kunden zugleich Wahlmöglichkeiten und Flexibilität bei der Art und Weise zu geben, wie sie Oracle-Software in Oracle-Clouds und anderen Clouds bereitstellen. Aufgrund der Partnerschaft zwischen Oracle und Microsoft haben Kunden die Möglichkeit, Oracle-Software in öffentlichen und privaten Clouds von Microsoft bereitzustellen und können dabei auf die Zertifizierung und den Support von Oracle vertrauen. Oracles Engagement und die Investition in öffentliche und private Cloudlösungen von Oracle bleibt unverändert.

## SUSE

[][3]<http://www.suse.com/suse-linux-enterprise-server-on-azure></a>

SUSE Linux Enterprise Server unter Azure ist eine bewährte Plattform, die hervorragende Zuverlässigkeit und Sicherheit für Cloud Computing bietet. Die vielseitige Linux-Plattform SUSE lässt sich nahtlos in Azure-Cloud-Dienste integrieren, um eine einfach zu verwaltende Cloudumgebung bereitzustellen. Mit mehr als 9.200 zertifizierten Anwendungen von über 1.800 unabhängigen Softwareanbietern für SUSE Linux Enterprise Server stellt SUSE zudem sicher, dass vorhandene im Rechenzentrum unterstützte Arbeitsauslastungen vertrauensvoll unter Azure bereitgestellt werden können.

## Unterstützte Versionen

Die folgende Tabelle enthält die verschiedenen Distributionsversionen, LIS-Treiber (Linux Integration Services) sowie Azure Linux Agent-Versionen, die für den Einsatz unter Azure getestet wurden. LIS-Treiber sind entweder standardmäßig in den Kernel der Distribution integriert oder stehen [hier][hier] zur Verfügung. Linux-Agent-Versionen stehen im Repository des Pakets der Distribution oder auf [Github][Github] zur Verfügung.

Die Tabelle enthält zudem einen Link zum [Kernel-Kompatibilitätspatch][Kernel-Kompatibilitätspatch], das von einigen Distributions-/Kernelversionen für eine optimale Funktion in Azure benötigt wird.

<table border="1" width="600">
<tr bgcolor="#E9E7E7">
<th>
Distribution

</th>
<th>
Version

</th>
<th>
Treiber

</th>
<th>
Kernel-Kompatibilitätspatch

</th>
<th>
Agent

</th>
</tr>
<tr>
<th>
Canonical Ubuntu

</th>
<td>
Ubuntu 12.04.1+, 13.10 und 14.04

</td>
<td>
Im Kernel

</td>
<td>
[Erforderlich nur für 12.04 oder 12.04.01][Erforderlich nur für 12.04 oder 12.04.01]

</td>
<td>
Paket: Im Paketrepository unter walinuxagent
: Quelle: [GitHub][GitHub]

</td>
</tr>
<tr>
<th>
CentOS by OpenLogic

</th>
<td>
CentOS 6.3+

</td>
<td>
CentOS 6.3: [LIS-Treiber][hier], CentOS 6.4+-Treiber: Im Kernel

</td>
<td>
[Erforderlich nur für 6.3][Erforderlich nur für 6.3]

</td>
<td>
Paket: Im [Open Logic-Paketrepository][Open Logic-Paketrepository] unter walinuxagent
 Quelle: [GitHub][GitHub]

</td>
</tr>
<tr>
<th>
Oracle Linux

</th>
<td>
6.4+

</td>
<td>
Im Kernel

</td>
<td>
N/V

</td>
<td>
Paket: Im Repository, Name: WALinuxAgent
 Quelle: [GitHub][GitHub]

</td>
</tr>
<tr>
<th>
SUSE Linux Enterprise

</th>
<td>
SLES 11 SP3+

</td>
<td>
Im Kernel

</td>
<td>
N/V

</td>
<td>
Paket: Im [Cloud:Tools][Cloud:Tools]-Repository, Name: WALinuxAgent
 Quellcode: [GitHub][GitHub]

</td>
</tr>
<tr>
<th>
openSUSE

</th>
<td>
openSUSE 13.1+

</td>
<td>
Im Kernel

</td>
<td>
N/V

</td>
<td>
Paket: Im [Cloud:Tools][Cloud:Tools]-Repository, Name: WALinuxAgent
 Quellcode: [GitHub][GitHub]

</td>
</tr>
</table>

  [dieser Seite]: ../virtual-machines-linux-create-upload-vhd/
  []: http://www.ubuntu.com/cloud/azure
  [1]: http://www.openlogic.com/azure
  [2]: http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html
  [3]: http://www.suse.com/suse-linux-enterprise-server-on-azure
  [hier]: http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409
  [Github]: https://github.com/azure/walinuxagent
  [Kernel-Kompatibilitätspatch]: http://go.microsoft.com/fwlink/?LinkID=403027&clcid=0x409
  [Erforderlich nur für 12.04 oder 12.04.01]: http://go.microsoft.com/fwlink/?LinkID=275152&clcid=0x409
  [GitHub]: http://go.microsoft.com/fwlink/p/?LinkID=250998
  [Erforderlich nur für 6.3]: http://go.microsoft.com/fwlink/?LinkID=275153&clcid=0x409
  [Open Logic-Paketrepository]: http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/
  [Cloud:Tools]: https://build.opensuse.org/project/show/Cloud:Tools
