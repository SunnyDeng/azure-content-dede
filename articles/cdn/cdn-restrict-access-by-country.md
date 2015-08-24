<properties 
	pageTitle="CDN – Einschränken des Zugriffs auf Inhalte nach Ländern" 
	description="Wenn ein Benutzer Ihren Inhalt anfordert, wird unabhängig davon, wo der Benutzer diese Anforderung vorgenommen hat, der Inhalt angezeigt. In einigen Fällen empfiehlt es sich, den Zugriff auf Ihre Inhalte nach Ländern einzuschränken. In diesem Thema wird erläutert, wie Sie das Feature **Länderfilter** einsetzen, um den Zugriff je nach Land zu genehmigen oder verweigern." 
	services="cdn" 
	documentationCenter=".NET" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/08/2015" 
	ms.author="juliako"/>

#Einschränken des Zugriffs auf Inhalte nach Ländern

Wenn ein Benutzer Ihren Inhalt anfordert, wird unabhängig davon, wo der Benutzer diese Anforderung vorgenommen hat, der Inhalt angezeigt. In einigen Fällen empfiehlt es sich, den Zugriff auf Ihre Inhalte nach Ländern einzuschränken. In diesem Thema wird erläutert, wie Sie das Feature **Länderfilter** einsetzen, um den Zugriff je nach Land zu genehmigen oder verweigern.

>[AZURE.NOTE]Nachdem die Konfiguration eingerichtet wurde, gilt diese für alle CDN-Endpunkte in Ihrem Abonnement.

Informationen zu Überlegungen, die Sie beim Konfigurieren dieser Art von Einschränkungen berücksichtigen sollten, finden Sie im Abschnitt [Überlegungen](cdn-restrict-access-by-country.md#considerations) am Ende dieses Themas.

![Länderfilter](./media/cdn-filtering/cdn-country-filtering.png)


##Schritt 1: Definieren des Verzeichnispfads

Wenn Sie einen Länderfilter konfigurieren, müssen Sie den relativen Pfad zum Speicherort angeben, an dem der Zugriff für Benutzer zugelassen oder verweigert wird. Sie können Länderfilter für alle Dateien (durch Eingabe von „/“) oder ausgewählte Ordner anwenden, indem Sie Verzeichnispfade angeben.

Beispiel für Verzeichnispfadfilter:

	/                                 
	/Photos/
	/Photos/Strasbourg

##Schritt 2: Definieren der Aktion: Blockieren oder Zulassen

**Blockieren:** Benutzern aus den angegebenen Ländern wird der von diesem rekursiven Pfad angeforderte Zugriff auf Ressourcen verweigert. Wenn keine anderen Länderfilteroptionen für diesen Standort konfiguriert wurden, wird der Zugriff für alle anderen Benutzer zugelassen.

**Zulassen:** Nur für Benutzer aus den angegebenen Ländern wird der von diesem rekursiven Pfad angeforderte Zugriff auf Ressourcen zugelassen.

##Schritt 3: Definieren der Länder

Wählen Sie die Länder aus, die Sie für den Pfad zulassen oder blockieren möchten. Weitere Informationen finden Sie im Thema [Landeskennzahlen](cdn-country-codes.md).

Beispielsweise werden durch die Regel zum Blockieren von "/Photos/Strasbourg/" die folgenden Dateien gefiltert:

	http://az123456.vo.msecnd.net/Photos/Strasbourg/1000.jpg. 
	http://az123456.vo.msecnd.net/Photos/Strasbourg/Cathedral/1000.jpg. 


##Landeskennzahlen

Die Funktion **Länderfilter** verwendet Landeskennzahlen, um die Länder zu definieren, von denen aus eine Anforderung für ein sicheres Verzeichnis zugelassen oder blockiert wird. Die Landeskennzahlen finden Sie in [diesem](cdn-country-codes.md) Thema. Wenn Sie "EU" (Europa) oder "AP" (Asien/Pazifik) angeben, wird eine Teilmenge der IP-Adressen, die aus einem Land in diesen Regionen stammen, blockiert oder zugelassen.


##<a id="considerations"></a>Überlegungen

- Es kann bis zu einer Stunde dauern, bis die Änderungen an Ihrem Länderfilter wirksam werden.
- Diese Funktion unterstützt keine Platzhalterzeichen (z. B. "*").
- Das dem relativen Pfad zugeordnete Länderfilterkonfiguration wird rekursiv auf diesen Pfad angewendet.
- Pro relativen Pfad kann nur eine Regel angewendet werden (Sie können nicht mehrere Länderfilter erstellen, die auf den gleichen relativen Pfad verweisen). Jedoch können mehrere Länderfilter auf einen Ordner angewendet werden. Das liegt an der rekursiven Natur der Länderfilter. Anders ausgedrückt: Ein Unterordner eines zuvor konfigurierten Ordners kann einem anderen Länderfilter zugewiesen werden.

<!---HONumber=August15_HO7-->