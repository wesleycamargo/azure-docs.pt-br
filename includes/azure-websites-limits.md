---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 515aeac3531a45080824df126ad674353f70cdb8
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2018
ms.locfileid: "52279425"
---
| Recurso | Grátis | Compartilhado | Basic | Standard | Premium (v2) | Isolado </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [Aplicativos Web, móveis ou de API](https://azure.microsoft.com/services/app-service/) por [plano do Serviço de Aplicativo](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)<sup>1</sup> |10 |100 |Ilimitado<sup>2</sup> |Ilimitado<sup>2</sup> |Ilimitado<sup>2</sup> |Ilimitado<sup>2</sup>|
| [Plano do Serviço de Aplicativo](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) |1 por região |10 por grupo de recursos |100 por grupo de recursos |100 por grupo de recursos |100 por grupo de recursos |100 por grupo de recursos|
| Tipo de instância de computação |Compartilhado |Compartilhado |Dedicado<sup>3</sup> |Dedicado<sup>3</sup> |Dedicado<sup>3</sup></p> |Dedicado<sup>3</sup>|
| [Escalabilidade](../articles/app-service/web-sites-scale.md) (máximo de instâncias) |1 compartilhada |1 compartilhada |3 dedicados<sup>3</sup> |10 dedicados<sup>3</sup> |20 dedicados<sup>3</sup>|100 dedicados<sup>4</sup>|
| Armazenamento<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup></p> |1 TB<sup>5</sup>|
| Tempo de CPU (5 min)<sup>6</sup> |3 minutos |3 minutos |Ilimitado, pagamento das [tarifas](https://azure.microsoft.com/pricing/details/app-service/)</a> padrão |Ilimitado, pagamento das [tarifas](https://azure.microsoft.com/pricing/details/app-service/)</a> padrão |Ilimitado, pagamento das [tarifas](https://azure.microsoft.com/pricing/details/app-service/)</a> padrão |Ilimitado, pagamento das [tarifas](https://azure.microsoft.com/pricing/details/app-service/)</a> padrão|
| Tempo de CPU (dia)<sup>6</sup> |60 minutos |240 minutos |Ilimitado, pagamento das [tarifas](https://azure.microsoft.com/pricing/details/app-service/)</a> padrão |Ilimitado, pagamento das [tarifas](https://azure.microsoft.com/pricing/details/app-service/)</a> padrão |Ilimitado, pagamento das [tarifas](https://azure.microsoft.com/pricing/details/app-service/)</a> padrão |Ilimitado, pagamento das [tarifas](https://azure.microsoft.com/pricing/details/app-service/)</a> padrão |
| Memória (1 hora) |1024 MB por plano de Serviço de Aplicativo |1024 MB por aplicativo |N/D |N/D |N/D |N/D |
| Largura de banda |165 MB |Ilimitada, aplicam-se [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) |Ilimitada, aplicam-se [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) |Ilimitada, aplicam-se [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) |Ilimitada, aplicam-se [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) |Ilimitada, aplicam-se [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) |
| Arquitetura do aplicativo |32 bits |32 bits |32 bits/64 bits |32 bits/64 bits |32 bits/64 bits |32 bits/64 bits |
| Soquetes Web por instância<sup>7</sup> |5 |35 |350 |Ilimitado |Ilimitado |Ilimitado |
| [Conexões do depurador](../articles/app-service/web-sites-dotnet-troubleshoot-visual-studio.md) simultâneas por aplicativo |1 |1 |1 |5 |5 |5 |
| Certificados de serviço de aplicativo por assinatura<sup>10</sup>| Sem suporte | Sem suporte |10 |10 |10 |10 |
| Domínios personalizados por aplicativo</a> |0 (somente subdomínio do azurewebsites.net)|500 |500 |500 |500 |500 |
| domínio personalizado [Suporte a SSL](../articles/app-service/app-service-web-tutorial-custom-ssl.md) |Sem suporte. Certificado curinga para *.azurewebsites.net disponível por padrão.|Sem suporte. Certificado curinga para *.azurewebsites.net disponível por padrão.|Conexões SSL de SNI ilimitadas |Conexões SSL de SNI ilimitadas e IP SSL incluídas |Conexões SSL de SNI ilimitadas e IP SSL incluídas | Conexões SSL de SNI ilimitadas e IP SSL incluídas|
| Balanceador de carga integrado | |X |X |X |X |X<sup>9</sup> |
| [Sempre ativo](../articles/app-service/web-sites-configure.md) | | |X |X |X |X |
| [Backups agendados](../articles/app-service/web-sites-backup.md) | | | | Backups agendados a cada 2 horas, um máximo de 12 backups por dia (manual + agendado) | Backups agendados a cada hora, um máximo de 50 backups por dia (manual + agendado) | Backups agendados a cada hora, um máximo de 50 backups por dia (manual + agendado) |
| [Dimensionamento automático](../articles/app-service/web-sites-scale.md) | | | |X |X |X |
| [WebJobs](../articles/app-service/web-sites-create-web-jobs.md)<sup>8</sup> |X |X |X |X |X |X |
| [Agendador do Azure](https://azure.microsoft.com/services/scheduler/)  | |X |X |X |X |X |
| [Monitoramento do ponto de extremidade](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| [Slots de preparação](../articles/app-service/web-sites-staged-publishing.md) | | | |5 |20 |20 |
| Contrato de Nível de Serviço | |  |99,9% |99,95%|99,95%|99,95%|  

<sup>1</sup> As cotas de aplicativos e armazenamento são por plano do Serviço de aplicativo, a menos que seja indicado o contrário.  
<sup>2</sup> O número real de aplicativos que você pode hospedar nessas máquinas depende da atividade dos aplicativos, do tamanho das instâncias da máquina e da utilização de recursos correspondente.  
<sup>3</sup> Instâncias dedicadas podem ter tamanhos diferentes. Confira [Preços do Serviço de Aplicativo](https://azure.microsoft.com/pricing/details/app-service/) para obter mais detalhes.  
<sup>4</sup> mais são permitidas sob solicitação.  
<sup> 5 </sup> O limite de armazenamento é o tamanho total do conteúdo em todos os aplicativos no mesmo plano do Serviço de aplicativos.  
<sup>6</sup> Esses recursos são restritos por recursos físicos nas instâncias dedicadas (o tamanho da instância e o número de instâncias).  
<sup>7</sup> Se você dimensionar um aplicativo na camada Básica para duas instâncias, você terá 350 conexões simultâneas para cada uma das duas instâncias.  
<sup>8</sup> Execute executáveis e / ou scripts personalizados sob demanda, em uma programação ou continuamente como uma tarefa em segundo plano em sua instância do Serviço de Aplicativo. Para a execução contínua de Trabalhos Web, a opção Sempre Ativado é obrigatória. Trabalhos Web agendados requerem o Agendador do Azure Gratuito ou Standard. Não há nenhum limite predefinido na quantidade de WebJobs que podem ser executados em uma instância do Serviço de Aplicativo, mas há limites práticos que dependem do que o código do aplicativo está tentando fazer.  
<sup>9</sup> Serviço de Aplicativo Os SKUs isolados podem ser balanceados com carga interna (ILB) com o Balanceador de carga do Azure, o que significa que não há conectividade pública da Internet. Como resultado, alguns recursos do Serviço de Aplicativo Isolado de ILB devem ser usados de computadores com acesso direto ao ponto de extremidade de rede de ILB.  
<sup>10</sup> O limite de cota do Certificado de Serviço de Aplicativo por assinatura pode ser aumentado por meio de uma solicitação de suporte para um limite máximo de 200.  