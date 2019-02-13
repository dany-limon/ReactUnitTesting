# ReactUnitTesting
Automatizando las pruebas unitarias con Jest

## Test unitarios

* Son una forma de **comprobar el correcto funcionamiento** de una unidad de código (funciones, componentes...)
* Existen gran cantidad de frameworks para React (**Jest**, Mocha, Enzyme, ...)
* Utilizaremos Jest por su sencillez de uso

## Características tests unitarios (principio FIRST)

* **Simples**. No debemos realizar afirmaciones innecesarias
* **Independientes**. Un test debe poder ejecutarse de forma independiente al resto
* **Repetibles.** El resultado debe ser el mismo independientemente de donde se ejecuten
* **Auto evaluables.** Cada test comprueba una unidad de código cada vez
* **Rápidos.** Los tests deben ser muy rápidos de probar
* **Automatizables**

## Ventajas

* **Detección de errores** temprana
* Facilitan los cambios y **refactorizaciones**
* Sirven de **documentación**
* Son un indicador de **calidad**
* Son un paso más de la **Integración continua**
* **TDD** (Test-driven development)

## Render por defecto

Testar que el componente visual se renderiza correctamente con los valores por defecto

```javascript
import React from 'react'
import ReactDOM from 'react-dom'
import View from '.'

test('renders without crashing', () => {
	const div = document.createElement('div')
	ReactDOM.render( <View />, div )
	ReactDOM.unmountComponentAtNode(div)
})
```

También pueden testarse las páginas y los componentes conectados a Redux

```javascript
import React from 'react'
import ReactDOM from 'react-dom'
import View from '.'
import { Provider } from "react-redux"
import configureMockStore from "redux-mock-store"
import { MemoryRouter } from 'react-router-dom'
const mockStore = configureMockStore()
const store = mockStore()

test('renders without crashing', () => {
  const div = document.createElement('div')
  ReactDOM.render(
        <Provider store={store}>
          <MemoryRouter>
            <View />
          </MemoryRouter>
        </Provider>
    div
  )
  ReactDOM.unmountComponentAtNode(div)
})
```

## Funciones de utilidad

Testar cada función de utilidad. 

* Debemos comprobar el flujo normal y los posibles casos de error

```javascript
import { AuthUtils } from './'

test('isRolClub', () => {
   expect(AuthUtils.isRolClub()).toBeFalsy()
   expect(AuthUtils.isRolClub({})).toBeFalsy()
   expect(AuthUtils.isRolClub({ rol: 'liga' })).toBeFalsy()
   expect(AuthUtils.isRolClub({ rol: 'club' })).toBeTruthy()
})

```

## Action Creators

Testar cada Action Creator de Redux (y su Reducer asociado)

1. Comprobar el estado inicial del store
2. Ejecutar la acción
3. Comprobar que el estado ha cambiado correctamente

```javascript
import { AuthActions } from './'
import { createStore } from 'redux'

test('setIsFetching', () => {
   let store = createStore(reducer)

   expect(store.getState().isFetching).toBeFalsy()

   store.dispatch(AuthActions.setIsFetching(true))
   expect(store.getState().isFetching).toBeTruthy()
 })
```

## Thunks

Testar cada acción asíncrona (thunk) de Redux

* Los thunks deben devolver una promesa (también piden probarse si son callbacks)
* Utilizamos la librería redux-mock-store (sólo sirve para ver las acciones, el estado no cambia)
* Comprobar que las acciones se ejecutan el orden correcto

```javascript
import { ClubsOperations, ClubsTypes } from './'
import thunk from 'redux-thunk'
import { createStore } from 'redux'
import configureMockStore from 'redux-mock-store'
import reducer, { initialState } from './reducer'
import * as api from '../../api/mock'

test('fetchClubs', () => {
   const middlewares = [thunk.withExtraArgument({ api })]
   const mockStore = configureMockStore(middlewares)
   const store = mockStore({ clubs: { ...initialState } })
   
   return store.dispatch(ClubsOperations.fetchClubs()).then(() => {
     const actions = store.getActions()
     expect(actions[0].type).toEqual(ClubsTypes.SET_IS_FETCHING)
     expect(actions[1].type).toEqual(ClubsTypes.SET_ITEMS)
     expect(actions[2].type).toEqual(ClubsTypes.SET_IS_FETCHING)
   })
 })
```

## Agrupar las pruebas

```javascript
describe('AuthUtils', () => {
  test('isRolClub', () => {} )
  test('isRolLiga', () => {} )
})

describe('AuthActions', () => {
  test('logOut', () => {} )
  test('setSesionLoaded', () => {} )
  test('setIsFetching', () => {} )
})

describe('AuthOperations', () => {
  test('logIn', () => {} )
  test('handleLoginError', () => {} )
})
```

## ¿Cómo ejecutar las pruebas?

* reate-react-app ya viene preconfigurado con Jest
* Jest busca las pruebas almacenadas en ficheros **test.js**
* `$ yarn test` para iniciar
* `$ yarn test --coverage` para ver las estadísticas de cobertura

## Referencias

* Clean Code (Robert C. Martin)
* [**h**ttps://www.paradigmadigital.com/dev/principio-first-aumentar-la-calidad-tests-unitarios/](https://www.paradigmadigital.com/dev/principio-first-aumentar-la-calidad-tests-unitarios/)
* [https://jestjs.io](https://jestjs.io)
* [https://jestjs.io/docs/en/tutorial-react](https://jestjs.io/docs/en/tutorial-react)
* [https://redux.js.org/recipes/writing-tests](https://redux.js.org/recipes/writing-tests)
* [https://github.com/dmitry-zaets/redux-mock-store](https://github.com/dmitry-zaets/redux-mock-store)
