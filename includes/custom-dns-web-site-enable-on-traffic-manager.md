Depois que os registros do nome de domínio forem propagados, você deverá ser capaz de usar o navegador para verificar se o nome de domínio personalizado pode ser usado para acessar seu aplicativo Web no Serviço de Aplicativo do Azure.

> [AZURE.NOTE]Pode levar algum tempo para o CNAME propagar por meio do sistema DNS. É possível usar um serviço como <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> para verificar se o CNAME está disponível.

Se ainda não tiver adicionado o aplicativo Web como um ponto de extremidade do Gerenciador de Tráfego, você deverá fazer isso antes da resolução de nome funcionar, porque o nome de domínio personalizado é encaminhado para o Gerenciador de Tráfego. Em seguida, o Gerenciador de Tráfego encaminha para o aplicativo Web. Use as informações em [Adicionar ou Excluir Pontos de Extremidade](../traffic-manager/traffic-manager-endpoints.md) para adicionar o aplicativo Web como um ponto de extremidade no perfil do Gerenciador de Tráfego.

> [AZURE.NOTE]Se seu aplicativo Web não estiver listado, ao adicionar um ponto de extremidade, verifique se ele está configurado como **Padrão** no modo do plano de Serviço de Aplicativo. Você deve usar o modo **Padrão** para o aplicativo Web para trabalhar com o Gerenciador de Tráfego.

1. No seu navegador, abra o [Portal do Azure](https://portal.azure.com).

2. Na guia **Aplicativos Web**, clique no nome do seu aplicativo Web, selecione **Configurações** e, em seguida, selecione **Domínios personalizados e SSL**

	![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3. Na folha **Domínios personalizados e SSL**, clique em **Trazer domínios externos**.

	![](./media/custom-dns-web-site/dncmntask-cname-7.png)

4. Use as caixas de texto **NOMES DE DOMÍNIO** para inserir o nome de domínio do Gerenciador de Tráfego (contoso.trafficmanager.net) a associar a este aplicativo Web.

	![](./media/custom-dns-web-site/dncmntask-cname-8.png)

5. Clique em **Salvar** para salvar a configuração de nome de domínio.

	Depois que a configuração estiver concluída, o nome do domínio personalizado será listado na seção **nomes de domínio** de seu aplicativo Web.

Nesse ponto, você deve poder inserir o nome de domínio personalizado do Gerenciador de Tráfego (contoso.trafficmanager.net) no navegador e ver se ele te leva com êxito ao aplicativo Web.

<!---HONumber=August15_HO9-->