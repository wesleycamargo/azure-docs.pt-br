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
ms.openlocfilehash: 0f066915046e363932652c104fabe96ed0a9d4c4
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58227177"
---
1. Para criar um novo repositório de configurações de aplicativo, entre no [portal do Azure](https://aka.ms/azconfig/portal). No canto superior esquerdo da página, selecione **+ Criar um recurso**. Na caixa **Pesquisar no Marketplace**, insira **Configuração de Aplicativo** e pressione Enter.

    ![Pesquisar a Configuração de Aplicativo](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-new.png)

2. Selecione **Configuração de Aplicativo** nos resultados da pesquisa e, em seguida, selecione **Criar**.

3. Na página **Configuração de Aplicativo** > **Criar**, insira as configurações a seguir.

    | Configuração | Valor sugerido | DESCRIÇÃO |
    |---|---|---|
    | **Nome do recurso** | Nome globalmente exclusivo | Insira um nome exclusivo do recurso a ser usado para o recurso do repositório de configurações de aplicativo. O nome deve ser uma cadeia de caracteres com 1 a 63 caracteres e deve conter somente números, letras e o caractere `-`. O nome não pode iniciar ou terminar com o caractere `-` e caracteres `-` consecutivos não são válidos.  |
    | **Assinatura** | Sua assinatura | Selecione a assinatura do Azure que deseja usar para testar a Configuração de Aplicativo. Se a conta tiver apenas uma assinatura, ela será selecionada automaticamente e a lista suspensa **Assinatura** não será exibida. |
    | **Grupo de recursos** | *AppConfigTestResources* | Selecione ou crie um grupo de recursos para o recurso do repositório de configurações de aplicativo. Esse grupo é útil para organizar vários recursos que você pode querer excluir ao mesmo tempo, excluindo o grupo de recursos. Para saber mais, veja [Usar os grupos de recursos para gerenciar seus recursos do Azure](/azure/azure-resource-manager/resource-group-overview). |
    | **Localidade** | *Centro dos EUA* | Use **Localização** para especificar a localização geográfica na qual o repositório de configurações de aplicativo está hospedado. Para obter o melhor desempenho, crie o recurso na mesma região que outros componentes do aplicativo. |

    ![Criar um recurso do repositório de configurações de aplicativo](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-create.png)

4. Selecione **Criar**. A implantação pode levar alguns minutos para ser concluída.

5. Após a conclusão da implantação, selecione **Configurações** > **Chaves de acesso**. Anote a cadeia de conexão de chave de leitura/gravação primária ou somente leitura primária. Você usará essa cadeia de conexão posteriormente para configurar o aplicativo a comunicar-se com o repositório de configurações do aplicativo que você criou.

6. Selecione **Explorador de Chave/Valor** > **+ Criar** para adicionar os pares chave-valor a seguir:

    | Chave | Valor |
    |---|---|
    | TestApp:Settings:BackgroundColor | Branco |
    | TestApp:Settings:FontSize | 24 |
    | TestApp:Settings:FontColor | Preto |
    | TestApp:Settings:Message | Dados da Configuração de Aplicativo do Azure |

    Deixe **Rótulo** e **Tipo de Conteúdo** vazios por enquanto.
