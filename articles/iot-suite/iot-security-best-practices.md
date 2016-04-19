<properties
 pageTitle="Práticas Recomendadas de Segurança de IoT | Microsoft Azure"
 description="Práticas recomendadas de segurança para proteger sua infraestrutura de IoT"
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="YuriDio"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/05/2016"
 ms.author="yurid"/>

# Práticas recomendadas de segurança de IoT (Internet das Coisas)

Proteger uma infraestrutura de IoT requer uma estratégia de segurança em camadas rigorosa. Desde proteger os dados na nuvem até proteger a integridade dos dados em trânsito na Internet pública e fornecendo a capacidade de provisionar dispositivos com segurança, cada camada agrega maior garantia de segurança à infraestrutura total.

## Proteger uma infraestrutura de IoT
 
Essa estratégia de segurança em camadas pode ser desenvolvida e executada com a participação ativa de vários participantes envolvidos na produção, desenvolvimento e implantação de dispositivos e infraestrutura de IoT. Veja a seguir uma descrição de alto nível desses participantes.

- **Fabricante/integrador de hardware de IoT**: normalmente esses são os fabricantes de hardware de IoT que está sendo implantado, o integrador de hardware que monta hardware de vários fabricantes ou o fornecedores de hardware que fornece hardware para uma implantação de IoT fabricado ou integrado por outros fornecedores.
- **Desenvolvedor de soluções IoT**: o desenvolvimento de uma solução IoT normalmente é feito por um desenvolvedor de soluções, que pode ser parte de uma equipe interna ou um SI (Integrador de Sistema) especializado nesta atividade. O desenvolvedor de soluções IoT pode desenvolver vários componentes da solução IoT do zero, integrar vários componentes prontos ou de software livre, ou então adotar soluções pré-configuradas com pequenas adaptações.
- **Implantador de solução IoT**: depois que uma solução IoT é desenvolvida, ela precisa ser implantada em campo. Isso envolve a implantação de hardware, interconexão de dispositivos e implantação de soluções nos dispositivos de hardware ou na nuvem.
- **Operador de solução IoT**: quando a solução IoT é implantada, ela requer operações, monitoramento, atualizações e manutenção de longo prazo. Isso pode ser feito por uma equipe interna composta por especialistas em tecnologia da informação, equipes de operações e manutenção de hardware e especialistas em domínio que monitoram o comportamento correto da infraestrutura geral de IoT. 

As seções a seguir fornecem práticas recomendadas para cada um desses participantes para desenvolver, implantar e operar uma infraestrutura de IoT segura.

## Fabricante/integrador de hardware de IoT

Siga as práticas recomendadas a seguir se você for um fabricante ou integrador de hardware de IoT:

- **Escopo de hardware para requisitos mínimos**: o design de hardware deve incluir o mínimo de recursos necessário para a operação de hardware e nada mais. Um exemplo é incluir portas USB somente se necessário para a operação do dispositivo. Esses recursos adicionais expõem o dispositivo a vetores de ataque indesejados, o que deve ser evitado. 
- **Tornar o hardware à prova de adulteração**: mecanismo interno para detectar a violação física do hardware, tal como a abertura da tampa ou remoção de parte dele, etc. Esses sinais de violação podem fazer parte do fluxo de dados carregado para a nuvem, permitindo que os alertar tais eventos para os operadores. 
- **Criado em hardware seguro**: se COGS permitir, criar recursos de segurança como as funcionalidades de inicialização baseadas no armazenamento seguro e criptografado e no TPM (Trusted Platform Module). Esses recursos tornam os dispositivos mais seguros protegendo a infraestrutura geral de IoT.
- **Tornar as atualizações seguras**: atualizar o firmware durante o tempo de vida do dispositivo é inevitável. Criar dispositivos com caminhos seguros para atualizações e garantia criptográfica da versão de firmware permitirá que o dispositivo fique seguro durante e após atualizações.

## Desenvolvedor de soluções IoT

Siga as práticas recomendadas a seguir se você for um desenvolvedor de solução IoT:

- **Seguir a metodologia de desenvolvimento de software seguro**: desenvolver software seguro requer uma consideração inicial desde a concepção do projeto até sua implementação, teste e implantação. A escolha de plataformas, linguagens e ferramentas são influenciados por essa metodologia. O Microsoft Security Development Lifecycle fornece uma abordagem passo a passo para a criação de software seguro.
- **Escolher software livre com cuidado**: software livre fornece uma oportunidade o rápido desenvolvimento de soluções. Ao escolher o software livre, considere o nível de atividade da comunidade para cada componente de software livre. Uma comunidade ativa garante que existirá suporte para o software; os problemas serão descobertos e tratados. Como alternativa, um software livre obscuro e inativo não terá suporte e os problemas provavelmente não serão descobertos.
- **Integrar com cuidado**: há muitas das falhas de segurança de software no limite de bibliotecas e APIs. Funcionalidades que podem não ser necessárias para a implantação atual ainda podem estar disponíveis por meio de uma camada de API. Verificar se todas as interfaces de componentes que estão sendo integradas são seguros garante a segurança geral.      

## Implantador de soluções IoT

Siga as práticas recomendadas a seguir se você for um implantador de solução IoT:

- **Implantar o hardware com segurança**: implantações de IoT podem exigir que o hardware seja implantado em locais não seguros, como espaços públicos ou localidades sem supervisão. Em tais situações, certifique-se de que a implantação de hardware seja à prova de adulteração o máximo possível. Se USB ou outras portas estiverem disponíveis no hardware, certifique-se de que elas estejam cobertas com segurança. Muitos vetores de ataque podem usá-las como ponto de entrada para ataques.
- **Manter as chaves de autenticação em segurança**: durante a implantação, cada dispositivo requer IDs de dispositivo e chaves de autenticação associadas geradas pelo serviço de nuvem. Mantenha essas chaves fisicamente seguras mesmo após a implantação. Qualquer chave comprometida pode ser usada por um dispositivo mal-intencionado passando-se por um dispositivo existente.

## Operador de solução IoT

Siga as práticas recomendadas a seguir se você for um operador de solução IoT:

- **Manter o sistema atualizado**: verifique se todos os sistemas operacionais e drivers do dispositivo estão atualizados para as versões mais recentes. O Windows 10 (IoT ou outras SKUs), com as atualizações automáticas ativadas, é mantido atualizado pela Microsoft, fornecendo um sistema operacional seguro para dispositivos IoT. Para outros sistemas operacionais, como o Linux, mantê-los atualizados garante que eles também estejam protegidos contra ataques mal-intencionados. 
- **Proteger contra atividades mal-intencionadas**: se o sistema operacional permitir, aplique as funcionalidades antivírus e antimalware mais recentes em cada sistema operacional do dispositivo. Isso pode ajudar a atenuar a maioria das ameaças externas. A maioria dos sistemas operacionais mais modernos, como o Windows 10 IoT e o Linux, podem ser protegidos contra essa ameaça seguindo as etapas apropriadas. 
- **Auditar frequentemente**: auditar problemas relacionados à infraestrutura de IoT é essencial ao responder a incidentes de segurança. A maioria dos sistemas operacionais, como o Windows 10 (IoT e outros SKUs), fornece o log de eventos internos que deve ser analisado com frequência para certificar-se de que não ocorreu nenhuma violação de segurança. Informações de auditoria podem ser enviadas como um fluxo de telemetria separado para o serviço de nuvem e analisadas.
- **Proteger fisicamente a infraestrutura de IoT**: os pior ataques de segurança contra infraestrutura de são iniciados usando o acesso físico aos dispositivos. Proteger contra uso mal-intencionado de USB, portas e demais acessos físicos é uma prática de segurança importante. Registrar o acesso físico em log, tal como o uso da porta USB, é fundamental para descobrir qualquer violação que possa ter ocorrido. Novamente, o Windows 10 (IoT e outros SKUs) habilita o registro em log detalhado desses eventos.
- **Proteger credenciais da nuvem**: credenciais de autenticação da nuvem usadas para configurar e operar uma implantação de IoT são possivelmente a maneira mais fácil para acessar e comprometer um sistema de IoT. Proteja as credenciais alterando a senha com frequência e não usando essas credenciais em computadores públicos. 

As funcionalidades de diferentes dispositivos IoT variam. Por um lado, alguns dispositivos podem ser computadores completos que executam comuns sistemas operacionais de desktop comuns, por outro lado, alguns dispositivos podem executar sistemas operacionais muito leves. As práticas recomendadas de segurança descritas acima podem ser aplicáveis a esses dispositivos em graus variados. Se fornecidas, as práticas recomendadas de segurança e de implantação indicadas pelo fabricante desses dispositivos devem ser seguidas.

Alguns dispositivos herdados e restritos podem não ter sido projetados especificamente para a implantação de IoT. Esses dispositivos podem não ter a capacidade de criptografar dados, conectar-se à Internet, fornecer auditoria avançada, etc. Nesses casos, usar um gateway de campo moderno e seguro para agregar dados de dispositivos herdados pode fornecer a segurança necessária para conectar esses dispositivos pela Internet. Nesse caso, os gateways de campo fornecem autenticação segura, negociação de sessões criptografadas, recebimento de comandos da nuvem e muitos outros recursos de segurança.

<!---HONumber=AcomDC_0406_2016-->