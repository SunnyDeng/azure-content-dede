<properties 
	pageTitle="Zugriff auf Ihre Inhalte nach Ländern einschränken" 
	description="Wenn ein Benutzer Ihren Inhalt anfordert, wird unabhängig davon, wo der Benutzer diese Anforderung vorgenommen hat, der Inhalt angezeigt. In einigen Fällen empfiehlt es sich, den Zugriff auf Ihre Inhalte nach Ländern einzuschränken. In diesem Thema wird erläutert, wie Sie den Dienst konfigurieren, um den Zugriff je nach Land zu genehmigen oder verweigern." 
	services="cdn" 
	documentationCenter=".net" 
	authors="zhangmanling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/16/2015" 
	ms.author="mazha"/>

#Zugriff auf Ihre Inhalte nach Ländern einschränken
 
 
Wenn ein Benutzer Ihren Inhalt anfordert, wird unabhängig davon, wo der Benutzer diese Anforderung vorgenommen hat, der Inhalt angezeigt. In einigen Fällen empfiehlt es sich, den Zugriff auf Ihre Inhalte nach Ländern einzuschränken. In diesem Thema wird erläutert, wie Sie den Dienst konfigurieren, um den Zugriff je nach Land zu genehmigen oder verweigern.

>[AZURE.NOTE]Nachdem die Konfiguration eingerichtet wurde, gilt diese für alle CDN-Endpunkte in Ihrem Abonnement.

##Schritt 1: Definieren des Verzeichnispfads 

Wenn Sie einen Länderfilter konfigurieren, müssen Sie den relativen Pfad zum Speicherort angeben, an dem der Zugriff für Benutzer zugelassen oder verweigert wird. Sie können Länderfilter für alle Dateien (durch Eingabe von „/“) oder ausgewählte Ordner anwenden, indem Sie Verzeichnispfade angeben.

Beispiel für Verzeichnispfadfilter:
		
	/                                 
	/Photos/
	/Photos/Strasbourg

##Schritt 2: Definieren der Aktion: Blockieren oder Zulassen

**Blockieren**: Benutzern aus den angegebenen Ländern wird der von diesem rekursiven Pfad angeforderte Zugriff auf Ressourcen verweigert. Wenn keine anderen Länderfilteroptionen für diesen Standort konfiguriert wurden, wird der Zugriff für alle anderen Benutzer zugelassen.

**Zulassen**: Nur für Benutzer aus den angegebenen Ländern wird der von diesem rekursiven Pfad angeforderte Zugriff auf Ressourcen zugelassen.
	 
##Schritt 3: Definieren der Länder 

Wählen Sie die Länder aus, die Sie für den Pfad zulassen oder blockieren möchten.

Beispiel:

Die Regel blockiert**/Fotos/Straßburg/**einschließlich filtern:

	http://az123456.vo.msecnd.net/Photos/Strasbourg/1000.jpg. 
	http://az123456.vo.msecnd.net/Photos/Strasbourg/Cathedral/1000.jpg. 

##Landeskennzahlen

Die Funktion **Länderfilter** verwendet Landeskennzahlen, um die Länder zu definieren, von denen aus eine Anforderung für ein sicheres Verzeichnis zugelassen oder blockiert wird. Derzeit werden die folgenden zwei Landeskennzahlen unterstützt: „EU“ (Europa) und „AP“ (Asien/Pazifik). Allerdings können diese Landeskennzahlen nicht verwendet werden, um alle Anforderungen aus diesen Regionen zuzulassen oder zu blockieren. Stattdessen werden diese Landeskennzahlen auf Anforderungen angewendet, die von IP-Adressen stammen, die auf eine Region statt auf ein Land verteilt sind. Wählen Sie zum Blockieren oder Zulassen des Zugriffs in EU und AP-Länder die Länder sowie die entsprechende Region EU und AP aus.


##Überlegungen
 
- Es kann bis zu einer Stunde dauern, bis die Änderungen an Ihrem Länderfilter wirksam werden.
- Diese Funktion unterstützt keine Platzhalterzeichen (z. B. *). - Das dem relativen Pfad zugeordnete Länderfilterkonfiguration wird rekursiv auf diesen Pfad angewendet.
- Pro relativen Pfad kann nur eine Regel angewendet werden (Sie können nicht mehrere Länderfilter erstellen, die auf den gleichen relativen Pfad verweisen). Jedoch können mehrere Länderfilter auf einen Ordner angewendet werden. Das liegt an der rekursiven Natur der Länderfilter. Anders ausgedrückt: Ein Unterordner eines zuvor konfigurierten Ordners kann einem anderen Länderfilter zugewiesen werden.

<!---HONumber=July15_HO3-->