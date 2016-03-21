<properties
	pageTitle="Conectar dispositivos ingressados no domínio ao AD do Azure para experiências com o Windows 10 | Microsoft Azure"
	description="Explica como os administradores podem configurar a Política de Grupo para permitir que dispositivos ingressem no domínio da rede corporativa."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""
	tags="azure-classic-portal"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/07/2016"
	ms.author="femila"/>

# Conectar dispositivos ingressados no domínio ao AD do Azure para experiências com o Windows 10

O ingresso no domínio é a maneira tradicional usada pelas organizações para conectar dispositivos de trabalho pelo menos nos últimos 15 anos. Esse recurso permitiu que os usuários entrassem em seus dispositivos usando suas contas corporativas ou de estudante do Windows Server Active Directory (Active Directory) e permitiu que a TI gerenciasse totalmente esses dispositivos. As organizações geralmente dependem de métodos de geração de imagens para provisionar dispositivos aos usuários e geralmente usam o SCCM (System Center Configuration Manager) ou política de grupo para gerenciá-los.

O ingresso no domínio no Windows 10 fornecerá os seguintes benefícios depois de conectar os dispositivos ao Active Directory do Azure (AD do Azure):

- SSO (logon único) para recursos do AD do Azure em qualquer lugar
- Acesso à Windows Store corporativa usando contas corporativas ou de estudante (sem a exigência de conta da Microsoft)
- Roaming de configurações de usuário entre dispositivos compatível com a empresa usando a conta corporativa ou de estudante (sem a exigência de conta da Microsoft)
- Autenticação forte e entrada conveniente para a conta corporativa ou de estudante com o Microsoft Passport e o Windows Hello
- Capacidade de restringir o acesso apenas aos dispositivos que estão em conformidade com as configurações da Política de Grupo do dispositivo organizacional

## Pré-requisitos

O ingresso no domínio continua a ser útil. No entanto, para obter os benefícios que o AD do Azure pode oferecer, por exemplo, SSO, roaming de configurações com conta corporativa ou de estudante, acesso ao Windows Store com conta corporativa ou de estudante, você precisará do seguinte:

- Assinatura do AD do Azure
- Azure AD Connect para estender o diretório local ao AD do Azure
- Política definida para conectar dispositivos ingressados no domínio ao AD do Azure
- Compilação do Windows 10 (compilação 10551 ou mais recente) para dispositivos

Para habilitar o Microsoft Passport for Work e o Windows Hello, você precisará também do seguinte:

- PKI (Infraestrutura de chave pública) para a emissão de certificados de usuário.
- System Center Configuration Manager versão 1509 para visualização técnica. Para saber mais, confira [Microsoft System Center Configuration Manager Technical Preview](https://technet.microsoft.com/library/dn965439.aspx#BKMK_TP3Update) e [Blog da equipe do System Center Configuration Manager](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx). Isso é necessário para implantar certificados de usuário baseados em chaves do Microsoft Passport.

Como alternativa ao requisito de implantação de PKI, você pode fazer o seguinte:

- Ter alguns controladores de domínio com os Serviços de Domínio do Active Directory do Windows Server 2016.

Para habilitar o acesso condicional, você pode criar configurações de Política de Grupo que permitam acesso a dispositivos ingressados no domínio sem implantações adicionais. Para gerenciar o controle de acesso baseado na conformidade do dispositivo, você precisará do seguinte:

- System Center Configuration Manager versão 1509 para a visualização técnica de cenários do Passport

## Instruções de implantação



### Etapa 1: implantar o Azure Active Directory Connect

O Azure Connect AD permitirá que computadores locais sejam provisionados como objetos de dispositivo na nuvem. Para implantar o Azure AD Connect, confira "Instalar o Azure AD Connect" no artigo [Integração de suas identidades locais ao Active Directory do Azure](active-directory-aadconnect/#install-azure-ad-connect).

 - Se você tiver usado uma [instalação personalizada do Azure AD Connect](active-directory-aadconnect-get-started-custom.md) (não a instalação Expressa), siga o procedimento **Criar um ponto de conexão de serviço no Active Directory local**, posteriormente nesta etapa.
 - Se você tiver uma configuração federada com o AD do Azure antes de instalar o Azure AD Connect (por exemplo, se tiver implantado o AD FS, Serviços de Federação do Active Directory, antes), siga o procedimento **Configurar regras de declaração do AD FS** posteriormente nesta etapa.

#### Criar um ponto de conexão de serviço no Active Directory local

Os dispositivos ingressados no domínio usarão esse ponto de conexão do serviço para descobrir as informações de locatário do AD do Azure no momento do registro automático no serviço de registro de dispositivo do Azure.

No servidor do Azure AD Connect, execute os seguintes comandos do PowerShell:

    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;


Ao executar o cmdlet $aadAdminCred = Get-Credential, use o formato *user@example.com* para o nome de usuário da credencial que é inserida quando o pop-up de Get-Credential é exibido.

Ao executar o cmdlet Initialize-ADSyncDomainJoinedComputerSync..., substitua [*nome de conta do conector*] pela conta de domínio que é usada como conta de conector do Active Directory.

#### Configurar regras de declaração do AD FS
A configuração das regras de declaração do AD FS possibilitou o registro instantâneo de um computador com o serviço de registro de dispositivo do Azure permitindo que os computadores se autentiquem usando Kerberos/NTLM por meio do AD FS. Sem essa etapa, os computadores chegarão ao AD do Azure com atraso (sujeito aos tempos de sincronização do Azure AD Connect).

>[AZURE.NOTE]
Se você não tiver o AD FS como o servidor de federação local, siga as instruções do fornecedor para criar as regras de declaração.

No servidor AD FS (ou em uma sessão conectada ao servidor AD FS), execute os seguintes comandos do PowerShell:

      <#----------------------------------------------------------------------
     |   Modify the Azure AD Relying Party to include the claims needed
     |   for DomainJoin++. The rules include:
     |   -ObjectGuid
     |   -AccountType
     |   -ObjectSid
     +---------------------------------------------------------------------#>

    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules

    $rule1 = '@RuleName = "Issue object GUID"
          c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] &&
          c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
          => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), query = ";objectguid;{0}", param = c2.Value);'

    $rule2 = '@RuleName = "Issue account type for domain joined computers"
          c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
          => issue(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "DJ");'

    $rule3 = '@RuleName = "Pass through primary SID"
          c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] &&
          c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
          => issue(claim = c2);'

    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3

    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules

    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString

>[AZURE.NOTE]
Os computadores com Windows 10 farão a autenticação usando a autenticação integrada do Windows para um ponto de extremidade WS-Trust ativo hospedado pelo AD FS. Habilite esse ponto de extremidade. Se você estiver usando o proxy Web de autenticação, verifique também se esse ponto de extremidade pode ser publicado por meio do proxy. Faça isso verificando o adfs/services/trust/13/windowstransport. Ele deve aparecer como habilitado no console de gerenciamento do AD FS em **Serviço** > **Pontos de extremidade**.


### Etapa 2: configurar o registro automático de dispositivo usando Política de Grupo no Active Directory

Você pode usar uma Política de Grupo no Active Directory para configurar os dispositivos ingressados no domínio do Windows 10 para registro automático no AD do Azure. Para isso, use as seguintes instruções passo a passo:

1. 	Abra o Gerenciador do Servidor e navegue até **Ferramentas** > **Gerenciamento de Política de Grupo**.
2.	No Gerenciamento de Política de Grupo, navegue até o nó de domínio que corresponde ao domínio no qual você deseja habilitar o Ingresso no AD do Azure.
3.	Clique com o botão direito do mouse em **Objetos de Política de Grupo** e selecione **Novo**. Nomeie seu objeto de Política de Grupo como, por exemplo, Ingresso no AD do Azure. Clique em **OK**.
4.	Clique com o botão direito do mouse em seu novo objeto de Política de Grupo e selecione **Editar**.
5.	Navegue até **Configuração do Computador** > **Políticas** > **Modelos Administrativos** > **Componentes do Windows** > **Ingresso no Local de Trabalho**.
6.	Clique com o botão direito do mouse em **Ingressar computadores cliente automaticamente no local de trabalho** e selecione **Editar**.
7.	Selecione o botão de opção **Habilitado** e clique em **Aplicar**. Clique em **OK**.
8.	Vincule o objeto da Política de Grupo a um local de sua escolha. Para habilitar essa política para todos os dispositivos do Windows 10 associados ao domínio em sua organização, vincule o objeto da Política de Grupo ao domínio. Por exemplo:
 - Uma UO (unidade organizacional) específica no Active Directory onde os computadores ingressados no domínio do Windows 10 estejam localizados.
 - Um grupo de segurança específico com computadores ingressados no domínio do Windows 10 que serão registrados automaticamente no AD do Azure.

>[AZURE.NOTE]
Esse modelo de Política de Grupo foi renomeado no Windows 10. Se você estiver executando a ferramenta Política de Grupo em um computador com Windows 10, a política será exibida como: <br> **Registrar computadores ingressados no domínio como dispositivos**<br> A política está no seguinte local:<br>***Configuração do Computadpor/Políticas/Modelos Administrativos/Componentes do Windows/Registro do Dispositivo***


## Informações adicionais
* [Windows 10 para a empresa: maneiras de usar dispositivos para o trabalho](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estendendo os recursos de nuvem para dispositivos Windows 10 por meio da Junção do Active Directory do Azure](active-directory-azureadjoin-user-upgrade.md)
* [Saiba mais sobre cenários de uso da Junção do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Conectar dispositivos ingressados no domínio ao AD do Azure para experiências com o Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar a Junção do Azure AD](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_0309_2016-->