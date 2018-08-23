---
title: Configurar a validação de pilha do Azure como uma conta de serviço | Microsoft Docs
description: Saiba como configurar a validação de uma conta de serviço.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 3f87721dcf7485e15a473e82597f6bf4baeca659
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2018
ms.locfileid: "42139725"
---
# <a name="set-up-your-validation-as-a-service-account"></a>Configurar sua validação como uma conta de serviço

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

A validação como um serviço (VaaS) é um serviço do Azure que é disponibilizado para os parceiros do Microsoft Azure Stack que tem um contrato de engenharia conjunta com a Microsoft para projetar, desenvolver, validar, vender, implantar e dar suporte a soluções do Azure Stack no mercado.

Saiba como preparar seu sistema para a validação como serviço. Configure a instância do Active Directory do Azure e executar outras tarefas necessárias para se prepararem para usar VaaS. 

As tarefas incluem:

- Criar um Azure Storage blob para armazenar logs
- Implantar o agente local
- Baixe as máquinas de virtuais do teste de imagem na instância do Azure Stack para ser testado

## <a name="create-an-azure-active-directory-tenant-id"></a>Criar uma ID de locatário do Azure Active Directory

1. Criar um locatário do Active Directory do Azure na [portal do Azure](https://portal.azure.com) ou use um locatário existente.

    É recomendável criar um locatário especificamente para uso com VaaS com um nome descritivo, como, ContosoVaaS@onmicrosoft.com. Os recursos de RBAC (controle) de acesso baseado em função do locatário serão ser usados pelo parceiro para gerenciar quem na organização do parceiro pode usar VaaS.  
    
    Obter mais informações, consulte [gerenciar seu diretório do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-administer).

    > [!Note]  
    > Para obter mais informações sobre a criação de novos locatários do Azure Active Directory, consulte [Introdução ao Azure AD](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad).

2. Adicione membros da sua organização que serão responsáveis por usar o serviço para o locatário. Atribua a cada usuário no locatário de uma das seguintes funções para controlar o nível de acesso para VaaS:

    | Nome da função | DESCRIÇÃO |
    |---------------------|------------------------------------------|
    | Proprietário | Tem acesso total a todos os recursos. |
    | Leitor | Pode exibir todos os recursos, mas não editar. |
    | Colaborador de teste | Pode gerenciar os recursos de teste. |
    | Colaborador do catálogo | Pode gerenciar recursos de publicação da solução. |

## <a name="set-up-your-tenant"></a>Configurar seu locatário

Configurar seu locatário na **serviço do Azure Stack validação** aplicativo. 

1. Enviar as informações a seguir sobre o locatário para a Microsoft em vaashelp@microsoft.com.

    | Dados | DESCRIÇÃO |
    |--------------------------------|---------------------------------------------------------------------------------------------|
    | Nome da Organização | Nome da organização oficial. |
    | Nome do diretório de locatário do Azure AD | Nome do diretório de locatário do AD do Azure que está sendo registrado. |
    | Id de diretório do locatário do Azure AD | Diretório de locatário do AD do Azure GUID associada com o diretório.<br> Para obter informações sobre como localizar sua ID de diretório do locatário do Azure AD pode ser encontrado, consulte "[obter ID de locatário](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-tenant-id)." |

    

2. A equipe do Azure Stack fornece confirmação de que seu locatário pode usar o portal de VaaS.

3. Usar as credenciais de administrador global do locatário para entrar na [VaaS portal](https://azurestackvalidation.com/
). Selecione **minha conta**.

    ![Entrar no portal de VaaS](media/vaas_portalsignin.png)

3. O site será solicitado que você conceda acesso para VaaS. Aceite os termos para continuar.

## <a name="assign-user-roles"></a>Atribuir funções de usuário

Para atribuir uma função de usuário:

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione **todos os serviços** > **do Azure Active Directory** no **identidade** grupo.
3. Selecione **aplicativos empresariais** > **serviço do Azure Stack validação** aplicativo.
4. Selecione **Usuários e grupos**. O **serviço do Azure Stack validação - usuários e grupos** folha lista os usuários com permissão para usar o aplicativo.
5. Selecione **+ adicionar usuário** para adicionar uma atribuição.

## <a name="create-an-azure-storage-blob-to-store-logs"></a>Criar um blob de armazenamento do Azure para armazenar logs

VaaS cria logs de diagnóstico ao executar os testes de validação. Você precisa de um local um URL SAS para armazenar seus logs de serviço de blob do Azure. A conta de armazenamento também pode ser usado para os pacotes de personalização de OEM de armazenamento.

Estas etapas explicam como configurar e gerar um URI (SAS) de armazenamento como um serviço para uma conta de armazenamento do Azure e onde especificar a conta de armazenamento no portal do VaaS quando os testes a partir do portal.

### <a name="create-an-azure-storage-account"></a>Criar uma conta de armazenamento do Azure

1. Para criar uma conta de armazenamento, siga as instruções [criar uma conta de armazenamento](../../storage/common/storage-quickstart-create-account.md).

2. Ao selecionar o tipo de conta de armazenamento, selecione o **armazenamento de BLOBs** tipo de conta.

### <a name="generate-a-sas-url-for-the-storage-account"></a>Gerar uma URL de SAS da conta de armazenamento

1. Navegue até a conta de armazenamento criada acima.

2. Na folha sob **as configurações**, selecione **assinatura de acesso compartilhado**.

3. Verificar somente **Blob** de **opções de serviços permitidos** (desmarque restantes).

4. Verifique **Service**, * * contêiner, e **objeto** da **tipos de recursos permitidos**.

5. Verifique **leitura**, **escrever**, **lista**, **adicionar**, **criar** de **permissões concedidas**  (desmarque restantes).

6. Definir **hora de início** para a hora atual, e **hora de término** três meses a partir da hora atual.

7. Selecione **cadeia de caracteres de conexão e de gerar SAS** e salve o **URL de SAS do serviço Blob** cadeia de caracteres.

> [!Note]  
> A URL de SAS expirar na hora de término que definir a URL foi gerada. Certifique-se de que a URL é suficientemente válida antes de compartilhá-lo com a equipe de produto para depuração ou que a URL é válida por mais de 30 dias, durante o agendamento de testes.

## <a name="next-steps"></a>Próximas etapas

- Use o agente local VaaS para verificar seu hardware. Para obter instruções, consulte [implantar as máquinas de virtuais de teste e o agente locais](azure-stack-vaas-test-vm.md).
- Para saber mais sobre [validação do Azure Stack como um serviço](https://docs.microsoft.com/azure/azure-stack/partner).