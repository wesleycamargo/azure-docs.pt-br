<properties 
	pageTitle="Como gerenciar sites - gerenciamento de serviços do Microsoft Azure" 
	description="Uma referência para as páginas de gerenciamento do site de portal no Microsoft Azure. Fornece detalhes para cada página de gerenciamento do site." 
	services="web-sites" 
	documentationCenter="" 
	authors="MikeWasson" 
	writer="mwasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/18/2014" 
	ms.author="mwasson"/>

#<a name="howtomanage"></a>Gerenciar sites por meio do Portal de Gerenciamento do Azure

Você gerencia seus sites no Portal do Azure com um conjunto de páginas ou "guias". Cada página de gerenciamento de Site é descrita abaixo.

## QuickStart ##
A página de gerenciamento **QuickStart** inclui as seguintes seções:

- **Obter as ferramentas** - Fornece links para [Install WebMatrix][mswebmatrix] e o [SDK do Microsoft Azure][azuresdk].
- **Publicar seu aplicativo** - Fornece links para baixar o perfil de publicação do site, redefinir as credenciais de implantação do site, adicionar um slot de publicação de preparação (implantação) em um site não preparado e informações sobre a publicação de preparação.
- **Integrar o controle do código-fonte** - configure e gerencie a implantação de ferramentas de controle de origem ou sites como TFS, CodePlex, GitHub, Dropbox, Bitbucket ou git local.

## Painel ##
A página de gerenciamento **Painel** inclui o seguinte:

Um gráfico que resume o uso de sites como medidas de determinadas métricas.

 - **Tempo de CPU** - uma medida do uso de CPU do site.
 - **Entrada de dados** - uma medida de dados recebidos pelo site de clientes.
 - **Saída de dados** - uma medida de dados enviados pelo site para clientes.
 - **Erros do Servidor HTTP** O número de mensagens HTTP "Erro de servidor 5xx" enviadas.
 - **Solicitações** - uma contagem de todas as solicitações de cliente para o site.

**Observação:** você pode adicionar métricas de desempenho adicionais na página de gerenciamento de **Monitorar** ao escolher **adicionar métricas** na parte inferior da página. Para obter mais informações, consulte [Como monitorar Sites][Monitoramento].

**Status de ponto de extremidade da Web**Uma lista de pontos de extremidade da web que foram configurados para o monitoramento. Se nenhum ponto de extremidade tiver sido configurado, clique em **Configurar monitoramento de ponto de extremidade da Web** e vá para a seção **Monitoramento** da página de gerenciamento **Configurar**. Para obter mais informações, consulte [Como monitorar sites][Monitoramento].

**Status de Autoescala**No modo padrão, você pode dimensionar automaticamente seus recursos para gastar somente o que precisa. Para habilitar a autoescala, escolha **Configurar AutoEscala**, que leva você para a página **Escala**. Se seu site estiver no modo livre ou compartilhado, será necessário alterar para o modo padrão (você pode fazer isso na página **Escala**) antes de configurar a autoescala. **Logs de Operação de AutoEscala** leva você para o portal **Serviços de Gerenciamento**, onde você pode exibir o histórico de autoescala de seu site. A consulta padrão é para as últimas 24 horas, mas você pode modificar a consulta.

**Visão geral sobre o uso** - esta seção mostra as estatísticas de CPU, sistema de arquivos e uso de memória do site.

**Recursos Vinculados** - Esta seção mostra uma lista de recursos, como um banco de dados MySQL ou SQL, ou uma conta de armazenamento do Microsoft Azure, que estão conectados ao site. Clique no nome do recurso para gerenciar o recurso. Se você tiver um banco de dados MySQL, ao clicar no nome dele você será levado para a página de gerenciamento do ClearDB. Lá você pode ver as métricas de desempenho ou ir para o painel do ClearDB, onde você pode atualizar seu banco de dados MySQL, se necessário. Se não houver recursos listados, clique em **Gerenciar Recursos Vinculados** para ir para a página **Recursos Vinculados**, onde você pode adicionar um link para um recurso de seu site.

Uma seção **Visão Rápida**, que inclui as seguintes informações de resumo e os links (dependendo das configurações, algumas das opções listadas abaixo podem não aparecer):

 - **Exibir Complementos Aplicáveis** - Abre a caixa de diálogo **Comprar na Loja**, onde você pode escolher os complementos para compra que oferecem funcionalidade adicional para seu site. Alguns complementos podem não estar disponíveis em seu ambiente ou região.
 - **Exibir cadeias de conexão** - Exiba suas cadeias de conexão de banco de dados de seu site.
 - **Baixar o perfil de publicação** - Clique neste link para baixar o perfil de publicação de seu site. O perfil de publicação contém suas credenciais (nome de usuário e senha) e as URLs para fazer upload do conteúdo para seu site com FTP e Git. O arquivo de perfil está em formato XML e pode ser exibido em um editor de texto.
 - **Configurar credenciais de implantação** - clique nesta opção para criar um nome de usuário e uma senha para carregar conteúdo de seu site com FTP ou Git. Você pode usar essas credenciais para enviar por push conteúdo a qualquer site na sua assinatura. (Consulte [Credenciais FTP].) **Observação:** não há suporte para a autenticação para um host FTP ou repositório Git usando as credenciais da Conta da Microsoft (Live ID).
 - **Redefinir as credenciais do perfil de publicação** - Redefine o perfil de publicação do site. Os perfis de publicação baixados anteriormente se tornarão inválidos.
 - **Configurar a implantação a partir do controle de origem**Exibe uma caixa de diálogo onde você pode configurar a publicação contínua do Team Foundation Service, CodePlex, GitHub, Dropbox, Bitbucket ou Git local.
 - **Adicionar um novo slot de implantação**Para sites no modo Padrão, use esse recurso para criar um slot de preparo para o site. O slot de preparo (site preparado) permite validar o conteúdo e a configuração do site antes de alternar para a produção. Você também pode usar a versão do site para adicionar gradualmente as atualizações de conteúdo e, em seguida, alternar o site para produção quando as atualizações forem concluídas no slot de preparo. (Você não pode adicionar um slot a um site que já está em preparo).
 - **Editar no Visual Studio Online** - Clique neste link para editar seu site diretamente online usando o Visual Studio Online no portal do Microsoft Azure. Essa opção não será exibida a menos que você a ative na página **Configurar**.
 - **Desconectar do Dropbox**Se você tiver configurado uma conexão com o Dropbox para fins de implantação, esse link permitirá que você a desconecte.
 - **Excluir repositório do Git**Se você tiver configurado um repositório do Git, esse link permitirá que você o exclua.
 - **Status** - Indica se o site está sendo executado.
 - **Serviços de Gerenciamento** - Clique no link **Logs de Operação** para exibir os logs de operação do site no Portal de Serviços de Gerenciamento do Microsoft Azure.
 - **Endereço IP virtual** - Mostra o endereço IP virtual do site se você tiver configurado uma ligação SSL com base em IP para o site na seção **Associações SSL** na guia **Configurar**. 
 - **URL do site** - Especifica o endereço de acesso público do site na Internet.
 - **Modo de computação** - Especifica se o site está em execução no modo Gratuito, Compartilhado, Básico ou Padrão. For more information about web scale group modes, see [How to Scale a Web Site][Escala].
 - **Nome de Host FTP** - Especifica a URL a ser usada ao publicar o site por FTP (consulte [Credenciais FTP]).
 - **Nome de Host FTPS** - Especifica a URL a ser usada ao publicar o site por FTPS (consultar [Credenciais FTP]). 
 - **Usuário da Implantação/Usuário FTP** - Indica a conta usada ao implantar o site do Microsoft Azure por FTP ou Git (consulte [Credenciais FTP]).
 - **Logs de diagnóstico FTP** - Especifica o local de FTP de logs de diagnóstico do site se o log de diagnóstico estiver ativado na página de gerenciamento **Configurar**.
 - **Logs de diagnóstico FTPS** - Especifica o local de FTPS de logs de diagnóstico do site se o log de diagnóstico estiver ativado na página de gerenciamento **Configurar**.
 - **Local** - Especifica a região do data center que hospeda o site.
 - **Nome da assinatura** - Especifica o nome da assinatura a qual o site está associado.
 - **ID da assinatura** - Especifica a ID única da assinatura (GUID) a qual o site está associado.


##Implantações##
 Essa guia é exibida somente se você tiver configurado a implantação por meio do controle do código-fonte. A página de gerenciamento **Implantações** fornece um resumo de todas as implantações feitas no site usando o método de publicação de sua escolha. Se a publicação Git foi configurada para o site, mas não é feita nenhuma implantação, a página de gerenciamento **Implantações** fornece informações que descrevem como usar o GIT para implantar o aplicativo Web no site.

##Monitorar##
A página de gerenciamento **Monitor** fornece um gráfico que exibe informações de uso do site. Por padrão, esse gráfico exibe as mesmas métricas do gráfico na página **Painel**, conforme descrito anteriormente na seção Painel. O gráfico também pode ser configurado para exibir as métricas de sucesso de HTTP, redirecionamentos HTTP, erros de HTTP 401, erros de HTTP 403, erros HTTP 404 e erros HTTP 406. Para obter mais informações sobre essas métricas, consulte [Como monitorar sites][Monitoramento].

##WebJobs##
A página de gerenciamento WebJobs permite criar tarefas sob demanda, agendadas ou em execução contínua para seu site. Para obter mais informações, consulte [Como usar o recurso WebJobs em Sites do Microsoft Azure](http://azure.microsoft.com/documentation/articles/web-sites-create-web-jobs/).

##Configurar##
A página de gerenciamento **Configurar** é usada para definir configurações específicas do aplicativo.

Para obter detalhes, consulte [Como configurar sites][Configurar].


##Escala##
Na página **Escala**, você pode especificar o modo do grupo de dimensionamento da web (**Gratuito**, **Compartilhado**, **Básico** ou **Padrão**). **Os modosCompartilhado**, **Básico** e **Padrão** fornecem melhor desempenho e produtividade. **Os modos Compartilhado****, Básico** e **Padrão** permitem aumentar a **Contagem de Instâncias**, que é o número de máquinas virtuais usadas pelo seu site e por outros sites no mesmo grupo de dimensionamento web.
 
No modo **Padrão**, você também pode aumentar a capacidade de contagem e memória do núcleo de cada instância alterando o **Tamanho da instância**.  Para maior economia de custo, você pode escolher a opção **Autoescala** para que o Microsoft Azure aloque dinamicamente os recursos de seu site. 

Para obter mais informações sobre a configuração de opções de dimensionamento de um site, consulte [Como dimensionar um site][Escala].

##Recursos Vinculados##
A página de gerenciamento **Recursos Vinculados** fornece uma lista de recursos do Microsoft Azure que seu site está usando, incluindo bancos de dados SQL, MySQL e contas de armazenamento do Azure. Clique no nome do recurso para gerenciá-lo.

##Backups##
A página de gerenciamento **Backups** permite criar backups manuais ou automatizados de seu site, restaurar seu site para um estado anterior ou criar um novo site baseado em um dos seus backups. Para obter mais informações, consulte [Backups de Sites do Microsoft Azure](http://azure.microsoft.com/documentation/articles/web-sites-backup/) e [Restaurar um site do Microsoft Azure](http://azure.microsoft.com/documentation/articles/web-sites-restore/).

##Ícones da Página de Gerenciamento##
Os ícones são exibidos na parte inferior de cada uma das páginas de gerenciamento do site. Vários desses ícones aparecem em várias páginas e alguns deles são exibidos apenas em páginas específicas.  Os ícones a seguir são exibidos na parte inferior da página de gerenciamento **Painel**:

- **Procurar** - Abre a página padrão do site.
- **Parar** - Para o site.
- **Reiniciar** - Reinicia o site.
- **Gerenciar domínios** - Mapeia um domínio para este site. Não está disponível para sites no modo de dimensionamento **livre**.
- **Excluir** - Exclui o site.
- **WebMatrix** - Abre sites com suporte no WebMatrix, permitindo que você faça alterações no site e publique essas alterações no Microsoft Azure.

Os ícones a seguir não são exibidos na parte inferior da página de gerenciamento do **Painel**, mas estão na parte inferior de outras páginas de gerenciamento para realizar tarefas específicas:

- **Adicionar métricas**Na parte inferior da página de gerenciamento **Monitor**, permite que você adicione métricas para o gráfico exibido na página de gerenciamento Monitor.
- **Link**Na parte inferior da página de gerenciamento **Recursos Vinculados**, permite que você crie links de gerenciamento para outros recursos do Microsoft Azure
-  Recursos do Azure. Por exemplo, se seu site acessa um banco de dados SQL, você pode criar um link de gerenciamento para o recurso de banco de dados clicando em **Link**.


## Credenciais FTP

Há dois conjuntos de credenciais FTP que você pode usar, credenciais *deployment* e credenciais  *publishing profile*.  Aqui estão as diferenças principais:

**Credenciais de implantação**

- Associadas com a conta da Microsoft. 
- Podem ser usadas para implantar para qualquer site, em todas as assinaturas associadas com a conta. 
- Você escolhe o nome/senha de usuário
- Tipicamente usado para implantação git ou FTP

	 
**Credenciais de perfil de publicação**

- Associadas apenas com um site. 
- Você não escolhe o nome ou a senha de usuário.
- Tipicamente usado para Implantação web, mas pode ser usado para FTP.


Você pode usar qualquer conjunto de credenciais. Os nomes de host FTP e FTPS estão listados no painel, em **Visão rápida**.


### Usar as credenciais de implantação

Para configurar as credenciais de implantação: 

1.	No portal de gerenciamento, abra a guia **Painel** do seu site.
2.	Clique em **Configurar as credenciais de implantação**.
3.	No diálogo, insira o nome de usuário e a senha.

Observação: Na etapa 2, se você já tiver credenciais implantadas, o portal informará **Redefinir credenciais de implantação**. Clique para configurar uma nova senha ou alterar o nome de usuário.

As credenciais de implantação estão associadas com uma conta da Microsoft. Se você alterar o nome de usuário ou a senha, a alteração se aplica a todos os sites associados com a conta. Se uma assinatura do Azure tiver vários administradores, cada pessoa tem suas próprias credenciais. 
O nome completo de usuário FTP é "website\username".  Isto é listado no portal em **Visão Rápida**, como **Implantação / usuário FTP**.


### Usar as credenciais de perfil de publicação

Cada site tem suas próprias credenciais de perfil de publicação. Para ver essas credenciais:

1.	No portal de gerenciamento, abra a guia **Painel** do seu site.
2.	Clique em **Baixar o perfil de publicação**.

O arquivo de perfil de publicação é um arquivo XML. Contém dois perfis, um para Implantação web, e outro para FTP.

<pre>
&lt;publishData&gt;
  &lt;publishProfile
    profileName="contoso - Web Deploy"
    publishMethod="MSDeploy"
    publishUrl="contoso.scm.azurewebsites.net:443"
    msdeploySite="contoso"
    userName="$contoso"
    userPWD="abc1234..."
    destinationAppUrl="http://contoso.azurewebsites.net"
    SQLServerDBConnectionString=""
    mySQLDBConnectionString=""
    hostingProviderForumLink="" 
    controlPanelLink="http://windows.azure.com"&gt;
    &lt;databases/&gt;
  &lt;/publishProfile&gt;
  &lt;publishProfile 
    profileName="contoso - FTP" 
    <mark>publishMethod="FTP"</mark> 
    publishUrl="ftp://waws-prod-bay-003.ftp.azurewebsites.windows.net/site/wwwroot" 
    ftpPassiveMode="True" 
    <mark>userName="contoso\$contoso"</mark> 
    <mark>userPWD=" abc1234..."</mark>  
    destinationAppUrl="http://contoso.azurewebsites.net" 
    SQLServerDBConnectionString="" 
    mySQLDBConnectionString="" 
    hostingProviderForumLink="" 
    controlPanelLink="http://windows.azure.com"&gt;
    &lt;databases/&gt;
  &lt;/publishProfile&gt;
&lt;/publishData&gt;
</pre>

Procure o perfil com <code>publishMethod="FTP"</code>.  O nome de usuário está listado em <code>userName</code>, e a senha está listada em <code>userPWD</code>.

Para redefinir a senha, clique em **Redefina as credenciais do perfil de publicação**. Para obter as novas credenciais, baixar o perfil de publicação novamente. As credenciais de perfil de publicação estão associadas com o site. Cada site tem seu próprio perfil de publicação.



<!-- LINKS -->
[mswebmatrix]:http://go.microsoft.com/fwlink/?LinkID=226244

[azuresdk]:http://go.microsoft.com/fwlink/?LinkId=246928

[Configurar]: http://azure.microsoft.com/manage/services/web-sites/how-to-configure-websites

[Monitoramento]: http://azure.microsoft.com/manage/services/web-sites/how-to-monitor-websites/

[Escala]: http://azure.microsoft.com/manage/services/web-sites/how-to-scale-websites


<!-- Anchors. -->
[Credenciais FTP]: #ftp-credentials



<!--HONumber=42-->
