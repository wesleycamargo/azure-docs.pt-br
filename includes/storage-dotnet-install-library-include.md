### Criar o aplicativo do console e obter o assembly
Para criar um novo aplicativo de console no Visual Studio e instalar o pacote NuGet com a Biblioteca de Cliente do Armazenamento do Azure:

1. No Visual Studio, escolha **Arquivo > Novo Projeto** e **Windows > Aplicativo de Console** na lista de modelos do Visual C#.
2. Forneça um nome para o aplicativo de console e clique em **OK**.
3. Após o projeto ser criado, clique com o botão direito do mouse no projeto no Gerenciador de Soluções e escolha **Gerenciar Pacotes NuGet**. Pesquise online "WindowsAzure.Storage" e clique em **Instalar** para instalar o pacote da Biblioteca de Cliente do Armazenamento do Azure para .NET e as dependências.

Os exemplos de código neste artigo também usam a [Biblioteca do Gerenciador de Configurações do Microsoft Azure](https://msdn.microsoft.com/library/azure/mt634646.aspx) para recuperar a cadeia de conexão de armazenamento de um arquivo app.config no aplicativo do console. Com o Gerenciador de Configurações do Azure, você pode recuperar a cadeia de conexão em tempo de execução, independentemente do aplicativo estar sendo executado no Microsoft Azure ou a partir de um desktop, celular ou aplicativo Web.

Para instalar o pacote do Gerenciador de Configurações do Azure, clique com o botão direito do mouse no projeto no Gerenciador de Soluções e escolha **Gerenciar Pacotes NuGet**. Pesquise online por "ConfigurationManager" e clique em **Instalar** para instalar o pacote.

O uso do Gerenciador de Configurações do Azure é opcional. Você também pode usar uma API como a [classe ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager.aspx) do .NET Framework.

<!---HONumber=AcomDC_0309_2016-->