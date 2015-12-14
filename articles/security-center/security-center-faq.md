<properties
   pageTitle="Perguntas frequentes (FAQ) sobre a Central de segurança do Azure | Microsoft Azure"
   description="Encontre respostas para perguntas frequentes sobre a Central de Segurança do Azure."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/02/2015"
   ms.author="terrylan"/>

# Perguntas frequentes (FAQ) sobre a Central de segurança do Azure

Esta FAQ responde às perguntas sobre a Central de Segurança do Azure, um serviço que ajuda você a impedir, detectar e responder a ameaças com maior visibilidade e controle sobre a segurança dos recursos do Microsoft Azure.

> [AZURE.NOTE]As informações neste documento se aplicam à versão de visualização da Central de Segurança do Azure.

## Perguntas gerais

### O que é a Central de segurança do Azure?
A Central de Segurança do Azure ajuda você a impedir, detectar e responder a ameaças com maior visibilidade e controle sobre a segurança dos recursos do Azure. Ela permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com uma enorme variedade de soluções de segurança.

### Como posso obter a Central de segurança do Azure?
A Central de segurança do Azure é habilitada com sua assinatura do Microsoft Azure e acessada do [portal de visualização do Microsoft Azure](http://azure.microsoft.com/features/azure-portal/). ([Conecte-se ao Portal de Visualização do Azure](https://ms.portal.azure.com/), selecione **Navegar** e role até a **Central de segurança**). Você pode ver algumas recomendações de segurança no painel imediatamente. Isso ocorre porque o serviço pode avaliar o estado de segurança de alguns controles com base em sua configuração no Azure. Para ativar o conjunto completo de monitoramento, recomendações e recursos de alerta de segurança, você precisará [habilitar a coleta de dados](#data-collection).

## Cobrança

### Como funciona o faturamento para a Central de segurança do Azure?
Consulte os [preços da Central de segurança do Azure](https://azure.microsoft.com/pricing/) para obter informações.

## Coleta de dados

### Como posso habilitar a coleta de dados?<a name=data-collection></a>
Você pode habilitar a coleta de dados para suas assinaturas do Azure na política de segurança. Para habilitar a coleta de dados, [conecte-se ao Portal de Visualização do Azure](https://ms.portal.azure.com/), selecione **Navegar**, **Central de segurança** e, então, **Política de segurança**. Defina a **coleta de dados** como **Ativada** e configure as contas de armazenamento para as quais você deseja que os dados sejam coletados para (veja a pergunta “[Onde meus dados são armazenados?](#where-is-my-data-stored)”). Quando a **Coleta de dados** estiver habilitada, ela coletará automaticamente as informações de configuração e os eventos de segurança de todas as máquinas virtuais com suporte na assinatura.

### O que acontece quando eu habilito a coleta de dados?
A coleta de dados é habilitada por meio do agente de monitoramento do Azure e da extensão de monitoramento de segurança do Azure. A extensão de monitoramento de segurança do Azure verifica várias configurações de segurança e eventos relevantes nos vestígios do [Rastreamento de Eventos para Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW). Além disso, o sistema operacional está gerando registros de eventos em qualquer atividade. O agente de monitoramento do Azure lê as entradas do registro de eventos e os vestígios de ETW e os copia para sua conta de armazenamento para análise. Essa é a conta de armazenamento configurada na política de segurança. Para mais informações sobre a conta de armazenamento, consulte a pergunta “[Onde meus dados são armazenados?](#where-is-my-data-stored)”

### A extensão do Agente de monitoramento ou o Monitoramento de segurança afetam o desempenho do meu servidor?
O agente e a extensão consomem uma quantidade nominal de recursos do sistema e devem causar pouco impacto sobre o desempenho.

### Como faço para reverter se eu não quiser mais que a coleta de dados permaneça habilitada?
Você pode desabilitar a **coleta de dados** para uma assinatura na Política de segurança. ([Conecte-se ao Portal de Visualização do Azure](https://ms.portal.azure.com/), selecione **Navegar**, **Central de segurança** e, então, **Política de segurança**.) Ao clicar em uma assinatura, uma nova folha será aberta e fornecerá a opção para desativar a coleta de dados. Selecione a opção **Excluir agentes** na faixa de opções para remover os agentes das máquinas virtuais existentes.

### Onde meus dados são armazenados?<a name=where-is-my-data-stored></a>
Para cada região em que você tiver máquinas virtuais em execução, você deverá escolher a conta de armazenamento na qual os dados coletados dessas máquinas virtuais serão armazenados. Isso facilita para manter os dados na mesma área geográfica para fins de privacidade e soberania de dados. Você pode escolher a conta de armazenamento para uma assinatura na Política de segurança. ([Conecte-se ao Portal de Visualização do Azure](https://ms.portal.azure.com/), selecione **Navegar**, **Central de segurança** e, então, **Política de segurança**.) Quando você clica em uma assinatura, uma nova folha será aberta. Clique em **Escolher contas de armazenamento** para selecionar uma região. Os dados coletados são isolados logicamente dos dados de outros clientes por motivos de segurança.

Para saber mais sobre as contas de armazenamento e o armazenamento do Azure, consulte a [documentação de armazenamento](https://azure.microsoft.com/documentation/services/storage/) e a seção [Sobre as contas de armazenamento do Azure](storage-create-storage-account.md).

## Como usar a Central de Segurança do Azure

### O que é uma política de segurança?
Uma política de segurança define o conjunto de controles que são recomendados para os recursos na assinatura especificada. Na Central de segurança do Azure, você pode definir as políticas para as assinaturas do Azure de acordo com os requisitos de segurança de sua empresa e os tipos de aplicativos ou a confidencialidade dos dados de cada assinatura.

Por exemplo, os recursos usados para desenvolvimento ou teste podem ter requisitos de segurança diferentes daqueles usados para aplicativos de produção. Da mesma forma, os aplicativos com dados regulamentados, como PII (informações de identificação pessoal), podem exigir um nível mais alto de segurança. As políticas de segurança habilitadas na Central de segurança do Azure determinarão as recomendações de segurança e monitoramento. Para saber mais sobre as políticas de segurança, consulte a seção [Monitoramento de integridade de segurança na Central de segurança do Azure](security-center-monitoring.md).

### Quem pode modificar uma política de segurança?
As políticas de segurança são configuradas para cada assinatura. Para modificar uma política de segurança, você deve ser um proprietário ou colaborador daquela assinatura.

Para saber como configurar uma política de segurança, consulte a seção [Como configurar de políticas de segurança na Central de segurança do Azure](security-center-policies.md).

### O que é uma recomendação de segurança?
A Central de Segurança do Azure analisa o estado de segurança de seus recursos do Azure. Quando possíveis vulnerabilidades de segurança são identificadas, as recomendações são criadas. As recomendações direcionam você pelo processo de configuração do controle necessário. Os exemplos abrangem:

- Provisionamento de antimalware para ajudar a identificar e remover software mal-intencionado
- Como configurar [Grupos de segurança de rede](virtual-networks-nsg.md) e as regras para controlar o tráfego para máquinas virtuais
- Provisionamento de um Firewall do Aplicativo Web para ajudar a proteger contra ataques direcionados aos seus aplicativos Web
- Como implantar atualizações de sistema ausentes
- Endereçamento de configurações do sistema operacional que não coincidem com as linhas de base recomendadas

Somente as recomendações que são habilitadas nas Políticas de segurança são mostradas aqui.

### Como é possível ver o estado atual da segurança dos meus recursos do Azure?
Um bloco **integridade de recursos** da folha da **Central de segurança** mostra a postura de segurança geral do seu ambiente dividido por máquinas virtuais, aplicativos web e outros recursos. Cada recurso tem um indicador mostrando se possíveis vulnerabilidades de segurança foram identificadas. Clicar no bloco de integridade de recursos exibe os recursos e identifica onde a atenção é requerida ou talvez haja problemas.

### O que dispara um alerta de segurança?
A Central de segurança do Azure automaticamente coleta, analisa e funde os dados de registro de seus recursos do Azure, da rede e das soluções de parceiros como antimalware e firewalls. Quando forem detectadas ameaças, é criado um alerta de segurança. Os exemplos abrangem a detecção de:

- As máquinas virtuais comprometidas se comunicam com os endereços IP mal-intencionados conhecidos
- Malware avançado detectado com o relatório de erros do Windows
- Ataques por força bruta contra máquinas virtuais
- Alertas de segurança das soluções de segurança de parceiro integradas, como antimalware ou Firewalls de aplicativo Web

### Como as permissões são tratadas na Central de segurança do Azure?
A Central de segurança do Azure oferece suporte ao acesso baseado em função. Para saber mais sobre o controle de acesso baseado em função (RBAC) no Azure, consulte o [Controle de acesso baseado em função do Active Directory do Azure](role-based-access-control-configure.md).

Quando um usuário abre a Central de segurança do Azure, somente as recomendações e alertas relacionados aos recursos que o usuário tem acesso aparecerão. Isso significa que os usuários verão apenas os itens relacionados a recursos nos quais o usuário recebe a função de leitor, colaborador ou proprietário para a assinatura ou grupo de recursos que o recurso pertence.

Para editar uma política de segurança, você deve ser um proprietário ou colaborador da assinatura.

### Quais tipos de máquinas virtuais terão suporte?
Ambas as máquinas virtuais [Gerenciador de Recursos e Clássica](azure-classic-rm.md) terão suporte, inclusive as máquinas virtuais que fazem parte dos clusters Service Fabric do Azure.

As recomendações da lista de controle de acesso no momento se aplicam às máquinas virtuais Classic. Os grupos de segurança de rede e recomendações para a instalação do Firewall do aplicativo Web no momento se aplicam somente às máquinas virtuais Gerenciador de Recursos.

### Há suporte para máquinas virtuais Linux?
A Central de segurança do Azure oferece monitoramento para as máquinas virtuais do Linux (Ubuntu) da linha de base. No futuro, o monitoramento de integridade de segurança adicional e coleta/análise de dados estarão disponíveis, bem como o suporte adicional para distribuições de Linux.

<!---HONumber=AcomDC_1203_2015-->