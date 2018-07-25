---
title: Perguntas comuns sobre Malha do Microsoft Azure Service Fabric | Microsoft Docs
description: Saiba mais sobre perguntas frequentes e respostas relacionadas à Malha do Microsoft Azure Service Fabric.
services: service-fabric-mesh
keywords: ''
author: chackdan
ms.author: chackdan
ms.date: 06/25/2018
ms.topic: troubleshooting
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 07c0347793f6541a3e047f3f357d0d1b05dc3bca
ms.sourcegitcommit: dc646da9fbefcc06c0e11c6a358724b42abb1438
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39136174"
---
# <a name="commonly-asked-service-fabric-mesh-questions"></a>Perguntas frequentes sobre Malha do Service Fabric
A Malha do Microsoft Azure Service Fabric é um serviço totalmente gerenciado que permite aos desenvolvedores implantar aplicativos de microsserviços sem gerenciar máquinas virtuais, armazenamento ou rede. Este artigo apresenta as respostas das perguntas frequentes.

## <a name="how-do-i-report-an-issue-or-ask-a-question"></a>Como fazer para relatar um problema ou fazer uma pergunta?

Faça perguntas, obtenha respostas de engenheiros da Microsoft e relate problemas no [Repositório GitHub de versão prévia da Malha do Service Fabric ](https://aka.ms/sfmeshissues).

## <a name="quota-and-cost"></a>Cota e custo

**Qual é o custo de participar da versão prévia?**

Não há custos para implantar aplicativos ou contêineres na versão prévia da Malha. No entanto, é recomendável excluir os recursos que você implanta e não deixá-los em execução, a menos que você esteja testando ativamente.

**Há um limite de cota do número de núcleos e RAM?**

Sim, as cotas de cada assinatura são:

- Número de aplicativos - 5 
- Número de núcleos por aplicativo – 12 
- Total de RAM por aplicativo - 48 GB 
- Número de pontos de extremidade de Entrada e Rede – 5  
- Número de volumes do Azure possíveis de anexar - 10 
- Número de réplicas de serviço – 3 
- O maior contêiner que pode ser implantado é limitado a 4 núcleos, 16 GB de RAM.
- É possível alocar núcleos parciais para os contêineres em incrementos de 0,5 núcleos até um máximo de 6 núcleos.

**Eu posso deixar o aplicativo em execução durante a noite?**

Sim, você pode, no entanto, é recomendável excluir os recursos que você implanta e não deixá-los em execução, a menos que esteja testando ativamente. Essa política poderá ser alterada no futuro e os recursos poderão ser excluídos se estiverem sendo utilizados incorretamente.

## <a name="supported-container-os-images"></a>Imagens de SO do contêiner com suporte
As imagens do SO do contêiner a seguir podem ser usadas ao implementar serviços.

- Windows - windowsservercore e nanoserver
    - Windows Server 2016
    - Windows Server versão 1709
- Linux
    - Sem limitações conhecidas

## <a name="features-gaps-and-known-issues"></a>Lacunas de recursos e problemas conhecidos

**Após implantar o aplicativo, o recurso de rede associado ao aplicativo não parece ter um endereço IP**

Há um problema conhecido hoje com o endereço IP chegando após um atraso. Verifique o status do recurso de rede em alguns minutos para ver o endereço IP associado.

**O aplicativo não está conseguindo acessar o recurso de volume/rede correto**

No modelo de aplicativo, é necessário usar a ID do recurso completa de redes e volumes para poder acessar o recurso associado. Aqui está a semelhança no exemplo de início rápido:

```json
"networkRefs": [
    {
    "name":  "[resourceId('Microsoft.ServiceFabric/networks', 'SbzVotingNetwork')]" 
    }
]
```

**Eu não vejo o modelo de aplicativo atual com suporte para criptografar meus segredos** 

Sim, a criptografia de segredos não tem suporte na versão prévia privada. 

**O DNS não funciona da mesma maneira no cluster de desenvolvimento do Service Fabric e na Malha**

Há um problema conhecido, em que talvez seja necessário referenciar serviços de maneira diferente no cluster de desenvolvimento local e na Malha do Azure. No cluster de desenvolvimento local, use {serviceName}.{applicationName}. Na Malha do Service Fabric do Azure, use {servicename}. A Malha do Azure não dá suporte à resolução de DNS nos aplicativos.

Para outros problemas conhecidos de DNS com a execução de um cluster de desenvolvimento do Service Fabric no Windows 10, consulte aqui: [Depurar contêineres do Windows](/azure/service-fabric/service-fabric-how-to-debug-windows-containers).

**Eu recebo este erro ao usar o módulo CLI _ImportError: nome da importação não pode ser 'sdk_no_wait'**

Se estiver usando uma versão da CLI mais antiga que 2.0.30, poderá receber este erro -

```
cannot import name 'sdk_no_wait'
Traceback (most recent call last):
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\knack\cli.py", line 193, in invoke cmd_result = self.invocation.execute(args)
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core\commands_init_.py", line 241, in execute self.commands_loader.load_arguments(command)
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core_init_.py", line 201, in load_arguments self.command_table[command].load_arguments() # this loads the arguments via reflection
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core\commands_init_.py", line 142, in load_arguments super(AzCliCommand, self).load_arguments()
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\knack\commands.py", line 76, in load_arguments cmd_args = self.arguments_loader()
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core_init_.py", line 332, in default_arguments_loader op = handler or self.get_op_handler(operation)
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core_init_.py", line 375, in get_op_handler op = import_module(mod_to_import)
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\importlib_init_.py", line 126, in import_module return _bootstrap._gcd_import(name[level:], package, level)
File "", line 978, in _gcd_import
File "", line 961, in _find_and_load
File "", line 950, in _find_and_load_unlocked
File "", line 655, in _load_unlocked
File "", line 678, in exec_module
File "", line 205, in _call_with_frames_removed
File "C:\Users\annayak.azure\cliextensions\azure-cli-sbz\azext_sbz\custom.py", line 18, in 
from azure.cli.core.util import get_file_json, shell_safe_json_parse, sdk_no_wait
ImportError: cannot import name 'sdk_no_wait'.
```

**Eu recebo um erro de nome de distribuição incompatível ao instalar o pacote de extensão da CLI**

Isso não significa que a extensão não foi instalada. Ainda é possível usar os comandos da CLI sem problemas.

**Ao escalar horizontalmente, vejo que todos os contêineres são afetados, incluindo aqueles em execução**

Este é um bug e deve ser corrigido na próxima atualização de tempo de execução.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a Malha do Service Fabric, leia a [visão geral](service-fabric-mesh-overview.md).
