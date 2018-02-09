---
title: "Meu primeiro runbook Python na Automação do Azure | Microsoft Docs"
description: "Este tutorial guia o usuário pela criação, pelos testes e pela publicação de um runbook Python simples."
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: tysonn
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/25/2017
ms.author: gwallace
ms.openlocfilehash: 1f005ae28309879f36a28df499685937c37a3be0
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2018
---
# <a name="my-first-python-runbook"></a>Meu primeiro runbook Python

> [!div class="op_single_selector"]
> * [Gráfico](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Fluxo de Trabalho do PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 
> 

Este tutorial orienta você durante a criação de um [Runbook Python](automation-runbook-types.md#python-runbooks) na Automação do Azure. Você começa com um runbook simples você testa e publica. Em seguida, você modifica o runbook para gerenciar os recursos do Azure, neste caso, iniciando uma máquina virtual do Azure. Depois, você torna o runbook mais robusto adicionando parâmetros de runbook.

## <a name="prerequisites"></a>pré-requisitos

Para concluir este tutorial, você precisará do seguinte:

* Assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automação](automation-offering-get-started.md) para manter o runbook e se autenticar nos recursos do Azure. Esta conta deve ter permissão para iniciar e parar a máquina virtual.
* Uma máquina virtual do Azure. Você para e inicia essa máquina, portanto, ele não deve ser uma VM de produção.

## <a name="create-a-new-runbook"></a>Criar um novo runbook

Você começa criando um runbook simples que exibe o texto *Olá, Mundo*.

1. No portal do Azure, abra sua conta da Automação.

    A página da conta de Automação fornece uma exibição rápida dos recursos nessa conta. Você já deve ter alguns ativos. A maioria desses ativos é de módulos incluídos automaticamente em uma nova conta de Automação. Você também deve ter o ativo de credencial que é mencionado nos [pré-requisitos](#prerequisites).<br>

1. Selecione **Runbooks** em **GERENCIAMENTO DE PROCESSO** para abrir a lista de runbooks.
2. Selecione **+ Adicionar um runbook** para criar um novo runbook.
3. Atribua o nome *MyFirstRunbook-Python* ao runbook.
4. Neste caso, você criará um [runbook Python](automation-runbook-types.md#python-runbooks), portanto, escolha **Python 2** como o **Tipo de runbook**.
5. Clique em **criar** para criar o runbook e abrir o editor textual.

## <a name="add-code-to-the-runbook"></a>Adicionar código ao runbook

Agora, você adiciona um comando simples para imprimir o texto "Olá, Mundo":

```python
print("Hello World!")
```

Clique em **Salvar** para salvar o runbook.

## <a name="test-the-runbook"></a>Testar o runbook

Antes de publicar o runbook para disponibilizá-lo na produção, teste-o para verificar se ele funciona corretamente. Quando você testa um runbook, executa sua versão de **Rascunho** e vê sua saída interativamente.

1. Clique em **Painel de teste** para abrir o Painel de teste.
2. Clique em **Iniciar** para iniciar o teste. Essa deve ser a única opção habilitada.
3. Um [trabalho de runbook](automation-runbook-execution.md) é criado e seu status é exibido.
   O status do trabalho inicia como *Na fila* , indicando que ele está aguardando um trabalho de runbook na nuvem ficar disponível. E ele muda para *Iniciando* quando um trabalhador reivindica o trabalho e para *Executando* quando o runbook realmente começa a ser executado.
4. Quando o trabalho do runbook é concluído, sua saída é exibida. Neste caso, você deve ver *Olá, Mundo*.
5. Feche o Painel de teste para retornar à tela.

## <a name="publish-and-start-the-runbook"></a>Publicar e iniciar o runbook

O runbook criado ainda está em modo de rascunho. Você precisa publicá-lo antes que possa executá-lo na produção.
Publicar um runbook substitui a versão publicada existente pela versão de Rascunho.
Neste caso, ainda não há uma versão publicada porque você acabou de criar o runbook.

1. Clique em **Publicar** para publicar o runbook e em **Sim** quando solicitado.
2. Se você rolar para a esquerda para exibir o runbook no painel **Runbooks**, ele mostrará o **Status de Criação** **Publicado**.
1. Role para a direita para exibir o painel do **MyFirstRunbook-Python**.
   As opções na parte superior nos permitem iniciar o runbook, exibi-lo, agendá-lo para iniciar em algum momento no futuro ou criar um [webhook](automation-webhooks.md) para que ele possa ser iniciado por meio de uma chamada de HTTP.
2. Você quer iniciar o runbook, sendo assim, clique em **Iniciar** e em **OK** quando a folha Iniciar Runbook for aberta.
3. Um painel de trabalho é aberto para o trabalho de runbook criado. Você pode fechar esse painel, mas neste caso, deixe-o averto para acompanhar o progresso do trabalho.
4. O status do trabalho é mostrado em **Resumo do trabalho** e corresponde aos status que você viu quando testou o runbook.
5. Assim que o status do runbook mostrar *Concluído*, clique em **Saída**. O painel Saída é aberto e você pode ver seu *Olá, Mundo*.
6. Feche o painel Saída.
7. Clique em **Todos os Logs** para abrir o painel Fluxos do trabalho do runbook. Você só deve ver *Olá, Mundo* no fluxo de saída, mas isso pode mostrar outros fluxos de um trabalho do runbook como Detalhado e Erro se o runbook gravar neles.
8. Feche os painéis Fluxos e Trabalho para retornar ao painel MyFirstRunbook-Python.
9. Clique em **Trabalhos** para abrir o painel de trabalhos para este runbook. Ele lista todos os trabalhos criados por esse runbook. Você deve ver apenas um trabalho listado, já que executou o trabalho apenas uma vez.
10. Você pode clicar nesse trabalho para abrir o mesmo painel do Trabalho exibido ao iniciar o runbook. Isso permite que você volte no tempo e veja os detalhes de qualquer trabalho que foi criado para um determinado runbook.

## <a name="add-authentication-to-manage-azure-resources"></a>Adicionar a autenticação para gerenciar os recursos do Azure

Você testou e publicou seu runbook, mas, até o momento, ele não faz nada útil. Você gostaria que ele gerencie recursos do Azure.
Para gerenciar recursos do Azure, o script precisa autenticar usando as credenciais da sua [Conta de automação](automation-offering-get-started.md).

> [!NOTE]
> A conta de Automação deve ter sido criada com o recurso da entidade de serviço para ter um certificado runas.
> Se a conta de automação não foi criada com a entidade de serviço, é possível autenticar usando o método descrito em [Autenticar com as Bibliotecas de Gerenciamento do Azure para Python](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate).

1. Abra o editor de texto clicando em **Editar** no painel MyFirstRunbook-Python.
1. Adicione o código a seguir para autenticar no Azure:
   ```python
   import os
   from azure.mgmt.compute import ComputeManagementClient
   import azure.mgmt.resource
   import automationassets

   def get_automation_runas_credential(runas_connection):
       from OpenSSL import crypto
       import binascii
       from msrestazure import azure_active_directory
       import adal

       # Get the Azure Automation RunAs service principal certificate
       cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
       pks12_cert = crypto.load_pkcs12(cert)
       pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM,pks12_cert.get_privatekey())

       # Get run as connection information for the Azure Automation service principal
       application_id = runas_connection["ApplicationId"]
       thumbprint = runas_connection["CertificateThumbprint"]
       tenant_id = runas_connection["TenantId"]

       # Authenticate with service principal certificate
       resource ="https://management.core.windows.net/"
       authority_url = ("https://login.microsoftonline.com/"+tenant_id)
       context = adal.AuthenticationContext(authority_url)
       return azure_active_directory.AdalAuthentication(
       lambda: context.acquire_token_with_client_certificate(
               resource,
               application_id,
               pem_pkey,
               thumbprint)
       )

   # Authenticate to Azure using the Azure Automation RunAs service principal
   runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
   azure_credential = get_automation_runas_credential(runas_connection)
   ```

## <a name="add-code-to-create-python-compute-client-and-start-the-vm"></a>Adicionar código para criar o cliente da Computação em Python e iniciar a VM

Para trabalhar com VMs do Azure, crie uma instância do [Cliente da Computação do Azure para Python](https://docs.microsoft.com/python/api/azure.mgmt.compute.computemanagementclient?view=azure-python).

Use o Cliente de computação para iniciar a VM. Adicione o seguinte código ao runbook:

```python
# Initialize the compute management client with the RunAs credential and specify the subscription to work against.
compute_client = ComputeManagementClient(
azure_credential,
  str(runas_connection["SubscriptionId"])
)


print('\nStart VM')
async_vm_start = compute_client.virtual_machines.start("MyResourceGroup", "TestVM")
async_vm_start.wait()
```

Em que _MyResourceGroup_ é o nome do grupo de recursos que contém a VM e _TestVM_ é o nome da VM que você deseja iniciar. 

Teste e execute o runbook novamente para ver se ele inicia a VM.

## <a name="use-input-parameters"></a>Usar parâmetros de entrada

Atualmente, o runbook usa valores embutidos em código para os nomes do Grupo de Recursos e da VM.
Agora, vamos adicionar o código que obtém esses valores dos parâmetros de entrada.

Você usa a variável `sys.argv` para obter os valores de parâmetro.
Adicione o seguinte código ao runbook imediatamente após as outras instruções `import`:

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

Isso importa o módulo `sys` e cria duas variáveis para armazenar os nomes do Grupo de Recursos e da VM.
Observe que o elemento da lista de argumentos, `sys.argv[0]`, é o nome do script e não é fornecido pelo usuário.

Agora, é possível modificar as duas últimas linhas do runbook para usar os valores de parâmetro de entrada em vez de usar valores embutidos em código:

```python
async_vm_start = compute_client.virtual_machines.start(resource_group_name, vm_name)
async_vm_start.wait()
```

Ao iniciar um runbook Python (na página **Teste** ou como um runbook publicado), é possível inserir os valores dos parâmetros na página **Iniciar Runbook** em **Parâmetros**.

Depois de começar a inserir um valor na primeira caixa, uma segunda aparecerá e assim por diante, para que você possa inserir quantos valores de parâmetro forem necessários.

Os valores estão disponíveis para o script como a matriz `sys.argv` no código que você acabou de adicionar.

Insira o nome do seu grupo de recursos como o valor para o primeiro parâmetro e o nome da VM para iniciar como o valor do segundo parâmetro.

![Inserir valores de parâmetro](media/automation-first-runbook-textual-python/runbook-python-params.png)

Clique em **OK** para iniciar o runbook. Ele é executado e inicia a VM especificada.

## <a name="next-steps"></a>Próximas etapas

* Para começar a usar os runbooks do PowerShell, veja [Meu primeiro runbook do PowerShell](automation-first-runbook-textual-powershell.md)
* Para começar a usar os runbooks Gráficos, consulte [Meu primeiro runbook gráfico](automation-first-runbook-graphical.md)
* Para começar a usar runbooks de fluxo de trabalho do PowerShell, veja [Meu primeiro runbook de Fluxo de Trabalho do PowerShell](automation-first-runbook-textual.md)
* Para saber mais sobre os tipos de runbook, suas vantagens e limitações, consulte [Tipos de runbook de Automação do Azure](automation-runbook-types.md)
* Para obter informações sobre como desenvolver para o Azure em Python, consulte [Azure para desenvolvedores de Python](https://docs.microsoft.com/python/azure/?view=azure-python).
* Para exibir runbooks de amostra do Python 2, consulte o [GitHub de Automação do Azure](https://docs.microsoft.com/python/azure/?view=azure-python).
