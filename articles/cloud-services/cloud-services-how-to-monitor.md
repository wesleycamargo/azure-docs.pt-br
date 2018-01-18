---
title: "Monitorar um Serviço de Nuvem do Azure | Microsoft Docs"
description: "Descreve o que envolve o monitoramento de um Serviço de Nuvem do Azure e quais são algumas das opções."
services: cloud-services
documentationcenter: 
author: thraka
manager: timlt
editor: 
ms.assetid: 
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/22/2017
ms.author: adegeo
ms.openlocfilehash: c63a49c65f2d8261caa534308477888c752a89da
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/10/2018
---
# <a name="introduction-to-cloud-service-monitoring"></a>Introdução ao Monitoramento de Serviço de Nuvem

Você pode monitorar as principais métricas de desempenho de qualquer serviço de nuvem. Toda função de serviço de nuvem coleta o mínimo de dados: uso da CPU, uso da rede e utilização do disco. Se o serviço de nuvem tiver a extensão `Microsoft.Azure.Diagnostics` aplicada a uma função, essa função poderá coletar outros pontos de dados. Este artigo fornece uma introdução aos Diagnósticos do Azure para Serviços de Nuvem.

Com o monitoramento básico, os dados do contador de desempenho das instâncias de função passam por amostragem e são coletados em intervalos de três minutos. Esses dados de monitoramentos básico não são armazenados em sua conta de armazenamento, e não há custo adicional associado a eles.

Com o monitoramento avançado, as métricas adicionais passam por amostragem e são coletadas em intervalos de cinco minutos, uma hora e 12 horas. Os dados agregados são armazenados em uma conta de armazenamento, em tabelas, e são excluídos após 10 dias. A conta de armazenamento usada é configurada pela função; use contas de armazenamento diferentes para funções diferentes. Isso é configurado com uma cadeia de conexão nos arquivos [.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) e [.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg).


## <a name="basic-monitoring"></a>Monitoramento básico

Conforme afirmado na introdução, um serviço de nuvem coleta automaticamente os dados de monitoramentos básico da máquina virtual do host. Esses dados incluem a porcentagem da CPU, entrada/saída de rede e leitura/gravação no disco. Os dados de monitoramento coletados são exibidos automaticamente nas páginas de visão geral e de métricas do serviço de nuvem, no Portal do Azure. 

O monitoramento básico não exige uma conta de armazenamento. 

![blocos de monitoramento básico de serviço de nuvem](media/cloud-services-how-to-monitor/basic-tiles.png)

## <a name="advanced-monitoring"></a>Monitoramento avançado

O monitoramento avançado envolve o uso da extensão de Diagnóstico do Azure (e, opcionalmente, o SDK do Application Insights) na função que você deseja monitorar. A extensão de diagnóstico usa um arquivo de configuração (por função) chamado **diagnostics.wadcfgx** para configurar as métricas de diagnóstico monitoradas. Os dados coletados pela extensão de Diagnóstico do Azure são armazenados em uma conta de Armazenamento do Azure, que é configurada nos arquivos **.wadcfgx**, [.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) e [.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg). Isso significa que há um custo extra associado ao monitoramento avançado.

À medida que cada função é criada, o Visual Studio adiciona a extensão de Diagnóstico do Azure a ela. Essa extensão pode coletar os seguintes tipos de informação:

* Contadores de desempenho personalizados
* Logs de aplicativo
* Logs de eventos do Windows
* Fonte de evento do .NET
* Logs IIS
* Manifesto com base no ETW
* Despejos de falhas
* Logs de erro do cliente

Embora todos esses dados sejam agregados à conta de armazenamento, o portal não fornece um modo nativo para colocar os dados em gráfico. Use outro serviço, como o Application Insights, para correlacionar e exibir os dados.

### <a name="use-application-insights"></a>Usar o Application insights

Ao publicar o Serviço de Nuvem do Visual Studio, você recebe a opção de enviar os dados de diagnóstico ao Application Insights. Você pode criar o recurso do Application Insights no momento ou enviar os dados para um recurso existente. Seu serviço de nuvem pode ser monitorado pelo Application Insights com relação à disponibilidade, ao desempenho, falhas e uso. É possível adicionar gráficos personalizados ao Application Insights, para que você veja os dados mais importantes para você. Os dados de instância da função podem ser coletados usando o SDK do Application Insights em seu projeto de serviço de nuvem. Para saber mais sobre como integrar o Application Insights, consulte [Application Insights com Serviços de Nuvem](../application-insights/app-insights-cloudservices.md).

Perceba que embora você possa usar o Application Insights para exibir os contadores de desempenho (e as outras configurações) especificados por meio da extensão de Diagnóstico do Windows Azure, você só terá uma experiência mais rica integrando o SDK do Application Insights às suas funções de trabalho e da Web.


## <a name="add-advanced-monitoring"></a>Adicionar monitoramento avançado

Primeiro, se você não tiver uma conta de armazenamento **clássica**, [crie uma](../storage/common/storage-create-storage-account.md#create-a-storage-account). Crie a conta de armazenamento com o **Modelo de implantação clássico** especificado.

Em seguida, navegue até o recurso **Conta de armazenamento (clássico)**. Selecione **Configurações** > **Chaves de Acesso** e copie o valor de **Cadeia de conexão primária**. Você precisa desse valor para o serviço de nuvem. 

Você precisa alterar dois arquivos de configuração para habilitar o diagnóstico avançado, **ServiceDefinition.csdef** e **ServiceConfiguration.cscfg**. Provavelmente, você tem dois arquivos **.cscfg**, um denominado **ServiceConfiguration.cloud.cscfg** para a implantação no Azure, e outro chamado **ServiceConfiguration.local.cscfg**, que é usado para implantações de depuração local. Altere os dois.

No arquivo **ServiceDefinition.csdef**, adicione uma nova configuração chamada `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` para cada função que usa diagnóstico avançado. O Visual Studio adiciona esse valor ao arquivo quando você cria um novo projeto. Caso não esteja presente, adicione-o agora. 

```xml
<ServiceDefinition name="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
```

Isso define uma nova configuração que deve ser adicionada a cada arquivo **ServiceConfiguration.cscfg**. Abra e altere cada arquivo **.cscfg**. Adicione uma configuração chamada `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`. Defina o valor como o **Cadeia de conexão primária** da conta de armazenamento clássico, ou como `UseDevelopmentStorage=true`, se você quiser usar o armazenamento local no computador de desenvolvimento.

```xml
<ServiceConfiguration serviceName="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2015-04.2.6">
  <Role name="WorkerRoleWithSBQueue1">
    <Instances count="1" />
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=mystorage;AccountKey=KWwkdfmskOIS240jnBOeeXVGHT9QgKS4kIQ3wWVKzOYkfjdsjfkjdsaf+sddfwwfw+sdffsdafda/w==" />

<!-- or use the local development machine for storage
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
-->
```

## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre o Application Insights com o Serviços de Nuvem.](../application-insights/app-insights-cloudservices.md)

