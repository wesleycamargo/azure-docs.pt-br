

Em seguida, você criará um novo serviço móvel para substituir a lista na memória pelo armazenamento de dados. Siga estas etapas para criar um novo serviço móvel.

1. Faça logon no [portal clássico do Azure](https://manage.windowsazure.com/). 
2.	Na parte inferior do painel de navegação, clique em **+NOVO**.

	![plus-new](./media/mobile-services-create-new-service-data/plus-new.png)

3.	Expanda **Computação** e **Serviço Móvel** e, em seguida, clique em **Criar**.

	![mobile-create](./media/mobile-services-create-new-service-data/mobile-create.png)

    Isso exibe a caixa de diálogo **Novo Serviço Móvel**.

4.	Na página **Criar um serviço móvel**, selecione **Criar um Banco de Dados SQL gratuito com 20 MB** e, em seguida, digite um nome de subdomínio para o novo serviço móvel na caixa de texto **URL** e aguarde a verificação de nome. Uma vez concluída a verificação do nome, clique no botão de seta para a direita para ir para a próxima página.

	![mobile-create-page1](./media/mobile-services-create-new-service-data/mobile-create-page1.png)

    Isso exibe a página **Especificar configurações de banco de dados**.

    
	> [AZURE.NOTE]Como parte deste tutorial, você criará um novo servidor e uma nova instância do Banco de Dados SQL. Você pode reutilizar este novo banco de dados e administrá-lo como faria com qualquer outra instância de Banco de Dados SQL. Se você já tiver um banco de dados na mesma região como novo serviço móvel, você poderá escolher **Usar o banco de dados existente** e, em seguida, selecionar esse banco de dados. O uso de um banco de dados em uma região diferente não é recomendado devido a latências maiores e aos custos adicionais de largura de banda.

5.	Em **Nome**, digite o nome do novo banco de dados, digite **Nome de logon**, que é o nome de logon de administrador do novo servidor de Banco de Dados SQL, digite e confirme a senha e clique no botão de seleção para concluir o processo.

	![mobile-create-page2](./media/mobile-services-create-new-service-data/mobile-create-page2.png)

    
	> [AZURE.NOTE]Será exibido um aviso se a senha fornecida não atender aos requisitos mínimos ou em caso de incompatibilidade.
	>
	> Recomendamos que você anote a senha e o nome de logon do administrador especificados. Você precisará dessas informações para reutilizar, no futuro, o servidor ou a instância do Banco de Dados SQL.

Você criou um novo serviço móvel que pode ser usado por seus aplicativos móveis. Em seguida, você adicionará uma nova tabela para armazenar dados do aplicativo. Essa tabela será usada pelo aplicativo em vez de coleção em memória.

<!---HONumber=AcomDC_1203_2015-->