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
ms.openlocfilehash: 880f28a6f6e0029812e50dd97ff96d149c956b33
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55252244"
---
# <a name="interactive-feature-verification-testing"></a>Teste de verificação do recurso interativo  

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Você pode usar a estrutura de teste de verificação do recurso interativo para testes de solicitação para o seu sistema. Quando você solicita um teste, a Microsoft usa a estrutura para preparar os testes que exigem etapas interativas manuais. Microsoft pode usar a estrutura para encadear vários testes autônomo automatizado.

Este artigo descreve um cenário simples de manual. O teste verifica a substituição de um disco no Azure Stack. A estrutura de coleta de logs de diagnóstico em cada etapa. Você pode depurar os problemas conforme você encontrá-los. O framework também permite o compartilhamento de logs gerados por outras ferramentas ou processos e permite que você fornecer comentários sobre o cenário.

## <a name="overview-of-a-test-pass"></a>Visão geral de uma aprovação de teste

Um teste para substituição do disco é um cenário comum. Neste exemplo, o teste tem sete etapas:

1.  Criar um novo **aprovação do teste** fluxo de trabalho.
2.  Selecione o **teste de identificação de disco**.
3.  Inicie o teste.
4.  Escolha as ações no cenário de verificação interativa.
5.  Verifique o resultado do cenário.
6.  Envie o resultado do teste para Microsoft.
7.  Verifique o status no portal do VaaS.

## <a name="create-a-new-test-pass"></a>Criar uma nova passagem de teste

1.  Navegue até a [portal do Azure Stack validação](https://www.azurestackvalidation.com) e entre.

2.  Criar uma nova solução ou escolha um existente.

3.  Selecione **iniciar** sobre o **aprovação do teste** lado a lado.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image1.png)

4.  Insira um nome para o **aprovação do teste** fluxo de trabalho.

5.  Insira o ambiente e os parâmetros comuns de teste, seguindo as instruções de [parâmetros comuns de fluxo de trabalho para validação de pilha do Azure como um serviço](azure-stack-vaas-parameters.md) artigo.

6.  A cadeia de caracteres de conexão de diagnóstico deve seguir o formato especificado na [parâmetros de teste](azure-stack-vaas-parameters.md#test-parameters) seção o [parâmetros comuns de fluxo de trabalho](azure-stack-vaas-parameters.md) artigo.

7.  Insira os parâmetros necessários para o teste.

8.  Selecione o agente para executar a execução de teste interativa.

> [!Note]  
> Usuário administrador de domínio e a senha devem ser especificados para teste de verificação do recurso interativo de identificação de disco.

![Alt text](media/azure-stack-vaas-interactive-feature-verification/image2.png)

## <a name="select-the-test"></a>Selecione o teste

1.  Selecione **teste de identificação de disco\<versão >**.

    > [!Note]  
    > A versão do teste será incrementado conforme são feitas melhorias para o material de apoio para teste. A versão mais recente sempre deve ser usada, a menos que a Microsoft indica o contrário.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image4.png)

2.  Fornecer o usuário administrador de domínio e a senha, selecionando **editar**.

3.  Selecione o agente de execução apropriado de teste/DVM para iniciar o teste em.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image5.png)

4.  Selecione **enviar** para iniciar o teste.

![Alt text](media/azure-stack-vaas-interactive-feature-verification/image6.png)

## <a name="start-the-test"></a>Iniciar o teste

Mostram os prompts de teste de identificação de disco no computador que executa o agente VaaS. Normalmente, isso é o DVM ou o Jumpbox para a instância do Azure Stack.

![Alt text](media/azure-stack-vaas-interactive-feature-verification/image8.png)

## <a name="choose-the-actions"></a>Escolher as ações

1.  Siga as **documentação** e **validação** links para examinar as instruções da Microsoft sobre como executar esse cenário.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image9.png)

2.  Selecione **Avançar**.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image10.png)

3.  Siga as instruções para executar o script nas pré-verificações.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image11.png)

4.  Depois que o script nas pré-verificações for concluído com êxito, execute o cenário manual (substituição do disco) de acordo o **documentação** e **validação** vincula do **informações**guia.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image12.png)

5.  Não feche a caixa de diálogo enquanto você estiver executando o cenário manual.

6.  Quando você terminar de executar o cenário manual, siga as instruções para executar o script de verificação de postagem.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image13.png)

7.  Após a conclusão bem-sucedida do cenário manual (substituição do disco), selecione **próxima**.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image14.png)

> [!Important]  
> Se você fechar a janela, o teste será parado antes que ele seja feito.

## <a name="check-the-status"></a>Verifique o status

1.  Quando o teste for concluído, você será solicitado a fornecer comentários.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image15.png)

2.  Essas perguntas ajudarão a Microsoft a avaliar a qualidade de lançamento e de taxa de sucesso do cenário.

## <a name="send-the-test-result"></a>Enviar o resultado do teste

1.  Anexe os arquivos de log que você deseja enviar à Microsoft.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image16.png)

2.  Aceite o EULA de envio de comentários.

3.  Selecione **enviar** para enviar os resultados à Microsoft.

## <a name="next-steps"></a>Próximas etapas

- [Monitorar e gerenciar testes no portal VaaS](azure-stack-vaas-monitor-test.md)
