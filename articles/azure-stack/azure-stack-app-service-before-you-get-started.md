---
title: Antes de implantar o serviço de aplicativo no Azure Stack | Microsoft Docs
description: Etapas para concluir antes de implantar o serviço de aplicativo no Azure Stack
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2018
ms.author: anwestg
ms.openlocfilehash: ec34b99f48fbe7b634d7d143e8e108e8f2868f67
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55182720"
---
# <a name="before-you-get-started-with-app-service-on-azure-stack"></a>Antes de começar com o serviço de aplicativo no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Antes de implantar o serviço de aplicativo do Azure no Azure Stack, você deve concluir as etapas de pré-requisito neste artigo.

> [!IMPORTANT]
> Aplicar a atualização 1809 seu sistema integrado do Azure Stack ou implantar o mais recente do Azure Stack desenvolvimento ASDK (Kit) antes de implantar 1.4 de serviço de aplicativo do Azure.

## <a name="download-the-installer-and-helper-scripts"></a>Baixe os instalador e scripts auxiliares

1. Baixe o [serviço de aplicativo em scripts de auxiliar de implantação do Azure Stack](https://aka.ms/appsvconmashelpers).
2. Baixe o [serviço de aplicativo no instalador do Azure Stack](https://aka.ms/appsvconmasinstaller).
3. Extraia os arquivos do arquivo. zip de scripts do auxiliar. Os seguintes arquivos e pastas são extraídas:

   - Common.ps1
   - Create-AADIdentityApp.ps1
   - Create-ADFSIdentityApp.ps1
   - Create-AppServiceCerts.ps1
   - Get-AzureStackRootCert.ps1
   - Remove-AppService.ps1
   - Pasta de módulos
     - GraphAPI.psm1

## <a name="syndicate-the-custom-script-extension-from-the-marketplace"></a>Distribuir a extensão de Script personalizado do Marketplace

Serviço de aplicativo do Azure no Azure Stack requer v1.9.0 de extensão de Script personalizado.  A extensão deve ser [agregado do Marketplace](https://docs.microsoft.com/azure/azure-stack/azure-stack-download-azure-marketplace-item) antes de iniciar a implantação ou atualização do serviço de aplicativo do Azure no Azure Stack

## <a name="high-availability"></a>Alta disponibilidade

A atualização 1802 de pilha do Azure adicionou suporte para domínios de falha. Novas implantações do serviço de aplicativo do Azure no Azure Stack serão distribuídas entre domínios de falha e fornecem tolerância a falhas.

Para as implantações existentes do serviço de aplicativo do Azure no Azure Stack, que foram implantadas antes da atualização 1802, consulte o [reequilibrar um provedor de recursos do serviço de aplicativo entre domínios de falha](azure-stack-app-service-fault-domain-update.md) artigo.

Além disso, implante o servidor de arquivos necessários e a instâncias do SQL Server em uma configuração altamente disponível.

## <a name="get-certificates"></a>Obter certificados

### <a name="azure-resource-manager-root-certificate-for-azure-stack"></a>Certificado de raiz do Azure Resource Manager para o Azure Stack

Abra uma sessão do PowerShell com privilégios elevados em um computador que pode alcançar o ponto de extremidade com privilégios no sistema integrado do Azure Stack ou Host do Kit de desenvolvimento do Azure Stack.

Execute o *Get-AzureStackRootCert.ps1* script da pasta onde você extraiu os scripts auxiliares. O script cria um certificado raiz na mesma pasta que o script que precisa para criar certificados de serviço de aplicativo.

Quando você executa o seguinte comando do PowerShell, você precisará fornecer o ponto de extremidade com privilégios e as credenciais para o AzureStack\CloudAdmin.

```PowerShell
    Get-AzureStackRootCert.ps1
```

#### <a name="get-azurestackrootcertps1-script-parameters"></a>Get-AzureStackRootCert.ps1 script parameters

| Parâmetro | Obrigatório ou opcional | Valor padrão | Descrição |
| --- | --- | --- | --- |
| PrivilegedEndpoint | Obrigatório | AzS-ERCS01 | Ponto de extremidade com privilégios |
| CloudAdminCredential | Obrigatório | AzureStack\CloudAdmin | Credencial de conta de domínio para que os administradores de nuvem do Azure Stack |

### <a name="certificates-required-for-asdk-deployment-of-azure-app-service"></a>Certificados necessários para a implantação de ASDK do serviço de aplicativo do Azure

O *AppServiceCerts.ps1 criar* script funciona com a autoridade de certificação do Azure Stack para criar os quatro certificados que precisa do serviço de aplicativo.

| Nome do arquivo | Uso |
| --- | --- |
| _.appservice.local.azurestack.external.pfx | Certificado SSL padrão do Serviço de Aplicativo |
| api.appservice.local.azurestack.external.pfx | Certificado SSL de API do serviço de aplicativo |
| ftp.appservice.local.azurestack.external.pfx | Certificado de SSL de Editor do serviço de aplicativo |
| sso.appservice.local.azurestack.external.pfx | Certificado de aplicativo de identidade do serviço de aplicativo |

Para criar os certificados, siga estas etapas:

1. Faça logon no host do Kit de desenvolvimento do Azure Stack usando a conta AzureStack\AzureStackAdmin.
2. Abra uma sessão do PowerShell com privilégios elevados.
3. Execute o *AppServiceCerts.ps1 criar* script da pasta onde você extraiu os scripts auxiliares. Esse script cria quatro certificados na mesma pasta que o script que precisa para criar certificados de serviço de aplicativo.
4. Insira uma senha para proteger os arquivos. pfx e faça uma observação sobre isso. Você terá que inseri-los no serviço de aplicativo no instalador do Azure Stack.

#### <a name="create-appservicecertsps1-script-parameters"></a>AppServiceCerts.ps1 criar parâmetros de script

| Parâmetro | Obrigatório ou opcional | Valor padrão | Descrição |
| --- | --- | --- | --- |
| pfxPassword | Obrigatório | Null | Senha que ajuda a proteger a chave privada do certificado |
| DomainName | Obrigatório | local.azurestack.external | Sufixo de região e o domínio de pilha do Azure |

### <a name="certificates-required-for-azure-stack-production-deployment-of-azure-app-service"></a>Certificados necessários para a implantação de produção do Azure Stack do serviço de aplicativo do Azure

Para executar o provedor de recursos na produção, você deve fornecer os seguintes certificados:

- Certificado de domínio padrão
- Certificado de API
- Certificado de publicação
- Certificado de identidade

#### <a name="default-domain-certificate"></a>Certificado de domínio padrão

O certificado de domínio padrão é colocado na função de Front-End. Aplicativos de usuário para solicitação de domínio de curinga ou padrão para o serviço de aplicativo do Azure usam esse certificado. O certificado também é usado para operações de controle do código-fonte (Kudu).

O certificado deve estar no formato. pfx e deve ser um certificado curinga do assunto de três. Esse requisito permite que um certificado cobrir o domínio padrão e o ponto de extremidade SCM para operações de controle do código-fonte.

| Formato | Exemplo |
| --- | --- |
| \*.appservice.\<region\>.\<DomainName\>.\<extension\> | \*.appservice.redmond.azurestack.external |
| \*.scm.appservice.<region>.<DomainName>.<extension> | \*.scm.appservice.redmond.azurestack.external |
| \*.sso.appservice.<region>.<DomainName>.<extension> | \*.sso.appservice.redmond.azurestack.external |

#### <a name="api-certificate"></a>Certificado de API

O certificado de API é colocado na função de gerenciamento. O provedor de recursos usa para ajudar a proteger chamadas de API. O certificado para publicação deve conter um assunto que corresponda à entrada DNS de API.

| Formato | Exemplo |
| --- | --- |
| api.appservice.\<region\>.\<DomainName\>.\<extension\> | api.appservice.redmond.azurestack.external |

#### <a name="publishing-certificate"></a>Certificado de publicação

O certificado para a função publicador protege o tráfego FTPS para proprietários de aplicativo ao carregar o conteúdo. O certificado para publicação deve conter um assunto que corresponda à entrada DNS FTPS.

| Formato | Exemplo |
| --- | --- |
| ftp.appservice.\<region\>.\<DomainName\>.\<extension\> | ftp.appservice.redmond.azurestack.external |

#### <a name="identity-certificate"></a>Certificado de identidade

Permite que o certificado para o aplicativo de identidade:

- Integração entre o Azure Active Directory (Azure AD) ou os serviços de Federação do Active Directory (AD FS) directory, Azure Stack e o serviço de aplicativo para dar suporte à integração com o provedor de recursos de computação.
- Únicos cenários de logon para ferramentas de desenvolvedor avançada dentro do serviço de aplicativo do Azure no Azure Stack.

O certificado para a identidade deve conter um assunto que corresponda ao formato a seguir.

| Formato | Exemplo |
| --- | --- |
| sso.appservice.\<region\>.\<DomainName\>.\<extension\> | sso.appservice.redmond.azurestack.external |


### <a name="validate-certificates"></a>Validar certificados
Antes de implantar o provedor de recursos do serviço de aplicativo, você deve [validar os certificados a serem usados](azure-stack-validate-pki-certs.md#perform-platform-as-a-service-certificate-validation) usando a ferramenta de verificador de preparação de pilha do Azure disponível na [Galeria do PowerShell](https://aka.ms/AzsReadinessChecker). A ferramenta de verificador de preparação do Azure Stack valida que os certificados PKI gerados são adequados para implantação de serviços de aplicativo. 

Como prática recomendada, ao trabalhar com qualquer um dos necessários [certificados PKI de pilha do Azure](azure-stack-pki-certs.md), você deve planejar deixar tempo suficiente para testar e emitir novamente os certificados, se necessário. 

## <a name="virtual-network"></a>Rede virtual

> [!NOTE]
> Pré-criação de uma rede virtual personalizada é opcional, pois o serviço de aplicativo do Azure no Azure Stack pode criar a rede virtual necessária, mas, em seguida, será necessário para se comunicar com o SQL e o servidor de arquivos por meio de endereços IP públicos.

Serviço de aplicativo do Azure no Azure Stack permite que você implantar o provedor de recursos em uma rede virtual existente ou permite que você crie uma rede virtual como parte da implantação. Usar uma rede virtual existente permite o uso de IPs internos para se conectar ao servidor de arquivos e do SQL server necessárias pelo serviço de aplicativo do Azure no Azure Stack. A rede virtual deve ser configurada com o intervalo de endereços e sub-redes a seguir antes de instalar o serviço de aplicativo do Azure no Azure Stack:

Rede virtual - /16

Sub-redes

- ControllersSubnet /24
- ManagementServersSubnet /24
- FrontEndsSubnet /24
- PublishersSubnet /24
- WorkersSubnet /21

## <a name="prepare-the-file-server"></a>Preparar o servidor de arquivos

O serviço de aplicativo do Azure requer o uso de um servidor de arquivos. Para implantações de produção, o servidor de arquivos deve ser configurado para ser capaz de lidar com falhas e altamente disponível.

### <a name="quickstart-template-for-file-server-for-deployments-of-azure-app-service-on-asdk"></a>Modelo de início rápido do servidor de arquivos para implantações do serviço de aplicativo do Azure no ASDK.

Kit de desenvolvimento do Azure Stack apenas para implantações, você pode usar o [modelo de implantação do Azure Resource Manager de exemplo](https://aka.ms/appsvconmasdkfstemplate) para implantar um servidor de arquivos configurado de nó único. O servidor de arquivos de nó único será um grupo de trabalho.

### <a name="quickstart-template-for-highly-available-file-server-and-sql-server"></a>Modelo de início rápido para o servidor de arquivos altamente disponível e o SQL Server

Um [modelo de início rápido de arquitetura de referência](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/appservice-fileserver-sqlserver-ha) agora está disponível, que será implantado o servidor de arquivos, SQL Server, suporte ao Active Directory infraestrutura em uma rede Virtual configurado para dar suporte a uma implantação altamente disponível do Serviço de aplicativo do Azure no Azure Stack.  

### <a name="steps-to-deploy-a-custom-file-server"></a>Etapas para implantar um servidor de arquivo personalizado

>[!IMPORTANT]
> Se você optar por implantar o serviço de aplicativo em uma rede Virtual existente, o servidor de arquivos devem ser implantado em uma sub-rede separada do serviço de aplicativo.

#### <a name="provision-groups-and-accounts-in-active-directory"></a>Provisionar grupos e contas no Active Directory

1. Crie os seguintes grupos de segurança global do Active Directory:

   - FileShareOwners
   - FileShareUsers

2. Crie as seguintes contas do Active Directory como contas de serviço:

   - FileShareOwner
   - FileShareUser

   Como uma segurança melhor prática, os usuários dessas contas (e para todas as funções web) deve ser único e têm senhas e nomes de usuário de alta segurança. Defina as senhas com as seguintes condições:

   - Habilitar **a senha nunca expira**.
   - Habilitar **usuário não pode alterar a senha**.
   - Desabilitar **usuário deve alterar a senha no próximo logon**.

3. Adicione as contas às associações de grupo, da seguinte maneira:

   - Adicione **FileShareOwner** para o **FileShareOwners** grupo.
   - Adicione **FileShareUser** para o **FileShareUsers** grupo.

#### <a name="provision-groups-and-accounts-in-a-workgroup"></a>Provisionar grupos e contas em um grupo de trabalho

>[!NOTE]
> Quando você estiver configurando um servidor de arquivos, execute todos os comandos a seguir em um **Prompt de comando do administrador**. <br>***Não use o PowerShell.***

Quando você usa o modelo do Azure Resource Manager, os usuários já estão criados.

1. Execute os seguintes comandos para criar as contas FileShareOwner e FileShareUser. Substitua `<password>` pelos seus próprios valores.

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

#### <a name="provision-the-content-share"></a>Provisionar o compartilhamento de conteúdo

O compartilhamento de conteúdo contém o conteúdo do site de locatário. O procedimento para provisionar o compartilhamento de conteúdo em um único servidor de arquivos é o mesmo para ambientes do Active Directory e o grupo de trabalho. Mas ele é diferente de um cluster de failover no Active Directory.

#### <a name="provision-the-content-share-on-a-single-file-server-active-directory-or-workgroup"></a>Provisionar o compartilhamento de conteúdo em um único servidor de arquivos (Active Directory ou grupo de trabalho)

Em um único servidor de arquivos, execute os seguintes comandos em um prompt de comando elevado. Substitua o valor de `C:\WebSites` pelos caminhos correspondentes no seu ambiente.

```DOS
set WEBSITES_SHARE=WebSites
set WEBSITES_FOLDER=C:\WebSites
md %WEBSITES_FOLDER%
net share %WEBSITES_SHARE% /delete
net share %WEBSITES_SHARE%=%WEBSITES_FOLDER% /grant:Everyone,full
```

### <a name="configure-access-control-to-the-shares"></a>Configurar controle de acesso para os compartilhamentos

Execute os seguintes comandos em um prompt de comando elevado no servidor de arquivos ou no nó de cluster de failover, que é o proprietário do recurso de cluster atual. Substitua valores em itálico por valores que são específicas para seu ambiente.

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

Para o serviço de aplicativo do Azure no Azure Stack de hospedagem e medição de bancos de dados, você deve preparar uma instância do SQL Server para manter os bancos de dados do serviço de aplicativo.

Para implantações do Kit de desenvolvimento do Azure Stack, você pode usar o SQL Server Express 2014 SP2 ou posterior.

Para fins de alta disponibilidade e de produção, você deve usar uma versão completa do SQL Server 2014 SP2 ou posterior, habilitar a autenticação de modo misto e implantar em uma [configuração de alta disponibilidade](https://docs.microsoft.com/sql/sql-server/failover-clusters/high-availability-solutions-sql-server).

Instância do SQL Server para o serviço de aplicativo do Azure no Azure Stack deve ser acessível de todas as funções de serviço de aplicativo. Você pode implantar o SQL Server na assinatura do provedor padrão no Azure Stack. Ou você pode fazer usar da infra-estrutura existente dentro da sua organização (desde que haja conectividade com o Azure Stack). Se você estiver usando uma imagem do Marketplace do Azure, lembre-se de configurar o firewall adequadamente.

>[!NOTE]
> Um número de imagens de máquina virtual de IaaS do SQL está disponível por meio do recurso de gerenciamento do Marketplace. Verifique se você sempre baixar a versão mais recente da extensão SQL IaaS antes de implantar uma VM usando um item do Marketplace. As imagens do SQL são o mesmo que as VMs do SQL que estão disponíveis no Azure. Para VMs criadas a partir dessas imagens, a extensão IaaS e correspondente aprimoramentos do portal do SQL fornecem recursos como recursos de backup e aplicação de patch automática.
>
Para qualquer uma das funções do SQL Server, você pode usar uma instância padrão ou uma instância nomeada. Se você usar uma instância nomeada, certifique-se de iniciar o serviço navegador do SQL Server e abra a porta 1434 manualmente.

>[!IMPORTANT]
> Se você optar por implantar o serviço de aplicativo em uma rede Virtual existente do SQL Server devem ser implantado em uma sub-rede separada do serviço de aplicativo e o servidor de arquivos.
>

## <a name="create-an-azure-active-directory-application"></a>Criar um aplicativo do Azure Active Directory

Configure uma entidade de serviço do Azure AD para dar suporte as seguintes operações:

- Integração do conjunto de dimensionamento de máquina virtual em camadas de trabalhador.
- SSO para ferramentas de desenvolvedor avançada e portal do Azure Functions.

Essas etapas se aplicam aos ambientes do Azure protegidos pelo AD do Azure Stack apenas.

Os administradores devem configurar o SSO:

- Habilite as ferramentas de desenvolvedor avançada dentro do serviço de aplicativo (Kudu).
- Habilite o uso da experiência do portal do Azure Functions.

Siga estas etapas:

1. Abra uma instância do PowerShell como azurestack\AzureStackAdmin.
2. Vá para o local dos scripts que você baixou e extraiu na [etapa de pré-requisito](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started).
3. [Instale o PowerShell para o Azure Stack](azure-stack-powershell-install.md).
4. Execute o **AADIdentityApp.ps1 criar** script. Quando solicitado, insira a ID do locatário do Azure AD que você está usando para sua implantação do Azure Stack. Por exemplo, digite **myazurestack.onmicrosoft.com**.
5. No **credencial** janela, insira sua conta de administrador de serviço do Azure AD e a senha. Selecione **OK**.
6. Insira o caminho do arquivo de certificado e a senha do certificado para o [certificado criado anteriormente](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started). O certificado criado para esta etapa por padrão é **sso.appservice.local.azurestack.external.pfx**.
7. O script cria um novo aplicativo na instância do AD do Azure do locatário. Anote a ID do aplicativo que é retornada na saída do PowerShell. Você precisará dessas informações durante a instalação.
8. Abra uma nova janela do navegador e entrar para o [portal do Azure](https://portal.azure.com) como administrador de serviço do Azure Active Directory.
9. Abra o provedor de recursos do Azure AD.
10. Selecione **registros de aplicativo**.
11. Procure a ID do aplicativo retornada como parte da etapa 7. Um aplicativo de serviço de aplicativo está listado.
12. Selecione **aplicativo** na lista.
13. Escolha a opção **Configurações**.
14. Selecione **permissões necessárias** > **conceder permissões** > **Sim**.

```PowerShell
    Create-AADIdentityApp.ps1
```

| Parâmetro | Obrigatório ou opcional | Valor padrão | Descrição |
| --- | --- | --- | --- |
| DirectoryTenantName | Obrigatório | Null | ID de locatário do Azure AD. Forneça o GUID ou uma cadeia de caracteres. Um exemplo é myazureaaddirectory.onmicrosoft.com. |
| AdminArmEndpoint | Obrigatório | Null | Ponto de extremidade de administração do Azure Resource Manager. Um exemplo é adminmanagement.local.azurestack.external. |
| TenantARMEndpoint | Obrigatório | Null | Ponto de extremidade do locatário do Azure Resource Manager. Um exemplo é management.local.azurestack.external. |
| AzureStackAdminCredential | Obrigatório | Null | Credencial de administrador de serviço de AD do Azure. |
| CertificateFilePath | Obrigatório | Null | **Caminho completo** para o arquivo de certificado do aplicativo de identidade gerado anteriormente. |
| CertificatePassword | Obrigatório | Null | Senha que ajuda a proteger a chave privada do certificado. |
| Ambiente | Opcional | AzureCloud | O nome do ambiente de nuvem com suporte na qual o serviço do Graph do Azure Active Directory de destino está disponível.  Valores permitidos: 'AzureCloud', 'AzureChinaCloud', 'AzureUSGovernment', 'AzureGermanCloud'.|

## <a name="create-an-active-directory-federation-services-application"></a>Criar um aplicativo de serviços de Federação do Active Directory

Para ambientes do Azure Stack protegidos pelo AD FS, você deve configurar uma entidade de serviço do AD FS para dar suporte as seguintes operações:

- Integração do conjunto de dimensionamento de máquina virtual em camadas de trabalhador.
- SSO para ferramentas de desenvolvedor avançada e portal do Azure Functions.

Os administradores devem configurar o SSO:

- Configure uma entidade de serviço para a integração de conjunto de escala de máquina virtual em camadas de trabalhador.
- Habilite as ferramentas de desenvolvedor avançada dentro do serviço de aplicativo (Kudu).
- Habilite o uso da experiência do portal do Azure Functions.

Siga estas etapas:

1. Abra uma instância do PowerShell como azurestack\AzureStackAdmin.
2. Vá para o local dos scripts que você baixou e extraiu na [etapa de pré-requisito](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started).
3. [Instale o PowerShell para o Azure Stack](azure-stack-powershell-install.md).
4. Execute o **ADFSIdentityApp.ps1 criar** script.
5. No **credencial** janela, insira sua conta de administrador de nuvem do AD FS e a senha. Selecione **OK**.
6. Forneça o caminho do arquivo de certificado e a senha do certificado para o [certificado criado anteriormente](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started). O certificado criado para esta etapa por padrão é **sso.appservice.local.azurestack.external.pfx**.

```PowerShell
    Create-ADFSIdentityApp.ps1
```

| Parâmetro | Obrigatório ou opcional | Valor padrão | Descrição |
| --- | --- | --- | --- |
| AdminArmEndpoint | Obrigatório | Null | Ponto de extremidade de administração do Azure Resource Manager. Um exemplo é adminmanagement.local.azurestack.external. |
| PrivilegedEndpoint | Obrigatório | Null | O ponto de extremidade com privilégios. Um exemplo é AzS-ERCS01. |
| CloudAdminCredential | Obrigatório | Null | Credencial de conta de domínio para que os administradores de nuvem do Azure Stack. Um exemplo é Azurestack\CloudAdmin. |
| CertificateFilePath | Obrigatório | Null | **Caminho completo** ao arquivo do PFX de certificado do aplicativo de identidade. |
| CertificatePassword | Obrigatório | Null | Senha que ajuda a proteger a chave privada do certificado. |

## <a name="next-steps"></a>Próximas etapas

[Instalar o provedor de recursos do serviço de aplicativo](azure-stack-app-service-deploy.md)
