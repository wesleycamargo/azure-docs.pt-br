<properties
	pageTitle="Solução de problemas de controle de acesso baseado em função | Microsoft Azure"
	description="Obtenha ajuda para problemas ou dúvidas sobre recursos do Controle de Acesso Baseado em Função."
	services="azure-portal"
	documentationCenter="na"
	authors="kgremban"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/12/2016"
	ms.author="kgremban"/>

# Solução de problemas de Controle de Acesso baseado em função

## Introdução

O [Controle de Acesso Baseado em Função](role-based-access-control-configure.md) é um recurso avançado que permite a você delegar acesso refinado aos recursos no Azure. Isso significa que você pode ficar tranquilo, concedendo a uma certa pessoa o direito de usar exatamente o que ela precisa, e nada mais. No entanto, às vezes o modelo de recurso para os recursos do Azure pode ser complicado e pode ser difícil entender para o que exatamente você está concedendo permissões.

Este documento informará a você o que esperar ao usar algumas das funções no Portal do Azure. Estas três funções abrangem todos os tipos de recurso:

- Proprietário
- Colaborador
- Leitor

Os proprietários e colaboradores têm acesso completo a experiência de gerenciamento, mas um colaborador não pode conceder acesso aos outros usuário ou grupos. As coisas se tornam um pouco mais interessante com a função do leitor, sendo assim foi onde dedicamos algum tempo. Consulte o [Artigo de introdução ao Controle de Acesso Baseado em Função](role-based-access-control-configure.md) para obter detalhes sobre como conceder acesso.

## Cargas de trabalho do serviço de aplicativo

### Recursos do acesso de gravação

Se você conceder a um usuário o acesso somente leitura a um aplicativo Web, para sua surpresa, alguns recursos estarão desabilitados. Os seguintes recursos de gerenciamento exigem o acesso de **gravação** para um aplicativo Web (Colaborador ou Proprietário) e não estarão disponíveis em um cenário de somente leitura.

- Comandos (por exemplo, iniciar, parar, etc.)
- Alterar configurações como configuração geral, configurações de escala, configurações de backup e configurações de monitoramento.
- Acessar credenciais de publicação e outros segredos como configurações de aplicativos e cadeias de conexão.
- Logs de streaming
- Configuração dos logs de diagnóstico
- Console (prompt de comando)
- Ativo e implantações recentes (para a implantação contínua do git local)
- Gasto estimado
- Testes da Web
- Rede virtual (somente visível para um leitor se uma rede virtual foi anteriormente configurada por um usuário com acesso para gravação).

Se você não conseguir acessar nenhum desses blocos, precisará solicitar ao seu administrador o acesso de Colaborador para o aplicativo Web.

### Lidando com recursos relacionados

Os aplicativos Web são complicados pela presença de alguns recursos diferentes que interagem. Aqui encontra-se um grupo de recursos típico com alguns sites:

![Grupo de recursos do aplicativo Web](./media/role-based-access-control-troubleshooting/website-resource-model.png)

Como resultado, se você conceder a alguém acesso somente ao aplicativo Web, muitas das funcionalidades na folha do site no portal do Azure serão desabilitadas.

Esses itens exigem acesso de **gravação** ao **plano do Serviço de Aplicativo** que corresponde ao seu site:

- Exibindo o tipo de preço do aplicativo Web (Grátis ou Standard)
- Configuração de escala (número instâncias, tamanho da máquina virtual, configurações de escalonamento automático)
- Cotas (armazenamento, largura de banda, CPU)

Esses itens exigem acesso de **gravação** a todo o **Grupo de recursos** que contém o seu site:

- Associações e certificados SSL (Isso ocorre porque certificados SSL podem ser compartilhados entre sites no mesmo grupo de recursos e localização geográfica)
- Regras de alerta
- Configurações de autoescala
- Componentes do Application insights
- Testes da Web

## Cargas de trabalho da máquina virtual

Como muitos aplicativos Web, alguns recursos na folha da máquina virtual requerem o acesso de gravação para a máquina virtual ou a outros recursos no grupo de recursos.

As máquinas virtuais são relacionadas a nomes de domínio, redes virtuais, contas de armazenamento e regras de alerta.

Estes itens exigem acesso de **gravação** à **Máquina virtual**:

- Pontos de extremidade
- Endereços IP
- Discos
- Extensões

Eles exigem acesso de **gravação** à **Máquina virtual** e ao **Grupo de recursos** (juntamente com o Nome de domínio) que está em:

- Conjunto de disponibilidade
- Conjunto de balanceamento de carga
- Regras de alerta

Se você não conseguir acessar nenhum desses blocos, precisará solicitar ao seu administrador o acesso de Colaborador para o Grupo de recursos.

## Veja mais
- [Controle de Acesso Baseado em Função](role-based-access-control-configure.md): introdução ao RBAC no portal do Azure.
- [Funções internas](role-based-access-built-in-roles.md): obter detalhes sobre as funções que são incluídas por padrão no RBAC.
- [Funções personalizadas no Azure RBAC](role-based-access-control-custom-roles.md): aprenda a criar funções personalizadas para atender às suas necessidades de acesso.
- [Criar um relatório de histórico de alterações de acesso](role-based-access-control-access-change-history-report.md): mantenha o controle das alterações de atribuições de função no RBAC.

<!---HONumber=AcomDC_0713_2016-->