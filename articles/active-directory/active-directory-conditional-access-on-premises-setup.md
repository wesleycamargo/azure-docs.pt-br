<properties
	pageTitle="Configurando o acesso condicional no local usando o registro do dispositivo do Active Directory do Azure | Microsoft Azure"
	description="Um passo a passo para habilitar o acesso condicional para aplicativos locais usando o AD FS (Serviço de Federação do Active Directory) no Windows Server 2012 R2."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/23/2016"
	ms.author="femila"/>


# Configurando o acesso condicional no local usando o registro do dispositivo do Active Directory do Azure

Dispositivos de propriedade dos seus usuários podem ser marcados como conhecidos pela sua organização se for exigido aos usuários que adicionem seus dispositivos ao serviço do registro de dispositivo do Active Directory do Azure no ambiente de trabalho. Abaixo está um passo a passo para habilitar o acesso condicional para aplicativos locais usando o AD FS (Serviço de Federação do Active Directory) no Windows Server 2012 R2.

> [AZURE.NOTE]
A licença do Office 365 ou a do Azure AD Premium é necessária quando usar dispositivos registrados nas políticas de acesso condicional do serviço de registro de dispositivo do Active Directory do Azure. Isso inclui as políticas impostas pelo AD FS (Serviços de Federação do Active Directory) para recursos locais.

Para saber mais sobre os cenários de acesso condicional local, confira [Ingressar no local de trabalho de qualquer dispositivo para SSO e segundo fator de autenticação entre aplicativos da empresa](https://technet.microsoft.com/library/dn280945.aspx).

Esses recursos estão disponíveis para os clientes que comprarem uma licença Active Directory Premium do Azure.

Dispositivos com suporte
-------------------------------------------------------------------------
* Dispositivos ingressados ao domínio do Windows 7.
* Dispositivos do Windows 8.1 pessoais e adicionados ao domínio.
* iOS 6 e versões posteriores, para o navegador Safari
* Android 4.0 ou posterior, Samsung GS3 ou telefones superiores, Samsung Note2 ou tablets superiores.


Pré-requisitos do cenário
------------------------------------------------------------------------
* Assinatura do Office 365 ou Azure Active Directory Premium
* Um locatário do Azure Active Directory
* Windows Server Active Directory (Windows Server 2008 ou superior)
* Esquema atualizado no Windows Server 2012 R2
* Licença do Azure Active Directory Premium
* Serviços de Federação do Windows Server 2012 R2, configurados para logon único ao Azure AD
* Microsoft Azure Active Directory Connect (Azure AD Connect) do Proxy do Aplicativo Web do Windows Server 2012 R2. [Baixe o Azure AD Connect aqui](http://www.microsoft.com/pt-BR/download/details.aspx?id=47594).
* Domínio verificado.

Problemas conhecidos nesta versão
-------------------------------------------------------------------------------
* As políticas de acesso condicional baseadas em dispositivo exigem o write-back do objeto de dispositivo para o Active Directory do Active Directory do Azure. Pode levar até 3 horas para que o write-back de objetos de dispositivo seja realizado no Active Directory.
* Os dispositivos iOS 7 sempre solicitarão que o usuário selecione um certificado durante a autenticação de certificado de cliente.
* Algumas versões do iOS8 anteriores à iOS 8.3 não funcionam.

## Suposições de cenário
Este cenário presume que você tem um ambiente híbrido que consiste em um locatário do Azure AD e um Active Directory local. Esses locatários devem ser conectados usando o Azure AD Connect e com um domínio verificado e AD FS para SSO. A lista de verificação a seguir ajudará você a configurar seu ambiente para o estágio descrito acima.

Lista de verificação: pré-requisitos para o cenário de Acesso Condicional
--------------------------------------------------------------
Conecte seu locatário do Azure AD ao Active Directory local.

## Configurar o Serviço de Registro de Dispositivos do Active Directory do Azure
Use este guia para implantar e configurar o serviço de registro de dispositivo do Active Directory do Azure para a sua organização.

Este guia pressupõe que você tenha configurado o Windows Server Active Directory e assinado o Active Directory do Microsoft Azure. Confira os pré-requisitos acima.

Para implantar o serviço de registro de dispositivo do Active Directory do Azure com seu locatário do Active Directory do Azure, complete as tarefas na lista de verificação abaixo, na ordem. Quando um link de referência levá-lo para um tópico conceitual, retorne a esta lista de verificação depois de revisar o tópico conceitual para que você possa prosseguir com as tarefas restantes desta lista de verificação. Algumas tarefas incluirão uma etapa de validação de cenário que pode ajudá-lo a confirmar que a etapa foi concluída com êxito.

## Parte 1: habilitar o Registro de Dispositivos do Active Directory do Azure

Siga a lista de verificação a seguir para habilitar e configurar o serviço de registro de dispositivo do Active Directory do Azure.

| Tarefa | Referência |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Habilite o registro de dispositivos em seu locatário do Active Directory do Azure para permitir que dispositivos sejam adicionados ao local de trabalho. Por padrão, a autenticação multifator não está habilitada para o serviço. No entanto, a autenticação multifator é recomendável ao registrar um dispositivo. Antes de habilitar a autenticação multifator em ADRS, verifique se o AD FS está configurado para um provedor de autenticação multifator. | [Habilitar o registro de dispositivos do Active Directory do Azure](active-directory-conditional-access-device-registration-overview.md) |
| Os dispositivos detectarão o serviço de registro de dispositivos do Active Directory do Azure procurando registros DNS conhecidos. Você deve configurar o DNS da sua empresa para que os dispositivos possam descobrir seu serviço de registro de dispositivo do Active Directory do Azure. | [Configurar a descoberta de registro de dispositivos do Active Directory do Azure](active-directory-conditional-access-device-registration-overview.md) |

##Parte 2: implantar e configurar os Serviços de Federação do Active Directory do Windows Server 2012 R2 e configurar uma relação de federação com o AD do Azure

| Tarefa | Referência |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Implantar domínio dos Serviços de Domínio do Active Directory com as extensões de esquema do Windows Server 2012 R2. Não é necessário atualizar nenhum dos seus controladores de domínio para o Windows Server 2012 R2. A atualização do esquema é o único requisito. | [Atualizar seu Esquema de Serviços de Domínio do Active Directory] (#Atualizar seu Esquema dos Serviços de Domínio do Active Directory) |
| Os dispositivos detectarão o serviço de registro de dispositivos do Active Directory do Azure procurando registros DNS conhecidos. Você deve configurar o DNS da sua empresa para que os dispositivos possam descobrir seu serviço de registro de dispositivo do Active Directory do Azure. | [Preparar seu Active Directory para dar suporte a dispositivos] (#Preparar seu Active Directory para dar suporte a dispositivos) |


##Parte 3: habilitar write-back de dispositivos no AD do Azure

| Tarefa | Referência |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Conclua a parte 2 de Habilitar write-back de dispositivos no Azure AD Connect. Após a conclusão, retorne para este guia. | [Habilitar write-back de dispositivos no Azure AD Connect] (#Atualizar o Esquema de Serviços de Domínio do Active Directory) |


##[Opcional] Parte 4: habilitar a autenticação multifator

É altamente recomendável que você configure uma das várias opções para autenticação multifator. Se você quiser exigir MFA, consulte [Escolha a solução de segurança multifator para você](../multi-factor-authentication/multi-factor-authentication-get-started.md). Ele inclui uma descrição de cada solução e links para ajudá-lo a configurar a solução de sua escolha.

## Parte 5: verificação

A implantação foi concluída. Agora você pode experimentar alguns cenários. Siga os links abaixo para fazer experiências com o serviço e se familiarizar com os recursos


| Tarefa | Referência |
|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| Adicione alguns dispositivos à sua área de trabalho usando o Registro de Dispositivos do Active Directory do Azure. Você pode associar dispositivos Android, iOS e Windows | [Associe dispositivos à sua área de trabalho usando o Registro de Dispositivos do Active Directory do Azure] (#Associe dispositivos à sua área de trabalho usando o Registro de Dispositivos do Active Directory do Azure) |
| Você pode exibir e habilitar/desabilitar dispositivos registrados usando o Portal de Administrador. Nesta tarefa, você exibirá alguns dispositivos registrados usando o Portal do Administrador. | [Visão geral do registro de dispositivos do Azure Active Directory](active-directory-conditional-access-device-registration-overview.md) |
| Verifique se o write-back dos objetos de dispositivo é feito do Active Directory do Azure para o Windows Server Active Directory. | [Verificar se o write-back dos dispositivos registrados é feito no Active Directory] (#Verificar se o write-back dos dispositivos registrados é feito no Active Directory) |
| Agora que os usuários podem registrar seus dispositivos, você pode criar políticas de acesso de aplicativo no AD FS que permitem apenas dispositivos registrados. Nesta tarefa, você criará uma regra de acesso de aplicativo e uma mensagem de acesso negado personalizada | [Criar uma política de acesso do aplicativo e uma mensagem de acesso negado personalizada] (#Criar uma política de acesso do aplicativo e uma mensagem de acesso negado personalizada) |



## Integrar o Azure Active Directory ao Active Directory local
Isso o ajudará a integrar o locatário do Azure AD ao Active Directory local, usando o Azure AD Connect. Embora as etapas estejam disponíveis no portal clássico do Azure, anote quaisquer eventuais instruções especiais listadas nesta seção.

1.	Faça logon no portal clássico do Azure usando uma conta que seja um Administrador Global no Azure AD.
2.	No painel esquerdo, selecione **Active Directory**.
3.	Na guia **Diretório**, selecione o diretório.
4.	Selecione a guia **Integração de Diretórios**.
5.	Na seção **implantar e gerenciar**, siga as etapas 1 a 3 para integrar o Azure Active Directory ao diretório local.
  1.	Adicionar domínios.
  2.	Instalar e executar o Azure AD Connect: instale o Azure AD Connect usando as instruções a seguir, [Instalação personalizada do Azure AD Connect](active-directory-aadconnect-get-started-custom.md).
  3. Verificar e gerenciar a sincronização de diretórios. Instruções de logon único estão disponíveis nessa etapa.
  

  >[AZURE.NOTE] Configure a Federação com o AD FS conforme descrito no documento vinculado acima.
 Você não precisa configurar nenhum dos recursos de visualização.


## Atualizar o esquema dos Serviços de Domínio do Active Directory

> [AZURE.NOTE]
A atualização do esquema do Active Directory não pode ser desfeita. É recomendável executar isso primeiro em um ambiente de teste.

1. Faça logon no controlador de domínio com uma conta que tenha direitos de administrador corporativo e de administrador de esquema.
2. Copie o diretório **[media]\\support\\adprep** e seus subdiretórios em um dos controladores de domínio do Active Directory.
3. Onde [media] é o caminho para a mídia de instalação do Windows Server 2012 R2.
4. Em um prompt de comando, navegue até o diretório adprep e execute: **adprep.exe /forestprep**. Siga as instruções na tela para concluir a atualização do esquema.

## Preparar seu Active Directory para dar suporte a dispositivos

>[AZURE.NOTE] Isso é uma operação única que você deve executar para preparar a floresta do Active Directory para dar suporte a dispositivos. Você deve fazer logon com permissões de administrador corporativo e sua floresta do Active Directory deve ter o esquema do Windows Server 2012 R2 para concluir esse procedimento.


##Preparar sua floresta do Active Directory para dar suporte a dispositivos

> [AZURE.NOTE]
Isso é uma operação única que você deve executar para preparar a floresta do Active Directory para dar suporte a dispositivos. Você deve fazer logon com permissões de administrador corporativo e sua floresta do Active Directory deve ter o esquema do Windows Server 2012 R2 para concluir esse procedimento.

### Prepare sua floresta do Active Directory

1.	No servidor de federação, abra uma janela de comando do Windows PowerShell e digite: Initialize-ADDeviceRegistration
2.	Quando solicitado que você informe o **ServiceAccountName**, digite o nome da conta de serviço que você selecionou como a conta de serviço do AD FS. Se for uma conta gMSA, digite a conta no formato **domínio\\nomedaconta$**. Para uma conta de domínio, use o formato **domínio\\nomedaconta**.



### Habilitar a autenticação de dispositivo no AD FS

1. No servidor de federação, abra o console de gerenciamento do AD FS e navegue até **AD FS** > **Políticas de Autenticação**.
2. Selecione **Editar Autenticação Primária Global...** no painel **Ações**.
3. Marque **Habilitar a autenticação de dispositivo** e selecione**OK**.
4. Por padrão, o AD FS removerá periodicamente dispositivos não utilizados do Active Directory. Você deve desabilitar essa tarefa ao usar o registro de dispositivos do Active Directory do Azure para que os dispositivos possam ser gerenciados no Azure.


### Desabilitar a limpeza de dispositivos não utilizados
1. No servidor de federação, abra uma janela de comando do Windows PowerShell e digite: Set-AdfsDeviceRegistration -MaximumInactiveDays 0

### Preparar o Azure AD Connect para write-back do dispositivo

1.	Conclua a Parte 1: Preparar o Azure AD Connect


## Adicione dispositivos à sua área de trabalho usando o registro de dispositivo do Active Directory do Azure

### Adicionar um dispositivo iOS usando o registro de dispositivo do Active Directory do Azure

O registro de dispositivo do Active Directory do Azure usa o processo de registro de perfil Over-the-Air para dispositivos iOS. Esse processo começa com o usuário se conectando à URL de registro de perfil usando o navegador Safari. O formato da URL é o seguinte:

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/"yourdomainname"

Onde `yourdomainname` é o nome de domínio que você configurou com o Active Directory do Azure. Por exemplo, se seu nome de domínio for contoso.com, a URL seria:

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/contoso.com

Há muitas maneiras diferentes de comunicar essa URL para seus usuários. Uma maneira recomendada é publicando a URL em uma mensagem de acesso de aplicativo negado personalizada no AD FS. Isso é abordado na próxima seção: [Criar uma política de acesso do aplicativo e uma mensagem de acesso negado personalizada] (#Criar uma política de acesso do aplicativo e uma mensagem de acesso negado personalizada).

###Adicionar um dispositivo Windows 8.1 usando o registro de dispositivo do Active Directory do Azure

1. No dispositivo Windows 8.1, navegue até **Configurações do PC** > **Rede** > **Local de Trabalho**.
2. Digite seu nome de usuário no formato UPN. Por exemplo: dan@contoso.com..
3. Selecione **Ingressar**.
4. Quando solicitado, entre com suas credenciais. O dispositivo agora está associado.

###Adicionar um dispositivo Windows 7 usando o registro de dispositivo do Active Directory do Azure
Para registrar dispositivos Windows 7 adicionados ao domínio, é necessário implantar o pacote de software de registro de dispositivo. O pacote de software chama-se Workplace Join for Windows 7 e está disponível para download no [site Microsoft Connect](https://connect.microsoft.com/site1164). Há instruções sobre como usar o pacote disponíveis em [Configurar o registro automático de dispositivos para dispositivos Windows 7 ingressados no domínio](active-directory-conditional-access-automatic-device-registration-windows7.md).

### Adicionar um dispositivo Android usando o registro de dispositivo do Active Directory do Azure

O [tópico Azure Authenticator para Android](active-directory-conditional-access-azure-authenticator-app.md) tem instruções sobre como instalar o aplicativo autenticador do Azure em seu dispositivo Android e adicionar uma conta de trabalho. Quando uma conta de trabalho é criada com êxito em um dispositivo Android, esse dispositivo é adicionado ao local de trabalho para a organização.

## Verifique se os dispositivos registrados são gravados no Active Directory
Você pode exibir e verificar se os objetos de dispositivo foram gravados no Active Directory usando LDP.exe ou Edição ADSI. Ambos estão disponíveis com as ferramentas do administrador do Active Directory.

Por padrão, os objetos de dispositivo que são gravados no Active Directory do Azure serão colocados no mesmo domínio do farm do AD FS.

    CN=RegisteredDevices,defaultNamingContext

## Criar uma política de acesso do aplicativo e uma mensagem de acesso negado personalizada
Considere o seguinte cenário: você cria uma relação de confiança de aplicativo com a terceira parte confiável no AD FS e configura uma regra de autorização de emissão que permite apenas dispositivos registrados. Agora somente os dispositivos que estão registrados têm permissão para acessar o aplicativo. Para tornar mais fácil o acesso ao aplicativo para os usuários, você deve configurar uma mensagem de acesso negado personalizada que inclui instruções sobre como adicionar seu dispositivo. Agora os usuários têm uma maneira perfeita de registrar seus dispositivos para acessar um aplicativo.

As etapas a seguir mostram como implementar esse cenário.

>[AZURE.NOTE]
Esta seção pressupõe que você já tenha configurado uma relação de confiança de terceira parte confiável para o seu aplicativo no AD FS.

1. Abra a ferramenta AD FS MMC e vá até AD FS > Relações de Confiança > Confiança de Terceira Parte Confiável
2. Localize o aplicativo ao qual a nova regra de acesso se aplica. Clique com o botão direito do mouse no aplicativo e selecione Editar Regras de Declaração...
3. Selecione a guia **Regras de Autorização de Emissão** e selecione **Adicionar Regra...**
4. Na lista suspensa do modelo **Regra de declaração**, selecione **Permitir ou negar usuários baseados em uma declaração de entrada**. Selecione **Avançar**.
5. No campo nome da Regra de Declaração:, digite: **Permitir o acesso de dispositivos registrados**
6. Na lista suspensa Tipo de declaração de Entrada:, selecione **É um Usuário Registrado**.
7. No campo Valor da declaração de entrada:, digite: **true**
8. Selecione o botão de opção **Permitir o acesso a usuários com esta declaração de entrada**.
9. Selecione **Concluir** e **Aplicar**.
10. Remova as regras que são mais permissivas do que a regra que você acabou de criar. Por exemplo, remova a regra padrão **Permitir o Acesso a todos os Usuários**.

Seu aplicativo agora está configurado para permitir acesso somente quando o usuário for proveniente de um dispositivo registrado e adicionado ao local de trabalho. Para políticas de acesso mais avançadas, confira [Gerenciar Riscos com Controle de Acesso Multifator](https://technet.microsoft.com/library/dn280949.aspx).

Em seguida, você configurará uma mensagem de erro personalizada para o seu aplicativo. A mensagem de erro avisará aos usuários que eles devem adicionar seu dispositivo ao local de trabalho antes de poder acessar o aplicativo. Você pode criar uma mensagem personalizada de acesso negado de aplicativo usando HTML personalizado e o Windows PowerShell.

No servidor de federação, abra uma janela de comando do Windows PowerShell e digite o seguinte comando. Substitua partes do comando com itens que são específicos para o seu sistema:

    Set-AdfsRelyingPartyWebContent -Name "relying party trust name" -ErrorPageAuthorizationErrorMessage
Você deve registrar seu dispositivo antes de poder acessar este aplicativo.

**Se você estiver usando um dispositivo iOS, selecione este link para adicionar seu dispositivo**:

    a href='https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/yourdomain.com

Adicione o dispositivo iOS à área de trabalho.


**Se você estiver usando um dispositivo Windows 8.1**, pode adicionar seu dispositivo acessando **Configurações do PC**> **Rede** >**Local de Trabalho**.


Em que "**nome de confiança de terceira parte confiável**" é o nome do objeto de confiança de terceira parte confiável do seu aplicativo no AD FS. Em que **seudomínio.com** é o nome de domínio que você configurou com o Active Directory do Azure. Por exemplo, contoso.com. Não deixe de remover qualquer quebra de linha (se houver) do conteúdo html que você passa para o cmdlet **Set-AdfsRelyingPartyWebContent**.


Agora, quando os usuários acessam seu aplicativo e um dispositivo que não está registrado com o Serviço de Registro de Dispositivos do Active Directory do Azure, eles receberão uma página com aparência semelhante à captura de tela abaixo.

![Captura de tela de um erro quando os usuários não tiverem registrado seu dispositivo com o AD do Azure](./media/active-directory-conditional-access/error-azureDRS-device-not-registered.gif)

##Artigos relacionados

- [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)

<!---HONumber=AcomDC_0629_2016-->