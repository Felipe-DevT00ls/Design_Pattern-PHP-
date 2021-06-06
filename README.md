# Design_Pattern PHP
Autor: Gabriel Felipe.

<h1 id="sobre">Sobre</h1>
Nesse arquivo busco listar e explicar a maior parte dos design pattern utilizando a linguagem php.<br>
Estou fazendo este documento para ajudar quem está começando a estudalos, ou até mesmo, servir como<br>
uma colinha para programadores ou desenvolvedores que já conhecem sobre.

<h1 id="sobre">Tabela de Conteúdo</h1>

-  <a href="#1">SimpleFactory</a>
-  <a href="#2">Bridge</a>
-  <a href="#3">DependencyInjection</a>  
-  <a href="#4">AbstractFactory</a>
-  <a href="#5">Builder</a>
-  <a href="#6">StractFactory</a>
-  <a href="#7">Strategy</a>
-  <a href="#8">Composite</a>
-  <a href="#9">Facade</a>
-  <a href="#10">FluenteInterface</a> 
-  <a href="#11">ChainOfResponsabilities</a>
-  <a href="#12">Iterator</a>
-  <a href="#13">Observer</a>

<h3 id="1">SimpleFactory</h3>
O primeiro design pattern que veremos, será o simplefactory, para começarmos temos que saber<br>
que ele servirá para você acessar um método de maneira indireta, isto é, sem instanciar diretamente a <br>
classe do metódo que lhe interessa.<br><br>

Para facilitar a compreensão irei demonstrar um exemplo:<br>


```
class Nota
    {
        private $nota;
        public function setNota($nota)
        {
            $this->nota = $nota;
        }

        public function getNota()
        {
            echo $this->nota;
        }
    }


    class Aluno
    {
        public function defNota()
        {
            return new Nota();
        }
    }
```

aqui temos duas classes, porém só acessaremos uma delas, que será a classe Aluno<br>
através dela utilizaremos os metódos da classe Nota. <br>
Para isso definimos um metódo chamado `defNota()`, ele é responsavél por instanciar a classe que<br>
que possui nosso método desejado.<br><br>

logo para definirmos uma nota, ficará dessa maneira: <br>
```
    $instance = new Aluno();
    $aluno1 = $instance->defNota();
    $aluno1->setNota(10);
    $aluno1->getNota();
```

<br><br>

Dessa forma percebemos que ao utilizarmos o SimpleFactory evitamos que nossa classe seja utilizada de maneira direta.<br>
pensando além podemos até mesmo fazer validações antes de acessar indiretamente nossa classe.<br><br>

Para isso faremos algumas alterações nas nossas classes.<br>
Primeiro de tudo, definiremos uma interface, e logo veremos a sua utilidade
```
  interface InterfaceNotas{
        public function setNota($nota);
        public function getNota();

    }
```
<br><br>

Logo após alteraremos a classe Nota, e ficará assim:<br>

```
class Nota implements InterfaceNotas
    {
        private $nota;
        public function setNota($nota)
        {
            if($typeAcount == "teacher"){
                $this->nota = $nota;
            }else{
                echo "só contas de professores podem alterar as notas dos alunos.";
            }
            
        }

        public function getNota()
        {
            echo $this->nota;
        }
    }
```
Agora a nossa classe Nota implementa a interface `InterfaceNota`, e você já verá o porque.<br>
Agora criaremos uma segunda classe que é a `UserNotRootAccess` e ela também implementará `InterfaceNota`...

```
class UserNotRootAccess implements InterfaceNotas{
        public function setNota($nota){
            echo "você nao possui acesso a essa funcionalidade";
        }

    
        public function getNota(){
            echo "você nao possui acesso a essa funcionalidade";
        }
    }
```

essa classe será retornada no lugar da classe Nota quando,<br>
a instancia que estiver chamando a classe Nota não possuir privilegios suficientes para acessala<br>
agora iremos alterar a classe Aluno.<br>

```

class Aluno
    {
        public function defPrivileges($typeAcount)
        {
            if( $typeAcount == "teacher"){
                return new Nota();
            }else{
                return new UserNotRootAccess();
            }
            
        }
    }

```

após isso instanciaremos a classe aluno novamente.<br>
```
$instance = new Aluno();
    $aluno1 = $instance->defPrivileges("aluno");
    $aluno1->setNota(10);
    $aluno1->getNota();
```

e o retorno será :
```
você nao possui acesso a essa funcionalidade
você nao possui acesso a essa funcionalidade
```

Bem acredito, que pude demonstrar algumas possibilidades que o simplefactory nos dá.
<br><br>





<h3 id="2">Bridge</h3>
