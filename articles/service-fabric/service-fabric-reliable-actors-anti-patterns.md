<properties
   pageTitle="Alguns antipadrões de Atores da Malha do Serviço do Azure"
   description="Algumas possíveis armadilhas para clientes que estão aprendendo sobre os Atores da Malha do Serviço do Azure"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/17/2015"
   ms.author="claudioc"/>

# Padrão de design de Atores da Malha do Serviço: alguns antipadrões

Identificamos as seguintes armadilhas possíveis para clientes que estão aprendendo sobre os Atores da Malha do Serviço do Azure:

* Tratar os Atores da Malha do Serviço do Azure como um sistema transacional. Os Atores da Malha do Serviço do Azure não é um sistema baseado em confirmação de duas fases que oferece ACID. Se não implementarmos a persistência opcional e o computador em que o ator está em execução for desativado, seu estado atual o acompanhará. O ator surgirá em outro nó muito rapidamente, porém, a menos que tenhamos implementado a persistência de suporte, o estado não retornará. No entanto, entre o aproveitamento de novas tentativas, filtragem duplicada e/ou design idempotentes, você pode atingir um alto nível de confiabilidade e consistência.

* Bloquear. Tudo o que fazemos em Atores da Malha do Serviço do Azure deve ser assíncrono. De modo geral, isso costuma ser fácil porque agora as APIs assíncronas são produtivas na plataforma Microsoft. Mas se, por algum motivo, tivermos que interagir com um sistema que fornece apenas uma API de bloqueio, vamos precisar colocá-la em um wrapper que use explicitamente o Pool de Threads .NET.

* “Pular” o arquiteto. Deixe o ambiente trabalhar. Para os desenvolvedores que estão acostumados a se preocupar com as coleções e os bloqueios simultâneos ou a usar ferramentas para compilar objetos do XML, pode ser difícil simplesmente codificar uma classe que realize tarefas simples como atribuir um valor a uma variável ou agendar um trabalho. As tarefas agendadas são internas. Os bloqueios não são necessários. O estado não é um inimigo mortal. Para muitas pessoas que tenham trabalhado muito do lado do servidor em ambientes de larga escala, pode levar algum tempo para que elas se acostumem com isso.

* Tornar um único ator o afunilamento. Normalmente, é muito fácil cair em armadilhas como esta, ter milhões de atores afunilados em uma única instância de outro ator. Use a abordagem de agregação que demonstramos em [padrão de design de computação distribuída](service-fabric-reliable-actors-pattern-distributed-computation.md).

* Mapear modelos de entidade cegamente. Isso acontece com desenvolvedores que estão vindo de um universo relacional, onde os problemas são modelados usando entidades e seus relacionamentos. Embora essa abordagem ainda seja útil para entender o domínio do assunto, ela deve ser unida ao pensamento orientado ao serviço e combinada com o comportamento.
 

<!---HONumber=July15_HO2-->