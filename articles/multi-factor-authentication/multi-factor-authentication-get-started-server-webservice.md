<properties 
	pageTitle="Introdução ao Serviço Web de Aplicativos Móveis do Servidor MFA"
	description="O aplicativo Azure Multi-Factor Authentication oferece uma opção de autenticação adicional fora de banda. Ele permite que o servidor MFA envie notificações por push aos usuários."
	services="multi-factor-authentication"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2015"
	ms.author="billmath"/>

# Introdução ao Serviço Web de Aplicativos Móveis do Servidor MFA

O aplicativo Azure Multi-Factor Authentication oferece uma opção de autenticação adicional fora de banda. Em vez de fazer um chamada telefônica automatizada ou SMS para o usuário durante o logon, o Azure Multi-Factor Authentication envia uma notificação ao aplicativo do Azure Multi-Factor Authentication no smartphone ou tablet do usuário. O usuário simplesmente toca em "Autenticar" (ou digita um PIN e toca em "Autenticar") no aplicativo para fazer logon.

Para usar o aplicativo Azure Multi-Factor Authentication, é preciso fazer o seguinte para que o aplicativo possa se comunicar com êxito com o Serviço Web de Aplicativos Móveis:

- Consulte Requisitos de hardware e software para saber o que é preciso
- Você deve estar usando a v6.0 ou superior do Servidor Azure Multi-Factor Authentication
- O Serviço Web de Aplicativos Móveis deve estar instalado em um servidor Web da Internet executando o Microsoft® Internet Information Services (IIS) 6.x ou IIS 7.x.
- Ao usar o IIS 6.x, certifique-se de que o ASP.NET v2.0.50727 esteja instalado, registrado e definido como Permitido
- Os serviços de função obrigatórios ao usar o IIS 7.x ou superior incluem o ASP.NET e compatibilidade de Metabase do IIS 6
- O Serviço Web de Aplicativos Móveis deve ser protegido por meio de uma URL pública
- O Serviço Web de Aplicativos Móveis deve ser protegido com um certificado SSL.
- O SDK de Serviço Web do Azure Multi-Factor Authentication deve estar instalado no IIS 6.x, o IIS 7.x no mesmo servidor que o Servidor Azure Multi-Factor Authentication
- O SDK de Serviço Web do Azure Multi-Factor Authentication deve estar protegido com um certificado SSL.
- O Serviço Web de Aplicativos Móveis deve poder se conectar ao SDK de Serviço Web do Azure Multi-Factor Authentication por SSL
- O Serviço Web de Aplicativos Móveis deve poder se autenticar no SDK de Serviço Web do Azure Multi-Factor Authentication usando as credenciais de uma conta de serviço que seja membro de um grupo de segurança chamado “Administradores do Azure Multi-Factor Authentication”. Essa conta de serviço e o grupo existem no Active Directory se o Servidor Azure Multi-Factor Authentication for executado em um servidor ingressado em um domínio. Essa conta de serviço e o grupo existem localmente no Servidor Azure Multi-Factor Authentication se ele não tiver ingressado em um domínio.


A instalação do Portal do Usuário em um servidor diferente do Servidor Azure Multi-Factor Authentication exige estas três etapas:

1. Instalar o SDK de Serviço Web
2. Instalar o Serviço Web de Aplicativos Móveis
3. Configurar as definições do aplicativo móvel no Servidor Azure Multi-Factor Authentication
4. Ativar o aplicativo do Azure Multi-Factor Authentication para usuários finais

## Instalar o SDK de Serviço Web

Se o SDK de Serviço Web do Azure Multi-Factor Authentication já não estiver instalado no Servidor do Azure Multi-Factor Authentication, vá até esse servidor e abra o Servidor Azure Multi-Factor Authentication. Clique no ícone do SDK de Serviço Web, clique no botão Instalar SDK de Serviço Web... e siga as instruções apresentadas. O SDK de Serviço Web deve ser protegido com um certificado SSL. Um certificado autoassinado funciona para essa finalidade, mas ele deve ser importado para o repositório de "Autoridades de Certificação Confiáveis" da conta do Computador Local no servidor web do Portal do Usuário, para que ele confie no certificado quando iniciar a conexão SSL.

<center>![Setup](./media/multi-factor-authentication-get-started-server-webservice/sdk.png)</center>

## Instalar o Serviço Web de Aplicativos Móveis
Antes de instalar o Serviço Web de Aplicativos Móveis, esteja ciente do seguinte:

- Se o Portal do Usuário do Azure Multi-Factor Authentication já estiver instalado no servidor da Internet, o nome de usuário, senha e URL do SDK do Serviço Web podem ser copiados do arquivo web.config do Portal do usuário. 
- É recomendável abrir um navegador Web no servidor Web da Internet e navegar até a URL do SDK de Serviço Web que foi inserido no arquivo web.config. Se o navegador conseguir chegar ao serviço Web com êxito, ele deve solicitar credenciais. Insira o nome de usuário e a senha que foram inseridos no arquivo web.config exatamente como ele aparece no arquivo. Certifique-se de que nenhum aviso de certificado ou erro seja exibido.
- Se um proxy reverso ou firewall estiver à frente do servidor Web do Serviço Web de Aplicativos Móveis e executar o descarregamento de SSL, você pode editar o arquivo web.config do Serviço Web de Aplicativos Móveis e adicionar a seguinte chave à seção <appSettings> para que o Serviço Web de Aplicativos Móveis possa usar http em vez de https. No entanto, o SSL ainda é exigido do Aplicativo Móvel para o firewall/proxy reverso. <add key="SSL_REQUIRED" value="false"/> 

### Para instalar o Serviço Web de Aplicativos Móveis

<ol>
<li>Abra o Windows Explorer no Servidor Azure Multi-Factor Authentication e navegue até a pasta onde o Servidor Azure Multi-Factor Authentication está instalado (por exemplo, C:\Program Files\Azure Multi-Factor Authentication). Escolha a versão de 32 bits ou 64 bits do arquivo de instalação Azure Multi-Factor AuthenticationPhoneAppWebServiceSetup conforme apropriado para o servidor em que o Serviço Web de Aplicativos Móveis será instalado. Copie o arquivo de instalação no servidor da Internet.</li>

<li>No servidor da web da Internet, o arquivo de instalação deve ser executado com direitos de administrador. A maneira mais fácil de fazer isso é abrir um prompt de comando como administrador e navegar até o local onde o arquivo de instalação foi copiado.</li>

<li>Execute o arquivo de instalação Multi-Factor AuthenticationMobileAppWebServiceSetup, mude o Site se desejado e altere o Diretório Virtual para um nome curto, como "PA". É recomendável dar um nome ao diretório virtual, pois os usuários devem digitar a URL do Serviço Web de Aplicativos Móveis no dispositivo móvel durante a ativação.</li>

<li>Após concluir a instalação do Azure Multi-Factor AuthenticationMobileAppWebServiceSetup, navegue até C:\inetpub\wwwroot\PA (ou o diretório apropriado com base no nome do diretório virtual) e edite o arquivo web.config.</li>

<li>Localize as chaves WEB_SERVICE_SDK_AUTHENTICATION_USERNAME e WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD e defina os valores para o nome de usuário e a senha da conta de serviço que é membro do grupo de segurança PhoneFactor Admins (consulte a seção Requisitos acima). Essa pode ser a mesma conta que está sendo usada como a Identidade do Portal do Usuário do Azure multi-Factor Authentication que foi instalado anteriormente. Digite o Nome de usuário e Senha entre as aspas no final da linha, (value=””/>). É recomendável usar um nome de usuário qualificado (por exemplo, domínio\nomedeusuário ou máquina\nomedeusuário).</li>

<li>Localize a configuração pfMobile App Web Service_pfwssdk_PfWsSdk e mude o valor de “http://localhost:4898/PfWsSdk.asmx” para a URL do SDK de Serviço Web que está em execução no Servidor Azure Multi-Factor Authentication (por exemplo, https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx). Como o SSL é usado para esta conexão, você deve referenciar o SDK de Serviço Web pelo nome do servidor e não o endereço IP, pois o certificado SSL terá sido emitido para o nome do servidor e a URL usada deve corresponder ao nome no certificado. Se o nome do servidor não resolver para um endereço IP do servidor da Internet, adicione uma entrada ao arquivo de host nesse servidor para mapear o nome do Servidor Azure Multi-Factor Authentication para seu endereço IP. Depois de feitas as alterações, salve o arquivo web.config.</li>

<li>Se o site em que o Serviço Web de Aplicativos Móveis foi instalado (por exemplo, Site Padrão) ainda não tiver sido associado com um certificado assinado publicamente, instale o certificado no servidor, se ainda não estiver instalado, abra o Gerenciador do IIS e associe o certificado ao site.</li>

<li>Abra um navegador Web em qualquer computador e navegue até a URL onde o Serviço Web de Aplicativos Móveis foi instalado (por exemplo, https://www.publicwebsite.com/PA). Certifique-se de que nenhum aviso de certificado ou erro seja exibido.</li>

### Configurar as definições do aplicativo móvel no Servidor Azure Multi-Factor Authentication
Agora que o Serviço Web de Aplicativos Móveis está instalado, você precisa configurar o Servidor Azure Multi-Factor Authentication para trabalhar com o portal.

#### Para configurar as definições do aplicativo móvel no Servidor Azure Multi-Factor Authentication

1. No Servidor Azure Multi-Factor Authentication, clique no ícone Portal do Usuário. Se os usuários têm permissão para controlar seus métodos de autenticação, na guia Configurações, em Permitir que usuários selecionem o método, marque Aplicativos Móveis. Sem esse recurso habilitado, os usuários finais precisarão entrar em contato com o Suporte Técnico para concluir a ativação para Aplicativos Móveis.
2. Marque a caixa Permitir que usuários ativem o aplicativo móvel.
3. Marque a caixa Permitir registro de usuário.
4. Clique no ícone Aplicativos Móveis.
5. Insira a URL que está sendo usada com o diretório virtual que foi criado ao instalar o Azure Multi-Factor AuthenticationMobileAppWebServiceSetup. Um Nome da conta pode ser inserido no espaço fornecido. Este nome de empresa será exibido no aplicativo móvel. Se ficar em branco, o nome do seu Provedor de Autenticação Multifator criado no Portal de Gerenciamento do Azure será exibido. 



<center>![Setup](./media/multi-factor-authentication-get-started-server-webservice/mobile.png)</center>

<!---HONumber=August15_HO9-->