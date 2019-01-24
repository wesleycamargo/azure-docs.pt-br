---
title: Integração do datacenter do Azure Stack - identidade
description: Saiba como integrar o Azure Stack AD FS com o seu datacenter do AD FS
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 01/23/19
ms.author: patricka
ms.reviewer: thoroet
keywords: ''
ms.openlocfilehash: 4f599379de07a9628ee81425ddac2374411bdf97
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54852755"
---
# <a name="azure-stack-datacenter-integration---identity"></a>Integração do datacenter do Azure Stack - identidade
Você pode implantar o Azure Stack usando o Azure Active Directory (Azure AD) ou os serviços de Federação do Active Directory (AD FS) como os provedores de identidade. Você deve fazer a escolha antes de implantar o Azure Stack. Implantação usando o AD FS também é chamada da implantação do Azure Stack no modo desconectado.

A tabela a seguir mostra as diferenças entre as opções de dois identidade:

||Desconectado da internet|Conectado à internet|
|---------|---------|---------|
|Cobrança|Deve ser a capacidade<br> Enterprise Agreement (EA) apenas|Capacidade ou pagamento-como-uso<br>EA ou provedor de soluções de nuvem (CSP)|
|Identidade|Deve ser o AD FS|Azure AD ou AD FS|
|Marketplace |Com suporte<br>Licenciamento de BYOL|Com suporte<br>Licenciamento de BYOL|
|Registro|Obrigatório, requer mídia removível<br> e um dispositivo conectado separado.|Automatizado|
|Patches e atualizações|Obrigatório, requer mídia removível<br> e um dispositivo conectado separado.|Pacote de atualização pode ser baixada diretamente<br> da Internet para o Azure Stack.|

> [!IMPORTANT]
> É possível alternar o provedor de identidade sem reimplantar a solução inteira do Azure Stack.

## <a name="active-directory-federation-services-and-graph"></a>Gráfico e os serviços de Federação do Active Directory

Implantando com o AD FS permite que as identidades em uma floresta existente do Active Directory para autenticar com recursos no Azure Stack. Essa floresta existente do Active Directory requer uma implantação do AD FS para permitir a criação de uma relação de confiança de Federação do AD FS.

Autenticação é uma parte da identidade. Para gerenciar com base em acesso RBAC (controle) no Azure Stack, o componente gráfico deve ser configurado. Quando o acesso a um recurso é delegado, o componente gráfico procura a conta de usuário na floresta do Active Directory existente usando o protocolo LDAP.

![Arquitetura de pilha do AD FS do Azure](media/azure-stack-integrate-identity/Azure-Stack-ADFS-architecture.png)

O AD FS existente é o conta Serviço de token segurança (STS) que envia declarações para o Azure Stack AD FS (o recurso STS). No Azure Stack, a automação cria a relação de confiança do provedor de declarações com o ponto de extremidade de metadados para o AD FS existente.

No AD FS existente, uma terceira parte confiável deve ser configurada. Esta etapa não é feita pela automação e deve ser configurada pelo operador. O ponto de extremidade de metadados do Azure Stack está documentado no arquivo AzureStackStampDeploymentInfo.JSON ou por meio do ponto de extremidade com privilégios, executando o comando `Get-AzureStackInfo`.

A configuração de confiança da terceira parte confiável também exige que você configure as regras de transformação de declaração são fornecidas pela Microsoft.

Para a configuração do gráfico, uma conta de serviço deve ser fornecida que tem permissão de leitura no Active Directory existente. Essa conta é necessária como entrada para a automação habilitar cenários RBAC.

Para a última etapa, um novo proprietário é configurado para a assinatura de provedor padrão. Essa conta tem acesso completo a todos os recursos quando conectado ao portal do administrador do Azure Stack.

Requisitos:

|Componente|Requisito|
|---------|---------|
|Grafo|Microsoft Active Directory 2012/2012 R2/2016|
|AD FS|Windows Server 2012/2012 R2/2016|

## <a name="setting-up-graph-integration"></a>Configurar a integração do Graph

Gráfico só dá suporte à integração com uma única floresta do Active Directory. Se existirem várias florestas, apenas a floresta especificada na configuração do será usada para buscar os usuários e grupos.

As seguintes informações são necessárias como entradas para os parâmetros de automação:

|Parâmetro|DESCRIÇÃO|Exemplo|
|---------|---------|---------|
|CustomADGlobalCatalog|FQDN do destino da floresta do Active Directory<br>que você deseja integrar ao|Contoso.com|
|CustomADAdminCredentials|Um usuário com permissão de leitura LDAP|YOURDOMAIN\graphservice|

### <a name="configure-active-directory-sites"></a>Configurar os Sites do Active Directory

Para implantações do Active Directory tendo vários sites, configure o Site do Active Directory mais próximo à sua implantação do Azure Stack. A configuração evita a necessidade do serviço do Graph do Azure Stack resolver consultas usando um servidor de Catálogo Global em um site remoto.

Adicionar o Azure Stack [rede VIP pública](azure-stack-network.md#public-vip-network) sub-rede para o Site do AD do Azure mais próxima para o Azure Stack. Por exemplo, se o Active Directory tem dois sites de Seattle e Redmond com o Azure Stack implantado no site de Seattle, você adicionaria a sub-rede de rede do VIP público do Azure Stack para o site do Azure AD de Seattle.

Para obter mais informações sobre Sites do Active Directory, consulte [Projetando a topologia de site](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology).

> [!Note]  
> Se o seu Active Directory consistem em um único Site, você pode ignorar esta etapa. Caso você tenha uma sub-rede pega-tudo configurada validar que a sub-rede de rede do VIP público do Azure Stack não faz parte dele.

### <a name="create-user-account-in-the-existing-active-directory-optional"></a>Criar conta de usuário no Active Directory existente (opcional)

Opcionalmente, você pode criar uma conta para o serviço de gráfico no Active Directory existente. Execute esta etapa se você ainda não tiver uma conta que você deseja usar.

1. No Active Directory existente, crie a seguinte conta de usuário (recomendação):
   - **Nome de usuário**: graphservice
   - **Senha**: use uma senha forte<br>Configure a senha para nunca expirar.

   Nenhuma permissão especial ou a associação é necessária.

#### <a name="trigger-automation-to-configure-graph"></a>Automação de gatilho para configurar o gráfico

Para este procedimento, use um computador em sua rede de datacenter que pode se comunicar com o ponto de extremidade privilegiado no Azure Stack.

1. Abra uma sessão do Windows PowerShell com privilégios elevados (Executar como administrador) e conecte-se para o endereço IP do ponto de extremidade com privilégios. Usar as credenciais para **CloudAdmin** para autenticar.

   ```PowerShell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Agora que você está conectado ao ponto de extremidade com privilégios, execute o seguinte comando: 

   ```PowerShell  
   Register-DirectoryService -CustomADGlobalCatalog contoso.com
   ```

   Quando solicitado, especifique a credencial da conta de usuário que você deseja usar para o serviço de gráfico (como graphservice). A entrada para o cmdlet Register-DirectoryService deve ser o nome da floresta / raiz de domínio na floresta, em vez de qualquer outro domínio na floresta.

   > [!IMPORTANT]
   > Aguarde até que as credenciais de pop-up (Get-Credential não é suportado no ponto de extremidade com privilégios) e insira as credenciais de conta de serviço do Graph.

#### <a name="graph-protocols-and-ports"></a>Portas e protocolos de gráfico

Serviço do Graph no Azure Stack usa os seguintes protocolos e portas para se comunicar com um servidor de catálogo de Global (GC) gravável e o Centro de distribuição de chaves (KDC) que pode processar solicitações de logon no destino de floresta do Active Directory.

Serviço do Graph no Azure Stack usa os seguintes protocolos e portas para se comunicar com o destino do Active Directory:

|Tipo|Porta|Protocolo|
|---------|---------|---------|
|LDAP|389|TCP E UDP|
|LDAP SSL|636|TCP|
|LDAP GC|3268|TCP|
|LDAP SSL DE GC|3269|TCP|

## <a name="setting-up-ad-fs-integration-by-downloading-federation-metadata"></a>Como configurar a integração do AD FS, baixando metadados de Federação

As informações a seguir são necessárias como entrada para os parâmetros de automação:

|Parâmetro|DESCRIÇÃO|Exemplo|
|---------|---------|---------|
|CustomAdfsName|Nome do provedor de declarações.<br>Ele aparece dessa forma, na página de aterrissagem do AD FS.|Contoso|
|CustomAD<br>FSFederationMetadataEndpointUri|Link de metadados de Federação|https://ad01.contoso.com/federationmetadata/2007-06/federationmetadata.xml|


### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>Automação de gatilho para configurar a relação de confiança de provedor de declarações no Azure Stack

Para este procedimento, use um computador que possa se comunicar com o ponto de extremidade privilegiado no Azure Stack. Espera-se que o certificado usado pela conta **STS AD FS** é confiável pelo Azure Stack.

1. Abra uma sessão do Windows PowerShell com privilégios elevados e conecte-se ao ponto de extremidade com privilégios.

   ```PowerShell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Agora que você está conectado ao ponto de extremidade com privilégios, execute o seguinte comando usando os parâmetros adequados para seu ambiente:

   ```PowerShell  
   Register-CustomAdfs -CustomAdfsName Contoso -CustomADFSFederationMetadataEndpointUri https://win-SQOOJN70SGL.contoso.com/federationmetadata/2007-06/federationmetadata.xml
   ```

3. Execute o seguinte comando para atualizar o proprietário da assinatura do provedor padrão, usando os parâmetros apropriados para seu ambiente:

   ```PowerShell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

## <a name="setting-up-ad-fs-integration-by-providing-federation-metadata-file"></a>Como configurar a integração do AD FS, fornecendo o arquivo de metadados de Federação

Começando com a versão 1807, use este método se alguma das seguintes condições é verdadeira:

- A cadeia de certificados é diferente do AD FS em comparação comparado todos os outros pontos de extremidade no Azure Stack.
- Não há nenhuma conectividade de rede no servidor do AD FS existente da instância do AD FS do Azure Stack.

As informações a seguir são necessárias como entrada para os parâmetros de automação:


|Parâmetro|DESCRIÇÃO|Exemplo|
|---------|---------|---------|
|CustomAdfsName|Nome do provedor de declarações. Ele aparece dessa forma, na página de aterrissagem do AD FS.|Contoso|
|CustomADFSFederationMetadataFileContent|Conteúdo de metadados|$using:federationMetadataFileContent|

### <a name="create-federation-metadata-file"></a>Criar arquivo de metadados de Federação

Para o procedimento a seguir, você deve usar um computador que tenha conectividade de rede para a implantação do AD FS existente, que se torna o STS da conta. Além disso, os certificados necessários devem ser instalados.

1. Abra uma sessão do Windows PowerShell com privilégios elevados e execute o seguinte comando, usando os parâmetros adequados para seu ambiente:

   ```PowerShell  
    $url = "https://win-SQOOJN70SGL.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml"
    $webclient = New-Object System.Net.WebClient
    $webclient.Encoding = [System.Text.Encoding]::UTF8
    $metadataAsString = $webclient.DownloadString($url)
    Set-Content -Path c:\metadata.xml -Encoding UTF8 -Value $metadataAsString
   ```

2. Copie o arquivo de metadados para um computador que possa se comunicar com o ponto de extremidade com privilégios.

### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>Automação de gatilho para configurar a relação de confiança de provedor de declarações no Azure Stack

Para este procedimento, use um computador que possa se comunicar com o ponto de extremidade privilegiado no Azure Stack e tem acesso ao arquivo de metadados que você criou na etapa anterior.

1. Abra uma sessão do Windows PowerShell com privilégios elevados e conecte-se ao ponto de extremidade com privilégios.

   ```PowerShell  
   $federationMetadataFileContent = get-content c:\metadata.xml
   $creds=Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Agora que você está conectado ao ponto de extremidade com privilégios, execute o seguinte comando usando os parâmetros adequados para seu ambiente:

    ```PowerShell
    Register-CustomAdfs -CustomAdfsName Contoso -CustomADFSFederationMetadataFileContent $using:federationMetadataFileContent
    ```

3. Execute o seguinte comando para atualizar o proprietário da assinatura do provedor padrão, usando os parâmetros apropriados para seu ambiente:

   ```PowerShell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

   > [!Note]  
   > Quando você gira o certificado existente do AD FS (conta de STS) você deve configurar a integração do AD FS novamente. Você deve configurar a integração com o mesmo que o ponto de extremidade de metadados está acessível ou foi configurada, fornecendo o arquivo de metadados.

## <a name="configure-relying-party-on-existing-ad-fs-deployment-account-sts"></a>Configurar a terceira parte confiável na implantação existente do AD FS (STS da conta)

A Microsoft fornece um script que configura a terceira parte confiável, incluindo as regras de transformação de declaração. Usando o script é opcional, pois você pode executar os comandos manualmente.

Você pode baixar o script auxiliar [as ferramentas do Azure Stack](https://github.com/Azure/AzureStack-Tools/tree/vnext/DatacenterIntegration/Identity) no GitHub.

Se você decidir executar manualmente os comandos, siga estas etapas:

1. Copie o seguinte conteúdo para um arquivo. txt (por exemplo, salvo como c:\ClaimRules.txt) no membro de instância ou farm do AD FS do seu data center:

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
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
   => issue(claim = c);
   ```

2. Validar que a autenticação baseada em formulários do Windows para extranet e intranet está habilitada. Primeiro, valide se seu já habilitado executando o seguinte cmdlet:

   ```PowerShell  
   Get-AdfsAuthenticationProvider | where-object { $_.name -eq "FormsAuthentication" } | select Name, AllowedForPrimaryExtranet, AllowedForPrimaryIntranet
   ```

    > [!Note]  
    > O Windows integrado autenticação (WIA) podem cadeias de caracteres de agente do usuário com suporte desatualizado para AD FS implantação pode exigir a ser atualizado para dar suporte a clientes mais recentes. Você pode ler mais sobre como atualizar a WIA suporte para cadeias de caracteres de agente de usuário no artigo [Configurando autenticação baseada em formulários de intranet para dispositivos que não dão suporte a WIA](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-intranet-forms-based-authentication-for-devices-that-do-not-support-wia).<br>As etapas para habilitar a política de autenticação baseada em formulário estão documentadas no artigo [configurar políticas de autenticação](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-authentication-policies).

3. Para adicionar a terceira parte confiável, execute o seguinte comando do Windows PowerShell em sua instância do AD FS ou um membro do farm. Certifique-se de atualizar o ponto de extremidade do AD FS e apontar para o arquivo criado na etapa 1.

   **Para o AD FS 2016**

   ```PowerShell  
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true -AccessControlPolicyName "Permit everyone" -TokenLifeTime 1440
   ```

   **Para o AD FS 2012/2012 R2**

   ```PowerShell  
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true -TokenLifeTime 1440
   ```

   > [!IMPORTANT]  
   > Você deve usar o snap-in do MMC do AD FS para configurar as regras de autorização de emissão, ao usar o Windows Server 2012 ou 2012 R2 AD FS.

4. Quando você usa o Internet Explorer ou o navegador Microsoft Edge para acessar o Azure Stack, você deve ignorar as associações de token. Caso contrário, as tentativas de logon falham. Em sua instância do AD FS ou um membro do farm, execute o seguinte comando:

   > [!note]  
   > Essa etapa não é aplicável ao usar o Windows Server 2012 ou 2012 R2 AD FS. É seguro ignorar esse comando e continue com a integração.

   ```PowerShell  
   Set-AdfsProperties -IgnoreTokenBinding $true
   ```

## <a name="spn-creation"></a>Criação de SPN

Há muitos cenários que exigem o uso de um nome de entidade de serviço (SPN) para autenticação. A seguir está alguns exemplos:

- Uso da CLI com a implantação do AD FS do Azure Stack
- Pacote de gerenciamento do System Center para o Azure Stack quando implantado com o AD FS
- Provedores de recursos no Azure Stack quando implantado com o AD FS
- Vários aplicativos
- Você precisar de uma entrada não interativa

> [!Important]  
> O AD FS dá suporte apenas a sessões de logon interativo. Se você precisar de um logon não interativo para um cenário de automatizado, você deve usar um SPN.

Para obter mais informações sobre como criar um SPN, consulte [criar entidade de serviço do AD FS](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#create-service-principal-for-ad-fs).


## <a name="troubleshooting"></a>solução de problemas

### <a name="configuration-rollback"></a>Reversão de configuração

Se ocorrer um erro que deixa o ambiente em um estado em que você não poderá mais autenticar, uma opção de reversão está disponível.

1. Abra uma sessão do Windows PowerShell com privilégios elevados e execute os seguintes comandos:

   ```PowerShell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Em seguida, execute o seguinte cmdlet:

   ```PowerShell  
   Reset-DatacenterIntegrationConfiguration
   ```

   Depois de executar a ação de reversão, todas as alterações de configuração são revertidas. Somente a autenticação com o interno **CloudAdmin** usuário é possível.

   > [!IMPORTANT]
   > Você deve configurar o proprietário original da assinatura do provedor padrão

   ```PowerShell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "azurestackadmin@[Internal Domain]"
   ```

### <a name="collecting-additional-logs"></a>Coletando logs adicionais

Se qualquer um dos cmdlets falharem, você pode coletar logs adicionais usando o `Get-Azurestacklogs` cmdlet.

1. Abra uma sessão do Windows PowerShell com privilégios elevados e execute os seguintes comandos:

   ```PowerShell  
   $creds = Get-Credential
   Enter-pssession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Em seguida, execute o seguinte cmdlet:

   ```PowerShell  
   Get-AzureStackLog -OutputPath \\myworstation\AzureStackLogs -FilterByRole ECE
   ```


## <a name="next-steps"></a>Próximas etapas

[Integrar soluções de monitoramento externas](azure-stack-integrate-monitor.md)
