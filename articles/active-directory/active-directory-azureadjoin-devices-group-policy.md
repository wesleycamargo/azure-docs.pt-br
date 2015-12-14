<properties 
	pageTitle="Conectar dispositivos ingressados no domínio ao AD do Azure para experiências com o Windows 10 | Microsoft Azure" 
	description="Explica como os administradores podem configurar políticas de grupo para permitir que dispositivos ingressem no domínio da rede corporativa." 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="stevenpo" 
	editor=""
	tags="azure-classic-portal"/>

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article"

	ms.date="11/19/2015" 

	ms.author="femila"/>

# Conectar dispositivos ingressados no domínio ao AD do Azure para experiências com o Windows 10

O ingresso no domínio é a maneira tradicional usada pelas organizações para conectar dispositivos de trabalho pelo menos nos últimos 15 anos. Ele permitiu que usuários entrassem em seus dispositivos usando suas contas de trabalho do AD e que o departamento de TI gerenciasse totalmente esses dispositivos. As organizações geralmente dependem de métodos de geração de imagens para provisionar dispositivos aos usuários e geralmente usam o SCCM (System Center Configuration Manager) ou política de grupo para gerenciá-los.

O ingresso no domínio do Windows 10 trará os seguintes benefícios depois de estar conectado ao AD do Azure:

- SSO (logon único) para recursos do AD do Azure em qualquer lugar.
- Acesso à Windows Store empresarial usando contas de trabalho (sem a exigência de conta da Microsoft).
- Roaming de configurações de usuário entre dispositivos em conformidade com a empresa usando a conta de trabalho (sem a exigência de conta da Microsoft)
- Autenticação forte e entrada conveniente para a conta de trabalho com o Microsoft Passport e o Windows Hello.
- Capacidade de restringir o acesso a dispositivos em conformidade com as políticas de dispositivo organizacionais.

##Pré-requisitos

O ingresso no domínio continua a ser como sempre foi; no entanto, para obter os benefícios do AD do Azure como SSO, roaming de configurações com conta de trabalho ou acesso a Windows Store com conta de trabalho, os seguintes passos são necessários:

- Assinatura do AD do Azure.
- Azure AD Connect para estender o diretório local ao AD do Azure.
- Definir a política para conectar dispositivos ingressados no domínio ao AD do Azure.
- Compilação do Windows 10 (compilação 10551 ou mais recente) para dispositivos.

Para habilitar o Microsoft Passport for Work e o Windows Hello, você precisará também do seguinte:

- Infraestrutura de PKI para a emissão de certificados de usuário.
- System Center Configuration Manager versão 1509 para visualização técnica. Para saber mais, confira [Microsoft System Center Configuration Manager Technical Preview](https://technet.microsoft.com/library/dn965439.aspx#BKMK_TP3Update) e [Blog da equipe do System Center Configuration Manager](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx). Isso é necessário para implantar certificados de usuário baseados em chaves do Microsoft Passport.

Como alternativa ao requisito de implantação de PKI, você pode fazer o seguinte:

- Ter alguns controladores de domínio com os Serviços de Domínio do Active Directory do Windows Server 2016.

Para habilitar o acesso condicional, você pode criar políticas que permitam acesso a dispositivos ‘ingressados no domínio’ sem implantações adicionais. Para gerenciar o controle de acesso baseado na conformidade do dispositivo, você precisará do seguinte:

- System Center Configuration Manager versão 1509 para a visualização técnica de cenários do Passport.

## Instruções de implantação


## Etapa 1: implantar o Azure Active Directory Connect

O Azure Connect AD permitirá que computadores locais sejam provisionados como objetos de dispositivo na nuvem. Para implantar o Azure AD Connect, confira Habilitando o diretório para gerenciamento híbrido com o Azure AD Connect.

 - Se você tiver usado uma [instalação personalizada do Azure AD Connect](active-directory-aadconnect-get-started-custom.md) (não a instalação expressa), deve seguir o procedimento **Criar um SCP (ponto de conexão de serviço) no Active Directory local**, descrito abaixo.
 - Se você tiver uma configuração federada com o AD do Azure antes de instalar o Azure AD Connect (por exemplo, se tiver implantado o AD FS, Serviços de Federação do Active Directory, antes) terá que seguir o procedimento **Configurar regras de declaração do AD FS** abaixo.

### Criar um SCP (ponto de conexão de serviço) no Active Directory local

Os dispositivos ingressados no domínio usarão esse objeto para descobrir as informações de locatário do AD do Azure no momento do registro automático no serviço de registro de dispositivo do Azure. No servidor do Azure AD Connect, execute os seguintes comandos do PowerShell:

    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;

>[AZURE.NOTE]Substitua [*nome da conta do conector*] pela conta de domínio usada como a conta do AD Connector.

>[AZURE.NOTE]O nome de usuário da credencial inserida quando o pop-up Get-Credential aparece precisa estar no formato **user@example.com*

### Configurar regras de declaração do AD FS
Isso permite o registro instantâneo de um computador com o Azure DRS, permitindo que os computadores se autentiquem usando Kerberos/NTLM por meio do AD FS. Sem essa etapa, os computadores chegarão ao AD do Azure com atraso (sujeito aos tempos de sincronização do Azure AD Connect).

>[AZURE.NOTE]Se você não tiver o AD FS como o servidor de federação local, siga as instruções do fornecedor para criar as regras de declaração.

No servidor AD FS, execute os comandos do PowerShell a seguir (ou em uma sessão conectada ao servidor AD FS):

      <#----------------------------------------------------------------------
     |   Modify the Azure AD Relying Party to include the claims needed 
     |   for DomainJoin++.  The rules include:
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

>[AZURE.NOTE]Os computadores com Windows 10 farão a autenticação usando a autenticação integrada do Windows para um ponto de extremidade WS-Trust ativo hospedado pelo AD FS. Esse ponto de extremidade tem que estar habilitado. Se você estiver usando o proxy Web de autenticação, também deve fazer com que esse ponto de extremidade seja publicado por meio do proxy. Você pode fazer isso verificando se adfs/services/trust/13/windowstransport aparece como ativado no console de gerenciamento do AD FS em Serviço > Pontos de Extremidade.


## Etapa 2: configurar o registro automático de dispositivo usando Política de Grupo no Active Directory

Você pode usar uma Política de Grupo do Active Directory e configurar os dispositivos associados ao domínio do Windows 10 para registro automático no AD do Azure. Para isso, confira as seguintes instruções passo a passo:

1. 	Abra o Gerenciador do Servidor e navegue até **Ferramentas** > **Gerenciamento de Política de Grupo**.
2.	No Gerenciamento de Política de Grupo, navegue até o nó de domínio que corresponde ao domínio no qual você deseja habilitar o Ingresso no AD do Azure.
3.	Clique com o botão direito do mouse em **Objetos de Política de Grupo** e selecione **Novo**. Nomeie seu objeto de Política de Grupo como, por exemplo, Ingresso no AD do Azure. Clique em **OK**.
4.	Clique com o botão direito do mouse em seu novo objeto de Política de Grupo e selecione **Editar**.
5.	Navegue até **Configuração do Computador** > **Políticas** > **Modelos Administrativos** > **Componentes do Windows** > **Ingresso no Local de Trabalho**.
6.	Clique com o botão direito do mouse em **Ingressar computadores cliente automaticamente no local de trabalho** e selecione **Editar**.
7.	Selecione o botão de opção **Habilitado** e clique em **Aplicar**. Clique em **OK**.
8.	Agora você pode vincular o objeto de Política de Grupo para um local de sua escolha. Para habilitar essa política para todos os dispositivos do Windows 10 associados ao domínio em sua organização, vincule a Política de Grupo ao domínio. Por exemplo:
 - Uma UO (unidade organizacional) específica no AD onde os computadores ingressados em domínio do Windows 10 estejam localizados.
 - Um grupo de segurança específico com computadores ingressados no domínio do Windows 10 que serão registrados automaticamente no Azure AD.
 
>[AZURE.NOTE]Esse modelo de Política de Grupo foi renomeado no Windows 10. Se você estiver executando a ferramenta Política de Grupo em um computador com Windows 10, a política será exibida como <br> **Registrar computadores ingressados no domínio como dispositivos** e pode ser encontrada no seguinte local:<br> ***Computer Configuration/Policies/Administrative Templates/Windows Components/Device Registration***

 
## Informações adicionais
* [Windows 10 para a empresa: maneiras de usar dispositivos para o trabalho](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estendendo os recursos de nuvem para dispositivos Windows 10 por meio da Junção do Active Directory do Azure](active-directory-azureadjoin-user-upgrade.md)
* [Saiba mais sobre cenários de uso da Junção do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Conectar dispositivos ingressados no domínio ao AD do Azure para experiências com o Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar a Junção do Azure AD](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_1203_2015-->