<properties
 pageTitle="Entenda e resolva erros do WebHCat no HDInsight"
 description="Saiba sobre erros comuns retornados pelo WebHCat no HDInsight e como resolvê-los."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="jhubbard"
 editor="cgronlun"
 tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="07/12/2016"
 ms.author="larryfr"/>

#Entenda e resolva erros recebidos do WebHCat (Templeton) no HDInsight

Ao usar o WebHCat (anteriormente conhecido como Templeton) para trabalhar com o HDInsight, você pode receber erros. Este documento fornece orientação sobre erros comuns – por que eles ocorrem e o que você pode fazer para resolvê-los.

##O que é o WebHCat?

O [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) é uma API REST para o [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog), uma camada de gerenciamento de armazenamento e tabela para Hadoop. O WebHCat é habilitado por padrão em clusters do HDInsight e é usado por várias ferramentas para enviar trabalhos, obter o status do trabalho etc. sem logon no cluster.

##Modificando a configuração

> [AZURE.IMPORTANT] Vários dos erros listados neste documento ocorrerem porque um máximo configurado foi excedido. Quando a etapa de solução menciona que você pode alterar um valor, use um dos seguintes para realizar a alteração:

* Para clusters do **Windows**: use uma ação de script para configurar o valor durante a criação do cluster. Para obter mais informações, consulte [Desenvolver ações de script](hdinsight-hadoop-script-actions.md).

* Para clusters do **Linux**: use o Ambari (API REST ou Web) para modificar o valor. Para obter mais informações, consulte [Gerenciar o HDInsight usando o Ambari](hdinsight-hadoop-manage-ambari.md)

###Configuração padrão

Estes são os valores de configuração padrão que podem afetar o desempenho do WebHCat ou causar erros se esses valores forem excedidos:

| Configuração | O que faz | Valor padrão |
| ------- | ------------ | ------------- |
| [yarn.scheduler.capacity.maximum-applications][maximum-applications] | O número máximo de trabalhos que podem estar ativos ao mesmo tempo (em execução ou pendentes) | 10\.000 |
| [templeton.exec.max-procs][max-procs] | O número máximo de solicitações que podem ser atendidas simultaneamente | 20 |
| [mapreduce.jobhistory.max-age-ms][max-age-ms] | O número de dias em que o histórico de trabalhos será mantido | 7 dias |

##Número excessivo de solicitações

**Código de status HTTP**: 429

| Causa | Resolução |
| ----- | ---------- |
| Você excedeu o máximo de solicitações simultâneas atendidas pelo WebHCat por minuto (padrão de 20) | Reduza a carga de trabalho para garantir que não sejam enviadas mais do que o número máximo de solicitações simultâneas ou aumente o limite de solicitações simultâneas modificando `templeton.exec.max-procs`. Consulte [Modificando a configuração](#modifying-configuration) para obter mais informações |

##Servidor indisponível

**Código de status HTTP**: 503

| Causa | Resolução |
| ---------------- | ------------------- |
| Isso geralmente ocorre durante o failover entre o HeadNode primário e secundário para o cluster | Aguarde dois minutos e repita a operação |

##Conteúdo de solicitação incorreto: não foi possível encontrar o trabalho

**Código de status HTTP**: 400

| Causa | Resolução |
| ---------------- | ------------------- |
| Os detalhes do trabalho foram apagados pelo limpador de histórico de trabalhos | O período de retenção padrão do histórico de trabalhos é de 7 dias. Isso pode ser alterado modificando `mapreduce.jobhistory.max-age-ms`. Consulte [Modificando a configuração](#modifying-configuration) para obter mais informações |
| O trabalho foi encerrado devido a um failover | Repita o envio do trabalho em até dois minutos |
| Foi usada uma ID de trabalho inválida | Verifique se a ID de trabalho está correta |

##Gateway inválido

**Código de status HTTP**: 502

| Causa | Resolução |
| ---------------- | ------------------- |
| A coleta de lixo interna está ocorrendo no processo do WebHCat | Aguarde até que a coleta de lixo seja concluída ou reinicie o serviço do WebHCat |
| Tempo limite aguardando uma resposta do serviço ResourceManager. Isso pode ocorrer quando o número de aplicativos ativos atinge o máximo configurado (padrão de 10.000) | Aguarde até que os trabalhos em execução no momento sejam concluídos ou aumente o limite de trabalhos simultâneos modificando `yarn.scheduler.capacity.maximum-applications`. Consulte [Modificando a configuração](#modifying-configuration) para obter mais informações |
| Ao tentar recuperar todos os trabalhos por meio da chamada [GET /jobs](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs) quando `Fields` está definido como `*` | Não recupere *todos* os detalhes de trabalho; em vez disso, use `jobid` para recuperar detalhes de trabalhos somente maiores que determinada ID de trabalho. Ou não use `Fields` |
| O serviço do WebHCat está inativo durante o failover do HeadNode | Aguarde dois minutos e repita a operação |
| Há mais de 500 trabalhos pendentes enviados por meio do WebHCat | Aguarde até que os trabalhos pendentes no momento sejam concluídos antes de enviar mais trabalhos |

[maximum-applications]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://hive.apache.org/javadocs/hcat-r0.5.0/configuration.html
[max-age-ms]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml
 

<!---HONumber=AcomDC_0914_2016-->