Para usar as operações de armazenamento, você precisa de uma conta de armazenamento do Windows Azure. Você pode criar uma conta de armazenamento seguindo essas etapas. (Você também pode criar uma conta de armazenamento [usando a API REST].)

1.  Faça logon no [Portal de Gerenciamento do Windows Azure].

2.  Na parte inferior do painel de navegação, clique em **NOVO**:

	![+novo][plus-new]

3.  Clique em **SERVIÇOS DE DADOS**, **ARMAZENAMENTO** e em **CRIAÇÃO RÁPIDA**.

	![Caixa de diálogo Criação rápida][quick-create-storage]

4.  No URL, digite um nome de subdomínio para usar no URI para a conta de armazenamento. A entrada pode conter de 3 a 24 letras minúsculas e números. Esse valor se torna o nome do host no URI que é usado para lidar com os recursos Blob, Fila ou Tabela da assinatura.

5.  Escolha uma Região/Grupo de Afinidade no qual deseja localizar o armazenamento. Se usar o armazenamento de seu aplicativo do Windows Azure, selecione a mesma região onde você implantará seu aplicativo.

6. Opcionalmente, você pode habilitar a replicação geográfica.

6.  Clique em **CRIAR CONTA DE ARMAZENAMENTO**.

[usando a API REST]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh264518.aspx
[Portal de Gerenciamento do Windows Azure]: http://manage.windowsazure.com
[plus-new]: ./media/create-storage-account/plus-new.png
[quick-create-storage]: ./media/create-storage-account/quick-storage-2.png

