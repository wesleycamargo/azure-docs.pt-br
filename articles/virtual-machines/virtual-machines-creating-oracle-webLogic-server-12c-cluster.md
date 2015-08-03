<properties pageTitle="Criando um cluster do Oracle WebLogic Server 12c no Azure" description="Percorrer um exemplo de como criar um cluster do Oracle WebLogic Server 12c no Microsoft Azure." services="virtual-machines" authors="bbenz" documentationCenter=""/>
<tags ms.service="virtual-machines" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="infrastructure-services" ms.date="06/22/2015" ms.author="bbenz" />
#Criando um cluster do Oracle WebLogic Server 12c no Azure
O exemplo a seguir mostra como você pode criar um cluster do Oracle WebLogic Server no Azure, com base em uma imagem fornecida pelo Microsoft Oracle WebLogic Server 12c em execução no Windows Server 2012.

Cada instância em um cluster WebLogic Server deve estar executando a mesma versão do Oracle WebLogic Server. Este exemplo usa o WebLogic Server 12c Enterprise Edition.

##Criar Máquinas Virtuais para usar no cluster
Você criará uma Máquina Virtual para usar como o servidor de administração de cluster, e criará Máquinas Virtuais adicionais para usar como parte do cluster.

### Criar uma Máquina Virtual para usar como o servidor de administração

Criar uma Máquina Virtual usando a imagem do **Oracle WebLogic Server 12c Enterprise Edition no Windows Server 2012** disponível no Azure. As etapas para criar essa Máquina Virtual podem ser encontradas em [Criar uma Máquina Virtual Oracle WebLogic Server 12c no Azure](#creating-an-oracle-weblogic-server-12c-virtual-machine-in-azure-new-article). Para fins deste tutorial, chame a Máquina Virtual de **ADMIN MYVM1**. Anote o nome da Rede Virtual da VM, você usará esse valor ao criar VMs adicionais para adicionar ao cluster. (O nome da VM e o nome de Rede Virtual podem ser qual você desejar, desde que seja exclusivo no Azure.)

### Criar Máquinas Virtuais gerenciadas para usar no cluster

Crie Máquinas Virtuais adicionais, que serão gerenciadas pelo servidor de administração, usando a imagem de Oracle WebLogic Server 12c disponível no Azure. Para fins deste tutorial, nomeie as outras máquinas virtuais como **MANAGED MYVM2** e **MANAGED MYVM3**. As etapas para criar essas Máquinas Virtuais podem ser encontradas em [criar uma Máquina Virtual de Oracle WebLogic Server 12c no Azure](#creating-an-oracle-weblogic-server-12c-virtual-machine-in-azure-new-article), *exceto* a seguinte alteração, que é necessária:

-  Ao criar as Máquinas Virtuais, não crie uma nova Rede Virtual. Especificamente, a caixa de diálogo **Configuração opcional > Rede Virtual**, em vez do padrão **Criar uma nova Rede Virtual**, selecione a Rede Virtual que foi criada para a VM do servidor de administração. Por exemplo, se durante a criação do seu servidor de administração, você criou uma Rede Virtual chamada **EXAMPLE**, selecione **EXAMPLE** ao criar máquinas virtuais de cluster gerenciado.

##Criar um domínio

1. Faça logon no [Portal do Azure](https://ms.portal.azure.com/).

2. Clique em **Máquinas Virtuais**.

3. Clique no nome da Máquina Virtual que você criou para ser o servidor de administração de cluster (por exemplo, **MYVM1 ADMIN**).

4. Clique em **Conectar**.

5. Responda às solicitações conforme necessário para se conectar à Máquina Virtual. Quando for solicitado o nome do administrador e a senha, use os valores que você forneceu quando criou a Máquina Virtual.

6. Na **Página 1** da caixa de diálogo **Assistente de configuração do Fusion Middleware**, clique em **Criar um novo domínio** e, em seguida, clique em **Avançar**. (Se a caixa de diálogo **Assistente de configuração do Fusion Middleware** ainda não estiver aberta, abra-a clicando em **Windows Iniciar**, digitando **Assistente de configuração** e, em seguida, clicando no ícone do **Assistente de configuração**).

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image19.png)

7. Na **Página 2** da caixa de diálogo **Assistente de configuração do Fusion Middleware**, selecione o modelo **Domínio básico do servidor WebLogic** e clique em **Avançar**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image20.png)

8. Na **Página 3** da caixa de diálogo **Assistente de configuração do Fusion Middleware**:

	1. [Opcional] Altere o nome de usuário de **weblogic** para um valor de sua escolha.
	
	2. Especifique e confirme uma senha para o administrador do servidor WebLogic.
	
	3. Clique em **Próximo**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image21.png)

9. Na **Página 4** da caixa de diálogo **Assistente de configuração do Fusion Middleware**, selecione **Produção** para o modo de domínio, selecione o JDK disponível (ou navegue até um JDK, se desejado) e, em seguida, clique em **Avançar**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image22.png)

10.  Na **Página 5** da caixa de diálogo **Assistente de configuração do Fusion Middleware**, não selecione nenhuma opção e clique em **Avançar**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image23.png)

11.  Na **Página 6** da caixa de diálogo **Assistente de configuração do Fusion Middleware**, clique em **Criar**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image24.png)

12.  Na **Página 7** da caixa de diálogo **Assistente de configuração do Fusion Middleware**, após o domínio ter sido criado, clique em **Avançar**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image25.png)

13.  Na **Página 8** da caixa de diálogo **Assistente de configuração do Fusion Middleware**, verifique Iniciar Servidor de Administração e, em seguida, clique em **Concluir**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image26.png)

14.  Um prompt de comando para **startWebLogic.cmd** é iniciado. Quando solicitado, forneça o seu nome de usuário do WebLogic e a senha.

##Configurar o cluster

1. Ainda conectado à Máquina Virtual de administração, execute o **Console de administração do WebLogic Server**, <http://localhost:7001/console>. Quando solicitado, forneça seu nome de usuário do WebLogic Server e a senha.

2. No **Console de administração do WebLogic Server**, clique em **Bloquear e Editar**.

3. No painel **Estrutura de domínio**, expanda **Ambiente** e, em seguida, clique em **Clusters**.

4. Na caixa de diálogo **Resumo de Clusters**, clique em **Novo** e, em seguida, clique em **Cluster**.

5. Na caixa de diálogo **Propriedade de Clusters**:

	1. Insira um nome para o cluster.

	2. Selecione **Transmitir em Unicast ** para o **Modo de mensagens**.

		![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image001.png)

	
	3. Clique em **OK**.

6. No painel **Estrutura de domínio**, expanda **Ambiente** e, em seguida, clique em **Servidores**.

7. Adicione o seu primeiro servidor gerenciado ao cluster.

	1. Clique em **Novo**.

	2. Na caixa de diálogo **Criar um novo servidor**:

		1. Para **Nome do servidor**, digite o nome do seu primeiro servidor gerenciado. Por exemplo**, MYVM2-MANAGED.**

		2. Para **Endereço de escuta do servidor**, digite o nome novamente.

		3. Para a **Porta de escuta**, tipo **7008**.

		4. Marque **Sim, tornar este servidor membro de um cluster existente.**

		5. Na lista suspensa **Selecionar um cluster**, selecione o cluster que você criou anteriormente.

			34d27e82-bb2e-4f9c-aaad-ca3e28c0f5fc

		6. Clique em **Próximo**.

		7. Clique em **Concluir**.

8. Adicione o segundo servidor gerenciado ao cluster, usando as etapas acima. Para **Nome do servidor** e **Endereço de escuta do servidor**, use o nome do segundo computador gerenciado. Para a **Porta de escuta**, use **7008**.

9. Ainda no console de Administração do Servidor WebLogic, clique em **Ativar alterações.**

10. No administrador de Máquina Virtual, crie uma variável de ambiente denominada **SERVER_HOME** com o valor definido para **C:\\Oracle\\Middleware\\Oracle_Home\\wlserver.** Você pode criar uma variável de ambiente usando as seguintes etapas:

	1. Clique em **Windows Iniciar**, digite **Painel de Controle**, clique no ícone **Painel de Controle**, clique em **Sistema e Segurança**, clique em **Sistema** e, em seguida, clique em **Configurações avançadas do sistema**.

	2. Clique na guia **Avançado** e, em seguida, clique em **Variáveis de Ambiente**.

	3. Na seção **Variáveis do sistema**, clique em **Novo** para criar a variável.

	4. Na caixa de diálogo **Nova variável do sistema**, digite **SERVER_HOME** para o nome da variável e digite **C:\\Oracle\\Middleware\\Oracle_Home\\wlserver** para o valor.

	5. Clique em **OK** para salvar a nova variável de ambiente e feche a caixa de diálogo **Nova variável do sistema**.

	6. Feche as outras caixas de diálogo que foram abertas pelo painel de controle.

11. Abra um novo prompt de comando (para que a variável de ambiente **SERVER_HOME** entre em vigor).

	>[AZURE.NOTE]Algumas das etapas restantes exigem o uso de um prompt de comando após o logon em suas Máquinas Virtuais. Para habilitar facilmente as Noções básicas sobre qual computador você está conectado, depois de abrir o prompt de comando, execute **title %COMPUTERNAME%.**
	>
	>**( %COMPUTERNAME%** é uma variável de ambiente definida pelo sistema que é definida automaticamente como o nome do computador. Executar o comando **title** **%COMPUTERNAME%** fará com que a barra de título do prompt de comando exiba o nome do computador).

12. Execute o comando a seguir:

		%SERVER_HOME%\\common\\bin\\pack.cmd -managed=true -domain=C:\\Oracle\\Middleware\\Oracle_Home\\user_projects\\domains\\base_domain -template=c:\\mytestdomain.jar -template_name="mytestdomain" 

	Este comando cria um jar chamado **c:\\mytestdomain.jar.** Posteriormente, você copiará este jar para as Máquinas Virtuais gerenciadas em seu cluster.

13. Permitir uma conexão de entrada por meio do firewall na porta 7001.

	1. Enquanto ainda conectado à sua Máquina Virtual, clique em **Windows Iniciar**, digite **Firewall do Windows com Segurança Avançada** e clique no ícone **Firewall do Windows com Segurança Avançada**. Isso abre o console de gerenciamento do **Firewall do Windows com Segurança Avançada**.

	2. No console de gerenciamento do firewall, clique em **Regras de Entrada** no painel esquerdo (se você não vir **Regras de Entrada**, expanda o nó superior no painel à esquerda) e, em seguida, clique em **Nova Regra** no painel direito.

	3. Para **Tipo de Regra**, selecione **Porta** e clique em **Avançar**.

	4. Para **Protocolo e Porta**, selecione **TCP**, selecione **Portas locais específicas**, digite **7001** para a porta e, em seguida, clique em **Avançar**.

	5. Selecione **Permitir a conexão** e clique em **Avançar**.

	6. Aceite os padrões para os perfis aos quais a regra se aplica e clique em **Avançar**.

	7. Especifique um nome para a regra e, opcionalmente, uma descrição e depois clique em **Concluir**.

14. Para cada uma das Máquinas Virtuais gerenciadas:

	1. Faça logon na Máquina Virtual.

	2. Crie uma variável de ambiente denominada **SERVER_HOME** com o valor definido para **C:\\Oracle\\Middleware\\Oracle_Home\\wlserver.**

	3. Copie C:\\mytestdomain.jar da administração de Máquina Virtual para C:\\mytestdomain.jar na Máquina Virtual gerenciada.

	4. Abra um prompt de comando (lembre-se de executar **title %COMPUTERNAME%** no prompt de comando, para tornar claro qual computador está sendo acessado).

	5. Execute o comando a seguir:

			%SERVER_HOME%\\common\\bin\\unpack.cmd -domain=C:\\Oracle\\Middleware\\Oracle_Home\\user_projects\\domains\\base_domain -template=c:\\mytestdomain.jar

	6. Altere o diretório atual do prompt de comando para **C:\\Oracle\\Middleware\\Oracle_Home\\user_projects\\domains\\base_domain\\bin.**

	7. Execute start<<*MACHINENAME*>>.cmd, em que <<*MACHINENAME*>> é o nome do computador gerenciado. Por exemplo, **startMYVM2-MANAGED.**

	8. Quando solicitado, forneça o nome de usuário do WebLogic Server e a senha.

	9. Permitir uma conexão de entrada por meio do firewall na porta 7008. (Siga as etapas usadas para abrir a porta 7001 no servidor de administração, mas use 7008 em vez disso, para os servidores gerenciados).

15. Na administração de Máquina Virtual, abra o **Console de Administração do WebLogic Server,** <http://localhost:7001/console>, e veja os servidores que estão em execução.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image003.png)

16. Crie um ponto de extremidade com balanceamento de carga definido para as Máquinas Virtuais gerenciadas:

	1. Dentro do [Portal do Azure](https://ms.portal.azure.com/), na seção **Máquinas Virtuais **, selecione a primeira Máquina Virtual gerenciada (como **MYVM2-MANAGED)**. 
	2. Clique em **Configurações**, **Pontos de extremidade** e, em seguida, **Adicionar**.

	3. Especifique um nome para o ponto de extremidade, especifique **TCP** para o protocolo, especifique a porta pública **80** e a porta privada **7008**. Deixe o restante das opções como estão.

	4. Marque **Criar um conjunto com balanceamento de carga** e, em seguida, clique em **Concluir**.

	5. Especifique um nome para o balanceamento de carga definido, aceite os padrões para os outros parâmetros e, em seguida, clique em **Concluir. **

17. Crie um ponto de extremidade para a sua Máquina Virtual:

	1. Faça logon no [Portal do Azure](https://ms.portal.azure.com/).

	2. Clique em **Procurar**

	3. Clique em **Máquinas Virtuais**

	4. Selecione a Máquina Virtual

	5. Clique em **Configurações**

	6. Clique em **Conjuntos de balanceamento de carga**.

	7. Clique em **Ingressar**.

	8. Defina o tipo de carga balanceada definido como **Interno**

	9. Especifique um nome para o ponto de extremidade

		1. Use **TCP** para o protocolo

		2. Use **80** para a porta pública

		3. Use **7008** para a porta de investigação.

	10. Deixe o restante das opções como estão.

	11. Clique em **OK**

	12. Aguarde até que essa Máquina Virtual una a Carga Balanceada definida antes de prosseguir para a próxima etapa.

18. Dentro do [Portal do Azure](https://ms.portal.azure.com/), na seção **Máquinas Virtuais**, selecione a segunda Máquina Virtual gerenciada (como **MYVM3-MANAGED**). Siga as etapas acima para unir o conjunto de balanceamento de carga que você criou para a primeira Máquina Virtual gerenciada.

##Implantando um aplicativo no cluster

Neste ponto, você pode implantar o seu aplicativo usando as seguintes etapas. Vamos supor que você esteja implantando o aplicativo de carrinho de compras Oracle, disponível para download em <http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war>.

1. Faça logon na Máquina Virtual que serve como administrador para o cluster do WebLogic Server (por exemplo, **MYVM1 ADMIN**). 

2. Copie o shoppingcart.war localmente. Por exemplo, crie uma pasta chamada **C:\\mywar** e salve o WAR na <http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war> para **c:\\mywar**.

3. Abra o **Console de administração do WebLogic Server**, <http://localhost:7001/console>. Quando solicitado, forneça o seu nome de usuário do WebLogic e a senha.

4. Dentro do **Console de administração do WebLogic Server**, clique em **Bloquear e Editar**, **Implantações** e, em seguida, clique em **Instalar**.

5. Para **Caminho**, tipo **C:\\mywar\\shoppingcart.war**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image004.png)

	Clique em **Próximo**.

6. Selecione **Instalar essa implantação como um aplicativo** e, em seguida, clique em **Avançar**.

7. Clique em **Concluir**.

8. Para **Destinos disponíveis**, selecione o cluster previamente criado e certifique-se de que **Todos os servidores no cluster** esteja selecionado e, em seguida, clique em **Avançar**.

9. Em **Acessibilidade Fonte**, selecione **Copiar esse aplicativo em cada destino para mim** e, em seguida, clique em **Concluir**.

10.  Dentro do **Console de administração do WebLogic Server**, clique em **Salvar** e, em seguida, em **Ativar alterações**.

11.  Clique em **Implantações**, selecione **carrinho de compras**, clique em **Iniciar** e, em seguida, clique em **Todas as solicitações de serviço**. Quando solicitado a confirmar, clique em **Sim**.

12.  Para ver o aplicativo de carrinho de compras em execução na Internet, abra um navegador para a URL na forma de `http://<<unique_domain_name>>/shoppingcart`. (Você pode determinar o valor de `<<unique_domain_name>>` dentro do [Portal do Azure](https://ms.portal.azure.com/) clicando em Máquinas Virtuais e, em seguida, selecionando a Máquina Virtual que você está usando para executar o Oracle WebLogic Server).

## Próximas etapas

Para ver mais que o cluster está funcionando conforme o esperado, é possível modificar o projeto shoppingcart.war para exibir o nome da máquina que atende a sessão do navegador, iniciar uma sessão do navegador, interromper a máquina que atendia a sessão do navegador e atualizar a sessão do navegador para ver se uma máquina diferente continua a atender a sessão do navegador.

Por exemplo:

1. Modificar o arquivo **DWRHeader1.jspf** para conter o código a seguir na parte superior do arquivo:

		<table>
		
		<tr><td><CENTER><b><h3><% out.println("Your request is served from " + System.getenv("computername") ); %></h3></b></CENTER></td></tr>
		
		</table>             

2. Modifique o arquivo **weblogic.xm**l para conter o seguinte código após a linha de comentário `Insert session descriptor element here`.

		<session-descriptor>
			<persistent-store-type>replicated_if_clustered</persistent-store-type>
		</session-descriptor>


3. Recompile e reimplante o shoppingcart.war atualizado.

4. Abra uma sessão do navegador e execute o aplicativo carrinho de compras. Adicione alguns itens ao carrinho de compras e observe qual máquina está atendendo a sessão do navegador.

5. No Portal do Azure, na interface do usuário **Máquinas Virtuais**, selecione a VM que atendeu a sessão do navegador e clique em **Desligar**. Aguarde até que o status da VM esteja **Parado (desalocado)** antes de continuar.

6. Atualize a sessão do navegador que está executando o aplicativo do carrinho de compras e veja se uma máquina diferente está atendendo a sessão do navegador.

7. Clique no link do carrinho de compras e veja os itens adicionados anteriormente que ainda estão no carrinho de compras.

## Recursos adicionais

Agora que você configurou o cluster executando o Oracle WebLogic Server, consulte os seguintes tópicos para obter informações adicionais.

- [Imagens de Máquinas Virtuais Oracle – diversas considerações](virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images.md)

- [Documentação do produto Oracle WebLogic Server](http://www.oracle.com/technetwork/middleware/weblogic/documentation/index.html)

- [Oracle WebLogic Server 12c usando o Linux no Microsoft Azure](http://www.oracle.com/technetwork/middleware/weblogic/learnmore/oracle-weblogic-on-azure-wp-2020930.pdf)

<!---HONumber=July15_HO4-->