---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: cognitive-services
author: MikeDodaro
ms.service: cognitive-services
ms.topic: include
ms.custom: include file
ms.date: 04/19/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: f166ceac1ae848565f861a94781ce0500c24747e
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2018
ms.locfileid: "51289148"
---
Os requisitos de uso e exibição se aplicam a qualquer implementação do conteúdo e informações associadas. Por exemplo, os requisitos se aplicam a relações, metadados e outros sinais. Eles podem estar disponíveis por meio de chamadas para as seguintes APIs:

- Pesquisa Personalizada do Bing
- Pesquisa de Entidade do Bing
- Pesquisa de Imagem do Bing
- Pesquisa de Notícias do Bing
- Pesquisa de Vídeo do Bing
- Pesquisa Visual do Bing
- Pesquisa da Web do Bing
- Verificação Ortográfica do Bing
- Sugestão Automática do Bing

Você pode encontrar detalhes de implementação relacionados a esses requisitos na documentação para recursos e resultados específicos.     

## <a name="bing-spell-check-and-bing-autosuggest-apis"></a>Api de Verificação Ortográfica do Bing e API de Sugestão Automática do Bing

Não:

- Copiar, armazenar ou armazenar em cache os dados recebidos da Verificação Ortográfica do Bing ou das APIs de Sugestão Automática do Bing.
- Use os dados recebidos das APIs de Verificação Ortográfica do Bing e Sugestão Automática do Bing como parte de qualquer aprendizado de máquina ou atividade algorítima semelhante. Não use esses dados para treinar, avaliar ou melhorar os serviços de novos ou existentes que você ou terceiros podem oferecer.

## <a name="definitions"></a>Definições

- *Resposta* refere-se a uma categoria de resultados retornados em uma resposta. Por exemplo, uma resposta da API de Pesquisa de Web do Bing pode incluir respostas nas categorias de resultados de página da Web, imagens, vídeo, visual e notícias.   
- *Resposta* significa quaisquer respostas e dados associados recebidos em resposta a uma única chamada a uma API de Pesquisa.
- *Resultado* se refere a um item de informação em uma resposta. Por exemplo, o conjunto de dados conectados com um artigo de notícias único é um resultado em uma resposta de notícias.
- *APIs de pesquisa* significa, coletivamente, Pesquisa Personalizada do Bing, pesquisa de entidade, pesquisa de imagem, pesquisa de notícias, pesquisa de vídeo, pesquisa Visual e APIs de pesquisa da Web. 


## <a name="search-apis"></a>APIs de pesquisa

Os requisitos desta seção se aplicam às APIs de pesquisa. As APIs de pesquisa não incluem a Verificação Ortográfica do Bing e a Sugestão Automática do Bing. Os requisitos para os duas APIs são abordados na seção anterior.

### <a name="internet-search-experience"></a>Experiência de pesquisa da Internet

Todos os dados retornados em respostas só podem ser usados em experiências de pesquisa da Internet. Uma experiência de pesquisa da Internet significa que o conteúdo é exibido, conforme aplicável: 
- É relevante e responsivo à consulta direta do usuário final ou outra indicação de interesse de pesquisa do usuário e à intenção (por exemplo, consulta de pesquisa indicada pelo usuário). 
- Ajuda os usuários a localizar e navegar até as fontes de dados (por exemplo, as URLs fornecidas são implementadas como hiperlinks para que o conteúdo ou a atribuição seja um link clicável exibido visivelmente com os dados). Ou, se usar uma API de Pesquisa de Entidade do Bing, acompanhada por um link visível para a URL do bing.com fornecida na resposta que permite que o usuário navegue para os resultados da pesquisa da consulta relevante no bing.com.
- Inclui vários resultados para o usuário selecionar (por exemplo, vários resultados da resposta de notícias são exibidos ou todos os resultados, se poucos forem retornados). 
- É limitado a um valor apropriado à finalidade de pesquisa (por exemplo, imagem de miniaturas têm tamanho de miniatura em proporção à tela do usuário). 
- Inclui uma indicação visível para o usuário final que o conteúdo consiste em resultados da pesquisa da Internet (por exemplo, uma instrução de que o conteúdo é "da Web").
- Inclui qualquer outra combinação de medidas apropriadas para garantir que o uso de dados recebidos das APIs de pesquisa não viole leis ou direitos de terceiros aplicáveis. Por exemplo, se você depender de uma licença Creative Commons, cumpra com os termos de licença aplicáveis. Consulte seus consultores jurídicos para saber quais medidas podem ser apropriadas.
A única exceção para o requisito de experiência de pesquisa da Internet é para a descoberta de URL, conforme descrito depois neste artigo. 

### <a name="restrictions"></a>Restrições

Não:

- Ccopiar, armazenar ou armazenar em cache os dados de respostas (exceto pela retenção, até a extensão permitida pela seção "Continuidade do Serviço" mais adiante neste artigo). 
- Use os dados recebidos das APIs de pesquisa como parte de qualquer atividade algorítmica semelhante ou de aprendizado de máquina. Não use esses dados para treinar, avaliar ou melhorar os serviços de novos ou existentes que você ou terceiros podem oferecer.
- Modificar o conteúdo de resultados (exceto reformatá-los de uma forma que não viole qualquer outro requisito), a menos que exigido por lei ou acordado com a Microsoft. 
- Omitir a atribuição e as URLs associadas ao conteúdo de resultado.
- Reordenar, incluindo por omissão, resultados exibidos em uma resposta quando uma ordem ou classificação é fornecida, a menos que exigido por lei ou acordado pela Microsoft. (Para a API de Pesquisa Personalizada do Bing, essa regra não se aplica à reordenação implementada por meio do portal customsearch.ai.)
- Exibir outros tipos de conteúdo em qualquer parte de uma resposta de uma maneira que possa levar a um usuário a acreditar que o outro conteúdo faz parte da resposta. 
- Exibir anúncios que não são fornecidos pela Microsoft em qualquer página que exiba qualquer parte de uma resposta. 
- Exibir qualquer anúncio com as respostas (i) a partir de imagem do Bing, pesquisa de notícias, pesquisa de vídeo ou APIs de pesquisa Visual, ou (ii) que são filtradas ou limitadas principalmente (ou unicamente) para a imagem, notícias e/ou vídeo ou resultados visuais.

### <a name="notices-and-branding"></a>Notificações e identidade visual 

- Proeminentemente incluem um hiperlink funcional para a [Declaração de Privacidade da Microsoft](https://go.microsoft.com/fwlink/?LinkId=521839), perto de cada ponto na experiência do usuário (UX) que oferece a capacidade de uma consulta de pesquisa de entrada de um usuário. Hiperlink de rótulo **Declaração de Privacidade da Microsoft**.
- Exibir em proeminentemente a identidade visual do Bing, consistente com as [Diretrizes de Uso da Marca Registrada do Bing](https://go.microsoft.com/fwlink/?linkid=833278), perto de cada ponto de UX que oferece a capacidade de uma consulta de pesquisa de entrada a um usuário. Essa marca deve claramente indicar ao usuário que a Microsoft está incluindo a experiência de pesquisa da internet.
- Você pode atribuir cada resposta (ou parte de uma resposta) exibida na pesquisa do Bing na Web, pesquisa de imagem, pesquisa de notícias, pesquisa de vídeo e APIs de pesquisa Visual para a Microsoft, a menos que a Microsoft especifique o contrário na gravação para seu uso. Isso é descrito em [Diretrizes de uso de Marca Registrada do Bing](https://go.microsoft.com/fwlink/?linkid=833278). 
- Não atribua respostas (ou partes de respostas) exibidas na API de Pesquisa Personalizada para a Microsoft, a menos que a Microsoft especifique o contrário na gravação para seu uso específico.

### <a name="transferring-responses"></a>Transferindo respostas

Se você habilitar um usuário a transferir uma resposta de uma API de Pesquisa para outro usuário, como por meio de um aplicativo de mensagens ou postagem em mídia social, o seguinte será aplicável: 
- As respostas transferidas devem:
  - Consistem em conteúdo que não foi modificado em relação ao conteúdo das respostas exibidas ao usuário de transferência. Alterações de formatação são permitidas.
  - Não incluir dados no formulário de metadados.
  - Para respostas de Web do Bing, imagem, notícias, vídeo e APIs Visual, idioma de exibição, indicando que a resposta foi obtida por meio de uma experiência de pesquisa da internet ativada pelo Bing. Por exemplo, você pode exibir a linguagem, como "Powered by Bing" ou "Saiba mais informações sobre essa imagem no Bing”, ou você pode usar o logotipo do Bing.
  - Para respostas da API de Pesquisa Personalizada do Bing, exiba o idioma indicando que a resposta foi obtida através de uma experiência de pesquisa na internet. Por exemplo, você pode exibir o idioma como “Saber mais sobre esse resultado de pesquisa”.
  - Exiba em destaque a consulta completa usada para gerar a resposta.
  - Inclua um link de destaque ou atribuição semelhante para a fonte subjacente da resposta, seja diretamente ou por meio do mecanismos de pesquisa (bing.com, m.bing.com, ou seu serviço de pesquisa personalizado, conforme aplicável).
- Você não pode automatizar a transferência de respostas. Uma transferência deve ser iniciada por uma ação do usuário, evidenciando claramente uma tentativa de transferir uma resposta.
- Só é possível habilitar um usuário a transferir as respostas que forem exibidas em resposta à consulta do usuário de transferência.

### <a name="continuity-of-service"></a>Continuidade do serviço 

Não copie, armazene ou armazene em cache os dados de respostas da API de Pesquisa. No entanto, para permitir a continuidade de acesso ao serviço e a renderização de dados, você pode manter os resultados somente sob as seguintes condições:

**Dispositivo.** Você pode permitir que um usuário mantenha os resultados em um dispositivo para o que for menor: (i) 24 horas a partir da hora da consulta ou (ii) até que um usuário envie outra consulta para resultados atualizados, contanto que resultados retidos possam ser usados somente:

- Para permitir que o usuário acesse os resultados retornados anteriormente para o usuário no dispositivo (por exemplo, no caso de interrupção de serviço).
- Para armazenar os resultados retornados para sua consulta proativa personalizada prevendo as necessidades do usuário com base em sinais do usuário (por exemplo, no caso de interrupção de serviço prevista).

**Servidor.** Você pode manter os resultados específicos para um único usuário com segurança em um servidor que controla e exibir os resultados mantidos somente:

- Para habilitar o usuário para acessar um relatório histórico de resultados previamente retornado ao usuário em sua solução. Os resultados podem não ser (i) mantidos por mais de 21 dias a partir do momento da consulta inicial do usuário final e (ii) exibidos em resposta à consulta de um usuário novo ou repetido.
- Para armazenar os resultados retornados para sua consulta proativa personalizada para prever as necessidades do usuário, com base em sinais do usuário final. Você pode armazenar esses resultados para o menor do que (i) 24 horas da hora da consulta, ou (ii) até que um usuário envie outra consulta para obter os resultados atualizados.

Sempre que forem retidos, osresultados para um usuário específico não podem ser misturados com os resultados de outro usuário. Ou seja, os resultados de cada usuário devem ser retidos e entregues separadamente.

### <a name="general"></a>Geral 

Para obter toda a apresentação dos resultados retidos, faça o seguinte:

- Inclua um aviso criptografado visível do horário em que a consulta foi enviada.
- Apresente ao usuário um botão ou meios semelhantes para consultar novamente e obter resultados atualizados. 
- Mantenha a identidade visual do Bing na apresentação dos resultados.
- Exclua (e atualize com uma nova consulta, se necessário) os resultados armazenados em períodos de tempo especificados.

### <a name="non-display-url-discovery"></a>Descoberta de URL não de exibição 

Você só pode usar as respostas da pesquisa em uma experiência de pesquisa não na Internet para o único propósito de descoberta de URLs de fontes de informação responsivas a uma consulta do usuário ou cliente. Você pode copiar essas URLs em um relatório ou uma resposta semelhante que você fornecer:

- Somente para esse usuário ou cliente, em resposta à consulta.
- Somente se ele inclui significativo conteúdo adicional valioso, relevante para a consulta.

As seções anteriores de APIs de Pesquisa usam e exibem requisitos que não se aplicam a esse uso de não exibição, exceto para o seguinte: 

- Você não deve armazenar em cache, copiar ou armazenar dados ou conteúdo de ou derivado da resposta de pesquisa, exceto a cópia de URL limitada descrita anteriormente.
- Você deve garantir que o uso de dados (inclusive as URLs) recebidos das APIs de Pesquisa não viole leis ou direitos de terceiros.
- Não use os dados (inclusive as URLs) recebidos das APIs de Pesquisa como parte de qualquer índice de pesquisa ou aprendizado de máquina ou atividade algorítmica semelhante. Não use esses dados para treinar, avaliar ou melhorar os serviços que você ou terceiros podem oferecer.

## <a name="gdpr-compliance"></a>Conformidade de GDPR  

Com relação a alguns dados pessoais sujeitos à Regulação de Proteção de Dados Gerais da União Europeia (GDPR) e que é processada em conexão com as chamadas para as APIs de Pesquisa, API de Verificação Ortográfica do Bing ou API de Sugestão Automática do Bing, você entende que você e a Microsoft são controladores de dados independentes da GDPR. Você é responsável independentemente de sua conformidade com a GDPR.  

