O Azure Marketplace disponibiliza uma ampla gama de aplicativos Web populares desenvolvidos pela Microsoft, por outras empresas e por iniciativas de software livre. Os aplicativos Web criados no Azure Marketplace não exigem a instalação de qualquer software que não seja o navegador usado para se conectar ao [Portal de Visualização do Azure](http://go.microsoft.com/fwlink/?LinkId=529715).

Neste tutorial, você aprenderá:

- Como criar um novo aplicativo Web por meio do Azure Marketplace.

- Como implantar o aplicativo Web por meio do Portal de Visualização do Azure.
 
Você criará um blog do WordPress que usa um modelo padrão. A ilustração a seguir mostra o aplicativo concluído:


![Blog do WordPress][13]

>[AZURE.NOTE]Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

## Criar um aplicativo Web no portal

1. Faça logon no Portal de Visualização do Azure.

2. Abra o Azure Marketplace clicando no ícone do **Marketplace**:

    ![Ícone do Marketplace][marketplace]

    Ou clicando no ícone **Novo** na parte superior direita do painel de controle e selecionando **Marketplace** na parte inferior da lista.
	
    ![Criar Novo][5]
	
3. Selecione **Web + Celular**. Procure **WordPress** e clique no ícone **WordPress**.

	![WordPress da lista][7]
	
5. Depois de ler a descrição do aplicativo WordPress, selecione **Criar**.

6. Clique em **Aplicativo Web** e forneça os valores necessários para configurar seu aplicativo Web.
	
    ![configurar o aplicativo][8]

7. Clique em **Banco de Dados** e forneça os valores necessários para configurar seu banco de dados MySQL.

    ![configurar o banco de dados][database]

8. Forneça um nome para um novo grupo de recursos.

    ![Definir grupo de recursos][groupname]

8. Se necessário, clique em **ASSINATURA** e especifique a assinatura a ser usada.

7. Quando terminar de definir o aplicativo Web, clique em **Criar** e aguarde até que o novo aplicativo Web seja criado.

   Quando o aplicativo tiver sido criado, você verá o novo grupo de recursos que contém o banco de dados e o aplicativo Web.

   ![mostrar grupo][resourcegroup]

## Inicie e gerencie seu aplicativo Web do WordPress
	
1. Clique no seu novo aplicativo Web para ver detalhes sobre ele.

    ![iniciar painel][10]

2. Na página **Informações Gerais**, clique em **Procurar** no link em **Url** para abrir a página de boas-vindas do aplicativo Web.

    ![URL do site][browse]

3. Se você não tiver instalado o WordPress, insira as informações de configuração apropriadas exigidas pelo WordPress e clique em **Instalar WordPress** para finalizar a configuração e abra a página de logon do aplicativo Web.

4. Clique em **Logon** e insira suas credenciais.

5. Você terá um novo aplicativo Web do WordPress semelhante ao aplicativo Web abaixo.

	![seu site do WordPress][13]






[5]: ./media/website-from-gallery/start-marketplace.png
[6]: ./media/website-from-gallery/wordpressgallery-02.png
[7]: ./media/website-from-gallery/search-web-app.png
[8]: ./media/website-from-gallery/set-web-app.png
[9]: ./media/website-from-gallery/wordpressgallery-05.png
[10]: ./media/website-from-gallery/select-web.png
[13]: ./media/website-from-gallery/wordpressgallery-09.png
[webapps]: ./media/website-from-gallery/selectwebapps.png
[database]: ./media/website-from-gallery/set-db.png
[resourcegroup]: ./media/website-from-gallery/show-rg.png
[browse]: ./media/website-from-gallery/browse-web.png
[marketplace]: ./media/website-from-gallery/marketplace-icon.png
[groupname]: ./media/website-from-gallery/set-rg.png

<!---HONumber=August15_HO6-->