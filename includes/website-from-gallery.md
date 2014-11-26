A galeria disponibiliza uma ampla gama de aplicativos populares da Web desenvolvidos pela Microsoft, outras empresas e iniciativas de software de código aberto. Os aplicativos da Web criados a partir da galeria não exigem a instalação de qualquer software que não seja o navegador usado para se conectar ao Portal de Gerenciamento do Azure.

Neste tutorial, você aprenderá:

-   Como criar um novo site por meio da galeria.

- Como implantar o site por meio do Portal do Azure.

Você criará um blog do WordPress que usa um modelo padrão. A ilustração a seguir mostra o aplicativo concluído:

![Blog do WordPress][Blog do WordPress]

<div class="dev-callout"><strong>Observa&ccedil;&atilde;o</strong>
<p>Para concluir este tutorial, voc&ecirc; precisa de uma conta do Azure. Voc&ecirc; pode criar uma conta de avalia&ccedil;&atilde;o gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/develop/php/tutorials/create-a-windows-azure-account/" target="_blank">Criar uma conta do Azure</a>.</p>
</div>

## Criar um site no portal

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].

2.  Clique no ícone **Novo** na parte inferior esquerda do painel.

    ![Criar Novo][Criar Novo]

3.  Clique no ícone **Site**, então clique em **Da Galeria**.

    ![Criar Da Galeria][Criar Da Galeria]

4.  Localize e clique no ícone do WordPress na lista e, em seguida, clique em **Próximo**.

    ![WordPress da lista][WordPress da lista]

5.  Na página **Configurar seu aplicativo**, insira ou selecione valores para todos os campos:

 -   Insira um nome de URL de sua escolha
 -   Deixe **Criar um novo banco de dados MySQL** selecionado no campo **Banco de dados**
 -   Selecione a região mais próxima de você

    ![configurar o aplicativo][configurar o aplicativo]

6.  Em seguida, clique em **Próximo**.

7.  Na página **Criar Novo Banco de Dados**, você pode especificar um nome para o novo banco de dados MySQL ou usar o nome padrão. Selecione a região mais próxima de você como o local de hospedagem. Marque a caixa na parte inferior da tela para concordar com os termos de uso do ClearDB do banco de dados MySQL hospedado. Em seguida, clique em verificar para concluir a criação do site.

    ![criar banco de dados][criar banco de dados]

Após você clicar em **Concluir**, a Azure iniciará o processo de compilar e implantar as operações. Enquanto o site estiver sendo criado e implantado, o status dessas operações será exibido na parte inferior da página de Sites. Depois que todas as operações forem realizadas, será exibida uma mensagem de status final, quando o site for implantado com êxito.

## Inicie e gerencie seu site do WordPress

1.  Clique no novo site da página **Sites** para abrir o painel do site.

    ![iniciar painel][iniciar painel]

2.  Na página de gerenciamento **Painel**, role para baixo e clique no link à esquerda em **Url do Site** para abrir a página de boas-vindas do site.

    ![URL do site][URL do site]

3.  Insira as informações de configuração apropriadas exigidas pelo WordPress e clique em **Instalar WordPress** para finalizar a configuração e abra a página de logon do site.

    ![logon no WordPress][logon no WordPress]

4.  Faça logon no novo site do WordPress, inserindo o nome de usuário e a senha que você especificou na página de **Boas-vindas**.

5.  Você terá um novo site do WordPress, parecido com o site a seguir.

    ![seu site do WordPress][Blog do WordPress]

  [Blog do WordPress]: ./media/website-from-gallery/wordpressgallery-09.png
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  [Criar Novo]: ./media/website-from-gallery/wordpressgallery-01.png
  [Criar Da Galeria]: ./media/website-from-gallery/wordpressgallery-02.png
  [WordPress da lista]: ./media/website-from-gallery/wordpressgallery-03.png
  [configurar o aplicativo]: ./media/website-from-gallery/wordpressgallery-04.png
  [criar banco de dados]: ./media/website-from-gallery/wordpressgallery-05.png
  [iniciar painel]: ./media/website-from-gallery/wordpressgallery-06.png
  [URL do site]: ./media/website-from-gallery/wordpressgallery-07.png
  [logon no WordPress]: ./media/website-from-gallery/wordpressgallery-08.png
