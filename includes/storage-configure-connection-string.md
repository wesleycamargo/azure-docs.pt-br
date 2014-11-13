A Biblioteca de Cliente de Armazenamento do Azure para .NET oferece suporte ao uso de uma cadeia de conexão de armazenamento para configurar pontos de extremidade e credenciais para acesso a serviços de armazenamento. Recomendamos a você colocar a cadeia de conexão de armazenamento em um arquivo de configuração, em vez de embuti-la no código de seu aplicativo. Você tem duas opções para salvar sua cadeia de conexão:

-   Ao usar os Serviços de Nuvem do Azure, é recomendável armazenar a cadeia de conexão usando o sistema de configuração de serviço do Azure (arquivos `*.csdef` e `*.cscfg`).
-   Se seu aplicativo é executado em máquinas virtuais do Azure, ou se você está compilando aplicativos .NET destinados a serem executados fora do Azure, é recomendável armazenar a cadeia de conexão usando o sistema de configuração do .NET (por exemplo, arquivo `web.config` ou `app.config`).

Posteriormente neste guia, lhe mostraremos como obter sua cadeia de conexão a partir de seu código.

### Configurar sua cadeia de conexão a partir de um serviço de nuvem do Azure

Os Serviços de Nuvem do Azure têm um mecanismo de configuração de serviço exclusivo, que permite que você altere dinamicamente os parâmetros de configuração no Portal de Gerenciamento do Azure sem reimplantar o aplicativo.

Para configurar a cadeia de conexão na configuração de serviço do Azure:

1.  No Gerenciador de Soluções do Visual Studio, na pasta **Funções**
    de seu Projeto de Implantação do Azure, clique com o botão direito do mouse na sua
    função web ou função de trabalho e clique em **Propriedades**.
    ![Selecione as propriedades em uma função de Serviço de Nuvem do Visual Studio][Selecione as propriedades em uma função de Serviço de Nuvem do Visual Studio]

2.  Clique na guia **Configurações** e pressione o botão **Adicionar Configuração**.
    ![Adicione uma configuração de Serviço de Nuvem do Visual Studio][Adicione uma configuração de Serviço de Nuvem do Visual Studio]

    Uma nova entrada **Setting1** será mostrada na grade de configurações.

3.  No menu suspenso **Tipo** da nova entrada **Setting1**, escolha
    **Cadeia de Conexão**.
    ![Defina o tipo da cadeia de conexão][Defina o tipo da cadeia de conexão]

4.  Clique no botão **...** na extremidade direita da entrada **Setting1**.
    A caixa de diálogo **Cadeia de Conexão da Conta de Armazenamento** se abrirá.

5.  Escolha se deseja direcionar o emulador de armazenamento (armazenamento do Windows
    Azure simulado em seu computador local) ou uma conta de
    armazenamento na nuvem. O código deste guia funciona com qualquer uma dessas
    opções. Insira o valor **Chave de Acesso Primária** copiado na
    etapa anterior deste tutorial se você desejar utilizar como destino a
    conta de armazenamento que criamos anteriormente no Azure.

    > [WACOM.NOTE] Você pode utilizar o emulador de armazenamento como destino para evitar quaisquer custos associados ao Armazenamento do Microsoft Azure. Todavia, se você escolher utilizar como destino uma conta de armazenamento do Azure na nuvem, os custos para realização das operações neste tutorial serão insignificantes.

    ![Selecione o ambiente de destino][Selecione o ambiente de destino]

6.  Altere a entrada **Nome** de **Setting1** para um nome "mais amigável”,
    como **StorageConnectionString**. Você fará referência a essa
    cadeia de conexão mais tarde no código deste guia.
    ![Alterar nome da cadeia de conexão][Alterar nome da cadeia de conexão]

### Configurando sua cadeia de conexão usando configuração do .NET

Se você estiver escrevendo um aplicativo que não seja um serviço de nuvem do Azure (consulte a seção anterior), será recomendável usar o sistema de configuração do .NET (por exemplo, `web.config` ou `app.config`). Isso inclui sites ou máquinas virtuais do Azure, bem como aplicativos projetados para serem executados fora do Azure. Você armazena a cadeia de conexão usando o elemento `<appSettings>` da seguinte maneira. Substitua `account-name` pelo nome da sua conta de armazenamento e `account-key` pela chave de acesso da conta de armazenamento:

    <configuration>
        <appSettings>
            <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
        </appSettings>
    </configuration>

Por exemplo, o parâmetro de configuração em seu arquivo de configuração pode ser similar a:

    <configuration>
        <appSettings>
            <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=nYV0gln9fT7bvY+rxu2iWAEyzPNITGkhM88J8HUoyofpK7C8fHcZc2kIZp6cKgYRUM74lHI84L50Iau1+9hPjB==" />
        </appSettings>
    </configuration>

Leia [Configurando cadeias de conexão][Configurando cadeias de conexão] para obter mais informações sobre cadeias de conexão de armazenamento.

Agora você está pronto para executar as tarefas das instruções deste guia.

  [Selecione as propriedades em uma função de Serviço de Nuvem do Visual Studio]: ./media/storage-configure-connection-string/connection-string1.png
  [Adicione uma configuração de Serviço de Nuvem do Visual Studio]: ./media/storage-configure-connection-string/connection-string2.png
  [Defina o tipo da cadeia de conexão]: ./media/storage-configure-connection-string/connection-string3.png
  [Selecione o ambiente de destino]: ./media/storage-configure-connection-string/connection-string4.png
  [Alterar nome da cadeia de conexão]: ./media/storage-configure-connection-string/connection-string5.png
  [Configurando cadeias de conexão]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee758697.aspx
