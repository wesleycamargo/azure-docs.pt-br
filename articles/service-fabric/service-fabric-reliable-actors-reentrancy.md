<properties
   pageTitle="Reentrância de Reliable Actors | Microsoft Azure"
   description="Introdução à reentrância para Reliable Actors do Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/19/2016"
   ms.author="amanbha"/>


# Reentrância de Reliable Actors
Por padrão, o tempo de execução dos Atores do Fabric permitem reentrância baseada no contexto de chamada lógica. Isso possibilita que os atores sejam reentrantes se estiverem na mesma cadeia de contexto de chamada. Por exemplo, se um Ator A envia a mensagem para o Ator B, que envia a mensagem para o Ator C. Como parte do processamento da mensagem no caso de o Ator C chamar o Ator A, a mensagem é reentrante e por isso será permitida. Todas as outras mensagens que fazem parte de um contexto de chamada diferente serão bloqueadas no Ator A até a conclusão do processamento.

Os atores que não querem permitir a reentrância baseada no contexto de chamada lógica podem desabilitá-la decorando a classe do ator com `ReentrantAttribute(ReentrancyMode.Disallowed)`.

```csharp
public enum ReentrancyMode
{
    LogicalCallContext = 1,
    Disallowed = 2
}
```

O código a seguir mostra uma classe de ator que define o modo de reentrância como `ReentrancyMode.Disallowed`. Nesse caso, se um ator envia uma mensagem reentrante para outro ator, será lançada uma exceção do tipo `FabricException`.

```csharp
[Reentrant(ReentrancyMode.Disallowed)]
class VoicemailBoxActor : StatefulActor<VoicemailBox>, IVoicemailBoxActor
{
    ...
}
```

<!---HONumber=AcomDC_0224_2016-->