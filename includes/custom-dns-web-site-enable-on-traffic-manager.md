Depois que os registros do nome de domínio forem propagados, você deverá ser capaz de usar o navegador para verificar se o nome de domínio personalizado pode ser usado para acessar seu aplicativo Web no Serviço de Aplicativo do Azure.

> [AZURE.NOTE] Pode levar algum tempo para o CNAME propagar por meio do sistema DNS. É possível usar um serviço como <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> para verificar se o CNAME está disponível.

Se ainda não tiver adicionado o aplicativo Web como um ponto de extremidade do Gerenciador de Tráfego, você deverá fazer isso antes da resolução de nome funcionar, porque o nome de domínio personalizado é encaminhado para o Gerenciador de Tráfego. Em seguida, o Gerenciador de Tráfego encaminha para o aplicativo Web. Use as informações em [Adicionar ou Excluir Pontos de Extremidade](http://msdn.microsoft.com/library/windowsazure/hh744839.aspx) para adicionar o aplicativo Web como um ponto de extremidade no perfil do Gerenciador de Tráfego.

> [AZURE.NOTE] Se seu aplicativo Web não estiver listado, ao adicionar um ponto de extremidade, verifique se ele está configurado como **Padrão** no modo do plano de Serviço de Aplicativo. Você deve usar o modo **Padrão** para o aplicativo Web para trabalhar com o Gerenciador de Tráfego.
<!--HONumber=52--> 
