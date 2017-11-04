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
ms.date: 10/17/2017
ms.author: anwestg
ms.openlocfilehash: f2e7b5b96b70333ae4ee92d24c354960008c7f00
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2017
---
# <a name="before-you-get-started-with-app-service-on-azure-stack"></a>Antes de iniciar o serviço de aplicativo na pilha do Azure

Serviço de aplicativo do Azure na pilha do Azure tem um número de etapas de pré-requisito que devem ser concluídas antes da implantação:

- Baixe o serviço de aplicativo do Azure em Scripts do auxiliar de pilha do Azure
- Alta disponibilidade
- Certificados necessários para o serviço de aplicativo do Azure na pilha do Azure
- Preparar o servidor de arquivos
- Preparar o SQL Server
- Criar um aplicativo do Azure Active Directory
- Criar um aplicativo de serviços de Federação do Active Directory

## <a name="download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts"></a>Baixe o serviço de aplicativo do Azure no instalador de pilha do Azure e Scripts de auxiliar

1. Baixe o [do serviço de aplicativo em scripts de auxiliar de implantação do Azure pilha](https://aka.ms/appsvconmashelpers).
2. Baixe o [do serviço de aplicativo no instalador do Azure pilha](https://aka.ms/appsvconmasinstaller).
3. Extraia os arquivos do arquivo zip auxiliar scripts. A estrutura de pastas e arquivos a seguir aparecem:
  - Common.ps1
  - Criar AADIdentityApp.ps1
  - Criar ADFSIdentityApp.ps1
  - Criar AppServiceCerts.ps1
  - Get-AzureStackRootCert.ps1
  - Remover AppService.ps1
  - Módulos
    - GraphAPI.psm1
    
## <a name="high-availability"></a>Alta disponibilidade

Serviço de aplicativo do Azure na pilha do Azure não é atualmente pode oferecer alta disponibilidade porque a pilha Azure implantará somente as cargas de trabalho em um único domínio de falha.

Para preparar o serviço de aplicativo do Azure na pilha do Azure para alta disponibilidade, certifique-se de implantar o servidor de arquivos necessários e o SQL Server em uma configuração altamente disponível. Quando a pilha do Azure oferece suporte a vários domínios de falha, forneceremos orientações sobre como habilitar o serviço de aplicativo do Azure na pilha do Azure em uma configuração altamente disponível.


## <a name="certificates-required-for-azure-app-service-on-azure-stack"></a>Certificados necessários para o serviço de aplicativo do Azure na pilha do Azure

### <a name="certificates-required-for-the-azure-stack-development-kit"></a>Certificados necessários para o Kit de desenvolvimento de pilha do Azure

Este primeiro script funciona com a autoridade de certificação de pilha do Azure para criar quatro certificados que são necessárias para o serviço de aplicativo.

| Nome do arquivo | Uso |
| --- | --- |
| _.appservice.local.azurestack.external.pfx | Certificado SSL do serviço de aplicativo padrão |
| API.appservice.local.azurestack.external.pfx | Certificado de SSL da API do serviço de aplicativo |
| FTP.appservice.local.azurestack.external.pfx | Certificado SSL do fornecedor de serviço de aplicativo |
| SSO.appservice.local.azurestack.external.pfx | Certificado do aplicativo de identidade de serviço de aplicativo |

Execute o script no host do Kit de desenvolvimento de pilha do Azure e certifique-se de que você está executando o PowerShell como azurestack\CloudAdmin.

1. Em uma sessão do PowerShell executando como azurestack\CloudAdmin, execute o script de criação AppServiceCerts.ps1 da pasta onde você extraiu os scripts de auxiliar. O script cria quatro certificados na mesma pasta que o script de criação de certificados que precisa do serviço de aplicativo.
2. Insira uma senha para proteger os arquivos. pfx e anote-lo. Você deve inseri-lo no serviço de aplicativo no instalador de pilha do Azure.

#### <a name="create-appservicecertsps1-parameters"></a>AppServiceCerts.ps1 criar parâmetros

| Parâmetro | Obrigatórios/opcionais | Valor padrão | Descrição |
| --- | --- | --- | --- |
| PfxPassword | Obrigatório | Nulo | Senha usada para proteger a chave privada do certificado |
| DomainName | Obrigatório | local.azurestack.external | Sufixo de domínio e de região de pilha do Azure |

### <a name="certificates-required-for-a-production-deployment-of-azure-app-service-on-azure-stack"></a>Certificados necessários para uma implantação de produção do serviço de aplicativo do Azure na pilha do Azure

Para operar o provedor de recursos em produção, você deve fornecer os seguintes quatro certificados:

#### <a name="default-domain-certificate"></a>Certificado de domínio padrão

O certificado de domínio padrão é colocado na função de Front-End. Ele é usado por aplicativos de usuário para solicitações de curinga ou padrão do domínio para o serviço de aplicativo do Azure. O certificado também é usado para operações de controle de origem (KUDU).

O certificado deve estar no formato. pfx e deve ser um certificado curinga com duas entidades. Isso permite que o domínio padrão e o ponto de extremidade scm para operações de controle do código-fonte sejam abrangidos por um certificado.

| Formatar | Exemplo |
| --- | --- |
| \*.appservice. \<região\>.\< DomainName\>.\< extensão\> | \*. appservice.redmond.azurestack.external |
| \*. scm.appservice. <region>. <DomainName>.<extension> | \*. appservice.scm.redmond.azurestack.external |

#### <a name="api-certificate"></a>Certificado de API

O certificado de API é colocado na função de gerenciamento e é usado pelo provedor de recursos para proteger chamadas de api. O certificado para publicação deve conter uma entidade que corresponda à entrada DNS de API:

| Formatar | Exemplo |
| --- | --- |
| API.appservice. \<região\>.\< DomainName\>.\< extensão\> | API.appservice.Redmond.azurestack.external |

#### <a name="publishing-certificate"></a>Certificado de publicação

O certificado para a função publicador protege o tráfego FTPS para proprietários de aplicativos ao carregar o conteúdo.  O certificado para publicação precisa conter uma entidade que corresponda à entrada DNS FTPS.

| Formatar | Exemplo |
| --- | --- |
| FTP.appservice. \<região\>.\< DomainName\>.\< extensão\> | API.appservice.Redmond.azurestack.external |

#### <a name="identity-certificate"></a>Certificado de identidade

Permite que o certificado para o aplicativo de identidade:
- integração entre o diretório AAD/ADFS, a pilha do Azure e o serviço de aplicativo para oferecer suporte à integração com o provedor de recursos de computação
- Logon único em situações avançadas para ferramentas de desenvolvedor dentro do serviço de aplicativo do Azure na pilha do Azure.
O certificado de identidade deve conter uma entidade que corresponda o seguinte formato:

| Formatar | Exemplo |
| --- | --- |
| SSO.appservice. \<região\>.\< DomainName\>.\< extensão\> | SSO.appservice.Redmond.azurestack.external |

#### <a name="extract-the-azure-stack-azure-resource-manager-root-certificate"></a>Extraia o certificado de raiz do Gerenciador de recursos do Azure pilha do Azure

Em uma sessão do PowerShell executando como azurestack\CloudAdmin, execute o script Get-AzureStackRootCert.ps1 da pasta onde você extraiu os scripts de auxiliar. O script cria quatro certificados na mesma pasta que o script de criação de certificados que precisa do serviço de aplicativo.

| Parâmetro de Get-AzureStackRootCert.ps1 | Obrigatórios/opcionais | Valor padrão | Descrição |
| --- | --- | --- | --- |
| PrivelegedEndpoint | Obrigatório | AzS ERCS01 | Ponto de extremidade com privilégios. |
| CloudAdminCredential | Obrigatório | AzureStack\CloudAdmin | Credencial de conta de domínio de administrador da nuvem de pilha do Azure |


## <a name="prepare-the-file-server"></a>Preparar o servidor de arquivos

Serviço de aplicativo do Azure requer o uso de um servidor de arquivos. Para implantações de produção, o servidor de arquivos deve ser configurado para alta disponibilidade e capaz de lidar com falhas.

Para usar com implantações do Kit de desenvolvimento de pilha do Azure somente, você pode usar este exemplo de modelo de implantação do Gerenciador de recursos do Azure para implantar um servidor de arquivos de nó único configurado: https://aka.ms/appsvconmasdkfstemplate.

### <a name="provision-groups-and-accounts-in-active-directory"></a>Provisionar grupos e contas no Active Directory

>[!NOTE]
> Execute os seguintes comandos, ao configurar o servidor de arquivos, em uma sessão de Prompt de comando do administrador.  **Não use o PowerShell.**

1. Crie os seguintes grupos de segurança global do Active Directory:
    - FileShareOwners
    - FileShareUsers
2. Crie as seguintes contas do Active Directory como contas de serviço:
    - FileShareOwner
    - FileShareUser

    Como prática recomendada, os usuários dessas contas (e para todas as funções Web) deve ser diferentes entre si e ter nomes fortes de usuário e senhas.
    Defina as senhas com as seguintes condições:
     - Habilitar **senha nunca expira**.
     - Habilitar **usuário não pode alterar a senha**.
     - Desabilitar **usuário deve alterar a senha no próximo logon**.
3. Adicione as contas às associações de grupo, da seguinte maneira:
    - Adicionar **FileShareOwner** para o **FileShareOwners** grupo.
    - Adicionar **FileShareUser** para o **FileShareUsers** grupo.

### <a name="provision-groups-and-accounts-in-a-workgroup"></a>Provisionar grupos e contas em um grupo de trabalho

Em um grupo de trabalho, execute net e comandos WMIC para provisionar grupos e contas.

1. Execute os seguintes comandos para criar as contas FileShareOwner e FileShareUser. Substituir <password> com seus próprios valores.
``` DOS
net user FileShareOwner <password> /add /expires:never /passwordchg:no
net user FileShareUser <password> /add /expires:never /passwordchg:no
```
2. Defina as senhas para as contas para nunca expirar, executando os seguintes comandos WMIC:
``` DOS
WMIC USERACCOUNT WHERE "Name='FileShareOwner'" SET PasswordExpires=FALSE
WMIC USERACCOUNT WHERE "Name='FileShareUser'" SET PasswordExpires=FALSE
```
3. Crie os grupos locais FileShareUsers e FileShareOwners e adicione as contas na primeira etapa para eles.
``` DOS
net localgroup FileShareUsers /add
net localgroup FileShareUsers FileShareUser /add
net localgroup FileShareOwners /add
net localgroup FileShareOwners FileShareOwner /add
```

### <a name="provision-the-content-share"></a>Provisionar o compartilhamento de conteúdo

O compartilhamento de conteúdo contém o conteúdo do site de locatário. O procedimento para provisionar o compartilhamento de conteúdo em um único servidor de arquivos é o mesmo para ambientes do Active Directory e o grupo de trabalho, mas diferente para um cluster de Failover no Active Directory.

#### <a name="provision-the-content-share-on-a-single-file-server-ad-or-workgroup"></a>Provisionar o compartilhamento de conteúdo em um único servidor de arquivos (AD ou Workgroup)

Em um único servidor de arquivos, execute os seguintes comandos em um prompt de comando com privilégios elevados. Substitua o valor de < C:\WebSites > pelos caminhos correspondentes no seu ambiente.

```DOS
set WEBSITES_SHARE=WebSites
set WEBSITES_FOLDER=<C:\WebSites>
md %WEBSITES_FOLDER%
net share %WEBSITES_SHARE% /delete
net share %WEBSITES_SHARE%=%WEBSITES_FOLDER% /grant:Everyone,full
```

### <a name="to-enable-winrm-add-the-fileshareowners-group-to-the-local-administrators-group"></a>Para habilitar o WinRM, adicione o grupo FileShareOwners ao grupo Administradores local

Em ordem para o gerenciamento remoto do Windows funcione corretamente, adicione o grupo FileShareOwners ao grupo de administradores local.

#### <a name="active-directory"></a>Active Directory

Execute os seguintes comandos em um prompt de comando elevado no servidor de arquivos ou em cada nó de Cluster de Failover do servidor de arquivos. Substitua o valor de <DOMAIN> com o nome de domínio que você deseja usar.

```DOS
set DOMAIN=<DOMAIN>
net localgroup Administrators %DOMAIN%\FileShareOwners /add
```

#### <a name="workgroup"></a>Grupo de trabalho

Execute o seguinte comando em um prompt de comando elevado no servidor de arquivos.

```DOS
net localgroup Administrators FileShareOwners /add
```

### <a name="configure-access-control-to-the-shares"></a>Configurar controle de acesso para os compartilhamentos

Execute os seguintes comandos em um prompt de comando elevado no servidor de arquivos ou no nó de Cluster de Failover do servidor de arquivos, que é o proprietário atual do recurso de cluster. Substitua valores em itálico por valores específicos ao seu ambiente.

#### <a name="active-directory"></a>Active Directory
```DOS
set DOMAIN=<DOMAIN>
set WEBSITES_FOLDER=<C:\WebSites>
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

#### <a name="workgroup"></a>Grupo de trabalho
```DOS
set WEBSITES_FOLDER=<C:\WebSites>
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

## <a name="prepare-the-sql-server"></a>Preparar o SQL Server

Para o serviço de aplicativo do Azure em bancos de dados de medição e hospedagem de pilha do Azure, você deve preparar um SQL Server para armazenar os bancos de dados do serviço de aplicativo do Azure.

Para uso com o Kit de desenvolvimento de pilha do Azure, você pode usar o SQL Express 2014 SP2 ou posterior.

Para fins de alta disponibilidade e de produção, você deve usar uma versão completa do SQL 2014 SP2 ou posterior, habilitar a autenticação de modo misto e implantar em um [configuração altamente disponível](https://docs.microsoft.com/en-us/sql/sql-server/failover-clusters/high-availability-solutions-sql-server).

O serviço de aplicativo do Azure no servidor de SQL do Azure pilha deve ser acessível de todas as funções de serviço de aplicativo. SQL Server pode ser implantado dentro da assinatura de provedor padrão na pilha do Azure. Ou você pode fazer uso da infraestrutura existente na sua organização (desde que haja conectividade com a pilha do Azure).

Para qualquer uma das funções do SQL Server, você pode usar uma instância padrão ou uma instância nomeada. No entanto, se você usar uma instância nomeada, certifique-se que inicie manualmente o serviço navegador do SQL e abra a porta 1434.

## <a name="create-an-azure-active-directory-application"></a>Criar um aplicativo do Azure Active Directory

Configure uma entidade de serviço do AD do Azure para dar suporte aos seguintes:
- Integração do conjunto de escala de máquina virtual em camadas de trabalhador.
- SSO para as ferramentas de desenvolvedor do portal e avançado de funções do Azure.

Estas etapas se aplicam ao AD do Azure protegido apenas a ambientes de pilha do Azure.

Os administradores devem configurar SSO para:
- Habilite as ferramentas de desenvolvedor avançadas dentro do serviço de aplicativo (Kudu).
- Habilite o uso da experiência do portal do Azure funções.

Siga estas etapas:

1. Abra uma instância do PowerShell como azurestack\cloudadmin.
2. Vá para a localização dos scripts baixados e extraídos no [etapa de pré-requisito](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started#download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts).
3. [Instale o PowerShell do Azure pilha](azure-stack-powershell-install.md).
4. Execute o **criar AADIdentityApp.ps1** script. Quando você for solicitado a fornecer sua ID de locatário do AD do Azure, insira a ID de locatário de AD do Azure que você está usando para sua implantação de pilha do Azure, por exemplo, myazurestack.onmicrosoft.com.
5. No **credencial** janela, insira sua conta de administrador de serviço do AD do Azure e a senha. Clique em **OK**.
6. Insira o caminho do arquivo de certificado e a senha do certificado para o [certificado criado anteriormente](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#certificates-required-for-azure-app-service-on-azure-stack). O certificado criado para esta etapa por padrão é sso.appservice.local.azurestack.external.pfx.
7. O script cria um novo aplicativo no locatário do AD do Azure. Anote a ID do aplicativo que é retornado na saída do PowerShell. Você precisará dessas informações durante a instalação.
8. Abra uma nova janela do navegador e entre no portal do Azure (portal.azure.com) como o **administrador de serviço do Azure Active Directory**.
9. Abra o provedor de recursos do AD do Azure.
10. Clique em **registros do aplicativo**.
11. Procure o **ID do aplicativo** retornadas como parte da etapa 7. Um aplicativo de serviço de aplicativo está listado.
12. Clique em **aplicativo** na lista
13. Clique em **as permissões necessárias** > **conceder permissões** > **Sim**.

| Parâmetro AADIdentityApp.ps1 criar | Obrigatórios/opcionais | Valor padrão | Descrição |
| --- | --- | --- | --- |
| DirectoryTenantName | Obrigatório | Nulo | ID de locatário do Azure AD. Forneça o GUID ou a cadeia de caracteres, por exemplo, myazureaaddirectory.onmicrosoft.com |
| AdminArmEndpoint | Obrigatório | Nulo | O administrador do Azure Gerenciador de recursos de ponto de extremidade, por exemplo adminmanagement.local.azurestack.external |
| TenantARMEndpoint | Obrigatório | Nulo | Locatário do Azure ponto de extremidade de Gerenciador de recursos, por exemplo: management.local.azurestack.external |
| AzureStackAdminCredential | Obrigatório | Nulo | Credenciais de administrador de serviço do AD do Azure |
| CertificateFilePath | Obrigatório | Nulo | Caminho para o arquivo de certificado do aplicativo de identidade gerado anteriormente. |
| CertificatePassword | Obrigatório | Nulo | Senha usada para proteger a chave privada do certificado. |

## <a name="create-an-active-directory-federation-services-application"></a>Criar um aplicativo de serviços de Federação do Active Directory

Para ambientes de pilha do Azure protegidos pelo AD FS, você deve configurar uma entidade de serviço do AD FS para dar suporte aos seguintes:
- Integração do conjunto de escala de máquina virtual em camadas de trabalhador.
- SSO para as ferramentas de desenvolvedor do portal e avançado de funções do Azure.

Os administradores precisam configurar SSO para:
- Configure uma entidade de serviço para a integração do conjunto de escala de máquina virtual em camadas de trabalhador.
- Habilite as ferramentas de desenvolvedor avançadas dentro do serviço de aplicativo (Kudu).
- Habilite o uso da experiência do portal do Azure funções.

Siga estas etapas:

1. Abra uma instância do PowerShell como azurestack\azurestackadmin.
2. Vá para a localização dos scripts baixados e extraídos no [etapa de pré-requisito](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts).
3. [Instale o PowerShell do Azure pilha](azure-stack-powershell-install.md).
4.  Execute o **criar ADFSIdentityApp.ps1** script.
5.  No **credencial** janela, insira sua conta de administrador de nuvem do AD FS e a senha. Clique em **OK**.
6.  Forneça o caminho do arquivo de certificado e a senha do certificado para o [certificado criado anteriormente](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#certificates-required-for-azure-app-service-on-azure-stack). O certificado criado para esta etapa por padrão é sso.appservice.local.azurestack.external.pfx.

| Parâmetro ADFSIdentityApp.ps1 criar | Obrigatórios/opcionais | Valor padrão | Descrição |
| --- | --- | --- | --- |
| AdminArmEndpoint | Obrigatório | Nulo | O ponto de extremidade do Azure Resource Manager admin. Por exemplo, adminmanagement.local.azurestack.external. |
| PrivilegedEndpoint | Obrigatório | Nulo | Ponto de extremidade com privilégios. Por exemplo, AzS ERCS01. |
| CloudAdminCredential | Obrigatório | Nulo | Azure pilha cloudadmin domínio credencial da conta. Por exemplo, Azurestack\CloudAdmin. |
| CertificateFilePath | Obrigatório | Nulo | Caminho para o arquivo PFX de certificado do aplicativo de identidade. |
| CertificatePassword | Obrigatório | Nulo | Senha usada para proteger a chave privada do certificado. |


## <a name="next-steps"></a>Próximas etapas

[Instalar o provedor de recursos do serviço de aplicativo](azure-stack-app-service-deploy.md).
