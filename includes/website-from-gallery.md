A galeria disponibiliza uma ampla gama de aplicativos Web populares desenvolvidos pela Microsoft, por outras empresas e através de iniciativas de software livre. Os aplicativos da Web criados por meio da galeria não exigem a instalação de qualquer software que não seja o navegador usado para se conectar ao Portal de Gerenciamento do Azure. 

Neste tutorial, você aprenderá:

- Como criar um novo site por meio da galeria.

- Como implantar o site por meio do Portal do Azure.
 
Você criará um blog do WordPress que usa um modelo padrão. A ilustração a seguir mostra o aplicativo concluído:


![Wordpress blog][13]

> [AZURE.IMPORTANTE] Para concluir este tutorial, você precisa de uma conta do Azure. Você pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Criar uma conta do Azure](http://azure.microsoft.com/develop/php/tutorials/create-a-windows-azure-account/"%20target="_blank").

## Criar um site no portal

1. Faça logon no [Portal de Gerenciamento do Azure](http://manage.windowsazure.com).

2. Clique no ícone **Novo** na parte inferior esquerda do painel.
	
	![Create New][5]

3. Clique no ícone **Site**, então clique em **Da Galeria**.
	
	![Create From Gallery][6]

4. Localize e clique no ícone do WordPress na lista e, em seguida, clique em **Próximo**.
	
	![WordPress from list][7]

5. Na página **Configurar seu aplicativo**, insira ou selecione valores para todos os campos:
	
- Insira um nome de URL de sua escolha	
- Deixe **Criar um novo banco de dados MySQL** selecionado no campo **Banco de dados**
- Selecione a região mais próxima de você

	![configure your app][8]

6. Em seguida, clique em **Próximo**.

7. Na página **Criar Novo Banco de Dados**, você pode especificar um nome para o novo banco de dados MySQL ou usar o nome padrão. Selecione a região mais próxima de você como o local de hospedagem. Marque a caixa na parte inferior da tela para concordar com os termos de uso do ClearDB do banco de dados MySQL hospedado. Em seguida, clique em verificar para concluir a criação do site. 
	
	![create database][9]

Após você clicar em **Concluir**, a Azure iniciará o processo de compilar e implantar as operações. Enquanto o site estiver sendo criado e implantado, o status dessas operações será exibido na parte inferior da página de Sites. Depois que todas as operações forem realizadas, será exibida uma mensagem de status final, quando o site for implantado com êxito.

## Inicie e gerencie seu site do WordPress

1. Clique no novo site da página **Sites** para abrir o painel do site.

	![launch dashboard][10]

2. Na página de gerenciamento **Painel**, role para baixo e clique no link à esquerda em **Url do Site** para abrir a página de boas-vindas do site.

	![site URL][11] 

3. Insira as informações de configuração apropriadas exigidas pelo WordPress e clique em **Instalar WordPress** para finalizar a configuração e abra a página de logon do site.

	![login to WordPress][12]

4. Faça logon no novo site do WordPress, inserindo o nome de usuário e a senha que você especificou na página de **Boas-vindas**.

5. Você terá um novo site do WordPress, parecido com o site a seguir.  

	![your WordPress site][13]






[5]: ./media/website-from-gallery/wordpressgallery-01.png
[6]: ./media/website-from-gallery/wordpressgallery-02.png
[7]: ./media/website-from-gallery/wordpressgallery-03.png
[8]: ./media/website-from-gallery/wordpressgallery-04.png
[9]: ./media/website-from-gallery/wordpressgallery-05.png
[10]: ./media/website-from-gallery/wordpressgallery-06.png

[13]: ./media/website-from-gallery/wordpressgallery-09.png





<!--HONumber=42-->
