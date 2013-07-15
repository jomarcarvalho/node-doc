# Complementos

Complementos são dinamicamente ligados a objetos compartilhados. Eles podem fornecer a ligação para as bibliotecas C e
C++. A API (no momento) é bastante complexa, envolvendo
conhecimento de várias bibliotecas:

 - JavaScript V8 é uma biblioteca escrita em C++. Usada para fazer a interface com JavaScript: Como
 criação de objetos, as funções de chamada e etc. Documentada principalmente na
 `v8.h` e em (`deps/v8/include/v8.h`), a qual também está disponível aqui.
 [Acessar](http://izs.me/v8-docs/main.html).

 - [libuv](https://github.com/joyent/libuv) É uma biblioteca escrita em C de ciclo de eventos.
 Sempre é preciso esperar por um descritor de arquivo para se tornar legível,
 esperar por um timer, ou esperar por um sinal a ser recebido, que terá
 A interface com libuv. Ou seja, se você executar qualquer I/O, libuv
 precisa de ser utilizado.

 - Bibliotecas internas do Node. A mais importante ainda é o `node::ObjectWrap`.
 classe que você provavelmente você vai querer usar.

 - Outros. Procure em `deps/` para ver o que mais está disponível.

Node estaticamente compila todas as suas dependências para o executável.
Ao compilar o módulo, você não precisa se preocupar com links para
qualquer destas bibliotecas.

Todos os exemplos que se seguem estão disponíveis em
[download](https://github.com/rvagg/node-addon-examples) e pode ser
usado como um ponto de partida para o seu próprio Addon.

## Olá Mundo

Para começar, vamos fazer um pequeno Addon em C++ que é o equivalente ao seguinte código JavaScript:

    module.exports.hello = function() { return 'world'; };

Primeiro vamos criar um arquivo `hello.cc`:

    #include <node.h>

    using namespace v8;

    Handle<Value> Method(const Arguments& args) {
      Isolate* isolate = Isolate::GetCurrent();
      HandleScope scope(isolate);
      return scope.Close(String::New("world"));
    }

    void init(Handle<Object> exports) {
      exports->Set(String::NewSymbol("hello"),
          FunctionTemplate::New(Method)->GetFunction());
    }

    NODE_MODULE(hello, init)

Note-se que todos os Addons do Node devem exportar uma função de inicialização:

    void Initialize (Handle<Object> exports);
    NODE_MODULE(module_name, Initialize)

Não há ponto e vírgula após `NODE_MODULE`, uma vez que não é uma função (ver
`node.h`).

O `module_name` precisa coincidir com o nome do arquivo do binário final (menos o
sufixo .node).

O código-fonte precisa ser construído em `hello.node`, o complemento binário. Ao
fazer isso, crie um arquivo chamado `binding.gyp` que descreve a configuração
para construir o módulo em um arquivo JSON. Este arquivo é compilado para
[node-gyp](https://github.com/TooTallNate/node-gyp).

    {
      "targets": [
        {
          "target_name": "hello",
          "sources": [ "hello.cc" ]
        }
      ]
    }

O próximo passo é gerar o projeto apropriado construir arquivos para o
plataforma atual. Use `node-gyp configure` para isso.

Agora você vai ter um `Makefile` (em plataformas Unix) ou um `vcxproj` file
(no Windows) no diretório `/build`. Em seguida executar o comando `node-gyp build`

Agora você tem os seu arquivo `.node` compilado! Os bindings files compilados vão estar em
em `build/Release`.

Agora você pode usar o addon binário em um projeto Node `hello.js` apontando
`require` para o recém-construído módulo `hello.node`:

    var addon = require('./build/Release/hello');

    console.log(addon.hello()); // 'world'

Por favor, veja os padrões abaixo para mais informações ou
<https://github.com/arturadib/node-qt> para ver exemplos para colocar em produção.


## Padrões de Complementos

Abaixo estão alguns padrões de addons para ajudar você a começar. Consulte on-line
[Referência do V8] (http://izs.me/v8-docs/main.html) para obter ajuda com os vários V8
Calls e [Guia do Embedder] do v8 (http://code.google.com/apis/v8/embed.html)
para uma explicação de diversos conceitos utilizados, tais como handles, scopes,
function templates e etc.

Para utilizar estes exemplos que você precisa compilá-los usando `node-gyp`.
Crie o seguinte arquivo `binding.gyp`:

    {
      "targets": [
        {
          "target_name": "addon",
          "sources": [ "addon.cc" ]
        }
      ]
    }

Nos casos em que houver mais de um arquivo `.cc`, basta adicionar o nome do arquivo para
o array `sources`, por exemplo:

    "sources": ["addon.cc", "myexample.cc"]

Agora que você tem o seu `binding.gyp` pronto, você pode configurar e construir o
complemento:

    $ node-gyp configure build


### Argumentos da Função

O padrão a seguir ilustra como ler os argumentos de JavaScript
chamados e retornar um resultado. Esta é a fonte principal e só precisava de
`addon.cc`:

    #include <node.h>

    using namespace v8;

    Handle<Value> Add(const Arguments& args) {
      Isolate* isolate = Isolate::GetCurrent();
      HandleScope scope(isolate);

      if (args.Length() < 2) {
        ThrowException(Exception::TypeError(
            String::New("Wrong number of arguments")));
        return scope.Close(Undefined(isolate));
      }

      if (!args[0]->IsNumber() || !args[1]->IsNumber()) {
        ThrowException(Exception::TypeError(String::New("Wrong arguments")));
        return scope.Close(Undefined(isolate));
      }

      Local<Number> num = Number::New(args[0]->NumberValue() +
          args[1]->NumberValue());
      return scope.Close(num);
    }

    void Init(Handle<Object> exports) {
      exports->Set(String::NewSymbol("add"),
          FunctionTemplate::New(Add)->GetFunction());
    }

    NODE_MODULE(addon, Init)

Você pode testá-lo com o seguinte trecho de JavaScript:

    var addon = require('./build/Release/addon');

    console.log( 'This should be eight:', addon.add(3,5) );


### Callbacks

Você pode passar funções JavaScript para uma função C++ e executá-los a partir de
lá. Aqui está o `addon.cc`:

    #include <node.h>

    using namespace v8;

    Handle<Value> RunCallback(const Arguments& args) {
      Isolate* isolate = Isolate::GetCurrent();
      HandleScope scope(isolate);

      Local<Function> cb = Local<Function>::Cast(args[0]);
      const unsigned argc = 1;
      Local<Value> argv[argc] = { String::New("hello world") };
      cb->Call(Context::GetCurrent()->Global(), argc, argv);

      return scope.Close(Undefined(isolate));
    }

    void Init(Handle<Object> exports, Handle<Object> module) {
      module->Set(String::NewSymbol("exports"),
          FunctionTemplate::New(RunCallback)->GetFunction());
    }

    NODE_MODULE(addon, Init)

Observe que este exemplo usa dois argumentos em `Init()` que recebe `exports` e
o `module` como o segundo argumento. Isto permite que o complemento
possa substituir completamente as `exports` com uma única função em vez de
acrescentando a função como uma propriedade de `exports`

Para testá-lo, execute o seguinte trecho de código JavaScript:

    var addon = require('./build/Release/addon');

    addon(function(msg){
      console.log(msg); // 'hello world'
    });


### Fábrica de Objetos

Você pode criar e retornar novos objetos de dentro de uma função C++ com esta
`addon.cc` padrão, que retorna um objeto com a propriedade `msg` que mostra
a string passada para `createObject()`:

    #include <node.h>

    using namespace v8;

    Handle<Value> CreateObject(const Arguments& args) {
      Isolate* isolate = Isolate::GetCurrent();
      HandleScope scope(isolate);

      Local<Object> obj = Object::New();
      obj->Set(String::NewSymbol("msg"), args[0]->ToString());

      return scope.Close(obj);
    }

    void Init(Handle<Object> exports, Handle<Object> module) {
      module->Set(String::NewSymbol("exports"),
          FunctionTemplate::New(CreateObject)->GetFunction());
    }

    NODE_MODULE(addon, Init)

Para testá-lo em JavaScript:

    var addon = require('./build/Release/addon');

    var obj1 = addon('hello');
    var obj2 = addon('world');
    console.log(obj1.msg+' '+obj2.msg); // 'hello world'


### Fábrica de Funções

Este padrão mostra como criar e retornar uma função JavaScript que
envolve uma função C++:

    #include <node.h>

    using namespace v8;

    Handle<Value> MyFunction(const Arguments& args) {
      Isolate* isolate = Isolate::GetCurrent();
      HandleScope scope(isolate);
      return scope.Close(String::New("hello world"));
    }

    Handle<Value> CreateFunction(const Arguments& args) {
      Isolate* isolate = Isolate::GetCurrent();
      HandleScope scope(isolate);

      Local<FunctionTemplate> tpl = FunctionTemplate::New(MyFunction);
      Local<Function> fn = tpl->GetFunction();

      // omit this to make it anonymous
      fn->SetName(String::NewSymbol("theFunction"));

      return scope.Close(fn);
    }

    void Init(Handle<Object> exports, Handle<Object> module) {
      module->Set(String::NewSymbol("exports"),
          FunctionTemplate::New(CreateFunction)->GetFunction());
    }

    NODE_MODULE(addon, Init)

Para Testar:

    var addon = require('./build/Release/addon');

    var fn = addon();
    console.log(fn()); // 'hello world'


### Envolvendo Objetos C++

Aqui vamos criar um wrapper para um objeto/classe C++ `MyObject`, que pode ser
instanciado em JavaScript através do operador `new`. Primeiro prepare o
módulo principal `addon.cc`:

    #include <node.h>
    #include "myobject.h"

    using namespace v8;

    void InitAll(Handle<Object> exports) {
      MyObject::Init(exports);
    }

    NODE_MODULE(addon, InitAll)

Então, em `myobject.h` fazer o wrapper herdar `node::ObjectWrap`:

    #ifndef MYOBJECT_H
    #define MYOBJECT_H

    #include <node.h>

    class MyObject : public node::ObjectWrap {
     public:
      static void Init(v8::Handle<v8::Object> exports);

     private:
      MyObject();
      ~MyObject();

      static v8::Handle<v8::Value> New(const v8::Arguments& args);
      static v8::Handle<v8::Value> PlusOne(const v8::Arguments& args);
      double counter_;
    };

    #endif

E em `myobject.cc` implementar os vários métodos que você deseja expor.
Aqui vamos expor o método `PlusOne` adicionando-o para o construtor do
protótipo:

    #include <node.h>
    #include "myobject.h"

    using namespace v8;

    MyObject::MyObject() {};
    MyObject::~MyObject() {};

    void MyObject::Init(Handle<Object> exports) {
      Isolate* isolate = Isolate::GetCurrent();

      // Prepare constructor template
      Local<FunctionTemplate> tpl = FunctionTemplate::New(New);
      tpl->SetClassName(String::NewSymbol("MyObject"));
      tpl->InstanceTemplate()->SetInternalFieldCount(1);

      // Prototype
      tpl->PrototypeTemplate()->Set(String::NewSymbol("plusOne"),
          FunctionTemplate::New(PlusOne)->GetFunction());

      Persistent<Function> constructor
          = Persistent<Function>::New(isolate, tpl->GetFunction());

      exports->Set(String::NewSymbol("MyObject"), constructor);
    }

    Handle<Value> MyObject::New(const Arguments& args) {
      Isolate* isolate = Isolate::GetCurrent();
      HandleScope scope(isolate);

      MyObject* obj = new MyObject();
      obj->counter_ = args[0]->IsUndefined() ? 0 : args[0]->NumberValue();
      obj->Wrap(args.This());

      return args.This();
    }

    Handle<Value> MyObject::PlusOne(const Arguments& args) {
      Isolate* isolate = Isolate::GetCurrent();
      HandleScope scope(isolate);

      MyObject* obj = ObjectWrap::Unwrap<MyObject>(args.This());
      obj->counter_ += 1;

      return scope.Close(Number::New(obj->counter_));
    }

Teste isso com:

    var addon = require('./build/Release/addon');

    var obj = new addon.MyObject(10);
    console.log( obj.plusOne() ); // 11
    console.log( obj.plusOne() ); // 12
    console.log( obj.plusOne() ); // 13

### Fábrica de Objetos Envolvidos

Isto é útil quando você quer ser capaz de criar objetos nativos sem
instanciar explicitamente com o operador `new` em JavaScript, por exemplo.

    var obj = addon.createObject();
    // instead of:
    // var obj = new addon.Object();

Vamos registrar o nosso `createObject` em `addon.cc`:

    #include <node.h>
    #include "myobject.h"

    using namespace v8;

    Handle<Value> CreateObject(const Arguments& args) {
      Isolate* isolate = Isolate::GetCurrent();
      HandleScope scope(isolate);
      return scope.Close(MyObject::NewInstance(args));
    }

    void InitAll(Handle<Object> exports, Handle<Object> module) {
      MyObject::Init();

      module->Set(String::NewSymbol("exports"),
          FunctionTemplate::New(CreateObject)->GetFunction());
    }

    NODE_MODULE(addon, InitAll)

Em `myobject.h` agora introduzir o método estático `NewInstance` que se leva o
cuidado de instanciar o objeto (ou seja, faz o trabalho de `new` em JavaScript):

    #ifndef MYOBJECT_H
    #define MYOBJECT_H

    #include <node.h>

    class MyObject : public node::ObjectWrap {
     public:
      static void Init();
      static v8::Handle<v8::Value> NewInstance(const v8::Arguments& args);

     private:
      MyObject();
      ~MyObject();

      static v8::Persistent<v8::Function> constructor;
      static v8::Handle<v8::Value> New(const v8::Arguments& args);
      static v8::Handle<v8::Value> PlusOne(const v8::Arguments& args);
      double counter_;
    };

    #endif

A implementação é semelhante ao referido na `myobject.cc`:

    #include <node.h>
    #include "myobject.h"

    using namespace v8;

    MyObject::MyObject() {};
    MyObject::~MyObject() {};

    Persistent<Function> MyObject::constructor;

    void MyObject::Init() {
      Isolate* isolate = Isolate::GetCurrent();
      // Prepare constructor template
      Local<FunctionTemplate> tpl = FunctionTemplate::New(New);
      tpl->SetClassName(String::NewSymbol("MyObject"));
      tpl->InstanceTemplate()->SetInternalFieldCount(1);

      // Prototype
      tpl->PrototypeTemplate()->Set(String::NewSymbol("plusOne"),
          FunctionTemplate::New(PlusOne)->GetFunction());

      constructor = Persistent<Function>::New(isolate, tpl->GetFunction());
    }

    Handle<Value> MyObject::New(const Arguments& args) {
      Isolate* isolate = Isolate::GetCurrent();
      HandleScope scope(isolate);

      MyObject* obj = new MyObject();
      obj->counter_ = args[0]->IsUndefined() ? 0 : args[0]->NumberValue();
      obj->Wrap(args.This());

      return args.This();
    }

    Handle<Value> MyObject::NewInstance(const Arguments& args) {
      Isolate* isolate = Isolate::GetCurrent();
      HandleScope scope(isolate);

      const unsigned argc = 1;
      Handle<Value> argv[argc] = { args[0] };
      Local<Object> instance = constructor->NewInstance(argc, argv);

      return scope.Close(instance);
    }

    Handle<Value> MyObject::PlusOne(const Arguments& args) {
      Isolate* isolate = Isolate::GetCurrent();
      HandleScope scope(isolate);

      MyObject* obj = ObjectWrap::Unwrap<MyObject>(args.This());
      obj->counter_ += 1;

      return scope.Close(Number::New(obj->counter_));
    }

Teste isso com:

    var createObject = require('./build/Release/addon');

    var obj = createObject(10);
    console.log( obj.plusOne() ); // 11
    console.log( obj.plusOne() ); // 12
    console.log( obj.plusOne() ); // 13

    var obj2 = createObject(20);
    console.log( obj2.plusOne() ); // 21
    console.log( obj2.plusOne() ); // 22
    console.log( obj2.plusOne() ); // 23


### Passando objetos em envolvidos

Além de envolver e retornar objetos C++, você pode passá-los para desembrulhar-los com a função auxiliar `node::ObjectWrap::Unwrap`.
Na seguinte `addon.cc` apresentamos uma função `add()` que pode assumir dois objetos
`MyObject`:

    #include <node.h>
    #include "myobject.h"

    using namespace v8;

    Handle<Value> CreateObject(const Arguments& args) {
      Isolate* isolate = Isolate::GetCurrent();
      HandleScope scope(isolate);
      return scope.Close(MyObject::NewInstance(args));
    }

    Handle<Value> Add(const Arguments& args) {
      Isolate* isolate = Isolate::GetCurrent();
      HandleScope scope(isolate);

      MyObject* obj1 = node::ObjectWrap::Unwrap<MyObject>(
          args[0]->ToObject());
      MyObject* obj2 = node::ObjectWrap::Unwrap<MyObject>(
          args[1]->ToObject());

      double sum = obj1->Val() + obj2->Val();
      return scope.Close(Number::New(sum));
    }

    void InitAll(Handle<Object> exports) {
      MyObject::Init();

      exports->Set(String::NewSymbol("createObject"),
          FunctionTemplate::New(CreateObject)->GetFunction());

      exports->Set(String::NewSymbol("add"),
          FunctionTemplate::New(Add)->GetFunction());
    }

    NODE_MODULE(addon, InitAll)

Para tornar as coisas mais interessantes, apresentamos um método público em `myobject.h` para que
pode sondar valores privados depois de desembrulhar o objeto:

    #ifndef MYOBJECT_H
    #define MYOBJECT_H

    #include <node.h>

    class MyObject : public node::ObjectWrap {
     public:
      static void Init();
      static v8::Handle<v8::Value> NewInstance(const v8::Arguments& args);
      double Val() const { return val_; }

     private:
      MyObject();
      ~MyObject();

      static v8::Persistent<v8::Function> constructor;
      static v8::Handle<v8::Value> New(const v8::Arguments& args);
      double val_;
    };

    #endif

A implementação do `myobject.cc` é semelhante à anterior:

    #include <node.h>
    #include "myobject.h"

    using namespace v8;

    MyObject::MyObject() {};
    MyObject::~MyObject() {};

    Persistent<Function> MyObject::constructor;

    void MyObject::Init() {
      Isolate* isolate = Isolate::GetCurrent();

      // Prepare constructor template
      Local<FunctionTemplate> tpl = FunctionTemplate::New(New);
      tpl->SetClassName(String::NewSymbol("MyObject"));
      tpl->InstanceTemplate()->SetInternalFieldCount(1);

      constructor = Persistent<Function>::New(isolate, tpl->GetFunction());
    }

    Handle<Value> MyObject::New(const Arguments& args) {
      Isolate* isolate = Isolate::GetCurrent();
      HandleScope scope(isolate);

      MyObject* obj = new MyObject();
      obj->val_ = args[0]->IsUndefined() ? 0 : args[0]->NumberValue();
      obj->Wrap(args.This());

      return args.This();
    }

    Handle<Value> MyObject::NewInstance(const Arguments& args) {
      Isolate* isolate = Isolate::GetCurrent();
      HandleScope scope(isolate);

      const unsigned argc = 1;
      Handle<Value> argv[argc] = { args[0] };
      Local<Object> instance = constructor->NewInstance(argc, argv);

      return scope.Close(instance);
    }

Teste isso com:

    var addon = require('./build/Release/addon');

    var obj1 = addon.createObject(10);
    var obj2 = addon.createObject(20);
    var result = addon.add(obj1, obj2);

    console.log(result); // 30
