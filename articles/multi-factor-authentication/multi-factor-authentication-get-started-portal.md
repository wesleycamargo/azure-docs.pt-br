<properties 
	pageTitle="Implantando o portal do usuário para o Servidor Azure Multi-Factor Authentication" 
	description="Esta é a página de autenticação do Azure Multi-Factor Authentication que descreve como começar com o Azure MFA e o Portal do Usuário." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="02/18/2016" 
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

1. Dentro do Servidor Azure Multi-Factor Authentication: clique no ícone do Portal do Usuário no menu à esquerda, clique no botão Instalar Portal do Usuário. 
1. Clique em Avançar.
1. Clique em Avançar.
1. Se o computador tiver ingressado em um domínio e a configuração do Active Directory para proteger a comunicação entre o Portal do Usuário e o serviço Azure Multi-Factor Authentication estiver incompleta, a etapa do Active Directory será exibida. Clique no botão Avançar para cocluir automaticamente essa configuração.
1. Clique em Avançar.
1. Clique em Avançar.
1. Clique em Fechar.
1. Abra um navegador da Web em qualquer computador e navegue até a URL onde o Portal do Usuário foi instalado (por exemplo, https://www.publicwebsite.com/MultiFactorAuth). Certifique-se de que nenhum aviso de certificado ou erro seja exibido.

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
- Se um proxy reverso ou firewall estiver à frente do servidor Web do Portal do Usuário e executando o descarregamento de SSL, você pode editar o arquivo web.config do Portal do Usuário e adicionar a seguinte chave à seção <appSettings>, para que o Portal do Usuário possa usar http em vez de https. <add key="SSL_REQUIRED" value="false"/>

#### Para instalar o Portal do Usuário

1. Abra o Windows Explorer no Servidor Azure Multi-Factor Authentication e navegue até a pasta onde o Servidor Azure Multi-Factor Authentication está instalado (por exemplo, C:\\Arquivos de Programas\\Servidor Multi-Factor Authentication). Escolha a versão de 32 bits ou 64 bits do arquivo de instalação MultiFactorAuthenticationUserPortalSetup conforme apropriado para o servidor em que o Portal do Usuário será instalado. Copie o arquivo de instalação no servidor da Internet.
2. No servidor da web da Internet, o arquivo de instalação deve ser executado com direitos de administrador. A maneira mais fácil de fazer isso é abrir um prompt de comando como administrador e navegar até o local onde o arquivo de instalação foi copiado.
3. Execute o arquivo de instalação MultiFactorAuthenticationUserPortalSetup64, altere o nome de Site e Diretório Virtual, se desejado.
4. Após concluir a instalação do Portal do Usuário, navegue até C:\\inetpub\\wwwroot\\MultiFactorAuth (ou o diretório apropriado com base no nome do diretório virtual) e edite o arquivo web.config.
5. Localize a chave USE\_WEB\_SERVICE\_SDK e altere o valor de false para true. Localize as chaves WEB\_SERVICE\_SDK\_AUTHENTICATION\_USERNAME e WEB\_SERVICE\_SDK\_AUTHENTICATION\_PASSWORD e defina os valores para o nome de usuário e a senha da conta de serviço que é membro do grupo de segurança PhoneFactor Admins (consulte a seção Requisitos acima). Digite o Nome de usuário e Senha entre as aspas no final da linha, (value=””/>). É recomendável usar um nome de usuário qualificado (por exemplo, domínio\\nomedeusuário ou computador\\nomedeusuário).
6. Localize a configuração pfup\_pfwssdk\_PfWsSdk e altere o valor de "http://localhost:4898/PfWsSdk.asmx" para a URL do SDK do serviço Web em execução no servidor Azure Multi-Factor Authentication (por exemplo,https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx). Como o SSL é usado para esta conexão, você deve referenciar o SDK de Serviço Web pelo nome do servidor e não o endereço IP, pois o certificado SSL terá sido emitido para o nome do servidor e a URL usada deve corresponder ao nome no certificado. Se o nome do servidor não resolver para um endereço IP do servidor da Internet, adicione uma entrada ao arquivo de hosts nesse servidor para mapear o nome do Servidor Azure Multi-Factor Authentication para seu endereço IP. Depois de feitas as alterações, salve o arquivo web.config.
7. Se o site em que o Portal do Usuário foi instalado (por exemplo, Site Padrão) ainda não tiver sido associado com um certificado assinado publicamente, instale o certificado no servidor, se ainda não estiver instalado, abra o Gerenciador do IIS e associe o certificado ao site.
8. Abra um navegador da Web em qualquer computador e navegue até a URL onde o Portal do Usuário foi instalado (por exemplo, https://www.publicwebsite.com/MultiFactorAuth). Certifique-se de que nenhum aviso de certificado ou erro seja exibido.



## Configurar as definições do Portal do Usuário no Servidor Azure Multi-Factor Authentication
Agora que o portal está instalado, você precisa configurar o Servidor Azure Multi-Factor Authentication para trabalhar com o portal.

O servidor Azure Multi-Factor Authentication fornece várias opções para o portal do usuário. A tabela a seguir fornece uma lista dessas opções e uma explicação de como elas são usadas.

Configurações do Portal de Usuário|Descrição|
:------------- | :------------- | 
URL do portal do usuário| Permite que você insira a URL de onde o portal está sendo hospedado.
Autenticação primária| Permite que você especifique o tipo de autenticação a ser usado ao entrar no portal. Autenticação do Windows, Radius ou LDAP.
Permitir que os usuários façam logon|Permite que os usuários insiram um nome de usuário e uma senha na página de entrada para o portal do usuário. Se não forem selecionadas, as caixas ficarão esmaecidas.
Permitir registro de usuário|Permite que o usuário se registre na autenticação multifator levando-os para uma tela de instalação que solicita informações adicionais, como número de telefone. O prompt de telefone de backup permite que os usuários especifiquem um número de telefone secundário. O prompt de token OATH permite que os usuários especifiquem um token OATH de terceiros.
Permitir que os usuários iniciem Desvio Único| Isso permite que os usuários iniciem um desvio único. Se um usuário tiver essa configuração, ela terá efeito na próxima vez em que ele entrar. O prompt de ignorar segundos fornece ao usuário uma caixa para que ele possa alterar o padrão de 300 segundos. Caso contrário, o desvio único é válido somente por 300 segundos.
Permitir que os usuários selecionem o método| Permite aos usuários especificar o método de contato principal. Isso pode ser uma chamada telefônica, uma mensagem de texto, um aplicativo móvel ou token OATH.
Permitir que os usuários selecionem o idioma| Permite que o usuário altere o idioma usado para chamada telefônica, mensagem de texto, aplicativo móvel ou token OATH.
Permitir que os usuários ativem aplicativos móveis| Permite que os usuários gerem um código de ativação para concluir o processo de ativação de aplicativo móvel que seja usado com o servidor. Você também pode definir o número de dispositivos em que eles podem ativar. Entre 1 e 10.
Usar perguntas de segurança para fallback|Permite que você use as perguntas de segurança no caso de falha de autenticação multifator. Você pode especificar o número de perguntas de segurança que devem ser respondidas com sucesso.
Permitir que os usuários associem token OATH de terceiros| Permite que os usuários especifiquem um token OATH de terceiros.
Usar token OATH para fallback|Permite o uso de um token OATH caso a autenticação multifator não seja bem-sucedida. Você também pode especificar o tempo limite da sessão em minutos.
Habilitar o registro em log|Habilita o registro em log no portal do usuário. Os arquivos de log estão localizados em: C:\\Program Files\\Multi-Factor Authentication Server\\Logs.

A maioria dessas configurações ficam visíveis para o usuário quando ele for habilitado e entrar no portal do usuário.

![Configurações do portal de usuário](./media/multi-factor-authentication-get-started-portal/portalsettings.png)



### Para configurar as definições do Portal do Usuário no Servidor Azure Multi-Factor Authentication




1. No Servidor Azure Multi-Factor Authentication, clique no ícone Portal do Usuário. Na guia Configurações, insira a URL no Portal do Usuário na caixa de texto URL do Portal do Usuário. Essa URL será inserida em emails que são enviados aos usuários quando eles são importados para o Servidor Azure Multi-Factor Authentication se a funcionalidade de email tiver sido habilitada.
2. Escolha as configurações que você deseja usar no Portal do Usuário. Por exemplo, se os usuários tiverem permissão para controlar seus métodos de autenticação, certifique-se de que Permitir que usuários selecionem o método esteja selecionado junto com os métodos que eles podem escolher.
3. Clique no link Ajuda no canto superior direito para ajudar a entender as configurações exibidas.

<center>![Setup](./media/multi-factor-authentication-get-started-portal/config.png)</center>


## Guia Administradores
Essa guia simplesmente permite que você adicione os usuários com privilégios administrativos. Ao adicionar um administrador, você pode ajustar as permissões que ele recebe. Dessa forma, você pode ter certeza de que concedeu somente as permissões necessárias ao administrador. Basta clicar no botão Adicionar, selecionar um usuário e suas permissões e clicar em Adicionar.

![Administradores do portal do usuário](./media/multi-factor-authentication-get-started-portal/admin.png)


## Perguntas de segurança
Essa guia permite que você especifique as perguntas de segurança que os usuários precisarão responder se a opção Usar perguntas de segurança para fallback estiver selecionada. O servidor Azure Multi-Factor Authentication vem com perguntas padrão que você pode usar. Você também pode alterar a ordem ou adicionar suas próprias perguntas. Ao adicionar suas próprias perguntas, você pode especificar o idioma no qual gostaria que as perguntas aparecessem também.

![Perguntas de segurança do portal do usuário](./media/multi-factor-authentication-get-started-portal/secquestion.png)


## Sessões passadas

## SAML
Permite configurar o portal do usuário para aceitar declarações de um provedor de identidade usando SAML. Você pode especificar a tempo limite de sessão, o certificado de verificação e a URL de redirecionamento de logoff.

![SAML](./media/multi-factor-authentication-get-started-portal/saml.png)

## IPs Confiáveis
Essa guia permite especificar endereços IP simples ou intervalos de endereços IP que podem ser adicionados de forma que, se um usuário estiver conectando a partir de um desses endereços IP, a autenticação multifator será ignorada.

![IPs confiáveis do portal do usuário](./media/multi-factor-authentication-get-started-portal/trusted.png)

## Registro de usuário de autoatendimento
Se você desejar que os usuários entrem e se registrem, deve selecionar as opções Permitir que usuários façam logon e Permitir registro de usuário. Lembre-se de que as configurações selecionadas afetarão a experiência de entrada do usuário.

Por exemplo, quando um usuário faz logon no Portal do Usuário e clica no botão Logon, eles são levados para a página de configuração de usuário do Azure Multi-Factor Authentication. Dependendo de como você configurou o Azure Multi-Factor Authentication, o usuário poderá selecionar o método de autenticação.

Se o método de autenticação Chamada de Voz tiver sido selecionado ou houver pré-configuração para usar esse método, a página solicitará que o usuário insira seu número de telefone principal e extensão, se aplicável. Eles também podem ser autorizados a inserir um número de telefone de backup.

![IPs confiáveis do portal do usuário](./media/multi-factor-authentication-get-started-portal/backupphone.png)

Se o usuário for solicitado a usar um PIN quando autenticar, a página também lhe pedirá para inserir um PIN. Depois de digitar seu número de telefone e PIN (se aplicável), o usuário clica no botão Telefonar para Mim para Autenticar. O Azure Multi-Factor Authentication realizará uma chamada telefônica de autenticação para o número de telefone principal do usuário. O usuário deve responder à chamada telefônica, inserir o PIN (se aplicável) e pressionar # para passar para a próxima etapa do processo de auto-registro.

Se o usuário selecionar o método de autenticação de texto SMS ou tiver sido pré-configurado para usar esse método, a página solicitará ao usuário seu número de telefone celular. Se o usuário for solicitado a usar um PIN quando autenticar, a página também lhe pedirá para inserir um PIN. Depois de digitar seu número de telefone e PIN (se aplicável), o usuário clica no botão Enviar uma Mensagem de Texto para Autenticar. O Azure Multi-Factor Authenticaton enviará uma autenticação SMS para o celular do usuário. O usuário deve receber o SMS que contém uma OTP (senha de uso único) e responder à mensagem com esta OTP e com o seu PIN, se aplicável) para passar para a próxima etapa do processo de auto-registro.

![SMS do portal do usuário](./media/multi-factor-authentication-get-started-portal/text.png)

Se o usuário selecionar o método de autenticação de aplicativo móvel ou tiver sido pré-configurado para usar esse método, a página solicitará ao usuário a instalação do aplicativo Azure Multi-Factor Authentication em seu dispositivo e a geração de um código de ativação. Depois de instalar o aplicativo Azure Multi-Factor Authentication, o usuário clica no botão Gerar Código de Ativação.

>[AZURE.NOTE]Para usar o aplicativo Azure Multi-Factor Authentication, o usuário deve habilitar notificações por push para seu dispositivo.

A página exibe um código de ativação e uma URL junto com uma imagem de código de barras. Se o usuário for solicitado a usar um PIN quando autenticar, a página também lhe pedirá para inserir um PIN. O usuário insere o código de ativação e a URL do aplicativo Azure multi-Factor Authentication ou usa o scanner de código de barras para digitalizar a imagem do código de barras e clica no botão Ativar.

Após concluir a ativação, o usuário clica no botão Autenticar Agora. O Azure Multi-Factor Authentication executará a autenticação do aplicativo móvel do usuário. O usuário deve inserir seu PIN (se aplicável) e pressionar o botão Autenticar em seu aplicativo móvel para passar para a próxima etapa do processo de auto-registro.


Se os administradores tiverem configurado o servidor Azure Multi-Factor Authentication para coletar perguntas e respostas de segurança, o usuário será levado para a página Perguntas de Segurança. O usuário deve selecionar quatro perguntas de segurança e fornecer respostas às perguntas selecionadas.

![Perguntas de segurança do portal do usuário](./media/multi-factor-authentication-get-started-portal/secq.png)

O auto-registro do usuário está concluído e o usuário está conectado ao Portal do usuário. Os usuários podem fazer logon no Portal do Usuário a qualquer momento no futuro para alterar seus números de telefone, PINs, métodos de autenticação e perguntas de segurança se for permitido pelos seus administradores.

 

<!---HONumber=AcomDC_0224_2016-->