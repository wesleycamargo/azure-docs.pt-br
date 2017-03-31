---
title: "Autenticar com um registro de contêiner do Azure | Microsoft Docs"
description: "Como fazer logon em um registro de contêiner do Azure usando uma entidade de serviço do Azure Active Directory em uma conta de administrador"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: cristyg
tags: 
keywords: 
ms.assetid: 128a937a-766a-415b-b9fc-35a6c2f27417
ms.service: container-registry
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: stevelas
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 774c844375df89864274e3376ed07dd565c8ecff
ms.lasthandoff: 03/27/2017

---
# <a name="authenticate-with-a-private-docker-container-registry"></a>Autenticar com um Registro de contêiner privado do Docker
Para trabalhar com imagens de contêiner em um registro de contêiner do Azure, você faz logon usando o comando `docker login`. Você pode fazer logon usando uma **[entidade de serviço do Azure Active Directory](../active-directory/active-directory-application-objects.md)** ou uma **conta de administrador** específica do registro. Este artigo fornece mais detalhes sobre essas identidades.



## <a name="service-principal"></a>Entidade de serviço

Você pode [atribuir uma entidade de serviço](container-registry-get-started-azure-cli.md#assign-a-service-principal) ao seu registro e usá-la para autenticação básica do Docker. Usar uma entidade de serviço é recomendado para a maioria dos cenários. Forneça a ID do aplicativo e a senha da entidade de serviço para o comando `docker login`, conforme mostrado no seguinte exemplo:

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

Depois de conectado, o Docker armazena em cache as credenciais, portanto, você não precisa se lembrar da ID do aplicativo.

> [!TIP]
> Se quiser, você pode regenerar a senha de uma entidade de serviço executando o comando `az ad sp reset-credentials`.
>


As entidades de serviço permitem o [acesso baseado em função](../active-directory/role-based-access-control-configure.md) a um registro. As funções disponíveis são Leitor (acesso somente para efetuar pull), Colaborador (efetuar pull e enviar por push) e Proprietário (efetuar pull, enviar por push e atribuir funções a outros usuários). É possível atribuir várias entidades de serviço a um registro, o que permite definir acesso para diferentes usuários ou aplicativos. As entidades de serviço também permitem conectividade "sem periféricos" a um registro em cenários de desenvolvedor ou DevOps, como os seguintes:

  * Implantações de contêiner de um registro para sistemas de orquestração, incluindo DC/OS, Docker Swarm e Kubernetes. Também é possível efetuar pull de registros para serviços do Azure relacionados, como [Serviço de Contêiner](../container-service/index.md), [Serviço de Aplicativo](../app-service/index.md), [Lote](../batch/index.md) e [Service Fabric](../service-fabric/index.md).

  * Soluções de integração e implantação contínuas (como Visual Studio Team Services ou Jenkins) que criam imagens de contêiner e as envia por push para um registro.





## <a name="admin-account"></a>Conta de administrador
Com cada registro que você cria, uma conta de administrador é criada automaticamente. Por padrão, a conta está desabilitada, mas você pode habilitá-la e gerenciar as credenciais, por exemplo, por meio do [portal](container-registry-get-started-portal.md#manage-registry-settings) ou usando os [comandos da CLI 2.0 do Azure](container-registry-get-started-azure-cli.md#manage-admin-credentials). Se a conta estiver habilitada, você pode passar o nome de usuário e a senha para o comando `docker login` para autenticação básica no registro. Por exemplo:

```
docker login myregistry.azurecr.io -u myAdminName -p myPassword
```

> [!IMPORTANT]
> A conta de administrador destina-se um único usuário para acessar o registro, principalmente para fins de teste. Não é recomendável compartilhar as credenciais da conta de administrador com outros usuários. Todos os usuários aparecem como um único usuário no registro. Alterar ou desabilitar essa conta desabilita o acesso ao registro para todos os usuários que usam as credenciais.
>


### <a name="next-steps"></a>Próximas etapas
* [Enviar por push sua primeira imagem usando a CLI do Docker](container-registry-get-started-docker-cli.md).
* Para saber mais sobre a autenticação na visualização do Registro de Contêiner, confira a [postagem no blog](https://blogs.msdn.microsoft.com/stevelasker/2016/11/17/azure-container-registry-user-accounts/).

