---
title: "Guia de solução de problemas do Mobile Engagement do Azure - serviço"
description: "Guias de solução de problemas para o Mobile Engagement do Azure"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: dwrede
editor: 
ms.assetid: 8b4275da-c0b4-4690-824a-48e9d7a1fc6e
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 01862d158f4771989ae6eb3b20fe8e080123a231


---
# <a name="troubleshooting-guide-for-service-issues"></a>Guia de solução de problemas de serviço
A seguir, possíveis problemas que podem ser encontrados na execução do Mobile Engagement do Azure.

## <a name="service-outages"></a>Interrupções de serviço
### <a name="issue"></a>Problema
* Problemas que parecem ser provocados por interrupções de serviços do Mobile Engagement do Azure.

### <a name="causes"></a>Causas
* Os problemas que parecem ser provocados por interrupções de serviços do Mobile Engagement do Azure podem ser causados por vários problemas diferentes:
  * Problemas isolados que originalmente parecem sistêmicos para o Mobile Engagement do Azure
  * Problemas conhecidos causados por interrupções de servidor (nem sempre são mostrados no status do servidor):
  * Atrasos de agendamento, erros de direcionamento, problemas de atualização de notificação, interrupção da coleta de estatísticas, envio por push para de funcionar, interrupção do funcionamento das APIs, não é possível criar novos aplicativos ou usuários, erros de DNS e erros de tempo limite na interface do usuário, na API ou em aplicativos em um dispositivo.
  * Interrupções de dependência de nuvem [Status do serviço do Azure](http://status.azure.com/)
  * Interrupções de dependência de Serviços de notificação de envio (PNS)
  * Interrupções de loja de aplicativos

1) Para testar para ver se o problema é sistemático, você pode testar a mesma função de um

* Aplicativo integrado Mobile Engagement do Azure
* Dispositivo de teste
* Versão do SO do dispositivo de teste
* Campanha
* Conta de usuário administrador
* Navegador (IE, Firefox, Chrome, etc.)
* Computador

2) Para testar se o problema afeta somente a interface do usuário ou a API:

* Teste a mesma função da IU do Mobile Engagement do Azure e das APIs do Mobile Engagement do Azure.

3) Para testar se o problema é com a rede de telefone celular:

* Teste tanto quando estiver conectado à Internet via WIFI quanto conectado via rede de celular 3G.
* Confirme se o firewall não está bloqueando algum endereço IP ou porta do Mobile Engagement do Azure.

4) Para testar se o problema é com o dispositivo:

* Teste se o dispositivo é capaz de se conectar ao Mobile Engagement do Azure com outro aplicativo integrado Mobile Engagement do Azure.
* Verifique se você pode gerar eventos, trabalhos e travamentos de seu telefone que podem ser vistos na interface do usuário do Mobile Engagement do Azure 
* Teste se você pode enviar notificações por push da IU do Mobile Engagement do Azure para o seu dispositivo com base em sua identificação do dispositivo. 

5) Para testar se o problema é com seu aplicativo:

* Instale e teste seu aplicativo de um emulador em vez de um dispositivo físico:

6) Para testar se o problema é com as atualizações do sistema operacional para dispositivos do usuário final, que exigem uma atualização do SDK para resolver:

* Teste seu aplicativo em diferentes dispositivos com diferentes versões do sistema operacional.
* Confirme que você está usando a versão mais recente do SDK.

## <a name="connectivity-and-incorrect-information-issues"></a>Conectividade e problemas de informações incorretas
### <a name="issue"></a>Problema
* Problemas de logon na interface do usuário do Mobile Engagement do Azure.
* Erros de conexão com as APIs do Mobile Engagement do Azure.
* Problemas ao carregar marcas de informações de aplicativo por meio da API do dispositivo.
* Problemas de download de logs ou de dados exportados do Mobile Engagement do Azure.
* Informações incorretas mostradas na interface do usuário do Mobile Engagement do Azure.
* Informações incorretas mostradas nos logs do Mobile Engagement do Azure.

### <a name="causes"></a>Causas
* Confirme se sua conta de usuário tem permissões suficientes para executar a tarefa.
* Confirme se o problema não está restrito a um computador ou à sua rede local.
* Confirme se o serviço do Mobile Engagement do Azure não tem nenhuma interrupção relatada.
* Confirme se os arquivos de marca de informações do aplicativo seguem todas estas regras:
  * Usam somente o conjunto de caracteres UTF8 (não há suporte para o conjunto de caracteres ANSI).
  * Usam uma vírgula "," como o caractere separador (você pode abrir uma solicitação de serviço para pedir a alteração do caractere separador do .csv de uma vírgula "," para um outro caractere, como um ponto e vírgula ";").
  * Usam letras maiúsculas para valores boolianos “verdadeiro” e “falso”.
  * Usam um arquivo menor do que o tamanho máximo de 35 MB.




<!--HONumber=Nov16_HO3-->


