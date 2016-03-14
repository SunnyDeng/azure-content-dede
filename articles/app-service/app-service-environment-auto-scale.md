<properties 
	pageTitle="Automatische Skalierung und App Service-Umgebungen" 
	description="Automatische Skalierung und App Service-Umgebungen" 
	services="app-service"
	documentationCenter=""
	authors="btardif" 
	manager="wpickett" 
	editor="" 
/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/01/2016" 
	ms.author="byvinyal"
/>
	
#Automatische Skalierung und App Service-Umgebungen

##Einführung
**App Service-Umgebungen** unterstützen die automatische Skalierung. Dies wird erreicht, indem Ihnen die automatische Skalierung einzelner Workerpools basierend auf Metriken oder Zeitplänen ermöglicht wird.
 
![][intro]
 
Durch die automatische Skalierung können Sie die Ressourcenverwendung optimieren, indem Sie eine **App Service-Umgebung** automatisch vergrößern und verkleinern, sodass sie zu Ihrem Budget und Lastprofil passt.

##Konfigurieren der automatischen Skalierung von Workerpools 
Sie können auf die Funktionalität für die automatische Skalierung über die Registerkarte "Einstellungen" des **Workerpools** zugreifen.
 
![][settings-scale]

Die Benutzeroberfläche sollte Ihnen ziemlich vertraut sein, da sie der für das Skalieren eines **App Service-Plans** entspricht. Sie können einen Skalierungswert manuell eingeben:
 
![][scale-manual]
 
Sie können aber auch ein Profil für die automatische Skalierung konfigurieren:
 
![][scale-profile]
 
Profile für die automatische Skalierung sind nützlich, um Grenzwerte für die Skalierung festzulegen. Auf diese Weise erreichen Sie eine einheitliche Leistung, indem Sie einen unteren Grenzwert der Skalierung (1) festlegen, und vorhersagbare Kosten, indem Sie eine Obergrenze (2) festlegen.
 
![][scale-profile2]
 
Nach der Definition eines Profils können auf Metriken basierende Regeln für die automatische Skalierung hinzugefügt werden, um die Anzahl der Instanzen im **Workerpool** innerhalb der Grenzen, die vom Profil definiert werden, hoch- oder herunterzuskalieren.

![][scale-rule]

 Die **Workerpool-** oder **Front-End**-Metriken können zum Definieren von Regeln für die automatische Skalierung verwendet werden. Hierbei handelt es sich um die gleichen Metriken, die Sie in den Diagrammen des Ressourcenblatts überwachen oder für die Sie Warnungen festlegen können.
 
##Beispiel für die automatische Skalierung
Die automatische Skalierung einer **App Service-Umgebung** kann am besten anhand eines Szenarios dargestellt werden. In diesem Artikel werden alle Aspekte erläutert, die beim Einrichten der automatischen Skalierung erforderlich sind. Zudem werden alle Interaktionen beschrieben, die beim Berücksichtigen der automatischen Skalierung von **App Service-Umgebungen**, die in einer ASE gehostet werden, relevant sind.

###Einführung in das Szenario
Frank ist Systemadministrator für ein Unternehmen. Er hat einen Teil der Workloads, die er verwaltet, in eine **App Service-Umgebung** migriert.

Die **App Service-Umgebung** ist wie folgt für die manuelle Skalierung konfiguriert:
* Front-Ends: 3
* Workerpool 1: 10
* Workerpool 2: 5
* Workerpool 3: 5

**Workerpool 1** wird für Produktionsworkloads verwendet, **Workerpool 2** und **Workerpool 3** werden für Workloads der Qualitätskontrolle und der Entwicklung verwendet.

Die **App Service-Pläne** für die Qualitätskontrolle und die Entwicklung sind für **manuelles Skalieren** konfiguriert, der **App Service-Plan** für die Produktion ist jedoch auf **automatisches Skalieren** festgelegt, um Schwankungen bei Last und Datenverkehr zu bewältigen.

Frank ist mit der Anwendung gut vertraut, und er weiß, dass die Spitzenzeiten für die Last zwischen 9:00 Uhr und 18:00 Uhr liegen, da dies eine **Branchenanwendung** ist, die von Mitarbeitern verwendet wird, während sie sich im Büro befinden. Die Nutzung fällt ab, sobald die Benutzer Feierabend machen. Es gibt jedoch noch eine gewisse Last, da Benutzer über eine Remoteverbindung mit ihren mobilen Geräten oder Heimcomputern zugreifen können. Der **App Service-Plan** für die Produktion ist bereits konfiguriert, um eine **automatische Skalierung** basierend auf der CPU-Auslastung mit den folgenden Regeln vorzunehmen:

![][asp-scale]

|	**Profil für die automatische Skalierung – Werktage – App Service-Plan** |	**Profil für die automatische Skalierung – Wochenenden – App Service-Plan** |
|	----------------------------------------------------	|	----------------------------------------------------	|
|	**Name:** Profil für Werktage |	**Name:** Profil für Wochenenden |
|	**Skalieren nach:** Zeitplan und Leistungsregeln |	**Skalieren nach:** Zeitplan und Leistungsregeln |
|	**Profil:** Werktage |	**Profil:** Wochenende |
|	**Typ:** Serie |	**Typ:** Serie |
|	**Zielbereich:** 5 bis 20 Instanzen |	**Zielbereich:** 3 bis 10 Instanzen |
|	**Tage:** Montag, Dienstag, Mittwoch, Donnerstag, Freitag |	**Tage:** Samstag, Sonntag |
|	**Startzeit:** 9:00 Uhr |	**Startzeit:** 9:00 Uhr |
|	**Zeitzone:** UTC-08 |	**Zeitzone:** UTC-08 |
| | |
|	**Regel für die automatische Skalierung (Hochskalieren)** |	**Regel für die automatische Skalierung (Hochskalieren)** |
|	**Ressource:** Produktion (App Service-Umgebung) |	**Ressource:** Produktion (App Service-Umgebung) |
|	**Metrik:** CPU in % |	**Metrik:** CPU in % |
|	**Operation:** Größer als 60 % |	**Operation:** Größer als 80 % |
|	**Dauer:** 5 Minuten |	**Dauer:** 10 Minuten |
|	**Zeitaggregation:** Durchschnitt |	**Zeitaggregation:** Durchschnitt |
|	**Aktion:** Anzahl um 2 erhöhen |	**Aktion:** Anzahl um 1 erhöhen |
|	**Abkühlen (Minuten):** 15 |	**Abkühlen (Minuten):** 20 |
| | |
|	**Regel für die automatische Skalierung (Herunterskalieren)** |	**Regel für die automatische Skalierung (Herunterskalieren)** |
|	**Ressource:** Produktion (App Service-Umgebung) |	**Ressource:** Produktion (App Service-Umgebung) |
|	**Metrik:** CPU in % |	**Metrik:** CPU in % |
|	**Operation:** Weniger als 30 % |	**Operation:** Weniger als 20 % |
|	**Dauer:** 10 Minuten |	**Dauer:** 15 Minuten |
|	**Zeitaggregation:** Durchschnitt |	**Zeitaggregation:** Durchschnitt |
|	**Aktion:** Anzahl um 1 verringern |	**Aktion:** Anzahl um 1 verringern |
|	**Abkühlen (Minuten):** 20 |	**Abkühlen (Minuten):** 10 |

###Inflationsrate für den App Service-Plan
**App Service-Pläne**, die für die automatische Skalierung konfiguriert sind, nutzen dafür eine maximale Rate pro Stunde. Diese Rate kann basierend auf den Werten berechnet werden, die in der Regel für die automatische Skalierung bereitgestellt werden.

Das Verstehen und Berechnen der **Inflationsrate für den App Service-Plan** ist wichtig für die automatische Skalierung von **Workerpools** in einer **App-Service-Umgebung**, da Größenänderungen bei einem **Workerpool** erst nach einiger Zeit wirksam werden.

Die Inflationsrate für den **App Service-Plan** wird wie folgt berechnet:

![][ASP-Inflation]

Basierend auf der *Regel für die automatische Skalierung – Hochskalieren* für das Profil *Werktage* des **App Service-Plans** für die Produktion würde dies wie folgt aussehen:

![][Equation1]

Für die *Regel für die automatische Skalierung – Hochskalieren* für das Profil *Wochenenden* des **App Service-Plans** für die Produktion würde die Formel wie folgt aufgelöst werden:

![][Equation2]

Dieser Wert kann auch für Operationen zum Herunterskalieren berechnet werden:

Basierend auf der *Regel für die automatische Skalierung – Herunterskalieren* für das Profil *Werktage* des **App Service-Plans** für die Produktion würde dies wie folgt aussehen:

![][Equation3]

Für die *Regel für die automatische Skalierung – Herunterskalieren* für das Profil *Wochenenden* des **App Service-Plans** für die Produktion würde die Formel wie folgt aufgelöst werden:

![][Equation4]

Dies bedeutet, dass der **App Service-Plan** für die Produktion mit einer maximalen Rate von **8** Instanzen pro Stunde während der Woche und **4** Instanzen pro Stunde an Wochenenden vergrößert werden kann. Instanzen können mit einer maximalen Rate von **4** Instanzen pro Stunde während der Woche und **6** Instanzen pro Stunde an Wochenenden freigegeben werden.

Wenn mehrere **App Service-Pläne** in einem **Workerpool** gehostet werden, muss die **Gesamtinflationsrate** berechnet werden. Diese kann als die *Summe* der Inflationsraten für alle **App Service-Pläne** ausgedrückt werden, die in diesem **Workerpool** gehostet werden.

![][ASP-Total-Inflation]

###Definieren von Regeln für die automatische Skalierung für Workerpools mithilfe der Inflationsrate für den App Service-Plan
**Workerpools**, die **App Service-Pläne** hosten, die für die automatische Skalierung konfiguriert sind, muss ein Kapazitätspuffer zugewiesen werden, um die Operationen für die automatische Skalierung zu ermöglichen, mit denen der **App Service-Plan** je nach Bedarf vergrößert/verkleinert wird. Die Mindestgröße des Puffers wäre die berechnete **Gesamtinflationsrate für den App Service-Plan**.

Da Skalierungsoperationen in der **App Service-Umgebung** einige Zeit beanspruchen, sollten bei jeder Änderung weitere Bedarfsänderungen berücksichtigt werden, die stattfinden können, während eine Skalierung ausgeführt wird. Daher empfiehlt es sich, die berechnete **Gesamtinflationsrate für den App Service-Plan** als die minimale Anzahl von Instanzen zu verwenden, die für jede Operation zur automatischen Skalierung hinzugefügt werden.

Mit diesen Informationen kann Frank die folgenden Profile und Regeln für die automatische Skalierung definieren

![][Worker-Pool-Scale]

|	**Profil für die automatische Skalierung – Werktage** |	**Profil für die automatische Skalierung – Wochenenden** |
|	----------------------------------------------------	|	--------------------------------------------	|
|	**Name:** Profil für Werktage |	**Name:** Profil für Wochenenden |
|	**Skalieren nach:** Zeitplan und Leistungsregeln |	**Skalieren nach:** Zeitplan und Leistungsregeln |
|	**Profil:** Werktage |	**Profil:** Wochenende |
|	**Typ:** Serie |	**Typ:** Serie |
|	**Zielbereich:** 13 bis 25 Instanzen |	**Zielbereich:** 6 bis 15 Instanzen |
|	**Tage:** Montag, Dienstag, Mittwoch, Donnerstag, Freitag |	**Tage:** Samstag, Sonntag |
|	**Startzeit:** 7:00 Uhr |	**Startzeit:** 9:00 Uhr |
|	**Zeitzone:** UTC-08 |	**Zeitzone:** UTC-08 |
| | |
|	**Regel für die automatische Skalierung (Hochskalieren)** |	**Regel für die automatische Skalierung (Hochskalieren)** |
|	**Ressource:** Workerpool 1 |	**Ressource:** Workerpool 1 |
|	**Metrik:** Verfügbare Worker |	**Metrik:** Verfügbare Worker |
|	**Operation:** Weniger als 8 |	**Operation:** Weniger als 3 |
|	**Dauer:** 20 Minuten |	**Dauer:** 30 Minuten |
|	**Zeitaggregation:** Durchschnitt |	**Zeitaggregation:** Durchschnitt |
|	**Aktion:** Anzahl um 8 erhöhen |	**Aktion:** Anzahl um 3 erhöhen |
|	**Abkühlen (Minuten):** 180 |	**Abkühlen (Minuten):** 180 |
| | |
|	**Regel für die automatische Skalierung (Herunterskalieren)** |	**Regel für die automatische Skalierung (Herunterskalieren)** |
|	**Ressource:** Workerpool 1 |	**Ressource:** Workerpool 1 |
|	**Metrik:** Verfügbare Worker |	**Metrik:** Verfügbare Worker |
|	**Operation:** Größer als 8 |	**Operation:** größer als 3 |
|	**Dauer:** 20 Minuten |	**Dauer:** 15 Minuten |
|	**Zeitaggregation:** Durchschnitt |	**Zeitaggregation:** Durchschnitt |
|	**Aktion:** Anzahl um 2 verringern |	**Aktion:** Anzahl um 3 verringern |
|	**Abkühlen (Minuten):** 120 |	**Abkühlen (Minuten):** 120 |

Der im Profil definierte Zielbereich wird anhand der minimalen Instanzen, die im Profil für den **App Service-Plan** definiert sind, plus dem Puffer berechnet.

Der maximale Bereich wäre die Summe aller maximalen Bereiche für alle **App Service-Pläne**, die im **Workerpool** gehostet werden.

Bei den Regeln für das zentrale Hochskalieren sollte die Anzahl, um die erhöht wird, auf mindestens 1x die **Inflationsrate für den App Service-Plan** für das zentrale Hochskalieren festgelegt werden.

Die Anzahl, um die verringert wird, kann zwischen 1/2 X und 1 X die **Inflationsrate für den App Service-Plan** für die Herunterskalierung liegen.

###Automatische Skalierung für Front-End-Pools
Regeln für die automatische Skalierung für **Front-Ends** sind einfacher als die für **Workerpools**. Dabei muss vor allem darauf geachtet werden, dass bei der Dauer der Messung und der Abkühldauer die Tatsache berücksichtigt wird, dass das Skalieren in einem **App Service-Plan** nicht sofort ausgeführt wird.

In diesem Szenario weiß Frank, dass sich die Fehlerrate erhöht, sobald Front-Ends eine CPU-Auslastung von 80 % erreichen. Um dies zu verhindern, legt er die Regel für die automatische Skalierung zum Erhöhen von Instanzen wie folgt fest:
 
![][Front-End-Scale]
  
|	**Profil für die automatische Skalierung – Front-Ends** |
|	--------------------------------------------	|
|	**Name:** Automatische Skalierung – Front-Ends |
|	**Skalieren nach:** Zeitplan und Leistungsregeln |
|	**Profil:** Täglich |
|	**Typ:** Serie |
|	**Zielbereich:** 3 bis 10 Instanzen |
|	**Tage:** Täglich |
|	**Startzeit:** 9:00 Uhr |
|	**Zeitzone:** UTC-08 |
| |
|	**Regel für die automatische Skalierung (Hochskalieren)** |
|	**Ressource:** Front-End-Pool |
|	**Metrik:** CPU in % |
|	**Operation:** Größer als 60 % |
|	**Dauer:** 20 Minuten |
|	**Zeitaggregation:** Durchschnitt |
|	**Aktion:** Anzahl um 3 erhöhen |
|	**Abkühlen (Minuten):** 120 |
| |
|	**Regel für die automatische Skalierung (Herunterskalieren)** |
|	**Ressource:** Workerpool 1 |
|	**Metrik:** CPU in % |
|	**Operation:** Weniger als 30 % |
|	**Dauer:** 20 Minuten |
|	**Zeitaggregation:** Durchschnitt |
|	**Aktion:** Anzahl um 3 verringern |
|	**Abkühlen (Minuten):** 120 |

<!-- IMAGES -->
[intro]: ./media/app-service-environment-auto-scale/introduction.png
[settings-scale]: ./media/app-service-environment-auto-scale/settings-scale.png
[scale-manual]: ./media/app-service-environment-auto-scale/scale-manual.png
[scale-profile]: ./media/app-service-environment-auto-scale/scale-profile.png
[scale-profile2]: ./media/app-service-environment-auto-scale/scale-profile-2.png
[scale-rule]: ./media/app-service-environment-auto-scale/scale-rule.png
[asp-scale]: ./media/app-service-environment-auto-scale/asp-scale.png
[ASP-Inflation]: ./media/app-service-environment-auto-scale/asp-inflation-rate.png
[Equation1]: ./media/app-service-environment-auto-scale/equation1.png
[Equation2]: ./media/app-service-environment-auto-scale/equation2.png
[Equation3]: ./media/app-service-environment-auto-scale/equation3.png
[Equation4]: ./media/app-service-environment-auto-scale/equation4.png
[ASP-Total-Inflation]: ./media/app-service-environment-auto-scale/asp-total-inflation-rate.png
[Worker-Pool-Scale]: ./media/app-service-environment-auto-scale/wp-scale.png
[Front-End-Scale]: ./media/app-service-environment-auto-scale/fe-scale.png

<!---HONumber=AcomDC_0302_2016-->