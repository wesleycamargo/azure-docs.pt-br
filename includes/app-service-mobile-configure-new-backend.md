
1. Novamente nas configurações de back-end do Aplicativo Móvel, clique em **Introdução** > sua plataforma cliente. 

2. Em **Criar uma API de tabela**, selecione a **Linguagem do back-end**, **C#** ou **Node.js**:

	+ **Back-end do Node.js** (via portal): aceite a confirmação e clique em **Criar tabela TodoItem**. Isso cria uma nova tabela *TodoItem* no banco de dados.
	 
		>[AZURE.IMPORTANT]Alternar um back-end do aplicativo existente para o Node.js substituirá todo o conteúdo do site.

	+ **Back-end do .NET (C#)**: clique em **Baixar**, extraia os arquivos de projeto compactados para o computador local, abra a solução no Visual Studio, compile o projeto para restaurar os pacotes NuGet e implante o projeto no Azure. Para saber como implantar um projeto de servidor de back-end .NET no Azure, consulte a seção *Implantar o projeto no aplicativo Web* de [Criar um aplicativo Web ASP.NET no Serviço de Aplicativo do Azure](../articles/app-service-web/web-sites-dotnet-get-started.md#deploy-the-project-to-the-web-app). No Aplicativo de Serviço, um back-end do Aplicativo Móvel é equivalente a um aplicativo Web.
	 
O back-end do Aplicativo Móvel está pronto para ser usado com o aplicativo cliente.

<!---HONumber=AcomDC_1210_2015-->