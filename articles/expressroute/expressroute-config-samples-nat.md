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
   ms.date="10/12/2015"
   ms.author="cherylmc"/>

# Beispiele für die Routerkonfiguration zum Einrichten und Verwalten von NAT (Network Address Translation, Netzwerkadressübersetzung)

Diese Seite enthält NAT-Konfigurationsbeispiele für Router der Reihen Cisco ASA und Juniper MX. Diese Beispiele dienen nur als Leitfaden und müssen nicht wie angegeben verwendet werden. Sie können mit Ihrem Anbieter an der Ausarbeitung geeigneter Konfigurationen für Ihr Netzwerk zusammenarbeiten.

>[AZURE.IMPORTANT]Die Beispiele auf dieser Seite sind ausschließlich als Leitfaden konzipiert. Sie müssen mit dem Vertriebsteam/Technikteam Ihres Anbieters und Ihrem Netzwerkteam zusammenarbeiten, um für Ihre Anforderungen geeignete Konfigurationen zu bestimmen. Microsoft bietet keine Unterstützung bei Problemen im Zusammenhang mit Konfigurationen, die auf dieser Seite aufgeführt sind. Sie müssen sich bei Problemen an den Gerätehersteller wenden.

Die nachstehenden Beispiele für die Routerkonfiguration gelten für Azure Public- und Microsoft-Peerings. Sie dürfen NAT nicht für privates Azure-Peering konfigurieren. Unter [ExpressRoute-Peerings](expressroute-circuit-peerings.md) und [NAT-Anforderungen für ExpressRoute](expressroute-nat.md) finden Sie weitere Details.

**Hinweis:** Sie müssen separate NAT-IP-Adresspools für Konnektivität mit dem Internet und ExpressRoute verwenden. Das Verwenden des gleichen NAT-IP-Adresspools für Internet und ExpressRoute führt zu einem asymmetrischen Routing und Verlust der Konnektivität.

## Cisco ASA-Firewalls

### PAT-Konfiguration für den Datenverkehr von Kundennetzwerk zu Microsoft

    object network MSFT-PAT
      range <SNAT-START-IP> <SNAT-END-IP>
    
    
    object-group network MSFT-Range
      network-object <IP> <Subnet_Mask>
    
    object-group network on-prem-range-1
      network-object <IP> <Subnet_Mask>
    
    object-group network on-prem-range-2
      network-object <IP> <Subnet_Mask>
    
    object-group network on-prem
      network-object object on-prem-range-1
      network-object object on-prem-range-2
    
    nat (outside,inside) source dynamic on-prem pat-pool MSFT-PAT destination static MSFT-Range MSFT-Range



## Juniper MX-Series-Router 

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
	                address <IP_Address/Subnet_mask>;
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
	                address <IP_Address/Subnet_mask>;
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
		            pool SNAT_To_ExpressRoute {
		                routing-instance {
		                    External_ExpressRoute;
		                }
		                address {
		                    <NAT_IP_address/Subnet_mask>;
		                }
		            }
		            pool SNAT_From_ExpressRoute {
		                routing-instance {
		                    Internal;
		                }
		                address {
		                    <NAT_IP_address/Subnet_mask>;
		                }
		            }
		            rule-set Outbound_NAT {
		                from routing-instance Internal;
		                to routing-instance External_ExpressRoute;
		                rule SNAT_Out {
		                    match {
		                        source-address 0.0.0.0/0;
		                    }
		                    then {
		                        source-nat {
		                            pool {
		                                SNAT_To_ExpressRoute;
		                            }
		                        }
		                    }
		                }
		            }
		            rule-set Inbound_NAT {
		                from routing-instance External_ExpressRoute;
		                to routing-instance Internal;
		                rule SNAT_In {
		                    match {
		                        source-address 0.0.0.0/0;
		                    }
		                    then {
		                        source-nat {
		                            pool {
		                                SNAT_From_ExpressRoute;
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
	    	      autonomous-system <Customer_ASN>;
	}
	policy-options {
	    prefix-list Microsoft_Prefixes {
	        <IP_Address/Subnet_Mask;
	        <IP_Address/Subnet_Mask;
	    }
	    prefix-list private-ranges {
	        10.0.0.0/8;
	        172.16.0.0/12;
	        192.168.0.0/16;
	        100.64.0.0/10;
	    }
	    policy-statement Advertise_NAT_Pools {
	        from {
	            protocol static;
	            route-filter <NAT_Pool_Address/Subnet_mask> prefix-length-range /32-/32;
	        }
	        then accept;
	    }
	    policy-statement Accept_from_Microsoft {
	        term 1 {
	            from {
	                instance External_ExpressRoute;
	                prefix-list-filter Microsoft_Prefixes orlonger;
	            }
	            then accept;
	        }
	        term deny {
	            then reject;
	        }
	    }
	    policy-statement Accept_from_Internal {
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
	                route <NAT_Pool_IP_Address/Subnet_mask> discard;
	            }
	            instance-import Accept_from_Microsoft;
	        }
	        protocols {
	            bgp {
	                group customer {
	                    export <Advertise_NAT_Pools>;
	                    peer-as <Customer_ASN_1>;
	                    neighbor <BGP_Neighbor_IP_Address>;
	                }
	            }
	        }
	    }
	    External_ExpressRoute {
	        instance-type virtual-router;
	        interface reth1.100;
	        routing-options {
	            static {
	                route <NAT_Pool_IP_Address/Subnet_mask> discard;
	            }
	            instance-import Accept_from_Internal;
	        }
	        protocols {
	            bgp {
	                group edge_router {
	                    export <Advertise_NAT_Pools>;
	                    peer-as <Customer_Public_ASN>;
	                    neighbor <BGP_Neighbor_IP_Address>;
	                }
	            }
	        }
	    }
	}

## Nächste Schritte

Weitere Informationen finden Sie unter [ExpressRoute – FAQ](expressroute-faqs.md).

<!---HONumber=Oct15_HO3-->