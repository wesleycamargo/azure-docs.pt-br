<properties linkid="itpro-windows-howto-howto-setup-hybrid-cluster-hpcpack" urlDisplayName="" pageTitle="Set up a Hybrid Compute Cluster in Azure with Microsoft HPC Pack" metaKeywords="" description="Learn how to use Microsoft HPC Pack and Azure to set up a small, hybrid high performance computing (HPC) cluster" metaCanonical="" services="" documentationCenter="" title="Set up a Hybrid Compute Cluster with Microsoft HPC Pack" authors="danlep" solutions="" manager="timlt" editor="mattshel" />

<tags ms.service="cloud-services" ms.workload="big-compute" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/16/2014" ms.author="danlep"></tags>

# Configure um cluster de computação híbrido com o Microsoft HPC Pack

Este tutorial lhe mostra como usar o Microsoft HPC Pack 2012 R2 e o Azure para instalar um pequeno cluster híbrido de computação de alto desempenho (HPC). O cluster consistirá em um nó de cabeça no local (um computador que execute o sistema operacional Windows Server e o HPC Pack) e alguns nós de computação implantados sob demanda como instâncias de função de trabalho em um serviço de nuvem do Azure. É possível executar trabalhos de computação no cluster híbrido.

![Cluster híbrido de HPC][Cluster híbrido de HPC]

Este tutorial mostra uma abordagem, às vezes chamada de cluster "de estouro para a nuvem", para usar os recursos de computação dimensionáveis e sob demanda no Azure para executar aplicativos com uso intensivo de computação.

Este tutorial não pressupõe nenhuma experiência anterior com clusters de computação ou com o HPC Pack. Ele destina-se somente para ajudá-lo a implantar um cluster híbrido de computação de forma rápida para fins de demonstração. Para obter as considerações e as etapas para implantar um cluster híbrido do HPC Pack em uma escala maior em um ambiente de produção, consulte as [diretrizes detalhadas][diretrizes detalhadas].

> [WACOM.NOTE] O Azure oferece uma [variedade de tamanhos][variedade de tamanhos] para seus recursos de computação, adequados para diferentes cargas de trabalho. Por exemplo, as instâncias A8 e A9 combinam o alto desempenho e acesso a uma rede de aplicativos com alta taxa de transferência e baixa latência necessários para alguns aplicativos de HPC. Para obter mais informações, consultar [Sobre as instâncias A8 e A9 com uso intensivo de computação][Sobre as instâncias A8 e A9 com uso intensivo de computação].

Este tutorial apresenta e explica as seguintes etapas básicas:

-   [Pré-requisitos][Pré-requisitos]
-   [Instalar o HPC Pack no nó de cabeça][Instalar o HPC Pack no nó de cabeça]
-   [Preparar a assinatura do Azure][Preparar a assinatura do Azure]
-   [Configurar o nó de cabeça][Configurar o nó de cabeça]
-   [Adicionar nós do Azure ao cluster][Adicionar nós do Azure ao cluster]
-   [Iniciar os nós do Azure][Iniciar os nós do Azure]
-   [Executar um comando no cluster][Executar um comando no cluster]
-   [Executar um trabalho de teste][Executar um trabalho de teste]
-   [Parar os nós do Azure][Parar os nós do Azure]

## Pré-requisitos

> [WACOM.NOTE]Para concluir este tutorial, você precisa de uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Criar uma conta do Azure][Criar uma conta do Azure].

Além disso, é necessário o seguinte para este tutorial.

-   Um computador local que esteja executando uma edição do Windows Server 2012 R2 ou o Windows Server 2012. Este computador será o nó de cabeça do cluster de HPC. Se não estiver executando o Windows Server, você pode baixar e instalar uma [versão de avaliação][versão de avaliação].

    -   O computador deve estar ingressado a um domínio do Active Directory.

    -   Certifique-se de que nenhuma função de servidor ou serviço de função adicional esteja instalado.

    -   Para oferecer suporte ao HPC Pack, o sistema operacional deve ser instalado em um desses idiomas: inglês, japonês ou chinês (simplificado).

    -   Verifique se as atualizações importantes e críticas foram instaladas.

-   Arquivos de instalação para o HPC Pack 2012 R2, que está disponível gratuitamente. [Baixe][Baixe] a instalação completa do pacote e copie os arquivos para o computador do nó de cabeça ou para um local da rede. Escolha os arquivos de instalação no mesmo idioma da sua instalação do Windows Server.

-   Uma conta de domínio com permissões de Administrador local no nó de cabeça.

-   Conectividade TCP na porta 443 do nó de cabeça para o Azure.

## Instalar o HPC Pack no nó de cabeça

Primeiro instale o Microsoft HPC Pack em um computador local executando o Windows Server que será o nó de cabeça do cluster.

1.  Faça login no nó de cabeça usando uma conta de domínio que tenha permissões de Administrador local.

2.  Inicie o Assistente de Instalação do HPC Pack executando Setup.exe nos arquivos de instalação do HPC Pack.

3.  Na tela **Instalação do HPC Pack 2012 R2**, clique em **Nova instalação ou adicionar novos recursos a uma instalação existente**.

    ![Instalação do HPC Pack 2012][Instalação do HPC Pack 2012]

4.  Na **página do Contrato de Usuário do Software da Microsoft**, clique em **Avançar**.

5.  Na página **Selecionar Tipo de instalação**, clique em **Criar um novo cluster de HPC, criando um nó de cabeça**e, em seguida, clique em **Avançar**.

    ![Selecione o Tipo de instalação][Selecione o Tipo de instalação]

6.  O assistente executa vários testes de pré-instalação. Clique em **Avançar** na página **Regras de instalação** se todos os testes forem aprovados. Caso contrário, examine as informações fornecidas e faça as alterações necessárias em seu ambiente. Execute os testes novamente ou se necessário inicie o Assistente de Instalação novamente.

    ![Regras de instalação][Regras de instalação]

7.  Na página **Configuração do BD de HPC**, certifique-se de que **Nó de cabeça** esteja selecionado para todos os bancos de dados de HPC e, em seguida, clique em **Avançar**.

    ![Configuração do BD][Configuração do BD]

8.  Aceite as seleções padrão nas páginas restantes do assistente. Na página **Instalar Componentes necessários**, clique em **Instalar**.

    ![Instalar][Instalar]

9.  Após a conclusão da instalação, desmarque **Iniciar o Gerenciador de Cluster de HPC** e, em seguida, clique em **Concluir**. (Você iniciará o Gerenciador de Cluster de HPC em uma etapa posterior ao concluir a configuração do nó de cabeça).

    ![Concluir][Concluir]

## Preparar a assinatura do Azure

Use o [Portal de Gerenciamento][Portal de Gerenciamento] para realizar as etapas a seguir com sua assinatura do Azure. Elas são necessárias para implantar posteriormente os nós do Azure no nó de cabeça local.

-   Carregue um certificado de gerenciamento (necessário para proteger conexões entre o nó de cabeça e os serviços do Azure)

-   Crie um serviço de nuvem do Azure no qual os nós do Azure (instâncias de função de trabalho) serão executados

-   Criar uma conta de armazenamento do Azure

    > [WACOM.NOTE]Também anote sua ID de assinatura do Azure, que será necessária posteriormente. Encontre-a nas informações sobre sua conta do Azure [][].

### Carregue o certificado de gerenciamento padrão

O HPC Pack instala um certificado autoassinado no nó de cabeça, chamado de Certificado de gerenciamento padrão de HPC no Azure da Microsoft, que pode ser carregado como um certificado de gerenciamento do Azure. Este certificado é fornecido para implantações de prova de conceito e fins de teste.

1.  No computador do nó de cabeça, faça login no [Portal de Gerenciamento][Portal de Gerenciamento].

2.  Clique em **Configurações**e, em seguida, clique em **Certificados de gerenciamento**.

3.  Na barra de comandos, clique em **Nova**.

    ![Configurações de certificado][Configurações de certificado]

4.  Procure no nó de cabeça pelo arquivo C:\\Program Files\\Microsoft HPC Pack 2012\\Bin\\hpccert.cer. Em seguida, clique no botão **verificar**.

    ![Carregar um certificado][Carregar um certificado]

Você verá **Gerenciamento padrão de HPC no Azure** na lista de certificados de gerenciamento.

### Criar um serviço de nuvem do Azure

> [WACOM.NOTE]Para maior desempenho, crie o serviço de nuvem e a conta de armazenagem na mesma região geográfica.

1.  No Portal de Gerenciamento, na barra de comandos, clique em **Novo**.

2.  Clique em **Computação**, **Serviço de Nuvem** e em **Criação Rápida**.

3.  Digite uma URL para o serviço de nuvem e, em seguida, clique em **Criar Serviço de Nuvem**.

    ![Criar Serviço][Criar Serviço]

### Criar uma conta de armazenamento do Azure

1.  No Portal de Gerenciamento, na barra de comandos, clique em **Novo**.

2.  Clique em **Serviços de Dados**, **Armazenamento** e em **Criação Rápida**.

3.  Digite uma URL para a conta e, em seguida, clique em **Criar Conta de armazenamento**.

    ![Criar Armazenamento][Criar Armazenamento]

## Configurar o nó de cabeça

Para usar o Gerenciador de Cluster de HPC para implantar nós do Azure e para enviar trabalhos, primeiro realize algumas etapas de configuração necessárias do cluster.

1.  No nó de cabeça, inicie o Gerenciador de Cluster de HPC. Se a caixa de diálogo **Selecione Nó de cabeça** for exibida, clique em **Computador Local**. A **Lista de tarefas de implantação** é exibida.

2.  Em **Tarefas de implantação necessárias**, clique em **Configurar sua rede**.

    ![Configurar Rede][Configurar Rede]

3.  No Assistente de Configuração de Rede, selecione **Todos os nós somente em uma rede corporativa** (Topologia 5).

    ![Topologia 5][Topologia 5]

    > [WACOM.NOTE]Essa é a configuração mais simples para fins de demonstração, pois o nó de cabeça só precisa de um adaptador de rede para se conectar ao Active Directory e à Internet. Este tutorial não abrange cenários de cluster que exigem redes adicionais.

4.  Clique em **Avançar** para aceitar os valores padrão nas páginas restantes do assistente. Em seguida, na guia **Examinar**, clique em **Configurar** para concluir a configuração de rede.

5.  Na **Lista de tarefas de implantação**, clique em **Fornecer credenciais de instalação**.

6.  Na caixa de diálogo **Credenciais de instalação**, digite as credenciais da conta de domínio usada para instalar o HPC Pack. Em seguida, clique em **OK**.

    ![Credenciais de instalação][Credenciais de instalação]

    > [WACOM.NOTE]Os serviços do HPC Pack só usam credenciais de instalação para implantar nós de computação locais.

7.  Na **Lista de tarefas de implantação**, clique em **Configurar a nomeação dos novos nós**.

8.  Na caixa de diálogo **Especificar Série de nomeação de nós**, aceite a série de nomeação padrão e clique em **OK**.

    ![Nomeação de nós][Nomeação de nós]

    > [WACOM.NOTE]A série de nomeação apenas gera nomes para nós de computação ingressados no domínio. Os nós do Azure são nomeados automaticamente.

9.  Na **Lista de tarefas de implantação**, clique em **Criar um modelo de nó**. Você usará o modelo de nó para adicionar nós do Azure ao cluster.

10. Em Criar Assistente do modelo de nó, faça o seguinte:

    a. Na página **Escolher Tipo de modelo de nó**, clique em **Modelo de nó do Azure**e, em seguida, clique em **Avançar**.

    ![Modelo do nó][Modelo do nó]

    b. Clique em **Avançar** para aceitar o nome do modelo padrão.

    c. Na página **Fornecer informações sobre assinatura**, insira sua ID da assinatura do Azure (disponível em informações sobre sua conta do Azure [][]). Em seguida, em **Certificado de gerenciamento**, clique em **Procurar** e selecione **Gerenciamento de HPC no Azure padrão**. Em seguida, clique em **Próximo**.

    ![Modelo do nó][1]

    d. Na página **Fornecer informações sobre serviço**, selecione o serviço de nuvem e a conta de armazenamento criados na etapa anterior. Em seguida, clique em **Próximo**.

    ![Modelo do nó][2]

    e. Clique em **Avançar** para aceitar os valores padrão nas páginas restantes do assistente. Em seguida, na guia **Examinar**, clique em **Criar** para criar o modelo de nó.

    > [WACOM.NOTE]Por padrão, o modelo de nó do Azure inclui configurações iniciar (provisionar) e interromper os nós manualmente. Você também pode configurar uma agenda para iniciar e interromper os nós do Azure automaticamente.

## Adicionar nós do Azure ao cluster

Agora você usará o modelo do nó para adicionar nós do Azure ao cluster. Adicionar os nós ao cluster armazena suas informações de configuração para que você possa iniciá-los (provisioná-los) a qualquer momento como instâncias de função no serviço de nuvem. Sua assinatura somente é cobrada pelos nós do Azure depois que as instâncias de função estiverem sendo execucatas no serviço de nuvem.

Para este tutorial, você adicionará dois nós Pequenos.

1.  No Gerenciador de Cluster de HPC, em **Gerenciamento de nós**, no painel **Ações**, clique em **Adicionar nó**.

    ![Adicionar nó][Adicionar nó]

2.  No Assistente de Adição de Nó, na página **Selecionar Método de implantação**, clique **Adicionar Nós do Azure**e em **Avançar**.

    ![Adicionar nó do Azure][Adicionar nó do Azure]

3.  Na página **Especificar Novos nós**, selecione o modelo do nó do Azure criado anteriormente (chamado por padrão **Modelo de nós do Azure padrão**). Em seguida, especifique **2** nós de tamanho **Pequeno**e clique em **Avançar**.

    ![Especificar nós][Especificar nós]

    Para obter mais detalhes sobre os tamanhos de máquinas virtuais disponíveis, consulte [Tamanhos de máquina virtual e serviço de nuvem para o Azure][Tamanhos de máquina virtual e serviço de nuvem para o Azure].

4.  Na página de **Conclusão do Assistente de Adição de Nós**, clique em **Concluir**.

    Dois nós do Azure, chamados **AzureCN-0001** e **AzureCN-0002**, agora são exibidos no Gerenciador de Cluster de HPC. Eles estão no estado **Não implantado**.

    ![Nós adicionados][Nós adicionados]

## Iniciar os nós do Azure

Quando desejar usar os recursos de cluster no Azure, use o Gerenciador de Cluster de HPC para iniciar (provisionar) os nós no Azure e colocá-los on-line.

1.  No Gerenciador de Cluster de HPC, em **Gerenciamento de nós**, clique em um ou nos dois nós e no painel **Ações**, clique em **Iniciar**.

    ![Iniciar nós][Iniciar nós]

2.  Na caixa de diálogo **Iniciar nós do Azure**, clique em **Iniciar**.

    ![Iniciar nós][3]

    Os nós farão a transição para o **Provisionamento**. Você pode exibir o log de provisionamento para controlar o progresso de provisionamento.

    ![Provisionar nós][Provisionar nós]

3.  Depois de alguns minutos, os nós do Azure concluirão o provisionamento e estarão no estado **Off-line**. Nesse estado, as instâncias de função estarão sendo executadas, mas ainda não aceitarão trabalhos de cluster.

4.  Para confirmar que as instâncias de função estão sendo executadas, no [Portal de Gerenciamento][Portal de Gerenciamento], clique em **Serviços de Nuvem**, no nome do serviço de nuvem e em **Instâncias**.

    ![Executando instâncias][Executando instâncias]

    Você visualizará duas instâncias de função de trabalho sendo executadas no serviço. O HPC Pack implanta automaticamente duas instâncias **HpcProxy** (de tamanho Médio) para manipular as comunicações entre o nó de cabeça e o Azure.

5.  Para colocar os nós do Azure on-line para executar trabalhos de cluster, selecione os nós, clique com o botão direito e clique em **Colocar on-line**.

    ![Nós off-line][Nós off-line]

    O Gerenciador de Cluster de HPC indica que os nós estão no estado **On-line**.

## Executar um comando no cluster

Você pode usar o comando **clusrun** do HPC Pack para executar um comando ou aplicativo em um ou mais nós do cluster. Como um exemplo simples, use **clusrun** para obter a configuração de IP dos nós do Azure.

1.  No nó de cabeça, abra um prompt de comando.

2.  Digite o seguinte comando:

    `clusrun /nodes:azurecn* ipconfig`

3.  Você verá saídas semelhantes ao seguinte.

    ![Clusrun][Clusrun]

## Executar um trabalho de teste

Você pode enviar um trabalho de teste que é executado no cluster híbrido. Este exemplo é um trabalho simples de "limpeza paramétrica" (um tipo de computação intrinsecamente paralela) que executa as subtarefas que adicionam um número inteiro a elas mesmas, usando o comando **set /a**. Todos os nós no cluster contribuem para concluir as subtarefas para números inteiros de 1 a 100.

1.  No Gerenciador de Cluster de HPC, em **Gerneciamento de trabalho**, no painel **Ações**, clique em **Novo trabalho de limpeza paramétrica**.

    ![Novo trabalho][Novo trabalho]

2.  Na caixa de diálogo **Novo trabalho de limpeza paramétrica** em **Linha de comando**, digite `set /a *+*` (substituindo a linha de comando padrão que é exibida). Deixe os valores padrão para o restante das configurações e clique em **Enviar** para enviar o trabalho.

    ![Limpeza paramétrica][Limpeza paramétrica]

3.  Quando o trabalho estiver concluído, clique duas vezes no trabalho **Minhas tarefas de limpeza**.

4.  Clique em **Exibir tarefas** e em uma subtarefa para exibir a saída calculada dessa subtarefa.

    ![Resultados da tarefa][Resultados da tarefa]

5.  Para visualizar qual nó executou o cálculo para dessa subtarefa, clique em **Nós alocados**. (Seu cluster pode mostrar um nome de nó diferente).

    ![Resultados da tarefa][4]

## Parar os nós do Azure

Depois de testar o cluster, você pode usar o Gerenciador de Cluster de HPC para parar os nós do Azure para evitar encargos desnecessários em sua conta. Isso interromperá o serviço de nuvem e removerá as instâncias de função do Azure.

1.  No Gerenciador de Cluster de HPC, em **Gerenciamento de nós**, selecione os dois nós do Azure. Em seguida, no painel **Ações**, clique **Parar**.

    ![Parar os nós][Parar os nós]

2.  Na caixa de diálogo **Parar nós do Azure**, clique em **Parar**.

    ![Parar os nós][5]

3.  Os nós farão a transição para a **Parada**. Depois de alguns minutos, o Gerenciador de Cluster de HPC mostra o estado dos nós como **Não implantados**.

    ![Nós não implantados][Nós não implantados]

4.  Para confirmar que as instâncias de função não estão mais sendo executadas no Azure, no [Portal de Gerenciamento][Portal de Gerenciamento], clique em **Serviços de Nuvem**, no nome do serviço de nuvem e em **Instâncias**. Nenhuma instância será implantada no ambiente de produção.

    ![Nenhuma instância][Nenhuma instância]

    Assim, concluímos o tutorial.

## Recursos relacionados

-   [HPC Pack 2012 R2 e HPC Pack 2012][HPC Pack 2012 R2 e HPC Pack 2012]
-   [Estouro para o Azure com o Microsoft HPC Pack][diretrizes detalhadas]
-   [Implantando aplicativos aos nós do Azure][Implantando aplicativos aos nós do Azure]
-   [Microsoft HPC Pack nas VMs do Azure][Microsoft HPC Pack nas VMs do Azure]

  [Cluster híbrido de HPC]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/hybrid_cluster_overview.png
  [diretrizes detalhadas]: http://go.microsoft.com/fwlink/p/?LinkID=200493
  [variedade de tamanhos]: http://go.microsoft.com/fwlink/p/?LinkId=389844
  [Sobre as instâncias A8 e A9 com uso intensivo de computação]: http://go.microsoft.com/fwlink/p/?Linkid=328042
  [Pré-requisitos]: #BKMK_Prereq
  [Instalar o HPC Pack no nó de cabeça]: #BKMK_DeployHN
  [Preparar a assinatura do Azure]: #BKMK_Prpare
  [Configurar o nó de cabeça]: #BKMK_ConfigHN
  [Adicionar nós do Azure ao cluster]: #BKMK_worker
  [Iniciar os nós do Azure]: #BKMK_start
  [Executar um comando no cluster]: #BKMK_RunCommand
  [Executar um trabalho de teste]: #BKMK_RunJob
  [Parar os nós do Azure]: #BKMK_stop
  [Criar uma conta do Azure]: http://www.windowsazure.com/pt-br/develop/php/tutorials/create-a-windows-azure-account/
  [versão de avaliação]: http://technet.microsoft.com/evalcenter/dn205286.aspx
  [Baixe]: http://go.microsoft.com/fwlink/p/?linkid=389557
  [Instalação do HPC Pack 2012]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc1.png
  [Selecione o Tipo de instalação]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc2.png
  [Regras de instalação]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc3.png
  [Configuração do BD]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc4.png
  [Instalar]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc6.png
  [Concluir]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc7.png
  [Portal de Gerenciamento]: https://manage.windowsazure.com
  []: [https://account.windowsazure.com/Subscriptions
  [Configurações de certificado]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/upload_cert1.png
  [Carregar um certificado]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc10.png
  [Criar Serviço]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/createservice1.png
  [Criar Armazenamento]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/createstorage1.png
  [Configurar Rede]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc2.png
  [Topologia 5]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc3.png
  [Credenciais de instalação]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc6.png
  [Nomeação de nós]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc8.png
  [Modelo do nó]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc10.png
  [1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc12.png
  [2]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc13.png
  [Adicionar nó]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node1.png
  [Adicionar nó do Azure]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node1_1.png
  [Especificar nós]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node2.png
  [Tamanhos de máquina virtual e serviço de nuvem para o Azure]: http://msdn.microsoft.com/library/windowsazure/dn197896.aspx
  [Nós adicionados]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node3.png
  [Iniciar nós]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node4.png
  [3]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node5.png
  [Provisionar nós]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node6.png
  [Executando instâncias]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/view_instances1.png
  [Nós off-line]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node7.png
  [Clusrun]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/clusrun1.png
  [Novo trabalho]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/test_job1.png
  [Limpeza paramétrica]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/param_sweep1.png
  [Resultados da tarefa]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/view_job361.png
  [4]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/view_job362.png
  [Parar os nós]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/stop_node1.png
  [5]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/stop_node2.png
  [Nós não implantados]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/stop_node4.png
  [Nenhuma instância]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/view_instances2.png
  [HPC Pack 2012 R2 e HPC Pack 2012]: http://go.microsoft.com/fwlink/p/?LinkID=263697
  [Implantando aplicativos aos nós do Azure]: http://go.microsoft.com/fwlink/p/?LinkId=325317
  [Microsoft HPC Pack nas VMs do Azure]: http://go.microsoft.com/fwlink/p/?linkid=330375
