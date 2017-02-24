---
title: "Configurar a Autenticação com o Amazon Web Services | Microsoft Docs"
description: "Este artigo descreve como criar e validar uma credencial do AWS para runbooks na Automação do Azure que gerenciam recursos do AWS."
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: tysonn
keywords: "autenticação aws, configurar aws"
ms.assetid: b6dde4bb-26ac-4876-9aa9-e586bed30d6b
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/11/2016
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 843f0862f9e4d8dc1d40e110710e48ad66716168
ms.openlocfilehash: fe590e7fc551c175d2f41f5b98e1558a756df806


---
# <a name="authenticate-runbooks-with-amazon-web-services"></a>Autenticar runbooks com o Amazon Web Services
A automação de tarefas comuns com recursos do AWS (Amazon Web Services) pode ser realizada com runbooks de Automação no Azure.  Você pode automatizar muitas tarefas no AWS usando runbooks de Automação, assim como faz com recursos no Azure.  Bastam duas coisas:

* Uma assinatura do AWS e um conjunto de credenciais.  Especificamente, sua Chave Secreta e sua Chave de Acesso do AWS.  Para saber mais, leia o artigo [Usando credenciais do AWS](http://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html).
* Uma assinatura do Azure e uma conta de Automação.  Para saber mais sobre como configurar uma conta de Automação do Azure, leia o artigo [Configurar uma conta Executar como do Azure](automation-sec-configure-azure-runas-account.md).  

Para a autenticação com o AWS, você deve especificar um conjunto de credenciais do AWS para autenticar seus runbooks em execução da Automação do Azure. Se já tiver criado uma conta de Automação e quiser usá-la para a autenticação com o AWS, você poderá executar as etapas na seção a seguir.  Se quiser dedicar uma conta para runbooks voltados para recursos do AWS, primeiro crie uma nova [conta Executar como de Automação](automation-sec-configure-azure-runas-account.md) (ignore a opção para criar uma entidade de serviço) e siga as etapas abaixo.

## <a name="configure-automation-account"></a>Configurar conta de Automação
Para que a Automação do Azure se comunique com o AWS, primeiro você precisará recuperar suas credenciais do AWS e armazená-las como ativos na Automação do Azure.  Execute as etapas a seguir registradas no documento do AWS [Gerenciamento de Chaves de Acesso para sua conta do AWS](http://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html) para criar uma Chave de Acesso e copie a **ID da Chave de Acesso** e a **Chave de Acesso do Segredo** (opcionalmente, baixe o arquivo de chave para armazená-lo em um lugar seguro).

Após criar e copiar as chaves de segurança do AWS, você precisará criar um ativo de Credencial com uma conta de Automação do Azure para armazená-las com segurança e fazer referência a elas com seus runbooks.  Siga as etapas na seção **Criar um novo ativo de credencial** no artigo [Ativos de credenciais na Automação do Azure](automation-credentials.md) e insira as seguintes informações:

1. Na caixa **Nome**, digite **AWScred** ou um valor apropriado seguindo seus padrões de nomenclatura.  
2. Na caixa **Nome de usuário**, digite a **ID de acesso** e a **Chave de Acesso do Segredo** nas caixas **Senha** e **Confirmar senha**.   

## <a name="next-steps"></a>Próximas etapas
* Examine o artigo sobre solução [Automatização da implantação de uma VM no Amazon Web Services](automation-scenario-aws-deployment.md) para saber como criar runbooks para automatizar tarefas no AWS.




<!--HONumber=Nov16_HO3-->


