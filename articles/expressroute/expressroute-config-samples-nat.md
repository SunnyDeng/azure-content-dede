<properties
   pageTitle="ExpressRoute-Beispiele für die Konfiguration von Kundenroutern | Microsoft Azure"
   description="Diese Seite enthält Konfigurationsbeispiele für Router von Cisco und Juniper."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/16/2016"
   ms.author="cherylmc"/>

# Beispiele für die Routerkonfiguration zum Einrichten und Verwalten von NAT (Network Address Translation, Netzwerkadressübersetzung)

Diese Seite enthält NAT-Konfigurationsbeispiele für Router der Serien Cisco ASA und Juniper SRX. Diese Beispiele dienen nur als Leitfaden und müssen nicht wie angegeben verwendet werden. Sie können mit Ihrem Anbieter an der Ausarbeitung geeigneter Konfigurationen für Ihr Netzwerk zusammenarbeiten.

>[AZURE.IMPORTANT] Die Beispiele auf dieser Seite sind ausschließlich als Leitfaden konzipiert. Sie müssen mit dem Vertriebsteam/Technikteam Ihres Anbieters und Ihrem Netzwerkteam zusammenarbeiten, um für Ihre Anforderungen geeignete Konfigurationen zu bestimmen. Microsoft bietet keine Unterstützung bei Problemen im Zusammenhang mit Konfigurationen, die auf dieser Seite aufgeführt sind. Sie müssen sich bei Problemen an den Gerätehersteller wenden.

Die nachstehenden Beispiele für die Routerkonfiguration gelten für Azure Public- und Microsoft-Peerings. Sie dürfen NAT nicht für privates Azure-Peering konfigurieren. Unter [ExpressRoute-Peerings](expressroute-circuit-peerings.md) und [NAT-Anforderungen für ExpressRoute](expressroute-nat.md) finden Sie weitere Details.

**Hinweis:** Sie müssen separate NAT-IP-Adresspools für Konnektivität mit dem Internet und ExpressRoute verwenden. Das Verwenden des gleichen NAT-IP-Adresspools für Internet und ExpressRoute führt zu einem asymmetrischen Routing und Verlust der Konnektivität.

## Cisco ASA-Firewalls

### PAT-Konfiguration für den Datenverkehr von Kundennetzwerk zu Microsoft

    object network MSFT-PAT
      range <SNAT-START-IP> <SNAT-END-IP>
    
    
    object-group network MSFT-Range
      network-object <IP> <Subnet_Mask>
    
    object-group network on-prem-range-1
      network-object <IP> <Subnet-Mask>
    
    object-group network on-prem-range-2
      network-object <IP> <Subnet-Mask>
    
    object-group network on-prem
      network-object object on-prem-range-1
      network-object object on-prem-range-2
    
    nat (outside,inside) source dynamic on-prem pat-pool MSFT-PAT destination static MSFT-Range MSFT-Range

### PAT-Konfiguration für den Datenverkehr von Microsoft zum Kundennetzwerk

#### Schnittstellen und Richtung:
	Source Interface (where the traffic enters the ASA): inside
	Destination Interface (where the traffic exits the ASA): outside

#### Konfiguration:
NAT-Pool:

	object network outbound-PAT
		host <NAT-IP>

Zielserver:

	object network Customer-Network
		network-object <IP> <Subnet-Mask>

Objektgruppe für Kunden-IP-Adressen

	object-group network MSFT-Network-1
		network-object <MSFT-IP> <Subnet-Mask>
	
	object-group network MSFT-PAT-Networks
		network-object object MSFT-Network-1

NAT-Befehle:

	nat (inside,outside) source dynamic MSFT-PAT-Networks pat-pool outbound-PAT destination static Customer-Network Customer-Network


## Router der Juniper SRX-Serie 

### 1\. Erstellen redundanter Ethernet-Schnittstellen für den Cluster

	interfaces {
	    reth0 {
	        description "To Internal Network";
	        vlan-tagging;
	        redundant-ether-options {
	            redundancy-group 1;
	        }
	        unit 100 {
	            vlan-id 100;
	            family inet {
	                address <IP-Address/Subnet-mask>;
	            }
	        }
	    }
	    reth1 {
	        description "To Microsoft via Edge Router";
	        vlan-tagging;
	        redundant-ether-options {
	            redundancy-group 2;
	        }
	        unit 100 {
	            description "To Microsoft via Edge Router";
	            vlan-id 100;
	            family inet {
	                address <IP-Address/Subnet-mask>;
	            }
	        }
	    }
	}


### 2\. Erstellen von zwei Sicherheitszonen

 - Richten Sie eine Trust Zone für das interne Netzwerk und eine Untrust Zone für Edgerouter zum externen Netzwerk ein.
 - Weisen Sie den Zonen entsprechende Schnittstellen zu.
 - Lassen Sie Dienste an den Schnittstellen zu.


	security { zones { security-zone Trust { host-inbound-traffic { system-services { ping; } protocols { bgp; } } interfaces { reth0.100; } } security-zone Untrust { host-inbound-traffic { system-services { ping; } protocols { bgp; } } interfaces { reth1.100; } } } }


### 3\. Erstellen von Sicherheitsrichtlinien zwischen Zeitzonen
 
	security {
	    policies {
	        from-zone Trust to-zone Untrust {
	            policy allow-any {
	                match {
	                    source-address any;
	                    destination-address any;
	                    application any;
	                }
	                then {
	                    permit;
	                }
	            }
	        }
	        from-zone Untrust to-zone Trust {
	            policy allow-any {
	                match {
	                    source-address any;
	                    destination-address any;
	                    application any;
	                }
	                then {
	                    permit;
	                }
	            }
	        }
	    }
	}


### 4\. Konfigurieren von NAT-Richtlinien
 - Erstellen Sie zwei NAT-Pools. Einer dient für an Microsoft ausgehenden NAT-Datenverkehr, der andere für NAT-Datenverkehr von Microsoft zum Kunden.
 - Erstellen Sie Regeln für die NAT des jeweiligen Datenverkehrs.

		security {
		    nat {
		        source {
		            pool SNAT-To-ExpressRoute {
		                routing-instance {
		                    External-ExpressRoute;
		                }
		                address {
		                    <NAT-IP-address/Subnet-mask>;
		                }
		            }
		            pool SNAT-From-ExpressRoute {
		                routing-instance {
		                    Internal;
		                }
		                address {
		                    <NAT-IP-address/Subnet-mask>;
		                }
		            }
		            rule-set Outbound_NAT {
		                from routing-instance Internal;
		                to routing-instance External-ExpressRoute;
		                rule SNAT-Out {
		                    match {
		                        source-address 0.0.0.0/0;
		                    }
		                    then {
		                        source-nat {
		                            pool {
		                                SNAT-To-ExpressRoute;
		                            }
		                        }
		                    }
		                }
		            }
		            rule-set Inbound-NAT {
		                from routing-instance External-ExpressRoute;
		                to routing-instance Internal;
		                rule SNAT-In {
		                    match {
		                        source-address 0.0.0.0/0;
		                    }
		                    then {
		                        source-nat {
		                            pool {
		                                SNAT-From-ExpressRoute;
		                            }
		                        }
		                    }
		                }
		            }
		        }
		    }
		}


### 5\. Konfigurieren von BGP zum Ankündigen selektiver Namespacepräfixe in jede Richtung

Beispielen finden Sie auf der Seite [Beispiele für Routingkonfiguration](expressroute-config-samples-routing.md).

### 6\. Erstellen von Richtlinien

	routing-options {
	    	      autonomous-system <Customer-ASN>;
	}
	policy-options {
	    prefix-list Microsoft-Prefixes {
	        <IP-Address/Subnet-Mask;
	        <IP-Address/Subnet-Mask;
	    }
	    prefix-list private-ranges {
	        10.0.0.0/8;
	        172.16.0.0/12;
	        192.168.0.0/16;
	        100.64.0.0/10;
	    }
	    policy-statement Advertise-NAT-Pools {
	        from {
	            protocol static;
	            route-filter <NAT-Pool-Address/Subnet-mask> prefix-length-range /32-/32;
	        }
	        then accept;
	    }
	    policy-statement Accept-from-Microsoft {
	        term 1 {
	            from {
	                instance External-ExpressRoute;
	                prefix-list-filter Microsoft-Prefixes orlonger;
	            }
	            then accept;
	        }
	        term deny {
	            then reject;
	        }
	    }
	    policy-statement Accept-from-Internal {
	        term no-private {
	            from {
	                instance Internal;
	                prefix-list-filter private-ranges orlonger;
	            }
	            then reject;
	        }
	        term bgp {
	            from {
	                instance Internal;
	                protocol bgp;
	            }
	            then accept;
	        }
	        term deny {
	            then reject;
	        }
	    }
	}
	routing-instances {
	    Internal {
	        instance-type virtual-router;
	        interface reth0.100;
	        routing-options {
	            static {
	                route <NAT-Pool-IP-Address/Subnet-mask> discard;
	            }
	            instance-import Accept-from-Microsoft;
	        }
	        protocols {
	            bgp {
	                group customer {
	                    export <Advertise-NAT-Pools>;
	                    peer-as <Customer-ASN-1>;
	                    neighbor <BGP-Neighbor-IP-Address>;
	                }
	            }
	        }
	    }
	    External-ExpressRoute {
	        instance-type virtual-router;
	        interface reth1.100;
	        routing-options {
	            static {
	                route <NAT-Pool-IP-Address/Subnet-mask> discard;
	            }
	            instance-import Accept-from-Internal;
	        }
	        protocols {
	            bgp {
	                group edge-router {
	                    export <Advertise-NAT-Pools>;
	                    peer-as <Customer-Public-ASN>;
	                    neighbor <BGP-Neighbor-IP-Address>;
	                }
	            }
	        }
	    }
	}

## Nächste Schritte

Weitere Informationen finden Sie unter [ExpressRoute – FAQ](expressroute-faqs.md).

<!---HONumber=AcomDC_0323_2016-->