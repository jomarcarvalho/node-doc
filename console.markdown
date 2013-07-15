# Console

    Estabilidade: 4 - API Congelada

* {Object}

<!--type=global-->

Para imprimir em stdout e stderr. Semelhante às funções objeto do console
fornecida pela maioria dos navegadores web, aqui a saída é enviada para stdout ou stderr.

As funções do console são síncronas quando o destino é um terminal ou
um arquivo (para evitar mensagens perdidas em caso de abandono prematuro) e assíncronas
quando é um pipe (para evitar o bloqueio por longos períodos de tempo).

No exemplo a seguir, stdout é não-bloquedo, enquanto stderr
está bloqueado:

    $ node script.js 2> error.log | tee info.log

No uso diário, o bloqueio/não-bloqueio não é algo que você
deve se preocupar a não ser que você entrar com enormes quantidades de dados.


## console.log([data], [...])

Mostra dados de stdout com nova linha. Esta função pode ter vários argumentos em como uma
`printf()`. Exemplo:

    console.log('count: %d', count);

Se os elementos de formatação não são encontrados na primeira string, em seguida `util.inspect`
é usado em cada argumento. Veja [util.format ()][] para mais informações.

## console.info([data], [...])

Igual a `console.log`.

## console.error([data], [...])

Igual a `console.log` mas as impressões vão para stderr.

## console.warn([data], [...])

Igual a `console.error`.

## console.dir(obj)

Usa `util.inspect` em `obj` e mostra a string resultante para stdout. Esta função
ignora qualquer função `inspect()` personalizada em `obj`.

## console.time(label)

Marcar um tempo.

## console.timeEnd(label)

Concluir cronômetro, registro da saida. Exemplo:

    console.time('100-elements');
    for (var i = 0; i < 100; i++) {
      ;
    }
    console.timeEnd('100-elements');

## console.trace(label)

Imprimir um rastreamento de pilha para stderr da posição atual.

## console.assert(expression, [message])

Mesmo que [assert.ok()][] onde se a `expression` avalia como `falso` lançar uma
AssertionError com `message`.

[assert.ok()]: assert.html#assert_assert_value_message_assert_ok_value_message
[util.format()]: util.html#util_util_format_format
