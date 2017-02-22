---
title: "Portal do usuário para o servidor Azure MFA | Microsoft Docs"
description: "Esta é a página de autenticação do Azure Multi-Factor Authentication que descreve como começar com o Azure MFA e o Portal do Usuário."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 06b419fa-3507-4980-96a4-d2e3960e1772
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/04/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 1222223f8c45249402bfdd04c8754074f877e132
ms.openlocfilehash: 1236489212b2a9c421972599a12511d5bc42efdf


---
# <a name="deploy-the-user-portal-for-the-azure-multi-factor-authentication-server"></a>Implante o Portal do Usuário para o Servidor Autenticação Multifator do Azure
O portal do usuário permite ao administrador instalar e configurar o Portal do Usuário do Autenticação Multifator do Azure. O portal do usuário é um site do IIS que permite aos usuários se registrar no Autenticação Multifator do Azure e manter suas contas. Um usuário pode alterar o número de telefone, alterar o PIN ou optar por ignorar a verificação em duas etapas durante a próxima entrada.

Os usuários entram no Portal do usuário usando seu nome de usuário normal e uma senha e, depois, concluem uma chamada de verificação em duas etapas ou respondem a perguntas de segurança para concluir a autenticação. Se o registro do usuário for autorizado, o usuário configurará seu número de telefone e PIN na primeira vez que entrar no portal do usuário.

Os Administradores do Portal do Usuário podem receber uma configuração e permissão para adicionar novos usuários e atualizar usuários existentes.

<center>![Configuração](./media/multi-factor-authentication-get-started-portal/install.png)</center>

## <a name="deploy-the-user-portal-on-the-same-server-as-the-azure-multi-factor-authentication-server"></a>Implantar o Portal do Usuário no servidor que o Servidor Autenticação Multifator do Azure
Os seguintes pré-requisitos são necessários para instalar o portal do usuário no mesmo servidor que o Servidor Autenticação Multifator do Azure:

* O IIS, incluindo a compatibilidade da metabase do IIS 6 e asp.net (para o IIS 7 ou superior)
* O usuário conectado deve ter direitos de administrador para o computador e o domínio, se aplicável.  Isso ocorre porque a conta precisa ter permissões para criar grupos de segurança do Active Directory.

### <a name="to-deploy-the-user-portal"></a>Para implantar o portal do usuário
1. Dentro do Servidor Autenticação Multifator do Azure: clique no ícone do **Portal do Usuário** no menu à esquerda e clique em **Instalar Portal do Usuário**.
2. Clique em **Próximo**.
3. Clique em **Próximo**.
4. Se o computador estiver associado a um domínio e o Active Directory não estiver configurado para comunicação segura entre o portal do usuário e o serviço Autenticação Multifator do Azure, será exibida a etapa do Active Directory. Clique no botão **Avançar** para cocluir automaticamente essa configuração.
5. Clique em **Próximo**.
6. Clique em **Próximo**.
7. Clique em **fechar**
8. Abra um navegador da web em qualquer computador e navegue até a URL onde o portal do usuário foi instalado (por exemplo, https://www.publicwebsite.com/MultiFactorAuth ). Certifique-se de que nenhum aviso de certificado ou erro seja exibido.

<center>![Configuração](./media/multi-factor-authentication-get-started-portal/portal.png)</center>

## <a name="deploy-the-azure-multi-factor-authentication-server-user-portal-on-a-separate-server"></a>Implantar o Portal do Usuário do Servidor Autenticação Multifator do Azure em um servidor separado
Para permitir que o aplicativo Microsoft Authenticator se comunique com o portal do usuário, conclua os seguintes requisitos: 

* Você deve estar usando a v6.0 ou superior do Servidor Azure Multi-Factor Authentication.
* O portal do usuário deve estar instalado em um servidor da web da Internet executando o Microsoft® Internet Information Services (IIS) 6.x, o IIS 7.x ou superior.
* Ao usar o IIS 6.x, certifique-se de que o ASP.NET v2.0.50727 esteja instalado, registrado e definido como **Permitido**.
* Os serviços de função obrigatórios ao usar o IIS 7.x ou superior incluem ASP.NET e compatibilidade de Metabase do IIS 6.
* O portal do usuário deve ser protegido com um certificado SSL.
* O SDK de Serviço Web do Autenticação Multifator do Azure deve estar instalado no IIS 6.x, o IIS 7.x ou superior no mesmo servidor em que o Servidor Autenticação Multifator do Azure está instalado.
* O SDK de Serviço Web do Azure Multi-Factor Authentication deve estar protegido com um certificado SSL.
* O portal do usuário deve ser capaz de conectar ao SDK de Serviço Web do Autenticação Multifator do Azure por SSL.
* O portal do usuário deve ser capaz de realizar a autenticação para o SDK do Serviço Web Autenticação Multifator do Azure usando as credenciais de uma conta de serviço no grupo de segurança "PhoneFactor Admins". Essa conta de serviço e o grupo existem no Active Directory se o Servidor Azure Multi-Factor Authentication for executado em um servidor ingressado em um domínio. Essa conta de serviço e o grupo existem localmente no Servidor Azure Multi-Factor Authentication se ele não tiver ingressado em um domínio.

A instalação do Portal do Usuário em um servidor diferente do Servidor Azure Multi-Factor Authentication exige estas três etapas:

1. Instalar o SDK de Serviço Web
2. Instalar o Portal do Usuário
3. Configurar as definições do Portal do Usuário no Servidor Azure Multi-Factor Authentication

### <a name="step-1-install-the-web-service-sdk"></a>Etapa 1: instalar o SDK de serviço Web
Se o SDK de Serviço Web do Azure Multi-Factor Authentication já não estiver instalado no Servidor do Azure Multi-Factor Authentication, vá até esse servidor e abra o Servidor Azure Multi-Factor Authentication. Clique no ícone do **SDK do Serviço Web** e em **Instalar o SDK do Serviço Web**. Siga as instruções apresentadas. 

O SDK de Serviço Web deve ser protegido com um certificado SSL. Um certificado autoassinado funciona para essa finalidade, mas ele deve ser importado para o repositório de "Autoridades de Certificação Confiáveis" da conta do Computador Local no servidor. Agora o SDK do Serviço Web confia no certificado ao iniciar a conexão SSL.

<center>![Configuração](./media/multi-factor-authentication-get-started-portal/sdk.png)</center>

### <a name="step-2-install-the-user-portal"></a>Etapa 2: instalar o portal do usuário
Antes de instalar o Portal do Usuário em um servidor separado, esteja ciente das seguintes práticas recomendadas:

* É recomendável abrir um navegador Web no servidor Web da Internet e navegar até a URL do SDK de Serviço Web que foi inserido no arquivo web.config. Se o navegador conseguir chegar ao serviço Web com êxito, ele deve solicitar credenciais. Insira o nome de usuário e a senha que foram inseridos no arquivo web.config exatamente como ele aparece no arquivo. Certifique-se de que nenhum aviso de certificado ou erro seja exibido.
* Se um proxy reverso ou firewall estiver à frente do servidor Web do portal do usuário e executando o descarregamento de SSL, edite o arquivo web.config do portal do usuário e adicione a seguinte chave à seção `<appSettings>`, para que o Portal do Usuário possa usar http em vez de https:

    `<add key="SSL_REQUIRED" value="false"/>`

#### <a name="to-install-the-user-portal"></a>Para instalar o Portal do Usuário
1. Abra o Windows Explorer no Servidor Azure Multi-Factor Authentication e navegue até a pasta onde o Servidor Azure Multi-Factor Authentication está instalado (por exemplo, C:\Arquivos de Programas\Servidor Multi-Factor Authentication). Escolha a versão de 32 bits ou 64 bits do arquivo de instalação MultiFactorAuthenticationUserPortalSetup conforme apropriado para o servidor em que o Portal do Usuário será instalado. Copie o arquivo de instalação no servidor da Internet.
2. No servidor da web da Internet, o arquivo de instalação deve ser executado com direitos de administrador. A maneira mais fácil de fazer isso é abrir um prompt de comando como administrador e navegar até o local onde o arquivo de instalação foi copiado.
3. Execute o arquivo de instalação MultiFactorAuthenticationUserPortalSetup64, altere o nome de Site e Diretório Virtual, se desejado.
4. Após concluir a instalação do Portal do Usuário, navegue até C:\inetpub\wwwroot\MultiFactorAuth (ou o diretório apropriado com base no nome do diretório virtual) e edite o arquivo web.config.
5. Localize a chave USE_WEB_SERVICE_SDK e altere o valor de false para true. Localize as chaves WEB_SERVICE_SDK_AUTHENTICATION_USERNAME e WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD e defina os valores para o nome de usuário e a senha da conta de serviço no grupo de segurança PhoneFactor Admins (confira a seção Requisitos). Digite o Nome de usuário e Senha entre as aspas no final da linha, (value=””/>). Você deve usar um nome de usuário qualificado (por exemplo, domínio\nomedeusuário ou máquina\nomedeusuário)
6. Localize a configuração pfup_pfwssdk_PfWsSdk e altere o valor de “http://localhost:4898/PfWsSdk.asmx” para a URL do SDK de Serviço Web em execução no Servidor Azure Multi-Factor Authentication (por exemplo, https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx). Como o SSL é usado para essa conexão, confira o SDK do Serviço Web pelo nome do servidor, não pelo endereço IP, pois o certificado SSL foi emitido para o nome do servidor. Se o nome do servidor não resolver para um endereço IP do servidor da Internet, adicione uma entrada ao arquivo de hosts nesse servidor para mapear o nome do Servidor Azure Multi-Factor Authentication para seu endereço IP. Depois de feitas as alterações, salve o arquivo web.config.

    Acesse [Proteger seus recursos usando o Servidor de Autenticação Multifator do Azure com o AD FS](multi-factor-authentication-get-started-adfs-w2k12.md#edit-the-multifactorauthenticationadfsadapterconfig-file) para obter mais detalhes sobre como editar o arquivo de configuração.

7. Se o site em que o portal do usuário foi instalado (por exemplo, Site Padrão) ainda não tiver sido vinculado a um certificado assinado publicamente, instale o certificado no servidor, abra o Gerenciador do IIS e associe o certificado ao site.
8. Abra um navegador da web em qualquer computador e navegue até a URL onde o Portal do Usuário foi instalado (por exemplo, https://www.publicwebsite.com/MultiFactorAuth). Certifique-se de que nenhum aviso de certificado ou erro seja exibido.

### <a name="step-3-configure-the-user-portal-settings-in-the-azure-multi-factor-authentication-server"></a>Etapa 3: definir as configurações do Portal do Usuário no Servidor Autenticação Multifator do Azure
Agora que o portal do usuário está instalado, você precisa configurar o Servidor Autenticação Multifator do Azure para trabalhar com o portal.

O servidor Azure Multi-Factor Authentication fornece várias opções para o portal do usuário.  A tabela a seguir fornece uma lista dessas opções e uma explicação de como elas são usadas.

| Configurações do Portal de Usuário | Descrição |
|:--- |:--- |
| URL do portal do usuário | Insira a URL de onde o portal está sendo hospedado. |
| Autenticação primária | Especifique o tipo de autenticação a ser usado ao entrar no portal.  Autenticação do Windows, Radius ou LDAP. |
| Permitir que os usuários façam logon | Permita que os usuários insiram um nome de usuário e uma senha na página de entrada para o portal do usuário.  Se não forem marcadas, as caixas ficarão esmaecidas. |
| Permitir registro de usuário | Permita que o usuário se registre na Autenticação Multifator levando-os para uma tela de instalação que solicita informações adicionais, como número de telefone. O prompt de telefone de backup permite que os usuários especifiquem um número de telefone secundário. O prompt de token OATH permite que os usuários especifiquem um token OATH de terceiros. |
| Permitir que os usuários iniciem Desvio Único | Permitir que os usuários iniciem um desvio único.  Se um usuário tiver essa configuração, ela terá efeito na próxima vez em que ele entrar. O prompt de ignorar segundos fornece ao usuário uma caixa para que ele possa alterar o padrão de 300 segundos. Caso contrário, o desvio único é válido somente por 300 segundos. |
| Permitir que os usuários selecionem o método | Permita aos usuários especificar o método de contato principal.  Isso pode ser uma chamada telefônica, uma mensagem de texto, um aplicativo móvel ou token OATH. |
| Permitir que os usuários selecionem o idioma | Permita que os usuários alterem o idioma usado para chamada telefônica, mensagem de texto, aplicativo móvel ou token OATH. |
| Permitir que os usuários ativem aplicativos móveis | Permita que os usuários gerem um código de ativação para concluir o processo de ativação de aplicativo móvel que seja usado com o servidor.  Você também pode definir o número de dispositivos em que eles podem ativar o aplicativo, entre 1 e 10. |
| Usar perguntas de segurança para fallback | Permita perguntas de segurança caso a verificação em duas etapas falhe.  Você pode especificar o número de perguntas de segurança que devem ser respondidas com sucesso. |
| Permitir que os usuários associem token OATH de terceiros | Permita que os usuários especifiquem um token OATH de terceiros. |
| Usar token OATH para fallback | Permita o uso de um token OATH caso a verificação em duas etapas não seja bem-sucedida. Você também pode especificar o tempo limite da sessão em minutos. |
| Habilitar o registro em log | Habilite o registro em log no portal do usuário. Os arquivos de log estão localizados em: C:\Program Files\Multi-Factor Authentication Server\Logs. |

A maioria dessas configurações ficam visíveis para o usuário quando ele for habilitado e conectado ao portal do usuário.

![Configurações do Portal de Usuário](./media/multi-factor-authentication-get-started-portal/portalsettings.png)

### <a name="to-configure-the-user-portal-settings-in-the-azure-multi-factor-authentication-server"></a>Para configurar as definições do Portal do Usuário no Servidor Azure Multi-Factor Authentication
1. No Servidor Autenticação Multifator do Azure, clique no ícone do **Portal do Usuário**. Na guia Configurações, insira a URL no Portal do Usuário na caixa de texto **URL do Portal do Usuário**. Essa URL é incluída em emails que são enviados aos usuários quando eles são importados para o Servidor Autenticação Multifator do Azure se a funcionalidade de email tiver sido habilitada.
2. Escolha as configurações que você deseja usar no Portal do Usuário. Por exemplo, se os usuários tiverem permissão para controlar seus métodos de autenticação, verifique se **Permitir que usuários selecionem o método** está selecionado junto com os métodos que eles podem escolher.
3. Clique no link **Ajuda** no canto superior direito para ajudar a entender as configurações exibidas.

<center>![Configuração](./media/multi-factor-authentication-get-started-portal/config.png)</center>


## <a name="administrators-tab"></a>Guia Administradores
Essa guia simplesmente permite que você adicione os usuários com privilégios administrativos.  Ao adicionar um administrador, você pode ajustar as permissões que ele recebe. Clique no botão **Adicionar**, selecione um usuário e suas permissões e clique em **Adicionar**.

![Administradores do portal do usuário](./media/multi-factor-authentication-get-started-portal/admin.png)

## <a name="security-questions"></a>Perguntas de segurança
Essa guia permite que você especifique as perguntas de segurança às quais os usuários precisarão responder se a opção **Usar perguntas de segurança para fallback** estiver selecionada.  O servidor Autenticação Multifator do Azure vem com perguntas padrão que você pode usar. Você pode alterar a ordem ou adicionar suas próprias perguntas.  Ao adicionar suas próprias perguntas, você pode especificar o idioma no qual gostaria que as perguntas aparecessem também.

![Perguntas de segurança do portal do usuário](./media/multi-factor-authentication-get-started-portal/secquestion.png)

## <a name="saml"></a>SAML
Use esta guia para configurar o portal do usuário para aceitar declarações de um provedor de identidade usando SAML.  Você pode especificar o tempo limite de sessão, o certificado de verificação e a URL de redirecionamento de logoff.

![SAML](./media/multi-factor-authentication-get-started-portal/saml.png)

## <a name="trusted-ips"></a>IPs confiáveis
Esta guia permite que você especifique endereços IP simples ou intervalos de endereços IP que podem ser adicionados para que os usuários que entram de um desses endereços não precisem concluir a verificação em duas etapas.

![IPs confiáveis do portal do usuário](./media/multi-factor-authentication-get-started-portal/trusted.png)

## <a name="self-service-user-enrollment"></a>Registro de usuário de autoatendimento
Se desejar que os usuários entrem e se registrem, você deverá selecionar as opções **Permitir que usuários façam logon** e **Permitir registro de usuário** na guia Configurações. Lembre-se de que as configurações selecionadas afetam a experiência de entrada do usuário.

Por exemplo, quando um usuário entra no portal do usuário pela primeira vez, é levado para a página de Configuração de Usuário do Autenticação Multifator do Azure.  Dependendo de como você configurou o Azure Multi-Factor Authentication, o usuário poderá selecionar o método de autenticação.  

Se o método de verificação Chamada de Voz tiver sido selecionado ou houver pré-configuração para usar esse método, a página solicitará que o usuário insira seu número de telefone principal e extensão, se aplicável.  Eles também podem ser autorizados a inserir um número de telefone de backup.  

![IPs confiáveis do portal do usuário](./media/multi-factor-authentication-get-started-portal/backupphone.png)

Se o usuário for solicitado a usar um PIN quando autenticar, a página também lhe pedirá para criar um PIN.  Depois de digitar seu número de telefone e PIN (se aplicável), o usuário clica no botão **Telefonar para Mim para Autenticar**.  O Autenticação Multifator do Azure realizará uma chamada telefônica de verificação para o número de telefone principal do usuário.  O usuário deve responder à chamada telefônica, inserir o PIN (se aplicável) e pressionar # para passar para a próxima etapa do processo de auto-registro.   

Se o usuário selecionar o método de verificação de Mensagem de Texto ou tiver sido pré-configurado para usar esse método, a página solicitará ao usuário seu número de telefone celular.  Se o usuário for solicitado a usar um PIN quando autenticar, a página também lhe pedirá para inserir um PIN.  Depois de digitar seu número de telefone e PIN (se aplicável), o usuário clica no botão **Enviar uma Mensagem de Texto para Autenticar**.  O Azure Multi-Factor Authenticaton enviará uma verificação SMS para o celular do usuário.  O usuário recebe a mensagem de texto com uma OTP (senha de uso único) e responde à mensagem com esta OTP e seu PIN (se aplicável).

![SMS do portal do usuário](./media/multi-factor-authentication-get-started-portal/text.png)   

Se o usuário selecionar o método de verificação de Aplicativo Móvel, a página solicitará que o usuário instale o aplicativo Microsoft Authenticator em seu dispositivo e gere um código de ativação.  Depois de instalar o aplicativo, o usuário clica no botão Gerar Código de Ativação.    

> [!NOTE]
> Para usar o aplicativo Microsoft Authenticator, o usuário deve habilitar notificações por push para seu dispositivo.

A página exibe um código de ativação e uma URL junto com uma imagem de código de barras.  Se o usuário for solicitado a usar um PIN quando autenticar, a página também lhe pedirá para inserir um PIN.  O usuário insere o código de ativação e a URL no aplicativo Microsoft Authenticator ou usa o scanner de código de barras para digitalizar a imagem do código de barras e clica no botão Ativar.    

Após concluir a ativação, o usuário clica no botão **Autenticar Agora**.  O Autenticação Multifator do Azure executará uma verificação do aplicativo móvel do usuário.  O usuário deve inserir seu PIN (se aplicável) e pressionar o botão Autenticar em seu aplicativo móvel para passar para a próxima etapa do processo de auto-registro.  

Se os administradores tiverem configurado o servidor Azure Multi-Factor Authentication para coletar perguntas e respostas de segurança, o usuário será levado para a página Perguntas de Segurança.  O usuário deve selecionar quatro perguntas de segurança e fornecer respostas às perguntas selecionadas.    

![Perguntas de segurança do portal do usuário](./media/multi-factor-authentication-get-started-portal/secq.png)  

O autorregistro do usuário agora está concluído e o usuário está conectado ao portal do usuário.  Os usuários podem entrar no portal do usuário a qualquer momento no futuro para alterar seus números de telefone, PINs, métodos de autenticação e perguntas de segurança se for permitido pelos seus administradores.




<!--HONumber=Feb17_HO3-->


