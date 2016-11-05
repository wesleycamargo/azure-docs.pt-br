---
title: Exibir e gerenciar alertas de Matriz Virtual StorSimple | Microsoft Docs
description: Descreve as condições de alerta e gravidade da Matriz Virtual StorSimple, e como usar o serviço StorSimple Manager para gerenciar alertas.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''

ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/07/2016
ms.author: alkohli

---
# Usar o serviço StorSimple Manager para exibir e gerenciar alertas para o StorSimple Virtual Array.
## Visão geral
A guia **Alertas** no serviço StorSimple Manager fornece uma maneira para examinar e limpar os alertas relacionados à Matriz Virtual do StorSimple em tempo real. Nessa guia, você pode monitorar centralmente os problemas de integridade de suas matrizes virtuais do StorSimple (também conhecidas como dispositivos locais virtuais do StorSimple) e a solução geral do Microsoft Azure StorSimple.

Este tutorial descreve como configurar notificações de alerta, condições de alerta comuns, níveis de gravidade do alerta e como exibir e controlar os alertas. Além disso, ele inclui tabelas de referência rápida de alerta, que permitem que você localize um alerta específico rapidamente e responda o mesmo adequadamente.

![Página de alertas](./media/storsimple-ova-manage-alerts/alerts1.png)

## Configurar definições de alerta
Você pode escolher se deseja ser notificado por email das condições de alerta para cada um dos dispositivos virtuais StorSimple. Além disso, você pode identificar outros destinatários da notificação de alerta, digitando seus endereços de email na caixa **Outros destinatários de email**, separados por ponto e vírgula.

> [!NOTE]
> Você pode inserir um máximo de 20 endereços de email por dispositivo virtual.
> 
> 

Depois de habilitar a notificação por email para um dispositivo virtual, os membros da lista de notificação receberão uma mensagem de email sempre que um alerta crítico ocorrer. As mensagens serão enviadas por *storsimple-alerts-noreply@mail.windowsazure.com* e descreverão a condição de alerta. Os destinatários podem clicar em **Cancelar inscrição** para serem removidos da lista de notificação de email.

#### Para habilitar a notificação por email de alertas para um dispositivo virtual
1. Vá para **Dispositivos** > **Configuração** para o dispositivo virtual. Vá para a seção **Configurações de alerta**.
   
    ![configurações de alerta](./media/storsimple-ova-manage-alerts/alerts2.png)
2. Em **Configurações de Alerta**, defina o seguinte:
   
   1. No campo **Enviar notificação por email**, selecione **SIM**.
   2. No campo **Administradores do serviços de email**, selecione **SIM** se desejar que o administrador de serviços e todos os coadministradores recebam as notificações de alerta.
   3. No campo **Outros destinatários de email**, insira os endereços de email de todos os outros destinatários que devem receber as notificações de alerta. Insira os nomes no formato *someone@somewhere.com*. Use ponto e vírgula para separar os endereços de email. Você pode configurar um máximo de 20 endereços de email por dispositivo virtual.
      
       ![configuração da notificação de alertas](./media/storsimple-ova-manage-alerts/alerts3.png)
3. Na parte inferior da página, clique em **Salvar** para salvar sua configuração.
4. Para enviar uma notificação de email de teste, clique no ícone de seta ao lado de **Enviar email de teste**. O serviço StorSimple Manager exibirá mensagens de status conforme ele encaminha a notificação de teste.
5. Quando a seguinte mensagem for exibida, clique em **OK**.
   
    ![Email de notificação de teste de alertas enviado](./media/storsimple-ova-manage-alerts/alerts4.png)
   
   > [!NOTE]
   > Se a mensagem de notificação de teste não pode ser enviada, o serviço StorSimple Manager exibirá uma mensagem apropriada. Clique em **OK**, aguarde alguns minutos e, em seguida, tente enviar a mensagem de notificação de teste novamente.
   > 
   > 
   
    A mensagem de notificação de teste será semelhante ao seguinte.
   
    ![Exemplo de email de teste de alertas](./media/storsimple-ova-manage-alerts/alerts5.png)

## Condições comuns de alerta
A matriz Virtual StorSimple gera alertas em resposta a uma variedade de condições. A seguir estão os tipos mais comuns de condições de alerta:

* **Problemas de conectividade** – esses alertas ocorrem quando há dificuldade na transferência de dados. Podem ocorrer problemas de comunicação durante a transferência de dados de e para a conta de armazenamento do Azure ou devido à falta de conectividade entre os dispositivos virtuais e o serviço StorSimple Manager. Problemas de comunicação são alguns dos mais difíceis de corrigir porque há muitos pontos de falha. Você deve sempre verificar primeiro se a conectividade de rede e o acesso à Internet estão disponíveis antes de continuar com a solução de problemas mais avançados. Para obter informações sobre portas e configurações de firewall, vá para [Requisitos do sistema StorSimple Virtual Array](storsimple-ova-system-requirements.md). Para obter ajuda com a solução de problemas, acesse [Solucionar problemas com o cmdlet Test-Connection](storsimple-troubleshoot-deployment.md).
* **Problemas de desempenho** – esses alertas são causados quando o sistema não estiver sendo executado corretamente, como quando ele está sob uma carga pesada.

Além disso, você pode ver alertas relacionados à segurança, atualizações ou falhas no trabalho.

## Níveis de severidade do alerta
Alertas têm diferentes níveis de severidade, dependendo do impacto que terá a situação de alerta e a necessidade de uma resposta ao alerta. Os níveis de severidade são:

* **Crítico** – esse alerta ocorre em resposta a uma condição que está afetando o desempenho correto do seu sistema. Uma ação é necessária para garantir que o serviço StorSimple não seja interrompido.
* **Aviso** – essa condição poderá se tornar crítica se não for resolvida. Você deve investigar a situação e realizar qualquer ação necessária para resolver o problema.
* **Informações** – esse alerta contém informações que podem ser úteis para controlar e gerenciar seu sistema.

## Exibir e controlar os alertas
O painel do serviço StorSimple Manager fornece uma visão rápida do número de alertas em seus dispositivos virtuais, organizados por nível de gravidade.

![Painel de alertas](./media/storsimple-ova-manage-alerts/alerts6.png)

Clicar no nível de severidade abre a guia **Alertas**. Os resultados incluem apenas os alertas que correspondem a esse nível de severidade.

![Relatórios de alerta limitados ao tipo de alerta](./media/storsimple-ova-manage-alerts/alerts7.png)

Clicar em um alerta na lista fornece detalhes adicionais para o mesmo, incluindo a última vez em que o alerta foi relatado, o número de ocorrências do alerta no dispositivo e a ação recomendada para resolver o alerta.

Você pode copiar os detalhes do alerta para um arquivo de texto se precisar enviar as informações ao Suporte da Microsoft. Depois de seguir a recomendação e resolver localmente a condição de alerta, você deverá limpar o alerta selecionando-o na guia **Alertas** e clicando em **Limpar**. Para limpar vários alertas, selecione cada alerta, clique em qualquer coluna exceto na coluna **alerta** e clique em **Limpar** após ter selecionado todos os alertas a serem limpos. Observe que alguns alertas são removidos automaticamente quando o problema é resolvido ou quando o sistema atualiza o alerta com novas informações.

Ao clicar em **Limpar**, você terá a oportunidade de fornecer comentários sobre o alerta e as etapas que você seguiu para resolver o problema.

![comentários de alerta](./media/storsimple-ova-manage-alerts/clear-alert.png)

Clique no ícone de verificação ![check-icon](./media/storsimple-ova-manage-alerts/check-icon.png) para salvar seus comentários.

Alguns eventos serão removidos pelo sistema se outro evento for disparado com novas informações. Nesse caso, você verá a seguinte mensagem de erro.

![Limpar mensagem de alerta](./media/storsimple-ova-manage-alerts/alerts8.png)

## Classificação e análise de alertas
A guia **Alertas** pode exibir até 250 alertas. Se você excedeu o número de alertas, nem todos os alertas serão exibidos no modo de exibição padrão. Você pode combinar os campos a seguir para personalizar quais alertas são exibidos:

* **Status**– Você pode exibir alertas **Ativos** ou **Removidos**. Alertas ativos ainda estão sendo disparados em seu sistema, embora os alertas removidos tenham sido limpos manualmente por um administrador ou por meio de programação porque o sistema atualizou a condição de alerta com novas informações.
* **Severidade**– Você pode exibir alertas de todos os níveis de severidade (críticos, avisos, informações) ou apenas uma determinada severidade, como, por exemplo, somente alertas críticos.
* **Fonte**– Você pode exibir alertas de todas as fontes ou limitar os alertas para aqueles que vêm de serviço ou de um ou todos os dispositivos virtuais.
* **Intervalo de tempo**– Especificando os carimbos de data e hora **De** e **Para**, você pode examinar os alertas durante o período de tempo desejado.

## Referência rápida de alertas
As tabelas a seguir listam alguns dos alertas do Microsoft Azure StorSimple que você pode encontrar, bem como recomendações e informações adicionais quando disponíveis. Os alertas do dispositivo virtual StorSimple se encaixam em uma das seguintes categorias:

* [Alertas de conectividade de nuvem](#cloud-connectivity-alerts)
* [Alertas de configuração](#configuration-alerts)
* [Alertas de falha nos trabalhos](#job-failure-alerts)
* [Alertas de desempenho](#performance-alerts)
* [Alertas de segurança](#security-alerts)
* [Alertas de atualização](#update-alerts)

### Alertas de conectividade de nuvem
| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| O dispositivo *<device name>* não está conectado à nuvem. |O dispositivo nomeado não pode se conectar à nuvem. |Não foi possível conectar-se à nuvem. Isso pode ser devido a um dos seguintes:<ul><li>Pode haver um problema com as configurações de rede no seu dispositivo.</li><li>Pode haver um problema com as credenciais da conta de armazenamento.</li></ul>Para obter mais informações sobre como solucionar problemas de conectividade, vá para a [interface do usuário da web local](storsimple-ova-web-ui-admin.md) do dispositivo. |

### Alertas de configuração
| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| Configuração do dispositivo virtual local sem suporte. |Desempenho lento. |A configuração atual pode resultar em degradação de desempenho. Certifique-se de que o servidor atenda aos requisitos mínimos de configuração. Para saber mais, acesse os [requisitos do StorSimple Virtual Array](storsimple-ova-system-requirements.md). |
| Você está com pouco espaço em disco provisionado em <*nome do dispositivo*>. |Aviso de espaço em disco. |Você está com pouco espaço em disco provisionado. Para liberar espaço, considere mover cargas de trabalho para outro volume ou compartilhar ou excluir dados. |

### Alertas de falha nos trabalhos
| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| O backup de <*nome do dispositivo*> não pôde ser concluído. |Falha no trabalho de backup. |Não foi possível criar um backup. Considere o seguinte:<ul><li>Problemas de conectividade podem estar impedindo que a operação de backup seja concluída com êxito. Certifique-se de que não haja nenhum problema de conectividade. Para saber mais sobre como solucionar problemas de conectividade, vá para a [Interface do usuário da Web local](storsimple-ova-web-ui-admin.md) para seu dispositivo virtual.</li><li>Você atingiu o limite de armazenamento disponível. Para liberar espaço, considere a exclusão de todos os backups que não são mais necessários.</li></ul> Resolva os problemas, limpe o alerta e repita a operação. |
| A restauração de <*nome do dispositivo*> não pôde ser concluída. |Falha no trabalho de restauração. |Não foi possível restaurar do backup. Considere o seguinte:<ul><li>sua lista de backup pode não ser válida. Atualize a lista para verificar se ela ainda é válida.</li><li>Problemas de conectividade podem estar impedindo que a operação de restauração seja concluída com êxito. Certifique-se de que não haja nenhum problema de conectividade.</li><li>Você atingiu o limite de armazenamento disponível. Para liberar espaço, considere a exclusão de todos os backups que não sejam mais necessários.</li></ul>Resolva os problemas, limpe o alerta e repita a operação de restauração. |
| A clonagem de <*nome do dispositivo*> não pôde ser concluída. |Falha no trabalho de clonagem. |Não foi possível criar um clone. Considere o seguinte:<ul><li>sua lista de backup pode não ser válida. Atualize a lista para verificar se ela ainda é válida.</li><li>Problemas de conectividade podem estar impedindo que a operação de clonagem seja concluída com êxito. Certifique-se de que não haja nenhum problema de conectividade.</li><li>Você atingiu o limite de armazenamento disponível. Para liberar espaço, considere a exclusão de todos os backups que não são mais necessários.</li></ul>Resolva os problemas, limpe o alerta e repita a operação. |

### Alertas de desempenho
| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| Você está sofrendo atrasos inesperados na transferência de dados. |Transferência de dados lenta. |Erros de limitação acontecem quando você excede os alvos de escalabilidade de um serviço de armazenamento. O serviço de armazenamento faz isso para garantir que nenhum cliente ou locatário possa usar o serviço a custos de outros. Para saber mais sobre como solucionar problemas de sua conta de armazenamento do Azure, vá para [Monitorar, diagnosticar e solucionar problemas do Armazenamento do Microsoft Azure](../storage/storage-monitoring-diagnosing-troubleshooting.md). |
| Há pouco espaço local de reserva em disco no <*nome do dispositivo*>. |Tempo de resposta lento. |10% do tamanho total provisionado para <*nome do dispositivo*> é reservado no dispositivo local e agora está acabando o espaço reservado. A carga de trabalho de <*nome do dispositivo*> está gerando uma maior taxa de rotatividade ou você pode ter migrado recentemente uma grande quantidade de dados. Isso pode resultar em desempenho reduzido. Considere uma das ações a seguir para resolver esse problema:<ul><li>aumentar a largura de banda de nuvem para este dispositivo.</li><li>Reduzir ou mover cargas de trabalho para outro volume ou compartilhamento.</li></ul> |

### Alertas de segurança
| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| A senha de <*nome do dispositivo*> expirará em <*número*> dias. |Aviso de senha. |Sua senha expirará em < número> dias. Considere alterar sua senha. Para saber mais, vá para [Alterar a senha de administrador do dispositivo StorSimple Virtual Array](storsimple-ova-change-device-admin-password.md). |

### Alertas de atualização
| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| Novas atualizações estão disponíveis para seu dispositivo. |Estão disponíveis atualizações para a Matriz Virtual StorSimple. |Você pode instalar novas atualizações da página **Manutenção**. |
| Não foi possível verificar novas atualizações do <*nome do dispositivo*>. |Falha de atualização. |Ocorreu um erro durante a instalação de novas atualizações. Você pode instalar manualmente as atualizações. Se o problema persistir, contate o [Suporte da Microsoft](storsimple-contact-microsoft-support.md). |

## Próximas etapas
* [Saiba mais sobre o StorSimple Virtual Array](storsimple-ova-overview.md).

<!---HONumber=AcomDC_0622_2016-->
