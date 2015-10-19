<properties
   pageTitle="Observações de Atores Confiáveis sobre a serialização do tipo de Ator"
   description="Discute os requisitos básicos para definir as classes serializáveis que podem ser usadas para estabelecer as interfaces e o estado do Ator Confiável do Service Fabric"
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
   ms.date="08/11/2015"
   ms.author="vturecek"/>

# Observações sobre a serialização de tipo dos Atores Confiáveis do Service Fabric

Há alguns aspectos importantes que precisam ser considerados ao definir as interfaces e o estado do ator: os tipos precisam ser serializáveis pelo contrato de dados. Para obter mais informações sobre contratos de dados podem ser encontradas no [MSDN](https://msdn.microsoft.com/library/ms731923.aspx).

## Tipos usados nas interfaces de ator

Os argumentos de todos os métodos e o tipo de resultado da tarefa retornada por cada método definido na [interface ator](service-fabric-reliable-actors-introduction.md#actors) precisam ser serializáveis pelo contrato de dados. Isso também se aplica aos argumentos de métodos definidos nas [interfaces de evento de ator](service-fabric-reliable-actors-events.md#actor-events). (Os métodos de interface de eventos de ator sempre retornam nulo). Por exemplo, se a interface do `IVoiceMail` define um método como:

```csharp

Task<List<Voicemail>> GetMessagesAsync();

```

`List<T>` é um tipo de .NET padrão que já seja serializável pelo contrato de dados. O tipo do `Voicemail` deve ser serializável pelo contrato de dados.

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

O estado do ator deve ser serializável pelo contrato de dados. Por exemplo, se tivermos uma definição de classe de ator que se pareça com:

```csharp

public class VoiceMailActor : Actor<VoicemailBox>, IVoiceMail
{
...

```

A classe de estado será definida com a classe e seus membros anotados com os atributos DataContract e DataMember, respectivamente.

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

<!---HONumber=Oct15_HO2-->