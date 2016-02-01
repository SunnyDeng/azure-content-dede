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

# Beispiele für die Routerkonfiguration zum Einrichten und Verwalten des Routings

Diese Seite enthält Schnittstellen- und Routingkonfigurationsbeispiele für Router der Reihen Cisco IOS-XE und Juniper MX. Diese Beispiele dienen nur als Leitfaden und müssen nicht wie angegeben verwendet werden. Sie können mit Ihrem Anbieter an der Ausarbeitung geeigneter Konfigurationen für Ihr Netzwerk zusammenarbeiten.

>[AZURE.IMPORTANT]Die Beispiele auf dieser Seite sind ausschließlich als Leitfaden konzipiert. Sie müssen mit dem Vertriebsteam/Technikteam Ihres Anbieters und Ihrem Netzwerkteam zusammenarbeiten, um für Ihre Anforderungen geeignete Konfigurationen zu bestimmen. Microsoft bietet keine Unterstützung bei Problemen im Zusammenhang mit Konfigurationen, die auf dieser Seite aufgeführt sind. Sie müssen sich bei Problemen an den Gerätehersteller wenden.

Die unten aufgeführten Beispiele zur Routerkonfiguration gelten für alle Peerings. Unter [ExpressRoute-Peerings](expressroute-circuit-peerings.md) und [Routinganforderungen für ExpressRoute](expressroute-routing.md) finden Sie weitere Details.

## Cisco IOS-XE-basierte Router

Die Beispiele in diesem Abschnitt beziehen sich auf Router, auf denen die IOS-XE-Betriebssystemfamilie ausgeführt wird.

### 1\. Konfigurieren von Schnittstellen und Unterschnittstellen

Sie benötigen auf jedem Router, den Sie mit Microsoft verbinden, eine Unterschnittstelle pro Peering. Eine Unterschnittstelle kann anhand einer VLAN-ID oder eines gestapelten Paars von VLAN-IDs und einer IP-Adresse identifiziert werden.

#### Dot1Q-Schnittstellendefinition

Dieses Beispiel stellt die Unterschnittstellendefinition für eine Unterschnittstelle mit einer einzelnen VLAN-ID bereit. Die VLAN-ID ist pro Peering eindeutig. Das letzte Oktett Ihrer IPv4-Adresse ist immer eine ungerade Zahl.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

#### QinQ-Schnittstellendefinition

Dieses Beispiel stellt die Unterschnittstellendefinition für eine Unterschnittstelle mit zwei VLAN-IDs bereit. Falls die äußere VLAN-ID (s-Tag) verwendet wird, bleibt sie über alle Peerings gleich. Die innere VLAN-ID (c-Tag) ist pro Peering eindeutig. Das letzte Oktett Ihrer IPv4-Adresse ist immer eine ungerade Zahl.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>
    
### 2\. Einrichten von eBGP-Sitzungen

Sie müssen für jedes Peering eine BGP-Sitzung bei Microsoft einrichten. Mit dem folgenden Beispiel können Sie eine BGP-Sitzung bei Microsoft einrichten. Wenn die IPv4-Adresse, die Sie für Ihre Unterschnittstelle verwendet haben, "a.b.c.d" war, lautet die IP-Adresse des BGP-Nachbarn (Microsoft) "a.b.c.d+1". Das letzte Oktett in der IPv4-Adresse des BGP-Nachbarn ist immer eine gerade Zahl.

	router bgp <Customer_ASN>
	 bgp log-neighbor-changes
	 neighbor <IP#2_used_by_Azure> remote-as 12076
	 !        
	 address-family ipv4
	 neighbor <IP#2_used_by_Azure> activate
	 exit-address-family
	!

### 3\. Einrichten von Präfixen für die Ankündigung über die BGP-Sitzung

Sie können den Router so konfigurieren, dass Microsoft bestimmte Präfixe angekündigt werden. Dies ist mit dem folgenden Beispiel möglich.

	router bgp <Customer_ASN>
	 bgp log-neighbor-changes
	 neighbor <IP#2_used_by_Azure> remote-as 12076
	 !        
	 address-family ipv4
	  network <Prefix_to_be_advertised> mask <Subnet_mask>
	  neighbor <IP#2_used_by_Azure> activate
	 exit-address-family
	!

### 4\. Routenzuordnungen

Anhand von Routenzuordnungen und Präfixlisten können Sie Präfixe filtern, die in Ihrem Netzwerk weitergegeben werden. Diese Aufgabe können Sie mit dem folgenden Beispiel durchführen. Stellen Sie sicher, dass entsprechende Präfixlisten eingerichtet wurden.

	router bgp <Customer_ASN>
	 bgp log-neighbor-changes
	 neighbor <IP#2_used_by_Azure> remote-as 12076
	 !        
	 address-family ipv4
	  network <Prefix_to_be_advertised> mask <Subnet_mask>
	  neighbor <IP#2_used_by_Azure> activate
	  neighbor <IP#2_used_by_Azure> route-map <MS_Prefixes_Inbound> in
	 exit-address-family
	!
	route-map <MS_Prefixes_Inbound> permit 10
	 match ip address prefix-list <MS_Prefixes>
	!


## Router der Juniper MX-Serie 

Die Beispiele in diesem Abschnitt gelten für Router der Juniper MX-Serie.

### 1\. Konfigurieren von Schnittstellen und Unterschnittstellen

#### Dot1Q-Schnittstellendefinition

Dieses Beispiel stellt die Unterschnittstellendefinition für eine Unterschnittstelle mit einer einzelnen VLAN-ID bereit. Die VLAN-ID ist pro Peering eindeutig. Das letzte Oktett Ihrer IPv4-Adresse ist immer eine ungerade Zahl.

    interfaces {
    	vlan-tagging;
    	<Interface_Number> {
    		unit <Number> {
    			vlan-id <VLAN_ID>;
    			family inet {
    				address <IPv4_Address/Subnet_Mask>;
    			}
    		}
    	}
    }


#### QinQ-Schnittstellendefinition

Dieses Beispiel stellt die Unterschnittstellendefinition für eine Unterschnittstelle mit zwei VLAN-IDs bereit. Falls die äußere VLAN-ID (s-Tag) verwendet wird, bleibt sie über alle Peerings gleich. Die innere VLAN-ID (c-Tag) ist pro Peering eindeutig. Das letzte Oktett Ihrer IPv4-Adresse ist immer eine ungerade Zahl.

	interfaces {
	    <Interface_Number> {
	        flexible-vlan-tagging;
	        unit <Number> {
	            vlan-tags outer <S-tag> inner <C-tag>;
	            family inet {
	                address <IPv4_Address/Subnet_Mask>;
	            }                           
	        }                               
	    }                                   
	}                           

### 2\. Einrichten von eBGP-Sitzungen

Sie müssen für jedes Peering eine BGP-Sitzung bei Microsoft einrichten. Mit dem folgenden Beispiel können Sie eine BGP-Sitzung bei Microsoft einrichten. Wenn die IPv4-Adresse, die Sie für Ihre Unterschnittstelle verwendet haben, "a.b.c.d" war, lautet die IP-Adresse des BGP-Nachbarn (Microsoft) "a.b.c.d+1". Das letzte Oktett in der IPv4-Adresse des BGP-Nachbarn ist immer eine gerade Zahl.

	routing-options {
	    autonomous-system <Customer_ASN>;
	}
	}
	protocols {
	    bgp { 
	        group <Group_Name> { 
	            peer-as 12076;              
	            neighbor <IP#2_used_by_Azure>;
	        }                               
	    }                                   
	}

### 3\. Einrichten von Präfixen für die Ankündigung über die BGP-Sitzung

Sie können den Router so konfigurieren, dass Microsoft bestimmte Präfixe angekündigt werden. Dies ist mit dem folgenden Beispiel möglich.

	policy-options {
	    policy-statement <Policy_Name> {
	        term 1 {
	            from protocol OSPF;
		route-filter <Prefix_to_be_advertised/Subnet_Mask> exact;
	            then {
	                accept;
	            }
	        }
	    }
	}
	protocols {
	    bgp { 
	        group <Group_Name> { 
	            export <Policy_Name>
	            peer-as 12076;              
	            neighbor <IP#2_used_by_Azure>;
	        }                               
	    }                                   
	}


### 4\. Routenzuordnungen

Anhand von Routenzuordnungen und Präfixlisten können Sie Präfixe filtern, die in Ihrem Netzwerk weitergegeben werden. Diese Aufgabe können Sie mit dem folgenden Beispiel durchführen. Stellen Sie sicher, dass entsprechende Präfixlisten eingerichtet wurden.

	policy-options {
	    prefix-list MS_Prefixes {
	        <IP_Prefix_1/Subnet_Mask>;
	        <IP_Prefix_2/Subnet_Mask>;
	    }
	    policy-statement <MS_Prefixes_Inbound> {
	        term 1 {
	            from {
		prefix-list MS_Prefixes;
	            }
	            then {
	                accept;
	            }
	        }
	    }
	}
	protocols {
	    bgp { 
	        group <Group_Name> { 
	            export <Policy_Name>
	            import <MS_Prefixes_Inbound>
	            peer-as 12076;              
	            neighbor <IP#2_used_by_Azure>;
	        }                               
	    }                                   
	}

## Nächste Schritte

Weitere Informationen finden Sie unter [ExpressRoute – FAQ](expressroute-faqs.md).

<!---HONumber=AcomDC_0121_2016-->