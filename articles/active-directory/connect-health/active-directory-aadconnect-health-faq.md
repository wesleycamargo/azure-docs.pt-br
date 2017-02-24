---
title: Perguntas frequentes sobre o Azure Active Directory Connect Health - Azure | Microsoft Docs
description: "Encontre respostas para perguntas frequentes sobre o Azure AD Connect Health. Estas perguntas frequentes abordam dúvidas sobre como usar o serviço, incluindo o modelo de cobrança, recursos, limitações e suporte."
services: active-directory
documentationcenter: 
author: billmath
manager: samueld
editor: curtand
ms.assetid: f1b851aa-54d7-4cb4-8f5c-60680e2ce866
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 7c320a043322fefea1f58301492d4c5a0567320c
ms.openlocfilehash: aff2ced283de8282860d0aa356b12478f647d09c

---
# <a name="azure-ad-connect-health-frequently-asked-questions-faq"></a>Perguntas frequentes (FAQ) do Azure AD Connect Health
Encontre respostas para perguntas frequentes sobre o Azure AD Connect Health. Estas perguntas frequentes abordam dúvidas sobre como usar o serviço, incluindo o modelo de cobrança, recursos, limitações e suporte.

## <a name="general-questions"></a>Perguntas gerais
**P: Eu gerencio vários diretórios do Azure AD. Como alternar entre diferentes diretórios no Portal do Azure?**

Você pode alternar entre diferentes locatários do Azure AD selecionando o Nome de usuário conectado atualmente no canto superior direito e escolhendo a conta apropriada. Se a conta não estiver listada aqui, selecione Sair e, em seguida, use as credenciais apropriadas do diretório para entrar.

**P: Qual versão de funções de identidade tem suporte do Azure AD Connect Health?**

A tabela a seguir lista as funções e as versões de sistema operacional com suporte.

|Função| Sistema Operacional/Versão|
|--|--|
|AD FS| <ul> <li> Windows Server 2008R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|
|Azure AD Connect | Versão 1.0.9125 ou superior.|
|AD DS| <ul> <li> Windows Server 2008R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|

Observe que os recursos fornecidos pelo serviço mudam de acordo com a função e o sistema operacional. Em outras palavras, todos os recursos não estejam disponíveis para todas as versões de sistema operacional. Consulte as descrições de recurso para obter detalhes.

**P: quantas licenças é necessário monitorar minha infra-estrutura?**

* Primeiro o agente de integridade de conexão requer pelo menos uma licença AD Premium do Azure.
* Cada agente registrado adicional requer 25 licenças AADP adicionais. 
* A contagem de agentes é equivalente ao número total de agentes registrados em todas as funções monitoradas (AD FS, Azure Connect AD e AD DS).

As informações de licenciamento também podem ser encontradas na [página Preços do Azure AD](https://aka.ms/aadpricing).

Exemplo:

| Agentes registrados | Licenças necessárias | Configuração de Monitoramento de Exemplo |
| ------ | --------------- | --- |
| 1 | 1 | Servidor de conexão do&1; AAD |
| 2 | 26| 1 AAD Connect Server e 1 Controlador de Domínio |
| 3 | 51 | 1 Servidor AD FS, 1 Proxy AD FS e 1 Controlador de Domínio |
| 4 | 76 | 1 Servidor AD FS, 1 Proxy AD FS e 2 Controladores de Domínio |
| 5 | 101 | 1 AAD Connect Server, servidor 1 Servidor AD FS, 1 Proxy AD FS e 2 Controladores de Dominio |
## <a name="installation-questions"></a>Perguntas sobre a instalação
**P: Qual é o impacto da instalação do agente do Azure AD Connect Health sobre servidores individuais?**

O impacto da instalação do ADFS de Agentes do Microsoft Azure AD Connect Health, de servidores de Proxy de Aplicativo Web, de servidores do Azure AD Connect (sincronização), de Controladores de Domínio é mínimo em relação à CPU, ao consumo de memória, à largura de banda de rede e ao armazenamento.

Os números a seguir são uma aproximação.

    * Consumo de CPU: ~1 a&5;% de aumento
    * Consumo de memória: 10% da memória total do sistema

> [!NOTE]
> Se o agente de integridade perde a conectividade com o serviço de integridade de conexão, ele armazena os dados localmente, até o limite máximo definido. O agente substitui os dados em "cache" de forma “atendido menos recentemente”.
>
>

* Armazenamento em buffer local para Agentes do Azure AD Connect Health: ~20 MB
* Para servidores do AD FS, é recomendável que você provisione um espaço em disco de 1024 MB (1 GB) para o Canal de Auditoria do AD FS para Agentes do Azure AD Connect Health para processar todos os dados de auditoria antes que eles sejam substituídos.

**P: Será necessário reinicializar meus servidores durante a instalação dos agentes do Azure AD Connect Health?**

Não. A instalação dos agentes não exigirá a reinicialização do servidor.

A instalação de algumas das etapas de pré-requisito pode exigir uma reinicialização do servidor. Por exemplo, no Windows Server 2008 R2, a instalação do .net Framework 4.5 requer a reinicialização do servidor.

**P: Os Serviços do Azure AD Connect Health funcionam por meio de um proxy http de passagem?**

Sim.  Para operações em andamento, você pode configurar o Agente de integridade para encaminhar solicitações http de saída usando um HTTP Proxy. Leia mais sobre [configurar HTTP Proxy para agentes de integridade](active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)

Se precisar configurar um proxy durante o registro do Agente, talvez seja necessário modificar as configurações de Proxy do Internet Explorer com antecedência.

1. Abra o Internet Explorer -> Configurações -> Opções de Internet -> Conexões -> Configurações da LAN.
2. Selecione Usar um Servidor de Proxy para a LAN.
3. Selecione Avançado SE você tiver portas de proxy diferente de HTTP e HTTPS/Secure.

**P: Os serviços do Azure AD Connect Health oferecem suporte à autenticação básica ao se conectar a Proxies Http?**

Não. Um mecanismo para especificar o nome de usuário/senha arbitrariamente para fins de autenticação básica não é suportado.

**P: Quais portas de firewall eu preciso abrir para o agente do Azure AD Connect Health funcione?**

Veja a [seção Requisitos](active-directory-aadconnect-health-agent-install.md#requirements) para a lista de portas de firewall e outros requisitos de conectividade.


**P: por que vejo dois servidores com o mesmo nome no Portal do Azure AD Connect Health?**

Quando você remove um agente de um servidor, o servidor não é automaticamente removido do Portal do Azure AD Connect.  Se você tiver removido um agente manualmente de um servidor ou o próprio servidor, precisará excluir manualmente a entrada do servidor no portal do Azure AD Connect Health.

Se você tiver criado um servidor com uma nova imagem ou um novo servidor com os mesmos detalhes (como o nome do computador), e se não tiver removido o servidor já registrado do portal do Azure AD Connect Health e instalado o agente no novo servidor, poderá ver duas entradas com o mesmo nome.  
Nesse caso, você deve excluir a entrada que pertence ao servidor mais antigo manualmente. Os dados para esse servidor devem estar desatualizados.

## <a name="health-agent-registration-and-data-freshness"></a>Registro de agente de integridade e atualização de dados

**P: Quais são os motivos comuns para as falhas de registro do agente de integridade e como solucionar problemas?**

O agente de integridade não conseguirá registrar devido a motivos possíveis
    * O Agente não consegue se comunicar com os pontos de extremidade necessários porque há um firewall bloqueando o tráfego. Isso é especialmente comum em Servidores de Proxy de Aplicativo Web. Verifique se você permitiu a comunicação de saída para as portas e os pontos de extremidade necessários. Confira a [seção Requisitos](active-directory-aadconnect-health-agent-install.md#requirements) para obter detalhes.
    * A comunicação de saída está sujeita a uma inspeção SSL feita pela camada de rede. Isso faz com que o certificado usado pelo agente a ser substituído por entidade/servidor de inspeção e ele falhar executar as etapas necessárias para concluir o registro do agente.
    * O usuário não tem acesso para executar o registro do agente. Por padrão, os administradores globais têm acesso. Você pode usar [controle de acesso com base em função](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control) para delegar acesso a outros usuários.

**P: por que estou obtendo alertado sobre "dados do serviço de integridade não estão atualizados". Como solucionar os problemas?**

Este alerta é gerado pelo serviço de integridade quando recebe todos os pontos de dados do servidor nas últimas 2 horas. Pode haver várias razões para esse alerta seja acionado.
    * O Agente não consegue se comunicar com os pontos de extremidade necessários porque há um firewall bloqueando o tráfego. Isso é especialmente comum em Servidores de Proxy de Aplicativo Web. Verifique se você permitiu a comunicação de saída para as portas e os pontos de extremidade necessários. Confira a [seção Requisitos](active-directory-aadconnect-health-agent-install.md#requirements) para obter detalhes.
    * A comunicação de saída está sujeita a uma inspeção SSL feita pela camada de rede. Isso faz com que o certificado usado pelo agente a ser substituído pela entidade/servidor inspeção e Falha ao carregar dados para o serviço de integridade de conexão.
    * Você pode usar o comando de conectividade criado no agente. [Leia mais](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service).
    * Os agentes também dão suporte a conectividade de saída por meio de um HTTP Proxy não autenticado. [Leia mais](active-directory-aadconnect-health-agent-install.md##configure-azure-ad-connect-health-agents-to-use-http-proxy).


## <a name="operations-questions"></a>Perguntas de operações
**P: Preciso habilitar a auditoria em Servidores de Proxy de Aplicativo da Web?**

Não, a auditoria não precisa estar habilitada nos Servidores de Proxy de Aplicativo da Web (WAP).

**P: Como Alertas do Azure AD Connect Health são resolvidos?**

Alertas do Azure AD Connect Health são resolvidos em uma condição de êxito. Os agentes do Azure AD Connect Health detectam e relatam as condições de sucesso para o serviço periodicamente. Para alguns alertas, a supressão é periódica. Em outras palavras, se a mesma condição de erro não for observada no período de 72 horas a partir da geração do alerta, este será resolvido automaticamente.


## <a name="migration-questions"></a>Perguntas sobre migração

Esta seção se aplica somente aos clientes, que foram notificados sobre uma migração próxima de seus dados de serviço do Azure AD Connect Health.

**P: Será necessário registrar novamente meus agentes ou redefinir as configurações de notificação após a migração?**

Não, as configurações de notificação e informações de registro do agente serão movidas como parte da migração.

**P: Quanto tempo após a migração eu começarei a ver os dados no portal?**

Os dados começarão a aparecer no portal dentro de uma hora após a migração.

**P: O que acontece com meus alertas ativos existentes?**

Todos os alertas aplicáveis serão reativados dentro de uma hora após a migração.


## <a name="related-links"></a>Links relacionados
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Instalação do Agente do Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Operações de Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Usando o Azure AD Connect Health com o AD FS](active-directory-aadconnect-health-adfs.md)
* [Usando o Azure AD Connect Health para sincronização](active-directory-aadconnect-health-sync.md)
* [Usar o Azure AD Connect Health com o AD DS](active-directory-aadconnect-health-adds.md)
* [Histórico de versão do Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)



<!--HONumber=Feb17_HO2-->


