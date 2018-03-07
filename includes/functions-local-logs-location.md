Quando o host Funções executa localmente, ele grava logs no caminho a seguir:

```
<DefaultTempDirectory>\LogFiles\Application\Functions
```

No Windows, `<DefaultTempDirectory>` é o primeiro valor encontrado das variáveis de ambiente TMP, TEMP, USERPROFILE ou o diretório do Windows.
No MacOS ou Linux, `<DefaultTempDirectory>` é a variável de ambiente TMPDIR.

[!Note]
Quando o host Funções for iniciado, ele substituirá a estrutura de arquivos existente no diretório.