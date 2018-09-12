---
title: Escala e hospedagem no Azure Functions | Microsoft Docs
description: Saiba como escolher entre o plano de Consumo do Azure Functions e o plano do Serviço de Aplicativo.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: azure functions, functions, plano de consumo, plano do serviço de aplicativo, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 08/09/2018
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b562b10be8add02d0a3c6eb95e8df8eb0711a208
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44093525"
---
# <a name="azure-functions-scale-and-hosting"></a>Escala e hospedagem no Azure Functions

O Azure Functions executa em dois modos diferentes: plano de Consumo e plano do Serviço de Aplicativo do Azure. O plano de Consumo aloca automaticamente a potência de computação quando o código está em execução. O aplicativo é escalado horizontalmente quando necessário para lidar com a carga e reduzido verticalmente quando o código não está em execução. Você não precisa pagar por VMs ociosas nem reservar capacidade com antecedência. Este artigo se concentra no plano de Consumo, um modelo de aplicativo [sem servidor](https://azure.microsoft.com/overview/serverless-computing/). Para obter detalhes sobre como o plano do Serviço de Aplicativo dedicado funciona, consulte [Visão geral detalhada de planos de Serviço de Aplicativo do Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

> [!NOTE]  
> Atualmente, a [hospedagem do Linux](functions-create-first-azure-function-azure-cli-linux.md) só está disponível em um Plano do Serviço de Aplicativo.

Se ainda não estiver familiarizado com o Azure Functions, veja a [Visão geral do Azure Functions](functions-overview.md).

Ao criar um aplicativo de funções, você escolhe o plano de hospedagem para funções no aplicativo. Em qualquer plano, uma instância do *host do Azure Functions* executa as funções. O tipo de controles de plano:

* Como as instâncias de host são dimensionadas.
* Os recursos que estão disponíveis para cada host.

> [!IMPORTANT]
> Você deverá escolher o tipo de plano de hospedagem durante a criação do aplicativo de funções. Não é possível alterá-lo posteriormente.

Em um Plano do Serviço de Aplicativo, você pode dimensionar entre as camadas para alocar uma quantidade diferente de recursos. No plano de Consumo, o Azure Functions trata automaticamente de toda a alocação de recursos. 

## <a name="consumption-plan"></a>Plano de consumo

Ao usar um plano de Consumo, as instâncias do host do Azure Functions são adicionadas e removidas dinamicamente com base no número de eventos de entrada. Esse plano sem servidor escala automaticamente, e você é cobrado pelos recursos de computação apenas durante a execução de suas funções. Em um plano de consumo, a execução de uma função expire após um período configurável.

> [!NOTE]
> O tempo limite padrão para funções em um plano de consumo é de 5 minutos. O valor pode ser aumentado para o App de Função até um máximo de 10 minutos, alterando a propriedade `functionTimeout` no arquivo de projeto [ host.json ](functions-host-json.md#functiontimeout).

A cobrança baseia-se no número de execuções, no tempo de execução e na memória usada. A cobrança é agregada entre todas as funções em um aplicativo de funções. Para saber mais, confira a [página de preços do Azure Functions].

O plano de Consumo é o plano de hospedagem padrão e oferece os seguintes benefícios:

* Pague apenas quando suas funções forem executadas.
* Escale horizontalmente de forma automática, mesmo durante períodos de carga alta.

## <a name="app-service-plan"></a>Plano do Serviço de Aplicativo

No plano do Serviço de Aplicativo dedicado, os aplicativos de funções executam em VMs dedicadas em SKUs Básico, Standard, Premium e Isolado, o que é igual a outros aplicativos do Serviço de Aplicativo. VMs dedicadas são alocadas para o aplicativo de funções, o que significa que o host de funções pode estar [sempre em execução](#always-on). Os Planos do Serviço de Aplicativo oferecem suporte a Linux.

Considere um Plano do Serviço de Aplicativo nos seguintes casos:

* Você tem VMs subutilizadas que já estão executando outras instâncias do Serviço de Aplicativo.
* Os aplicativos de funções executam continuamente ou quase continuamente. Nesse caso, um Plano de Serviço de Aplicativo pode ser mais econômico.
* Você precisa de mais opções de CPU ou memória do que é fornecido no plano de Consumo.
* O código precisa ser executado por mais tempo do que o tempo máximo de execução permitido no plano de Consumo, que é de até 10 minutos.
* Você precisa de recursos que estão disponíveis somente em um Plano do Serviço de Aplicativo, como suporte para o Ambiente de Serviço de Aplicativo, conectividade VPN/VNET e tamanhos de VM maiores.
* Você deseja executar seu aplicativo de funções no Linux, ou você deseja fornecer uma imagem personalizada na qual executará suas funções.

Uma VM baseia o curso no número de execuções, no tempo de execução e na memória usada. Como resultado, você não paga mais do que o custo da instância da VM alocada. Para obter detalhes sobre como o plano do Serviço de Aplicativo funciona, consulte [Visão geral detalhada de planos de Serviço de Aplicativo do Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). 

Com um plano do Serviço de Aplicativo, você pode escalar horizontalmente manualmente adicionando mais instâncias de VM ou você pode habilitar o dimensionamento automático. Para saber mais, confira [Dimensionar a contagem de instâncias manual ou automaticamente](../monitoring-and-diagnostics/monitoring-autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json). Você também pode escalar verticalmente escolhendo um plano do Serviço de Aplicativo diferente. Para obter mais informações, consulte [Escalar verticalmente um aplicativo no Azure](../app-service/web-sites-scale.md). 

Ao executar funções JavaScript em um plano do Serviço de Aplicativo, você deve escolher um plano que tenha menos vCPUs. Para saber mais, acesse [Escolher Planos do Serviço de Aplicativo de núcleo único](functions-reference-node.md#considerations-for-javascript-functions).  

<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 
<a name="always-on"></a>
### <a name="always-on"></a>Always On

Se executar em um plano do Serviço de Aplicativo, você deverá habilitar a configuração **Always On** para que o aplicativo de funções execute corretamente. Em um plano do Serviço de Aplicativo, o tempo de execução das funções ficará ocioso após alguns minutos de inatividade, portanto, apenas gatilhos HTTP "despertarão" suas funções. Always On está disponível apenas em um plano de Serviço de Aplicativo. Em um plano de Consumo, a plataforma ativa automaticamente os aplicativos de função.

## <a name="what-is-my-hosting-plan"></a>Qual é o meu plano de hospedagem

Para determinar o plano de hospedagem usado pelo aplicativo de funções, consulte **plano do Serviço de Aplicativo/tipo de preço** na guia **Visão geral** do aplicativo de funções no [portal do Azure](https://portal.azure.com). Para planos do Serviço de Aplicativo, o tipo de preço também é indicado. 

![Exibir o plano de dimensionamento no portal](./media/functions-scale/function-app-overview-portal.png)

Também é possível usar a CLI do Azure para determinar o plano, da seguinte maneira:

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

Quando a saída desse comando for `dynamic`, o aplicativo de funções estará no plano de Consumo. Todos os outros valores indicam camadas de um plano do Serviço de Aplicativo.

Mesmo com Always On habilitado, o tempo limite de execução para funções individuais é controlado pela configuração `functionTimeout` no arquivo de projeto [host.json](functions-host-json.md#functiontimeout).

## <a name="storage-account-requirements"></a>Requisitos da conta de armazenamento

Em um plano de Consumo ou em um plano do Serviço de Aplicativo, um aplicativo de funções requer uma conta de Armazenamento do Microsoft Azure geral, que dá suporte para Armazenamento de Blobs, Filas, Arquivos e Tabelas do Azure. Isso ocorre porque o Functions depende do Armazenamento do Microsoft Azure para operações como o gerenciamento de gatilhos e execuções de funções de registro em log, mas algumas contas de armazenamento não dão suporte a filas e tabelas. Essas contas, que incluem contas de armazenamento somente blob (incluindo armazenamento Premium) e contas de armazenamento para uso geral com replicação de armazenamento com redundância de zona, são filtradas das seleções da **Conta de Armazenamento** existente quando você cria um aplicativo de funções.

<!-- JH: Does using a Premium Storage account improve perf? -->

Para saber mais sobre os tipos de conta de armazenamento, consulte [Apresentando os serviços de armazenamento do Azure](../storage/common/storage-introduction.md#azure-storage-services).

## <a name="how-the-consumption-plan-works"></a>Como funciona o plano de consumo

No plano de Consumo, o controlador de escala dimensiona automaticamente os recursos de CPU e memória adicionando outras instâncias do host do Functions de acordo com o número de eventos nos quais suas funções são disparadas. Cada instância do host do Funções é limitada a 1,5 GB de memória.  Uma instância do host é o aplicativo de funções, o que significa que todas as funções dentro de um aplicativo de funções compartilham recursos em uma instância e uma escala ao mesmo tempo. Aplicativos de função que compartilham o mesmo plano de consumo são dimensionados de forma independente.  

Ao usar o plano de hospedagem de Consumo, os arquivos do código de função são armazenados em compartilhamentos dos Arquivos do Azure na conta de armazenamento principal da função. Quando você exclui a conta de armazenamento principal do aplicativo de funções, os arquivos de código de função são excluídos e não podem ser recuperados.

> [!NOTE]
> Quando estiver usando um gatilho de blob em um plano de consumo, poderá haver um atraso de até 10 minutos no processamento de novos blobs. Esse atraso ocorre quando um aplicativo de funções torna-se ocioso. Depois que o aplicativo de funções estiver em execução, os blobs serão processados imediatamente. Para evitar esse atraso de inicialização a frio, use um plano do Serviço de Aplicativo com **Always On** habilitado ou use o gatilho de Grade de Eventos. Para obter mais informações, consulte [o artigo de referência de associação de gatilho de blob](functions-bindings-storage-blob.md#trigger).

### <a name="runtime-scaling"></a>Escalonamento de tempo de execução

O Azure Functions usa um componente chamado *controlador de escala* para monitorar a taxa de eventos e determinar se deve aumentar ou reduzir. O controlador de escala usa heurística para cada tipo de gatilho. Por exemplo, ao usar um gatilho do armazenamento de Filas do Azure, ele escala com base no tamanho da fila e na idade da mensagem em fila mais antiga.

A unidade de escala é o aplicativo de funções. Quando o aplicativo de funções é dimensionado na horizontal, mais recursos são alocados para executar várias instâncias do host do Azure Functions. Em contrapartida, quando a demanda por computação é reduzida, o controlador de escala remove as instâncias do host de função. O número de instâncias é eventualmente reduzido a zero quando nenhuma função está em execução em um aplicativo de funções.

![Controlador de escala monitorando eventos e criando instâncias](./media/functions-scale/central-listener.png)

### <a name="understanding-scaling-behaviors"></a>Noções básicas dos comportamentos de dimensionamento

O dimensionamento pode variar em uma série de fatores e ser diferente com base no gatilho e na linguagem selecionada. No entanto, há alguns aspectos de dimensionamento que existem no sistema atualmente:

* Um único aplicativo de funções será escalado verticalmente somente para um máximo de 200 instâncias. Uma única instância pode processar mais de uma mensagem ou solicitação por vez, portanto, não há um limite definido de número de execuções simultâneas.
* Novas instâncias só serão alocadas no máximo uma vez a cada 10 segundos.

Gatilhos diferentes também podem ter diferentes limites de dimensionamento, como documentado abaixo:

* [Hub de Evento](functions-bindings-event-hubs.md#trigger---scaling)

### <a name="best-practices-and-patterns-for-scalable-apps"></a>Melhores práticas e padrões para aplicativos escalonáveis

Há muitos aspectos de um aplicativo de funções que afetarão a qualidade da escala, incluindo a configuração do host, o espaço de tempo de execução e a eficiência dos recursos.  Para obter mais informações, consulte a [seção de escalabilidade do artigo sobre considerações de desempenho](functions-best-practices.md#scalability-best-practices). Adicionalmente, é necessário que você saiba como as conexões se comportam na medida em que o aplicativo de funções é dimensionado. Para saber mais, confira [Como gerenciar conexões no Azure Functions](manage-connections.md).

### <a name="billing-model"></a>Modelo de cobrança

A cobrança do plano de Consumo é descrita detalhadamente na [página de preços do Azure Functions]. O uso é agregado no nível do aplicativo de funções e conta apenas o tempo durante o qual o código de função é executado. Veja a seguir as unidades de cobrança:

* **Consumo de recursos em GB/s (gigabyte por segundo)**. Calculado como uma combinação do tamanho da memória e o tempo de execução para todas as funções dentro de um aplicativo de Funções. 
* **Execuções**. Contadas toda vez que uma função é executada em resposta a um gatilho de evento.

[Página de preços do Azure Functions]: https://azure.microsoft.com/pricing/details/functions
