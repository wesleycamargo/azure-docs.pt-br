<properties
   pageTitle="Operando o Repositório de Consultas no Banco de Dados SQL do Azure"
   description="Saiba como operar o Repositório de Consultas no Banco de Dados SQL do Azure"
   keywords=""
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="sqldb-performance"
   ms.workload="data-management"
   ms.date="05/25/2016"
   ms.author="carlrab"/>

# Operando o Repositório de Consultas no Banco de Dados SQL do Azure 

O Repositório de Consultas no Azure é um recurso de banco de dados totalmente gerenciado que continuamente coleta e apresenta informações históricas sobre todas as consultas. Você pode pensar sobre Repositório de Consultas de forma semelhante à caixa-preta de bordo do avião que simplifica significativamente o desempenho da consulta de solução de problemas para a nuvem e os clientes locais. Este artigo explica os aspectos específicos da operação de Repositório de Consultas no Azure. Usando esta consulta de dados previamente coletados, os usuários podem diagnosticar e resolver problemas de desempenho rapidamente e, portanto, gastar mais tempo concentrando-se em seus negócios.

O Repositório de Consultas ficou [disponível globalmente](https://azure.microsoft.com/updates/general-availability-azure-sql-database-query-store/) no Banco de Dados SQL do Azure desde novembro de 2015. O Repositório de Consulta é a base para a análise de desempenho e ajuste de recursos, tais como o [Painel do Orientador e Desempenho do Banco de Dados SQL](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). No momento da publicação deste artigo, o Repositório de Consultas está sendo executado em mais de 200.000 bancos de dados do usuário no Azure, coletando informações relacionadas a consultas por vários meses, sem interrupção.

> [AZURE.IMPORTANT] A Microsoft está em processo de ativação do Repositório de Consultas para todos os bancos de dados SQL do Azure (novos e existentes). Esse processo de ativação atualmente inclui apenas os bancos de dados singleton, mas será expandido para bancos de dados do Pool Elástico em breve. Ao usar Pools Elásticos, habilite o Repositório de Consultas para o pequeno subconjunto de bancos de dados. Habilitar o Repositório de Consulta para todos os bancos de dados em um Pool Elástico pode levar a uso excessivo de recursos e poderia deixar o sistema sem resposta.

## Repositório de Consulta como um recurso gerenciado no Banco de Dados SQL

O Repositório de Consulta opera no Banco de Dados SQL do Azure com base nestes dois princípios fundamentais:

- Não causa impacto visível à carga de trabalho do cliente
- Monitore continuamente as consultas, a menos que uma carga de trabalho do cliente seja afetada

O impacto sobre a carga de trabalho do cliente tem duas dimensões:

- ***Disponibilidade***: o [SLA para o Banco de Dados SQL](https://azure.microsoft.com/support/legal/sla/sql-database/v1_0/) não é reduzido quando o Repositório de Consulta está em execução.
- ***Desempenho***: a média de sobrecarga introduzida pelo Repositório de Consultas no intervalo de 1–2%

O Repositório de Consultas no Azure opera usando recursos limitados (CPU, memória, disco de E/S, tamanho em disco, etc.). Ele respeita várias limitações de sistema para afetar minimamente a carga de trabalho normal:

- ***Limitações estáticas;*** limitações impostas pela capacidade do recurso de um tipo de serviço (Básico, Standard, Premium e Pool Elástico).
- ***Limitações dinâmicas:*** limitações impostas pelo consumo de carga de trabalho atual (ou seja, recursos disponíveis).

Para garantir a operação contínua e confiável, o Banco de Dados SQL do Azure criou uma infraestrutura de monitoramento permanente baseada no Repositório de Consultas que coleta dados os operacionais principais de cada banco de dados. Como resultado, vários KPIs técnicos estão sendo monitorados constantemente para garantir a confiabilidade:

- Número de exceções e reduções automáticas
- Número de bancos de dados no estado READ\_ONLY e a duração do estado READ\_ONLY
- Principais bancos de dados com consumo de memória do Repositório de Consulta acima do limite.
- Bancos de dados principais por frequência e duração de limpeza automática
- Principais bancos de dados por duração de carregamento de dados na memória (inicialização do Repositório de Consulta)
- Bancos de dados principais por duração da liberação dos dados para o disco

O Banco de Dados SQL do Azure usa os dados coletados para:

- ***Aprender os padrões de uso em um grande número de bancos de dados e, consequentemente, melhorar a qualidade e a confiabilidade do recurso:*** o Repositório de Consultas é aprimorado a cada atualização do Banco de Dados SQL do Azure. 
- ***Solucionar ou atenuar problemas causados pelo Repositório de Consultas:*** o Banco de Dados SQL do Azure pode detectar e reduzir os problemas que têm um impacto substancial sobre a carga de trabalho do cliente, com baixa latência (menos de uma hora). Com frequência, os problemas são tratados configurando o Repositório de Consultas para ***DESLIGADO*** temporariamente.

De tempos em tempos, as atualizações do Repositório de Consultas introduzem alterações com as configurações padrão aplicadas a configurações internas e raramente externas (voltadas para o cliente). Consequentemente, a experiência do cliente com Repositório de Consultas no Banco de Dados SQL pode diferir de ambientes locais devido às ações automáticas realizadas pela plataforma do Azure:

- O estado do Repositório de Consultas poderá ser alterado para ***DESLIGADO*** para atenuar os problemas e novamente para ***LIGADO*** quando o problema tiver sido resolvido.
- A configuração do Repositório de Consultas pode ser alterada para garantir que funcionem de maneira confiável. Isso pode ser executado como:
    - Banco de dados individuais alteram problemas de instabilidade do endereço ou falta de confiabilidade.
    - A distribuição global de alterações de configuração ideal oferecendo benefícios para todos os bancos de dados que usam o Repositório de Consultas.

Definir o Repositório de Consultas como ***DESLIGADO*** é uma ação automática com escopo para bancos de dados individuais. Ocorre quando há um comportamento de produto que afeta negativamente os bancos de dados do usuário para o qual o mecanismo de detecção disparou um alerta. Para um determinado banco de dados, o Repositório de Consultas permanece ***DESLIGADO*** até que uma nova versão com uma melhor implementação do Repositório de Consultas fique disponível. Quando uma transição para o estado ***DESLIGADO*** ocorre, é aconselhável que o cliente seja informado por email e não habilite novamente o Repositório de Consultas até que uma nova versão seja implementada. Após uma nova distribuição, a infraestrutura do Banco de Dados SQL do Azure ativa automaticamente o Repositório de Consultas para qualquer banco de dados para o qual ele foi definido como ***DESLIGADO***.

Com menos frequência, o Banco de Dados SQL pode aplicar novos padrões de configuração para todos os bancos de dados de usuário, otimizados para trabalho confiável e coleta de dados contínua.

## Configuração do Repositório de Consulta ideais

Esta seção descreve os padrões de configuração ideais que são projetados para garantir a operação confiável do Repositório de Consultas, bem como recursos dependentes, como o [Painel de Orientador e Desempenho do Banco de Dados SQL](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). A configuração padrão é otimizada para coleta de dados contínua, ou seja, tempo mínimo gasto em estados OFF/READ\_ONLY.

| Configuração | Descrição | Padrão | Comentário |
| ------------- | ----------- | ------- | ------- |
| MAX\_STORAGE\_SIZE\_MB | Especifica o limite para o espaço de dados que o Repositório de Consultas ocupará no banco de dados do cliente | 100 | Imposto para novos bancos de dados |
| INTERVAL\_LENGTH\_MINUTES | Define o tamanho da janela de tempo durante o qual as estatísticas de tempo de execução coletadas para planos de consulta são agregadas e persistidas. Cada plano de consulta ativa terá no máximo uma linha por um período de tempo definido com esta configuração | 60 | Imposto para novos bancos de dados |
| STALE\_QUERY\_THRESHOLD\_DAYS | Política de limpeza com base em tempo que controla o período de retenção de estatísticas de tempo de execução persistidas e consultas inativas | 30 | Imposto para novos bancos de dados e bancos de dados padrão anteriores (367) |
| SIZE\_BASED\_CLEANUP\_MODE | Especifica se a limpeza automática de dados ocorrerá quando o tamanho dos dados do Repositório de Consultas aproximar-se do limite | AUTO | Imposto para todos os bancos de dados |
| QUERY\_CAPTURE\_MODE | Especifica se todas as consultas ou apenas um subconjunto de consultas será controlado | AUTO | Imposto para todos os bancos de dados |
| FLUSH\_INTERVAL\_SECONDS | Especifica o período máximo durante o qual as estatísticas de tempo de execução coletadas serão mantidas na memória antes de liberar para disco | 900 | Imposto para novos bancos de dados |
||||||

> [AZURE.IMPORTANT] Esses padrões serão aplicados automaticamente no estágio final da ativação do Repositório de Consultas em todos os bancos de dados SQL do Azure (veja a observação importante acima). Após essa iluminação, o Banco de Dados SQL do Azure não alterará os valores de configuração definidos por clientes, a menos que afete negativamente as operações confiáveis de armazenamento de consulta ou carga de trabalho principal no Repositório de Consultas.

Se você quiser manter suas configurações personalizadas, use [ALTER DATABASE com opções de Repositório de Consultas](https://msdn.microsoft.com/library/bb522682.aspx) para reverter a configuração ao estado anterior. Confira as [Práticas recomendadas com o Repositório de Consultas](https://msdn.microsoft.com/library/mt604821.aspx) para saber como escolher os parâmetros de configuração ideais.

## Próximas etapas

[Análise de Desempenho de Banco de Dados SQL](sql-database-performance.md)

## Recursos adicionais

Para obter mais informações, confira os seguintes artigos:

- [Uma caixa-preta de bordo para seu banco de dados](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database) 

- [Monitorando o desempenho usando o Repositório de Consultas](https://msdn.microsoft.com/library/dn817826.aspx)

- [Cenários de Uso do Repositório de Consultas](https://msdn.microsoft.com/library/mt614796.aspx)

- [Monitorando o desempenho usando o Repositório de Consultas](https://msdn.microsoft.com/library/dn817826.aspx)

<!---HONumber=AcomDC_0615_2016-->