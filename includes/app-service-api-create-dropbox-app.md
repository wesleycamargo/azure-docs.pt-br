As etapas a seguir mostram o processo de criação de um aplicativo Dropbox usando o site Dropbox.com. Como o site Dropbox.com pode ser alterado sem aviso, você poderá ver diferenças entre a interface do usuário e o que é exibido.

1. Vá para o [Portal do desenvolvedor do Dropbox](https://www.dropbox.com/developers/apps), clique em **Console do Aplicativo**, e, em seguida, clique em **Criar Aplicativo**.

	![Criar aplicativo Dropbox](./media/app-service-api-create-dropbox-app/dbappcreate.png)

2. Escolha **Aplicativo de API do Dropbox** e defina outras configurações.
 
	As opções de acesso do arquivo mostradas na captura de tela abaixo permitem testar o acesso à sua conta do Dropbox com uma simples solicitação Get de HTTP, se você tiver arquivos em sua conta.

	O nome do aplicativo de API do Dropbox pode ser qualquer um e o site do Dropbox aceitará.

3. Clique em **Criar aplicativo**.

	![Criar aplicativo Dropbox](./media/app-service-api-create-dropbox-app/dbapiapp.png)

	A próxima página mostra a chave do aplicativo e configurações confidenciais do aplicativo (ou seja, ID do cliente e o segredo do cliente no Azure) que você usará para configurar o conector de Dropbox do Azure.

	Essa página também tem um campo onde você pode inserir um URI de redirecionamento, o valor que você obterá na próxima seção.

	![Criar aplicativo Dropbox](./media/app-service-api-create-dropbox-app/dbappsettings.png)

<!---HONumber=August15_HO6-->