# SOLID

## S (Single Responsability Principle - SRP)
  - Uma classe so deve ter um motivo para ser modificada
  - Uma classe deve ser especializada em um unico assunto e deve ter apenas uma responsabilidade dentro do software
  - A classe deve ter uma unica tarefa ou ação para executar

Problemas:
 - Quando utilizamos orientação a objetos acabamos dando a uma classe mais de uma reponsabiliade, e criando classes que fazem de tudo, que são conhecidas como **god class**
 - Modificar um **god class** onde vc quer modificar uma responsabilidade sem comprometer as outras, se torna um trabalho dificil. E faz com que a cada alteração gere um grande nivel de incerteza, principalmente se não existir testes automatizados

### Exemplos:

#### Exemplo 1:

Erro:
```javascript
public class GerarNotaFiscal {
  public function ValidarCamposCliente() {...}
  public function GerarImpostoFiscal(decimal valorVenda) {...}
  public function SalvarNotaFisca(decimal ValorFinal) {...}
  public function ImprimirNota() {...}
  public function EnviarCupomPorEmail() {...}
}

```

Correção:
```javascript

public class Cliente {
  public function ValidarCamposCliente() {...}
}
public class Imposto {
  public function GerarImpostoFiscal(decimal valorVenda) {...}
}
public class NotaFiscal {
  public function SalvarNotaFiscal(decimal ValorFinal) {...}
  public function ImprimirNota() {...}
}
public class Cupom {
  public function EnviarCupomPorEmail() {...}
}
```

#### Exemplo 2

Erro:
```php
class Order {
  public function calculaTotalSum() {...}
  public function getItems() {...}
  public function getItemCount() {...}
  public function addItem($item) {...}
  public function deleteItem($item) {...}

  public function printOrder() {...}
  public function showOrder() {...}

  public function load() {...}
  public function save() {...}
  public function update() {...}
  public function delete() {...}

}

```
> Ela quebra o principio (SRP) devido a mesma além de lidar com informações do pedido, ela é responsavel pela exibição o pedido e manipulação dos dados

Problemas gerados:
 - Falta de coesão - devido a classe ter várias responsabilidades que não são as suas
 - Alto acoplamento - Ou seja, mais responsabilidades geram um alto nivel de dependencias, deixando o sistema engessado e frágil para alterações
 - Dificuldade para implementar testes automatizado, tornando uma grande dificuldade para mockar esses tipos de classes
 - Dificuldade de reaproveitamento

Correção:
```php

class Order {
  public function calculaTotalSum() {...}
  public function getItems() {...}
  public function getItemCount() {...}
  public function addItem($item) {...}
  public function deleteItem($item) {...}
}
class OrderViewer {
  public function printOrder() {...}
  public function showOrder() {...}
}

class OrderRepository {
  public function load(...) {...}
  public function save(...) {...}
  public function update(...) {...}
  public function delete(...) {...}
}

}

```


### Exemplo 3


Erro:
```javascript
class Car {
  turnOnEngine() {...}
  turnOffEngine() {...}
  turnOnAc() {...}
  turnOffAc() {...}
  turnOnMusic() {...}
  turnOffMusic() {...}
}

```

Correção:
```javascript
class CarIngnition {
  turnOnEngine() {console.log('on engine')}
  turnOffEngine() {console.log('ff engine')}
}
class AirConditionSystem {
  turnOnAc() {console.log('on ac')}
  turnOffAc() {console.log('off ac')}
}

class MusicSystem {
  turnOnMusic() {console.log('on music')}
  turnOffMusic() {console.log('off music')}
}

class Car {
    public ingnitions: CarIngnition;
    public air: AirConditionSystem;
    public music: MusicSystem

    constructor(ingnitions: CarIngnition, air: AirConditionSystem, music: MusicSystem) {
        this.ingnitions = ingnitions;
        this.air = air;
        this.music = music;

    }
}

const ingnitions = new CarIngnition();
const air = new AirConditionSystem();
const music = new MusicSystem();

const car = new Car(ingnitions, air, music);

car.ingnitions.turnOnEngine();
car.air.turnOnAc();
car.music.turnOnMusic();

```
> Usou aqui o D do Solid (Dependence Injection)

> O SRP não se limita a só classe, Da para aplicar em métodos e funções

> é a base para outros principios


> Desenvolvedores têm tendência a tentar resolver problemas especificos com soluções gerais. Isso leva ao acoplamento e complexidade. Em vez de ser geral, o código deve ser especifico. (Greg Young)

> Se o nome tiver mais que duas palavras, possa ser que esteja que esteja tendo mais que uma resposabilidade. ex: public void ConsultarSalvarUsuario() {...}


## O - (Open / Close Principle - OCP) - Objetos e entidades devem estar aberto para extensão e fechado para modificação

 - Você tem que ser capaz de estender um comportamento de uma classe sem a necessidade de modifica-la


### Exemplos

#### Exemplo 1
Erro:
```javascript
class EnviadorDeEmail {
  public function enviar(mensagem: String, assunto: String, tipoEmail: TipoEmail) {
    if(tipoEmail === TipoEmai.texto) {
      mensagem = this.removerFormatacao(mensagem);
    
    } else if(tipoEmail === TipoEmail.Html) {
      mensagem = this.InserirHtml(mensagem);
    
    } else if(tipoEmail === TipoEmail.Criptografado) {
      mensagem = this.CriptografarMensagem(mensagem);
    }
  }
}
```

Solução:
```javascript

abstract class Email {
  abstract enviar(assunto: String, mensagem: String);
}

class TextoEmail extends Email {
    enviar(assunto: String, mensagem: String): void {
    }
}

class HtmlEmail extends Email {
    enviar(assunto: String, mensagem: String): void {
    }
}

class CriptografadoEmail extends Email {
    enviar(assunto: String, mensagem: String): void {
    }
}

```

#### Exemplo 2

Erro:
```php 
class ContratoClt {
  public function salario() {...}
}

class Estagio {
  public function bolsaAuxilio() {...}
}

class FolhaDePagamento {
  protected $saldo;

  public function calcular($contrato) {
    if($contrato instaceof ContratoClt) {
      $this->saldo = $contrato->salario();
    }else if($contrato instaceof Estagio) {
      $this->saldo = $contrato->bolsaAuxilio();
    }
  }
}

```
> se acaso quesessemos adicionar um classe de ContratoPJ sera necessário modificar a classe FolhaPagamento, e isso quebra o principio Open/Closed do Solid

Conselho:
> Separe o comportamento extensivél por trás de uma interface e inverta as dependências. (Uncle Bob)
> Abstrair o contexto em uma interface

Solução:

```php 
interface Remuneravel {
  public function remuneracao();
}

class ContratoClt implements Remuneravel {
  public function remuneracao() {...}
}

class ContratoPJ implements Remuneravel {
  public function remuneracao() {...}
}

class Estagio implements Remuneravel {
  public function remuneracao() {...}
}

class FolhaDePagamento {
  protected $saldo;

  public function  calcular(Remuneravel $contrato) {
    $this->saldo = $contrato->remuneracao();
  }
}

class main {
  public run () {
    $folhaDePagamento = new FolhaDePagamento(new Remuneravel());
    $folhaDePagamento->remuneracao();
  }
}

```


Diagrama:

```
                  |-------------|
                  |ContratoPJ   |
                  |-------------|
                        |
  |-------------|       |
  |ContratoClt  |       | (implements)
  |-------------|       |
        | (implements)  |   (import)  |-------------|  
       \./              |-------------|ContratoPJ   |  
  |-------------|       |             |-------------| 
  |Remuneralvel | <------
  |-------------|       |(implements)
                        |
                  |-------------|
                  |Estagio      |
                  |-------------|



```

### Exemplo 3

```javascript
// domain/ApiClient.ts
 
export interface ApiClient {
  createUser: (user: User) => Promise<void>;
  getUserByEmail: (email: string) => Promise<User>;
}

// infra/HttpClient.ts
import axios from "axios";
import ApiClient from "domain/ApiClient";

export function HttpClient(): ApiClient {
  return {
    createUser: async (user: User) => {
      return axios.post(/* ... */);
    },
    getUserByEmail: async (email: string) => {
      return axios.get(/* ... */);
    },
  };
}

// domain/SignupService.ts
import ApiClient from "domain/ApiClient"; // ✅ the domain depends on an abstraction of the infra

export function SignupService(client: ApiClient) {
  return async (email: string, password: string) => {
    const existingUser = await client.getUserByEmail(email);

    if (existingUser) {
      throw new Error("Email already used");
    }

    return client.createUser({ email, password });
  };
}


// index.ts
import SignupService from "domain/signup";
import HttpClient from "infra/HttpClient";

const signup = SignupService(HttpClient());

signup("bob@bob.com", "pwd123");

```

## L (Liskov Substitution Principle - LSP) - Substituição de Liskove

> Uma classe derivada deve ser substituível por sua classes base

### Exemplo 1
```php
class A {
  public function getNome() {
    echo 'A';
  }
}

class B extends A {
  public function getNome() {
    echo 'B';
  }
}

$object1 = new A();
$object2 = new B();

function imprimeNome(A $object){
  return $object->getNome();
}

imprimeNome($object1);
imprimeNome($object2);

```

### Exemplos que quebra o principio
```c#
public class Conta {
  public virtual void Cobrar(decimal valor) {
    return valor * 10;
  }
}

public class ContaGratuita : Conta{
  public override void Cobrar(decimal valor) {
    /// nao faz nada aqui
  }
}
```

```javascript
class Ave {
  public function voar(): void {...}
  public function comer(): void {...}
}

class Aguia extends Ave {
  public function voar(): void {}
  public function comer(): void {...}
}

class Pinguim extends Ave {
  public function voar(): void {
    throw new Exeception('Nao consegue voar')
  }
  public function comer(): void {...}
}

```

> Nao utilize herança para reaproveitamento de código

> Herança deve ser evitada na maioria dos casos

> Conforme adiciona novas classes, algumas delas podem não ter o mesmo comportamento definido na classe base e ai que entra o problema do DuckType, nem todo pato voa.

> Utilizar interfaces para os comportamentos que estão em um tipo e não estão em outro. IFlyable (para patos que possam voar)

##### Correção do exemplo acima que quebra o principios de Liskov

```javascript
class Aves {
  public function comer(): void {...}
}

class AvesQueVoam {
  public function voar(): void {...}
  public function comer(): void {...}
}

class Aguia implements AveQueVoam {
  public function voar(): void {}
  public function comer(): void {...}
}

class Pinguim implements Aves {
  public function comer(): void {...}
}

```


## I - (Interface Segregation Principle - ISP)

> Onde uma classe não deve implementar interfaces e metodos que não vao utilizar

> Devemos criar interfaces especificas, ao inves de criar interfaces genericas

#### Exemplos

Erro:
```javascript
class Ave {
  public function voar(): void {...}
  public function comer(): void {...}
}

class Aguia extends Ave {
  public function voar(): void {}
  public function comer(): void {...}
}

class Pinguim extends Ave {
  public function voar(): void {
    throw new Exeception('Nao consegue voar')
  }
  public function comer(): void {...}
}

```
> Quebra o principio de Interface segregation e o Liskov Substitution, devido ao pinguim nao saber voar

> O ISP reconhece que existem objetos que requerem interfaces não coesivas; no entanto, sugere que os clientes não devem conhecê-los como uma única classe. Em vez disso, os clientes devem saber sobre classes base abstratas que possuem interfaces coesas.

```javascript
class Aves {
  public function comer(): void {...}
}

class AvesQueVoam extends {
  public function voar(): void {...}
}

class Aguia implements AvesQueVoam {
  public function voar(): void {}
  public function comer(): void {...}
}

class Pinguim implements Aves {
  public function comer(): void {...}
}

```


## D - (Depency Inversion Principle - DIP)

> Devemos depender de abstrações e não de implementações

> Modulos de alto nivel, não devem depender de modulos de baixo nível, ambos devem depender da abstração, e que abstrações não devem depender de detalhes, detalhes devem depender de abstrações (Uncle Bob)

> Inversão de dependecias é diferente de injeção de dependencia

> Modulos de alto nivel sao modulos que dependende de algo para serem executados. So modulos que dependende de outras classes concretas

### Exemplo

```php 
use MySQLConnection;

class PasswordReminder {
  private $dbConnection;

  public function __construct() {
    $this->dbConnection = new MySQLConnection();
  }

  // do something
}

```

> Aqui temos um alto nivel de acoplamento. Onde a classe PasswordRemider tem a responsabilidade de criar uma instancia da classe MySQLConnection.

> Se fossemos reaproveitar essa classe em outro sistema, teriamos que levar a classe MySQLConnection junto, ou seja, tem um forte acoplamento


Solução:
```php
use MySQLConnection;

class PasswordReminder {
  private $dbConnection;

  public function __construct(MySQLConnection $dbConnection) {
    $this->dbConnection = $dbConnection;
  }

  // do something
}
```
> utilizamos aqui a injecão de dependecia
> Mas mesmo assim precisamos melhorar o nivel de acoplamento. Pois ele esta violando o principio de inversão de dependencia e ainda viola o principio Open/Closed. Pois, imagine que por algum motivo queiramos modificar o banco de dados para um Postgress ou Mongodb. Com iso, obrigatoriamente teriamos que alterar nossa classe.

> No nosso modulo, a classes PasswordReminder depende de MySQLConnection. Sendo assim, PasswordReminder é um modulo de alto nivel e o MySQLCOnnection é um modulo de baixo nível, que por sua vez é uma implementação e não uma abstração

Solução:
```php
interface DBConnectionInterface {
  public function connect() {...};
}

class MySQLConnection implements DBConnectionInterface {
    public function connect() {...};
}

class MongoConnection implements DBConnectionInterface {
    public function connect() {...};
}

class PostgreeConnection implements DBConnectionInterface {
    public function connect() {...};
}


class PasswordReminder {
  private $dbConnection;

  public function __construct(DBConnectionInterface $dbConnection) {
    $this->dbConnection = $dbConnection;
  }

  // do something
}

```

> Aqui nesse codigo estamos respeitando tando o LSP quanto o OCP

### Exemplo 4

Erro: 
```javascript 
// repos/userRepo.ts

/**
 * @class UserRepo
 * @desc Responsible for pulling users from persistence.
 **/

export class UserRepo {
  constructor () {}

  getUsers (): Promise<User[]> {
  }
}

// controllers/userController.ts

import { UserRepo } from '../repos' // Bad

/**
 * @class UserController
 * @desc Responsible for handling API requests for the
 * /user route.
 **/

class UserController {
  private userRepo: UserRepo;

  constructor (userRepo: UserRepo) { // Better, inject via constructor
    this.userRepo = userRepo; 
  }

  async handleGetUsers (req, res): Promise<void> {
    const users = await this.userRepo.getUsers();
    return res.status(200).json({ users });
  }
}

```

> UserController depende de UserRepo

Diagrama

```
 |               |           |        |
 |UserController | ------- > |UserRepo|
 |               |           |        | 

```


Correção:

```javascript

// repos/userRepo.ts
/**
 * @interface IUserRepo
 * @desc Responsible for pulling users from persistence.
 **/

export interface IUserRepo {          // Exported
  getUsers (): Promise<User[]>
}

class UserRepo implements IUserRepo { // Not exported
  constructor () {}

  getUsers (): Promise<User[]> {
  
  }
}


// controllers/userController.ts
import { IUserRepo } from '../repos' // Good!

/**
 * @class UserController
 * @desc Responsible for handling API requests for the
 * /user route.
 **/

class UserController {
  private userRepo: IUserRepo; // like here

  constructor (userRepo: IUserRepo) { // and here
    this.userRepo = userRepo;
  }

  async handleGetUsers (req, res): Promise<void> {
    const users = await this.userRepo.getUsers();
    return res.status(200).json({ users });
  }
}

```

Diagrama

```
 |               |           |         |      |         |
 |UserController | ------- > |IUserRepo| <----| UserRepo|
 |               |           |         |      |         |

```

>  Alterando todas as referências de classes concretas para interfaces , acabamos de inverter o gráfico de dependência e criar um limite de arquitetura entre os dois componentes.