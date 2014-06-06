<properties linkid="manage-scenarios-how-to-manage-websites" urlDisplayName="Como gerenciar" pageTitle="Como gerenciar sites - Gerenciamento de serviços do Azure - Gerenciamento de serviços do Microsoft Azure" metaKeywords="gerenciamento do site do portal do Azure" description="Uma referência às páginas de gerenciamento de sites do Portal no Azure. São fornecidos os detalhes de cada página de gerenciamento de sites." metaCanonical="" services="web-sites" documentationCenter="" title="Como gerenciar Sites" authors="timamm"  solutions="" writer="timamm" manager="" editor=""  />





#<a name="howtomanage"></a>Como gerenciar Sites

Você gerencia seus sites no Portal do Azure com um conjunto de páginas ou "guias". Cada página de gerenciamento de Site é descrita abaixo.

## QuickStart ##
A página de gerenciamento **QuickStart** inclui as seguintes seções:

- **Obter as ferramentas** - Fornece links para [Instalar o WebMatrix][mswebmatrix] e o [SDK do Microsoft Azure][azuresdk].
- **Publicar seu aplicativo** - Fornece links para baixar o perfil de publicação do site, redefinir as credenciais de implantação do site, adicionar um slot de publicação de preparação (implantação) em um site não preparado e informações sobre a publicação de preparação.
- **Integrar o controle de origem** - Configure e gerencie a implantação de ferramentas de controle de origem ou sites, como TFS, CodePlex, GitHub, Dropbox, Bitbucket ou Git local.

## Painel ##
A página de gerenciamento **Painel** inclui o seguinte:

- Um gráfico que resume o uso de sites como medidas de determinadas métricas.
 - **Tempo de CPU** - Uma medida do uso de CPU do site.
 - **Entrada de Dados** - Uma medida dos dados recebidos pelo site de clientes.
 - **Saída de Dados** - Uma medida de dados enviados pelo site aos clientes.
 - **Erros do Servidor HTTP** - O número de mensagens HTTP "Erro de servidor 5xx" enviadas.
 - **Solicitações** - Uma contagem de todas as solicitações de cliente para o site.
 <br />**Observação:**
Você pode adicionar métricas de desempenho extras na página de gerenciamento do **Monitor** escolhendo **Adicionar métricas** na parte inferior da página. Para obter mais informações, consulte [Como monitorar sites (a página pode estar em inglês)][Monitor].

- **Status de ponto de extremidade da Web** - Uma lista de pontos de extremidade da web que foram configurados para o monitoramento. Se nenhum ponto de extremidade tiver sido configurado, clique em **Configurar monitoramento de ponto de extremidade da Web** e vá para a seção **Monitoramento** da página de gerenciamento **Configurar**. Para obter mais informações, consulte [Como monitorar sites (a página pode estar em inglês)][Monitor].

- **Status de Autoescala** - No modo padrão, você pode dimensionar automaticamente seus recursos para gastar somente o que precisa. Para habilitar a autoescala, escolha **Configurar AutoEscala**, que leva você para a página **Escala**. Se seu site estiver no modo livre ou compartilhado, será necessário alterar para o modo padrão (você pode fazer isso na página **Escala**) antes de configurar a autoescala. **Logs de Operação de AutoEscala** leva você para o portal **Serviços de Gerenciamento**, onde você pode exibir o histórico de autoescala de seu site. A consulta padrão é para as últimas 24 horas, mas você pode modificar a consulta.

- **Visão geral sobre o uso** - Esta seção mostra as estatísticas de uso de CPU, sistema de arquivos e memória do site.
- **Recursos Vinculados** - Esta seção mostra uma lista de recursos, como um banco de dados MySQL ou SQL, ou uma conta de armazenamento do Microsoft Azure, que estão conectados ao site. Clique no nome do recurso para gerenciar o recurso. Se você tiver um banco de dados MySQL, ao clicar no nome dele você será levado para a página de gerenciamento do ClearDB. Lá você pode ver as métricas de desempenho ou ir para o painel do ClearDB, onde você pode atualizar seu banco de dados MySQL, se necessário. Se não houver recursos listados, clique em **Gerenciar Recursos Vinculados** para ir para a página **Recursos Vinculados**, onde você pode adicionar um link para um recurso de seu site.
- Uma seção **Visão Rápida** que inclui as seguintes informações de resumo e os links (dependendo de suas configurações, algumas das opções listadas abaixo podem não aparecer):
 - **Exibir Complementos Aplicáveis** - Abre a caixa de diálogo **Comprar na Loja** onde você pode escolher complementos para compra que fornecem funcionalidade adicional para seu site. Alguns complementos podem não estar disponíveis em seu ambiente ou região.
 - **Exibir cadeias de conexão** - Exiba suas cadeias de conexão do banco de dados de seu site.
 - **Baixar o perfil de publicação** - Clique neste link para baixar o perfil de publicação de seu site. O perfil de publicação contém suas credenciais (nome de usuário e senha) e as URLs para fazer upload do conteúdo para seu site com FTP e Git. O arquivo de perfil está em formato XML e pode ser exibido em um editor de texto.
 - **Configurar credenciais de implantação** - Clique nesta opção para criar um nome de usuário e uma senha para carregar conteúdo em seu site com FTP ou Git. Depois que você tiver criado as suas credenciais de implantação FTP/Git, você pode usá-las para enviar conteúdo para qualquer site na sua assinatura usando FTP ou Git.  Para exibir as credenciais depois de criá-las, clique em **Baixar o perfil de publicação**. O perfil de publicação que você baixou é um arquivo de texto em formato XML, que pode ser visualizado em um editor de texto. **Observação**: não há suporte para a autenticação para um host FTP ou repositório Git usando as credenciais da Conta da Microsoft (Live ID).
 - **Redefinir as credenciais do perfil de publicação** - Redefine o perfil de publicação do site. Os perfis de publicação baixados anteriormente se tornarão inválidos.
 - **Configurar a implantação a partir do controle de origem** - Exibe uma caixa de diálogo onde você pode configurar a publicação contínua do Team Foundation Service, CodePlex, GitHub, Dropbox, Bitbucket ou Git local.
 - **Adicionar um novo slot de implantação** - Para sites no modo Padrão, use esse recurso para criar um slot de preparo para o site. O slot de preparo (site preparado) permite validar o conteúdo e a configuração do site antes de alternar para a produção. Você também pode usar a versão do site para adicionar gradualmente as atualizações de conteúdo e, em seguida, alternar o site para produção quando as atualizações forem concluídas no slot de preparo. (Você não pode adicionar um slot a um site que já está em preparo).
 - **Editar no Visual Studio Online** - Clique neste link para editar seu site diretamente online usando o Visual Studio Online no portal do Microsoft Azure. Essa opção não será exibida a menos que você a ative na página **Configurar**.
 - **Desconectar do Dropbox** - Se você tiver configurado uma conexão com o Dropbox para fins de implantação, esse link permitirá que você a desconecte.
 - **Excluir repositório do Git** - Se você tiver configurado um repositório do Git, esse link permitirá que você o exclua.
 - **Status** - Indica se o site está em execução.
 - **Serviços de Gerenciamento** - Clique no link **Logs de Operação** para exibir os logs de operação do site no Portal de Serviços de Gerenciamento do Microsoft Azure.
 - **Endereço IP Virtual** - Mostra o endereço IP virtual do site se você tiver configurado uma associação SSL com base em IP para o site na seção **Associações SSL** na guia **Configurar**. 
 - **URL do site** - Especifica o endereço de acesso público do site na Internet.
 - **Modo de computação** - Especifica se o site está em execução no modo Gratuito, Compartilhado, Básico ou Padrão. Para obter mais informações sobre os modos de grupos de dimensionamento da web, consulte [Como dimensionar um site (a página pode estar em inglês)][Scale].
 - **Nome de Host FTP** - Especifica a URL a ser usada ao publicar o site por FTP.
 - **Nome do Host FTPS** - Especifica a URL a ser usada ao publicar o site por FTPS.
 - **Usuário da Implantação / Usuário FTP** - Indica a conta usada ao implantar o site do Microsoft Azure por FTP ou Git.
 - **Logs de Diagnóstico FTP** - Especifica o local FTP dos logs de diagnóstico do site se o log de diagnóstico estiver habilitado na página de gerenciamento **Configurar**.
 - **Logs de Diagnóstico FTPS** - Especifica o local FTPS dos logs de diagnóstico do site se o log de diagnóstico estiver habilitado na página de gerenciamento **Configurar**.
 - **Local** - Especifica a região do data center que hospeda o site.
 - **Nome da Assinatura** - Especifica o nome da assinatura à qual o site está associado.
 - **ID da assinatura** - Especifica a ID (GUID) exclusiva da assinatura à qual o site está associado.


##Implantações##
 Essa guia é exibida somente se você tiver configurado a implantação a partir do controle de origem. A página de gerenciamento **Implantações** fornece um resumo de todas as implantações feitas no site usando o método de publicação de sua escolha. Se a publicação Git foi configurada para o site, mas não é feita nenhuma implantação, a página de gerenciamento **Implantações** fornece informações que descrevem como usar o GIT para implantar o aplicativo web no site.

##Monitoramento##
A página de gerenciamento **Monitor** fornece um gráfico que exibe informações de uso do site. Por padrão, esse gráfico exibe as mesmas métricas do gráfico na página **Painel**, conforme descrito anteriormente na seção Painel. O gráfico também pode ser configurado para exibir as métricas de sucesso de HTTP, redirecionamentos HTTP, erros de HTTP 401, erros de HTTP 403, erros HTTP 404 e erros HTTP 406. Para obter mais informações sobre essas métricas, consulte [Como monitorar sites (a página pode estar em inglês)][Monitor].

##Trabalhos Web##
A página de gerenciamento Trabalhos Web permite criar tarefas sob demanda, agendadas ou em execução contínua para seu site. Para obter mais informações, consulte [Como usar o recurso Trabalhos Web em Sites do Microsoft Azure (a página pode estar em inglês)](http://www.windowsazure.com/pt-br/documentation/articles/web-sites-create-web-jobs/).

##Configurar##
A página de gerenciamento **Configurar** é usada para definir configurações específicas do aplicativo, incluindo:

- **Geral** - Defina a versão do .NET Framework, PHP, Python ou Java requerida por seu aplicativo Web. Em sites no modo Padrão ou Básico, há uma opção de escolher uma plataforma de 64 bits. Defina o **Modo de Pipeline Gerenciado**  como **Clássico** somente se você tiver sites herdados que são executados exclusivamente em versões anteriores do IIS (**integrado** é o padrão.) Para habilitar o seu site para usar aplicativos de solicitação padrão em tempo real, como o bate-papo, você pode definir o **Web Sockets** como **Ativado**. Para ativar a edição de seu site diretamente online, defina **Editar no Visual Studio Online** como **Ativado**.
- **Certificados** - Carrega um certificado SSL para um domínio personalizado. Os certificados SSL podem ser carregados apenas no modo Padrão ou Básico. Os certificados que você carregar estão listados aqui e podem ser atribuídos a qualquer site da web na sua assinatura e região. Há suporte para certificados de curinga (certificados com um asterisco).
- **Nomes de Domínios** - Exiba ou adicione nomes de domínios personalizados a um site. Nomes de domínios personalizados só podem ser usados no modo Compartilhado, Básico ou Padrão.
- **Associações SSL** - As associações SSL para domínios personalizados só podem ser usadas no modo Básico ou Padrão. Escolha um modo SSL (**SNI**, **IP** ou **Sem SSL**) para um nome de domínio específico. Se você escolher SNI ou IP, você pode especificar um certificado para o domínio de certificados que você carregou.  
- **Implantações** - Esta seção é exibida somente se você tiver habilitado a implantação no controle de origem. Use essas configurações para definir implantações.
- **Diagnóstico de Aplicativo** - Defina as opções de coleta de informações de diagnóstico para aplicativos web que dão suporte ao registro em log. Você pode optar por registrar em log no sistema de arquivos ou em uma conta de armazenamento do Microsoft Azure e escolher um nível de log para especificar a quantidade de informações coletadas.
- **Diagnóstico de Site** - Defina opções de log para coletar informações de diagnóstico para seu site ou habilite o Visual Studio 2012 ou o Visual Studio 2013 para depurar o site remotamente por um máximo de 48 horas.
- **Monitoramento** - Em sites no modo Padrão, teste a disponibilidade de pontos de extremidade HTTP ou HTTPS em locais distribuídos geograficamente.
- **Análise para o Desenvolvedor** - Monitor de análise de desempenho do seu aplicativo Web. Escolha um complemento de análise no repositório do Microsoft Azure ou escolha um provedor de análise personalizada, como o New Relic.
- **Configurações do Aplicativo** - Especifique os pares de nome/valor que serão carregados por seu aplicativo Web na inicialização. Para sites .NET, essas configurações serão injetadas em sua configuração AppSettings do .NET em tempo de execução, substituindo as configurações existentes. Para sites PHP e de nó essas configurações estarão disponíveis como variáveis de ambiente em tempo de execução.
- **Cadeias de conexão** - Exiba cadeias de conexão para recursos vinculados. Para sites .NET, essas cadeias de conexão serão injetadas em suas configurações connectionStrings do site .NET em tempo de execução substituindo as entradas existentes onde a chave é igual ao nome do banco de dados vinculado. Para sites PHP e de nó essas configurações estarão disponíveis como variáveis de ambiente em tempo de execução.
- **Documentos Padrão** - Adicione o documento padrão do seu site a essa lista se ele ainda não estiver na lista. Se o site contiver mais de um dos arquivos na lista, certifique-se de que documento do seu site será exibido na parte superior da lista, alterando a ordem dos arquivos na lista.
- **Mapeamentos de Manipulador** - Adicione processadores de script personalizados que manipulem as solicitações de tipos de arquivos específicos (por exemplo, *.php).
- **Diretórios e Aplicativos Virtuais**  - Configure aplicativos virtuais e diretórios associados ao seu site. Você também tem a opção de marcar um diretório virtual como um aplicativo de configuração do site.

Para obter informações adicionais sobre como um Site, consulte [Como configurar sites][Configure].


##Escala##
Na página **Escala**, você pode especificar o modo do grupo de dimensionamento da web (**Gratuito**, **Compartilhado**, **Básico** ou **Padrão**). Os modos**Compartilhado**, **Básico** e **Padrão** fornecem melhor desempenho e produtividade. Os modos **Compartilhado**, **Básico** e **Padrão** permitem aumentar a **Contagem de Instâncias**, que é o número de máquinas virtuais usadas pelo seu site e por outros sites no mesmo grupo de dimensionamento web.
 
No modo **Padrão**, você também pode aumentar a capacidade de contagem e memória do núcleo de cada instância alterando o **Tamanho da instância**.  Para maior economia de custo, você pode escolher a opção **Autoescala** para que o Microsoft Azure aloque dinamicamente os recursos de seu site. 

Para obter mais informações sobre a configuração de opções de dimensionamento de um site, consulte [Como dimensionar um site][Scale].

##Recursos Vinculados##
A página de gerenciamento **Recursos Vinculados** fornece uma lista de recursos do Microsoft Azure que seu site está usando, incluindo bancos de dados SQL, MySQL e contas de armazenamento do Azure. Clique no nome do recurso para gerenciá-lo.

##Backups##
A página de gerenciamento **Backups** permite criar backups manuais ou automatizados de seu site, restaurar seu site para um estado anterior ou criar um novo site baseado em um dos seus backups. Para obter mais informações, consulte [Backups de sites do Microsoft Azure (a página pode estar em inglês)](http://www.windowsazure.com/pt-br/documentation/articles/web-sites-backup/) e [Restaurar um site do Microsoft Azure (a página pode estar em inglês)](http://www.windowsazure.com/pt-br/documentation/articles/web-sites-restore/).

##Ícones da Página de Gerenciamento##
Os ícones são exibidos na parte inferior de cada uma das páginas de gerenciamento do site. Vários desses ícones aparecem em várias páginas e alguns deles são exibidos apenas em páginas específicas.  Os ícones a seguir são exibidos na parte inferior da página de gerenciamento **Painel**:

- **Procurar** - Abre a página padrão do site.
- **Parar** - Para o site.
- **Reiniciar** - Reinicia o site.
- **Gerenciar domínios** - Mapeia um domínio para este site. Não está disponível para sites no modo de dimensionamento **livre**.
- **Excluir** - Exclui o site.
- **WebMatrix** - Abre sites com suporte no WebMatrix, permitindo que você faça alterações no site e publique essas alterações no Microsoft Azure.

Os ícones a seguir não são exibidos na parte inferior da página de gerenciamento do **Painel**, mas estão na parte inferior de outras páginas de gerenciamento para realizar tarefas específicas:

- **Adicionar métricas** - Na parte inferior da página de gerenciamento **Monitor**, permite que você adicione métricas para o gráfico exibido na página de gerenciamento Monitor.
- **Link** - Na parte inferior da página de gerenciamento **Recursos Vinculados**, permite que você crie links de gerenciamento para outros recursos do Microsoft Azure.
-  Recursos do Azure. Por exemplo, se seu site acessa um banco de dados SQL, você pode criar um link de gerenciamento para o recurso de banco de dados clicando em **Link**.


<!-- LINKS -->
[mswebmatrix]:http://go.microsoft.com/fwlink/?LinkID=226244

[azuresdk]:http://go.microsoft.com/fwlink/?LinkId=246928

[Configure]: http://www.windowsazure.com/pt-br/manage/services/web-sites/how-to-configure-websites

[Monitor]: http://www.windowsazure.com/pt-br/manage/services/web-sites/how-to-monitor-websites/

[Scale]: http://www.windowsazure.com/pt-br/manage/services/web-sites/how-to-scale-websites


