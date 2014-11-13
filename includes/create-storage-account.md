Você precisa de uma conta de armazenamento para usar o armazenamento do Azure. Você
pode criar uma conta de armazenamento seguindo as etapas a seguir. (Também é possível
criar uma conta de armazenamento usando a biblioteca de cliente de gerenciamento de serviços do Azure ou o gerenciamento de serviços [API REST]).

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].

2.  Na parte inferior do painel de navegação, clique em **NOVO**.

    ![+novo][+novo]

3.  Clique em **SERVIÇOS DE DADOS**, **ARMAZENAMENTO** e em **CRIAÇÃO RÁPIDA**.

    ![Caixa de diálogo Criação rápida][Caixa de diálogo Criação rápida]

4.  Na URL, digite um nome de subdomínio a ser usado no URI para a
    conta de armazenamento. A entrada pode conter de 3 a 24 letras minúsculas
    e números. Esse valor se torna o nome do host no URI que é
    usado para lidar com os recursos Blob, Fila ou Tabela para a
    assinatura.

5.  Escolha uma Região/Grupo de Afinidade no qual localizar o
    armazenamento. Se você estiver usando o armazenamento de seu aplicativo do Azure,
    selecione a mesma região na qual você implantará seu
    aplicativo.

6.  Você também pode selecionar o tipo de replicação que deseja para sua conta. A replicação com redundância geográfica é o padrão e oferece a máxima durabilidade. Para obter mais detalhes sobre as opções de replicação, consulte [Opções de Redundância de Armazenamento do Azure][Opções de Redundância de Armazenamento do Azure] e o [Blog da equipe de Armazenamento do Azure][Blog da equipe de Armazenamento do Azure].

7.  Clique em **CRIAR CONTA DE ARMAZENAMENTO**.

  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  [+novo]: ./media/create-storage-account/plus-new.png
  [Caixa de diálogo Criação rápida]: ./media/create-storage-account/quick-storage-2.png
  [Opções de Redundância de Armazenamento do Azure]: http://msdn.microsoft.com/pt-br/library/azure/dn727290.aspx
  [Blog da equipe de Armazenamento do Azure]: http://blogs.msdn.com/b/windowsazurestorage/
