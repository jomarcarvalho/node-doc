# Query String

    Estabilidade: 3 - Estável

<!--name=querystring-->

Este módulo fornece utilitários para lidar com query strings.
Ele fornece os seguintes métodos:

## querystring.stringify(obj, [sep], [eq])

Serializar um objeto em uma string.
Opcionalmente, substituir os caracteres separador padrão (`'&'`) e atribuição (`'='`)

Exemplo:

    querystring.stringify({ foo: 'bar', baz: ['qux', 'quux'], corge: '' })
    // returns
    'foo=bar&baz=qux&baz=quux&corge='

    querystring.stringify({foo: 'bar', baz: 'qux'}, ';', ':')
    // returns
    'foo:bar;baz:qux'

## querystring.parse(str, [sep], [eq], [options])

Desserializar uma querystring para um objeto.
Opcionalmente, substituir o separador padrão (`'&'`) e atribuição (`'='`)
caracteres.

Opções de objeto pode conter a propriedade `maxKeys` (igual a 1000 por padrão), ele vai
ser utilizado para limitar chaves processadas. Configure para 0 para remover essa limitação de chave contagem.

Exemplo:

    querystring.parse('foo=bar&baz=qux&baz=quux&corge')
    // returns
    { foo: 'bar', baz: ['qux', 'quux'], corge: '' }

## querystring.escape

A função de escape usado por `querystring.stringify`
fornecida de modo que ela pode ser substituída se for necessário.

## querystring.unescape

A função unescape usada por `querystring.parse`,
fornecida de modo que ela pode ser substituída se for necessário.