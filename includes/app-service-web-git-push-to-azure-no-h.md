---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 9f865897ee478f25a44fe876d44aec253e84eb62
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/22/2018
ms.locfileid: "36319702"
---
De volta na _janela do terminal local_, adicione um remoto do Azure ao repositório Git local. Substitua  _&lt;deploymentLocalGitUrl-from-create-step>_ pela URL do Git remoto que você salvou de [Criar um aplicativo Web](#create).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Envie por push para o Azure remoto para implantar seu aplicativo com o comando a seguir. Quando solicitado a fornecer credenciais pelo Gerenciador de Credenciais do Git, insira as credenciais criadas em [Configurar um usuário de implantação](#configure-a-deployment-user), não as credenciais usadas para iniciar sessão no portal do Azure.

```bash
git push azure master
```

Esse comando pode demorar um pouco para ser executado. Na execução, ele exibe informações semelhantes ao seguinte exemplo:
