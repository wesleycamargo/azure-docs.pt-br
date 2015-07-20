<properties 
	pageTitle="Implantando o portal do usuário para o Servidor Azure Multi-Factor Authentication" 
	description="Esta é a página de autenticação do Azure Multi-Factor Authentication que descreve como começar com o Azure MFA e o Portal do Usuário." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="bryanla"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/02/2015" 
	ms.author="billmath"/>

# Implantando o portal do usuário para o Servidor Azure Multi-Factor Authentication

O Portal do Usuário permite ao administrador instalar e configurar o Portal do Usuário do Azure Multi-Factor Authentication. O Portal do Usuário é um site do IIS que permite aos usuários se registrar no Azure Multi-Factor Authentication e manter suas contas. Um usuário pode alterar o número de telefone, alterar o PIN ou ignorar o Azure Multi-Factor Authentication durante o próximo logon.

Os usuários entrarão no Portal do Usuário usando seu nome de usuário e senha normais e fará uma chamada do Azure Multi-Factor Authentication ou responderá perguntas de segurança para concluir a autenticação. Se o registro do usuário for autorizado, o usuário irá configurar seu número de telefone e PIN na primeira vez que entrar no Portal do Usuário.

Os Administradores do Portal do Usuário podem receber uma configuração e permissão para adicionar novos usuários e atualizar usuários existentes.

<center>![Setup](./media/multi-factor-authentication-get-started-portal/install.png)</center>

## Implantando o Portal do Usuário no servidor que o Servidor Azure Multi-Factor Authentication

Os seguintes pré-requisitos são necessários para instalar o Portal do Usuário no mesmo servidor que o Servidor Azure Multi-Factor Authentication

- O IIS precisa ser instalado com a compatibilidade da Metabase do IIS 6 e asp.net (para o IIS 7 ou superior) 
- O usuário conectado deve ter direitos de administrador para o computador e o domínio, se aplicável. Isso ocorre porque a conta precisa ter permissões para criar grupos de segurança do Active Directory.

### Para implantar o Portal do Usuário para o Servidor Azure Multi-Factor Authentication

<ol>
<li>Dentro do Servidor Azure Multi-Factor Authentication: clique ícone Portal do Usuário no menu à esquerda, clique em botão Instalar Portal do usuário. <li>Clique em Avançar. <li>Clique em Avançar. <li>Se o computador tiver ingressado em um domínio e a configuração do Active Directory para proteger a comunicação entre o Portal do Usuário e o serviço do Azure Multi-Factor Authentication estiver incompleta, a etapa do Active Directory será exibida. Clique no botão Avançar para cocluir automaticamente essa configuração. <li>Clique em Avançar. <li>Clique em Avançar. <li>Clique em Fechar. <li>Abra um navegador da web em qualquer computador e navegue até a URL onde o Portal do Usuário foi instalado (por exemplo, https://www.publicwebsite.com/MultiFactorAuth). Certifique-se de que nenhum aviso de certificado ou erro seja exibido.</li>

<center>![Setup](./media/multi-factor-authentication-get-started-portal/portal.png)</center>

## Implantando o Portal do Usuário do Servidor Azure Multi-Factor Authentication em um servidor separado

Os fatores abaixo são necessários para usar o aplicativo Azure Multi-Factor Authentication, para que o aplicativo possa se comunicar com êxito com o Portal do Usuário:

Consulte Requisitos de hardware e software para saber o que é preciso:

- Você deve estar usando a v6.0 ou superior do Servidor Azure Multi-Factor Authentication.
- O Portal do Usuário deve estar instalado em um servidor da web da Internet executando o Microsoft® Internet Information Services (IIS) 6.x, o IIS 7.x ou superior.
- Ao usar o IIS 6.x, certifique-se de que o ASP.NET v2.0.50727 esteja instalado, registrado e definido como Permitido.
- Os serviços de função obrigatórios ao usar o IIS 7.x ou superior incluem ASP.NET e compatibilidade de Metabase do IIS 6.
- O Portal do Usuário deve ser protegido com um certificado SSL.
- O SDK de Serviço Web do Azure Multi-Factor Authentication deve estar instalado no IIS 6.x, o IIS 7.x ou superior no mesmo servidor em que o Servidor Azure Multi-Factor Authentication está instalado.
- O SDK de Serviço Web do Azure Multi-Factor Authentication deve estar protegido com um certificado SSL.
- O Portal do Usuário deve ser capaz de conectar ao SDK de Serviço Web do Azure Multi-Factor Authentication por SSL.
- O Portal do Usuário deve ser capaz de autenticar no SDK de Serviço Web do Azure Multi-Factor Authentication usando as credenciais de uma conta de serviço que é membro de um grupo de segurança chamado "PhoneFactor Admins". Essa conta de serviço e o grupo existem no Active Directory se o Servidor Azure Multi-Factor Authentication for executado em um servidor ingressado em um domínio. Essa conta de serviço e o grupo existem localmente no Servidor Azure Multi-Factor Authentication se ele não tiver ingressado em um domínio.

A instalação do Portal do Usuário em um servidor diferente do Servidor Azure Multi-Factor Authentication exige estas três etapas:

1. Instalar o SDK de Serviço Web
2. Instalar o Portal do Usuário
3. Configurar as definições do Portal do Usuário no Servidor Azure Multi-Factor Authentication


### Instalar o SDK de Serviço Web

Se o SDK de Serviço Web do Azure Multi-Factor Authentication já não estiver instalado no Servidor do Azure Multi-Factor Authentication, vá até esse servidor e abra o Servidor Azure Multi-Factor Authentication. Clique no ícone do SDK de Serviço Web, clique no botão Instalar SDK de Serviço Web... e siga as instruções apresentadas. O SDK de Serviço Web deve ser protegido com um certificado SSL. Um certificado autoassinado funciona para essa finalidade, mas ele deve ser importado para o repositório de "Autoridades de Certificação Confiáveis" da conta do Computador Local no servidor web do Portal do Usuário, para que ele confie no certificado quando iniciar a conexão SSL.

<center>![Setup](./media/multi-factor-authentication-get-started-portal/sdk.png)</center>

### Instalar o Portal do Usuário

Antes de instalar o Portal do Usuário em um servidor separado, esteja ciente do seguinte:

- É recomendável abrir um navegador Web no servidor Web da Internet e navegar até a URL do SDK de Serviço Web que foi inserido no arquivo web.config. Se o navegador conseguir chegar ao serviço Web com êxito, ele deve solicitar credenciais. Insira o nome de usuário e a senha que foram inseridos no arquivo web.config exatamente como ele aparece no arquivo. Certifique-se de que nenhum aviso de certificado ou erro seja exibido.
- Se um proxy reverso ou firewall estiver à frente do servidor da web do Portal do Usuário e executar o descarregamento de SSL, você pode editar o arquivo web.config do Portal do Usuário e adicionar a seguinte chave à seção <appSettings> para que o Portal do Usuário possa usar http em vez de https. <add key="SSL_REQUIRED" value="false"/>

#### Para instalar o Portal do Usuário

<ol>




<li>Abra o Windows Explorer no Servidor Azure Multi-Factor Authentication e navegue até a pasta onde o Servidor Azure Multi-Factor Authentication está instalado (por exemplo, C:\Arquivos de Programas\Servidor Multi-Factor Authentication). Escolha a versão de 32 bits ou 64 bits do arquivo de instalação MultiFactorAuthenticationUserPortalSetup conforme apropriado para o servidor em que o Portal do Usuário será instalado. Copie o arquivo de instalação no servidor da Internet.</li>

<li>No servidor da web da Internet, o arquivo de instalação deve ser executado com direitos de administrador. A maneira mais fácil de fazer isso é abrir um prompt de comando como administrador e navegar até o local onde o arquivo de instalação foi copiado.</li>

<li>Execute o arquivo de instalação MultiFactorAuthenticationUserPortalSetup64, altere o nome de Site e Diretório Virtual, se desejado.</li>

<li>Após concluir a instalação do Portal do Usuário, navegue até C:\inetpub\wwwroot\MultiFactorAuth (ou o diretório apropriado com base no nome do diretório virtual) e edite o arquivo web.config.</li>

<li>Localize a chave USE_WEB_SERVICE_SDK e altere o valor de false para true. Localize as chaves WEB_SERVICE_SDK_AUTHENTICATION_USERNAME e WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD e defina os valores para o nome de usuário e a senha da conta de serviço que é membro do grupo de segurança PhoneFactor Admins (consulte a seção Requisitos acima). Digite o Nome de usuário e Senha entre as aspas no final da linha, (value=””/>). É recomendável usar um nome de usuário qualificado (por exemplo, domínio\nomedeusuário ou máquina\nomedeusuário).</li>

<li>Localize a configuração pfup_pfwssdk_PfWsSdk e altere o valor de “http://localhost:4898/PfWsSdk.asmx” para a URL do SDK de Serviço Web em execução no Servidor Azure Multi-Factor Authentication (por exemplo, https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx). Como o SSL é usado para esta conexão, você deve referenciar o SDK de Serviço Web pelo nome do servidor e não o endereço IP, pois o certificado SSL terá sido emitido para o nome do servidor e a URL usada deve corresponder ao nome no certificado. Se o nome do servidor não resolver para um endereço IP do servidor da Internet, adicione uma entrada ao arquivo de hosts nesse servidor para mapear o nome do Servidor Azure Multi-Factor Authentication para seu endereço IP. Depois de feitas as alterações, salve o arquivo web.config.</li>

<li>Se o site em que o Portal do Usuário foi instalado (por exemplo, Site Padrão) ainda não tiver sido associado com um certificado assinado publicamente, instale o certificado no servidor, se ainda não estiver instalado, abra o Gerenciador do IIS e associe o certificado ao site.</li>

<li>Abra um navegador da web em qualquer computador e navegue até a URL onde o Portal do Usuário foi instalado (por exemplo, https://www.publicwebsite.com/MultiFactorAuth ). Certifique-se de que nenhum aviso de certificado ou erro seja exibido.</li>

## Configurar as definições do Portal do Usuário no Servidor Azure Multi-Factor Authentication
Agora que o portal está instalado, você precisa configurar o Servidor Azure Multi-Factor Authentication para trabalhar com o portal.

### Para configurar as definições do Portal do Usuário no Servidor Azure Multi-Factor Authentication




1. No Servidor Azure Multi-Factor Authentication, clique no ícone Portal do Usuário. Na guia Configurações, insira a URL no Portal do Usuário na caixa de texto URL do Portal do Usuário. Essa URL será inserida em emails que são enviados aos usuários quando eles são importados para o Servidor Azure Multi-Factor Authentication se a funcionalidade de email tiver sido habilitada.
2. Escolha as configurações que você deseja usar no Portal do Usuário. Por exemplo, se os usuários tiverem permissão para controlar seus métodos de autenticação, certifique-se de que Permitir que usuários selecionem o método esteja selecionado junto com os métodos que eles podem escolher.
3. Clique no link Ajuda no canto superior direito para ajudar a entender as configurações exibidas.

<center>![Setup](./media/multi-factor-authentication-get-started-portal/config.png)</center>

<!---HONumber=July15_HO2-->