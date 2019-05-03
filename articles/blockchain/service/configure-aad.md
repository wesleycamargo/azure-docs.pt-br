---
title: Como configurar o acesso do Active Directory do Azure
description: Como configurar o serviço de Blockchain do Azure com acesso ao Azure Active Directory
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: seal
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: 616e342f1d52179c40c225c5dafc9de13ce85e06
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028209"
---
# <a name="how-to-configure-azure-active-directory-access"></a>Como configurar o acesso do Active Directory do Azure

Neste artigo, você aprenderá como conceder acesso e se conectar a nós de serviço de Blockchain do Azure usando o usuário, grupo ou IDs de aplicativo do Azure Active Directory (Azure AD).

Azure AD fornece gerenciamento de identidade baseado em nuvem e permite que você use uma única identidade entre toda aplicativos enterprise e acesso no Azure. Azure Blockchain Service é integrado ao AD do Azure e oferece benefícios como a federação de identificação, única logon e autenticação multifator.

## <a name="prerequisites"></a>Pré-requisitos

* [Criar um membro de blockchain usando o portal do Azure](create-member.md)

## <a name="grant-access"></a>Conceder acesso

Você pode conceder acesso no nível do membro e o nível de nó. Concedendo direitos de acesso no nível do membro por sua vez concederá acesso a todos os nós sob o membro.

### <a name="grant-member-level-access"></a>Conceder acesso de nível de membro

Para conceder permissão de acesso no nível do membro.

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Navegue até **controle de acesso (IAM) > Adicionar > Adicionar atribuição de função**.
1. Selecione o **acesso de nó de membro do Blockchain (visualização)** função e adicione o objeto de ID do Azure AD que você deseja conceder acesso ao. Objeto de ID do AD do Azure pode ser:

    | Objeto do AD do Azure | Exemplo |
    |-----------------|---------|
    | Usuário do Azure AD   | `frank@contoso.onmicrosoft.com` |
    | Grupo do AD do Azure  | `sales@contoso.onmicrosoft.com` |
    | ID do aplicativo  | `13925ab1-4161-4534-8d18-812f5ca1ab1e` |

    ![Adicionar atribuição de função](./media/configure-aad/add-role-assignment.png)

1. Clique em **Salvar**.

### <a name="grant-node-level-access"></a>Conceder acesso de nível de nó

1. Você pode conceder acesso no nível do nó, navegando até a segurança de nó e clique no nome do nó que você deseja conceder acesso.
1. Selecione a função de acesso de nó de membro do Blockchain (visualização) e adicione o objeto de ID do Azure AD que você deseja conceder acesso ao. 

## <a name="connect-using-azure-blockchain-connector"></a>Conecte-se usando o conector do Azure Blockchain

Baixe ou clone a [conector de Blockchain do Azure do GitHub](https://github.com/Microsoft/azure-blockchain-connector/).

```bash
git clone https://github.com/Microsoft/azure-blockchain-connector.git
```

O acompanhamento de seção do guia de início rápido a **Leiame** para criar o conector do código-fonte.

### <a name="connect-using-an-azure-ad-user-account"></a>Conectar-se usando uma conta de usuário do AD do Azure

1. Execute o seguinte comando para se autenticar usando uma conta de usuário do AD do Azure. Substitua \<myAADDirectory\> com um domínio do AD do Azure. Por exemplo, `yourdomain.onmicrosoft.com`.

    ```
    connector.exe -remote <myMemberName>.blockchain.azure.com:3200 -method aadauthcode -tenant-id <myAADDirectory> 
    ```

1. Azure AD solicita credenciais.
1. Entrar com seu nome de usuário e senha.
1. Após a autenticação bem-sucedida, o proxy local se conecta ao seu nó de blockchain. Agora você pode anexar seu cliente Geth com o ponto de extremidade local.

    ```bash
    geth attach http://127.0.0.1:3100
    ```

### <a name="connect-using-an-application-id"></a>Conecte-se usando uma ID de aplicativo

Muitos aplicativos se autenticar com o Azure AD usando uma ID de aplicativo em vez de uma conta de usuário do AD do Azure.

Para se conectar ao seu nó usando uma ID de aplicativo, substitua **aadauthcode** com **aadclient**.

```
connector.exe -remote <myBlockchainEndpoint>  -method aadclient -client-id <myClientID> -client-secret "<myClientSecret>" -tenant-id <myAADDirectory>
```

| Parâmetro | DESCRIÇÃO |
|-----------|-------------|
| id do locatário | Domínio do Azure AD, por exemplo, `yourdomain.onmicrosoft.com`
| id do cliente | ID do cliente do aplicativo registrado no AD do Azure
| segredo do cliente | Segredo do cliente do aplicativo registrado no AD do Azure

Para obter mais informações sobre como registrar um aplicativo no Azure AD, consulte [como: Usar o portal para criar um aplicativo e uma entidade de serviço do Azure AD que possa acessar recursos](../../active-directory/develop/howto-create-service-principal-portal.md)

### <a name="connect-a-mobile-device-or-text-browser"></a>Conectar-se um navegador de texto ou de dispositivo móvel

Para um dispositivo móvel ou navegador baseado em texto, em que a exibição de pop-up de autenticação do AD do Azure não é possível, o AD do Azure gera uma senha única. Você pode copiar a senha e prosseguir com a autenticação do Azure AD em outro ambiente.

Para gerar a senha, substitua **aadauthcode** com **aaddevice**. Substitua \<myAADDirectory\> com um domínio do AD do Azure. Por exemplo, `yourdomain.onmicrosoft.com`.

```
connector.exe -remote <myBlockchainEndpoint>  -method aaddevice -tenant-id <myAADDirectory>
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a segurança de dados no serviço de Blockchain do Azure, consulte:

> [!div class="nextstepaction"]
> [Segurança do Azure Blockchain Service](data-security.md)