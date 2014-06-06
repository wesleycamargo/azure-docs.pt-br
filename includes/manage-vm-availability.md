<properties writer="kathydav" editor="tysonn" manager="jeffreyg" /> 

#Gerenciar a disponibilidade de máquinas virtuais#

Para ajudar a garantir a disponibilidade do seu aplicativo, é altamente recomendável usar várias máquinas virtuais que forneçam redundância. Para fornecer redundância, configure mais de uma máquina virtual para realizar a mesma função. Essa redundância é necessária para receber um nível de serviço garantido. Também certifique-se de considerar as dependências. Por exemplo, se a máquina virtual "IIS1" depender de serviços fornecidos por máquina virtual "SQL1", a redundância para a máquina virtual "SQL1" é fornecida por "SQL2" para ajudar a evitar interrupções de serviço. Para obter mais informações sobre contratos de nível de serviço, consulte a seção "Serviços de nuvem, Máquinas Virtuais e Rede Virtual" em [Contratos de Nível de Serviço](http://www.windowsazure.com/pt-br/support/legal/sla/)

Essa abordagem pode ajudar a garantir que seu aplicativo esteja disponível durante falhas da rede local, falhas de hardware de disco local e tempo de inatividade planejado que talvez a plataforma exija.

Você gerencia a disponibilidade de um aplicativo que usa várias máquinas virtuais adicionando as máquinas virtuais a um conjunto de disponibilidade. Os conjuntos de disponibilidade estão diretamente relacionados aos domínios de falha e aos domínios de atualização. Um domínio de falha no Azure é definido por evitar pontos únicos de falha, como a unidade de switch ou alimentação de rede de um rack de servidores. Na verdade, um domínio de falha é diretamente equivalente a um rack de servidores físicos. Quando várias máquinas virtuais estão conectadas em um serviço em nuvem, um conjunto de disponibilidade coloca as máquinas virtuais em domínios de falha diferentes. O diagrama a seguir mostra dois conjuntos de disponibilidade com duas máquinas virtuais em cada conjunto.

![Domínios de atualização](./media/manage-vm-availability/UpdateDomains.png)

O Azure periodicamente atualiza o sistema operacional que hospeda as instâncias de um aplicativo. Uma máquina virtual será desligada quando uma atualização for aplicada. Um domínio de atualização é usado para garantir que nem todas as instâncias de máquina virtual sejam atualizadas ao mesmo tempo. Ao atribuir várias máquinas virtuais para um conjunto de disponibilidade, o Azure garante que as máquinas são atribuídas a domínios de atualização diferentes. O diagrama anterior mostra duas máquinas virtuais executando o Serviço de Informações da Internet (IIS) em domínios de atualização separados e duas máquinas virtuais executando o SQL Server também em domínios de atualização separados.

Você deve usar uma combinação de conjuntos de disponibilidade e pontos de extremidade de balanceamento de carga para ajudar a garantir que seu aplicativo esteja sempre disponível e sendo executado com eficiência. Para obter mais informações sobre balanceamento de carga de máquinas virtuais, consulte [Realizando o balanceamento de carga de máquinas virtuais] [].

Esta tarefa inclui as seguintes etapas:

- [Etapa 1: criar uma máquina virtual e um conjunto de disponibilidade] []
- [Etapa 2: adicionar uma máquina virtual ao serviço em nuvem e atribuí-la ao conjunto de disponibilidade durante o processo de criação] []
- [Etapa 3 (opcional): criar um conjunto de disponibilidade para máquinas virtuais criadas anteriormente] []
- [Etapa 4 (opcional): adicionar uma máquina virtual criada anteriormente a um conjunto de disponibilidade] []

## <a id="createset"> </a>Etapa 1: criar uma máquina virtual e um conjunto de disponibilidade ##

Para criar um conjunto de disponibilidade que contenha as máquinas virtuais você pode criar a primeira máquina virtual e o conjunto de disponibilidade ao mesmo tempo e, em seguida, adicionar máquinas virtuais ao conjunto de disponibilidade conforme você as cria. Você pode também criar máquinas virtuais, criar um conjunto de disponibilidade e adicionar todas as máquinas ao conjunto.

**Para criar uma máquina virtual e um conjunto de disponibilidade**

1. Se você ainda não tiver feito isso, entre no Portal de Gerenciamento do Azure.

2. Na barra de comandos, clique em **Novo**.

	![Criar uma máquina virtual](./media/manage-vm-availability/Create.png)

3. Clique em **Máquina Virtual** e, em seguida, clique em **Da Galeria**.


	A caixa de diálogo **Selecionar o sistema operacional da máquina virtual** será exibida. 
	
4. Na página **Imagens da Plataforma**, selecione uma imagem e clique na seta para continuar.

	A caixa de diálogo **Configuração da máquina virtual** será exibida.

5. Em **Nome de Máquina Virtual**, digite o nome que você deseja usar para a máquina virtual.

6. Em **Novo Nome de Usuário**, digite um nome para a conta administrativa que você deseja usar para gerenciar o servidor. 

7. Em **Nova Senha**, digite uma senha forte para a conta administrativa. Em **Confirmar senha**, digite novamente a senha que você digitou anteriormente.

8. Em **Tamanho**, selecione o tamanho que você deseja usar para a máquina virtual. O tamanho que você seleciona depende do número de núcleos que são necessários para o seu aplicativo.

9. Clique na seta para continuar.

	A caixa de diálogo **Modo da máquina virtual** será exibida.
	
10. Selecione **Máquina virtual autônoma**.

11.	Em **Nome DNS**, digite um nome para o serviço em nuvem criado para a máquina. O nome pode conter de 3 a 24 letras minúsculas e números.

12.	Em **Conta de Armazenamento**, selecione uma conta de armazenamento para armazenar o arquivo. vhd ou escolha criar a conta de armazenamento automaticamente. Apenas uma conta de armazenamento por região será criada automaticamente. Todas as outras máquinas virtuais que você criar com essa configuração estarão localizadas nessa conta de armazenamento. Você está limitado a 20 contas de armazenamento.

13.	Em **Região/Grupo de Afinidade/Rede Virtual**, selecione a região, grupo de afinidade ou rede virtual que você deseja que contenha a máquina virtual. Para obter mais informações sobre grupos de afinidade, consulte [Sobre grupos de afinidade da rede Virtual] [].

14. Clique na seta para continuar.

	A caixa de diálogo **Opções da máquina virtual** será exibida.

15. Em **Conjunto de disponibilidade**, selecione **Criar conjunto de disponibilidade**.
 
16. Em **Nome do Conjunto de Disponibilidade**, digite o nome do conjunto de disponibilidade.

17.	Clique na seta para criar a máquina virtual e o grupo de disponibilidade.

	No painel da nova máquina virtual, você pode clicar em **Configurar** e verificar que a máquina virtual é um membro do novo conjunto de disponibilidade.

## <a id="addmachine"> </a>Etapa 2: adicionar uma máquina virtual ao serviço em nuvem e atribuí-la ao conjunto de disponibilidade durante o processo de criação ##

A etapa anterior mostrou como criar uma máquina virtual e um conjunto de disponibilidade ao mesmo tempo. Você pode agora criar uma nova máquina virtual, conectá-la ao serviço em nuvem da primeira máquina virtual e adicioná-lo ao conjunto de disponibilidade que você criou anteriormente.

**Para conectar a uma nova máquina virtual e adicioná-lo ao conjunto de disponibilidade**

1. Se você ainda não tiver feito isso, entre no Portal de Gerenciamento do Azure.

2. Na barra de comandos, clique em **Novo**.

	![Criar uma máquina virtual](./media/manage-vm-availability/Create.png)

3. Clique em **Máquina Virtual** e, em seguida, clique em **Da Galeria**.

	![Criar da galeria](./media/manage-vm-availability/CreateNew.png)

	A caixa de diálogo **Selecionar o sistema operacional da máquina virtual** será exibida. Agora você pode selecionar uma imagem da Galeria de Imagens.

	
4. Clique em **Imagens da Plataforma**, selecione a imagem da plataforma que você deseja usar e, em seguida, clique na seta para continuar.

	A caixa de diálogo **Configuração da máquina virtual** será exibida.

5. Em **Nome de Máquina Virtual**, digite o nome que você deseja usar para a máquina virtual.

6. Em **Novo Nome de Usuário**, digite um nome para a conta administrativa que você deseja usar para gerenciar o servidor. 

7. Em **Nova Senha**, digite uma senha forte para a conta administrativa da máquina virtual. Em **Confirmar Senha**, digite a senha novamente.

8. Em **Tamanho**, selecione o tamanho que você deseja usar para a máquina virtual. O tamanho que você seleciona depende do número de núcleos que são necessários para o seu aplicativo.

9. Em uma máquina virtual que executa o sistema operacional Linux, é possível selecionar a opção para proteger a máquina com uma chave SSH.

10.	Clique na seta para continuar.

	A caixa de diálogo **Modo da máquina virtual** será exibida.

	
11. Selecione **Conectar-se a Máquina Virtual existente** para criar uma nova máquina virtual que será conectada com a primeira máquina virtual no conjunto de disponibilidade.  Selecione o serviço e, nuvem que contém a máquina virtual no conjunto de disponibilidade.

12. Em **Conta de Armazenamento**, selecione a conta de armazenamento onde o arquivo VHD está armazenado.

13. Em **Região/Grupo de Afinidade/Rede Virtual**, selecione a região que você deseja que contenha a máquina virtual.

14. Clique na seta para continuar.

	A caixa de diálogo **Opções da máquina virtual** será exibida.

15. Selecione o conjunto de disponibilidade que foi criado quando você criou a primeira máquina virtual.

16. Clique na marca de seleção para criar a máquina virtual e adicioná-la ao conjunto de disponibilidade.

	No painel da nova máquina virtual, você pode clicar em **Configurar** e verificar que a máquina virtual é um membro do novo conjunto de disponibilidade.

## <a id="previousmachine"> </a>Etapa 3 (opcional): criar um conjunto de disponibilidade para máquinas virtuais criadas anteriormente ##

Você pode criar um conjunto de disponibilidade e adicionar uma máquina virtual a ele após criar a máquina. Depois de criar uma máquina virtual, você pode configurar o tamanho da máquina e se ela será um membro de um conjunto de disponibilidade.

**Para criar um novo conjunto de disponibilidade**

1. Se você ainda não tiver feito isso, entre no Portal de Gerenciamento do Azure.

2. Clique em **Máquinas Virtuais**e, em seguida, selecione a máquina virtual que você deseja configurar.

3. Clique em **Configurar**.

4. Na seção **Conjunto de disponibilidade**, selecione **Criar Conjunto de Disponibilidade** e digite um nome para o conjunto.


5. Clique em **Salvar**.

	**Observação:** isso pode fazer com que a máquina virtual seja reiniciada para finalizar a participação no conjunto de disponibilidade. 

## <a id="existingset"> </a>Etapa 4 (opcional): adicionar uma máquina virtual criada anteriormente a um conjunto de disponibilidade ##

Você pode facilmente adicionar uma máquina virtual existente a um conjunto de disponibilidade que já foi criado anteriormente. Para adicionar uma máquina virtual a um conjunto de disponibilidade, ele deve ser conectado ao serviço em nuvem como as outras máquinas virtuais do conjunto. Para obter mais informações sobre como conectar máquinas virtuais, consulte [Como conectar máquinas virtuais em um serviço de nuvem] [].

**Para adicionar uma máquina virtual existente a um conjunto de disponibilidade**

1. Se você ainda não tiver feito isso, entre no Portal de Gerenciamento do Azure.

2. Clique em **Máquinas Virtuais** e, em seguida, selecione a máquina virtual que você deseja adicionar ao conjunto de disponibilidade.

3. Clique em **Configurar**.

4. Na seção **Conjunto de disponibilidade**, selecione o conjunto de disponibilidade que você criou anteriormente.

5. Clique em **Salvar**. 

	**Observação:** isso pode fazer com que a máquina virtual seja reiniciada para finalizar a participação no conjunto de disponibilidade.


[Etapa 1: criar uma máquina virtual e um conjunto de disponibilidade]: #createset
[Etapa 2: adicionar uma máquina virtual ao serviço em nuvem e atribuí-la ao conjunto de disponibilidade durante o processo de criação]: #addmachine
[Etapa 3 (opcional): criar um conjunto de disponibilidade para máquinas virtuais criadas anteriormente]: #previousmachine
[Etapa 4 (opcional): adicionar uma máquina virtual criada anteriormente a um conjunto de disponibilidade]: #existingset


<!-- LINKS -->
[Realizando o balanceamento de carga de máquinas virtuais]: ../load-balance-virtual-machines
[Sobre grupos de afinidade da rede virtual]: http://msdn.microsoft.com/library/windowsazure/jj156085.aspx
[Como conectar máquinas virtuais em um serviço de nuvem]: ../virtual-machines-connect-cloud-service

