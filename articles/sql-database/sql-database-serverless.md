---
title: Banco de dados SQL sem servidor (versão prévia do Azure) | Microsoft Docs
description: Este artigo descreve a nova camada de computação sem servidor e o compara com a camada de computação provisionada existente
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 05/07/2019
ms.openlocfilehash: 2ab8f272fc264f153144803be772d381c1780512
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65143263"
---
# <a name="sql-database-serverless-preview"></a>Banco de dados SQL sem servidor (visualização)

## <a name="what-is-the-serverless-compute-tier"></a>O que é a camada de computação sem servidor

Banco de dados SQL sem servidor (versão prévia) é uma camada de computação cobra do computação usada por um único banco de dados por segundo. Sem servidor é o preço-desempenho otimizado para bancos de dados individuais com padrões de uso oscilantes que podem arcar com algum atraso no aquecimento de computação após períodos de uso ocioso.
Em contraste, as ofertas disponíveis publicamente na fatura atual do banco de dados SQL do computação provisionada por hora. Essa camada de computação provisionada é o preço-desempenho otimizado para bancos de dados individuais ou Elásticos pools com uso médio de mais alto que não podem manter qualquer atraso na computação aquecimento.

Um banco de dados na camada do computador sem servidor é parametrizado por ele pode usar o intervalo de computação e um atraso de autopause.

![cobrança sem servidor](./media/sql-database-serverless/serverless-billing.png)

### <a name="performance"></a>Desempenho

- `MinVcore` e `MaxVcore` são parâmetros configuráveis que definem o intervalo de capacidade de computação disponível para o banco de dados. Limites de memória e e/s são proporcionais ao intervalo vCore especificado.  
- O atraso de autopause é um parâmetro configurável que define o período de tempo que o banco de dados deve ficar inativo antes que ele está em pausa automaticamente. O banco de dados é retomado automaticamente quando ocorre o próximo logon.

### <a name="pricing"></a>Preços

- A cobrança total para um banco de dados sem servidor é o resumo da fatura de computação e cobrança do armazenamento.
Cobrança para a computação é baseada na quantidade de memória usada por segundo e vCores usados.
- A computação mínima cobrada baseia-se no mínimo de vCores e memória mínima.
- Enquanto o banco de dados está em pausa, apenas o armazenamento é cobrado.

## <a name="scenarios"></a>Cenários

Sem servidor é o preço-desempenho otimizado para bancos de dados individuais com padrões de uso oscilantes que podem arcar com algum atraso no aquecimento de computação após períodos de uso ocioso. A camada de computação provisionada é otimizado para bancos de dados individual ou em pool com maior uso médio que não podem manter qualquer atraso na computação aquecimento preço-desempenho.

A tabela a seguir compara a camada de computação sem servidor com a camada de computação provisionada:

||Computação sem servidor|Computação provisionada|
|---|---|---|
|**Cenário de uso típico**|Bancos de dados com o uso de intermitente, imprevisível intercalados com períodos de inatividade|Bancos de dados ou pools Elásticos com o uso mais regular|
|**Esforço de gerenciamento de desempenho**|inferior|Maior|
|**Dimensionamento de computação**|Automático|Manual|
|**Capacidade de resposta de computação**|Inferior após períodos de inatividade|Imediata|
|**Granularidade de cobrança**|por segundo|Por hora|
|

### <a name="scenarios-well-suited-for-serverless-compute"></a>Cenários adequados para a computação sem servidor

- Bancos de dados individuais com padrões de uso oscilantes intercalados com períodos de inatividade podem aproveitar economias de preço com base em cobrança por segundo para a quantidade de computação usada.
- Bancos de dados individuais com demanda de recursos, é difícil prever e clientes que preferem para delegar o dimensionamento de computação para o serviço.
- Bancos de dados individuais na camada de computação provisionada com tanta frequência alteram níveis de desempenho.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>Cenários adequados para computação provisionada

- Bancos de dados individuais regular mais e mais substanciais utilização ao longo do tempo de computação.
- Bancos de dados que não podem tolerar vantagens e desvantagens de desempenho resultantes da mais frequentam de corte de memória ou atraso em autoresuming de um estado em pausa.
- Vários bancos de dados com padrões de uso oscilantes que podem ser consolidados em um único servidor e usa pools Elásticos para melhor otimização de preços.


## <a name="purchasing-model-and-service-tier"></a>Camada de serviço e modelo de compra

Banco de dados SQL sem servidor é atualmente compatível apenas com a camada de uso geral em 5 de geração de hardware em que o modelo de compra de vcore.

## <a name="autoscaling"></a>Dimensionamento automático

### <a name="scaling-responsiveness"></a>Dimensionamento de capacidade de resposta

Em geral, os bancos de dados são executados em uma máquina com capacidade suficiente para satisfazer as demandas de recursos sem interrupção para qualquer quantidade de computação solicitada dentro de limites definidos pelo `maxVcores` valor. Ocasionalmente, automaticamente de balanceamento de carga ocorre se o computador não puder atender à demanda de recursos dentro de alguns minutos. O banco de dados permanece online durante o balanceamento de carga, exceto um breve período no final da operação quando as conexões são removidas.

### <a name="memory-management"></a>Gerenciamento de memória

Memória para bancos de dados sem servidor é recuperada mais frequentemente que em bancos de dados provisionados. Esse comportamento é importante controlar os custos em sem servidor. Ao contrário de computação provisionada, memória do cache de SQL é recuperada de um banco de dados sem servidor quando a utilização de CPU ou de cache é baixa.

## <a name="autopause-and-autoresume"></a>Autopause e autoresume

### <a name="autopause"></a>Autopause

Autopause será disparada se todas as seguintes condições forem verdadeiras para a duração do atraso autopause:

- Número sessões = 0
- CPU = 0 (para a carga de trabalho de usuário em execução no pool de usuário)

Uma opção é fornecida para desabilitar autopause se desejado.

### <a name="autoresume"></a>Autoresume

Autoresume será disparada se qualquer uma das seguintes condições forem verdadeiras a qualquer momento:

|Recurso|Gatilho de Autoresume|
|---|---|
|Autenticação e autorização|Logon|
|Detecção de ameaças|Habilitar/desabilitar as configurações de detecção de ameaças no nível do banco de dados ou servidor<br>Modificando as configurações de detecção de ameaças no nível do banco de dados ou servidor|
|Descoberta e classificação de dados|Adicionando, modificando, excluindo ou exibindo os rótulos de confidencialidade|
|Auditoria|Exibindo registros de auditoria.<br>Atualizando ou exibindo a política de auditoria|
|Mascaramento de dados|Adicionar, modificar, excluir ou exibir regras de mascaramento de dados|
|Transparent Data Encryption|Estado de exibição ou status de criptografia de dados transparente|
|Armazenamento de dados de consulta (desempenho)|Modificar ou exibir configurações do repositório de consulta; o ajuste automático|
|Autotuning|Aplicativo e a verificação de recomendações de autotuning, como a indexação automática|
|Cópia de banco de dados|Criar banco de dados como cópia<br>Exportar para um arquivo BACPAC|
|Sincronização de dados SQL|Sincronização entre bancos de dados hub e membro que são executados em uma agenda configurável ou são executadas manualmente|
|Modificar alguns metadados do banco de dados|Adicionando novas marcas de banco de dados<br>Alterar vcores máximo, mínimo de vcores, atraso autopause|
|SQL Server Management Studio (SSMS)|Usando o SSMS versão 18 e abrindo uma nova janela de consulta para qualquer banco de dados no servidor continuará a qualquer banco de dados em pausa automática no mesmo servidor. Esse comportamento não ocorre se usando o SSMS versão 17.9.1 com o IntelliSense desativado-off.|

### <a name="connectivity"></a>Conectividade

Se um banco de dados sem servidor está em pausa, o primeiro logon retomar o banco de dados e retornar um erro informando que o banco de dados está disponível. Depois que o banco de dados é retomado, o logon deve ser repetido para estabelecer a conectividade. Os clientes do banco de dados com lógica de repetição de conexão não deve ser modificado.

### <a name="latency"></a>Latency

A latência para autopause ou autoresume um banco de dados sem servidor é geralmente na ordem de 1 minuto.

### <a name="feature-support"></a>Suporte ao recurso

Não há suporte para os seguintes recursos autopausing e autoresuming. Ou seja, se qualquer um dos seguintes recursos são usados, o banco de dados permanece online, independentemente de duração da inatividade do banco de dados:

- Replicação geográfica (Active Directory automático e replicação geográfica grupos de failover)
- Retenção de longo prazo (LTR)
- O banco de dados de sincronização usado na sincronização de dados SQL.


## <a name="on-boarding-into-the-serverless-compute-tier"></a>Integração com o tipo de computação sem servidor

Criando um novo banco de dados ou mover que um banco de dados existente para uma camada de computação sem servidor segue o mesmo padrão como criar um novo banco de dados em provisionado de camada de computação e envolve duas etapas a seguir:

1. Especifique o nome do objetivo de serviço. A tabela a seguir mostra a camada de serviço disponível e os tamanhos de computação atualmente disponível na visualização pública.

   |Camada de serviço|Tamanho da computação|
   |---|---|
   |Uso geral|GP_S_Gen5_1|
   |Uso geral|GP_S_Gen5_2|
   |Uso geral|GP_S_Gen5_4|

2. Opcionalmente, especifique o atraso mínimo de vCores e autopause para alterar seus valores padrão. A tabela a seguir mostra os valores disponíveis para esses parâmetros.

   |Parâmetro|Opções de valor|Valor padrão|
   |---|---|---|---|
   |Mínimo de vCores|Qualquer uma das {0,5, 1, 2, 4} não exceda o máximo vCores|0,5 vCores|
   |Autopause atraso|Mín.: 360 minutos (6 horas)<br>Máx.: 10.080 minutos (7 dias)<br>Incrementos: 60 minutos<br>Desabilitar autopause: -1|360 minutos|

> [!NOTE]
> Usando o T-SQL para mover um banco de dados existente para sem servidor ou alterar seu tamanho de computação não é suportada, mas pode ser feito por meio do portal do Azure ou PowerShell.

### <a name="create-new-database-using-the-azure-portal"></a>Criar novo banco de dados usando o portal do Azure

Confira [Início Rápido: Criar um banco de dados no banco de dados do SQL Azure usando o portal do Azure](sql-database-single-database-get-started.md).

### <a name="create-new-database-using-powershell"></a>Criar novo banco de dados usando o PowerShell

O exemplo a seguir cria um novo banco de dados na camada de computação sem servidor definida por objetivo de serviço chamado GP_S_Gen5_4 com valores padrão para o atraso mínimo de vCores e autopause.

Sem servidor requer uma versão mais recente do PowerShell que está atualmente na galeria, portanto, execute `Update-Module Az.Sql` para obter os cmdlets mais recentes sem servidor habilitado.

```powershell
New-AzSqlDatabase `
  -ResourceGroupName $resourceGroupName `
  -ServerName $serverName `
  -DatabaseName $databaseName `
  -ComputeModel Serverless `
  -Edition GeneralPurpose `
  -ComputeGeneration Gen5 `
  -MinVcore 0.5 `
  -MaxVcore 2 `
  -AutoPauseDelay 720
```

### <a name="move-existing-database-into-the-serverless-compute-tier"></a>Mover o banco de dados existente para a camada de computação sem servidor

O exemplo a seguir move um único banco de dados da camada de computação provisionada para a camada de computação sem servidor. Este exemplo usa os valores padrão para o mínimo de vCores, vCores max e autopause atraso.

```powershell
Set-AzSqlDatabase
  -ResourceGroupName $resourceGroupName `
  -ServerName $serverName `
  -DatabaseName $databaseName `
  -Edition GeneralPurpose `
  -ComputeModel Serverless `
  -ComputeGeneration Gen5 `
  -MinVcore 1 `
  -MaxVcore 4 `
  -AutoPauseDelay 1440
```

### <a name="move-a-database-out-of-the-serverless-compute-tier"></a>Mover um banco de dados fora da camada de computação sem servidor

Um banco de dados sem servidor pode ser movido para uma camada de computação provisionada da mesma forma como a movimentação de um banco de dados de computação provisionada em uma camada de computação sem servidor.

## <a name="modify-serverless-configuration-parameters"></a>Modificar parâmetros de configuração sem servidor

### <a name="maximum-vcores"></a>Máximo de vCores

Modificar os vCores máximo é executada usando o [AzSqlDatabase conjunto](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) comando no PowerShell usando o `MaxVcore` argumento.

### <a name="minimum-vcores"></a>Mínimo de vCores

Modificar os vCores máximo é executada usando o [AzSqlDatabase conjunto](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) comando no PowerShell usando o `MinVcore` argumento.

### <a name="autopause-delay"></a>Autopause atraso

Modificar os vCores máximo é executada usando o [AzSqlDatabase conjunto](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) comando no PowerShell usando o `AutoPauseDelay` argumento.

## <a name="monitor-serverless-database"></a>Banco de dados sem servidor Monitor

### <a name="resources-used-and-billed"></a>Recursos usados e cobrado

Os recursos de um banco de dados sem servidor são encapsulados pelas entidades a seguir:

#### <a name="app-package"></a>Pacote do aplicativo

O pacote do aplicativo é o limite de gerenciamento de recursos a maioria dos externo para um banco de dados, independentemente de é o banco de dados em uma camada de computação sem servidor ou provisionado. O pacote de aplicativo contém a instância do SQL e serviços externos desse escopo junto todos os recursos de usuário e do sistema usados por um banco de dados no banco de dados SQL. R e pesquisa de texto completo são exemplos de serviços externos. Em geral, a instância do SQL domina a utilização geral do recurso entre o pacote do aplicativo.

#### <a name="user-resource-pool"></a>Pool de recursos do usuário

O pool de recursos do usuário é interna a maioria dos limites de gerenciamento de recursos para um banco de dados, independentemente de é o banco de dados em uma camada de computação sem servidor ou provisionado. O pool de recursos do usuário escopos da CPU e e/s para carga de trabalho do usuário gerada por consultas DDL (por exemplo, CREATE, ALTER, etc.) e DML (por exemplo, SELECT, INSERT, UPDATE, DELETE, etc.). Essas consultas geralmente representam a proporção mais substancial de utilização de dentro do pacote do aplicativo.

### <a name="metrics"></a>Métricas

|Entidade|Métrica|DESCRIÇÃO|Unidades|
|---|---|---|---|
|Pacote do aplicativo|app_cpu_percent|Porcentagem de vCores usados pelo aplicativo em relação ao vCores máximo permitido para o aplicativo.|Percentual|
|Pacote do aplicativo|app_cpu_billed|A quantidade de computação cobrada para o aplicativo durante o período do relatório. O valor pago durante esse período é o produto dessa métrica e o preço unitário de vCore.<br>Os valores dessa métrica são determinados pela agregação ao longo do tempo, o número máximo de CPU usado e a memória usada por segundo.<br>Se o valor usado for menor que a quantidade mínima provisionada conforme definido pelo mínimo de vCores e memória mínima, em seguida, a quantidade mínima provisionada é cobrada.  Para comparar a CPU com a memória para fins de cobrança, memória é normalizada em unidades de vCores, pelo redimensionamento a quantidade de memória em GB por 3 GB por vCore.|segundos de vCore|
|Pacote do aplicativo|app_memory_percent|Porcentagem de memória usada pelo aplicativo em relação à memória máxima permitida para o aplicativo.|Percentual|
|Pool de usuário|cpu_percent|Porcentagem de vCores usados pela carga de trabalho do usuário em relação ao vCores máximo permitido para a carga de trabalho do usuário.|Percentual|
|Pool de usuário|data_IO_percent|Porcentagem de dados usado pela carga de trabalho do usuário em relação a IOPS de dados máximo de IOPS permitidos para carga de trabalho do usuário.|Percentual|
|Pool de usuário|log_IO_percent|Porcentagem de log MB/s usada pela carga de trabalho do usuário em relação ao MB/s de log máximo permitido para carga de trabalho do usuário.|Percentual|
|Pool de usuário|workers_percent|Porcentagem de trabalhadores usados por carga de trabalho do usuário em relação ao máximo de trabalhos permitidos para a carga de trabalho do usuário.|Percentual|
|Pool de usuário|sessions_percent|Porcentagem de sessões usadas pela carga de trabalho do usuário em relação ao máximo permitido para a carga de trabalho do usuário de sessões.|Percentual|
____

> [!NOTE]
> Métricas no portal do Azure estão disponíveis no painel de banco de dados para um banco de dados sob **monitoramento**.

### <a name="pause-and-resume-status"></a>Status de pausar e retomar

No portal do Azure, o status do banco de dados é exibido no painel de visão geral do servidor que lista os bancos de dados que ele contém. O status do banco de dados também é exibido no painel de visão geral do banco de dados.

Usando o seguinte comando do PowerShell para consultar a pausa e retomar o status de um banco de dados:

```powershell
Get-AzSqlDatabase `
  -ResourceGroupName $resourcegroupname `
  -ServerName $servername `
  -DatabaseName $databasename `
  | Select -ExpandProperty "Status"
```

## <a name="resource-limits"></a>Limites de recursos

Para limites de recursos, consulte [camada de computação sem servidor](sql-database-vCore-resource-limits-single-databases.md#serverless-compute-tier)

## <a name="billing"></a>Cobrança

A quantidade de computação cobrada por segundo é o número máximo de uso de CPU e memória usada por segundo. Se usado a quantidade de CPU e memória usada é menor que a quantidade mínima provisionada para cada um, a quantidade provisionada é cobrada. Para comparar a CPU com a memória para fins de cobrança, memória é normalizada em unidades de vCores, pelo redimensionamento a quantidade de memória em GB por 3 GB por vCore.

- **Recurso cobrado**: CPU e memória
- **Valor cobrado ($)**: preço unitário de vCore * max (mínimo de vCores, vCores usado, a memória mínima do GB * memória de 1/3 GB usado * 1/3) 
- **Frequência de cobrança**: por segundo

A quantidade de computação cobrada é exposta pela métrica a seguir:

- **Métrica**: app_cpu_billed (vCore segundos)
- **Definição**: max (mínimo de vCores, vCores usado, a memória mínima do GB * memória de 1/3 GB usado * 1/3) *
- **Frequência do relatório**: Por minuto

> [!NOTE]
> \* Essa quantidade é calculada a cada segundo e agregada em 1 minuto.

**Exemplo**: Considere um banco de dados usando o GP_S_Gen5_4 durante um período de uma hora com o uso a seguir:

|Tempo (horas: minutos)|app_cpu_billed (vCore segundos)|
|---|---|
|0:01|63|
|0:02|123|
|0:03|95|
|0:04|54|
|0:05|41|
|0:06 - 1:00|1255|
||Total: 1631|

Suponha que o preço de unidade de computação é $0.2609/vCore/hour. Em seguida, a computação cobrada para esse período de uma hora é determinada usando a seguinte fórmula: **$0.2609/vCore/hour * 1631 vCore segundos * 1 hora/3.600 segundos = US $0.1232**

## <a name="available-regions"></a>Regiões disponíveis

A camada de computação sem servidor está disponível em todas as regiões, exceto regiões a seguir: Austrália Central, China Oriental, Norte da China, Sul da França, Alemanha Central, Alemanha nordeste, Índia Ocidental, Sul da Coreia, Oeste da África do Sul, Norte do Reino Unido, Sul do Reino Unido, oeste do Reino Unido e Oeste dos EUA

## <a name="next-steps"></a>Próximas etapas

Para limites de recursos, consulte [limites de recursos de camada de computação sem servidor](sql-database-vCore-resource-limits-single-databases.md#serverless-compute-tier).