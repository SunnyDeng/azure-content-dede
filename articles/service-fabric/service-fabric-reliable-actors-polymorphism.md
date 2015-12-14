<properties
   pageTitle="Polymorphie im Akteurframework | Microsoft Azure"
   description="Erstellen Sie Hierarchien von .NET-Schnittstellen und -Typen im Reliable Actors-Framework zur Wiederverwendung von Funktionen und API-Definitionen."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="12/01/2015"
   ms.author="seanmck"/>

# Polymorphie im Reliable Actor-Framework

Das Reliable Actor-Framework vereinfacht die Programmierung verteilter Systeme, sodass Sie Ihren Dienst unter Verwendung zahlreicher Techniken erstellen können, die Sie auch beim objektorientierten Design verwenden würden. Eine dieser Techniken ist Polymorphie, bei der Typen und Schnittstellen von allgemeineren übergeordneten Elementen erben. Die Vererbung im Akteurframework folgt normalerweise dem .NET-Modell mit einigen zusätzlichen Einschränkungen.

## Schnittstellen

Das Akteurframework erfordert die Definition mindestens einer Schnittstelle, die von Ihrem Akteurtyp implementiert wird. Diese Schnittstelle wird verwendet, um eine Proxyklasse zu generieren, die von den Clients zur Kommunikation mit dem Akteur verwendet werden kann. Schnittstellen können von anderen Schnittstellen erben, solange jede durch einen Akteurtyp implementierte sowie alle übergeordneten Schnittstellen letztendlich von IActor abgeleitet werden. Dabei handelt es sich um die durch die Plattform definierte Basisschnittstelle für Akteure. Folglich kann das klassische Polymorphie-Beispiel mithilfe von Formen wie folgt aussehen:

![Schnittstellenhierarchie für Formakteure][shapes-interface-hierarchy]


## Typen

Sie können auch eine Hierarchie von Akteurtypen erstellen, die von der durch die Plattform bereitgestellten Akteurbasisklasse abgeleitet werden. Für die statusbehafteten Akteure können Sie ebenso eine Hierarchie von Statustypen erstellen. Im Fall von Formen haben Sie möglicherweise einen Basistyp `Shape` mit einem Statustyp `ShapeState`.

    public abstract class Shape : Actor<ShapeState>, IShape
    {
        ...
    }

Untertypen von `Shape` können Untertypen von `ShapeType` zum Speichern weiterer spezifischer Eigenschaften verwenden.

    [ActorService(Name = "Circle")]
    public class Circle : Shape, ICircle
    {
        private CircleState CircleState => this.State as CircleState;

        public override ShapeState InitializeState()
        {
            return new CircleState();
        }

        [Readonly]
        public override Task<int> GetVerticeCount()
        {
            return Task.FromResult(0);
        }

       [Readonly]
       public override Task<double> GetArea()
       {
           return Task.FromResult(
               Math.PI*
               this.CircleState.Radius*
               this.CircleState.Radius);
       }

       ...
    }

Beachten Sie das `ActorService`-Attribut des Akteurtyps. Dies teilt dem Service Fabric-SDK mit, dass es automatisch einen Dienst zum Hosten von Akteuren dieses Typs erstellen soll. In einigen Fällen sollten Sie möglicherweise einen Basistyp erstellen, der ausschließlich für die Freigabe von Funktionen für Untertypen vorgesehen ist und nicht verwendet wird, um tatsächliche Akteure zu instanziieren. In diesen Fällen sollten Sie das Schlüsselwort `abstract` verwenden, um anzugeben, dass Sie nie einen Akteur basierend auf diesem Typ erstellen werden.


## Nächste Schritte

- Weitere Informationen darüber [wie das Reliable Actors-Framework die Service Fabric-Plattform nutzt](service-fabric-reliable-actors-platform.md), um Zuverlässigkeit, Skalierbarkeit und konsistente Status zu gewährleisten.
- Erfahren Sie mehr über den [Akteurlebenszyklus](service-fabric-reliable-actors)

<!-- Image references -->

[shapes-interface-hierarchy]: ./media/service-fabric-reliable-actors-polymorphism/Shapes-Interface-Hierarchy.png

<!---HONumber=AcomDC_1203_2015-->