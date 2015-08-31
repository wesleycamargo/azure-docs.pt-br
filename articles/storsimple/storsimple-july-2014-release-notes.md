<properties 
    pageTitle="Notas de versão da versão de lançamento do StorSimple série 8000 – julho de 2014 | Microsoft Azure"
    description="Descreve os novos recursos, problemas e soluções alternativas da versão de julho de 2014 do Microsoft Azure StorSimple."
    services="storsimple"
    documentationCenter="NA"
    authors="SharS"
    manager="carolz"
    editor="" />
 <tags 
    ms.service="storsimple"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="TBD"
    ms.date="08/19/2015"
    ms.author="v-sharos" />

# Notas de versão de lançamento do StorSimple série 8000 - julho de 2014 

## Visão geral

As notas de versão a seguir identificam os problemas críticos abertos para o StorSimple série 8000 da GA (disponibilidade geral) de julho de 2014 do Microsoft Azure StorSimple. Esta versão corresponde à versão do software 6.3.9600.17215.

Salvo indicação em contrário, estas notas de versão se aplicam a todos os modelos do dispositivo StorSimple. As notas de versão são continuamente atualizadas; à medida que são descobertas questões críticas que exijam uma solução alternativa, elas são adicionadas. Antes de implantar sua solução Microsoft Azure StorSimple, considere as seguintes informações.

## Problemas conhecidos nesta versão
A tabela a seguir fornece um resumo dos problemas conhecidos nesta versão.
 
| Nº | Recurso | Problema | Comentários/soluções alternativas | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | Redefinição de fábrica | Em alguns casos, quando você executa uma redefinição de fábrica, o dispositivo StorSimple pode estar travado e exibe esta mensagem: **a redefinição de fábrica está em andamento (fase 8)**. Isso acontece se você pressionar CTRL + C enquanto o cmdlet estiver em andamento. | Não pressione CTRL + C após iniciar uma redefinição de fábrica. Se você já estiver nesse estado, entre em contato com o Suporte da Microsoft para as próximas etapas. | Sim | Não |
| 2 | Quorum de disco | Em casos raros, se a maioria dos discos no invólucro de EBOD de um dispositivo 8600 for desconectada, fazendo com que não haja quórum de disco, em seguida, o pool de armazenamento ficará offline. Permanecerá offline, mesmo que os discos sejam reconectados. | Você precisará reiniciar o dispositivo. Se o problema persistir, entre em contato com o Suporte da Microsoft para as próximas etapas. | Sim | Não |
| 3 | Falhas de instantâneo de nuvem | Em casos raros, um instantâneo de nuvem pode falhar com o erro **Limite máximo de backup atingido**. Isso ocorre se você exceder 255 clones online no mesmo dispositivo, a partir do mesmo volume original que foi excluído. | | Sim | Sim |
| 4 | ID de controlador incorreta | Quando a substituição do controlador é executada, o controlador 0 pode aparecer como controlador 1. Durante a substituição do controlador, quando a imagem é carregada a partir do nó par, a ID do controlador pode ser exibida inicialmente como a ID do controlador de pares. Em casos raros, esse comportamento pode ser percebido após uma reinicialização do sistema. | Nenhuma ação do usuário é necessária. Esta situação se resolverá depois que a substituição do controlador for concluída. | Sim | Não |
| 5 | Gráficos de monitoramento de dispositivo | No serviço StorSimple Manager, os gráficos de monitoramento de dispositivos não funcionam quando a autenticação Básica ou NTLM é habilitada na configuração do servidor proxy para o dispositivo. | Modifique a configuração de proxy da Web para o dispositivo registrado no serviço StorSimple Manager para que a autenticação seja definida como NENHUMA. Para fazer isso, execute o Windows PowerShell para o cmdlet do StorSimple Set-HcsWebProxy. | Sim | Sim |
| 6 | Contas de armazenamento | Usar o serviço de Armazenamento para excluir a conta de armazenamento é um cenário sem suporte. Isso levará a uma situação na qual os dados do usuário não podem ser recuperados. | | Sim | Sim |
| 7 | Failback | Não há suporte para um failback em até 24 horas após a recuperação de desastres (DR). | | Sim | Não |
| 8 | Failover de dispositivo | Não há suporte para vários failovers de um contêiner de volume do mesmo dispositivo de origem para diferentes dispositivos de destino. O failover de um único dispositivo inativo para vários dispositivos fará com que os contêineres de volume no primeiro dispositivo com failover percam a propriedade dos dados. Após o failover, esses contêineres de volume serão exibidos ou se comportarão de maneira diferente quando forem exibidos no Portal de Gerenciamento. | | Sim | Não |
| 9 | Instalação | Durante o Adaptador StorSimple para instalação do SharePoint, você precisa fornecer um IP do dispositivo para que a instalação seja concluída com êxito. | | Sim | Não |
| 10 | Interfaces de rede | Na versão anterior, as interfaces de rede DADOS 2 e DADOS 3 foram trocadas no software. | Contate o Microsoft Support se você precisa configurar essas interfaces. | Sim | Não |


 

<!---HONumber=August15_HO8-->