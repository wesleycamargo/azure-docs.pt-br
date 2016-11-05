Após os registros do nome de domínio terem sido propagados, você deverá associá-lo ao aplicativo Web. Use as etapas a seguir para habilitar os nomes de domínio usando o navegador.

> [!NOTE]
> Pode levar algum tempo para que os registros TXT criados nas etapas anteriores sejam propagados no sistema DNS. Você não pode adicionar o nome de domínio ao seu aplicativo Web até que o registro TXT seja propagado. Se estiver usando um registro A, você não poderá adicionar o nome de domínio do registro A ao aplicativo Web até o registro TXT criado na etapa anterior ser propagado.
> 
> É possível usar um serviço como <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> para verificar se registro TXT está disponível.
> 
> 

1. No seu navegador, abra o [Portal do Azure](https://portal.azure.com).
2. Na guia **Aplicativos Web**, clique no nome do seu aplicativo Web e, em seguida, selecione **Domínios personalizados**
   
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)
3. Na folha **Domínios personalizados**, clique em **Adicionar nome do host**.
4. Use as caixas de texto **Nome do host** para inserir os nomes de domínio para associar a esse aplicativo Web.
   
    ![](./media/custom-dns-web-site/add-custom-domain.png)
5. Clique em **Validar**.
6. Ao clicar em **Validar**, o Azure iniciará o fluxo de trabalho de verificação de domínio. Isso verificará a propriedade do domínio, bem como a disponibilidade do nome do host e o sucesso ou o erro detalhado do relatório com diretrizes prescritivas sobre como corrigir o erro.

Nesse ponto, você deve poder inserir o nome de domínio personalizado no navegador e ver se ele te leva com êxito ao aplicativo Web.

<!---HONumber=AcomDC_0824_2016-->