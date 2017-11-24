---
title: "Práticas Recomendadas para a Sincronização de Dados SQL do Azure | Microsoft Docs"
description: "Conheça as práticas recomendadas para configurar e executar a Sincronização de Dados SQL do Azure"
services: sql-database
ms.date: 11/13/2017
ms.topic: article
ms.service: sql-database
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: 7d9529fc8acd9347b0505b1c578febc1c2219b37
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2017
---
# <a name="best-practices-for-sql-data-sync-preview"></a>Práticas recomendadas para a Sincronização de Dados SQL (Versão prévia) 

Este artigo descreve as práticas recomendadas para a Sincronização de Dados SQL (versão prévia).

Para obter uma visão geral da Sincronização de Dados SQL, consulte [Sincronizar dados entre vários bancos de dados locais e de nuvem com a Sincronização de Dados SQL do Azure (versão prévia)](sql-database-sync-data.md).

## <a name="security-and-reliability"></a> Segurança e confiabilidade

### <a name="client-agent"></a>Agente cliente

-   Instale o agente cliente usando a conta o mínimo de privilégios e que tem acesso ao serviço de rede.

-   É melhor que o agente cliente seja instalado em um computador diferente do computador do SQL Server local.

-   Não registre um banco de dados local com mais de um agente.

-   Essa orientação se mantém mesmo ao sincronizar diferentes tabelas para diferentes grupos de sincronização.

-   Registrar um banco de dados local com vários agentes cliente apresenta desafios ao se excluir um dos grupos de sincronização.

### <a name="database-accounts-with-least-required-privilege"></a>Contas de banco de dados com o mínimo de privilégios necessários

-   **Para configuração da sincronização**. Criar/alterar tabela, alterar banco de dados, criar procedimento, selecionar/alterar esquema, criar tipo definido pelo usuário.

-   **Para a sincronização em andamento**. Selecionar/Inserir/Atualizar/Excluir em tabelas selecionadas para sincronização e em metadados de sincronização e tabelas de acompanhamento, permissão para Executar em procedimentos armazenados criados pelo serviço, permissão para Executar em tipos de tabela definidos pelo usuário.

-   **Para desprovisionamento**. Alterar em tabelas que fazem parte da sincronização, Selecionar/Excluir em tabelas de metadados de sincronização, Controlar em tabelas de acompanhamento de sincronização, procedimentos armazenados e tipos definidos pelo usuário.

**Como você pode usar essas informações quando há apenas uma única credencial para um banco de dados no grupo de sincronização?**

-   Altere as credenciais para diferentes fases (por exemplo, *credencial1* para instalação e *credencial2* para em andamento).

-   Altere a permissão das credenciais (ou seja, altere a permissão após a sincronização estar configurada).

## <a name="setup"></a>Configuração

### <a name="database-considerations-and-constraints"></a> Restrições e considerações de banco de dados

#### <a name="sql-database-instance-size"></a>Tamanho da instância do Banco de Dados SQL

Quando você criar uma nova instância do Banco de Dados SQL, defina o tamanho máximo para que ele sempre seja maior que o banco de dados que você implanta. Se você não definir o tamanho máximo maior do que o banco de dados implantado, a sincronização falhará. Embora não haja nenhum aumento automático, você pode fazer um ALTER DATABASE para aumentar o tamanho do banco de dados após ele ter sido criado. Não deixe de permanecer dentro dos limites de tamanho da instância do Banco de Dados SQL.

> [!IMPORTANT]
> A Sincronização de Dados SQL armazena metadados adicionais com cada banco de dados. Não deixe de considerar esses metadados ao calcular o espaço necessário. A quantidade de sobrecarga adicionada é governada pela largura das tabelas (por exemplo, tabelas estreitas exigem mais sobrecarga) e a quantidade de tráfego.

### <a name="table-considerations-and-constraints"></a> Restrições e considerações de tabela

#### <a name="selecting-tables"></a>Selecionando tabelas

Nem todas as tabelas em um banco de dados precisam estar em um grupo de sincronização. A seleção de quais tabelas incluir em um grupo de sincronização e quais excluir (ou incluir em um grupo de sincronização diferente) pode afetar a eficiência e os custos. Inclua em um grupo de sincronização somente as tabelas que as necessidades comerciais demandam e as tabelas das quais elas são dependentes.

#### <a name="primary-keys"></a>Chaves primárias

Cada tabela em um grupo de sincronização deve ter uma chave primária. O serviço de Sincronização de Dados SQL (versão prévia) não é capaz de sincronizar nenhuma tabela que não tem uma chave primária.

Antes de implantar em produção, teste o desempenho de sincronização inicial e em andamento.

### <a name="provisioning-destination-databases"></a> Provisionamento de bancos de dados de destino

A Sincronização de Dados SQL (versão prévia) fornece provisionamento automático de banco de dados básico.

Esta seção discute as limitações do provisionamento da Sincronização de Dados SQL (versão prévia).

#### <a name="auto-provisioning-limitations"></a>Limitações do provisionamento automático

A seguir estão limitações do provisionamento automático da Sincronização de Dados SQL (versão prévia).

-   Somente as colunas selecionadas são criadas na tabela de destino.
Portanto, se algumas colunas não fazem parte do grupo de sincronização, essas colunas não são provisionadas nas tabelas de destino.

-   Índices são criados somente para as colunas selecionadas.
Se o índice da tabela de origem tem colunas que não fazem parte do grupo de sincronização, esses índices não são provisionados nas tabelas de destino.

-   Índices em colunas de tipo XML não são provisionados.

-   Restrições CHECK não são provisionadas.

-   Os gatilhos existentes nas tabelas de origem não são provisionados.

-   Exibições e procedimentos armazenados não são criados no banco de dados de destino.

#### <a name="recommendations"></a>Recomendações

-   Use a funcionalidade de provisionamento automático somente para experimentar o serviço.

-   Para a produção, você deve provisionar o esquema de banco de dados.

### <a name="locate-hub"></a> Onde localizar o banco de dados hub

#### <a name="enterprise-to-cloud-scenario"></a>Cenário de empresa para nuvem

Para minimizar a latência, mantenha o banco de dados hub próximo da maior concentração de tráfego de banco de dados do grupo de sincronização.

#### <a name="cloud-to-cloud-scenario"></a>Cenário de nuvem para nuvem

-   Quando todos os bancos de dados em um grupo de sincronização estiverem em um data center, o hub deverá estar localizado no mesmo data center. Essa configuração reduz a latência e o custo da transferência de dados entre data centers.

-   Quando os bancos de dados em um grupo de sincronização estiverem em vários data centers, o hub deverá estar localizado no mesmo data center que a maioria dos bancos de dados e do tráfego de banco de dados.

#### <a name="mixed-scenarios"></a>Cenários mistos

Aplique as diretrizes anteriores para configurações de grupo de sincronização mais complexas.

## <a name="sync"></a>Sincronizar

### <a name="avoid-a-slow-and-costly-initial-synchronization"></a> Evitar uma sincronização inicial lenta e dispendiosa

Esta seção discute a sincronização inicial de um grupo de sincronização e o que você pode fazer para evitar uma sincronização inicial demorando mais do que o necessário e custando mais do que deveria.

#### <a name="how-initial-synchronization-works"></a>Como a sincronização inicial funciona

Quando você criar um grupo de sincronização, comece com os dados em apenas um banco de dados. Se você tiver dados em vários bancos de dados, a Sincronização de Dados SQL (versão prévia) tratará cada linha como um conflito que precisa de resolução. Essa resolução de conflitos faz com que a sincronização inicial seja lenta, levando de vários dias a vários meses, dependendo do tamanho do banco de dados.

Além disso, se os bancos de dados estiverem em data centers diferentes, os custos de sincronização inicial serão mais altos do que o necessário, pois cada linha deverá fazer o percurso entre os diferentes data centers.

#### <a name="recommendation"></a>Recomendações

Sempre que possível, comece com os dados em apenas um dos bancos de dados do grupo de sincronização.

### <a name="design-to-avoid-synchronization-loops"></a> Design para evitar loops de sincronização

Um loop de sincronização ocorre quando há referências circulares dentro de um grupo de sincronização, de modo que cada alteração em um banco de dados é replicada pelos bancos de dados no grupo de sincronização, circularmente e indefinidamente. É desejável evitar loops de sincronização, pois eles degradam o desempenho e podem aumentar significativamente os custos.

### <a name="handling-changes-that-fail-to-propagate"></a> Lidando com alterações com falha de propagação

#### <a name="reasons-that-changes-fail-to-propagate"></a>Razões pelas quais as alterações falham ao se propagar

Alterações podem falhar ao se propagar por muitas razões. Algumas causas seriam:

-   Incompatibilidade de esquema e tipos de dados.

-   Tentativa de inserir nulo em colunas não anuláveis.

-   Violação de restrições de chave estrangeira.

#### <a name="what-happens-when-changes-fail-to-propagate"></a>O que acontece quando as alterações falham ao ser propagadas?

-   O grupo de sincronização mostra que ele está em um estado de aviso.

-   Os detalhes estão no Visualizador de Log de interface do usuário do Portal.

-   Se o problema não for resolvido por 45 dias, o banco de dados se tornará desatualizado.

> [!NOTE]
> Essas alterações nunca se propagarão. A única maneira de recuperar-se é recriar o grupo de sincronização.

#### <a name="recommendation"></a>Recomendações

Monitore a integridade do banco de dados e do grupo de sincronização regularmente através da interface de log e do Portal.


## <a name="maintenance"></a>Manutenção 

### <a name="avoid-out-of-date-databases-and-sync-groups"></a> Evitar bancos de dados e grupos de sincronização desatualizados

Um grupo de sincronização ou um banco de dados dentro de um grupo de sincronização pode ficar desatualizado. Quando o status de um grupo de sincronização for "desatualizado", ele deixará de funcionar. Quando o status de um banco de dados é "desatualizado", os dados podem ser perdidos. É melhor evitar essas situações em vez de precisar recuperar-se delas.

#### <a name="avoid-out-of-date-databases"></a>Evitar bancos de dados desatualizados

O status de um banco de dados é definido como desatualizado quando ele ficou offline por 45 dias ou mais. Evite o status desatualizado em um banco de dados, assegurando que nenhum dos bancos de dados fique offline por 45 dias ou mais.

#### <a name="avoid-out-of-date-sync-groups"></a>Evitar grupos de sincronização desatualizados

O status de um grupo de sincronização é definido como desatualizado quando alguma alteração no grupo de sincronização não consegue se propagar para o restante do grupo de sincronização por 45 dias ou mais. Evite o status desatualizado em um grupo de sincronização verificando regularmente o log do histórico do grupo de sincronização. Verifique se todos os conflitos são resolvidos e as alterações propagadas com êxito pelos bancos de dados do grupo de sincronização.

Os motivos pelos quais um grupo de sincronização pode falhar ao aplicar uma alteração incluem:

-   Incompatibilidade de esquema entre tabelas.

-   Incompatibilidade de dados entre tabelas.

-   Inserir uma linha com um valor nulo em uma coluna que não permite valores nulos.

-   Atualizar uma linha com um valor que viola uma restrição de chave estrangeira.

Você pode impedir que grupos de sincronização fiquem desatualizados:

-   Atualizando o esquema para permitir os valores contidos nas linhas com falha.

-   Atualizando os valores das chaves estrangeiras para incluir os valores contidos nas linhas com falha.

-   Atualizando os valores de dados na linha com falha para serem compatíveis com o esquema ou chaves estrangeiras no banco de dados de destino.

### <a name="avoid-deprovisioning-issues"></a> Evitar problemas de desprovisionamento

Em determinadas circunstâncias, cancelar o registro de um banco de dados com um agente cliente pode causar falhas de sincronização.

#### <a name="scenario"></a>Cenário

1. O grupo de sincronização foi criado com uma instância do Banco de Dados SQL e um Banco de Dados do SQL Server local, que está associado ao agente local 1.

2. O mesmo banco de dados local está registrado com o agente local 2 (esse agente não está associado a nenhum grupo de sincronização).

3. Cancelar o registro do banco de dados local do agente local 2 remove as tabelas meta/de acompanhamento referentes ao grupo de sincronização A do banco de dados local.

4. Agora, as operações do grupo de sincronização A falham com o seguinte erro: "A operação atual não pôde ser concluída porque o banco de dados não está provisionado para sincronização ou você não tem permissões para as tabelas de configuração de sincronização."

#### <a name="solution"></a>Solução

Evite a situação completamente nunca registrando um banco de dados com mais de um agente.

Para se recuperar dessa situação:

1. Remova o banco de dados de cada grupo de sincronização a que ele pertence.

2. Adicione o banco de dados de volta a cada grupo de sincronização de que você acabou de removê-lo.

3. Implante cada grupo de sincronização afetado (o que provisiona o banco de dados).

### <a name="modifying-your-sync-group"></a> Modificando um grupo de sincronização

Não tente remover um banco de dados de um grupo de sincronização e, em seguida, editar o grupo de sincronização sem primeiro implantar uma das alterações.

Primeiro, remova um banco de dados de um grupo de sincronização. Em seguida, implante a alteração e aguarde a conclusão do desprovisionamento. Quando essa operação for concluída, você poderá editar o grupo de sincronização e implantar as alterações.

Se você tentar remover um banco de dados e, em seguida, editar um grupo de sincronização sem primeiro implantar as alterações, uma ou outra operação falhará e a interface do portal poderá entrar em um estado inconsistente. Nesse caso, você pode atualizar a página para restaurar o estado correto.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre a Sincronização de Dados SQL, veja:

-   [Sincronizar dados entre vários bancos de dados locais e de nuvem com a Sincronização de Dados SQL do Azure](sql-database-sync-data.md)
-   [Configurar a Sincronização de Dados SQL Azure](sql-database-get-started-sql-data-sync.md)
-   [Monitorar Sincronização de Dados SQL Azure com o Log Analytics do OMS](sql-database-sync-monitor-oms.md)
-   [Solucionar problemas com a Sincronização de Dados SQL do Azure](sql-database-troubleshoot-data-sync.md)

-   Conclua os exemplos do PowerShell que mostram como configurar a Sincronização de Dados SQL:
    -   [Usar o PowerShell para sincronização entre vários banco de dados SQL do Azure](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [Usar o PowerShell para sincronizar entre um Banco de Dados SQL do Azure e um banco de dados local do SQL Server](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [Baixe a documentação da API REST de Sincronização de Dados SQL](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Para saber mais sobre o Banco de Dados SQL, veja:

-   [Visão geral do Banco de Dados SQL](sql-database-technical-overview.md)
-   [Gerenciamento de ciclo de vida do banco de dados](https://msdn.microsoft.com/library/jj907294.aspx)
