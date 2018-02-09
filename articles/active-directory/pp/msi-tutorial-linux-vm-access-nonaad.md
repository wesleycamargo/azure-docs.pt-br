---
title: Use um MSI de VM do Linux para acessar o Azure Key Vault
description: "Um tutorial que orienta você durante o processo de uso de uma Identidade de Serviço Gerenciado (MSI) de VM do Linux para acessar o Azure Resource Manager."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: a6f9ade9fca76045799edb8efa070acdfd40b7fe
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2018
---
# <a name="use-a-linux-vm-managed-service-identity-msi-to-access-azure-key-vault"></a>Usar uma MSI (Identidade de Serviço Gerenciado) da VM do Linux para acessar o Azure Key Vault 

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Este tutorial mostra como habilitar a MSI (Identidade de Serviço Gerenciado) para uma Máquina Virtual do Linux e, em seguida, usar essa identidade para acessar o Azure Key Vault. Atuando como bootstrap, o Key Vault possibilita que o aplicativo cliente use o segredo para acessar recursos não protegidos pelo Azure AD (Active Directory). As Identidades de Serviço Gerenciadas são gerenciadas automaticamente pelo Azure e permitem a você autenticar os serviços que dão suporte à autenticação do Azure AD sem necessidade de inserir as credenciais em seu código. 

Você aprenderá como:

> [!div class="checklist"]
> * Habilitar MSI em uma Máquina Virtual do Linux 
> * Conceder o acesso a um segredo armazenado em um Key Vault para sua VM 
> * Obtenha um token de acesso usando a identidade da VM e use-o para recuperar o segredo do Key Vault 
 
## <a name="prerequisites"></a>pré-requisitos

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Entrar no Azure
Entre no portal do Azure em [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Criar uma Máquina Virtual do Linux em um novo Grupo de Recursos

Para este tutorial, vamos criar uma nova VM do Linux. Você também pode habilitar o MSI em uma VM existente.

1. Clique no botão **Novo** no canto superior esquerdo do Portal do Azure.
2. Selecione **Computação** e, em seguida, selecione **Ubuntu Server 16.04 LTS**.
3. Insira as informações da máquina virtual. Para **Tipo de autenticação**, selecione **Chave pública SSH** ou **Senha**. As credenciais criadas permitirão que você faça logon na máquina virtual.

    ![Texto Alt da imagem](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Escolha uma **Assinatura** para a máquina virtual na lista suspensa.
5. Para selecionar um novo **Grupo de Recursos** no qual você deseja criar a máquina virtual, escolha **Criar Novo**. Ao concluir, clique em **OK**.
6. Selecione o tamanho para a VM. Para ver mais tamanhos, selecione **Exibir todos** ou altere o filtro Tipo de disco com suporte. Na página de configurações, mantenha os padrões e clique em **OK**.

## <a name="enable-msi-on-your-vm"></a>Habilitar o MSI na sua VM

Um MSI de máquina virtual permite obter tokens de acesso do Azure AD sem a necessidade de colocar as credenciais no seu código. Nos bastidores, habilitar o MSI faz duas coisas: instala a extensão de VM do MSI em sua VM, e isso permite MSI para a VM.  

1. Selecione a **Máquina Virtual** na qual você deseja habilitar MSI.
2. Na barra de navegação à esquerda, clique em **Configuração**.
3. Você verá **Identidade de Serviço Gerenciado**. Para registrar e habilitar o MSI, selecione **Sim**; se você deseja desabilitá-la, escolha Não.
4. Lembre-se de clicar em **Salvar** para salvar a configuração.

    ![Texto Alt da imagem](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Se você deseja verificar quais extensões estão nesta **VM do Linux**, clique em **Extensões**. Se o MSI estiver habilitado, **ManagedIdentityExtensionforLinux** aparecerá na lista.

    ![Texto Alt da imagem](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)


## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>Conceder o acesso a um segredo armazenado em um Key Vault para sua VM  

Usando o MSI seu código pode obter tokens de acesso para autenticar para recursos que oferecem suporte à autenticação do Azure Active Directory. No entanto, nem todos os serviços do Azure dão suporte à autenticação do Azure AD. Para usar o MSI com esses serviços, armazene as credenciais de serviço no Azure Key Vault e use o MSI para acessar o Key Vault para recuperar as credenciais. 

Primeiro, precisamos criar um Key Vault e conceder acesso de identidade da nossa VM para o Key Vault.   

1. Na parte superior da barra de navegação à esquerda, selecione **+ Novo**, em seguida, **Segurança + Identidade**, em seguida, **Key Vault**.  
2. Forneça um **Nome** para o novo Key Vault. 
3. Localize o Key Vault no mesmo grupo de recursos e assinatura da VM que você criou anteriormente. 
4. Selecione **Políticas de acesso** e clique em **Adicionar nova**. 
5. Em Configurar usando modelo, selecione **Gerenciamento de Segredo**. 
6. Escolha **Selecionar Entidade de Segurança** e no campo de pesquisa insira o nome da VM que você criou anteriormente.  Selecione a VM na lista de resultados e clique em **Selecionar**. 
7. Clique em **OK** para terminar de adicionar a nova política de acesso e **OK** para concluir a seleção de política de acesso. 
8. Clique em **Criar** para terminar de criar o Key Vault. 

    ![Texto Alt da imagem](~/articles/active-directory/media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)

Em seguida, adicione um segredo ao Key Vault, para que posteriormente você possa recuperar o segredo usando código em execução em sua VM: 

1. Selecione **Todos os Recursos** e localize e selecione o Key Vault criado. 
2. Selecione **Segredos** e clique em **Adicionar**. 
3. Selecione **Manual** em **Opções de upload**. 
4. Insira um nome e um valor para o segredo.  O valor pode ser qualquer coisa que você desejar. 
5. Deixe a data de ativação e a data de validade em branco e deixe **Habilitado** como **Sim**. 
6. Clique em **Criar** para criar o segredo. 
 
## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>Obtenha um token de acesso usando a identidade da VM e use-o para recuperar o segredo do Key Vault  

Para concluir essas etapas, você precisará do cliente SSH.  Se você estiver usando o Windows, poderá usar o cliente SSH no [Subsistema do Windows para Linux](https://msdn.microsoft.com/commandline/wsl/about). Se precisar de ajuda para configurar as chaves do cliente SSH, confira [Como usar chaves SSH com o Windows no Azure](~/articles/virtual-machines/linux/ssh-from-windows.md), ou [Como criar e usar um par de chaves SSH pública e privada para VMs Linux no Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).
 
1. No portal, navegue até a VM do Linux e em **Visão geral**, clique em **Conectar**. 
2. **Conecte-se** à VM com um cliente SSH de sua escolha. 
3. Na janela de terminal, usando CURL, faça uma solicitação para o ponto de extremidade do MSI local para obter um token de acesso para o Azure Key Vault.  
 
    A solicitação CURL para o token de acesso está abaixo.  
    
    ```bash
    curl http://localhost:50342/oauth2/token --data "resource=https://vault.azure.net" -H Metadata:true  
    ```
    A resposta inclui o token de acesso de que você precisa para acessar o Azure Resource Manager. 
    
    Resposta:  
    
    ```bash
    {"access_token":"eyJ0eXAi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://vault.azure.net",
    "token_type":"Bearer"} 
    ```
    
    Você pode usar esse token de acesso para autenticar o Azure Key Vault.  A próxima solicitação CURL mostra como ler um segredo do Key Vault usando CURL e a API REST do Key Vault.  Você precisará da URL de seu Key Vault, que está na seção **Essentials** da página **Visão geral** do Key Vault.  O token de acesso que você obteve na chamada anterior também será necessário. 
        
    ```bash
    curl https://<YOUR-KEY-VAULT-URL>/secrets/<secret-name>?api-version=2016-10-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    A resposta terá a seguinte aparência: 
    
    ```bash
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Depois de recuperar o segredo do Key Vault, você pode usá-lo para autenticar um serviço que requer um nome e uma senha.


## <a name="related-content"></a>Conteúdo relacionado

- Para obter uma visão geral do MSI, confira [Visão geral da Identidade de Serviço Gerenciado](msi-overview.md).

Use a seção de comentários a seguir para fornecer seus comentários e nos ajudar a aprimorar e adaptar nosso conteúdo.




