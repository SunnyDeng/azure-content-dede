<properties 
	pageTitle="Steuern von eingehendem Datenverkehr in eine App Service-Umgebung" 
	description="Erfahren Sie, wie Netzwerksicherheitsregeln für das Steuern des in einer App Service-Umgebung eingehenden Datenverkehrs konfiguriert werden." 
	services="app-service\web" 
	documentationCenter="" 
	authors="ccompy" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/14/2015" 
	ms.author="stefsh"/>

# Steuern von eingehendem Datenverkehr in eine App Service-Umgebung

## Übersicht ##
Eine App Service-Umgebung wird immer in einem Subnetz eines regionalen [virtuellen Netzwerks][virtualnetwork] erstellt. Ein neues regionales virtuelles Netzwerk und ein neues Subnetz können bei der Erstellung einer App Service-Umgebung definiert werden. Alternativ kann eine App Service-Umgebung in einem bereits vorhandenen regionalen virtuellen Netzwerk und einem bereits vorhandenen Subnetz erstellt werden. Weitere Informationen zum Erstellen einer App Service-Umgebung finden Sie unter [Erstellen einer App Service-Umgebung][HowToCreateAnAppServiceEnvironment].

Eine App Service-Umgebung muss immer innerhalb eines Subnetzes erstellt werden, da ein Subnetz eine Netzwerkgrenze bereitstellt, die zum Sperren von eingehendem Datenverkehr hinter Upstreamgeräten und -diensten verwendet werden kann. Auf diese Weise wird HTTP- und HTTPS-Datenverkehr nur von bestimmten Upstream-IP-Adressen akzeptiert.

Der ein- und ausgehende Netzwerkdatenverkehr in einem Subnetz wird anhand einer [Netzwerksicherheitsgruppe][NetworkSecurityGroups] gesteuert. Zur Steuerung des eingehenden Datenverkehrs müssen Netzwerksicherheitsregeln in einer Netzwerksicherheitsgruppe erstellt werden. Anschließend wird die Netzwerksicherheitsgruppe dem Subnetz mit der App Service-Umgebung zugewiesen.

Sobald eine Netzwerksicherheitsgruppe einem Subnetz zugewiesen wurde, wird der in den Apps in der App Service-Umgebung eingehende Datenverkehr abhängig von den Zulassungs- und Ablehnungsregeln, die in der Netzwerksicherheitsgruppe definiert wurden, entweder zugelassen oder blockiert.

## In einer App Service-Umgebung verwendete Netzwerkports ##
Bevor Sie den eingehenden Netzwerkdatenverkehr mithilfe einer Netzwerksicherheitsgruppe sperren, sollten Sie unbedingt die erforderlichen und optionalen Netzwerkports kennen, die von einer App Service-Umgebung verwendet werden. Das versehentliche Ausschließen von Datenverkehr für bestimmte Ports kann zu einem Funktionalitätsverlust in einer App Service-Umgebung führen.

Im Folgenden sehen Sie eine Liste der Ports, die von einer App Service-Umgebung verwendet werden:

- 454: **Erforderlicher Port**, der von der Azure-Infrastruktur für die Verwaltung und Wartung von App Service-Umgebungen verwendet wird. Der Datenverkehr für diesen Port darf nicht blockiert werden.
- 455: **Erforderlicher Port**, der von der Azure-Infrastruktur für die Verwaltung und Wartung von App Service-Umgebungen verwendet wird. Der Datenverkehr für diesen Port darf nicht blockiert werden.
- 80: Standardport für eingehenden HTTP-Datenverkehr in Apps, die in App Service-Plänen in einer App Service-Umgebung ausgeführt werden.
- 443: Standardport für eingehenden SSL-Datenverkehr in Apps, die in App Service-Plänen in einer App Service-Umgebung ausgeführt werden.
- 21: Steuerungskanal für FTP. Dieser Port kann sicher blockiert werden, wenn FTP nicht verwendet wird.
- 10001 10020: Datenkanäle für FTP. Wie der Steuerungskanal können diese Ports sicher blockiert werden, wenn FTP nicht verwendet wird (**Hinweis:** Die FTP-Datenkanäle können sich während der Vorschau ändern.)
- 4016: Wird zum Remotedebuggen in Visual Studio 2012 verwendet. Dieser Port kann sicher blockiert werden, wenn die Funktion nicht verwendet wird.
- 4018: Wird zum Remotedebuggen in Visual Studio 2013 verwendet. Dieser Port kann sicher blockiert werden, wenn die Funktion nicht verwendet wird.
- 4020: Wird zum Remotedebuggen in Visual Studio 2015 verwendet. Dieser Port kann sicher blockiert werden, wenn die Funktion nicht verwendet wird.

## Erstellen einer Netzwerksicherheitsgruppe ##
Einzelheiten zur Funktionsweise von Netzwerksicherheitsgruppen finden Sie in den folgenden [Informationen][NetworkSecurityGroups]. Die unten aufgeführten Details gehen kurz auf die wichtigsten Aspekte von Netzwerksicherheitsgruppen ein. Dabei liegt der Schwerpunkt auf dem Konfigurieren und Anwenden einer Netzwerksicherheitsgruppe auf ein Subnetz, das eine App Service-Umgebung enthält.

Netzwerksicherheitsgruppen werden zuerst als eigenständige Entität erstellt, die einem Abonnement zugeordnet ist. Da Netzwerksicherheitsgruppen in einer Azure-Region erstellt werden, stellen Sie sicher, dass die Netzwerksicherheitsgruppe in derselben Region wie die App Service-Umgebung erstellt wird.

Das folgende Beispiel veranschaulicht das Erstellen einer Netzwerksicherheitsgruppe:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Sobald eine Netzwerksicherheitsgruppe erstellt wurde, werden ihr eine oder mehrere Netzwerksicherheitsregeln hinzugefügt. Da der Regelsatz sich mit der Zeit ändern kann, wird empfohlen, in dem für Regelprioritäten verwendeten Nummerierungsschema Abstände einzuräumen, damit zusätzliche Regeln leicht eingefügt werden können.

Das folgende Beispiel zeigt eine Regel, die explizit Zugriff auf die Verwaltungsports gewährt, die von der Azure-Infrastruktur für die Verwaltung und Wartung einer App Service-Umgebung benötigt werden. Beachten Sie, dass der gesamte Verwaltungsdatenverkehr über SSL verläuft und durch Clientzertifikate gesichert wird. Selbst wenn die Ports geöffnet werden, sind sie für keine Entität außer für die Azure-Verwaltungsinfrastruktur zugänglich.


    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP
    

Wenn Sie den Zugriff auf Port 80 und 443 sperren, um eine App Service-Umgebung hinter Upstreamgeräten oder -diensten "auszublenden", müssen Sie die Upstream-IP-Adresse kennen. Wenn Sie beispielsweise eine Web Application Firewall (WAF) verwenden, besitzt die WAF eine eigene IP-Adresse (oder mehrere Adressen) zum Leiten von Datenverkehr über einen Proxy an eine Downstream-App Service-Umgebung. Diese IP-Adresse müssen Sie im Parameter *SourceAddressPrefix* einer Netzwerksicherheitsregel verwenden.

Im folgenden Beispiel wird der eingehende Datenverkehr von einer bestimmten Upstream-IP-Adresse explizit zugelassen. Die Adresse *1.2.3.4* dient als Platzhalter für die IP-Adresse einer Upstream-WAF. Ändern Sie den Wert in die Adresse, die von Ihrem Upstreamgerät oder -dienst verwendet wird.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
    
Wenn FTP-Unterstützung gewünscht wird, können die folgenden Regeln als Vorlage für das Erteilen des Zugriffs auf den FTP-Steuerungsport und die Datenkanalports verwendet werden. Da es sich bei FTP um ein zustandsbehaftetes Protokoll handelt, können Sie FTP-Datenverkehr möglicherweise nicht über eine herkömmliche HTTP/HTTPS-Firewall oder ein Proxygerät leiten. In diesem Fall müssen Sie das *SourceAddressPrefix* auf einen anderen Wert festlegen, z. B. den IP-Adressbereich von Entwicklungs- oder Bereitstellungscomputern, auf denen FTP-Clients ausgeführt werden.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP

(**Hinweis:** Der Datenkanal-Portbereich kann sich während des Vorschauzeitraums möglicherweise ändern.)

Für das Remotedebuggen mit Visual Studio wird das Erteilen des Zugriffs durch die folgenden Regeln veranschaulicht. Es gibt für jede unterstützte Version von Visual Studio eine separate Regel, da jede Version einen anderen Port für das Remotedebuggen verwendet. Wie beim FTP-Zugriff fließt der Datenverkehr für das Remotedebuggen möglicherweise nicht ordnungsgemäß durch eine herkömmliche WAF oder ein Proxygerät. Das *SourceAddressPrefix* kann stattdessen auf den IP-Adressbereich von Entwicklungscomputern mit Visual Studio festgelegt werden.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP

## Zuweisen einer Netzwerksicherheitsgruppe zu einem Subnetz ##
Eine Netzwerksicherheitsgruppe verfügt über eine Standardsicherheitsregel, die den Zugriff auf den gesamten externen Datenverkehr verweigert. Die Kombination der oben beschriebenen Netzwerksicherheitsregeln und der Standardsicherheitsregel zum Blockieren des eingehenden Datenverkehrs hat zur Folge, dass nur der Datenverkehr aus den Quelladressbereichen, die einer *Zulassen*-Aktion zugeordnet sind, Datenverkehr an Apps senden kann, die in einer App Service-Umgebung ausgeführt werden.

Nachdem eine Netzwerksicherheitsgruppe mit Sicherheitsregeln aufgefüllt wurde, muss sie dem Subnetz mit der App Service-Umgebung zugewiesen werden. Der Befehl für die Zuordnung verweist sowohl auf den Namen des virtuellen Netzwerks, in dem sich die App Service-Umgebung befindet, als auch auf den Namen des Subnetzes, in dem die App Service-Umgebung erstellt wurde.

Das folgende Beispiel zeigt eine Netzwerksicherheitsgruppe, die einem Subnetz und einem virtuellen Netzwerk zugewiesen wird:


    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

Nach der erfolgreichen Zuweisung der Netzwerksicherheitsgruppe (die Zuweisung ist ein längerer Vorgang und kann einige Minuten dauern) erreicht nur derjenige eingehende Datenverkehr die Apps in der App Service-Umgebung, der den *Zulassen*-Regeln entspricht.

Das folgende Beispiel zeigt der Vollständigkeit halber das Entfernen und somit das Trennen der Netzwerksicherheitsgruppe vom Subnetz:


    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

## Besonderheiten für explizites IP-SSL ##
Wenn eine Anwendung mit einer expliziten IP-Adresse und nicht mit der Standard-IP-Adresse der App Service-Umgebung konfiguriert wird, fließt sowohl HTTP- als auch HTTPS-Datenverkehr über einen anderen Satz von Ports (nicht über Port 80 und 443) in das Subnetz.

Während der ersten Vorschau von App Service-Umgebungen ist es nicht möglich, die spezifischen von IP-SSL verwendeten Ports zu bestimmen. Sobald diese Informationen jedoch über das Portal, Befehlszeilentools und REST-APIs verfügbar gemacht wurden, können Entwickler Netzwerksicherheitsgruppen so konfigurieren, dass diese auch den Datenverkehr über diese Ports regeln.

## Erste Schritte

Informationen zum Einstieg in App Service-Umgebungen finden Sie unter [Einführung in die App Service-Umgebung][IntroToAppServiceEnvironment]

Einzelheiten zum Herstellen einer sicheren Verbindung von Apps mit der Back-End-Ressource von einer App Service-Umgebung aus finden Sie unter [Sicheres Verbinden mit Back-End-Ressourcen von einer App Service-Umgebung aus][SecurelyConnecttoBackend]

Weitere Informationen zur Azure App Service-Plattform finden Sie unter [Azure App Service][AzureAppService].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://msdn.microsoft.com/library/azure/dn133803.aspx
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[NetworkSecurityGroups]: https://msdn.microsoft.com/library/azure/dn848316.aspx
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[IntroToAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[SecurelyConnecttoBackend]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-securely-connecting-to-backend-resources/

<!-- IMAGES -->

<!---HONumber=62-->