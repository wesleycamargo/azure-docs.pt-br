<properties writer="kathydav" editor="tysonn" manager="jeffreyg" />

#Como criar uma máquina virtual personalizada

Criando uma máquina virtual personalizada permite que você escolha opções não estarão disponíveis se você usar o **criação rápida** método. Essas opções incluem conectando-se a uma rede virtual, um serviço de nuvem existente e um conjunto de disponibilidade.

Cada máquina virtual está associada com um serviço de nuvem, por si só, ou com outras máquinas virtuais no mesmo serviço de nuvem. Uma razão comum para colocar mais de uma máquina virtual no mesmo serviço de nuvem é fornecer balanceamento de carga para o aplicativo. Se seu aplicativo precisa apenas de uma máquina virtual ou se você estiver criando a primeira máquina virtual, você irá criar o serviço de nuvem quando você criar a máquina virtual. Caso contrário, você adicionará a nova máquina virtual a um serviço de nuvem existente.

Se você quiser que sua máquina virtual use uma rede virtual para que possa se conectar às suas máquinas virtuais diretamente pelo nome do host ou estabelecer conexões entre locais, use o método **Da Galeria** e especifique a rede virtual ao criar a máquina virtual. Uma máquina virtual pode ser configurada para ingressar em uma rede virtual somente quando você criar a máquina virtual. Para obter mais informações sobre redes virtuais, consulte [Visão geral da rede virtual do Windows Azure](http://go.microsoft.com/fwlink/p/?LinkID=294063)

1. Entre no [Portal de Gerenciamento](http://manage.windowsazure.com) do Windows Azure.

2. Na barra de comandos, clique em **Novo**.

3. Clique em **Computar**, clique em **Máquina Virtual**e, em seguida, clique em **Da galeria**.

4. Clique em **Imagens da Plataforma**, selecione a imagem da plataforma que você deseja usar e, em seguida, clique na seta para continuar.
	
5.  Se várias versões da imagem estão disponíveis, na **Data de lançamento da versão**, selecione a versão que deseja usar.

6. Em **Nome de Máquina Virtual**, digite o nome que você deseja usar para a máquina virtual.

7. Em **Tamanho**, selecione o tamanho que você deseja usar para a máquina virtual. O tamanho que você seleciona depende do número de núcleos que são necessários para o seu aplicativo.

8. Em **Novo Nome de Usuário**, digite um nome para a conta administrativa que você deseja usar para gerenciar o servidor. 

9. Em **Nova Senha**, digite uma senha forte para usar na conta administrativa da máquina virtual. Em **Confirmar Senha**, digite a mesma senha novamente.

10. Clique na seta para continuar.

11. Na **serviço de nuvem**, siga um destes procedimentos:
	
-Se esta for a máquina virtual primeira ou apenas no serviço de nuvem, selecione **criar um novo serviço de nuvem** Então, em **Nome DNS de serviço de nuvem**, digite um nome que usa entre 3 e 24 letras minúsculas e números. Esse nome se torna parte do URI que é usado para entrar em contato com a máquina virtual por meio do serviço de nuvem.
-Se esta máquina virtual está sendo adicionada a um serviço de nuvem, selecione-o na lista.

	**Observação**: para obter mais informações sobre como conectar máquinas virtuais, consulte [Como conectar máquinas virtuais em um serviço de nuvem](http://www.windowsazure.com/pt-BR/manage/windows/how-to-guides/connect-to-a-cloud-service/).

12. Em **Região/Grupo de Afinidade/Rede Virtual**, selecione a região, grupo de afinidade ou rede virtual que você deseja usar que contenha a máquina virtual. Para obter mais informações sobre grupos de afinidade, consulte [Sobre grupos de afinidade da rede Virtual (a página pode estar em inglês)][].

13. Na **conta de armazenamento**, selecione uma conta de armazenamento existente para o arquivo VHD ou usar uma conta de armazenamento gerado automaticamente. Apenas uma conta de armazenamento por região será criada automaticamente. Todas as outras máquinas virtuais que você criar com essa configuração estarão localizadas nessa conta de armazenamento. Você está limitado a 20 contas de armazenamento.

14. Se você quiser que a máquina virtual deve pertencer a um conjunto de disponibilidade, na **disponibilidade definida**, selecione **criar conjunto de disponibilidade** ou adicioná-la a um conjunto existente de disponibilidade. 

	**Observação**: Máquina Virtual que são membros de um conjunto de disponibilidade são implantados em domínios de falha diferente. Saiba como usar várias máquinas virtuais no Windows Azure para que seus aplicativos permaneçam disponíveis durante falhas de rede locais, falhas de hardware de disco local e tempo de inatividade planejado.

15.  Em **Agente de VM**, decida se deseja instalar o agente de VM. Este agente fornece o ambiente para a instalação de extensões que podem ajudá-lo a interagir com a máquina virtual. Para obter detalhes, consulte [Usando extensões](http://go.microsoft.com/FWLink/p/?LinkID=394093).
	**Importante**: O agente de VM pode ser instalado somente quando você criar a máquina virtual.  

15.  Em **pontos de extremidade**, examine os novos pontos de extremidade que serão criados para permitir conexões para a máquina virtual, como a área de trabalho remota ou um cliente de Secure Shell (SSH). Você pode também adicionar mais pontos de extremidade agora ou criá-los mais tarde. Para obter instruções sobre como criá-los mais tarde, consulte [Como definir a comunicação com uma máquina virtual](http://www.windowsazure.com/pt-BR/manage/linux/how-to-guides/setup-endpoints/). 


16. Clique na seta para criar a máquina virtual.


	![A personalização da máquina virtual foi bem-sucedida](./media/howto-custom-create-vm/VMSuccessWindows.png)


[Crie uma máquina virtual a uma rede virtual]: /pt-br/manage/services/networking/add-a-vm-to-a-virtual-network/

[Sobre Grupos de Afinidade para Rede Virtual]: http://msdn.microsoft.com/pt-BR/library/windowsazure/

[Criar uma nova máquina virtual]: ./media/howto-custom-create-vm/Create.png

[Como criar uma máquina virtual personalizada]: ./media/howto-custom-create-vm/CreateNew.png



