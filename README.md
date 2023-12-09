# clean-code-swift

## Índice
  1. [Introdução](#introdução)
  2. [Variáveis](#variáveis)
  3. [Funções](#funções)
  4. [Objetos e Estruturas de Dados](#objetos-e-estruturas-de-dados)
  5. [Classes](#classes)
  6. [SOLID](#solid)
  7. [Testes](#testes)
  8. [Concorrência](#concorrência)
  9. [Tratamento de Erros](#tratamento-de-erros)
  10. [Formatação](#formatação)
  11. [Comentários](#comentários)

# Introdução
![Imagem humorística da estimativa de qualidade do software baseado na contagem de quantos palavrões você gritou enquanto lia o código.](http://www.osnews.com/images/comics/wtfm.jpg)

Princípios de Engenharia de Software, do livro de Robert C. Martin [*Clean Code*](https://www.amazon.com.br/C%C3%B3digo-Limpo-Habilidades-Pr%C3%A1ticas-Software/dp/8576082675), adaptados para Swift. Este não é um guia de estilo. É um guia para produzir código [legível, reutilizável e passível de refatoração](https://github.com/ryanmcdermott/3rs-of-software-architecture) em Swift.

Não é necessário seguir rigorosamente todos os princípios demonstrados, e ainda menos eles são um consenso universal. Esses princípios são diretrizes e nada mais, contudo, foram codificados ao longo de muitos anos de experiência coletiva pelos autores de *Clean Code*.

A engenharia de software tem pouco mais de 50 anos e ainda estamos aprendendo muito. Quando a arquitetura de software for tão antiga quanto a própria arquitetura, talvez tenhamos regras mais rígidas para seguir. Por enquanto, deixe que essas diretrizes sirvam como critério para avaliar a qualidade do código Swift que você e sua equipe produzem.

Mais uma coisa: aprender isso não irá transformá-lo imediatamente em um desenvolvedor de software melhor, e trabalhar com esses princípios por muitos anos não garante que você não cometerá erros. Cada porção de código começa como um rascunho, como argila molhada sendo moldada em sua forma final. Finalmente, talhamos as imperfeições ao revisar com nossos colegas. Não se sinta culpado pelos primeiros rascunhos que ainda precisam de melhorias. Em vez disso, concentre-se em aprimorar seu código.

# Variáveis
### Use nomes de variáveis que tenham significado e sejam pronunciáveis

**Ruim:**
```swift
let currentDateStr = DateFormatter.localizedString(from: Date(), dateStyle: .short, timeStyle: .none)
```

**Bom:**
```swift
let currentDate = DateFormatter.localizedString(from: Date(), dateStyle: .short, timeStyle: .none)
```

**[⬆ voltar ao topo](#Índice)**

### Use o mesmo vocabulário para o mesmo tipo de variável

**Ruim:**
```swift
getUserInfo()
getClientData()
getCustomerRecord()
```

**Bom:**
```swift
getUser()
```

**[⬆ voltar ao topo](#Índice)**

### Use nomes pesquisáveis
Nós iremos ler mais código que escrever. É importante que o código que escrevemos seja legível e pesquisável. *Não* dando nomes em variáveis que sejam significativos para entender nosso programa, machucamos nossos leitores. Torne seus nomes pesquisáveis.

**Ruim:**
```swift
// Para que diabos serve 86400000?
setTimeout(blastOff, 86400000)
```

**Bom:**
```swift
// Declare-as como `let` ou `var` em letras maiúsculas.
let millisecondsPerDay = 86400000

setTimeout(blastOff, millisecondsPerDay)
```

**[⬆ voltar ao topo](#Índice)**

### Use variáveis explicativas
**Ruim:**
```swift
let address = "One Infinite Loop, Cupertino 95014"
let cityZipCodeRegex = try! NSRegularExpression(pattern: "^[^,\\]+[,\\\\\\s]+(.+?)\\s*(\\d{5})?$")
saveCityZipCode(address.match(cityZipCodeRegex)![1], address.match(cityZipCodeRegex)![2])
```

**Bom:**
```swift
let address = "One Infinite Loop, Cupertino 95014"
let cityZipCodeRegex = try! NSRegularExpression(pattern: "^[^,\\]+[,\\\\\\s]+(.+?)\\s*(\\d{5})?$")
if let match = cityZipCodeRegex.firstMatch(in: address) {
    let city = (address as NSString).substring(with: match.range(at: 1))
    let zipCode = (address as NSString).substring(with: match.range(at: 2))
    saveCityZipCode(city, zipCode)
}
```

**[⬆ voltar ao topo](#Índice)**

### Evite Mapeamento Mental
Explicito é melhor que implícito.

**Ruim:**
```swift
let locations = ["Austin", "New York", "San Francisco"]
locations.forEach({ l in
    doStuff()
    doSomeOtherStuff()
    // ...
    // ...
    // ...
    // Espera, para que serve o `l` mesmo?
    dispatch(l)
})
```

**Bom:**
```swift
let locations = ["Austin", "New York", "San Francisco"]
locations.forEach({ location in
    doStuff()
    doSomeOtherStuff()
    // ...
    // ...
    // ...
    dispatch(location)
})
```

**[⬆ voltar ao topo](#Índice)**

### Não adicione contextos desnecessários
Se o nome de sua classe/objeto já lhe diz alguma coisa, não as repita nos nomes de suas variáveis.

**Ruim:**
```swift
let car = [
    "carMake": "Honda",
    "carModel": "Accord",
    "carColor": "Blue"
]

func paintCar(car: [String: String], color: String) {
    car["carColor"] = color
}
```

**Bom:**
```swift
let car = [
    "make": "Honda",
    "model": "Accord",
    "color": "Blue"
]

func paintCar(car: [String: String], color: String) {
    car["color"] = color
}
```

**[⬆ voltar ao topo](#Índice)**

# Funções

### Argumentos de funções (idealmente 2 ou menos)

O cuidado na gestão de parâmetros em funções desempenha um papel crucial na facilitação dos testes e na manutenção do código, seguindo os princípios SOLID. Quando uma função possui mais de três argumentos, a complexidade cresce exponencialmente, exigindo testes detalhados para cada parâmetro separadamente.

O ideal é manter no máximo dois argumentos, evitando três sempre que possível. Se a função exigir mais parâmetros, uma alternativa é consolidá-los em um objeto. Uma função com mais de dois argumentos geralmente tenta abranger diversas responsabilidades, sinalizando a necessidade de reavaliação. Em muitos casos, a passagem de um objeto como argumento é suficiente.

Considerando a capacidade do Swift de criar objetos de maneira eficiente, o uso de objetos ao lidar com múltiplos argumentos é uma prática eficaz.

**[⬆ Voltar ao Topo](#Índice)**

**Ruim:**
```swift
func createMenu(title: String, body: String, buttonText: String, cancellable: Bool) {
  // ...
}
```

**Bom:**
```swift
struct MenuViewData {
  let title: String
  let body: String
  let buttonText: String
  let cancellable: Bool
}

func createMenu(viewData: MenuViewData) {
  // ...
}

// Uso:
let viewData = .init(
  title: "Foo", 
  body: "Bar", 
  buttonText: "Baz", 
  cancellable: true
)

createMenu(viewData: viewData)
```

**[⬆ voltar ao topo](#Índice)**

### Use argumentos padrões ao invés de curto circuitar ou usar condicionais

Argumentos padrões são geralmente mais limpos do que curto circuitos. Esteja ciente que se você usá-los, sua função apenas irá fornecer valores padrões para argumentos `undefined`. Outros valores "falsos" como `''`, `""`, `false`, `null`, `0`, e `NaN`, não serão substituídos por valores padrões.

**Ruim:**
```swift
func createMicrobrewery(name: String?) {
  let breweryName = name ?? "Hipster Brew Co."
  // ...
}
```

**Bom:**
```swift
func createMicrobrewery(breweryName: String = "Hipster Brew Co.") {
  // ...
}
```

**[⬆ voltar ao topo](#Índice)**

### Funções devem fazer uma coisa
Essa é de longe a regra mais importante em engenharia de software. Quando funções fazem mais que uma coisa, elas se tornam difíceis de serem compostas, testadas e raciocinadas. Quando você pode isolar uma função para realizar apenas uma ação, elas podem ser refatoradas facilmente e seu código ficará muito mais limpo. Se você não levar mais nada desse guia além disso, você já estará na frente de muitos desenvolvedores.

**Ruim:**
```swift
func emailClients(clients: [Client]) {
  clients.forEach { client in
    let clientRecord = database.lookup(client)
    if clientRecord.isActive() {
      email(client)
    }
  }
}
```

**Bom:**
```swift
func emailActiveClients(clients: [Client]) {
  clients
    .filter { isActiveClient(client: $0) }
    .forEach { email(client: $0) }
}

func isActiveClient(client: Client) -> Bool {
  let clientRecord = database.lookup(client)
  return clientRecord.isActive()
}
```

**[⬆ voltar ao topo](#Índice)**

### Nomes de funções devem dizer o que elas fazem

**Ruim:**
```swift
func addToDate(date: Date, month: Int) {
  // ...
}

let date = Date()

// É difícil dizer pelo nome da função o que é adicionado
addToDate(date: date, month: 1)
```

**Bom:**
```swift
func addMonthToDate(month: Int, date: Date) {
  // ...
}

let date = Date()
addMonthToDate(month: 1, date: date)
```

**[⬆ voltar ao topo](#Índice)**

### Funções devem ter apenas um nível de abstração
Quando você tem mais de um nível de abstração, sua função geralmente está fazendo coisas demais. Dividir suas funções leva a uma melhor reusabilidade e testabilidade.

**Ruim:**
```swift
func parseInput(input: String) {
  // ...
  let inputData = ...
  // ...
  saveData(inputData)
}

func saveData(data: Data) {
  // ...
  database.save(data)
}
```

**Bom:**
```swift
func parseInput(input: String) {
  // ...
  let inputData = ...
  saveData(data: inputData)
}

func saveData(data: Data) {
  // ...
  database.save(data)
}
```

**[⬆ voltar ao topo](#Índice)**

### Remova código duplicado
Faça o melhor para evitar código duplicado. Código duplicado é ruim porque significa que há mais do que um lugar para se mudar alguma coisa se você precisar fazer uma mudança.

Imagine se você tem um restaurante e mantém uma lista de todos os seus clientes em dois lugares: um no qual você mantém a ordem deles para o chef e outro no qual você mantém a ordem deles para a entrega. Se você tiver clientes que cancelam, você agora tem que o fazer em dois lugares. Se você só tiver uma lista, há apenas um lugar para se atualizar!

O que acontece se você esquecer de atualizar em um lugar e não no outro? E se o pedido de entrega chegar enquanto o chef estiver fazendo outra coisa e ele não tiver visto o pedido? Agora você tem um problema.

Muitas vezes, você tem código duplicado porque você tem duas ou mais coisas ligeiramente diferentes, que compartilham muitas coisas em comum, mas suas diferenças os forçam a ter dois ou mais métodos que fazem coisas muito similares. Remover o código duplicado significa criar uma abstração que pode lidar com essas diferenças com apenas uma função/método.

Fazendo isso, você agora tem apenas um lugar para atualizar se algo mudar.

**Ruim:**
```swift
func showDeveloper(name: String) {
  print("Developer: \(name)")
  print("Coding...")
}

func showManager(name: String) {
  print("Manager: \(name)")
  print("Meeting...")
}
```

**Bom:**
```swift
func showPerson(name: String, role: String) {
  print("\(role): \(name)")
  if role == "Developer" {
    print("Coding...")
  } else if role == "Manager" {
    print("Meeting...")
  }
}
```

**[⬆ voltar ao topo](#Índice)**

### Use Objetos Puros
Objetos são ditos serem puros quando eles não compartilham estado com outros objetos. Imagine que você está no espaço sideral e você tem uma nave espacial. Esta nave espacial tem um tanque de combustível. Imagine que existem vários sistemas diferentes na nave espacial que podem modificar esse tanque de combustível.

Existem três tipos diferentes de objetos aqui:

1. **Objeto Impuro:**
```swift
class Spaceship {
  var fuelTank: Int

  init(fuelTank: Int) {
    self.fuelTank = fuelTank
  }

  func launch() {
    Rocket().ignite(boosters: self.fuelTank)
  }

  func addFuel(fuel: Int) {
    self.fuelTank += fuel
  }
}
```

2. **Objeto Menos Puro:**
```swift
class Spaceship {
  var fuelTank: Int

  init(fuelTank: Int) {
    self.fuelTank = fuelTank
  }

  func launch() {
    Rocket().ignite(boosters: self.fuelTank)
  }

  func visitSpaceStation(spaceStation: SpaceStation) {
    spaceStation.refuel(ship: self)
  }
}

class SpaceStation {
  func refuel(ship: Spaceship) {
    ship.addFuel(fuel: self.fuelTank)
  }

  var fuelTank: Int
}
```

3. **Objeto Puro:**
```swift
class Spaceship {
  var fuelTank: Int

  init(fuelTank: Int) {
    self.fuelTank = fuelTank
  }

  func launch() {
    Rocket().ignite(boosters: self.fuelTank)
  }

  func refuel(amount: Int) -> Spaceship {
    return Spaceship(fuelTank: self.fuelTank + amount)
  }
}
```

Por que objetos puros são preferíveis? Eles são mais fáceis de testar e entender. Eles não podem ser mudados por outros sistemas enquanto estão sendo usados. Dados que são passados para eles podem ser confiáveis, e eles não têm efeitos colaterais que podem causar bugs difíceis de rastrear.


**[⬆ voltar ao topo](#Índice)**

### Faça decisões que sejam baseadas em um objeto

**Ruim:**
```swift
if car.engine == "v8" {
  // ...
}

if bike.tires == "fat" {
  // ...
}
```

**Bom:**
```swift
class Engine {
  var type: String
}

class Tire {
  var type: String
}

if car.engine.type == "v8" {
  // ...
}

if bike.tires.type == "fat" {
  // ...
}
```

**[⬆ voltar ao topo](#Índice)**

### Evite o uso de flags como parâmetros de função
Funções que têm sinalizadores booleanos como parâmetros são mais difíceis de entender do que funções que fazem apenas uma coisa. Sinalizadores indicam que a função faz mais de uma coisa. Separe essas funções em várias funções se for o caso.

**Ruim:**
```swift
func createFile(name: String, temporary: Bool) {
  if temporary {
    // Cria um arquivo temporário
  } else {
    // Cria um arquivo permanente
  }
}
```

**Bom:**
```swift
func createTemporaryFile(name: String) {
  // Cria um arquivo temporário
}

func createPermanentFile(name: String) {
  // Cria um arquivo permanente
}
```

**[⬆ voltar ao topo](#Índice)**

### Evite Efeitos Colaterais (parte 1)
Uma função produz um efeito colateral se ela faz alguma coisa que não seja receber um valor de entrada e retornar outro(s) valor(es). Um efeito colateral pode ser escrever em um arquivo, modificar uma variável global, ou acidentalmente transferir todo seu dinheiro para um estranho.

Agora, você precisa de efeitos colaterais ocasionalmente no seu programa. Como no exemplo anterior, você pode precisar escrever em um arquivo. O que você quer fazer é centralizar aonde está fazendo isto. Não tenha diversas funções e classes que escrevam para um arquivo em particular. Tenha um serviço que faça isso. Um e apenas um.

O ponto principal é evitar armadilhas como compartilhar o estado entre objetos sem nenhuma estrutura, usando tipos de dados mutáveis que podem ser escritos por qualquer coisa, e não centralizando onde seu efeito colateral acontece. Se você conseguir fazer isto, você será muito mais feliz que a grande maioria dos outros programadores.

**Ruim:**
```swift
// Variável global referenciada pela função seguinte
// Se tivéssemos outra função que usa esse nome, então seria um vetor (array) e poderia quebrar seu código
var name = "Matheus Gois"

func splitIntoFirstAndLastName() {
  name = name.split(separator: " ").joined(separator: " ")
}

splitIntoFirstAndLastName()

print(name) // 'Matheus Gois'
```

**Bom:**
```swift
func splitIntoFirstAndLastName(name: String) -> (firstName: String, lastName: String) {
  let components = name.split(separator: " ").map { String($0) }
  guard components.count >= 2 else {
    return (firstName: name, lastName: "")
  }
  let firstName = components[0]
  let lastName = components[1..<components.count].joined(separator: " ")
  return (firstName: firstName, lastName: lastName)
}

let fullName = "Matheus Gois"
let nameComponents = splitIntoFirstAndLastName(name: fullName)

print(fullName) // 'Matheus Gois'
print(nameComponents.firstName) // 'Ryan'
print(nameComponents.lastName) // 'McDermott'
```


**[⬆ voltar ao topo](#Índice)**

### Evite Efeitos Colaterais (parte 2)
Em Swift, tipos primitivos são passados por valor e objetos/vetores são passados por referência. No caso de objetos e vetores, se sua função faz uma mudança em um vetor de um carrinho de compras, por exemplo, adicionando um item para ser comprado, então qualquer outra função que use o vetor `cart` também será afetada por essa adição. Isso pode ser ótimo, mas também pode ser ruim. Vamos imaginar uma situação ruim:

O usuário clica no botão "Comprar", botão que invoca a função `purchase` que dispara uma série de requisições e manda o vetor `cart` para o servidor. Devido a uma conexão ruim de internet, a função `purchase` precisa fazer novamente a requisição. Agora, imagine que nesse meio tempo o usuário acidentalmente clique no botão `Adicionar ao carrinho` em um produto que ele não queria antes da requisição começar. Se isto acontecer e a requisição for enviada novamente, então a função `purchase` irá enviar acidentalmente o vetor com o novo produto adicionado porque existe uma referência para o vetor `cart` que a função `addItemToCart` modificou adicionando um produto indesejado.

Uma ótima solução seria que a função `addCartToItem` sempre clonasse o vetor `cart`, editasse-o, e então retornasse seu clone. Isso garante que nenhuma outra função que possua uma referência para o carrinho de compras seja afetada por qualquer mudança feita.

Duas ressalvas desta abordagem:

  1. Podem haver casos onde você realmente quer mudar o objeto de entrada, mas quando você adota este tipo de programação, você vai descobrir que estes casos são bastante raros. A maioria das coisas podem ser refatoradas para não terem efeitos colaterais.

  2. Clonar objetos grandes pode ser bastante caro em termos de desempenho. Com sorte, na prática isso não é um problema, porque existem [ótimas bibliotecas](https://github.com/apple/swift-collections) que permitem que este tipo de programação seja rápida e não seja tão intensa no uso de memória quanto seria se você clonasse manualmente objetos e vetores.

**Ruim:**
```swift
var cart = [
  CartItem(item: "Widget", date: Date()),
  CartItem(item: "Gadget", date: Date())
]

func addItemToCart(item: CartItem) {
  cart.append(item)
}
```

**Bom:**
```swift
func addItemToCart(cart: [CartItem], item: CartItem) -> [CartItem] {
  var updatedCart = cart
  updatedCart.append(item)
  return updatedCart
}
```

### Não escreva em funções globais
Poluir globais é uma prática ruim em Swift porque você pode causar conflito com outra biblioteca e o usuário da sua API não faria a menor ideia até que ele tivesse um exceção sendo levantada em produção. Vamos pensar em um exemplo: e se você quisesse estender o método nativo Array do Swift para ter um método `diff` que poderia mostrar a diferença entre dois vetores? Você poderia escrever sua nova função em `Array.prototype`, mas poderia colidir com outra biblioteca que tentou fazer a mesma coisa. E se esta outra biblioteca estava apenas usando `diff` para achar a diferença entre o primeiro e último elemento de um vetor?

**Ruim:**
```swift
extension Array {
  func diff(_ comparisonArray: [Element]) -> [Element] {
    let hash = Set(comparisonArray)
    return filter { !hash.contains($0) }
  }
}
```

**Bom:**
```swift
class ExtendedArray<Element>: Array<Element> {
  func diff(_ comparisonArray: [Element]) -> [Element] {
    let hash = Set(comparisonArray)
    return filter { !hash.contains($0) }
  }
}
```


**[⬆ voltar ao topo](#Índice)**

### Favoreça programação funcional sobre programação imperativa
Swift não é uma linguagem funcional da mesma forma que Haskell é, mas tem um toque de funcional em si. Linguagens funcionais são mais limpas e fáceis de se testar. Favoreça esse tipo de programação quando puder.

**Ruim:**
```swift
let programmerOutput = [
  Programmer(name: "Uncle Bobby", linesOfCode: 500),
  Programmer(name: "Suzie Q", linesOfCode: 1500),
  Programmer(name: "Jimmy Gosling", linesOfCode: 150),
  Programmer(name: "Gracie Hopper", linesOfCode: 1000)
]

var totalOutput = 0

for programmer in programmerOutput {
  totalOutput += programmer.linesOfCode
}
```

**Bom:**
```swift
let programmerOutput = [
  Programmer(name: "Uncle Bobby", linesOfCode: 500),
  Programmer(name: "Suzie Q", linesOfCode: 1500),
  Programmer(name: "Jimmy Gosling", linesOfCode: 150),
  Programmer(name: "Gracie Hopper", linesOfCode: 1000)
]

let totalOutput = programmerOutput
  .map { $0.linesOfCode }
  .reduce(0, +)
```

**[⬆ volta ao topo](#Índice)**

### Encapsule condicionais

**Ruim:**
```swift
if fsm.state == "fetching" && isEmpty(listNode) {
  // ...
}
```

**Bom:**
```swift
func shouldShowSpinner(fsm: FSM, listNode: Node) -> Bool {
  return fsm.state == "fetching" && isEmpty(listNode)
}

if shouldShowSpinner(fsm: fsmInstance, listNode: listNodeInstance) {
  // ...
}
```


**[⬆ voltar ao topo](#Índice)**

### Evite negações de condicionais

**Ruim:**
```swift
func isDOMNodeNotPresent(node: Node) -> Bool {
  // ...
}

if !isDOMNodeNotPresent(node: node) {
  // ...
}
```

**Bom:**
```swift
func isDOMNodePresent(node: Node) -> Bool {
  // ...
}

if isDOMNodePresent(node: node) {
  // ...
}
```


**[⬆ voltar ao topo](#Índice)**

### Evite condicionais
Esta parece ser uma tarefa impossível. Da primeira vez que as pessoas escutam isso, a maioria diz, “como eu supostamente faria alguma coisa sem usar `if`? ” A resposta é que você pode usar polimorfismo para realizar a mesma tarefa em diversos casos. A segunda questão é geralmente, “bom, isso é ótimo, mas porque eu deveria fazer isso?” A resposta é um conceito de código limpo aprendido previamente: uma função deve fazer apenas uma coisa. Quando você tem classes e funções que tem declarações `if`, você esta dizendo para seu usuário que sua função faz mais de uma coisa. Relembre-se, apenas uma coisa.

**Ruim:**
```swift
class Airplane {
  // ...
  func getCruisingAltitude() -> Int {
    switch self.type {
      case "777":
        return self.getMaxAltitude() - self.getPassengerCount()
      case "Air Force One":
        return self.getMaxAltitude()
      case "Cessna":
        return self.getMaxAltitude() - self.getFuelExpenditure()
      default:
        return 0
    }
  }
}
```

**Bom:**
```swift
class Airplane {
  // ...
}

class Boeing777: Airplane {
  // ...
  func getCruisingAltitude() -> Int {
    return self.getMaxAltitude() - self.getPassengerCount()
  }
}

class AirForceOne: Airplane {
  // ...
  func getCruisingAltitude() -> Int {
    return self.getMaxAltitude()
  }
}

class Cessna: Airplane {
  // ...
  func getCruisingAltitude() -> Int {
    return self.getMaxAltitude() - self.getFuelExpenditure()
  }
}
```


**[⬆ voltar ao topo](#Índice)**

### Evite checagem de tipos (parte 1)
Swift não possui tipos, o que significa que suas funções podem receber qualquer tipo de argumento. Algumas vezes esta liberdade pode te morder, e se torna tentador fazer checagem de tipos em suas funções. Existem muitas formas de evitar ter que fazer isso. A primeira coisa a se considerar são APIs consistentes.

**Ruim:**
```swift
func travelToTexas(vehicle: Any) {
  if let bicycle = vehicle as? Bicycle {
    bicycle.pedal(currentLocation: self.currentLocation, newLocation: Location("texas"))
  } else if let car = vehicle as? Car {
    car.drive(currentLocation: self.currentLocation, newLocation: Location("texas"))
  }
}
```

**Bom:**
```swift
func travelToTexas(vehicle: Vehicle) {
  vehicle.move(currentLocation: self.currentLocation, newLocation: Location("texas"))
}
```


**[⬆ voltar ao topo](#Índice)**

### Evite checagem de tipos (parte 2)
Se você estiver trabalhando com valores primitivos básicos como strings e inteiros, e você não pode usar polimorfismo, mas ainda sente a necessidade de checar o tipo, você deveria considerar usar TypeScript. É uma excelente alternativa para o Swift normal, já que fornece uma tipagem estática sobre a sintaxe padrão do Swift. O problema com checagem manual em Swift é que para se fazer bem feito requer tanta verborragia extra que a falsa “tipagem-segura” que você consegue não compensa pela perca de legibilidade. Mantenha seu Swift limpo, escreva bons testes, e tenha boas revisões de código. Ou, de outra forma, faça tudo isso, mas com TypeScript (que, como eu falei, é uma ótima alternativa!).

**Ruim:**
```swift
func combine(val1: Any, val2: Any) -> String {
  if let number1 = val1 as? Int, let number2 = val2 as? Int {
    return String(number1 + number2)
  } else if let string1 = val1 as? String, let string2 = val2 as? String {
    return string1 + string2
  }

  fatalError("Must be of type String or Number")
}
```

**Bom:**
```swift
func combine(val1: Any, val2: Any) -> String {
  return String(describing: val1) + String(describing: val2)
}
```


**[⬆ voltar ao topo](#Índice)**

### Remova código morto
Código morto é tão ruim quanto código duplicado. Não existe nenhum motivo para deixá-lo em seu código. Se ele não estiver sendo chamado, livre-se dele. Ele ainda estará a salvo no seu histórico de versionamento se ainda precisar dele.

**Ruim:**
```swift
func oldRequestModule(url: String) {
  // ...
}

func newRequestModule(url: String) {
  // ...
}

let req = newRequestModule
inventoryTracker(item: "apples", requestModule: req, url: "www.inventory-awesome.io")
```

**Bom:**
```swift
func newRequestModule(url: String) {
  // ...
}

let req = newRequestModule
inventoryTracker(item: "apples", requestModule: req, url: "www.inventory-awesome.io")
```

**[⬆ voltar ao topo](#Índice)**

# Objetos e Estruturas de Dados
### Use getters e setters
Usar getters e setters para acessar dados em objetos é bem melhor que simplesmente procurar por uma propriedade em um objeto. "Por quê?", você deve perguntar. Bem, aqui vai uma lista desorganizada de motivos:

* Quando você quer fazer mais além de pegar (get) a propriedade de um objeto, você não tem que procurar e mudar todos os acessores do seu código
* Torna mais fácil fazer validação quando estiver dando um `set`
* Encapsula a representação interna
* Mais fácil de adicionar logs e tratamento de erros quando dando get and set
* Você pode usar lazy loading nas propriedades de seu objeto, digamos, por exemplo, pegando ele de um servidor.

**Ruim:**
```swift
func makeBankAccount() -> [String: Any] {
  // ...

  return [
    "balance": 0,
    // ...
  ]
}

let account = makeBankAccount()
account["balance"] = 100
```

**Bom:**
```swift
func makeBankAccount() -> BankAccount {
  // este é privado
  var balance = 0

  // um "getter", feito público através do objeto retornado abaixo
  func getBalance() -> Int {
    return balance
  }

  // um "setter", feito público através do objeto retornado abaixo
  func setBalance(amount: Int) {
    // ... validate before updating the balance
    balance = amount
  }

  return BankAccount(getBalance: getBalance, setBalance: setBalance)
}

let account = makeBankAccount()
account.setBalance(100)
```

**[⬆ voltar ao topo](#Índice)**

### Faça objetos terem membros privados
Isto pode ser alcançado através de closures (para ES5 e além).

**Ruim:**
```swift
class Employee {
  var name: String

  init(name: String) {
    self.name = name
  }

  func getName() -> String {
    return self.name
  }
}

let employee = Employee(name: "John Doe")
print("Employee name: \(employee.getName())") // Employee name: John Doe
employee.name = "Jane Doe"
print("Employee name: \(employee.getName())") // Employee name: Jane Doe
```

**Bom:**
```swift
func makeEmployee(name: String) -> () -> String {
  var privateName = name

  return {
    return privateName
  }
}

let employee = makeEmployee(name: "John Doe")
print("Employee name: \(employee())") // Employee name: John Doe
```

**[⬆ voltar ao topo](#Índice)**


# Classes

### Use encadeamento de métodos
Este padrão é muito útil em Swift e você o verá em muitas bibliotecas como jQuery e Lodash. Ele permite que seu código seja expressivo e menos verboso. Por esse motivo, eu digo, use encadeamento de métodos e dê uma olhada em como o seu código ficará mais limpo. Em suas funções de classes, apenas retorne `self` no final de cada função, e você poderá encadear mais métodos de classe nele.

**Ruim:**
```swift
class Car {
  var make: String
  var model: String
  var color: String

  init(make: String, model: String, color: String) {
    self.make = make
    self.model = model
    self.color = color
  }

  func setMake(_ make: String) -> Car {
    self.make = make
    return self
  }

  func setModel(_ model: String) -> Car {
    self.model = model
    return self
  }

  func setColor(_ color: String) -> Car {
    self.color = color
    return self
  }

  func save() {
    print("\(self.make) \(self.model) \(self.color)")
  }
}

let car = Car(make: "Ford", model: "F-150", color: "red")
car.setColor("pink").save()
```

**Bom:**
```swift
class Car {
  private var make: String
  private var model: String
  private var color: String

  init(make: String, model: String, color: String) {
    self.make = make
    self.model = model
    self.color = color
  }

  func setMake(_ make: String) -> Car {
    self.make = make
    return self
  }

  func setModel(_ model: String) -> Car {
    self.model = model
    return self
  }

  func setColor(_ color: String) -> Car {
    self.color = color
    return self
  }

  func save() -> Car {
    print("\(self.make) \(self.model) \(self.color)")
    return self
  }
}

let car = Car(make: "Ford", model: "F-150", color: "red")
car.setColor("pink").save()
```

**[⬆ voltar ao topo](#Índice)**

### Prefira composição ao invés de herança
Como dito famosamente em [*Padrão de projeto*](https://pt.wikipedia.org/wiki/Padr%C3%A3o_de_projeto_de_software) pela Gangue dos Quatro, você deve preferir composição sobre herança onde você puder. Existem muitas boas razões para usar herança e muitas boas razões para se usar composição. O ponto principal para essa máxima é que se sua mente for instintivamente para a herança, tente pensar se composição poderia modelar melhor o seu problema. Em alguns casos pode.

Você deve estar pensando então, "quando eu deveria usar herança?" Isso depende especificamente do seu problema, mas essa é uma lista decente de quando herança faz mais sentido que composição:

1. Sua herança representa uma relação de "isto-é" e não uma relação de "isto-tem" (Human→Animal vs. User->UserDetails)
2. Você pode reutilizar código de classes de base (Humanos podem se mover como todos os animais).
3. Você quer fazer mudanças globais para classes derivadas mudando apenas a classe base. (Mudar o custo calórico para todos os animais quando se movem).

**Ruim:**
```swift
class Employee {
  var name: String
  var email: String

  init(name: String, email: String) {
    self.name = name
    self.email = email
  }

  // ...
}

// Ruim porque Employees (Empregados) "tem" dados de impostos. EmployeeTaxData não é um tipo de Employee
class EmployeeTaxData: Employee {
  var ssn: String
  var salary: Double

  init(name: String, email: String, ssn: String, salary: Double) {
    self.ssn = ssn
    self.salary = salary
    super.init(name: name, email: email)
  }

  // ...
}
```

**Bom:**
```swift
class EmployeeTaxData {
  var ssn: String
  var salary: Double

  init(ssn: String, salary: Double) {
    self.ssn = ssn
    self.salary = salary
  }

  // ...
}

class Employee {
  var name: String
  var email: String
  var taxData: EmployeeTaxData?

  init(name: String, email: String) {
    self.name = name
    self.email = email
  }

  func setTaxData(ssn: String, salary: Double) {
    self.taxData = EmployeeTaxData(ssn: ssn, salary: salary)
  }
  // ...
}
```

**[⬆ voltar ao topo](#Índice)**

# SOLID
### Princípio da Responsabilidade Única (SRP)
Como dito em Código Limpo, "Nunca deveria haver mais de um motivo para uma classe ter que mudar". É tentador empacotar uma classe em excesso com muitas funcionalidades, como quando você pode levar apenas uma mala em seu voo. O problema com isso é que sua classe não será conceitualmente coesa e dar-lhe-á diversos motivos para mudá-la. Minimizar o número de vezes que você precisa mudar uma classe é importante, porque, se muitas funcionalidades estão em uma classe e você mudar uma porção dela, pode ser difícil entender como isto afetará outros módulos que dependem dela no seu código.

**Ruim:**
```swift
class UserSettings {
  var user: User

  init(user: User) {
    self.user = user
  }

  func changeSettings(settings: Settings) {
    if self.verifyCredentials() {
      // ...
    }
  }

  func verifyCredentials() -> Bool {
    // ...
  }
}
```

**Bom:**
```swift
class UserAuth {
  var user: User

  init(user: User) {
    self.user = user
  }

  func verifyCredentials() -> Bool {
    // ...
  }
}

class UserSettings {
  var user: User
  var auth: UserAuth

  init(user: User) {
    self.user = user
    self.auth = UserAuth(user: user)
  }

  func changeSettings(settings: Settings) {
    if self.auth.verifyCredentials() {
      // ...
    }
  }
}
```

**[⬆ voltar ao topo](#Índice)**

### Princípio do Aberto/Fechado (OCP)
Como foi dito por Bertrand Meyer, "entidades de software (classes, módulos, funções, etc.) devem se manter abertas para extensões, mas fechadas para modificações." Mas o que isso significa? Esse princípio basicamente diz que você deve permitir que usuários adicionem novas funcionalidades sem mudar código já existente.

**Ruim:**
```swift
class AjaxAdapter: Adapter {
  override init() {
    super.init()
    self.name = "ajaxAdapter"
  }
}

class NodeAdapter: Adapter {
  override init() {
    super.init()
    self.name = "nodeAdapter"
  }
}

class HttpRequester {
  var adapter: Adapter

  init(adapter: Adapter) {
    self.adapter = adapter
  }

  func fetch(url: String) -> Promise<Response> {
    if self.adapter.name == "ajaxAdapter" {
      return makeAjaxCall(url: url).then { response in
        // transforma a resposta e retorna
      }
    } else if self.adapter.name == "httpNodeAdapter" {
      return makeHttpCall(url: url).then { response in
        // transforma a resposta e retorna
      }
    }
    fatalError("Adapter não suportado")
  }
}

func makeAjaxCall(url: String) -> Promise<Response> {
  // faz a request e retorna a promessa
}

func makeHttpCall(url: String) -> Promise<Response> {
  // faz a request e retorna a promessa
}
```

**Bom:**
```swift
class AjaxAdapter: Adapter {
  override init() {
    super.init()
    self.name = "ajaxAdapter"
  }

  func request(url: String) -> Promise<Response> {
    // faz a request e retorna a promessa
  }
}

class NodeAdapter: Adapter {
  override init() {
    super.init()
    self.name = "nodeAdapter"
  }

  func request(url: String) -> Promise<Response> {
    // faz a request e retorna a promessa
  }
}

class HttpRequester {
  var adapter: Adapter

  init(adapter: Adapter) {
    self.adapter = adapter
  }

  func fetch(url: String) -> Promise<Response> {
    return self.adapter.request(url: url).then { response in
      // transforma a resposta e retorna
    }
  }
}
```

**[⬆ voltar ao topo](#Índice)**


### Princípio de Substituição de Liskov (LSP)
Esse é um termo assustador para um conceito extremamente simples. É formalmente definido como “Se S é um subtipo de T, então objetos do tipo T podem ser substituídos por objetos com o tipo S (i.e., objetos do tipo S podem substituir objetos do tipo T) sem alterar nenhuma das propriedades desejáveis de um programa (corretude, desempenho em tarefas, etc.).” Esta é uma definição ainda mais assustadora.

A melhor explicação para este conceito é se você tiver uma classe pai e uma classe filha, então a classe base e a classe filha pode ser usadas indistintamente sem ter resultados incorretos. Isso ainda pode ser confuso, então vamos dar uma olhada no exemplo clássico do Quadrado-Retângulo (Square-Rectangle). Matematicamente, um quadrado é um retângulo, mas se você modelá-lo usando uma relação “isto-é” através de herança, você rapidamente terá problemas.

**Ruim:**
```swift
class Rectangle {
  var width: Double
  var height: Double

  init() {
    self.width = 0
    self.height = 0
  }

  func setColor(color: String) {
    // ...
  }

  func render(area: Double) {
    // ...
  }

  func setWidth(width: Double) {
    self.width = width
  }

  func setHeight(height: Double) {
    self.height = height
  }

  func getArea() -> Double {
    return self.width * self.height
  }
}

class Square: Rectangle {
  override func setWidth(width: Double) {
    self.width = width
    self.height = width
  }

  override func setHeight(height: Double) {
    self.width = height
    self.height = height
  }
}

func renderLargeRectangles(rectangles: [Rectangle]) {
  rectangles.forEach { rectangle in
    rectangle.setWidth(width: 4)
    rectangle.setHeight(height: 5)
    let area = rectangle.getArea() // RUIM: Retorna 25 para o Quadrado. Deveria ser 20.
    rectangle.render(area: area)
  }
}

let rectangles: [Rectangle] = [Rectangle(), Rectangle(), Square()]
renderLargeRectangles(rectangles: rectangles)
```

**Bom:**
```swift
class Shape {
  func setColor(color: String) {
    // ...
  }

  func render(area: Double) {
    // ...
  }
}

class Rectangle: Shape {
  var width: Double
  var height: Double

  init(width: Double, height: Double) {
    self.width = width
    self.height = height
  }

  func getArea() -> Double {
    return self.width * self.height
  }
}

class Square: Shape {
  var length: Double

  init(length: Double) {
    self.length = length
  }

  func getArea() -> Double {
    return self.length * self.length
  }
}

func renderLargeShapes(shapes: [Shape]) {
  shapes.forEach { shape in
    let area = shape.getArea()
    shape.render(area: area)
  }
}

let shapes: [Shape] = [Rectangle(width: 4, height: 5), Rectangle(width: 4, height: 5), Square(length: 5)]
renderLargeShapes(shapes: shapes)
```

**[⬆ voltar ao topo](#Índice)**

### Princípio da Segregação de Interface (ISP) com Protocolos

Embora Swift não adote o conceito tradicional de interfaces, podemos aplicar o Princípio da Segregação de Interface (ISP) por meio de protocolos, aproveitando a flexibilidade do Swift.

O ISP declara que "Clientes não devem ser forçados a depender de interfaces que eles não usam." Em Swift, onde a tipagem pato (duck typing) prevalece, podemos criar protocolos que representam interfaces segregadas.

**Exemplo Ruim:**

```swift
// Protocolo único com muitos requisitos
protocol Styling {
    var font: UIFont { get }
    var backgroundColor: UIColor { get }
    var cornerRadius: CGFloat { get }

    func applyStyles()
}

// Implementação do protocolo
class BadStylableView: Styling {
    var font: UIFont
    var backgroundColor: UIColor
    var cornerRadius: CGFloat

    init(font: UIFont, backgroundColor: UIColor, cornerRadius: CGFloat) {
        self.font = font
        self.backgroundColor = backgroundColor
        self.cornerRadius = cornerRadius
    }

    func applyStyles() {
        // Aplicar estilos com base na configuração
    }
}

// Exemplo de uso
let badView = BadStylableView(font: .systemFont(ofSize: 14), backgroundColor: .white, cornerRadius: 8)
```

**Exemplo Bom:**

```swift
// Protocolo para configuração de estilo
protocol StyleConfigurable {
    var font: UIFont { get }
    var backgroundColor: UIColor { get }
    var cornerRadius: CGFloat { get }
}

// Protocolo para exibição de estilos
protocol StyleApplicable {
    func applyStyles()
}

// Implementação padrão para configuração de estilo
struct AppearanceConfig: StyleConfigurable {
    var font: UIFont
    var backgroundColor: UIColor
    var cornerRadius: CGFloat
}

// View que adota os protocolos
class GoodStylableView: StyleApplicable {
    var styleConfig: StyleConfigurable

    init(styleConfig: StyleConfigurable) {
        self.styleConfig = styleConfig
        self.setup()
    }

    func setup() {
        applyStyles()
    }

    func applyStyles() {
        // Aplicar estilos com base na configuração
    }
}

// Exemplo de uso
let goodView = GoodStylableView(styleConfig: AppearanceConfig(font: .systemFont(ofSize: 14)))
```

No exemplo ruim, um único protocolo `Styling` contém muitos requisitos, forçando os clientes a implementarem métodos e propriedades que podem não ser necessários. No exemplo bom, utilizamos protocolos segregados (`StyleConfigurable` e `StyleApplicable`) para permitir uma implementação mais específica e flexível.

**[⬆ Voltar ao Topo](#Índice)**

### Princípio da Inversão de Dependência (DIP)
Este princípio nos diz duas coisas essenciais:
1. Módulos de alto nível não devem depender de módulos de baixo nível. Ambos devem depender de abstrações.
2. Abstrações não devem depender de detalhes. Detalhes devem depender de abstrações.

Isso pode ser difícil de entender a princípio, mas se você já trabalhou com AngularSwift, você já viu uma implementação deste princípio na forma de injeção de dependência (DI). Apesar de não serem conceitos idênticos, DIP não deixa módulos de alto nível saber os detalhes de seus módulos de baixo nível, assim como configurá-los. Isso pode ser alcançado através de DI. Um grande benefício é que reduz o acoplamento entre os módulos. Acoplamento é um padrão de desenvolvimento muito ruim porque torna seu código mais difícil de ser refatorado.

Como dito anteriormente, Swift não possui interfaces, então as abstrações que são necessárias são contratos implícitos. Que quer dizer que, os métodos e as classes que um objeto/classe expõe para outros objeto/classe. No exemplo abaixo, o contrato implícito é que qualquer módulo de Request  para `InventoryTracker` terá um método `requestItems`:

**Ruim:**
```swift
class InventoryRequester {
  init() {
    self.REQ_METHODS = ["HTTP"]
  }

  func requestItem(item: String) {
    // ...
  }
}

class InventoryTracker {
  var items: [String]
  var requester: InventoryRequester

  init(items: [String]) {
    self.items = items

    // Ruim: Nós criamos uma dependência numa implementação de request especifica.
    // Nós deveríamos apenas ter requestItems dependendo de um método de request: `request`
    self.requester = InventoryRequester()
  }

  func requestItems() {
    self.items.forEach { item in
      self.requester.requestItem(item: item)
    }
  }
}

let inventoryTracker = InventoryTracker(items: ["apples", "bananas"])
inventoryTracker.requestItems()
```

**Bom:**
```swift
class InventoryTracker {
  var items: [String]
  var requester: RequesterProtocol

  init(items: [String], requester: RequesterProtocol) {
    self.items = items
    self.requester = requester
  }

  func requestItems() {
    self.items.forEach { item in
      self.requester.requestItem(item: item)
    }
  }
}

protocol RequesterProtocol {
  func requestItem(item: String)
}

class InventoryRequesterV1: RequesterProtocol {
  func requestItem(item: String) {
    // ...
  }
}

class InventoryRequesterV2: RequesterProtocol {
  func requestItem(item: String) {
    // ...
  }
}

// Construindo nossas dependências externamente e injetando-as, podemos facilmente
// substituir nosso módulo de request por um novo mais chique que usa WebSockets
let inventoryTracker = InventoryTracker(items: ["apples", "bananas"], requester: InventoryRequesterV2())
inventoryTracker.requestItems()
```

**[⬆ voltar ao topo](#Índice)**

# Testes
Testes são mais importantes que entregas. Se você não possui testes ou uma quantidade inadequada, então toda vez que você entregar seu código você não terá certeza se você não quebrou alguma coisa. Decidir o que constitui uma quantidade adequada é responsabilidade do seu time, mas ter 100% de cobertura (todas as sentenças e branches) é a maneira que se alcança uma alta confiança e uma paz de espírito em desenvolvimento. Isso quer dizer que além de ter um ótimo framework de testes, você também precisa usar uma [boa ferramenta de cobertura](https://www.sonarsource.com/products/sonarqube/).

Não existe desculpa para não escrever testes. Existem [diversos frameworks de testes em Swift ótimos](https://github.com/vsouza/awesome-ios#testing), então encontre um que seu time prefira. Quando você encontrar um que funciona para seu time, então tenha como objetivo sempre escrever testes para cada nova funcionalidade/módulo que você introduzir. Se seu método preferido for Desenvolvimento Orientado a Testes (TDD), isso é ótimo, mas o ponto principal é apenas ter certeza que você está alcançado suas metas de cobertura antes de lançar qualquer funcionalidade, ou refatorar uma já existente.

### Um conceito por teste

**Ruim:**
```swift
import XCTest

class MakeMomentSwiftGreatAgainTests: XCTestCase {

  func testHandlesDateBoundaries() {
    var date = MakeMomentSwiftGreatAgain("1/1/2015")
    date.addDays(30)
    XCTAssertEqual("1/31/2015", date)

    date = MakeMomentSwiftGreatAgain("2/1/2016")
    date.addDays(28)
    XCTAssertEqual("02/29/2016", date)

    date = MakeMomentSwiftGreatAgain("2/1/2015")
    date.addDays(28)
    XCTAssertEqual("03/01/2015", date)
  }
}
```

**Bom:**
```swift
import XCTest

class MakeMomentSwiftGreatAgainTests: XCTestCase {

  func testHandles30DayMonths() {
    let date = MakeMomentSwiftGreatAgain("1/1/2015")
    date.addDays(30)
    XCTAssertEqual("1/31/2015", date)
  }

  func testHandlesLeapYear() {
    let date = MakeMomentSwiftGreatAgain("2/1/2016")
    date.addDays(28)
    XCTAssertEqual("02/29/2016", date)
  }

  func testHandlesNonLeapYear() {
    let date = MakeMomentSwiftGreatAgain("2/1/2015")
    date.addDays(28)
    XCTAssertEqual("03/01/2015", date)
  }
}
```

**[⬆ voltar ao topo](#Índice)**

# Concorrência
**[⬆ voltar ao topo](#Índice)**
### Async/Await são ainda mais limpas que Promessas
 Depois do iOS 13 o Swift traz `async` e `await` que oferecem uma solução ainda mais limpa. Tudo o que você precisa é uma função que tem como prefixo a palavra-chave `async`, e então você pode escrever sua lógica imperativamente sem usar `completions` para encadear suas funções. Use isto se você puder tirar vantagem das funcionalidades do Swift hoje!

**Ruim:**
```swift
import Foundation

let url = URL(string: "https://en.wikipedia.org/wiki/Robert_Cecil_Martin")!

URLSession.shared.dataTask(with: url) { (data, response, error) in
    guard let data = data, error == nil else {
        print(error?.localizedDescription ?? "Unknown error")
        return
    }

    do {
        try data.write(to: URL(fileURLWithPath: "article.html"))
        print("File written")
    } catch {
        print(error.localizedDescription)
    }
}.resume()
```

**Bom:**
```swift
import Foundation

async func getCleanCodeArticle() {
    let url = URL(string: "https://en.wikipedia.org/wiki/Robert_Cecil_Martin")!

    do {
        let (data, _) = try await URLSession.shared.data(from: url)
        try await data.write(to: URL(fileURLWithPath: "article.html"))
        print("File written")
    } catch {
        print(error.localizedDescription)
    }
}

Task {
    await getCleanCodeArticle()
}
```

**[⬆ voltar ao topo](#Índice)**


# Tratamento de Erros
`throw error` é uma coisa boa! Eles significam que o programa identificou
com sucesso quando algo deu errado e está permitindo que você saiba parando
a execução da função no processo atual, fechando o processo (em Swift), e
notificando você no console com a pilha de processos.

### Não ignore erros capturados
Não fazer nada com um erro capturado não te dá a habilidade de resolvê-lo ou
reagir ao erro informado. Exibir um log no console(`print`) não é muito
melhor porque muitas vezes ele pode ficar perdido entre um monte de outras
coisas impressas no console. Se você envolver qualquer pedaço de código em um
`do/catch` isso significa que você acredita que um erro pode ocorrer lá e então
você deveria ter um plano, ou criar caminho de código para quando isso ocorrer.

**Ruim:**
```swift
do {
    try funcThatMightThrow()
} catch {
    print(error)
}
```

**Bom:**
```swift
do {
    try funcThatMightThrow()
} catch {
    // Uma opção (mais chamativa que print):
    print(error)
    // Outra opção:
    notifyUserOfError(error)
    // Outra opção:
    reportErrorToService(error)
    // OU as três!
}
```

# Formatação

A formatação é subjetiva. Como muitas das regras aqui, não há uma regra dura e rápida que você precise seguir. A principal ideia é NÃO discutir sobre formatação. Há ferramentas que automatizam esse processo; deixe-as lidar com isso. A maior parte do código Swift deve seguir o [Guia de Estilo da Swift](https://google.github.io/swift/) da Google.

### Use espaços em vez de tabs
Apesar da luta épica entre espaços e tabs, o importante é ser consistente. O Swift usa espaços e é uma prática comum em outros projetos de Swift. Faça o mesmo.

**Ruim:**
```swift
func bad() {
∙∙∙∙var name:String?
∙∙∙∙guard let unwrappedName = name else {
∙∙∙∙∙∙∙return
∙∙∙∙}
}
```

**Bom:**
```swift
func good() {
    var name: String?
    guard let unwrappedName = name else {
        return
    }
}
```

### Use linhas em branco de maneira eficaz
Separar blocos de código com linhas em branco pode tornar o código mais legível e organizado. No entanto, o excesso de linhas em branco pode ter o efeito oposto, criando uma sensação de fragmentação. Use linhas em branco com moderação.

**Ruim:**
```swift
func calculateTotalScore(score: Int) {
    
    var totalScore = 0
    
    for i in 1...score {
        
        totalScore += i
        
    }
    
    print("The total score is: \(totalScore)")
    
}
```

**Bom:**
```swift
func calculateTotalScore(score: Int) {
    var totalScore = 0
    
    for i in 1...score {
        totalScore += i
    }
    
    print("The total score is: \(totalScore)")
}
```

### Limite o comprimento das linhas
O comprimento recomendado de uma linha de código é de 80 caracteres. Isso garante que você não precise rolar horizontalmente para ler o código. É comum em muitos ambientes de desenvolvimento ter dois arquivos lado a lado. Tornar isso possível facilita a leitura do código.

**Ruim:**
```swift
let errorMessage = "This is a very long error message that exceeds the recommended line length of 80 characters and makes the code harder to read."
```

**Bom:**
```swift
let errorMessage = "This is a short error message."
```

### Use espaçamento consistente
Manter uma consistência no uso de espaços ao redor dos operadores e após as vírgulas contribui para a legibilidade do código.

**Ruim:**
```swift
let sum = 1+2
let array = [1 , 2,3, 4]
```

**Bom:**
```swift
let sum = 1 + 2
let array = [1, 2, 3, 4]
```

### Evite espaços em branco em excesso
Espaços em branco em excesso podem poluir visualmente o código e torná-lo menos legível. Mantenha um uso moderado de espaços em branco.

**Ruim:**
```swift
func   foo  (   a : Int  , b   :   Int   ) -> Int {
    return a + b
}
```

**Bom:**
```swift
func foo(a: Int, b: Int) -> Int {
    return a + b
}
```

### Siga a formatação recomendada pela SwiftLint
A formatação do código pode ser um tópico controverso, mas é importante manter um padrão consistente dentro do seu projeto. O SwiftLint é uma ferramenta útil para impor padrões de formatação. Integre o SwiftLint no seu fluxo de trabalho para garantir que o código siga as práticas recomendadas.

**[⬆ voltar ao topo](#Índice)**

# Comentários

### Apenas comente coisas que tenham complexidade de lógica de negócio.
Comentários devem ser usados para explicar partes do código que têm uma complexidade não óbvia ou para fornecer informações adicionais sobre a lógica de negócio. Evite comentar o óbvio ou detalhes triviais que podem ser facilmente compreendidos lendo o código.

**Ruim:**
```swift
func calculateTotalScore(score: Int) {
  // Inicializa a pontuação total
  var totalScore = 0
  
  // Loop através de cada pontuação individual
  for i in 1...score {
    // Adiciona a pontuação individual à pontuação total
    totalScore += i
  }
  
  // Imprime a pontuação total
  print("A pontuação total é: \(totalScore)")
}
```

**Bom:**
```swift
func calculateTotalScore(score: Int) {
    // A pontuação total é calculada usando a fórmula de soma de Gauss
    var totalScore = (score * (score + 1)) / 2
    
    // Imprime a pontuação total
    print("A pontuação total é: \(totalScore)")
}
```

**[⬆ voltar ao topo](#Índice)**

### Não deixe código comentado na sua base de código
O controle de versão (como o Git) é uma ferramenta poderosa para rastrear alterações ao longo do tempo. Não há necessidade de manter código comentado na base de código, pois ele apenas adiciona ruído e dificulta a leitura.

**Ruim:**
```swift
func doSomething() {
    // Código anterior que não é mais necessário
    // ...
    
    // Código desativado para fins de referência futura
    /*
    if someCondition {
        // Código a ser executado
    }
    */
    
    // ...
}
```

**Bom:**
```swift
func doSomething() {
    // Código atual
    // ...
}
```

**[⬆ voltar ao topo](#Índice)**

### Não comente registro de alterações
Evite adicionar comentários de registro de alterações diretamente no código. Use um sistema de controle de versão para rastrear as alterações e forneça registros de alterações no repositório ou em um arquivo específico.

**Ruim:**
```swift
/**
 * 2022-03-15: Removida a lógica de fallback (RM)
 * 2022-01-20: Corrigido bug na validação de entrada (JP)
 * 2021-12-01: Adicionada nova função de processamento (LI)
 */
func processInput(input: String) {
  // Lógica de processamento
}
```

**Bom:**
```swift
func processInput(input: String) {
  // Lógica de processamento
}
```

Registros de alterações podem ser mantidos em um arquivo `CHANGELOG.md` ou similar, ou dentro das mensagens de commit no controle de versão.

**[⬆ voltar ao topo](#Índice)**

### Evite marcadores de posição
Evite usar marcadores de posição, como barras ou linhas de asteriscos, para dividir ou destacar seções de código. Em vez disso, use uma boa estrutura de código com identação e formatação adequadas para tornar o código facilmente compreensível.

**Ruim:**
```swift
struct Example {
    // MARK: - Propriedades
    var name: String
    var age: Int
    
    // MARK - Inicializador
    init(name: String, age: Int) {
        self.name = name
        self.age = age
    }
    
    // MARK - Funções
    
    // Método para realizar uma ação
    func performAction() {
        // ...
    }
}
```

**Bom:**
```swift
struct Example {
    var name: String
    var age: Int
    
    init(name: String, age: Int) {
        self.name = name
        self.age = age
    }
    
    func performAction() {
        // ...
    }
}
```

**[⬆ voltar ao topo](#Índice)**
