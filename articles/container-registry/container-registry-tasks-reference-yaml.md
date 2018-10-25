---
title: Referência das Tarefas do Registro de Contêiner do Azure – YAML
description: Referência para definir tarefas na YAML para tarefas do ACR, incluindo propriedades da tarefa, tipos de etapas, propriedades das etapas e variáveis internas.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 09/24/2018
ms.author: danlep
ms.openlocfilehash: a26b766c18ea2b14796af796abe7f36aa4fc1c66
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2018
ms.locfileid: "48854942"
---
# <a name="acr-tasks-reference-yaml"></a>Referência das Tarefas do ACR: YAML

A definição de tarefas com várias etapas em Tarefas do ACR fornece um primitivo de computação centrado em contêiner, com foco em compilar, testar e corrigir contêineres. Este artigo aborda os comandos, os parâmetros, as propriedades e a sintaxe para os arquivos YAML que definem as tarefas com várias etapas.

Este artigo contém uma referência para criar arquivos YAML de tarefas com várias etapas para Tarefas do ACR. Se você quiser uma introdução às Tarefas do ACR, confira a [Visão geral das Tarefas de ACR](container-registry-tasks-overview.md).

> [!IMPORTANT]
> O recurso de tarefas com várias etapas das Tarefas do ACR está atualmente na versão prévia. As versões prévias são disponibilizadas com a condição de que você concorde com os [termos de uso complementares][terms-of-use]. Alguns aspectos desse recurso podem alterar antes da GA (disponibilidade geral).

## <a name="acr-taskyaml-file-format"></a>Formato de arquivo acr-task.yaml

As Tarefas do ACR dão suporte à declaração de tarefas com várias etapas na sintaxe YAML padrão. As etapas de uma tarefa são definidas em um arquivo YAML que você pode executar manualmente ou acionar automaticamente com confirmação Git ou atualização de imagem base. Embora este artigo se refira a `acr-task.yaml` como o arquivo que contém as etapas, as Tarefas do ACR dão suporte a qualquer nome de arquivo válido com uma [extensão com suporte](#supported-task-filename-extensions).

Os primitivos `acr-task.yaml` de nível superior são **propriedades das tarefas**, **tipos de etapas** e **propriedades das etapas**:

* As [propriedades das tarefas](#task-properties) se aplicam a todas as etapas em toda a execução da tarefa. Há três propriedades de tarefas globais:
  * version
  * stepTimeout
  * totalTimeout
* Os [tipos de etapas das tarefas](#task-step-types) representam os tipos de ações que podem ser executadas em uma tarefa. Há três tipos de etapas:
  * compilar
  * efetuar push
  * cmd
* As [propriedades das etapas das tarefas](#task-step-properties) são parâmetros que se aplicam a uma etapa individual. Existem várias propriedades das etapas, incluindo:
  * startDelay
  * Tempo limite
  * when
  * ... e muito mais.

O formato base de um arquivo `acr-task.yaml`, incluindo algumas propriedades das etapas comuns, segue. Apesar de não ser uma representação completa de todas as propriedades das etapas disponíveis ou do uso do tipo de etapa, fornece uma rápida visão geral do formato de arquivo básico.

```yaml
version: # acr-task.yaml format version.
stepTimeout: # Seconds each step may take.
totalTimeout: # Total seconds allowed for all steps to complete.
steps: # A collection of image or container actions.
    build: # Equivalent to "docker build," but in a multi-tenant environment
    push: # Push a newly built or retagged image to a registry.
      when: # Step property that defines either parallel or dependent step execution.
    cmd: # Executes a container, supports specifying an [ENTRYPOINT] and parameters.
      startDelay: # Step property that specifies the number of seconds to wait before starting execution.
```

### <a name="supported-task-filename-extensions"></a>Extensões de nome de arquivo das tarefas com suporte

As Tarefas do ACR reservaram diversas extensões de nome de arquivo, incluindo `.yaml`, que processará como um arquivo de tarefas. Qualquer extensão que *não* constar na lista a seguir será considerada pelas Tarefas do ACR como um Dockerfile: .yaml, .yml, .toml, .json, .sh, .bash, .zsh, .ps1, .ps, .cmd, .bat, .ts, .js, .php, .py, .rb, .lua

YAML é o único formato de arquivo com suporte, no momento, pelas Tarefas do ACR. As outras extensões de nome de arquivo estão reservadas para um possível suporte futuro.

## <a name="run-the-sample-tasks"></a>Executar as tarefas de exemplo

Vários arquivos de tarefas de exemplo são referenciados nas próximas seções deste artigo. As tarefas de exemplo estão em um repositório público do GitHub, [Azure-Samples/acr-tasks][acr-tasks]. É possível executá-las com o comando da CLI do Azure [az acr run][az-acr-run]. Os comandos de exemplo são semelhantes a:

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

A formatação dos comandos de exemplo pressupõe que você configurou um Registro padrão na CLI do Azure; portanto, o parâmetro `--registry` é omitido. Para configurar um Registro padrão, use o comando [az configure][az-configure] com o parâmetro `--defaults`, que aceita um valor `acr=REGISTRY_NAME`.

Por exemplo, para configurar a CLI do Azure com um Registro padrão denominado "myregistry":

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>Propriedades das tarefas

Normalmente, as propriedades das tarefas são exibidas na parte superior de um arquivo `acr-task.yaml` e são propriedades globais aplicadas durante toda a execução da tarefa. Algumas dessas propriedades globais podem ser substituídas em uma etapa individual.

| Propriedade | Tipo | Opcional | DESCRIÇÃO | Substituição com suporte | Valor padrão |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | string | Não  | A versão do arquivo `acr-task.yaml` conforme analisado pelo serviço de Tarefas do ACR. Enquanto as Tarefas do ACR se esforçam para manter a compatibilidade com versões anteriores, esse valor permite que as Tarefas do ACR mantenham a compatibilidade dentro de uma versão definida. | Não  | Nenhum |
| `stepTimeout` | int (segundos) | SIM | O número máximo de segundos em que uma etapa pode ser executada. Essa propriedade pode ser substituída em uma etapa ao definir a propriedade [timeout](#timeout). | SIM | 600 (10 minutos) |
| `totalTimeout` | int (segundos) | SIM | O número máximo de segundos em que uma tarefa pode ser executada. Uma "execução" inclui a execução e a conclusão de todas as etapas na tarefa, sejam elas concluídas com êxito ou falha. Também está inclusa uma saída da tarefa de impressão, como dependências de imagem detectadas e status de execução da tarefa. | Não  | 3600 (1 hora) |

## <a name="task-step-types"></a>Tipos de etapas das tarefas

As Tarefas do ACR dão suporte a três tipos de etapas. Cada tipo de etapa dá suporte a várias propriedades, detalhadas na seção para cada tipo de etapa.

| Tipo de etapa | DESCRIÇÃO |
| --------- | ----------- |
| [`build`](#build) | Compila uma imagem de contêiner usando a sintaxe `docker build` familiar. |
| [`push`](#push) | Executa um `docker push` de imagens recentemente compiladas ou remarcadas em um registro de contêiner. Há suporte para o Registro de Contêiner do Azure, outros Registros privados e o Hub do Docker público.
| [`cmd`](#cmd) | Executa um contêiner como comando, com os parâmetros passados para o `[ENTRYPOINT]` do contêiner. O tipo de etapa `cmd` dá suporte a parâmetros como env, detach e outras opções de comandos `docker run` familiares, habilitando o teste funcional e de unidade com execução simultânea de contêiner. |

## <a name="build"></a>compilar

Compilar uma imagem de contêiner. O tipo de etapa `build` representa um meio seguro e com vários locatários de executar `docker build` na nuvem como primitivo de primeira classe.

### <a name="syntax-build"></a>Sintaxe: compilar

```yaml
version: 1.0-preview-1
steps:
    - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
      [property]: [value]
```

O tipo de etapa `build` dá suporte aos seguintes parâmetros:

| Parâmetro | DESCRIÇÃO | Opcional |
| --------- | ----------- | :-------: |
| `-t` &#124; `--image` | Define o `image:tag` totalmente qualificado da imagem compilada.<br /><br />Como as imagens podem ser utilizadas para validações de tarefas internas, como testes funcionais, nem todas as imagens exigem `push` para um Registro. No entanto, para criar uma instância de uma imagem dentro de uma execução de Tarefa, a imagem precisa de um nome para fazer referência.<br /><br />Ao contrário de `az acr build`, a execução de Tarefas do ACR não fornece um comportamento de push padrão. Com as Tarefas do ACR, o cenário padrão pressupõe a capacidade de compilar, validar e efetuar push de uma imagem. Confira [push](#push) para saber como efetuar push de imagens compiladas opcionalmente. | SIM |
| `-f` &#124; `--file` | Especifica o Dockerfile passado para `docker build`. Se não for especificado, o Dockerfile padrão na raiz do contexto será considerado. Para especificar um Dockerfile alternativo, passe o nome do arquivo relativo à raiz do contexto. | SIM |
| `context` | O diretório raiz passado para `docker build`. O diretório raiz de cada tarefa é definido como um [workingDirectory](#task-step-properties) compartilhado e inclui a raiz do diretório clonado Git associado. | Não  |

### <a name="properties-build"></a>Propriedades: compilar

O tipo de etapa `build` dá suporte às propriedades a seguir. É possível encontrar detalhes sobre essas propriedades na seção [Propriedades das etapas das tarefas](#task-step-properties) deste artigo.

| | | |
| -------- | ---- | -------- |
| `detach` | bool | Opcional |
| `entryPoint` | string | Opcional |
| `env` | [string, string, ...] | Opcional |
| `id` | string | Opcional |
| `ignoreErrors` | bool | Opcional |
| `keep` | bool | Opcional |
| `startDelay` | int (segundos) | Opcional |
| `timeout` | int (segundos) | Opcional |
| `when` | [string, string, ...] | Opcional |
| `workingDirectory` | string | Opcional |

### <a name="examples-build"></a>Exemplos: compilar

#### <a name="build-image---context-in-root"></a>Compilar imagem – contexto na raiz

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml --> [!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>Compilar imagem – contexto no subdiretório

```yaml
version: 1.0-preview-1
steps:
- build: -t {{.Run.Registry}}/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>efetuar push

Efetuar push de uma ou mais imagens compiladas ou remarcadas em um registro de contêiner. Dá suporte ao push de Registros privados como o Registro de Contêiner do Azure ou o Hub do Docker público.

### <a name="syntax-push"></a>Sintaxe: efetuar push

O tipo de etapa `push` dá suporte a uma coleção de imagens. A sintaxe de coleção YAML dá suporte a formatos embutidos e aninhados. O push de uma única imagem geralmente é representado usando a sintaxe embutida:

```yaml
version: 1.0-preview-1
steps:
  # Inline YAML collection syntax
  - push: ["{{.Run.Registry}}/hello-world:{{.Run.ID}}"]
```

Para maior legibilidade, use a sintaxe aninhada ao efetuar push de várias imagens:

```yaml
version: 1.0-preview-1
steps:
  # Nested YAML collection syntax
  - push:
    - {{.Run.Registry}}/hello-world:{{.Run.ID}}
    - {{.Run.Registry}}/hello-world:latest
```

### <a name="properties-push"></a>Propriedades: efetuar push

O tipo de etapa `push` dá suporte às propriedades a seguir. É possível encontrar detalhes sobre essas propriedades na seção [Propriedades das etapas das tarefas](#task-step-properties) deste artigo.

| | | |
| -------- | ---- | -------- |
| `env` | [string, string, ...] | Opcional |
| `id` | string | Opcional |
| `ignoreErrors` | bool | Opcional |
| `startDelay` | int (segundos) | Opcional |
| `timeout` | int (segundos) | Opcional |
| `when` | [string, string, ...] | Opcional |

### <a name="examples-push"></a>Exemplos: efetuar push

#### <a name="push-multiple-images"></a>Efetuar push de várias imagens

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml --> [!code-yml[task](~/acr-tasks/build-push-hello-world.yaml)]

#### <a name="build-push-and-run"></a>Compilar, efetuar push e executar

```azurecli
az acr run -f build-run-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml --> [!code-yml[task](~/acr-tasks/build-run-hello-world.yaml)]

## <a name="cmd"></a>cmd

O tipo de etapa `cmd` executa um contêiner.

### <a name="syntax-cmd"></a>Sintaxe: cmd

```yaml
version: 1.0-preview-1
steps:
    - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>Propriedades: cmd

O tipo de etapa `cmd` dá suporte às propriedades a seguir:

| | | |
| -------- | ---- | -------- |
| `detach` | bool | Opcional |
| `entryPoint` | string | Opcional |
| `env` | [string, string, ...] | Opcional |
| `id` | string | Opcional |
| `ignoreErrors` | bool | Opcional |
| `keep` | bool | Opcional |
| `startDelay` | int (segundos) | Opcional |
| `timeout` | int (segundos) | Opcional |
| `when` | [string, string, ...] | Opcional |
| `workingDirectory` | string | Opcional |

É possível encontrar detalhes sobre essas propriedades na seção [Propriedades das etapas das tarefas](#task-step-properties) deste artigo.

### <a name="examples-cmd"></a>Exemplos: cmd

#### <a name="run-hello-world-image"></a>Executar imagem olá, mundo

Este comando executa o arquivo da tarefa `hello-world.yaml`, que faz referência à imagem [olá, mundo](https://hub.docker.com/_/hello-world/) no Hub do Docker.

```azurecli
az acr run -f hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml --> [!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>Executar imagem Bash e ecoar "olá, mundo"

Este comando executa o arquivo da tarefa `bash-echo.yaml`, que faz referência à imagem [bash](https://hub.docker.com/_/bash/) no Hub do Docker.

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml --> [!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>Executar a tag da imagem Bash específica

Para executar uma versão de imagem específica, especifique a tag no `cmd`.

Este comando executa o arquivo da tarefa `bash-echo-3.yaml`, que faz referência à imagem [bash:3.0](https://hub.docker.com/_/bash/) no Hub do Docker.

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml --> [!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>Executar imagens personalizadas

O tipo de etapa `cmd` faz referência a imagens usando o formato `docker run` padrão. As imagens não precedidas por um registro são consideradas originárias de docker.io. O exemplo anterior também poderia ser representado como:

```yaml
version: 1.0-preview-1
steps:
    - cmd: docker.io/bash:3.0 echo hello world
```

Usando a convenção de referência de imagem `docker run` padrão, o `cmd` consegue executar imagens que residem em qualquer registro particular ou no Hub do Docker público. Se estiver fazendo referência a imagens no mesmo Registro em que uma Tarefa do ACR estiver em execução, você não precisará especificar credenciais do Registro.

* Executar uma imagem que reside em um Registro de Contêiner do Azure

    Substitua `[myregistry]` pelo nome do seu Registro:

    ```yaml
    version: 1.0-preview-1
    steps:
        - cmd: [myregistry].azurecr.io/bash:3.0 echo hello world
    ```

* Generalizar a referência de registro com uma variável de execução

    Em vez do hard-coding do nome do Registro em um arquivo `acr-task.yaml`, você pode torná-lo mais portátil usando [Executar variável](#run-variables). A variável `Run.Registry` se expande no tempo de execução até o nome do Registro no qual a tarefa está em execução.

    A fim de generalizar a tarefa anterior para que ela funcione em qualquer Registro de Contêiner do Azure, faça referência à variável [Run.Registry](#runregistry) no nome da imagem:

    ```yaml
    version: 1.0-preview-1
    steps:
        - cmd: {{.Run.Registry}}/bash:3.0 echo hello world
    ```

## <a name="task-step-properties"></a>Propriedades das etapas das tarefas

Cada tipo de etapa dá suporte a várias propriedades apropriadas para seu tipo. A tabela a seguir define todas as propriedades das etapas disponíveis. Nem todos os tipos de etapas dão suporte a todas as propriedades. Para ver quais dessas propriedades estão disponíveis para cada tipo de etapa, confira as seções de referência de tipo de etapa [cmd](#cmd), [compilar](#build) e [efetuar push](#push).

| Propriedade | Tipo | Opcional | DESCRIÇÃO |
| -------- | ---- | -------- | ----------- |
| `detach` | bool | SIM | Se o contêiner deve ser desanexado quando está em execução. |
| `entryPoint` | string | SIM | Substitui o `[ENTRYPOINT]` do contêiner de uma etapa. |
| `env` | [string, string, ...] | SIM | Matriz de cadeias de caracteres no formato `key=value` que definem as variáveis de ambiente para a etapa. |
| [`id`](#example-id) | string | SIM | Identifica a etapa dentro da tarefa com exclusividade. Outras etapas na tarefa podem fazer referência ao `id` da etapa, como para verificação de dependência com `when`.<br /><br />O `id` também é o nome do contêiner em execução. Processos em execução em outros contêineres na tarefa podem consultar o `id` como seu nome de host DNS ou para acessá-lo com logs de docker [id], por exemplo. |
| `ignoreErrors` | bool | SIM | Quando definida como `true`, a etapa está marcada como concluída, independentemente de ter ocorrido um erro durante sua execução. Padrão: `false`. |
| `keep` | bool | SIM | Se o contêiner da etapa deve ser mantido após a execução. |
| `startDelay` | int (segundos) | SIM | Número de segundos para atrasar a execução de uma etapa. |
| `timeout` | int (segundos) | SIM | Número máximo de segundos em que uma etapa poderá ser executada antes de terminar. |
| [`when`](#example-when) | [string, string, ...] | SIM | Configura a dependência de uma etapa em relação a uma ou mais etapas diferentes dentro da tarefa. |
| `workingDirectory` | string | SIM | Define o diretório de trabalho para uma etapa. Por padrão, as Tarefas do ACR criam um diretório raiz como o diretório de trabalho. No entanto, se o build tiver várias etapas, as etapas anteriores poderão compartilhar artefatos com as etapas posteriores se o mesmo diretório de trabalho for especificado. |

### <a name="examples-task-step-properties"></a>Exemplos: propriedades das etapas das tarefas

#### <a name="example-id"></a>Exemplo: id

Compile duas imagens, criando uma instância de uma imagem de teste funcional. Cada etapa é identificada por um `id` exclusivo, ao qual as outras etapas da tarefa fazem referência na propriedade `when`.

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml --> [!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>Exemplo: quando

A propriedade `when` especifica a dependência de uma etapa em relação a uma ou mais etapas dentro da tarefa. Ela dá suporte a dois valores de parâmetro:

* `when: ["-"]` – Indica que não há dependência de outras etapas. Uma etapa que especifica `when: ["-"]` iniciará a execução imediatamente e habilita a execução em etapas simultânea.
* `when: ["id1", "id2"]` – Indica que a etapa é dependente das etapas com `id` "id1" e `id` "id2". Essa etapa não será executada até a conclusão das etapas "id1" e "id2".

Se `when` não estiver especificado em uma etapa, essa etapa dependerá da conclusão da etapa anterior no arquivo `acr-task.yaml`.

Execução em etapas sequenciais sem `when`:

```azurecli
az acr run -f when-sequential-default.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-default.yaml --> [!code-yml[task](~/acr-tasks/when-sequential-default.yaml)]

Execução em etapas sequenciais com `when`:

```azurecli
az acr run -f when-sequential-id.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-id.yaml --> [!code-yml[task](~/acr-tasks/when-sequential-id.yaml)]

Compilação de imagens paralelas:

```azurecli
az acr run -f when-parallel.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml --> [!code-yml[task](~/acr-tasks/when-parallel.yaml)]

Compilação de imagens paralelas e teste dependente:

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml --> [!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

## <a name="run-variables"></a>Executar variáveis

As Tarefas do ACR incluem um conjunto padrão de variáveis que estão disponíveis para as etapas das tarefas durante a execução. Essas variáveis podem ser acessadas usando o formato `{{.Run.VariableName}}`, em que `VariableName` é um dos seguintes:

* `Run.ID`
* `Run.Registry`
* `Run.Date`

### <a name="run46id"></a>Run&#46;ID

Cada execução, por meio de `az acr run`, ou execução baseada em gatilhos de tarefas criadas por meio de `az acr task create`, tem uma ID exclusiva. A ID representa a execução que está sendo executada no momento.

Normalmente usado para uma marcação exclusiva de uma imagem:

```yaml
version: 1.0-preview-1
steps:
    - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
```

### <a name="runregistry"></a>Run.Registry

O nome do servidor totalmente qualificado do Registro. Normalmente usado para fazer uma referência genérica ao registro em que a tarefa está sendo executada.

```yaml
version: 1.0-preview-1
steps:
    - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
```

### <a name="rundate"></a>Run.Date

O horário UTC atual em que a execução começou.

## <a name="next-steps"></a>Próximas etapas

Para uma visão geral das tarefas com várias etapas, confira [Executar tarefas com várias etapas de compilar, testar e corrigir nas Tarefas do ACR](container-registry-tasks-multi-step.md).

Para builds de uma única etapa, confira a [Visão geral das Tarefas do ACR](container-registry-tasks-overview.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[acr-tasks]: https://github.com/Azure-Samples/acr-tasks
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-run]: /cli/azure/acr/run#az-acr-run
[az-configure]: /cli/azure/reference-index#az-configure