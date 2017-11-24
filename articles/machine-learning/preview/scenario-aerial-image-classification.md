---
title: "Classificação de imagem aérea | Microsoft Docs"
description: "Fornece instruções para cenário do mundo real sobre a classificação de imagem área"
author: mawah
ms.author: mawah
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.topic: article
ms.service: machine-learning
services: machine-learning
ms.date: 10/27/2017
ms.openlocfilehash: 07e74c64e587cce99612cd5047516bf131943f2e
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2017
---
# <a name="aerial-image-classification"></a>Classificação de imagem aérea

Este exemplo demonstra como usar o Azure Machine Learning Workbench para coordenar o treinamento distribuído e a operacionalização de modelos de classificação de imagem. Usamos duas abordagens para treinamento: (i) refinando uma rede neural profunda usando um cluster de GPU do [Azure Batch AI](https://docs.microsoft.com/azure/batch-ai/) e (ii) usando o pacote [MMLSpark (Microsoft Machine Learning for Apache Spark)](https://github.com/Azure/mmlspark) para personalizar imagens usando modelos CNTK pré-treinados e para treinar classificadores usando os recursos derivados. Podemos aplicar os modelos treinados de modo paralelo para conjuntos de imagem grandes na nuvem usando um cluster do [Azure HDInsight Spark](https://azure.microsoft.com/services/hdinsight/apache-spark/), possibilitando dimensionar a velocidade de treinamento e operacionalização adicionando ou removendo nós de trabalho.

Este exemplo destaca as duas abordagens para transferir aprendizado, que aproveita modelos pré-treinados para evitar custos de redes neurais profundas do treinamento desde o início. Retreinar uma rede neural profunda normalmente requer a computação de GPU, mas pode levar a maior precisão quando o conjunto de treinamento for grande o suficiente. Treinar um classificador simples em imagens características não exige computação de GPU, é inerentemente rápido e arbitrariamente escalonável e necessita de menos parâmetros. Esse método é, portanto, uma excelente opção quando poucos exemplos de treinamento estão disponíveis – como geralmente é o caso de casos de uso personalizados. 

O cluster do Spark usado neste exemplo tem 40 nós de trabalho e custa aproximadamente $40/h para operar. Os recursos de cluster do Batch AI incluem oito GPUs e custam aproximadamente $10/h para operar. Concluir este passo a passo leva aproximadamente três horas. Quando terminar, siga as instruções de limpeza para remover os recursos criados e parar de incorrer em encargos.

## <a name="link-to-the-gallery-github-repository"></a>Link para o repositório GitHub da galeria

O repositório público do GitHub para este cenário do mundo real contém todos os materiais, incluindo exemplos de código, necessários para este exemplo:

[https://github.com/Azure/MachineLearningSamples-AerialImageClassification](https://github.com/Azure/MachineLearningSamples-AerialImageClassification)

## <a name="use-case-description"></a>Descrição do caso de uso

Neste cenário, treinamos modelos de aprendizado de máquina para classificar o tipo de terra mostrado em imagens aéreas em gráficos de 224 metros por 224 metros. Os modelos de classificação de uso de terra podem ser usados para acompanhar a urbanização, desflorestamento, perda de mangues e outras importantes tendências ambientais usando imagens aéreas coletadas periodicamente. Preparamos conjuntos de imagem de treinamento e de validação com base em imagens do U.S. National Agriculture Imagery Program (Programa nacional dos EUA de imagens de agricultura) e rótulos de uso de terra publicados pelo U.S. National Land Cover Database (Banco de dados nacional dos EUA de cobertura de terra). São mostradas abaixo imagens de exemplo em cada classe de uso da terra:

![Regiões de exemplo para cada rótulo de uso da terra](media/scenario-aerial-image-classification/example-labels.PNG)

Após treinar e validar o modelo do classificador, o aplicaremos às imagens aéreas abrangendo Middlesex County, MA – lar do Centro NERD (New England Research & Development) da Microsoft – para demonstrar como esses modelos podem ser usados para estudar tendências no desenvolvimento urbano.

Para produzir um classificador de imagem usando o aprendizado de transferência, os cientistas de dados geralmente constroem vários modelos com uma variedade de métodos e selecionam o modelo de melhor desempenho. O Azure Machine Learning Workbench pode ajudar os cientistas de dados a coordenar o treinamento em diferentes ambientes de computação, acompanhar e comparar o desempenho de vários modelos e aplicar um modelo escolhido a grandes conjuntos de dados na nuvem.

## <a name="scenario-structure"></a>Estrutura do cenário

Neste exemplo, os dados de imagem e os modelos pré-treinados são armazenados em uma conta de armazenamento do Azure. Um cluster do Azure HDInsight Spark lê esses arquivos e constrói um modelo de classificação de imagem usando o MMLSpark. O modelo treinado e suas previsões são gravados na conta de armazenamento, em que eles podem ser analisados e visualizados por um bloco de anotações do Jupyter em execução localmente. O Azure Machine Learning Workbench coordena a execução remota de scripts no cluster Spark. Ele também acompanha as métricas de precisão para vários modelos treinados usando métodos diferentes, permitindo que o usuário selecione o modelo com o melhor desempenho.

![Esquema para o cenário do mundo real de classificação de imagem aérea](media/scenario-aerial-image-classification/scenario-schematic.PNG)

As [instruções passo a passo](https://github.com/MicrosoftDocs/azure-docs-pr/tree/release-ignite-aml-v2/articles/machine-learning/) começam orientando você na criação e preparação de uma conta de armazenamento do Azure e do cluster Spark, incluindo transferência de dados e instalação de dependência. Em seguida, elas descrevem como iniciar trabalhos de treinamento e comparar o desempenho dos modelos resultantes. Por fim, elas ilustram como aplicar um modelo escolhido a um grande conjunto de imagens no cluster Spark e analisar os resultados da previsão localmente.


## <a name="set-up-the-execution-environment"></a>Configurar o ambiente de execução

As instruções a seguir orientarão você no processo de configurar o ambiente de execução para este exemplo.

### <a name="prerequisites"></a>Pré-requisitos
- Uma [conta do Azure](https://azure.microsoft.com/en-us/free/) (avaliações gratuitas estão disponíveis)
    - Você criará um cluster do HDInsight Spark com 40 nós de trabalho (total de 168 núcleos). Certifique-se de que a sua conta tem núcleos disponíveis suficientes revisando a guia "Uso + cotas" para sua assinatura no Portal do Azure.
       - Se você tiver menos núcleos disponíveis, você poderá modificar o modelo do cluster do HDInsight para diminuir o número de trabalhos provisionados. Instruções para isso são exibidas na seção "Criar o cluster do HDInsight Spark".
    - Este exemplo cria um cluster de treinamento do Batch AI com duas VMs NC6 (1 GPU, 6 vCPU). Certifique-se de que a sua conta tem núcleos disponíveis suficientes na região Leste dos EUA revisando a guia "Uso + cotas" para sua assinatura no Portal do Azure.
- [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md)
    - Siga o [Guia de início rápido de instalação e de criação](quickstart-installation.md) para instalar o Azure Machine Learning Workbench e criar as Contas de gerenciamento de modelos e de experimentação.
- SDK do Python do [Batch AI](https://github.com/Azure/BatchAI) e CLI do Azure 2.0
    - Instale o SDK do Batch AI e a CLI do Azure 2.0 seguindo as instruções da [seção Pré-requisitos de receitas](https://github.com/Azure/BatchAI/tree/master/recipes).
        - A partir da redação deste artigo, o Azure Machine Learning Workbench usa um fork separado da CLI do Azure 2.0. Para maior clareza, nós nos referimos à versão do Workbench da CLI como "uma CLI iniciada no Azure Machine Learning Workbench" e a versão de lançamento geral (que inclui o Batch AI) como "CLI do Azure 2.0".
    - Crie uma entidade de serviço e um aplicativo do Azure Active Directory seguindo [essas instruções](https://github.com/Azure/azure-sdk-for-python/wiki/Contributing-to-the-tests#getting-azure-credentials). Registre a ID do cliente, o segredo e a ID de locatário.
- [AzCopy](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy), um utilitário gratuito para coordenar a transferência de arquivos entre contas de armazenamento do Azure
    - Certifique-se de que a pasta que contém o executável AzCopy está na variável de ambiente PATH do seu sistema. (Instruções sobre como modificar as variáveis de ambiente estão disponíveis [aqui](https://support.microsoft.com/en-us/help/310519/how-to-manage-environment-variables-in-windows-xp).)
- Um cliente SSH. Recomendamos o [PuTTY](http://www.putty.org/).

Este exemplo foi testado em um computador Windows 10. Você deve poder executá-lo em qualquer computador Windows, incluindo Máquinas Virtuais de Ciência de Dados do Azure. A CLI do Azure 2.0 foi instalada de um MSI de acordo com [estas instruções](https://github.com/Azure/azure-sdk-for-python/wiki/Contributing-to-the-tests#getting-azure-credentials). Pequenas modificações podem ser necessárias (por exemplo, as alterações em filepaths) ao executar este exemplo no macOS.

### <a name="set-up-azure-resources"></a>Configurar recursos do Azure

Este exemplo requer um cluster do HDInsight Spark e uma conta de armazenamento do Azure para hospedar arquivos relevantes. Siga estas instruções para criar esses recursos em um novo grupo de recursos do Azure:

#### <a name="create-a-new-workbench-project"></a>Criar um novo projeto de Workbench

Crie um novo projeto usando este exemplo como modelo:
1.  Abra o Azure Machine Learning Workbench
2.  Na página **Projetos**, clique no sinal **+** e selecione **Novo Projeto**
3.  No painel **Criar Novo Projeto**, preencha as informações do seu novo projeto
4.  Na caixa de pesquisa **Pesquisar modelos de projeto**, digite "Classificação de imagem aérea" e selecione o modelo
5.  Clique em **Criar**
 
#### <a name="create-the-resource-group"></a>Criar o grupo de recursos

1. Após carregar seu projeto do Azure Machine Learning Workbench, abra uma CLI (Interface de Linha de Comando) clicando em Arquivo -> Abrir prompt de comando.
    Use essa versão da CLI em grande parte desse tutorial. (Onde indicado, você será solicitado a abrir outra versão da CLI para preparar recursos do Batch AI).

1. Na interface de linha de comando, faça logon em sua conta do Azure executando o seguinte comando:

    ```
    az login
    ```

    Você será solicitado a acessar uma URL e digitar um código temporário fornecido. O site solicitará as credenciais da sua conta do Azure.
    
1. Depois de fazer logon, retorne à CLI e execute o comando a seguir a fim de determinar quais assinaturas do Azure estão disponíveis para sua conta do Azure:

    ```
    az account list
    ```

    Este comando enumera todas as assinaturas associadas à conta do Azure. Localize a ID da assinatura a ser utilizada. Escreva a ID da assinatura no local indicado no comando a seguir e, em seguida, a assinatura ativa, executando o seguinte comando:

    ```
    az account set --subscription [subscription ID]
    ```

1. Os recursos do Azure criados neste exemplo são armazenados juntos em um grupo de recursos do Azure. Escolha um nome exclusivo para o grupo de recursos e escreva-o no local indicado. Em seguida, execute os dois comandos para criar o grupo de recursos do Azure:

    ```
    set AZURE_RESOURCE_GROUP=[resource group name]
    az group create --location eastus --name %AZURE_RESOURCE_GROUP%
    ```

#### <a name="create-the-storage-account"></a>Criar a conta de armazenamento

Agora, está criada a conta de armazenamento que hospeda os arquivos de projeto que devem ser acessados pelo HDInsight Spark.

1. Escolha um nome exclusivo para a conta de armazenamento e escreva-o no local indicado no comando `set` a seguir. Em seguida, crie uma conta de armazenamento do Azure executando os dois comandos:

    ```
    set STORAGE_ACCOUNT_NAME=[storage account name]
    az storage account create --name %STORAGE_ACCOUNT_NAME% --resource-group %AZURE_RESOURCE_GROUP% --sku Standard_LRS
    ```

1. Liste as chaves de conta de armazenamento executando o seguinte comando:

    ```
    az storage account keys list --resource-group %AZURE_RESOURCE_GROUP% --account-name %STORAGE_ACCOUNT_NAME%
    ```

    Registre o valor de `key1` como chave de armazenamento no comando a seguir. Depois, execute o comando para armazenar o valor.
    ```
    set STORAGE_ACCOUNT_KEY=[storage account key]
    ```
1. Crie um compartilhamento de arquivos chamado `baitshare` em sua conta de armazenamento com o seguinte comando:

    ```
    az storage share create --account-name %STORAGE_ACCOUNT_NAME% --account-key %STORAGE_ACCOUNT_KEY% --name baitshare
    ```
1. No seu editor de texto favorito, carregue o arquivo `settings.cfg` do subdiretório “Código” do projeto do Azure Machine Learning Workbench e insira o nome da conta de armazenamento e a chave, conforme indicado. Salve e feche o arquivo `settings.cfg`.
1. Se isso ainda não foi feito, faça download e instale o utilitário [AzCopy](http://aka.ms/downloadazcopy). Verifique se o arquivo executável do AzCopy está no caminho do sistema digitando “AzCopy” e pressionando a tecla Enter para mostrar a documentação.
1. Emita os seguintes comandos para copiar todos os dados de exemplo, modelos pré-treinados e scripts de treinamento de modelo para os locais apropriados na conta de armazenamento:

    ```
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/test /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/test /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/train /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/train /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/middlesexma2016 /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/middlesexma2016 /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/pretrainedmodels /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/pretrainedmodels /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/pretrainedmodels /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.file.core.windows.net/baitshare/pretrainedmodels /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/scripts /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.file.core.windows.net/baitshare/scripts /DestKey:%STORAGE_ACCOUNT_KEY% /S
    ```

    A transferência de arquivo deve durar cerca de 20 minutos. Enquanto espera, prossiga para a seguinte seção: talvez seja necessário abrir outra Interface de Linha de Comando por meio do Workbench e redefinir as variáveis temporárias.

#### <a name="create-the-hdinsight-spark-cluster"></a>Criar o cluster do HDInsight Spark

O método recomendado para criar um cluster do HDInsight usa o modelo do gerenciador de recursos do cluster do HDInsight Spark incluso na subpasta "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning" desse projeto.

1. O modelo do cluster do HDInsight Spark é o arquivo “template.json” na subpasta “Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning” desse projeto. Por padrão, o modelo cria um cluster Spark com 40 nós de trabalho. Se for necessário ajustar esse número, abra o modelo no seu editor de texto favorito e substitua todas as instâncias de “40” pelo número do nó de trabalho de sua escolha.
    - Erros de memória insuficiente poderão ser encontrados se o número de nós de trabalho for pequeno. Para evitar erros de memória, execute os scripts de treinamento e operacionalização em um subconjunto dos dados disponíveis, conforme descrito mais adiante neste documento.
2. Escolha um nome exclusivo e uma senha para o cluster do HDInsight e escreva-os no local indicado no comando a seguir: depois, crie o cluster emitindo os comandos:

    ```
    set HDINSIGHT_CLUSTER_NAME=[HDInsight cluster name]
    set HDINSIGHT_CLUSTER_PASSWORD=[HDInsight cluster password]
    az group deployment create --resource-group %AZURE_RESOURCE_GROUP% --name hdispark --template-file "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning\template.json" --parameters storageAccountName=%STORAGE_ACCOUNT_NAME%.blob.core.windows.net storageAccountKey=%STORAGE_ACCOUNT_KEY% clusterName=%HDINSIGHT_CLUSTER_NAME% clusterLoginPassword=%HDINSIGHT_CLUSTER_PASSWORD%
    ```

A implantação do cluster pode levar até 30 minutos (incluindo a execução do provisionamento e da ação de script).

### <a name="set-up-batch-ai-resources"></a>Configurar recursos do Batch AI

Enquanto aguarda a transferência de arquivo da conta de armazenamento e a conclusão da implantação de cluster do Spark, você pode preparar o NFS (Servidor de Arquivos de Rede) do Batch AI e o cluster de GPU. Abra um prompt de comando da CLI do Azure 2.0 e execute o seguinte comando:

```
az --version 
```

Confirme se `batchai` está listado entre os módulos instalados. Se não estiver, você poderá estar usando uma Interface de Linha de Comando diferente (por exemplo, uma aberta pelo Workbench).

Em seguida, verifique se o registro do provedor foi concluído com êxito. (O registro do provedor leva até quinze minutos e ainda pode estar em andamento se você concluiu recentemente as [instruções de configuração do Batch AI](https://github.com/Azure/BatchAI/tree/master/recipes).) Verifique se "Microsoft.Batch" e "Microsoft.BatchAI" são exibidos com o status "Registrado" na saída do comando a seguir:

```
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Caso contrário, execute os seguintes comandos de registro do provedor e aguarde aproximadamente 15 minutos para o registro concluir.
```
az provider register --namespace Microsoft.Batch
az provider register --namespace Microsoft.BatchAI
```

Modifique os comandos a seguir para substituir as expressões entre colchetes com os valores que você usou anteriormente durante a criação de conta de armazenamento e grupo de recursos. Em seguida, armazene os valores como variáveis executando os comandos:
```
az account set --subscription [subscription ID]
set AZURE_RESOURCE_GROUP=[resource group name]
set STORAGE_ACCOUNT_NAME=[storage account name]
set STORAGE_ACCOUNT_KEY=[storage account key]
az configure --defaults location=eastus
az configure --defaults group=%AZURE_RESOURCE_GROUP%
```

Identifique a pasta que contém o projeto do Azure Machine Learning (por exemplo, `C:\Users\<your username>\AzureML\aerialimageclassification`). Substitua o valor entre colchetes pelo caminho de arquivo da pasta (sem barra invertida) e execute o comando:
```
set PATH_TO_PROJECT=[The filepath of your project's root directory]
```
Agora você está pronto para criar os recursos do Batch AI necessários para este tutorial.

#### <a name="prepare-the-batch-ai-network-file-server"></a>Preparar o Servidor de Arquivos de Rede do Batch AI

Seu cluster do Batch AI acessa seus dados de treinamento em um Servidor de Arquivos de Rede. O acesso a dados pode ser muito mais rápido ao acessar arquivos de um NFS em comparação a um Compartilhamento de Arquivos do Azure ou Armazenamento de Blobs do Azure.

1. Use o comando a seguir para criar um Servidor de Arquivos de Rede:

    ```
    az batchai file-server create -n landuseclassifier -u demoUser -p Dem0Pa$$w0rd --vm-size Standard_D2_V2 --disk-count 1 --disk-size 1000 --storage-sku Premium_LRS
    ```

1. Verifique o status de provisionamento do Servidor de Arquivos de Rede usando o seguinte comando:

    ```
    az batchai file-server list
    ```

    Quando o "provisioningState" do Servidor de Arquivos de Rede denominado "landuseclassifier" for "com êxito", ele estará pronto para uso. O provisionamento pode levar cerca de cinco minutos.
1. Localize o endereço IP do seu NFS na saída do comando anterior (a propriedade "fileServerPublicIp" em "mountSettings"). Grave o endereço IP onde indicado no comando a seguir, em seguida, armazene o valor executando o comando:

    ```
    set AZURE_BATCH_AI_TRAINING_NFS_IP=[your NFS IP address]
    ```

1. Usando sua ferramenta favorita de SSH (o comando de exemplo a seguir usa [PuTTY](http://www.putty.org/)), execute o script `prep_nfs.sh` do projeto em NFS para transferir os conjuntos de imagem de validação e treinamento.

    ```
    putty -ssh demoUser@%AZURE_BATCH_AI_TRAINING_NFS_IP% -pw Dem0Pa$$w0rd -m %PATH_TO_PROJECT%\Code\01_Data_Acquisition_and_Understanding\02_Batch_AI_Training_Provisioning\prep_nfs.sh
    ```

    Não se preocupe se as atualizações de andamento de download e extração de dados rolarem pela janela do shell de modo tão rápido que se tornam ilegíveis.

Se desejar, você poderá confirmar que a transferência de dados ocorreu como planejado efetuando logon no servidor de arquivos com sua ferramenta favorita de SSH e verificando os conteúdos do diretório `/mnt/data`. Você deve encontrar duas pastas, training_images e validation_images, cada uma contendo subpastas nomeadas de acordo com as categorias de uso de terra.  Os conjuntos de treinamento e validação devem conter aproximadamente 44 mil e 11 mil imagens, respectivamente.

#### <a name="create-a-batch-ai-cluster"></a>Criar um cluster do Batch AI

1. Crie o cluster emitindo os seguintes comandos:

    ```
    set AZURE_BATCHAI_STORAGE_ACCOUNT=%STORAGE_ACCOUNT_NAME%
    set AZURE_BATCHAI_STORAGE_KEY=%STORAGE_ACCOUNT_KEY%
    az batchai cluster create -n landuseclassifier -u demoUser -p Dem0Pa$$w0rd --afs-name baitshare --nfs landuseclassifier --image UbuntuDSVM --vm-size STANDARD_NC6 --max 2 --min 2 
    ```

1. Use o seguinte comando para verificar o status de provisionamento do seu cluster:

    ```
    az batchai cluster list
    ```

    Quando o estado de alocação para o cluster chamado "landuseclassifier" for alterado de redimensionamento para inalterado, será possível enviar trabalhos. No entanto, os trabalhos não começam a execução até que todas as VMs no cluster tenham deixado o estado de "preparação". Se a propriedade de "erros" do cluster não for nula, ocorreu um erro durante a criação do cluster e ele não deverá ser usado.

#### <a name="record-batch-ai-training-credentials"></a>Registrar as credenciais de treinamento do Batch AI

Enquanto você aguarda a alocação de cluster ser concluída, abra o arquivo `settings.cfg` do subdiretório "Código" deste projeto no editor de texto de sua escolha. Atualize as seguintes variáveis com suas próprias credenciais:
- `bait_subscription_id` (sua ID da assinatura do Azure com 36 caracteres)
- `bait_aad_client_id` (a ID do aplicativo/cliente do Azure Active Directory mencionada na seção "Pré-requisitos")
- `bait_aad_secret` (o segredo do aplicativo do Azure Active Directory mencionado na seção "Pré-requisitos")
- `bait_aad_tenant` (a ID do locatário do Azure Active Directory mencionada na seção "Pré-requisitos")
- `bait_region` (a partir da redação deste artigo, a única opção é: eastus)
- `bait_resource_group_name` (o grupo de recursos que você escolheu anteriormente)

Depois que você atribuiu esses valores, as linhas modificadas do arquivo settings.cfg devem se parecer com o seguinte texto:

```
[Settings]
    # Credentials for the Azure Storage account
    storage_account_name = yoursaname
    storage_account_key = kpIXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXQ==
    
    # Batch AI training credentials
    bait_subscription_id = 0caXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX9c3
    bait_aad_client_id = d0aXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX7f8
    bait_aad_secret = ygSXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX6I=
    bait_aad_tenant = 72fXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXb47
    bait_region = eastus
    bait_resource_group_name = yourrgname
```

Salve e feche `settings.cfg`.

Agora você pode fechar a janela de CLI em que você executou os comandos de criação de recurso do Batch AI. Todas as demais etapas neste tutorial usam uma CLI iniciada no Azure Machine Learning Workbench.

### <a name="prepare-the-azure-machine-learning-workbench-execution-environment"></a>Prepare o ambiente de execução do Azure Machine Learning Workbench

#### <a name="register-the-hdinsight-cluster-as-an-azure-machine-learning-workbench-compute-target"></a>Registre o cluster do HDInsight como um destino de computação do Azure Machine Learning Workbench

Depois da criação do cluster do HDInsight, registre o cluster como um destino de computação para o projeto, da seguinte maneira:

1.  Emita o seguinte comando da Interface de Linha de Comando do Azure Machine Learning:

    ```
    az ml computetarget attach --name myhdi --address %HDINSIGHT_CLUSTER_NAME%-ssh.azurehdinsight.net --username sshuser --password %HDINSIGHT_CLUSTER_PASSWORD% -t cluster
    ```

    Esse comando adiciona dois arquivos, `myhdi.runconfig` e `myhdi.compute`, à pasta `aml_config` do projeto.

1. Abra o arquivo `myhdi.compute` no seu editor de texto favorito. Modifique a linha `yarnDeployMode: cluster`, para que se leia `yarnDeployMode: client` e, em seguida, salve e feche o arquivo.
1. Execute o comando a seguir para preparar o ambiente do HDInsight para uso:
   ```
   az ml experiment prepare -c myhdi
   ```

#### <a name="install-local-dependencies"></a>Instale as dependências locais

Abra uma CLI do Azure Machine Learning Workbench e instale as dependências necessárias para execução local emitindo o comando a seguir:

```
pip install matplotlib azure-storage==0.36.0 pillow scikit-learn azure-mgmt-batchai
```

## <a name="data-acquisition-and-understanding"></a>Compreensão e aquisição de dados

Esse cenário usa dados de imagens aéreas disponíveis publicamente, do [National Agriculture Imagery Program](https://www.fsa.usda.gov/programs-and-services/aerial-photography/imagery-programs/naip-imagery/), a uma resolução de um metro. Geramos conjuntos de arquivos PNG de 224 x 224 pixels recortados dos dados originais do NAIP e classificados de acordo com rótulos de uso de terra do [National Land Cover Database](https://www.mrlc.gov/nlcd2011.php). Uma imagem de exemplo com o rótulo “Desenvolvido” é mostrada em tamanho máximo:

![Um bloco de exemplo de terra desenvolvida](media/scenario-aerial-image-classification/sample-tile-developed.png)

Conjuntos com balanceamento de classe de aproximadamente 44k e imagens de 11k são usados para treinamento e validação de modelos, respectivamente. Demonstramos a implantação do modelo em um conjunto de imagens de aproximadamente 67k, definindo em blocos o Condado de Middlesex, nos EUA, lar do centro NERD (New England Research and Development) da Microsoft. Para saber mais sobre como esses conjuntos de imagens foram criados, consulte o [Repositório do Git Classificação de Imagem Embaraçosamente Paralela](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification).

![Condado de Middlesex, Massachusetts](media/scenario-aerial-image-classification/middlesex-ma.png)

Durante a instalação, os conjuntos de imagem aéreas usados neste exemplo foram transferidos para a conta de armazenamento que você criou. As imagens de treinamento, validação e operacionalização são todas arquivos PNG de 224 x 224 pixels a uma resolução de um pixel por metro quadrado. As imagens de treinamento e validação foram organizadas nas subpastas com base no rótulo de uso de terra. (Os rótulos de uso de terra das imagens de operacionalização são desconhecidos e, em muitos casos, ambíguos. Algumas dessas imagens contêm vários tipos de terra). Para saber mais sobre como esses conjuntos de imagens foram criados, consulte o [Repositório do Git Classificação de Imagem Embaraçosamente Paralela](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification).

Para exibir imagens de exemplo na conta de armazenamento do Azure (opcional):
1. Faça logon no [Portal do Azure](https://portal.azure.com).
1. Pesquise o nome da conta de armazenamento na barra de pesquisa na parte superior da tela. Clique na conta de armazenamento nos resultados da pesquisa.
2. Clique no link “Blobs” no painel principal da conta de armazenamento.
3. Clique no contêiner de nome “trem”. Uma lista de diretórios nomeada de acordo com o uso de terra será exibida.
4. Clique em um desses diretórios para carregar a lista de imagens contida nela.
5. Clique em qualquer imagem e faça download para exibi-la.
6. Se quiser, clique nos contêineres nomeados como “teste” e “middlesexma2016” para exibir os conteúdos deles também.

## <a name="modeling"></a>Modelagem

### <a name="training-models-with-azure-batch-ai"></a>Modelos de treinamento com o Azure Batch AI

O script `run_batch_ai.py` na subpasta “Code\02_Modeling” do projeto do Workbench é usado para emitir um trabalho de treinamento do Batch AI. Esse trabalho treina novamente um classificador de imagem DNN escolhido pelo usuário (AlexNet ou ResNet 18 pré-treinado no ImageNet). A profundidade de retreinamento também pode ser especificada: o retreinamento apenas da camada final de rede pode reduzir o sobreajuste quando alguns exemplos de treinamento estiverem disponíveis, enquanto ajustar toda a rede (ou, para AlexNet, as camadas totalmente conectadas) pode resultar em um maior desempenho de modelo quando o conjunto de treinamento for grande o suficiente.

Quando o trabalho de treinamento for concluído, esse script salvará o modelo (junto com um arquivo que descreve o mapeamento entre a saída de inteiro do modelo e os rótulos de cadeia de caracteres) e as previsões para o armazenamento de blob. O arquivo de log do trabalho BAIT é analisado para extrair o curso de tempo de melhoria da taxa de erro em relação às épocas de treinamento. O curso de tempo de aprimoramento da taxa de erro é registrado no recurso de histórico de execução do AML Workbench para análise posterior.

Selecione um nome para o modelo treinado, um tipo de modelo pré-treinado e uma intensidade de retreinamentos. Grave suas seleções no local indicado no comando a seguir, depois, comece novamente o treinamento executando o comando de uma Interface de Linha de Comando do Azure ML:

```
az ml experiment submit -c local Code\02_Modeling\run_batch_ai.py --config_filename Code/settings.cfg --output_model_name [unique model name, alphanumeric characters only] --pretrained_model_type {alexnet,resnet18} --retraining_type {last_only,fully_connected,all} --num_epochs 10
```

A execução do Azure Machine Learning pode levar cerca de meia hora para ser concluída. É recomendável que você execute alguns comandos semelhantes (variando o nome do modelo de saída, o tipo de modelo pré-treinado e a profundidade de retreinamentos) para poder comparar o desempenho dos modelos treinados com métodos diferentes.

### <a name="training-models-with-mmlspark"></a>Modelos de treinamento com MMLSpark

O script `run_mmlspark.py` na subpasta “Code\02_Modeling” do projeto do Workbench é usado para treinar um modelo [MMLSpark](https://github.com/Azure/mmlspark) para classificação de imagens. Primeiro, o script destaca as imagens do conjunto de treinamento usando um classificador de imagens DNN pré-treinado no conjunto de dados ImageNet (AlexNet ou um ResNet de 18 camadas). Então, o script usa as imagens destacadas para treinar o modelo MMLSpark (um modelo de floresta aleatória ou regressão logística), a fim de classificar as imagens. O conjunto de imagens de teste, depois, é destacado e pontuado com o modelo treinado. A precisão das previsões do modelo no conjunto de testes é calculada e registrada no recurso de histórico de execuções do Azure Machine Learning Workbench. Por fim, o modelo treinado MMLSpark e suas previsões no conjunto de testes são salvos no armazenamento de blobs.

Selecione um nome exclusivo para o modo de saída do modelo treinado, um tipo de modelo pré-treinado e um tipo de modelo MMLSpark. Grave suas seleções no local indicado no modelo de comando a seguir, depois, comece novamente o treinamento executando o comando de uma Interface de Linha de Comando do Azure ML:

```
az ml experiment submit -c myhdi Code\02_Modeling\run_mmlspark.py --config_filename Code/settings.cfg --output_model_name [unique model name, alphanumeric characters only] --pretrained_model_type {alexnet,resnet18} --mmlspark_model_type {randomforest,logisticregression}
```

Outro parâmetro `--sample_frac` pode ser usado para treinar e testar o modelo com um subconjunto de dados disponíveis. Usar uma pequena fração de exemplo diminui o tempo de execução e os requisitos de memória, às custas da precisão do modelo treinado. (Por exemplo, uma execução com `--sample_frac 0.1` deve levar aproximadamente vinte minutos.) Para saber mais sobre esse e outros parâmetros, execute `python Code\02_Modeling\run_mmlspark.py -h`.

Os usuários são incentivados a executar esse script várias vezes, com diferentes parâmetros de entrada. O desempenho dos modelos resultantes pode ser comparado, em seguida, no recurso Histórico de Execuções do Azure Machine Learning Workbench.

### <a name="comparing-model-performance-using-the-workbench-run-history-feature"></a>Comparando o desempenho do modelo com o recurso Histórico de Execuções do Workbench

Depois de duas ou mais execuções de treinamento de qualquer tipo, navegue até o recurso Histórico de Execuções no Workbench clicando no ícone de relógio na barra de menus à esquerda. Selecione `run_mmlspark.py` na lista de scripts à esquerda. O painel carregado compara a precisão do conjunto de testes em todas as execuções. Para ver mais detalhes, role para baixo e clique no nome de uma execução individual.

## <a name="deployment"></a>Implantação

Para aplicar um dos modelos treinados às imagens aéreas em bloco do Condado de Middlesex, nos EUA, usando execução remota no HDInsight, insira o nome do modelo desejado no comando a seguir e execute-o:

```
az ml experiment submit -c myhdi Code\03_Deployment\batch_score_spark.py --config_filename Code/settings.cfg --output_model_name [trained model name chosen earlier]
```

Outro parâmetro `--sample_frac` pode ser usado para operacionalizar o modelo com um subconjunto de dados disponíveis. Usar uma pequena fração de exemplo diminui o tempo de execução e os requisitos de memória, às custas da integridade da previsão. Para saber mais sobre esse e outros parâmetros, execute `python Code\03_Deployment\batch_score_spark -h`.

Esse script grava as previsões do modelo na conta de armazenamento. As previsões podem ser examinadas conforme descrito na próxima seção.

## <a name="visualization"></a>Visualização

O notebook do Jupyter “Análise de previsão do modelo”, na subpasta “Code\04_Result_Analysis” do projeto do Workbench, visualiza as previsões de um modelo. Carregar e executar o notebook da seguinte maneira:
1. Abra o projeto no Workbench e clique no ícone da pasta (“Arquivos”) no menu à esquerda para carregar a lista de diretórios.
2. Navegue até a subpasta “Code\04_Result_Analysis” e clique no notebook de nome “Análise de previsão do modelo”. Uma versão prévia que renderiza o notebook deve ser exibida.
3. Clique em “Iniciar Servidor do Notebook” para carregar o notebook.
4. Na primeira célula, digite o nome do modelo cujos resultados você gostaria de analisar no local indicado.
5. Clique em “Célula > Executar tudo” para executar todas as células do notebook.
6. Leia junto com o notebook para saber mais sobre as análises e visualizações apresentadas.

## <a name="cleanup"></a>Limpeza
Quando o exemplo for concluído, recomenda-se excluir todos os recursos criados executando o seguinte comando da Interface de Linha de Comando do Azure:

  ```
  az group delete --name %AZURE_RESOURCE_GROUP%
  ```

## <a name="references"></a>Referências

- [Repositório de Classificação de Imagem Embaraçosamente Paralelo](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification)
   - Descreve a criação de um conjunto de dados de imagens e rótulos disponíveis gratuitamente
- Repositório do GitHub [MMLSpark](https://github.com/Azure/mmlspark)
   - Contém mais exemplos de treinamento e avaliação de modelo com MMLSpark

## <a name="conclusions"></a>Conclusões

O Azure Machine Learning Workbench ajuda cientistas de dados a implantar código facilmente em destinos de computação remotos. Neste exemplo, o código de treinamento do MMLSpark local foi implantado para execução remota em um cluster do HDInsight e um script local iniciou um trabalho de treinamento em um cluster de GPU do Azure Batch AI. O recurso de histórico de execuções do Azure Machine Learning Workbench acompanhou o desempenho de vários modelos e ajudou a identificar o modelo mais preciso. Os recurso de notebooks Jupyter do Workbench ajudaram a visualizar as previsões dos nossos modelos em um ambiente interativo e gráfico.

## <a name="next-steps"></a>Próximas etapas
Para aprofundar-se neste exemplo:
- No recurso Histórico de Execuções do Azure Machine Learning Workbench, clique nos símbolos de engrenagem para selecionar quais gráficos e métricas serão exibidos.
- Examine os scripts de exemplo de instruções que chamam o `run_logger`. Verifique se você entendeu como cada métrica está sendo gravada.
- Examine os scripts de exemplo de instruções que chamam o `blob_service`. Verifique se você entendeu como os modelos treinados e previsões são armazenados e recuperados na nuvem.
- Explore o conteúdo dos contêineres criados na conta de armazenamento de blobs. Verifique se você entendeu qual script ou comando é responsável pela criação de cada grupo de arquivos.
- Modifique o script de treinamento para treinar um tipo de modelo MMLSpark diferente ou para alterar os hiperparâmetros modelo. Use o recurso Histórico de Execuções para determinar se as alterações aumentaram ou diminuíram a precisão do modelo.
