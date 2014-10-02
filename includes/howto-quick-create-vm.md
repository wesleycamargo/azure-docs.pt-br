<properties writer="kathydav" editor="tysonn" manager="jeffreyg" />

# Como criar rapidamente uma máquina virtual

Você usa o método **Criação Rápida** para criar rapidamente uma máquina virtual no Portal de Gerenciamento. Ao criar essa máquina, use uma caixa de diálogo para fornecer os detalhes de configuração.

**Observação**: este artigo cria uma máquina virtual que não está conectada a uma rede virtual. Se você quiser que sua máquina virtual use uma rede virtual, use o método **Da Galeria** e especifique a rede virtual ao criar a máquina virtual. Para obter mais informações sobre redes virtuais, consulte [Visão geral da rede virtual do Azure][]

1.  Entre no [Portal de Gerenciamento do Azure][].

2.  Na barra de comandos, clique em **Nova**.

    ![Criar uma nova máquina virtual][]

3.  Clique em **Máquinas Virtuais** e, em seguida, clique em **Criação Rápida**.

    ![Crie rapidamente uma nova máquina virtual][]

    A caixa de diálogo **Criar uma Nova Máquina Virtual** será exibida.

4.  Digite as seguintes informações para a nova máquina virtual:

    -   **Nome DNS** - o nome que é usado para a máquina virtual que é criada e o serviço de nuvem que contém a máquina virtual.
    -   **Imagem** -a imagem de plataforma que é usada para criar a máquina virtual.
    -   **Nome de Usuário** - o nome da conta que você deseja usar para gerenciar a máquina virtual.
    -   **Senha da Conta** - digite e confirme uma senha forte para a conta.
    -   **Local** - a região que contém a máquina virtual.

5.  Clique na marca de seleção para criar a máquina virtual.

    **Observação:** uma conta de armazenamento será criada automaticamente para conter esta máquina virtual.

    Você verá a nova máquina virtual mostrada na página **Máquinas Virtuais**.

    ![A criação da máquina virtual foi bem-sucedida][]

  [Visão geral da rede virtual do Azure]: http://go.microsoft.com/fwlink/p/?LinkID=294063
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  [Criar uma nova máquina virtual]: ./media/howto-quick-create-vm/create.png
  [Crie rapidamente uma nova máquina virtual]: ./media/howto-quick-create-vm/createquick.png
  [A criação da máquina virtual foi bem-sucedida]: ./media/howto-quick-create-vm/vmsuccesswindows.png
