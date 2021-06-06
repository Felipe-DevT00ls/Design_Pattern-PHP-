# Design_Pattern PHP
Autor: Gabriel Felipe.

<h1 id="sobre">Sobre</h1>
Nesse arquivo busco listar e explicar a maior parte dos design pattern utilizando a linguagem php.<br>
Estou fazendo este documento para ajudar quem está começando a estuda-los, ou até mesmo, servir como<br>
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
            $this->nota = $nota; 
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
a instancia que estiver chamando a classe Nota não possuir privilegios suficientes para acessá-la<br>
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
O segundo design pattern que falaremos será o Brigde, ele consiste em uma injeção de dependencia<br> 
em uma classe abstrata, e essa injeção de dependencia será dinamica, ou seja,essa dependencia que <br>
está sendo injetada pode ter comportamentos variados, claro obedecendo a sua interface.<br>
Ou seja o bridge, ele funciona desacoplando a interface que injeta a classe abstrata <br>

Explicando sem exemplo, fica meio confuso, então partiremos para exemplos:<br>

Primeiro de tudo Vamos imaginar que temos um projeto pra fazer, mas uma das necessidades da aplicação é que ela tenha capacidade de conectar tanto com um banco de 
dados Mariadb como um banco Mongodb. Observando esse desafio do projeto observamos que facilmente poderiamos ter uma enorme dificuldade de atualizar essa funcionalidade do projeto no futuro caso ela seja mal desenvolvida.

Bem, através do design paternn bridge podemos driblar varias das dificuldades que esse projeto nos propõe.
Então vamos começar: 

Primeiro de tudo sabemos que teremos que preparar o back end pra consiguir lidar com dois tipos de bd, dependendo da necessidade.
entao definiremos a interface `APIDatabaseInterface` que ficará assim:

```
interface APIDatabaseInterface{
    public function ConfigConnection($user, $pass);
    public function InsertUser($data);
    public function UpdateUser($data);
}
```
essa interface será importante para padronizar os metodos entre os bancos de dados mariaDB e mongoDB, ou seja,
caso eu queira inserir um usuario eu utilizo a `public function InsertInto` para os dois banco de dados.
Agora implementaremos essa interface:

```
class MariaDB implements APIDatabaseInterface{
    public function ConfigConnection($user, $pass){
        echo "conectado no maria db com as config {$user}:{$pass}";
    }

    public function InsertUser($data){
        echo "Inserindo no maria db o user {$data}";
    }

    public function UpdateUser($data){
        echo "Atualizando no maria db os dados do user {$data}";
    }
}
```

```
class MongoDB implements APIDatabaseInterface{
    public function ConfigConnection($user, $pass){
        echo "conectado no mongo db com as config {$user}:{$pass}";
    }

    public function InsertUser($data){
        echo "Inserindo no mongo db o user {$data}";
    }

    public function UpdateUser($data){
        echo "Atualizando no mongo db os dados do user {$data}";
    }
}
```

agora criaremos a classe abstrata que configura o banco de dados.
```
abstract class ConfigDatabase{
    protected $user;
    protected $pass;
    protected $data;
    protected $db;
    public function __construct(APIDatabaseInterface $database){
        $this->db = $database;
    }
}
```

essa classe abstrata é responsavel por definir os metodos e as variaveis importantes da nossa classe principal.

observe esse trecho do codigo:
```
public function __construct(APIDatabaseInterface $database){
        $this->db = $database;
    }
```

lembra no começo que foi dito que o design pattern Bridge : 
>  "consiste em uma injeção de dependencia"<br>


também foi dito:
>   "funciona desacoplando a interface que injeta a classe abstrata"

agora observe novamente tanto a interface quanto o construct da classe abstrata:

```
interface APIDatabaseInterface{
    public function ConfigConnection($user, $pass);
    public function InsertUser($data);
    public function UpdateUser($data);
}

public function __construct(APIDatabaseInterface $database){
        $this->db = $database;
    }
```

percebe que utilizamos a interface para garantir que teremos sempre os metodos desejados, e que com isso podemos 
ter variações dessa interface, ou seja, podemos criar também outra implementações que seriam outros tipos de banco de dados 
que mesmo assim ainda funcionaria sem modificar ou modificando o minimo de linha de codigo da classe principal...

continuando o codigo, definiremos a classe principal que herdera a classe abstrata

```
class ConnectDatabase extends ConfigDatabase{
    public function __construct(APIDatabaseInterface $database, $user, $pass, $data){
        parent::__construct($database);
        $this->user = $user;
        $this->pass = $pass;
        $this->data = $data;
    }

    public function connect(){
        $this->db->ConfigConnection($this->user, $this->pass);
    }

    public function Insert(){
        $this->db->InsertUser($this->data);
    }

    public function Update(){
        $this->db->UpdateUser($this->data);
    }
}

```

agora é só instanciarmos a classe: 
```
$mariaDb = new ConnectDatabase(new MariaDB, "root", "123", "alanzin");
$mariaDb->connect();
```
>resposta: conectado no maria db com as config root:123



```
$mongoDb = new ConnectDatabase(new MongoDB, "root", "123", "alanzin");
$mongoDb->connect();
```
>resposta: conectado no mongo db com as config root:123


Aqui está o codigo completo caso queira edita-lo:

```
<?php
interface APIDatabaseInterface{
    public function ConfigConnection($user, $pass);
    public function InsertUser($data);
    public function UpdateUser($data);
}

class MariaDB implements APIDatabaseInterface{
    public function ConfigConnection($user, $pass){
        echo "conectado no maria db com as config {$user}:{$pass}";
    }

    public function InsertUser($data){
        echo "Inserindo no maria db o user {$data}";
    }

    public function UpdateUser($data){
        echo "Atualizando no maria db os dados do user {$data}";
    }
}

class MongoDB implements APIDatabaseInterface{
    public function ConfigConnection($user, $pass){
        echo "conectado no mongo db com as config {$user}:{$pass}";
    }

    public function InsertUser($data){
        echo "Inserindo no mongo db o user {$data}";
    }

    public function UpdateUser($data){
        echo "Atualizando no mongo db os dados do user {$data}";
    }
}


abstract class ConfigDatabase{
    protected $user;
    protected $pass;
    protected $data;
    protected $db;
    public function __construct(APIDatabaseInterface $database){
        $this->db = $database;
    }
}

class ConnectDatabase extends ConfigDatabase{
    public function __construct(APIDatabaseInterface $database, $user, $pass, $data){
        parent::__construct($database);
        $this->user = $user;
        $this->pass = $pass;
        $this->data = $data;
    }

    public function connect(){
        $this->db->ConfigConnection($this->user, $this->pass);
    }

    public function Insert(){
        $this->db->InsertUser($this->data);
    }

    public function Update(){
        $this->db->UpdateUser($this->data);
    }
}


$mariaDb = new ConnectDatabase(new MariaDB, "root", "123", "alanzin");
$mariaDb->connect();

$mongoDb = new ConnectDatabase(new MongoDB, "root", "123", "alanzin");
$mongoDb->connect();
?>
```


<h1>Em desenvolvimento</h1>
As partes faltantes, serão adicionadas em breve
