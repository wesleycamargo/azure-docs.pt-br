---
title: "SQL Data Warehouse do Azure - Usando o Azure Functions para automatizar níveis de computação do SQL Data Warehouse | Microsoft Docs"
description: "Como usar o Azure Functions para gerenciar a computação do seu data warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: barbkess
ms.assetid: 52DFC191-E094-4B04-893F-B64D5828A901
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: quickstart
ms.date: 11/06/2017
ms.author: elbutter
ms.openlocfilehash: 56543946c2a2cfe63feb249fcd4ac63517700115
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/11/2017
---
# <a name="use-azure-functions-to-automate-sql-dw-compute-levels"></a>Use o Azure Functions para automatizar os níveis de computação SQL DW

Este tutorial demonstra como você pode usar o Azure Functions para gerenciar os níveis de computação do SQL Data Warehouse do Azure. Essas arquiteturas são recomendadas para uso com o SQL Data Warehouse [Otimizado para elasticidade][Performance Tiers].

Para usar o Aplicativo de funções do Azure com o Azure SQL Data Warehouse, você deve criar uma [Conta de entidade de serviço](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal) com acesso de colaborador na mesma assinatura que a sua instância do data warehouse. 

## <a name="deploy-timer-based-scaler-with-an-azure-resource-manager-template"></a>Implantar o escalonador baseado em temporizador com um modelo do Azure Resource Manager

Para implantar o modelo, você precisará das informações a seguir:

- Nome do grupo de recursos onde está a sua instância do SQL DW
- Nome do servidor lógico onde está a sua instância do SQL DW
- Nome da sua instância do SQL DW
- A ID de locatário (ID do diretório) do Azure Active Directory
- ID da assinatura 
- ID do aplicativo da entidade de serviço
- Chave secreta da entidade de serviço

Após reunir as informações acima, implantar este modelo:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

Depois de implantar o modelo, você deve encontrar três novos recursos: um plano do Serviço de Aplicativo do Azure gratuito, um plano do Aplicativo de funções baseado em consumo e uma conta de armazenamento que manipulará o registro em log e a fila de operações. Continue a ler as outras seções para saber como modificar as funções implantadas de acordo com as suas necessidades.

### <a name="change-the-scale-up-or-scale-down-compute-level"></a>Alterar o nível de computação para escalar ou reduzir verticalmente

1. Navegue até o serviço d do Aplicativo de funções. Se você implantou o modelo com os valores padrão, esse serviço deve ser chamado *DWOperations*. Quando seu Aplicativo de funções for aberto, você deve observar que há cinco funções implantadas para a função do Serviço de Aplicativo de funções. 

   ![Funções implantadas com o modelo](media/manage-compute-with-azure-functions/five-functions.png)

2. Selecione *DWScaleDownTrigger* ou *DWScaleUpTrigger* dependendo se você deseja alterar a hora para escalar ou reduzir verticalmente. Na lista suspensa, selecione integrar.

   ![Selecione Integrar para a função](media/manage-compute-with-azure-functions/select-integrate.png)

3. O valor exibido deve ser *ScaleDownTime %* ou *ScaleUpTime %*. Esses valores indicam que o agendamento se baseia nos valores definidos nas suas [Configurações do aplicativo][Application Settings]. Por enquanto, você pode ignorar isso e alterar o agendamento para a hora de sua preferência com base nas próximas etapas.

4. Na área de agendamento, adicione a hora à expressão CRON para refletir a frequência desejada para escalar verticalmente o SQL Data Warehouse. 

  ![Alterar o agendamento da função](media/manage-compute-with-azure-functions/change-schedule.png)

  O valor de `schedule` é uma [expressão CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) que inclui estes seis campos: 
  ```json
  {second} {minute} {hour} {day} {month} {day-of-week}
  ```

  Por exemplo *"0 30 9 * * 1-5"* refletiria um gatilho cada dia útil às 9:30. Para obter mais informações, visite [exemplos de agendamento][schedule examples] do Azure Functions.


### <a name="change-the-scale-up-or-scale-down-time"></a>Alterar a hora para escalar ou reduzir verticalmente

1. Navegue até o serviço d do Aplicativo de funções. Se você implantou o modelo com os valores padrão, esse serviço deve ser chamado *DWOperations*. Quando seu Aplicativo de funções for aberto, você deve observar que há cinco funções implantadas para a função do Serviço de Aplicativo de funções. 

2. Selecione *DWScaleDownTrigger* ou *DWScaleUpTrigger* dependendo se você deseja alterar o valor de computação para escalar ou reduzir verticalmente. Ao selecionar as funções, o painel deve mostrar o arquivo *index.js*.

   ![Alterar o nível de computação do gatilho da função](media/manage-compute-with-azure-functions/index-js.png)

3. Altere o valor de *ServiceLevelObjective* para o nível desejado e aperte para salvar a opção. Este é o nível de computação para o qual a sua instância do data warehouse será dimensionada com base no agendamento definido na seção Integrar.

### <a name="use-pause-or-resume-instead-of-scale"></a>Usar pausar ou retomar em vez do dimensionamento 

Atualmente, as funções habilitadas por padrão são *DWScaleDownTrigger* e *DWScaleUpTrigger*. No entanto, se você quiser usar a funcionalidade de pausar e retomar, você pode habilitar *DWPauseTrigger* ou *DWResumeTrigger*.

1. Navegue até o painel Funções.

   ![Painel Funções](media/manage-compute-with-azure-functions/functions-pane.png)



2. Clique no botão de alternância deslizante para os gatilhos correspondentes que você deseja habilitar.

3. Navegue até as guias *Integrar* dos respectivos gatilhos para alterar o agendamento.

   [!NOTE]: The functional difference between the scaling triggers and the pause/resume triggers is the message that is sent to the queue. See [Add a new trigger function][Add a new trigger function] for more information.



### <a name="add-a-new-trigger-function"></a>Adicionar uma nova função de gatilho

Atualmente, há apenas duas funções de dimensionamento incluídas no modelo. Isso significa que, ao longo de um dia, você só pode escalar e reduzir verticalmente somente uma vez. Para um controle mais granular, como reduzir verticalmente várias vezes por dia ou ter um comportamento de dimensionamento diferente nos finais de semana, você precisará adicionar outro gatilho.

1. Criar uma nova função em branco. Selecione o botão *+* perto da localização de Funções para exibir o painel de modelos de função.

   ![Criar nova função](media/manage-compute-with-azure-functions/create-new-function.png)

2. Em Idioma, selecione *Javascript*, em seguida, selecione *TimerTrigger*.

   ![Criar nova função](media/manage-compute-with-azure-functions/timertrigger-js.png)

3. Nomeie a sua função e defina o agendamento. A imagem mostra como fazer para disparar sua função todos os sábados à meia-noite (tarde da noite de sexta-feira).

   ![Reduzir verticalmente sábado](media/manage-compute-with-azure-functions/scale-down-saturday.png)

4. Copie o conteúdo de *index.js* de uma das outras funções de gatilho.

   ![Copiar index.js](media/manage-compute-with-azure-functions/index-js.png)

5. Defina a sua variável de operação para o comportamento desejado da seguinte maneira:

   ```javascript
   // Resume the data warehouse instance
   var operation = {
       "operationType": "ResumeDw"
   }

   // Pause the data warehouse instance
   var operation = {
       "operationType": "PauseDw"
   }

   // Scale the data warehouse instance to DW600
   var operation = {
       "operationType": "ScaleDw",
       "ServiceLevelObjective": "DW600"
   }
   ```


### <a name="complex-scheduling"></a>Agendamentos complexos

Esta seção demonstrará brevemente o que é necessário para aproveitar mais os recursos de agendamento complexo para pausar, retomar e dimensionar.

#### <a name="example-1"></a>Exemplo 1:

Escalar verticalmente diariamente às 8:00 para DW600 e reduzir verticalmente às 20:00 para DW200.

| Função  | Agenda     | Operação                                |
| :-------- | :----------- | :--------------------------------------- |
| Function1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",  "ServiceLevelObjective": "DW600"}` |
| Function2 | 0 0 20 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

#### <a name="example-2"></a>Exemplo 2: 

Escalar verticalmente diariamente às 8:00 para DW1000, reduzir verticalmente uma vez para DW600 às 16:00 e reduzir verticalmente às 22:00 para DW200.

| Função  | Agenda     | Operação                                |
| :-------- | :----------- | :--------------------------------------- |
| Function1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",  "ServiceLevelObjective": "DW1000"}` |
| Function2 | 0 0 16 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600"}` |
| Function3 | 0 0 22 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

#### <a name="example-3"></a>Exemplo 3: 

Escalar verticalmente às 8:00 para DW1000, reduzir verticalmente uma vez para DW600 às 16:00 em dias úteis. Pausar sexta-feira às 23:00, retomar segunda-feira de manhã às 7:00.

| Função  | Agenda       | Operação                                |
| :-------- | :------------- | :--------------------------------------- |
| Function1 | 0 0 8 * * 1-5  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000"}` |
| Function2 | 0 0 16 * * 1-5 | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600"}` |
| Function3 | 0 0 23 * * 5   | `var operation = {"operationType": "PauseDw"}` |
| Function4 | 0 0 7 * * 0    | `var operation = {"operationType": "ResumeDw"}` |



## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as funções de [gatilho de temporizador](../azure-functions/functions-create-scheduled-function.md) do Azure.

Confira o [repositório de exemplos](https://github.com/Microsoft/sql-data-warehouse-samples) do SQL Data Warehouse.



[schedule examples]: ../azure-functions/functions-bindings-timer.md#schedule-examples

[Application Settings]: ../azure-functions/functions-how-to-use-azure-function-app-settings.md
[Add a new trigger function]: manage-compute-with-azure-functions.md#add-a-new-trigger-function

[Performance Tiers]: performance-tiers.md
