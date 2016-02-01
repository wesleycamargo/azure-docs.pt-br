<properties
   pageTitle="Alguns antipadrões dos Atores do Service Fabric do Azure | Microsoft Azure"
   description="Algumas possíveis armadilhas para clientes que estão aprendendo sobre os Atores da Malha do Serviço do Azure"
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

# Padrão de design dos Reliable Actors: alguns antipadrões

Identificamos as seguintes armadilhas possíveis para clientes que estão aprendendo sobre os Reliable Actors do Service Fabric:

* Trate Reliable Actors como um sistema transacional. Os Reliable Actors do Service Fabric não são um sistema de duas fases baseado em confirmação que oferece atomicidade, consistência, isolamento e durabilidade (ACID). Se você não implantar a persistência opcional e o computador em que o ator está em execução for desativado, seu estado atual o acompanhará. O ator surgirá em outro nó muito rapidamente, mas, a menos que tenhamos implementado a persistência de suporte, o estado não retornará. No entanto, ao aproveitar novas tentativas, filtragem duplicada e/ou design idempotentes, você poderá atingir um alto nível de confiabilidade e consistência.

* Bloquear. Tudo o que você faz no Reliable Actors deve ser assíncrono. De modo geral, isso costuma ser fácil porque agora há muitas APIs assíncronas na plataforma Microsoft. Mas se você precisar interagir com um sistema que forneça apenas uma API de bloqueio, terá que colocar isso em um wrapper que use explicitamente o pool de threads do .NET.

* Extrapole a arquitetura e deixe o ambiente funcionar. Para os desenvolvedores que estão acostumados a se preocupar com as coleções e os bloqueios simultâneos ou a usar ferramentas para compilar objetos do XML, pode ser difícil simplesmente codificar uma classe que realize tarefas simples como atribuir um valor a uma variável ou agendar um trabalho. As tarefas agendadas são internas e os bloqueios não são necessários. O estado não é um inimigo mortal. Para desenvolvedores que trabalharam muito no lado do servidor em ambientes de grande escala, isso poderá exigir alguma familiarização.

* Tornar um único ator o afunilamento. É fácil cair em armadilhas como esta, ter milhões de atores afunilados em uma única instância de outro ator. Use a abordagem de agregação que demonstramos em [Padrão de design de computação distribuída](service-fabric-reliable-actors-pattern-distributed-computation.md).

* Mapear modelos de entidade cegamente. Isso acontece com desenvolvedores que estão vindo de um universo relacional, onde os problemas são modelados usando entidades e seus relacionamentos. Embora essa abordagem ainda seja útil para entender o domínio do assunto, ela deve ser unida ao pensamento orientado ao serviço e combinada com o comportamento.

<!---HONumber=AcomDC_0121_2016-->