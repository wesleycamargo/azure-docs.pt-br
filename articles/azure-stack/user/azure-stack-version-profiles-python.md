---
title: Usando perfis de versão de API com o Python no Azure Stack | Microsoft Docs
description: Saiba mais sobre como usar perfis de versão de API com o Python no Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: sijuman
<!-- dev: viananth -->
ms.openlocfilehash: c4600eda74fbc0ae53f30dac00e0127a984a4fff
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2018
ms.locfileid: "49093408"
---
# <a name="use-api-version-profiles-with-python-in-azure-stack"></a>Use perfis de versão de API com o Python no Azure Stack

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

## <a name="python-and-api-version-profiles"></a>Perfis de versão da API e Python

O SDK do Python dá suporte a perfis de versão da API para direcionar diferentes plataformas de nuvem, como o Azure Stack e o Azure global. Você pode usar perfis de API na criação de soluções para uma nuvem híbrida. O SDK do Python dá suporte aos seguintes perfis de API:

1. **latest**  
    O perfil tem como alvo as versões de API mais recentes para todos os provedores de serviço na plataforma do Azure.
2.  **2017-03-09-perfil**  
    **2017-03-09-perfil**  
    O perfil tem como alvo as versões de API provedores de recursos com suporte do Azure Stack.

    Para obter mais informações sobre perfis de API e o Azure Stack, consulte [perfis de versão da API de gerenciar no Azure Stack](azure-stack-version-profiles.md).

## <a name="install-azure-python-sdk"></a>Instalar o SDK do Python do Azure

1.  Instale o Git [o site oficial do](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
2.  Para obter instruções instalar o SDK do Python, consulte [do Azure para desenvolvedores de Python](https://docs.microsoft.com/python/azure/python-sdk-azure-install?view=azure-python).
3.  Se não estiver disponível, crie uma assinatura e salve a ID da assinatura a ser usado posteriormente. Para obter instruções criar uma assinatura, consulte [criar assinaturas de ofertas no Azure Stack](../azure-stack-subscribe-plan-provision-vm.md). 
4.  Crie uma entidade de serviço e salve sua ID e o segredo. Para obter instruções criar uma entidade de serviço para o Azure Stack, consulte [fornecer acesso a aplicativos para o Azure Stack](../azure-stack-create-service-principals.md). 
5.  Certifique-se de que sua entidade de serviço tem a função de proprietário/Colaborador em sua assinatura. Para obter instruções sobre como atribuir a função à entidade de serviço, consulte [fornecer acesso a aplicativos para o Azure Stack](../azure-stack-create-service-principals.md).

## <a name="prerequisites"></a>Pré-requisitos

Para usar o SDK do Python do Azure com o Azure Stack, você deve fornecer os seguintes valores e, em seguida, definir valores com variáveis de ambiente. Consulte as instruções após a tabela para seu sistema operacional sobre como definir as variáveis de ambiente. 

| Valor | Variáveis de ambiente | DESCRIÇÃO |
|---------------------------|-----------------------|-------------------------------------------------------------------------------------------------------------------------|
| ID do locatário | AZURE_TENANT_ID | O valor do Azure Stack [ID do locatário](../azure-stack-identity-overview.md). |
| ID do cliente | AZURE_CLIENT_ID | O serviço de ID do aplicativo principal salvo quando a entidade de serviço foi criada na seção anterior deste documento. |
| ID da assinatura | AZURE_SUBSCRIPTION_ID | O [ID da assinatura](../azure-stack-plan-offer-quota-overview.md#subscriptions) é como você pode acessar ofertas no Azure Stack. |
| Segredo do cliente | AZURE_CLIENT_SECRET | O aplicativo de serviço principal o segredo salvo quando a entidade de serviço foi criada. |
| Ponto de extremidade do Gerenciador de recursos | ARM_ENDPOINT | Ver [o ponto de extremidade do Gerenciador de recursos do Azure Stack](azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint). |


## <a name="python-samples-for-azure-stack"></a>Exemplos de Python para o Azure Stack 

Você pode usar os exemplos de código a seguir para executar tarefas comuns de gerenciamento para máquinas virtuais no Azure Stack.

Os exemplos de código mostram a você:

- Crie máquinas virtuais:
    - Criar uma máquina virtual Linux
    - Criar uma máquina virtual do Windows
- Atualize uma máquina virtual:
    - Expandir uma unidade
    - Marque uma máquina virtual
    - Anexar discos de dados
    - Desanexar discos de dados
- Opere uma máquina virtual:
    - Iniciar uma máquina virtual
    - Parar uma máquina virtual
    - Reiniciar uma máquina virtual
- Listar máquinas virtuais
- Excluir uma máquina virtual

Para examinar o código para executar essas operações, confira a **run_example()** função no script do Python **Hybrid/unmanaged-disks/example.py** no repositório GitHub [ virtual-máquinas-python-gerenciar](https://github.com/viananth/virtual-machines-python-manage/tree/8643ed4bec62aae6fdb81518f68d835452872f88).

Cada operação é claramente rotulada com um comentário e uma função de impressão.
Os exemplos não são necessariamente na ordem mostrada na lista acima.


## <a name="run-the-python-sample"></a>Executar o exemplo de Python

1.  Se você ainda não tiver, [instalar o Python](https://www.python.org/downloads/).

    Este exemplo (e o SDK) são compatível com o Python 2.7, 3.4, 3.5 e 3.6.

2.  A recomendação geral para o desenvolvimento do Python é usar um ambiente Virtual. 
    Para obter mais informações, consulte https://docs.python.org/3/tutorial/venv.html
    
    Instalar e inicializar o ambiente virtual com o módulo de "venv" no Python 3 (você deve instalar [virtualenv](https://pypi.python.org/pypi/virtualenv) para Python 2.7):

    ````bash
    python -m venv mytestenv # Might be "python3" or "py -3.6" depending on your Python installation
    cd mytestenv
    source bin/activate      # Linux shell (Bash, ZSH, etc.) only
    ./scripts/activate       # PowerShell only
    ./scripts/activate.bat   # Windows CMD only
    ````

3.  Clone o repositório.

    ````bash
    git clone https://github.com/Azure-Samples/virtual-machines-python-manage.git
    ````

4.  Instale as dependências usando pip.

    ````bash
    cd virtual-machines-python-manage\Hybrid
    pip install -r requirements.txt
    ````

5.  Criar uma [entidade de serviço](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals) para trabalhar com o Azure Stack. Certifique-se a entidade de serviço tenha [função de Colaborador/proprietário](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal) em sua assinatura.

6.  Defina as seguintes variáveis e exportar essas variáveis de ambiente em seu shell atual. 

    ```bash
    export AZURE_TENANT_ID={your tenant id}
    export AZURE_CLIENT_ID={your client id}
    export AZURE_CLIENT_SECRET={your client secret}
    export AZURE_SUBSCRIPTION_ID={your subscription id}
    export ARM_ENDPOINT={your AzureStack Resource Manager Endpoint}
    ```

7.  Para executar este exemplo, Ubuntu 16.04-LTS e imagens do Windows Server 2012-R2-Datacenter devem estar presentes no marketplace do Azure Stack. Eles podem ser [baixado do Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-download-azure-marketplace-item) ou [adicionada ao repositório de imagens de plataforma](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-vm-image).

8. Execute o exemplo.

    ```
    python unmanaged-disks\example.py
    ```

## <a name="notes"></a>Observações

Você pode ficar tentado a tentar recuperar o disco do sistema operacional da VM usando `virtual_machine.storage_profile.os_disk`.
Em alguns casos, isso pode fazer o que você deseja, mas lembre-se de que ele proporciona um `OSDisk` objeto.
Para atualizar o tamanho do disco do sistema operacional, como `example.py` , você não precisa um `OSDisk` objeto, mas um `Disk` objeto.
`example.py` Obtém o `Disk` objeto com o seguinte:

```python
os_disk_name = virtual_machine.storage_profile.os_disk.name
os_disk = compute_client.disks.get(GROUP_NAME, os_disk_name)
```

## <a name="next-steps"></a>Próximas etapas

- [Centro de desenvolvimento do Python do Azure](https://azure.microsoft.com/develop/python/)
- [Documentação de máquinas virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/)
- [Roteiro de aprendizagem para máquinas virtuais](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/)
- Se você não tiver uma assinatura do Microsoft Azure, você pode obter uma conta de avaliação gratuita [aqui](http://go.microsoft.com/fwlink/?LinkId=330212).
