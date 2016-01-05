#### Para restaurar seu dispositivo físico para o dispositivo virtual StorSimple

1. Verifique se o contêiner de volume para o qual você deseja fazer o failover associou instantâneos de nuvem.

2. Abra a página **Dispositivo** e então clique na guia **Contêineres de Volume**.

3. Selecione um contêiner de volume para o qual você gostaria de fazer failover para o dispositivo virtual. Clique no contêiner de volume para exibir a lista de volumes no contêiner. Selecione um volume e clique em **Colocar Offline** para colocar o volume offline. Repita este processo para todos os volumes no contêiner de volume.

4. Repita a etapa anterior para todos os contêineres de volume para os quais você gostaria de fazer o failover para outro dispositivo.

5. Na página **Dispositivo**, selecione o dispositivo para o qual você precisa fazer failover e, em seguida, clique em **Failover** para abrir o assistente **Failover de Dispositivo**.

6. Em **Escolher o contêiner de volume para failover**, selecione o volume para o qual você gostaria de fazer failover. Para ser exibido nesta lista, o contêiner de volume deve conter um instantâneo de nuvem e deve estar offline. Se um contêiner de volume que você esperava ver não estiver presente, cancele o assistente e verifique se ele está offline.

7. Na próxima página, em **Escolher um dispositivo de destino para os volumes**, nos contêineres selecionados, selecione o dispositivo virtual na lista suspensa de dispositivos disponíveis. Apenas os dispositivos com a capacidade disponível são exibidos na lista suspensa.

8. Examine todas as configurações de failover na página **Confirmar failover**. Se estiverem corretas, clique no ícone de seleção.

O processo de failover começará. Quando o failover estiver concluído, vá para a página Dispositivos e selecione o dispositivo virtual que foi usado como o destino para o processo de failover. Vá para a página Contêineres de Volume. Todos os contêineres de volume, juntamente com os volumes do antigo dispositivo, devem aparecer.

<!---HONumber=AcomDC_1217_2015-->