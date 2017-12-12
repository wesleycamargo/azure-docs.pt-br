### <a name="azure-powershell"></a>Azure PowerShell

#### <a name="install-powershell"></a>Instalar o PowerShell
Instale o PowerShell mais recente se você não o tiver em seu computador. 

1. No navegador da Web, navegue até a página [Downloads do Azure](https://azure.microsoft.com/downloads/). 
2. Clique em **Instalar Windows** na seção **Ferramentas de linha de comando** -> **PowerShell**. 
3. Para instalar o PowerShell, execute o arquivo **MSI**. 

Para saber mais, confira [Como instalar e configurar o Azure PowerShell](/powershell/azure/install-azurerm-ps). 

#### <a name="log-in-to-powershell"></a>Fazer logon no PowerShell

1. Iniciar o **PowerShell** no seu computador. Mantenha o PowerShell aberto até o fim deste guia de início rápido. Se você fechá-la e reabri-la, precisará executar esses comandos novamente.

    ![Inicializar o PowerShell](media/data-factory-quickstart-prerequisites-2/search-powershell.png)
1. Execute o comando a seguir e insira o mesmo nome de usuário e senha do Azure que você usa para entrar no Portal do Azure:
       
    ```powershell
    Login-AzureRmAccount
    ```        
2. Execute o comando abaixo para exibir todas as assinaturas dessa conta:

    ```powershell
    Get-AzureRmSubscription
    ```
3. Se você vir várias assinaturas associadas à sua conta, execute o seguinte comando para selecionar a assinatura com que deseja trabalhar. Substitua **SubscriptionId** pela ID da assinatura do Azure:

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```
