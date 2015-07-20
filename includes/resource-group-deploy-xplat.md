## Como implantar com o CLI do Azure

1. Faça logon em sua conta do Azure.

        azure login

  Depois de fornecer suas credenciais, o comando retornará o resultado do seu logon.

        ...
        info:    login command OK

2. Se você tiver várias assinaturas, forneça a ID da assinatura que deseja usar para implantação.

        azure account set <YourSubscriptionNameOrId>

3. Alternar para o módulo do Gerenciador de Recursos do Azure

        azure config mode arm

   Você receberá a confirmação do novo modo.

        info:     New mode is arm

4. Se você não tiver um grupo de recursos existente, crie um novo grupo de recursos. Forneça o nome do grupo de recursos e o local necessários para sua solução.

        azure group create -n ExampleResourceGroup -l "West US"

   Um resumo do novo grupo de recursos é retornado.

        info:    Executing command group create
        + Getting resource group ExampleResourceGroup
        + Creating resource group ExampleResourceGroup
        info:    Created resource group ExampleResourceGroup
        data:    Id:                  /subscriptions/####/resourceGroups/ExampleResourceGroup
        data:    Name:                ExampleResourceGroup
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags:
        data:
        info:    group create command OK

5. Para criar uma nova implantação para seu grupo de recursos, execute o comando a seguir e ofereça os parâmetros necessários. Os parâmetros incluirão um nome para sua implantação, o nome do seu grupo de recursos, o caminho ou a URL para o modelo criado e qualquer outro parâmetro necessário para seu cenário.

   Você tem as seguintes opções para fornecer valores de parâmetro:

   - Use parâmetros embutidos e um modelo local.

             azure group deployment create -f <PathToTemplate> {"ParameterName":"ParameterValue"} -g ExampleResourceGroup -n ExampleDeployment

   - Use parâmetros embutidos e um link para um modelo.

             azure group deployment create --template-uri <LinkToTemplate> {"ParameterName":"ParameterValue"} -g ExampleResourceGroup -n ExampleDeployment

   - Use um arquivo de parâmetro.

             azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

  Quando o grupo de recursos tiver sido implantado, você verá um resumo da implantação.

         info:    Executing command group deployment create
         + Initializing template configurations and parameters
         + Creating a deployment
         ...
         info:    group deployment create command OK


6. Para obter informações sobre sua implantação mais recente.

         azure group log show -l ExampleResourceGroup

7. Para obter informações detalhadas sobre falhas de implantação.

         azure group log show -l -v ExampleResourceGroup

<!---HONumber=July15_HO2-->