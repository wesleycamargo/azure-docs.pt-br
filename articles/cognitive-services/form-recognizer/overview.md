---
title: O que é o Reconhecimento de Formulários?
titleSuffix: Azure Cognitive Services
description: Saiba como usar o Reconhecimento de Formulários para analisar dados de formulário e de tabela.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: overview
ms.date: 04/08/2019
ms.author: pafarley
ms.openlocfilehash: 2a120a59a58eb8d7a017cce0dd85c21038bdcf51
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65143209"
---
# <a name="what-is-form-recognizer"></a>O que é o Reconhecimento de Formulários?

O Reconhecimento de Formulários do Azure é um serviço cognitivo que usa a tecnologia de aprendizado de máquina para identificar e extrair pares chave-valor e dados de tabela de documentos de formulário. Em seguida, ele gera dados estruturados que incluem as relações no arquivo original. Chame seu modelo personalizado do Reconhecimento de Formulários usando uma API REST simples para reduzir a complexidade e integrá-lo com facilidade ao seu fluxo de trabalho ou aplicativo. Você precisa apenas de cinco documentos de formulário ou um formulário vazio do mesmo tipo como o material de entrada para começar a usar. É possível obter resultados com rapidez e precisão, adaptados ao seu conteúdo específico, sem a necessidade de intervenção manual intensa nem ampla experiência em ciência de dados.

## <a name="request-access"></a>Solicitar acesso
O Reconhecimento de Formulários está disponível como uma versão prévia de acesso limitado. Para obter acesso à versão prévia, preencha e envie o formulário de [solicitação de acesso do Reconhecimento de Formulários dos Serviços Cognitivos](https://aka.ms/FormRecognizerRequestAccess). O formulário solicita informações sobre você, sua empresa e o cenário do usuário para o qual usará o Reconhecimento de Formulários. Caso sua solicitação seja aprovada pela equipe dos Serviços Cognitivos do Azure, você receberá um email com instruções sobre como acessar o serviço.

## <a name="what-it-does"></a>O que faz

Quando você envia seus dados de entrada, o algoritmo é treinado neles, agrupa os formulários por tipos, descobre quais chaves e tabelas estão presentes e aprende a associar valores a chaves e entradas a tabelas. O aprendizado sem supervisão permite que o modelo entenda o layout e as relações entre os campos e as entradas sem rotulagem manual de dados nem codificação e manutenção intensivas. Por outro lado, os modelos de machine learning pré-treinados exigem dados padronizados e são menos precisos com o material de entrada que se desvia de formatos tradicionais, como formulários específicos do setor.

Depois que o modelo é treinado, você pode testá-lo, treiná-lo novamente e, por fim, usá-lo para extrair, de forma confiável, dados de mais formulários de acordo com suas necessidades.

## <a name="what-it-includes"></a>O que inclui

O Reconhecimento de Formulários está disponível como uma API REST. Você pode criar, treinar e pontuar um modelo invocando a API e, opcionalmente, executar o modelo em um contêiner local do Docker.

## <a name="input-requirements"></a>Requisitos de entrada

O Reconhecimento de Formulários funciona em documentos de entrada que atendem aos seguintes requisitos:

* Formato JPG, PNG ou PDF (texto ou digitalizado). PDFs inseridos com texto são preferíveis porque não há nenhuma possibilidade de erro na extração e na localização de caracteres.
* O tamanho do arquivo precisa ser inferior a 4 MB (megabytes)
* Para imagens, as dimensões precisam estar entre 50x50 e 4.200x4.200 pixels
* Se forem digitalizados de documentos em papel, os formulários deverão ser digitalizações de alta qualidade
* Precisam usar o alfabeto latino (caracteres em inglês)
* Dados impressos (não manuscritos)
* Precisam conter chaves e valores
* As chaves podem aparecer acima ou à esquerda dos valores, mas não abaixo nem à direita.

Além disso, o Reconhecimento de Formulários ainda não dá suporte aos seguintes tipos de dados de entrada:

* Tabelas complexas (tabelas aninhadas, cabeçalhos mesclados ou células e assim por diante) 
* Caixas de seleção ou botões de opção
* Documentos PDF com mais de 50 páginas

## <a name="where-do-i-start"></a>Por onde começo?

**Etapa 1:** Criar um recurso do Reconhecimento de Formulários no portal do Azure.

**Etapa 2:** Experimentar um Início Rápido para obter experiência prática:
* [Início Rápido: Treinar um modelo do Reconhecimento de Formulários e extrair dados de formulário usando a API REST com o cURL](quickstarts/curl-train-extract.md)
* [Início Rápido: Treinar um modelo do Reconhecimento de Formulários e extrair dados de formulário usando a API REST com o Python](quickstarts/python-train-extract.md)

Recomendamos o serviço Gratuito para fins de aprendizado, mas esteja ciente de que o número de páginas gratuitas é limitado a 500 páginas por mês.

**Etapa 3:** Examinar a API REST e usar as APIs a seguir para treinar e extrair dados estruturados de formulários.

| API REST | DESCRIÇÃO |
|-----|-------------|
| Treinar | Treine um novo modelo para analisar seus formulários usando 5 formulários do mesmo tipo ou um formulário vazio.  |
| Analise  |Analise um único documento passado como um fluxo para extrair pares chave-valor e tabelas do formulário com o modelo personalizado.  |

Explore o [documento de referência da API REST](https://aka.ms/form-recognizer/api). 

## <a name="data-privacy-and-security"></a>Segurança e privacidade de dados

O serviço é oferecido como uma [versão prévia](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de um serviço do Azure de acordo com os [Termos de Serviço Online](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31). Assim como ocorre com todos os Serviços Cognitivos, os desenvolvedores que usam o serviço Reconhecimento de Formulários devem estar cientes das políticas da Microsoft referentes aos dados do cliente. Confira a [página de Serviços Cognitivos](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) na Central de Confiabilidade da Microsoft para saber mais.

## <a name="next-steps"></a>Próximas etapas

Siga um [Início Rápido](quickstarts/curl-train-extract.md) para começar a usar as [APIs do Reconhecimento de Formulários](https://aka.ms/form-recognizer/api).
