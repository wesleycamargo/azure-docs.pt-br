---
title: 'Microsoft Genomics: Perguntas frequentes - Perguntas frequentes | Microsoft Docs'
titleSuffix: Azure
description: Respostas a perguntas frequentes que os clientes fazem sobre o Genomics Microsoft.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: article
ms.date: 12/07/2017
ms.openlocfilehash: 56256a6c10ecb0d06dfd6194668b9c32c5540c0e
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51683893"
---
# <a name="microsoft-genomics-common-questions"></a>Microsoft Genomics: Perguntas frequentes

Este artigo lista as principais dúvidas que você pode ter relacionadas ao Microsoft Genomics. Para obter mais informações sobre o serviço do Microsoft Genomics, consulte [O que é Microsoft Genomics?](overview-what-is-genomics.md). Para obter mais informações sobre Solução de problemas, consulte nosso [Guia de solução de problemas](troubleshooting-guide-genomics.md). 

## <a name="what-is-the-microsoft-genomics-service-gatk-4-promotion"></a>O que é a promoção GATK 4 do serviço Microsoft Genomics?
Até o final do ano civil, 2018, o serviço Microsoft Genomics oferece gratuitamente 20 WGS com o GATK4. Para participar deste registro de oferta [aqui](https://aka.ms/msgatk4). 

### <a name="what-are-the-common-issues-i-might-encounter-while-running-the-microsoft-genomics-service-gatk4-promotion"></a>Quais são os problemas comuns que podem surgir durante a execução da promoção Microsoft Genomics serviço GATK4
Aqui está a lista de erros comuns que você pode encontrar e sua resolução recomendada:

| **Mensagem**                                                                                                                                                                                    | **Causa**                                                                                                    | **Resolução**                                                                                                                                                                                                       |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `gatk4-promo` não está habilitado para sua conta. Para mais informações, consulte https://docs.microsoft.com/azure/genomics/frequently-asked-questions-genomics                               | Você está tentando executar fluxos de trabalho do GATK4 com o serviço Microsoft Genomics sem ser ativado.       | Por favor, visite [aqui](https://aka.ms/msgatk4) para ativar sua conta. Observe que o teste expira no final do ano civil de 2018. Você não poderá ativar sua conta para as execuções promocionais após essa data. |
| Obrigado por experimentar `gatk4-promo`. Seu período de teste terminou. Para obter mais informações, https://docs.microsoft.com/azure/genomics/frequently-asked-questions-genomics                  | A avaliação do GATK4 expirou no final do ano civil e você está tentando invocar o `gatk4-promo` process_name.  | O parâmetro nome_do processo, de opção `gatk4`, em vez de `gatk4-promo`. Esta é a versão oficial do gatk4, e seu fluxo de trabalho será cobrado se você usar esse parâmetro.                                         |
| Obrigado por tentar `gatk4-promo` Você usou todas as execuções alocadas. Para mais informações, consulte https://docs.microsoft.com/azure/genomics/frequently-asked-questions-genomics | Você enviou com sucesso todas as suas 20 corridas promocionais para o GATK4.                               | Envie qualquer nova execução do gatk4 com o argumento process_name definido como `gatk4` em vez de `gatk4-promo`. Seu fluxo de trabalho será cobrado quando você usar esse parâmetro.                                                          |        


## <a name="can-i-run-gatk4-workflows-on-microsoft-genomics-without-signing-up-for-the-gatk4-promotion"></a>Posso executar fluxos de trabalho do GATK4 no Microsoft Genomics sem se inscrever na promoção do GATK4?
Sim, no arquivo config.txt do serviço Microsoft Genomics, especifique o process_name para `gatk4`. Observe que você será cobrado em taxas de faturamento regulares e as 20 execuções gratuitas não serão aplicadas à sua conta do Microsoft Genomics.



## <a name="what-is-the-sla-for-microsoft-genomics"></a>O que é o SLA para Microsoft Genomics?
Garantimos que em 99,9% do tempo o serviço Microsoft Genomics estará disponível para receber solicitações de API de fluxo de trabalho. Para saber mais, veja [SLA](https://azure.microsoft.com/support/legal/sla/genomics/v1_0/).

## <a name="how-does-the-usage-of-microsoft-genomics-show-up-on-my-bill"></a>Como o uso do Microsoft Genomics aparece na minha fatura?
O Microsoft Genomics é faturado de acordo com o número de gigabases processados por fluxo de trabalho. Para saber mais, consulte [Preços](https://azure.microsoft.com/pricing/details/genomics/).


## <a name="where-can-i-find-a-list-of-all-possible-commands-and-arguments-for-the-msgen-client"></a>Onde encontrar uma lista de todos os comandos e argumentos possíveis para o `msgen` cliente?
Você pode obter uma lista completa dos comandos e argumentos disponíveis executando `msgen help`. Se nenhum argumento adicional é fornecido, ele mostra uma lista de seções de ajuda disponíveis, uma para cada um dos `submit`, `list`, `cancel`, e `status`. Para obter ajuda para um comando específico, digite `msgen help command`; por exemplo, `msgen help submit` lista todas as opções de envio.

## <a name="what-are-the-most-commonly-used-commands-for-the-msgen-client"></a>Quais são os comandos mais comuns usados para o `msgen` cliente?
Os comandos mais usados são argumentos para o `msgen` cliente incluem: 

 |**Comando**          |  **Descrição do campo** |
 |:--------------------|:-------------         |
 |`list`               |Retorna uma lista de trabalhos que você enviou. Para argumentos, consulte `msgen help list`.  |
 |`submit`             |Envia uma solicitação de fluxo de trabalho para o serviço. Para argumentos, consulte `msgen help submit`.|
 |`status`             |Retorna o status do fluxo de trabalho especificado por `--workflow-id`. Consulte também `msgen help status`. |
 |`cancel`             |Envia uma solicitação para cancelar o processamento do fluxo de trabalho especificado por `--workflow-id`. Consulte também `msgen help cancel`. |

## <a name="where-do-i-get-the-value-for---api-url-base"></a>Onde posso obter o valor de `--api-url-base`?
Vá ao portal do Azure e abra a página da sua conta do Genomics. No cabeçalho **Gerenciamento**, escolha **Chaves de acesso**. Lá, você encontra a URL de API e suas chaves de acesso.

## <a name="where-do-i-get-the-value-for---access-key"></a>Onde posso obter o valor de `--access-key`?
Vá ao portal do Azure e abra a página da sua conta do Genomics. No cabeçalho **Gerenciamento**, escolha **Chaves de acesso**. Lá, você encontra a URL de API e suas chaves de acesso.

## <a name="why-do-i-need-two-access-keys"></a>Por que preciso duas chaves de acesso?
São necessárias duas chaves de acesso no caso de você desejar atualizá-las (gerá-las novamente) sem interromper o uso do serviço. Por exemplo, se você quiser atualizar a primeira chave, você deve ter todos os novos fluxos de trabalho usa a segunda chave. Em seguida, aguarde para todos os fluxos de trabalho usando a primeira chave para concluir antes de atualizar a primeira chave.

## <a name="do-you-save-my-storage-account-keys"></a>Você salva minhas chaves de conta de armazenamento?
Sua chave de conta de armazenamento é usada para criar tokens de acesso de curto prazo para o serviço Microsoft Genomics ler os arquivos de entrada e gravar os arquivos de saída. A vida útil padrão do token é de 48 horas. A duração do token pode ser alterada com a `-sas/--sas-duration` opção do comando Enviar; o valor é em horas.

## <a name="what-genome-references-can-i-use"></a>Quais referências de genoma posso usar?

Há suporte para essas referências:
 |Referência              | Valor de `-pa/--process-args` |
 |:-------------         |:-------------                 |
 |b37                    | `R=b37m1`                     |
 |hg38                   | `R=hg38m1`                    |      
 |hg38 (nenhuma análise alt) | `R=hg38m1x`                   |  
 |hg19                   | `R=hg19m1`                    |    

## <a name="how-do-i-format-my-command-line-arguments-as-a-config-file"></a>Como formato meus argumentos de linha de comando como um arquivo de configuração? 

msgen compreende os arquivos de configuração no seguinte formato:
* Todas as opções são fornecidas como pares chave-valor com valores separados de chaves por dois-pontos.
Espaço em branco é ignorado.
* Linhas que começam com `#` serão ignoradas.
* Qualquer argumento de linha de comando no formato longo pode ser convertido a uma chave retirando seus principais traços e substituindo traços entre palavras com sublinhados. Aqui estão alguns exemplos de conversão:

 |Argumento de linha de comando            | Linha de arquivo de configuração |
 |:-------------                   |:-------------                 |
 |`-u/--api-url-base https://url`  | *api_url_base:https://url*    |
 |`-k/--access-key KEY`            | *access_key:KEY*              |      
 |`-pa/--process-args R=B37m1`     | *process_args:R-b37m1*        |  

## <a name="next-steps"></a>Próximas etapas

Use os seguintes recursos para começar com o Microsoft Genomics:
- Comece executando o seu primeiro fluxo de trabalho pelo serviço do Microsoft Genomics. [Execute um fluxo de trabalho através do serviço do Microsoft Genomics](quickstart-run-genomics-workflow-portal.md)
- Envie seus próprios dados para processamento pelo serviço do Microsoft Genomics: [FASTQ emparelhado](quickstart-input-pair-FASTQ.md) | [BAM](quickstart-input-BAM.md) | [FASTQ múltiplos ou BAM](quickstart-input-multiple.md) 

