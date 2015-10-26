Após os registros do nome de domínio terem sido propagados, você deverá associá-lo ao aplicativo Web. Use as etapas a seguir para habilitar os nomes de domínio usando o navegador.

> [AZURE.NOTE]Pode levar algum tempo para os registros CNAME criados nas etapas anteriores para propagar no sistema DNS. Não é possível adicionar o nome do domínio do seu aplicativo Web até o CNAME ser propagado. Se estiver usando um registro A, você não poderá adicionar o nome de domínio do registro A ao aplicativo Web até o registro CNAME **awverify** criado na etapa anterior ser propagado.
>
> É possível usar um serviço como <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> para verificar se o CNAME está disponível.

1. No seu navegador, abra o [Portal de Gerenciamento do Azure](https://portal.azure.com).

2. Na guia **Aplicativos Web**, clique no nome do seu aplicativo Web, selecione **Configurações** e, em seguida, selecione **Domínios personalizados e SSL**

	![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3. Na folha **Domínios personalizados e SSL**, clique em **Trazer domínios externos**.

	![](./media/custom-dns-web-site/dncmntask-cname-7.png)

4. Use as caixas de texto **NOMES DE DOMÍNIO** para inserir os nomes de domínio a serem associados a este aplicativo Web.

	![](./media/custom-dns-web-site/dncmntask-cname-8.png)

5. Clique em **Salvar** para salvar a configuração de nome de domínio.

	Depois que a configuração estiver concluída, o nome do domínio personalizado será listado na seção **nomes de domínio** de seu aplicativo Web.

Nesse ponto, você deve poder inserir o nome de domínio personalizado no navegador e ver se ele te leva com êxito ao aplicativo Web.

<!---HONumber=Oct15_HO3-->