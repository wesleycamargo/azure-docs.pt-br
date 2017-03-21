---
title: "Salvar o estado do aplicativo nos microsserviços do Azure | Microsoft Docs"
description: "Os serviços com monitoração de estado do Service Fabric fornecem coleções confiáveis que permitem escrever aplicativos em nuvem altamente disponíveis, escalonáveis e com baixa latência."
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: masnider,vturecek
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 3/1/2017
ms.author: mcoskun
translationtype: Human Translation
ms.sourcegitcommit: 4952dfded6ec5c4512a61cb18d4c754bf001dade
ms.openlocfilehash: b5fab7cf91493d477cafd66e27e346ea3ad02f04
ms.lasthandoff: 03/02/2017


---
# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>Introdução à Reliable Collections nos serviços com monitoração de estado do Service Fabric do Azure
As Coleções Confiáveis permitem desenvolver aplicativos em nuvem altamente disponíveis, escalonáveis e de baixa latência como se você estivesse desenvolvendo aplicativos de computador único. As classes no namespace **Microsoft.ServiceFabric.Data.Collections** fornecem um conjunto de coleções prontas para uso que automaticamente tornam seu estado altamente disponível. Os desenvolvedores só precisam programar para as APIs de Coleções Confiáveis e permitir que as Coleções Confiáveis gerenciem o estado local e replicado.

A principal diferença entre Coleções Confiáveis e outras tecnologias de alta disponibilidade (como Redis, serviço Tabela do Azure e serviço Fila do Azure) é que o estado é mantido localmente na instância de serviço, além de se tornar altamente disponível. Isso significa que:

* Todas as leituras são locais, o que resulta em leituras de baixa latência e alta taxa de transferência.
* Todas as gravações geram o mínimo de operações de E/S de rede, o que resulta em gravações de baixa latência e alta taxa.

![Imagem da evolução das coleções.](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

As Coleções Confiáveis podem ser interpretadas como a evolução natural das classes **System.Collections** : um novo conjunto de coleções projetadas para a nuvem e aplicativos com vários computadores sem aumentar a complexidade para desenvolvedores. Sendo assim, as Coleções confiáveis são:

* Replicadas: alterações de estado são replicadas para alta disponibilidade.
* Persistentes: os dados são persistidos no disco para durabilidade contra interrupções em larga escala (por exemplo, uma interrupção de energia de datacenter).
* Assíncronas: as APIs são assíncronas para garantir que os threads não sejam bloqueados quando gerarem E/S.
* Transacionais: as APIs utilizam a abstração de transações para que você possa gerenciar facilmente várias Coleções Confiáveis dentro de um serviço.

As Coleções Confiáveis fornecem sólidas garantias de consistência prontas para uso para facilitar o raciocínio sobre o estado do aplicativo.
A coerência forte é obtida com a garantia de que as confirmações de transação só são concluídas depois que toda a transação tiver sido registrada em uma quorum de réplicas que seja a maioria, incluindo a primária.
Para obter consistência mais fraca, os aplicativos podem confirmar para o cliente/solicitante antes de a confirmação assíncrona retornar.

As APIs de Coleções Confiáveis são uma evolução das APIs de coleções simultâneas (encontradas no namespace **System.Collections.Concurrent** ):

* Assíncronas: retorna uma tarefa, já que, ao contrário das coleções concorrentes, as operações são replicadas e mantidas.
* Sem parâmetro de saída: usam `ConditionalValue<T>` para retornar um bool e um valor em vez de parâmetros. `ConditionalValue<T>` é como `Nullable<T>`, mas não requer T para ser um struct.
* Transações: usam um objeto de transação para permitir que o usuário agrupe as ações em várias Coleções Confiáveis em uma transação.

Hoje, **Microsoft.ServiceFabric.Data.Collections** contém duas coleções:

* [Dicionário Confiável](https://msdn.microsoft.com/library/azure/dn971511.aspx): representa uma coleção de pares chave/valor replicada, transacional e assíncrona. Semelhante a **ConcurrentDictionary**, a chave e o valor podem ser de qualquer tipo.
* [Fila Confiável](https://msdn.microsoft.com/library/azure/dn971527.aspx): representa uma fila PEPS (primeiro a entrar, primeiro a sair) estrita, assíncrona, transacional e replicada. Semelhante a **ConcurrentQueue**, a chave pode ser de qualquer tipo.

## <a name="isolation-levels"></a>Níveis de isolamento
Nível de isolamento define o grau no qual a transação deve ser isolada de modificações feitas por outras transações.
Há dois níveis de isolamento com suporte nas Coleções Confiáveis:

* **Leitura repetida**: especifica que as instruções não podem ler dados que foram modificados, mas ainda não foram confirmados por outras transações e que nenhuma outra transação pode modificar dados que foram lidos pela transação atual até que a transação atual seja concluída. Para obter mais detalhes, consulte [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).
* **Instantâneo**: especifica que os dados lidos por qualquer instrução em uma transação serão a versão transacionalmente consistente dos dados que existiam no início da transação.
  A transação pode reconhecer apenas modificações de dados que foram confirmadas antes do início da transação.
  Modificações de dados feitas por outras transações após o início da transação atual não são visíveis para instruções em execução na transação atual.
  O efeito é como se as instruções em uma transação obtivessem um instantâneo dos dados confirmados conforme existiam no início da transação.
  Os instantâneos são consistentes entre as Coleções Confiáveis.
  Para obter mais detalhes, consulte [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).

As Coleções Confiáveis escolhem automaticamente o nível de isolamento a ser usado para uma determinada operação de leitura dependendo da operação e da função da réplica no momento da criação da transação.
A seguir está a tabela que descreve os padrões de nível de isolamento para operações de Dicionário Confiável e Fila.

| Operação\função | Primário | Secundário |
| --- |:--- |:--- |
| Leitura de entidade única |Leitura repetida |Instantâneo |
| Enumeração\contagem |Instantâneo |Instantâneo |

> [!NOTE]
> Exemplos comuns de Operações de Entidade Única são `IReliableDictionary.TryGetValueAsync`, `IReliableQueue.TryPeekAsync`.
> 
> 

O Dicionário Confiável e a Fila Confiável dão suporte Read Your Writes.
Em outras palavras, qualquer gravação em uma transação será visível para uma leitura seguinte que pertence à mesma transação.

## <a name="locking"></a>Bloqueio
Nas Coleções Confiáveis, todas as transações têm duas fases: uma transação não libera os bloqueios que adquiriu até que a transação seja encerrada com uma confirmação ou anulação.

Dicionário Confiável usa bloqueio para todas as operações de entidade única em nível de linha.
Fila Confiável compensa simultaneidade para propriedade PEPS transacional estrita.
Fila Confiável usa bloqueios de nível de operação permitindo que uma transação com `TryPeekAsync` e/ou `TryDequeueAsync` e uma transação com `EnqueueAsync` por vez.
Observe que, para preservar PEPS, se um `TryPeekAsync` ou `TryDequeueAsync` nunca observarem que a Fila Confiável está vazia, também bloquearão `EnqueueAsync`.

Operações de gravação sempre utilizam bloqueios exclusivos.
Para operações de leitura, o bloqueio depende de alguns fatores.
Qualquer operação de leitura feita usando o isolamento de Instantâneo é livre de bloqueio.
Qualquer operação de Leitura Repetida por padrão recebe bloqueios compartilhados.
No entanto, para qualquer operação de leitura que dá suporte à Leitura Repetida, o usuário pode solicitar um bloqueio de atualização, em vez do bloqueio compartilhado.
Um Bloqueio de atualização é um bloqueio assimétrico usado para evitar uma forma comum de deadlock que ocorre quando várias transações bloqueiam recursos para atualização potencial em um momento posterior.

A matriz de compatibilidade de bloqueio pode ser encontrada abaixo:

| Solicitação\concedida | Nenhum | Compartilhado | Atualizar | Exclusivo |
| --- |:--- |:--- |:--- |:--- |
| Compartilhado |Sem conflito |Sem conflito |Conflito |Conflito |
| Atualizar |Sem conflito |Sem conflito |Conflito |Conflito |
| Exclusivo |Sem conflito |Conflito |Conflito |Conflito |

Observe que o argumento de tempo-limite nas APIs de Coleções Confiáveis é usado como uma detecção de deadlock.
Por exemplo, duas transações (T1 e T2) estão tentando ler e atualizar K1.
É possível que ocorra um deadlock, porque ambas acabam tendo o bloqueio compartilhado.
Nesse caso, uma ou ambas as operações atingirão o tempo limite.

Observe que o cenário de deadlock acima é um ótimo exemplo de como um Bloqueio de atualização pode evitar deadlocks.

## <a name="persistence-model"></a>Modelo de persistência
O Gerenciador de Estado Confiável e as Coleções Confiáveis seguem um modelo de persistência chamado Log e Ponto de Verificação.
Esse é um modelo em que cada alteração de estado é registrada no disco e aplicada apenas na memória.
O estado de conclusão em si é persistido apenas ocasionalmente (também conhecido como ponto de verificação).
O benefício é que deltas são transformados em gravações sequenciais somente de acréscimo em disco para melhorar o desempenho.

Para entender melhor o modelo de Log e Ponto de verificação, primeiro vamos analisar o cenário de disco infinito.
O Gerenciador de Estado Confiável registra cada operação antes que ela seja replicada.
Isso permite que a Coleção Confiável se aplique somente à operação na memória.
Como os logs são persistidos, mesmo quando a réplica falha e precisa ser reiniciada, o Gerenciador de Estado Confiável tem informações suficientes em seus logs para repetir todas as operações que a réplica perdeu.
Como o disco é infinito, registros de log nunca precisam ser removidos e a Coleção Confiável precisa apenas gerenciar o estado na memória.

Agora vamos examinar o cenário de disco finito.
Os registros do log se acumulam, o Gerenciador de Estado Confiável ficará sem espaço em disco.
Antes que isso ocorra, o Gerenciador de Estado Confiável precisa truncar seu log para liberar espaço para os registros mais recentes.
Ele solicitará às Reliable Collections para criar um ponto de verificação do seu estado na memória em disco.
É responsabilidade da Coleção Confiável persistir seu estado até esse ponto.
Depois que as Coleções Confiáveis concluírem seus pontos de verificação, o Gerenciador de Estado Confiável poderá truncar o log para liberar espaço em disco.
Dessa forma, quando a réplica precisar ser reiniciada, as Coleções Confiáveis irão recuperar seu estado com ponto de verificação, e o Gerenciador de Estado Confiável irá recuperar e reproduzir todas as alterações de estado que ocorreram desde o ponto de verificação.

> [!NOTE]
> A adição de outro valor de ponto de verificação aprimora o desempenho recuperação em casos comuns.
> Isso ocorre porque os pontos de verificação contêm somente as versões mais recentes.
> 
> 

## <a name="recommendations"></a>Recomendações
* Não modifique um objeto de tipo personalizado retornado por operações de leitura (por exemplo, `TryPeekAsync` ou `TryGetValueAsync`). As Coleções Confiáveis, como as Coleções Simultâneas, retornam uma referência aos objetos e não uma cópia.
* Faça uma cópia em profundidade do objeto de tipo personalizado retornado antes de modificá-lo. Como structs e tipos internos são pass-by-value, você não precisa fazer uma cópia em profundidade neles.
* Não use `TimeSpan.MaxValue` para tempos limites. Tempos limite devem ser usados para detectar deadlocks.
* Não use uma transação depois que ela tiver sido confirmada, anulada ou descartada.
* Não use uma enumeração fora do escopo da transação que em foi criado.
* Não crie uma transação dentro da instrução `using` de outra transação porque isso pode causar deadlocks.
* Certifique-se de que a implementação de `IComparable<TKey>` esteja correta. O sistema depende disso para mesclar os pontos de verificação.
* Use bloqueio de atualização durante a leitura de um item com uma intenção de atualizá-lo para impedir determinada classe de deadlocks.
* Considere o uso da funcionalidade de backup e restauração para ter uma recuperação de desastre.
* Evite a mistura de operações de entidade única e operações de várias entidades (por exemplo, de `GetCountAsync`, `CreateEnumerableAsync`) na mesma transação devido a níveis de isolamento diferentes.
* Trate InvalidOperationException. As transações do usuário podem ser anuladas pelo sistema por diversos motivos. Por exemplo, quando o Gerenciador de Estado Confiável muda sua função Primária ou quando uma transação de longa execução bloqueia o truncamento do log transacional. Nesses casos, o usuário pode receber InvalidOperationException, indicando que a transação já foi encerrada. Supondo que o encerramento da transação não foi solicitado pelo usuário, a melhor maneira de tratar essa exceção é descartar a transação, verificar se o token de cancelamento foi sinalizado (ou se a função da réplica foi alterada) e, caso não tenha, criar uma nova transação e tentar novamente.  

Eis aqui algumas coisas que se deve manter em mente:

* O tempo limite padrão é de 4 segundos para todas as APIs de Coleções Confiáveis. A maioria dos usuários não deve substituir isso.
* O token de cancelamento padrão é `CancellationToken.None` em todas as APIs de Coleções Confiáveis.
* O parâmetro de tipo de chave (*TKey*) para um Dicionário Confiável deve implementar corretamente `GetHashCode()` e `Equals()`. As chaves devem ser imutáveis.
* Para obter alta disponibilidade para as Coleções Confiáveis, cada serviço deve ter pelo menos um destino e uma réplica mínima com tamanho definido como 3.
* As operações de leitura no secundário podem ler versões que não são confirmadas por quorum.
  Isso significa que uma versão dos dados que é lida por meio de um único secundário pode ter um progresso falso.
  É evidente que as leituras do Primário são sempre estáveis: elas nunca podem ter um progresso falso.

## <a name="next-steps"></a>Próximas etapas
* [Início Rápido dos Serviços Confiáveis](service-fabric-reliable-services-quick-start.md)
* [Trabalhando com Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Notificações do Reliable Services](service-fabric-reliable-services-notifications.md)
* [Backup e restauração do Reliable Services (recuperação de desastre)](service-fabric-reliable-services-backup-restore.md)
* [Configuração do Gerenciador de Estado Confiável](service-fabric-reliable-services-configuration.md)
* [Introdução aos serviços de API da Web da Malha de Serviços](service-fabric-reliable-services-communication-webapi.md)
* [Uso avançado do modelo de programação de Serviços Confiáveis](service-fabric-reliable-services-advanced-usage.md)
* [Referência do desenvolvedor para Coleções Confiáveis](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)


