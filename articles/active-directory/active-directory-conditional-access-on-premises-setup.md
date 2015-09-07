<properties
	pageTitle="Configurando o acesso condicional no local usando o registro do dispositivo do Active Directory do Azure | Microsoft Azure"
	description="Um passo a passo para habilitar o acesso condicional para aplicativos locais usando o AD FS (Serviço de Federação do Active Directory) no Windows Server 2012 R2."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/19/2015"
	ms.author="femila"/>

# Configurando o acesso condicional no local usando o registro do dispositivo do Active Directory do Azure

Dispositivos de propriedade dos seus usuários podem ser marcados como conhecidos pela sua organização se for exigido aos usuários que adicionem seus dispositivos ao serviço do registro de dispositivo do Active Directory do Azure no ambiente de trabalho. Abaixo está um passo a passo para habilitar o acesso condicional para aplicativos locais usando o AD FS (Serviço de Federação do Active Directory) no Windows Server 2012 R2.

> [AZURE.NOTE]A licença do Office 365 ou a do Azure AD Premium é necessária quando usar dispositivos registrados nas políticas de acesso condicional do serviço de registro de dispositivo do Active Directory do Azure. Isso inclui as políticas impostas pelo AD FS (Serviços de Federação do Active Directory) para recursos locais.

Para saber mais sobre os cenários de acesso condicional local, confira [Ingressar no local de trabalho de qualquer dispositivo para SSO e segundo fator de autenticação entre aplicativos da empresa](https://technet.microsoft.com/library/dn280945.aspx). Esses recursos estão disponíveis para os clientes que comprarem uma licença Active Directory Premium do Azure.

Dispositivos com suporte
-------------------------------------------------------------------------
* Dispositivos ingressados ao domínio do Windows 7.
* Dispositivos do Windows 8.1 pessoais e adicionados ao domínio.
* iOS 6 e versões posteriores.
* Android 4.0 ou posterior, Samsung GS3 ou telefones superiores, Samsung Note2 ou tablets superiores.

Pré-requisitos do cenário
------------------------------------------------------------------------
* Windows Server Active Directory (Windows Server 2008 ou superior)
* Esquema atualizado no Windows Server 2012 R2
* Assinatura no Active Directory Premium do Azure
* Serviços de Federação do Windows Server 2012 R2, configurados para logon único ao Azure AD
* Proxy do aplicativo Web do Windows Server 2012 R2
* Ferramenta de sincronização de diretório (DirSync) com write-back do objeto de dispositivo. [Baixe a versão mais recente do dirsync.exe.](http://go.microsoft.com/fwlink/?LinkID=278924)

Problemas conhecidos nesta versão
-------------------------------------------------------------------------------
* As políticas de acesso condicional baseadas em dispositivo exigem o write-back do objeto de dispositivo para o Active Directory do Active Directory do Azure. Pode levar até 3 horas para objetos de dispositivo serem gravados no Active Directory.
* Os dispositivos iOS7 sempre solicitarão que o usuário selecione um certificado durante a autenticação de certificado de cliente. 
* Os dispositivos iOS8 beta não estão funcionando no momento.


## Implantar serviço de registro de dispositivos do Active Directory do Azure
Use este guia para implantar e configurar o serviço de registro de dispositivo do Active Directory do Azure para a sua organização.

Este guia pressupõe que você tenha configurado o Windows Server Active Directory e assinado o Active Directory do Microsoft Azure. Confira os pré-requisitos acima.

Para implantar o serviço de registro de dispositivo do Active Directory do Azure com seu locatário do Active Directory do Azure, complete as tarefas na lista de verificação abaixo, na ordem. Quando um link de referência levá-lo para um tópico conceitual, retorne a esta lista de verificação depois de revisar o tópico conceitual para que você possa prosseguir com as tarefas restantes desta lista de verificação. Algumas tarefas incluirão uma etapa de validação de cenário que pode ajudá-lo a confirmar que a etapa foi concluída com êxito.

**Lista de verificação: habilitar o registro de dispositivos do Active Directory do Azure**

Siga a lista de verificação a seguir para habilitar e configurar o serviço de registro de dispositivo do Active Directory do Azure.

| Tarefa | Referência |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Habilite o registro de dispositivos em seu locatário do Active Directory do Azure para permitir que dispositivos sejam adicionados ao local de trabalho. Por padrão, a autenticação multifator não está habilitada para o serviço. No entanto, a autenticação multifator é recomendável ao registrar um dispositivo. Antes de habilitar a autenticação multifator em ADRS, verifique se o AD FS está configurado para um provedor de autenticação multifator. | [Habilitar o registro de dispositivos do Active Directory do Azure](active-directory-conditional-access-device-registration-overview.md) |
| Os dispositivos detectarão o serviço de registro de dispositivos do Active Directory do Azure procurando registros DNS conhecidos. Você deve configurar o DNS da sua empresa para que os dispositivos possam descobrir seu serviço de registro de dispositivo do Active Directory do Azure. | [Configurar a descoberta de registro de dispositivos do Active Directory do Azure](active-directory-conditional-access-device-registration-overview.md) |

**Lista de verificação: implantar e configurar o Windows Server 2012 R2 Active Directory Federation Services e configurar uma relação de federação com o Active Directory do Azure** Com o próximo conjunto de tarefas, você irá integrar os serviços de Federação do Windows Server 2012 R2 com o Active Directory do Azure. Isso permite que você use dispositivos registrados no serviço de registro de dispositivo do Active Directory do Azure para políticas de acesso condicional no AD FS.


| Tarefa | Referência |
|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------|
| Implantar domínio dos Serviços de Domínio do Active Directory com as extensões de esquema do Windows Server 2012 R2. (Não é necessário atualizar qualquer um dos seus controladores de domínio para o Windows Server 2012 R2. A atualização do esquema é o único requisito.) | Atualizar o esquema de serviços do Domínio do Active Directory. |
| Implantar os Serviços de Federação do Windows Server 2012 R2 junto com o proxy do aplicativo Web. | Implantar os Serviços de Federação do Windows Server 2012 R2 junto com o proxy do aplicativo Web. |
| A floresta dos Serviços de Domínio do Active Directory deve ser configurada com os objetos e contêineres necessários para dar suporte a objetos de dispositivo. Você também habilitará a autenticação do dispositivo no AD FS. | Preparar sua floresta do Active Directory para dar suporte a dispositivos. |
| Configurar um relacionamento de federação entre a sua organização e o Active Directory do Azure; esta etapa lhe orientará para configurar seu locatário do Active Directory do Azure com o Serviços de Federação do Windows Server 2012 R2. | Configurar um relacionamento de federação com o Active Directory do Azure e configurar ferramenta de sincronização de diretório |
| Configure a sincronização de diretório (DirSync) para permitir o write-back do objeto de dispositivo. Os dispositivos criados no Active Directory do Azure serão gravados no Active Directory. | Configurar DirSync para permitir o write-back do objeto de dispositivo |
| É altamente recomendável que você configure um provedor de autenticação multifator com os Serviços de Federação do Windows Server 2012 R2; isso permitirá que os usuários registrem seus dispositivos usando autenticação multifator com segurança. | Configurar serviços de federação para fornecer autenticação multifator. |


### Atualizar o esquema de serviços do Domínio do Active Directory
> [AZURE.NOTE]A atualização do esquema do Active Directory não pode ser desfeita. É recomendável executar isso em um ambiente de teste.

1. Faça logon no controlador de domínio com uma conta que tenha direitos de administrador corporativo e de administrador de esquema.
2. Copie o diretório [media]\\support\\adprep e subdiretórios em um dos controladores de domínio do Active Directory. 
3. Onde [media] é o caminho para a mídia de instalação do Windows Server 2012 R2.
4. Em um prompt de comando, navegue até o diretório adprep e execute: adprep.exe /forestprep. Siga as instruções na tela para concluir a atualização do esquema.

### Implantar os Serviços de Federação do Windows Server 2012 R2 junto com o proxy de aplicativo Web
Siga os dois guias abaixo para implantar os Serviços de Federação do Windows Server 2012 R2 com o Proxy do aplicativo Web e retorne a este guia. Siga as instruções para implantar um farm de servidores de federação do Windows Server 2012 R2.

> [AZURE.NOTE]Ao implantar o AD FS, você deve ignorar a etapa opcional chamada: Etapa opcional: configurar um servidor de federação com o DRS (serviço de registro de dispositivo). Esta etapa não é necessária ao usar o registro de dispositivo do Active Directory do Azure. Siga as instruções para implantar um Proxy de Servidor de Federação do Windows Server 2012 R2.

Instale a atualização mais recente do Windows Server 2012 R2 em todas os seus servidores proxy e de federação do Windows Server 2012 R2. Isso pode ser feito antes ou depois de configurar seu farm de federação. A atualização pode ser encontrada aqui: atualização do Windows Server 2012 R2.


### Preparar sua floresta do Active Directory para dar suporte a dispositivos

> [AZURE.NOTE]Isso é uma operação única que você deve executar para preparar a floresta do Active Directory para dar suporte a dispositivos. Você deve fazer logon com permissões de administrador corporativo e sua floresta do Active Directory deve ter o esquema do Windows Server 2012 R2 para concluir esse procedimento.

1. No servidor de federação, abra uma janela de comando do Windows PowerShell e digite: *Initialize-ADDeviceRegistration*

2. Quando solicitado o ServiceAccountName, digite o nome da conta de serviço que você selecionou como a conta de serviço do AD FS. Se for uma conta gMSA, digite a conta no formato domínio\\nomedaconta$. Para uma conta de domínio, use o formato domínio\\nomedaconta.


### Habilitar a autenticação de dispositivo no AD FS

1. No servidor de federação, abra o console de gerenciamento do AD FS e navegue até **AD FS** > **Políticas de Autenticação**.
2. Selecione E**ditar Autenticação Primária Global...** no painel **Ações**.
3. Verifique **Habilitar a autenticação de dispositivo** e selecione **OK**.
4. Por padrão, o AD FS removerá periodicamente dispositivos não utilizados do Active Directory. Você deve desabilitar essa tarefa ao usar o registro de dispositivos do Active Directory do Azure para que os dispositivos possam ser gerenciados no Azure.


### Desabilitar a limpeza de dispositivos não utilizados
1. No servidor de federação, abra uma janela de comando do Windows PowerShell e digite: `*Set-AdfsDeviceRegistration -MaximumInactiveDays 0*`


###Configurar um relacionamento de federação com o Active Directory do Azure e configurar ferramenta de sincronização de diretório
A seção a seguir o ajudará a configurar uma relação de confiança de federação entre o Active Directory do Azure e o AD FS. Algumas etapas podem levá-lo para outra página. Siga as instruções abaixo e retorne a este guia.

Integrar o Active Directory do Azure com o Active Directory local e configurar o logon único
------------------------------------------------------------------------------------------------------

1. Faça logon no Portal do Azure como **Administrador**.
2. No painel esquerdo, selecione **Active Directory**.
3. Na guia **Diretório**, selecione o diretório.
4. Selecione a guia **Integração de Diretórios**.
5. Na integração com a seção local do active directory, localize a opção Sincronização de Diretórios e selecione Ativado.
6. Siga as etapas 1 a 4 para integrar o Active Directory do Azure ao seu diretório local. .
  1. **Adicionar domínios**
  2. **Configurar logon único e preparar para a sincronização de diretórios**. Se você já tiver implantado o AD FS, pode seguir estas sub-seções para configurar e validar confiança entre o AD FS e o Azure AD. 
      *  [Instalar o Windows PowerShell para logon único com o AD FS](https://msdn.microsoft.com/library/azure/jj151814.aspx).
      *  [Configurar confiança entre o AD FS e o Azure AD](https://msdn.microsoft.com/library/azure/jj205461.aspx).
      *  [Verificar e gerenciar o logon único com o AD FS](https://msdn.microsoft.com/library/azure/jj151809.aspx).
  	  *  [Referências adicionais do AD FS](https://msdn.microsoft.com/library/azure/dn151321.aspx)

  3. Instale e execute a ferramenta de sincronização de diretório. Se você já tiver instalado o DirSync, deve atualizar para a versão mais recente. A versão mínima requerida é 6862.0000.
  4. Verificar e gerenciar a sincronização de diretórios 
  5. Configure algumas regras de declaração adicionais no objeto de confiança de terceira parte confiável do Active Directory do Azure. Este objeto de terceira parte confiável normalmente é chamado de Plataforma de Identidade do Microsoft Office 365.

###Configure as regras de declaração de confiança de terceira parte confiável do Active Directory do Azure.

1. Abra o console de gerenciamento do AD FS e vá até AD FS > Relações de Confiança > **Relações de Confiança de Terceira Parte Confiável**. Com o botão direito no **objeto de relação de confiança de terceira parte confiável da Plataforma de Identidade do Microsoft Office 365** e selecione **Editar Regras de Declaração...
2. Na guia **Regras de Transformação de Emissão**, selecione Adicionar Regra.**
3. Selecione **Enviar Declarações Usando uma Regra Personalizada** na caixa suspensa do modelo de regra **Declaração**. Selecione **Avançar**.
4. Digite Regra de Declaração de Método de Autenticação na caixa de texto **Nome da regra de declaração**.
5. Digite a regra de declaração abaixo na caixa de texto Regra de declaração:

    ` c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"]` `=> issue(claim = c);`

6. Clique em **OK** duas vezes para concluir a caixa de diálogo.
7. Configure algumas referências de classe de autenticação adicionais no objeto de relação de confiança de terceira parte confiável do Active Directory do Azure. Este objeto de terceira parte confiável normalmente é chamado de Plataforma de Identidade do Microsoft Office 365.


###Configurar a referência de classe de autenticação de confiança de terceira parte confiável do Active Directory do Azure

No servidor de federação, abra uma janela de comando do Windows PowerShell e digite:

*Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn*


Onde <RPObjectName> é o nome do objeto de terceira parte confiável de seu objeto de confiança de terceira parte confiável do Active Directory do Azure. Esse objeto é tipicamente nomeado Plataforma de Identidade do Microsoft Office 365.

### Configurar DirSync para permitir o write-back do objeto de dispositivo
Você deve configurar a sincronização de diretório (DirSync) para permitir o write-back do objeto de dispositivo. Os dispositivos criados no Active Directory do Azure serão gravados no Active Directory. Os dispositivos criados no Active Directory do Azure podem levar até 3 horas antes de serem gravados no Active Directory.
>[Azure.Note]Você deve fazer logon com permissões de administrador corporativo para concluir o procedimento a seguir. Baixe aqui a versão mais recente do dirsync.exe.

> Uma licença do Office 365 ou licença do Azure AD Premium é necessária para permitir o write-back do dispositivo para o Active Directory.

> Se você acabou de concluir o assistente de instalação do DirSync, desconecte e entre novamente antes de continuar. Isso fará com que você tenha um token de acesso atualizado.

>Pode levar até 3 horas para objetos de dispositivo serem gravados nos serviços de domínio do Active Directory.

1. Em seu servidor de sincronização de diretório, abra uma janela de comando do Windows PowerShell e emita os seguintes comandos:

    `Import-Module DirSync`
    
    `$AADCreds = Get-Credential`
    
    `$ADCreds = Get-Credential`

2. Quando solicitado a fornecer credenciais, digite as credenciais de conta de administrador de serviço de nuvem e suas credenciais de administrador do Active Directory.

*Enable-MSOnlineObjectManagement –ObjectTypes Device –TargetCredential $AADCreds -Credential $ADCreds*

### Configurar serviços de federação para fornecer autenticação multifator
Os dispositivos que são registrados em sua organização podem ser usados como um segundo fator perfeito de autenticação. Portanto, é recomendável que você exija uma autenticação forte ao registrar um dispositivo. Você pode seguir o guia abaixo para configurar o AD FS com Azure Multi-Factor Authentication e retornar a este guia. Siga as instruções para implantar o Azure Multi-Factor Authentication.

## Adicione dispositivos à sua área de trabalho usando o registro de dispositivo do Active Directory do Azure

### Adicionar um dispositivo iOS usando o registro de dispositivo do Active Directory do Azure

O registro de dispositivo do Active Directory do Azure usa o processo de registro de perfil Over-the-Air para dispositivos iOS. Esse processo começa com o usuário se conectando à URL de registro de perfil usando o navegador Safari. O formato da URL é o seguinte:

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/"yourdomainname"

Onde <yourdomainname> é o nome de domínio que você configurou com o Active Directory do Azure. Por exemplo, se seu nome de domínio for contoso.com, a URL seria:

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/contoso.com

Há muitas maneiras diferentes de comunicar essa URL para seus usuários. Uma maneira recomendada é publicando a URL em uma mensagem de acesso de aplicativo negado personalizada no AD FS. Isso é abordado na próxima seção: Criar uma política de acesso a aplicativo e mensagem de acesso negado personalizada.

###Adicionar um dispositivo Windows 8.1 usando o registro de dispositivo do Active Directory do Azure

1. No dispositivo Windows 8.1, navegue até **Configurações do PC** > **Rede** > **Local de Trabalho**
2. Digite seu nome de usuário no formato UPN. Por exemplo: dan@contoso.com..
3. Selecione **Ingressar**.
4. Quando solicitado, entre com suas credenciais. O dispositivo agora está associado.

### Adicionar um dispositivo Android usando o registro de dispositivo do Active Directory do Azure

O tópico Azure Authenticator para Android possui instruções sobre como instalar o aplicativo autenticador do Azure em seu dispositivo Android e adicionar uma conta de trabalho. Quando uma conta de trabalho é criada com êxito em um dispositivo Android, esse dispositivo é adicionado ao local de trabalho para a organização.

### Verifique se os dispositivos registrados são gravados no Active Directory
Você pode exibir e verificar se os objetos de dispositivo foram gravados no Active Directory usando LDP.exe ou Edição ADSI. Ambos estão disponíveis com as ferramentas do administrador do Active Directory.

Por padrão, os objetos de dispositivo que são gravados no Active Directory do Azure serão colocados no mesmo domínio do farm do AD FS.

`CN=RegisteredDevices,defaultNamingContext`

###Criar uma política de acesso do aplicativo e uma mensagem de acesso negado personalizada
Considere o seguinte cenário: você cria uma relação de confiança de aplicativo com a terceira parte confiável no AD FS e configura uma regra de autorização de emissão que permite apenas dispositivos registrados. Agora somente os dispositivos que estão registrados têm permissão para acessar o aplicativo. Para tornar mais fácil o acesso ao aplicativo para os usuários, você deve configurar uma mensagem de acesso negado personalizada que inclui instruções sobre como adicionar seu dispositivo. Agora os usuários têm uma maneira perfeita de registrar seus dispositivos para acessar um aplicativo.

As etapas a seguir mostram como implementar esse cenário.
>[AZURE.NOTE]Esta seção pressupõe que você já tenha configurado uma relação de confiança de terceira parte confiável para o seu aplicativo no AD FS.

1. Abra a ferramenta AD FS MMC e vá até AD FS > Relações de Confiança > Confiança de Terceira Parte Confiável
2. Localize o aplicativo ao qual a nova regra de acesso se aplica. Clique com o botão direito do mouse no aplicativo e selecione Editar Regras de Declaração...
3. Selecione a guia **Regras de Autorização de Emissão** e selecione **Adicionar Regra...**
4. Da lista suspensa do modelo **Regra de declaração**, selecione **Permitir ou negar usuários baseados em uma declaração de entrada**. Selecione **Avançar**.
5. No nome da regra de declaração: tipo de campo: **Permitir o acesso de dispositivos registrados**
6. Do tipo de declaração de entrada: lista suspensa, selecione **É um Usuário Registrado**.
7. No valor Declaração de entrada: campo, digite: **true**
9. Selecione **Concluir** e **Aplicar**.
10. Remova as regras que são mais permissivas do que a regra que você acabou de criar. Por exemplo, remova o padrão 
8. Selecione o botão de opção **Permitir o acesso a usuários com esta declaração de entrada**. Regra Permitir o acesso a todos os usuários.

Seu aplicativo agora está configurado para permitir acesso somente quando o usuário for proveniente de um dispositivo registrado e adicionado ao local de trabalho. Para políticas de acesso mais avançadas, confira Gerenciar riscos com controle de acesso multifator.

Em seguida, você configurará uma mensagem de erro personalizada para o seu aplicativo. A mensagem de erro avisará aos usuários que eles devem adicionar seu dispositivo ao local de trabalho antes de poder acessar o aplicativo. Você pode criar uma mensagem personalizada de acesso negado de aplicativo usando HTML personalizado e o Windows PowerShell.

No servidor de federação, abra uma janela de comando do Windows PowerShell e digite o seguinte comando. Substitua partes do comando com itens que são específicos para o seu sistema:

`Set-AdfsRelyingPartyWebContent -Name "relying party trust name" -ErrorPageAuthorizationErrorMessage ` Você deve registrar seu dispositivo antes de poder acessar este aplicativo.

**Se você estiver usando um dispositivo iOS, selecione este link para adicionar seu dispositivo**:

    a href='https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/yourdomain.com

Adicione o dispositivo iOS à área de trabalho.


**Se você estiver usando um dispositivo Windows 8.1**, pode adicionar seu dispositivo acessando **Configurações do PC**> **Rede** > **Local de Trabalho**.


Onde "**nome de confiança de terceira parte confiável**" é o nome do objeto de confiança de terceira parte confiável do seu aplicativos no AD FS. Onde seudomínio.com é o nome de domínio que você configurou com o Active Directory do Azure. Por exemplo, contoso.com. Não deixe de remover qualquer quebra de linha (se houver) do conteúdo html que você passa para o cmdlet S**et-AdfsRelyingPartyWebContent**.

<!---HONumber=August15_HO9-->