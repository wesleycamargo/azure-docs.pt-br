O estágio final deste tutorial é compilar e executar seu novo aplicativo.

### Carregar projeto no Android Studio e sincronizar Gradle

1. Navegue até o local onde você salvou os arquivos compactados do projeto e expanda os arquivos em seu computador no diretório de projetos do Android Studio.

2. Abra o Android Studio. Se você estiver trabalhando com um projeto e ele for exibido, feche o projeto (Arquivo => Fechar Projeto).

3. Selecione **Abrir um projeto existente do Android Studio**, navegue até o local do projeto e, em seguida, clique em **OK**. Isso carregará o projeto e começará a sincronizá-lo com o Gradle.

 	![](./media/mobile-services-android-get-started/android-studio-import-project.png)

4. Aguarde até que a atividade de sincronização do Gradle seja concluída. Se você encontrar um erro "falha ao localizar destino", isso ocorre porque a versão usada no Android Studio não corresponde à versão do exemplo. A maneira mais fácil de corrigir esse problema é clicar no link **Instalar plataforma(a) ausente(s) e sincronizar o projeto** na mensagem de erro. Você pode receber mensagens de erro de versão adicionais, e deve simplesmente repetir esse processo até que nenhum erro seja exibido.
    - Há outra maneira de corrigir esse problema, se você deseja executar a versão "mais recente e melhor" do Android. Você pode atualizar **targetSdkVersion** no arquivo *build.gradle* no diretório de *aplicativo* para corresponder à versão já instalada em seu computador, que você pode descobrir clicando no ícone do **Gerenciador de SDK** e verificando qual versão está listada. Em seguida, pressione **Sincronizar Projeto com Arquivos do Gradle**. Você pode receber uma mensagem de erro para a versão das Ferramentas de Compilação e pode corrigi-la da mesma maneira.

### Executando o aplicativo

Você pode executar o aplicativo usando o emulador ou um dispositivo real.

1. Para executar de um dispositivo, conecte-se ao computador com um cabo USB. Você deve [configurar o dispositivo para o desenvolvimento](https://developer.android.com/training/basics/firstapp/running-app.html). Se estiver desenvolvendo em um computador com o Windows, você deverá baixar e instalar um driver USB.

2. Para executar usando o emulador Android, você deve definir no mínimo um AVD (Dispositivo Virtual Android). Clique no ícone do Gerenciador do AVD para criar e gerenciar esses dispositivos.

3. No menu **Executar**, clique em **Executar** para iniciar o projeto e escolha um dispositivo ou emulador na caixa de diálogo exibida.

4. Quando o aplicativo surgir, digite um texto significativo, como _Concluir o tutorial_ e clique em **Adicionar**.

   	![](./media/mobile-services-android-get-started/mobile-quickstart-startup-android.png)

   	Isso envia uma solicitação de POST para o novo serviço móvel hospedado no Azure. Os dados da solicitação são inseridos na tabela TodoItem. Os itens armazenados na tabela são retornados pelo serviço móvel, e os dados são exibidos na lista.

	> [AZURE.NOTE]Você pode examinar o código que acessa o serviço móvel para consultar e inserir dados, que estão localizados no arquivo ToDoActivity.java.

8. No Portal de Gerenciamento, clique na guia **Dados** e, em seguida na tabela **TodoItems**.

   	![](./media/mobile-services-android-get-started/mobile-data-tab1.png)

   	Isso permite que você procure os dados inseridos pelo aplicativo na tabela.

   	![](./media/mobile-services-android-get-started/mobile-data-browse.png)

<!---HONumber=Oct15_HO3-->