---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: azure-app-configuration
author: yegu
ms.service: azure-app-configuration
ms.topic: include
ms.date: 01/22/2019
ms.author: yegu
ms.custom: include file
ms.openlocfilehash: 71e63de247e05a4712687354ed548219b36e8f2f
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56885118"
---
1. Para criar um repositório de configurações de aplicativo, primeiro entre no [portal do Azure](https://aka.ms/azconfig/portal). No canto superior esquerdo da página, clique em **+ Criar um recurso**. Na caixa de texto **Pesquisar no Marketplace**, digite **Configuração de Aplicativo** e pressione **Enter**.

    ![Pesquisar a Configuração de Aplicativo](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-new.png)

2. Clique em **Configuração de Aplicativo** nos resultados da pesquisa e, em seguida, em **Criar**.

3. Na página **Configuração de Aplicativo** > **Criar**, insira as seguintes configurações:

    | Configuração | Valor sugerido | DESCRIÇÃO |
    |---|---|---|
    | **Nome do recurso** | Nome globalmente exclusivo | Insira um nome exclusivo do recurso a ser usado para o recurso do repositório de configurações de aplicativo. O nome deve ser uma cadeia de caracteres com 1 a 63 caracteres e deve conter somente números, letras e o caractere `-`. O nome não pode começar nem terminar com o caractere `-` e os caracteres `-` consecutivos não são válidos.  |
    | **Assinatura** | Sua assinatura | Selecione a assinatura do Azure que deseja usar para testar a Configuração de Aplicativo. Se sua conta tiver apenas uma assinatura, ela será automaticamente selecionada e o menu suspenso **Assinatura** não será exibido. |
    | **Grupo de recursos** | *AppConfigTestResources* | Selecione ou crie um grupo de recursos para o recurso do repositório de configurações de aplicativo. Esse grupo é útil para organizar vários recursos que você talvez queira excluir ao mesmo tempo, excluindo o grupo de recursos. Para obter mais informações, consulte [Usando os grupos de recursos para gerenciar seus recursos do Azure](/azure/azure-resource-manager/resource-group-overview). |
    | **Localidade** | *Centro dos EUA* | Use **Localização** para especificar a localização geográfica na qual o repositório de configurações de aplicativo está hospedado. Para obter o melhor desempenho, recomendamos que você crie o recurso na mesma região que outros componentes do seu aplicativo. |

    ![Criar um recurso do repositório de configurações de aplicativo](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-create.png)

4. Clique em **Criar**. A implantação pode levar alguns minutos para ser concluída.

5. Depois que a implantação for concluída, clique em **Configurações** > **Chaves de Acesso**. Anote a cadeia de conexão primária somente leitura ou a primária de leitura/gravação. Você usará isso posteriormente para configurar seu aplicativo para que ele se comunique com o repositório de configurações de aplicativo recém-criado.

6. Clique em **Gerenciador de Pares Chave-Valor** e em **+ Criar** para adicionar os seguintes pares chave-valor:

    | Chave | Valor |
    |---|---|
    | TestApp:Settings:BackgroundColor | white |
    | TestApp:Settings:FontSize | 24 |
    | TestApp:Settings:FontColor | black |
    | TestApp:Settings:Message | Dados da Configuração de Aplicativo do Azure |

    Você deixará **Rótulo** e **Tipo de Conteúdo** vazio por enquanto.
