
Um Ihr VPN-Gerät zu konfigurieren, benötigen Sie die öffentliche IP-Adresse des virtuellen Netzwerkgateways für die Konfiguration Ihres lokalen VPN-Geräts. Halten Sie mit Ihrem Gerätehersteller für spezifische Informationen zur Konfiguration Rücksprache, und konfigurieren Sie das Gerät. Weitere Informationen zu VPN-Geräten, die problemlos mit Azure eingesetzt werden können, finden Sie unter [VPN-Geräte](vpn-gateway-about-vpn-devices.md).

Um die öffentliche IP-Adresse des virtuellen Netzwerkgateways über PowerShell zu ermitteln, verwenden Sie das folgende Beispiel:

	Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG

Sie können die öffentliche IP-Adresse für Ihr virtuelles Netzwerkgateway auch im Azure-Portal anzeigen. Navigieren Sie zu **Virtuelle Netzwerkgateways**, und klicken Sie dann auf den Namen Ihres Gateways.

<!---HONumber=AcomDC_0107_2016-->