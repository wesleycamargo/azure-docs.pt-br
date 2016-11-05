---
title: Registro de dispositivo automático com o Azure Active Directory para dispositivos ingressados no domínio do Windows| Microsoft Docs
description: Administradores de TI podem optar para que seus dispositivos Windows ingressados no domínio registrem-se automaticamente e silenciosamente com o AD do Azure (Active Directory do Azure).
services: active-directory
documentationcenter: ''
author: markusvi
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2016
ms.author: markvi

---
# <a name="how-to-setup-automatic-registration-of-windows-domain-joined-devices-with-azure-active-directory"></a>Registro de dispositivo automático com o Azure Active Directory para dispositivos ingressados no domínio do Windows
O registro de computadores ingressados no domínio do Windows com o Azure AD é necessário para habilitar o [acesso condicional com base no dispositivo do Azure Active Directory](active-directory-conditional-access.md).

Atualize de modo a permitir o uso da conta corporativa ou de estudante para obter uma experiência aprimorada de SSO para aplicativos do Azure AD, roaming corporativo de configurações entre dispositivos, o uso da Windows Store para Empresas e para ter uma autenticação mais forte e entrada conveniente com o Windows Hello. 

> [!NOTE]
> A Atualização de novembro de 2015 do Windows 10 dá suporte a algumas das experiências do usuário aprimoradas; no entanto, é a Atualização de Aniversário que oferece suporte para acesso condicional com base no dispositivo.  
> Para obter mais informações sobre o acesso condicional, confira [Acesso condicional com base em dispositivo ao Azure Active Directory](active-directory-conditional-access.md).  
> Para obter mais informações sobre dispositivos Windows 10 no local de trabalho e as experiências que os usuários obtêm quando registrados com o Azure AD, consulte [Windows 10 para a empresa: maneiras de usar dispositivos para o trabalho](active-directory-azureadjoin-windows10-devices-overview.md). 
> 
> 

Há suporte para registro nas versões anteriores do Windows, incluindo: 

* Windows 8.1 
* Windows 7 

Para o caso de uso de computadores com Windows Server usados como um desktop (por exemplo, um desenvolvedor usando um servidor Windows como o computador principal), podem ser registradas as seguintes plataformas: 

* Windows Server 2016 
* Windows Server 2012 R2 
* Windows Server 2012 
* Windows Server 2008 R2 

Abaixo, você encontra o que você precisa fazer em seu ambiente para habilitar o registro de dispositivos ingressados no domínio do Windows com o Azure AD em sua organização. Você verá: 

1. Pré-requisitos 
2. Implantação e distribuição 
3. Solucionar problemas 
4. Perguntas frequentes 

## <a name="prerequisites"></a>Pré-requisitos
O principal requisito para habilitar o registro automático de dispositivos ingressados no domínio com o Azure AD é ter uma versão atualizada do Azure AD Connect. 

Dependendo de como você implantou o Azure AD Connect, de ter sido uma instalação Expressa ou Personalizada ou uma atualização in-loco, os pré-requisitos a seguir podem ter sido configurados automaticamente: 

1. O SCP (ponto de conexão de serviço) no Active Directory local para a descoberta de informações de locatário do Azure AD por computadores registrando-se no Azure AD. 
2. Regras de Transformação de Emissão do AD FS para autenticação do computador após o registro (aplicável às configurações federadas). 

Se você tiver computadores ingressados no domínio que não sejam do Windows 10 em sua organização, você precisará do seguinte: 

1. Certifique-se de que a política para permitir que os usuários registrem dispositivos esteja habilitada no Azure AD. 
2. Verifique se a WIA (Autenticação integrada do Windows) está definida como uma alternativa válida à MFA (Multi-Factor Authentication) no AD FS. 

## <a name="service-connection-point-for-discovery-of-azure-ad-tenant"></a>Ponto de conexão de serviço para descoberta do locatário do Azure AD
Um objeto de SCP contendo informações de descoberta sobre o locatário do Azure AD em que computadores serão registrados deve existir na partição de Contexto de Nomenclatura de Configuração da floresta do domínio no qual os computadores estão ingressados. Em uma configuração de várias florestas do Active Directory, o SCP deve existir em todas as florestas em que os computadores ingressaram. 

O SCP deve ser encontrado na seguinte localização no Active Directory: 

    CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Configuration Naming Context] 

> [!NOTE]
> Para uma floresta com um nome de domínio do Active Directory de exemplo.com, o Contexto de Nomenclatura de Configuração é CN=Configuration,DC=example,DC=com 
> 
> 

Você pode verificar a existência do objeto e os valores para a descoberta de locatário do Azure AD usando o seguinte script do PowerShell (substitua o Contexto de Nomenclatura de Configuração no exemplo pelo seu): 

    $scp = New-Object System.DirectoryServices.DirectoryEntry; 

    $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=example,DC=com"; 

    $scp.Keywords; 

A saída de $scp.Keyword mostra as informações de locatário do Azure AD como demonstrado a seguir: 

    azureADName:microsoft.com 

    azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47 

Se o SCP não existir, você poderá criá-lo executando o seguinte script do PowerShell no servidor do Azure AD Connect: 

    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1"; 

    $aadAdminCred = Get-Credential; 

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred; 


> [!NOTE]
> Ao executar $aadAdminCred = Get-Credential, use o formato user@example.com para o nome de usuário da credencial que é inserida quando o pop-up de Get-Credential é exibido.  
> Ao executar o cmdlet Initialize-ADSyncDomainJoinedComputerSync, substitua [nome de conta do conector] pela conta de domínio usada como conta do conector do Active Directory.  
> O cmdlet acima usa o módulo do Active Directory PowerShell que se baseia no ADWS (Serviços Web do Active Directory) DCs (controladores de domínio). Há suporte para o ADWS em DCs do Windows Server 2008 R2 ou superior. Se você tiver apenas DCs do Windows Server 2008 ou inferior, você poderá usar a API de System.DirectoryServices por meio do PowerShell para criar o SCP e atribuir os valores de palavras-chave apropriados. 
> 
> 

## <a name="ad-fs-rules-for-instant-computer-registration-(federated-orgs)"></a>Regras do AD FS para o registro instantâneo do computador (organizações federadas)
Em uma configuração federada do Azure AD, computadores dependerão do AD FS (ou o servidor de federação local) para autenticar no Azure AD para registro com o Azure DRS (Serviço de Registro de Dispositivos do Azure). 

> [!NOTE]
> Em uma configuração não federada (ou seja, hashes de senha do usuário sincronizados ao Azure AD), computadores ingressados no domínio do Windows 10 e Windows Server 2016 autenticam-se no Azure DRS usando uma credencial que eles gravam em suas contas de computador localmente e que é movida para o Azure AD por meio do Azure AD Connect. Para computadores não Windows 10/Windows Server 2016 em uma configuração não federada, consulte a seção Windows Installer package for registration of non-Windows 10/Windows Server 2016 domain joined computers under Deployment (Pacote do Windows Installer para registro de computadores não Windows 10/Windows Server 2016 ingressados no domínio em implantação e distribuição) abaixo neste documento para as opções que você tem para habilitar a AC baseada em dispositivo em sua organização. 
> 
> 

Para computadores Windows 10 e Windows Server 2016, o Azure AD Connect associa o objeto de dispositivo no Azure AD com o objeto de conta de computador local. Para que isso funcione, as seguintes declarações devem existir durante a autenticação para que o Azure DRS chegue a concluir o registro e a criar o objeto de dispositivo: 

* `http://schemas.microsoft.com/ws/2012/01/accounttype` - contém o valor de "DJ" que identifica a entidade de segurança autenticando-se como um computador ingressado em domínio. 
* `http://schemas.microsoft.com/identity/claims/onpremobjectguid` - contém o valor do atributo objectGUID da conta de computador local. 
* `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid` - contém a SID primária do computador, correspondente ao valor do atributo objectSid da conta do computador local. 
* `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid` - contém o valor apropriado que permite que o Azure AD confie no token emitido pelo AD FS ou pelo STS local. Isso é importante em uma configuração de várias florestas do Active Directory na qual os computadores podem estar ingressados em uma floresta diferente daquela que se conecta ao Azure AD em que está o AD FS ou STS local. No caso do AD FS o valor deve ser `http://<domain-name>/adfs/services/trust/`, em que "\<domain-name\>" é o nome de domínio validado no Azure AD. 

Para criar essas regras manualmente no AD FS, você pode usar o script do PowerShell a seguir na sessão conectada ao seu servidor. Observe que a primeira linha precisa ser substituída pelo nome de domínio validado no Azure AD para a sua organização. 

> [!NOTE]
> Se você não tiver o AD FS como seu servidor de federação local, siga as instruções do fornecedor para criar as regras apropriadas para emitir essas declarações. 
> 
> 

    $validatedDomain = "example.com"      # Replace example.com with your validated domain name in Azure AD 

    $rule1 = '@RuleName = "Issue object GUID" 

        c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "-515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] && 

        c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

        => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), query = ";objectguid;{0}", param = c2.Value);' 

    $rule2 = '@RuleName = "Issue account type for domain joined computers" 

      c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "-515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

      => issue(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "DJ");' 

    $rule3 = '@RuleName = "Pass through primary SID" 

      c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "-515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] && 

      c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

      => issue(claim = c2);' 

    $rule4 = '@RuleName = "Issue AccountType with the value User when it’s not a computer account" 

      NOT EXISTS([Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"]) 

      => add(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "User");' 

    $rule5 = '@RuleName = "Capture UPN when AccountType is User and issue the IssuerID" 

      c1:[Type == "http://schemas.xmlsoap.org/claims/UPN"] && 

      c2:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "User"] 

      => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c1.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));' 

    $rule6 = '@RuleName = "Update issuer for DJ computer auth" 

      c1:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"] 

      => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = "http://'+$validatedDomain+'/adfs/services/trust/");' 

    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules 

    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3 + $rule4 + $rule5 + $rule6 

    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules 

    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString 

> [!NOTE]
> Os computadores com Windows 10 e Windows Server 2016 ingressados no domínio farão a autenticação usando a autenticação integrada do Windows para um ponto de extremidade WS-Trust ativo hospedado pelo AD FS. Habilite esse ponto de extremidade. Se você estiver usando o proxy Web de autenticação, verifique também se esse ponto de extremidade pode ser publicado por meio do proxy. O ponto de extremidade é adfs/services/trust/13/windowstransport. Ele deve aparecer como habilitado no console de gerenciamento do AD FS em Serviço > Pontos de extremidade. Se você não tiver o AD FS como seu servidor de federação local, deverá seguir as instruções do fornecedor para certificar-se de que o ponto de extremidade correspondente esteja habilitado. 
> 
> 

## <a name="ensure-ad-fs-is-set-up-to-support-authentication-of-device-for-registration"></a>Certifique-se de que o AD FS esteja configurado para dar suporte à autenticação do dispositivo para o registro
Você deve certificar-se de que a WIA (Autenticação integrada do Windows) esteja definida como uma alternativa válida à MFA (Multi-Factor Authentication) no AD FS para o registro do dispositivo.

Para isso, você precisa ter uma regra de transformação de emissão que transmita o método de autenticação.

1. Abra o console de gerenciamento do AD FS e navegue até **AD FS > Relações de Confiança > Objetos de confiança de terceira parte confiável**. 
2. Clique com o botão direito do mouse no objeto de confiança de terceira parte confiável da Plataforma de Identidade do Microsoft Office 365 e selecione **Editar Regras de Declaração**.
3. Na guia **Regras de Transformação de Emissão**, selecione **Adicionar Regra**.
4. Selecione **Enviar declarações usando uma regra personalizada** na lista de modelos **Regra de declaração**. 
5. Selecione **Avançar**.
6. Na caixa de texto **Nome da regra de declaração**, digite **Regra de Declaração de Método de Autenticação**.
7. Na caixa de texto **Regra de declaração**, digite `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"]
   => issue(claim = c);`
8. No seu servidor de federação, abra o Windows PowerShell.
9. Digite o seguinte comando: 
   
    `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

O **\<RPObjectName>\** é o nome do objeto de terceira parte confiável para seu objeto de confiança de terceira parte confiável do Azure Active Directory. Esse objeto é normalmente nomeado como Plataforma de Identidade do Microsoft Office 365.

## <a name="deployment-and-roll-out"></a>Implantação e distribuição
Depois que os pré-requisitos estiverem cumpridos, os computadores ingressados em domínio estarão prontos para registrar-se no Azure AD. 

Computadores ingressados no domínio executando a Atualização de Aniversário do Windows 10 e Windows Server 2016 serão registrados automaticamente no Azure AD na próxima reinicialização ou entrada do usuário no Windows. Novos computadores que ingressaram no domínio serão registrados com o Azure AD na reinicialização após a operação de ingresso no domínio. 

> [!NOTE]
> Computadores ingressados no domínio executando a Atualização do Windows 10 serão registrados automaticamente no Azure AD somente se o Objeto de Política de Grupo de distribuição estiver definido. Para obter mais informações, consulte a seção a seguir. 
> 
> 

Há um objeto de Política de Grupo que você pode usar para a finalidade de controlar a distribuição de registro automático de computadores ingressados no domínio executando Windows 10/Windows Server 2016. Há um pacote do Windows Installer que você pode implantar nos computadores selecionados a fim de realizar a distribuição do registro automático de computadores não Windows 10 ingressados no domínio. 

> [!NOTE]
> A Política de Grupo para controle de distribuição também dispara o registro de computadores com Windows 8.1 ingressados no domínio. Você poderá optar por usar a política para registro de computadores com Windows 8.1 ingressados no domínio ou, se você tiver uma mistura de versões do Windows, incluindo 7 ou versões do Windows Server, você poderá optar por habilitar o registro de todos os seus computadores não Windows 10/Windows Server 2016 usando o pacote do Windows Installer. 
> 
> 

### <a name="group-policy-object-to-control-roll-out-of-automatic-registration"></a>Objeto de Política de Grupo para controlar a distribuição de registro automático
Para controlar a distribuição de registro automático de computadores ingressados no domínio com o Azure AD, você pode implantar a Política de Grupo Registrar computadores ingressados no domínio como dispositivos aos computadores que você deseja registrar. Por exemplo, você pode implantar a política com base em um grupo de segurança ou em uma unidade organizacional (UO). 

Para definir a política, execute as seguintes etapas: 

1. Abra o Gerenciador do Servidor e navegue até **Ferramentas > Gerenciamento de Política de Grupo**. 
2. No Gerenciamento de Política de Grupo, navegue até o nó de domínio que corresponde ao domínio no qual você deseja habilitar o registro automático de computadores com Windows 10 ou Windows Server 2016. 
3. Clique com o botão direito do mouse em **Objetos de Política de Grupo** e selecione **Novo**. 
4. Nomeie seu objeto de política de grupo, por exemplo, *Registro Automático no Azure AD*. Clique em OK. 
5. Clique com o botão direito do mouse em seu novo Objeto de Política de Grupo e selecione **Editar**. 
6. Navegue até **Configuração do Computador > Políticas > Modelos Administrativos > Componentes do Windows > Registro de Dispositivo,** clique com o botão direito do mouse em **Registrar computadores ingressados no domínio como dispositivos**, e, em seguida, selecione **Editar**. 
   
   > [!NOTE]
   > Esse modelo de Política de Grupo foi renomeado de versões anteriores do console de Gerenciamento de Política de Grupo. Se você estiver executando uma versão anterior do console, a política estará em configuração do computador > Políticas > Modelos Administrativos > Componentes do Windows > Ingresso no Local de Trabalho, com o nome Ingressar computadores cliente no local de trabalho automaticamente. 
   > 
   > 
7. Selecione o botão de opção **Habilitado** e clique em **Aplicar**. 
8. Clique em **OK**. 
9. Vincule o objeto de política de grupo a uma localização de sua escolha. Por exemplo, uma UO (unidade organizacional) específica em que computadores estão localizados ou um grupo de segurança específico contendo computadores que serão automaticamente registrados no Azure AD. Para habilitar essa política para todos os computadores com Windows 10 e Windows Server 2016 ingressados no domínio em sua organização, vincule o objeto de Política de Grupo ao domínio. 

## <a name="msi-package-for-non-windows-10-computers"></a>Pacote MSI para computadores não Windows 10
Para registrar computadores ingressados no domínio executando o Windows 7, Windows 8.1, Windows Server 2008 R2, Windows Server 2012 ou Windows Server 2012 R2, um pacote do Windows Installer (.msi) está disponível para download:

* [x64](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x64.msi)
* [x86](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x86.msi)

Você deve implantar o pacote usando um sistema de distribuição de software como o System Center Configuration Manager. O pacote dá suporte às opções de instalação silenciosa padrão com o parâmetro /quiet. Se usar o System Center Configuration Manager 2016, você aproveitará os benefícios adicionais como a capacidade de controlar registros concluídos. Para obter mais informações, consulte [System Center 2016](https://www.microsoft.com/cloud-platform/system-center-2016). 

O instalador cria uma Tarefa Agendada no sistema que é executada no contexto do usuário e é disparada na entrada do usuário no Windows. A tarefa registra silenciosamente o dispositivo no Azure AD com as credenciais do usuário após a autenticação usando a Autenticação integrada do Windows. A Tarefa Agendada pode ser encontrada na Biblioteca do Agendador de Tarefas em **Microsoft > Ingresso no Local de Trabalho**. 

## <a name="additional-topics"></a>Tópicos adicionais
* [Acesso condicional ao Azure Active Directory](active-directory-conditional-access.md)

<!--HONumber=Oct16_HO2-->


