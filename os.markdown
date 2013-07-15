# Sistema Operacional

    Estabilidade: 4 - API Congelada

Fornece algumas funções utilitárias relacionadas com sistema operacional básico.

Para usar você precisa requerir `require('os')`

## os.tmpdir()

Retorna o diretório padrão do sistema operacional para arquivos temporários.

## os.endianness()

Retorna a ordenação da CPU. Os valores possíveis são `"BE"` ou `"LE"`.

## os.hostname()

Retorna o nome do host do sistema operacional.

## os.type()

Retorna o nome do sistema operacional.

## os.platform()

Retorna a plataforma de sistema operacional.

## os.arch()

Retorna a arquitetura de CPU do sistema operacional.

## os.release()

Retorna a versão do sistema operacional.

## os.uptime()

Retorna o tempo de funcionamento do sistema em segundos.

## os.loadavg()

Retorna um array contendo as médias de carga 1, 5 e 15 minutos.

## os.totalmem()

Retorna a quantidade total de memória do sistema em bytes.

## os.freemem()

Retorna a quantidade de memória livre do sistema em bytes.

## os.cpus()

Retorna uma matriz de objetos que contêm informações sobre cada CPU / core
instalado: modelo, a velocidade (em MHz), e vezes (um objeto que contém o número de
milissegundos da CPU / gasto em: user, nice, sys, ocioso, e IRQ).

Exemplo de retorno os.cpus()

    [ { model: 'Intel(R) Core(TM) i7 CPU         860  @ 2.80GHz',
        speed: 2926,
        times:
         { user: 252020,
           nice: 0,
           sys: 30340,
           idle: 1070356870,
           irq: 0 } },
      { model: 'Intel(R) Core(TM) i7 CPU         860  @ 2.80GHz',
        speed: 2926,
        times:
         { user: 306960,
           nice: 0,
           sys: 26980,
           idle: 1071569080,
           irq: 0 } },
      { model: 'Intel(R) Core(TM) i7 CPU         860  @ 2.80GHz',
        speed: 2926,
        times:
         { user: 248450,
           nice: 0,
           sys: 21750,
           idle: 1070919370,
           irq: 0 } },
      { model: 'Intel(R) Core(TM) i7 CPU         860  @ 2.80GHz',
        speed: 2926,
        times:
         { user: 256880,
           nice: 0,
           sys: 19430,
           idle: 1070905480,
           irq: 20 } },
      { model: 'Intel(R) Core(TM) i7 CPU         860  @ 2.80GHz',
        speed: 2926,
        times:
         { user: 511580,
           nice: 20,
           sys: 40900,
           idle: 1070842510,
           irq: 0 } },
      { model: 'Intel(R) Core(TM) i7 CPU         860  @ 2.80GHz',
        speed: 2926,
        times:
         { user: 291660,
           nice: 0,
           sys: 34360,
           idle: 1070888000,
           irq: 10 } },
      { model: 'Intel(R) Core(TM) i7 CPU         860  @ 2.80GHz',
        speed: 2926,
        times:
         { user: 308260,
           nice: 0,
           sys: 55410,
           idle: 1071129970,
           irq: 880 } },
      { model: 'Intel(R) Core(TM) i7 CPU         860  @ 2.80GHz',
        speed: 2926,
        times:
         { user: 266450,
           nice: 1480,
           sys: 34920,
           idle: 1072572010,
           irq: 30 } } ]

## os.networkInterfaces()

Obter uma lista de interfaces de rede:

    { lo0:
       [ { address: 'fe80::1', netmask: 'ffff:ffff:ffff:ffff::',
           family: 'IPv6', internal: true },
         { address: '127.0.0.1', netmask: '255.0.0.0',
           family: 'IPv4', internal: true },
         { address: '::1', netmask: 'ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff',
           family: 'IPv6', internal: true } ],
      en1:
       [ { address: 'fe80::226:8ff:fedc:1dd', netmask: 'ffff:ffff:ffff:ffff::',
           family: 'IPv6', internal: false },
         { address: '10.0.1.6', netmask: '255.255.255.0',
           family: 'IPv4', internal: false } ],
      vmnet1:
       [ { address: '192.168.252.1', netmask: '255.255.255.0',
           family: 'IPv4', internal: false } ],
      vmnet8:
       [ { address: '192.168.207.1', netmask: '255.255.255.0',
           family: 'IPv4', internal: false } ] }

## os.EOL

Uma constante que define o marcador de fim de linha apropriada para o sistema operacional.
