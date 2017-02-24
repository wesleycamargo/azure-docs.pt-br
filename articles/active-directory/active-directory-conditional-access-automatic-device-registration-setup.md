---
title: "Como configurar o registro automático de dispositivos ingressados no domínio do Windows com o Azure Active Directory| Microsoft Docs"
description: "Configure seus dispositivos de domínio do Windows para serem registrados de forma automática e silenciosa com o Azure Active Directory."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/04/2016
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 617599c7df6284e6319a1b3331d1e894e5d4b2d1
ms.openlocfilehash: 2ced7d0d4e2e653013c605932066c412c4202082


---
# <a name="how-to-configure-automatic-registration-of-windows-domain-joined-devices-with-azure-active-directory"></a>Como configurar o registro automático de dispositivos ingressados no domínio do Windows com o Azure Active Directory

Para usar o [Acesso condicional baseado no dispositivo do Azure Active Directory](active-directory-conditional-access.md), computadores devem estar registrados no Azure AD (Azure Active Directory). Este artigo fornece as etapas para configurar o registro automático de dispositivos ingressados no domínio do Windows com o Azure AD em sua organização.

> [!NOTE]
> A atualização de novembro do Windows 10 oferece algumas das experiências do usuário aprimoradas no Azure AD, mas a atualização do Aniversário do Windows 10 é totalmente compatível com acesso condicional baseado no dispositivo. Para obter mais informações sobre o acesso condicional, confira [Acesso condicional com base em dispositivo ao Azure Active Directory](active-directory-conditional-access.md). Para obter mais informações sobre os dispositivos com Windows 10 no local de trabalho e como um usuário registra um dispositivo com Windows 10 com o Azure AD, confira [Windows 10 para a empresa: usar dispositivos para o trabalho](active-directory-azureadjoin-windows10-devices-overview.md).
> 
> 

No caso de dispositivos que executam o Windows, você pode registrar algumas versões anteriores do Windows, incluindo:

- Windows 8.1
- Windows 7

No caso de dispositivos que executam o Windows Server, você pode registrar as seguintes plataformas:

- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2



## <a name="prerequisites"></a>Pré-requisitos

O principal requisito para o registro automático de dispositivos de domínio usando o Azure AD é ter uma versão atualizada do Azure AD Connect (Azure Active Directory Connect).

Dependendo de como você implantou o Azure AD Connect e se usou uma instalação Expressa ou personalizada ou uma atualização in-loco, os pré-requisitos a seguir podem ter sido configurados automaticamente:

- **SCP (ponto de conexão de serviço) no Active Directory local** - para a descoberta de informações de locatário do Azure AD por computadores que se registram no Azure AD.
 
- **Regras de Transformação de Emissão do AD FS (Serviços de Federação do Active Directory)** - para autenticação do computador após o registro (aplicável às configurações federadas).

Se alguns dispositivos em sua organização não forem dispositivos de domínio do Windows 10, execute as seguintes tarefas:

* Definir uma política no Azure AD para permitir que os usuários registrem dispositivos
* Definir IWA (Autenticação Integrada do Windows) como uma alternativa válida para autenticação multifator no AD FS

## <a name="step-1-configure-service-connection-point"></a>Etapa 1: configurar o ponto de conexão de serviço 

Um objeto de SCP (ponto de conexão de serviço) deve existir na partição de contexto de nomenclatura de configuração do domínio do computador. O ponto de conexão de serviço contém informações de descoberta sobre o locatário do Azure AD em que os computadores se registram. Em uma configuração de várias florestas do Active Directory, o ponto de conexão de serviço deve existir em todas as florestas que têm computadores do domínio.

O SCP está localizado em:  

**CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Configuração de registro do dispositivo,CN=Serviços,[seu contexto de nomenclatura de configuração]**

Para uma floresta com um nome de domínio do Active Directory *example.com*, o contexto de nomenclatura de configuração é:  

**CN = configuração, DC = exemplo, DC = com**

Com o script do Windows PowerShell abaixo, você pode verificar a existência do objeto e recuperar os valores de descoberta: 

    $scp = New-Object System.DirectoryServices.DirectoryEntry;

    $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=example,DC=com";

    $scp.Keywords;

A saída **$scp.Keywords** mostra as informações de locatário do Azure AD, por exemplo:

azureADName:microsoft.com  
azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47

Se o ponto de conexão do serviço não existir, você deverá criá-lo executando o seguinte script do PowerShell no seu servidor do Azure AD Connect:

    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;



**Comentários:**

- Quando você executa **$aadAdminCred = Get-Credential**, precisa digitar um nome de usuário. Para o nome de usuário, use o seguinte formato: **user@example.com** 


- Ao executar o cmdlet **Initialize-ADSyncDomainJoinedComputerSync**, substitua [*nome de conta do conector*] pela conta de domínio usada como conta de conector do Active Directory.
  
- O cmdlet usa o módulo Active Directory do PowerShell, que se baseia nos serviços Web do Active Directory em um controlador de domínio. Os Serviços Web do Active Directory têm suporte em controladores de domínio no Windows Server 2008 R2 e posterior. Para controladores de domínio no Windows Server 2008 ou versões anteriores, use a API de System.DirectoryServices por meio do PowerShell para criar o ponto de conexão de serviço e atribuir os valores de palavras-chave.
 
 



##<a name="step-2-register-your-devices"></a>Etapa 2: registrar seus dispositivos

As etapas corretas para registrar seu dispositivo dependem de a sua organização ser federada ou não. 


### <a name="device-registration-in-non-federated-organizations"></a>Registro de dispositivos em organizações não federadas

O registro de dispositivo em uma organização não federada só tem suporte se as condições abaixo são verdadeiras:

- Você está executando o Windows 10 e o Windows Server 2016 em seu dispositivo
- Os dispositivos ingressaram no domínio
- A sincronização de senha usando o Azure AD Connect está habilitada

Se esses requisitos forem atendidos, você não precisará fazer nada para registrar seus dispositivos.  


### <a name="device-registration-in-federated-organizations"></a>Registro de dispositivos em organizações federadas

Em uma configuração do Azure AD federada, os dispositivos confiam no AD FS (ou no servidor de federação local) para se autenticar no Azure AD. Eles registram no Serviço de Registro de Dispositivos do Azure Active Directory.

Para computadores Windows 10 e Windows Server 2016, o Azure AD Connect associa o objeto de dispositivo no Azure AD ao objeto de conta de computador local. As seguintes declarações devem existir durante a autenticação de serviço de registro de dispositivos do Azure AD para concluir o registro e criar o objeto de dispositivo:

- **http://schemas.microsoft.com/ws/2012/01/accounttype** - contém o valor DJ, que identifica o autenticador principal como um computador associado ao domínio.

- **http://schemas.microsoft.com/identity/claims/onpremobjectguid** - contém o valor do atributo **objectGUID** da conta do computador local.
 
- **http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid** - contém o SID (identificador de segurança principal) do computador, que corresponde ao valor de atributo **objectSid** da conta do computador local.

- **http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid** - contém o valor que o Azure AD usa para confiar no token emitido do AD FS ou do STS (Serviço de Token de Segurança) local. Isso é importante se você tem vários domínios verificados no Azure AD. Para o AD FS, use o valor **http://\<*nome de domínio*\>/adfs/services/trust/**, em que **\<nome de domínio\>** é o nome de domínio validado no Azure AD.

Para obter mais detalhes sobre nomes de domínio verificados, confira [Adicionar um nome de domínio ao Azure Active Directory](active-directory-add-domain.md).  
Para obter uma lista dos domínios da empresa verificados, você pode usar o cmdlet [Get-MsolDomain](https://docs.microsoft.com/powershell/msonline/v1/get-msoldomain). 

![Get-MsolDomain](./media/active-directory-conditional-access-automatic-device-registration-setup/01.png)


Os computadores com Windows 10 e Windows Server 2016 ingressados no domínio fazem a autenticação usando a autenticação integrada do Windows para um ponto de extremidade WS-Trust ativo hospedado pelo AD FS. Habilite esse ponto de extremidade. Se você estiver usando o proxy Web de autenticação, verifique também se esse ponto de extremidade pode ser publicado por meio do proxy. O ponto de extremidade é **adfs/services/trust/13/windowstransport**. 

Ele deve aparecer como habilitado no console de gerenciamento do AD FS em **Serviço > Pontos de Extremidade**. Se você não tiver o AD FS como seu servidor de federação local, deverá seguir as instruções do fornecedor para certificar-se de que o ponto de extremidade correspondente esteja habilitado. 



> [!NOTE]
> Se você não usar o AD FS para o servidor de federação local, siga as instruções do fornecedor para criar as regras que emitem essas declarações.
> 
> 




**Para criar as regras manualmente, no AD FS:**

- Selecione um dos scripts do Windows PowerShell a seguir 
- Execute o script do Windows PowerShell em uma sessão que está conectada ao seu servidor. 
- Substitua a primeira linha pelo nome de domínio da organização validado no Azure AD.




#### <a name="setting-ad-fs-rules-in-a-single-domain-environment"></a>Configurando regras do AD FS em um ambiente de domínio único

Use o script abaixo para adicionar regras do AD FS se você tem apenas **um domínio verificado**:


    <#----------------------------------------------------------------------
    |   Modify the Azure AD Relying Party to include the claims needed
    |   for DomainJoin++. The rules include:
    |   -ObjectGuid
    |   -AccountType
    |   -ObjectSid
    +---------------------------------------------------------------------#>

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

    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules 

    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3

    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules 

    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString 


#### <a name="setting-ad-fs-rules-in-a-multi-domain-environment"></a>Configurando regras do AD FS em um ambiente com vários domínios

Se você tem mais de um domínio verificado, execute as seguintes etapas:

1. Remova a regra **IssuerID** existente criada pelo Azure AD Connect.  
Veja um exemplo da regra: c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)",  "http://${domain}/adfs/services/trust/")); 


2. Execute este script: 

        <#----------------------------------------------------------------------  
        |   Modify the Azure AD Relying Party to include the claims needed  
        |   for DomainJoin++. The rules include:
        |   -ObjectGuid
        |   -AccountType
        |   -ObjectSid
        +---------------------------------------------------------------------#>

        $VerifiedDomain = 'example.com'      # Replace example.com with one of your verified domains

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

        $rule4 = '@RuleName = "Issue AccountType with the value User when its not a computer account" 

        NOT EXISTS([Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"]) 

        => add(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "User");' 

        $rule5 = '@RuleName = "Capture UPN when AccountType is User and issue the IssuerID" 

        c1:[Type == "http://schemas.xmlsoap.org/claims/UPN"] && 

        c2:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "User"] 

        => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c1.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));' 

        $rule6 = '@RuleName = "Update issuer for DJ computer auth" 

        c1:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"] 

        => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = "http://'+$VerifiedDomain+'/adfs/services/trust/");' 

        $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules 

        $updatedRules = $existingRules + $rule1 + $rule2 + $rule3 + $rule4+ $rule5+  $rule6 

        $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules 

        Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString 



## <a name="step-3-setup-ad-fs-for-authentication-of-device-registration"></a>Etapa 3: configurar o AD FS para autenticação de registro do dispositivo

Verifique se WIA está definido como uma alternativa válida para autenticação multifator para registro de dispositivo no AD FS. Para fazer isso, você precisa ter uma regra de transformação de emissão que passe pelo método de autenticação.

1. No console de gerenciamento do AD FS, vá para **AD FS** > **Relações de Confiança** > **Terceiras Partes Confiáveis**.
2. Clique com o botão direito do mouse no objeto de confiança de terceira parte confiável da Plataforma de Identidade do Microsoft Office 365 e selecione **Editar Regras de Declaração**.
3. Na guia **Regras de Transformação de Emissão**, selecione **Adicionar Regra**.
4. Na lista de modelos **Regra de declaração**, selecione **Enviar Declarações Usando uma Regra Personalizada**.
5. Selecione **Avançar**.
6. Na caixa **Nome da regra de declaração**, digite **Regra de Declaração de Método de Autenticação**.
7. Na caixa **Regra de declaração** , digite esta regra:  
**c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"] => issue(claim = c);**
8. No servidor de federação, digite este comando do PowerShell:
   
    `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

**\<RPObjectName\>** é o nome do objeto da terceira parte confiável para seu objeto de confiança da terceira parte confiável do Azure AD. Geralmente, este objeto é nomeado como **Plataforma de Identidade do Microsoft Office 365**.



##<a name="step-4-deployment-and-rollout"></a>Etapa 4: implantação e distribuição

Quando os computadores do domínio atendem aos pré-requisitos, eles estão prontos para serem registrados no Azure AD.

Os computadores de domínio da Atualização de Aniversário do Windows 10 e Windows Server 2016 se registram automaticamente no Azure AD da próxima vez que o dispositivo é reiniciado ou quando um usuário entra no Windows. Novos computadores que ingressam no domínio são registrados no Azure AD quando o dispositivo é reiniciado após a operação de ingresso no domínio.

> [!NOTE]
> Os computadores ingressados no domínio executando a Atualização de 10 de novembro Windows 10 só serão registrados automaticamente no Azure AD se o Objeto de Política de Grupo de distribuição estiver definido.
> 
> 

Você pode usar um objeto de Política de Grupo para controlar a distribuição de registro automático de computadores associados ao domínio do Windows 10 e do Windows Server 2016. 

Para distribuir o registro automático de computadores de domínio não Windows 10, você pode implantar um pacote do Windows Installer para os computadores que selecionar.

> [!NOTE]
> Para todos os computadores do Windows não 10/Windows Server 2016, é recomendável usar o pacote do Windows Installer, conforme descrito a seguir neste documento.
> 
> 

### <a name="create-a-group-policy-object-to-control-the-rollout-of-automatic-registration"></a>Criar um objeto de Política de Grupo para controlar a distribuição de registro automático

Para controlar a distribuição de registro automático de computadores ingressados no domínio com o Azure AD, você pode implantar a Política de Grupo **Registrar computadores ingressados no domínio como dispositivos** nos computadores que deseja registrar. Por exemplo, você pode implantar a política em uma unidade organizacional ou um grupo de segurança.

**Para definir a política:**

1. Abra o Gerenciador de Servidor e vá para **Ferramentas** > **Gerenciamento de Política de Grupo**.
2. Vá para o nó de domínio que corresponde ao domínio em que você deseja ativar o registro automático de computadores Windows 10 ou Windows Server 2016.
3. Clique com o botão direito do mouse em **Objetos de Política de Grupo** e selecione **Novo**.
4. Insira um nome para o objeto de Política de Grupo. Por exemplo, *Registro Automático no Azure AD*. Selecione **OK**.
5. Clique com o botão direito do mouse em seu novo objeto de Política de Grupo e selecione **Editar**.
6. Vá para **Configuração do Computador** > **Políticas** > **Modelos Administrativos** > **Componentes do Windows** > **Registro de Dispositivo**. Clique com o botão direito do mouse em **Registrar computadores associados ao domínio como dispositivos** e selecione **Editar**.
   
   > [!NOTE]
   > Esse modelo de Política de Grupo foi renomeado de versões anteriores do console de Gerenciamento de Política de Grupo. Se você estiver usando uma versão anterior do console, vá para **Configuração do Computador** > **Políticas** > **Modelos Administrativos** > **Componentes do Windows** > **Ingressar no Local de Trabalho** > **Ingressar automaticamente computadores cliente**.
   > 
   > 
7. Selecione **Habilitado** e selecione **Aplicar**.
8. Selecione **OK**.
9. Vincule o objeto da Política de Grupo a um local de sua escolha. Por exemplo, você pode vinculá-lo a uma unidade organizacional específica. Você também pode vinculá-lo a um grupo específico de computadores que se registram automaticamente no Azure AD. Para definir essa política para todos os computadores com Windows 10 e Windows Server 2016 ingressados no domínio em sua organização, vincule o objeto de Política de Grupo ao domínio.

### <a name="windows-installer-packages-for-non-windows-10-computers"></a>Pacotes do Windows Installer para computadores que não são do Windows 10
Para registrar computadores associados ao domínio que executam o Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2 em um ambiente federado, você pode baixar e instalar estes arquivos de pacote (.msi) do Windows Installer:

* [x64](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x64.msi)
* [x86](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x86.msi)

Implante o pacote usando um sistema de distribuição de software como o System Center Configuration Manager. O pacote dá suporte às opções de instalação silenciosa padrão com o parâmetro *quiet*. O System Center Configuration Manager 2016 oferece benefícios adicionais em relação a versões anteriores, como a capacidade de controlar registros concluídos. Para obter mais informações, confira [System Center 2016](https://www.microsoft.com/en-us/cloud-platform/system-center).

O instalador cria uma tarefa agendada no sistema que é executada no contexto do usuário. A tarefa é disparada quando o usuário entra no Windows. A tarefa registra silenciosamente o dispositivo com o Azure AD com as credenciais do usuário após a autenticação por meio de IWA. Para ver a tarefa agendada, vá para **Microsoft** > **Ingressar no local de trabalho** e vá para a biblioteca do Agendador de Tarefas.

## <a name="next-steps"></a>Próximas etapas

- Se algo deu errado durante o registro automático de dispositivos, consulte os seguintes tópicos de solução de problemas para obter ajuda:
    - [Para Windows 10 e Windows Server 2016](active-directory-conditional-access-automatic-device-registration-troubleshoot-windows.md)
    - [Para clientes de nível inferior do Windows](active-directory-conditional-access-automatic-device-registration-troubleshoot-windows-legacy.md)
- Para obter respostas a perguntas comuns, consulte as [Perguntas frequentes sobre o registro automático do dispositivo](active-directory-conditional-access-automatic-device-registration-faq.md).




<!--HONumber=Feb17_HO2-->


