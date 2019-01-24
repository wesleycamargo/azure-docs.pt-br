---
title: Perguntas frequentes sobre os relatórios do Azure Active Directory | Microsoft Docs
description: Perguntas frequentes sobre os relatórios do Azure Active Directory.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: d6062720e3b747966c60426a5db76e6dd18109a7
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54813009"
---
# <a name="frequently-asked-questions-around-azure-active-directory-reports"></a>Perguntas frequentes sobre os relatórios do Azure Active Directory

Este artigo inclui respostas a perguntas frequentes sobre os relatórios do Azure AD (Azure Active Directory). Para saber mais, consulte [Relatórios do Azure Active Directory](overview-reports.md). 

## <a name="getting-started"></a>Introdução 

**P: Atualmente, eu uso as APIs de ponto de extremidade https://graph.windows.net/&lt;tenant-name&gt;/reports/ para efetuar pull programaticamente de relatórios de uso do aplicativo integrado e auditoria do Azure AD em nossos sistemas de relatórios. Para qual devo mudar?**

**R:** Consulte a [Referência de API](https://developer.microsoft.com/graph/) para saber como é possível [usar as APIs para acessar relatórios de atividades](concept-reporting-api.md). Esse ponto de extremidade tem dois relatórios (**Auditoria** e **Entradas**) que fornecem todos os dados que você obteve no ponto de extremidade de API antigo. Esse novo ponto de extremidade também tem um relatório de entradas com a licença do Azure AD Premium que você pode usar para obter informações de uso de aplicativo uso de dispositivo e conexão do usuário.

--- 

**P: Atualmente, eu uso as APIs de ponto de extremidade https://graph.windows.net/&lt;tenant-name&gt;/reports/ para efetuar pull programaticamente de relatórios de segurança do Azure AD (tipos específicos de detecções como credenciais vazadas ou entradas de endereços IP anônimos) em nossos sistemas de relatórios. Para qual devo mudar?**

**R:** Você pode usar a [API de eventos de risco do Identity Protection](../identity-protection/graph-get-started.md) para acessar detecções de segurança através do Microsoft Graph. Esse novo formato oferece maior flexibilidade ao modo de consultar os dados, com filtragem avançada, seleção de campo e mais, além de padronizar eventos de risco em um tipo para integração mais fácil ao SIEMs e outras ferramentas de coleta de dados. Uma vez que os dados estão em um formato diferente, você não pode substituir uma nova consulta para suas consultas antigas. No entanto, [a nova API usa o Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent), que é o padrão da Microsoft para essas APIs, como O365 ou Azure AD. Para que o trabalho necessário possa estender seus investimentos atuais no MS Graph ou ajudá-lo a começar a fazer a transição para essa nova plataforma padrão.

--- 

**P: Como fazer para obter uma licença Premium?**

**R:** Consulte [Introdução ao Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) para fazer upgrade da edição do Azure Active Directory.

---

**P: Em quanto tempo devo ver os dados das atividades após obter uma licença Premium?**

**R:** Se você já tiver dados de atividades como uma licença gratuita, poderá vê-los imediatamente. Se você não tiver dados, levará um ou dois dias para os dados serem exibidos nos relatórios.

---

**P: Eu poderei visualizar os dados do último mês, após obter uma licença Premium do Azure AD?**

**R:** Se você mudou recentemente para uma versão Premium (incluindo uma versão de avaliação), você poderá inicialmente ver dados de até 7 dias. Quando os dados forem acumulados, será possível visualizar os dados dos últimos 30 dias.

---

**P: Eu preciso ser um administrador global para ver as entradas da atividade no portal do Azure ou obter dados por meio da API?**

**R:** Não, você também poderá acessar os dados do relatório por meio do portal ou por meio da API se for um **Leitor de Segurança** ou **Administrador de Segurança** do locatário. É claro que os  **Administradores Globais** também terão acesso a esses dados.

---


## <a name="activity-logs"></a>Logs de atividade


**P: Qual é a retenção de dados para logs de atividades (Auditoria e Entradas) no portal do Azure?** 

**R:** A tabela a seguir lista o período de retenção de dados para logs de atividades. Para obter mais informações, consulte as [políticas de retenção de dados para relatórios do Azure AD](reference-reports-data-retention.md).

| Relatório                 | AD do Azure Gratuito | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Logs de auditoria             | 7 dias        | 30 dias             | 30 dias             |
| Entradas               | N/D           | 30 dias             | 30 dias             |
| Uso do MFA do Azure        | 30 dias       | 30 dias             | 30 dias             |

--- 

**P: Quanto tempo demora até que eu possa ver os dados da atividade, após concluir minha tarefa?**

**R:** Os logs de auditoria têm uma latência que varia de 15 minutos a uma hora. Logs de atividade de conexão podem levar de 15 minutos a até 2 horas para alguns registros.

---

**P: Posso obter informações do log de atividades do Office 365 por meio do portal do Azure?**

**R:** Embora os logs de atividades do Office 365 e de atividades do Azure AD compartilhem muitos dos recursos de diretório, se você quiser uma exibição completa dos logs de atividades do Office 365, vá para o Centro de Administração do Office 365 para obter informações de log de atividades do Office 365.

---

**P: Quais APIs devo usar para obter informações sobre logs de atividades do Office 365?**

**R:** Use as [APIs de Gerenciamento do Office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview) para acessar os logs de atividade do Office 365 por meio de uma API.

---

**P: Quantos registros posso baixar do portal do Azure?**

**R:** É possível baixar até 5000 registros do portal do Azure. Os registros são classificados por *mais recentes* e, por padrão, você obterá 5000 registros mais recentes.

---

## <a name="risky-sign-ins"></a>Entradas de risco

**P: Há um evento de risco no Identity Protection, mas não vejo a entrada correspondente no relatório de entradas. Isso é esperado?**

**R:** Sim, o Identity Protection avalia o risco de todos os fluxos de autenticação, sejam eles interativos ou não. No entanto, o relatório de todas as entradas mostra apenas as entradas interativas.

---

**P: Como posso saber o motivo pelo qual uma entrada ou um usuário foi sinalizado como arriscado no portal do Azure?**

**R:** Se você tiver uma assinatura do **Azure AD Premium**, poderá saber mais sobre os eventos de risco subjacentes, selecionando o usuário em **Usuários sinalizados para risco** ou selecionando um registro no relatório **Entradas arriscadas**. Se você tiver uma assinatura **Gratuita** ou **Básica**, então, poderá exibir os usuários em risco e os relatórios de entradas arriscadas, mas não poderá ver as informações do evento de risco subjacente.

---

**P: Como os endereços IP são calculados no relatório de entradas e entradas arriscadas?**

**R:** Os endereços IP são emitidos de uma forma que não há nenhuma conexão definitiva entre um endereço IP e em que o computador com esse endereço está localizado fisicamente. O mapeamento de endereços IP é ainda mais complicado por fatores como provedores móveis e VPNs que emitem endereços IP de pools centrais, geralmente muito longe de onde o dispositivo cliente é realmente usado. Atualmente, nos relatórios do Azure AD, a conversão do endereço IP em um local físico é um melhor esforço com base em rastreamentos, dados de registro, pesquisas inversas e outras informações. 

---

**P: O que significa o evento de risco "Entrar com risco adicional detectado"?**

**R:** Para fornecer insights sobre todos as entradas arriscadas no ambiente, "Entrar com risco adicional detectado" funciona como espaço reservado para entradas de detecções exclusivas dos assinantes do Azure AD Identity Protection.

---

## <a name="conditional-access"></a>Acesso condicional

**P: Quais são as novidades com esse recurso?**

**R:** Os clientes agora podem solucionar problemas de políticas de acesso condicional por meio de todo o relatório de entradas. Os clientes podem rever o status de acesso condicional e analisar os detalhes das políticas aplicadas ao login e o resultado de cada política.

**P: Como fazer para começar?**

**R:** Introdução:
    * Navegue até o relatório de logins no [Portal do Azure](https://portal.azure.com). 
    * Clique no login que você deseja solucionar.
    * Navegue até a guia **Acesso condicional**. Aqui, você pode exibir todas as políticas que afetados na entrada e o resultado para cada política. 
    
**P: Quais são todos os valores possíveis para o status de acesso condicional?**

**R:** O status de acesso condicional pode ter os valores a seguir:
    * **Não aplicado**: Isso significa que não havia uma política de Autoridade de Certificação com o usuário e o aplicativo no escopo. 
    * **Êxito**: Isso significa que havia uma política de Autoridade de Certificação com o usuário e o aplicativo no escopo e as políticas de Autoridade de Certificação foram atendidas com êxito. 
    * **Falha**: Isso significa que havia uma política de Autoridade de Certificação com o usuário e o aplicativo no escopo e as políticas de Autoridade de Certificação não foram atendidas. 
    
**P: Quais são todos os valores possíveis para o resultado da política de acesso condicional?**

**R:** Uma política de acesso condicional pode ter os seguintes resultados:
    * **Êxito**: A política foi atendida com êxito.
    * **Falha**: A política não foi atendida.
    * **Não aplicado**: Isso pode acontecer porque as condições da política não foram atendidas.
    * **Não habilitado**: Isso ocorre devido à política em estado desabilitado. 
    
**P: O nome da política no relatório de entradas completo não corresponde ao nome da política na Autoridade de Certificação. Por quê?**

**R:** O nome da política no relatório de entrada todo é baseado no nome da política de Autoridade de Certificação no momento da entrada. Isso pode ser inconsistente com o nome da política na autoridade de certificação, se você tiver atualizado o nome da política mais tarde, ou seja, após o logon.

**P: Minha entrada foi bloqueada devido a uma política de acesso condicional, mas o relatório de atividades de entrada mostra que a entrada houve êxito ao entrar. Por quê?**

**R:** Atualmente, o relatório de entrada pode não mostrar resultados precisos para cenários do Exchange ActiveSync quando o acesso condicional é aplicado. Pode haver casos em que o resultado do login no relatório mostra um login bem-sucedido, mas o login realmente falhou devido a uma política de acesso condicional. 
