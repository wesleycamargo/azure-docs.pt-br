Após os registros do nome de domínio terem sido propagados, você deverá associá-lo ao site. Use as etapas a seguir para habilitar os nomes de domínio usando o navegador.

> [WACOM.NOTE] Pode levar algum tempo para os registros CNAME criados nas etapas anteriores para propagar no sistema DNS. Não é possível adicionar o nome do domínio do site do Azure até o CNAME ser propagado. Se estiver usando um registro A, você não poderá adicionar o nome de domínio de registro A ao site do Azure até os registros CNAME **awverify.www** ou **www** criados na etapa anterior serem propagados.
> 
> É possível usar um serviço como <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> para verificar se o CNAME está disponível.

1. No navegador, abra o [Portal de Gerenciamento do Azure](https://manage.windowsazure.com).

2. Na guia **Sites**, clique no nome do seu site, selecione **Painel** e **Gerenciar domínios** na parte inferior da página.

	![](./media/custom-dns-web-site/dncmntask-cname-6.png)

6. Use as caixas de texto **NOMES DE DOMÍNIO** para inserir os nomes de domínio a serem associados a este site. 

	![](./media/custom-dns-web-site/dncmntask-cname-7.png)

6. Clique na marca de seleção no canto inferior direito para salvar a configuração do nome de domínio.

	Após a conclusão da configuração, o nome de domínio personalizado será listado em **nomes de domínio** seção da página **Configurar** do seu site.

Neste ponto, você deve inserir o nome de domínio personalizado no navegador e ver se ele leva você com êxito para o site do Azure. 

