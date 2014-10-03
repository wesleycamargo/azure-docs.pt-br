Após os registros do nome de domínio serem propagados, você deverá associá-los ao seu site. Use as etapas a seguir para habilitar os nomes de domínio usando o navegador.

> [WACOM.NOTE] Pode levar algum tempo para que os registros CNAME criados nas etapas anteriores sejam propagados pelo sistema DNS. Não é possível adicionar o nome do domínio do Site do Azure até o CNAME ser propagado. Se estiver usando um registro A, você não poderá adicionar o nome de domínio do registro A ao Site do Azure até o registro CNAME **awverify** criado na etapa anterior ser propagado.
>
> É possível usar um serviço como <http://www.digwebinterface.com/> para verificar se o CNAME está disponível.

1.  No seu navegador, abra o [Portal de Gerenciamento do Azure][].

2.  Na guia **Sites**, clique no nome do seu site, selecione **Painel** e **Gerenciar Domínios** na parte inferior da página.

    ![][]

3.  Use as caixas de texto **NOMES DE DOMÍNIO** para inserir os nomes de domínio a serem associados a este site.

    ![][1]

4.  Clique na marca de seleção no canto inferior direito para salvar a configuração do nome de domínio.

    Após a conclusão da configuração, o nome de domínio personalizado será listado na seção **nomes de domínio** da página **Configurar** do seu site.

Nesse ponto, você deve poder inserir o nome de domínio personalizado no navegador e ver se ele te leva com êxito ao site do Azure.

  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com
  []: ./media/custom-dns-web-site/dncmntask-cname-6.png
  [1]: ./media/custom-dns-web-site/dncmntask-cname-7.png
