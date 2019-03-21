---
title: Tutorial - configurar recursos para a validação como um serviço | Microsoft Docs
description: Neste tutorial, saiba como configurar recursos para a validação como um serviço.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 55c9120547472bb9a9a74533fe532d346844e89c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58081756"
---
# <a name="tutorial-set-up-resources-for-validation-as-a-service"></a>Tutorial: Configurar recursos para a validação como um serviço

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

A validação como um serviço (VaaS) é um serviço do Azure que é usado para validar e dar suporte a soluções do Azure Stack no mercado. Siga este artigo antes de usar o serviço para validar sua solução.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Prepare-se de usar VaaS, definindo-se o Azure Active Directory (AD).
> * Crie uma conta de armazenamento.

## <a name="configure-an-azure-ad-tenant"></a>Configurar um locatário do AD do Azure

Um locatário do AD do Azure é usado para registrar uma organização e autenticar usuários com VaaS. O parceiro usará os recursos de RBAC (controle) de acesso baseado em função do locatário para gerenciar quem na organização do parceiro pode usar VaaS. Para obter mais informações, confira [O que é Azure Active Directory?](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals/active-directory-whatis).

### <a name="create-a-tenant"></a>Criar um locatário

Crie um locatário que sua organização irá usar para acessar os serviços de VaaS. Use um nome descritivo, por exemplo, `ContosoVaaS@onmicrosoft.com`.

1. Criar um locatário do AD do Azure na [portal do Azure](https://portal.azure.com), ou use um locatário existente. <!-- For instructions on creating new Azure AD tenants, see [Get started with Azure AD](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad). -->

2. Adicione membros de sua organização para o locatário. Esses usuários será responsáveis por usar o serviço para exibir ou agendar os testes. Depois de concluir o registro, você vai definir níveis de acesso dos usuários.

    Autorize os usuários em seu locatário para executar as ações na VaaS atribuindo uma das seguintes funções:

    | Nome da Função | Descrição |
    |---------------------|------------------------------------------|
    | Proprietário | Tem acesso total a todos os recursos. |
    | Leitor | Pode exibir todos os recursos, mas não criar ou gerenciar. |
    | Colaborador de teste | Pode criar e gerenciar recursos de teste. |

    Para atribuir funções na **serviço do Azure Stack validação** aplicativo:

   1. Entre no [Portal do Azure](https://portal.azure.com).
   2. Selecione **todos os serviços** > **do Azure Active Directory** sob o **identidade** seção.
   3. Selecione **aplicativos empresariais** > **serviço do Azure Stack validação** aplicativo.
   4. Selecione **Usuários e grupos**. O **serviço do Azure Stack validação - usuários e grupos** folha lista os usuários com permissão para usar o aplicativo.
   5. Selecione **+ adicionar usuário** para adicionar um usuário do seu locatário e atribuir uma função.

      Se você quiser isolar recursos de VaaS e ações entre diferentes grupos dentro de uma organização, você pode criar vários diretórios de locatário do AD do Azure.

### <a name="register-your-tenant"></a>Registrar seu locatário

Esse processo autoriza seu locatário com o **serviço do Azure Stack validação** aplicativo do Azure AD.

1. Enviar as informações a seguir sobre o locatário para a Microsoft em [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com).

    | Dados | Descrição |
    |--------------------------------|---------------------------------------------------------------------------------------------|
    | Nome da Organização | O nome da organização oficial. |
    | Nome do diretório de locatário do Azure AD | O nome do diretório de locatário do Azure AD que está sendo registrado. |
    | ID de diretório do locatário do Azure AD | O diretório de locatário do Azure AD GUID associada com o diretório. Para obter informações sobre como localizar sua ID de diretório do locatário do Azure AD, consulte [obter ID de locatário](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-tenant-id). |

2. Aguarde a confirmação da equipe do Azure Stack validação para verificar que seu locatário pode usar o portal de VaaS.

### <a name="consent-to-the-vaas-application"></a>Consentimento ao aplicativo VaaS

Como o administrador do AD do Azure, dê ao aplicativo de VaaS do Azure AD as permissões necessárias em nome do seu locatário:

1. Usar as credenciais de administrador global do locatário para entrar na [VaaS portal](https://azurestackvalidation.com/). 

2. Selecione **minha conta**.

3 aceitar os termos para continuar quando for solicitado a conceder permissões VaaS listada do Azure AD.

## <a name="create-an-azure-storage-account"></a>Criar uma conta de Armazenamento do Azure

Durante a execução de teste, VaaS gera logs de diagnóstico para uma conta de armazenamento do Azure. Além dos logs de teste, a conta de armazenamento também pode ser usado para os pacotes de extensão de upload de OEM para o fluxo de trabalho de validação do pacote.

A conta de armazenamento do Azure está hospedada na nuvem pública do Azure, não em seu ambiente do Azure Stack.

1. No portal do Azure, selecione **todos os serviços** > **armazenamento** > **contas de armazenamento**. Sobre o **contas de armazenamento** folha, selecione **Add**.

2. Selecione a assinatura na qual você deseja criar a conta de armazenamento.

3. Sob **grupo de recursos**, selecione **criar novo**. Insira um nome para seu novo grupo de recursos.

4. Examine os [convenções de nomenclatura](https://docs.microsoft.com/en-us/azure/architecture/best-practices/naming-conventions#storage) para contas de armazenamento do Azure. Insira um nome para a conta de armazenamento.

5. Selecione o **Oeste dos EUA** região de sua conta de armazenamento.

    Para garantir que os encargos de rede não são incorridos para armazenar os logs, a conta de armazenamento do Azure pode ser configurada para usar somente o **Oeste dos EUA** região. O recurso de camada de armazenamento quente e replicação de dados não são necessárias para esses dados. Habilitar um desses recursos aumentará consideravelmente os custos.

6. Deixe as configurações para os valores padrão, exceto para **tipo de conta**:

    - O **modelo de implantação** campo é definido como **Gerenciador de recursos** por padrão.
    - O campo **Desempenho** é definido como **Standard** por padrão.
    - Selecione **tipo de conta** campo como **armazenamento de BLOBs**.
    - O **campo de replicação** é definido como **armazenamento localmente redundante (LRS)** por padrão.
    - A **Camada de acesso** é definido como **Frequente** por padrão.

7. Selecione **Revisar + Criar** para examinar as configurações da conta de armazenamento e criar a conta.

## <a name="next-steps"></a>Próximas etapas

Se seu ambiente não permitir conexões de entrada, siga o tutorial sobre como implantar o agente local para executar um teste em seu hardware.

> [!div class="nextstepaction"]
> [Implantar o agente local](azure-stack-vaas-local-agent.md)
