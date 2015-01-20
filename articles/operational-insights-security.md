<properties urlDisplayName="Operational Insights security" pageTitle="Segurança de Insights operacionais" metaKeywords="" description="Insights operacionais é um serviço de análise que permite aos administradores de TI obter uma visão mais profunda entre ambientes locais e na nuvem. Ele permite interagir com os dados históricos e em tempo real de máquina para rapidamente desenvolver soluções personalizadas e fornecer padrões desenvolvidos pela comunidade e pela Microsoft para análise de dados." metaCanonical="" services="operational-insights" documentationCenter="" title="What is Operational Insights?" authors="banders" solutions="" manager="johndaw" editor="" />

<tags ms.service="operational-insights" ms.workload="appservices" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/06/2014" ms.author="banders" />





<h1 id="">Segurança de Insights operacionais</h1>


A Microsoft está comprometida em proteger sua privacidade e segurança de dados, oferecendo os softwares e serviços que ajudam a gerenciar a infraestrutura de TI da sua organização. Reconhecemos que quando você entrega seus dados a outros, essa confiança requer segurança rigorosa. A Microsoft obedece às diretrizes rígidas de conformidade e segurança - da codificação à operação de um serviço.

Segurança e proteção de dados é uma prioridade principal da Microsoft. Entre em contato conosco com quaisquer perguntas, sugestões ou problemas sobre qualquer uma das seguintes informações, incluindo nossas políticas de segurança: <a href="mailto:scdata@microsoft.com" target="_blank">scdata@microsoft.com</a>.

Este artigo explica como os dados são coletados, processados e protegidos no Insights Operacionais do Microsoft Azure. Você pode usar qualquer um dos agentes para se conectar diretamente ao serviço da web ou você pode usar o System Center Operations Manager para coletar dados operacionais para o serviço de Insights operacionais. Os dados coletados são enviados pela Internet para o serviço de Insights operacionais, que está hospedado no Microsoft Azure.

O serviço de Insights operacionais gerencia seus dados com segurança usando os seguintes métodos:

**Separação de dados:** os dados do cliente são mantidos separados logicamente em cada componente em todo o serviço de Insights operacionais. Todos os dados são marcados por organização. Essa marcação persiste em todo o ciclo de vida dos dados, e isso é reforçado em cada camada do serviço.

Cada cliente tem um blob do Azure dedicado que hospeda os dados de longo prazo. O blob é criptografado com chaves exclusivas por cliente, que são alteradas a cada 90 dias.

**Retenção de dados:** métricas agregadas para cada um dos pacotes de inteligência são armazenadas em um banco de dados SQL hospedado pelo Microsoft Azure. Esses dados são armazenados por dias 390. Dados de pesquisa indexada são armazenados em média por 10 dias antes que os dados sejam removidos. Se o limite superior de 20 milhões de registros para cada tipo de dados for atingido anteriormente, Insights operacional remove os dados anteriores ao 10 dias. Se o limite de dados não for atingido por 10 dias, Insights operacional aguarda até que o limite é alcançado para limpar a ele.

**Segurança física:** o serviço de Insights operacionais é operado por funcionários da Microsoft e todas as atividades são registradas e podem ser auditadas. O serviço de Insights operacionais é totalmente executado no Azure e está em conformidade com os critérios de engenharia comuns do Azure. Você pode visualizar detalhes sobre a segurança física dos ativos do Azure na página 18 da  <a href="http://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf" target="_blank">Visão geral de segurança do Microsoft Azure</a>.

**Conformidade e certificações:** a equipe de serviço e desenvolvimento de software do Insights operacionais trabalha ativamente com as equipes de conformidade normativa e Legal da Microsoft e outros parceiros do setor para adquirir várias certificações, incluindo ISO, antes do serviço de Insights operacionais estar disponível normalmente.

Estamos atendendo atualmente aos seguintes padrões de segurança:

- Critérios de engenharia comum do Windows
- Certificação de computação confiável da Microsoft



<!--HONumber=35.2-->
