<properties
	pageTitle="Proteger um aplicativo Web no Serviço de Aplicativo do Azure"
	description="Saiba como proteger um aplicativo Web do Azure."
	services="app-service"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="09/16/2015"
	ms.author="cephalin"/>


#Proteger um aplicativo Web no Serviço de Aplicativo do Azure

Um dos desafios do desenvolvimento de um aplicativo Web é como fornecer um dispositivo seguro e protegido para os clientes. Neste artigo, você saberá os recursos do [Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714) que podem proteger o aplicativo Web.

> [AZURE.NOTE]Uma discussão completa das considerações sobre segurança para aplicativos Web está além do escopo deste documento. Como ponto de partida para mais diretrizes sobre como proteger aplicativos Web, consulte o [Open Web Application Security Project (OWASP)](https://www.owasp.org/index.php/Main_Page), mais especificamente o [o projeto das 10 mais](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project), que lista as 10 principais falhas de segurança em aplicativos Web, conforme determinado pelos membros do OWASP.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

##<a name="https"></a> Comunicações seguras

Se você usar o nome de domínio ****.azurewebsites.net** criado para o aplicativo Web, será possível usar imediatamente o protocolo HTTPS, já que um certificado SSL é fornecido para todos os nomes de domínio ****.azurewebsites.net**. Se o site usar um [nome de domínio personalizado](web-sites-custom-domain-name.md), será possível carregar um certificado SSL para [habilitar o protocolo HTTPS](web-sites-configure-ssl-certificate.md) para o domínio personalizado.

##<a name="develop"></a> Desenvolvimento seguro

### Publicando configurações de publicação de perfis e publicação

Durante o desenvolvimento de aplicativos, a realização de tarefas de gerenciamento ou a automação de tarefas usando utilitários como **Visual Studio**, **Web Matrix**, **Azure PowerShell** ou a **Interface de linha de comando do Azure (CLI do Azure)**, é possível usar um arquivo de *configurações de publicação* ou um *perfil de publicação*. Ambos autenticam você no Azure e devem ser protegidos para evitar o acesso não autorizado.

* Um arquivo de **configurações de publicação** contém

	* ID da assinatura do Azure

	* Um certificado de gerenciamento que permite realizar tarefas de gerenciamento para a assinatura *sem precisar fornecer um nome de conta ou uma senha*.

* Um arquivo de **perfil de publicação** contém

	* Informações de publicação para seu aplicativo Web

Se você usa um utilitário que utiliza configurações de publicação ou perfil de publicação, importe o arquivo que contém as configurações de publicação ou o perfil para o utilitário e **exclua** o arquivo. Se você precisar manter o arquivo para compartilhar com outros que trabalhem no projeto, por exemplo, armazene-o em um local seguro, como um diretório **criptografado** com permissões restritas.

Além disso, você deve se certificar de que as credenciais importadas sejam seguras. Por exemplo, o **Azure PowerShell** e a **Interface da Linha de Comando do Azure (CLI do Azure)** armazenam informações importadas no seu **diretório base** (*~* em sistemas Linux ou OS X e em */usuários/seunomedeusuário* em sistemas Windows). Para ter segurança extra, convém **criptografar** esses locais usando as ferramentas de criptografia disponíveis para o sistema operacional.

### Definições de configuração e cadeias de conexão
É uma prática comum armazenar cadeias de conexão, credenciais de autenticação e outras informações confidenciais em arquivos de configuração. Infelizmente, esses cookies podem ser expostos no site ou pode haver check-in deles em um repositório público, expondo essas informações.

O Serviço de Aplicativo do Azure permite que você armazene informações de configuração como parte do ambiente de tempo de execução de aplicativos Web, como **configurações do aplicativo** e **cadeias de conexão**. Os valores são expostos ao aplicativo durante o tempo de execução por meio de *variáveis do ambiente* na maioria das linguagens de programação. Para aplicativos do .NET, esses valores são injetados na configuração do .NET durante o tempo de execução.

As **Configurações do aplicativo** e as **cadeias de conexão** podem ser configuradas usando o [portal de visualização do Azure](http://portal.azure.com) ou utilitários, como o PowerShell ou a CLI do Azure.

Para obter mais informações sobre configurações do aplicativo e cadeias de conexão, consulte [Configurando aplicativos Web](web-sites-configure.md).

### Protocolo FTPS

O Azure oferece acesso FTP seguro ao sistema de arquivos para o aplicativo Web por meio do protocolo **FTPS**. Isso permite acessar com segurança o código do aplicativo no aplicativo Web, bem como os logs de diagnóstico. O link FTPS para seu aplicativo Web pode ser encontrado seguindo estas etapas:

1. Abra o [portal de visualização do Azure](http://portal.azure.com).
2. Selecione **Procurar Tudo**.
3. Na folha **Procurar**, selecione **Aplicativos Web**.
4. Na folha **Aplicativos Web**, selecione o aplicativo Web desejado.
5. Na folha do aplicativo Web, selecione **Todas as configurações**.
6. Na folha **Configurações**, selecione **Propriedades**.
7. Os links FTP e FTPS são fornecidos na folha **Configurações**. 

Para obter mais informações sobre o protocolo FTPS, consulte [Protocolo FTPS](http://en.wikipedia.org/wiki/File_Transfer_Protocol).

>[AZURE.NOTE]Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, vá até [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

## Próximas etapas

Para obter mais informações sobre a segurança da plataforma Azure, informações sobre como relatar um **incidente de segurança ou um abuso**, ou informar à Microsoft que você realizará **testes de penetração** no site, consulte a seção de segurança da [Central de Confiabilidade do Microsoft Azure](http://azure.microsoft.com/support/trust-center/security/).

Para obter mais informações sobre os arquivos **web.config** ou **applicationhost.config** em aplicativos Web, consulte [Opções de configuração desbloqueadas em aplicativos Web do Serviço de Aplicativo do Azure](http://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/).

Para obter informações sobre como registrar em log informações de aplicativos Web, que podem ser úteis na detecção de ataques, consulte [Habilitar registro em log de diagnóstico](web-sites-enable-diagnostic-log.md).

## O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

* Para obter um guia sobre a alteração do portal antigo para o novo portal, consulte: [Referência para navegar no portal de visualização](http://go.microsoft.com/fwlink/?LinkId=529715)
 

<!---HONumber=Oct15_HO1-->