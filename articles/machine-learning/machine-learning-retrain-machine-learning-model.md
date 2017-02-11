---
title: Readaptar um modelo do Machine Learning | Microsoft Docs
description: "Aprenda como readaptar um modelo e atualizar o serviço Web para usar o modelo recentemente adaptado no Machine Learning do Azure."
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: raymondl
editor: 
ms.assetid: d1cb6088-4f7c-4c32-94f2-f7523dad9059
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: v-donglo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f897a155d277e41695f5f52b22ab0c512413e5d8


---
# <a name="retrain-a-machine-learning-model"></a>Readaptar um modelo do Machine Learning
Como parte do processo de operacionalização de modelos de aprendizado de máquina no Aprendizado de Máquina do Azure, um modelo deve ser treinado e salvo. Em seguida, use-o para criar um serviço Web predicativo. O serviço Web pode ser consumido nos sites, painéis e aplicativos móveis. 

Os modelos que você cria usando o Aprendizado de Máquina geralmente não são estáticos. Conforme novos dados ficam disponíveis ou quando o consumidor da API tem seus próprios dados, o modelo precisa ser readaptado. 

A readaptação pode ocorrer com frequência. Com o recurso da API de Readaptação Programática, você pode recuperar programaticamente o modelo usando as APIs de Readaptação e atualizar o Serviço Web com o modelo treinado recentemente. 

Este documento descreve o processo de readaptação e mostra como usar as APIs de Readaptação.

## <a name="why-retrain-defining-the-problem"></a>Por que readaptar: definindo o problema
Como parte do processo de treinamento do Machine Learning, um modelo é treinado usando um conjunto de dados. Os modelos que você cria usando o Aprendizado de Máquina geralmente não são estáticos. Conforme novos dados ficam disponíveis ou quando o consumidor da API tem seus próprios dados, o modelo precisa ser readaptado.

Nesses cenários, uma API de programação fornece uma maneira conveniente de permitir que você ou o consumidor de suas APIs criem um cliente que pode, em uma base única ou regular, readaptar o modelo usando seus próprios dados. Em seguida, podem avaliar os resultados da readaptação e atualizar a API do serviço Web para usar o modelo readaptado recentemente.

> [!NOTE]
> Se você tiver um Teste de Treinamento e um Novo serviço Web, talvez seja conveniente conferir Readaptar um serviço Web Preditivo existente em vez de seguir o passo a passo mencionado na seção a seguir.
> 
> 

## <a name="end-to-end-workflow"></a>Fluxos de trabalho completos
O processo envolve os seguintes componentes: um Teste de Treinamento e um Teste Preditivo publicados como um serviço Web. Para habilitar a readaptação de um modelo treinado, o Teste de Treinamento também deve ser publicado como um serviço Web com a saída de um modelo treinado. Isso permite o acesso de API para o modelo de readaptação. 

As etapas a seguir se aplicam aos serviços Web Novo e Clássico:

Crie o serviço Web Preditivo inicial:

* Criar um teste de treinamento
* Criar um teste da Web preditivo
* Implantar um serviço Web preditivo

Readapte o serviço Web:

* Atualizar teste de treinamento para permitir a readaptação
* Implantar o serviço Web de readaptação
* Usar o código Serviço de Execução em Lotes para readaptar o modelo

Para ver uma passo a passo das etapas anteriores, confira [Readaptar os modelos do Machine Learning de forma programática](machine-learning-retrain-models-programmatically.md).

Se você implantou um Serviço Web Clássico:

* Criar um novo ponto de extremidade no serviço Web Preditivo
* Obter a URL de PATCH e o código
* Usar a URL de PATCH para apontar o novo ponto de extremidade no modele readaptado 

Para ver um passo a passo das etapas anteriores, confira [Readaptar um serviço Web Clássico](machine-learning-retrain-a-classic-web-service.md).

Se estiver enfrentando dificuldades ao readaptar um serviço Web Clássico, confira [Solução de problemas de readaptação de um serviço Web Clássico do Machine Learning do Azure](machine-learning-troubleshooting-retraining-models.md).

Se você tiver implantado um Novo serviço Web:

* Entrar em sua conta do Azure Resource Manager
* Obter a definição do serviço Web
* Exportar a Definição do Serviço Web como JSON
* Atualizar a referência para o blob `ilearner` no JSON
* Importar o JSON para uma Definição do Serviço Web
* Atualizar o serviço Web com a nova Definição do Serviço Web

Para ver um passo a passo das etapas anteriores, confira [Readaptar um Novo serviço Web usando os cmdlets do PowerShell de Gerenciamento do Machine Learning](machine-learning-retrain-new-web-service-using-powershell.md).

O processo para configurar a readaptação de um serviço Web Clássico envolve as seguintes etapas:

![Visão geral do processo readaptação][1]

O processo para configurar a readaptação de um Novo serviço Web envolve as seguintes etapas:

![Visão geral do processo readaptação][7]

## <a name="other-resources"></a>Outros recursos
* [Retraining and Updating Azure Machine Learning models with Azure Data Factory](https://azure.microsoft.com/blog/retraining-and-updating-azure-machine-learning-models-with-azure-data-factory/) (Readaptando e atualizando modelos do Machine Learning com o Azure Data Factory)
* [Criar vários modelos do Machine Learning e pontos de extremidade de serviço Web com base em um teste usando o PowerShell](machine-learning-create-models-and-endpoints-with-powershell.md)
* O vídeo [AML Retraining Models Using APIs](https://www.youtube.com/watch?v=wwjglA8xllg) (Modelos de readaptação do AML usando APIs) mostra como readaptar modelos do Machine Learning criados no Machine Learning do Azure usando as APIs de readaptação e o PowerShell.

<!--image links-->
[1]: ./media/machine-learning-retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE01.png
[7]: ./media/machine-learning-retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE07.png




<!--HONumber=Nov16_HO3-->


