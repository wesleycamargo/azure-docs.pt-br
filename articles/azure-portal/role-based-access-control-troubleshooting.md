<properties 
	pageTitle="Solução de problemas de controle de acesso baseado em função" 
	description="Trabalhando com diferentes tipos de recursos para o controle de acesso baseado em função." 
	services="azure-portal"
	documentationCenter="na" 
	authors="stepsic-microsoft-com" 
	manager="terrylan" 
	editor=""/>

<tags 
	ms.service="azure-portal" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/25/2015" 
	ms.author="stepsic"/>

# Solução de problemas de controle de acesso baseado em função

## Introdução

O [Controle da acesso baseado em função](../role-based-access-control-configure.md) é um recurso avançado que permite a você delegar acesso refinado aos recursos no Azure. Isso significa que você pode se sentir confiante ao conceder a uma pessoa específica o direito ao qual exatamente elas precisam. No entanto, às vezes o modelo de recurso para os recursos do Azure pode ser complicado e pode ser difícil entender para o que exatamente você está concedendo permissões.

Este documento informará o que esperar ao usar algumas das novas funções no Portal do Azure. Há três funções predefinidas que estão incluídas nesta versão:* Proprietário * Colaborador * Leitor

Os proprietários e colaboradores terão acesso completo a experiência de gerenciamento, sendo que a diferença é que um colaborador não pode conceder acesso aos outros usuário ou grupos. As coisas se tornam um pouco mais interessante com a função do leitor, sendo assim foi onde dedicamos algum tempo. [Consulte este artigo](../role-based-access-control-configure.md) para obter detalhes sobre como exatamente conceder acesso.

## Cargas de trabalho do Serviço de Aplicativo

### Tendo somente acesso de leitura 

Se você conceder a um usuário, ou tiver somente, o acesso de leitura para um aplicativo Web, poderá haver alguns recursos desabilitados que você não esperava. Os seguintes recursos de gerenciamento requerem o acesso de **gravação** para um aplicativo Web (Colaborador ou Proprietário) e não estarão disponíveis em um cenário de somente leitura.
 
1. Comandos (por exemplo, iniciar, parar, etc.)
2. Alterar configurações como configuração geral, configurações de escala, configurações de backup e configurações de monitoramento.
3. Acessar credenciais de publicação e outros segredos como configurações de aplicativos e cadeias de conexão.
4. Logs de streaming
5. Configuração dos logs de diagnóstico
6. Console (prompt de comando)
7. Ativo e implantações recentes (para a implantação contínua do git local)
8. Gasto estimado
9. Testes da Web
10. Rede virtual (somente visível para um leitor se uma rede virtual foi anteriormente configurada por um usuário com acesso para gravação).
 
Se você não conseguir acessar nenhum desses blocos, precisará ter o acesso de Colaborador para o aplicativo Web.

### Lidando com recursos relacionados
 
Os aplicativos Web são complicados pela presença de alguns recursos diferentes que interagem. Aqui encontra-se um grupo de recursos típico com alguns sites:

![Grupo de recursos do aplicativo Web](./media/role-based-access-control-troubleshooting/Website-resource-model.png)

Como resultado, se você concede acesso a alguém para somente o site, muitas das funcionalidades na lâmina site serão completamente desabilitadas.
 
1. Esses itens exigem o acesso ao **plano do Serviço de Aplicativo** que corresponde ao seu site:  
    * Exibindo a camada de preços do aplicativo Web (por exemplo, Grátis ou Standard).
    * Configuração de dimensionamento (por exemplo, nº de instâncias, tamanho da máquina virtual, configurações de dimensionamento automático).
    * Cotas (por exemplo, armazenamento, largura de banda, CPU).
2. Esses itens exigem acesso ao **Grupo de recursos** todo que contém o seu site:  
    * Associações e certificados SSL (Isso é porque os certificados SSL podem ser compartilhados entre sites no mesmo grupo de recursos e localização geográfica).
    * Regras de alerta
    * Configurações de autoescala
    * Componentes do Application insights
    * Testes da Web

## Cargas de trabalho da máquina virtual

Como muitos aplicativos Web, alguns recursos na folha da máquina virtual requerem o acesso de gravação para a máquina virtual ou a outros recursos no grupo de recursos.

As máquinas virtuais têm esses recursos relacionados: * Nomes de domínio * Redes virtuais * Contas de armazenamento * Regras de alerta

1. Estes itens exigem acesso de **gravação** à máquina virtual:  
    * Pontos de extremidade
    * Endereços IP
    * Discos
    * Extensões
2. Estes exigem acesso de gravação para a máquina virtual e para o **Grupo de recursos** (juntamente com o Nome de domínio) que está no:  
    * Conjunto de disponibilidade
    * Conjunto de balanceamento de carga
    * Regras de alerta
    
Se você não conseguir acessar nenhum desses blocos, precisará solicitar ao seu administrador o acesso de Colaborador para o Grupo de recursos.

<!---HONumber=August15_HO6-->