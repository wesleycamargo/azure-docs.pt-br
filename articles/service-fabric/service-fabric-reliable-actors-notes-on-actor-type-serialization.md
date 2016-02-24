<properties
   pageTitle="Observações de Reliable Actors sobre a serialização do tipo de ator | Microsoft Azure"
   description="Discute os requisitos básicos para definir as classes serializáveis que podem ser usadas para estabelecer as interfaces e o estado dos Reliable Actors do Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/13/2015"
   ms.author="vturecek"/>

# Observações sobre a serialização de tipo dos Reliable Actors do Service Fabric

Você deve ter alguns aspectos importantes em mente ao definir as interfaces e o estado de um ator. Os tipos precisam ser serializáveis de acordo com o contrato de dados. Saiba mais sobre contratos de dados [no MSDN](https://msdn.microsoft.com/library/ms731923.aspx).

## Tipos de interface do ator

Os argumentos de todos os métodos e o tipo de resultado da tarefa retornada por cada método, conforme definido na [interface do ator](service-fabric-reliable-actors-introduction.md#actors), precisam ser serializáveis de acordo com o contrato de dados. Isso também se aplica aos argumentos dos métodos definidos nas [interfaces de evento de ator](service-fabric-reliable-actors-events.md#actor-events). (Os métodos de interface de eventos de ator sempre retornam nulo). Por exemplo, se a interface do `IVoiceMail` define um método como:

```csharp

Task<List<Voicemail>> GetMessagesAsync();

```

`List<T>` é um tipo de .NET padrão que já é serializável de acordo com o contrato de dados. O tipo `Voicemail` também deve ser serializável de acordo com o contrato de dados.

```csharp

[DataContract]
public class Voicemail
{
    [DataMember]
    public Guid Id { get; set; }

    [DataMember]
    public string Message { get; set; }

    [DataMember]
    public DateTime ReceivedAt { get; set; }
}

```

## Classe de estado do ator

O estado do ator deve ser serializável de acordo com o contrato de dados. Por exemplo, uma definição de classe de ator pode ter a seguinte aparência:

```csharp

public class VoiceMailActor : StatefulActor<VoicemailBox>, IVoiceMail
{
...

```

A classe de estado será definida pela classe e seus membros serão anotados com os atributos **DataContract** e **DataMember**, respectivamente.

```csharp

[DataContract]
public class VoicemailBox
{
    public VoicemailBox()
    {
        this.MessageList = new List<Voicemail>();
    }

    [DataMember]
    public List<Voicemail> MessageList { get; set; }

    [DataMember]
    public string Greeting { get; set; }
}

```

<!---HONumber=AcomDC_0121_2016-->