---
title: "Otimização de download de arquivo grande por meio do Azure CDN"
description: Otimizar downloads de arquivos grandes mergulho profundo
services: cdn
documentationcenter: 
author: smcevoy
manager: erikre
editor: 
ms.assetid: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: v-semcev
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 27e202b05f86eeee7071f3fae145caeba3d66827
ms.contentlocale: pt-br
ms.lasthandoff: 06/28/2017

---
# <a name="large-file-download-optimization-via-azure-cdn"></a>Otimização de download de arquivo grande por meio do Azure CDN

Os tamanhos de arquivos de conteúdo da internet estão crescendo progressivamente devido a funcionalidades avançadas, gráficos aprimorados e conteúdos de rich media. Isso é causado por vários fatores, inclusive a introdução de banda larga, dispositivos de armazenamento de baixo custo maiores, proliferação de vídeos de alta definição, dispositivos conectados à internet (IoT) etc.  Fornecer um mecanismo de entrega mais rápido e eficiente para esses arquivos grandes é essencial para garantir uma experiência do consumidor agradável e suave. 

Há vários desafios inerentes ao fornecimento de arquivos grandes. Primeiro, o tempo médio para baixar um arquivo grande pode ser significativo e muitos aplicativos não podem baixar todos os dados em sequência. Em alguns casos, os aplicativos podem baixar a última parte de um arquivo antes da primeira. Portanto, quando uma pequena quantidade de um arquivo for solicitada, ou o usuário pausar o download, isso pode resultar em falha ou atraso do download até que todo o arquivo seja recuperado da origem pela CDN. 

Em segundo lugar, com a proliferação de arquivos grandes na Internet, os usuários geralmente observam que a latência entre o usuário e o arquivo, por fim, determina a velocidade na qual os usuários podem exibir o conteúdo ou taxa de transferência. Além disso, os problemas de capacidade e o congestionamento de rede têm um impacto maior na taxa de transferência, e esses problemas, juntamente à distância maior entre o servidor e o usuário final, criam mais chances de ocorrência de perda de pacotes, reduzindo ainda mais a qualidade. A redução na qualidade causada pela taxa de transferência limitada e o aumento de perda de pacote pode se manifestar em um tempo de espera significativamente maior para o download de um arquivo ser concluído. 

Por fim, muitos arquivos grandes não são entregues em sua totalidade. Os usuários podem cancelar um download no meio ou assistir a somente os primeiros minutos de um vídeo MP4 longo. Portanto, é útil para muitas empresas de entrega de software e mídia entregar somente a parte de um arquivo que é solicitada pelo usuário final. Dessa forma, somente as partes solicitadas serão distribuídas com eficiência para os alcances mais distante da Internet, reduzindo o tráfego de saída da origem e, portanto, a memória e pressão I/O no servidor de origem. 

O Azure CDN do Akamai agora oferece um recurso fornecido para entregar arquivos grandes com eficiência para os usuários finais em todo o mundo em grande escala e latências reduzidas enquanto reduz a carga nos servidores de origem. Este recurso está disponível por meio do recurso "Otimizado para" no Ponto de Extremidade do Azure CDN criado em um Perfil do Azure CDN com o tipo de preço “Akamai Standard”.

## <a name="configuring-cdn-endpoint-to-optimize-delivery-of-large-files"></a>Configurar o ponto de extremidade CDN para otimizar a entrega de arquivos grandes

Você pode configurar o ponto de extremidade CDN para otimizar a entrega de arquivos grandes por meio do Portal do Azure simplesmente selecionando a opção “Download de Arquivo Grande” na seleção de propriedade “Otimizado por” durante a criação do ponto de extremidade. Você também pode usar nossas APIs REST ou qualquer um dos SDKs de cliente para fazer isso. As capturas de tela a seguir ilustram o processo por meio do Portal do Azure.

![Novo ponto de extremidade CDN](./media/cdn-large-file-optimization/01_Adding.png)  
 
*Figura 1: Adicionando um novo ponto de extremidade CDN do Perfil CDN*
 
![LFO selecionado](./media/cdn-large-file-optimization/02_Creating.png)

*Figura 2: Criar um ponto de extremidade CDN com Otimização de Download de Arquivo Grande selecionado*

Depois de criar o ponto de extremidade CDN, ele aplicará a otimização de arquivo grande para todos os arquivos que correspondem a certos critérios. A seção a seguir descreve isso em detalhes.

## <a name="optimizing-for-delivery-of-large-files-with-azure-cdn-from-akamai"></a>Otimizando para entrega de arquivos grandes com o Azure CDN do Akamai

Para o Azure CDN do Akamai, você pode usar o recurso de tipo de otimização de arquivo grande para ativar otimizações de rede e configurações que tornam a entrega de arquivos grandes mais rápida e mais ágil na resposta. Entrega geral web com Akamai é capaz de armazenar em cache somente arquivos abaixo 1,8 GB e pode transmitir (não armazenar em cache) arquivos de até 150 GB, enquanto a otimização de grandes arquivos permite que os arquivos sejam armazenados em cache até 150 GB.

A Otimização de Arquivo Grande é eficaz quando determinadas condições forem atendidas em relação a como funciona o servidor de origem, tipos de arquivos solicitados e o tamanho dos arquivos solicitados. Antes de entrarmos em detalhes sobre cada um deles, é importante entender uma visão geral de alto nível sobre como funciona a otimização. 

### <a name="object-chunking"></a>Agrupamento de objeto 

Azure CDN do Akamai emprega uma técnica chamada agrupamento de objeto onde o CDN recupera partes menores do arquivo da origem quando um arquivo grande é solicitado. Quando o servidor de borda/POP CDN recebe uma solicitação de arquivo completa ou intervalos de byte de um usuário final, ele primeiro verifica se o tipo de arquivo pertence à lista de tipos de arquivo com suporte para esta otimização, e se ela atende aos requisitos de tamanho do arquivo. Se o tamanho do arquivo é maior que 10 MB, o servidor de borda da CDN inicia solicitando o arquivo do servidor de origem em partes de 2MB. Quando a parte chega na borda da CDN, ela é armazenado em cache e imediatamente é fornecida para o usuário final, enquanto o CDN 'pré-busca' a próxima parte em paralelo essa "pré-busca" garante que o conteúdo esteja disponível antes por manter uma parte à frente do usuário e reduzindo a latência para o usuário final. Esse processo continua até que todo o arquivo é baixado (se o usuário final solicitou o arquivo inteiro), até que todos os intervalos de bytes solicitados estejam disponíveis (se o usuário final solicitado intervalos de bytes), ou até o cliente encerrar a conexão. 

Os detalhes da solicitação de intervalo de bytes podem ser encontrados no [7233 RFC](https://tools.ietf.org/html/rfc7233).

A CDN armazenará em cache todas as partes na forma como elas são recebidas e não requer que o arquivo inteiro seja armazenados em cache no cache da CDN. Solicitações subsequentes para os intervalos de bytes ou o arquivo serão fornecidas do cache da CDN e usarão o 'pré-busca' para solicitar as partes da origem se nem todas as partes são armazenadas em cache na CDN. Como pode ser visto, essa otimização se baseia em solicitações de intervalo de bytes com suporte ao servidor de origem. _Se o servidor de origem não oferece suporte a solicitações de intervalo de bytes, essa otimização não será eficaz._ 

### <a name="caching"></a>Cache
Arquivos grandes usam tempos de expiração de cache padrão diferentes que a entrega geral. Ele faz distinção entre cache positivo e negativo cache com base em códigos de resposta HTTP. Se a origem especifica uma hora usando um tempo de expiração por meio de Cache-Control ou cabeçalho Expires na resposta, a CDN sempre honrará esse valor. Quando não especifica a origem e o arquivo corresponda ao tipo de arquivo e a lista de condições de tamanho do arquivo para este tipo de otimização, a CDN usará os valores padrão para a otimização de arquivo grande. Caso contrário, a CDN usará padrões para a entrega da web geral.

 
|    | Web geral | Otimização de arquivos grandes 
--- | --- | --- 
Cache – Positivo <br> HTTP 200, 203, 300, <br> 301, 302 e 410 | 7 dias |1 dia  
Cache – Negativo <br> HTTP 204, 305, 404, <br> e 405 | nenhum | 1 segundo 

### <a name="dealing-with-origin-failure"></a>Lidando com falhas de origem

No tipo de otimização de arquivos grandes, o comprimento de leitura-tempo limite de origem é aumentado de 2 segundos em entrega de web geral para 2 minutos para contabilizar tamanhos maiores de arquivos para não atingir prematuramente o tempo limite de uma conexão.

Como com a entrega de web geral, quando uma conexão atingir o tempo limite, tentaremos novamente um determinado número de vezes antes de enviar o erro 504 Tempo Limite do Gateway para o cliente. 

### <a name="conditions-for-large-file-optimization"></a>Condições para a otimização de arquivo grande

A tabela a seguir lista o conjunto de critérios a serem atendidos para a otimização de arquivo grande:

Condição | Valores 
--- | --- 
Tipos de arquivo com suporte | 3g2, 3gp, asf, avi, bz2, dmg, exe, f4v, flv, <br> gz, hdp, iso, jxr, m4v, mkv, mov, mp4, <br> mpeg, mpg, mts, pkg, qt, rm, swf, tar, <br> tgz, wdp, webm, webp, wma, wmv, zip  
Tamanho mínimo do arquivo | 10 MB 
Tamanho máximo do arquivo | 150 GB 
Características do servidor de origem | Deve oferecer suporte a solicitações de intervalo de bytes 

## <a name="optimizing-for-delivery-of-large-files-with-azure-cdn-from-verizon"></a>Otimizando para entrega de arquivos grandes com o Azure CDN da Verizon

CDN do Azure da Verizon é capaz de entregar arquivos grandes sem um limite de tamanho do arquivo e tem vários recursos que tornam a entrega de arquivos grandes mais rápida ativados por padrão.

### <a name="complete-cache-fill"></a>Concluir o Preenchimento do Cache

A CDN do Azure da Verizon tem um recurso padrão chamado Preenchimento Completo de Cache no qual a CDN extrairá um arquivo em cache quando a solicitação inicial é abandonada ou perdida. 

Esse recurso é mais útil para ativos grandes em que os usuários normalmente não os baixarão do início ao fim (por exemplo, download progressivo de vídeos). Como resultado, esse recurso é habilitado por padrão com CDN do Azure da Verizon. Esse comportamento padrão é forçar o servidor de borda a iniciar uma busca em segundo plano do ativo do servidor de origem. Depois disso, o ativo estará no cache local do servidor de borda. Depois que o objeto completo está no cache, a borda pode atender as solicitações de intervalo de bytes para a CDN para o objeto armazenado em cache.

O comportamento de Preenchimento Completo de Cache padrão pode ser desabilitado por meio do mecanismo de regras na camada Verizon Premium.

### <a name="peer-cache-fill-hotfiling"></a>Hotfiling de Preenchimento do Cache de mesmo nível

Este é um recurso padrão da CDN do Azure da Verizon, em que um algoritmo sofisticado de proprietário pode aproveitar servidores de armazenamento em cache de borda adicional com base nas métricas, como largura de banda e solicitações de agregação para atender às solicitações de cliente para objetos grandes bastante populares. Isso impede uma situação em que grandes números de solicitações extras seriam enviados ao servidor de origem do cliente. 

### <a name="conditions-for-large-file-optimization"></a>Condições para a otimização de arquivo grande

Os recursos de otimização para Verizon estão ativados por padrão, e não há nenhum limite no tamanho máximo do arquivo. 

## <a name="additional-considerations"></a>Considerações adicionais

Há alguns aspectos adicionais a serem considerados ao usar esse tipo de otimização.
 
### <a name="azure-cdn-from-akamai"></a>CDN do Azure do Akamai

- Os resultados do processo de agrupamento em solicitações adicionais para o servidor de origem, mas o volume total de dados distribuídos da origem serão significativamente menores já que o agrupamento resulta em melhor características de cache na CDN.
- Também haverá um benefício adicional de pressão I/O e de memória reduzida na origem devido a entrega de partes menores do arquivo. 
- Para as partes que são armazenados em cache na CDN, não haverá solicitações adicionais para a origem até o conteúdo expirar do cache ou ser removido do cache devido a outros motivos. 
- O usuário pode fazer solicitações de intervalo para a CDN e eles serão tratados simplesmente como qualquer arquivo normal. A otimização será aplicada somente se for um tipo de arquivo válido e o intervalo de bytes estiver entre 10MB e 150GB. Se o tamanho médio do arquivo solicitado é menor do que 10MB, convém usar a entrega da web geral em vez disso.

### <a name="azure-cdn-from-verizon"></a>CDN do Azure da Verizon

A otimização de entrega da web geral é capaz de entregar arquivos grandes.

