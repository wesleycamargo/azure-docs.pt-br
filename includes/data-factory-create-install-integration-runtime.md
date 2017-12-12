## <a name="create-a-self-hosted-ir"></a>Criar um IR auto-hospedado

Nesta seção, você cria um tempo de execução de integração auto-hospedado e o associa com um computador local com o banco de dados do SQL Server. O tempo de execução de integração auto-hospedado é o componente que copia dados do SQL Server em seu computador para o armazenamento de Blobs do Azure. 

1. Crie uma variável para o nome do Integration Runtime. Use um nome exclusivo e anote o nome. Você o usará posteriormente neste tutorial. 

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```
1. Crie um Integration Runtime auto-hospedado. 

   ```powershell
   Set-AzureRmDataFactoryV2IntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName
   ```

   Veja o exemplo de saída:

   ```json
    Id                : /subscriptions/<subscription ID>/resourceGroups/ADFTutorialResourceGroup/providers/Microsoft.DataFactory/factories/onpremdf0914/integrationruntimes/myonpremirsp0914
    Type              : SelfHosted
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Name              : myonpremirsp0914
    Description       :
    ```
 

2. Execute o comando a seguir para recuperar o status do Integration Runtime criado. Confirme se o valor da propriedade **Estado** está definido como **NeedRegistration**. 

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
   ```

   Veja o exemplo de saída:

   ```json
   Nodes                     : {}
   CreateTime                : 9/14/2017 10:01:21 AM
   InternalChannelEncryption :
   Version                   :
   Capabilities              : {}
   ScheduledUpdateDate       :
   UpdateDelayOffset         :
   LocalTimeZoneOffset       :
   AutoUpdate                :
   ServiceUrls               : {eu.frontend.clouddatahub.net, *.servicebus.windows.net}
   ResourceGroupName         : <ResourceGroup name>
   DataFactoryName           : <DataFactory name>
   Name                      : <Integration Runtime name>
   State                     : NeedRegistration
   ```

3. Execute o comando a seguir para recuperar as **chaves de autenticação** para registrar o tempo de execução de integração auto-hospedado com o serviço Data Factory na nuvem. 

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
   ```

   Veja o exemplo de saída:

   ```json
   {
       "AuthKey1":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=",
       "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy="
   }
   ```    
4. Copie uma das chaves (excluir aspas duplas) para registrar o tempo de execução de integração auto-hospedado que você instala em seu computador na próxima etapa.  

## <a name="install-integration-runtime"></a>Instalar o Integration Runtime
1. Se você já tiver o **Microsoft Integration Runtime** em seu computador, desinstale-o usando **Adicionar ou Remover Programas**. 
2. [Baixe](https://www.microsoft.com/download/details.aspx?id=39717) o tempo de execução de integração auto-hospedado em uma máquina local do Windows e execute a instalação. 
3. No **Assistente de instalação Bem-vindo ao Microsoft Integration Runtime**, clique em **Avançar**.  
4. Na página **Contrato de Licença do Usuário Final**, aceite os termos e o contrato de licença e clique em **Avançar**. 
5. Na página **Pasta de destino**, clique em **Avançar**. 
6. Em **Pronto para instalar o Microsoft Integration Runtime**, clique em **Instalar**. 
7. Se você vir uma mensagem de aviso informando que computador está configurado para entrar no modo de suspensão ou hibernação quando não estiver em uso, clique em **OK**. 
8. Se você vir a janela **Opções de Energia**, feche-a e alterne para a janela de instalação. 
9. Na página **Assistente de instalação do Microsoft Integration Runtime concluído**, clique em **Concluir**.
10. Na página **Registrar Integration Runtime (auto-hospedado)**, cole a chave que você salvou na seção anterior e clique em **Registrar**. 

   ![Registrar Integration Runtime](media/data-factory-create-install-integration-runtime/register-integration-runtime.png)
2. Você verá a seguinte mensagem quando o Integration Runtime auto-hospedado for registrado com êxito:

   ![Registrado com êxito](media/data-factory-create-install-integration-runtime/registered-successfully.png)

3. Na página **Novo nó do Integration Runtime (auto-hospedado)** , clique em **Avançar**. 

    ![Nova página do nó do Integration Runtime](media/data-factory-create-install-integration-runtime/new-integration-runtime-node-page.png)
4. No **Canal de comunicação de Intranet**, clique em **Ignorar**. É possível selecionar uma certificação TLS/SSL para proteger a comunicação entre nós em um ambiente de Integration Runtime de vários nós. 

    ![Página de canal de comunicação de Intranet](media/data-factory-create-install-integration-runtime/intranet-communication-channel-page.png)
5. Na página **Registrar Integration Runtime (auto-hospedado)**, clique em **Iniciar o Gerenciador de Configurações**. 
6. Você verá a página a seguir quando o nó estiver conectado ao serviço de nuvem:

   ![O nó está conectado](media/data-factory-create-install-integration-runtime/node-is-connected.png)
7. Agora teste a conectividade com o seu banco de dados do SQL Server.

    ![Guia Diagnósticos](media/data-factory-create-install-integration-runtime/config-manager-diagnostics-tab.png)   

    - Na janela do **Gerenciador de Configurações**, alterne para a guia **Diagnóstico**.
    - Selecione **SqlServer** para o **Tipo da fonte de dados**.
    - Insira o nome do **servidor**.
    - Insira o nome do **banco de dados**. 
    - Selecione o modo de **autenticação**. 
    - Insira o nome de **usuário**. 
    - Insira a **senha** do nome de usuário.
    - Clique em **Testar** para confirmar que esse tempo de execução de integração pode se conectar ao SQL Server. Você verá uma marca de seleção verde se a conexão tiver êxito. Caso contrário, será exibida uma mensagem de erro associada à falha. Corrija todos os problemas e verifique se o tempo de execução de integração pode se conectar ao seu SQL Server.    

    > [!NOTE]
    > Anote esses valores (tipo de autenticação, servidor, banco de dados, usuário, senha). Você os usará posteriormente neste tutorial. 
    
