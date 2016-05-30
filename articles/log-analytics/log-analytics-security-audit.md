<properties
	pageTitle="Solução de Segurança e Auditoria no Log Analytics | Microsoft Azure"
	description="A solução de Segurança e Auditoria no Log Analytics fornece uma exibição abrangente da postura de segurança de TI de sua organização com consultas de pesquisa internas para problemas importantes que exigem sua atenção."
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/03/2016"
	ms.author="banders"/>

# Solução de Segurança e Auditoria no Log Analytics

A solução de Segurança e Auditoria no Log Analytics fornece uma exibição abrangente da postura de segurança de TI de sua organização com consultas de pesquisa internas para problemas importantes que exigem sua atenção.

## Instalando e configurando a solução
Use as informações a seguir para instalar e configurar a solução.

- Adicione a solução de Segurança e Auditoria ao seu espaço de trabalho do OMS usando o processo descrito em [Adicionar soluções do Log Analytics por meio da Galeria de Soluções](log-analytics-add-solutions.md). Não é necessária nenhuma configuração.



## Detalhes de coleta de dados de Segurança e Auditoria

A tabela a seguir mostra os métodos de coleta de dados e outros detalhes sobre como os dados são coletados para a solução de Segurança e Auditoria.

|tipo de dados| plataforma | Agente direto | Agente SCOM | Armazenamento do Azure | SCOM necessário? | Os dados do agente SCOM enviados por meio do grupo de gerenciamento | frequência de coleta |
|---|---|---|---|---|---|---|---|
|Logs de eventos de segurança do Windows|Windows|![Sim](./media/log-analytics-security-audit/oms-bullet-green.png)|![Sim](./media/log-analytics-security-audit/oms-bullet-green.png)|![Sim](./media/log-analytics-security-audit/oms-bullet-green.png)| ![Não](./media/log-analytics-security-audit/oms-bullet-red.png)|![Não](./media/log-analytics-security-audit/oms-bullet-red.png)| Para o armazenamento do Azure: 10 min; para o agente: na chegada|
|Logs do firewall do Windows|Windows|![Sim](./media/log-analytics-security-audit/oms-bullet-green.png)|![Sim](./media/log-analytics-security-audit/oms-bullet-green.png)|![Não](./media/log-analytics-security-audit/oms-bullet-red.png)| ![Não](./media/log-analytics-security-audit/oms-bullet-red.png)|![Não](./media/log-analytics-security-audit/oms-bullet-red.png)| na chegada|
|Logs de eventos do Windows|Windows|![Sim](./media/log-analytics-security-audit/oms-bullet-green.png)|![Sim](./media/log-analytics-security-audit/oms-bullet-green.png)|![Sim](./media/log-analytics-security-audit/oms-bullet-green.png)| ![Não](./media/log-analytics-security-audit/oms-bullet-red.png)|![Sim](./media/log-analytics-security-audit/oms-bullet-green.png)| para o armazenamento do Azure: 1 min; para o agente: na chegada|


## Práticas recomendadas para análise forense

**Procurar as evidências**

Ao realizar uma análise forense usando a solução de Segurança e Auditoria, você procura a evidência que usuários mal-intencionados deixarem para trás. Independentemente do que os usuários estão fazendo em seu ambiente de TI, muitas das atividades em que participam geram artefatos de segurança. Você deve observar que logs geralmente são apagados por invasores e costuma ser a primeira etapa que eles executam ocultando seu caminho.

No entanto, se os usuários estão acessando seus próprios computadores ou acessando-os remotamente, as evidências sobre seu uso são armazenadas nos logs de eventos. O OMS coleta esses artefatos *assim que eles ocorrem*, antes que qualquer pessoa possa adulterá-los, e permite executar diferentes tipos de análise correlacionando os dados em vários computadores.

**Saiba como configurar e coletar eventos de auditoria**

Para aproveitar ao máximo a solução de Segurança e Auditoria, é necessário configurar o nível dos eventos de auditoria coletados por seu ambiente do Windows que melhor atende às suas necessidades, usando os recursos da Web a seguir.

- [Como definir as configurações da política de segurança](https://technet.microsoft.com/library/dn135243&#40;v=ws.10&#41;.aspx)
- [Configuração avançada da política de auditoria](https://technet.microsoft.com/library/jj852202&#40;v=ws.10&#41;.aspx)
- [Recomendações de Política de Auditoria](https://technet.microsoft.com/library/dn487457.aspx)

**Habilitar o AppLocker**

Você também deve habilitar os eventos do AppLocker para obter informações detalhadas sobre as execuções do processo que ocorrem em seu ambiente de TI. Para obter mais informações, veja [Para configurar uma política do AppLocker como Somente auditoria](https://technet.microsoft.com/library/hh994622.aspx).

**Configurar o nível de auditoria de eventos do Windows**

O ambiente de computação do Windows fornece a capacidade de configurar o nível de captura de registros relacionados à segurança. Por exemplo, você pode configurar seu ambiente para que sempre que alguém acesse um arquivo, leia um arquivo ou abra um arquivo, um evento seja gerado. O nível de detalhe que você deseja coletar varia dependendo de suas necessidades. No entanto, todas as opções que você habilita vêm com algum tipo de custo, porque você precisa armazenar todas as informações coletadas. Por esse motivo, em muitos ambientes de organizações de TI, as pessoas optam por não habilitar a coleta de dados de gravação do objeto ou de leitura do objeto. Sempre que alguém acessa qualquer arquivo, milhares de eventos podem ser gerados, alguns dos quais são ruídos inúteis. O nível de coleção definido depende de seu bom senso.

>[AZURE.NOTE] Se você estiver usando o agente direto e tiver um servidor proxy em sua organização, será necessário configurá-lo para permitir que o agente acesse o OMS. Para obter mais informações, veja [Definir as configurações de proxy e firewall no Log Analytics](log-analytics-proxy-firewall.md).

## Práticas recomendadas para investigações de padrão de violação de segurança

**Investigue os padrões de atividade anormal**

Violações de segurança geralmente são originadas por credenciais legítimas e exigem que um usuário mal-intencionado tente obter um privilégio elevado por meio de ataques. A solução de Segurança e Auditoria não tem como foco a detecção de intrusões – em vez disso, ela ajuda a investigar e descobrir padrões de atividades anormais com Problemas Importantes. Por exemplo, você deve investigar as seguintes atividades anormais e quaisquer outras que apareçam nos **Problemas Importantes**.

- logons incomuns no computador de um usuário que normalmente não o utiliza
- enumeração de rede incomum de usuários ou computadores atípicos
- novas contas de usuários criadas com direitos administrativos
- alterações em políticas de segurança ou de log
- executáveis suspeitos

## Práticas recomendadas para cenários de auditoria

Sua organização pode ter regulamentos e políticas de conformidade de rede e computador que você deve cumprir, exigindo registros extensos de auditoria. Por exemplo, se sua organização é uma empresa de finanças, que talvez você precise manter registros provar para qualquer ponto no tempo, qual usuário executou uma operação específica na rede. Talvez você também seja solicitado a gerar relatórios detalhando a atividade de um usuário específico ou servidor selecionado sob demanda e a voltar no tempo muitos meses, às vezes até anos atrás.

Você pode usar a solução de Segurança e Auditoria para coletar dados de auditoria em todo o seu ambiente de TI, tanto se os computadores estiverem no local ou na nuvem. Todos os dados de auditoria são armazenados, indexados e mantidos por sua assinatura. Com uma assinatura Premium do OMS, uma quantidade ilimitada de dados é armazenada durante um ano. Em seguida você pode exibir dados de auditoria, realizar pesquisas e correlacionar através de diferentes tipos de dados e computadores para obter resultados abrangentes para qualquer intervalo de tempo desde que os dados foram coletados.

**Use a Política de Grupo para coletar dados de auditoria**

Todos os dados de auditoria que você deseja coletar e enviar ao OMS são totalmente gerenciados com a Política de Grupo. Você pode usá-lo para definir as configurações de segurança como parte de um Objeto de Política de Grupo (GPO), que são vinculadas aos contêineres do Active Directory como sites, domínios e unidades organizacionais, e eles ajudam a gerenciar as configurações de segurança. Os dados de política são registrados e, posteriormente, enviados ao serviço do OMS.

**Use o AppLocker para coletar dados de auditoria**

Além das configurações de política local, se você usar o AppLocker para coletar dados de auditoria, o OMS coletará os dados e, em seguida, você poderá exibi-los.

## Conduzir uma investigação simples para um executável suspeito

1. Entre no OMS.
2. Na página **Visão geral**, examine as informações exibidas no bloco **Segurança e Auditoria** e, em seguida, clique nele. ![Imagem da página Visão geral](./media/log-analytics-security-audit/oms-security-audit-dash02.png)
3. Na página **Segurança e Auditoria**, examine as informações na folha **PROBLEMAS IMPORTANTES**. Na imagem de exemplo você verá seis problemas importantes de hoje com dois de ontem. Neste exemplo, há um executável suspeito. Clique em **Executáveis Suspeitos** na folha **PROBLEMAS IMPORTANTES** . ![Imagem da página Segurança e Auditoria](./media/log-analytics-security-audit/oms-security-audit-dash03.png)
4. A pesquisa exibe a consulta e os resultados para o executável suspeito que você clicou. No exemplo, há um resultado e o hash do arquivo é exibido. Clique na ID **FILEHASH** . ![Imagem do filehash dos Resultados de pesquisa](./media/log-analytics-security-audit/oms-security-audit-search01.png)
5. A pesquisa exibe informações adicionais sobre o arquivo executável, incluindo seu nome de processo e o caminho do arquivo. Clique no **&lt;Nome do arquivo&gt; do processo**. No exemplo, isso é HEXEDIT. EXE. ![Imagem do processo dos Resultados de pesquisa](./media/log-analytics-security-audit/oms-security-audit-search02.png)
6. A pesquisa anexa o nome do processo entre aspas à consulta. "**HEXEDIT. EXE"**, neste exemplo.![Imagem da Consulta de pesquisa](./media/log-analytics-security-audit/oms-security-audit-search03.png)
7. Na caixa de Pesquisa, remova tudo menos o nome do processo e as aspas e, em seguida, clique no ícone de Pesquisa. ![Imagem das informações de pesquisa detalhada](./media/log-analytics-security-audit/oms-security-audit-search04.png)
8. A pesquisa exibe informações detalhadas sobre o processo, incluindo os computadores em que o processo é executado, a conta de usuário em que o processo é executado, e a data e hora em que um evento foi criado para o processo.
9. Usando as informações que encontrar, você pode tomar uma ação corretiva conforme necessário. Por exemplo, se determinar que o executável é malware, em seguida, você deve agir para removê-lo de todos os sistemas de computador que ele afeta. Depois que o executável for removido e o OMS receber os eventos de log e auditoria atualizados para os sistemas de computador, os valores na folha PROBLEMAS IMPORTANTES serão alterados no dia seguinte.

## Investigar endereços IP mal-intencionados

Um método para proteger seus servidores é garantir que eles não estão se comunicando com nenhum endereço IP suspeito ou mal-intencionado conhecido. No OMS, você tem uma exibição centralizada mostrando todos os IPs mal-intencionados conhecidos com os quais seus servidores e clientes gerenciados podem estar se comunicando. Trabalhando com o MSTIC (Microsoft Threat Intelligence Center), o OMS obtém atualizações por hora sobre os últimos IPs mal-intencionados conhecidos e informa se algum de seus servidores poderia estar comprometido. A equipe do MSTIC trabalha com vários parceiros de inteligência contra ameaças para coletar e fornecer essa lista consolidada para o serviço do OMS.

A exibição de IPs mal-intencionados pode ser encontrada na solução de Segurança e Auditoria no portal do OMS, mostrada como **Endereços IP Suspeitos Distintos Acessados**.

É possível abrir o bloco e exibir a lista completa de endereços IP suspeitos distintos com os quais seus dispositivos podem estar se comunicando. O OMS examina todas as fontes de dados que você envia para o serviço, incluindo:

- Logs do firewall do Windows
- Logs IIS
- WireData


O OMS exibe vários campos relacionados aos endereços IP suspeitos nos resultados da pesquisa de log. Alguns dos mais importantes incluem:

- **IndicatorThreatType**: alguns exemplos são botnet, proxy, darknet, comando de malware e nó de controle.
- **Description**: o tipo de ameaça e nosso nível de confiança de que o endereço IP é mal-intencionado. Esses são os mesmos dados que a Microsoft utiliza para sua própria proteção, que são disponibilizados aos usuários do OMS.

Para obter uma lista completa dos campos de endereços IP suspeitos, veja [Campos da pesquisa de log de IPs mal-intencionados](#Malicious-IP-log-search-fields) abaixo.

### Para realizar uma investigação de endereços IP mal-intencionados

Neste exemplo, vamos examinar um possível endereço IP mal-intencionado usando os dados de log do firewall do Windows.

1. Entre no OMS.
2. Na página **Visão geral**, examine as informações exibidas no bloco **Segurança e Auditoria** e, em seguida, clique nele. ![Imagem da página Visão geral](./media/log-analytics-security-audit/oms-security-audit-dash02.png)  
3. Na página **Segurança e Auditoria**, examine as informações resumidas no bloco **Endereços IP Suspeitos Distintos Acessados**.![Página de Segurança e Auditoria mostrando o IP suspeito](./media/log-analytics-security-audit/oms-maliciousip-01.png)  
4. Neste exemplo, há 6 endereços IP suspeitos. Clique no bloco **Endereços IP Suspeitos Distintos Acessados**.
5. A pesquisa exibe a consulta e os resultados para os endereços IP mal-intencionados encontrados. No exemplo, há 6 resultados e os endereços IP são exibidos.![resultados da pesquisa mostrando endereços IP suspeitos](./media/log-analytics-security-audit/oms-maliciousip-02-revised.png)  
6. Na imagem acima, observe o último endereço IP. Neste exemplo, ele é **94.102.56.130**. Clique nesse endereço.
7. A pesquisa exibe resultados detalhados, mostrando vários dados do indicador de ameaça. **Clique em Mostrar** mais para ver o conjunto completo de resultados.![resultados da pesquisa mostrando dados detalhados do indicador de ameaça](./media/log-analytics-security-audit/oms-maliciousip-03-revised.png)  
8. Se desejar exibir uma lista de todos os servidores em seu ambiente que podem estar se comunicando com um IP mal-intencionado, você poderá usar a consulta de pesquisa de log a seguir.

    ```
    IsActive=True | measure count() by Computer
    ```

### Realizando ações corretivas para a comunicação com IPs mal-intencionados

Se você tiver certeza de que um componente ou processo em sua rede está se comunicando com endereços IP mal-intencionados conhecidos com o envio de dados, a ação que deverá ser tomada será evidente:

- Verifique se o recurso está enviando dados para o endereço IP mal-intencionado.
- Possivelmente, remova ou bloqueie o software do computador que está enviando seus dados e bloqueie a comunicação com endereços IP específicos para software específico.
- Determine se as credenciais do usuário que executam um processo ou executável podem estar comprometidas investigando todas as contas sob as quais o processo questionável é executado. Determine quais outros recursos ele pode ter acessado.
- Determine como o software foi instalado no computador.
- Deixe temporariamente o software questionável instalado para que você possa monitorar atentamente suas atividades. Após uma avaliação completa, realize uma ação corretiva, conforme necessário.


No entanto, se você *não tiver certeza* de que seus dados estão sendo enviados para um endereço IP mal-intencionado conhecido, ou se a *confiança for questionável*, as ações corretivas serão menos evidentes. Em geral, talvez você queira investigar isso usando algumas das etapas listadas acima. Em outros casos, talvez você não queira realizar nenhuma ação. Em última análise, você é a pessoa que está mais familiarizada com a infraestrutura de TI de sua organização e está na melhor posição para determinar como as ameaças potenciais serão tratadas.


Os logs de firewall mostram a direção na qual os dados estão sendo movidos. Quando o firewall é configurado para bloquear a comunicação de saída (mostrado como “envio” nos logs) com IPs específicos, a tentativa de comunicação pelo software potencialmente comprometido com IPs mal-intencionados aparece como *bloqueada* nos resultados da pesquisa de log. Essa é uma situação em que você deverá realizar uma ação adicional – embora o software comprometido possa estar bloqueado para um endereço IP específico, ele poderá tentar se comunicar com outros endereços IP mal-intencionados desconhecidos e potenciais no futuro.

Os logs de firewall também mostram quando os dados de entrada foram bloqueados e você poderá ver muitas atividades para essa situação. Você deve se preocupar com isso? Provavelmente, não. Quando os logs de firewall mostram os dados de entrada bloqueados, isso comprova que o firewall está fazendo o que deveria – protegendo sua infraestrutura.


### Inteligência contra ameaças de IPs mal-intencionados

A Microsoft compartilha dados com uma variedade de organizações para criar uma exibição resumida de todos os indicadores conhecidos que podem comprometer os dados. O resumo visa identificar ocorrências em conjuntos de dados e mostrar os detalhes estendidos de ameaças como links, para que outros detalhes sejam exibidos com facilidade. Essa inteligência contra ameaças é exibida nos resultados da pesquisa de log para IPs mal-intencionados.

Há três métodos pelos quais a Microsoft recebe dados de ameaça, que são agregados para formar um conjunto de dados de inteligência contra ameaças. Basicamente, esses provedores são divididos nas seguintes categorias:

- Assinaturas pagas – empresas com as quais a Microsoft celebrou um contrato para a compra de seus dados.
- Dados de parceiros – empresas de inteligência de segurança. Você pode pensar nesses dados como uma “inteligência de sinal”, já que eles não contêm relatórios detalhados; no entanto, os dados são considerados válidos.
- Processos internos da Microsoft – principalmente, o MSTIC (Microsoft Threat Intelligence Center).

#### Campos da pesquisa de log de IPs mal-intencionados

Os campos de endereços IP mal-intencionados a seguir são exibidos nos resultados da pesquisa de log.

|campo de pesquisa| description |
|---|---|
| **IsActive**| Designa que o indicador está ativo e deverá ser considerado uma ocorrência válida se ele corresponder a um endereço IP encontrado. O MSTIC mantém esse indicador.|
|**Confidence**| Mostra um valor de 1 a 100. O MSTIC mantém esse valor e os algoritmos que o definem. Esse é um valor válido a ser usado para filtrar os resultados da pesquisa.|
|**TLPLevel**| O nível de protocolo de semáforo. Os valores são: Verde, Âmbar e Vermelho. Esses valores correspondem a baixo impacto nos negócios, impacto médio nos negócios e alto impacto nos negócios, respectivamente.|
| **IndicatorThreatType** | Uma breve descrição do tipo de ameaça causado pelo indicador. Alguns incluem: Botnet, DDoS, Malware, URL mal-intencionada, IP mal-intencionado, Phishing e Spam.|
| **Severidade** | Mostra um valor de 0 a 5. Indica a severidade da ameaça.|
| **MaliciousIP** | O endereço IP do host mal-intencionado.|
| **CommunicationDirection** | Mostra a direção na qual o tráfego IP está ocorrendo para o endereço IP mal-intencionado.|

## Próximas etapas

- [Pesquise nos logs](log-analytics-log-searches.md) para exibir dados detalhados de segurança e auditoria.

<!---HONumber=AcomDC_0518_2016-->