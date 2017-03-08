---
title: "Serviço Web de aplicativo móvel do servidor MFA do Azure | Microsoft Docs"
description: "O aplicativo Microsoft Authenticator oferece uma opção de autenticação adicional fora de banda.  Ele permite que o servidor MFA envie notificações por push aos usuários."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 6c8d6fcc-70f4-4da4-9610-c76d66635b8b
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/25/2017
ms.author: kgremban
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 20afeb3ba290ddf728d2b52c076c7a57fadc77c6
ms.openlocfilehash: 4014bf0217e25ea9bc8473ef2383279e5eb79b87
ms.lasthandoff: 02/28/2017

---
# <a name="enable-mobile-app-authentication-with-azure-multi-factor-authentication-server"></a>Habilitar a autenticação de aplicativo móvel com o Servidor de Autenticação Multifator do Azure

O aplicativo Microsoft Authenticator oferece uma opção de verificação adicional fora de banda. Em vez de enviar uma chamada telefônica automatizada ou um SMS para o usuário durante o logon, a Autenticação Multifator do Azure envia uma notificação por push ao aplicativo Microsoft Authenticator no smartphone ou tablet do usuário. O usuário simplesmente toca em **Verificar** (ou digita um PIN e toca em "Autenticar") no aplicativo para concluir a entrada. 

É preferível a utilização de um aplicativo móvel para a verificação em duas etapas quando a recepção do telefone não é confiável. Se você usar o aplicativo como um gerador de token OATH, ele não exigirá nenhuma conexão de rede ou Internet. 

A instalação do portal do usuário em um servidor diferente do Servidor de Autenticação Multifator do Azure exige as seguintes etapas:

1. Instalar o SDK de Serviço Web
2. Instalar o Serviço Web de Aplicativos Móveis
3. Configurar as definições do aplicativo móvel no Servidor Azure Multi-Factor Authentication
4. Ativar o aplicativo Microsoft Authenticator para usuários finais

## <a name="requirements"></a>Requisitos

Para usar o aplicativo Microsoft Authenticator e para que ele se comunique com êxito com o Serviço Web do Aplicativo Móvel, é preciso fazer o seguinte:

* Servidor de Autenticação Multifator do Azure v6.0 ou superior
* Instalar o Serviço Web do Aplicativo Móvel em um servidor Web da Internet executando o [IIS (Serviços de Informações da Internet) IIS 7.x ou superior](http://www.iis.net/) da Microsoft®
* Verificar se o ASP.NET v4.0.30319 está instalado, registrado e definido como Permitido
* Os serviços de função obrigatórios incluem o ASP.NET e a Compatibilidade de Metabase do IIS 6
* O Serviço Web do Aplicativo Móvel está acessível por meio de uma URL pública
* O Serviço Web do Aplicativo Móvel está protegido com um certificado SSL.
* Instalar o SDK de Serviço Web da Autenticação Multifator do Azure no IIS 7.x ou superior no mesmo servidor do Servidor de Autenticação Multifator do Azure
* O SDK de Serviço Web da Autenticação Multifator do Azure está protegido com um certificado SSL.
* O Serviço Web do Aplicativo Móvel pode conectar-se ao SDK de Serviço Web da Autenticação Multifator do Azure por SSL
* O Serviço Web do Aplicativo Móvel pode autenticar-se no SDK de Serviço Web da MFA do Azure usando as credenciais de uma conta de serviço que seja membro do grupo de segurança "Administradores de PhoneFactor". Essa conta de serviço e o grupo existem no Active Directory se o Servidor de Autenticação Multifator do Azure está em um servidor ingressado em domínio. Essa conta de serviço e o grupo existem localmente no Servidor Azure Multi-Factor Authentication se ele não tiver ingressado em um domínio.


## <a name="install-the-web-service-sdk"></a>Instalar o SDK de Serviço Web
Se o SDK de Serviço Web da Autenticação Multifator do Azure ainda não estiver instalado no Servidor da MFA (Autenticação Multifator) do Azure, acesse esse servidor e abra o Servidor da MFA do Azure. 

1. Clique no ícone do SDK de Serviço Web.
2. Clique em **Instalar SDK de Serviço Web** e siga as instruções apresentadas. 

O SDK de Serviço Web deve ser protegido com um certificado SSL. Um certificado autoassinado é suficiente para essa finalidade. Importe o certificado para o repositório de "Autoridades de Certificação Confiáveis" da conta do Computador Local no servidor Web do Portal do Usuário, para que ele confie no certificado ao iniciar a conexão SSL.

![Configuração](./media/multi-factor-authentication-get-started-server-webservice/sdk.png)

## <a name="install-the-mobile-app-web-service"></a>Instalar o Serviço Web de Aplicativos Móveis
Antes de instalar o Serviço Web do Aplicativo Móvel, esteja ciente dos seguintes detalhes:

* Se o Portal do Usuário da MFA do Azure já estiver instalado no servidor para a Internet, o nome de usuário, a senha e a URL do SDK do Serviço Web poderão ser copiados do arquivo web.config do Portal do Usuário.
* É recomendável abrir um navegador Web no servidor Web da Internet e navegar até a URL do SDK de Serviço Web que foi inserido no arquivo web.config. Se o navegador conseguir chegar ao serviço Web com êxito, ele deve solicitar credenciais. Insira o nome de usuário e a senha que foram inseridos no arquivo web.config exatamente como ele aparece no arquivo. Certifique-se de que nenhum aviso de certificado ou erro seja exibido.
* Se um proxy reverso ou firewall estiver à frente do servidor Web do Serviço Web do Aplicativo Móvel e executar o descarregamento de SSL, edite o arquivo web.config do Serviço Web do Aplicativo Móvel para que o Serviço Web do Aplicativo Móvel possa usar http em vez de https. O SSL ainda é exigido do Aplicativo Móvel para o firewall/proxy reverso. Adicione a seguinte chave à seção \<appSettings\>: 

        <add key="SSL_REQUIRED" value="false"/>

### <a name="install-the-service"></a>Instalar o serviço

1. Abra o Windows Explorer no Servidor de Autenticação Multifator do Azure e navegue até a pasta na qual o Servidor de MFA do Azure está instalado (geralmente, C:\Program Files\Azure Multi-Factor Authentication). Escolha a versão de 32 bits ou 64 bits do arquivo de instalação Azure Multi-Factor AuthenticationPhoneAppWebServiceSetup. Copie o arquivo de instalação no servidor da Internet.

2. No servidor Web para a Internet, execute o arquivo de instalação com direitos de administrador. Abra um prompt de comando como administrador e navegue até o local no qual o arquivo de instalação foi copiado.

3. Execute o arquivo de instalação Multi-Factor AuthenticationMobileAppWebServiceSetup, mude o Site se desejar e altere o Diretório virtual para um nome curto, como "PA".

  É recomendável dar um nome ao diretório virtual, pois os usuários devem digitar a URL do Serviço Web de Aplicativos Móveis no dispositivo móvel durante a ativação.

4. Após concluir a instalação do Azure Multi-Factor AuthenticationMobileAppWebServiceSetup, navegue até C:\inetpub\wwwroot\PA (ou o diretório apropriado com base no nome do diretório virtual) e edite o arquivo web.config. 

5. Localize as chaves WEB_SERVICE_SDK_AUTHENTICATION_USERNAME e WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD. Defina os valores para o nome de usuário e a senha da conta de serviço que seja membro do grupo de segurança Administradores de PhoneFactor. Essa pode ser a mesma conta que está sendo usada como a Identidade do Portal do Usuário do Azure multi-Factor Authentication que foi instalado anteriormente. Digite o Nome de usuário e Senha entre as aspas no final da linha, (value=””/>). Use um nome de usuário qualificado como, domínio\nome de usuário ou computador\nome de usuário.  

6. Localize a configuração pfMobile App Web Service_pfwssdk_PfWsSdk. Altere o valor de *http://localhost:4898/PfWsSdk.asmx* para a URL do SDK de Serviço Web em execução no Servidor de Autenticação Multifator do Azure (como, https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx). 

  Como o SSL é usado para esta conexão, você deve referenciar o SDK do Serviço Web pelo nome do servidor e não pelo endereço IP. O certificado SSL seria emitido para o nome do servidor e a URL usada deve corresponder ao nome no certificado. O nome do servidor pode não resolver para um endereço IP do servidor para a Internet. Se esse for o caso, adicione uma entrada ao arquivo de hosts nesse servidor para mapear o nome do Servidor de Autenticação Multifator do Azure para seu endereço IP. Depois de feitas as alterações, salve o arquivo web.config.

7. Se o site em que o Serviço Web do Aplicativo Móvel foi instalado ainda não tiver sido associado com um certificado assinado publicamente, instale o certificado no servidor, abra o Gerenciador do IIS e associe o certificado ao site.

8. Abra um navegador da Web em qualquer computador e navegue até a URL na qual o Serviço Web do Aplicativo Móvel foi instalado (por exemplo, https://www.publicwebsite.com/PA). Certifique-se de que nenhum aviso de certificado ou erro seja exibido.

### <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>Configurar as definições do aplicativo móvel no Servidor Azure Multi-Factor Authentication
Agora que o Serviço Web de Aplicativos Móveis está instalado, você precisa configurar o Servidor Azure Multi-Factor Authentication para trabalhar com o portal.

1. No Servidor de MFA do Azure, clique no ícone do Portal do Usuário. Se os usuários têm permissão para controlar seus métodos de autenticação, marque **Aplicativo Móvel** na guia Configurações, em **Permitir que usuários selecionem o método**. Sem esse recurso habilitado, os usuários finais precisarão entrar em contato com o Suporte Técnico para concluir a ativação para Aplicativos Móveis.
2. Marque a caixa **Permitir que usuários ativem o aplicativo móvel**.
3. Marque a caixa **Permitir registro de usuário**.
4. Clique no ícone Aplicativos Móveis.
5. Insira a URL que está sendo usada com o diretório virtual que foi criado ao instalar o Azure Multi-Factor AuthenticationMobileAppWebServiceSetup. Um Nome da conta pode ser inserido no espaço fornecido. Este nome de empresa será exibido no aplicativo móvel. Se ficar em branco, o nome do seu Provedor de Autenticação Multifator criado no Portal Clássico do Azure será exibido.

<center>![Configuração](./media/multi-factor-authentication-get-started-server-webservice/mobile.png)</center>

