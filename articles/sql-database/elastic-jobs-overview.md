---
title: Trabalhos de Banco de Dados Elástico do Azure SQL | Microsoft Docs
description: Usar trabalhos de Banco de Dados Elástico para executar scripts T-SQL (Transact-SQL) em um conjunto de um ou mais bancos de dados SQL do Azure
services: sql-database
author: srinia
manager: craigg
ms.service: sql-database
ms.topic: overview
ms.date: 06/14/2018
ms.author: srinia
ms.openlocfilehash: a15a64c1ccf1e40179a46baf9815f669ac4adfde
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751121"
---
# <a name="manage-groups-of-databases-with-elastic-database-jobs"></a>Gerenciar grupos de bancos de dados com trabalhos de Banco de Dados Elástico

Os **Trabalhos de Banco de Dados Elástico** permitem executar um ou mais scripts T-SQL em paralelo, em um grande número de bancos de dados, seja com agendamento ou sob demanda.

**Executar trabalhos em qualquer combinação de bancos de dados**: um ou mais bancos de dados individuais, todos os bancos de dados em um servidor, todos os bancos de dados em um pool elástico ou mapa de fragmentos, com a flexibilidade adicional de poder incluir ou excluir qualquer banco de dados específico. **Os trabalhos podem ser executados em vários servidores, vários pools e pode até mesmo executar em bancos de dados em assinaturas diferentes.** Os servidores e pools são enumerados dinamicamente no tempo de execução e, portanto, os trabalhos são executados em todos os bancos de dados existentes no grupo de destino no momento da execução.

A imagem a seguir mostra um agente de trabalho executando trabalhos em diferentes tipos de grupos de destino:

![Modelo conceitual do agente de Trabalho Elástico](media/elastic-jobs-overview/conceptual-diagram.png)


## <a name="why-use-elastic-jobs"></a>Por que usar trabalhos elásticos?

### <a name="manage-many-databases"></a>Gerenciar vários bancos de dados

- Agende tarefas administrativas para serem executadas todos os dias da semana, após o horário comercial, etc.
- Implante alterações de esquema, gerenciamento de credenciais, coleta de dados de desempenho ou coleta de telemetria do locatário (cliente). Atualize os dados de referência (informações comuns a todos os bancos de dados).
- Recompilar índices para melhorar o desempenho da consulta. Configure trabalhos para serem executados em um conjunto de bancos de dados de modo recorrente, por exemplo, fora dos horários de pico.
- Coletar resultados de consulta de um conjunto de bancos de dados em uma tabela central em uma base contínua. Consultas de desempenho podem ser executadas continuamente e configuradas para disparar tarefas adicionais a serem executadas.

### <a name="collect-data-for-reporting"></a>Coletar dados para relatórios

- Agregue dados de uma coleção de bancos de dados SQL do Azure em uma tabela de destino único.
- Executar consultas de processamento de dados mais longas em um grande conjunto de bancos de dados, por exemplo, a coleta de telemetria do cliente. Resultados são coletados em uma única tabela de destino para análise posterior.

### <a name="reduce-overhead"></a>Reduzir sobrecarga

- Normalmente, você deve se conectar a cada banco de dados de forma independente a fim de executar instruções Transact-SQL ou realizar outras tarefas administrativas. Um trabalho lida com a tarefa de fazer logon em cada banco de dados no grupo de destino. Você também define, mantém e persiste scripts T-SQL que serão executados em um grupo de bancos de dados SQL do Azure.

### <a name="accounting"></a>Contabilidade

- Os trabalhos registram em log o status de execução de cada banco de dados. Você também tem a repetição automática quando ocorrem falhas.

### <a name="flexibility"></a>Flexibilidade

- Defina grupos personalizados de bancos de dados SQL do Azure e defina agendas para executar um trabalho.


## <a name="elastic-job-components"></a>Componentes do Trabalho Elástico

|Componente  | Descrição (mais detalhes estão abaixo da tabela) |
|---------|---------|
|[**Agente de Trabalho Elástico**](#elastic-job-agent) |  O recurso do Azure que você cria para executar e gerenciar trabalhos.   |
|[**Banco de dados de trabalhos**](#job-database)    |    Um banco de dados SQL do Azure que o agente de trabalho usa para armazenar dados relacionados ao trabalho, definições de trabalho, etc.      |
|[**Grupo de destino**](#target-group)      |  O conjunto de servidores, pools, bancos de dados e mapas de fragmentos nos quais o trabalho é executado.       |
|[**Trabalho**](#job)  |  Um trabalho é uma unidade de trabalho composta de uma ou mais [etapas de trabalho](#job-step). As etapas de trabalho especificam a execução do script T-SQL, bem como outros detalhes necessários para executar o script.  |


### <a name="elastic-job-agent"></a>Agente de trabalho elástico

Um agente de Trabalho Elástico é o recurso do Azure para criar, executar e gerenciar trabalhos. O agente de Trabalho Elástico é um recurso do Azure que você cria no portal (há suporte também para [PowerShell](elastic-jobs-powershell.md) e REST). 

A criação de um **agente de Trabalho Elástico** requer um banco de dados SQL já criado. O agente configura esse banco de dados existente como o [ *Banco de dados do trabalho*](#job-database).

O agente de Trabalho Elástico é gratuito. O banco de dados de trabalhos é cobrado pela mesma taxa de qualquer banco de dados SQL.

### <a name="job-database"></a>Banco de dados de trabalhos

O *banco de dados de trabalhos* é usado para definir os trabalhos e rastrear o status e o histórico das execuções do trabalho. O *banco de dados de trabalhos* também é usado para armazenar metadados do agente, logs, resultados, definições de trabalho e também contém muitos procedimentos armazenados úteis e outros objetos de banco de dados para criar, executar e gerenciar trabalhos usando o T-SQL.

Na versão prévia atual, um banco de dados existente do SQL Azure (S0 ou superior) é necessário para criar um agente de Trabalho Elástico.

O *Banco de dados de trabalhos* não precisa ser literalmente novo, mas deve ser uma camada limpa, vazia, S0 ou de serviço superior. A camada de serviço recomendada do *Banco de dados de trabalhos* é S1 ou superior, mas realmente depende das necessidades de desempenho de seus trabalhos: número de etapas de trabalho, quantas vezes e com que frequência os trabalhos são executados. Por exemplo, um banco de dados S0 pode ser suficiente para um agente de trabalho que executa alguns trabalhos por hora, mas a execução de um trabalho a cada minuto poderia não ter desempenho suficientemente alto e uma camada de serviço superior poderia ser melhor.


#### <a name="job-database-permissions"></a>Permissões de banco de dados de trabalhos

Durante a criação do agente de trabalho, um esquema, tabelas e uma função chamada *jobs_reader* são criados no *Banco de dados de trabalhos*. A função é criada com a seguinte permissão e foi projetada para oferecer aos administradores um melhor controle de acesso para monitoramento do trabalho:


|Nome da função  |permissões de esquema 'jobs'  |permissões de esquema 'jobs_internal'  |
|---------|---------|---------|
|**jobs_reader**     |    SELECIONAR     |    Nenhum     |

> [!IMPORTANT]
> Considere as implicações de segurança antes de conceder acesso ao *banco de dados de trabalhos* como um administrador de banco de dados. Um usuário mal-intencionado com permissões para criar ou editar tarefas pode criar ou editar um trabalho que usa uma credencial armazenada para se conectar a um banco de dados sob controle do usuário mal-intencionado, o que permitiria que o usuário mal-intencionado determinasse a senha da credencial.



### <a name="target-group"></a>Grupo de destino

Um *grupo de destino* define o conjunto de bancos de dados em que uma etapa de trabalho será executada. Um grupo de destino pode conter qualquer número e combinação do seguinte:

- **Servidor SQL do Azure**: se um servidor for especificado, todos os bancos de dados existentes no servidor no momento da execução do trabalho farão parte do grupo. A credencial de banco de dados mestre deve ser fornecida para que o grupo possa ser enumerado e atualizado antes da execução do trabalho.
- **Pool elástico**: se um pool elástico for especificado, todos os bancos de dados que estão no pool elástico no momento da execução do trabalho farão parte do grupo. Assim como com o servidor, a credencial de banco de dados mestre deve ser fornecida para que o grupo possa ser atualizado antes da execução do trabalho.
- **Banco de dados único**: especifica um ou mais bancos de dados individuais como parte do grupo.
- **Mapa de fragmentos**: bancos de dados de um mapa de fragmentos.

> [!TIP]
> No momento da execução do trabalho, a *enumeração dinâmica* reavalia o conjunto de bancos de dados em grupos de destino que incluam servidores ou grupos. A enumeração dinâmica faz com que os **trabalhos sejam executados em todos os bancos de dados existentes no servidor ou pool no momento da execução do trabalho**. A reavaliação da lista de bancos de dados no tempo de execução é especialmente útil para cenários em que a associação de pools ou servidores é alterada com frequência.


Pools e bancos de dados únicos podem ser especificados como incluídos ou excluídos do grupo. Isso permite a criação de um grupo de destino com qualquer combinação de bancos de dados. Por exemplo, você pode adicionar um servidor a um grupo de destino, mas excluir bancos de dados específicos em um pool elástico (ou excluir um pool inteiro).

Um grupo de destino pode incluir bancos de dados em várias assinaturas e em várias regiões. Observe que as execuções entre regiões têm maior latência do que as execuções dentro da mesma região.


### <a name="job"></a>Trabalho

Um *trabalho* é uma unidade de trabalho executada com agendamento ou como um único trabalho. Um trabalho consiste em uma ou mais *etapas de trabalho*.

#### <a name="job-step"></a>Etapa de trabalho

Cada etapa de trabalho especifica um script T-SQL a ser executado, um ou mais grupos de destino no qual executar o script T-SQL e as credenciais de que o agente de trabalho precisa para se conectar ao banco de dados de destino. Cada etapa de trabalho tem tempo limite e políticas de repetição personalizáveis e pode, opcionalmente, especificar parâmetros de saída.

#### <a name="job-output"></a>Saída do trabalho

O resultado das etapas de um trabalho em cada banco de dados de destino é registrado em detalhes, e a saída do script pode ser capturada em uma tabela específica. Você pode especificar um banco de dados para salvar os dados retornados de um trabalho.

#### <a name="job-history"></a>Histórico de trabalho

O histórico de execução do trabalho é armazenado no *Banco de dados de trabalhos*. Um trabalho de limpeza do sistema limpa o histórico de execuções com mais de 45 dias. Para remover o histórico de menos de 45 dias, chame o procedimento armazenado **sp_purge_history** no *Banco de dados de trabalhos*.

## <a name="workflow-to-create-configure-and-manage-jobs"></a>Fluxo de trabalho para criar, configurar e gerenciar trabalhos

### <a name="create-and-configure-the-agent"></a>Criar e configurar o agente

1. Crie ou identifique um S0 vazio ou um banco de dados SQL superior. Ele será usado como o *Banco de dados de trabalhos* durante a criação do agente de Trabalho Elástico.
2. Crie um agente de Trabalho Elástico no [portal](https://portal.azure.com/#create/Microsoft.SQLElasticJobAgent) ou com o [PowerShell](elastic-jobs-powershell.md#create-the-elastic-job-agent).

   ![Criação de agente de Trabalho Elástico](media/elastic-jobs-overview/create-elastic-job-agent.png)

### <a name="create-run-and-manage-jobs"></a>Criar, executar e gerenciar trabalhos

1. Crie uma credencial para a execução de trabalhos no *Banco de dados de trabalhos* usando o [PowerShell](elastic-jobs-powershell.md#create-job-credentials-so-that-jobs-can-execute-scripts-on-its-targets) ou o [T-SQL](elastic-jobs-tsql.md#create-a-credential-for-job-execution).
2. Defina o grupo de destino (os bancos de dados em que você deseja executar o trabalho) usando o [PowerShell](elastic-jobs-powershell.md#define-the-target-databases-you-want-to-run-the-job-against) ou o [T-SQL](elastic-jobs-tsql.md#create-a-target-group-servers).
3. Crie uma credencial de agente de trabalho em cada banco de dados em que o trabalho será executado [(adicione o usuário (ou função) para cada banco de dados no grupo)](https://docs.microsoft.com/azure/sql-database/sql-database-control-access). Para obter um exemplo, confira o [tutorial do PowerShell](elastic-jobs-powershell.md#create-job-credentials-so-that-jobs-can-execute-scripts-on-its-targets).
4. Crie um trabalho usando o [PowerShell](elastic-jobs-powershell.md#create-a-job) ou o [T-SQL](elastic-jobs-tsql.md#deploy-new-schema-to-many-databases).
5. Adicione etapas de trabalho usando o [PowerShell](elastic-jobs-powershell.md#create-a-job-step) ou o [T-SQL](elastic-jobs-tsql.md#deploy-new-schema-to-many-databases).
6. Execute um trabalho usando o [PowerShell](elastic-jobs-powershell.md#run-the-job) ou o [T-SQL](elastic-jobs-tsql.md#begin-ad-hoc-execution-of-a-job).
7. Monitore o status de execução do trabalho usando o portal, o [PowerShell](elastic-jobs-powershell.md#monitor-status-of-job-executions) ou o [T-SQL](elastic-jobs-tsql.md#monitor-job-execution-status).

   ![Portal](media/elastic-jobs-overview/elastic-job-executions-overview.png)

## <a name="credentials-for-running-jobs"></a>Credenciais para executar trabalhos

Os trabalhos usam [credenciais no escopo do banco de dados](/sql/t-sql/statements/create-database-scoped-credential-transact-sql) para se conectar aos bancos de dados especificados pelo grupo de destino após a execução. Se um grupo de destino contém servidores ou pools, essas credenciais no escopo do banco de dados são usadas para conexão com o banco de dados mestre a fim de enumerar os bancos de dados disponíveis.

A configuração das devidas credenciais para executar um trabalho pode ser um pouco confusa e, portanto, tenha os seguintes pontos em mente:

- As credenciais no escopo do banco de dados devem ser criadas no *banco de dados de trabalhos*.
- **Todos os bancos de dados de destino devem ter um logon com [permissões suficientes](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) para que o trabalho seja concluído com êxito** (jobuser no diagrama abaixo).
- As credenciais devem ser reutilizadas entre os trabalhos e as senhas de credencial são criptografadas e protegidas contra usuários que possuem acesso somente leitura a objetos de trabalho.

A imagem a seguir foi criada para ajudar na compreensão e na configuração das credenciais de trabalho adequadas. **Lembre-se de criar o usuário em cada banco de dados (todos os *bancos de dados de usuário de destino*) em que o trabalho será executado**.

![Credenciais de trabalhos elásticos](media/elastic-jobs-overview/job-credentials.png)

## <a name="security-best-practices"></a>Práticas recomendadas de segurança

Algumas considerações sobre melhores práticas para usar trabalhos elásticos:

- Limite o uso das APIs somente a pessoas confiáveis.
- As credenciais devem ter os privilégios mínimos necessários para executar a etapa do trabalho. Para obter mais informações, confira [Autorizações e permissões do SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server).
- Ao usar um servidor e/ou um membro do grupo de destino de pool, é altamente recomendável criar uma credencial separada com direitos sobre o banco de dados mestre para exibir/listar bancos de dados, que será usada para expandir as listas de banco de dados dos servidores e/ou pools antes da execução do trabalho.



## <a name="agent-performance-capacity-and-limitations"></a>Desempenho, capacidade e limitações do agente

Os Trabalhos Elásticos usam o mínimo de recursos de computação enquanto aguardam a conclusão dos trabalhos de longa execução.

Dependendo do tamanho do grupo de destino de bancos de dados e do tempo de execução desejado para um trabalho (número de trabalhos simultâneos), o agente requer quantidades diferentes de computação e desempenho do *banco de dados de trabalhos* (quanto mais destinos e trabalhos, maior será a quantidade de computação necessária).

Atualmente, a versão prévia está limitada a 100 trabalhos simultâneos.

### <a name="prevent-jobs-from-reducing-target-database-performance"></a>Impedir que os trabalhos reduzam o desempenho do banco de dados de destino

Para que os recursos não fiquem sobrecarregados ao executar trabalhos em bancos de dados em um pool elástico do SQL, os trabalhos podem ser configurados para limitar o número de bancos de dados em que o trabalho pode ser executado simultaneamente.

##  <a name="differences-between-elastic-jobs-and-sql-server-agent"></a>Diferenças entre Trabalhos Elásticos e SQL Server Agent

Vale a pena observar algumas diferenças entre o SQL Server Agent (disponível no local e como parte da Instância Gerenciada do Banco de Dados SQL) e o agente Trabalho Elástico do Banco de Dados SQL do Azure (agora disponível para o Banco de Dados SQL e para o SQL Data Warehouse).


|  |Trabalhos elásticos  |SQL Server Agent |
|---------|---------|---------|
|Escopo     |  Qualquer número de bancos de dados SQL do Azure e/ou data warehouses na mesma nuvem do Azure que a do agente de trabalho. Os destinos podem estar em diferentes servidores lógicos, assinaturas e/ou regiões. <br><br>Os grupos de destino podem ser compostos de bancos de dados ou data warehouses individuais ou de todos os bancos de dados em um servidor, pool ou mapa de fragmentos (enumerados dinamicamente no tempo de execução do trabalho). | Qualquer banco de dados único na mesma instância do SQL Server que o SQL Agent. |
|Ferramentas e APIs com suporte     |  Portal, PowerShell, T-SQL, Azure Resource Manager      |   T-SQL, SSMS (SQL Server Management Studio)     |





## <a name="best-practices-for-creating-jobs"></a>Melhores práticas para a criação de trabalhos

### <a name="idempotent-scripts"></a>Scripts idempotentes
Os scripts T-SQL de um trabalho devem ser [idempotentes](https://en.wikipedia.org/wiki/Idempotence). **Idempotente** significa que, se o script tiver êxito e for executado novamente, o mesmo resultado ocorrerá. Um script pode falhar devido a problemas de rede transitórios. Nesse caso, o trabalho repetirá automaticamente a execução do script por um número predefinido de vezes antes de desistir. Um script idempotente tem o mesmo resultado, mesmo que tenha sido executado com êxito duas vezes (ou mais).

É uma tática simples para testar a existência de um objeto antes de criá-lo.


```sql
IF NOT EXIST (some_object)
    -- Create the object
    -- If it exists, drop the object before recreating it.
```

Da mesma forma, um script deve ser capaz de realizar a execução com êxito testando e combatendo logicamente quaisquer condições que encontrar.



## <a name="next-steps"></a>Próximas etapas

- [Criar e gerenciar trabalhos elásticos usando o PowerShell](elastic-jobs-powershell.md)
- [Criar e gerenciar trabalhos elásticos usando T-SQL (Transact-SQL)](elastic-jobs-tsql.md)