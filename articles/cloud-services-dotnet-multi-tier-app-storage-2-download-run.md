<properties linkid="develop-net-tutorials-multi-tier-web-site-2-download-and-run" urlDisplayName="Etapa 2: Baixar e executar" pageTitle="Aplicativo Web multicamadas ASP.NET com o Azure - Etapa 2: Baixar e executar" metaKeywords="tutorial do Azure, implantando aplicativo de serviço de email, publicando serviço de email." description="O segundo tutorial em uma série que ensina como configurar seu computador para desenvolvimento do Azure e implantar o aplicativo de Serviço de Email." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Configurando e implantando o aplicativo de Serviço de Email do Azure - 2 de 5" authors="riande,tdykstra" solutions="" manager="wpickett" editor="mollybos" />

# Configurando e implantando o aplicativo de Serviço de Email do Azure - 2 de 5

Este é o segundo de uma série de cinco tutoriais que mostram como criar e implantar o aplicativo de exemplo do Serviço de Email do Azure.  Para obter informações sobre o aplicativo e a série de tutoriais, consulte [o primeiro tutorial da série][firsttutorial].

Este tutorial mostra como configurar seu computador para o desenvolvimento do Azure e como implantar o aplicativo de Serviço de Email do Windows Azure em um Serviço de Nuvem do Windows Azure usando qualquer um dos seguintes produtos:

* Visual Studio 2012
* Visual Studio 2012 Express para Web
* Visual Studio 2010
* Visual Web Developer Express 2010.

>[WACOM.NOTE] Depois que este tutorial foi escrito, o Visual Studio 2013 foi lançado e o Portal de Gerenciamento e o SDK do Azure foram atualizados. Anotações como essa foram adicionadas em pontos onde é necessário fazer coisas de maneira diferente se você estiver usando Visual Studio 2013 e o SDK mais recente. As anotações foram escritas em março de 2014, e os procedimentos revisados foram testados com o SDK da versão 2.3.

É possível abrir uma conta do Azure gratuitamente e, se você ainda não tiver o Visual Studio 2013, o SDK instalará automaticamente o Visual Studio 2013 para o Web Express. Portanto, você pode começar a desenvolver para o Azure de maneira totalmente gratuita.

Neste tutorial, você aprenderá:

* Como configurar seu computador para desenvolvimento do Azure instalando o SDK do Azure.
* Como configurar e testar o aplicativo de Serviço de Email do Azure em seu computador local.
* Como publicar o aplicativo no Azure.
* Como exibir e editar blobs, filas e tabelas do Azure usando o Visual Studio ou o Azure Storage Explorer.
* Como configurar o rastreamento e exibir dados de rastreamento.
* Como dimensionar o aplicativo aumentando o número de instâncias da função de trabalho.

<div class="dev-callout"><strong>Observação</strong>
<p>Para concluir este tutorial, você precisa de uma conta do Azure. Você pode criar uma <a href="/pt-br/pricing/free-trial/" target="_blank">conta de avaliação gratuita</a> ou <a href="/pt-br/pricing/member-offers/msdn-benefits/" target="_blank">ativar os benefícios de assinante MSDN</a>.</p>
</div>
<br />

### Segmentos do tutorial

- [Configurar o ambiente de desenvolvimento][]
- [Configurar uma conta gratuita do Azure][]
- [Criar uma conta de Armazenamento do Azure][]
- [Instalar o Pesquisador de Armazenamento do Azure][]
- [Criar um Serviço de Nuvem][]
- [Baixar e executar a solução completa][]
- [Exibir o armazenamento de desenvolvedor no Visual Studio][]
- [Configurar o aplicativo para o Armazenamento do Azure][]
- [Implantar o aplicativo no Azure][]
- [Promover o aplicativo do preparo para a produção][]
- [Configurar o aplicativo para usar o SendGrid][]
- [Configurar e exibir dados de rastreamento][]
- [Adicionar outra instância de função de trabalho para tratar o aumento da carga][]


>[WACOM.NOTE] Na seção a seguir sobre a instalação do SDK, o link correto se você estiver usando o Visual Studio 2013 é [http://go.microsoft.com/fwlink/?LinkID=324322](http://go.microsoft.com/fwlink/?LinkID=324322). 


[WACOM.INCLUDE [install-sdk-2012-only](../includes/install-sdk-2012-only.md)]



<h2><a name="createWASA"></a><span class="short-header">Criar uma conta de Armazenamento</span>Criar uma conta de Armazenamento do Azure</h2>

Quando você executa o aplicativo de exemplo no Visual Studio, você pode acessar tabelas, filas e blobs no armazenamento de desenvolvimento do Azure ou em uma conta de Armazenamento do Azure na nuvem. O armazenamento de desenvolvimento usa um banco de dados SQL Server Express LocalDB para emular o funcionamento do Armazenamento do Azure na nuvem.  Neste tutorial você começará usando armazenamento de desenvolvimento e, em seguida, aprenderá como configurar o aplicativo para usar uma conta de armazenamento em nuvem quando ele é executado no Visual Studio. Nesta seção do tutorial, você cria a conta de Armazenamento do Azure onde você vai configurar o Visual Studio para usar posteriormente no tutorial.    

1. No navegador, abra o [Portal de Gerenciamento do Azure][NewPortal].

2. No [Portal de Gerenciamento do Azure][NewPortal], clique em **Armazenamento** e, em seguida, clique em **Novo**.

   ![Novo Armazenamento][mtas-portal-new-storage]

3. Clique em **Criação Rápida**.
   
   ![Criação Rápida][mtas-storage-quick]

4. Na caixa de entrada URL, digite um prefixo de URL. 

	Esse prefixo, além do texto que você vê sob a caixa será a URL exclusiva de sua conta de armazenamento. Se o prefixo inserido já tiver sido usado por outra pessoa, você verá a mensagem "O nome do armazenamento já está em uso" acima da caixa de texto e será necessário escolher outro prefixo.

5. Defina a região para a área onde você deseja implantar o aplicativo.

6. Defina a caixa suspensa**Replicação** como **Localmente redundante**. 

	Quando a replicação geográfica está habilitada para uma conta de armazenamento, o conteúdo armazenado é replicado para um local secundário para habilitar o failover para esse local no caso de um desastre maior no local principal. A replicação geográfica pode incorrer em custos adicionais. Para contas de teste e desenvolvimento, geralmente, você não deseja pagar pela replicação geográfica. Para obter mais informações, consulte [Como gerenciar contas de armazenamento][managestorage].

5. Clique em **Criar Conta de Armazenamento**. 

	Na imagem abaixo, uma conta de armazenamento é criada com a URL `aestest3.core.windows.net`.

	![criar um armazenamento com o prefixo da URL][mtas-create-storage-url-test]

	Esta etapa pode levar vários minutos para ser concluída. Enquanto aguarda, você pode repetir essas etapas e criar uma conta de armazenamento de produção. Muitas vezes é conveniente ter uma conta de armazenamento de teste para desenvolvimento local, outra conta de armazenamento de teste para testes no Azure e uma conta de armazenamento de produção.

5. Clique na conta de teste que você criou na etapa anterior e clique no ícone **Gerenciar Chaves de Acesso**.

	![Gerenciar Chaves][mtas-manage-keys]<br/>

	![GUID de chaves][mtas-guid-keys]<br/>

	Você precisará da tecla de acesso **Chave de Acesso Primária** ou da **Chave de Acesso Secundária** em todo este tutorial. Você pode usar qualquer uma dessas chaves em uma cadeia de conexão de armazenamento. 

	Existem duas chaves para que você possa alterar periodicamente a chave que você usa sem provocar uma interrupção no serviço em um aplicativo ativo. Você regenera a chave que não está usando e, em seguida, pode alterar a cadeia de conexão em seu aplicativo para usar a chave regenerada. Se houver apenas uma chave, o aplicativo poderá perder a conectividade com a conta de armazenamento quando você regenerar a chave. As chaves mostradas na imagem não são mais válidas porque foram regeneradas depois que a imagem foi capturada.

6. Copie uma dessas chaves na área de transferência para uso na próxima seção.





<h2><a name="installASE"></a><span class="short-header">Instalar o ASE</span>Instalar o Pesquisador de Armazenamento do Azure</h2>

O **Pesquisador de Armazenamento do Azure** (ASE) é uma ferramenta que você pode usar para consultar e atualizar tabelas, filas e blobs do Armazenamento do Azure. Você irá usá-lo durante estes tutoriais para verificar se os dados são atualizados corretamente e criar dados de teste.

1. Instalar o [Pesquisador de Armazenamento do Azure](http://azurestorageexplorer.codeplex.com/ ).

	As capturas de tela mostram o ASE 4, mas você pode instalar ASE 5 se preferir.

2. Inicie o **Pesquisador de Armazenamento do Azure** e clique em **Adicionar Conta**.

	![Adicionar conta do ASE][mtas-ase-add]

3. Digite o nome da conta de armazenamento de teste e cole a chave que você copiou anteriormente.

4. Clique em **Adicionar Conta de Armazenamento**.

	![Adicionar conta do ASE][mtas-ase-add2]

Também há outras ferramentas disponíveis que funcionam com o Armazenamento do Azure. Para obter mais informações, consulte [Pesquisadores de Armazenamento do Windows Azure (2014) (a página pode estar em inglês)](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx).




<h2><a name="createcloudsvc"></a><span class="short-header">Criar Serviço de Nuvem</span>Criar um Serviço de Nuvem</h2>

1. No navegador, abra o [Portal de Gerenciamento do Azure][NewPortal].

2. Clique em **Serviços de Nuvem** e, em seguida, clique no ícone **Novo**.

	![Nuvem rápida][mtas-new-cloud]

3. Clique em **Criação Rápida**.

4. Na caixa de entrada URL, digite um prefixo de URL. 

	Como a URL de armazenamento, essa URL deve ser exclusiva e você receberá uma mensagem de erro se o prefixo escolhido já estiver em uso por outra pessoa.

5. Defina a região para a área onde você deseja implantar o aplicativo.

	Você deve criar o serviço de nuvem na mesma região em que criou a conta de armazenamento. Quando a conta de armazenamento do serviço de nuvem estiver em outros datacenters (outras regiões), a latência será maior e você será cobrado pela largura de banda fora do data center. A largura de banda em um data center é gratuita.

	O grupos de afinidade do Azure fornecem um mecanismo para minimizar a distância entre os recursos em um data center, o que pode reduzir a latência. Este tutorial não usa grupos de afinidade. Para obter mais informações, consulte [Como criar um grupo de afinidade no Azure](http://msdn.microsoft.com/pt-br/library/jj156209.aspx).

6. Clique em **Criar Serviço de Nuvem**. 

	Na imagem a seguir, um serviço de nuvem é criado com a URL aescloud.cloudapp.net.

	![criar um armazenamento com o prefixo da URL][mtas-create-cloud]

	Você pode mover para a próxima etapa sem esperar que esta etapa seja concluída.





<h2><a name="downloadcnfg"></a><span class="short-header">Baixar e executar</span>Baixar e executar a solução concluída</h2>

1. Baixar e descompactar a [solução concluída (a página pode estar em inglês)](http://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36).

2. Inicie o Visual Studio com permissões elevadas.

	O emulador de computação que permite que o Visual Studio execute um projeto do Windows Azure localmente requer permissões elevadas.

	>[WACOM.NOTE] Com o SDK 2.3 e posterior, o emulador de computação padrão do Azure não requer permissões elevadas, mas este projeto ainda está configurado para usar o emulador de computação original, portanto ainda requer permissões elevadas.
	
3. Restaure os pacotes NuGet.

	Para manter o tamanho do download pequeno, a solução concluída é fornecida sem os assemblies ou outro conteúdo de pacotes NuGet instalados. Quando você abre e compila a solução, o NuGet obtém automaticamente todo o conteúdo do pacote. Para que isso funcione, você precisa habilitar a opção de restauração do pacote NuGet no Visual Studio. Se você ainda não ativou a restauração do pacote NuGet, execute as seguintes etapas.

	>[WACOM.NOTE] No Visual Studio 2013, a configuração padrão é permitir a restauração automática do pacote, mas como você está abrindo um projeto do Visual Studio 2012, a restauração não acontecerá automaticamente.  Espere até abrir a solução e, em seguida, abra a caixa de diálogo **Gerenciar Pacotes NuGet**, conforme as instruções a seguir. Você verá um botão**Restaurar** no canto superior direito: clique nesse botão para restaurar os pacotes. 

		1. No menu **Ferramentas**, clique no **Gerenciador de Pacotes de Biblioteca** e, em seguida, clique em **Gerenciar Pacotes NuGet para Solução**. 

		2. No canto inferior esquerdo da caixa de diálogo **Gerenciar Pacotes NuGet**, clique em **Configurações**.

		3. No painel esquerdo da caixa de diálogo **Opções**, selecione **Geral** em **Gerenciador de Pacotes**.

		4. Selecione **Permitir que o NuGet baixe pacotes ausentes durante a compilação**.

	![Habilitando a restauração do pacote do NuGet](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/NuGetPkgRestore.png)

3. No menu **Arquivo**, escolha **Abrir Projeto**, navegue até onde você baixou a solução e, em seguida, abra o arquivo de solução.

3. No **Gerenciador de Soluções**, verifique se **AzureEmailService** está selecionado como o projeto de inicialização.

1. Pressione CTRL+F5 para executar o aplicativo.

	>[WACOM.NOTE] Para habilitar a compilação da solução, você precisará adicionar referências aos assemblies atuais do SDK. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto MvcWebRole e clique em **Adicionar** -- **Referência**. Em **Assemblies**, clique em **Extensões**. Selecione *Microsoft.WindowsAzure.Diagnostics* e *Microsoft.WindowsAzure.ServiceRuntime* e, em seguida, clique em **OK**. Faça o mesmo para os projetos WorkerRoleA e WorkerRoleB.

	A home page do aplicativo é exibida em seu navegador.

	![Execute o aplicativo.][mtas-mailinglist1]

2. Clique em **Criar Novo**.

2. Insira alguns dados de teste e, em seguida, clique em **Criar**.

	![Execute o aplicativo.][mtas-create1]

3. Crie mais duas entradas na lista de endereçamento.

	![Página de índice da lista de endereçamento][mtas-mailing-list-index-page]

4. Clique em **Assinantes** e adicione alguns assinantes. Defina **Verified** como `true`.

	![Página do índice do assinante][mtas-subscribers-index-page]

4. Prepare-se para adicionar mensagens criando um arquivo *. txt* que contenha o corpo de um email que você deseja enviar. Em seguida, crie um arquivo *.htm* que contenha o mesmo texto, mas com algum HTML (por exemplo, tornar uma das palavras da mensagem em negrito ou itálico). Você usará esses arquivos na próxima etapa.

4. Clique em **Mensagens** e adicione algumas mensagens. Selecione os arquivos que você criou na etapa anterior. Não altere a data agendada cujo padrão é uma semana no futuro. O aplicativo não pode enviar mensagens até que você configure o SendGrid.

   ![Página de criação de mensagem][mtas-message-create-page]
	<br/><br/>
   ![Página de índice de mensagem][mtas-message-index-page]

Os dados que você inseriu e exibiu estão sendo armazenados no armazenamento de desenvolvimento do Azure. O armazenamento de desenvolvimento usa um banco de dados SQL Server Express LocalDB para emular o funcionamento do Armazenamento do Azure na nuvem.  O aplicativo está usando o armazenamento de desenvolvimento porque isso foi o que o projeto foi configurado para usar quando você o baixou. Essa configuração é armazenada em arquivos *.cscfg* no projeto **AzureEmailService**.  O arquivo *ServiceConfiguration.Local.cscfg* determina o que é usado quando você executa o aplicativo localmente no Visual Studio, e o arquivo *ServiceConfiguration.Cloud.cscfg* determina o que é usado quando você implanta o aplicativo na nuvem. Mais tarde, você verá como configurar o aplicativo para usar a conta de Armazenamento do Azure que você criou anteriormente.





<h2><a name="StorageExpVS"></a><span class="short-header">Armazenamento de desenvolvedor</span>Exibindo o armazenamento de desenvolvedor no Visual Studio</h2>

O navegador do **Armazenamento do Azure** no **Gerenciador de Servidores** fornece uma exibição somente leitura conveniente dos recursos do Armazenamento do Azure.

1. No menu **Exibir** no Visual Studio, selecione **Gerenciador de Servidores**.

2. Expanda o nó **(Desenvolvimento)** sob o nó **Armazenamento do Azure**.

	>[WACOM.NOTE] Com o SDK atual, expanda **Windows Azure** / **Armazenamento** / **(Desenvolvimento)**.

3. Expanda **Tabelas** para ver as tabelas que você criou nas etapas anteriores.

	![Gerenciador de Servidores][mtas-serverExplorer]

3. Clique duas vezes na tabela **MailingList**.

   ![Gerenciador de armazenamento do VS][mtas-wasVSdata]

	Observe como a janela mostra os diferentes esquemas na tabela. As entidades `MailingList` têm a propriedade `Description` e `FromEmailAddress`, e as entidades `Subscriber` têm a propriedade `Verified` (mais `SubscriberGUID`, que não é mostrada porque a imagem não é grande o suficiente). A tabela contém colunas para todas as propriedades e, se uma determinada linha da tabela for de uma entidade que não tenha um determinada propriedade, essa célula estará em branco.

Você não pode usar o navegador de armazenamento no Visual Studio para atualizar ou excluir recursos do Armazenamento do Azure. Você pode usar o [Gerenciador de Armazenamento do Azure](http://azurestorageexplorer.codeplex.com/ ) para atualizar ou excluir recursos de armazenamento de desenvolvimento. (Para configurar o Gerenciador de Armazenamento do Azure ou usar o armazenamento de desenvolvimento, clique na caixa de seleção **Armazenamento de Desenvolvedor** na caixa de diálogo **Adicionar Conta de Armazenamento**.)

>[WACOM.NOTE] Com o SDK mais recente, você pode atualizar o armazenamento de desenvolvimento no **Gerenciador de Servidores**. 



<h2><a name="conf4azureStorage"></a><span class="short-header">Usar sua conta de armazenamento</span>Configurar o aplicativo para usar sua conta de Armazenamento do Azure</h2>

Em seguida, você verá como configurar o aplicativo para que ele use sua conta de Armazenamento do Azure, quando for executado no Visual Studio, em vez do armazenamento de desenvolvimento. Há uma maneira mais nova para fazer isso no Visual Studio que foi introduzida na versão 1.8 do SDK, e uma maneira mais antiga que envolve copiar e colar configurações do Portal de Gerenciamento do Azure. As etapas a seguir mostram a maneira mais nova de definir configurações de conta de armazenamento.

4. No **Gerenciador de Soluções**, clique com o botão direito do mouse em **MvcWebRole** sob **Funções** no projeto **AzureEmailService** e clique em **Propriedades**.

	![Clicar com o botão direito do mouse em Propriedades][mtas-rt-prop]<br/>

5. Clique na guia **Configurações**. Na caixa suspensa **Configuração do Serviço**, selecione **Local**.

6. Selecione a entrada **StorageConnectionString**, e você verá um botão de reticências (**...**) na extremidade direita da linha. Clique no botão de reticências para abrir a caixa de diálogo **Cadeia de Conexão da Conta de Armazenamento**.

	![Clicar com o botão direito do mouse em Propriedades][mtas-elip]<br/>

7. Na caixa de diálogo **Criar Cadeia de Conexão de Armazenamento**, clique em **Sua assinatura** e, em seguida, clique no link **Baixar Configurações de Publicação**.

	>[WACOM.NOTE] Com o SDK mais recente, essa caixa de diálogo permite que você entre no Azure e simplesmente selecione a conta de armazenamento que deseja usar. 

	![Clicar com o botão direito do mouse em Propriedades][mtas-enter]<br/>

	O Visual Studio inicia uma nova instância de seu navegador padrão com a URL da página de configurações de publicação de download do portal do Azure. Se você não estiver conectado ao portal, o logon será solicitado. Depois de fazer logon, o navegador solicitará que você salve as configurações de publicação. Anote o local onde você salva as configurações.

   ![configurações de publicação][mtas-3]

1. Na caixa de diálogo **Criar Cadeia de Conexão de Armazenamento**, clique em **Importar** e, em seguida, navegue até o arquivo de configurações de publicação que você salvou na etapa anterior.

1. Selecione a assinatura e a conta de armazenamento que você deseja usar e clique em **OK**.

	![selecionar conta de armazenamento][mtas-5]

1. Siga o mesmo procedimento usado para a cadeia de conexão `StorageConnectionString` para definir a cadeia de conexão `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`.

	Não é necessário baixar o arquivo de configurações de publicação novamente. Quando você clicar nas reticências da cadeia de conexão `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`, descobrirá que a caixa de diálogo **Criar Cadeia de Conexão de Armazenamento** lembrará as informações de sua assinatura. Quando você clicar no botão de opção **Sua assinatura**, tudo o que você precisará fazer é selecionar a mesma **Assinatura** e **Nome da Conta** que selecionou anteriormente e clicar em **OK**. 

2. Siga o mesmo procedimento usado para as duas cadeias de conexão para a função MvcWebRole para definir as cadeias de conexão para as funções WorkerRoleA e workerRoleB.

### Método manual para configurar as credenciais da conta de armazenamento

O procedimento a seguir mostra como definir configurações de conta de armazenamento manualmente. Se você usou o método automático mostrado no procedimento anterior, que você poderá ignorar este procedimento ou pode ler todo ele para ver o que o método automático fez para você nos bastidores.

1. No navegador, abra o [Portal de Gerenciamento do Azure][NewPortal].

2. Clique na guia **Armazenamento** e clique na conta de teste que você criou na etapa anterior e, em seguida, clique no ícone **Gerenciar Chaves**.

   ![Gerenciar Chaves][mtas-manage-keys]<br/>

   ![GUID de chaves][mtas-guid-keys]<br/>

3. Copie a chave de acesso primária ou secundária.


4. No **Gerenciador de Soluções**, clique com o botão direito do mouse em **MvcWebRole** sob **Funções** no projeto **AzureEmailService** e clique em **Propriedades**.

   ![Clicar com o botão direito do mouse em Propriedades][mtas-rt-prop]<br/>

5. Clique na guia **Configurações**. Na caixa suspensa **Configuração do Serviço**, selecione **Local**.

6. Selecione a entrada **StorageConnectionString**, e você verá um botão de reticências (**...**) na extremidade direita da linha. Clique no botão de reticências para abrir a caixa de diálogo **Cadeia de Conexão da Conta de Armazenamento**.

   ![Clicar com o botão direito do mouse em Propriedades][mtas-elip]<br/>

7. Na caixa de diálogo **Criar Cadeia de Conexão de Armazenamento**, selecione o botão de opção **Credenciais inseridas manualmente**. Digite o nome da sua conta de armazenamento e a chave de acesso primário ou secundário copiada do portal. 

7. Clique em **OK**.

Você pode usar o mesmo procedimento para definir as configurações para as funções de trabalho ou pode propagar as configurações de função web para as funções de trabalho editando o arquivo de configuração. As etapas a seguir explicam como editar o arquivo de configuração. (Isso ainda faz parte do método manual para configurar credenciais de armazenamento, você não precisa fazer isso se já tiver propagado as configurações para as funções de trabalho usando o método automático.)

8. Abra o arquivo **ServiceConfiguration.Local.cscfg** localizado no projeto **AzureEmailService**.

	No elemento `Role` de `MvcWebRole`, você verá um elemento `ConfigurationSettings` com as configurações que você atualizou usando a interface do usuário do Visual Studio.

		  <Role name="MvcWebRole">
		    <Instances count="1" />
		    <ConfigurationSettings>
		      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[Key]" />
		      <Setting name="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=aestest;AccountKey=[Key]" />
		    </ConfigurationSettings>
		  </Role>
		
	Nos elementos `Role` das duas funções de trabalho, você verá as mesmas duas cadeias de conexão.

9. Exclua os elementos `Setting` dessas duas cadeias de conexão dos elementos `WorkerRoleA` e `WorkerRoleB` e, em seguida, copie e cole em seu lugar os elementos `Setting` do elemento `MvcWebRole`.

Para obter mais informações sobre os arquivos de configuração, consulte [Configurando um projeto do Azure (a página pode estar em inglês)](http://msdn.microsoft.com/pt-br/library/windowsazure/ee405486.aspx)

### Testar o aplicativo configurado para usar sua conta de armazenamento

9. Pressione CTRL+F5 para executar o aplicativo. Insira alguns dados clicando nos links **Mailing Lists**, **Subscribers** e **Messages** como você fez anteriormente neste tutorial.

Agora você pode usar o **Gerenciador de Armazenamento do Azure** ou o **Gerenciador de Servidores** para exibir os dados que o aplicativo inseriu nas tabelas do Azure.

### Usar o Gerenciador de Armazenamento do Azure para exibir os dados inseridos em sua conta de armazenamento

10. Abra o **Pesquisador de Armazenamento do Azure**.

11. Selecione a conta de armazenamento da qual você inseriu as credenciais anteriormente.

12. Em **Tipo de Armazenamento**, selecione **Tabelas**.

12. Selecione a tabela `MailingList` e, em seguida, clique em **Consulta** para ver os dados que você inseriu nas páginas **Mailing List** e **Assinante** do aplicativo.

   ![ASE][mtas-ase1]<br/>

### Usar o Gerenciador de Servidores para exibir os dados inseridos em sua conta de armazenamento

11. No **Gerenciador de Servidores** (ou no **Gerenciador de Banco de Dados**), clique com o botão direito do mouse em **Armazenamento do Azure** e clique em **Adicionar Nova Conta de Armazenamento**.

12. Siga o mesmo procedimento que você usou anteriormente para configurar suas credenciais de conta de armazenamento.

13. Expanda o novo nó sob **Armazenamento do Azure** para exibir os dados armazenados em sua conta de Armazenamento do Azure.

	![ASE][mtas-se3]<br/>

### Etapas opcionais para desabilitar a inicialização automática do Emulador de Armazenamento do Azure 

Se não estiver usando o emulador de armazenamento, você poderá reduzir o tempo de inicialização do projeto e usar menos recursos locais desabilitando a inicialização automática do emulador do Armazenamento do Azure.

11. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto em nuvem **AzureEmailService** e selecione **Propriedades**.

	![Selecionando as propriedades do projeto em nuvem][mtas-aesp]<br/>

11. Selecione a guia **Desenvolvimento**.

12. Defina **Iniciar o emulador de Armazenamento do Azure** como **Falso**.

	![Desabilitando a inicialização automática do emulador de armazenamento][mtas-1]<br/>

	**Observação**: você deve definir isso como false apenas se não estiver usando o emulador de armazenamento. 

	Essa janela também fornece uma maneira de alterar o arquivo de **Configuração do Serviço**, que é usado quando você executa o aplicativo localmente, de **Local** para **Nuvem** (de *ServiceConfiguration.Local.cscfg* para *ServiceConfiguration.Cloud.cscfg*.

13. Na bandeja do sistema do Windows, clique com o botão direito do mouse no ícone do emulador de computação e clique em **Desligar o Emulador de Armazenamento**.

	![ASE][mtas-se4]<br/>





<h2><a name="sendGrid"></a><span class="short-header">SendGrid</span>Configurar o aplicativo para usar o SendGrid</h2>

O aplicativo de exemplo usa o SendGrid para enviar emails.  Para enviar emails usando o SendGrid, você precisa configurar uma conta do SendGrid e, em seguida, atualizar um arquivo de configuração com as suas credenciais do SendGrid.

<div class="note"><p><strong>Observação:</strong> se não desejar usar o SendGrid ou não puder usar o SendGrid, você poderá substituir facilmente seu próprio serviço de email. O código que usa o SendGrid é isolado em dois métodos na função de trabalho B. [O Tutorial 5][tut5] explica o que você precisa alterar para implementar outro método de envio de emails. Se desejar fazer isso, você poderá ignorar este procedimento e continuar com este tutorial. Todo o restante do aplicativo funcionará (páginas da web, planejamento de email etc.), exceto o envio real de emails.</p></div>

### Criar uma conta de SendGrid
 
1. Siga as instruções em [Como enviar email usando o SendGrid com o Azure (a página pode estar em inglês)](http://www.windowsazure.com/pt-br/develop/net/how-to-guides/sendgrid-email-service/ "SendGrid") para inscrever-se para uma conta gratuita.

### Atualizar as credenciais de SendGrid nas propriedades da função de trabalho
 
No início do tutorial, quando definiu as credenciais da conta de armazenamento para a função web e as duas funções de trabalho, você talvez tenha observado que a função de trabalho B tinha três configurações que não estavam na função web ou na função de trabalho A. Você pode usar essa mesma interface do usuário agora para configurar essas três configurações (selecione **Nuvem** na lista suspensa **Configuração do Serviço**).

As etapas a seguir mostram um método alternativo para definir as propriedades editando o arquivo de configuração.

2. Edite o arquivo *ServiceConfiguration.Cloud.cscfg* no projeto `AzureEmailService` e insira os valores do nome e da senha do usuário do SendGrid que você obteve na etapa anterior para o elemento `WorkerRoleB` que possui essas configurações. O código a seguir mostra o elemento WorkerRoleB.

	![SendGridSettings][mtas-sg]<br/>

3. Também há uma configuração de AzureMailServiceURL. Defina esse valor como a URL que você selecionou quando criou seu Serviço de Nuvem do Azure, por exemplo: "http://aescloud.cloudapp.net".

Atualizando o arquivo de configuração de nuvem, você está definindo as configurações que serão usadas quando o aplicativo for executado na nuvem. Se você quiser que o aplicativo envie emails enquanto é executado localmente, você também precisará atualizar o arquivo *ServiceConfiguration.Local.cscfg*.




<h2><a name="deployAz"></a><span class="short-header">Implantar no Azure</span>Implantar o aplicativo no Azure</h2>

Para implantar o aplicativo, você pode criar um pacote no Visual Studio e carregá-lo usando o Portal de Gerenciamento do Azure ou publicá-lo diretamente no Visual Studio. Neste tutorial, você usará o método publish.

Você publicará o aplicativo primeiro no ambiente de preparo e mais tarde irá promover a implantação de preparo para a produção.

### Implementar restrições de IP

Quando você implanta na preparação, o aplicativo poderá ser publicamente acessível a qualquer pessoa que conheça a URL. Portanto, a primeira etapa é implementar as restrições de IP para garantir que nenhuma pessoa não autorizada possa usá-lo. Em um aplicativo de produção você implementaria um mecanismo de autenticação e autorização, como o sistema de associação ASP.NET, mas essas funções foram omitidas do aplicativo de exemplo para simplificar sua configuração, implantação e teste.

1. Abra o arquivo *Web.Release.config* localizado na pasta raiz do projeto `MvcWebRole` e substitua o valor do atributo **ipAddress** 127.0.0.1 pelo seu endereço IP. Para ver o arquivo **Web.Release.config** no **Gerenciador de Soluções** é necessário expandir o arquivo *Web. config*. 

	Você pode localizar seu endereço IP procurando por "Localizar meu IP" com o [Bing](http://www.bing.com/search?q=find+my+IP&qs=n&form=QBLH&pq=find+my+ip&sc=8-10&sp=-1&sk= "find my IP") ou outro mecanismo de pesquisa. 

	Quando o aplicativo é publicado, as transformações especificadas no arquivo *Web.release.config* são aplicadas, e os elementos de restrição IP são atualizados no arquivo *web. config* implantado na nuvem. Você pode exibir o arquivo *web. config* transformado na pasta *AzureEmailService\MvcWebRole\obj\Release\TransformWebConfig\transformed* após a criação do pacote.

### Configurar o aplicativo para usar sua conta de armazenamento quando executado na nuvem

No início do tutorial, quando você definiu as credenciais de conta de armazenamento para a função web e as duas funções de trabalho, você definiu as credenciais a serem usadas ao executar o aplicativo localmente. Agora você precisa definir as credenciais da conta de armazenamento a serem usadas ao executar o aplicativo na nuvem.

Para essa execução de teste, você usará as mesmas credenciais para a nuvem que usou para executar localmente. Se estiver implantando um aplicativo de produção, você normalmente usa uma conta de produção diferente da conta usada para teste. Uma prática recomendada para a produção também é usar uma conta para a connectionString de diagnóstico diferente da cadeia de conexão de armazenamento, mas para esta execução de teste, você usará a mesma conta.

Você pode usar a mesma interface de usuário para configurar as cadeias de conexão (apenas certifique-se de selecionar **Nuvem** na lista suspensa **Configuração do Serviço**. Como alternativa, você pode editar o arquivo de configuração, conforme explicado nas etapas a seguir.

1. Abra o arquivo *ServiceConfiguration.Local.cscfg* no projeto **AzureEmailService** e copie os elementos `Setting` para `StorageConnectionString` e `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`.  

2. Abra o arquivo *ServiceConfiguration.Cloud.cscfg* no projeto **AzureEmailService** e cole os elementos copiados no elemento `Configuration Settings` para `MvcWebRole`, `'WorkerRoleA` e `WorkerRoleB` substituindo os elementos `Setting` que já estavam lá.

2. Verifique se todos os elementos da função web e das duas funções de trabalho definem as mesmas cadeias de conexão.

### Publicar o aplicativo

2. Se o Visual Studio ainda não estiver aberto, abra-o como administrador e abra a solução **AzureEmailService**.

3.	Clique com o botão direito do mouse no projeto de nuvem **AzureEmailService** e selecione **Publicar**.

	![Pacote][mtas-6]

	A caixa de diálogo **Publicar aplicativo do Azure** é exibida. 

	![Pacote de nuvem][mtas-16]

4. Se você usou o método automático para importar as credenciais da conta de armazenamento anteriormente, sua assinatura do Azure estará na lista suspensa e você poderá selecioná-la e clicar em **Avançar**. Caso contrário, clique em **Entrar para baixar credenciais** e siga as instruções em [Configurar o aplicativo para o Armazenamento do Azure][] para baixar e importar as configurações de publicação.

1. Na guia **Configurações Comuns**, verifique a configuração na lista suspensa **Serviço de Nuvem**.

2. Na lista suspensa **Ambiente**, altere **Produção** para **Preparo**.

	![Painel][mtas-7]

3. Mantenha a configuração padrão **Versão** para **Configuração da Compilação** e **Nuvem** para **Configuração do serviço**.

	As configurações padrão na guia **Avançado** são adequadas para este tutorial. Na guia **Avançado** há algumas configurações que são úteis para desenvolvimento e teste. Para obter mais informações sobre a guia Avançado, consulte [Assistente de publicação de aplicativo do Azure (a página pode estar em inglês)](http://msdn.microsoft.com/library/windowsazure/hh535756.aspx "pub wiz").
   
1. Clique em **Avançar**.

1. Na etapa **Resumo** do assistente, clique no ícone **Salvar** (o ícone de disquete mostrado à direita da lista suspensa Perfil de destino) para salvar as configurações de publicação. 

	Na próxima vez que você publicar o aplicativo, as configurações salvas serão usadas e você não precisará percorrer o assistente de publicação novamente. 

1. Examine as configurações e clique em **Publicar**.

	![pub][mtas-8]

   A janela **Log de atividades do Azure** é aberta no Visual Studio. 

2. Clique no ícone de seta para a direita para expandir os detalhes da implantação.

	![pub][mtas-11]
	<br/><br/>
	![pub][mtas-9]

	A implantação pode levar cerca de cinco minutos ou mais para ser concluída.

1. Quando o status da implantação for concluído, clique na **URL do Site** para iniciar o aplicativo.

	![Painel][mtas-c55]

9. Insira alguns dados nas páginas da web **Mailing List**, **Subscriber** e **Message** para testar o aplicativo.

	**Observação**: exclua o aplicativo depois de concluir o teste para evitar pagar por recursos que você não está usando. Se você estiver usando uma [Conta de avaliação gratuita do Azure](http://www.windowsazure.com/pt-br/pricing/free-trial/ "free-trial account"), as três funções implantadas usarão o limite mensal em duas semanas. 	Para excluir uma implantação usando o Portal de Gerenciamento do Azure, selecione o serviço de nuvem e clique **EXCLUIR** na parte inferior da página e, em seguida, selecione a implantação de produção ou de preparo.

	![pub][mtas-19]

1. No Log de Atividades do Azure no Visual Studio, selecione **Abrir no Gerenciador de Servidores**.

	Em **Computação do Azure** no **Gerenciador de Servidores**, você pode monitorar a implantação. Se tiver selecionado **Habilitar Área de Trabalho para todas as funções** no assistente **Publicar Aplicativo do Azure**, você poderá clicar com o botão direito do mouse em uma instância da função e selecionar **Conectar Usando Área de Trabalho Remota**. 

	![pub][mtas-12]


<h2><a name="swap"></a><span class="short-header">Produção</span>Promover o aplicativo do preparo para a produção</h2>

1. No [Portal de Gerenciamento do Azure][NewPortal], clique no ícone **Serviços de Nuvem** no painel esquerdo e selecione o seu serviço de nuvem.

2. Clique em **Permutar**.

2. Clique em **Sim** para concluir a permuta VIP (IP virtual). Esta etapa pode levar vários minutos para ser concluída.

	![Painel][mtas-c6]

3. Quando a permuta for concluída, clique no ícone **Serviços de Nuvem** no painel esquerdo e selecione o seu serviço de nuvem.

4. Role a guia **Painel** para baixo para a implantação **Produção** até a seção **Visão Rápida** na parte inferior direita da página. Observe que a **URL do Site** foi alterada de um prefixo GUID para o nome do seu serviço de nuvem. 

	![Painel][mtas-c7]

5. Clique no link em **URL do Site** ou copie e cole-o em um navegador para testar o aplicativo em produção. 

	Se você não tiver alterado as configurações da conta de armazenamento, os dados inseridos durante o teste da versão de preparação do aplicativo serão mostrados quando você executar o aplicativo na nuvem.




<h2><a name="trace"></a><span class="short-header">Rastreamento</span>Configurar e exibir os dados de rastreamento</h2>

O rastreamento é uma ferramenta valiosa para a depuração de um aplicativo de nuvem. Nesta seção do tutorial, você verá como exibir dados de rastreamento.

1. Verifique se a cadeia de conexão de diagnóstico está configurada para usar sua conta de Armazenamento do Azure e não o armazenamento de desenvolvimento. 

	Se você seguiu as instruções anteriormente no tutorial, elas serão as mesmas. Você pode verificar se elas são as mesmas usando a interface do usuário do Visual Studio (a guia **Configurações** nas **Propriedades** das funções) ou examinando os arquivos *ServiceConfiguration.\*.cscfg*.
 
   **Observação:** uma prática recomendada é usar uma conta de armazenamento diferente para dados de rastreamento em vez da conta de armazenamento usada para dados de produção, mas para simplificar este tutorial você configurou a mesma conta para rastreamento.

1. No Visual Studio, abra *WorkerRoleA.cs* no projeto **WorkerRoleA**, procure por `ConfigureDiagnostics` e examine o método `ConfigureDiagnostics`. 

        private void ConfigureDiagnostics()
        {
            DiagnosticMonitorConfiguration config = DiagnosticMonitor.GetDefaultInitialConfiguration();
            config.ConfigurationChangePollInterval = TimeSpan.FromMinutes(1d);
            config.Logs.BufferQuotaInMB = 500;
            config.Logs.ScheduledTransferLogLevelFilter = LogLevel.Verbose;
            config.Logs.ScheduledTransferPeriod = TimeSpan.FromMinutes(1d);

            DiagnosticMonitor.Start(
                "Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString",
                config);
        }

	Nesse código, o `DiagnosticMonitor` está configurado para armazenar até 500 MB de informações de rastreamento (depois de 500 MB, os dados mais antigos são substituídos) e para armazenar todas as mensagens de rastreamento (LogLevel.Verbose). O `ScheduledTransferPeriod` transfere os dados de rastreamento para o armazenamento a cada minuto. Você deve definir o `ScheduledTransferPeriod` para salvar os dados de rastreamento. 

	O método `ConfigureDiagnostics` em cada uma das funções web e de trabalho configura o ouvinte de rastreamento para registrar dados quando você chama a API de rastreamento. Para obter mais informações, consulte [Usando rastreamento em aplicativos de nuvem do Windows Azure (a página pode estar em inglês)](http://blogs.msdn.com/b/windowsazure/archive/2012/10/24/using-trace-in-windows-azure-cloud-applications-1.aspx "Using Trace in Windows Azure")

1. No **Gerenciador de Servidores**, clique duas vezes em **WADLogsTable** (expanda **Armazenamento** / **yourstorageaccountname** / **Tables**) da conta de armazenamento que você adicionou anteriormente. Você pode inserir um [Filtro do WCF Data Services (a página pode estar em inglês)](http://msdn.microsoft.com/pt-br/library/windowsazure/ff683669.aspx "WCF filter") para limitar as entidades exibidas. Na imagem a seguir, somente mensagens de erro e de aviso são exibidas.

	![Painel][mtas-trc]





<h2><a name="addRole"></a><span class="short-header">Adicionar uma instância de função</span>Adicionar outra instância de função de trabalho para tratar o aumento da carga</h2>

Há duas abordagens para dimensionar recursos de computação em funções do Azure, especificando o [tamanho da máquina virtual (a página pode estar em inglês)](http://msdn.microsoft.com/pt-br/library/windowsazure/ee814754.aspx "VM sizes") e/ou especificando a contagem de instâncias de máquinas virtuais em execução. 

O tamanho da VM (máquina virtual) é especificado no atributo `vmsize` do elemento `WebRole` ou `WorkerRole` no arquivo *ServiceDefinition.csdef*. A configuração padrão é `Pequeno`, que fornece um núcleo e 1,75 GB de RAM. Para aplicativos com multithread que usam muita memória, disco e largura de banda, você pode aumentar o tamanho da VM para melhorar o desempenho. Por exemplo, uma VM `Extragrande` tem oito núcleos de CPU e 14 GB de RAM. O aumento da memória, dos núcleos de cpu, do disco e da largura de banda em um único computador é conhecido como *escalar verticalmente*. Bons candidatos para a escala vertical incluem aplicativos web ASP.NET que usam [métodos assíncronos (a página pode estar em inglês)](http://www.asp.net/mvc/tutorials/mvc-4/using-asynchronous-methods-in-aspnet-mvc-4 "Async MVC") Consulte [Tamanhos de máquina virtual (a página pode estar em inglês)](http://msdn.microsoft.com/pt-br/library/windowsazure/ee814754.aspx "VM sizes") para obter uma descrição dos recursos fornecidos por cada tamanho de VM.

A função de trabalho B neste aplicativo é o componente limitante sob carga alta porque faz o trabalho de envio de emails. (A função de trabalho A apenas cria mensagens da fila, que não é um uso intensivo de recursos.) Como a função de trabalho B não é multithread e não tem uma superfície de memória grande, ela não é uma boa candidata para escala vertical. A função de trabalho B pode ser dimensionada linearmente (isto é, quase o dobro do desempenho quando você dobra as instâncias) aumentando a contagem de instâncias. O aumento do número de instâncias de computação é conhecido como *escalar horizontalmente*. Há um custo para cada instância, você deve expandir apenas quando seu aplicativo exigir uma escala horizontal. 

Você pode escalar horizontalmente uma função de trabalho ou web, atualizando a configuração na interface do usuário do Visual Studio ou editando os arquivos *ServiceConfiguration.\*.cscfg* diretamente. A contagem de instâncias é especificada na guia **Configuração** da janela **Propriedades** da função e no elemento `Instances` nos arquivos *.cscfg*. Quando você atualiza a configuração, você precisa implantar o arquivo de configuração atualizado para que a alteração entre em vigor. Como alternativa, para aumentos de carga transitórios, você pode alterar o número de instâncias de função no Portal de Gerenciamento do Azure. Você também pode configurar o número de instâncias usando a API de Gerenciamento do Azure. Finalmente, você pode usar o [Autoscaling Application Block][autoscalingappblock] para escalar horizontalmente de maneira automática para atender ao aumento da carga. Para obter mais informações sobre dimensionamento automático, consulte os links no final do [último tutorial desta série][tut5].

Nesta seção do tutorial você irá escalar horizontalmente a função de trabalho B usando o portal de gerenciamento, mas primeiro você verá como isso é feito no Visual Studio.

Para fazer isso no Visual Studio, você clica com o botão direito do mouse em **Funções** no projeto de nuvem e seleciona **Propriedades**.

   ![Clicar com o botão direito do mouse em Propriedades][mtas-rt-prop]
 
Em seguida, você selecione a guia **Configuração** à esquerda e seleciona **Nuvem** no menu suspenso **Configuração do Serviço**. 

  ![Contagem de Instâncias][mtas-instanceCnt]

Observe que você também pode configurar o tamanho da VM nesta guia.

As etapas a seguir explicam como escalar horizontalmente usando o Portal de Gerenciamento do Azure.

1. No Portal de Gerenciamento do Azure, selecione seu serviço de nuvem e clique em **Dimensionar**.

1. Aumente o número de instâncias da função de trabalho B e, em seguida, clique em **Salvar**.

	![aumentar instâncias][mtas-in3]

	Pode levar alguns minutos para que as novas VMs sejam provisionadas.

1. Selecione a guia **Instâncias** para ver cada instância de função em seu aplicativo.

	![exibir instâncias][mtas-in2]




<h2><a name="nextsteps"></a><span class="short-header">Próximas etapas</span>Próximas etapas</h2>

Agora você já viu como configurar, implantar e dimensionar o aplicativo concluído. Os tutoriais seguintes mostram como criar o aplicativo desde o início. No [próximo tutorial][tut3] você criará a função web.

Para obter links para recursos adicionais para trabalhar com tabelas, filas e blobs do Armazenamento do Azure, consulte o final do [último tutorial desta série][tut5].



<div><a href="/pt-br/develop/net/tutorials/multi-tier-web-site/3-web-role/" class="site-arrowboxcta download-cta">Tutorial 3</a></div>

[Configurar o ambiente de desenvolvimento]: #setupdevenv
[Configurar uma conta gratuita do Azure]: #setupwindowsazure
[Criar uma conta de Armazenamento do Azure]: #createWASA
[Instalar o Pesquisador de Armazenamento do Azure]: #installASE
[Criar um Serviço de Nuvem]: #createcloudsvc
[Baixar e executar a solução completa]: #downloadcnfg
[Exibir o armazenamento de desenvolvedor no Visual Studio]: #StorageExpVS
[Configurar o aplicativo para o Armazenamento do Azure]: #conf4azureStorage
[Implantar o aplicativo no Azure]: #deployAz
[Promover o aplicativo do preparo para a produção]: #swap
[Configurar o aplicativo para usar o SendGrid]: #sendGrid
[Configurar e exibir dados de rastreamento]: #trace
[Adicionar outra instância de função de trabalho para tratar o aumento da carga]: #addRole

[firsttutorial]: /pt-br/develop/net/tutorials/multi-tier-web-site/1-overview/

[tut3]: /pt-br/develop/net/tutorials/multi-tier-web-site/3-web-role/
[tut5]: /pt-br/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/
[NewPortal]: http://manage.windowsazure.com
[managestorage]: /pt-br/manage/services/storage/how-to-manage-a-storage-account/
[autoscalingappblock]: /pt-br/develop/net/how-to-guides/autoscaling/


[mtas-portal-new-storage]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-portal-new-storage.png
[mtas-storage-quick]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-storage-quick.png
[mtas-create-storage-url-test]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create-storage-url-test.png
[mtas-manage-keys]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-manage-keys.png
[mtas-guid-keys]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-guid-keys.PNG
[mtas-new-cloud]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-new-cloud.png
[mtas-create-cloud]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create-cloud.png
[mtas-ase-add]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-ase-add.png
[mtas-ase-add2]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-ase-add2.png

[mtas-rt-prop]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-rt-prop.png
[mtas-mailinglist1]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-mailinglist1.png
[mtas-create1]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create1.png
[mtas-mailing-list-index-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-mailing-list-index-page.png
[mtas-subscribers-index-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-subscribers-index-page.png
[mtas-message-create-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-message-create-page.png
[mtas-message-index-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-message-index-page.png
[mtas-serverExplorer]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-serverExplorer.png
[mtas-wasVSdata]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-wasVSdata.png
[mtas-elip]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-elip.png
[mtas-enter]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-enter.png
[mtas-ase1]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-ase1.png


[mtas-se3]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-se3.png
[mtas-aesp]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-aesp.png
[mtas-1]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-1.png
[mtas-se4]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-se4.png









[mtas-c6]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c6.png
[mtas-c7]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c7.png

[mtas-sg]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-sg.png
[mtas-trc]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-trc.png
[mtas-instanceCnt]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-instanceCnt.png

[mtas-in3]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-in3.png
[mtas-in2]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-in2.png
[mtas-3]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-3.png
[mtas-5]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-5.png
[mtas-6]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-6.png
[mtas-16]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-16.png
[mtas-7]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-7.png
[mtas-8]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-8.png
[mtas-9]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-9.png
[mtas-11]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-11.png
[mtas-12]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-12.png
[mtas-c55]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c55.png

[mtas-19]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-19.png














