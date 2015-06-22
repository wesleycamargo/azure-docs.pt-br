<properties 
	pageTitle="Exibir relatórios de acesso e uso" 
	description="Um tópico que explica como exibir relatórios de acesso e uso para obter informações sobre a integridade e a segurança do diretório da organização." 
	services="active-directory" 
	documentationCenter="" 
	authors="kenhoff" 
	manager="TerryLan" 
	editor="LisaToft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/10/2015" 
	ms.author="kenhoff;Justinha"/>

# Exibir relatórios de acesso e uso

Você pode usar os relatórios de uso e de acesso do Active Directory do Azure para obter visibilidade quanto à integridade e a segurança do diretório da sua organização. Com essas informações, um administrador de diretório pode determinar melhor onde possíveis riscos de segurança podem estar, de modo que pode fazer planos adequados para mitigar esses riscos.

No Portal de Gerenciamento do Azure, os relatórios são categorizados das seguintes maneiras:

- Relatórios de anomalias – contêm eventos de entrada que nós identificamos como anômalos. Nosso objetivo é que você fique ciente dessas atividades e permitir que você possa tomar uma decisão quanto a um evento ser suspeito ou não. 
- Relatórios de aplicativos integrados – fornece um panorama de como os aplicativos em nuvem estão sendo usados na sua organização. O Active Directory do Azure oferece integração com milhares de aplicativos em nuvem. 
- Relatórios de erros – indicam erros que podem ocorrer ao provisionar contas para aplicativos externos.
- Relatórios específicos do usuário – exibem dados de atividade de entrada/dispositivo de vídeo de um usuário específico.
- Logs de atividades – contêm um registro de todos os eventos auditados no últimas 24 horas, últimos 7 dias ou últimos 30 dias, bem como alterações no grupo de atividade e atividades de registro e redefinição de senha.

> [AZURE.NOTE]
> 
- Alguns relatórios avançados de uso de recursos e anomalias estão disponíveis somente quando você habilita o [Azure Active Directory Premium](active-directory-get-started-premium.md). Relatórios avançados ajudam a melhorar a segurança de acesso, responder às ameaças potenciais e obter acesso a análises sobre o uso do aplicativo e acesso do dispositivo.
- As Azure Active Directory Premium e Basic estão disponíveis para clientes na China usando a instância mundial do Active Directory do Azure. As edições Azure Active Directory Premium e Basic não têm suporte atualmente no serviço Microsoft Azure operado pela 21Vianet na China. Para obter mais informações, entre em contato conosco no [Fórum do Active Directory do Azure](http://feedback.azure.com/forums/169401-azure-active-directory).


## Relatórios de anomalias

Nome do relatório | Disponível nesta edição    	
------------- | -------------  
[Entradas de fontes desconhecidas](#sign-ins-from-unknown-sources) | Gratuito e Premium
[Entradas após várias falhas](#sign-ins-after-multiple-failures) | Gratuito e Premium
[Entradas de várias regiões geográficas](sign-ins-from-multiple-geographies) | Gratuito e Premium
[Entradas de endereços IP com atividade suspeita](#sign-ins-from-ip-addresses-with-suspicious-activity) | Premium
[Atividade de entrada anômala](#anamolous-sign-in-activity) | Premium
[Entradas de dispositivos possivelmente infectados](#sign-ins-from-possibly-infected-devices) | Premium
[Usuários com atividade de entrada anômala](#users-with-anomalous-sign-in-activity) | Premium
[Uso do aplicativo: resumo](#application-usage-summary) | Premium
[Uso do aplicativo: detalhado](#application-usage-detailed) | Premium
[Painel do aplicativo](#application-dashboard) | Gratuito e Premium
[Erros de provisionamento de conta](#account-provisioning-errors) | Gratuito e Premium
[Dispositivos](#devices) | Premium
[Atividade](#activity) | Gratuito e Premium
[Relatório de auditoria](#audit-report) | Gratuito e Premium
[Relatório de atividade de grupos](#groups-activity-report) | Premium
[Relatório de atividade de registro de redefinição de senha](#password-reset-registration-activity-report) | Premium
[Atividade de redefinição de senha](#password-reset-activity) | Premium
 

### Entradas de fontes desconhecidas

| Descrição | Local do relatório |
| :-------------     | :-------        |
| <p>Este relatório indica os usuários que realizaram uma entrada bem-sucedida em seu diretório enquanto estava atribuído a eles um endereço IP de cliente que foi reconhecido pela Microsoft como um endereço IP de proxy anônimo. Esses proxies são geralmente usados pelos usuários que desejam ocultar o endereço IP dos seus computadores, e podem ser usados de modo mal-intencionado – às vezes, os hackers usam esses proxies. </p><p> Os resultados desse relatório mostrarão o número de vezes que um usuário realizou uma entrada bem-sucedida em seu diretório por meio daquele endereço e o endereço IP do proxy.</p> | Diretório > guia Relatórios |

### Entradas após várias falhas

| Descrição | Local do relatório |
| :-------------     | :-------        |
| Este relatório indica os usuários que obtiveram êxito após várias tentativas de entrada malsucedidas consecutivas. As possíveis causas incluem: <ul><li>o usuário esqueceu sua senha</li><li>o usuário for vítima de um ataque de detecção de senha com força bruta bem-sucedido</li></ul><p>os resultados desse relatório mostrarão o número de falhas consecutivas em tentativas de entrada feitas antes da entrada bem-sucedida e um carimbo de data/hora associado à primeira entrada bem-sucedida.</p><p><b>Configurações de relatório</b>: você pode configurar o número mínimo de falhas consecutivas nas tentativas que precisam ocorrer antes que ela possa ser exibida no relatório. Quando você fizer alterações nessa configuração, é importante observar que essas alterações não serão aplicadas a qualquer entrada com falha existente exibido no momento no relatório existente. Todavia, elas serão aplicadas a todas as futuras entradas. Alterações a esse relatório só podem ser feitas por administradores licenciados. | Diretório > guia Relatórios |

### Entradas de várias regiões geográficas

| Descrição | Local do relatório |
| :-------------     | :-------        |
| <p>Este relatório inclui atividades de entrada bem-sucedidas por um usuário, quando duas entradas parecem ter sido originadas de diferentes regiões e a diferença de tempo entre as duas entradas torna impossível para o usuário ter viajado entre tais regiões. Possíveis causas incluem:</p><ul><li>o usuário está compartilhando sua senha</li><li>o usuário está usando uma área de trabalho remota para iniciar um navegador da Web para entrar</li><li>um hacker entrou na conta de um usuário de um país diferente.</li></ul><p>Os resultados desse relatório mostrarão eventos de entrada bem-sucedida, juntamente com a diferença de tempo entre as entradas, as regiões das quais as entradas parecem ter se originado e o tempo estimado para deslocamento entre as regiões.</p><p>O tempo de deslocamento mostrado é apenas uma estimativa e pode ser diferente do tempo de deslocamento real entre os locais. Além disso, não há eventos gerados para entradas entre regiões vizinhas.</p> | Diretório > guia Relatórios |

### Entradas de endereços IP com atividade suspeita

| Descrição | Local do relatório |
| :-------------     | :-------        |
| <p>Este relatório inclui tentativas de entrada realizadas de endereços IP em que atividades suspeitas foram observadas. Atividades suspeitas incluem muitas tentativas de entrada com falha do mesmo endereço IP em um curto período de tempo e outras atividades consideradas suspeitas. Isso pode indicar que um hacker tentou entrar usando esse endereço IP.</p><p>Os resultados desse relatório mostrará a você tentativas de entrada que foram originadas de um endereço IP em que foi observada atividade suspeita, junto com o carimbo de data/hora associado à entrada.</p> | Diretório > guia Relatórios |

### Atividade de entrada anômala

| Descrição | Local do relatório |
| :-------------     | :-------        |
| <p>Este relatório inclui entradas identificadas como "anômalas" pelos nossos algoritmos de aprendizado de máquina. Os motivos para marcar uma tentativa de entrada como irregular incluem locais de entrada inesperados, horários e locais inesperados ou uma combinação desses elementos. Isso pode indicar que um hacker tentou entrar usando essa conta. O algoritmo de aprendizado de máquina classifica os eventos como "anômalos" ou "suspeitos", em que "suspeito" indica alta probabilidade de uma violação de segurança.</p><p>Os resultados desse relatório mostrarão essas entradas, junto com a classificação, local e um carimbo de data/hora associado a cada entrada.</p><p>Nós enviaremos um email de notificação para os administradores globais se encontrarmos 10 ou mais eventos anômalos de entrada em um período de 30 dias ou menos. Certifique-se de incluir aad-alerts-noreply@mail.windowsazure.com na sua lista de remetentes seguros.</p> | Diretório > guia Relatórios |

### Entradas de dispositivos possivelmente infectados

| Descrição | Local do relatório |
| :-------------     | :-------        |
| <p>Use este relatório quando desejar ver entradas de dispositivos nos quais algum malware (software mal-intencionado) pode estar em execução. Correlacionamos endereços IP de entradas com endereços IP por meio dos quais foi feita uma tentativa de contatar um servidor de malware.</p><p>Recomendação: como este relatório presume que um endereço IP foi associado ao mesmo dispositivo em ambos os casos, é recomendável que você entre em contato com o usuário e faça uma varredura no dispositivo dele para ter certeza.</p><p>Para obter mais informações sobre como tratar de infecções por malware, consulte o [Centro de Proteção contra Malware](http://go.microsoft.com/fwlink/?linkid=335773). </p> | Diretório > guia Relatórios |

### Usuários com atividade de entrada anômala

| Descrição | Local do relatório |
| :-------------     | :-------        |
| <p>Use esse relatório para exibir todas as contas de usuário para a quais foi identificada atividade anômala de entrada. Este relatório inclui dados de todos os outros relatórios de atividade anômala. Os resultados desse relatório mostrarão os detalhes sobre o usuário, a razão pela qual o evento de entrada foi identificado como anômalo, a data e hora e outras informações relevantes sobre o evento.</p> | Diretório > guia Relatórios |

## Relatórios de aplicativos integrados

### Uso do aplicativo: resumo

| Descrição | Local do relatório |
| :-------------     | :-------        |
| Use este relatório quando desejar consultar o uso de todos os aplicativos SaaS em seu diretório. Este relatório está baseado no número de vezes que os usuários clicaram no aplicativo no Painel de Acesso. | Diretório > guia Relatórios |

### Uso do aplicativo: detalhado

| Descrição | Local do relatório |
| :-------------     | :-------        |
| Use este relatório para ver o quanto um aplicativo SaaS específico está sendo usado. Este relatório está baseado no número de vezes que os usuários clicaram no aplicativo no Painel de Acesso. | Diretório > guia Relatórios |

### Painel do aplicativo

| Descrição | Local do relatório |
| :-------------     | :-------        |
| Este relatório indica entradas cumulativas no aplicativo pelos usuários em sua organização, durante um intervalo de tempo selecionado. O gráfico na página do painel ajudará você a identificar tendências para toda a utilização desse aplicativo. | Diretório > Aplicativo > guia Painel |

## Relatórios de erros

### Erros de provisionamento de conta

| Descrição | Local do relatório |
| :-------------     | :-------        |
| Use isto para monitorar os erros que ocorrem durante a sincronização de contas de aplicativos SaaS para o Active Directory do Azure. | Diretório > guia Relatórios |

## Relatórios específicos de usuário

### Dispositivos

| Descrição | Local do relatório |
| :-------------     | :-------        |
| Use este relatório quando desejar ver o endereço IP e a localização geográfica de dispositivos que um usuário específico utilizou para acessar o Active Directory do Azure. | Diretório > Usuários > <i>Usuário</i> > guia Dispositivos |

### Atividade

| Descrição | Local do relatório |
| :-------------     | :-------        |
| Mostra a atividade de entrada de um usuário. O relatório inclui informações como o aplicativo conectado ao dispositivo utilizado, o endereço IP e o local. Não coletamos o histórico de usuários que realizam a entrada com uma conta da Microsoft. | Diretório > Usuários > <i>Usuário</i> > guia Atividade |

#### Inscreva-se em eventos incluídos no relatório de Atividade de Usuário

Somente determinados tipos de eventos de entrada serão exibidos no relatório de Atividade de Usuário.

| Tipo de evento | Incluso? |
| ----------------------								| ---------		|
| Entradas no [Painel de Acesso](http://myapps.microsoft.com/) | Sim |
| Entradas no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com/) | Sim |
| Entradas no [Portal do Microsoft Azure](http://portal.azure.com/) | Sim |
| Entradas no [Portal do Office 365](http://portal.office.com/) | Sim |
| Entradas em um aplicativo nativo, como o Outlook (consulte exceção abaixo) | Sim |
| Entradas em um aplicativo federado/provisionado por meio do Painel de Acesso, como Salesforce | Sim |
| Entradas em um aplicativo baseado em senha por meio do Painel de Acesso, como o Twitter | Não (em breve) |
| Realiza a entrada em um aplicativo de negócios personalizado que foi adicionado ao diretório | Não (em breve) |

> Observação: Para reduzir a quantidade de ruído no relatório, as entradas no aplicativo nativo [Lync/Skype for Business](http://products.office.com/pt-br/skype-for-business/online-meetings) e o [Assistente de Conexão do Microsoft Online Services](http://community.office365.com/pt-br/w/sso/534.aspx) não são mostradas.

## Logs de atividade

### Relatório de auditoria

| Descrição | Local do relatório |
| :-------------     | :-------        |
| Mostra um registro de todos os eventos auditados durante as últimas 24 horas, últimos 7 dias ou últimos 30 dias. <br /> Para obter mais informações, consulte [Eventos de relatório de auditoria do Active Directory do Azure](active-directory-reporting-audit-events.md). | Diretório > guia Relatórios |

### Relatório de atividade de grupos

| Descrição | Local do relatório |
| :-------------     | :-------        |
| Mostra todas as atividades para os grupos gerenciados por autoatendimento em seu diretório. | Diretório > Usuários > <i>Usuário</i> > guia Dispositivos |

### Relatório de atividade de registro de redefinição de senha

| Descrição | Local do relatório |
| :-------------     | :-------        |
| Mostra todos os registros de redefinição de senha que ocorreram na organização | Diretório > guia Relatórios |

### Atividade de redefinição de senha

| Descrição | Local do relatório |
| :-------------     | :-------        |
| Mostra todas as tentativas de redefinição de senha que ocorreram na organização. | Diretório > guia Relatórios |

## Coisas a considerar se você suspeitar de violação de segurança

Se você suspeitar que uma conta de usuário pode estar comprometida ou qualquer tipo de atividade de usuário suspeita que pode levar a uma violação de segurança de seus dados de diretório na nuvem, você talvez queira considerar uma ou mais das seguintes ações:

- Contate o usuário para verificar a atividade
- Redefinir a senha do usuário
- [Habilitar a Multi-Factor Authentication](http://go.microsoft.com/fwlink/?linkid=335774) para segurança adicional

## Exibir ou baixar uma fatura

1. No Portal de Gerenciamento do Azure, clique em **Active Directory** e no nome do diretório da sua organização, então clique em **Relatórios**.
2. Na página Relatórios, clique no relatório que você deseja exibir e/ou baixar.
    >
    > [AZURE.NOTE]
    
3. Clique no menu suspenso ao lado de Intervalo e, em seguida, selecione um dos seguintes intervalos de tempo que deverá ser usado ao gerar este relatório:
    - Últimas 24 horas
    - Últimos 7 dias
    - Últimos 30 dias
4. Clique no ícone de marca de seleção para executar o relatório.
5. Se aplicável, clique em **Baixar** para baixar o relatório em um arquivo compactado no formato de valores separados por vírgulas (CSV) para visualização offline ou para fins de arquivamento.

## Ignorar um evento

Se você estiver exibindo os relatórios de anomalias, perceba que você pode ignorar vários eventos que aparecem em relatórios relacionados. Para ignorar um evento, basta destacar o evento no relatório e, em seguida, clicar em **Ignorar**. O botão **Ignorar** removerá permanentemente o evento realçado do relatório e só pode ser usado por administradores globais licenciados.

## Notificações automáticas por email 

### Quais relatórios geram notificações por email

Neste momento, somente o relatório de Atividade de Entrada Anômala e os usuários com o relatório de Atividade de Entrada Anômala estão usando o sistema de notificação por email.

### O que faz com que a notificação por email seja enviada?

Por padrão, o Active Directory do Azure é configurado para enviar notificações por email automaticamente para todos os administradores globais. O email é enviado sob as condições a seguir, para cada relatório.

Para o relatório de Atividade de Entrada Anômala:

- Fontes desconhecidas: 10 eventos
- Várias falhas: 10 eventos
- Endereços IP com atividade suspeita: 10 eventos
- Dispositivos infectados: 10 eventos

Para os usuários com relatório de Atividade de Entrada Anômala:

- Fontes desconhecidas: 10 eventos
- Várias falhas: 10 eventos
- Endereços IP com atividade suspeita: 10 eventos
- Dispositivos infectados: 5 eventos
- Relatório de entradas anômalas: 15 eventos

O email é enviado se qualquer uma das condições anteriores for atendida dentro de 30 dias, ou desde o envio do último email se for um período inferior a 30 dias.

As Entradas Anômalas são aquelas identificadas como "anômalas" por nossos algoritmos de Aprendizado de Máquina, com base em locais de entrada inesperados, hora do dia e locais de entrada inesperados ou uma combinação desses elementos. Isso pode indicar que um hacker tentou entrar usando essa conta. Mais informações sobre esse relatório podem ser encontradas na tabela acima.

### Quem recebe as notificações por email?

O email é enviado para todos os administradores globais as quais foi atribuída uma licença do Active Directory Premium. Para garantir que ele seja entregue, podemos enviá-lo também ao endereço de Email alternativo dos administradores. Os administradores devem incluir aad-alerts-noreply@mail.windowsazure.com em suas listas de remetentes seguros, para que eles não deixem de ver o email.

### Com que frequência esses emails são enviados?

Depois que um email é enviado, o outro será enviado somente quando forem encontrados 10 ou mais novos eventos de entrada anômala dentro de 30 dias a partir do envio de email. Como acessar o relatório mencionado no email?

Quando você clica no link, será redirecionado à página do relatório no Portal de Gerenciamento do Azure. Para acessar o relatório, você precisa ser ambos:

- Um administrador ou co-administrador de sua assinatura do Azure
- Um administrador global no diretório e ter uma licença do Active Directory Premium atribuída a você. Para obter mais informações, consulte Edições do Active Directory do Azure. 

### Posso desativar esses emails?

Sim, para desativar as notificações relacionadas a entradas anômalas no Portal de Gerenciamento do Azure, clique em **Configurar**, e, em seguida, selecione **Desabilitado** sob a seção **Notificações**.

## O que vem a seguir

- [Introdução ao Azure Active Directory Premium](active-directory-get-started-premium.md)
- [Adicionar identidade visual da empresa às páginas de Entrada e do Painel de acesso](active-directory-add-company-branding.md)

<!---HONumber=58--> 