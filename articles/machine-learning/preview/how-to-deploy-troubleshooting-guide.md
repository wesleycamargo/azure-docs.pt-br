---
title: "Guia de solução de problemas de implantação do Azure Machine Learning | Microsoft Docs"
description: "Guia de solução de problemas para implantação e criação de serviço"
services: machine-learning
author: raymondl
ms.author: raymondl
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 10/09/2017
ms.openlocfilehash: b43ed29bda4412fb57bcb772da00f6405c3f1c26
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/03/2017
---
# <a name="troubleshooting-service-deployment-and-environment-setup"></a>Solução de problemas de implantação de serviço e configuração do ambiente
As informações a seguir podem ajudar a determinar a causa de erros durante a configuração do ambiente de gerenciamento de modelos.

## <a name="model-management-environment"></a>Ambiente de gerenciamento de modelos
### <a name="owner-permission-required"></a>Permissão do proprietário necessária
Você deve ter permissão de proprietário na assinatura do Azure para registrar a Computação de Machine Learning.

Você também precisa de permissão de proprietário para configurar um cluster para a implantação de serviços Web.

### <a name="resource-availability"></a>Disponibilidade de recursos
Você precisa ter recursos suficientes disponíveis na sua assinatura para que possa provisionar os recursos do ambiente.

### <a name="subscription-caps"></a>Limites de assinatura
Sua assinatura pode ter um limite de cobrança que pode impedir que provisione os recursos de ambiente. Remova esse limite para habilitar o provisionamento.

### <a name="enable-debug-and-verbose-options"></a>Habilitar opções de depuração e detalhadas
Use os sinalizadores `--debug` e `--verbose` no comando de instalação para mostrar informações de depuração e rastreamento enquanto o ambiente está sendo provisionado.

```
az ml env setup -l <location> -n <name> -c --debug --verbose 
```

## <a name="service-deployment"></a>Implantação de serviço
As informações a seguir podem ajudar a determinar a causa de erros durante a implantação ou ao chamar o serviço Web.

### <a name="service-logs"></a>Logs de serviço
A opção `logs` da CLI de serviço fornece dados de log do Docker e Kubernetes.

```
az ml service logs realtime -i <web service id>
```

Para configurações de log adicionais, use a opção `--help` (ou `-h`).

```
az ml service logs realtime -h
```

### <a name="debug-and-verbose-options"></a>Opções de depuração e detalhadas
Use o sinalizador `--debug` para mostrar logs de depuração à medida que o serviço estiver sendo implantado.

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --debug
```

Use o sinalizador `--verbose` para ver detalhes adicionais à medida que o serviço estiver sendo implantado.

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --verbose
```

### <a name="enable-request-logging-in-app-insights"></a>Habilitar registro em log da solicitação no Application Insights
Defina o sinalizador `-l` como verdadeiro ao criar um serviço Web para habilitar o log de nível de solicitação. Os logs de solicitação são gravados na instância do App Insights para o seu ambiente no Azure. Pesquise por esta instância com o nome do ambiente usado ao usar o comando `az ml env setup`.

- Defina `-l` como verdadeiro ao criar o serviço.
- Abra o App Insights no portal do Azure. Use seu nome de ambiente para localizar a instância do App Insights.
- já no App Insights, clique em Pesquisar no menu superior para exibir os resultados.
- Ou vá para `Analytics` > `Exceptions` > `exceptions take | 10`.


### <a name="add-error-handling-in-scoring-script"></a>Adicionar o tratamento de erro no script de pontuação
No script do `scoring.py`, use a manipulação de exceção na função **executar** para retornar a mensagem de erro como parte de sua saída de serviço Web.

Exemplo de Python:
```
    try:
        <code to load model and score>
   except Exception as e:
        return(str(e))
```

## <a name="other-common-problems"></a>Outros problemas comuns
- Se o comando `env setup` falhar com `LocationNotAvailableForResourceType`, você provavelmente estará usando o local incorreto (região) para os recursos de aprendizado de máquina. Certifique-se de que seu local especificado com o parâmetro `-l` é `eastus2`, `westcentralus` ou `australiaeast`.
- Se o comando `env setup` falhar com `Resource quota limit exceeded`, certifique-se de ter núcleos suficientes disponíveis em sua assinatura e de que seus recursos não estejam sendo usados em outros processos.
- Se o comando `env setup` falhar com `Invalid environment name. Name must only contain lowercase alphanumeric characters`, certifique-se de que o nome do serviço não contém letras maiúsculas, símbolos ou sublinhado ( _ ) (como em *meu_ambiente*).
- Se o comando `service create` falhar com `Service Name: [service_name] is invalid. The name of a service must consist of lower case alphanumeric characters (etc.)`, certifique-se de que o nome do serviço tem entre 3 e 32 caracteres; comece e termine com caracteres alfanuméricos minúsculos e não contém letras maiúsculas, símbolos diferentes de hífen ( - ) e ponto ( . ) ou o sublinhado ( _ ) (como em *meu_serviçoweb*).
- Tente novamente se você receber um erro `502 Bad Gateway` ao chamar o serviço Web. Normalmente, isso significa que o contêiner não foi implantado para o cluster ainda.
- Se você receber o erro `CrashLoopBackOff` ao criar um serviço, verifique seus logs. Esse normalmente é o resultado de dependências ausentes na função **iniciar**.
