<properties writer="kathydav" editor="tysonn" manager="jeffreyg" />

# Como desanexar um disco de dados de uma máquina virtual

# 

-   [Conceitos][]
-   [Como: Localizar os discos que estão associados a uma máquina virtual][]
-   [Como: Desanexar um disco de dados][]

## <a id="concepts"> </a>Conceitos

Uma máquina virtual no Azure usa diferentes tipos de discos, como discos do sistema operacional, de local temporário e de dados opcionais. Você também pode anexar um disco de dados a uma máquina virtual para armazenar dados de aplicativo. Um disco de dados é um disco rígido virtual (VHD) que pode ser criado localmente com seu computador ou na nuvem com o Azure.

Você pode anexar e desanexar discos de dados sempre que desejar, mas você está limitado o número de discos que você pode se conectar a uma máquina virtual com base no tamanho da máquina.

Quando você não precisar mais um disco de dados que está conectado a uma máquina virtual, você pode desconectá-lo facilmente. Esse processo não exclui o disco de armazenamento. Se desejar usar os dados existentes no disco novamente, você pode facilmente conectar o disco novamente para a mesma máquina virtual ou anexá-lo a uma nova máquina virtual.

Para obter mais informações sobre o uso de discos de dados, consulte [Gerenciar discos e imagens][]

## <a id="finddisks"></a>Como: Localizar os discos anexados a uma máquina virtual

Você pode encontrar os discos que estão conectados a uma máquina virtual usando o painel de controle ou a página de discos de máquinas virtuais.

### Use o painel de controle para localizar informações sobre discos anexados

1.  Se você ainda não fez isso, entre no [Portal de Gerenciamento][] do Azure.

2.  Clique em **Máquinas Virtuais**e, em seguida, selecione a máquina virtual apropriada.

3.  Clique em **Painel**. No painel de controle para a máquina virtual, você pode encontrar o número de discos conectados e os nomes dos discos. O exemplo a seguir mostra um disco de dados conectado a uma máquina virtual:

    ![Encontrar disco de dados][]

**Observação:** Pelo menos um disco está anexado a todas as máquinas virtuais. Cada máquina virtual tem um sistema operacional disco conectado que não é possível desanexar sem excluir a máquina virtual. O local temporário de disco não está listado na seção de discos porque ele não é persistente.

### Use a página de discos de máquinas virtuais para encontrar informações sobre discos anexados

1.  Se você ainda não fez isso, entre no [Portal de Gerenciamento][] do Azure.

2.  Clique em **Máquinas Virtuais**, e, em seguida, clique em **Discos**. Esta página mostra uma lista de todos os discos que estão disponíveis para uso com máquinas virtuais e os discos que estão sendo usados por máquinas virtuais. A lista é uma combinação de discos de sistema operacional e dados. Para diferenciar entre os dois tipos de discos que estão conectados com a máquina virtual, use o painel de controle.

    **Observação:** Ao anexar um novo disco de dados a uma máquina virtual, você pode atribuir um nome ao arquivo. vhd usado para o disco, mas o Azure atribui o nome do disco. O nome consiste no nome do serviço de nuvem, o nome de máquina virtual e um identificador numérico.

## <a id="detachdisk"> </a>Como: Desanexar um disco de dados

Depois de localizar o nome do disco que você deseja desanexar, você pode concluir as seguintes etapas para desanexar o disco da máquina virtual.

1.  Se você ainda não fez isso, entre no Portal de Gerenciamento do Azure.

2.  Clique em **Máquinas Virtuais**, selecione a máquina virtual que tem o disco de dados que você deseja desanexar, e, em seguida, clique em **Desanexar o disco**.

3.  Selecione o disco de dados e, em seguida, clique na marca de seleção para desconectá-lo.

    ![Desanexar detalhes do disco][]

    O disco permanece no armazenamento mas não esteja conectado a uma máquina virtual.

Agora você pode anexar o disco novamente para a mesma máquina virtual ou para uma nova máquina. Para obter instruções, consulte [Como anexar um disco de dados a uma máquina virtual][].

  [Conceitos]: #concepts
  [Como: Localizar os discos que estão associados a uma máquina virtual]: #finddisks
  [Como: Desanexar um disco de dados]: #detachdisk
  [Gerenciar discos e imagens]: http://go.microsoft.com/fwlink/p/?LinkId=263439
  [Portal de Gerenciamento]: http://manage.windowsazure.com
  [Encontrar disco de dados]: ./media/howto-detach-disk-windows-linux/FindDataDisks.png
  [Desanexar detalhes do disco]: ./media/howto-detach-disk-windows-linux/DetachDiskDetails.png
  [Como anexar um disco de dados a uma máquina virtual]: /en-us/manage/windows/how-to-guides/attach-a-disk/
