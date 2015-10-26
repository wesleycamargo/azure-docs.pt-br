<properties
   pageTitle="Coleções Confiáveis"
   description="As Coleções Confiáveis permitem desenvolver aplicativos em nuvem altamente disponíveis, escalonáveis e de baixa latência."
   services="service-fabric"
   documentationCenter=".net"
   authors="mcoskun"
   manager="timlt"
   editor="masnider,jessebenson"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="08/05/2015"
   ms.author="mcoskun"/>

# Coleções Confiáveis

As Coleções Confiáveis permitem desenvolver aplicativos em nuvem altamente disponíveis, escalonáveis e de baixa latência como se você estivesse desenvolvendo aplicativos de máquina única. As classes no namespace `Microsoft.ServiceFabric.Data.Collections` fornecem um conjunto de coleções prontas para uso que automaticamente tornam seu estado altamente disponível. Os desenvolvedores só precisam programar para as APIs de Coleções Confiáveis e permitir que as Coleções Confiáveis gerenciem o estado local e replicado.

A principal diferença entre Coleções Confiáveis e outras tecnologias de alta disponibilidade (como Redis, serviço Tabela do Azure e serviço Fila do Azure) é que o estado é mantido localmente na instância de serviço, além de se tornar altamente disponível. Isso significa que:

1. Todas as leituras são locais, o que resulta em leituras de baixa latência e alta taxa de transferência.
2. Todas as gravações geram o mínimo de operações de E/S de rede, o que resulta em gravações de baixa latência e alta taxa.

![Imagem da evolução das coleções.](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

As Coleções Confiáveis podem ser interpretadas como a evolução natural das classes `System.Collections`: um novo conjunto de coleções projetadas para a nuvem e aplicativos com vários computadores sem aumentar a complexidade para desenvolvedores. Dessa forma, elas são:

1. Replicadas: alterações de estado são replicadas para alta disponibilidade.
2. Persistentes: os dados são persistidos no disco para durabilidade contra interrupções em larga escala (por exemplo, uma interrupção de energia de datacenter).
3. Assíncronas: as APIs são assíncronas para garantir que os threads não sejam bloqueados quando gerarem E/S.
4. Transacionais: as APIs utilizam a abstração de transações para permitir que você gerencie facilmente várias Coleções Confiáveis dentro de um serviço.

As Coleções Confiáveis fornecem sólidas garantias de consistência prontas para uso para facilitar o raciocínio sobre o estado do aplicativo. Consistência sólida é obtida garantindo que confirmações de transação só sejam concluídas depois que toda a transação tiver sido aplicada em um quorum de réplicas, inclusive a primária. Para obter consistência mais fraca, o aplicativo pode confirmar para o cliente/solicitante antes de a confirmação assíncrona retornar.

As APIs de Coleções Confiáveis são uma evolução das APIs de coleções simultâneas (encontradas no namespace `System.Collections.Concurrent`):

1. Assíncronas: retornam uma tarefa já que, ao contrário das Coleções Confiáveis, as operações são replicadas e persistidas.
2. Sem parâmetro de saída: usam `ConditionalResult<T>` para retornar um bool e um valor em vez de parâmetros. `ConditionalResult<T>` é como `Nullable<T>`, mas não requer T para ser um struct.
3. Transações: usam um objeto de transação para permitir que o usuário agrupe as ações em várias Coleções Confiáveis em uma transação.

Hoje, `Microsoft.ServiceFabric.Data.Collections` contém duas coleções:

1. [Dicionário Confiável](https://msdn.microsoft.com/library/azure/dn971511.aspx): representa uma coleção de pares chave/valor replicada, transacional e assíncrona. Semelhante a `ConcurrentDictionary`, a chave e o valor podem ser de qualquer tipo.
2. [Fila Confiável](https://msdn.microsoft.com/library/azure/dn971527.aspx): representa uma fila PEPS (primeiro a entrar, primeiro a sair) estrita, assíncrona, transacional e replicada. Semelhante a `ConcurrentQueue`, a chave pode ser de qualquer tipo.

## Níveis de isolamento
Nível de isolamento é uma medida do grau de isolamento alcançado. Isolamento significa que uma transação se comporta como se estivesse em um sistema que permite que somente uma transação esteja em andamento em qualquer determinado momento.

As Coleções Confiáveis escolhem automaticamente o nível de isolamento a ser usado para uma determinada operação de leitura dependendo da operação e da função da réplica.

Há dois níveis de isolamento com suporte nas Coleções Confiáveis:

- **Leitura repetida**: "Especifica que as instruções não podem ler dados que foram modificados, mas ainda não foram confirmados por outras transações e que nenhuma outra transação pode modificar dados que foram lidos pela transação atual até que a transação atual seja concluída. (https://msdn.microsoft.com/pt-BR/library/ms173763.aspx)"
- **Instantâneo**: "Especifica que os dados lidos por qualquer instrução em uma transação serão a versão transacionalmente consistente dos dados que existiam no início da transação. A transação pode reconhecer apenas modificações de dados que foram confirmadas antes do início da transação. Modificações de dados feitas por outras transações após o início da transação atual não são visíveis para instruções em execução na transação atual. O efeito é como se as instruções em uma transação obtivessem um instantâneo dos dados confirmados conforme existiam no início da transação. (https://msdn.microsoft.com/pt-BR/library/ms173763.aspx)"

O Dicionário Confiável e a Fila Confiável dão suporte Read Your Writes. Em outras palavras, qualquer gravação em uma transação será visível para uma leitura seguinte que pertence à mesma transação.

### Dicionário Confiável
| Operação\\função | Primário | Secundário |
| --------------------- | :--------------- | :--------------- |
| Leitura de entidade única | Leitura repetida | Instantâneo |
| Enumeração\\contagem | Instantâneo | Instantâneo |

### Fila Confiável
| Operação\\função | Primário | Secundário |
| --------------------- | :--------------- | :--------------- |
| Leitura de entidade única | Instantâneo | Instantâneo |
| Enumeração\\contagem | Instantâneo | Instantâneo |

## Modelo de persistência
O Gerenciador de Estado Confiável e as Coleções Confiáveis seguem um modelo de persistência chamado Log e Ponto de Verificação. Esse é um modelo em que cada alteração de estado é registrada no disco e aplicada apenas na memória. O estado de conclusão em si é persistido apenas ocasionalmente (também conhecido como ponto de verificação). O benefício que ele oferece é:

- Os Deltas são transformados em gravações sequenciais somente de acréscimo em disco para melhorar o desempenho.

Para entender melhor o modelo de log e ponto de verificação, primeiro vamos analisar o cenário de disco infinito. O Gerenciador de Estado Confiável registra cada operação antes que ela seja replicada. Isso permite que a Coleção Confiável se aplique somente à operação na memória. Como os logs são persistidos, mesmo quando a réplica falha e precisa ser reiniciada, o Gerenciador de Estado Confiável tem informações suficientes em seus logs para repetir todas as operações que a réplica perdeu. Como o disco é infinito, registros de log nunca precisam ser removidos e a Coleção Confiável precisa apenas gerenciar o estado na memória.

Agora vamos examinar o cenário de disco finito. Em um ponto, o Gerenciador de Estado Confiável ficará sem espaço em disco. Antes que isso ocorra, o Gerenciador de Estado Confiável precisa truncar seu log para liberar espaço para os registros mais recentes. Ele irá solicitar as Coleções Confiável para criar um ponto de verificação do seu estado na memória. É responsabilidade da Coleção Confiável persistir seu estado até esse ponto. Depois que as Coleções Confiáveis concluírem seus pontos de verificação, o Gerenciador de Estado Confiável poderá truncar o log para liberar espaço em disco. Dessa forma, quando a réplica precisar ser reiniciada, as Coleções Confiáveis irão recuperar seu estado com ponto de verificação, e o Gerenciador de Estado Confiável irá recuperar e reproduzir todas as alterações de estado que ocorreram desde o ponto de verificação.

## Bloqueio
Nas Coleções Confiáveis, todas as transações têm duas fases: uma transação não libera os bloqueios que adquiriu até que a transação seja encerrada com uma confirmação ou anulação.

Coleções Confiáveis sempre utilizam bloqueios exclusivos. Para leituras, o bloqueio depende de alguns fatores. Qualquer operação de leitura feita usando o isolamento de instantâneo é livre de bloqueio. Qualquer operação de Leitura Repetida por padrão recebe bloqueios compartilhados. No entanto, para qualquer operação de leitura que dá suporte à Leitura Repetida, o usuário pode solicitar um bloqueio de atualização, em vez do bloqueio compartilhado. Bloqueio de atualização é um bloqueio assimétrico usado para evitar uma forma comum de deadlock que ocorre quando várias transações bloqueiam recursos para atualização potencial em um momento posterior.

A matriz de compatibilidade de bloqueio pode ser encontrada abaixo:

| Solicitação\\concedida | Nenhum | Compartilhado | Atualizar | Exclusivo |
| ----------------- | :----------- | :----------- | :---------- | :----------- |
| Compartilhado | Sem conflito | Sem conflito | Conflito | Conflito |
| Atualizar | Sem conflito | Sem conflito | Conflito | Conflito |
| Exclusivo | Sem conflito | Conflito | Conflito | Conflito |

Observe que o argumento de tempo-limite nas APIs de Coleções Confiáveis é usado como uma detecção de deadlock. Por exemplo, duas transações (T1 e T2) estão tentando ler e atualizar K1. É possível que ocorra um deadlock, porque ambas acabam tendo o bloqueio compartilhado. Nesse caso, uma ou ambas as operações atingirão o tempo limite.

Observe que o cenário de deadlock acima é um ótimo exemplo de como o bloqueio de atualização pode evitar deadlocks.

## Recomendações

- **NÃO** modifique um objeto de tipo personalizado retornado por operações de leitura (por exemplo, `TryPeekAsync` ou `TryGetAsync`). As Coleções Confiáveis, como as Coleções Simultâneas, retornam uma referência aos objetos e não uma cópia.
- **FAÇA** uma cópia em profundidade do objeto de tipo personalizado retornado antes de modificá-lo. Como structs e tipos internos são pass-by-value, você não precisa fazer uma cópia em profundidade neles.
- **NÂO** use `TimeSpan.MaxValue` para tempos limites. Tempos limite devem ser usados para detectar deadlocks.
- **NÃO** crie uma transação dentro da instrução `using` de outra transação porque isso pode causar deadlocks.

Eis aqui algumas coisas que se deve manter em mente:

- O tempo limite padrão é de 4 segundos para todas as APIs de Coleções Confiáveis. A maioria dos usuários não deve substituir isso.
- O token de cancelamento padrão é `CancellationToken.None` em todas as APIs de Coleções Confiáveis.
- As Enumerações são instantâneos consistentes dentro de uma coleção. No entanto, as enumerações de várias coleções não são consistentes entre as coleções.
- Para obter alta disponibilidade para as Coleções Confiáveis, cada serviço deve ter pelo menos um destino e uma réplica mínima com tamanho definido como 3.

## Próximas etapas

- [Início Rápido dos Serviços Confiáveis](service-fabric-reliable-services-quick-start.md)
- [Introdução aos serviços de API da Web da Malha de Serviços](service-fabric-reliable-services-communication-webapi.md)
- [Uso avançado do modelo de programação de Serviços Confiáveis](../Service-Fabric/service-fabric-reliable-services-advanced-usage.md)
- [Referência do desenvolvedor para Coleções Confiáveis](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

<!---HONumber=Oct15_HO3-->