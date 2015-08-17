<properties
	pageTitle="Azure AD Connect Sync: conceitos técnicos"
	description="Explica os conceitos técnicos do Azure AD Connect Sync."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/27/2015"
	ms.author="markusvi"/>


# Azure AD Connect Sync: conceitos técnicos

O Azure AD Connect Sync tem como base uma plataforma sólida de sincronização de metadiretório.<br> As seções a seguir apresentam os conceitos de sincronização de metadiretório. Complementando MIIS, ILM e FIM, os Serviços de Sincronização do Active Directory do Azure fornecem a próxima plataforma para conexão a fontes de dados, realizando a sincronização de dados entre diferentes fontes e o provisionamento e desprovisionamento de identidades.

![Conceitos técnicos][1]
 


As seções a seguir fornecem mais detalhes sobre os seguintes aspectos do Serviço de Sincronização FIM:

- Conector 
- Fluxo de atributos 
- Espaço conector 
- Metaverso 
- Provisionamento 

 




## Conector

Os módulos de código que são usados para comunicação com uma fonte de dados conectada são chamados de conectores (anteriormente conhecidos como MAs (agentes de gerenciamento)).<br> Eles são instalados no computador que executa o Azure AD Connect Sync. Os conectores fornecem a capacidade de conversar sem agentes usando protocolos de sistema remoto, em vez de depender da implantação de agentes especializados. Isso significa que o risco e tempos de implantação reduzidos, especialmente ao lidar com sistemas e aplicativos críticos.

Na figura acima, o conector é sinônimo de espaço conector, mas abrange toda a comunicação com o sistema externo.

O conector é responsável por todas as funcionalidades de importação e exportação no sistema e libera os desenvolvedores da necessidade de compreender como conectar a cada sistema nativamente ao usar provisionamento declarativo para personalizar as transformações de dados. Importações e exportações ocorrem apenas quando agendadas, permitindo mais isolamento de alterações que ocorrem no sistema, desde que as alterações não sejam propagadas automaticamente para a fonte de dados conectada. Além disso, os desenvolvedores também podem criar seus próprios conectores para conectar a praticamente qualquer fonte de dados.
 




## Fluxo de atributos

O metaverso é a exibição consolidada de todas as identidades associadas de espaços conectores vizinhos. Na figura acima, o fluxo de atributos é representado por linhas com pontas de seta tanto para fluxo de entrada quanto de saída. O fluxo de atributos é o processo de cópia ou transformação de dados de um sistema para outro e todos os fluxos de atributos (de entrada ou saída).

O fluxo de atributos ocorre bidirecionalmente entre o metaverso e o espaço conector, quando as operações de sincronização (completa ou delta) estão agendadas para execução. O fluxo de atributos ocorre apenas quando essas sincronizações são executadas. Fluxos de atributos são definidos nas Regras de Sincronização. Eles podem ser entrada (ISR na imagem anterior) ou saída (OSR na figura acima).
 

## Espaço conector

Cada fonte de dados conectada é representada como um subconjunto filtrado dos objetos e atributos no espaço conector. Isso permite que o serviço de sincronização funcione localmente sem a necessidade de entrar em contato com o sistema remoto ao sincronizar os objetos; além disso, restringe a interação a apenas importações e exportações.

Quando a fonte de dados e o conector têm a capacidade de fornecer uma lista de alterações (uma importação de delta); em seguida, a eficiência operacional aumenta drasticamente, já que apenas as alterações desde o último ciclo de sondagem são trocadas. O espaço conector isola a fonte de dados conectada de propagação automática de alterações, ao exigir que o conector agende importações e exportações. Essa garantia adicional lhe dá tranquilidade ao testar, visualizar ou confirmar a próxima atualização.
 




## Metaverso

O metaverso é a exibição consolidada de todas as identidades associadas de espaços conectores vizinhos. Conforme as identidades são vinculadas uma à outra e atribui-se autoridade a vários atributos por meio de mapeamentos de fluxo de importação, o objeto metaverso central começa a agregar informações de vários sistemas. Desse fluxo de atributos de objeto, mapeamentos transportam informações para sistemas de saída. Objetos são criados quando um sistema autoritativo projeta-os no metaverso. Assim que todas as conexões são removidas, o objeto do metaverso é excluído. Objetos no metaverso não podem ser editados diretamente. Todos os dados do objeto devem ser fornecidos por meio de fluxo de atributos. O metaverso mantém conectores persistentes com cada espaço conector. Esses conectores não exigem reavaliação para cada execução de sincronização. Isso significa que o AADsync não precisa localizar o objeto remoto correspondente a cada vez. Isso evita a necessidade de usar agentes caros para que se possa evitar alterações em atributos que, normalmente, seriam responsáveis por correlacionar os objetos. Ao descobrir novas fontes de dados que podem ter objetos preexistentes que precisam ser gerenciados, o AADSync usa um processo chamado regra de junção para avaliar os candidatos em potencial com os quais deseja estabelecer um vínculo. Quando o vínculo é estabelecido, essa avaliação não ocorre novamente e o fluxo de atributos normal pode ocorrer entre a fonte de dados conectada remota e o metaverso.
 




## Provisionamento

Quando uma fonte autoritativa projeta um novo objeto no metaverso, um novo objeto de espaço conector pode ser criado em outro conector, representando uma fonte de dados conectada downstream. <br> Isso inerentemente estabelece um vínculo e o fluxo de atributo pode prosseguir bidirecionalmente.

Sempre que uma regra determina que um novo objeto de espaço conector precisa ser criado, ele é chamado de provisionamento. No entanto, como essa operação só ocorre dentro do espaço conector, ele não se transfere para a fonte de dados conectada até que uma exportação seja executada.



## Recursos adicionais

* [Azure AD Connect Sync: personalizando opções de sincronização](active-directory-aadconnectsync-whatis.md)
* [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)
 
<!--Image references-->
[1]: ./media/active-directory-aadsync-technical-concepts/ic750598.png

<!---HONumber=August15_HO6-->