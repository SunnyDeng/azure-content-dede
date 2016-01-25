1. Wechseln Sie im Portal zu **Neu** und dann zu **Durchsuchen**. Wählen Sie in der Liste den Eintrag **Virtuelle Netzwerkgateways**.
2. Klicken Sie auf **Hinzufügen**.
3. Benennen Sie Ihr Gateway. Dies ist nicht das Gleiche wie das Benennen eines Gatewaysubnetzes. Dies ist der Name des Gatewayobjekts. 
4. Wählen Sie unter **Virtual Network** das VNET aus, das Sie mit diesem Gateway verbinden möchten.
5. Erstellen Sie in den VNET-Einstellungen unter dem Wert **Öffentliche IP-Adresse** einen Namen für Ihre öffentliche IP-Adresse. Beachten Sie, dass hierbei nicht nach einer IP-Adresse gefragt wird. Die IP-Adresse wird dynamisch zugewiesen. Hierbei handelt es sich um den Namen des IP-Adressenobjekts, dem die Adresse zugewiesen wird. 
6. Unter **VPN-Typ** können Sie „Richtlinienbasiert“ oder „Routenbasiert“ auswählen. Wählen Sie den VPN Gateway-Typ aus, der sowohl vom Konfigurationsszenario als auch – falls dies für Ihre Konfiguration erforderlich ist – von dem VPN Gateway-Gerät unterstützt wird, das Sie verwenden möchten.
7. Wählen Sie unter **Ressourcengruppe** die Option **Vorhandene auswählen** und dann die Ressourcengruppe, in der sich Ihr VNET befindet (es sei denn, für Ihre Konfiguration ist eine andere Auswahl erforderlich).
8. Stellen Sie unter **Standort** sicher, dass der Standort angegeben ist, an dem sich sowohl die Ressourcengruppe als auch das VNET befinden.
9. Klicken Sie auf **Erstellen**. Die Kachel *Virtual Network-Gateway bereitstellen* wird auf dem Dashboard angezeigt. Das Erstellen eines Gateways nimmt einige Zeit in Anspruch. Im Hintergrund werden viele Vorgänge durchgeführt. Planen Sie mindestens 15 Minuten oder mehr ein. Unter Umständen müssen Sie die Portalseite aktualisieren, um den Status „Abgeschlossen“ anzuzeigen.
10. Nach der Erstellung des Gateways können Sie die zugewiesene IP-Adresse unter dem Virtual Network im Portal anzeigen. Das Gateway wird als verbundenes Gerät angezeigt. Sie können den Namen und die IP-Adresse anzeigen, die dem Gateway zugewiesen wurde.

<!---HONumber=AcomDC_0114_2016-->