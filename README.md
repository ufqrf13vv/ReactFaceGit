# Домашний проект: facegit 1/3

[Пример первой части](http://5adf00c7c96592692ff5f32e.quirky-ardinghelli-6bcd9a.netlify.com).

_Высокий старт будет компенсируется легкими дополнениями. Следующие 2 порции задач будут гораздо
проще, не волнуйтесь из-за объема 😉._

## Описание проекта

Мы разрабатываем клиент для github, где можно гулять по друзьям, репозиториям, смотреть issues,
комиты и так далее.

## Задачи. I часть

Для начала мы реализуем простой функционал хождения по последователям. Я расскажу, какие файлы нужно создать, и что именно протестировать, но всю работу вам прийдется выполнять самим.

```bash
├── api.js
├── components
│   ├── AppRouter
│   ├── Follower
│   ├── Followers
│   ├── Login
│   ├── PrivateRoute
│   └── UserPage
├── index.css
├── index.js
├── ducks
│   ├── auth.js
│   ├── index.js
│   └── users.js
├── sagas
│   ├── auth.js
│   ├── index.js
│   └── users.js
├── setupTests.js
└── store.js
```

> Файл api содержит логику по работе с токеном авторизации, распологать там ключ нужно с помощью
> саги `auth`.

---

1. Обычно все проекты начинаются с роутера, по этому надо начать написание компонента `AppRouter`, с `react-router-dom`.
1. Для компоненты `AppRouter` нужно:
   * Написать роутинг, но пока разместить 2 роута для `/users/me` и `/login`
   * Используйте Redirect, а также вам понадобится компонент PrivateRoute, который мы уже писали.
1. Компонент `Login`, в котором происходит сохранение токена для github
1. Написать компонент `UserPage`, который содержит верстку аватара пользователя, информацию о
   пользователе. При монтировании нужно делать запрос на получение информации о пользователе.
   В основной верстке должен быть:
    * аватар пользователя,
    * login пользователя,
    * количество фаловеров пользователя
1. Написать экшены, редьюсеры для получения данных о пользователе. При новом запросе
   пользователя, нужно удалять данные о предыдущем пользователе.
1. Для работы с localstorage нужно проверять наличие ключа в localstorage при авторизации:
    ```javascript
    function* authFlow() {
      while (true) {
        const isAuthorized = yield select(getIsAuthorized);
        const localStorageToken = yield call(getTokenFromLocalStorage);
        let token;
        if (!isAuthorized && localStorageToken) {
          token = localStorageToken;
          yield put(authorize());
        } else {
          const action = yield take(authorize);
          token = action.payload;
        }
        yield call(setTokenApi, token);
        yield call(setTokenToLocalStorage, token);
        yield take(logout);
        yield call(removeTokenFromLocalStorage);
        yield call(clearTokenApi);
      }
    }
    ```

### Как начать писать проект

Когда вам нужно приступать к написанию новых проектов, начинайте написание проекта с первой
компоненты, которая выводит вам что то простое, например пустой div. Посмотрите, какие данные и
логику нужно расположить на странице, в случае с UserPage, нужно получить данные по пользователю,
значит отправить экшен о получении данных. Так как нужно совершить запрос, вам понадобится 3 экшена,
флаги сетевых запросов, редьюсер для данных и для ошибки. После отправки экшена, нужно убедиться что
экшен действительно отправляется, это удобно делать через redux devtools. После того как экшен
отправляется, можно приступить к написанию саги, которая обрабатывает запрос. Сага должна получать
данные, и отправлять их в редьюсер. Как только вы увидете данные в редьюсере, можно приступать к
написанию верстки данных. Следующие шаги повторяют пройденные, смотрим какие данные нужны, какие
компоненты написать, как описать получение данных из сети.

<details>
<summary>Как работает авторизация?</summary>

Авторизация в этой домашней работе происходит с помощью токена который вы передаете в форме, на
странице `login`. Авторизация работает следующим образом, пользователь должен ввести токен, после
нажатия кнопки Еnter, компонент отправляет экшеном токен, который с помощью саги передается в модуль
api. Теперь все запросы будут содержать ключ авторизации, и гитхаб не будет ограничивать сетевые
запросы приложения, но даже с ключом там есть лимит на 5000 запросов, так что не удивляйтесь, если
вас заблокируют на 10-15 минут при очень большом количестве запросов.

</details>

<details>
<summary>Как подключить спиннер?</summary>

Если вы хотите такой же спиннер как примере кода, то используйте следующий код:

```js
import Spinner from 'react-svg-spinner';
...
if (isFetching) {
  return <Spinner size="64px" color="fuchsia" gap={5} />;
}
```

</details>


## Задачи. II часть
[Пример](http://5ae3a119e39e7c70d1a1aff4.determined-wing-535e2a.netlify.com)

1. Для компоненты `AppRouter` нужно **написать тесты**:
   * Проверить наличие `Switch`,
   * Проверить наличие компоненты `<PrivateRoute path="/user/dex157" />`,
   * Проверить наличие компоненты `<PrivateRoute path="/user/:name" />`,
   * Проверить наличие комполненты `<Route path="/login" />`,

1. **Написать тесты** для компоненты UserPage:
   * Проверить наличие метода componentDidMount,
   * Проверить наличие метода componentDidUpdate,
   * Проверить наличие спинера/лоадера если props.isFetching === true,
   * Проверить наличие сообщения об отсутствии пользователя если isFetching === false && user ==
     null,
   * В основной верстке должен быть:
     * аватар пользователя,
     * login пользователя,
     * количество фаловеров пользователя,
     * компонент Followers с передачей login через props.
1. **Написать тесты** для редьюсера `users`:
   * Проверить, что экшены `fetchUserRequest`, `fetchUserSuccess`, `fetchUserFailure`:
     * изменяют флаг `isFetching`,
     * очищают поле `data`, если приходит экшен `fetchUserRequest`,
     * наполняют данными `data`, если приходит экшен `fetchUserSuccess`,
     * очищают поле `error`, если приходит экшен `fetchUserRequest` или `fetchUserSuccess`,
     * наполняют данными `error`, если приходит экшен `fetchUserFailure`.
1. Написать компонент `Followers`. При монтировании этого компонента необходимо делать запрос на
   последователей пользователя. Очень удобно передавать `login`(идентификатор пользователя) текущего
   пользователя от `UserPage` к `Followers` через props. Не забудьте проверку на `isFetching`.
1. **Написать тесты** для компоненты `Followers`:
   * Проверить наличие метода класса `componentDidMount`,
   * Проверить наличие лоадера/спинера, если `isFetcing === true`,
   * Проверить что возвращаются компоненты `Followers` в том количестве, в котором передаются в
     props.followers.
1. Написать экшены, редьюсеры(саги присутствуют) для получения последователей(followers).

1. **Написать тесты** для редьюсера `followers`:

   * Проверить, что экшены `fetchFollowersRequest`, `fetchFollowersSuccess`,
     `fetchFollowersFailure`:
     * изменяют флаг `isFetching`,
     * очищают поле `ids`, если приходит экшен `fetchFollowersRequest`,
     * наполняют данными `ids`, если приходит экшен `fetchFollowersSuccess`,
     * очищают поле `error`, если приходит экшен `fetchFollowersRequest` или
       `fetchFollowersSuccess`,
     * наполняют данными `error`, если приходит экшен `fetchFollowersFailure`.

1. Компонент `Follower` очень простой, он содержит аватар и ссылку на страницу пользователя.
1. **Написать тесты** для компоненты `Follower`:
   * Проверить наличие аватара,
   * Проверить наличие `login` пользователя переданного через props,
   * Проверить что ссылка с логина пользователя ведет на `/user/{user.login}`.
<details>
<summary>Как писать тесты для компонент с connect()()</summary>
Не нужно импортировать в тест компонент обернутый в метод connect, нужно делать отдельный экспорт
компоненты, без обертки в метод connect, и тестировать только такой компонент.

```js
// App.js

export class App extends Component {

...
}

export default connect()(App)



// App.test.js

import {App} from './App'

...
```

</details>
