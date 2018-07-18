---
title: Usando perfis de versão de API com o Python na pilha do Azure | Microsoft Docs
description: Saiba mais sobre como usar perfis de versão de API com Python na pilha do Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: mabrigg
ms.reviewer: sijuman
<!-- dev: viananth -->
ms.openlocfilehash: a4fe62ba8c0732745326831b977e8975e1210436
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/01/2018
---
# <a name="use-api-version-profiles-with-python-in-azure-stack"></a>Use perfis de versão de API com Python na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

## <a name="python-samples-for-azure-stack"></a>Exemplos do Python para a pilha do Azure 

Você pode usar os exemplos de código a seguir para executar tarefas comuns de gerenciamento para máquinas virtuais em sua pilha do Azure.

Os exemplos de código mostram a você:

- Crie máquinas virtuais:
    - Criar uma máquina virtual Linux
    - Criar uma máquina virtual do Windows
- Atualize uma máquina virtual:
    - Expanda uma unidade
    - Marque uma máquina virtual
    - Anexar discos de dados
    - Desanexar discos de dados
- Opere uma máquina virtual:
    - Iniciar uma máquina virtual
    - Parar uma máquina virtual
    - Reiniciar uma máquina virtual
- Listar máquinas virtuais
- Excluir uma máquina virtual

Para examinar o código para executar essas operações, confira o **run_example()** função no script Python **Hybrid/unmanaged-disks/example.py** no repositório GitHub [ virtual máquinas-python-gerenciar](https://github.com/viananth/virtual-machines-python-manage/tree/8643ed4bec62aae6fdb81518f68d835452872f88).

Cada operação é claramente rotulada com um comentário e uma função de impressão.
Os exemplos não são necessariamente na ordem mostrada na lista acima.


## <a name="run-the-python-sample"></a>Executar a amostra do Python

1.  Se você ainda não o fez, [instalar Python](https://www.python.org/downloads/).

    Este exemplo (e o SDK) são compatível com o Python 2.7, 3.4, 3.5 e 3.6.

2.  A recomendação geral para o desenvolvimento do Python é usar um ambiente Virtual. 
    Para obter mais informações, consulte https://docs.python.org/3/tutorial/venv.html
    
    Instalar e inicializar o ambiente virtual com o módulo "venv" em Python 3 (você deve instalar [virtualenv](https://pypi.python.org/pypi/virtualenv) para Python 2.7):

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

5.  Criar um [entidade de serviço](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-create-service-principals) para trabalhar com a pilha do Azure. Verifique se a entidade de serviço tem [função Colaborador/proprietário](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal) em sua assinatura.

6.  Definir as seguintes variáveis e exportar essas variáveis de ambiente para o shell atual. 

`Note: provide an explanation of where these variables come from?`

    ````bash
    export AZURE_TENANT_ID={your tenant id}
    export AZURE_CLIENT_ID={your client id}
    export AZURE_CLIENT_SECRET={your client secret}
    export AZURE_SUBSCRIPTION_ID={your subscription id}
    export ARM_ENDPOINT={your AzureStack Resource Manager Endpoint}
    ```

7.  Observe que para executar este exemplo, Ubuntu 16.04-LTS e imagens de data center R2 2012 WindowsServer devem estar presentes no mercado pilha do Azure. Eles podem ser [baixados do Azure](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-download-azure-marketplace-item) ou [adicionada ao repositório de imagem de plataforma](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-add-vm-image).


8. Execute o exemplo.

    ```
    python unmanaged-disks\example.py
    ```

## <a name="notes"></a>Observações

Talvez seja tentado para tentar recuperar o disco do sistema operacional da VM usando `virtual_machine.storage_profile.os_disk`.
Em alguns casos, isso pode fazer desejado, mas lembre-se de que ele fornece uma `OSDisk` objeto.
Para atualizar o tamanho do disco do sistema operacional, como `example.py` , você não precisa um `OSDisk` objeto, mas um `Disk` objeto.
`example.py` Obtém o `Disk` objeto com o seguinte:

```python
os_disk_name = virtual_machine.storage_profile.os_disk.name
os_disk = compute_client.disks.get(GROUP_NAME, os_disk_name)
```

## <a name="next-steps"></a>Próximas etapas

- [Centro de desenvolvimento do Python do Azure](https://azure.microsoft.com/develop/python/)
- [Documentação de máquinas virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/)
- [Caminho de aprendizado para máquinas virtuais](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/)
- Se você não tiver uma assinatura Microsoft Azure, você pode obter uma conta de avaliação gratuita [aqui](http://go.microsoft.com/fwlink/?LinkId=330212).
