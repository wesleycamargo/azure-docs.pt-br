---
title: Use um MSI de VM do Windows para acessar o Azure Key Vault
description: "Um tutorial que orienta o processo de usar uma Identidade de Serviço Gerenciado (MSI) de VM do Windows para acessar o Azure Key Vault."
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
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: 5dd90d527afd81ad225b9693b126f48e48bde884
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2018
---
# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-azure-key-vault"></a>Usar o MSI (Identidade de Serviço Gerenciada) da VM do Windows para acessar o Azure Key Vault 

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Este tutorial mostra como habilitar a MSI (Identidade de Serviço Gerenciado) para uma Máquina Virtual do Windows e, em seguida, usar essa identidade para acessar o Azure Key Vault. Atuando como bootstrap, o Key Vault possibilita que o aplicativo cliente use o segredo para acessar recursos não protegidos pelo Azure AD (Active Directory). As Identidades de Serviço Gerenciadas são gerenciadas automaticamente pelo Azure e permitem a você autenticar os serviços que dão suporte à autenticação do Azure AD sem necessidade de inserir as credenciais em seu código. 

Você aprenderá como:


> [!div class="checklist"]
> * Habilitar a Identidade de Serviço Gerenciado na máquina virtual do Windows 
> * Conceda o acesso da máquina virtual a um segredo armazenado em um Key Vault 
> * Obter um token de acesso usando a identidade da máquina virtual e usá-la para recuperar o segredo do Key Vault 

## <a name="prerequisites"></a>pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Criar uma máquina virtual do Windows em um novo grupo de recursos

Para este tutorial, vamos criar uma nova VM do Windows. Você também pode habilitar o MSI em uma VM existente.

1.  Clique no botão **Novo** no canto superior esquerdo do Portal do Azure.
2.  Selecione **Computação** e, em seguida, selecione **Windows Server 2016 Datacenter**. 
3.  Insira as informações da máquina virtual. O **Nome de usuário** e **Senha** criados aqui são as credenciais usadas para fazer logon na máquina virtual.
4.  Escolha uma **Assinatura** para a máquina virtual na lista suspensa.
5.  Para selecionar um novo **Grupo de recursos** no qual você deseja criar a máquina virtual, escolha **Criar novo**. Ao concluir, clique em **OK**.
6.  Selecione o tamanho para a VM. Para ver mais tamanhos, selecione **Exibir todos os** ou altere o filtro **Tipo de disco com suporte**. Na folha de configurações, mantenha os padrões e clique em **OK**.

    ![Texto Alt da imagem](media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Habilitar o MSI na sua VM 

Um MSI de máquina virtual permite obter tokens de acesso do Azure AD sem a necessidade de colocar as credenciais no seu código. Habilitar o MSI informa ao Azure para criar uma identidade gerenciada para sua máquina virtual. Nos bastidores, habilitar o MSI faz duas coisas: instala a extensão de VM do MSI em sua VM, e isso permite MSI no Azure Resource Manager.

1.  Selecione a **Máquina Virtual** na qual você deseja habilitar MSI.  
2.  Na barra de navegação à esquerda, clique em **Configuração**. 
3.  Você verá **Identidade de Serviço Gerenciado**. Para registrar e habilitar o MSI, selecione **Sim**; se você deseja desabilitá-la, escolha Não. 
4.  Lembre-se de clicar em **Salvar** para salvar a configuração.  

    ![Texto Alt da imagem](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Se você deseja verificar quais as extensões que estão nessa VM, clique em **Extensões**. Se o MSI estiver habilitado, **ManagedIdentityExtensionforWindows** será exibido na lista.

    ![Texto Alt da imagem](media/msi-tutorial-windows-vm-access-arm/msi-windows-extension.png)

## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>Conceder o acesso a um segredo armazenado em um Key Vault para sua VM 
 
Usando o MSI seu código pode obter tokens de acesso para autenticar para recursos que oferecem suporte à autenticação do Azure AD.  No entanto, nem todos os serviços do Azure dão suporte à autenticação do Azure AD. Para usar o MSI com esses serviços, armazene as credenciais de serviço no Azure Key Vault e use o MSI para acessar o Key Vault para recuperar as credenciais. 

Primeiro, precisamos criar um Key Vault e conceder acesso de identidade da nossa VM para o Key Vault.   

1. Na parte superior da barra de navegação à esquerda, selecione **+ Novo**, em seguida, **Segurança + Identidade**, em seguida, **Key Vault**.  
2. Forneça um **Nome** para o novo Key Vault. 
3. Localize o Key Vault no mesmo grupo de recursos e assinatura da VM que você criou anteriormente. 
4. Selecione **Políticas de acesso** e clique em **Adicionar nova**. 
5. Em Configurar usando modelo, selecione **Gerenciamento de Segredo**. 
6. Escolha **Selecionar Entidade de Segurança** e no campo de pesquisa insira o nome da VM que você criou anteriormente.  Selecione a VM na lista de resultados e clique em **Selecionar**. 
7. Clique em **OK** para terminar de adicionar a nova política de acesso e **OK** para concluir a seleção de política de acesso. 
8. Clique em **Criar** para terminar de criar o Key Vault. 

    ![Texto Alt da imagem](media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)


Em seguida, adicione um segredo ao Key Vault, para que posteriormente você possa recuperar o segredo usando código em execução em sua VM: 

1. Selecione **Todos os Recursos** e localize e selecione o Key Vault criado. 
2. Selecione **Segredos** e clique em **Adicionar**. 
3. Selecione **Manual** em **Opções de upload**. 
4. Insira um nome e um valor para o segredo.  O valor pode ser qualquer coisa que você desejar. 
5. Deixe a data de ativação e a data de validade em branco e deixe **Habilitado** como **Sim**. 
6. Clique em **Criar** para criar o segredo. 
 
## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>Obtenha um token de acesso usando a identidade da VM e use-o para recuperar o segredo do Key Vault  

Se você não tiver o PowerShell 4.3.1 ou superior instalado, você precisará [baixar e instalar a versão mais recente](https://docs.microsoft.com/powershell/azure/overview).

Primeiro, usamos o MSI da VM para obter um token de acesso para autenticar para o Key Vault:
 
1. No portal, navegue até **Máquinas Virtuais** e vá para a máquina virtual do Windows e em **Visão geral**, clique em **Conectar**.
2. Insira o seu **Nome de usuário** e **Senha** que você adicionou quando criou a **VM do Windows**.  
3. Agora que você criou uma **Conexão de Área de Trabalho Remota** com a máquina virtual, abra o PowerShell na sessão remota.  
4. No PowerShell, invoque a solicitação Web no locatário para obter o token para o host local na porta específica para a máquina virtual.  

    A solicitação do PowerShell:
    
    ```powershell
    PS C:\> $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://vault.azure.net"} -Headers @{Metadata="true"} 
    ```
    
    Em seguida, extraia a resposta completa, que é armazenada como uma cadeia de caracteres JSON formatada no objeto $response.  
    
    ```powershell
    PS C:\> $content = $response.Content | ConvertFrom-Json 
    ```
    
    Em seguida, extraia o token de acesso da resposta.  
    
    ```powershell
    PS C:\> $KeyVaultToken = $content.access_token 
    ```
    
    Por fim, use o comando de Invoke-WebRequest do PowerShell para recuperar o segredo que você criou anteriormente no Key Vault, passando o token de acesso no cabeçalho de Autorização.  Você precisará da URL de seu Key Vault, que está na seção **Essentials** da página **Visão geral** do Key Vault.  
    
    ```powershell
    PS C:\> (Invoke-WebRequest -Uri https://<your-key-vault-URL>/secrets/<secret-name>?api-version=2016-10-01 -Method GET -Headers @{Authorization="Bearer $KeyVaultToken"}).content 
    ```
    
    A resposta terá a seguinte aparência: 
    
    ```powershell
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Depois de recuperar o segredo do Key Vault, você pode usá-lo para autenticar um serviço que requer um nome e uma senha. 

## <a name="related-content"></a>Conteúdo relacionado

- Para obter uma visão geral do MSI, confira [Visão geral da Identidade de Serviço Gerenciado](../active-directory/msi-overview.md).

Use a seção de comentários a seguir para fornecer seus comentários e nos ajudar a aprimorar e adaptar nosso conteúdo.
