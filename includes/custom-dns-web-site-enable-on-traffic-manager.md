Depois que os registros do nome de domínio tiverem sido propagados, você deverá ser capaz de usar o navegador para verificar se o nome de domínio personalizado pode ser usado para acessar o site.

> [AZURE.NOTE] Pode levar algum tempo para o CNAME propagar por meio do sistema DNS. Você pode usar um serviço como <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> para verificar se o CNAME está disponível.

Se ainda não tiver adicionado o site como um ponto de extremidade do Gerenciador de Tráfego, você deverá fazer isso antes de a resolução de nome funcionar, uma vez que o nome de domínio personalizado é encaminhado para o Gerenciador de Tráfego. Em seguida, o Gerenciador de Tráfego encaminha para o site. Use as informações contidas em [Adicionar ou Excluir Pontos de Extremidade](http://msdn.microsoft.com/library/windowsazure/hh744839.aspx) para adicionar o site como um ponto de extremidade em seu perfil do Gerenciador de Tráfego.

> [AZURE.NOTE] Se o site não estiver listado, ao adicionar um ponto de extremidade, verifique se ele está configurado para o modo Padrão. Você deve usar o modo Padrão para o site para trabalhar com o Gerenciador de Tráfego.
\<!--HONumber=42-->
