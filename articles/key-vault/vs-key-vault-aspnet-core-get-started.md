---
title: Introdução ao Serviço Conectado do Key Vault no Visual Studio (projetos ASP.NET Core) | Microsoft Docs
description: Use este tutorial para aprender a adicionar suporte do Key Vault para um aplicativo Web ASP.NET ou ASP.NET Core.
services: key-vault
author: ghogen
manager: douge
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: e591771ee9c9cb12d9ec2ff61ec7f5a76691c8c7
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/18/2018
ms.locfileid: "42140199"
---
# <a name="get-started-with-key-vault-connected-service-in-visual-studio-aspnet-core-projects"></a>Introdução ao Serviço Conectado do Key Vault no Visual Studio (projetos ASP.NET Core)

> [!div class="op_single_selector"]
> - [Introdução](vs-key-vault-aspnet-core-get-started.md)
> - [O que aconteceu](vs-key-vault-aspnet-core-what-happened.md)

Este artigo fornece orientações adicionais depois de você ter adicionado o Key Vault a um projeto do ASP.NET por meio do comando **Adicionar Serviços Conectados** do Visual Studio. Se ainda não tiver adicionado o serviço ao seu projeto, faça isso a qualquer momento seguindo as instruções em [Adicionar Key Vault ao seu aplicativo Web usando os Serviços Conectados do Visual Studio](vs-key-vault-add-connected-service.md).

Consulte [O que aconteceu com meu projeto do ASP.NET Core?](vs-key-vault-aspnet-core-what-happened.md) para as alterações feitas ao seu projeto ao adicionar o serviço conectado.

## <a name="after-you-connect"></a>Depois de se conectar

1. Adicione um segredo ao seu Key Vault no Azure. Para chegar ao local correto no portal, clique no link para Gerenciar segredos armazenados neste Key Vault. Se você fechou a página ou o projeto, poderá navegar no [Portal do Azure](https://portal.azure.com) escolhendo **Todos os serviços**. Em **Segurança**, escolha **Key Vault** e, em seguida, selecione o Key Vault que você acabou de criar.

   ![Navegar para o portal](media/vs-key-vault-add-connected-service/manage-secrets-link.jpg)

1. Na seção do Key Vault para o cofre de chaves que você criou, selecione **Segredos** e, em seguida, **Gerar/importar**.

   ![Gerar/Importar um segredo](media/vs-key-vault-add-connected-service/generate-secrets.jpg)

1. Digite um segredo, como "MySecret", e dê a ele qualquer valor de cadeia de caracteres como um teste e clique no botão **Criar**.

   ![Criar um segredo](media/vs-key-vault-add-connected-service/create-a-secret.jpg)
 
1. (opcional) Insira outro segredo, mas, desta vez, coloque-o em uma categoria nomeando-o como **Segredos--MySecret**. Esta sintaxe especifica uma categoria **Segredos** que contém um segredo **MySecret**.
1. Agora, em seu projeto do Visual Studio, você pode referenciar esses segredos usando as seguintes expressões no código:
 
   ```csharp
      config["MySecret"] // Access a secret without a section
      config["Secrets:MySecret"] // Access a secret in a section
      config.GetSection("Secrets")["MySecret"] // Get the configuration section and access a secret in it.
   ```

Parabéns! Você confirmou que seu aplicativo Web pode usar o Key Vault para acessar os segredos armazenados com segurança.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, exclua o grupo de recursos. Isso exclui o Key Vault e os recursos relacionados. Para excluir o grupo de recursos pelo portal:

1. Insira o nome do grupo de recursos na caixa de pesquisa na parte superior do portal. Quando você vir o grupo de recursos usado neste início rápido nos resultados da pesquisa, selecione-o.
2. Selecione **Excluir grupo de recursos**.
3. Na caixa **DIGITE O NOME DO GRUPO DE RECURSOS:**, digite o nome do grupo de recursos e selecione **Excluir**.

# <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o desenvolvimento com Key Vault no [Guia de Desenvolvedor do Key Vault](key-vault-developers-guide.md)