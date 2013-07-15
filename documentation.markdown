# Sobre esse Documento

<!-- type=misc -->

O objetivo deste documento é explicar de forma abrangente sobre a API do Node.js
tanto a partir de uma referência, bem como um ponto de vista conceptual. cada
seção descreve um módulo built-in ou o conceito de alto nível.

Se for caso disso, os tipos de propriedade, argumentos de métodos, e os argumentos
fornecido para manipuladores de eventos são detalhados em uma lista abaixo do tópico
com cabeçalho.

Cada documento `.html` tem um `.json` correspondente. Apresentando
a mesma informação de forma estruturada. Este recurso é
experimental, e acrescentou muito para o benefício das IDEs e outros utilitários que
desejam fazer as coisas programáticas com a documentação.

Cada arquivo`.html` e `.json` é gerado com base no correspondente
`.markdown` na pasta `doc/api/` na pasta de sources do node. A
documentação é gerada utilizando o `tools/doc/generate.js`.
O modelo HTML está localizado na `doc/template.html`.

## Estabilidade

<!--type=misc-->

Ao longo da documentação, você vai ver indicações de uma seção de
estabilidade. A API do Node.js ainda está mudando um pouco, e isso é natural a medida que
amadurece, certas partes são mais confiáveis ​​do que outras. Algumas são tão
aprovadas, e assim se tornam confiáveis, pois é improvável que alguma vez venha a
mudar tudo. Outras marcas são o Novo e Experimental, conhecidos por ser perigosos
estarem em processo e está sendo redesenhado.

Os índices de estabilidade são os seguintes:

```
Estabilidade: 0 - Obsoleto
Esse recurso é conhecido por ser problemático.
Não confie nele. O uso do recurso pode causar avisos. A
compatibilidade não deve ser esperada de recursos obsoletos.
```

```
Estabilidade: 1 - Experimental
Esta funcionalidade foi introduzida recentemente, e podem mudar
ou ser removido em versões futuras. Por favor, testá-la e fornecer um feedback.
Se trata de um caso de uso que é importante para você, diga a equipe principal do Node sobre seus testes e forneça um feedback.
```

```
Estabilidade: 2 - Instável
A API está no processo de decantação, mas ainda não teve
testes reais suficiente para ser considerada estável. Compatibilidade com versões anteriores
será mantida se razoável.
```

```
Estabilidade: 3 - Estável
A API tem se mostrado satisfatória, mas a limpeza no
código pode causar alterações menores. Compatibilidade com versões anteriores é garantida.
```

```
Estabilidade: 4 - API Congelada
Esta API foi testada extensivamente na produção e é
improvável que alguma vez venha a mudar.
```

```
Estabilidade: 5 - Trancada
A não ser que erros graves forem encontrados, este código nunca irá
mudar. Por favor, não sugira mudanças nesta área, pois elas vão ser recusadas.
```

## Saida em JSON

    Estabilidade: 1 - Experimental

Cada arquivo HTML na mark tem um arquivo JSON correspondente com o
mesmos dados.

Este recurso é novo a partir do node v0.6.12. E é experimental.
