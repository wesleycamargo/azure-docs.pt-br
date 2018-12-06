---
title: O que há de novo no Azure Machine Learning Studio? | Microsoft Docs
description: Novos recursos disponíveis no Microsoft Azure Machine Learning Studio.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=yahajiza, author=YasinMSFT)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: ddc716ed-2615-4806-bf27-6c9a5662a7f2
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/28/2018
ms.openlocfilehash: c93bb9a2fa7991df265b4767585d920c137a436d
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2018
ms.locfileid: "52311136"
---
# <a name="whats-new-in-azure-machine-learning-studio"></a>O que há de novo no Azure Machine Learning Studio?

## <a name="october-2018"></a>Outubro de 2018

O mecanismo da linguagem R no módulo [Executar script R](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-r-script) adicionou uma nova versão de tempo de execução de R – MRO (Microsoft R Open) 3.4.4. O MRO 3.4.4 tem base no software livre CRAN R 3.4.4 e, portanto, é compatível com pacotes que funcionam com essa versão do R. Saiba mais sobre os pacotes de R com suporte neste artigo, "[Pacotes de R com suporte do Azure Machine Learning Studio](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/r-packages-supported-by-azure-machine-learning#bkmk_List)".

## <a name="march-2017"></a>Março de 2017 
Esta versão das atualizações do Microsoft Azure Machine Learning fornece o seguinte recurso:

* Capacidade dedicada para trabalhos BES do Machine Learning

    O processamento em Pool do Lote do Machine Learning usa o [serviço de Lote do Azure](../../batch/batch-technical-overview.md) para fornecer escala gerenciada pelo cliente para o Serviço de Execução em Lotes do Machine Learning do Azure. O processamento em Pool do Lote permite criar pools de Lote do Azure nos quais é possível enviar trabalhos em lotes e tê-los executados de maneira previsível.

    Para saber mais, confira [serviço de Lote do Azure para trabalhos do Machine Learning](dedicated-capacity-for-bes-jobs.md).


## <a name="august-2016"></a>Agosto de 2016 
Esta versão do Microsoft Azure Machine Learning fornece os seguintes recursos:
* Os serviços da Web clássicos agora podem ser gerenciados no novo portal dos [Serviços Web do Microsoft Azure Machine Learning](https://services.azureml.net/), que fornece um local para gerenciar todos os aspectos do serviço da Web.    
  * Que fornece [estatísticas de uso](manage-new-webservice.md)do serviço da Web.
  * Simplifica o teste de chamadas de solicitação remotas do Azure Machine Learning usando dados de exemplo.
  * Fornece uma nova página de teste do Serviço de Execução em Lote com dados de exemplo e histórico de envio de trabalhos.
  * Fornece gerenciamento do ponto de extremidade simplificado.

## <a name="july-2016"></a>Julho de 2016 
Esta versão do Microsoft Azure Machine Learning fornece os seguintes recursos:
* Os serviços da Web agora são gerenciados como recursos do Azure gerenciados por meio das interfaces do [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) , permitindo os seguintes aprimoramentos:
  * Há novas [APIs REST](https://msdn.microsoft.com/library/azure/Dn950030.aspx) para implantar e gerenciar seus serviços da Web baseados no Resource Manager.
  * Há um novo portal dos [Serviços Web do Microsoft Azure Machine Learning](https://services.azureml.net/) que fornece um local para gerenciar todos os aspectos do serviço da Web.
* Incorpora um novo modelo de implantação de serviço da Web de várias regiões, baseado em assinatura, usando APIs baseadas no Resource Manager que utilizam o Provedor de Recursos do Resource Manager para Serviços da Web.
* Introduz novos [planos de preços](https://azure.microsoft.com/pricing/details/machine-learning/) e recursos de gerenciamento de planos usando o novo RP do Resource Manager para Cobranças.
  * Agora você pode [implantar seu serviço da Web em várias regiões](how-to-deploy-to-multiple-regions.md) sem a necessidade de criar uma assinatura para cada região.
* Fornece [estatísticas de uso](manage-new-webservice.md)do serviço da Web.
* Simplifica o teste de chamadas de solicitação remotas do Azure Machine Learning usando dados de exemplo.
* Fornece uma nova página de teste do Serviço de Execução em Lote com dados de exemplo e histórico de envio de trabalhos.

Além disso, o Machine Learning Studio foi atualizado para permitir que você implante o novo modelo de serviço da Web ou continue a implantar o modelo de serviço da Web clássico. 

