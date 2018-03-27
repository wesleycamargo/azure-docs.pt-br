---
title: Usando o Azure AD Connect Health com o AD FS | Microsoft Docs
description: Esta é a página do Azure AD Connect Health sobre como monitorar a sua infraestrutura local do AD FS.
services: active-directory
documentationcenter: ''
author: karavar
manager: mtillman
editor: curtand
ms.assetid: dc0e53d8-403e-462a-9543-164eaa7dd8b3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/18/2017
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4e82b1364593ff70ed87efcaa24c135277002904
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/17/2018
---
# <a name="monitor-ad-fs-using-azure-ad-connect-health"></a>Monitorar o AD FS usando o Azure AD Connect Health
A documentação a seguir é específica para monitorar a sua infraestrutura do AD FS com o Azure AD Connect Health. Para saber mais sobre como monitorar o Azure AD Connect (Sincronização) com o Azure AD Connect Health, confira [Usar o Azure AD Connect Health para Sincronização](active-directory-aadconnect-health-sync.md). Além disso, para obter informações sobre como monitorar os Serviços de Domínio do Active Directory com o Azure AD Connect Health, confira [Usar o Azure AD Connect Health com o AD DS](active-directory-aadconnect-health-adds.md).

## <a name="alerts-for-ad-fs"></a>Alertas do AD FS
A seção de Alertas do Azure AD Connect Health fornece a lista de alertas ativos. Cada alerta inclui informações relevantes, etapas de resolução e links para documentação relacionada.

Clique duas vezes em um alerta ativo ou resolvido para abrir uma nova folha com as informações adicionais, as etapas a serem seguidas para resolver o alerta e os links para documentação relevante. Você também pode exibir dados históricos sobre alertas que foram resolvidas no passado.

![Portal do Azure AD Connect Health](./media/active-directory-aadconnect-health/alert2.png)

## <a name="usage-analytics-for-ad-fs"></a>Análises de Uso do AD FS
A Análise de Uso do Azure AD Connect Health analisa o tráfego de autenticação de servidores de Federação. Você pode clicar duas vezes na caixa de análise de uso para abrir a folha de análise de uso, que mostra várias métricas e agrupamentos.

> [!NOTE]
> Para usar a análise de uso com o AD FS, você deve garantir que a auditoria do AD FS esteja habilitada. Para obter mais informações, consulte [Habilitar a auditoria do AD FS](active-directory-aadconnect-health-agent-install.md#enable-auditing-for-ad-fs).
>
>

![Portal do Azure AD Connect Health](./media/active-directory-aadconnect-health/report1.png)

Para selecionar métricas adicionais, especifique um intervalo de tempo ou, para alterar o agrupamento, clique no gráfico de análise de uso e selecione Editar gráfico. Em seguida, você pode especificar o intervalo de tempo, selecionar uma métrica diferente e alterar o agrupamento. Você pode exibir a distribuição do tráfego de autenticação com base em diferentes “métricas” e agrupar cada métrica usando parâmetros relevantes "agrupar por" descritos na seção a seguir:

**Métrica: Total de solicitações** – Número total de solicitações processadas por servidores AD FS.

|Agrupar Por | O que significa que o agrupamento e por que ele é útil? |
| --- | --- |
| Todos | Mostra a contagem do número total de solicitações processadas por todos os servidores AD FS.|
| Aplicativo | Agrupa o total de solicitações com base na terceira parte confiável de destino. Esse agrupamento é útil para entender qual aplicativo está recebendo o percentual do tráfego total. |
|  Servidor |Agrupa o total de solicitações com base no servidor que processa a solicitação. Esse agrupamento é útil para entender a distribuição de carga do tráfego total.
| Ingresso no local |Agrupa o total de solicitações com base em se as solicitações são provenientes de dispositivos que são ingressados no local (conhecido). Esse agrupamento é útil para compreender se os recursos são acessados usando dispositivos desconhecidos para a infraestrutura de identidades. |
|  Método de autenticação | Agrupa o total de solicitações com base no método de autenticação usado para autenticação. Esse agrupamento é útil para entender o método de autenticação comum que é usado para autenticação. Estes são os métodos de autenticação possíveis <ol> <li>Autenticação Integrada do Windows (Windows)</li> <li>Autenticação Baseada em Formulários (Formulários)</li> <li>SSO (Logon único)</li> <li>Autenticação de Certificado X509 (Certificado)</li> <br>Se os servidores de federação receberem a solicitação com um Cookie SSO, essa solicitação será contabilizada como SSO (logon único). Nesses casos, se o cookie for válido, o usuário não precisa fornecer credenciais e obtém acesso contínuo ao aplicativo. Esse comportamento é comum se você tiver várias partes confiáveis protegidas pelos servidores da Federação. |
| Local de rede | Agrupa o total de solicitações com base no local de rede do usuário. Pode ser qualquer intranet ou extranet. Esse agrupamento é útil para saber qual é a porcentagem do tráfego que está vindo da intranet em comparação com a extranet. |


**Métrica: Total de solicitações com falha** – O número total de solicitações com falha processadas pelo serviço de federação. (Essa métrica só está disponível no AD FS para o Windows Server 2012 R2)

|Agrupar Por | O que significa que o agrupamento e por que ele é útil? |
| --- | --- |
| Tipo de erro | Mostra o número de erros com base nos tipos de erro predefinidos. Esse agrupamento é útil para entender os tipos comuns de erros. <ul><li>Nome de usuário ou senha incorretos: erros causados por nome de usuário ou senha incorretos.</li> <li>"Bloqueio de extranet": falhas devido a solicitações recebidas de um usuário que foi bloqueado da extranet </li><li> "Senha expirada": falhas devido a usuários que fazem logon com uma senha expirada.</li><li>"Conta desabilitada": falhas devido a usuários que fazem logon com uma conta desabilitada.</li><li>"Autenticação do dispositivo": falhas devido a usuários que não foram autenticados usando a Autenticação do Dispositivo.</li><li>"Autenticação de Certificado de Usuário": falhas devido a usuários que não foram autenticados devido a um certificado inválido.</li><li>"MFA": falhas devido ao usuário que não foi autenticado usando a Autenticação Multifator.</li><li>"Outras Credenciais": "Autorização de Emissão": falhas devido a falhas de autorização.</li><li>"Delegação de Emissão": falhas devido a erros de delegação de emissão.</li><li>"Aceitação de Token": falhas causadas pelo ADFS rejeitando o token de um provedor de identidade de terceiros.</li><li>"Protocolo": falha devido a erros de protocolo.</li><li>"Desconhecido": envolve tudo. Quaisquer outras falhas que não se encaixam nas categorias definidas.</li> |
| Servidor | Agrupa os erros com base no servidor. Esse agrupamento é útil para entender a distribuição de erros entre servidores. A distribuição desigual poderia ser um indicador de um servidor em um estado com falha. |
| Local de rede | Agrupa os erros com base no local de rede das solicitações (intranet versus extranet). Esse agrupamento é útil para entender que tipo de solicitações está falhando. |
|  Aplicativo | Agrupa as falhas com base no aplicativo de destino (terceira parte confiável). Esse agrupamento é útil para entender qual aplicativo de destino está tendo maior número de erros. |

**Métrica: Contagem de usuários** – número médio de usuários exclusivos autenticando-se ativamente usando o AD FS

|Agrupar Por | O que significa que o agrupamento e por que ele é útil? |
| --- | --- |
|Todos |Essa métrica fornece uma contagem do número médio de usuários que usam o serviço de federação na fração de tempo selecionada. Os usuários não estão agrupados. <br>A média depende da fração de tempo selecionada. |
| Aplicativo |Agrupa o número médio de usuários com base no aplicativo de destino (terceira parte confiável). Esse agrupamento é útil para entender a quantos usuários estão usando o aplicativo. |

## <a name="performance-monitoring-for-ad-fs"></a>Monitoramento de desempenho do AD FS
O Monitoramento de Desempenho do Azure Active Directory Connect Health fornece informações de monitoramento nas métricas. Selecionar a caixa Monitoramento abre uma nova folha com informações detalhadas sobre as métricas.

![Portal do Azure AD Connect Health](./media/active-directory-aadconnect-health/perf1.png)

Ao selecionar a opção de filtro na parte superior da folha, você pode filtrar por servidor para ver as métricas individuais de um servidor. Para alterar as métricas, clique com o botão direito do mouse no gráfico de monitoramento na folha de monitoramento e selecione Editar Gráfico (ou selecione o botão Editar Gráfico). Na nova folha que é aberta, você pode selecionar métricas adicionais na lista suspensa e especificar um intervalo de tempo para exibir os dados de desempenho.

## <a name="top-50-users-with-failed-usernamepassword-logins"></a>50 principais usuários com logons com falha de nome de usuário/senha
Um dos motivos comuns para a falha de uma solicitação de autenticação em um servidor do AD FS é uma solicitação com credenciais inválidas, ou seja, um nome de usuário ou uma senha incorretos. Costuma acontecer par aos usuários devido a senhas complexas, senhas esquecidas ou erros de digitação.

Mas há outros motivos que podem resultar em um número inesperado de solicitações manipuladas pelos servidores do AD FS, tais como: um aplicativo que armazena em cache as credenciais de usuário e as credenciais expiram ou um usuário mal-intencionado tenta entrar em uma conta com uma série de senhas conhecidas. Estes dois exemplos são motivos válidos que poderiam levar a um aumento nas solicitações.

O Azure AD Connect Health para ADFS fornece um relatório sobre os 50 principais usuários com falhas em tentativas de logon devido a um nome de usuário ou uma senha inválidos. Esse relatório é obtido com o processamento dos eventos de auditoria gerados por todos os servidores do AD FS nos farms.

![Portal do Azure AD Connect Health](./media/active-directory-aadconnect-health-adfs/report1a.png)

Neste relatório, você tem acesso fácil às seguintes informações:

* Número total de solicitações com falha com nome de usuário/senha incorretos nos últimos 30 dias
* Número médio de usuários com falha de logon com um nome de usuário/senha inválidos por dia.

Ao clicar nessa opção, você é levado até a folha de relatório principal, que fornece detalhes adicionais. Esta folha inclui um grafo com informações de tendência para ajudar a estabelecer uma linha de base sobre solicitações com nome de usuário ou senha incorretos. Além disso, ele fornece a lista dos 50 principais usuários com o maior número de tentativas com falha durante a semana passada. A observação dos 50 principais usuários da semana anterior poderia ajudar a identificar picos de senhas inválidas.  

O grafo fornece as seguintes informações:

* O número total de logons com falha devido a um nome de usuário/senha inválidos a cada dia.
* O número total de usuários exclusivos com logons com falha a cada dia.
* Endereço IP do cliente da última solicitação

![Portal do Azure AD Connect Health](./media/active-directory-aadconnect-health-adfs/report3a.png)

O relatório fornece as seguintes informações:

| Item do relatório | DESCRIÇÃO |
| --- | --- |
| Id de Usuário |Mostra a ID de usuário que foi usada. Esse valor é o que o usuário digitou, que, em alguns casos, é o uso da ID de usuário errada. |
| Tentativas com falha |Mostra o número total de tentativas com falha para essa ID de usuário específica. A tabela é classificada com o maior número de tentativas com falha em ordem decrescente. |
| Última falha |Mostra o carimbo de data/hora quando a última falha ocorreu. |
| IP da última falha |Mostra o endereço IP do cliente da solicitação incorreta mais recente. |

> [!NOTE]
> Esse relatório é atualizado automaticamente a cada 12 horas com as novas informações coletadas no momento. Como resultado, as tentativas de logon nas últimas 12 horas podem não ser incluídas no relatório.
>
>

## <a name="risky-ip-report"></a>Relatório IP arriscado 
Os clientes do AD FS podem expor pontos de extremidade de autenticação de senha para a Internet a fim de fornecer serviços de autenticação para os usuários finais acessarem aplicativos SaaS como o Office 365. Nesse caso, é possível que alguém mal-intencionado tente fazer logons em seu sistema de AD FS adivinhando a senha do usuário final e obtendo acesso aos recursos do aplicativo. O AD FS fornece a funcionalidade de bloqueio de conta de extranet para evitar esses tipos de ataque desde a sua versão no Windows Server 2012 R2. Se você estiver usando uma versão inferior, recomendamos fortemente que atualize seu sistema do AD FS para o Windows Server 2016. <br />
Além disso, é possível que um único endereço IP tente vários logons em relação a vários usuários. Nesses casos, o número de tentativas por usuário pode estar abaixo do limite para a proteção de bloqueio de conta no AD FS. O Azure AD Connect Health agora fornece o "Relatório IP arriscado", que detecta essa condição e notifica os administradores quando isso ocorre. Estes são os principais benefícios do relatório: 
- Detecção de endereços IP que excedem um limite de logons com falha com base em senha
- Dá suporte a logons com falha devido a senha incorreta ou a estado de bloqueio de extranet
- Notificação por email para alertar os administradores assim que isso ocorrer com configurações de email personalizáveis
- Configurações de limite personalizáveis que correspondem à política de segurança de uma organização
- Relatórios que podem ser baixados para análise offline e integração com outros sistemas por meio de automação

> [!NOTE]
> Para usar esse relatório, você deve habilitar a auditoria do AD FS. Para obter mais informações, consulte [Habilitar a auditoria do AD FS](active-directory-aadconnect-health-agent-install.md#enable-auditing-for-ad-fs).
>
>

### <a name="what-is-in-the-report"></a>O que o relatório exibe
Cada item no Relatório de IP arriscado mostra informações agregadas sobre atividades de entrada do AD FS com falha que excedem o limite designado. Ele fornece as seguintes informações: ![Portal do Azure AD Connect Health](./media/active-directory-aadconnect-health-adfs/report4a.png)

| Item do relatório | DESCRIÇÃO |
| ------- | ----------- |
| Carimbo de Data/Hora | Mostra o carimbo de data/hora com base na hora local do portal do Azure quando a janela de tempo de detecção é iniciada.<br /> Todos os eventos diários são gerados à meia-noite, horário UTC. <br />Os eventos por hora têm o carimbo de data/hora arredondado para o início da hora. Você pode encontrar a hora de início da primeira atividade de "firstAuditTimestamp" no arquivo exportado. |
| Tipo de gatilho | Mostra o tipo de janela de tempo de detecção. Os tipos de gatilho de agregação são por hora ou por dia. Isso é útil para detectar um ataque de força bruta de alta frequência versus um ataque lento, em que o número de tentativas é distribuído ao longo do dia. |
| Endereço IP | O único endereço IP arriscado que tinha senha incorreta ou atividades de entrada do bloqueio de extranet. Isso pode ser um endereço IPv4 ou IPv6. |
| Contagem de erro de senha inválida | Ocorreu a contagem de erro de senha incorreta no endereço IP durante a janela de tempo de detecção. Os erros de senha incorreta podem ocorrer várias vezes para determinados usuários. Observe que isso não inclui tentativas com falha devido a senhas expiradas. |
| Contagem de erro de bloqueio de extranet | Ocorreu a contagem de erro de bloqueio de extranet no endereço IP durante a janela de tempo de detecção. Os erros de bloqueio de Extranet podem ocorrer várias vezes para determinados usuários. Isso só será visto se o Bloqueio de Extranet for configurado no AD FS (versões 2012R2 ou superior). <b>Observação</b> Recomendamos ativar esse recurso se você permite logons extranet com senhas. |
| Usuários exclusivos tentados | Contagem de tentativas de contas de usuário exclusivas no endereço IP durante a janela de tempo de detecção. Isso fornece um mecanismo para diferenciar um padrão de ataque de usuário único versus um padrão de ataque de multiusuário.  |

Por exemplo, o item de relatório abaixo indica, da janela de 18h às 19h em 28/2/2018, que o endereço IP <i>104.2XX.2XX.9</i> não teve erros de senha incorreta e 284 erros de bloqueio de extranet. 14 usuários exclusivos foram afetados dentro dos critérios. O evento de atividade excedeu o limite horário definido para o relatório. 

![Portal do Azure AD Connect Health](./media/active-directory-aadconnect-health-adfs/report4b.png)

> [!NOTE]
> - Apenas atividades ultrapassando o limite designado serão exibidas na lista de relatórios. 
> - Esse relatório pode controlar um histórico de 30 dias.
> - Esse relatório de alerta não mostra endereços IP privado ou endereços IP do Exchange. Eles ainda são incluídos na lista de exportação. 
>


![Portal do Azure AD Connect Health](./media/active-directory-aadconnect-health-adfs/report4c.png)

### <a name="download-risky-ip-report"></a>Baixar o relatório de IP arriscado
Usando a função **Baixar**, a lista de endereços IP arriscados inteira nos últimos 30 dias pode ser exportada do Portal do Connect Health O resultado de exportação incluirá todas as atividades de entrada do AD FS com falha em cada janela de tempo de detecção, para que você possa personalizar a filtragem após a exportação. Além de agregações realçadas no portal, o resultado da exportação também mostra mais detalhes sobre as atividades de entrada com falha por endereço IP:

|  Item do relatório  |  DESCRIÇÃO  | 
| ------- | ----------- | 
| firstAuditTimestamp | Mostra o primeiro carimbo de data/hora de quando as atividades com falha começaram durante a janela de tempo de detecção.  | 
| lastAuditTimestamp | Mostra o último carimbo de data/hora de quando as atividades com falha terminaram durante a janela de tempo de detecção.  | 
| attemptCountThresholdIsExceeded | O sinalizador que indica se as atividades atuais estão excedendo o limite de alerta.  | 
| isWhitelistedIpAddress | O sinalizador que indica se o endereço IP foi filtrado de alertas e relatórios. Endereços IP privados (<i>10.x.x.x, 172.x.x.x & 192.168.x.x</i>) e endereços IP do Exchange são filtrados e marcados como Verdadeiros. Se você está vendo intervalos de endereços IP privados, é muito provável que o balanceador de carga externo não esteja enviando o endereço IP do cliente ao passar a solicitação para o servidor proxy do aplicativo Web.  | 

### <a name="configure-notification-settings"></a>Definir configurações de notificação
Os contatos de Admin do relatório podem ser atualizados por meio das **Configurações de Notificação**. Por padrão, a notificação de email de alerta de IP arriscado está desativada. Você pode habilitar a notificação alternando o botão em "Obter notificações por email para endereços IP que excederem o limite de atividades com falha do relatório" Como as configurações de notificação de alerta genérico no Connect Health, ele permite que você personalize a lista de destinatários designados para a notificação sobre o relatório de IPs arriscados aqui. Você também pode notificar todos os administradores globais ao fazer a alteração. 

### <a name="configure-threshold-settings"></a>Definir configurações de limite
O limite de alerta pode ser atualizado com as Configurações de Limite. Para começar, o sistema tem um limite definido por padrão. Há quatro categorias nas configurações de limite do relatório de IP arriscado:

![Portal do Azure AD Connect Health](./media/active-directory-aadconnect-health-adfs/report4d.png)

| Item de limite | DESCRIÇÃO |
| --- | --- |
| (U/P má + Bloqueio de Extranet) / Dia  | Configuração de limite para relatar a atividade e disparar a notificação de alerta quando a contagem de senha incorreta mais a contagem de bloqueio de extranet o exceder, por **dia**. |
| (U/P má + Bloqueio de Extranet) / Hora | Configuração de limite para relatar a atividade e disparar a notificação de alerta quando a contagem de senha incorreta mais a contagem de bloqueio de extranet o exceder, por **hora**. |
| Bloqueio de Extranet / Dia | Configuração de limite para relatar a atividade e disparar a notificação de alerta quando a contagem de bloqueio de extranet o exceder, por **dia**. |
| Bloqueio de Extranet / Hora| Configuração de limite para relatar a atividade e disparar a notificação de alerta quando a contagem de bloqueio de extranet o exceder, por **hora**. |

> [!NOTE]
> - A alteração do limite do relatório será aplicada após uma hora a contar da alteração da configuração. 
> - Os itens relatados existentes não serão afetados pela alteração de limite. 
> - Recomendamos que você analise o número de eventos vistos em seu ambiente e ajuste o limite adequadamente. 
>
>

### <a name="faq"></a>Perguntas frequentes
1. Por que estou vendo intervalos de endereços IP privados no relatório?  <br />
Endereços IP privados (<i>10.x.x.x, 172.x.x.x & 192.168.x.x</i>) e endereços IP do Exchange são filtrados e marcados como Verdadeiros na lista de permissões IP. Se você está vendo intervalos de endereços IP privados, é muito provável que o balanceador de carga externo não esteja enviando o endereço IP do cliente ao passar a solicitação para o servidor proxy do aplicativo Web.

2. O que fazer para bloquear o endereço IP?  <br />
Você deve adicionar o endereço IP mal-intencionado ao firewall ou bloqueá-lo no Exchange.   <br />
Para o AD FS 2016 + 1803.C+ QFE, você pode bloquear o endereço IP diretamente no AD FS. 

3. Por que não vejo todos os itens no relatório? <br />
   - Atividades de entrada com falha não excedem as configurações de limite. 
   - Verifique se nenhum alerta “Serviço Health desatualizado” está ativo na sua lista de servidores AD FS.  Leia mais sobre [como solucionar esse alerta](active-directory-aadconnect-health-data-freshness.md).
   - As auditorias não estão habilitadas em farms de servidores do AD FS.


## <a name="related-links"></a>Links relacionados
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Instalação do Agente do Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Operações de Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Usando o Azure AD Connect Health para sincronização](active-directory-aadconnect-health-sync.md)
* [Usar o Azure AD Connect Health com o AD DS](active-directory-aadconnect-health-adds.md)
* [Perguntas frequentes do Azure AD Connect Health](active-directory-aadconnect-health-faq.md)
* [Histórico de versão do Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)
