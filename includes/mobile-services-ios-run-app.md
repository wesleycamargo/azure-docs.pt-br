


O estágio final deste tutorial é compilar e executar seu novo aplicativo.

1. Navegue até o local onde você salvou os arquivos compactados do projeto, expanda os arquivos em seu computador e abra o arquivo do projeto usando o Xcode.

   	![](./media/mobile-services-ios-run-app/mobile-xcode-project.png)

2. Pressione o botão **Executar** para criar o projeto e iniciar o aplicativo no emulador do iPhone, que é o padrão para este projeto.

3. No aplicativo, digite um texto significativo, como _Concluir o tutorial_ e clique no ícone de adição (**+**).

   	![](./media/mobile-services-ios-run-app/mobile-quickstart-startup-ios.png)

   	Isso envia uma solicitação de POST ao novo serviço móvel hospedado no Azure. Os dados da solicitação são inseridos na tabela TodoItem. Os itens armazenados na tabela são retornados pelo serviço móvel, e os dados são exibidos na lista.

	>[WACOM.NOTE]Você pode examinar o código que acessa o serviço móvel para consultar e inserir dados que estão localizados no arquivo TodoService.m.</p> 
 	</div>

