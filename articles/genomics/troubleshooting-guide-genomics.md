---
title: 'Microsoft Genomics: Guia de solução de problemas | Microsoft Docs'
titleSuffix: Azure
description: Saiba mais sobre estratégias para solucionar problemas
keywords: solução de problemas, erro, depuração
services: microsoft-genomics
author: grhuynh
manager: jhubbard
editor: jasonwhowell
ms.author: grhuynh
ms.service: microsoft-genomics
ms.workload: genomics
ms.topic: article
ms.date: 07/18/2018
ms.openlocfilehash: 9bd1690003fd37b6c2edd0f0421cf8d0e74f8cb5
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2018
ms.locfileid: "39144169"
---
# <a name="troubleshooting-guide"></a>Guia de Solução de Problemas
Esta visão geral descreve estratégias para resolver problemas comuns ao usar o serviço Microsoft Genomics. Para perguntas frequentes gerais, consulte [Perguntas comuns](frequently-asked-questions-genomics.md). 


## <a name="how-do-i-check-my-job-status"></a>Como verificar o status do meu trabalho?
Você pode verificar o status do fluxo de trabalho chamando `msgen status` da linha de comando, conforme mostrado. 

```
msgen status -u URL -k KEY -w ID [-f CONFIG] 
```

Há três argumentos necessários:
* URL - o URI de base para a API
* CHAVE - a chave de acesso da sua conta do Genomics. 
* ID - a ID do fluxo de trabalho

Para localizar a URL e a chave, vá ao portal do Azure e abra a página da sua conta do Genomics. No cabeçalho **Gerenciamento**, escolha **Chaves de acesso**. Lá, você encontra a URL de API e suas chaves de acesso.

Como alternativa, você pode incluir o caminho para o arquivo de configuração em vez de inserir diretamente a URL e a chave. Observe que, se você incluir esses argumentos na linha de comando, bem como o arquivo de configuração, os argumentos de linha de comando terá precedência. 

Depois de chamar `msgen status`, uma mensagem amigável será exibida, que descreve se o fluxo de trabalho foi bem-sucedido ou que fornece um motivo para a falha do trabalho. 


## <a name="get-more-information-about-my-workflow-status"></a>Obter mais informações sobre o status do fluxo de trabalho

Para obter mais informações sobre por que um trabalho não pode ter êxito, você pode explorar os arquivos de log gerados durante o fluxo de trabalho. Em seu contêiner de saída, você deve ver uma pasta `[youroutputfilename].logs.zip`.  Extraindo essa pasta, você verá os seguintes itens:

* outputFileList.txt - uma lista dos arquivos de saída produzida durante o fluxo de trabalho
* standarderror.txt - este arquivo está em branco.
* standardoutput.txt - contém o log de nível superior do fluxo de trabalho. 
* Arquivos de Log GATK - todos os outros arquivos na pasta `logs`

A pasta `standardoutput.txt` é um bom lugar para começar a determinar por que o fluxo de trabalho não teve êxito, pois inclui mais informações de baixo nível do fluxo de trabalho. 

## <a name="common-issues-and-how-to-resolve-them"></a>Problemas comuns e como resolvê-los
Esta seção descreve brevemente os problemas comuns e como resolvê-los.

### <a name="fastq-files-are-unmatched"></a>Arquivos Fastq não correspondem
Arquivos Fastq devem diferem apenas pelo /1 ou/2 ao lado no identificador de exemplo. Se você possuir arquivos FASTQ que não correspondem acidentalmente, você poderá ver as seguintes mensagens de erro ao chamar `msgen status`.
* `Encountered an unmatched read`
* `Error reading a FASTQ file, make sure the input files are valid and paired correctly` 

Para resolver esse problema, examine se os arquivos fastq enviados ao fluxo de trabalho são, na verdade, um conjunto correspondente. 


### <a name="error-uploading-bam-file-output-blob-already-exists-and-the-overwrite-option-was-set-to-false"></a>Erro ao carregar arquivo .bam. Blob de saída já existe e a opção de substituição foi definida como False.
Se a seguinte mensagem de erro for exibida, `Error uploading .bam file. Output blob already exists and the overwrite option was set to False`, a pasta de saída já conterá um arquivo de saída com o mesmo nome.  Exclua o arquivo de saída existente ou ative a opção de substituição no arquivo de configuração. Em seguida, envie novamente o fluxo de trabalho.

### <a name="when-to-contact-microsoft-genomics-support"></a>Quando entrar em contato com o Suporte do Microsoft Genomics
Se você vir as mensagens de erro a seguir, ocorreu um erro interno. 

* `Error locating input files on worker machine`
* `Process management failure`

Tente enviar novamente o fluxo de trabalho. Se você continuar a ter falhas de trabalho, ou se você tiver outras dúvidas, contate o suporte do Microsoft Genomics a partir do portal do Azure. Informações adicionais sobre como enviar uma solicitação de suporte podem ser encontradas [aqui](file-support-ticket-genomics.md).

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu como solucionar problemas e resolver problemas comuns com o serviço Microsoft Genomics. Para obter mais informações e perguntas frequentes mais gerais, consulte [Perguntas comuns](frequently-asked-questions-genomics.md). 
