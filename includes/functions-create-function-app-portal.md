1. Clique no botão **Novo** no canto superior esquerdo do Portal do Azure.

1. Clique em **Computação** > **Aplicativo de Funções** e selecione sua **Assinatura**. Em seguida, use as configurações do aplicativo de funções especificadas na tabela.

    ![Criar um aplicativo de funções no portal do Azure](./media/functions-create-function-app-portal/function-app-create-flow.png)

    | Configuração      | Valor sugerido  | Descrição                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome do aplicativo** | Nome globalmente exclusivo | Nome que identifica seu novo aplicativo de funções. | 
    | **[Grupo de Recursos](../articles/azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Nome do novo grupo de recursos no qual criar o seu aplicativo de funções. | 
    | **[Plano de hospedagem](../articles/azure-functions/functions-scale.md)** |   Plano de consumo | Plano de hospedagem que define como os recursos são alocados para seu aplicativo de funções. No **Plano de Consumo** padrão, os recursos são adicionados dinamicamente conforme exigido por suas funções. Você paga apenas pelo tempo durante o qual suas funções são executadas.   |
    | **Localidade** | Europa Ocidental | Escolha uma localização perto de você ou perto de outros serviços que suas funções acessarão. |
    | **[Conta de armazenamento](../articles/storage/storage-create-storage-account.md#create-a-storage-account)** |  Nome globalmente exclusivo |  Nome da nova conta de armazenamento usada pelo seu aplicativo de funções. Você também pode usar uma conta existente. |

1. Clique em **Criar** para provisionar e implantar o novo aplicativo de funções.