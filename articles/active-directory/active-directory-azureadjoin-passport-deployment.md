<properties 
	pageTitle="Habilitar o Microsoft Passport for Work na organização | Microsoft Azure" 
	description="Instruções de implantação para habilitar o Microsoft Passport na sua organização." 
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
	ms.date="11/19/2015" 
	ms.author="femila"/>

# Habilitar o Microsoft Passport for Work na organização

Depois de conectar os dispositivos integrados ao domínio do Windows 10 ao Azure AD, faça o que se segue para habilitar o Microsoft Passport for Work na organização.

## Implantar o System Center Configuration Manager versão 1509 para visualização técnica
Para implantar certificados de usuário baseados em chaves do Microsoft Passport, você precisa do seguinte:

- **System Center Configuration Manager versão 1509 para visualização técnica**. Para saber mais, consulte [Microsoft System Center Configuration Manager Technical Preview](https://technet.microsoft.com/library/dn965439.aspx#BKMK_TP3Update) e [Blog da equipe do System Center Configuration Manager](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx).
- **Infraestrutura de PKI**: para habilitar o Microsoft Passport for Work usando certificados de usuário, você deve ter uma infraestrutura de PKI em vigor. Caso você não tenha uma ou não queira usá-la para certificados de usuário, pode implantar um controlador de domínio (DC) da nova versão do Windows Server:
 - **Implantar um controlador de domínio da nova versão do Windows Server**: em uma versão 10551 do Windows Server ou mais recente (Os ISOs estão disponíveis para download em [Signiant Media Exchange](https://datatransfer.microsoft.com/signiant_media_exchange/spring/main?sdkAccessible=true)) siga as etapas para [instalar uma réplica do controlador de domínio em um domínio existente](https://technet.microsoft.com/pt-BR/library/jj574134.aspx) ou para [instalar uma nova floresta do Active Directory, se estiver criando um novo ambiente](https://technet.microsoft.com/pt-BR/library/jj574134.aspx).

## Configurar o Microsoft Passport for Work usando a Política de Grupo no Active Directory

 Você pode usar uma Política de Grupo do Active Directory para configurar seus dispositivos integrados ao domínio do Windows 10 para fornecer aos usuários do Microsoft Passport credenciais após o logon do usuário no Windows:

1. 	Abra o Gerenciador do Servidor e navegue até **Ferramentas** > **Gerenciamento de Política de Grupo**.
2.	No Gerenciamento de Política de Grupo, navegue até o nó de domínio que corresponde ao domínio no qual você deseja habilitar o Ingresso no AD do Azure.
3.	Clique com o botão direito do mouse em **Objetos de Política de Grupo** e selecione **Novo**. Nomeie seu objeto de Política de Grupo como **Habilitar o Microsoft Passport**, por exemplo. Clique em **OK**.
4.	Clique com o botão direito do mouse em seu novo objeto de Política de Grupo e selecione **Editar**.
5.	Navegue até **Configuração do Computador** > **Políticas** > **Modelos Administrativos** > **Componentes do Windows** > **Passport for Work**.
6.	Clique com o botão direito do mouse em **Habilitar o Passport for Work** e selecione **Editar**.
7.	Selecione o botão de opção **Habilitado** e clique em **Aplicar**. Clique em **OK**.
8.	Agora você pode vincular o objeto de Política de Grupo para um local de sua escolha. Para habilitar essa política para todos os dispositivos do Windows 10 associados ao domínio em sua organização, vincule a Política de Grupo ao domínio. Por exemplo:
 - Uma UO (unidade organizacional) específica no AD onde os computadores ingressados em domínio do Windows 10 estejam localizados.
 - Um grupo de segurança específico com computadores ingressados no domínio do Windows 10 que serão registrados automaticamente no Azure AD.

## Configurar o Microsoft Passport for Work usando a implantação do PowerShell através do Gerenciador de Configurações 

Execute o seguinte comando do PowerShell:

    powershell.exe -ExecutionPolicy Bypass -NoLogo -NoProfile -Command "& {New-ItemProperty "HKLM:\Software\Policies\Microsoft\PassportForWork" -Name "Enabled" -Value 1 -PropertyType "DWord" -Force}"

## Configurar o perfil de certificado para registrar o certificado de registro do "Passport for Work" no Gerenciador de Configurações
Para usar o Certificado Passport for Work com base no logon/Microsoft Hello, configure o perfil do Certificado (Ativos e Conformidade -> Configurações de Conformidade -> Acesso aos Recursos da Empresa -> Perfis de Certificado) selecionando um modelo que tenha um EKU de logon de Cartão Inteligente.

##Configurar uma tarefa agendada, que é acionada quando o contêiner do Passport for Work é habilitado e solicita a avaliação do Certificado
Essa é uma correção de curto prazo que os administradores precisam para criar uma tarefa agendada que identificará a criação de um contêiner do Passport for Work e solicitará uma avaliação do Certificado. Isso reduz o atraso na configuração do contêiner e do PIN e da capacidade de usá-lo no próximo logon.

**Para criar a tarefa agendada, use o comando abaixo ou use a IU ** schtasks /create /xml %0..<EnrollCertificate.xml> /tn <Task Name>

O xml de exemplo é o seguinte:

    <?xml version="1.0" encoding="UTF-16"?>
    <Task version="1.2" xmlns="http://schemas.microsoft.com/windows/2004/02/mit/task">
       <RegistrationInfo>
         <Date>2015-09-04T17:48:45.9973761</Date>
          <Author><DomainName/UserName></Author>
        <URI>\Enroll Certificates</URI>
      </RegistrationInfo>
      <Triggers>
        <EventTrigger>
          <Enabled>true</Enabled>
          <Subscription>&lt;QueryList&gt;&lt;Query Id="0" Path="Microsoft-Windows-User Device Registration/Admin"&gt;&lt;Select Path="Microsoft-Windows-User Device Registration/Admin"&gt;*[System[Provider[@Name='Microsoft-Windows-User Device Registration'] and EventID=300]]&lt;/Select&gt;&lt;/Query&gt;&lt;/QueryList&gt;</Subscription>
        </EventTrigger>
      </Triggers>
      <Principals>
      </Principals>
      <Settings>
        <MultipleInstancesPolicy>IgnoreNew</MultipleInstancesPolicy>
        <DisallowStartIfOnBatteries>false</DisallowStartIfOnBatteries>
        <StopIfGoingOnBatteries>true</StopIfGoingOnBatteries>
        <AllowHardTerminate>true</AllowHardTerminate>
        <StartWhenAvailable>true</StartWhenAvailable>
        <RunOnlyIfNetworkAvailable>false</RunOnlyIfNetworkAvailable>
        <IdleSettings>
          <StopOnIdleEnd>true</StopOnIdleEnd>
          <RestartOnIdle>false</RestartOnIdle>
        </IdleSettings>
        <AllowStartOnDemand>true</AllowStartOnDemand>
        <Enabled>true</Enabled>
        <Hidden>false</Hidden>
        <RunOnlyIfIdle>false</RunOnlyIfIdle>
        <WakeToRun>false</WakeToRun>
        <ExecutionTimeLimit>PT1H</ExecutionTimeLimit>
        <Priority>7</Priority>
        <RestartOnFailure>
          <Interval>PT1M</Interval>
          <Count>3</Count>
        </RestartOnFailure>
      </Settings>
      <Actions Context="Author">
        <Exec>
          <Command>wmic</Command>
          <Arguments>/namespace:\\root\ccm\dcm path SMS_DesiredConfiguration call TriggerEvaluation 1,0,"ScopeId_73F3BB5E-5EDC-4928-87BD-4E75EB4BBC34/ConfigurationPolicy_db89c51e-1d1b-4a17-8e42-a5459b742ccd",8</Arguments>
        </Exec>
      </Actions>
    </Task>

Em que o ScopeId\_73F3BB5E-5EDC-4928-87BD-4E75EB4BBC34/ConfigurationPolicy\_db89c51e-1d1b-4a17-8e42-a5459b742ccd é a ID do perfil do certificado criado na etapa, "Configurar o perfil do certificado para registrar o certificado de registro do Passport for Work" e <8> é a versão.

## Informações adicionais
* [Windows 10 para a empresa: maneiras de usar dispositivos para o trabalho](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estendendo os recursos de nuvem para dispositivos Windows 10 por meio da Junção do Active Directory do Azure](active-directory-azureadjoin-user-upgrade.md)
* [Autenticando identidades sem senhas com o Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Saiba mais sobre cenários de uso da Junção do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Conectar dispositivos ingressados no domínio ao AD do Azure para experiências com o Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar a Junção do Azure AD](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_1125_2015-->