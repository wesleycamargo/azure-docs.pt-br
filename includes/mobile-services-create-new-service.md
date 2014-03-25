

Siga estas etapas para criar um novo serviço móvel.

1.	Faça logon no [Portal de Gerenciamento]. 

2.	Na parte inferior do painel de navegação, clique em **+NOVO**.

	![](./media/mobile-services-create-new-service/plus-new.png)

3.	Expanda **Computação** e **Serviço Móvel** e, em seguida, clique em **Criar**.

	![](./media/mobile-services-create-new-service/mobile-create.png)

	Isso exibe a caixa de diálogo **Novo Serviço Móvel**.

4.	Na página **Criar um serviço móvel**, selecione **Criar um Banco de Dados SQL gratuito com 20 MB**, selecione tempo de execução **Node.js**. Em seguida, digite um nome de subdomínio para o novo serviço móvel na caixa de texto **URL** e aguarde a verificação de nome. Uma vez concluída a verificação do nome, clique no botão de seta para a direita para ir para a próxima página.	

	![](./media/mobile-services-create-new-service/mobile-create-page1.png)

   	Isso exibe a página **Especificar configurações de banco de dados**.

	<div class="dev-callout"> 
	<b>Observação</b> 
	<p>Como parte deste tutorial, você cria uma nova instância de Banco de Dados SQL e o servidor. Você pode reutilizar este novo banco de dados e administrá-lo como faria com qualquer outra instância de Banco de Dados SQL. Se você já tiver um banco de dados na mesma região como novo serviço móvel, você poderá escolher <strong>Usar o banco de dados existente</strong> e, em seguida, selecionar esse banco de dados. O uso de um banco de dados em uma região diferente não é recomendado devido a latências maiores e aos custos de largura de banda adicional.</p></div>	

6.	Em **Nome**, digite o nome do novo banco de dados, digite **Nome de logon**, que é o nome de logon de administrator do novo servidor de Banco de Dados SQL, digite e confirme a senha e clique no botão de seleção para concluir o processo.

	![](./media/mobile-services-create-new-service/mobile-create-page2.png)

	<div class="dev-callout"> 
	<b>Observação</b> 
	<p>Será exibido um aviso caso a senha fornecida não atenda aos requisitos mínimos ou em caso de incompatibilidade. <br/>Recomendamos que você anote o nome de logon de administrador e a senha que você especificar. Você precisará dessas informações para reutilizar a instância de Banco de Dados SQL ou o servidor no futuro.</p> 
	</div>

Você criou um novo serviço móvel que pode ser usado por seus aplicativos móveis.



<!-- URLs. -->
[Portal de Gerenciamento]: https://manage.windowsazure.com/

