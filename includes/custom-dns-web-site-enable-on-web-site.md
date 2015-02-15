Após os registros do nome de domínio serem propagados, você deverá associá-los ao seu site. Use as etapas a seguir para habilitar os nomes de domínio usando o navegador.

> [AZURE.NOTE] Pode levar algum tempo para os registros CNAME criados nas etapas anteriores para propagar no sistema DNS. Não é possível adicionar o nome do domínio do Site do Azure até o CNAME ser propagado. Se estiver usando um registro A, você não poderá adicionar o nome de domínio do registro A ao Site do Azure até o registro CNAME **awverify** criado na etapa anterior ser propagado.
> 
> Você pode usar um serviço como <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> para verificar se o CNAME está disponível.

1. No seu navegador, abra o [Portal de Gerenciamento do Azure](https://manage.windowsazure.com).

2. Na guia **Sites**, clique no nome do seu site, selecione **Painel** e, em seguida, selecione **Gerenciar Domínios** na parte inferior da página.

	![](./media/custom-dns-web-site/dncmntask-cname-6.png)

6. Use as caixas de texto **NOMES DE DOMÍNIO** para inserir os nomes de domínio a serem associados a este site. 

	![](./media/custom-dns-web-site/dncmntask-cname-7.png)

6. Clique na marca de seleção no canto inferior direito para salvar a configuração do nome de domínio.

	Após a conclusão da configuração, o nome de domínio personalizado será listado na seção **nomes de domínio** da página **Configurar** do seu site.

Nesse ponto, você deve poder inserir o nome de domínio personalizado no navegador e ver se ele te leva com êxito ao site do Azure. 
<!--HONumber=42-->
