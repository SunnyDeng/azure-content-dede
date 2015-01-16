<properties urlDisplayName="Get Started with Mobile Analytics" pageTitle="Erste Schritte mit Mobile Analytics | Mobile Dev Center" metaKeywords="" description="Erste Schritte mit Mobile Analytics." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Get Started with Mobile Analytics" authors="mahender" manager="dwrede"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="10/10/2014" ms.author="mahender" />

# Erste Schritte mit Mobile Analytics (Capptain)

<div class="dev-center-tutorial-selector sublanding">
<a href="/de-de/documentation/articles/mobile-services-ios-get-started-mobile-analytics" title="iOS" class="current">iOS</a>
</div>

In diesem Lernprogramm fügen Sie Ihrer App unter Verwendung von [Capptain] Mobile Analytics-Funktionen hinzu. Mit Mobile Analytics können Sie festlegen, wie Benutzer mit Ihrer App interagieren und welche Abschnitte am meisten Aktivität fördern. Um diese Daten erfassen zu können, instrumentieren Sie Ihre App mit dem Capptain-SDK.


>[AZURE.NOTE] "Capptain.com" von Microsoft bietet kostenlose Analysen für mobile Apps bei monatlich bis zu 100.000 aktiven Benutzern für Kunden von Azure Mobile Services der Stufe "Standard". Um dieses Angebot nutzen zu können, wenden Sie sich für weitere Anweisungen bitte an uns unter mobileservices@microsoft.com. Im unten stehenden Lernprogramm werden die Features und Funktionen von Capptain.com zusammengefasst und Informationen zur Verwendung gegeben.


In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1. [Initialisieren des Capptain-SDK]
2. [Überladung für UIViewController]

Für dieses Lernprogramm ist Folgendes erforderlich:

* Ein [Capptain]-Konto
* Eine App der [Mobile Services-Stufe "Standard"]

## <a name="initialize"></a>Initialisieren des Capptain-SDK

1. Navigieren Sie zur Seite  mit den **Anwendungsdetails** Ihrer in Capptain registrierten App. Wählen Sie die Registerkarte "SDK" und laden Sie das Paket herunter.

2. Fügen Sie in XCode das Capptain-SDK Ihrem Projekt hinzu, indem Sie mit der rechten Maustaste auf das Projekt klicken und "Dateien hinzufügen zu..." auswählen. Wählen Sie den Ordner "CapptainSDK".

3. Wählen Sie Ihr Projekt aus. Wählen Sie auf der Registerkarte **Build Phases** (Build-Phasen) die Option **Link Binary With Libraries** (Binär mit Bibliotheken verknüpfen) und fügen Sie die folgenden Frameworks hinzu:
    * AdSupport.framework - Link als optional festlegen
    * SystemConfiguration.framework
    * CoreTelephony.framework
    * CFNetwork.framework
    * CoreLocation.framework
    * libxml2.dylib

    >[AZURE.NOTE] Das AdSupport-Framework kann entfernt werden. Capptain benötigt dieses Framework zum Erfassen der IDFA. Die IDFA-Erfassung kann jedoch deaktiviert werden, um die Apple-Richtlinie zu dieser ID zu erfüllen.

4. Importieren Sie in der Implementierungsdatei des Anwendungsdelegats den Capptain-Agent.


        #import "CapptainAgent.h"


5. Initialisieren Sie in "applicationDidFinishLaunching:" oder "application:didFinishLaunchingWithOptions:" den Capptain-Agent, indem Sie "registerapp:identifiedby:" mit Ihrer App-ID und dem SDK-Schlüssel übergeben.

         - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
          [...]
          [CapptainAgent registerApp:@"YOUR_APPID" identifiedBy:@"YOUR_SDK_KEY"];
          [...]
        }

## <a name="instrument"></a>Überladung für UIViewController

1. Finden Sie alle untergeordneten Elemente von "UIViewController" in Ihrem Projekt und stellen Sie sicher, dass sie stattdessen von "CapptainViewController" erben.

        #import <UIKit/UIKit.h>
        #import "CapptainViewController.h"

        // formerly @interface Tab1ViewController : UIViewController<UITextFieldDelegate>
        @interface Tab1ViewController : CapptainViewController<UITextFieldDelegate> {
          UITextField* myTextField1;
          UITextField* myTextField2;
        }

        @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
        @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

2. Finden Sie alle untergeordneten Elemente von "UITableViewController" in Ihrem Projekt und stellen Sie sicher, dass sie stattdessen von "CapptainTableViewController" erben.

    Ihre App ist nun konfiguriert, um Analysedaten an Capptain zu senden.

## Nächste Schritte
Unter [http://www.capptain.com] erfahren Sie mehr zu den Vorteilen von Capptain für Ihre App.(http://www.capptain.com)

<!-- Anchors. -->
[Initialisieren des Capptain-SDK]: #initialize
[Überladung für UIViewController]: #instrument


<!-- URLs. -->
[Capptain]: http://www.capptain.com
[Mobile Services-Stufe "Standard"]: /de-de/pricing/details/mobile-services/

<!--HONumber=35.1-->
