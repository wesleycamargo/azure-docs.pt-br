<properties
   pageTitle="Perguntas frequentes sobre a Central de Segurança do Azure | Microsoft Azure"
   description="Encontre respostas para perguntas frequentes sobre a Central de Segurança do Azure."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/09/2016"
   ms.author="terrylan"/>

# Perguntas frequentes sobre a Central de Segurança do Azure

Este artigo sobre perguntas frequentes responde a perguntas sobre a Central de Segurança do Azure.

> [AZURE.NOTE] As informações neste documento se aplicam à versão de visualização da Central de Segurança do Azure.

## Perguntas gerais

### O que é a Central de Segurança?
 A Central de Segurança ajuda você a impedir, detectar e responder a ameaças com maior visibilidade e controle sobre a segurança dos recursos do Azure. Ela permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com uma enorme variedade de soluções de segurança.

### Como posso obter a Central de Segurança?
 A Central de Segurança vem habilitada com sua assinatura do Microsoft Azure e pode ser acessada no [portal do Azure](https://azure.microsoft.com/features/azure-portal/). ([Entre no portal](https://portal.azure.com), selecione **Procurar** e role até a **Central de Segurança**). Talvez você veja algumas recomendações de segurança no painel instantaneamente. Isso ocorre porque o serviço pode avaliar o estado de segurança de alguns controles com base em sua configuração no Azure. Para acessar o conjunto completo de recomendações de monitoramento de segurança e recursos de alerta, você precisará [habilitar a coleta de dados](#data-collection).

## Cobrança

### Como funciona o faturamento para a Central de Segurança?
Confira [Preços da Central de Segurança do Azure](https://azure.microsoft.com/pricing/details/security-center/) para saber mais.

## Coleta de dados

### Como posso habilitar a coleta de dados?<a name=data-collection></a>
Você pode habilitar a coleta de dados para suas assinaturas do Azure na política de segurança. Para habilitar a coleta de dados, [entre no portal do Azure](https://portal.azure.com), selecione **Procurar**, **Central de Segurança** e **Política de segurança**. Defina a **coleta de dados** como **Ativada** e configure as contas de armazenamento onde você deseja que os dados sejam coletados (veja a pergunta “[Onde meus dados são armazenados?](#where-is-my-data-stored)”). Quando a **Coleta de dados** estiver habilitada, ela coletará automaticamente as informações de configuração e os eventos de segurança de todas as máquinas virtuais com suporte na assinatura.

### O que acontece quando eu habilito a coleta de dados?
A coleta de dados é habilitada por meio do agente de monitoramento do Azure e da extensão de monitoramento de segurança do Azure. A extensão de Monitoramento de Segurança do Azure verifica várias configurações de segurança e eventos relevantes nos rastreamentos do [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (Rastreamento de Eventos para Windows).

Além disso, o sistema operacional gera eventos de log em todas as atividades. O Agente de Monitoramento de Segurança do Azure lê as entradas do registro de eventos e os vestígios de ETW e os copia para sua conta de armazenamento para análise. Essa é a conta de armazenamento configurada na política de segurança. Para obter mais informações sobre a conta de armazenamento, confira a pergunta “[Onde meus dados são armazenados?](#where-is-my-data-stored)”

### A extensão do Agente de monitoramento ou o Monitoramento de segurança afetam o desempenho de meus servidores?
O agente e a extensão consomem uma quantidade nominal de recursos do sistema e devem causar pouco impacto sobre o desempenho.

### Como faço para reverter se eu não quiser mais que a coleta de dados permaneça habilitada?
Você pode desabilitar a coleta de dados para uma assinatura na política de segurança. ([Entre no portal do Azure](https://portal.azure.com), selecione **Procurar**, **Central de Segurança** e **Política de segurança**). Quando você clicar em uma assinatura, uma nova folha será aberta e fornecerá a opção de desativar a coleta de dados. Selecione a opção **Excluir agentes** na faixa de opções para remover os agentes das máquinas virtuais existentes.

### Onde meus dados são armazenados?<a name=where-is-my-data-stored></a>
Para cada região em que você tiver máquinas virtuais em execução, você deverá escolher a conta de armazenamento na qual os dados coletados dessas máquinas virtuais serão armazenados. Isso facilita para manter os dados na mesma área geográfica para fins de privacidade e soberania de dados.

Você pode escolher a conta de armazenamento para uma assinatura na política de segurança. ([Entre no portal do Azure](https://portal.azure.com), selecione **Procurar**, **Central de Segurança** e **Política de segurança**). Quando você clica em uma assinatura, uma nova folha é aberta. Clique em **Escolher contas de armazenamento** para selecionar uma região. Os dados coletados são isolados logicamente dos dados de outros clientes por motivos de segurança.

Para saber mais sobre as contas de armazenamento e o Armazenamento do Azure, confira a [documentação de armazenamento](https://azure.microsoft.com/documentation/services/storage/) e a seção [Sobre as contas de armazenamento do Azure](../storage/storage-create-storage-account.md).

## Como usar a Central de Segurança

### O que é uma política de segurança?
Uma política de segurança define o conjunto de controles que são recomendados para os recursos na assinatura especificada. Na Central de segurança, você pode definir as políticas para as assinaturas do Azure de acordo com os requisitos de segurança de sua empresa, os tipos de aplicativos usados e a confidencialidade dos dados de cada assinatura.

Por exemplo, os recursos usados para desenvolvimento ou teste podem ter requisitos de segurança diferentes daqueles usados para aplicativos de produção. Da mesma forma, os aplicativos com dados regulamentados, como PII (informações de identificação pessoal), podem exigir um nível mais alto de segurança. As políticas de segurança habilitadas na Central de segurança determinarão as recomendações de segurança e monitoramento. Para saber mais sobre as políticas de segurança, consulte a seção [Monitoramento de integridade de segurança na Central de segurança do Azure](security-center-monitoring.md).

### Quem pode modificar uma política de segurança?
As políticas de segurança são configuradas para cada assinatura. Para modificar uma política de segurança, você deve ser o proprietário da assinatura ou um colaborador dela.

Para saber como configurar uma política de segurança, consulte a seção [Como configurar de políticas de segurança na Central de segurança do Azure](security-center-policies.md).

### O que é são recomendações de segurança?
 A Central de Segurança analisa o estado de segurança de seus recursos do Azure. Quando são identificadas possíveis vulnerabilidades de segurança, a Central de Segurança cria recomendações. As recomendações guiam você pelo processo de configuração do controle necessário. Os exemplos abrangem:

- Como provisionar programas antimalware para ajudar a identificar e remover software mal-intencionado
- Como configurar [grupos de segurança de rede](../virtual-network/virtual-networks-nsg.md) e regras para controlar o tráfego para máquinas virtuais
- Provisionamento de firewalls de aplicativo Web para ajudar a proteger contra ataques direcionados aos seus aplicativos Web
- Como implantar atualizações de sistema ausentes
- Endereçamento de configurações do sistema operacional que não coincidem com as linhas de base recomendadas

Somente as recomendações que são habilitadas nas políticas de segurança são mostradas aqui.

### Como é possível ver o estado atual da segurança dos meus recursos do Azure?
O bloco **integridade de recursos** da folha da **Central de segurança** mostra a postura de segurança geral do seu ambiente dividido por máquinas virtuais, aplicativos web e outros recursos. Cada recurso tem um indicador que mostra se possíveis vulnerabilidades de segurança foram identificadas. Clicar no bloco de **Integridade de recursos** exibe os recursos e identifica onde a atenção é requerida ou talvez haja problemas.

### O que dispara um alerta de segurança?
 A Central de segurança automaticamente coleta, analisa e reúne os dados de registro de seus recursos do Azure, da rede e das soluções de parceiros como programas antimalware e firewalls. Quando a Central de Segurança detecta ameaças, é criado um alerta de segurança. Os exemplos abrangem a detecção de:

- As máquinas virtuais comprometidas se comunicam com os endereços IP mal-intencionados conhecidos.
- Malware avançado detectado usando o relatório de erros do Windows.
- Ataques por força bruta contra máquinas virtuais.
- Alertas de segurança de soluções de segurança de parceiro integradas, como programas antimalware ou firewalls de aplicativos Web.

### Como as permissões são tratadas na Central de segurança?
 A Central de segurança dá suporte ao acesso baseado em função. Para saber mais sobre o controle de acesso baseado em função (RBAC) no Azure, confira o [controle de acesso baseado em função do Active Directory do Azure](../active-directory/role-based-access-control-configure.md).

Quando os usuários abrem a Central de Segurança, as recomendações e os alertas que eles veem só estão relacionados a recursos aos quais eles têm acesso. Isso significa que os usuários verão apenas os itens que estão relacionados aos recursos dos quais eles são proprietários, colaboradores ou leitores para a assinatura ou o grupo de recursos a que o recurso pertence.

Para editar uma política de segurança, você deve ser o proprietário da assinatura ou um colaborador dela.

### Quais tipos de máquinas virtuais têm suporte?
Ambas as máquinas virtuais [Gerenciador de Recursos e clássica](../azure-classic-rm.md) terão suporte, inclusive as máquinas virtuais que fazem parte dos clusters Service Fabric do Azure.

As recomendações da lista de controle de acesso no momento se aplicam às máquinas virtuais clássicas. Regras de grupo de segurança de rede, juntamente com recomendações para a instalação de firewalls de aplicativo Web, no momento se aplicam somente a máquinas virtuais do Gerenciador de Recursos.

### Há suporte para as máquinas virtuais do Linux?
A linha de base Central de Segurança do Azure oferece monitoramento para as máquinas virtuais do Linux (somente o Ubuntu versões 12.04, 14.04, 14.10 e 15.04). No futuro, o monitoramento de integridade de segurança adicional e a coleta/análise de dados estarão disponíveis, bem como o suporte adicional para distribuições do Linux.

<!---HONumber=AcomDC_0218_2016-->