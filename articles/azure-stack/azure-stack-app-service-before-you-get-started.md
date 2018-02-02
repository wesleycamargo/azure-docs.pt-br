---
title: "Antes de implantar o serviço de aplicativo na pilha do Azure | Microsoft Docs"
description: "Etapas para concluir antes de implantar o serviço de aplicativo na pilha do Azure"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: anwestg
ms.openlocfilehash: 18a671fe49b57dda3df33b58a464b300e574376f
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="before-you-get-started-with-app-service-on-azure-stack"></a>Antes de iniciar o serviço de aplicativo na pilha do Azure
*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Antes de implantar o serviço de aplicativo do Azure na pilha do Azure, você deve concluir os pré-requisitos neste artigo.

## <a name="download-the-installer-and-helper-scripts"></a>Baixe os scripts do instalador e auxiliar

1. Baixe o [do serviço de aplicativo em scripts de auxiliar de implantação do Azure pilha](https://aka.ms/appsvconmashelpers).
2. Baixe o [do serviço de aplicativo no instalador do Azure pilha](https://aka.ms/appsvconmasinstaller).
3. Extraia os arquivos do arquivo. zip de scripts de auxiliar. A estrutura de pastas e arquivos a seguir aparecem:
   - Common.ps1
   - Create-AADIdentityApp.ps1
   - Create-ADFSIdentityApp.ps1
   - Create-AppServiceCerts.ps1
   - Get-AzureStackRootCert.ps1
   - Remove-AppService.ps1
   - Módulos
     - GraphAPI.psm1

## <a name="prepare-for-high-availability"></a>Preparar para alta disponibilidade

Serviço de aplicativo do Azure na pilha do Azure atualmente não pode oferecer alta disponibilidade porque a pilha do Azure implanta cargas de trabalho em apenas um domínio de falha.

Para preparar o serviço de aplicativo do Azure na pilha do Azure para alta disponibilidade, implante o servidor de arquivo necessário e a instância do SQL Server em uma configuração altamente disponível. Quando a pilha do Azure oferece suporte a vários domínios de falha, forneceremos orientações sobre como habilitar o serviço de aplicativo do Azure na pilha do Azure em uma configuração altamente disponível.


## <a name="get-certificates"></a>Obter certificados

### <a name="certificates-required-for-the-azure-stack-development-kit"></a>Certificados necessários para o Kit de desenvolvimento de pilha do Azure

O primeiro script funciona com a autoridade de certificação de pilha do Azure para criar quatro certificados que precisa do serviço de aplicativo:

| Nome do arquivo | Uso |
| --- | --- |
| _.appservice.local.azurestack.external.pfx | Certificado SSL do serviço de aplicativo padrão |
| Api.appservice.local.azurestack.external.pfx | Certificado de SSL da API de serviço de aplicativo |
| ftp.appservice.local.azurestack.external.pfx | Certificado SSL do fornecedor de serviço de aplicativo |
| Sso.appservice.local.azurestack.external.pfx | Certificado do aplicativo de identidade de serviço de aplicativo |

Execute o script no host do Kit de desenvolvimento de pilha do Azure e certifique-se de que você está executando o PowerShell como azurestack\CloudAdmin:

1. Em uma sessão do PowerShell executando como azurestack\AzureStackAdmin, execute o script de criação AppServiceCerts.ps1 da pasta onde você extraiu os scripts de auxiliar. O script cria quatro certificados na mesma pasta que o script que precisa para criar certificados de serviço de aplicativo.
2. Insira uma senha para proteger os arquivos. pfx e anote-lo. Você deve inseri-lo no serviço de aplicativo no instalador de pilha do Azure.

#### <a name="create-appservicecertsps1-parameters"></a>AppServiceCerts.ps1 criar parâmetros

| Parâmetro | Obrigatório ou opcional | Valor padrão | DESCRIÇÃO |
| --- | --- | --- | --- |
| pfxPassword | Obrigatório | Nulo | Senha que ajuda a proteger a chave privada do certificado |
| DomainName | Obrigatório | local.azurestack.external | Sufixo de domínio e de região de pilha do Azure |

### <a name="certificates-required-for-a-production-deployment-of-azure-app-service-on-azure-stack"></a>Certificados necessários para uma implantação de produção do serviço de aplicativo do Azure na pilha do Azure

Para operar o provedor de recursos em produção, você deve fornecer os seguintes quatro certificados.

#### <a name="default-domain-certificate"></a>Certificado de domínio padrão

O certificado de domínio padrão é colocado na função de Front-End. Aplicativos de usuário para solicitações de domínio curinga ou padrão para o serviço de aplicativo do Azure usam este certificado. O certificado também é usado para operações de controle de origem (Kudu).

O certificado deve estar no formato. pfx e deve ser um certificado curinga com duas entidades. Isso permite que um certificado cobrir o domínio padrão e o ponto de extremidade SCM para operações de controle de origem.

| Formatar | Exemplo |
| --- | --- |
| \*.appservice.\<region\>.\<DomainName\>.\<extension\> | \*.appservice.redmond.azurestack.external |
| \*.scm.appservice.<region>.<DomainName>.<extension> | \*.appservice.scm.redmond.azurestack.external |

#### <a name="api-certificate"></a>Certificado de API

O certificado de API é colocado na função de gerenciamento. O provedor de recursos usa para ajudar a proteger chamadas de API. O certificado para publicação deve conter uma entidade que corresponda à entrada DNS de API.

| Formatar | Exemplo |
| --- | --- |
| api.appservice.\<region\>.\<DomainName\>.\<extension\> | api.appservice.redmond.azurestack.external |

#### <a name="publishing-certificate"></a>Certificado de publicação

O certificado para a função publicador protege o tráfego FTPS para proprietários de aplicativos ao carregar o conteúdo. O certificado para publicação deve conter uma entidade que corresponda à entrada DNS FTPS.

| Formatar | Exemplo |
| --- | --- |
| ftp.appservice.\<region\>.\<DomainName\>.\<extension\> | api.appservice.redmond.azurestack.external |

#### <a name="identity-certificate"></a>Certificado de identidade

Permite que o certificado para o aplicativo de identidade:
- Integração entre o Azure Active Directory (AD do Azure) ou os serviços de Federação do Active Directory (AD FS) directory pilha do Azure e do serviço de aplicativo para oferecer suporte à integração com o provedor de recursos de computação.
- Único cenários de logon para as ferramentas de desenvolvedor avançadas dentro do serviço de aplicativo do Azure na pilha do Azure.

O certificado de identidade deve conter uma entidade que corresponda o seguinte formato:

| Formatar | Exemplo |
| --- | --- |
| sso.appservice.\<region\>.\<DomainName\>.\<extension\> | sso.appservice.redmond.azurestack.external |

### <a name="azure-resource-manager-root-certificate-for-azure-stack"></a>Certificado de raiz de Gerenciador de recursos do Azure para a pilha do Azure

Em uma sessão do PowerShell executando como azurestack\CloudAdmin, execute o script Get-AzureStackRootCert.ps1 da pasta onde você extraiu os scripts de auxiliar. O script cria quatro certificados na mesma pasta que o script que precisa para criar certificados de serviço de aplicativo.

| Get-AzureStackRootCert.ps1 parameter | Obrigatório ou opcional | Valor padrão | DESCRIÇÃO |
| --- | --- | --- | --- |
| PrivelegedEndpoint | Obrigatório | AzS-ERCS01 | Ponto de extremidade com privilégios |
| CloudAdminCredential | Obrigatório | AzureStack\CloudAdmin | Credencial da conta de domínio para que os administradores de nuvem de pilha do Azure |


## <a name="prepare-the-file-server"></a>Preparar o servidor de arquivos

Serviço de aplicativo do Azure requer o uso de um servidor de arquivos. Para implantações de produção, o servidor de arquivos deve ser configurado para ser altamente disponível e é capaz de lidar com falhas.

Para implantações somente no Kit de desenvolvimento de pilha do Azure, você pode usar o [modelo de implantação do Azure Resource Manager exemplo](https://aka.ms/appsvconmasdkfstemplate) para implantar um servidor de arquivos de nó único configurado. O servidor de arquivos de nó único será um grupo de trabalho.

### <a name="provision-groups-and-accounts-in-active-directory"></a>Provisionar grupos e contas no Active Directory


1. Crie os seguintes grupos de segurança global do Active Directory:
   - FileShareOwners
   - FileShareUsers
2. Crie as seguintes contas do Active Directory como contas de serviço:
   - FileShareOwner
   - FileShareUser

   Como prática recomendada, os usuários dessas contas (e para todas as funções web) deve ser diferentes entre si e ter forte nomes de usuário e senhas. Defina as senhas com as seguintes condições:
   - Habilitar **senha nunca expira**.
   - Habilitar **usuário não pode alterar a senha**.
   - Desabilitar **usuário deve alterar a senha no próximo logon**.
3. Adicione as contas às associações de grupo, da seguinte maneira:
   - Adicionar **FileShareOwner** para o **FileShareOwners** grupo.
   - Adicionar **FileShareUser** para o **FileShareUsers** grupo.

### <a name="provision-groups-and-accounts-in-a-workgroup"></a>Provisionar grupos e contas em um grupo de trabalho

>[!NOTE]
> Quando você estiver configurando um servidor de arquivos, execute os seguintes comandos em uma janela de Prompt de comando administrativa. *Não use o PowerShell.*

Quando você usa o modelo do Gerenciador de recursos do Azure, os usuários já são criados.

1. Execute os seguintes comandos para criar as contas FileShareOwner e FileShareUser. Substituir `<password>` com seus próprios valores.
``` DOS
net user FileShareOwner <password> /add /expires:never /passwordchg:no
net user FileShareUser <password> /add /expires:never /passwordchg:no
```
2. Defina as senhas para as contas para nunca expirar, executando os seguintes comandos WMIC:
``` DOS
WMIC USERACCOUNT WHERE "Name='FileShareOwner'" SET PasswordExpires=FALSE
WMIC USERACCOUNT WHERE "Name='FileShareUser'" SET PasswordExpires=FALSE
```
3. Crie os grupos locais FileShareUsers e FileShareOwners e adicione as contas na primeira etapa para eles:
``` DOS
net localgroup FileShareUsers /add
net localgroup FileShareUsers FileShareUser /add
net localgroup FileShareOwners /add
net localgroup FileShareOwners FileShareOwner /add
```

### <a name="provision-the-content-share"></a>Provisionar o compartilhamento de conteúdo

O compartilhamento de conteúdo contém o conteúdo do site de locatário. O procedimento para provisionar o compartilhamento de conteúdo em um único servidor de arquivos é o mesmo para ambientes do Active Directory e o grupo de trabalho. Mas é diferente de um cluster de failover no Active Directory.

#### <a name="provision-the-content-share-on-a-single-file-server-active-directory-or-workgroup"></a>Provisionar o compartilhamento de conteúdo em um único servidor de arquivos (Active Directory ou grupo de trabalho)

Em um único servidor de arquivos, execute os seguintes comandos em um prompt de comando com privilégios elevados. Substitua o valor de `C:\WebSites` pelos caminhos correspondentes no seu ambiente.

```DOS
set WEBSITES_SHARE=WebSites
set WEBSITES_FOLDER=C:\WebSites
md %WEBSITES_FOLDER%
net share %WEBSITES_SHARE% /delete
net share %WEBSITES_SHARE%=%WEBSITES_FOLDER% /grant:Everyone,full
```

### <a name="add-the-fileshareowners-group-to-the-local-administrators-group"></a>Adicionar o grupo fileshareowners ao grupo Administradores local

Para gerenciamento remoto do Windows funcione corretamente, você deve adicionar o grupo FileShareOwners ao grupo de administradores local.

#### <a name="active-directory"></a>Active Directory

Execute os seguintes comandos em um prompt de comando elevado no servidor de arquivos ou em cada servidor que atua como um nó de cluster de failover. Substitua o valor de `<DOMAIN>` com o nome de domínio que você deseja usar.

```DOS
set DOMAIN=<DOMAIN>
net localgroup Administrators %DOMAIN%\FileShareOwners /add
```

#### <a name="workgroup"></a>Grupo de trabalho

Execute o seguinte comando em um prompt de comando elevado no servidor de arquivos:

```DOS
net localgroup Administrators FileShareOwners /add
```

### <a name="configure-access-control-to-the-shares"></a>Configurar controle de acesso para os compartilhamentos

Execute os seguintes comandos em um prompt de comando elevado no servidor de arquivos ou no nó de cluster de failover, que é o proprietário atual do recurso de cluster. Substitua valores em itálico por valores que são específicos para seu ambiente.

#### <a name="active-directory"></a>Active Directory
```DOS
set DOMAIN=<DOMAIN>
set WEBSITES_FOLDER=C:\WebSites
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

#### <a name="workgroup"></a>Grupo de trabalho
```DOS
set WEBSITES_FOLDER=C:\WebSites
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

## <a name="prepare-the-sql-server-instance"></a>Preparar a instância do SQL Server

Para o serviço de aplicativo do Azure em bancos de dados de medição e hospedagem de pilha do Azure, você deve preparar uma instância do SQL Server para armazenar os bancos de dados do serviço de aplicativo.

Para implantações do Kit de desenvolvimento de pilha do Azure, você pode usar o SQL Server Express 2014 SP2 ou posterior.

Para fins de alta disponibilidade e de produção, você deve usar uma versão completa do SQL Server 2014 SP2 ou posterior, habilitar a autenticação de modo misto e implantar em um [configuração altamente disponível](https://docs.microsoft.com/sql/sql-server/failover-clusters/high-availability-solutions-sql-server).

Instância do SQL Server para o serviço de aplicativo do Azure na pilha do Azure deve ser acessível de todas as funções de serviço de aplicativo. Você pode implantar o SQL Server na assinatura do provedor padrão na pilha do Azure. Ou você pode fazer uso da infraestrutura existente na sua organização (desde que haja conectividade com a pilha do Azure). Se você estiver usando uma imagem do Azure Marketplace, lembre-se de configurar o firewall adequadamente.

Para qualquer uma das funções do SQL Server, você pode usar uma instância padrão ou uma instância nomeada. Se você usar uma instância nomeada, certifique-se de iniciar o serviço navegador do SQL Server manualmente e abra a porta 1434.

## <a name="create-an-azure-active-directory-application"></a>Criar um aplicativo do Azure Active Directory

Configure uma entidade de serviço do AD do Azure para dar suporte aos seguintes:
- Integração do conjunto de escala de máquina virtual em camadas de trabalhador
- SSO para as ferramentas de desenvolvedor do portal e avançado de funções do Azure

Estas etapas se aplicam somente a ambientes de pilha de Azure protegidos pelo AD do Azure.

Os administradores devem configurar SSO para:
- Habilite as ferramentas de desenvolvedor avançadas dentro do serviço de aplicativo (Kudu).
- Habilite o uso da experiência do portal do Azure funções.

Siga estas etapas:

1. Abra uma instância do PowerShell como azurestack\AzureStackAdmin.
2. Vá para o local dos scripts que você baixou e extraiu no [etapa de pré-requisito](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started#download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts).
3. [Instale o PowerShell para Azure pilha](azure-stack-powershell-install.md).
4. Execute o **criar AADIdentityApp.ps1** script. Quando você for solicitado, insira a ID de locatário do AD do Azure que você está usando para sua implantação de pilha do Azure. Por exemplo, digite **myazurestack.onmicrosoft.com**.
5. No **credencial** janela, insira sua conta de administrador de serviço do AD do Azure e a senha. Selecione **OK**.
6. Insira o caminho do arquivo de certificado e a senha do certificado para o [certificado criado anteriormente](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#certificates-required-for-azure-app-service-on-azure-stack). O certificado criado para esta etapa por padrão é **sso.appservice.local.azurestack.external.pfx**.
7. O script cria um novo aplicativo na instância do AD do Azure locatário. Anote a ID do aplicativo que é retornada na saída do PowerShell. Você precisará dessas informações durante a instalação.
8. Abra uma nova janela do navegador e entrar para o [portal do Azure](https://portal.azure.com) como o administrador de serviço do Active Directory do Azure.
9. Abra o provedor de recursos do AD do Azure.
10. Selecione **registros do aplicativo**.
11. Procure a ID do aplicativo retornada como parte da etapa 7. Um aplicativo de serviço de aplicativo está listado.
12. Selecione **aplicativo** na lista.
13. Selecione **as permissões necessárias** > **conceder permissões** > **Sim**.

| Create-AADIdentityApp.ps1  parameter | Obrigatório ou opcional | Valor padrão | DESCRIÇÃO |
| --- | --- | --- | --- |
| DirectoryTenantName | Obrigatório | Nulo | ID de locatário do Azure AD. Forneça o GUID ou uma cadeia de caracteres. Um exemplo é myazureaaddirectory.onmicrosoft.com. |
| AdminArmEndpoint | Obrigatório | Nulo | Ponto de extremidade do Gerenciador de recursos do administrador do Azure. Um exemplo é adminmanagement.local.azurestack.external. |
| TenantARMEndpoint | Obrigatório | Nulo | Ponto de extremidade do locatário do Azure Resource Manager. Um exemplo é management.local.azurestack.external. |
| AzureStackAdminCredential | Obrigatório | Nulo | Credenciais de administrador de serviço de AD do Azure. |
| CertificateFilePath | Obrigatório | Nulo | Caminho para o arquivo de certificado do aplicativo de identidade gerado anteriormente. |
| CertificatePassword | Obrigatório | Nulo | Senha que ajuda a proteger a chave privada do certificado. |

## <a name="create-an-active-directory-federation-services-application"></a>Criar um aplicativo de serviços de Federação do Active Directory

Para ambientes de pilha do Azure protegidos pelo AD FS, você deve configurar uma entidade de serviço do AD FS para dar suporte aos seguintes:
- Integração do conjunto de escala de máquina virtual em camadas de trabalhador
- SSO para as ferramentas de desenvolvedor do portal e avançado de funções do Azure

Os administradores devem configurar SSO para:
- Configure uma entidade de serviço para a integração do conjunto de escala de máquina virtual em camadas de trabalhador.
- Habilite as ferramentas de desenvolvedor avançadas dentro do serviço de aplicativo (Kudu).
- Habilite o uso da experiência do portal do Azure funções.

Siga estas etapas:

1. Abra uma instância do PowerShell como azurestack\AzureStackAdmin.
2. Vá para o local dos scripts que você baixou e extraiu no [etapa de pré-requisito](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts).
3. [Instale o PowerShell para Azure pilha](azure-stack-powershell-install.md).
4.  Execute o **criar ADFSIdentityApp.ps1** script.
5.  No **credencial** janela, insira sua conta de administrador de nuvem do AD FS e a senha. Selecione **OK**.
6.  Forneça o caminho do arquivo de certificado e a senha do certificado para o [certificado criado anteriormente](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#certificates-required-for-azure-app-service-on-azure-stack). O certificado criado para esta etapa por padrão é **sso.appservice.local.azurestack.external.pfx**.

| Create-ADFSIdentityApp.ps1  parameter | Obrigatório ou opcional | Valor padrão | DESCRIÇÃO |
| --- | --- | --- | --- |
| AdminArmEndpoint | Obrigatório | Nulo | Ponto de extremidade do Gerenciador de recursos do administrador do Azure. Um exemplo é adminmanagement.local.azurestack.external. |
| PrivilegedEndpoint | Obrigatório | Nulo | Ponto de extremidade com privilégios. Um exemplo é AzS ERCS01. |
| CloudAdminCredential | Obrigatório | Nulo | Credencial da conta de domínio para que os administradores de nuvem de pilha do Azure. Um exemplo é Azurestack\CloudAdmin. |
| CertificateFilePath | Obrigatório | Nulo | Caminho para o arquivo PFX de certificado de identidade do aplicativo. |
| CertificatePassword | Obrigatório | Nulo | Senha que ajuda a proteger a chave privada do certificado. |


## <a name="next-steps"></a>Próximas etapas

[Instalar o provedor de recursos do serviço de aplicativo](azure-stack-app-service-deploy.md)
