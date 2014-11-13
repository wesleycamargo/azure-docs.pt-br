<properties authors="kathydav" editor="tysonn" manager="donaldg" />

# Como conectar máquinas virtuais em um serviço de nuvem

Quando você cria uma máquina virtual, um serviço de nuvem é criado automaticamente para conter a máquina. Você pode criar várias máquinas virtuais no mesmo serviço de nuvem para ativar as máquinas virtuais para se comunicar uns com os outros, para balanceamento de carga entre máquinas virtuais e para manter a alta disponibilidade das máquinas.

Para obter mais informações sobre balanceamento de carga de máquinas virtuais, consulte [Máquinas virtuais de balanceamento de carga][Máquinas virtuais de balanceamento de carga]. Para obter mais informações sobre o gerenciamento da disponibilidade do seu aplicativo, consulte [Gerenciar a disponibilidade de máquinas virtuais][Gerenciar a disponibilidade de máquinas virtuais].

Primeiro, você precisará criar uma máquina virtual com um novo serviço de nuvem e, em seguida, você poderá conectar mais máquinas virtuais à primeira máquina virtual no mesmo serviço de nuvem.

1.  Criar uma máquina virtual usando as etapas em [Como criar uma máquina virtual personalizada][Como criar uma máquina virtual personalizada].

2.  Depois de criar a primeira máquina virtual personalizada, na [barra de comando do][barra de comando do] Portal de gerenciamento, clique em **Novo**.

    ![Criar uma nova máquina virtual][Criar uma nova máquina virtual]

3.  Clique em **Máquina Virtual** e, em seguida, clique em **Da Galeria**.

    ![Crie uma máquina virtual personalizada][Crie uma máquina virtual personalizada]

    A caixa de diálogo **Selecionar o sistema operacional da máquina virtual** será exibida.

4.  Na página **Escolher uma imagem**, selecione uma imagem e, em seguida, clique na seta para continuar.

    A primeira página **Configuração de máquina virtual** será exibida.

5.  Em **Nome de Máquina Virtual**, digite o nome que você deseja usar para a máquina virtual.

6.  Em **Tamanho**, selecione o tamanho que você deseja usar para a máquina virtual. O tamanho que você seleciona depende do número de núcleos que são necessários para o seu aplicativo.

7.  Em **Novo Nome de Usuário**, digite um nome para a conta administrativa que você deseja usar para gerenciar o servidor.

8.  Em **Nova Senha**, digite uma senha forte para a conta administrativa. Em **Confirmar Senha**, digite a senha novamente.

9.  Em uma máquina virtual que executa o sistema operacional Linux, é possível selecionar a opção para proteger a máquina com uma chave SSH.

10. Em **Serviço de Nuvem**, selecione o serviço de nuvem que conterá a nova máquina virtual.

11. Em **Conta de Armazenamento**, selecione uma conta de armazenamento para armazenar o arquivo. vhd ou deixe o campo definido como padrão para criar a conta de armazenamento automaticamente. Apenas uma conta de armazenamento será criada automaticamente. Todas as outras máquinas virtuais que você criar com essa configuração estarão localizadas nessa conta de armazenamento. Você está limitado a 20 contas de armazenamento.

12. Para usar um conjunto de disponibilidade, selecione aquela que foi criada quando você criou a primeira máquina virtual.

13. Revise a configuração de ponto de extremidade padrão e modifique se necessário.

14. Clique na marca de seleção para criar a máquina virtual conectada.

  [Máquinas virtuais de balanceamento de carga]: ../../articles/load-balance-virtual-machines/
  [Gerenciar a disponibilidade de máquinas virtuais]: ../../articles/manage-availability-virtual-machines/
  [Como criar uma máquina virtual personalizada]: ../../articles/virtual-machines-create-custom/
  [barra de comando do]: http://manage.windowsazure.com
  [Criar uma nova máquina virtual]: ./media/howto-connect-vm-cloud-service/Create.png
  [Crie uma máquina virtual personalizada]: ./media/howto-connect-vm-cloud-service/CreateNew.png
