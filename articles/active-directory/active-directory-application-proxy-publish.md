<properties
	pageTitle="Publicação de aplicativos com o Proxy de Aplicativo do AD do Azure"
	description="Explica como publicar aplicativos locais usando o Proxy de Aplicativo do AD do Azure."
	services="active-directory"
	documentationCenter=""
	authors="rkarlin"
	manager="terrylan"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="06/02/2015"
	ms.author="rkarlin"/>


# Publicar aplicativos usando o Proxy de Aplicativo do AD do Azure

> [AZURE.NOTE]O Proxy de Aplicativo é um recurso que está disponível somente se você tiver atualizado para a edição Premium ou Básica do Active Directory do Azure. Para obter mais informações, consulte [Edições do Active Directory do Azure](https://msdn.microsoft.com/library/azure/dn532272.aspx).

Depois de habilitar o Proxy de Aplicativo do AD do Microsoft Azure, você pode publicar aplicativos para torná-los acessíveis aos usuários fora de sua rede privada.

Esta seção orienta você pelas etapas necessárias para publicar os aplicativos em execução em sua rede local para os quais deseja habilitar o acesso remoto seguro a partir de fora de sua rede.

> [AZURE.NOTE]Para verificar se o Conector está funcionando corretamente, o primeiro aplicativo que você publicará, antes de publicar um aplicativo real, deve ser qualquer site acessível a partir de sua rede privada, para garantir que os usuários possam acessá-lo da Internet.

## Publicar um aplicativo usando o assistente

1. Abra um navegador de sua escolha e acesse o Portal de Gerenciamento do Azure.
2. No painel esquerdo do Portal de Gerenciamento do Azure, clique na guia Active Directory.
3. Clique no diretório em que você habilitou o Proxy do Aplicativo e no qual deseja publicar um aplicativo (por exemplo, Wingtip Toys).
4. Clique na guia **Aplicativos** e, em seguida, no botão **Adicionar** na parte inferior da tela ![Adicionar aplicativo](http://i.imgur.com/wEeZ7IR.png)
5. Na caixa de diálogo O que você deseja fazer?, clique em **Publicar um aplicativo que estará acessível fora de sua rede**.![Novo aplicativo que estará acessível de fora da rede](http://i.imgur.com/Wj3vkyD.png)

6. Siga as instruções da tela para fornecer as seguintes informações sobre o aplicativo:![Propriedades do aplicativo](http://i.imgur.com/MkPNc93.png)

**Configuração** | **Detalhes**
---|---
URL externa | Esta é a URL do serviço de nuvem que é usada para acessar o aplicativo de fora da sua rede privada. A URL é gerada automaticamente com base no nome fornecido, com o sufixo msappproxy.net.
Método de pré-autenticação | <p>Defina o tipo do método de pré-autenticação que você deseja que o aplicativo use:</p><p>a. Active Directory do Microsoft Azure (AD do Microsoft Azure) – define o método de pré-autenticação para o Active Directory do Microsoft Azure (AD do Microsoft Azure). Quando um usuário tentar acessar um aplicativo, o Proxy de Aplicativo redirecionará o usuário para fazer logon com o AD do Azure, que autenticará o usuário, garantindo que ele tenha as permissões necessárias para o diretório e o aplicativo.</p><p>b. Passagem – a pré-autenticação não é realizada.</p>
Protocolo de URL externa | <p>Por padrão, os aplicativos são publicados usando o protocolo HTTPS.</p> <p>Para habilitar o HTTP para um aplicativo interno, é preciso definir a pré-autenticação como passagem e, em seguida, será possível alterar o protocolo da URL externa de HTTPS para HTTP.</p> <p>Observe que a publicação de aplicativos usando HTTP pode criar problemas de segurança para o aplicativo e os usuários.</p> <p>Você pode inserir um domínio personalizado em vez de usar o msappproxy.net padrão. Para obter mais informações, veja abaixo.</p>
URL interna | Esta é a URL interna que o conector do Proxy de Aplicativo usa para acessar o aplicativo internamente. Ela deverá ser a URL do aplicativo publicado que é usada para acessar o aplicativo de dentro de sua rede privada. Esta é uma URL válida sem caminho e sem espaços ou símbolos.

Para concluir o assistente, clique na marca de seleção na parte inferior da tela. O aplicativo agora está definido no AD do Azure.



## Atribuir usuários e grupos ao aplicativo

1. Para aplicativos pré-autenticados, é preciso atribuir usuários e grupos que terão acesso ao aplicativo. <p>Para aplicativos que são de passagem, o acesso está disponível a todos os usuários. No entanto, para um usuário ver o aplicativo na lista de aplicativos, é preciso atribuir o aplicativo ao usuário.
2. Depois de concluir o assistente de adição de aplicativos, a página Início Rápido do Proxy de Aplicativo será exibida. Para atribuir usuários, clique em **Atribuir usuários**.![Tela de início rápido do Proxy de Aplicativo](http://i.imgur.com/OmuWElV.png)
3. Selecione cada usuário ou grupo que você deseja atribuir a esse aplicativo e clique em **Atribuir**. 

> [AZURE.NOTE]Para aplicativos de autenticação integrada do Windows, é possível atribuir apenas usuários e grupos que são sincronizados a partir do seu Active Directory local. Usuários que fazem logon usando uma conta da Microsoft e convidados não podem ser atribuídos para aplicativos publicados com o Proxy de Aplicativo do Active Directory do Azure. Verifique se os usuários que você atribuiu fazem logon com as credenciais que fazem parte do mesmo domínio que o aplicativo que você está publicando.

## Configuração avançada

1. É possível modificar os aplicativos publicados ou configurar opções avançadas, como SSO para aplicativos locais, na página **Configurar**.![Configuração avançada](http://i.imgur.com/FhbRvoq.png)

2. Selecione o aplicativo e clique em **Configurar**. As opções a seguir estão disponíveis:

**Configuração** | **Detalhes**
---|---
Nome | Forneça um nome descritivo para o aplicativo
URL externa | Esta é a URL do serviço de nuvem que é usada para acessar o aplicativo de fora da sua rede privada. A URL é gerada automaticamente com base no nome fornecido, com o sufixo **msappproxy.net.**
Método de pré-autenticação | Defina o tipo do método de pré-autenticação que você deseja que o aplicativo use:<p>Active Directory – define o método de pré-autenticação para o Active Directory. Quando um usuário tentar acessar um aplicativo, o Proxy de Aplicativo redirecionará o usuário para fazer logon com o Proxy de Aplicativo, que autenticará o usuário, garantindo que ele tenha as permissões necessárias para o diretório e o aplicativo.</p><p> Passagem – a pré-autenticação não é realizada.</p>
URL externa | Por padrão, os aplicativos são publicados usando o protocolo HTTPS. Para habilitar o HTTP para um aplicativo interno, é preciso definir a pré-autenticação como passagem e, em seguida, será possível alterar o protocolo da URL externa de HTTPS para HTTP. Observe que a publicação de aplicativos usando HTTP pode criar problemas de segurança para o aplicativo e os usuários. Você pode definir um domínio personalizado em vez de usar o domínio padrão fornecido (msappproxy.net). Para obter mais informações, consulte Trabalhando com Domínios Personalizados.
URL interna | Esta é a URL interna que o Conector do Proxy de Aplicativo usa para acessar o aplicativo internamente. Ela deverá ser a URL do aplicativo publicado que é usada para acessar o aplicativo de dentro de sua rede privada. Esta é uma URL válida sem caminho e sem espaços ou símbolos.
Converter URL em cabeçalhos | Para aplicativos (como algumas configurações do SharePoint) que requerem que os cabeçalhos de host HTTP não sejam convertidos, defina como Não. Isso desabilitará a conversão de cabeçalhos de solicitação e resposta.
Método de autenticação interna | Se você usar o Proxy de Aplicativo para pré-autenticação, poderá definir um método de autenticação interna para permitir que os usuários aproveitem o logon único (SSO) para este aplicativo. <p> Selecione **Autenticação Integrada do Windows (IWA)** se o aplicativo usar IWA, e você pode configurar a Delegação Restrita de Kerberos (KCD) para habilitar o SSO para este aplicativo. <p> Selecione **Nenhum** se o aplicativo não usar IWA. <p> Aplicativos que usam IWA devem ser configurados usando a KCD; caso contrário, o Proxy de Aplicativo não conseguirá publicar esses aplicativos. <p> Para obter mais informações, consulte SSO para Aplicativos IWA Locais Usando a KCD com o Proxy de Aplicativo
SPN do aplicativo interno | Esse é o Nome da Entidade de Serviço (SPN) do aplicativo interno, conforme configurado no Proxy de Aplicativo local. O SPN é usado pelo Conector do Proxy de Aplicativo para buscar tokens de Kerberos para o aplicativo usando a Delegação Restrita de Kerberos (KCD). <p> Para obter mais informações, consulte SSO para Aplicativos IWA Locais Usando a KCD com o Proxy de Aplicativo

Depois de publicar aplicativos usando o Proxy de Aplicativo do Active Directory do Azure, eles aparecerão na lista de aplicativos do AD do Azure e será possível gerenciá-los de lá. Se você desabilitar os serviços do Proxy de Aplicativo depois de publicar aplicativos, os aplicativos não serão excluídos, mas deixarão de ficar acessíveis de fora da rede privada. Para exibir um aplicativo e verificar se ele está acessível, clique duas vezes no nome do aplicativo. Se o serviço do Proxy de Aplicativo estiver desabilitado e o aplicativo não estiver disponível, uma mensagem de aviso será exibida na parte superior da tela. Para excluir um aplicativo, selecione um aplicativo na lista e depois clique em **Excluir**.

## Recursos adicionais

* [Sobre a delegação restrita de Kerberos](http://technet.microsoft.com/library/cc995228.aspx)

<!---HONumber=July15_HO4-->