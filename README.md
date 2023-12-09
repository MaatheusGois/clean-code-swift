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

## Introdução
![Imagem humorística da estimativa de qualidade do software baseado na contagem de quantos palavrões você gritou enquanto lia o código.](http://www.osnews.com/images/comics/wtfm.jpg)

Princípios da Engenharia de Software, do livro de Robert C. Martin
[*Código Limpo*](https://www.amazon.com.br/C%C3%B3digo-Limpo-Habilidades-Pr%C3%A1ticas-Software/dp/8576082675),
adaptados para Swift. Isto não é um guia de estilos. É um guia para se produzir código [legível, reutilizável e refatorável](https://github.com/ryanmcdermott/3rs-of-software-architecture) em Swift.

Nem todo princípio demonstrado deve ser seguido rigorosamente, e ainda menos são um consenso universal. Estes princípios são orientações e nada mais, entretanto, foram codificados durante muitos anos de experiência coletiva pelos autores de *Código limpo*.

Nosso ofício de engenharia de software tem pouco mais de 50 anos e ainda estamos aprendendo muito. Quando a arquitetura de software for tão velha quanto a própria arquitetura, talvez então tenhamos regras mais rígidas para seguir. Por enquanto, deixe que estas orientações sirvam como critério para se avaliar a qualidade de código Swift que tanto você e o seu time produzirem.

Mais uma coisa: aprender isto não irá lhe transformar imediatamente em um desenvolvedor de software melhor, trabalhar com eles por muitos anos não quer dizer que você não cometerá erros. Toda porção de código começa com um rascunho, como argila molhada sendo moldada em sua forma final. Finalmente, talhamos as imperfeições quando revisamos com nossos colegas. Não se sinta culpado pelos primeiros rascunhos que ainda precisam de melhorias. Ao invés, desconte em seu código.

## **Variáveis**
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
Nós iremos ler mais código que escrever. É importante que o código que escrevemos seja legível e pesquisável. *Não* dando nomes em variáveis que sejam significativos para entender nosso programa, machucamos nossos leitores. Torne seus nomes pesquisáveis. Ferramentas como [buddy.js](https://github.com/danielstjules/buddy.js) e [ESLint](https://github.com/eslint/eslint/blob/660e0918933e6e7fede26bc675a0763a6b357c94/docs/rules/no-magic-numbers.md) podem ajudar a identificar constantes sem nome.

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
Quando você tem mais de um nível de abstração sua função provavelmente esta fazendo coisas demais. Dividir suas funções leva a reutilização e testes mais fáceis.

**Ruim:**
```swift
func parseBetterJSAlternative(code: String) {
  let regexes: [Regex] = [
    // ...
  ]

  let statements = code.split(separator: " ")
  var tokens: [Token] = []
  for regex in regexes {
    for statement in statements {
      // ...
    }
  }

  var ast: [ASTNode] = []
  for token in tokens {
    // lex...
  }

  for node in ast {
    // parse...
  }
}
```

**Bom:**
```swift
func tokenize(code: String) -> [Token] {
  let regexes: [Regex] = [
    // ...
  ]

  let statements = code.split(separator: " ")
  var tokens: [Token] = []
  for regex in regexes {
    for statement in statements {
      tokens.append( /* ... */ )
    }
  }

  return tokens
}

func lexer(tokens: [Token]) -> [ASTNode] {
  var ast: [ASTNode] = []
  for token in tokens {
    ast.append( /* ... */ )
  }

  return ast
}

func parseBetterJSAlternative(code: String) {
  let tokens = tokenize(code: code)
  let ast = lexer(tokens: tokens)
  for node in ast {
    // parse...
  }
}
```
**[⬆ voltar ao topo](#Índice)**

### Remova código duplicado
Faça absolutamente seu melhor para evitar código duplicado. Código duplicado quer dizer que existe mais de um lugar onde você deverá alterar algo se precisar mudar alguma lógica.

Imagine que você é dono de um restaurante e você toma conta do seu estoque: todos os seus tomates, cebolas, alhos, temperos, etc. Se você tem múltiplas listas onde guarda estas informações, então você terá que atualizar todas elas quando servir um prato que tenha tomates. Se você tivesse apenas uma lista, teria apenas um lugar para atualizar!

Frequentemente, você possui código duplicado porque você tem duas ou mais coisas levemente diferentes, que possuem muito em comum, mas suas diferenças lhe forçam a ter mais duas ou três funções que fazem muito das mesmas coisas. Remover código duplicado significa criar uma abstração que seja capaz de lidar com este conjunto de coisas diferentes com apenas uma função/módulo/classe.

Conseguir a abstração correta é crítico, por isso que você deveria seguir os princípios SOLID descritos na seção *Classes*. Abstrações ruins podem ser piores do que código duplicado, então tome cuidado! Dito isto, se você puder fazer uma boa abstração, faça-a! Não repita a si mesmo, caso contrário você se pegará atualizando muitos lugares toda vez que precisar mudar qualquer coisinha.

**Ruim:**
```swift
func showDeveloperList(developers: [Developer]) {
  for developer in developers {
    let expectedSalary = developer.calculateExpectedSalary()
    let experience = developer.getExperience()
    let githubLink = developer.getGithubLink()
    let data = [
      "expectedSalary": expectedSalary,
      "experience": experience,
      "githubLink": githubLink
    ]

    render(data: data)
  }
}

func showManagerList(managers: [Manager]) {
  for manager in managers {
    let expectedSalary = manager.calculateExpectedSalary()
    let experience = manager.getExperience()
    let portfolio = manager.getMBAProjects()
    let data = [
      "expectedSalary": expectedSalary,
      "experience": experience,
      "portfolio": portfolio
    ]

    render(data: data)
  }
}
```

**Bom:**
```swift
func showEmployeeList(employees: [Employee]) {
  for employee in employees {
    let expectedSalary = employee.calculateExpectedSalary()
    let experience = employee.getExperience()

    var data = [
      "expectedSalary": expectedSalary,
      "experience": experience
    ]

    switch employee.type {
    case .manager:
      data["portfolio

"] = (employee as! Manager).getMBAProjects()
    case .developer:
      data["githubLink"] = (employee as! Developer).getGithubLink()
    }

    render(data: data)
  }
}
```
**[⬆ voltar ao topo](#Índice)**

### Defina (set) objetos padrões com Object.assign

**Ruim:**
```swift
var menuConfig = [
  "title": nil,
  "body": "Bar",
  "buttonText": nil,
  "cancellable": true
]

func createMenu(config: [String: Any]?) {
  config?["title"] = config?["title"] ?? "Foo"
  config?["body"] = config?["body"] ?? "Bar"
  config?["buttonText"] = config?["buttonText"] ?? "Baz"
  config?["cancellable"] = config?["cancellable"] ?? true
}

createMenu(config: menuConfig)
```

**Bom:**
```swift
var menuConfig = [
  "title": "Order",
  // Usuário não incluiu a chave 'body'
  "buttonText": "Send",
  "cancellable": true
]

func createMenu(config: [String: Any]?) {
  config = config ?? [:]
  config!["title"] = config!["title"] ?? "Foo"
  config!["body"] = config!["body"] ?? "Bar"
  config!["buttonText"] = config!["buttonText"] ?? "Baz"
  config!["cancellable"] = config!["cancellable"] ?? true
  // configuração agora é: ["title": "Order", "body": "Bar", "buttonText": "Send", "cancellable": true]
  // ...
}

createMenu(config: menuConfig)
```
**[⬆ voltar ao topo](#Índice)**


### Não use flags como parâmetros de funções
Flags falam para o seu usuário que sua função faz mais de uma coisa. Funções devem fazer apenas uma coisa. Divida suas funções se elas estão seguindo caminhos de código diferentes baseadas em um valor booleano.

**Ruim:**
```swift
func createFile(name: String, temp: Bool) {
  if temp {
    fs.create("./temp/\(name)")
  } else {
    fs.create(name)
  }
}
```

**Bom:**
```swift
func createFile(name: String) {
  fs.create(name)
}

func createTempFile(name: String) {
  createFile("./temp/\(name)")
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
var name = 'Ryan McDermott'

func splitIntoFirstAndLastName() {
  name = name.split(separator: " ")
}

splitIntoFirstAndLastName()

print(name) // ['Ryan', 'McDermott']
```

**Bom:**
```swift
func splitIntoFirstAndLastName(name: String) -> [String] {
  return name.split(separator: " ").map { String($0) }
}

let name = 'Ryan McDermott'
let newName = splitIntoFirstAndLastName(name: name)

print(name) // 'Ryan McDermott'
print(newName) // ['Ryan', 'McDermott']
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
  return cart + [item]
}
```

### Não escreva em funções globais
Poluir globais é uma prática ruim em Swift porque você pode causar conflito com outra biblioteca e o usuário da sua API não faria a menor ideia até que ele tivesse um exceção sendo levantada em produção. Vamos pensar em um exemplo: e se você quisesse estender o método nativo Array do Swift para ter um método `diff` que poderia mostrar a diferença entre dois vetores? Você poderia escrever sua nova função em `Array.prototype`, mas poderia colidir com outra biblioteca que tentou fazer a mesma coisa. E se esta outra biblioteca estava apenas usando `diff` para achar a diferença entre o primeiro e último elemento de um vetor? É por isso que seria muito melhor usar as classes padrões do ES2015/ES6 e apenas estender o `Array` global.

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
class SuperArray<Element>: Array<Element> {
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

### Não otimize demais
Navegadores modernos fazem muitas otimizações por debaixo dos panos em tempo de execução. Muitas vezes, se você estiver otimizando, está apenas perdendo o seu tempo. [Existem bons recursos](https://github.com/petkaantonov/bluebird/wiki/Optimization-killers) para se verificar onde falta otimização. Foque nesses por enquanto, até que eles sejam consertados caso seja possível.

**Ruim:**
```swift
// Em navegadores antigos, cada iteração de `list.count` não cacheada seria custosa
// devido a recomputação de `list.count`. Em navegadores modernos, isto é otimizado.
for i in 0..<list.count {
  // ...
}
```

**Bom:**
```swift
for i in 0..<list.count {
  // ...
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

## **Objetos e Estruturas de Dados**
### Use getters e setters
Usar getters e setters para acessar dados em objetos é bem melhor que simplesmente procurar por uma propriedade em um objeto. "Por quê?", você deve perguntar. Bem, aqui vai uma lista desorganizada de motivos:

* Quando você quer fazer mais além de pegar (get) a propriedade de um objeto, você não tem que procurar e mudar todos os acessores do seu código
* Torna mais fácil fazer validação quando estiver dando um `set`
* Encapsula a representação intern

a
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


## **Classes**
### Prefira classes do ES2015/ES6 ao invés de funções simples do ES5
É muito difícil conseguir que herança de classe, construtores, e definições de métodos sejam legíveis para classes de ES5 clássicas. Se você precisa de herança (e esteja ciente que você talvez não precise), então prefira classes ES2015/ES6. Entretanto, prefira funções pequenas ao invés de classes até que você precise de objetos maiores e mais complexos.

**Ruim:**
```swift
class Animal {
  var age: Int

  init(age: Int) {
    self.age = age
  }

  func move() {}
}

class Mammal: Animal {
  var furColor: String

  init(age: Int, furColor: String) {
    self.furColor = furColor
    super.init(age: age)
  }

  func liveBirth() {}
}

class Human: Mammal {
  var languageSpoken: String

  init(age: Int, furColor: String, languageSpoken: String) {
    self.languageSpoken = languageSpoken
    super.init(age: age, furColor: furColor)
  }

  func speak() {}
}
```

**Bom:**
```swift
protocol Movable {
  func move()
}

class Animal: Movable {
  var age: Int

  init(age: Int) {
    self.age = age
  }

  func move() {}
}

protocol LiveBirth {
  func liveBirth()
}

class Mammal: Animal, LiveBirth {
  var furColor: String

  init(age: Int, furColor: String) {
    self.furColor = furColor
    super.init(age: age)
  }

  func liveBirth() {}
}

class Speaking {
  var languageSpoken: String

  init(languageSpoken: String) {
    self.languageSpoken = languageSpoken
  }

  func speak() {}
}

class Human: Mammal {
  var speaker: Speaking

  init(age: Int, furColor: String, languageSpoken: String) {
    self.speaker = Speaking(languageSpoken: languageSpoken)
    super.init(age: age, furColor: furColor)
  }

  func speak() {
    speaker.speak()
  }
}
```
**[⬆ voltar ao topo](#Índice)**


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

## **SOLID**
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

### Princípio da Segregação de Interface (ISP)
Swift não possui interfaces então esse princípio não se aplica estritamente como os outros. Entretanto, é importante e relevante até mesmo com a falta de um sistema de tipos em Swift.

ISP diz que "Clientes não devem ser forcados a depender de interfaces que eles não usam." Interfaces são contratos implícitos em Swift devido a sua tipagem pato (duck typing).

Um bom exemplo para se observar que demonstra esse princípio em Swift é de classes que requerem objetos de configurações grandes. Não pedir para clientes definirem grandes quantidades de opções é benéfico, porque na maioria das vezes eles não precisarão de todas as configurações. Torná-las opcionais ajuda a prevenir uma “interferência gorda”.

**Ruim:**
```swift
class DOMTraverser {
  var settings: DOMSettings

  init(settings: DOMSettings) {
    self.settings = settings
    self.setup()
  }

  func setup() {
    self.rootNode = self.settings.rootNode
    self.animationModule.setup()
  }

  func traverse() {
    // ...
  }
}

let $ = DOMTraverser(settings: DOMSettings(rootNode: document.getElementsByTagName('body'), animationModule: {}))
```

**Bom:**
```swift
class DOMTraverser {
  var settings: DOMSettings
  var options: DOMOptions

  init(settings: DOMSettings) {
    self.settings = settings
    self.options = settings.options
    self.setup()
  }

  func setup() {
    self.rootNode = self.settings.rootNode
    self.setupOptions()
  }

  func setupOptions() {
    if self.options.animationModule != nil {
      // ...
    }
  }

  func traverse() {
    // ...
  }
}

let $ = DOMTraverser(settings: DOMSettings(rootNode: document.getElementsByTagName('body'), options: DOMOptions(animationModule: {})))
```
**[⬆ voltar ao topo](#Índice)**

### Princípio da Inversão de Dependência (DIP)
Este princípio nos diz duas coisas essenciais:
1. Módulos de alto nível não devem depender de módulos de baixo nível. Ambos devem depender de abstrações.
2. Abstrações não devem depender de detalhes. Detalhes devem depender de abstrações.

Isso pode ser difícil de entender a princípio, mas se você já trabalhou com AngularJS, você já viu uma implementação deste princípio na forma de injeção de dependência (DI). Apesar de não serem conceitos idênticos, DIP não deixa módulos de alto nível saber os detalhes de seus módulos de baixo nível, assim como configurá-los. Isso pode ser alcançado através de DI. Um grande benefício é que reduz o acoplamento entre os módulos. Acoplamento é um padrão de desenvolvimento muito ruim porque torna seu código mais difícil de ser refatorado.

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

## **Testes**
Testes são mais importantes que entregas. Se você não possui testes ou uma quantidade inadequada, então toda vez que você entregar seu código você não terá certeza se você não quebrou alguma coisa. Decidir o que constitui uma quantidade adequada é responsabilidade do seu time, mas ter 100% de cobertura (todas as sentenças e branches) é a maneira que se alcança uma alta confiança e uma paz de espírito em desenvolvimento. Isso quer dizer que além de ter um ótimo framework de testes, você também precisa usar uma [boa ferramenta de cobertura](http://gotwarlost.github.io/istanbul/).

Não existe desculpa para não escrever testes. Existem [diversos frameworks de testes em Swift ótimos](https://github.com/vsouza/awesome-ios#testing), então encontre um que seu time prefira. Quando você encontrar um que funciona para seu time, então tenha como objetivo sempre escrever testes para cada nova funcionalidade/módulo que você introduzir. Se seu método preferido for Desenvolvimento Orientado a Testes (TDD), isso é ótimo, mas o ponto principal é apenas ter certeza que você está alcançado suas metas de cobertura antes de lançar qualquer funcionalidade, ou refatorar uma já existente.

### Um conceito por teste

**Ruim:**
```swift
import XCTest

class MakeMomentJSGreatAgainTests: XCTestCase {

  func testHandlesDateBoundaries() {
    var date = MakeMomentJSGreatAgain("1/1/2015")
    date.addDays(30)
    XCTAssertEqual("1/31/2015", date)

    date = MakeMomentJSGreatAgain("2/1/2016")
    date.addDays(28)
    XCTAssertEqual("02/29/2016", date)

    date = MakeMomentJSGreatAgain("2/1/2015")
    date.addDays(28)
    XCTAssertEqual("03/01/2015", date)
  }
}
```

**Bom:**
```swift
import XCTest

class MakeMomentJSGreatAgainTests: XCTestCase {

  func testHandles30DayMonths() {
    let date = MakeMomentJSGreatAgain("1/1/2015")
    date.addDays(30)
    XCTAssertEqual("1/31/2015", date)
  }

  func testHandlesLeapYear() {
    let date = MakeMomentJSGreatAgain("2/1/2016")
    date.addDays(28)
    XCTAssertEqual("02/29/2016", date)
  }

  func testHandlesNonLeapYear() {
    let date = MakeMomentJSGreatAgain("2/1/2015")
    date.addDays(28)
    XCTAssertEqual("03/01/2015", date)
  }
}
```
**[⬆ voltar ao topo](#Índice)**

## **Concorrência**
### Use Promessas, não callbacks
Callbacks não são limpos, e eles causam uma quantidade excessiva de aninhamentos. A partir de Swift 5.5, você pode usar async/await para lidar com assincronia de uma forma mais limpa. Isso oferece uma abordagem mais legível e funcional para o código assíncrono.

**Ruim:**
```swift
import Foundation

func fetchData(completion: @escaping (Result<Data, Error>) -> Void) {
    let url = URL(string: "https://api.example.com/data")!
    let task = URLSession.shared.dataTask(with: url) { data, response, error

 in
        if let error = error {
            completion(.failure(error))
            return
        }
        guard let data = data else {
            let error = NSError(domain: "com.example", code: 42, userInfo: nil)
            completion(.failure(error))
            return
        }
        completion(.success(data))
    }
    task.resume()
}

func processFetchedData(data: Data) {
    // Process the data
}

fetchData { result in
    switch result {
    case .success(let data):
        processFetchedData(data: data)
    case .failure(let error):
        print("Error: \(error)")
    }
}
```

**Bom:**
```swift
import Foundation

func fetchData() async throws -> Data {
    let url = URL(string: "https://api.example.com/data")!
    let (data, _) = try await URLSession.shared.data(from: url)
    return data
}

func processFetchedData(data: Data) {
    // Process the data
}

do {
    let data = try await fetchData()
    processFetchedData(data: data)
} catch {
    print("Error: \(error)")
}
```
**[⬆ voltar ao topo](#Índice)**

### Async/Await são ainda mais limpas que Promessas
Promessas são uma alternativa bem mais limpa que callbacks, mas o Swift traz `async` e `await` que oferecem uma solução ainda mais limpa. Tudo o que você precisa é uma função que tem como prefixo a palavra-chave `async`, e então você pode escrever sua lógica imperativamente sem usar `then` para encadear suas funções. Use isto se você puder tirar vantagem das funcionalidades do Swift hoje!

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


## **Tratamento de Erros**
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

### Não ignore promessas rejeitadas
Pela mesma razão que você não deveria ignorar erros
captados de `do/catch`

**Ruim:**
```swift
getData()
    .map { data in
        funcThatMightThrow(data)
    }
    .catch { error in
        print(error)
    }
```

**Bom:**
```swift
getData()
    .map { data in
        funcThatMightThrow(data)
    }
    .catch { error in
        // One option (more noisy than print):
        print(error)
        // Another option:
        notifyUserOfError(error)
        // Another option:
        reportErrorToService(error)
        // OR do all three!
    }
```

**[⬆ voltar ao topo](#Índice)**


## **Formatação**

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

## **Comentários**

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
    //////////////////////////////////////
    // Propriedades
    //////////////////////////////////////
    var name: String
    var age: Int
    
    //////////////////////////////////////
    // Inicializador
    //////////////////////////////////////
    init(name: String, age: Int) {
        self.name = name
        self.age = age
    }
    
    //////////////////////////////////////
    // Funções
    //////////////////////////////////////
    
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
