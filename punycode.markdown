# PunyCode

    Estabilidade: 2 - Instável

[Punycode.js](http://mths.be/punycode) vem com o Node.js v0.6.2+. Para usar você precisa requerir com
`require('punycode')` para usá-lo. (Para usá-lo com outras versões do Node.js,
use o npm para instalar o módulo `punycode`)

## punycode.decode(string)

Converte uma string Punycode com somente símbolos ASCII para uma série de símbolos Unicode.

    // decodificar partes de nomes de domínio
    punycode.decode('maana-pta'); // 'mañana'
    punycode.decode('--dqo34k'); // '☃-⌘'

## punycode.encode(string)

Converte uma string de símbolos Unicode em uma string de Punycode somente de símbolos ASCII.

    // codificar partes de nomes de domínios
    punycode.encode('mañana'); // 'maana-pta'
    punycode.encode('☃-⌘'); // '--dqo34k'

## punycode.toUnicode(domain)

Converte uma string Punycode representando um nome de domínio para Unicode. Apenas as
Partes Punycoded do nome de domínio serão convertidos, ou seja, não importa se
você chamá-lo em uma string que já foi convertido para Unicode.

    // decodificar nomes de domínio
    punycode.toUnicode('xn--maana-pta.com'); // 'mañana.com'
    punycode.toUnicode('xn----dqo34k.com'); // '☃-⌘.com'

## punycode.toASCII(domain)

Converte uma string de caracteres Unicode que representa um nome de domínio para Punycode. Apenas as
partes não-ASCII do nome de domínio será convertido, ou seja, não importa se
você chamá-lo com um domínio que já está em ASCII.

    // codificar nomes de domínio
    punycode.toASCII('mañana.com'); // 'xn--maana-pta.com'
    punycode.toASCII('☃-⌘.com'); // 'xn----dqo34k.com'

## punycode.ucs2

### punycode.ucs2.decode(string)

Cria um array contendo os valores de ponto de código numérico de cada símbolo Unicode na string. Enquanto [JavaScript usa UCS-2 internamente](http://mathiasbynens.be/notes/javascript-encoding), esta função
irá converter um par de metades de substituição (cada um dos quais o UCS-2 expõe como
caracteres em separado) em um ponto de código único, combinando com UTF-16.

    punycode.ucs2.decode('abc'); // [0x61, 0x62, 0x63]
    // par substituto para U+1D306 tetragram para o centro:
    punycode.ucs2.decode('\uD834\uDF06'); // [0x1D306]

### punycode.ucs2.encode(codePoints)

Cria uma string com base em uma matriz de valores de ponto de código numérico.

    punycode.ucs2.encode([0x61, 0x62, 0x63]); // 'abc'
    punycode.ucs2.encode([0x1D306]); // '\uD834\uDF06'

## punycode.version

Uma string representando o número da versão atual Punycode.js.
