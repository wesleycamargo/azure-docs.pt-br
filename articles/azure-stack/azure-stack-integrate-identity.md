---
title: "Integração do data center do Azure pilha - identidade"
description: Saiba como integrar a pilha do Azure AD FS com o data center do AD FS
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: article
ms.date: 12/12/2017
ms.author: mabrigg
keywords: 
ms.openlocfilehash: 642ed3298eec0bab5515df117c0310786358e417
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2017
---
# <a name="azure-stack-datacenter-integration---identity"></a>Integração do data center do Azure pilha - identidade

*Aplica-se a: sistemas integrados de pilha do Azure*

Você pode implantar a pilha do Azure usando o Azure Active Directory (AD do Azure) ou os serviços de Federação do Active Directory (AD FS) como os provedores de identidade. Antes de implantar a pilha do Azure, você deve fazer a escolha. Implantação usando o AD FS também é chamada de como implantar o Azure pilha no modo desconectado.

A tabela a seguir mostra as diferenças entre as opções de dois identidade:

||Fisicamente desconectada|Fisicamente conectados|
|---------|---------|---------|
|Cobrança|Deve ser a capacidade<br> Enterprise Agreement (EA) somente|Capacidade ou pagamento como você-uso<br>EA ou provedor de soluções de nuvem (CSP)|
|Identidade|Deve ser do AD FS|Azure AD ou AD FS|
|Distribuição do Marketplace|Não disponível no momento|Suportado<br>BYOL de licenciamento|
|Registro|Recomendado, requer uma mídia removível<br> e um dispositivo conectado separado.|Automatizada|
|Patch e atualização|Necessário, requer uma mídia removível<br> e um dispositivo conectado separado.|Pacote de atualização pode ser baixado diretamente<br> da Internet para a pilha do Azure.|

> [!IMPORTANT]
> Você não pode alternar o provedor de identidade sem reimplantar a solução do Azure pilha inteira.

## <a name="active-directory-federation-services-and-graph"></a>Gráfico e os serviços de Federação do active Directory

Implantando com o AD FS permite que as identidades em uma floresta existente do Active Directory para autenticar com recursos na pilha do Azure. Essa floresta existente do Active Directory requer uma implantação do AD FS para permitir a criação de uma relação de confiança de Federação do AD FS.

A autenticação é uma parte da identidade. Para gerenciar a função com base em acesso RBAC (controle) na pilha do Azure, o componente do Graph deve ser configurado. Quando o acesso a um recurso é delegado, o componente gráfico procura a conta de usuário na floresta existente do Active Directory usando o protocolo LDAP.

![Arquitetura de pilha do AD FS do Azure](media/azure-stack-integrate-identity/Azure-Stack-ADFS-architecture.png)

O AD FS existente é o conta Serviço de token segurança (STS) que envia solicitações para a pilha do Azure AD FS (o recurso STS). Na pilha do Azure, automação cria a relação de confiança do provedor de declarações com o ponto de extremidade de metadados para o AD FS existente.

No AD FS existente, uma terceira parte confiável deve ser configurada. Essa etapa não é feita através da automação e deve ser configurada pelo operador. O ponto de extremidade de metadados de pilha do Azure está documentado no arquivo AzureStackStampDeploymentInfo.JSON ou por meio do ponto de extremidade com privilégios ao executar o comando `Get-AzureStackInfo`.

A configuração de confiança terceira parte confiável também exige que você configure as regras de transformação de declaração que são fornecidas pela Microsoft.

Para a configuração de gráfico, uma conta de serviço deve ser fornecido que tem permissão no Active Directory existente de leitura. Essa conta é necessária como entrada para a automação habilitar cenários RBAC.

Para a última etapa, um novo proprietário é configurado para a assinatura do provedor padrão. Essa conta tem acesso completo a todos os recursos quando conectado ao portal do administrador de pilha do Azure.

Requisitos:


|Componente|Requisito|
|---------|---------|
|Grafo|Microsoft Active Directory 2012/2012 R2/2016|
|AD FS|Windows Server 2012/2012 R2/2016|

## <a name="setting-up-graph-integration"></a>Configurar a integração de gráfico

As informações a seguir são necessárias como entradas para os parâmetros de automação:


|Parâmetro|Descrição|Exemplo|
|---------|---------|---------|
|CustomADGlobalCatalog|FQDN do destino da floresta do Active Directory<br>Se você deseja integrar com|Contoso.com|
|CustomADAdminCredentials|Um usuário com permissão de leitura de LDAP|YOURDOMAIN\graphservice|

### <a name="create-user-account-in-the-existing-active-directory-optional"></a>Criar conta de usuário no Active Directory existente (opcional)

Opcionalmente, você pode criar uma conta para o serviço de gráfico no Active Directory existente. Execute esta etapa se você ainda não tiver uma conta que você deseja usar.

1. No Active Directory existente, crie a seguinte conta de usuário (recomendação):
   - **Nome de usuário**: graphservice
   - **Senha**: use uma senha forte<br>Configure a senha para nunca expirar.

   Nenhuma permissão especial ou a associação é necessária.

#### <a name="trigger-automation-to-configure-graph"></a>Automação de gatilho para configurar o gráfico

Para esse procedimento, use um computador em sua rede de datacenter que pode se comunicar com o ponto de extremidade com privilégios na pilha do Azure.

2. Abra uma sessão do Windows PowerShell com privilégios elevados (Executar como administrador) e conecte-se para o endereço IP do ponto de extremidade com privilégios. Usar as credenciais para **CloudAdmin** para autenticar.

   ```powershell
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

3. Agora que você está conectado ao ponto de extremidade com privilégios, execute o seguinte comando: 

   ```powershell
   Register-DirectoryService -CustomADGlobalCatalog contoso.com
   ```

   Quando solicitado, especifique a credencial da conta de usuário que você deseja usar para o serviço de gráfico (como graphservice).

   > [!IMPORTANT]
   > Aguarde até que as credenciais de pop-up (Get-Credential não há suporte para no ponto de extremidade com privilégios) e insira as credenciais de conta de serviço do Graph.

#### <a name="graph-protocols-and-ports"></a>Gráfico de protocolos e portas

Serviço de gráfico na pilha do Azure usa os seguintes protocolos e portas para se comunicar com o destino do Active Directory:

|Tipo|Porta|Protocolo|
|---------|---------|---------|
|LDAP|389|TCP E UDP|
|LDAP SSL|636|TCP|
|LDAP GC|3268|TCP|
|LDAP SSL DE GC|3269|TCP|

## <a name="setting-up-ad-fs-integration-by-downloading-federation-metadata"></a>Configurar a integração do AD FS baixando metadados de Federação

As informações a seguir são necessárias como entrada para os parâmetros de automação:

|Parâmetro|Descrição|Exemplo|
|---------|---------|---------|
|CustomAdfsName|Nome do provedor de declarações. <cr>Parece dessa forma na página de aterrissagem do AD FS.|Contoso|
|CustomAD<br>FSFederationMetadataEndpointUri|Link de metadados de Federação|https://AD01.contoso.com/federationmetadata/2007-06/federationmetadata.XML|


### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>Automação de gatilho para configurar a confiança do provedor de declarações na pilha do Azure

Para esse procedimento, use um computador que possa se comunicar com o ponto de extremidade com privilégios na pilha do Azure. Espera-se que o certificado usado pela conta **STS AD FS** confiável pela pilha do Azure.

1. Abra uma sessão do Windows PowerShell com privilégios elevados e conectar-se ao ponto de extremidade com privilégios.

   ```powershell
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Agora que você está conectado ao ponto de extremidade com privilégios, execute o comando a seguir usando os parâmetros adequados para seu ambiente:

   ```powershell
   Register-CustomAdfs -CustomAdfsName Contoso -CustomADFSFederationMetadataEndpointUri https://win-SQOOJN70SGL.contoso.com/federationmetadata/2007-06/federationmetadata.xml
   ```

3. Execute o seguinte comando para atualizar o proprietário da assinatura do provedor padrão, usando os parâmetros adequados para seu ambiente:

   ```powershell
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

## <a name="setting-up-ad-fs-integration-by-providing-federation-metadata-file"></a>Configurar a integração do AD FS, fornecendo o arquivo de metadados de Federação

Use este método se alguma das seguintes condições for verdadeira:

- A cadeia de certificados é diferente do AD FS em comparação comparado todos os outros pontos de extremidade na pilha do Azure.
- Não há nenhuma conectividade de rede para o servidor do AD FS existente da instância do AD FS da pilha do Azure.

As informações a seguir são necessárias como entrada para os parâmetros de automação:


|Parâmetro|Descrição|Exemplo|
|---------|---------|---------|
|CustomAdfsName|Nome do provedor de declarações. Parece que forma na página de aterrissagem do AD FS.|Contoso|
|CustomADFSFederationMetadataFile|Arquivo de metadados de Federação|https://AD01.contoso.com/federationmetadata/2007-06/federationmetadata.XML|

### <a name="create-federation-metadata-file"></a>Criar arquivo de metadados de Federação

Para o procedimento a seguir, você deve usar um computador que tenha conectividade de rede para a implantação do AD FS existente, que se torna a conta do STS. Além disso, os certificados necessários devem ser instalados.

1. Abra uma sessão do Windows PowerShell com privilégios elevados e execute o seguinte comando, usando os parâmetros adequados para seu ambiente:

   ```powershell
   [XML]$Metadata = Invoke-WebRequest -URI https://win-SQOOJN70SGL.contoso.com/federationmetadata/2007-06/federationmetadata.xml -UseBasicParsing

   $Metadata.outerxml|out-file c:\metadata.xml
   ```

2. Copie o arquivo de metadados para um compartilhamento que é acessível do ponto de extremidade com privilégios.


### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>Automação de gatilho para configurar a confiança do provedor de declarações na pilha do Azure

Para esse procedimento, use um computador que possa se comunicar com o ponto de extremidade com privilégios na pilha do Azure.

1. Abra uma sessão do Windows PowerShell com privilégios elevados e conectar-se ao ponto de extremidade com privilégios.

   ```powershell
   $creds=Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Agora que você está conectado ao ponto de extremidade com privilégios, execute o comando a seguir usando os parâmetros adequados para seu ambiente:

   ```powershell
   Register-CustomAdfs -CustomAdfsName Contoso – CustomADFSFederationMetadataFile \\share\metadataexample.xml
   ```

3. Execute o seguinte comando para atualizar o proprietário da assinatura do provedor padrão, usando os parâmetros adequados para seu ambiente:

   ```powershell
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

## <a name="configure-relying-party-on-existing-ad-fs-deployment-account-sts"></a>Configure a terceira parte confiável na implantação do AD FS existente (conta STS)

A Microsoft fornece um script que define a terceira parte confiável, incluindo as regras de transformação de declaração. Usando o script é opcional, você pode executar manualmente os comandos.

Você pode baixar o script de auxiliar de [ferramentas do Azure pilha](https://github.com/Azure/AzureStack-Tools/tree/vnext/DatacenterIntegration/Identity) no Github.

Se você optar por executar manualmente os comandos, siga estas etapas:

1. Copie o conteúdo a seguir em um arquivo. txt (por exemplo, salvo como c:\ClaimRules.txt) no membro de instância ou farm do AD FS do seu data center:

   ```text
   @RuleTemplate = "LdapClaims"
   @RuleName = "Name claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"), query = ";userPrincipalName;{0}", param = c.Value);

   @RuleTemplate = "LdapClaims"
   @RuleName = "UPN claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"), query = ";userPrincipalName;{0}", param = c.Value);

   @RuleTemplate = "LdapClaims"
   @RuleName = "ObjectID claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid"]
   => issue(Type = "http://schemas.microsoft.com/identity/claims/objectidentifier", Issuer = c.Issuer, OriginalIssuer = c.OriginalIssuer, Value = c.Value, ValueType = c.ValueType);

   @RuleName = "Family Name and Given claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname", "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname"), query = ";sn,givenName;{0}", param = c.Value);

   @RuleTemplate = "PassThroughClaims"
   @RuleName = "Pass through all Group SID claims"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
   => issue(claim = c);

   @RuleTemplate = "PassThroughClaims"
   @RuleName = "Pass through all windows account name claims"
   c:[Type == http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname]
   => issue(claim = c);
   ```

2. Para habilitar a autenticação baseada em formulários do Windows, abra uma sessão do Windows PowerShell como um usuário com privilégios elevados e execute o seguinte comando:

   ```powershell
   Set-AdfsProperties -WIASupportedUserAgents @("MSAuthHost/1.0/In-Domain","MSIPC","Windows Rights Management Client","Kloud")
   ```

3. Para adicionar a terceira parte confiável, execute o seguinte comando do Windows PowerShell na sua instância do AD FS ou um membro do farm. Certifique-se de atualizar o ponto de extremidade do AD FS e apontar para o arquivo criado na etapa 1.

   **Para o AD FS 2016**

   ```powershell
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true -AccessControlPolicyName "Permit everyone"
   ```

   **Para o AD FS 2012/2012 R2**

   ```powershell
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true
   ```

   > [!IMPORTANT]
   > Você deve usar o snap-in do MMC do AD FS para configurar as regras de autorização de emissão, ao usar o Windows Server 2012 ou 2012 R2 AD FS.

4. Quando você usa o Internet Explorer ou o navegador Edge para acessar a pilha do Azure, você deve ignorar associações de token. Caso contrário, as tentativas de logon falharem. Em sua instância do AD FS ou um membro do farm, execute o seguinte comando:

   ```powershell
   Set-AdfsProperties -IgnoreTokenBinding $true
   ```

5. Para habilitar os tokens de atualização, abra uma sessão do Windows PowerShell com privilégios elevados e execute o seguinte comando:

   ```powershell
   Set-ADFSRelyingPartyTrust -TargetName AzureStack -TokenLifeTime 1440
   ```

## <a name="spn-creation"></a>Criação de SPN

Há muitos cenários que exigem o uso de um nome principal de serviço (SPN) para autenticação. A seguir estão alguns exemplos:

- Uso de CLI com a implantação do AD FS da pilha do Azure
- Pacote de gerenciamento do System Center para Azure pilha quando implantado com o AD FS
- Provedores de recursos na pilha do Azure quando implantado com o AD FS
- Vários aplicativos
- Você precisa de um logon não interativo

Para obter mais informações sobre como criar um SPN, consulte [criar entidade de serviço do AD FS](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#create-service-principal-for-ad-fs).


## <a name="troubleshooting"></a>Solucionar problemas

### <a name="configuration-rollback"></a>Reversão de configuração

Se ocorrer um erro que deixa o ambiente em um estado em que você não pode autenticar, uma opção de reversão está disponível.

1. Abra uma sessão do Windows PowerShell com privilégios elevados e execute os seguintes comandos:

   ```powershell
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Em seguida, execute o seguinte cmdlet:

   ```powershell
   Reset-DatacenterIntegationConfiguration
   ```

   Depois de executar a ação de reversão, todas as alterações de configuração são revertidas. Somente a autenticação com o interno **CloudAdmin** usuário é possível.

   > [!IMPORTANT]
   > Você deve configurar o proprietário original da assinatura do provedor padrão

   ```powershell
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "azurestackadmin@[Internal Domain]"
   ```

### <a name="collecting-additional-logs"></a>Coletando logs adicionais

Se qualquer um dos cmdlets falhar, você poderá coletar logs adicionais usando o `Get-Azurestacklogs` cmdlet.

1. Abra uma sessão do Windows PowerShell com privilégios elevados e execute os seguintes comandos:

   ```powershell
   $creds = Get-Credential
   Enter-pssession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Em seguida, execute o seguinte cmdlet:

   ```powershell
   Get-AzureStackLog -OutputPath \\myworstation\AzureStackLogs -FilterByRole ECE
   ```


## <a name="next-steps"></a>Próximas etapas

[Azure pilha datacenter integração - publicar pontos de extremidade](azure-stack-integrate-endpoints.md)
