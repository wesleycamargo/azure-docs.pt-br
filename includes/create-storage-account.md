## <a name="create-account"> </a>Criar uma conta de Armazenamento do Azure

Você precisa de uma conta de armazenamento para usar o armazenamento do Azure. Você 
pode criar uma conta de armazenamento seguindo essas etapas. (Você também pode
criar uma conta de armazenamento usando a biblioteca de cliente de gerenciamento de serviços do Azure ou o gerenciamento de serviços [API REST].)

1.  Faça logon no [Portal de Gerenciamento do Azure].

2.  Na parte inferior do painel de navegação, clique em **NOVO**.

	![+new][plus-new]

3.  Clique em **SERVIÇOS DE DADOS**, **ARMAZENAMENTO** e em **CRIAÇÃO RÁPIDA**.

	![Quick create dialog][quick-create-storage]

4.  Na URL, digite um nome de subdomínio para utilizar no URI para a
    conta de armazenamento. A entrada pode conter de 3 a 24 letras minúsculas
    e números. Este valor se torna o nome do host dentro do URI que é
    usado para endereçar recursos de Blob, Fila ou Tabela para a
    assinatura.

5.  Escolha uma Região/Grupo de Afinidade no qual localizar o
    armazenamento. Se você usar o armazenamento por meio de seu
    aplicativo do Azure, selecione a mesma região onde você implantará o
    aplicativo.

6. Você também pode selecionar o tipo de replicação que deseja para sua conta. A replicação com redundância geográfica é o padrão e oferece a máxima durabilidade. Para obter mais detalhes sobre as opções de replicação, consulte [Opções de Redundância de Armazenamento do Azure](http://msdn.microsoft.com/library/azure/dn727290.aspx) e o [Blog da equipe de Armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/).

6.  Clique em **CRIAR CONTA DE ARMAZENAMENTO**.

[usando a API REST]: http://msdn.microsoft.com/library/windowsazure/hh264518.aspx
[Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
[plus-new]: ./media/create-storage-account/plus-new.png
[quick-create-storage]: ./media/create-storage-account/quick-storage-2.png
<!--HONumber=42-->
