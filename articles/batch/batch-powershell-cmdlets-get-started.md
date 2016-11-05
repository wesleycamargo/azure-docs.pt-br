---
title: Introdução aos cmdlets do PowerShell do Lote do Azure | Microsoft Docs
description: Obter uma rápida introdução aos cmdlets do Azure PowerShell que você pode usar para gerenciar o serviço Lote do Azure
services: batch
documentationcenter: ''
author: dlepow
manager: timlt
editor: ''

ms.service: batch
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: powershell
ms.workload: big-compute
ms.date: 07/28/2016
ms.author: danlep

---
# Introdução aos cmdlets do PowerShell para o Azure Batch
Com os cmdlets do PowerShell do Lote do Azure, você pode executar e criar scripts para muitas das mesmas tarefas que executa com as APIs do Lote, o portal do Azure e a CLI (Interface de Linha de Comando) do Azure. Essa é uma breve introdução aos cmdlets que você pode usar para gerenciar suas contas do Lote e trabalhar com seus recursos do Lote, como pools, trabalhos e tarefas. Este artigo baseia-se nos cmdlets do Azure PowerShell versão 1.6.0.

Para obter uma lista completa de cmdlets do Lote e a sintaxe detalhada do cmdlet, consulte a [Referência de cmdlet do Lote do Azure](https://msdn.microsoft.com/library/azure/mt125957.aspx).

## Pré-requisitos
* **PowerShell do Azure** ‒ consulte [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter instruções para baixar e instalar o Azure PowerShell.
  
  * Como os cmdlets do Lote do Azure são fornecidos no módulo do Azure Resource Manager, você precisará executar o cmdlet **Login-AzureRmAccount** para se conectar à sua assinatura.
  * É recomendável que você atualize o Azure PowerShell com frequência para tirar proveito de atualizações e aprimoramentos do serviço.
* **Registre-se no namespace do provedor do Lote (operação única)** - Antes de trabalhar com suas contas do Lote, você deverá registrar-se no namespace do provedor do Lote. Essa operação só precisa ser executada uma vez por assinatura. Execute o cmdlet a seguir:
  
        Register-AzureRMResourceProvider -ProviderNamespace Microsoft.Batch

## Gerenciar contas e chaves do Batch
### Criar uma conta do Batch
**New-AzureRmBatchAccount** cria uma nova conta do Lote em um grupo de recursos especificado. Se você ainda não tiver um grupo de recursos, crie um executando o cmdlet [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/azure/mt603739.aspx), especificando uma das regiões do Azure no parâmetro **Location**, como “EUA Central”. Por exemplo:

    New-AzureRmResourceGroup –Name MyBatchResourceGroup –location "Central US"


Em seguida, crie uma nova conta do Lote no grupo de recursos, especificando um nome para a conta em <*nome\_da\_conta*> e o local e o nome de seu grupo de recursos. A criação da conta de lote pode levar algum tempo para ser concluída. Por exemplo:

    New-AzureRmBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName MyBatchResourceGroup

> [!NOTE]
> O nome da conta do Lote deve ser exclusivo na região do Azure para o grupo de recursos, conter entre três e 24 caracteres e usar somente números e letras minúsculas.
> 
> 

### Obter chaves de acesso da conta
**Get-AzureRmBatchAccountKeys** mostra as chaves de acesso associadas a uma conta do Lote do Azure. Por exemplo, execute o seguinte para obter as chaves primária e secundária da conta criada por você.

    $Account = Get-AzureRmBatchAccountKeys –AccountName <accountname>

    $Account.PrimaryAccountKey

    $Account.SecondaryAccountKey


### Gerar uma nova chave de acesso
**New-AzureRmBatchAccountKey** gera uma nova chave de conta primária ou secundária para uma conta do Lote do Azure. Por exemplo, para gerar uma nova chave primária para a sua conta do Batch, digite:

    New-AzureRmBatchAccountKey -AccountName <account_name> -KeyType Primary


> [!NOTE]
> Para gerar uma nova chave secundária, especifique "Secondary" para o parâmetro **KeyType**. É necessário gerar novamente as chaves primária e secundária separadamente.
> 
> 

### Excluir uma conta do Batch
**Remove-AzureRmBatchAccount** exclui uma conta do Lote. Por exemplo:

    Remove-AzureRmBatchAccount -AccountName <account_name>

Quando solicitado, confirme que você deseja remover a conta. A remoção de conta pode levar algum tempo para ser concluída.

## Criar um objeto BatchAccountContext
Para se autenticar usando os cmdlets do PowerShell do Lote ao criar e gerenciar pools, trabalhos, tarefas e outros recursos do Lote, primeiro crie um objeto BatchAccountContext para armazenar o nome e as chaves da conta:

    $context = Get-AzureRmBatchAccountKeys -AccountName <account_name>

Você passa o objeto BatchAccountContext para os cmdlets que usam o parâmetro **BatchContext**.

> [!NOTE]
> Por padrão, a chave primária da conta é usada para autenticação, mas você pode selecionar a chave a ser usada alterando a propriedade **KeyInUse** do seu objeto de BatchAccountContext: `$context.KeyInUse = "Secondary"`.
> 
> 

## Criar e modificar recursos do Lote
Use cmdlets como **New-AzureBatchPool**, **New-AzureBatchJob** e **New-AzureBatchTask** para criar recursos em uma conta do Lote. Há cmdlets correspondentes **Get-** e **Set-** para atualizar as propriedades de recursos existentes e cmdlets **Remove-** para remover recursos em uma conta do Lote.

Ao usar muitos desses cmdlets, além de passar um objeto BatchContext, você precisa criar ou passar objetos que contêm as configurações detalhadas de recursos, conforme mostrado no exemplo a seguir. Confira a ajuda detalhada de cada cmdlet para obter exemplos adicionais.

### Criar um pool do Lote
Ao criar ou atualizar um pool de lote, selecione uma configuração de serviço de nuvem ou uma configuração de máquina virtual para o sistema operacional em nós de computação (confira [Visão geral do recurso de Lote](batch-api-basics.md#pool)). Sua escolha determina se os nós de computação têm a imagem gerada com uma do [versões de SO Convidado do Azure](../cloud-services/cloud-services-guestos-update-matrix.md#releases) ou com uma das imagens de VM com Linux ou Windows com suporte no Azure Marketplace.

Ao executar **New-AzureBatchPool**, passe as configurações do sistema operacional em um objeto PSCloudServiceConfiguration ou PSVirtualMachineConfiguration. Por exemplo, o cmdlet a seguir cria um novo pool do Lote com nós de computação de tamanho pequeno na configuração de serviço de nuvem, com a imagem criada com a última versão do sistema operacional da família 3 (Windows Server 2012). Aqui, o parâmetro **CloudServiceConfiguration** especifica a variável *$configuration* do objeto PSCloudServiceConfiguration. O parâmetro **BatchContext** especifica uma variável definida anteriormente *$context* como o objeto BatchAccountContext.

    $configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSCloudServiceConfiguration" -ArgumentList @(3,"*")

    New-AzureBatchPool -Id "AutoScalePool" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -AutoScaleFormula '$TargetDedicated=4;' -BatchContext $context

O número de nós de computação no novo pool de destino é determinado por uma fórmula de dimensionamento automático. Nesse caso, a fórmula é simplesmente **$TargetDedicated=4**, indicando que o número de nós de computação no pool é 4, no máximo.

## Consultar pools, trabalhos, tarefas e outros detalhes
Use cmdlets como**Get-AzureBatchPool** , **Get-AzureBatchJob** e **Get-AzureBatchTask** para consultar entidades criadas em uma conta do Lote.

### Consultar dados
Por exemplo, use **Get-AzureBatchPools** para encontrar os pools. Por padrão, isso consulta todos os pools em sua conta, supondo que você já tenha armazenado o objeto BatchAccountContext em *$context*:

    Get-AzureBatchPool -BatchContext $context

### Usar um filtro OData
Você pode fornecer um filtro OData usando o parâmetro **Filter** para localizar apenas os objetos de seu interesse. Por exemplo, você pode localizar todos os pools com ids que começam com "myPool":

    $filter = "startswith(id,'myPool')"

    Get-AzureBatchPool -Filter $filter -BatchContext $context


Esse método não é tão flexível quanto usar "Where-Object" em um pipeline local. No entanto, a consulta é enviada para o serviço Batch diretamente para que toda a filtragem ocorra no servidor, poupando largura de banda de Internet.

### Use o parâmetro de Id
Uma alternativa a um filtro OData é usar o parâmetro **Id**. Para consultar um pool específico com id "myPool":

    Get-AzureBatchPool -Id "myPool" -BatchContext $context


O parâmetro **ID** só dá suporte à pesquisa de ID completo, não a curingas ou a filtros de estilo OData.

### Usar o parâmetro MaxCount
Por padrão, cada cmdlet retorna no máximo 1.000 objetos. Se você atingir esse limite, refine seu filtro para retornar menos objetos ou defina explicitamente um máximo usando o parâmetro **MaxCount**. Por exemplo:

    Get-AzureBatchTask -MaxCount 2500 -BatchContext $context

Para remover o limite superior, defina **MaxCount** como 0 ou menos.

### Usar o pipeline
Os cmdlets do Batch podem aproveitar o pipeline do PowerShell para enviar dados entre cmdlets. Isso tem o mesmo efeito que especificar um parâmetro, mas facilita a listagem de várias entidades. Por exemplo, usar o seguinte localiza todas as tarefas em sua conta:

    Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context


## Próximas etapas
* Para obter a sintaxe detalhada do cmdlet, veja [Referência de cmdlet do Lote do Azure](https://msdn.microsoft.com/library/azure/mt125957.aspx).
* Veja [Consultar o serviço Lote com eficiência](batch-efficient-list-queries.md) para saber mais sobre a redução do número de itens e sobre o tipo de informação retornado por consultas do Lote.

<!---HONumber=AcomDC_0803_2016-->