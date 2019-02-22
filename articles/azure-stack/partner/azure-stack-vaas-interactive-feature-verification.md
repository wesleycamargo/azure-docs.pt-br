---
title: Teste na validação de pilha do Azure como um serviço de verificação de recurso interativo | Microsoft Docs
description: Saiba como criar testes de verificação do recurso interativo para o Azure Stack com validação como um serviço.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/07/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 01/07/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 20a48e67a24763af7bcce9e8831e2a1d1846d094
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2019
ms.locfileid: "56594297"
---
# <a name="interactive-feature-verification-testing"></a>Teste de verificação do recurso interativo  

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Você pode usar a estrutura de teste de verificação do recurso interativo para testes de solicitação para o seu sistema. Quando você solicita um teste, a Microsoft usa a estrutura para preparar os testes que exigem etapas interativas manuais. Microsoft pode usar a estrutura para encadear vários testes autônomo automatizado.

Este artigo descreve um cenário simples de manual. O teste verifica a substituição de um disco no Azure Stack. A estrutura de coleta de logs de diagnóstico em cada etapa. Você pode depurar os problemas conforme você encontrá-los. O framework também permite o compartilhamento de logs gerados por outras ferramentas ou processos e permite que você fornecer comentários sobre o cenário.

> [!Important]  
> Este artigo faz referência as etapas para executar a identificação de disco. Isso é simplesmente uma demonstração, pois todos os resultados coletados de fluxo de trabalho de aprovação do teste não podem ser usados para a nova verificação de solução.

## <a name="overview-of-interactive-testing"></a>Visão geral do teste interativo

Um teste para substituição do disco é um cenário comum. Neste exemplo, o teste tem sete etapas:

1. Criar um novo **aprovação do teste** fluxo de trabalho
1. Selecione o **teste de identificação de disco**
1. Conclua a etapa manual quando solicitado
1. Verifique o resultado do cenário
1. Enviar o resultado do teste para Microsoft

## <a name="create-a-new-test-pass"></a>Criar uma nova passagem de teste

Se você não tiver um teste existente passar disponível, siga as orientações para [agendamento de um teste](azure-stack-vaas-schedule-test-pass.md).

## <a name="schedule-the-test"></a>Agendar o teste

1. Selecione **teste de identificação de disco**.

    > [!Note]  
    > A versão do teste será incrementado conforme são feitas melhorias para o material de apoio para teste. A versão mais recente sempre deve ser usada, a menos que a Microsoft indica o contrário.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image4.png)

1. Forneça o nome de usuário de administrador de domínio e a senha, selecionando **editar**.

1. Selecione o agente de execução apropriado de teste/DVM para iniciar o teste em.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image5.png)

1. Selecione **enviar** para iniciar o teste.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image6.png)

1. Acesse a interface do usuário para o teste interativo do agente selecionado na etapa anterior.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image8.png)

1. Siga as **documentação** e **validação** links para examinar as instruções da Microsoft sobre como executar esse cenário.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image9.png)

1. Selecione **Avançar**.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image10.png)

1. Siga as instruções para executar o script nas pré-verificações.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image11.png)

1. Depois que o script nas pré-verificações foi concluída com êxito, execute o cenário manual (substituição do disco) de acordo o **documentação** e **validação** vincula do **informações**guia.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image12.png)

    > [!Important]  
    > Não feche a caixa de diálogo enquanto você estiver executando o cenário manual.

1. Quando você terminar de executar o cenário manual, siga as instruções para executar o script de verificação de postagem.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image13.png)

1. Após a conclusão bem-sucedida do cenário manual (substituição do disco), selecione **próxima**.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image14.png)

    > [!Important]  
    > Se você fechar a janela, o teste será parado antes que ele seja feito.

1. Fornece comentários para a experiência de teste. Essas perguntas ajudarão a Microsoft a avaliar a qualidade de lançamento e de taxa de sucesso do cenário.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image15.png)

1. Anexe os arquivos de log que você deseja enviar à Microsoft.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image16.png)

1. Aceite o EULA de envio de comentários.

1. Selecione **enviar** para enviar os resultados à Microsoft.

## <a name="next-steps"></a>Próximas etapas

- [Monitorar e gerenciar testes no portal VaaS](azure-stack-vaas-monitor-test.md)
