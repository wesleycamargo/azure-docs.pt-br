

As etapas a seguir criam um novo serviço móvel no Azure e adicionam o código ao seu projeto que permite o acesso a esse novo serviço. Antes de criar o serviço móvel, você deve importar o arquivo publishsettings da sua assinatura do Azure no Visual Studio. Isso permite que o Visual Studio se conecte ao Azure em seu nome. Ao criar um novo serviço móvel, você deve especificar um Banco de Dados SQL do Azure que será usado pelo serviço móvel para armazenar dados do aplicativo. 


1. No Visual Studio 2013, abra o Gerenciador de Soluções, clique com o botão direito no projeto, clique em **Adicionar** e, em seguida, em **Serviço Conectado**. 

	![adicionar serviço conectado](./media/mobile-services-create-new-service-vs2013/mobile-add-connected-service.png)

2. Na caixa de diálogo Gerenciador de serviços, clique em **Criar serviço...**, em seguida selecione **&lt;Importar...&gt;** de  **Assinatura** na caixa de diálogo Criar Serviço Móvel.  

	![criar um novo serviço móvel do VS 2013](./media/mobile-services-create-new-service-vs2013/mobile-create-service-from-vs2013.png)

3. Em Importar Assinaturas do Azure, clique em **Baixar arquivo de assinatura**, efetue login em sua conta do Azure (se necessário), clique em **Salvar** quando o navegador solicitar para salvar o arquivo.

	![baixe o arquivo de inscrição no VS](./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription.png)

	<div class="dev-callout"><strong>Nota</strong> <p>a janela de login é exibida no navegador, o que pode estar por trás da janela do Visual Studio. Lembre-se de anotar onde salvou o arquivo. publishsettings baixado. Você pode ignorar esta etapa se o projeto já estiver conectado à sua assinatura do Azure.</p></div> 

4. Clique em **Procurar**, navegue para o local em que salvou o arquivo .publishetings, selecione o arquivo, clique em **Abrir** e então em **Importar**. 

	![importar assinatura no VS](./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription-2.png)

	O Visual Studio importa os dados necessários para conectar-se a sua assinatura do Azure. Quando sua assinatura já tem um ou mais serviços móveis existentes, são exibidos os nomes de serviço. 

	<div class="dev-callout"><strong>Nota de segurança</strong> <p>depois de importar as configurações de publicação, considere a possibilidade de excluir o arquivo. publishsettings baixado devido ao fato de ele conter informações que podem ser usadas por outras pessoas para acessar sua conta. Proteja o arquivo se você pretende mantê-la para uso em outros projetos de aplicativo conectado.</p></div>

5. Na caixa de diálogo **Criar Serviço Móvel**, selecione sua **Assinatura** e a **Região** desejada para o serviço móvel e, em seguida, digite um **Nome** para o seu serviço móvel.

	<div class="dev-callout"><strong>Nota</strong> <p>nomes de serviço móvel devem ser exclusivos. Um X vermelho é exibido ao lado de <strong>Nome</strong> quando o nome fornecido não está disponível. </p></div>		

6. Na **Banco de dados**, selecione **&lt;Criar um Banco de Dados SQL gratuito&gt;**, forneça o **Nome de usuário do servidor**, a **Senha do servidor**, a **Confirmação de senha do servidor** e, em seguida, clique em **Criar**.

  	![criar serviço de VS 2013 parte 2](./media/mobile-services-create-new-service-vs2013/mobile-create-service-from-vs2013-2.png)


	> [WACOM.NOTE]
	> Como parte deste tutorial, você cria novos servidor e instância de Banco de Dados SQL gratuitos. Você pode reutilizar este novo banco de dados e administrá-lo como faria com qualquer outra instância de Banco de Dados SQL. Você só pode ter uma instância de banco de dados gratuito. Se você já tiver um banco de dados na mesma região como novo serviço móvel, você poderá escolher o banco de dados existente. Ao escolher um banco de dados existente, certifique-se de que você forneça as credenciais de login corretas. Se você fornecer credenciais de login incorreto, o serviço móvel é criado em um estado não íntegro.

   	Depois que o serviço móvel é criado, uma referência à biblioteca de cliente de Serviços Móveis é adicionada ao projeto e o código-fonte do seu projeto é atualizado.


