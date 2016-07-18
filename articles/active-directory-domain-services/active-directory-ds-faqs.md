<properties
	pageTitle="Visualização dos Serviços de Domínio do Active Directory do Azure: Perguntas frequentes | Microsoft Azure"
	description="Perguntas frequentes sobre os Serviços de Domínio do Active Directory do Azure"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/06/2016"
	ms.author="maheshu"/>

# Visualização dos Serviços de Domínio do Active Directory do Azure: perguntas frequentes

Esta página responde a perguntas frequentes sobre a visualização dos Serviços de Domínio do Active Directory do Azure. Continue verificando as atualizações.

### Guia de Solução de Problemas
Consulte nosso [Guia de solução de problemas](active-directory-ds-troubleshooting.md) a fim de obter soluções para os problemas comuns encontrados ao configurar ou administrar os Serviços de Domínio do AD do Azure.


### Configuração

#### Posso criar vários domínios para um único diretório do AD do Azure?
Não. Você só pode criar um único domínio atendido pelos Serviços de Domínio do AD do Azure para um único diretório do AD do Azure.

#### Posso disponibilizar os Serviços de Domínio do AD do Azure em várias redes virtuais na minha assinatura?
O próprio serviço não oferece suporte diretamente para esse cenário. Os Serviços de Domínio do AD do Azure estão disponíveis em somente uma rede virtual por vez. No entanto, você pode configurar a conectividade entre várias redes virtuais para expor Serviços de Domínio do AD do Azure para outras redes virtuais. Este artigo descreve como você pode [conectar redes virtuais no Azure](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

#### Posso habilitar os Serviços de Domínio do AD do Azure usando o PowerShell?
A implantação do PowerShell/automatizada dos Serviços de Domínio do AD do Azure não está disponível no momento.

#### Os Serviços de Domínio do AD do Azure estão disponíveis no novo portal do Azure?
Não. Os Serviços de Domínio do Azure AD podem ser configurados somente no [Portal Clássico do Azure](https://manage.windowsazure.com). Esperamos estender o suporte para o [Portal do Azure](https://portal.azure.com) no futuro.

#### Posso adicionar controladores de domínio a um domínio gerenciado dos Serviços de Domínio do AD do Azure?
Não. O domínio fornecido pelos Serviços de Domínio do AD do Azure é um domínio gerenciado. Você não precisa provisionar, configurar ou gerenciar de outra forma os controladores de domínio para este domínio. Essas atividades de gerenciamento são fornecidas como um serviço pela Microsoft. Portanto, você não poderá adicionar outros controladores de domínio (leitura-gravação nem somente leitura) para o domínio gerenciado.

### Administração e operações

#### Posso conectar ao controlador de domínio para meu domínio gerenciado usando a Área de Trabalho Remota?
Não. Como esse é um domínio gerenciado, não é possível conectar-se aos controladores de domínio por meio da Área de Trabalho Remota. Os membros do grupo “AAD DC Administrators” podem administrar o domínio gerenciado usando ferramentas de administração do AD como o ADAC (Active Directory Administration Center) ou o AD PowerShell. Essas ferramentas são instaladas usando o recurso “Ferramentas de Administração de Servidor Remoto” em um servidor Windows ingressado no domínio gerenciado.

#### Habilitei os Serviços de Domínio do AD do Azure. Qual conta de usuário eu posso usar para ingressar computadores no domínio para este domínio?
Qualquer uma das contas de usuário que você adicionou ao grupo administrativo (por exemplo, “AAD DC Administrators”) seria capaz de ingressar computadores no domínio. Além disso, os usuários desse grupo recebem acesso de área de trabalho remoto para computadores que ingressaram no domínio.

#### Posso exercer privilégios de administrador de domínio para o domínio fornecido pelos Serviços de Domínio do AD do Azure?
Não. Como esse é um serviço gerenciado, você não receberá privilégios administrativos no domínio. Isso significa que os privilégios de "Administrador do Domínio" e de "Administrador Corporativo" não estarão disponíveis dentro do domínio. O administrador de domínio existente ou os grupos de administradores corporativos no seu diretório do Azure AD também não receberão privilégios de administrador do domínio/administrador corporativo para o domínio.

#### Posso modificar associações de grupo usando o LDAP ou outras ferramentas administrativas do AD em domínios fornecidos pelos Serviços de Domínio do AD do Azure?
Não. Associações de grupo não podem ser modificadas em domínios atendidos pelos Serviços de Domínio do AD do Azure. O mesmo se aplica a atributos de usuário. No entanto, é possível alterar associações de grupo ou atributos de usuário no AD do Azure ou em seu domínio local. Essas alterações serão sincronizadas automaticamente nos Serviços de Domínio do AD do Azure.

#### Posso estender o esquema do domínio fornecido pelos Serviços de Domínio do AD do Azure?
Não. O esquema é administrado pela Microsoft para o domínio gerenciado. As extensões de esquema não têm suporte dos Serviços de Domínio do AD do Azure.

#### Posso modificar registros DNS fornecidos pelos Serviços de Domínio do Azure AD?
Sim. Usuários que pertencem ao grupo “AAD DC Administrators” recebem privilégios de “DNS Administrator” para modificar os registros de DNS no domínio gerenciado. Esses usuários podem usar o console do Gerenciador de DNS em um computador que executa o Windows Server ingressado no domínio gerenciado para gerenciar o DNS. Para usar o console do Gerenciador DNS, instale as “Ferramentas de Servidor DNS”, que fazem parte do recurso opcional de “Ferramentas de Administração de Servidor Remoto” no servidor. Mais informações sobre os [utilitários para administrar, monitorar e solucionar problemas de DNS](https://technet.microsoft.com/library/cc753579.aspx) estão disponíveis no TechNet.

### Disponibilidade e cobrança

#### É um serviço pago?
O serviço está disponível por um preço reduzido especial para a duração do período de visualização pública. A cobrança começará com o preço total quando o serviço estiver disponível (GA). Consulte a página de preços para obter mais informações.

#### Há uma avaliação gratuita para o serviço?
Esse serviço está incluído na avaliação gratuita do Azure. Você pode se inscrever para uma [avaliação gratuita de um mês do Azure](https://azure.microsoft.com/pricing/free-trial/).

#### Posso obter os Serviços de Domínio do AD do Azure como parte do EMS (Enterprise Mobility Suite)?
Não, os Serviços de Domínio do AD do Azure é um serviço pré-pago do Azure e não faz parte do EMS. Os Serviços de Domínio do Azure AD estão disponíveis para todas as SKUs do Azure AD (Gratuito, Básico e Premium) e são cobrados por hora dependendo do uso.

#### Em quais regiões do Azure o serviço está disponível?
Consulte nossa [página de regiões](active-directory-ds-regions.md) para ver uma lista das regiões do Azure onde os Serviços de Domínio do AD do Azure estão disponíveis.

#### Quando os Serviços de Domínio do AD do Azure estão disponíveis completamente?
Atualmente, não é possível compartilhar cronogramas para quando o serviço estará disponível.

<!---HONumber=AcomDC_0706_2016-->