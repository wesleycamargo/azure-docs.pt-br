Siga estas etapas para criar um novo serviço móvel.

1.  Faça logon no [Portal de Gerenciamento][Portal de Gerenciamento].

2.  Na parte inferior do painel de navegação, clique em **+NOVO**.

    ![][0]

3.  Expanda **Computação** e **Serviço Móvel** e, em seguida, clique em **Criar**.

    ![][1]

    Isso exibe a caixa de diálogo **Novo Serviço Móvel**.

4.  Na página **Criar um serviço móvel**, selecione **Criar um Banco de Dados SQL gratuito com 20 MB**, selecione tempo de execução **Node.js**. Em seguida, digite um nome de subdomínio para o novo serviço móvel na caixa de texto **URL** e aguarde a verificação de nome. Uma vez concluída a verificação do nome, clique no botão de seta para a direita para ir para a próxima página.

    ![][2]

    Isso exibe a página **Especificar configurações de banco de dados**.

    <div class="dev-callout">

    **Observação**
    Como parte deste tutorial, você criará um novo servidor e uma nova instância do Banco de Dados SQL. Você pode reutilizar este novo banco de dados e administrá-lo como faria com qualquer outra instância de Banco de Dados SQL. Se você já tiver um banco de dados na mesma região como novo serviço móvel, você poderá escolher **Usar o banco de dados existente** e, em seguida, selecionar esse banco de dados. O uso de um banco de dados em uma região diferente não é recomendado devido a latências maiores e aos custos adicionais de largura de banda.

    </div>

5.  Em **Nome**, digite o nome do novo banco de dados, digite **Nome de logon**, que é o nome de logon de administrator do novo servidor de Banco de Dados SQL, digite e confirme a senha e clique no botão de seleção para concluir o processo.

    ![][3]

    <div class="dev-callout">

    **Observação**
    Será exibido um aviso se a senha fornecida não atender aos requisitos mínimos ou em caso de incompatibilidade.
    Recomendamos que você anote a senha e o nome de logon do administrador especificados. Você precisará dessas informações para reutilizar, no futuro, o servidor ou a instância do Banco de Dados SQL.

    </div>

Você criou um novo serviço móvel que pode ser usado por seus aplicativos móveis.



  [Portal de Gerenciamento]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-create-new-service/plus-new.png
  [1]: ./media/mobile-services-create-new-service/mobile-create.png
  [2]: ./media/mobile-services-create-new-service/mobile-create-page1.png
  [3]: ./media/mobile-services-create-new-service/mobile-create-page2.png
