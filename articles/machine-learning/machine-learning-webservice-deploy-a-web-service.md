---
title: "Implantar um novo serviço Web no Azure Machine Learning | Microsoft Docs"
description: "O fluxo de trabalho de implantar um serviço Web baseado em ARM"
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: raymondl
editor: 
ms.assetid: a358b04f-0d08-4d50-820e-eeac971854cf
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: v-donglo
ROBOTS: NOINDEX, NOFOLLOW
redirect_url: machine-learning-publish-a-machine-learning-web-service
translationtype: Human Translation
ms.sourcegitcommit: 0813611f581a68efb8f09a1e041cfbe429bf0c5c
ms.openlocfilehash: 902be47eb59444a1214b096be10525743f406d1c


---
# <a name="deploy-a-new-web-service"></a>Implantar um novo serviço Web
O Microsoft Azure Machine Learning fornece serviços Web baseados no [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) permitindo novas opções do plano de faturamento e implantando seu serviço Web em várias regiões.

O fluxo de trabalho geral para implantar um serviço Web usando serviços Web do Aprendizado de Máquina do Microsoft Azure é:

* Criar um teste preditivo
* implantá-lo
* configurar seu nome
* Plano de Faturamento
* testá-lo
* consumi-lo.

O gráfico a seguir ilustra esse fluxo de trabalho.

![Fluxo de trabalho de implantação de serviço Web][1]

## <a name="deploy-web-service-from-studio"></a>Implantar serviço Web do Studio
Para implantar um teste como um novo serviço Web. Entre no Estúdio de Aprendizado de Máquina e crie um novo serviço Web preditivo. 

**Observação**: se você já implantou um teste como um serviço Web clássico, não poderá implantá-lo como um novo serviço Web.

Clique em **Executar** na parte inferior da tela do teste e clique em **Implantar Serviço Web** e em **Implantar Serviço Web [Novo]**. A página de implantação do gerente do serviço Web de Aprendizado de Máquina será aberta.

## <a name="machine-learning-web-service-manager-deploy-experiment-page"></a>Página de teste de implantação do Gerente de Serviços Web de Aprendizado de Máquina
Na página de teste de implantação, insira um nome para o serviço Web.
Selecione um plano de preços. Se você tiver um plano de preços existente, selecione-o, caso contrário, você deverá criar um novo plano de preços para o serviço. 

1. Na lista suspensa **Plano de Preços**, selecione um plano existente ou selecione a opção **Selecione novo plano**.
2. Em **Nome do Plano**, digite um nome que identificará o plano na sua conta.
3. Selecione uma dos **Níveis de Planos Mensais**. Observe que os níveis de plano padrão são os planos para sua região padrão e o serviço web que está implantado para essa região.

Clique em **Implantar** e na página Início Rápido para abrir seu serviço Web.

## <a name="quickstart-page"></a>Página Início Rápido
A página de início rápido do serviço Web fornece acesso e orientações sobre as tarefas mais comuns que você executará depois de criar um novo serviço Web. A partir daqui, você poderá acessar facilmente a página **Teste** e a página **Consumo**.

## <a name="testing-your-web-service"></a>Testando seu serviço Web
Na página Início Rápido, clique em Testar serviço Web em tarefas comuns.   

Para testar o serviço Web como um Serviço de solicitação-resposta (RRS):

* Clique em **Teste** na barra de menus.
* Clique em **Solicitação-Resposta**.
* Insira os valores apropriados para as colunas de entrada do seu teste.
* Clique em **Solicitação-Resposta**.

Seus resultados serão exibidos no lado direito da página.

Para testar um serviço Web de Serviço de execução em lote (BES), você usará um arquivo CSV:

* Clique em **Teste** na barra de menus.
* Clique em **Lote**.
* Na sua entrada, clique em Pesquisar e navegue até o arquivo de dados de exemplo.
* Clique em **Testar**.

O status do teste é exibido em **Trabalhos em lote do teste**.

## <a name="consuming-your-web-service"></a>Consumindo seu serviço Web
Quando implantado como um serviço Web, os testes de Aprendizado de Máquina do Azure fornecem uma API REST que pode ser consumida por uma ampla variedade de dispositivos e plataformas. Isso ocorre porque a API REST simples aceita e responde com mensagens formatadas em JSON. O portal de Aprendizado de Máquina do Azure fornece código que pode ser usado para chamar o serviço Web em R, C# e Python.

Na página Consumo, você pode encontrar:

* A chave de API e o URI para o consumo de serviço Web em aplicativos.
* Modelos de aplicativo Web e do Excel para iniciar seu processo de consumo.
* Exemplo de código em C#, Python e R para começar.

Para saber mais sobre serviços Web de consumo, veja [Como consumir um serviço Web do Aprendizado de Máquina do Azure implantado de um teste do Aprendizado de Máquina](machine-learning-consume-web-services.md).

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre o consumo dos serviços Web, consulte:

[Como consumir um serviço Web de Aprendizado de Máquina do Azure que foi implantado por meio de um teste de Aprendizado de Máquina](machine-learning-consume-web-services.md)

[Serviços Web do Azure Machine Learning: implantação e consumo](machine-learning-deploy-consume-web-service-guide.md)

<!--Image references-->
[1]: ./media/machine-learning-webservice-deploy-a-web-service/armdeploymentworkflow.png


<!--links-->



<!--HONumber=Jan17_HO4-->


