---
title: Azure Stack repositório de credenciais de serviço principal no cofre de chaves | Microsoft Docs
description: Saiba como o Key Vault armazena as credenciais da entidade de serviço no Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2019
ms.author: sethm
ms.openlocfilehash: 570c1adc2f4615e78cbe5656c13b0e22b863baf7
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54192363"
---
# <a name="store-service-principal-credentials-in-key-vault"></a>Store credenciais da entidade de serviço no cofre de chaves

Desenvolvimento de aplicativos no Azure Stack normalmente requer criação de um serviço de entidade e usar essas credenciais para autenticar antes de implantar. No entanto, muitas vezes as credenciais armazenadas para a entidade de serviço estão no local erradas. Este artigo descreve como criar um serviço de entidade e armazenar os valores no Azure Key Vault para recuperação posterior.

Para obter mais informações sobre o Key Vault, consulte [deste artigo](azure-stack-key-vault-intro.md).

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura para uma oferta que inclui o serviço do Azure Key Vault.
- PowerShell está configurado para uso com o Azure Stack.

## <a name="key-vault-in-azure-stack"></a>Cofre de chaves no Azure Stack

Cofre de chaves no Azure Stack ajuda a proteger chaves criptográficas e segredos que aplicativos e serviços de nuvem usam. Ao usar o Cofre de chaves, você pode criptografar chaves e segredos.

Para criar um cofre de chaves, siga estas etapas:

1. Entrar no portal do Azure Stack.

2. O painel, selecione **+ criar um recurso**, em seguida, **segurança + identidade**, em seguida, selecione **Cofre de chaves.**

   ![Criar cofre chaves](media/azure-stack-key-vault-store-credentials/create-key-vault.png)

3. No **criar Key Vault** painel, atribuir uma **nome** para o cofre. Nomes de cofre podem conter apenas caracteres alfanuméricos e o caractere de hífen (-). Eles não devem iniciar com um número.

4. Escolha uma assinatura na lista de assinaturas disponíveis.

5. Selecione um grupo de recursos existente ou crie um novo.

6. Selecione o tipo de preço.

7. Escolha uma das políticas de acesso existentes ou criar um novo. Uma política de acesso permite que você conceda permissões para um usuário, aplicativo ou um grupo de segurança executar operações com este cofre.

8. Como opção, escolha uma política de acesso avançadas para habilitar o acesso aos recursos.

9. Depois de definir as configurações, selecione **Okey**e, em seguida, selecione **criar**. Inicia a implantação do Cofre de chaves.

## <a name="create-a-service-principal"></a>Criar uma entidade de serviço

1. Entrar sua conta do Azure por meio do portal do Azure.

2. Selecione **Azure Active Directory**, em seguida, **registros de aplicativo**, em seguida, **adicionar**.

3. Forneça um nome e uma URL para o aplicativo. Selecione **aplicativo Web/API** ou **Nativo** para o tipo de aplicativo que você deseja criar. Depois de definir os valores, selecione **Criar**.

4. Selecione **do Active Directory**, em seguida, **registros de aplicativo**e selecione seu aplicativo.

5. Copie a **ID do aplicativo** e armazene-a no código do aplicativo. Os aplicativos em aplicativos de exemplo usam **ID do cliente** ao fazer referência à **ID do aplicativo**.

6. Para gerar uma chave de autenticação, selecione **Chaves**.

7. Forneça uma descrição para a chave e uma duração.

8. Clique em **Salvar**.

9. Cópia de **chave** que fica disponível depois que você clicou **salvar**.

## <a name="store-the-service-principal-inside-key-vault"></a>A entidade de serviço no Key Vault da Store

1. Entre portal do usuário para o Azure Stack, em seguida, selecione o Cofre de chaves que você criou anteriormente e, em seguida, selecione a **segredo** lado a lado.

2. No **segredo** painel, selecione **gerar/importar**.

3. No **criar um segredo** painel, na lista de opções, selecione **Manual**.

4. Insira **a ID do aplicativo** copiado da entidade de serviço como o nome para a sua chave. O nome da chave pode conter apenas caracteres alfanuméricos e o caractere de hífen (-).

5. Cole o valor da sua chave de entidade de serviço na **valor** guia.

6. Selecione **entidade de serviço** para o **tipo de conteúdo**.

7. Defina as **data de ativação** e **data de validade** valores para a sua chave.

8. Selecione **criar** para iniciar a implantação.

Depois que o segredo é criado com êxito, as informações de entidade de serviço serão armazenadas ali. Você pode selecioná-lo a qualquer momento sob **segredos**e exibir ou modificar suas propriedades. A seção de propriedades contém o identificador do segredo, que é um identificador de URI (Uniform Resource) que aplicativos externos usam para acessar este segredo.

## <a name="next-steps"></a>Próximas etapas

- [Usar entidades de serviço](azure-stack-create-service-principals.md)
- [Gerenciar cofre de chaves no Azure Stack, o portal](azure-stack-key-vault-manage-portal.md)  
- [Gerenciar o Key Vault no Azure Stack usando o PowerShell](azure-stack-key-vault-manage-powershell.md)