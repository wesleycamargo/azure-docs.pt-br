<properties 
   pageTitle="Notas de versão da StorSimple Virtual Arraye | Microsoft Azure"
   description="Descreve os problemas em aberto críticos e resoluções para a StorSimple Virtual Array."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/13/2016"
   ms.author="alkohli" />

# Notas de versão da StorSimple Virtual Array(visualização) 

## Visão geral

As notas de versão a seguir identificam os problemas críticos abertos da versão de Visualização Pública de dezembro de 2015 da Matriz Virtual Microsoft Azure StorSimple (também conhecida como o dispositivo virtual local do StorSimple ou o dispositivo virtual StorSimple).

As notas de versão são continuamente atualizadas e, à medida que são descobertas questões críticas que exijam uma solução alternativa, elas são adicionadas. Examine cuidadosamente as informações contidas nas notas de versão antes de implantar seu dispositivo virtual StorSimple.

>[AZURE.IMPORTANT]
>
>- A StorSimple Virtual Array está em visualização e é destinada para fins de planejamento de implantação e avaliação. Não há suporte para a instalação dessa visualização em um ambiente de produção. 
>
>- Se você tiver quaisquer problemas com a StorSimple Virtual Array, publique-os no [fórum do MSDN do StorSimple](https://social.msdn.microsoft.com/Forums/home?forum=StorSimple).

A tabela a seguir fornece um resumo dos problemas conhecidos nesta versão.

| Nº| Recurso | Problema | Solução alternativa/comentários |
|----|---------|-------|---------------------|                                                                                                                                                                                                                                                      
| **1.** | Atualizações | Os dispositivos virtuais criados na versão de visualização não podem ser atualizados para uma versão de Disponibilidade Geral com suporte.| Esses dispositivos virtuais devem fazer failover para a versão de Disponibilidade Geral usando um fluxo de trabalho de DR (Recuperação de Desastre).|
| **2.** | Compartilhamentos ou volumes em camadas | Não há suporte para bloqueio de intervalo de bytes para aplicativos que funcionam com os volumes em camadas do StorSimple. Se o bloqueio de intervalo de bytes estiver habilitado, a disposição em camadas do StorSimple não funcionará. | As medidas recomendadas incluem:<ul><li> desligar o bloqueio de intervalo de bytes na sua lógica de aplicativo.</li><li> Opte por inserir dados para este aplicativo em volumes fixos localmente em vez de volumes em camadas.</li>*Ressalva*: se estiver usando volumes fixados localmente e o bloqueio de intervalo de bytes estiver habilitado, esteja ciente de que o volume fixo local pode ficar online antes mesmo da restauração ser concluída. Nesses casos, se uma restauração está em andamento, você deve aguardar a restauração ser concluída.|
| **3.** | Compartilhamentos em camadas | Trabalhar com arquivos grandes pode resultar em uma divisão em camadas lenta. | Ao trabalhar com arquivos grandes, certifique-se de que o maior arquivo é menor do que 3% do tamanho do compartilhamento.|
| **4.** | Volumes ou compartilhamentos fixados localmente | A capacidade de uso de um compartilhamento ou volume fixado localmente é 85-90% da capacidade provisionada. O intervalo de 10 a 15% é reservado para os metadados. | Você precisará provisionar um volume de tamanho maior para obter a capacidade utilizável desejada. Por exemplo, para uma capacidade utilizável de 1 TB, você talvez precise provisionar um volume de 1,15 TB.|
| **5.** | Interface do Usuário da Web local | Se os recursos de segurança aprimorados estão habilitados no Internet Explorer (IE ESC), algumas páginas da interface do usuário da Web local, como **Solução de problemas** ou **Manutenção**, podem não funcionar corretamente. Os botões nessas páginas também podem não funcionar. | Desligue os recursos de segurança reforçada do Internet Explorer.|
| **6.** | Interface do Usuário da Web local | Não há suporte para a localização para interface do usuário da Web local para esta versão. | Isso será implementado em uma versão posterior.|
| **7.** | Interface do Usuário da Web local | Em uma máquina virtual de Hyper-V, as interfaces de rede na interface do usuário da Web são exibidas como interfaces de 10 Gbps. | Esse comportamento é um reflexo do Hyper-V. O Hyper-V sempre mostra 10 Gbps para adaptadores de rede virtual.|                                                                                                 
| **8.** | Recuperação de desastre | Você só pode executar a recuperação de desastres de um servidor de arquivos no mesmo domínio que o dispositivo de origem. Não há suporte nesta versão para a recuperação de desastres para um dispositivo de destino em outro domínio. | Isso será implementado em uma versão posterior.|
| **9.**| Capacidade utilizada para compartilhamentos| Você pode ver o consumo de compartilhamento na ausência de quaisquer dados no compartilhamento. Isso ocorre porque a capacidade utilizada para compartilhamentos inclui metadados. | |                                                                                                                                                                                                                                                         
| **10.** | Configurações de fuso horário| Se o fuso horário é alterado para um dispositivo registrado, a alteração não será refletida nos testes de diagnóstico executados por meio da interface do usuário da Web local. | Esse problema será corrigido em uma versão futura.|
| **11.** | Configurações de rede | Quando um endereço IP estático é configurado para uma interface de rede através da interface do usuário da Web, a alteração de IP ocorre. No entanto, o IP do servidor DNS também é modificado para um endereço de local de site do IPv6. | Esse problema será corrigido em uma versão futura.|
| **12.** | PowerShell do Azure | Os dispositivos virtuais StorSimple não podem ser gerenciados por meio do Azure PowerShell nesta versão. | Todo o gerenciamento dos dispositivos virtuais deve ser feito por meio do portal clássico do Azure e da interface do usuário da Web local.|
| **13.** | Trabalhos | O andamento do trabalho não é granular. O andamento do trabalho pode passar diretamente de 0 para 100%. | Isso será corrigido em uma versão futura.|
| **14.** | Disco de dados provisionado | Após você ter provisionado um disco de dados de um determinado tamanho especificado e criado o dispositivo virtual StorSimple correspondente, você não deve expandir nem reduzir o disco de dados. Tentar fazer isso pode resultar em uma perda de todos os dados nas camadas locais do dispositivo. | |
| **15.** | Gaveta Ajuda | Os tópicos da gaveta Ajuda não estarão disponíveis.| Toda a documentação está disponível por meio do Centro de Download da Microsoft e do site de documentação do Azure. |   

<!---HONumber=AcomDC_0121_2016-->