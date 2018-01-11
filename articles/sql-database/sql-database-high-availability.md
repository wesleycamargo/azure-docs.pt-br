---
title: "Alta disponibilidade - Serviço do Banco de Dados SQL do Azure | Microsoft Docs"
description: "Saiba mais sobre os recursos e capacidades de alta disponibilidade do serviço de Banco de Dados SQL do Microsoft Azure"
keywords: 
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
ms.assetid: 
ms.service: sql-database
ms.custom: 
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 12/13/2017
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: c0a140c959f14c2e8ceaddad5d323f0900be5d2f
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2017
---
# <a name="high-availability-and-azure-sql-database"></a>Banco de dados SQL do Microsoft Azure e de alta disponibilidade
Desde o início da oferta do PaaS do Banco de Dados do SQL do Microsoft Azure, a Microsoft prometeu aos nossos clientes que a Alta Disponibilidade (HA) seria incorporada no serviço e os clientes não precisam operar, adicionar uma lógica especial para isso, ou tomar decisões para a Alta Disponibilidade. A Microsoft oferece aos clientes um SLA e mantém o controle total sobre a configuração do sistema de Alta Disponibilidade e operação. O SLA de Alta Disponibilidade se aplica a um Banco de Dados SQL em uma região e não fornece proteção em caso de uma falha de região total devido a fatores fora de nosso controle razoável (por exemplo, desastres naturais, guerra, atos de terrorismo, tumultos, ação governamental ou uma falha de rede ou dispositivo que seja externa aos nossos centros de dados, incluindo nas instalações do cliente ou entre as instalações do cliente e o nosso centro de dados).

Para simplificar o problema de espaço de alta disponibilidade, a Microsoft usa as seguintes suposições:
1.  Falhas de hardware e software são inevitáveis
2.  A equipe operacional comete erros que levam a falhas
3.  As operações de manutenção planejadas causam interrupções 

Embora esses eventos individuais sejam incomuns em escala de nuvem, eles ocorrem toda semana, se não todos os dias. 

## <a name="fault-tolerant-sql-databases"></a>Bancos de dados SQL tolerantes a falhas
Os clientes estão mais interessados na resiliência de seus próprios bancos de dados e menos interessados na resiliência do serviço de banco de dados SQL como um todo. 99,99% de tempo produtivo para um serviço é inútil se o "meu banco de dados" for parte do 0,01% dos bancos de dados que estão inativos. Cada banco de dados precisa ser tolerante a falhas, e a mitigação de falhas nunca deve resultar na perda de uma transação confirmada. 

Para dados, o Banco de Dados SQL usa armazenamento local (LS) com base em discos/VHDs anexados diretos e o armazenamento remoto (RS) com base em blobs de páginas de Armazenamento Premium do Azure. 
- O armazenamento local é usado em bancos de dados Premium e pools, que são projetados para aplicativos OLTP com altos requisitos de IOPS. 
- O armazenamento remoto é usado para camadas de serviço do plano básico e padrão, projetadas para bancos de dados pequenos, médios ou grandes que exigem capacidade de computação e armazenamento para dimensionar independentemente. Eles usam um único blob de páginas para arquivos de log e banco de dados, e mecanismos de replicação e failover de armazenamento interno.

Em ambos os casos, os mecanismos de replicação, detecção de falhas e failover do Banco de Dados SQL são totalmente automatizados e operam sem intervenção humana. Essa arquitetura foi projetada para garantir que os dados confirmados nunca serão perdidos e que a durabilidade dos dados tenha prioridade sobre tudo.

Principais benefícios:
- Os clientes recebem todos os benefícios dos bancos de dados replicados sem a necessidade de configurar ou fazer manutenção de hardware, software, sistemas operacionais ou ambientes de virtualização complicados.
- Propriedades ACID completas de bancos de dados relacionais são mantidas pelo sistema.
- Failovers são completamente automatizados sem perda dos dados confirmados.
- O roteamento de conexões para a réplica primária é gerenciado dinamicamente pelo serviço sem nenhuma lógica de aplicativo necessária.
- O alto nível de redundância automatizada é fornecido sem custo adicional.

> [!NOTE]
> A arquitetura de alta disponibilidade descrita está sujeita a alterações sem aviso prévio. 

## <a name="data-redundancy"></a>Redundância de dados

A solução de alta disponibilidade no Banco de Dados SQL é baseada na tecnologia [AlwaysON](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) do SQL Server e funciona com bancos de dados LS e RS com diferenças mínimas. Na configuração LS, o AlwaysON é usado para persistência, enquanto na configuração RS, ele é usado para disponibilidade (baixo RTO). 

## <a name="local-storage"></a>Armazenamento local

No caso de LS, cada banco de dados é colocado online pelo serviço de gerenciamento (MS) dentro do anel de controle. Uma réplica primária e pelo menos duas réplicas secundárias (conjunto de quorum) estão localizadas em um anel de locatário que abrange três subsistemas físicos independentes dentro do mesmo centro de dados. Todas as leituras e gravações são enviadas pelo gateway (GW) à réplica primária e as gravações são replicadas assincronamente para as réplicas secundárias. O Banco de Dados SQL usa um esquema de confirmação baseado em quorum em que os dados são gravados na réplica primária e em uma secundária antes de que a transação seja considerada confirmada.

O sistema de failover [Service Fabric](/azure/service-fabric/service-fabric-overview.md) recompila réplicas automaticamente como falhas de nós e mantém a associação do conjunto de quorum conforme os nós saem e se unem ao sistema. A manutenção planejada é cuidadosamente coordenada para evitar que o conjunto de quorum fique abaixo da contagem mínima de réplicas (normalmente é 2). Esse modelo funciona bem para bancos de dados Premium, mas exige redundância de componentes de computação e de armazenamento, e resulta em um custo mais alto.

## <a name="remote-storage"></a>Armazenamento remoto

Para configurações de armazenamento remoto (camadas do plano básico e padrão), exatamente uma cópia do banco de dados é mantida no armazenamento de blob remoto, alavancando os recursos dos sistemas de armazenamento para detecção de bits corrompidos, redundância e durabilidade. 

A arquitetura de alta disponibilidade é ilustrada pelo diagrama a seguir:
 
![Arquitetura da alta disponibilidade](./media/sql-database-high-availability/high-availability-architecture.png)

## <a name="failure-detection--recovery"></a>Detecção de falhas e recuperação 
Um sistema distribuído em larga escala precisa de um sistema de detecção de falhas altamente confiável, que possa detectar falhas de forma confiável, rápida e de forma mais próxima possível do cliente. Para o Banco de Dados SQL, esse sistema se baseia no Azure Service Fabric. 

Com a réplica primária, ela fica imediatamente evidente se e quando a réplica primária falhar e o trabalho não puder continuar porque todas as leituras e gravações ocorreram primeiro na réplica primária. Esse processo de promover uma réplica secundária para o status da primária tem o objetivo de tempo de recuperação (RTO)=30 segundos e o objetivo de ponto de recuperação (RPO)=0. Para atenuar o impacto do RTO de 30 segundos, a melhor prática é tentar se reconectar várias vezes com um menor tempo de espera para tentativas de falha de conexão.

Quando uma réplica secundária falha, o banco de dados é reduzido a um conjunto de quorum mínimo, sem nenhum extra. O Service Fabric inicia o processo de reconfiguração semelhante ao processo que segue a falha da réplica primária; portanto, após uma breve espera para determinar se a falha é permanente, outra réplica secundária é criada. Em casos de estado fora de serviço temporário, como uma falha do sistema operacional ou uma atualização, uma nova réplica não é criada imediatamente para permitir que o nó com falha seja reiniciado. 

Para as configurações de armazenamento remoto, o Banco de Dados SQL usa a funcionalidade AlwaysON para os bancos de dados de failover durante as atualizações. Para isso, uma nova instância do SQL é transferida com antecedência como parte do evento de atualização planejado, e anexa e recupera o arquivo do banco de dados do armazenamento remoto. No caso de falhas de processo ou outros eventos não planejados, o Windows Fabric gerencia a disponibilidade da instância e, como uma última etapa de recuperação, anexa o arquivo do banco de dados remoto.

## <a name="conclusion"></a>Conclusão
O DB do SQL do Azure está profundamente integrado com a plataforma do Azure e é altamente dependente do Service Fabric para recuperação e detecção de falhas e do Azure Storage Blobs para proteção de dados. Ao mesmo tempo, o Banco de Dados SQL do Microsoft Azure usa a tecnologia AlwaysOn do SQL Server para replicação e failover. A combinação dessas tecnologias permite que os aplicativos percebam em sua totalidade os benefícios de um modelo de armazenamento misto e deem suporte aos SLAs mais exigentes. 

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Service Fabric](/azure/service-fabric/service-fabric-overview.md)
- Saiba mais sobre o [Gerenciador de Tráfego do Microsoft Azure](/traffic-manager/traffic-manager-overview.md) 
