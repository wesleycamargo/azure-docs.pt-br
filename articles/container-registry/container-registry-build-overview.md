---
title: Automatizar o sistema operacional e a aplicação de patches de estrutura com o Build do Registro de Contêiner do Azure (Build do ACR)
description: Uma introdução ao Build do ACR, um conjunto de recursos no Registro de Contêiner do Azure que fornece aplicação de patches e build de imagem de contêiner automatizados e seguros na nuvem.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 08/01/2018
ms.author: marsma
ms.openlocfilehash: 63bbd9b5711330207c34ac4aa05aac3a71304653
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413572"
---
# <a name="automate-os-and-framework-patching-with-acr-build"></a>Automatizar o sistema operacional e a aplicação de patch de estrutura com o Build do ACR

Os contêineres fornecem novos níveis de virtualização, isolando as dependências de aplicativo e desenvolvedor dos requisitos de infraestrutura e operacionais. No entanto, o que sobra é a necessidade para tratar como a virtualização de aplicativos é corrigida.

**O ACR Build** é um conjunto de recursos do Azure Container Registry. Ele fornece criação de imagens de contêiner baseada em nuvem para Linux, Windows e ARM e pode automatizar [o patch de sistema operacional e de estrutura](#automate-os-and-framework-patching) para seus contêineres do Docker.

[!INCLUDE [container-registry-build-preview-note](../../includes/container-registry-build-preview-note.md)]

## <a name="what-is-acr-build"></a>O que é Build do ACR?

O Build do Registro de Contêiner do Azure é um serviço de build de imagem de contêiner nativo do Azure. O Build do ACR permite o desenvolvimento de loop interno na nuvem com builds de imagem de contêiner sob demanda e builds automatizados na configuração do código-fonte e na atualização da imagem de base.

Imagem de contêiner de gatilho é criada automaticamente quando o código é confirmado em um repositório Git ou quando a imagem de base do contêiner é atualizada. Com gatilhos de atualização de imagem de base, você pode automatizar seu sistema operacional e o fluxo de trabalho de aplicação de patch da estrutura de aplicativos, mantendo os ambientes protegidos enquanto se adere às entidades de segurança de contêineres imutáveis.

## <a name="quick-build-inner-loop-extended-to-the-cloud"></a>Quick Build: loop interno estendido para a nuvem

O início do gerenciamento do ciclo de vida ocorre antes de os desenvolvedores confirmarem as primeiras linhas de código. O recurso [Quick Build](container-registry-tutorial-quick-build.md) do Build do ACR oferece uma experiência de desenvolvimento de loop interno local integrado, descarregando builds no Azure. Com Quick Builds, você pode verificar as definições de build automatizadas antes de confirmar seu código.

Usando o familiar `docker build`formato, o [comando az acr build][az-acr-build] na CLI do Azure usa um **contexto** (o conjunto de arquivos para compilar), envia-o para o serviço ACR Build e, por padrão, envia a imagem incorporada ao seu registro após a conclusão.

A tabela a seguir mostra alguns exemplos de locais de contexto suportados para o ACR Build:

| Local do contexto | DESCRIÇÃO | Exemplo |
| ---------------- | ----------- | ------- |
| Sistema de arquivos local | Arquivos dentro de um diretório no sistema de arquivos local. | `/home/user/projects/myapp` |
| Branch mestre do GitHub | Arquivos dentro da ramificação principal (ou outro padrão) de um repositório GitHub.  | `https://github.com/gituser/myapp-repo.git` |
| Ramificação GitHub | Filial específica de um repositório GitHub.| `https://github.com/gituser/myapp-repo.git#mybranch` |
| Solicitação de pull do GitHub | Solicitação de pull em um repositório GitHub. | `https://github.com/gituser/myapp-repo.git#pull/23/head` |
| Subpasta do GitHub | Arquivos dentro de uma subpasta em um repositório do GitHub. Exemplo mostra a combinação de especificação de PR e subpasta. | `https://github.com/gituser/myapp-repo.git#pull/24/head:myfolder` |
| Tarball remoto | Arquivos em um arquivo compactado em um servidor remoto. | `http://remoteserver/myapp.tar.gz` |

O ACR Build também segue seus registros replicados geograficamente, permitindo que as equipes de desenvolvimento dispersas aproveitem o registro replicado mais próximo.

O Build do ACR foi projetado como um ciclo de vida de contêiner primitivo. Por exemplo, integre o Build do ACR à sua solução CI/CD. Executando [az login][az-login] com uma [entidade de serviço][az-login-service-principal], sua solução CI/CD pode enviar os comandos [az acr build] [ az-acr-build] para iniciar compilações da imagem.

Saiba como usar Quick Builds no primeiro tutorial do Bui do ACR, [Compilar imagens de contêiner na nuvem com o Build do Registro de Contêiner do Azure](container-registry-tutorial-quick-build.md).

## <a name="automatic-build-on-source-code-commit"></a>Build automático na confirmação de código-fonte

Use o Build do ACR para disparar automaticamente um build de imagem de contêiner quando o código for confirmado para um repositório Git. As tarefas de build, que podem ser configuradas com o comando [az acr build-task][az-acr-build-task] da CLI do Azure, permitem que você especifique um repositório Git e, opcionalmente, um branch e Dockerfile. Quando sua equipe confirmar o código para o repositório, um webhook criado pelo Build do ACR disparará um build de imagem de contêiner definido no repositório.

Saiba como disparar builds na confirmação de código-fonte no segundo tutorial do Build do ACR, [Automatizar builds de imagem de contêiner com o Build do Registro de Contêiner do Azure](container-registry-tutorial-build-task.md).

## <a name="automate-os-and-framework-patching"></a>Automatizar sistema operacional e aplicação de patch de estrutura

O poder do Build do ACR de realmente aprimorar seu pipeline de build de contêiner vem da sua capacidade de detectar uma atualização para uma imagem de base. Quando a imagem base atualizada é enviada para o registro, o Build do ACR pode criar automaticamente qualquer imagem de aplicativo com base nele.

As imagens de contêiner podem ser amplamente categorizadas em imagens de *base* e imagens de *aplicativo*. Suas imagens de base normalmente incluem o sistema operacional e estruturas de aplicativo com base nos quais seu aplicativo é criado, em conjunto com outras personalizações. Essas imagens básicas são elas próprias baseadas em imagens upstream públicas, por exemplo: [Alpine Linux][base-alpine], [Windows][base-windows], [.NET][base-dotnet] ou [Node.js][base-node]. Várias das suas imagens de aplicativo podem compartilhar uma imagem de base comum.

Quando um sistema operacional ou uma imagem de estrutura de aplicativo é atualizado pelo mantenedor upstream, por exemplo, com um patch de segurança crítico de sistema operacional, você deverá também atualizar suas imagens de base para incluir a correção crítica. Cada imagem de aplicativo deve também ser reconstruída para incluir essas correções upstream agora incluídas em sua imagem de base.

Como o Build do ACR detecta dinamicamente as dependências de imagem base quando ele cria uma imagem de contêiner, ele pode detectar quando a imagem de base de uma imagem de aplicativo é atualizada. Com uma [tarefa de build](container-registry-tutorial-base-image-update.md#create-build-task) pré-configurada, o Build do ACR **recria automaticamente cada imagem de aplicativo** para você. Com essa detecção e recriação automáticas, o Build do ACR poupa o tempo e o esforço normalmente necessários para controlar e atualizar manualmente cada imagem de aplicativo que faz referência à imagem base atualizada.

Saiba mais sobre o sistema operacional e a aplicação de patch de infraestrutura no terceiro tutorial do Build do ACR, [Automatizar builds de imagem na atualização da imagem de base com o Build do Registro de Contêiner do Azure](container-registry-tutorial-base-image-update.md).

> [!NOTE]
> Para a visualização inicial, o acionador de atualizações de imagem de base é compilado apenas quando as imagens de base e de aplicativo residem no mesmo registro de contêiner do Azure ou em repositórios do Docker Hub acessíveis publicamente.

## <a name="next-steps"></a>Próximas etapas

Quando você estiver pronto para automatizar o sistema operacional e a aplicação de patch de infraestrutura criando suas imagens de contêiner na nuvem, confira a série de tutoriais do Build do ACR de três partes.

> [!div class="nextstepaction"]
> [Criar imagens de contêiner na nuvem com o Build do Registro de Contêiner do Azure](container-registry-tutorial-quick-build.md)

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-build-task]: /cli/azure/acr#az-acr-build-task
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli#log-in-with-a-service-principal

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
