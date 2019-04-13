---
title: Como investigar usuários arriscados no Azure Active Directory Identity Protection (atualizado) | Microsoft Docs
description: Saiba como investigar usuários arriscados no Azure Active Directory Identity Protection (atualizado).
services: active-directory
keywords: azure active directory identity protection, cloud app discovery, gerenciamento de aplicativos, segurança, risco, nível de risco, vulnerabilidade, política de segurança
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.author: joflore
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.date: 01/25/2019
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 646119f223067fac5ee36574fb10273819251cce
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2019
ms.locfileid: "59547049"
---
# <a name="how-to-investigate-risky-users-and-sign-ins"></a>Como: Investigar entradas e usuários arriscados 


Usando os relatórios de entradas arriscadas e usuários arriscados, você pode investigar e obter insights sobre riscos em seu ambiente. Com a capacidade de filtrar e classificar as entradas e usuários arriscados, você pode entender melhor possíveis invasões em sua organização. 


## <a name="risky-users-report"></a>Relatório de usuários arriscados

Com as informações fornecidas pelo relatório de usuários arriscados, você pode encontrar respostas a perguntas como:

- Quais usuários são de risco alto?
- Quais usuários têm um estado de risco "remediado"?



O primeiro ponto de entrada para este relatório é a seção **Investigar** na página de segurança.

![Relatório de usuários arriscados](./media/howto-investigate-risky-users-signins/01.png)


O relatório de usuários arriscados tem uma exibição padrão que mostra:

- NOME

- Estado do risco

- Nível de risco

- Detalhe do risco

- Última atualização do risco

- Type

- Status
 

![Relatório de usuários arriscados](./media/howto-investigate-risky-users-signins/03.png)


Você pode personalizar o modo de exibição de lista clicando em **Colunas** na barra de ferramentas.

![Relatório de usuários arriscados](./media/howto-investigate-risky-users-signins/04.png)

Isso permite a você exibir campos adicionais ou remover campos que já estão exibidos.

Ao clicar em um item na exibição de lista, você obterá mais detalhes sobre ele em uma exibição horizontal.

![Relatório de usuários arriscados](./media/howto-investigate-risky-users-signins/05.png)


A exibição de detalhes mostra:

- Informações básicas

- Entradas arriscadas recentes

- Eventos de risco não vinculados a uma entrada

- Histórico de risco



Além disso, você pode:

![Relatório de usuários arriscados](./media/howto-investigate-risky-users-signins/08.png)

- Atalho Exibir todas as entradas para exibir o relatório de entradas para o usuário em questão.

- Exibir todas as entradas arriscadas para exibir todas as entradas para o usuário em questão que foram marcadas como arriscadas.

- Redefinir a senha de um usuário se acreditar que a identidade do usuário foi comprometida.

- Ignorar o risco do usuário se achar que os eventos de risco ativos de um usuário são falsos positivos. Para obter mais informações, confira [como melhorar a precisão da detecção](howto-improve-detection-accuracy.md).



### <a name="filter-risky-users"></a>Filtrar usuários arriscados

Para restringir os dados relatados a um nível que funciona para você, filtre os dados de usuários arriscados usando os seguintes campos padrão:

- NOME

- Nome de Usuário

- Estado do risco

- Nível de risco

- Type

- Status

![Relatório de usuários arriscados](./media/howto-investigate-risky-users-signins/06.png)



O filtro **Nome** permite que você especifique o nome ou o UPN (nome UPN) do usuário desejado.


O filtro **Riscos Detectados** permite que você selecione:

- Em risco
- Remediado
- Ignorado


O filtro **Nível de risco** permite que você selecione:

- Alto
- Média
- Baixo


O filtro **Tipo** permite que você selecione:

- Convidado
- Membro

O filtro **Status** permite que você selecione:

- Deleted
- Ativo


### <a name="download-risky-users-data"></a>Baixar dados de usuários arriscados

Se você quiser trabalhar com eles fora do portal do Azure, você pode baixar os dados de usuários arriscados. Clicando em Download cria um arquivo CSV dos registros de 2.500 mais recentes. 

![Relatório de usuários arriscados](./media/howto-investigate-risky-users-signins/07.png)


Você pode personalizar o modo de exibição de lista clicando em Colunas na barra de ferramentas.
 
Isso permite a você exibir campos adicionais ou remover campos que já estão exibidos.
 
Para saber mais sobre um usuário arriscado, clique na gaveta de Detalhes para expandi-lo

 



## <a name="risky-sign-ins-report"></a>Relatório de entradas arriscadas

Com as informações fornecidas pelo relatório de entradas arriscadas, você pode encontrar respostas a perguntas como:

- Na última semana, qual o número de entradas bem-sucedidas que tinham eventos de risco com endereço IP anônimo?

- No último mês, quais usuários foram confirmados como comprometidos?

- Quais usuários tinham entradas arriscadas no portal do Office 365?




O primeiro ponto de entrada para este relatório é a seção **Investigar** na página de segurança.

![Relatório de entradas arriscadas](./media/howto-investigate-risky-users-signins/02.png)

O relatório de entradas arriscadas tem uma exibição padrão que mostra:

- Data

- Usuário

- Aplicativo

- Status de entrada

- Estado do risco

- Nível de risco (agregação)

- Nível de risco (tempo real)

- Acesso condicional

- MFA necessário  
 

![Relatório de entradas arriscadas](./media/howto-investigate-risky-users-signins/09.png)


Você pode personalizar o modo de exibição de lista clicando em **Colunas** na barra de ferramentas.

![Relatório de usuários arriscados](./media/howto-investigate-risky-users-signins/11.png)

Isso permite a você exibir campos adicionais ou remover campos que já estão exibidos.

Ao clicar em um item na exibição de lista, você obterá mais detalhes sobre ele em uma exibição horizontal.

![Relatório de usuários arriscados](./media/howto-investigate-risky-users-signins/12.png)


A exibição de detalhes mostra:

- Informações básicas

- Informações do dispositivo

- Informações de risco

- Informações de MFA

- Acesso condicional





Além disso, você pode:

![Relatório de usuários arriscados](./media/howto-investigate-risky-users-signins/13.png)

- Confirmar comprometido 

- Confirmar seguro

Para obter mais informações, confira [como melhorar a precisão da detecção](howto-improve-detection-accuracy.md).




### <a name="filter-risky-sign-ins"></a>Filtrar entradas arriscadas

Para restringir os dados relatados a um nível que funciona para você, filtre os dados de usuários arriscados usando os seguintes campos padrão:

- Usuário
- Aplicativo
- Status de entrada
- Estado do risco
- Nível de risco (agregação)
- Nível de risco (tempo real)
- Acesso condicional
- Data
- Tipo de nível de risco

![Relatório de entradas arriscadas](./media/howto-investigate-risky-users-signins/14.png)



O filtro **Nome** permite que você especifique o nome ou o UPN (nome UPN) do usuário desejado.

O filtro **Aplicativo** permite que você especifique o aplicativo de nuvem que o usuário tentou acessar.

O filtro **status de entrada** permite que você selecione:

- Todos
- Sucesso
- Failure


O filtro **Riscos Detectados** permite que você selecione:

- Em risco
- Comprometido confirmado
- Seguro confirmado
- Ignorado
- Remediado


O filtro **Nível de risco (agregação)** permite que você selecione:

- Alto
- Média
- Baixo

O filtro **Nível de risco (tempo real)** permite que você selecione:

- Alto
- Média
- Baixo


O filtro **Acesso condicional** permite que você selecione:

- Todos
- Não aplicado
- Sucesso
- Failure


O filtro **Data** permite definir um período de tempo para os dados retornados.
Os valores possíveis são:

- Último mês
- Últimos 7 dias
- Últimas 24 horas
- Intervalo de tempo personalizado





### <a name="download-risky-sign-ins-data"></a>Baixar dados de entradas arriscadas

Se você quiser trabalhar com eles fora do portal do Azure, você pode baixar os dados de entradas de risco. Clicando em Download cria um arquivo CSV dos registros de 2.500 mais recentes. 

![Relatório de usuários arriscados](./media/howto-investigate-risky-users-signins/15.png)



## <a name="next-steps"></a>Próximas etapas

Para obter uma visão geral do Azure AD Identity Protection, veja a [Visão geral do Azure AD Identity Protection](overview-v2.md).
