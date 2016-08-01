<properties
   pageTitle="Solução do problema de lentidão de backup de arquivos e pastas no Backup do Azure | Microsoft Azure"
   description="Fornece uma orientação para ajudar você a diagnosticar e a refinar a causa do baixo desempenho do Backup do Azure"
   services="backup"
   documentationCenter=""
   authors="genlin"
   manager="markgal"
   editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/20/2016"
    ms.author="genli"/>

# Solução do problema de lentidão de backup de arquivos e pastas no Backup do Azure

Este artigo fornece uma orientação para ajudar você a diagnosticar e a refinar a causa do baixo desempenho de backup de arquivos e pastas usando o Backup do Azure. Quando você usa o agente do Backup do Azure para fazer backup de arquivos, o processo de backup pode demorar mais do que o esperado. Esse problema pode ter uma das seguintes causas:

-	[Afunilamentos de desempenho no computador do qual está sendo feito o backup](#cause1).
-	[Outro processo ou software antivírus está interferindo com o Backup do Azure](#cause2).
-	[O agente do Backup está em execução em uma VM (máquina virtual) do Azure](#cause3).
-	[Você está tentando fazer backup de uma quantidade muito grande de arquivos (bilhões)](#cause4).

A próxima seção ajudará a determinar a causa específica do problema.

Antes de começar a solucionar o problema, recomendamos o download e a instalação do [agente mais recente do Backup do Azure](http://aka.ms/azurebackup_agent). Fazemos atualizações frequentes do agente de Backup para corrigir vários problemas, adicionar recursos e melhorar o desempenho.

Também recomendamos que você revise as [Perguntas frequentes do serviço Backup do Azure](backup-azure-backup-faq.md) para ter certeza de que você não está enfrentando problemas comuns de configuração.

## Etapas para solucionar problemas
<a id="cause1"></a>
## Causa 1: lentidão no backup devido a afunilamentos de desempenho no computador do qual está sendo feito o backup

### Solução

Pode haver alguns afunilamentos no computador do qual está sendo feito o backup que podem causar atrasos. Por exemplo, capacidade do computador de ler ou gravar no disco, larguras de banda para enviar dados pela rede etc.

O Windows fornece uma ferramenta interna chamada [Monitor de desempenho](https://technet.microsoft.com/magazine/2008.08.pulse.aspx)(Perfmon) para detectar esses afunilamentos. A tabela a seguir resume os contadores de desempenho e os intervalos para obter os Backups ideais.

Veja alguns contadores de desempenho e intervalos que podem ser úteis para diagnosticar afunilamentos.

| Contador | Status |
|---|---|
|Disco Lógico(Disco Físico) – %ocioso | • 100% ocioso a 50% ocioso = Íntegro</br>• 49% ocioso a 20% ocioso = Aviso ou Monitorar</br>• 19% ocioso a 0% ocioso = Crítico ou Fora de Especificação|
| Disco Lógico(Disco Físico) -- %média Leitura ou Gravação do Disco por S | • 0,001ms a 0,015ms = Íntegro</br>• 0,015ms a 0,025 = Aviso ou Monitorar</br>• 0,026ms ou mais = Crítico ou Fora de Especificação|
| Disco Lógico(Disco Físico) -- Comprimento da Fila do Disco Atual (para todas as instâncias) | 80 solicitações por mais de seis minutos |
| Memória--Bytes de Pool não Pagináveis|• Menos de 60% do pool consumido = Íntegro<br>• 61% a 80% do pool consumido = Aviso ou Monitorar</br>• Mais de 80% do pool consumido = Crítico ou Fora de Especificação|
| Memória--Bytes de Pool Pagináveis |• Menos de 60% do pool consumido = Íntegro</br>• 61% a 80% do pool consumido = Aviso ou Monitorar.</br>• Mais de 80% do pool consumido = Crítico ou Fora de Especificação.|
| Memória--Megabytes disponíveis| • 50% de memória livre disponível ou mais = Íntegro</br>• 25% de memória livre disponível = Monitorar.</br>• 10% de memória livre disponível = Aviso.</br>• Menos de 100MB ou 5% de memória livre disponível = Crítico ou Fora de Especificação.|
|Processor--\\%Tempo do Processor (todas as instâncias)|• Menos de 60% consumido = Íntegro</br>• 61% a 90% consumido = Monitorar ou Cuidado</br>• 91% a 100% consumido = Crítico|


> [AZURE.NOTE] Se a infraestrutura isolada for a possível culpada, é aconselhável desfragmentar os discos que estão sendo protegidos regularmente para melhorar o desempenho.

<a id="cause2"></a>
## Causa 2: outro processo ou software antivírus está interferindo com o processo de Backup do Azure

Vimos várias instâncias nas quais outros processos no sistema do Windows afetou negativamente o desempenho do processo do agente de Backup do Azure. Por exemplo, se você usar o agente de Backup do Azure e outro programa para fazer backup de dados, ou um software antivírus estiver em execução e bloquear os arquivos dos quais o backup deverá ser feito, os vários bloqueios nos arquivos poderão causar contenção. Nessa situação, o backup pode falhar ou o trabalho pode demorar mais do que o esperado.

### Solução

A melhor recomendação nesse cenário é desativar o outro programa backup para ver se o tempo de backup do agente de Backup do Azure muda. Geralmente, certificar-se de que não exista vários trabalhos de backup em execução simultaneamente é suficiente para impedir uma sobreposição.

Para programas antivírus, recomendamos a exclusão dos seguintes arquivos e locais:

- Exclua C:Arquivos de Programas\\Agente dos Serviços de Recuperação do Microsoft Azure\\bin\\cbengine.exe como um processo.
- Exclua pastas C:Arquivos de Programas\\Agente dos Serviços de Recuperação do Microsoft Azure\\.
- Exclua o local do Scratch (se não estiver usando o local padrão acima).

<a id="cause3"></a>
## Causa 3 O agente do Backup está em execução em uma VM (máquina virtual) do Azure

### Solução

Se você estiver executando o agente de Backup em uma máquina virtual, o desempenho será mais lento do que quando você executá-lo em uma máquina física. Isso é esperado devido a limitações de IOPS. No entanto, você pode otimizar o desempenho alternando as unidades de dados que estão passando por backup no armazenamento premium. Estamos trabalhando para corrigir esse problema, e a correção estará disponível em uma versão futura.

<a id="cause4"></a>
## Causa 4 Backup de uma grande quantidade de arquivos (bilhões)

### Solução

Espera-se que a movimentação de um grande volume de dados leve mais tempo do que um volume menor. Mas, em alguns casos, o tempo de backup não está relacionado apenas ao tamanho dos dados, mas também ao número de arquivos ou pastas, especialmente para o cenário em que milhões de arquivos pequenos (alguns Bytes até alguns Quilobytes) estão passando por backup.

Esse comportamento ocorre porque durante o backup dos dados e movimentação para o Azure, estamos catalogando simultaneamente os arquivos e, em algumas situações raras, a operação de catálogo pode demorar mais.

Execute as etapas abaixo para entender o gargalo e trabalhar adequadamente nas próximas etapas:

a. **Interface do usuário mostra o progresso da quantidade de dados transferidos**- Nesse caso, os dados ainda estão sendo transferidos e a largura de banda de rede ou o tamanho dos dados pode estar causando atrasos.

b. **Interface do usuário não mostra o progresso**- Nesse caso, abra os logs localizados em "C:\\Agente do Serviços de Recuperação do Microsoft Azure\\Temp" e procure a entrada "FileProvider::EndData" nos logs. Essa entrada significa que a transferência de dados está concluída e a operação de catálogo está acontecendo. Não cancele os trabalhos de backup, em vez disso, aguarde algum tempo para a conclusão do catálogo. Se o problema persistir, contate o [Suporte do Azure](https://portal.azure.com/#create/Microsoft.Support).

<!---HONumber=AcomDC_0720_2016-->