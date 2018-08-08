---
title: Vários mestres em escala global com o Azure Cosmos DB | Microsoft Docs
description: ''
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: rimman
ms.openlocfilehash: 4911a302bf9055948827a72f2e631663b8be741e
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2018
ms.locfileid: "39266571"
---
# <a name="multi-master-at-global-scale-with-azure-cosmos-db"></a>Vários mestres em escala global com o Azure Cosmos DB 
 
O desenvolvimento de aplicativos distribuídos mundialmente que respondam com latências locais, ao mesmo tempo em que se mantém exibições de dados consistentes em todo o mundo, é um problema desafiador. Os clientes usam bancos de dados distribuídos globalmente porque precisam melhorar a latência de acesso a dados, obter alta disponibilidade de dados, assegurar recuperação de desastre garantida e atender aos requisitos de negócios. Vários mestres no Azure Cosmos DB fornecem altos níveis de disponibilidade (99,999%), latência menor que dez milissegundos para gravar dados e a escalabilidade com suporte interno para resolução de conflitos flexível e abrangente. Esses recursos simplificam significativamente o desenvolvimento de aplicativos distribuídos globalmente. Para aplicativos distribuídos globalmente, o suporte para vários mestres é crucial. 

![Arquitetura de vários mestres](./media/multi-region-writers/multi-master-architecture.png)

Com o suporte para vários mestres do Azure Cosmos DB, você pode executar gravações em contêineres de dados (por exemplo, coleções, gráficos, tabelas) distribuídos em qualquer lugar no mundo. Você pode atualizar dados em qualquer região que esteja associada à sua conta de banco de dados. Essas atualizações de dados podem propagar-se assincronamente. Além de fornecer acesso rápido e latência de gravação aos seus dados, os vários mestres também fornecem uma solução prática para problemas de balanceamento de carga e failover. Em resumo, com o Azure Cosmos DB, você obtém uma latência de gravação de <10 ms no 99º percentil em qualquer lugar no mundo, 99,999% de disponibilidade de gravação e leitura em qualquer lugar no mundo e a capacidade de dimensionar a taxa de transferência de gravação e leitura em qualquer lugar do mundo.   

> [!IMPORTANT]
> O suporte a vários mestres está em versão prévia privada. Para usar a versão prévia, [inscreva-se](#sign-up-for-multi-master-support) agora.

## <a name="sign-up-for-multi-master-support"></a>Inscreva-se para o suporte a vários mestres

Caso você já tenha uma assinatura do Azure, inscreva-se no programa de versão prévia de vários mestres no Portal do Azure. Se estiver conhecendo o Azure agora, inscreva-se para uma [avaliação gratuita](https://azure.microsoft.com/free), em que você obtém 12 meses de acesso gratuito ao Azure Cosmos DB. Conclua as etapas a seguir para solicitar o acesso ao programa de versão prévia de vários mestres.

1. No [portal do Azure](https://portal.azure.com), clique em **Criar um recurso** > **Bancos de dados** > **Azure Cosmos DB**.  

2. Na página Nova Conta, forneça um nome para sua conta do Azure Cosmos DB, escolha a API, a Assinatura, o Grupo de Recursos e o Local.  

3. Em seguida, selecione **Inscreva-se para visualização hoje mesmo** sob o campo de visualização de mestre múltiplo.  

   ![Inscreva-se para a versão prévia de vários mestres](./media/multi-region-writers/sign-up-for-multi-master-preview.png)

4. No painel **Inscrever-se na versão prévia hoje**, clique em **OK**. Depois de enviar a solicitação, o status mudará para **Aprovação pendente** na folha de criação de conta.  

Depois de enviar a solicitação, você receberá uma notificação por email de que sua solicitação foi aprovada. Devido ao alto volume de solicitações, você deve receber a notificação dentro de uma semana. Você não precisa criar um tíquete de suporte para concluir a solicitação. Solicitações serão examinadas na ordem em que foram recebidas.

## <a name="a-simple-multi-master-example--content-publishing"></a>Um simples exemplo de vários mestres – publicação de conteúdo  

Vamos examinar um cenário do mundo real que descreve como usar o suporte para vários mestres com o Azure Cosmos DB. Considere uma plataforma de publicação de conteúdo criada no Azure Cosmos DB. Aqui estão alguns requisitos que essa plataforma deve atender para uma excelente experiência de usuário para editores e consumidores. 

* Os autores e os assinantes estão espalhados pelo mundo todo.  

* Os autores devem publicar artigos (gravação) em sua região local (mais próxima).  

* Os autores têm leitores/assinantes de seus artigos distribuídos pelo mundo.  

* Os assinantes devem receber uma notificação quando novos artigos são publicados.  

* Os assinantes devem ser capazes de ler artigos da sua região local. Também devem ser capazes de adicionar críticas a esses artigos.  

* Qualquer pessoa, incluindo o autor dos artigos, deve ser capaz de exibir todas as críticas anexados aos artigos de uma região local.  

Supondo que haja milhões de clientes e fornecedores com bilhões de artigos, assim temos de enfrentar os problemas de escala junto com a garantia de localidade de acesso. Esse caso de uso é um candidato perfeito para os vários mestres do Azure Cosmos DB. 

## <a name="benefits-of-having-multi-master-support"></a>Benefícios do suporte para vários mestres 

O suporte para vários mestres é essencial para aplicativos distribuídos globalmente. Os vários mestres são compostos de [várias regiões mestres](distribute-data-globally.md) que igualmente participam de um modelo de gravação em qualquer lugar (padrão ativo-ativo) e são usados para garantir que dados estejam disponíveis a qualquer momento em que você precisar. As atualizações feitas a uma região individual são propagadas assincronamente para todas as outras regiões (que por sua vez são regiões mestres em si). As regiões do Azure Cosmos DB operando como regiões mestres em uma configuração de vários mestres funcionam automaticamente para convergir os dados de todas as réplicas e garantir [a integridade dos dados e a consistência global](consistency-levels.md). A imagem a seguir mostra a replicação de leitura/gravação para um único mestre e vários mestres.

![Único mestre e vários mestres](./media/multi-region-writers/single-vs-multi-master.png)

A implementação de vários mestres por conta própria adiciona uma sobrecarga para os desenvolvedores. Clientes grandes que tentam implementar vários mestres por conta própria podem gastar centenas de horas configurando e testando uma configuração de vários mestres mundial e muitos têm um conjunto dedicado de engenheiros cujo único trabalho é a monitorar e manter a replicação de vários mestres. A criação e o gerenciamento da configuração de vários mestres por conta própria consome tempo, esgota recursos que poderiam ser aplicados na inovação do próprio aplicativo e resulta em custos muito maiores. O Azure Cosmos DB fornece suporte para vários mestres prontos para uso e remove essa sobrecarga dos desenvolvedores.  

Em resumo, os vários mestres fornecem os seguintes benefícios:

* **Melhor recuperação de desastre, disponibilidade de gravação e failover** - Os vários mestres podem ser usados para preservar ainda mais a alta disponibilidade de um banco de dados crítico. Por exemplo, um banco de dados de vários mestres pode replicar dados de uma região para uma região de failover quando a região principal fica indisponível devido a uma interrupção ou um desastre regional. Essa região de failover servirá como uma região mestre totalmente funcional para suportar o aplicativo. O mestre múltiplo oferece maior proteção à “capacidade de sobrevivência” em relação a desastres naturais, quedas de energia ou sabotagem, porque as regiões restantes podem utilizar multi-mestres geograficamente diferentes com disponibilidade de gravação garantida> 99,999%. 

* **Melhor latência de gravação para os usuários finais** - Quanto mais próximos seus dados (que você está fornecendo) estão dos usuários finais, melhor será a experiência. Por exemplo, se você tiver usuários na Europa, mas seu banco de dados fica dos Estados Unidos ou na Austrália, a latência será de aproximadamente 140 ms e 300 ms respectivamente. Atrasos são inaceitáveis para muitos jogos populares, requisitos de serviços bancários ou aplicativos interativos (web ou móvel). A latência desempenha uma grande parte na percepção do cliente de uma experiência de alta qualidade e provou-se que ela afeta o comportamento de usuários de certo modo. Com a melhora da tecnologia e especialmente com o surgimento de AR, VR e MR, exigindo experiências ainda mais envolventes e realistas, os desenvolvedores agora precisam produzir sistemas com requisitos rígidos de latência. Portanto, é mais importante ter aplicativos e dados disponíveis localmente (conteúdo para os aplicativos). Com vários mestres no Azure Cosmos DB, o desempenho é rápido como leituras e gravações locais comuns e é aprimorado globalmente pela distribuição geográfica.  

* **Melhor escalabilidade de gravação e taxa de transferência de gravação** - Os vários mestres oferecerão maior taxa de transferência e maior utilização ao mesmo tempo em que oferecem vários modelos de consistência com garantias de correção e backup por SLAs. 

  ![Dimensionamento da taxa de transferência de gravação com vários mestres](./media/multi-region-writers/scale-write-throughput.png)

* **Melhor suporte para ambientes desconectados (por exemplo, dispositivos de borda)** -Os vários mestres permitem que usuários repliquem todos ou um subconjunto de dados de um dispositivo de borda para uma região mais próxima em um ambiente desconectado. Esse cenário é típico de sistemas de automação de equipes de vendas, onde o laptop de um indivíduo (dispositivo desconectado) armazena um subconjunto de dados relacionados ao vendedor individual da equipe de vendas. As regiões mestres na nuvem que estão localizadas em qualquer lugar no mundo podem funcionar como destino da cópia dos dispositivos de borda remotos.  

* **Balanceamento de carga** - Com vários mestres, a carga pelo aplicativo pode ser balanceada movendo cargas de trabalho/usuários de uma região fortemente carregada para regiões em que a carga é distribuída uniformemente. A capacidade de gravação pode ser facilmente estendida adicionando uma nova região e, em seguida, alterando algumas gravações para a nova região. 

* **Melhor uso da capacidade provisionada** - Com vários mestres, para cargas de trabalho mistas e com muita gravação, você pode esgotar a capacidade provisionada por várias regiões.  Em alguns casos, você pode redistribuir leituras e gravações mais igualmente, isso requer menos taxa de transferência a ser provisionada e leva a mais economia de custo para os clientes.  

* **Arquiteturas de aplicativo mais simples e mais resilientes** - Aplicativos movendo para a configuração de vários mestres obtêm garantia de resiliência de dados.  Com o Azure Cosmos DB ocultando toda a complexidade, ele pode simplificar consideravelmente a arquitetura e o design do aplicativo. 

* **Teste de failover sem riscos** - O teste de failover não tem uma queda na taxa de transferência de gravação. Com vários mestres, todas as outras regiões são mestres totais, então o failover não terá muito impacto na taxa de transferência de gravação.  

* **Menor TCO (Custo Total de Propriedade) e DevOps** - Atender os objetivos referentes a escalabilidade, desempenho, distribuição global e tempo de recuperação são geralmente caros devido a complementos caros ou à manutenção de uma infraestrutura de back-up que está em repouso até o desastre atingi-la. Com os vários mestres do Azure Cosmos DB apoiados por SLAs líderes no setor, os desenvolvedores não mais exigem a criação e a manutenção da "lógica de cola de back-end" e obtém paz de espírito ao executar as cargas de trabalho críticas. 

## <a name="use-cases-where-multi-master-support-is-needed"></a>Casos de uso onde o suporte para vários mestres é necessário

Há numeroso casos de uso de vários mestres no Azure Cosmos DB: 

* **IoT** - Os vários mestres do Azure Cosmos DB permite a implementação distribuída simplificada de processamento de dados de IoT. Implantações de borda distribuídas geograficamente que usam tipos de dados sem conflito com CRDT geralmente precisam controlar dados de série temporal de vários locais. Cada dispositivo pode ser hospedado em uma das regiões mais próximas e um dispositivo pode viajar (por exemplo, um carro) e pode ser realocado dinamicamente para gravar em outra região.  

* **Comércio eletrônico** - A garantia de excelente experiência do usuário em cenários de comércio eletrônico precisa de alta disponibilidade e resiliência para cenários de falha. No caso de falha de uma região, as sessões de usuários, carrinhos de compra, lista de desejos ativa precisam ser perfeitamente selecionadas por outra região sem perda de estado. Enquanto isso, as atualizações feitas pelo usuário devem ser tratadas apropriadamente (por exemplo, adições e remoções do carrinho de compras devem ser transferidos). Com vários mestres, o Azure Cosmos DB pode lidar com esses cenários normalmente, com uma transição suave entre regiões ativas, mantendo uma exibição consistente do ponto de vista do usuário. 

* **Detecção de fraudes/anomalias** - Geralmente os aplicativos que monitoram a atividade de usuários ou atividade de contas são distribuídos globalmente e devem manter o controle de vários eventos ao mesmo tempo. Durante a criação e a manutenção de pontuações de um usuário, ações de regiões geográficas diferentes devem atualizas simultaneamente as pontuações para manter as métricas de risco alinhadas. O Azure Cosmos DB pode garantir que os desenvolvedores não precisem lidar com cenários de conflito no nível do aplicativo. 

* **Colaboração** - Para aplicativos de classificados com base na popularidade dos artigos, como mercadorias em venda ou mídia a ser consumido etc. O acompanhamento da popularidade em várias regiões geográficas pode tornar-se complicado, especialmente quando royalties precisam ser pagos ou decisões de publicidade em tempo real precisam ser tomadas. A classificação, a triagem e a emissão de relatórios em várias regiões em todo o mundo, em tempo real com o Azure Cosmos DB permitem aos desenvolvedores oferecer recursos com pouco esforço e sem comprometer as latências. 

* **Medição** - A contagem e a regulação do uso (como chamadas de API, transações por segundo, minutos usados) podem ser implementadas globalmente de forma simples usando os vários mestres do Microsoft Azure Cosmos DB. A resolução de conflitos interna garante tanto a precisão de contagens como a regulamentação em tempo real. 

* **Personalização** - Se você estiver mantendo contadores distribuídos geograficamente que disparam ações como premiação por pontos de fidelidade ou implementação de sessões personalizadas do usuário, a alta disponibilidade e a contagem distribuída geograficamente simplificada fornecidas pelo Azure Cosmos DB permitem que os aplicativos forneçam alto desempenho com simplicidade. 

## <a name="conflict-resolution-with-multi-master"></a>Resolução de conflitos com vários mestres 

Com vários mestres, o desafio geralmente é que duas (ou mais) réplicas do mesmo registro podem ser atualizadas simultaneamente pelos diferentes autores em duas ou mais regiões diferentes. Gravações simultâneas podem levar a duas versões diferentes do mesmo registro e sem resolução de conflitos interna, e o aplicativo em si deve executar a resolução de conflitos para resolver essa inconsistência.  

**Exemplo** - Vamos supor que você esteja usando Microsoft Azure Cosmos DB como repositório de persistência para um aplicativo de carrinho de compras e esse aplicativo é implantado em duas regiões: Leste dos EUA e Oeste dos EUA.  Se aproximadamente, ao mesmo tempo, um usuário em São Francisco adiciona um item ao carrinho de compras (por exemplo, um livro) enquanto um processo de gerenciamento de estoque no Leste dos EUA invalida um item de carrinho compras diferente (por exemplo, um novo telefone) para esse mesmo usuário em resposta a uma notificação do fornecedor de que a data de lançamento foi adiada. No horário T1, os registros de carrinho de compras em duas regiões são diferentes. O banco de dados usará sua replicação e o mecanismo de resolução de conflitos para resolver essa inconsistência e, eventualmente, uma das duas versões do carrinho de compras será selecionada. Usando a heurística de resolução de conflitos geralmente aplicada por bancos de dados de vários mestres (por exemplo, última gravação prevalece), é impossível para o usuário ou aplicativo prever qual versão será selecionada. Em ambos os casos, os dados são perdidos ou pode ocorrer um comportamento inesperado. Se a versão da região Leste for selecionada, a seleção do usuário de um novo item de compra (ou seja, um livro) será perdida e se a região Oeste for selecionada, o item escolhido anteriormente (ou seja, telefone) ainda estará no carrinho. De qualquer forma, informações serão perdidas. Finalmente, qualquer outro processo inspecionando o carrinho de compras entre horários T1 e T2 vai ver também o comportamento não determinístico. Por exemplo, um processo em segundo plano que seleciona o depósito de atendimento e atualiza os custos de envio do carrinho produziria resultados que estão em conflito com o conteúdo final do carrinho. Se o processo está em execução na região Oeste e 1 alternativo se torna a realidade, ele calcularia os custos de envio para dois itens, embora o carrinho possa logo ter apenas um item, o livro. 

O Azure Cosmos DB implementa a lógica para lidar com gravações conflitantes dentro do próprio mecanismo do banco de dados. O Azure Cosmos DB oferece **suporte de resolução de conflitos abrangente e flexível** oferecendo diversos modelos de resolução de conflitos, incluindo Automático (tipos de dados replicados livre de conflitos de CRDT), Última gravação prevalece (LWW) e Personalizado (procedimento armazenado) para resolução de conflitos automática. Os modelos de resolução de conflitos fornecem garantias de exatidão e a consistência e removem a carga dos desenvolvedores para parar para pensar sobre consistência, disponibilidade, desempenho, latência de replicação e combinações complexas de eventos em failovers geográficos e conflitos de gravação entre regiões.  

  ![Resolução de conflitos de vários mestres](./media/multi-region-writers/multi-master-conflict-resolution-blade.png)

Você terá de 3 tipos de modelos de resolução de conflitos oferecidoss pelo Azure Cosmos DB. A semântica dos modelos de resolução de conflitos é a seguinte: 

**Automático** - É a política de resolução de conflito padrão. Selecionar essa política faz com que o Azure Cosmos DB resolva automaticamente as atualizações em conflito no lado do servidor e forneça garantia de consistência forte/eventual. Internamente, o Azure Cosmos DB implementa a resolução de conflitos automática aproveitando os tipos de dados replicados livres de conflito (CRDTs) dentro do mecanismo do banco de dados.  

**Última gravação prevalece (LWW)** - A escolha desta política permitirá que você resolva conflitos com base em uma propriedade sincronizada de data/hora definida pelo sistema ou uma propriedade personalizada definida na versão conflitante dos registros. A resolução de conflitos ocorre no lado do servidor e a versão com a data/hora mais recente é selecionada como prevalecente.  

**Personalizado** - Você pode registrar uma lógica de resolução de conflitos definida por aplicativo registrando um procedimento armazenado. O procedimento armazenado será invocado após a detecção de conflitos de atualização sob a responsabilidade de uma transação de banco de dados, no lado do servidor. Se você selecionar a opção, mas não registrar um procedimento armazenado (ou se o procedimento armazenado gerar uma exceção em tempo de execução), você poderá acessar todas as versões conflitantes por meio do Feed de Conflitos e resolvê-las individualmente.  

## <a name="next-steps"></a>Próximas etapas  

Neste artigo, você aprendeu a usar vários mestres distribuídos globalmente com o Azure Cosmos DB. Em seguida, dê uma olhada nos seguintes recursos: 

* [Saiba sobre como o Azure Cosmos DB dá suporte à distribuição global](distribute-data-globally.md)  

* [Saiba mais sobre failovers automáticos no Azure Cosmos DB](regional-failover.md)  

* [Saiba mais sobre consistência global com o Azure Cosmos DB](consistency-levels.md)  

* Desenvolver com várias regiões usando o Azure Cosmos DB – [API do SQL](tutorial-global-distribution-sql-api.md), [API do MongoDB](tutorial-global-distribution-mongodb.md) ou [API de Tabela](tutorial-global-distribution-table.md)  
