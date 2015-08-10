<properties 
	pageTitle="Monitorar sua infraestrutura de identidade local na nuvem." 
	description="Esta é a página de Integridade do Azure AD Connect que descreve o que ele é e por que você deve usá-lo." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/12/2015" 
	ms.author="billmath"/>

# Monitorar sua infraestrutura de identidade local na nuvem

A Integridade do Azure AD Connect ajuda a monitorar e obter percepções sobre sua infraestrutura de identidade local. Ela oferece a você a capacidade de exibir alertas, desempenho, padrões de uso e definições de configuração, o habilita a manter uma conexão confiável para o Office 365 e muito mais. Isso é feito usando um agente instalado nos servidores de destino. Para obter informações sobre instalação e requisitos do Azure AD Connect Health, consulte[Requisitos do Azure AD Connect Health](active-directory-aadconnect-health-requirements.md).

![O que é a Integridade do Azure AD Connect](./media/active-directory-aadconnect-health/aadconnecthealth2.png)


Todas essas informações são apresentadas no portal de Integridade do Azure AD Connect. Usando o portal de Integridade do Azure AD Connect, você pode exibir alertas, monitoramento de desempenho e análise de uso. Essas informações são apresentadas em um local fácil de usar para que você não precise perder tempo procurando as informações necessárias.

![O que é a Integridade do Azure AD Connect](./media/active-directory-aadconnect-health/usage.png)

Atualizações futuras da integridade do Azure AD Connec incluem monitoramento adicional e percepções sobre outros componentes de identidade e serviços como os serviços do Azure AD Connect Sync. Assim, você obtém um painel único da perspectiva da identidade, habilitando-o a ter um ambiente ainda mais robusto, íntegro e integrado que seus usuários podem aproveitar para aumentar sua produtividade.


![O que é a Integridade do Azure AD Connect](./media/active-directory-aadconnect-health/logo1.png)




## Por que usar a Integridade do Azure AD Connect

A integração de seus diretórios locais ao AD do Azure torna os usuários mais produtivos fornecendo uma identidade comum para acesso aos recursos na nuvem e locais. No entanto, essa integração gera os desafios de garantir de que esse ambiente esteja íntegro, para que os usuários possam acessar de forma confiável recursos locais e na nuvem por meio de qualquer dispositivo. A Integridade do Azure AD Connect fornece uma abordagem fácil baseada na nuvem para monitorar e obter percepções sobre sua infraestrutura de identidade local usada para acessar o Office 365 ou outros aplicativos do Azure AD. É tão simples quanto instalar um agente em cada um de seus servidores de identidade locais.

Integridade do Azure AD Connect para o AD FS dá suporte ao AD FS 2.0 no Windows Server 2008/2008 R2 e ao AD FS no Windows Server 2012/2012R2. Isso também inclui servidores Proxy do AD FS ou Proxy de Aplicativo Web que dão suporte à autenticação para acesso à extranet. A Integridade do Azure AD Connect para o AD FS fornece o seguinte conjunto de recursos principais:

- Exibir e tomar medidas sobre alertas para acesso confiável a aplicativos protegidos do AD FS, incluindo o AD do Azure
- Notificações de email para alertas críticos
- Exibir dados de desempenho para determinar o planejamento de capacidade
- Exibições detalhadas de seus padrões de logon do AD FS para determinar anomalias ou estabelecer linhas de base para planejamento de capacidade

O vídeo a seguir fornece uma visão geral do Azure AD Connect Health:

[AZURE.VIDEO azure-ad-connect-health--monitor-you-identity-bridge]


## Usando o Azure Active Directory Connect Health pela primeira vez no Portal do Azure
Para começar com o Azure Active Directory Connect Health, siga as etapas abaixo. Lembre-se de que, antes de você ver todos os dados em sua instância do Azure AD Connect Health, é necessário instalar o agente do Azure AD Connect Health nos servidores de destino. Para baixar o agente do Azure AD Connect Health, na primeira folha, selecione Início Rápido e Obter Ferramentas. Você também pode baixar o agente diretamente usando o link abaixo. Para usar o Azure Active Directory Connect Health, faça o seguinte:

1. Entre no [portal do Microsoft Azure](https://portal.azure.com/).
2. O Azure Active Directory Connect Health pode ser acessado pelo Marketplace e pesquisando por ele ou selecionando-o no Marketplace e selecionando Segurança + Identidade.
3. Na folha introdutória (uma folha é uma parte do modo de exibição geral. É possível pensar em um blade como uma janela ou saída), clique em criar. Isso abrirá outra folha com informações de seu diretório.
4. Na folha do diretório, clique em Criar. Se você não tiver uma licença do Azure Active Directory Premium, você precisará usar o Azure AD Connect Health. Para obter informações sobre o Azure AD Premium, consulte Introdução ao AD Premium do Azure.






![Portal do Azure AD Connect Health](./media/active-directory-aadconnect-health/portal1.png)



## O portal do Azure Active Directory Connect Health
O portal de Azure AD Connect Health permite a visualização de alertas, monitoramento de desempenho e análise de uso. No primeiro acesso ao Azure AD Connect Health, você verá a primeira folha. Uma folha é uma parte do modo da exibição geral. Você pode pensar uma folha como uma janela. A primeira folha que você vir mostra o Início rápido, Serviços e Configuração. Abaixo a captura de tela é uma breve explicação de cada um deles.

![Portal do Azure AD Connect Health](./media/active-directory-aadconnect-health/portal2.png)

- **Início rápido** – selecionar esta opção abrirá a folha de Início rápido. Aqui você poderá baixar o agente de Azure AD Connect Health, escolhendo Obter Ferramentas, acessar a documentação e fornecer comentários.
- **Serviços de Federação do Active Directory** – essa opção representa todos os serviços do AD FS que o Azure AD Connect Health atualmente está monitorando. As opções que aparecem nesta seção serão discutidas na seção a seguir. Consulte os serviços do Azure Active Directory Connect Health.
- Configurar – isso permite ativar ou desativar o seguinte:
<ol>
1. Atualização para atualizar automaticamente o agente do Azure AD Connect Health para a versão mais recente automática - isso significa que você será atualizada automaticamente para a versão mais recente do agente do Azure AD Connect Health quando ela estiver disponível. Isso é habilitado por padrão.
2. Permitir que o Microsoft accesse os dados de integridade do seu diretório AD do Azure para fins de solução de problemas: isso significa que, se isso estiver habilitado, o Microsoft será capaz de ver os mesmos dados que você está vendo. Isso pode ajudar na solução de problemas e obter ajuda com problemas. Isso está desabilitado por padrão.




## Serviços do Azure Active Directory Connect Health
Esta seção representa os serviços e as instâncias ativas desses serviços que o Azure AD Connect Health está monitorando. Ao clicar nas elipses, será aberta uma folha que mostra todas as instâncias.

![Serviços do Azure AD Connect Health](./media/active-directory-aadconnect-health/portal3.png)

Ao selecionar uma das instâncias, o Azure AD Connect Health abrirá uma folha com informações sobre essa instância de serviços. Aqui você encontrará muitas informações sobre sua instância. Essas informações incluem uma análise de visão geral, propriedades, alertas, monitoramento e análise de uso. Para obter informações sobre elas, consulte os links para as seções subsequentes na parte superior desta página.



----------------------------------------------------------------------------------------------------------
## Download do Agente de Integridade do Azure AD Connect

Para começar a usar a Integridade do o Azure AD Connect, você pode baixar a versão mais recente do agente aqui: [Baixar o Agente de Integridade do Azure AD Connect.](http://go.microsoft.com/fwlink/?LinkID=518973) Verifique se você adicionou o serviço do Marketplace antes de instalar os agentes.

----------------------------------------------------------------------------------------------------------

## Alertas do Azure Active Directory Connect Health
A seção de Alertas do Azure AD Connect Health fornece a lista de alertas ativos. Cada alerta inclui informações relevantes, etapas de resolução e links para documentação relacionada. Ao selecionar um alerta ativo ou resolvido, você verá uma nova folha com informações adicionais, bem como etapas a que serem seguidas para resolver o alerta e links para documentação adicional. Você também pode exibir dados históricos sobre alertas que foram resolvidas no passado.

![Portal do Azure AD Connect Health](./media/active-directory-aadconnect-health/alert1.png)

Ao selecionar um alerta, você receberá informações adicionais, bem como etapas que você pode seguir para resolver o alerta e links para documentação adicional.

![Portal do Azure AD Connect Health](./media/active-directory-aadconnect-health/alert2.png)

## Monitoramento de Desempenho do Azure Active Directory Connect Health
O Monitoramento de Desempenho do Azure Active Directory Connect Health fornece informações de monitoramento nas métricas. Ao selecionar a caixa Monitoramento, será aberta uma folha que fornece informações detalhadas sobre as métricas.


![Portal do Azure AD Connect Health](./media/active-directory-aadconnect-health/perf1.png)


Ao selecionar a opção de filtro na parte superior da folha, você pode filtrar por servidor para ver as métricas individuais de um servidor. Para alterar as métricas, basta clicar com o botão direito do mouse no gráfico de monitoramento na abaixo da folha de monitoramento e selecione Editar gráfico. Em seguida, na nova folha que é aberta, você pode selecionar métricas adicionais na lista suspensa e especificar um intervalo de tempo para exibir os dados de desempenho.


![Portal do Azure AD Connect Health](./media/active-directory-aadconnect-health/perf2.png)

## Relatório e análise de uso do Azure Active Directory Connect Health
A Análise de Uso do Azure AD Connect Health analisa o tráfego de autenticação de servidores de Federação. Ao selecionar a caixa de análise de uso será aberta a folha de análise de uso, que mostra as métricas e os agrupamentos.

>[AZURE.NOTE]Para usar a análise de uso com o AD FS, você deve garantir que a auditoria do AD FS esteja habilitada. Para obter mais informações, consulte requisitos do Azure AD Connect Health.

![Portal do Azure AD Connect Health](./media/active-directory-aadconnect-health/report1.png)

Para selecionar métricas adicionais, especifique um intervalo de tempo ou, para alterar o agrupamento, simplesmente clique no gráfico de análise de uso e selecione Editar gráfico. Em seguida, você pode especificar o intervalo de tempo, alterar ou selecionar métricas e alterar o agrupamento. Você pode exibir a distribuição do tráfego de autenticação com base em diferentes “métricas” e agrupar cada métrica usando parâmetros "agrupar por" relevantes descritos abaixo.

| Métrica | Agrupar Por | O que significa que o agrupamento e por que ele é útil? |
| ------ | -------- | -------------------------------------------- |
| Total de solicitações: o número total de solicitações processado pelo serviço de Federação | Todos | Isso mostrará a contagem do número total de solicitações sem agrupamento. |
| | Aplicativo | Essa opção agrupará o total de solicitações com base na terceira parte confiável de destino. Esse agrupamento é útil para entender qual aplicativo está recebendo o percentual do tráfego total. |
| | Servidor | Essa opção agrupará o total de solicitações com base no servidor que processa a solicitação. Esse agrupamento é útil para entender a distribuição de carga do tráfego total. |
| | Ingresso no local | Essa opção agrupará o total de solicitações com base em se as solicitações são provenientes de dispositivos que são ingressados no local (conhecido). Esse agrupamento é útil para compreender se os recursos são acessados usando dispositivos desconhecidos para a infraestrutura de identidades. |
| | Método de autenticação | Essa opção agrupará o total de solicitações com base no método de autenticação usado para autenticação. Esse agrupamento é útil para entender o método de autenticação comum que é usado para autenticação. Estes são os métodos de autenticação possíveis<ol><li>Autenticação integrada do Windows (Windows)</li><li>Autenticação baseada em formulários (formulários)</li><li>SSO (logon único)</li><li>Autenticação de certificado X 509 (certificado)</li><br>Observe que uma solicitação é contabilizada como SSO (logon único) se os servidores de Federação receberem a solicitação com um Cookie do SSO. Nesses casos, se o cookie for válido, o usuário não precisa fornecer credenciais e obtém acesso contínuo ao aplicativo. Isso é comum se você tiver várias partes confiáveis protegidas pelos servidores da Federação. |
| | Local de rede | Essa opção agrupará o total de solicitações com base no local de rede do usuário. Pode ser qualquer intranet ou extranet. Esse agrupamento é útil para saber qual é a porcentagem do tráfego que está vindo da intranet em comparação com a extranet. |
| Total de solicitações com falha: o número total de solicitações com falha processadas pelo serviço de Federação. <br>(Essa métrica só está disponível no AD FS para o Windows Server 2012 R2)| Tipo de erro | Ela mostrará o número de erros com base nos tipos de erro predefinidos. Esse agrupamento é útil para entender os tipos comuns de erros. <ul><li>Nome de usuário ou senha incorreta: erros devidos ao nome de usuário incorreto ou senha.</li> <li>"Bloqueio extranet": falhas devidas às solicitações recebidas de um usuário bloqueado na extranet</li><li>"Senha expirada": falhas devidas a usuários que fazem logon com uma senha expirada.</li><li>"Conta desativada": falhas devidas a usuários que fazem logon com uma conta desabilitada.</li><li>"Autenticação de dispositivo": falhas causadas por usuários que não estejam autenticados usando uma autenticação de dispositivo.</li><li>" Autenticação de certificado de usuário": falhas causadas por usuários que não estejam autenticados devido a um certificado inválido.</li><li>" MFA": falhas devido ao usuário que não esteja autenticado usando a autenticação multifator.</li><li>" Outras credenciais":"Autorização de emissão": falhas devidas a falhas de autorização.</li><li>" Delegação de emissão": falhas devido a erros de delegação de emissão.</li><li>"Aceitação do token": falhas causadas por rejeição do ADFS um terceiro confiável que seja provedor de identidade.</li><li>"Protocolo": falha devida a erros de protocolo.</li><li>" Desconhecido": capturar tudo. Quaisquer outras falhas não se encaixam nas categorias definidas.</li> |
| | Servidor | Isso agrupará os erros com base no servidor. Isso é útil para entender a distribuição de erro entre servidores. A distribuição desigual poderia ser um indicador de um servidor em um estado com falha. |
| | Local de rede | Isso agrupará os erros com base no local de rede das solicitações (intranet vs extranet). Isso é útil para entender o tipo de solicitações com falha. |
| | Aplicativo | Isso agrupará as falhas com base no aplicativo de destino (terceira parte confiável). Isso é útil para entender qual aplicativo de destino está tendo maior número de erros. |
| Contagem de usuários: o número médio de usuários exclusivos ativos no sistema | Todos | Isso fornece uma contagem do número médio de usuários que usam o serviço de federação na fração de tempo selecionada. Os usuários não estão agrupados. <br>A média dependerá da fração de tempo selecionada. |
| | Aplicativo | Isso agrupará o número médio de usuários com base no aplicativo de destino (terceira parte confiável). Isso é útil para entender a quantos usuários estão usando o aplicativo. |

## E agora?
Para continuar com o Azure AD Connect Health, consulte [Requisitos do Azure AD Connect Health](active-directory-aadconnect-health-requirements.md). Depois que o agente estiver instalado e coletando dados, consulte[Operações do Azure AD Connect Health](active-directory-aadconnect-health-operations.md)para obter mais informações sobre como configurar o Azure AD Connect Health ou faça check-out em [Perguntas Frequentes.](active-directory-aadconnect-health-faq.md)


**Recursos adicionais**

* [Integridade do Azure AD Connect no MSDN](https://msdn.microsoft.com/library/azure/dn906722.aspx)


 

<!---HONumber=July15_HO5-->