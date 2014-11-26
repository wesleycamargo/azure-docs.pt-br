1.  No Visual Studio, abra o projeto que você modificou quando concluiu o tutorial **Introdução aos dados**.

2.  Pressione a tecla **F5** para executar o aplicativo e, em seguida, digite texto em **Inserir um TodoItem** e clique em **Salvar**.

3.  Repita a etapa anterior, pelo menos três vezes, para que você tenha mais de três itens armazenados na tabela TodoItem.

4.  Abra o arquivo default.js e substitua o método **RefreshTodoItems** pelo seguinte código:

        var refreshTodoItems = function () {
            // Define a filtered query that returns the top 3 items.
            todoTable.where({ complete: false })
                .take(3)
                .read()
                .done(function (results) {
                    todoItems = new WinJS.Binding.List(results);
                    listItems.winControl.itemDataSource = todoItems.dataSource;
                });
        };

    Essa consulta, quando executada durante a associação de dados, retorna os três primeiros itens que não estiverem marcados como concluídos.

5.  Pressione a tecla **F5** para executar o aplicativo.

    Observe que apenas os três primeiros resultados da tabela TodoItem são exibidos.

6.  (Opcional) Exiba o URI da solicitação enviada ao serviço móvel usando software de inspeção de mensagem, como as ferramentas de desenvolvedor do navegador ou o [Fiddler][Fiddler].

    Observe que o método **take(3)** foi convertido na opção de consulta **$top=3** no URI da consulta.

7.  Atualize o método **RefreshTodoItems** mais uma vez com o código a seguir:

        var refreshTodoItems = function () {
            // Define a filtered query that skips the first 3 items and 
            // then returns the next 3 items.
            todoTable.where({ complete: false })
                .skip(3)
                .take(3)
                .read()
                .done(function (results) {
                    todoItems = new WinJS.Binding.List(results);
                    listItems.winControl.itemDataSource = todoItems.dataSource;
                });
        };

    Essa consulta ignora os três primeiros resultados e retorna os três seguintes. Essa é efetivamente a segunda "página" de dados, onde o tamanho da página é de três itens.

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>Este tutorial usa um cen&aacute;rio simplificado passando valores de pagina&ccedil;&atilde;o embutidos em c&oacute;digo para os m&eacute;todos <strong>Take</strong> e <strong>Skip</strong>. Em um aplicativo do mundo real, voc&ecirc; pode usar consultas semelhantes &agrave;s mencionadas acima com um controle de pagina&ccedil;&atilde;o ou interface do usu&aacute;rio compar&aacute;vel para permitir que os usu&aacute;rios naveguem para p&aacute;ginas anteriores e posteriores.  Voc&ecirc; tamb&eacute;m pode chamar o m&eacute;todo <strong>includeTotalCount</strong> para obter a contagem total dos itens dispon&iacute;veis no servidor, juntamente com os dados paginados.</p>
</div>

8.  (Opcional) Novamente, você pode ver o URI da solicitação enviada ao serviço móvel.

    Observe que o método **skip(3)** foi convertido na opção de consulta **$skip=3** no URI da consulta.

<!-- URLs -->

  [Fiddler]: http://go.microsoft.com/fwlink/?LinkID=262412
