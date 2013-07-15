# Testes Unitários

    Estabilidade: 5 - Trancado

Este módulo é utilizado para escrever testes de unidade para as suas aplicações, você pode
acessá-lo com `require('assert')`.

## assert.fail(actual, expected, message, operator)

Lança uma exceção que exibe os valores de `actual` e `expected` separadas pelo operador fornecido.

## assert(value, message), assert.ok(value, [message])

Testa se o valor for verdade, é equivalente a `assert.equal (true, !!value, message);`

## assert.equal(actual, expected, [message])

Testes superficiais, igualdade com o operador de comparação de igualdade (`==`).

## assert.notEqual(actual, expected, [message])

Testes superficiais, não igualdade com o operador de comparação não é igual (`!=`).

## assert.deepEqual(actual, expected, [message])

Testes de igualdade profunda.

## assert.notDeepEqual(actual, expected, [message])

Testes para qualquer profunda desigualdade.

## assert.strictEqual(actual, expected, [message])

Testes de igualdade estrita, conforme determinado pelo operador de extrema igualdade (`===`)

## assert.notStrictEqual(actual, expected, [message])

Testes de igualdade não-estrita, tal como determinado pelo operador não extrema igualdade (`!==`)

## assert.throws(block, [error], [message])

Espera 'block' lançar um erro. `error` pode ser construtor, regexp ou
função de validação.

Validate instanceof using constructor:

    assert.throws(
      function() {
        throw new Error("Wrong value");
      },
      Error
    );

Validate error message using RegExp:

    assert.throws(
      function() {
        throw new Error("Wrong value");
      },
      /value/
    );

Validação de erro personalizada:

    assert.throws(
      function() {
        throw new Error("Wrong value");
      },
      function(err) {
        if ( (err instanceof Error) && /value/.test(err) ) {
          return true;
        }
      },
      "unexpected error"
    );

## assert.doesNotThrow(block, [message])

Espera 'block' não lançar um erro, consulte assert.throws para mais detalhes.

## assert.ifError(value)

Tests if value is not a false value, throws if it is a true value. Useful when
testing the first argument, `error` in callbacks.
Testa se o valor não é um valor falso, lança, se for um valor verdadeiro. Útil quando
testando o primeiro argumento, `error` em retornos de chamada.
