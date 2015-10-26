4. Em outra janela ou guia do navegador, vá para o [Portal de visualização do Azure](https://portal.azure.com).

3. Vá para a folha **Aplicativo de API** do seu conector de Dropbox. (Se você ainda estiver na folha **Grupo de Recursos**, basta clicar no conector no Dropbox no diagrama.)

4. Clique em **Configurações**, e na folha **Configurações** clique em **Autenticação**.

	![Clique em Configurações](./media/app-service-api-exchange-dropbox-settings/clicksettings.png)

	![Clique em Autenticação](./media/app-service-api-exchange-dropbox-settings/clickauth.png)

5. Na folha Autenticação, insira a ID do cliente e o segredo do cliente do site do Dropbox e, em seguida, clique em **Salvar**.

	![Insira as configurações e clique em Salvar](./media/app-service-api-exchange-dropbox-settings/authblade.png)

3. Copie o **URI de Redirecionamento** (a caixa cinza acima da ID do cliente e do segredo do cliente) e adicione o valor à página deixada aberta na etapa anterior.

	O URI de redirecionamento segue este padrão:

		[gatewayurl]/api/consent/redirect/[connectorname]

	Por exemplo:

		https://dropboxrgaeb4ae60b7.azurewebsites.net/api/consent/redirect/DropboxConnector

	![Obter URI de redirecionamento](./media/app-service-api-exchange-dropbox-settings/redirecturi.png)

	![Criar aplicativo Dropbox](./media/app-service-api-exchange-dropbox-settings/dbappsettings2.png)

<!---HONumber=Oct15_HO3-->