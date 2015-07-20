

As etapas a seguir criam um novo serviço móvel no Azure e adicionam o código ao seu projeto que conectam seu aplicativo a esse novo serviço. O Visual Studio 2013 se conecta ao Azure por meio da criação do novo serviço móvel, ao usar as credenciais que você forneceu. Ao criar um novo serviço móvel, especifique um Banco de Dados SQL do Azure que será usado pelo serviço móvel para armazenar dados do aplicativo.


1. No Visual Studio 2013, abra o Gerenciador de Soluções, clique com o botão direito do mouse no projeto do aplicativo do Windows Store, clique em **Adicionar**e, em seguida, em **Serviço Conectado...**.  

2. No diálogo Gerenciador de Serviços, clique em **Criar serviço...**, selecione **&lt;Gerir...&gt;** em **Assinatura** na caixa de diálogo Criar Serviço Móvel.

	![criar serviço de gerenciar assinaturas](./media/mobile-services-create-new-service-vs2013/mobile-create-service-from-vs2013.png)

3. Em Gerenciar Assinaturas do Microsoft Azure, clique em **Entrar...** para entrar em sua conta Azure (se necessário), selecione uma assinatura disponível e clique em **Fechar**.

	Quando sua assinatura já tem um ou mais serviços móveis existentes, são exibidos os nomes de serviço.

5. Na caixa de diálogo **Criar serviço móvel**, selecione sua **Assinatura**, o back-end do **JavaScript** em **Tempo de execução** e uma **Região** para seu serviço móvel, e digite um **Nome** para o serviço móvel.

	>[AZURE.NOTE]Nomes de serviço móvel devem ser exclusivos. Um X vermelho é exibido ao lado de **Nome** quando o nome fornecido não está disponível.

6. Em **Banco de Dados**, selecione **&lt;Criar um Banco de Dados SQL gratuito;**, forneça o **Nome de usuário do servidor**, a **Senha do servidor** e a **Confirmação de senha do servidor**. Depois, clique em **Criar**.

  	![criar novo serviço móvel no VS 2013](./media/mobile-services-create-new-service-vs2013/mobile-create-service-from-vs2013-2.png)


	> [AZURE.NOTE]Como parte deste tutorial, você cria novos servidor e instância de Banco de Dados SQL gratuitos. Você pode reutilizar este novo banco de dados e administrá-lo como faria com qualquer outra instância de Banco de Dados SQL. Você só pode ter uma instância de banco de dados gratuito. Se você já tiver um banco de dados na mesma região como novo serviço móvel, você poderá escolher o banco de dados existente. Ao escolher um banco de dados existente, certifique-se de que você forneça as credenciais de login corretas. Se você fornecer credenciais de login incorreto, o serviço móvel é criado em um estado não íntegro.

7. Depois que o serviço móvel é criado, selecione o serviço móvel criado mais recentemente da lista no Service Manager e clique em **OK**.

	Depois que o assistente é concluído, os pacotes NuGet necessários são instalados, uma referência à biblioteca de cliente de Serviços Móveis é adicionada ao projeto e o código-fonte do seu projeto é atualizado.

<!---HONumber=July15_HO2-->