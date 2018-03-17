---
title: "Neste tutorial, você cria uma oferta de pilha do Azure | Microsoft Docs"
description: Saiba como criar uma oferta de pilha do Azure, incluindo cotas e planos.
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 083b5e20b89f22cb8e523926858fe9ffb1441319
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-offer-azure-stack-iaas-services"></a>Tutorial: oferecem serviços de IaaS de pilha do Azure
Como um administrador de nuvem de pilha do Azure, você pode criar ofertas que seus usuários (às vezes chamados de locatários) podem se inscrever. Usando sua assinatura, os usuários podem consumir serviços da pilha do Azure.

Este tutorial mostra como criar uma oferta para permitir aos usuários criar máquinas virtuais com base na imagem do Datacenter do Windows Server 2016 marketplace do Azure pilha carregados no [tutorial anterior](asdk-marketplace-item.md).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma oferta
> * Criar um plano
> * Definir cotas
> * Oferta de conjunto para o público

Na pilha do Azure, os serviços são fornecidos para usuários que usam assinaturas, ofertas e planos. Os usuários podem assinar várias ofertas. Ofertas podem ter um ou mais planos e planos podem ter um ou mais serviços, conforme mostrado no diagrama a seguir:

![Planos, ofertas e assinaturas](media/asdk-offer-services/sop.png)

Como um operador de pilha do Azure oferece serviços, você precisará primeiro criar a oferta, um plano e, finalmente, as cotas. Depois que uma oferta foi criada, os usuários de pilha do Azure, em seguida, podem se inscrever para ofertas através do portal do usuário.

## <a name="create-an-offer"></a>Criar uma oferta
**Oferece** são grupos de um ou mais planos que os operadores de pilha do Azure apresentam aos usuários para comprar ou assinar.

1. Entrar para o [portal do Azure pilha](https://adminportal.local.azurestack.external) como um administrador de nuvem.

2. Clique em **novo** > **oferece + planos** > **oferecem**.

   ![Nova oferta](media/asdk-offer-services/new-offer.png)

2. No **oferecem nova** seção, preencha **nome de exibição** e **nome do recurso**e, em seguida, selecione um novo ou existente **grupo de recursos**. O nome de exibição é público nome amigável a oferta que os usuários veem. Somente o operador de nuvem pode ver o nome do recurso, que é usado por administradores para trabalhar com a oferta como um recurso do Gerenciador de recursos do Azure.

   ![Nome de exibição](media/asdk-offer-services/offer-display-name.png)


## <a name="create-a-plan"></a>Criar um plano
Depois de inserir as informações básicas de oferta, você deve adicionar pelo menos um plano de base para a oferta. 

**Planos de** permitem que os operadores de pilha do Azure para serviços de grupo e as cotas associadas, a ser oferecidos aos usuários.

1. Clique em **Base planos**e no **plano** seção, clique em **adicionar** para adicionar um novo plano para a oferta.

   ![Adicionar um plano](media/asdk-offer-services/new-plan.png)

2. No **novo plano** seção, preencha **nome de exibição** e **nome do recurso**. O nome de exibição é o nome amigável do plano do que os usuários veem. Somente o operador de nuvem pode ver o nome do recurso usado por operadores de nuvem para trabalhar com o plano como um recurso do Gerenciador de recursos do Azure.

   ![Nome de exibição do plano](media/asdk-offer-services/plan-display-name.png)

3. Em seguida, selecione os serviços a serem incluídos no plano. Para oferecer serviços de IaaS, clique em **serviços**, selecione **Microsoft. Compute**, **Network**, e **Microsoft**e, em seguida, Clique em **selecione**.

   ![Serviços do plano](media/asdk-offer-services/select-services.png)


## <a name="set-quotas"></a>Definir cotas
Agora que a oferta tem um plano que inclui serviços, você deve definir cotas para cada um deles. 

**Cotas** determinar a quantidade de recursos que um usuário pode consumir para cada serviço incluído no plano que está sendo oferecido.

1. Clique em **cotas**e, em seguida, selecione o serviço para o qual você deseja criar uma cota. 

   Para começar, crie primeiro uma cota para o serviço de computação. Na lista de namespace, selecione o **Microsoft. Compute** namespace e, em seguida, clique em **criar nova cota**.
   
   ![Criar nova cota](media/asdk-offer-services/create-quota.png)

2. Sobre o **criar cota** seção, digite um nome para a cota, defina os parâmetros desejados para a cota e clique em **Okey**.

   ![Nome da cota](media/asdk-offer-services/quota-properties.png)

3. Selecione a cota que você criou para o **Microsoft. Compute** serviço.

   ![Selecione cota](media/asdk-offer-services/set-quota.png)

4. Repita as etapas 1 a 3 para definir cotas para os serviços de rede e armazenamento e, em seguida, clique em Okey na seção de cotas. Em seguida, clique em **Okey** no **novo plano** seção para concluir a configuração do plano. 

   ![Definem todas as cotas](media/asdk-offer-services/all-quotas-set.png)

5. Concluir a criação da oferta clicando **criar** na seção de plano. Você verá uma notificação quando a oferta é criada com êxito e ele será listado como uma oferta disponível.

   ![Oferta criada](media/asdk-offer-services/offer-complete.png)

## <a name="set-offer-to-public"></a>Oferta de conjunto para o público
Ofertas devem ser feitas públicas para os usuários para vê-los ao selecionar uma oferta para se inscrever. 

Ofertas podem ser:
- **Público**: visível para todos os usuários.
- **Privada**: só é visível para os administradores de nuvem. Útil ao projeto o plano ou a oferta, ou se desejar que o administrador da nuvem criar cada assinatura para os usuários.
- **Desativados**: fechados para novos assinantes. O administrador de nuvem pode usar encerrado para evitar futuras assinaturas, mas deixe os assinantes atuais inalterados.

> [!TIP]
> As alterações para a oferta não ficam imediatamente visíveis para os usuários. Para ver as alterações, os usuários podem ter para logout e login novamente para o [portal do usuário](https://portal.local.azurestack.external) para ver a nova oferta.

Para definir a nova oferta para público: 

1. No menu do painel, clique em **oferece** e, em seguida, clique na oferta que você criou.

2. Clique em **alterar estado**e, em seguida, clique em **público**.

   ![Estado público](media/asdk-offer-services/set-public.png)

3. A oferta estarão disponível no portal do usuário de pilha do Azure.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar uma oferta
> * Criar um plano
> * Definir cotas
> * Oferta de conjunto para o público

Avança para o próximo tutorial para aprender como inscrever-se para a oferta que você acabou de criar como um usuário de pilha do Azure.

> [!div class="nextstepaction"]
> [Assinar uma oferta](asdk-subscribe-services.md)