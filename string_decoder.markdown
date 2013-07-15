# Decodificador de String

    Estabilidade: 3 - Estével

Para utilizar este módulo, você vai precisar requisitar ele com `require('string_decoder')`. StringDecoder decodifica um buffer para uma string. É uma interface simples para `buffer.toString()`, mas oferece
suporte adicional para utf8.

    var StringDecoder = require('string_decoder').StringDecoder;
    var decoder = new StringDecoder('utf8');

    var cent = new Buffer([0xC2, 0xA2]);
    console.log(decoder.write(cent));

    var euro = new Buffer([0xE2, 0x82, 0xAC]);
    console.log(decoder.write(euro));

## Classe: StringDecoder

Aceita um único parâmetro `encoding` que por padrão é `utf8`.

### decoder.write(buffer)

Retorna a string decodificada.

### decoder.end()

Retorna qualquer bytes à direita que foram deixados no buffer.
