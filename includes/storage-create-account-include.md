## Criar uma conta de Armazenamento do Azure

Você precisa de uma conta de armazenamento para usar o armazenamento do Azure. Você pode criar uma conta de armazenamento seguindo essas etapas. \(Você também pode criar uma conta de armazenamento usando a biblioteca do cliente de gerenciamento de serviço do Azure ou o gerenciamento de serviço [API REST].\)

1.  Faça logon no [Portal de Gerenciamento do Azure].

2.  Na parte inferior do painel de navegação, clique em **NOVO**.

	![\+novo][plus-new]

3.  Clique em **SERVIÇOS DE DADOS**, **ARMAZENAMENTO** e em **CRIAÇÃO RÁPIDA**.

	![Caixa de diálogo Criação rápida][quick-create-storage]

4.  Na URL, digite um nome de subdomínio a ser usado na URI para a conta de armazenamento. A entrada pode conter de 3 a 24 letras minúsculas e números. Esse valor torna-se o nome de host no URI que é usado para lidar com os recursos de Blob, Fila ou Tabela da assinatura em questão.

5.  Escolha uma Região/Grupo de Afinidade no qual localizar o armazenamento. Se você usar o armazenamento de seu aplicativo do Azure, selecione a mesma região onde você implantará seu aplicativo.

6. Você também pode selecionar o tipo de replicação que deseja para sua conta. A replicação com redundância geográfica é o padrão e oferece a máxima durabilidade. Para obter mais detalhes sobre as opções de replicação, consulte [Opções de Redundância de Armazenamento do Azure](http://msdn.microsoft.com/library/azure/dn727290.aspx) e o [Blog da equipe de Armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/).

6.  Clique em **CRIAR CONTA DE ARMAZENAMENTO**.

[API REST]: http://msdn.microsoft.com/library/azure/hh264518.aspx
[Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
[plus-new]: ./media/storage-create-account-include/plus-new.png
[quick-create-storage]: ./media/storage-create-account-include/quick-storage-2.png

<!--HONumber=52-->
