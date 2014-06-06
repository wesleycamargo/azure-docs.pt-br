<properties linkid="manage-services-how-to-create-websites" urlDisplayName="Como criar" pageTitle="Como criar sites da Web - gerenciamento de serviço do Azure" metaKeywords="Criando site no Azure, excluindo site no Azure" description="Saiba como criar um site usando o Portal de Gerenciamento do Azure." metaCanonical="" services="web-sites" documentationCenter="" title="Como criar e implantar um Site" authors=""  solutions="" writer="timamm" manager="" editor=""  />

#Como criar um site

Este tópico mostra como criar um site na galeria ou usando o portal de gerenciamento.

Para obter informações sobre como implantar o conteúdo em um Site no Azure Web que você criou, consulte a seção **Implantar** em [Sites do Azure](/pt-br/documentation/services/web-sites/)

## Sumário ##

- [Como criar um Site usando o Portal de Gerenciamento](#createawebsiteportal)
- [Como criar um site na galeria](#howtocreatefromgallery)
- [Como excluir um site](#deleteawebsite)
- [Próximas etapas](#nextsteps)

##<a name="createawebsiteportal"></a>Como criar um site usando o Portal de Gerenciamento

Siga estas etapas para criar um site no Azure.
	
1. Faça logon no [Portal de Gerenciamento do Azure](http://manage.windowsazure.com/).

2. Clique no ícone **Criar Novo** na parte inferior esquerda do Portal de Gerenciamento.

3. Clique no ícone **Site**, clique no ícone **Criação Rápida**, digite um valor para a URL e clique na marca de verificação ao lado de **Criar Site** no canto inferior direito da página.

4. Quando o site for criado, você verá o texto **Criando o site <*web site name*> com êxito**. Você pode navegar até o site clicando em **Procurar** na parte inferior da página do portal.

5. No portal, clique no nome do site exibido na lista de sites para abrir a página de gerenciamento **Início Rápido** do site.

6. Na página **Início Rápido**, são fornecidas opções para você obter as ferramentas de desenvolvimento de site, configurar a publicação de seu site ou configurar a implantação de um provedor de controle de origem, como TFS ou Git. Por padrão, a publicação FTP é configurada para sites, e o nome do Host FTP é exibido na seção **Visão Rápida** da página **Painel** em **Nome do Host FTP**. Antes da publicação com FTP ou Git, escolha a opção para **Redefinir credenciais de implantação** na página **Painel** para que você possa autenticar no Host FTP ou no Repositório Git ao implantar conteúdo em seu site.

7. A página de gerenciamento **Configurar** expõe as configurações de seu site nas seguintes categorias:

 - **Geral**: definir a versão do .NET Framework ou PHP exigido por seu aplicativo Web. Para sites no modo Padrão, a opção **Plataforma** permite que você escolha um ambiente de 32 ou de 64 bits.

- **Certificados**: no modo Padrão, você pode carregar certificados SSL para domínios personalizados. 

- **Nomes de Domínio**: nos modos Padrão e Compartilhado, você pode exibir e gerenciar nomes de domínio personalizado para seu site.

- **Associações SSL**: no modo Padrão, você pode atribuir certificados SSL para seus nomes de domínio personalizado usando SSL baseado em IP ou SNI.

 - **Implantações**: quando configura a implantação no controle de origem, você pode configurar suas implantações aqui.

 - **Diagnóstico de Aplicativo**: definir opções para a coleta de rastreamentos de diagnóstico de um aplicativo Web que tiver sido instrumentado com rastreamentos. 

- **Diagnóstico de Site**: definir opções de log para coletar informações de diagnóstico para seu site. As opções incluem o Log de servidor Web, Mensagens de Erro Detalhadas e Falha no Rastreamento de Solicitação.

- **Monitoramento**: para sites no modo Padrão, você pode usar esse recurso para testar a disponibilidade de pontos de extremidade HTTP ou HTTPS. 

- **Configurações do Aplicativo**: especificar pares nome/valor que serão carregados pelo seu aplicativo Web na inicialização. Para sites .NET, essas configurações serão injetadas em sua configuração **AppSettings** do .NET em tempo de execução, substituindo as configurações existentes. Para sites PHP e de nó essas configurações estarão disponíveis como variáveis de ambiente em tempo de execução.

 - **Cadeias de conexão**: exibir e editar cadeias de conexão. Para sites .NET, essas cadeias de conexão serão injetadas em suas configurações **connectionStrings** do .NET em tempo de execução substituindo as entradas existentes onde a chave é igual ao nome do banco de dados vinculado. Para sites PHP e de nó, essas configurações estarão disponíveis como variáveis de ambiente em tempo de execução.

 - **Documentos Padrão**: o documento padrão de um site é a página que é exibida por padrão quando um usuário navega para um site. Adicione o documento padrão de seu aplicativo a essa lista se ele ainda não estiver presente.  O documento padrão do seu site deve estar na parte superior da lista.

- **Mapeamentos de Manipulador**: especificar processadores de script que manipularão as solicitações de extensões de arquivo específicas como *.php.

##<a name="howtocreatefromgallery"></a>Como criar um site na galeria

[WACOM.INCLUDE [website-from-gallery](../includes/website-from-gallery.md)]

##<a name="deleteawebsite"></a>Como excluir um site
Os sites são excluídos usando o ícone **Excluir** no Portal de Gerenciamento do Azure. O ícone **Excluir** está disponível no Portal do Azure, quando você clica em **Sites da Web** para listar todos os seus sites e, na parte inferior de cada uma das páginas de gerenciamento de site.

##<a name="nextsteps"></a>Próximas etapas

Para obter mais informações, consulte [Sites do Azure](/pt-br/documentation/services/web-sites/).

