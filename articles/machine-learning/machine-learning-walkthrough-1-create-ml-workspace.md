---
title: "Etapa 1: Criar um Espaço de Trabalho do Machine Learning | Microsoft Docs"
description: "Etapa 1 - desenvolver um passo a passo de solução de previsão: Saiba como configurar um novo espaço de trabalho do Estúdio de Aprendizado de Máquina do Azure."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: b3c97e3d-16ba-4e42-9657-2562854a1e04
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 8ca42ef8f5314866301f5c9e93caa90dc837a66e
ms.lasthandoff: 03/25/2017


---
# <a name="walkthrough-step-1-create-a-machine-learning-workspace"></a>Etapa 1 do passo a passo: Criar um espaço de trabalho de Aprendizado de Máquina
Esta é a primeira etapa do passo a passo, [Desenvolver uma solução de análise preditiva com o Aprendizado de Máquina do Azure](machine-learning-walkthrough-develop-predictive-solution.md).

1. **Criar um espaço de trabalho do Aprendizado de Máquina**
2. [Carregar dados existentes](machine-learning-walkthrough-2-upload-data.md)
3. [Criar um novo teste](machine-learning-walkthrough-3-create-new-experiment.md)
4. [Treinar e avaliar os modelos](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5. [Implantar o serviço Web](machine-learning-walkthrough-5-publish-web-service.md)
6. [Acessar o serviço Web](machine-learning-walkthrough-6-access-web-service.md)

- - -
<!-- This needs to be updated to refer to the new way of creating workspaces in the Ibiza portal -->

Para usar o Estúdio de Aprendizado de Máquina, você precisa ter um espaço de trabalho do Aprendizado de Máquina do Microsoft Azure. Esse espaço de trabalho contém as ferramentas necessárias para criar, gerenciar e publicar testes.  

<!--
## To create a workspace
1. Sign in to the [Azure classic portal](https://manage.windowsazure.com).
2. In the  Azure services panel, click **MACHINE LEARNING**.  
   ![Create workspace][1]
3. Click **CREATE AN ML WORKSPACE**.
4. On the **QUICK CREATE** page, enter your workspace information and then click **CREATE AN ML WORKSPACE**.
-->

O administrador de sua assinatura do Azure precisa criar o espaço de trabalho e, em seguida, adicioná-lo como um proprietário ou colaborador. Para obter mais detalhes, consulte [Criar e compartilhar um espaço de trabalho do Azure Machine Learning](machine-learning-create-workspace.md).

Após a criação do espaço de trabalho, abra o Machine Learning Studio:[https://studio.azureml.net/Home](https://studio.azureml.net/Home). Se você tiver mais de um espaço de trabalho, poderá selecionar o espaço de trabalho na barra de ferramentas no canto superior direito da janela.

![Selecionar o espaço de trabalho no estúdio][2]

> [!TIP]
> Se você tiver sido adicionado como proprietário do espaço de trabalho, será possível compartilhar os testes em que está trabalhando convidando outras pessoas para seu espaço de trabalho. Pode fazer isso no Estúdio de Aprendizado de Máquina na página **CONFIGURAÇÕES** . Basta ter a conta da Microsoft ou a conta da empresa de cada usuário.
> 
> Na página **CONFIGURAÇÕES**, clique em **USUÁRIOS** e, em seguida, clique em **CONVIDAR MAIS USUÁRIOS** na parte inferior da janela.
> 
> 

- - -
**Em seguida:[ Carregar dados existentes](machine-learning-walkthrough-2-upload-data.md)**

[1]: ./media/machine-learning-walkthrough-1-create-ml-workspace/create1.png
[2]: ./media/machine-learning-walkthrough-1-create-ml-workspace/open-workspace.png

