---
title: "Autenticar com um Registro de contêiner do Azure"
description: "Opções de autenticação para um Registro de Contêiner do Azure, incluindo o logon do registro e o logon direto de entidades de serviço do Azure Active Directory."
services: container-registry
author: stevelas
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 11/05/2017
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 278c343124e776ccaee71f472f0889e784e0e935
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2017
---
# <a name="authenticate-with-a-private-docker-container-registry"></a>Autenticar com um Registro de contêiner privado do Docker

Há várias maneiras de autenticar com um Registro de Contêiner do Azure, cada uma das quais é aplicável a um ou mais cenários de uso do registro.

Você pode fazer logon em um registro diretamente por meio de [logon individual](#individual-login-with-azure-ad) e seus aplicativos e orquestradores de contêiner podem realizar a autenticação autônoma ou "remota" usando uma [entidade de serviço](#service-principal) do Azure AD (Azure Active Directory).

O Registro de Contêiner do Azure não dá suporte a operações de Docker não autenticadas nem a acesso anônimo. Para imagens públicas, você pode usar o [Hub do Docker](https://docs.docker.com/docker-hub/).

## <a name="individual-login-with-azure-ad"></a>Logon individual com o Azure AD

Ao trabalhar com o registro diretamente, assim como ao extrair imagens para sua estação de trabalho de desenvolvimento e ao enviar imagens por push para ela, autentique-se usando o comando [az acr login](/cli/azure/acr?view=azure-cli-latest#az_acr_login) da [CLI do Azure](/cli/azure/install-azure-cli):

```azurecli
az acr login --name <acrName>
```

Ao fazer logon com `az acr login`, a CLI usa o token criado quando você executou `az login` para, sem interrupções, autenticar a sessão com o registro. Depois de você fazer logon dessa maneira, suas credenciais são armazenadas em cache e os comandos `docker` subsequentes não requerem um nome de usuário ou senha. Se o token expirar, você poderá atualizá-lo usando o comando `az acr login` novamente para se reautenticar. Usar `az acr login` com identidades do Azure fornece [acesso baseado em função](../active-directory/role-based-access-control-configure.md).

## <a name="service-principal"></a>Entidade de serviço

Você pode atribuir uma [entidade de serviço](../active-directory/develop/active-directory-application-objects.md) ao registro e seu aplicativo ou serviço pode usá-lo para a autenticação remota. As entidades de serviço permitem [acesso baseado em função](../active-directory/role-based-access-control-configure.md) a um registro ao qual você pode atribuir várias entidades de serviço. Várias entidades de serviço permitem que você defina acesso diferente para diferentes aplicativos.

As funções disponíveis são:

  * **Leitor**: pull
  * **Colaborador**: pull e push
  * **Proprietário**: pull, push e atribuir funções a outros usuários

As entidades de serviço permitem conectividade remota a um registro tanto em cenários de push quanto nos de pull, tais como os seguintes:

  * *Leitor*: implantações de contêiner de um registro para sistemas de orquestração, incluindo DC/SO, Docker Swarm e Kubernetes. Também é possível efetuar pull de registros de contêiner para serviços do Azure relacionados, como [AKS](../aks/index.yml), [Serviço de Aplicativo](../app-service/index.yml), [Lote](../batch/index.md) e [Service Fabric](/azure/service-fabric/), entre outros.

  * *Colaborador*: soluções de integração e implantação contínuas como VSTS (Visual Studio Team Services) ou Jenkins, que criam imagens de contêiner e as enviam por push para um registro.

> [!TIP]
> Você pode regenerar a senha de uma entidade de serviço executando o comando [az ad sp reset-credentials](/cli/azure/ad/sp?view=azure-cli-latest#az_ad_sp_reset_credentials).
>

Você também pode fazer logon diretamente com uma entidade de serviço. Forneça a ID do aplicativo e a senha da entidade de serviço para o comando `docker login`:

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

Depois de conectado, o Docker armazena em cache as credenciais, portanto, você não precisa se lembrar da ID do aplicativo.

Dependendo da versão do Docker que você tiver instalado, você verá um aviso de segurança recomendando o uso do parâmetro `--password-stdin`. Embora seu uso esteja fora do escopo deste artigo, é recomendável seguir essa prática recomendada. Para obter mais informações, consulte a referência do comando [docker login](https://docs.docker.com/engine/reference/commandline/login/).

## <a name="admin-account"></a>Conta de administrador

Cada registro de contêiner inclui uma conta de usuário administrador, que fica desabilitada por padrão. Você pode habilitar o usuário administrador e gerenciar as credenciais dele no [Portal do Azure](container-registry-get-started-portal.md#create-a-container-registry) ou usando a CLI do Azure.

> [!IMPORTANT]
> A conta do administrador destina-se para um único usuário acessar o registro, principalmente para fins de teste. Não é recomendável compartilhar as credenciais da conta do administrador com vários usuários. Todos os usuários que se autenticam com a conta do administrador aparecem como um único usuário com acesso de push e de pull ao registro. Alterar ou desabilitar essa conta desabilita o acesso ao registro para todos os usuários que usam as credenciais dela. Para cenários remotos, recomenda-se identidade individual para usuários e entidades de serviço.
>

A conta do administrador é fornecida com duas senhas, que podem ser regeneradas. As duas senhas permitem manter conexões com o registro usando uma senha enquanto a outra é regenerada. Se a conta do administrador estiver habilitada, você poderá passar o nome de usuário e a senha para o comando `docker login` para autenticação básica no registro. Por exemplo:

```
docker login myregistry.azurecr.io -u myAdminName -p myPassword1
```

Novamente, o Docker recomenda que você use o parâmetro `--password-stdin` em vez de fornecê-lo na linha de comando para maior segurança. Você também pode especificar apenas o nome de usuário sem `-p` e digitar a senha quando solicitado.

Para habilitar o usuário administrador para um registro existente, você pode usar o parâmetro `--admin-enabled` do comando [az acr update](/cli/azure/acr?view=azure-cli-latest#az_acr_update) na CLI do Azure:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

Você pode habilitar o usuário administrador no Portal do Azure navegando em seu registro, selecionando **Chaves de acesso** em **CONFIGURAÇÕES** e, em seguida, **Habilitar** em **Usuário administrador**.

![Habilitar a interface do usuário administrador no Portal do Azure][auth-portal-01]

## <a name="next-steps"></a>Próximas etapas

* [Enviar sua primeira imagem por push usando a CLI do Azure](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
