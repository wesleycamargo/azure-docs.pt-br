<properties 
	pageTitle="Conectar-se ao SQL Server local por meio de um aplicativo de API no Serviço de Aplicativo do Azure usando Conexões Híbridas" 
	description="Criar um aplicativo de API no Microsoft Azure e conectá-lo a um banco de dados do SQL Server local"
	services="app-service\api" 
	documentationCenter="" 
	authors="TomArcher" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/03/2015" 
	ms.author="tdykstra"/>

# Conectar-se ao SQL Server local por meio de um aplicativo de API no Serviço de Aplicativo do Azure usando Conexões Híbridas

As Conexões Híbridas podem conectar aplicativos de API do [Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714) a recursos locais que usam uma porta TCP estática. Os recursos com suporte incluem Microsoft SQL Server, MySQL, APIs Web HTTP, Serviços Móveis e os serviços Web mais personalizados.

Neste tutorial, você aprenderá como criar um aplicativo de API do Serviço de Aplicativo na [Visualização do Azure](http://go.microsoft.com/fwlink/?LinkId=529715) que se conecta a um banco de dados SQL Server local usando o novo recurso de Conexão Híbrida. O tutorial pressupõe que você não tem nenhuma experiência anterior com o Azure ou com o SQL Server.

>[AZURE.NOTE]Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

## Pré-requisitos

Para concluir este tutorial, você precisará dos produtos a seguir. Todos estão disponíveis em versões gratuitas, portanto, é possível começar a desenvolver soluções do Azure de maneira totalmente gratuita.

- **Assinatura do Azure** - para uma assinatura gratuita, consulte [Avaliação Gratuita do Azure](/pricing/free-trial/). 

- **Visual Studio** - para baixar uma versão de avaliação gratuita do Visual Studio 2013 ou do Visual Studio 2015, consulte [Downloads do Visual Studio](http://www.visualstudio.com/downloads/download-visual-studio-vs). Instale um deles antes de continuar. (As capturas de tela neste tutorial foram feitas usando o Visual Studio 2013)

- **SQL Server 2014 Express with Tools** - baixe o Microsoft SQL Server Express gratuitamente na [página do Banco de dados da plataforma da web da Microsoft](https://www.microsoft.com/pt-BR/download/details.aspx?id=42299). Posteriormente neste tutorial, você verá como [instalar o SQL Server](#InstallSQLDB) para garantir que ele esteja configurado corretamente.

- **SQL Server Management Studio Express** - este item está incluso no item para download SQL Server 2014 Express with Tools mencionado acima, mas se você precisar instalá-lo separadamente, poderá baixá-lo e instalá-lo a partir da [página de download do SQL Server Express](https://www.microsoft.com/pt-BR/download/details.aspx?id=42299).

O tutorial pressupõe que você tem uma assinatura do Azure, que você tem o Visual Studio 2013 instalado e que você instalou ou habilitou o .NET Framework 3.5. O tutorial mostra a você como instalar o SQL Server 2014 Express em uma configuração que funciona bem com o recurso de Conexões Híbridas do Azure (uma instância padrão com uma porta TCP estática). Antes de iniciar o tutorial, baixe (mas não instale) o SQL Server 2014 Express with Tools do local mencionado acima, se você ainda não tiver o SQL Server instalado.

### Observações
Para usar um banco de dados local SQL Server ou SQL Server Express com uma conexão híbrida, o TCP/IP precisa ser habilitado em uma porta estática. As instâncias padrão no SQL Server usam a porta estática 1433, ao passo que instâncias nomeadas não.

O computador no qual você instala o agente do Hybrid Connection Manager local:

- Deve ter conectividade de saída com o Azure por:

> <table border="1">
    <tr>
       <th><strong>Porta</strong></th>
        <th>Porque</th>
    </tr>
    <tr>
        <td>80</td>
        <td><strong>Necessária</strong> para porta HTTP para validação de certificado e, opcionalmente, para conectividade de dados.</td>
    </tr>
    <tr>
        <td>443</td>
        <td><strong>Opcional</strong> para conectividade de dados. Se a conectividade de saída para 443 estiver indisponível, a porta TCP 80 é usada.</td>
    </tr>
	<tr>
        <td>5671 e 9352</td>
        <td><strong>Recomendada</strong>, mas opcional para conectividade de dados. Observe que esse modo costuma produzir uma taxa de transferência maior. Se a conectividade de saída para essas portas estiver indisponível, a porta TCP 443 é usada.</td>
	</tr>
</table>

- Precisa ser capaz de atingir o *hostname*:*portnumber* do seu recurso local. 

As etapas neste artigo pressupõem que você está utilizando o navegador a partir do computador que hospedará o agente local de conexão híbrida.

Se você já tiver o SQL Server instalado em uma configuração e em um ambiente que atenda às condições descritas acima, é possível avançar e iniciar com [Criar um banco de dados SQL Server local](#CreateSQLDB).

<a name="InstallSQL"></a>
## Instalar o SQL Server Express, habilitar TCP/IP e criar um banco de dados SQL Server local

Esta seção mostra a você como instalar o SQL Server Express, habilitar o TCP/IP e criar um banco de dados, de modo que seu aplicativo de API funcionará com o [Portal de Visualização do Azure](https://portal.azure.com).

<a name="InstallSQLDB"></a>
### Instalar SQL Server Express

1. Para instalar o SQL Server Express, baixe o arquivo **SQLEXPRWT\_x64\_ENU.exe** (versão 64 bits) ou **SQLEXPR\_x86\_ENU.exe** (versão 32 bits) e extraia-o na pasta desejada. 

2. Depois de extrair os arquivos de instalação do SQL Server Express, execute o **setup.exe**.

3. Quando a **Central de Instalação do SQL Server** for exibida, selecione **Nova instalação autônoma do SQL Server ou adicionar recursos a uma instalação existente**.

	![Central de Instalação do SQL Server](./media/app-service-api-hybrid-on-premises-sql-server/sql-server-installation-center.png)

4. Siga as instruções, aceitando as configurações e escolhas padrão, até chegar à página **Configuração de Instância**.
	
5. Na página **Configuração de Instância**, escolha **Instância padrão** e clique em **Avançar**.
	
	![Configuração da instância](./media/app-service-api-hybrid-on-premises-sql-server/instance-configuration.png)
	
	A instância padrão do SQL Server escuta a solicitações de clientes do SQL Server na porta estática 1433, que é aquela requerida pelo recurso de Conexões Híbridas. Instâncias nomeadas utilizam portas dinâmicas e UDP, que não têm suporte em Conexões Híbridas.
	
6. Aceite os padrões na página **Configuração de Servidor** e clique em **Avançar**.
	
7. Na página **Configuração do Mecanismo do Banco de Dados**, em **Modo de Autenticação**, selecione **Modo Misto (autenticação de SQL Server e autenticação do Windows)** e forneça uma senha.
	
	![Configuração do mecanismo de banco de dados](./media/app-service-api-hybrid-on-premises-sql-server/database-engine-configuration.png)
	
	Neste tutorial, você irá utilizar autenticação de SQL Server. Não se esqueça da senha fornecida, pois você precisará dela mais tarde.
	
8. Passe pelas etapas restantes do assistente, aceitando os padrões conforme avança, para concluir a instalação.

9. Feche a caixa de diálogo **Central de Instalação do SQL Server**.

### Habilitar TCP/IP
Para habilitar TCP/IP, você utilizará o SQL Server Configuration Manager, que foi instalado quando você instalou o SQL Server Express. Siga as etapas em [Habilitar Protocolo de Rede TCP/IP para SQL Server](http://technet.microsoft.com/library/hh231672%28v=sql.110%29.aspx) antes de continuar.

<a name="CreateSQLDB"></a>
### Crie um banco de dados local do SQL Server

1. No **SQL Server Management Studio**, conecte-se ao SQL Server que você acabou de instalar. Para **Tipo de servidor**, selecione **Mecanismo de Banco de Dados**. Como **Nome do Servidor**, você pode utilizar **localhost** ou o nome do computador que você está utilizando. Escolha **Autenticação do SQL Server** e faça logon com o nome de usuário `sa` e a senha que você criou anteriormente.

	![Conectar-se ao servidor](./media/app-service-api-hybrid-on-premises-sql-server/connect-to-server.png)
	
	Se a caixa de diálogo **Conectar ao Servidor** não aparecer automaticamente, vá até **Pesquisador de Objetos** no painel à esquerda, clique em **Conectar** e em **Mecanismo de Banco de Dados**.
	
2. Para criar um novo banco de dados utilizando o SQL Server Management Studio, clique com o botão direito em **Bancos de Dados** no Pesquisador de Objetos, e clique em **Novo Bancos de Dados**.
	
	![Menu Criar novo banco de dados](./media/app-service-api-hybrid-on-premises-sql-server/new-database-menu.png)
	
3. Na caixa de diálogo **Novo Banco de Dados**, insira `LocalDatabase` como nome do banco de dados e clique em **OK**.
	
	![Criando um novo banco de dados](./media/app-service-api-hybrid-on-premises-sql-server/new-database.png)
	
### Criar e popular uma tabela do SQL Server

1. No **Pesquisador de Objetos** do **SQL Server Management Studio**, expanda a entrada `LocalDatabase`.

	![Banco de dados expandido](./media/app-service-api-hybrid-on-premises-sql-server/local-database-expanded.png)

2. Clique com o botão direito em **Tabelas** e selecione a opção **Tabela...** no menu de contexto.

	![Menu Nova tabela](./media/app-service-api-hybrid-on-premises-sql-server/new-table-menu.png)

3. Quando a grade do designer de tabela for exibida, insira as informações de coluna, conforme mostrado na figura a seguir.

	![Novas colunas da tabela](./media/app-service-api-hybrid-on-premises-sql-server/table-def.png)

4. Pressione **&lt;Ctrl>S** para salvar a nova definição da tabela. Será solicitado que você insira um nome de tabela. Digite `Speakers` e pressione **OK**.

	![Salvar nova tabela](./media/app-service-api-hybrid-on-premises-sql-server/save-new-table.png)

5. Em **Pesquisador de Objetos**, clique com o botão direito na tabela `Speakers` recém-criada e selecione **Editar as 200 primeiras linhas** no menu de contexto.

	![Editar as 200 primeiras linhas](./media/app-service-api-hybrid-on-premises-sql-server/edit-top-200-rows.png)

6. Quando a grade for exibida para inserir/modificar os dados da tabela, insira alguns dados de teste, conforme mostrado na figura a seguir.

	![Dados de teste](./media/app-service-api-hybrid-on-premises-sql-server/speakers-data.png)

## Criar e implantar o aplicativo de API de demonstração no Azure 

Esta seção orienta você pela criação do aplicativo de API de demonstração.

1. Abra o Visual Studio 2013 e selecione **Arquivo > Novo > Projeto**. 

2. Selecione o modelo **Visual C# > Web > Aplicativo Web ASP .NET**, desmarque a opção **Adicionar application insights ao projeto**, atribua o nome *SpeakersList* ao projeto e clique em **OK**.

	![](./media/app-service-api-hybrid-on-premises-sql-server/new-project.png)

3. Na caixa de diálogo **Novo Projeto ASP.NET**, selecione o modelo de projeto **Aplicativo de API do Azure** e clique em **OK**.

	![](./media/app-service-api-hybrid-on-premises-sql-server/new-project-api-app.png)

4. No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta **Modelos** e selecione **Adicionar > Classe...** no menu de contexto.

	![](./media/app-service-api-hybrid-on-premises-sql-server/new-model-menu.png)

5. Atribua o nome *Speaker.cs* ao novo arquivo e clique em **Adicionar**.

	![](./media/app-service-api-hybrid-on-premises-sql-server/new-model-class.png)

6. Substitua todo o conteúdo do arquivo `Speaker.cs` pelo código a seguir.

		namespace SpeakersList.Models
		{
			public class Speaker
			{
				public int Id { get; set; }
				public string Name { get; set; }
				public string EmailAddress { get; set; }
			}
		}

7. No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta **Controladores** e selecione **Adicionar > Controlador...** no menu de contexto.

	![](./media/app-service-api-hybrid-on-premises-sql-server/new-controller.png)

8. Na caixa de diálogo **Adicionar Scaffold**, selecione a opção **Controlador de API Web 2 - Vazio** e clique em **Adicionar**.

	![](./media/app-service-api-hybrid-on-premises-sql-server/add-scaffold.png)

9. Atribua o nome **SpeakersController** ao controlador e clique em **Adicionar**.

	![](./media/app-service-api-hybrid-on-premises-sql-server/add-controller-name.png)

10. Substitua o código no arquivo `SpeakersController.cs` pelo código a seguir. Certifique-se de especificar seus próprios valores para os espaços reservados &lt;serverName> e &lt;password> em `connectionString`. O valor <serverName> é o nome do computador no qual o SQL Server está localizado, e o valor <password> é o definido quando você instalou e configurou o SQL Server.

	> [AZURE.NOTE]O trecho de código a seguir inclui as informações de senha. Isso foi feito para manter a demonstração simples. Em um ambiente de produção real, você não deve armazenar suas credenciais no código. Em vez disso, consulte as [práticas recomendadas para implantar as senhas (e outros dados confidenciais) para o ASP.NET e o Azure](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure).

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Net;
		using System.Net.Http;
		using System.Web.Http;
		using SpeakersList.Models;
		using System.Data.Sql;
		using System.Data.SqlClient;
		
		namespace SpeakersList.Controllers
		{
		    public class SpeakersController : ApiController
		    {
		        [HttpGet]
		        public IEnumerable<Speaker> Get()
		        {
					// Instantiate List object that will be populated and returned
					// from this method.
		            List<Speaker> speakers = new List<Speaker>();
	
					// Build database connection string.
		            string connectionString = "Server=<serverName>;" +
		                                      "Initial Catalog=LocalDatabase;" +
		                                      "User Id=sa;Password=<password>;" +
		                                      "Asynchronous Processing=true;";
		
					// Build query string to select all three columns from the Speakers table.
		            string queryString = "SELECT Id, EmailAddress, Name FROM dbo.Speakers;";

					// Instantiate the SqlConnection object using the connection string.
		            using (SqlConnection connection = new SqlConnection(connectionString))
		            {
						// Instantiate the SqlCommand object using the connection and query strings.
		                SqlCommand command = new SqlCommand(queryString, connection);

						// Open the connection to the database.
		                connection.Open();

						// Execute the command and return a SqlDataReader object
						// that can be iterated.
		                SqlDataReader reader = command.ExecuteReader();
		                try
		                {
							// Read a row from the SqlDataReader object
		                    while (reader.Read())
		                    {
								// For each row, use the returned data to instantiate
								// and add to the List a new Speaker object.
		                        speakers.Add(new Speaker 
		                            { 
		                                Id = Convert.ToInt32(reader[0]), 
		                                EmailAddress = reader[1].ToString(), 
		                                Name = reader[2].ToString() 
		                            }
		                        );
		                    }
		                }
		                finally
		                {
							// Close the SqlDataReader object.
		                    reader.Close();
		                }
		            }
					// Return the List of Speaker objects.
		            return speakers;
		        }
		    }
		}

### Habilitar interface do usuário do Swagger

Habilitar a interface do usuário do Swagger permitirá que você teste seu aplicativo de API sem precisar escrever o código do cliente para chamá-lo.

1. Abra o arquivo *App\_Start/SwaggerConfig.cs*, e pesquise por **EnableSwaggerUI**:

	![](./media/app-service-api-hybrid-on-premises-sql-server/swagger-ui-disabled.png)

2. Remova os comentários das seguintes linhas de código:

	        })
	    .EnableSwaggerUi(c =>
	        {

3. Quando terminar, verifique se que o arquivo é semelhante à imagem a seguir.

	![](./media/app-service-api-hybrid-on-premises-sql-server/swagger-ui-enabled.png)

### Testar o aplicativo de API

1. Para exibir a página de teste de API, execute o aplicativo localmente por meio do **&lt;Ctrl>F5**. Você deverá ver um erro semelhante à imagem a seguir.

	![](./media/app-service-api-hybrid-on-premises-sql-server/error-forbidden.png)

2. Na barra de endereços do navegador, adicione `/swagger` ao final da URL e, em seguida, clique em **&lt;Enter>**. Isso exibirá a interface do usuário do Swagger habilitada na seção anterior.

	![](./media/app-service-api-hybrid-on-premises-sql-server/swagger-ui.png)

3. Clique na seção **Alto-falantes** para expandi-la.

4. Clique em **GET /api/alto-falantes** para expandir essa seção.

5. Clique em **Experimentar!** para exibir os dados inseridos anteriormente no banco de dados.

	![](./media/app-service-api-hybrid-on-premises-sql-server/try-it-out.png)

### Implantando o aplicativo de API

Agora que você testou o aplicativo localmente, é hora de implantar o aplicativo no Azure.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto (não na solução) e clique em **Publicar...**. 

	![Opção de menu de publicação do projeto](./media/app-service-api-hybrid-on-premises-sql-server/publish-menu.png)

2. Clique na guia **Perfil** e clique em **Aplicativos de API do Microsoft Azure (Visualização)**.

	![Opção de menu de publicação do projeto](./media/app-service-api-hybrid-on-premises-sql-server/publish-web.png)

3. Clique em **Novo** para provisionar um novo Aplicativo de API em sua assinatura do Azure.

	![Selecione a caixa de diálogo Serviços de API Existentes](./media/app-service-api-hybrid-on-premises-sql-server/publish-to-existing-api-app.png)

4. Na caixa de diálogo **Criar um Aplicativo de API**, digite o seguinte:

	- Em **Nome do Aplicativo de API**, forneça um nome para o aplicativo. 
	- Se você tiver várias assinaturas do Azure, selecione a que deseja usar.
	- Em **Plano de Serviço de Aplicativo**, selecione entre seus planos de Serviço de Aplicativo existentes ou selecione **Criar novo Plano de Serviço de Aplicativo** e digite o nome do novo plano. 
	- Em **Grupo de Recursos**, selecione entre os grupos de recursos existentes ou selecione **Criar novo grupo de recursos** e digite um nome. O nome deve ser exclusivo. Considere usar o nome do aplicativo como prefixo e acrescentar algumas informações pessoais, como sua ID da Microsoft (sem o sinal @).  
	- Em **Nível de Acesso**, selecione **Disponível para Qualquer Pessoa**. Esta opção tornará sua API totalmente pública, o que é adequado para este tutorial. Você pode restringir o acesso mais tarde no [Portal de visualização do Azure](https://portal.azure.com).
	- Selecione uma região.

	Clique em **OK** para criar o Aplicativo de API em sua assinatura.

	![Configurar a caixa de diálogo de aplicativo Web do Microsoft Azure](./media/app-service-api-hybrid-on-premises-sql-server/publish-new-api-app.png)

5. O processo pode levar alguns minutos, então o Visual Studio mostra uma caixa de diálogo informando que o processo foi iniciado. Clique em **OK** na caixa de diálogo de confirmação.

	![Mensagem de confirmação de criação do serviço de API iniciado](./media/app-service-api-hybrid-on-premises-sql-server/create-api-app-confirmation.png)

6. Enquanto o processo de provisionamento está criando o grupo de recursos e o aplicativo de API em sua assinatura do Azure, o Visual Studio mostra o progresso na janela **Atividade do Serviço de Aplicativo do Azure**.

	![Notificação de status pela janela de atividade do Serviço de Aplicativo do Azure](./media/app-service-api-hybrid-on-premises-sql-server/app-service-activity.png)

7. Após o Aplicativo de API ser provisionado, clique com o botão direito do mouse no **Gerenciador de Soluções** e selecione **Publicar** para reabrir a caixa de diálogo de publicação. O perfil de publicação criado na etapa anterior deve estar pré-selecionado. Clique em **Publicar** para iniciar o processo de implantação.

	![Implantando o aplicativo de API](./media/app-service-api-hybrid-on-premises-sql-server/publish2.png)

A janela **Atividade do Serviço de Aplicativo do Azure** mostra o progresso da implantação e indicará quando o processo de publicação estiver concluído.

## Criar uma Conexão Híbrida e um Serviço do BizTalk ##

1. No navegador, vá para o [Portal de visualização do Azure](https://portal.azure.com). 

2. Clique na opção **Procurar tudo** à esquerda.

3. Na folha **Procurar**, selecione **Aplicativos de API**.

4. Na folha **Aplicativos de API**, localize seu aplicativo de API e clique nele.

5. A folha do seu aplicativo de API, clique no valor sob **Host de aplicativo de API**.
 
	![Folha Aplicativo de API](./media/app-service-api-hybrid-on-premises-sql-server/api-app-blade-api-app-host.png)

6. Quando a folha **Host de Aplicativo de API** for exibida, role para baixo até a seção **Rede** e clique em **Conexões híbridas**.
	
	![Conexões Híbridas](./media/app-service-api-hybrid-on-premises-sql-server/api-app-host-blade-hybrid-connections.png)
	
7. Na folha **Conexões híbridas**, clique em **Adicionar** > **Nova conexão híbrida**.
	
8. Na lâmina **Criar Conexão Híbrida**:
	- Como **Nome**, forneça um nome para a conexão.
	- Como **Hostname**, insira o nome do computador que hospeda o seu SQL Server.
	- Como **Porta**, insira `1433` (a porta padrão para SQL Server).
	- Clique em **Serviço BizTalk** e digite um nome para o serviço BizTalk.
	
	![Criar uma Conexão Híbrida](./media/app-service-api-hybrid-on-premises-sql-server/create-biztalk-service.png)
		
9. Clique em **OK** duas vezes.

	Quando o processo for concluído, a área **Notificações** piscará **SUCESSO** em verde e a folha **Conexões híbridas** mostrará a nova conexão híbrida com o status **Não conectado**.
	
	![Conexão híbrida criada](./media/app-service-api-hybrid-on-premises-sql-server/hybrid-not-connected-yet.png)
	
Nesse ponto, você concluiu uma parte importante da infraestrutura de conexão híbrida de nuvem. Em seguida, você criará uma parte local correspondente.

<a name="InstallHCM"></a>
## Instalar o Gerenciador de Conexões Híbridas local para completar a conexão

[AZURE.INCLUDE [app-service-hybrid-connections-manager-install](../../includes/app-service-hybrid-connections-manager-install.md)]

Agora que a infraestrutura de conexão híbrida está concluída, é o momento de testar o aplicativo.

<a name="CreateASPNET"></a>
## Testar o aplicativo de API concluído no Azure

1. No Portal de Visualização do Azure, volte para a folha Host de aplicativo de API e clique no valor sob **URL**.
	
2. Depois que a página de host do aplicativo de API for exibida no navegador, acrescente `/swagger` à URL na barra de endereços do navegador e clique em **&lt;Enter>**.
	
3. Clique na seção **Alto-falantes** para expandi-la.

4. Clique em **GET /api/alto-falantes** para expandir essa seção.

5. Clique em **Experimentar!** para exibir os dados inseridos anteriormente no banco de dados.

	![](./media/app-service-api-hybrid-on-premises-sql-server/try-it-out-azure.png)
	
**Parabéns!** Você criou um aplicativo de API que é executado no Azure e usa uma conexão híbrida para se conectar a um banco de dados do SQL Server local.

## Consulte também
[Visão geral de Conexões Híbridas](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Josh Twist apresenta conexões híbridas (vídeo no Channel 9)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[Visão geral de Conexões Híbridas](/services/biztalk-services/)

[Serviços BizTalk: guias Painel, Monitor, Escala, Configurar e Conexão Híbrida](../biztalk-dashboard-monitor-scale-tabs/)

[Criando uma nuvem híbrida no mundo real com portabilidade perfeita com aplicativo (vídeo no Channel 9)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[Conectar-se a um SQL Server no local por meio de um serviço móvel do Azure utilizando Conexões Híbridas](../mobile-services-dotnet-backend-hybrid-connections-get-started.md)

[Conectar-se a um SQL Server local a partir de serviços móveis do Azure utilizando Conexões Híbridas (vídeo no Channel 9)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

[Visão geral de Identidade ASP.NET](http://www.asp.net/identity)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

<!---HONumber=Nov15_HO2-->