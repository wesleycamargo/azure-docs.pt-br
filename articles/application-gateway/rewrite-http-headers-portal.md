---
title: Reescreva cabeçalhos de solicitação e resposta HTTP com o Gateway de aplicativo do Azure - portal do Azure | Microsoft Docs
description: Saiba como usar o portal do Azure para configurar um Gateway de aplicativo do Azure para reconfigurar os cabeçalhos HTTP em solicitações e respostas, passando por meio do gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/10/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: e144214a58f9fe383cf4edd878554792d9d6a6f9
ms.sourcegitcommit: ed66a704d8e2990df8aa160921b9b69d65c1d887
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64947157"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-portal"></a>Reescreva cabeçalhos de solicitação e resposta HTTP com o Gateway de aplicativo do Azure - portal do Azure

Este artigo descreve como usar o portal do Azure para configurar uma [SKU do Gateway de aplicativo v2](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) instância reescreva os cabeçalhos HTTP em solicitações e respostas.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="before-you-begin"></a>Antes de começar

Você precisa ter uma instância SKU do Gateway de aplicativo v2 para concluir as etapas neste artigo. Regravação de cabeçalhos não tem suporte no SKU do v1. Se você não tiver o SKU do v2, crie uma [SKU do Gateway de aplicativo v2](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) instância antes de começar.

## <a name="create-required-objects"></a>Criar objetos necessários

Para configurar a reconfiguração do cabeçalho HTTP, você precisa concluir estas etapas.

1. Crie os objetos que são necessários para a reconfiguração do cabeçalho HTTP:

   - **Reescreva a ação**: Usado para especificar a solicitação e campos de cabeçalho de solicitação que você pretende reescrever e o novo valor para os cabeçalhos. Você pode associar um ou mais condições com uma ação de regravação de reconfiguração.

   - **Reescreva a condição**: Uma configuração opcional. Condições de regravação avaliar o conteúdo do HTTP (S) de solicitações e respostas. A ação de regravação ocorrerá se a resposta ou solicitação de HTTP (S) corresponde à condição de regravação.

     Se você associar mais de uma condição com uma ação, a ação ocorre somente quando todas as condições são atendidas. Em outras palavras, a operação é uma operação AND lógica.

   - **Regra de reescrita de**: Contém várias ação de regravação / reescrever combinações de condição.

   - **Sequência de regra**: Ajuda a determinar a ordem na qual executar regras de regravação. Essa configuração é útil quando você tiver várias regras de reescrita em um conjunto de regravação. Uma regra de regravação que tem um valor de sequência de regra mais baixo será executado primeira. Se você atribuir o mesmo valor de sequência de regra a duas regras de reescrita, a ordem de execução é não determinística.

   - **Reescreva o conjunto**: Contém várias regras de reconfiguração que serão associadas uma regra de roteamento de solicitação.

2. Anexe a reescrita definida como uma regra de roteamento. A configuração de regravação é anexada ao ouvinte de origem por meio da regra de roteamento. Quando você usa uma regra de roteamento básica, a configuração de regravação de cabeçalho está associada um ouvinte de origem e é uma reformulação de cabeçalho global. Quando você usa uma regra de roteamento com base no caminho, a configuração de regravação de cabeçalho é definida no mapa de caminho de URL. Nesse caso, ele se aplica somente à área de caminho específico de um site.

Você pode criar vários conjuntos de reconfiguração do cabeçalho HTTP e aplicar cada reescrita definida para vários ouvintes. Mas você pode aplicar somente um reescrever definido como um ouvinte específico.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com/) com sua conta do Azure.

## <a name="configure-header-rewrite"></a>Configurar a reconfiguração de cabeçalho

Neste exemplo, modificaremos a uma URL de redirecionamento reescrevendo o cabeçalho location na resposta HTTP enviada por um aplicativo de back-end.

1. Selecione **todos os recursos**e, em seguida, selecione seu gateway de aplicativo.

2. Selecione **regrava** no painel esquerdo.

3. Selecione **reescreva conjunto**:

   ![Adicionar conjunto de regravação](media/rewrite-http-headers-portal/add-rewrite-set.png)

4. Forneça um nome para o conjunto de regravação e associá-lo a uma regra de roteamento:

   - Insira o nome para a reescrita definido **nome** caixa.
   - Selecione um ou mais das regras listadas na **associados a regras de roteamento** lista. Você pode selecionar apenas as regras que ainda não foram associadas a outros conjuntos de regravação. As regras que já foram associadas a outros conjuntos de regravação estão esmaecidas.
   - Selecione **Avançar**.
   
     ![Adicionar nome e a associação](media/rewrite-http-headers-portal/name-and-association.png)

5. Crie uma regra de regravação:

   - Selecione **Adicionar regra de regravação**.

     ![Adicionar regra de regravação](media/rewrite-http-headers-portal/add-rewrite-rule.png)

   - Insira um nome para a regra de reescrita na **nome da regra de regravação** caixa. Insira um número na **sequência de regra** caixa.

     ![Adicione o nome da regra de regravação](media/rewrite-http-headers-portal/rule-name.png)

6. Neste exemplo, podemos vai reescreva o cabeçalho location apenas quando ele contém uma referência ao azurewebsites.net. Para fazer isso, adicione uma condição para avaliar se o cabeçalho location na resposta contém azurewebsites.net:

   - Selecione **Adicionar condição** e, em seguida, selecione a caixa que contém o **se** instruções para expandi-lo.

     ![Adicione uma condição](media/rewrite-http-headers-portal/add-condition.png)

   - No **tipo de variável para verificar** lista, selecione **cabeçalho HTTP**.

   - No **tipo de cabeçalho** lista, selecione **resposta**.

   - Como neste exemplo estamos avaliando o cabeçalho location, que é um cabeçalho comuns, selecione **cabeçalho Common** sob **nome do cabeçalho**.

   - No **cabeçalho Common** lista, selecione **local**.

   - Sob **diferencia maiusculas de minúsculas**, selecione **não**.

   - No **operador** lista, selecione **igual (=)**.

   - Insira uma expressão regular padrão. Neste exemplo, vamos usar o padrão de `(https?):\/\/.*azurewebsites\.net(.*)$`.

   - Selecione **OK**.

     ![Configurar um se condição](media/rewrite-http-headers-portal/condition.png)

7. Adicione uma ação para reescrever o cabeçalho de localização:

   - No **tipo de ação** lista, selecione **definir**.

   - No **tipo de cabeçalho** lista, selecione **resposta**.

   - Sob **nome do cabeçalho**, selecione **cabeçalho comuns**.

   - No **cabeçalho Common** lista, selecione **local**.

   - Insira o valor do cabeçalho. Neste exemplo, vamos usar `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` como o valor do cabeçalho. Esse valor substituirá *azurewebsites.net* com *contoso.com* no cabeçalho location.

   - Selecione **OK**.

     ![Adicionar uma ação](media/rewrite-http-headers-portal/action.png)

8. Selecione **criar** para criar a reescrita de conjunto:

   ![Selecione Criar](media/rewrite-http-headers-portal/create.png)

9. O modo de exibição de conjunto de regravação será aberta. Verifique se o conjunto de reconfiguração, que você criou está na lista de conjuntos de reconfiguração:

   ![Reescreva o modo de exibição de conjunto](media/rewrite-http-headers-portal/rewrite-set-list.png)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como configurar alguns casos de uso comuns, consulte [cenários de reconfiguração de cabeçalho comum](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).