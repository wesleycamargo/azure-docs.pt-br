---
title: Requisitos de uso e exibição da Pesquisa de Respostas de Projeto - Serviços Cognitivos da Microsoft | Microsoft Docs
description: Requisitos de exibição e uso para o ponto de extremidade da Pesquisa de Respostas de Projeto.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.technology: project-answer-search
ms.topic: article
ms.date: 04/13/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 6e8eaaaa2c83a1420f2de86b23e15f4f19f7a565
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364269"
---
# <a name="project-answer-search-use-and-display-requirements"></a>Requisitos de exibição e uso para o ponto de extremidade da Pesquisa de Respostas de Projeto

Os requisitos de uso e exibição se aplicam a qualquer implementação do conteúdo e a informações associadas, por exemplo, relações, metadados e outros sinais, disponíveis por meio de chamadas para APIS de Pesquisa de Conhecimento do Bing, Pesquisa Personalizada do Bing, Pesquisa de Entidade, Pesquisa de Imagens, Pesquisa de Notícias, Pesquisa de Vídeo, Pesquisa Visual, Pesquisa na Web, Pesquisa de Verificação Ortográfica e Sugestão Automática. Detalhes de implementação relacionados a esses requisitos podem ser encontrados na documentação para recursos específicos e resultados.

## <a name="1-bing-spell-check-and-bing-autosuggest-api"></a>1. API de Verificação Ortográfica do Bing e API de Sugestão Automática do Bing.

Não:

- copiar, armazenar ou armazenar em cache os dados recebidos da Verificação Ortográfica do Bing ou das APIs de Sugestão Automática do Bing
- use os dados recebidos das APIs de Verificação Ortográfica do Bing ou de Sugestão Automática do Bing como parte de aprendizado de máquina ou atividade algorítmica semelhante para treinar, avaliar ou melhorar os serviços novos ou existentes que você ou terceiros podem oferecer.

## <a name="2-definitions"></a>2. Definições

- "resposta" refere-se a uma categoria de resultados retornados em uma resposta. Por exemplo, uma resposta da API de Pesquisa da Web do Bing pode incluir respostas nas categorias de resultados de página da Web, imagens, vídeo e notícias;
- "resposta" significa quaisquer respostas e dados associados recebidos em resposta a uma única chamada a uma API de pesquisa;
- "resultado" se refere a um item de informações em uma resposta. Por exemplo, o conjunto de dados conectados com um artigo de notícias único é um resultado em uma resposta de notícias.
- “APIs de pesquisa” significa, coletivamente, Pesquisa Personalizada do Bing, Pesquisa de Entidade, Pesquisa de Imagem, Pesquisa de Notícias, Pesquisa de Vídeo, Pesquisa Visual e APIs de Pesquisa na Web. 


## <a name="3-search-apis"></a>3. APIs de pesquisa

Os requisitos desta Seção 3 se aplicam às APIs de Pesquisa.

**A. Experiência de pesquisa da Internet.** Todos os dados retornados em respostas só podem ser usados em experiências de pesquisa da Internet. Uma experiência de pesquisa da Internet significa que o conteúdo é exibido, conforme aplicável: 
- é relevante e responsivo à consulta direta do usuário final ou outra indicação de interesse de pesquisa do usuário e à intenção (por exemplo, consulta de pesquisa indicada pelo usuário); 
- ajuda os usuários a localizar e navegar até as fontes de dados (por exemplo, as URLs fornecidas são implementadas como hiperlinks para que o conteúdo ou a atribuição seja um link clicável exibido visivelmente com os dados); ou, se da API de Pesquisa de Entidade do Bing, vincular-se visivelmente à URL de bing.com fornecida na resposta que permite que o usuário navegue até os resultados de pesquisa da consulta relevante em bing.com;
- inclui vários resultados para o usuário final escolher (por exemplo, vários resultados da resposta de notícias são exibidos ou todos os resultados, se poucos forem retornados); 
- é limitado a um valor apropriado à finalidade de pesquisa (por exemplo, imagem de miniaturas têm tamanho de miniatura em proporção à tela do usuário); 
- inclui uma indicação visível para o usuário final que o conteúdo consiste em resultados da pesquisa na Internet (por exemplo, uma instrução de que o conteúdo é "da Web"); e
- inclui qualquer outra combinação de medidas apropriadas para garantir que o uso de dados recebidos das APIs de pesquisa não viole leis ou direitos de terceiros aplicáveis (por exemplo, se depender de uma licença da Creative Commons, em conformidade com os termos aplicáveis da licença). Consulte seus consultores jurídicos para saber quais medidas podem ser apropriadas.
A única exceção para o requisito de experiência de pesquisa da Internet é para a descoberta de URL, conforme descrito na Seção 3E (detecção de URL de não exibição) a seguir. 

**B. Restrições.** Não:

- copiar, armazenar ou armazenar em cache os dados de respostas (exceto pela retenção, até a extensão permitida pela seção "Continuidade do Serviço" a seguir); 
- usar dados recebidos das APIs de Pesquisa como parte de aprendizado de máquina ou atividade algorítmica semelhante para treinar, avaliar ou melhorar os serviços novos ou existentes que você ou terceiros podem oferecer.
- modificar o conteúdo de resultados (exceto reformatá-los de uma forma que não viole qualquer outro requisito), a menos que exigido por lei ou acordado com a Microsoft; 
- omitir a atribuição e as URLs associadas ao conteúdo de resultado;
- reordenar, incluindo por omissão, resultados exibidos em uma resposta quando uma ordem ou classificação é fornecida (para a API de Pesquisa Personalizada do Bing, essa regra não se aplica ao reordenamento implementado pelo portal customsearch.ai), a menos que exigido por lei ou acordado pela Microsoft;
- exibir outros tipos de conteúdo em qualquer parte de uma resposta de uma maneira que possa levar a um usuário final a acreditar que o outro conteúdo faz parte da resposta; 
- a exibição de publicidade que não seja fornecida pela Microsoft em outra página que exiba qualquer parte de uma resposta; -a exibição de qualquer publicidade com respostas (i) a partir das APIs de Imagem, Notícias ou Pesquisa de vídeo do Bing; ou (ii) que são principalmente filtradas ou limitadas (ou unicamente) para resultados de imagens, notícias e/ou vídeo.

**C. Notificações e identidade visual.** 

- Proeminentemente incluem um hiperlink funcional para a Declaração de Privacidade da Microsoft, disponível em https://go.microsoft.com/fwlink/?LinkId=521839, perto de cada ponto na experiência do usuário (UX) que oferece a capacidade de uma consulta de pesquisa de entrada de um usuário. Dê ao hiperlink o rótulo “Declaração de Privacidade da Microsoft”.
- Exibir em proeminentemente a identidade visual do Bing, consistente com as diretrizes disponíveis em https://go.microsoft.com/fwlink/?linkid=833278, perto de cada ponto de UX que oferece a capacidade de uma consulta de pesquisa de entrada a um usuário.  Essa marca deve claramente indicar ao usuário que a Microsoft está incluindo a experiência de pesquisa da internet.
- Você pode atribuir cada resposta (ou parte de uma resposta) exibida nas APIs da Web, de Imagem, Notícias e Vídeos do Bing na Web para a Microsoft, conforme descrito em https://go.microsoft.com/fwlink/?linkid=833278, a menos que a Microsoft especifique o contrário na gravação para seu uso. 
- Não atribua respostas (ou partes de respostas) exibidas na API de Pesquisa Personalizada para a Microsoft, a menos que a Microsoft especifique o contrário na gravação para seu uso específico.


**D. Transferência de respostas.** Se você habilitar um usuário a transferir uma resposta de uma API de Pesquisa para outro usuário, como por meio de um aplicativo de mensagens ou postagem em mídia social, o seguinte será aplicável: 
- As respostas transferidas devem:
  - Consistem em conteúdo que não foi modificado em relação ao conteúdo das respostas exibidas ao usuário de transferência (alterações de formatação são permitidas);
  - Não incluir dados no formulário de metadados;
  - Para respostas das APIs da Web, Imagem, Notícias e Vídeo do Bing,exiba o idioma indicando que a resposta foi obtida por meio de uma experiência de pesquisa na Internet da plataforma Bing (por exemplo, "Da plataforma Bing" ou com o uso do logotipo do Bing);
  - Para respostas da API de Pesquisa Personalizada do Bing, exiba o idioma indicando que a resposta foi obtida através de uma experiência de pesquisa na internet (por exemplo, "Saiba mais sobre esse resultado de pesquisa”);
  - Exiba em destaque a consulta completa usada para gerar a resposta; e
  - Inclua um link de destaque ou atribuição semelhante para a fonte subjacente da resposta, seja diretamente ou por meio do mecanismos de pesquisa (bing.com, m.bing.com, ou seu serviço de pesquisa personalizado, conforme aplicável).
- Você não pode automatizar a transferência de respostas. Uma transferência deve ser iniciada por uma ação do usuário, evidenciando claramente uma tentativa de transferir uma resposta.
- Só é possível habilitar um usuário a transferir as respostas que forem exibidas em resposta à consulta do usuário de transferência.

**E. Continuidade do serviço.** Não copie, armazene ou armazene em cache os dados de respostas da API de Pesquisa. No entanto, para permitir a continuidade de acesso ao serviço e a renderização de dados, você pode manter os resultados somente sob as seguintes condições:

**Dispositivo.** Você pode permitir que um usuário final mantenha os resultados em um dispositivo para o que for menor: (i) 24 horas a partir da hora da consulta ou (ii) até que um usuário envie outra consulta para resultados atualizados, contanto que resultados retidos possam ser usados somente:

- para permitir que o usuário final acesse os resultados retornados anteriormente para o usuário final no dispositivo (por exemplo, no caso de interrupção de serviço); ou
- para armazenar os resultados retornados para sua consulta proativa personalizada prevendo as necessidades do usuário final com base em sinais do usuário final (por exemplo, no caso de interrupção de serviço prevista).

**Servidor.** Você pode manter os resultados específicos para um único usuário final com segurança em um servidor que controla e exibir os resultados mantidos somente:

- para permitir que o usuário final acesse um relatório histórico de resultados retornado anteriormente para esse usuário em sua solução, desde que os resultados não possam ser (i) retidos por mais de 21 dias a partir do momento da consulta inicial do usuário final e (ii) exibidos em resposta a uma consulta nova ou repetida de um usuário final; ou
- para armazenar os resultados retornados para a sua consulta proativa personalizada prevendo as necessidades do usuário final, com base em sinais do usuário final para o que for menor: (i) 24 horas a partir da hora da consulta ou (ii) até que um usuário final envie outra consulta para resultados atualizados.

Sempre que retidos, os resultados para um usuário específico não podem ser associados aos resultados de outro usuário, ou seja, os resultados de cada usuário devem ser retidos e entregues separadamente.

**Geral.** Para obter toda a apresentação dos resultados retidos, faça o seguinte:

- incluir um aviso criptografado visível do horário em que a consulta foi enviada,
- apresente ao usuário um botão ou meios semelhantes para consultar novamente e obter resultados atualizados, 
- manter a identidade visual do Bing na apresentação dos resultados e
- excluir (e atualizar com uma nova consulta, se necessário) os resultados armazenados em períodos de tempo especificados.

**F. Descoberta de URL não de exibição.** Você só pode usar as respostas da pesquisa em uma experiência de pesquisa não na Internet para o único propósito de descoberta de URLs de fontes de informação responsivas a uma consulta do usuário ou cliente. Você pode copiar essas URLs em um relatório ou uma resposta semelhante que você fornece (i) somente para esse usuário ou cliente, em resposta a uma consulta e (ii) que inclui conteúdo adicional significativo e valioso que é relevante para a consulta. Os requisitos nas seções 3A a 3E desses requisitos de uso e exibição não se aplicam ao uso de não exibição, exceto: 

- Você não deve armazenar em cache, copiar ou armazenar dados ou conteúdo de ou derivado da resposta de pesquisa, exceto a cópia de URL limitada descrita anteriormente;
- Você deve garantir que o uso de dados (inclusive as URLs) recebidos das APIs de Pesquisa não viole leis ou direitos de terceiros; e
- Você não deve usar os dados (inclusive as URLs) recebidos das APIs de Pesquisa como parte de qualquer índice de pesquisa ou aprendizado de máquina ou atividade algorítmica semelhante para criar, treinar, avaliar ou melhorar os serviços que você ou terceiros podem oferecer.

## <a name="next-steps"></a>Próximas etapas
[Visão Geral da Pesquisa de Resposta](overview.md)
