---
title: "Introdução à CLI do Azure do Lote | Microsoft Docs"
description: "Obtenha uma introdução rápida dos comandos do Lote na CLI do Azure para gerenciar recursos de serviço do Lote do Azure"
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: fcd76587-1827-4bc8-a84d-bba1cd980d85
ms.service: batch
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 01/23/2017
ms.author: tamram
translationtype: Human Translation
ms.sourcegitcommit: bf22cd3426e936c8d74377f59443e5e1a6834286
ms.openlocfilehash: a2efffa5e250e02fec1a1834c9256ffef79b9be3


---
# <a name="get-started-with-azure-batch-cli"></a>Introdução à CLI do Lote do Azure
A CLI (Interface de linha de comando) do Azure de plataforma cruzada permite que você gerencie suas contas e recursos do Lote, como pools, trabalhos e tarefas em shells de comando do Linux, Mac e Windows. Com a CLI do Azure, você pode executar e criar scripts para diversas tarefas que você executa com as APIs do Lote, com o Portal do Azure e com cmdlets de PowerShell do Lote.

Este artigo se baseia na CLI do Azure versão 0.10.5.

## <a name="prerequisites"></a>Pré-requisitos
* [Instalar a CLI do Azure](../xplat-cli-install.md)
* [Conectar a CLI do Azure à sua assinatura do Azure](../xplat-cli-connect.md)
* Mudar para o **modo do Resource Manager**: `azure config mode arm`

> [!TIP]
> Recomendamos que você atualize frequentemente sua instalação da CLI do Azure para aproveitar as atualizações e aprimoramentos do serviço.
> 
> 

## <a name="command-help"></a>Ajuda de comando
Você pode exibir um texto de ajuda para todo comando na CLI do Azure acrescentando `-h` como a única opção após o comando. Por exemplo:

* Para obter ajuda sobre o comando `azure`, insira: `azure -h`
* Para obter uma lista de todos os comandos do Lote na CLI, use: `azure batch -h`
* Para obter ajuda com a criação de uma conta do Lote, insira: `azure batch account create -h`

Em caso de dúvida, use a opção de linha de comando `-h` para obter ajuda sobre qualquer comando da CLI do Azure.

## <a name="create-a-batch-account"></a>Criar uma conta do Batch
Uso:

    azure batch account create [options] <name>

Exemplo:

    azure batch account create --location "West US"  --resource-group "resgroup001" "batchaccount001"

Cria uma nova conta do Lote com os parâmetros especificados. Você deve especificar pelo menos um local, um grupo de recursos e uma conta. Se você ainda não tiver um grupo de recursos, crie um executando `azure group create` e especifique uma das regiões do Azure (como "Oeste dos EUA") para a opção `--location`. Por exemplo:

    azure group create --name "resgroup001" --location "West US"

> [!NOTE]
> O nome da conta do Lote deve ser exclusivo na região do Azure em que a conta é criada. Ele pode conter somente caracteres alfanuméricos minúsculos e deve ter de três a 24 caracteres. Você não pode usar caracteres especiais, como `-` ou `_`, em nomes de conta do Lote.
> 
> 

### <a name="linked-storage-account-autostorage"></a>Conta de armazenamento vinculada (armazenamento automático)
Você pode (opcionalmente) vincular uma conta de armazenamento de **Finalidade geral** à sua conta do Lote durante a criação. O recurso [pacotes de aplicativos](batch-application-packages.md) do Lote usa o armazenamento de blobs em uma conta do Armazenamento de Uso geral vinculada, como faz a biblioteca [.NET de Convenções de Arquivo do Lote](batch-task-output.md). Esses recursos opcionais ajudarão você a implantar os aplicativos executados por suas tarefas do Lote, persistindo os dados que eles produzem.

Para vincular uma Conta de armazenamento existente do Azure a uma nova conta do Lote durante sua criação, especifique a opção `--autostorage-account-id` . Essa opção exige a ID de recurso totalmente qualificada da conta de armazenamento.

Primeiro, mostre os detalhes de sua conta de armazenamento:

    azure storage account show --resource-group "resgroup001" "storageaccount001"

Em seguida, use o valor **Url** para a opção `--autostorage-account-id`. O valor da Url começa com "/subscriptions/" e contém a ID de sua assinatura e o caminho de recurso até a Conta de armazenamento:

    azure batch account create --location "West US"  --resource-group "resgroup001" --autostorage-account-id "/subscriptions/8ffffff8-4444-4444-bfbf-8ffffff84444/resourceGroups/resgroup001/providers/Microsoft.Storage/storageAccounts/storageaccount001" "batchaccount001"

## <a name="delete-a-batch-account"></a>Excluir uma conta do Batch
Uso:

    azure batch account delete [options] <name>

Exemplo:

    azure batch account delete --resource-group "resgroup001" "batchaccount001"

Exclui a conta especificada do Lote. Quando receber uma solicitação, confirme que deseja remover a conta (a remoção da conta pode demorar algum tempo para ser concluída).

## <a name="manage-account-access-keys"></a>Gerenciar as chaves de acesso da conta
Você precisa de uma chave de acesso para [criar e modificar recursos](#create-and-modify-batch-resources) em sua conta do Lote.

### <a name="list-access-keys"></a>Listar as chaves de acesso
Uso:

    azure batch account keys list [options] <name>

Exemplo:

    azure batch account keys list --resource-group "resgroup001" "batchaccount001"

Lista as chaves de conta da conta especificada do Lote.

### <a name="generate-a-new-access-key"></a>Gerar uma nova chave de acesso
Uso:

    azure batch account keys renew [options] --<primary|secondary> <name>

Exemplo:

    azure batch account keys renew --resource-group "resgroup001" --primary "batchaccount001"

Regenera a chave da conta especificada para a conta especificada do Lote.

## <a name="create-and-modify-batch-resources"></a>Criar e modificar recursos do Lote
Você pode usar a CLI do Azure para criar, ler, atualizar e excluir (CRUD) recursos do Lote, como pools, nós de computação, trabalhos e tarefas. Essas operações de CRUD exigem o nome da conta do Lote, a chave de acesso e o ponto de extremidade. Você pode especificar essas informações com as opções `-a`, `-k` e `-u` ou definir as [variáveis de ambiente](#credential-environment-variables) que a CLI usa automaticamente (caso sejam preenchidas).

### <a name="credential-environment-variables"></a>Variáveis de ambiente de credencial
Você pode definir as variáveis de ambiente `AZURE_BATCH_ACCOUNT`, `AZURE_BATCH_ACCESS_KEY` e `AZURE_BATCH_ENDPOINT` em vez de especificar as opções `-a`, `-k` e `-u` na linha de comando para cada comando executado. A CLI do Lote usa essas variáveis (se forem definidas) para que você possa omitir as opções `-a`, `-k` e `-u`. O restante deste artigo pressupõe o uso dessas variáveis de ambiente.

> [!TIP]
> Liste suas chaves com `azure batch account keys list`, e exiba o ponto de extremidade da conta com `azure batch account show`.
> 
> 

### <a name="json-files"></a>Arquivos JSON
Ao criar recursos do Lote, como pools e trabalhos, você pode especificar um arquivo JSON contendo a nova configuração do recurso, em vez de passar seus parâmetros como opções da linha de comando. Por exemplo:

`azure batch pool create my_batch_pool.json`

Embora seja possível executar várias operações de criação de recursos usando apenas as opções de linha de comando, alguns recursos exigem um arquivo formatado em JSON contendo os detalhes do recurso. Por exemplo, você deve usar um arquivo JSON se quiser especificar arquivos de recurso para uma tarefa de inicialização.

Para encontrar o JSON necessário para criar um recurso, consulte a documentação de [referência da API REST do Lote][rest_api] no MSDN. Cada tópico "Adicionar *tipo de recurso*" contém o exemplo de JSON para criar o recurso, que você pode usar como modelos para seus arquivos JSON. Por exemplo, o JSON para criação do pool pode ser encontrado em [Adicionar um pool a uma conta][rest_add_pool].

> [!NOTE]
> Se você especificar um arquivo JSON durante a criação de um recurso, todos os outros parâmetros especificados na linha de comando desse recurso serão ignorados.
> 
> 

## <a name="create-a-pool"></a>Criar um pool
Uso:

    azure batch pool create [options] [json-file]

Exemplo (Configuração de Máquina Virtual):

    azure batch pool create --id "pool001" --target-dedicated 1 --vm-size "STANDARD_A1" --image-publisher "Canonical" --image-offer "UbuntuServer" --image-sku "14.04.2-LTS" --node-agent-id "batch.node.ubuntu 14.04"

Exemplo (Configuração de Serviços de Nuvem):

    azure batch pool create --id "pool002" --target-dedicated 1 --vm-size "small" --os-family "4"

Cria um pool de nós de computação no serviço do Lote.

Conforme mencionado na [Visão geral do recurso de Lote](batch-api-basics.md#pool), você tem duas opções ao selecionar um sistema operacional para os nós no pool: **Configuração de Máquina Virtual** e **Configuração dos Serviços de Nuvem**. Use as opções `--image-*` para criar pools de Configuração de Máquina Virtual, e `--os-family` para criar pools de Configuração de Serviços de Nuvem. Não é possível especificar as opções `--os-family` e `--image-*`.

Você pode especificar um pool de [pacotes de aplicativos](batch-application-packages.md) e a linha de comando para uma [tarefa inicial](batch-api-basics.md#start-task). Para especificar arquivos de recurso para a tarefa inicial, no entanto, você deve usar um [arquivo JSON](#json-files).

Exclua um pool com:

    azure batch pool delete [pool-id]

> [!TIP]
> Verifique a [lista de imagens de máquina virtual](batch-linux-nodes.md#list-of-virtual-machine-images) em busca dos valores apropriados para as opções `--image-*`.
> 
> 

## <a name="create-a-job"></a>Criar um trabalho
Uso:

    azure batch job create [options] [json-file]

Exemplo:

    azure batch job create --id "job001" --pool-id "pool001"

Adiciona um trabalho à conta do Lote e especifica o pool no qual as tarefas são executadas.

Excluir um trabalho com:

    azure batch job delete [job-id]

## <a name="list-pools-jobs-tasks-and-other-resources"></a>Listar pools, trabalhos, tarefas e outros recursos
Cada tipo de recurso do Lote dá suporte a um comando `list` que consulta sua conta do Lote e lista os recursos desse tipo. Por exemplo, você pode listar os pools em sua conta e as tarefas em um trabalho:

    azure batch pool list
    azure batch task list --job-id "job001"

### <a name="listing-resources-efficiently"></a>Listagem de recursos com eficiência
Para uma consulta mais rápida, você pode especificar opções de cláusula **select**, **filter** e **expand** para as operações `list`. Use essas opções para limitar a quantidade de dados retornada pelo serviço do Lote. Como toda a filtragem ocorre no lado do servidor, apenas os dados nos quais você está interessado são transmitidos. Use essas cláusulas para economizar largura de banda (e, portanto, tempo) ao executar operações de lista.

Por exemplo, isso retornará apenas os pools cujas IDs começam com "renderTask":

    azure batch task list --job-id "job001" --filter-clause "startswith(id, 'renderTask')"

A CLI do Lote dá suporte a todas as três cláusulas com suporte do serviço do Lote:

* `--select-clause [select-clause]` Retornar um subconjunto de propriedades para cada entidade
* `--filter-clause [filter-clause]` Retornar apenas as entidades que correspondam à expressão de OData especificada
* `--expand-clause [expand-clause]` Obter as informações de entidade em uma única chamada REST subjacente. No momento, a cláusula expand oferece suporte apenas à propriedade `stats` .

Para obter detalhes sobre as três cláusulas e executar consultas de lista com elas, confira [Consultar o serviço do Lote do Azure com eficiência](batch-efficient-list-queries.md).

## <a name="application-package-management"></a>Gerenciamento de pacote de aplicativos
Os pacotes de aplicativos fornecem uma maneira simplificada de implantar aplicativos para nós de computação em seus pools. Com a CLI do Azure, você pode carregar pacotes de aplicativos, gerenciar versões de pacote e excluir pacotes.

Para criar um novo aplicativo e adicionar uma versão de pacote:

**Criar** um aplicativo:

    azure batch application create "resgroup001" "batchaccount001" "MyTaskApplication"

**Adicionar** um pacote de aplicativos:

    azure batch application package create "resgroup001" "batchaccount001" "MyTaskApplication" "1.10-beta3" package001.zip

**Ativar** o pacote:

    azure batch application package activate "resgroup001" "batchaccount001" "MyTaskApplication" "1.10-beta3" zip

Defina a **versão padrão** para o aplicativo:

    azure batch application set "resgroup001" "batchaccount001" "MyTaskApplication" --default-version "1.10-beta3"

### <a name="deploy-an-application-package"></a>Implantar um pacote de aplicativos
Você pode especificar um ou mais pacotes de aplicativos para implantação durante a criação de um novo pool. Quando você especifica um pacote no momento da criação do pool, ele é implantado em cada nó como o pool de junções de nó. Pacotes também são implantados quando um nó é reinicializado ou quando sua imagem é refeita.

Especifique a opção `--app-package-ref` durante a criação de um pool para implantar um pacote de aplicativos nos nós do pool à medida que eles ingressarem no pool. A opção `--app-package-ref` aceita uma lista delimitada por ponto-e-vírgula de ids de aplicativo a ser implantada nos nós de computação.

    azure batch pool create --pool-id "pool001" --target-dedicated 1 --vm-size "small" --os-family "4" --app-package-ref "MyTaskApplication"

Quando você cria um pool usando as opções de linha de comando, não pode especificar atualmente *qual* versão do pacote de aplicativos será implantada nos nós de computação, por exemplo, "1.10-beta3". Portanto, primeiro você deve especificar uma versão padrão para o aplicativo com `azure batch application set [options] --default-version <version-id>` antes de criar o pool (veja a seção anterior). No entanto, especifique uma versão do pacote para o pool se usar um [arquivo JSON](#json-files) em vez das opções de linha de comando durante a criação do pool.

Veja mais informações sobre pacotes de aplicativo em [Implantação de aplicativos com pacotes de aplicativos do Lote do Azure](batch-application-packages.md).

> [!IMPORTANT]
> Você deve [vincular uma conta de Armazenamento do Azure](#linked-storage-account-autostorage) à sua conta do Lote para usar os pacotes de aplicativos.
> 
> 

### <a name="update-a-pools-application-packages"></a>Atualizar pacotes de aplicativos de um pool
Para atualizar os aplicativos atribuídos a um pool existente, emita o comando `azure batch pool set` com a opção `--app-package-ref`:

    azure batch pool set --pool-id "pool001" --app-package-ref "MyTaskApplication2"

Para implantar o novo pacote de aplicativos nos nós de computação que já estejam em um pool existente, você deverá reinicializar ou refazer a imagem dos nós:

    azure batch node reboot --pool-id "pool001" --node-id "tvm-3105992504_1-20160930t164509z"

> [!TIP]
> Você pode obter uma lista de nós de um pool, juntamente com suas ids de nó, com `azure batch node list`.
> 
> 

Tenha em mente que você já deve ter configurado o aplicativo com uma versão padrão antes da implantação (`azure batch application set [options] --default-version <version-id>`).

## <a name="troubleshooting-tips"></a>Dicas de solução de problemas
Esta seção tem como intenção fornecer recursos a serem usados ao solucionar problemas com a CLI do Azure. Ela não solucionará necessariamente todos os problemas, mas pode ajudar você a restringir a causa e indicar recursos de ajuda.

* Use `-h` para obter um **texto de ajuda** para qualquer comando da CLI
* Use `-v` e `-vv` para exibir a saída de comando **detalhada**; `-vv` é "extra" detalhado e exibe as solicitações e as respostas reais de REST. Essas opções são úteis para exibir a saída completa do erro.
* Você pode exibir a **saída do comando como JSON** com a opção `--json`. Por exemplo, o `azure batch pool show "pool001" --json` exibe propriedades do pool001 no formato JSON. Você pode copiar e modificar essa saída a fim de usá-la em um `--json-file` (consulte [Arquivos JSON](#json-files) anteriormente neste artigo).
* O [Fórum do Lote no MSDN][batch_forum] é um excelente recurso de ajuda, e é monitorado de perto por membros da equipe do Lote. Publique suas perguntas nesse local se você se deparar com problemas ou se quiser obter ajuda com uma operação específica.
* No momento, nem toda operação de recurso do Lote recebe suporte da CLI do Azure. Por exemplo, no momento, você não pode especificar uma *versão* de pacote de aplicativos para um pool, somente a ID do pacote. Nesses casos, talvez seja necessário fornecer um `--json-file` para seu comando em vez de usar as opções de linha de comando. Mantenha-se atualizado com a versão mais recente da CLI para aproveitar os aperfeiçoamentos futuros.

## <a name="next-steps"></a>Próximas etapas
* Consulte [Implantação de aplicativos com os pacotes de aplicativos do Lote do Azure](batch-application-packages.md) para descobrir como usar esse recurso para gerenciar e implantar os aplicativos executados em nós de computação do Lote.
* Veja [Consultar o serviço Lote com eficiência](batch-efficient-list-queries.md) para saber mais sobre a redução do número de itens e sobre o tipo de informação retornado por consultas do Lote.

[batch_forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
[github_readme]: https://github.com/Azure/azure-xplat-cli/blob/dev/README.md
[rest_api]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx



<!--HONumber=Jan17_HO4-->


