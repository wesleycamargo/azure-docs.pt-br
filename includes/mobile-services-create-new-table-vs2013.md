

Antes de armazenar dados em seu novo serviço móvel, você deve criar uma nova tabela de armazenamento no serviço. Essas etapas mostram como usar o Visual Studio 2013 para criar uma nova tabela no serviço móvel. Em seguida, atualize o aplicativo para usar os Serviços Móveis para armazenar itens no Azure em vez de na coleção local.


1. Em Gerenciador de Servidores, expanda **Serviços Móveis do Azure**, clique duas vezes no serviço móvel, clique em **Criar Tabela** e, em seguida, digite  `TodoItem` no **Nome da Tabela**.

	![create table in VS 2013](./media/mobile-services-create-new-table-vs2013/mobile-create-table-vs2013.png)

2. Expanda **Avançado**, verifique se as permissões de operação padrão da tabela estão como **Qualquer pessoa que tiver a Chave do Aplicativo** e, em seguida, clique em **Criar**. 

	![create table in VS 2013 part 2](./media/mobile-services-create-new-table-vs2013/mobile-create-table-vs2013-2.png)

	Isso cria a tabela de TodoItem no servidor em que qualquer pessoa que tenha a chave de aplicativo pode acessar e alterar dados na tabela sem precisar ser autenticado primeiro. 

	>[AZURE.NOTE]A chave de aplicativo é distribuída com o aplicativo. Como essa chave não é distribuída com segurança, ela não pode ser considerada como um token de segurança. Para proteger o acesso aos seus dados de serviços móveis, você deve autenticar os usuários antes de acessar. Para obter mais informações, consulte [Permissões](http://msdn.microsoft.com/library/windowsazure/jj193161.aspx).




<!--HONumber=42-->
