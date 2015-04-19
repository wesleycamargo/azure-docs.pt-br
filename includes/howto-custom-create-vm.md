<properties authors="kathydav" editor="tysonn" manager="jeffreyg" />

#Como criar uma máquina virtual personalizada

Uma máquina virtual *personalizada* se refere a uma máquina virtual que você cria usando o método **Da Galeria**, porque permite configurar mais opções do que o método **Criar Rápido**. Estas opções incluem:

- Mais escolhas para a imagem usar para criar a máquina virtual (VM)
- Conectar a VM a uma rede virtual 
- Adicionar a VM a um serviço de nuvem existente 
- Adicionar a VM a um grupo de disponibilidade

**Importante**: Se você quiser que sua máquina virtual use uma rede virtual para que possa se conectar às suas máquinas virtuais diretamente pelo nome do host ou estabelecer conexões entre locais, use o método Da Galeria e especifique a rede virtual ao criar a máquina virtual. Uma máquina virtual pode ser configurada para ingressar em uma rede virtual somente quando você criar a máquina virtual. Para obter mais informações sobre redes virtuais, consulte [Visão geral da rede virtual do Azure](http://go.microsoft.com/fwlink/p/?LinkID=294063).

1. Entre no [Portal de Gerenciamento do Azure](http://manage.windowsazure.com).

2. Na barra de comandos, clique em **Novo**.

3. Clique em **Calcular**, em **Máquina Virtual** e depois em **Da Galeria**.

4. Escolha a imagem que você quer usar e, em seguida, clique na seta para continuar.

5. Se várias versões da imagem estiverem disponíveis, na **Data de lançamento da versão**, selecione a versão que deseja usar. 

6. Em **Nome da Máquina Virtual**, digite o nome que você deseja usar para a máquina virtual.

7. Use a **Camada** e **Tamanho** para selecionar o tamanho apropriado para a máquina virtual. O tamanho que você seleciona afeta a configuração máxima da máquina virtual, bem como o seu preço. Para obter detalhes de configuração, consulte [Tamanhos de máquinas virtuais e serviço de nuvem para o Azure](http://go.microsoft.com/fwlink/p/?LinkID=389844).

8. Em **Novo Nome de Usuário**, digite um nome para a conta administrativa que você deseja usar para gerenciar o servidor. 

9. Em **Nova Senha**, digite uma senha forte para a conta administrativa. Em **Confirmar Senha**, digite a mesma senha novamente.

10. Clique na seta para continuar.

11. Na **serviço de nuvem**, siga um destes procedimentos:
	
	- Se esta for a primeira ou a única máquina virtual no serviço de nuvem, selecione **Criar um novo serviço de nuvem**. Então, em **Nome DNS de serviço de nuvem**, digite um nome que usa entre 3 e 24 letras minúsculas e números. Esse nome se torna parte do URI que é usado para entrar em contato com a máquina virtual por meio do serviço de nuvem.
	- Se esta máquina virtual está sendo adicionada a um serviço de nuvem, selecione-o na lista.

	**Observação**: Para obter mais informações sobre como conectar máquinas virtuais ao mesmo serviço de nuvem, consulte [Como conectar máquinas virtuais em um serviço de nuvem](http://www.windowsazure.com/pt-br/manage/windows/how-to-guides/connect-to-a-cloud-service/).

12. Em **Região/Grupo de Afinidade/Rede Virtual**, selecione a região, grupo de afinidade ou rede virtual que você deseja usar para a máquina virtual. Para obter mais informações sobre grupos de afinidade, consulte [Sobre grupos de afinidade para a rede virtual](http://msdn.microsoft.com/library/azure/jj156085.aspx).

13. Na **Conta de armazenamento**, selecione uma conta de armazenamento existente para o arquivo VHD ou use uma conta de armazenamento gerada automaticamente. Apenas uma conta de armazenamento por região será criada automaticamente. Todas as outras máquinas virtuais que você criar com essa configuração estarão localizadas nessa conta de armazenamento. Você está limitado a 20 contas de armazenamento.

14. Se você quiser que a máquina virtual pertença a um conjunto de disponibilidade, no **Conjunto de Disponibilidade**, selecione **Criar conjunto de disponibilidade** ou adicioná-lo a um conjunto de disponibilidade existente. 

	**Observação**: Máquina Virtual que são membros de um conjunto de disponibilidade são implantados em domínios de falha diferente. Saiba como usar várias máquinas virtuais em um conjunto de disponibilidade para que seus aplicativos permaneçam disponíveis durante falhas de rede, falhas de hardware de disco local e qualquer tempo de inatividade planejado.

15.  Em **Pontos de extremidade**, examine os novos pontos de extremidade que serão criados para permitir conexões à máquina virtual, como a área de trabalho remota ou um cliente de Secure Shell (SSH). Você pode também adicionar mais pontos de extremidade agora ou criá-los mais tarde. Para obter instruções sobre como criá-las mais tarde, consulte [Como configurar pontos de extremidade para uma máquina virtual](http://azure.microsoft.com/documentation/articles/virtual-machines-set-up-endpoints/). 

16.   Em **Agente de VM**, decida se deseja instalar o agente de VM. Este agente fornece o ambiente para a instalação de extensões que podem ajudá-lo a interagir com a máquina virtual. Para obter detalhes, consulte [Gerenciar extensões](http://go.microsoft.com/FWLink/p/?LinkID=390493).

17. Clique na seta para criar a máquina virtual.


	![Custom virtual machine creation successful](./media/howto-custom-create-vm/VMSuccessWindows.png)

##Próximas etapas##
Depois que a máquina virtual é criada, ela é iniciada automaticamente. Quando o portal mostra o status como Executando, você poderá fazer logon na máquina virtual. Para obter instruções, consulte um dos seguintes artigos:

- [Como fazer logon em uma máquina virtual executando o Linux](virtual-machines-linux-how-to-log-on.md)
- [Como fazer logon em uma máquina virtual executando o Windows Server](virtual-machines-log-on-windows-server.md)


