<properties urlDisplayName="" pageTitle="Role based access control troubleshooting" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Role based access control troubleshooting" authors="Stephen Siciliano"  solutions="" writer="" manager="" editor=""  />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="Stephen="" Siciliano" />

# Solução de problemas de controle de acesso baseado em função

## Introdução

O [Controle da acesso baseado em função](http://azure.microsoft.com/pt-br/documentation/articles/role-based-access-control-configure/) é um recurso avançado que permite a você delegar acesso refinado aos recursos no Azure. Isso significa que você pode se sentir confiante ao conceder a uma pessoa específica o direito ao qual exatamente elas precisam. No entanto, às vezes o modelo de recurso para os recursos do Azure pode ser complicado e pode ser difícil entender para o que exatamente você está concedendo permissões.

Este documento informará você o que esperar ao usar algumas destas novas funções no portal de visualização. Há três funções predefinidas que estão incluídas nesta versão:

-   Proprietário
-   Colaborador
-   Leitor

Os proprietários e colaboradores terão acesso completo a experiência de gerenciamento, sendo que a diferença é que um colaborador não pode conceder acesso aos outros usuário ou grupos. As coisas se tornam um pouco mais interessante com a função do leitor, sendo assim foi onde dedicamos algum tempo. [Consulte este artigo](http://azure.microsoft.com/pt-br/documentation/articles/role-based-access-control-configure/) para obter detalhes sobre como exatamente conceder acesso.

## Cargas de trabalho do site

### Tendo somente acesso de leitura

Se você conceder a um usuário ou tiver somente acesso de leitura para um site, pode haver alguns recursos que estão desabilitados que você não esperava. As seguintes capacidades de gerenciamento exigem acesso de **gravação** para o site (Colaborador ou Proprietário) e não estarão disponíveis em um cenário de somente leitura.

1.  Comandos (por exemplo, iniciar, parar, etc.)
2.  Alterar configurações como configuração geral, configurações de escala, configurações de backup e configurações de monitoramento.
3.  Acessar credenciais de publicação e outros segredos como configurações de aplicativos e cadeias de conexão.
4.  Logs de streaming
5.  Configuração dos logs de diagnóstico
6.  Sites Console (prompt de comando)
7.  Ativo e implantações recentes (para a implantação contínua do git local)
8.  Gasto estimado
9.  Testes da Web
10. Rede virtual (somente visível para um leitor se uma rede virtual foi anteriormente configurada por um usuário com acesso para gravação).

Se você não consegue acessar nenhuma destas partes, precisará ter acesso de Colaborador ao site.

### Lidando com recursos relacionados

Os sites são complicados pela presença de alguns recursos diferentes que interagem. Aqui encontra-se um grupo de recursos típico com alguns sites:

![Grupo de recursos do site](./media/role-based-access-control-troubleshooting/Website-resource-model.png)

Como resultado, se você concede acesso a alguém para somente o site, muitas das funcionalidades na lâmina site serão completamente desabilitadas.

1.  Esses itens exigem acesso ao **Plano de hospedagem na Web** que corresponde ao seu site:
    -   Exibindo a faixa de preço do site (por exemplo, Grátis, Compartilhado, Básico ou Padrão).
    -   Configuração de escala (por exemplo, nº de instâncias, tamanho de VM, configurações de autoescala).
    -   Cotas (por exemplo, armazenamento, largura de banda, CPU).
2.  Esses itens exigem acesso ao **Grupo de recursos** todo que contém o seu site:
    -   Associações e certificados SSL (Isso é porque os certificados SSL podem ser compartilhados entre sites no mesmo grupo de recursos e localização geográfica).
    -   Regras de alerta
    -   Configurações de autoescala
    -   Componentes do Application insights
    -   Testes da Web

## Cargas de trabalho da máquina virtual

Como muitos sites, alguns recursos na lâmina da Máquina Virtual exigem acesso de gravação para a Máquina Virtual ou a outros recursos no grupo de recursos.

As máquinas virtuais têm estes recursos relacionados:

-   Nomes de domínio
-   Redes virtuais
-   Contas de armazenamento
-   Regras de alerta

1.  Estes itens exigem acesso de **gravação** à máquina virtual:
    -   Pontos de extremidade
    -   Endereços IP
    -   Discos
    -   Extensões
2.  Estes exigem acesso de gravação para a máquina virtual e para o **Grupo de recursos** (juntamente com o Nome de domínio) que está no:
    -   Conjunto de disponibilidade
    -   Conjunto de balanceamento de carga
    -   Regras de alerta

Se você não conseguir acessar nenhuma destas partes, você precisará solicitar ao seu administrador o acesso de Colaborador para o Grupo de recursos.

