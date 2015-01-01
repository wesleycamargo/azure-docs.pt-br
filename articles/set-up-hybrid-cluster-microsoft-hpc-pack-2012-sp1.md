<properties pageTitle="Configurar um cluster de computação híbrido com o Microsoft HPC Pack" metaKeywords="" description="Learn how to use Microsoft HPC Pack and Azure to set up a small, hybrid high performance computing (HPC) cluster" metaCanonical="" services="" documentationCenter="" title="Set up a Hybrid Cluster with Microsoft HPC Pack" authors="danlep" solutions="" manager="timlt" editor="mattshel" />

<tags ms.service="cloud-services" ms.workload="big-compute" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/16/2014" ms.author="danlep" />


#Configurar um cluster de computação híbrido com o Microsoft HPC Pack
Este tutorial lhe mostra como usar o Microsoft HPC Pack 2012 R2 e o Azure para instalar um pequeno cluster híbrido de computação de alto desempenho (HPC). O cluster consistirá em um nó de cabeçalho local (um computador que execute o sistema operacional Windows Server e o HPC Pack) e alguns nós de computação implantados sob demanda como instâncias de função de trabalho em um serviço de nuvem do Azure. É possível executar trabalhos de computação no cluster híbrido.
 
![Hybrid HPC cluster][Overview] 

Este tutorial mostra uma abordagem, às vezes chamada de cluster "de estouro para a nuvem", para usar os recursos de computação dimensionáveis e sob demanda no Azure para executar aplicativos com uso intensivo de computação.

Este tutorial não pressupõe nenhuma experiência anterior com clusters de computação ou com o HPC Pack. Ele destina-se somente para ajudá-lo a implantar um cluster híbrido de computação de forma rápida para fins de demonstração. Para considerações e etapas de como implantar um cluster híbrido do HPC Pack em uma escala maior em um ambiente de produção, consulte a [orientação detalhada](http://go.microsoft.com/fwlink/p/?LinkID=200493). Se você quiser criar um cluster inteiro de HPC Pack no Azure, consulte [Microsoft HPC Pack em VMs do Azure](http://go.microsoft.com/fwlink/p/?linkid=330375). 

>[WACOM.NOTE] O Azure oferece uma[variedade de tamanhos](http://go.microsoft.com/fwlink/p/?LinkId=389844) para os seus recursos de computação, adequados para diferentes cargas de trabalho. Por exemplo, as instâncias A8 e A9 combinam alto desempenho e acesso a uma rede de aplicativos com alta taxa de transferência e baixa latência, necessários para alguns aplicativos de HPC. Para obter mais informações, consulte [Sobre as instâncias A8 e A9 com uso intensivo de computação](http://go.microsoft.com/fwlink/p/?Linkid=328042). 

Este tutorial apresenta e explica as seguintes etapas básicas:

* [Pré-requisitos](#BKMK_Prereq)
* [Instalar o HPC Pack no nó de cabeçalho](#BKMK_DeployHN)
* [Preparar a assinatura do Azure](#BKMK_Prpare)
* [Configurar o nó de cabeçalho](#BKMK_ConfigHN)
* [Adicionar nós do Azure ao cluster](#BKMK_worker)
* [Iniciar os nós do Azure](#BKMK_start)
* [Executar um comando no cluster](#BKMK_RunCommand)
* [Executar um trabalho de teste](#BKMK_RunJob)
* [Parar os nós do Azure](#BKMK_stop)

<h2 id="BKMK_Prereq">Pré-requisitos</h2>

>[WACOM.NOTE]Para concluir este tutorial, você precisa de uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Criar uma conta do Azure](http://www.windowsazure.com/pt-br/develop/php/tutorials/create-a-windows-azure-account/).

Além disso, é necessário o seguinte para este tutorial.

* Um computador local que esteja executando uma edição do Windows Server 2012 R2 ou Windows Server 2012. Este computador será o nó de cabeçalho do cluster HPC. Se não estiver executando o Windows Server, poderá baixar e instalar uma [versão de avaliação](http://technet.microsoft.com/evalcenter/dn205286.aspx).

	* O computador deve estar associado a um domínio do Active Directory.

	* Certifique-se de que nenhuma função de servidor ou serviço de função adicionais estejam instalados.

	* Para dar suporte ao HPC Pack, o sistema operacional deve ser instalado em um desses idiomas: Inglês, japonês ou chinês (simplificado).

	* Verifique se as atualizações importantes e críticas foram instaladas.
	
* Arquivos de instalação para o HPC Pack 2012 R2, que está disponível gratuitamente. A versão mais recente é HPC Pack 2012 R2 Update 1. [Baixar](http://go.microsoft.com/fwlink/p/?linkid=328024) a instalação completa do pacote e copie os arquivos para o computador do nó de cabeçalho ou em um local de rede. Escolha os arquivos de instalação no mesmo idioma da sua instalação do Windows Server.

* Uma conta de domínio com permissões de Administrador local no nó de cabeçalho.

* Conectividade TCP na porta 443 do nó de cabeçalho para o Azure.

<h2 id="BKMK_DeployHN">Instalar o HPC Pack no nó de cabeçalho</h2>

Primeiro instale o Microsoft HPC Pack em um computador local executando o Windows Server que será o nó de cabeçalho do cluster.

1. Faça logon no nó de cabeçalho usando uma conta de domínio que tenha permissões de Administrador local.

2. Inicie o Assistente de Instalação do HPC Pack executando Setup.exe nos arquivos de instalação do HPC Pack.

3. Na tela**Instalação do HPC Pack 2012 R2** clique em **Nova instalação ou adicionar novos recursos a uma instalação existente**.

	![HPC Pack 2012 Setup][install_hpc1]

4. Na **página Contrato de Usuário de Software da Microsoft**, clique em**Avançar**.

5. Na página **Selecionar Tipo de instalação** clique em **Criar um novo cluster de HPC, criando um nó de cabeçalho**, e clique em **Avançar**.

	![Select Installation Type][install_hpc2]

6. O assistente executa vários testes de pré-instalação.  Clique em**Avançar** na página **Regras de Instalação** se todos os testes forem bem-sucedidos. Caso contrário, examine as informações fornecidas e faça as alterações necessárias em seu ambiente.Execute os testes novamente ou se necessário inicie o Assistente de Instalação novamente. 

	![Installation Rules][install_hpc3]

7. Na página **Configuração do BD de HPC** verifique se **Nó de Cabeçalho** está selecionado para todos os bancos de dados de HPC e, em seguida, clique em **Avançar**.

	![DB Configuration][install_hpc4]

8. Aceite as seleções padrão nas páginas restantes do assistente. Na página **Instalar Componentes necessários, ** clique em **Instalar**.

	![Install][install_hpc6]

9. Após a conclusão da instalação, desmarque **Iniciar o Gerenciador de Cluster de HPC** e, em seguida, clique em **Concluir**.(Você iniciará o Gerenciador de Cluster de HPC em uma etapa posterior ao concluir a configuração do nó de cabeçalho).

	![Finish][install_hpc7]

<h2 id="BKMK_Prepare">Preparar a assinatura do Azure</h2>
Use o [Portal de Gerenciamento](https://manage.windowsazure.com) para realizar as etapas a seguir com sua assinatura do Azure. Elas são necessárias para implantar posteriormente os nós do Azure no nó de cabeçalho local. 

- Carregue um certificado de gerenciamento (necessário para proteger conexões entre o nó de cabeçalho e os serviços do Azure)
 
- Crie um serviço de nuvem do Azure no qual os nós do Azure (instâncias de função de trabalho) serão executados

- Criar uma conta de armazenamento do Azure
	
	>[WACOM.NOTE]Anote também sua ID de assinatura do Azure, que será necessária posteriormente. Encontre-a nas informações de conta <a href="[https://account.windowsazure.com/Subscriptions">do Azure</a>.

<h3>Carregar o certificado de gerenciamento padrão</h3>
O HPC Pack instala um certificado autoassinado no nó de cabeçalho, chamado de Certificado de gerenciamento padrão de HPC no Azure da Microsoft, que pode ser carregado como um certificado de gerenciamento do Azure. Este certificado é fornecido para implantações de prova de conceito e fins de teste.

1. No computador do nó de cabeçalho, entre no [Portal de Gerenciamento](https://manage.windowsazure.com).

2. Clique em **Configurações**e, em seguida, clique em **Certificados de Gerenciamento**.

3. Na barra de comandos, clique em **Carregar**.

	![Certificate Settings][upload_cert1]

4. Procure no nó de cabeçalho pelo arquivo C:\Program Files\Microsoft HPC Pack 2012\Bin\hpccert.cer. Em seguida, clique no botão **Verificar**.

	![Upload Certificate][install_hpc10]

Você verá **Gerenciamento padrão de HPC no Azure** na lista de certificados de gerenciamento.

<h3>Criar um serviço de nuvem do Azure</h3> 

>[WACOM.NOTE]Para melhor desempenho, crie o serviço de nuvem e a conta de armazenamento na mesma região geográfica.

1. No Portal de Gerenciamento, na barra de comandos, clique em **Novo**.

2. Clique em **Computação**, clique em **Serviço de Nuvem**, e depois clique em **Criação Rápida**.

3. Digite uma URL para o serviço de nuvem e, em seguida, clique em **Criar Serviço de Nuvem**.

	![Create Service][createservice1]

<h3>Criar uma conta de armazenamento do Azure</h3>

1. No Portal de Gerenciamento, na barra de comandos, clique em **Novo**.

2. Clique em **Serviços de Dados**, clique **Armazenamento**, e depois clique em **Criação Rápida**.

3. Digite uma URL para a conta e, em seguida, clique em **Criar Conta de Armazenamento**.

	![Create Storage][createstorage1]

<h2 id="BKMK_ConfigHN">Configurar o nó de cabeçalho</h2>

Para usar o Gerenciador de Cluster de HPC para implantar nós do Azure e enviar trabalhos, primeiro realize algumas etapas de configuração necessárias do cluster. 

1. No nó de cabeçalho, inicie o Gerenciador de Cluster de HPC. Se a caixa de diálogo **Selecionar Nó de Cabeçalho** aparecer, clique em**Computador Local**. A **Lista de tarefas pendentes de implantação** é exibida.

2. Em **Tarefas de implantação necessárias**, clique em **Configurar sua rede**.

	![Configure Network][config_hpc2]

3. No Assistente de Configuração de Rede, selecione **Todos os nós somente em uma rede corporativa** (Topologia 5).

	![Topology 5][config_hpc3] 

	>[WACOM.NOTE]Essa é a configuração mais simples para fins de demonstração, pois o nó de cabeçalho só precisa de um adaptador de rede para se conectar ao Active Directory e à Internet. Este tutorial não abrange cenários de cluster que exigem redes adicionais. 
	 
4. Clique em**Avançar** para aceitar os valores padrão nas páginas restantes do assistente. Em seguida, na guia **Examinar**, clique em **Configurar** para concluir a configuração de rede.

5. Na **Lista de tarefas pendentes de Implantação**, clique em**Fornecer credenciais de instalação**. 

6. Na caixa de diálogo **Credenciais de instalação**, digite as credenciais da conta de domínio usada para instalar o HPC Pack. Em seguida, clique em **OK**.

	![Installation Credentials][config_hpc6]

	>[WACOM.NOTE]Os serviços do HPC Pack só usam credenciais de instalação para implantar nós de computação locais.
	
7. Na **Lista de tarefas pendentes de implantação**, clique em **Configurar a nomeação de novos nós**. 

8. Na caixa de diálogo **Especificar Série de Nomeação de Nós** aceite a série de nomeação padrão e clique em**OK**.

	![Node Naming][config_hpc8]

	>[WACOM.NOTE]A série de nomeação apenas gera nomes para nós de computação unidos ao domínio. Os nós do Azure são nomeados automaticamente.
	  
9. Na **Lista de tarefas pendentes de Implantação**, clique em **Criar um modelo de nó**.Você usará o modelo de nó para adicionar nós do Azure ao cluster.

10. Em Criar Assistente do modelo de nó, faça o seguinte:

	a. Na página **Escolher Tipo de Modelo de Nó** clique em **Modelo de nó do Azure**, e depois clique em **Avançar**.

	![Node Template][config_hpc10]

	b. Clique em **Avançar** para aceitar o nome do modelo padrão.

	c. Na página **Fornecer Informações Sobre Assinatura** insira sua ID de assinatura do Azure (disponível em informações sobre sua conta <a href="[https://account.windowsazure.com/Subscriptions">do Azure</a>). Em seguida, em **Certificado de gerenciamento**, clique em**Procurar** e selecione **Gerenciamento Padrão de HPC no Azure.** Em seguida, clique em **Avançar**.

	![Node Template][config_hpc12]

	d. Na página **Fornecer Informações Sobre Serviço**, selecione o serviço de nuvem e a conta de armazenamento criados na etapa anterior. Em seguida, clique em **Avançar**.

	![Node Template][config_hpc13]

	e. Clique em **Avançar** para aceitar os valores padrão nas páginas restantes do assistente. Clique na guia **Examinar** e clique em **Criar** para criar o modelo de nó.

	>[WACOM.NOTE]Por padrão, o modelo de nó do Azure inclui configurações para iniciar (provisionar) e interromper os nós manualmente. Você também pode configurar uma agenda para iniciar e interromper os nós do Azure automaticamente. 
	
<h2 id="#BKMK_worker">Adicionar nós do Azure ao cluster</h2>

Agora você usará o modelo do nó para adicionar nós do Azure ao cluster. Adicionar os nós ao cluster armazena suas informações de configuração para que você possa iniciá-los (provisioná-los) a qualquer momento como instâncias de função no serviço de nuvem. Sua assinatura só será cobrada pelos nós do Azure depois que as instâncias de função estiverem sendo executadas no serviço de nuvem.

Para este tutorial, você adicionará dois nós Pequenos.

1. No Gerenciador de Cluster de HPC, em **Gerenciamento de Nós**, no painel **Ações**, clique em **Adicionar Nó**. 

	![Add Node][add_node1]

2. No Assistente Adicionar Nó, na página **Selecionar Método de implantação**, clique em **Adicionar Nós do Azure**e em **Avançar**.

	![Add Azure Node][add_node1_1]

3. Na página **Especificar Novos Nós**, selecione o modelo do nó do Azure criado anteriormente (chamado por padrão **Modelo Padrão de Nós do Azure**). Em seguida, especifique**2** nós de tamanho**Pequeno**, e clique em **Avançar**.

	![Specify Nodes][add_node2]

	Para obter mais detalhes sobre os tamanhos de máquinas virtuais disponíveis, consulte [Tamanhos de máquina virtual e serviço de nuvem para o Azure](http://msdn.microsoft.com/library/windowsazure/dn197896.aspx).

4. Na página **Concluindo o Assistente Adicionar Nó** clique em**Concluir**. 

	 Dois nós do Azure, chamados **AzureCN-0001** e**AzureCN-0002**, agora são exibidos no Gerenciador de Cluster de HPC. Eles estão no estado **Não Implantado**.

	![Added Nodes][add_node3]

<h2 id="BKMK_Start">Iniciar os nós do Azure</h2>
Quando desejar usar os recursos de cluster no Azure, use o Gerenciador de Cluster de HPC para iniciar (provisionar) os nós no Azure e colocá-los online.

1.	No Gerenciador de Cluster de HPC, em **Gerenciamento de Nós**, clique em um ou nos dois nós e no painel **Ações**, clique em **Iniciar**. 

	![Start Nodes][add_node4]

2. Na caixa de diálogo **Iniciar Nós do Azure**, clique em **Iniciar**.

	![Start Nodes][add_node5]
 
	Os nós farão a transição para o estado **Provisionamento**.Você pode exibir o log de provisionamento para controlar o progresso de provisionamento.

	![Provision Nodes][add_node6]

3. Depois de alguns minutos, os nós do Azure concluirão o provisionamento e estarão no estado **Offline**.Nesse estado, as instâncias de função estarão sendo executadas, mas ainda não aceitarão trabalhos de cluster.

4. Para confirmar se as instâncias de função estão em execução, no [Portal de Gerenciamento](https://manage.windowsazure.com), clique em **Serviços de Nuvem**, clique no nome do serviço de nuvem e depois em **Instâncias**. 

	![Running Instances][view_instances1]

	Você visualizará duas instâncias de função de trabalho sendo executadas no serviço. O HPC Pack implanta automaticamente duas instâncias**HpcProxy** (de tamanho médio) para manipular as comunicações entre o nó de cabeçalho e o Azure.

5. Para colocar os nós do Azure online e executar trabalhos de cluster, selecione os nós, clique com o botão direito e clique em **Colocar Online**.

	![Offline Nodes][add_node7]
 
	O Gerenciador de Cluster de HPC indica que os nós estão no estado **Online**.

<h2 id="BKMK_RunCommand">Executar um comando no cluster</h2>	
Você pode usar o comando **clusrun** do HPC Pack para executar um comando ou aplicativo em um ou mais nós do cluster. Como um exemplo simples, use **clusrun** para obter a configuração de IP dos nós do Azure.

1. No nó de cabeçalho, abra um prompt de comando.

2. Digite o seguinte comando:

	`clusrun /nodes:azurecn* ipconfig`

3. Você verá saídas semelhantes ao seguinte.

	![Clusrun][clusrun1]

<h2 id="BKMK_RunJob">Executar um trabalho de teste</h2>

Você pode enviar um trabalho de teste que é executado no cluster híbrido.  Este exemplo é um trabalho simples de "limpeza paramétrica" (um tipo de computação intrinsecamente paralela) que executa as subtarefas que adicionam um número inteiro a elas mesmas, usando o comando **set /a**.Todos os nós no cluster contribuem para concluir as subtarefas para números inteiros de 1 a 100. 

1. No Gerenciador de Cluster de HPC, em **Gerenciamento de Trabalho**, no painel **Ações**, clique em **Novo Trabalho de Limpeza Paramétrica**.

	![New Job][test_job1]

2. Na caixa de diálogo **Novo Trabalho de Limpeza Paramétrica** em **Linha de comando**, digite `set /a *+* (substituindo a linha de comando padrão exibida). Deixe os valores padrão para o restante das configurações e clique em **Enviar** para enviar o trabalho.

	![Parametric Sweep][param_sweep1]

3. Quando o trabalho estiver concluído, clique duas vezes no trabalho **Minha Tarefa de Limpeza**.

4. Clique em **Exibir tarefas** e em uma subtarefa para exibir a saída calculada dessa subtarefa.

	![Task Results][view_job361]

5. Para ver qual nó realizou cálculo da subtarefa, clique em**Nós Alocados**.(Seu cluster pode mostrar um nome de nó diferente).

	![Task Results][view_job362]

<h2 id="BKMK_stop">Parar os nós do Azure</h2>

Depois de testar o cluster, você pode usar o Gerenciador de Cluster de HPC para parar os nós do Azure para evitar encargos desnecessários em sua conta. Isso interromperá o serviço de nuvem e removerá as instâncias de função do Azure. 

1. No Gerenciador de Cluster de HPC, em **Gerenciamento de nós**, selecione os dois nós do Azure. Em seguida, no painel **Ações**, clique **Parar**. 

	![Stop Nodes][stop_node1]

2. Na caixa de diálogo **Parar Nós do Azure**, clique em **Parar**.

	![Stop Nodes][stop_node2]

3. Os nós farão a transição para o estado **Parando**. Depois de alguns minutos, o Gerenciador de Cluster de HPC mostra o estado dos nós como **Não Implantados**.

	![Not Deployed Nodes][stop_node4]

4. Para confirmar se as instâncias de função não estão mais em execução no Azure, no [Portal de Gerenciamento](https://manage.windowsazure.com), clique em **Serviços de Nuvem**, clique no nome do serviço de nuvem e depois em **Instâncias**.Nenhuma instância será implantada no ambiente de produção.

	![No Instances][view_instances2]

	Assim, concluímos o tutorial.

<h2 id="">Recursos relacionados</h2>

* [HPC Pack 2012 R2 e HPC Pack 2012](http://go.microsoft.com/fwlink/p/?LinkID=263697)
* [Intermitência do Azure com o Microsoft HPC Pack](http://go.microsoft.com/fwlink/p/?LinkID=200493)
* [Microsoft HPC Pack em VMs do Azure](http://go.microsoft.com/fwlink/p/?linkid=330375)
* [Azure Big Compute: HPC e Lote](http://azure.microsoft.com/pt-br/solutions/big-compute/)


[Overview]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/hybrid_cluster_overview.png
[install_hpc1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc1.png
[install_hpc2]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc2.png
[install_hpc3]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc3.png
[install_hpc4]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc4.png
[install_hpc6]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc6.png
[install_hpc7]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc7.png
[install_hpc10]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc10.png
[upload_cert1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/upload_cert1.png
[createstorage1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/createstorage1.png
[createservice1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/createservice1.png
[config_hpc2]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc2.png
[config_hpc3]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc3.png
[config_hpc6]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc6.png
[config_hpc8]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc8.png
[config_hpc10]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc10.png
[config_hpc12]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc12.png
[config_hpc13]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc13.png
[add_node1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node1.png
[add_node1_1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node1_1.png
[add_node2]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node2.png
[add_node3]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node3.png
[add_node4]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node4.png
[add_node5]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node5.png
[add_node6]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node6.png
[add_node7]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node7.png
[view_instances1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/view_instances1.png
[clusrun1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/clusrun1.png
[test_job1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/test_job1.png
[param_sweep1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/param_sweep1.png
[view_job361]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/view_job361.png
[view_job362]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/view_job362.png
[stop_node1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/stop_node1.png
[stop_node2]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/stop_node2.png
[stop_node4]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/stop_node4.png
[view_instances2]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/view_instances2.png

<!--HONumber=35_1-->
