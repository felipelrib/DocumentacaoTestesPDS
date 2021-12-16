# Trabalho Prático sobre Testes de Software - Prática em Desenvolvimento de Software
O objetivo deste trabalho é ter contato com implementações reais de testes de software em sistemas reconhecidos no GitHub. Para isso, o sistema escolhido foi o [Bootstrap](https://github.com/twbs/bootstrap), framework de desenvolvimento web front-end.

## Bootstrap
Para os testes, é importante notar que é utilizado o conceito de fixture, criando um elemento HTML de teste antes da execução de todos os métodos e limpando esse elemento após a execução de cada teste. [🔗](https://github.com/twbs/bootstrap/blob/main/js/tests/unit/button.spec.js#L7)
```
// O elemento é armazenado no contexto global do arquivo de testes.
let fixtureEl

// Ele é criado antes de todos os testes através de uma função externa que inicializa um elemento limpo.
beforeAll(() => {
  fixtureEl = getFixture() 
})

// Após cada teste, esse elemento é resetado, mantendo o estado inicial no início do próximo teste.
afterEach(() => {
  clearFixture() 
})
```

Também, para todos os testes selecionados, esta fixture é inicializada definindo um elemento do tipo `button` diretamente no HTML e criando um objeto do tipo `Button`, específico do Bootstrap, para verificar se o comportamento desta classe está correto.

### Teste 1 - toggle - should toggle aria-pressed. [🔗](https://github.com/twbs/bootstrap/blob/main/js/tests/unit/button.spec.js#L65)
O teste começa inicializando o botão no HTML e o objeto do tipo `Button`. Após isso, é verificado se o estado inicial da propriedade `aria-pressed` e a presença da classe `active` do botão no HTML começam como falsas, indicando que o botão ainda não foi pressionado. É chamado, então, o método `toggle` do objeto `Button` e é verificado novamente, nesse botão no HTML, se a propriedade `aria-pressed` e a presença da classe `active` estão como verdadeiras, indicando que o botão foi pressionado.
```
it('should toggle aria-pressed', () => {
  // Cria o botão no HTML da fixture.
  fixtureEl.innerHTML = '<button class="btn" data-bs-toggle="button" aria-pressed="false"></button>'

  // Cria um objeto "Button" associado ao botão no HTML.
  const btnEl = fixtureEl.querySelector('.btn')
  const button = new Button(btnEl)

  // Verifica se ambas as propriedades estão inalteradas e inicializadas como "false".
  expect(btnEl.getAttribute('aria-pressed')).toEqual('false')
  expect(btnEl).not.toHaveClass('active')

  button.toggle()

  // Verifica se ambas as propriedades foram alteradas para "true".
  expect(btnEl.getAttribute('aria-pressed')).toEqual('true')
  expect(btnEl).toHaveClass('active')
})
```

### Teste 2 - jQueryInterface - should handle config passed and toggle existing button. [🔗](https://github.com/twbs/bootstrap/blob/main/js/tests/unit/button.spec.js#L97)
O teste começa inicializando o botão no HTML e o objeto do tipo `Button`. Após isso, é definido um *test double* do tipo *spy* na função `toggle` do objeto, para verificar se essa função será chamada quando o `toggle` do jQuery (que neste caso está mockado) for chamado.
```
it('should handle config passed and toggle existing button', () => {
  // Cria o botão no HTML da fixture.
  fixtureEl.innerHTML = '<button class="btn" data-bs-toggle="button"></button>'

  // Cria um objeto "Button" associado ao botão no HTML.
  const btnEl = fixtureEl.querySelector('.btn')
  const button = new Button(btnEl)

  // Adiciona o spy na função "toggle".
  spyOn(button, 'toggle')

  jQueryMock.fn.button = Button.jQueryInterface
  jQueryMock.elements = [btnEl]

  // Realiza a chamada da função "toggle" no jQuery mockado.
  jQueryMock.fn.button.call(jQueryMock, 'toggle')

  // Espera que a função "toggle" do objeto também tenha sido chamada.
  expect(button.toggle).toHaveBeenCalled()
})
```

### Teste 3 - jQueryInterface - should just create a button instance without calling toggle. [🔗](https://github.com/twbs/bootstrap/blob/main/js/tests/unit/button.spec.js#L127)
O teste começa inicializando o botão no HTML e o objeto do tipo `Button`. Depois disso, o teste inicializa o mock do jQuery associando a interface jQuery da classe `Button` e realiza a chamada do seletor do jQuery no elemento do botão, verificando se essa chamada cria a instância do objeto de forma válida e não altera o estado inicial da propriedade `active` do botão, indicando que ele não foi apertado.

```
it('should just create a button instance without calling toggle', () => {
  // Cria o botão no HTML da fixture.
  fixtureEl.innerHTML = '<button class="btn" data-bs-toggle="button"></button>'

  const btnEl = fixtureEl.querySelector('.btn')

  // Adiciona a interface jQuery da classe Button ao mock do jQuery e atribui o elemento do botão a ele.
  jQueryMock.fn.button = Button.jQueryInterface
  jQueryMock.elements = [btnEl]

  // Realiza a chamada do seletor do jQuery em cima do botão.
  jQueryMock.fn.button.call(jQueryMock)

  // Verifica se a chamada do seletor instanciou um objeto do tipo `Button` associado ao elemento e se essa chamada
  // não fez com que o botão tenha sido apertado através da existência da classe `active`.
  expect(Button.getInstance(btnEl)).not.toBeNull()
  expect(btnEl).not.toHaveClass('active')
})
```
