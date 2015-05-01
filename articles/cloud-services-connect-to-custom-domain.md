<properties
  pageTitle="Verbinden von Azure Cloud Services-Rollen mit einem in Azure gehosteten AD-Domänencontroller"
  description="Erfahren Sie, wie Sie mithilfe von PowerShell und der AD-Domänenerweiterung eine Verbindung zwischen Ihren Web-/Workerrollen und einer benutzerdefinierten AD-Domäne herstellen."
  services="cloud-services"
  documentationCenter=""
  authors="VMak"
  manager="MadhanA"
  editor=""/>

  <tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="3/5/2015"
    ms.author="vmaker"/>

# Verbinden von Azure Cloud Services-Rollen mit einem in Azure gehosteten AD-Domänencontroller

## Schrittanleitung zum Herstellen einer Verbindung zwischen Ihren Azure-Web-/Workerrollen mit einem in Azure gehosteten benutzerdefinierten Domänencontroller

Zunächst richten wir ein Virtual Network (VNET) in Azure ein. Anschließend fügen wir dem VNET einen Active Directory-Domänencontroller (gehostet auf einem virtuellen Azure-Computer) hinzu. Danach fügen wir dem erstellten VNET vorhandene Cloud-Dienst-Rollen hinzu und verbinden diese mit dem Domänencontroller.

Bedenken Sie vor dem Start Folgendes:
1.	In diesem Lernprogramm wird PowerShell verwendet, stellen Sie also sicher, dass Azure PowerShell installiert und einsatzbereit ist. Unterstützung bei der Einrichtung von Azure PowerShell finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](install-configure-powershell.md).
2.	Ihr AD-Domänencontroller und Ihre Web-/Workerrollen müssen sich im VNET befinden.

Befolgen Sie diese Schrittanleitung, und fügen Sie unten einen Kommentar ein, falls Probleme auftreten sollten. Wir werden uns mit Ihnen in Verbindung setzen (wir lesen die Kommentare wirklich).

## Erstellen eines virtuellen Netzwerks

Sie können ein Virtual Network in Azure über PowerShell oder das Azure-Portal erstellen. In diesem Lernprogramm wird PowerShell verwendet. Informationen zum Erstellen eines Virtual Network über das Azure-Portal finden Sie unter [Erstellen eines virtuellen Netzwerks](create-virtual-network.md).

    #Create Virtual Network

    $vnetStr =
    @"<?xml version="1.0" encoding="utf-8"?>
    <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="[your-vnet-name]" Location="West US">
            <AddressSpace>
              <AddressPrefix>[your-address-prefix]</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="[your-subnet-name]">
                <AddressPrefix>[your-subnet-range]</AddressPrefix>
              </Subnet>
            </Subnets>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>
    "@;

    $vnetConfigPath = "<path-to-vnet-config>"
    Set-AzureVNetConfig -ConfigurationPath $vnetConfigPath;

## Erstellen eines virtuellen Computers

Nachdem Sie das Virtual Network fertig eingerichtet haben, müssen Sie einen AD-Domänencontroller erstellen. In diesem Lernprogramm richten wir einen AD-Domänencontroller auf einem virtuellen Azure-Computer ein.

Erstellen Sie zu diesem Zweck über PowerShell mithilfe der unten stehenden Befehle einen virtuellen Computer.

    #Initialize variables

    $vnetname = '<your-vnet-name>'
    $subnetname = '<your-subnet-name>'
    $vmsvc1 = '<your-hosted-service>'
    $vm1 = '<your-vm-name>'
    $username = '<your-username>'
    $password = '<your-password>'
    $ affgrp = '<your- affgrp>'

    #Create a VM and add it to the Virtual Network

    New-AzureQuickVM -Windows -ServiceName $vmsvc1 -name $vm1 -ImageName $imgname -AdminUsername $username -Password $password -AffinityGroup $affgrp -SubnetNames $subnetname -VNetName $vnetname


## Heraufstufen des virtuellen Computers zu einem Domänencontroller
Sie müssen sich am virtuellen Computer anmelden und diesen konfigurieren, um ihn als AD-Domänencontroller zu konfigurieren.

Zur Anmeldung am virtuellen Computer können Sie die RDP-Datei über PowerShell mithilfe der unten stehenden Befehle abrufen.

    #Get RDP file

    Get-AzureRemoteDesktopFile -ServiceName $vmsvc1 -Name $vm1 -LocalPath <rdp-file-path>

Nachdem Sie am virtuellen Computer angemeldet sind, richten Sie ihn als AD-Domänencontroller ein, indem Sie den Schrittanleitungen unter [Einrichten eines benutzerdefinierten AD-Domänencontrollers](http://social.technet.microsoft.com/wiki/contents/articles/12370.windows-server-2012-set-up-your-first-domain-controller-step-by-step.aspx) folgen.

## Hinzufügen der Cloud-Dienst-Bereitstellung zum Virtual Network

Als Nächstes müssen Sie Ihre Cloud-Dienst-Bereitstellung zu dem VNET hinzufügen, das Sie gerade erstellt haben. Bearbeiten Sie zu diesem Zweck die CSCFG-Datei Ihres Cloud-Diensts, indem Sie die relevanten Abschnitte mithilfe von Visual Studio oder einem Editor Ihrer Wahl hinzufügen.

    <ServiceConfiguration serviceName="[hosted-service-name]" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="[os-family]" osVersion="*">
        <Role name="[role-name]">
        <Instances count="[number-of-instances]" />
      </Role>
      <NetworkConfiguration>

        <!--optional-->
        <Dns>
          <DnsServers><DnsServer name="[dns-server-name]" IPAddress="[ip-address]" /></DnsServers>
        </Dns>
        <!--optional-->

        <!--VNET settings-->
        <VirtualNetworkSite name="[virtual-network-name]" />
        <AddressAssignments>
          <InstanceAddress roleName="[role-name]">
            <Subnets>
              <Subnet name="[subnet-name]" />
            </Subnets>
          </InstanceAddress>
        </AddressAssignments>
        <!--VNET settings-->

      </NetworkConfiguration>
    </ServiceConfiguration>

Anschließend erstellen Sie Ihr Cloud-Dienst-Projekt und stellen es in Azure bereit. Unterstützung bei der Bereitstellung Ihres Cloud-Dienst-Pakets in Azure erhalten Sie unter [Erstellen und Bereitstellen eines Cloud-Diensts](cloud-services-how-to-create-deploy.md#deploy)

## Herstellen einer Verbindung zwischen Web-/Workerrollen und der benutzerdefinierten Domäne über die AD-Domänenerweiterung

Sobald Ihr Cloud-Dienst-Projekt auf Azure bereitgestellt ist, verbinden Sie Ihre Rolleninstanzen mithilfe der AD-Domänenerweiterung mit der benutzerdefinierten AD-Domäne. Um die AD-Domänenerweiterung zu Ihrer vorhandenen Cloud-Dienst-Bereitstellung hinzuzufügen und der benutzerdefinierten Domäne beizutreten, führen Sie folgende Befehle in PowerShell aus:

    #Initialize domain variables

    $domain = '<your-domain-name>';
    $dmuser = '$domain\<your-username>';
    $dmpswd = '<your-domain-password>';
    $dmspwd = ConvertTo-SecureString $dmpswd -AsPlainText -Force;
    $dmcred = New-Object System.Management.Automation.PSCredential ($dmuser, $dmspwd);

    #Add AD Domain Extension to the cloud service roles

    Set-AzureServiceADDomainExtension -Service <your-cloud-service-hosted-service-name> -Role <your-role-name> -Slot <staging-or-production> -DomainName $domain -Credential $dmcred -JoinOption 35;

Und das ist auch schon alles.

Ihre Cloud-Dienste sollten nun Ihrem benutzerdefinierten Domänencontroller beigetreten sein. Wenn Sie mehr über die verschiedenen verfügbaren Optionen zur Konfiguration der AD-Domänenerweiterung erfahren möchten, nutzen Sie die PowerShell-Hilfe, wie unten gezeigt.

    help Set-AzureServiceADDomainExtension;
    help New-AzureServiceADDomainExtensionConfig;

Wir möchten gerne von Ihnen wissen, ob Sie eine Erweiterung nützlich fänden, mit der ein virtueller Computer zu einem Domänencontroller heraufgestuft werden kann. Wenn Sie also der Meinung sind, eine solche Erweiterung wäre nützlich, geben Sie uns bitte Feedback im Kommentarbereich.

Wir hoffen, Sie fanden diesen Artikel hilfreich!


<!--HONumber=52-->