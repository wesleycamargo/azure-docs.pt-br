Em seguida, você criará um novo serviço móvel para substituir a lista na memória pelo armazenamento de dados. Siga estas etapas para criar um novo serviço móvel.

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].
2.  Na parte inferior do painel de navegação, clique em **+NOVO**.

    ![plus-new][plus-new]

3.  Expanda **Computação** e **Serviço Móvel** e, em seguida, clique em **Criar**.

    ![mobile-create][mobile-create]

    Isso exibe a caixa de diálogo **Novo Serviço Móvel**.

4.  Na página **Criar um serviço móvel**, selecione **Criar um Banco de Dados SQL gratuito com 20 MB** e, em seguida, digite um nome de subdomínio para o novo serviço móvel na caixa de texto **URL** e aguarde a verificação de nome. Uma vez concluída a verificação do nome, clique no botão de seta para a direita para ir para a próxima página.

    ![mobile-create-page1][mobile-create-page1]

    Isso exibe a página **Especificar configurações de banco de dados**.

    <div class="dev-callout"> 
    <b>Observa&ccedil;&atilde;o</b> 
	<p>Como parte deste tutorial, voc&ecirc; criar&aacute; um novo servidor e uma nova inst&acirc;ncia do Banco de Dados SQL. Voc&ecirc; pode reutilizar este novo banco de dados e administr&aacute;-lo como faria com qualquer outra inst&acirc;ncia de Banco de Dados SQL. Se voc&ecirc; j&aacute; tiver um banco de dados na mesma regi&atilde;o como novo servi&ccedil;o m&oacute;vel, voc&ecirc; poder&aacute; escolher <strong>Usar o banco de dados existente</strong> e, em seguida, selecionar esse banco de dados. O uso de um banco de dados em uma regi&atilde;o diferente n&atilde;o &eacute; recomendado devido a lat&ecirc;ncias maiores e aos custos adicionais de largura de banda.</p></div>

5.  Em **Nome**, digite o nome do novo banco de dados, digite **Nome de logon**, que é o nome de logon de administrator do novo servidor de Banco de Dados SQL, digite e confirme a senha e clique no botão de seleção para concluir o processo.

    ![mobile-create-page2][mobile-create-page2]

    <div class="dev-callout"> 
    <b>Observa&ccedil;&atilde;o</b> 
	<p>Ser&aacute; exibido um aviso se a senha fornecida n&atilde;o atender aos requisitos m&iacute;nimos ou em caso de incompatibilidade. <br/>Recomendamos que voc&ecirc; anote a senha e o nome de logon do administrador especificados. Voc&ecirc; precisar&aacute; dessas informa&ccedil;&otilde;es para reutilizar, no futuro, o servidor ou a inst&acirc;ncia do Banco de Dados SQL.</p> 
</div>

Você criou um novo serviço móvel que pode ser usado por seus aplicativos móveis. Em seguida, você adicionará uma nova tabela para armazenar dados do aplicativo. Essa tabela será usada pelo aplicativo em vez de coleção em memória.

  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [plus-new]: ./media/mobile-services-create-new-service-data/plus-new.png
  [mobile-create]: ./media/mobile-services-create-new-service-data/mobile-create.png
  [mobile-create-page1]: ./media/mobile-services-create-new-service-data/mobile-create-page1.png
  [mobile-create-page2]: ./media/mobile-services-create-new-service-data/mobile-create-page2.png
