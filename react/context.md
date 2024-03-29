# Context

> Context를 이용하면 컴포넌트마다 props를 넘겨주지 않아도 컴포넌트 트리 전체에 데이터를 제공할 수 있습니다.

일반적인 React 애플리케이션에서 데이터는 위에서 아래로 (즉, 부모로부터 자식으로) props를 통해 전달되지만, 애플리케이션 안의 여러 컴포넌트들에 전해줘야 하는 props의 경우 (예를 들면, 선호 로케일, UI 테마) 이 과정이 번거로울 수 있습니다. Context를 이용하면, 트리 단계마다 명시적으로 props를 넘겨주지 않아도 많은 컴포넌트가 데이터를 공유하도록 할 수 있습니다.

<br>

## 언제 context를 써야 할까

Context는 React 컴포넌트 트리 안에서 global data (현재 로그인한 유저, 테마, 선호하는 언어 등) 를 공유할 수 있도록 고안된 방법입니다.

예를 들어, 아래의 코드는 버튼 컴포넌트를 꾸미기 위해 theme props를 명시적으로 넘겨주고 있습니다.

```javascript
class App extends React.Component {
    render() {
        return <Toolbar theme='dark' />;
    }
}

// Toolbar 컴포넌트는 불필요한 theme props를 받아서 ThemedButton에 전달해야 합니다.
function Toolbar(props) {
    return (
        <div>
            <ThemedButton theme={props.theme} />
        </div>
    );
}

class ThemedButton extends React.Component {
    render() {
        return <Button theme={this.props.theme} />;
    }
}
```

Context를 사용하면 중간에 있는 엘리먼트들에게 props를 넘겨주지 않아도 됩니다.

```javascript
// light를 기본값으로 한느 테마 context를 만듭니다.
const ThemeContext = React.createContext("light");

// Provider를 이용해 하위 트리에 테마 값 dark를 보냅니다.
class App extends React.Component {
    render() {
        return (
            <ThemeContext.Provider value='dark'>
                <Toolbar />
            </ThemeContext.Provider>
        );
    }
}

// 이제 중간에 있는 컴포넌트가 일일이 테마를 넘겨줄 필요가 없습니다.
function Toolbar() {
    return (
        <div>
            <ThemedButton />
        </div>
    );
}

// 현재 선택된 테마 값을 읽기 위해 contextType을 지정합니다.
// React는 가장 가까이 있는 테마 Provider를 찾아 그 값을 사용할 것입니다.
class ThemedButton extends React.Component {
    static contextType = ThemeContext;
    render() {
        return <Button theme={this.context} />;
    }
}
```

<br>

## Context를 사용하기 전에 고려할 것

Context의 주된 용도는 다양한 레벨에 네스팅된 많은 컴포넌트에게 데이터를 전달하는 것입니다. Context를 사용하면 컴포넌트를 재사용하기 어려워지므로 꼭 필요할 때만 사용해야 합니다.

여러 레벨에 걸쳐 props 넘기는 걸 대체하는 데에 context보다 컴포넌트 합성이 더 간단한 해결책일 수도 있습니다.

예를 들어 여러 단계 아래에 있는 `Link`와 `Avatar` 컴포넌트에게 `user`와 `avatarSize`라는 props를 전달해야 하는 `Page` 컴포넌트를 생각해봅시다.

```javascript
<Page user={user} avatarSize={avatarSize} />
    // ... 그 아래에 ...
    <PageLayout user={user} avatarSize={avatarSize} />
        // ... 그 아래에 ...
        <NavigationBar user={user} avatarSize={avatarSize} />
            // ... 그 아래에 ...
            <Link href={user.permalink}>
                <Avatar user={user} size={avatarSize} />
            </Link>
```

실제로 사용되는 곳은 `Avatar` 컴포넌트 뿐인데 `user`와 `avatarSize` props를 여러 단계에 걸쳐 보내줘야 한다는 게 번거로워 보일 수 있습니다. 게다가 위에서 `Avatar` 컴포넌트로 보내줘야하는 props가 추가된다면 그 또한 중간 레벨에 모두 추가해줘야 합니다.

`Avatar` 컴포넌트 자체를 넘겨주면 context를 사용하지 않고 이를 해결할 수 있습니다. 그러면 중간에 있는 컴포넌트들이 `user`나 `avatarSize`에 대해 전혀 알 필요가 없습니다.

```javascript
function Page(props) {
    const user = props.user;
    const userLink = (
        <Link href={user.permalink}>
            <Avatar user={user} size={props.avatarSize} />
        </Link>
    );
    return <PageLayout userLink={userLink} />;
}

// 이제 이렇게 쓸 수 있습니다.
<Page user={user} avatarSize={avatarSize} />
    // ... 그 아래에 ...
    <PageLayout user={user} avatarSize={avatarSize} />
        // ... 그 아래에 ...
        <NavigationBar user={user} avatarSize={avatarSize} />
            // ... 그 아래에 ...
            {props.userLink}
```

이렇게 바꾸면 `Link`와 `Avatar` 컴포넌트가 `user`와 `avatarSize` props를 쓴다는 걸 알아야 하는 건 가장 위에 있는 `Page` 뿐입니다.

이러한 제어의 역전(inversion of control)을 이용하면 넘겨줘야 하는 props의 수는 줄고 최상위 컴포넌트의 제어력은 더 커지기 때문에 더 깔끔한 코드를 쓸 수 있는 경우가 많습니다. 하지만 이러한 역전이 항상 옳은 것은 아닙니다. 복잡한 로직을 상위로 옮기면 이 상위 컴포넌트들은 더 난해해지기 마련이고 하위 컴포넌트들은 필요 이상으로 유연해져야 합니다.

자식으로 둘 수 있는 컴포넌트의 수에 제한은 없습니다.

```javascript
function Page(props) {
    const user = props.user;
    const content = <Feed user={user} />;
    const topBar = (
        <NavigationBar>
            <Link href={user.premalink}>
                <Avatar user={user} size={props.avatarSize} />
            </Link>
        </NavigationBar>
    );
    return <PageLayout topBar={topBar} content={content} />;
}
```

이 패턴을 사용하면 자식 컴포넌트와 직속 부모를 분리(decouple)하는 문제는 대개 해결할 수 있습니다. 더 나아가 render props를 이용하면 렌더링 되기 전부터 자식 컴포넌트가 부모 컴포넌트와 소통하게 할 수 있습니다.

하지만 같은 데이터를 트리 안 여러 레벨에 있는 많은 컴포넌트에 주어야 할 때도 있습니다. 이런 데이터 값이 변할 때마다 모든 하위 컴포넌트에게 널리 "방송"하는 것이 context입니다. 흔히 예시로 드는 선호 로케일, 테마, 데이터 캐시 등을 관리하는 데 있어서는 일반적으로 context를 사용하는 게 가장 편리합니다.

<br>

## API

### React.createContext

```javascript
const MyContext = React.createContext(defaultValue);
```

Context 객체를 만듭니다. Centext 객체를 구독하고 있는 컴포넌트를 렌더링할 때 React는 트리 상위에서 가장 가까이 있는 짝이 맞는 `Provider`로부터 현재값을 읽습니다.

`defaultValue` 매개변수는 트리 안에서 적절한 Provider를 찾지 못했을 때만 쓰이는 값입니다. 이 기본값은 컴포넌트를 독립적으로 테스트할 때 유용한 값입니다. Provider를 통해 `undefined`를 값으로 보낸다고 해도 구독 컴포넌트들이 `defaultValue`를 읽지 않습니다.

### Context.Provider

```javascript
<MyContext.Provider value={/* 어떤 값 */}>
```

Context 객체에 포함된 React 컴포넌트인 Provider는 context를 구독하는 컴포넌트들에게 context의 변화를 알리는 역할을 합니다.

Provider 컴포넌트는 `value` prop을 받아서 이 값을 하위 컴포넌트에게 전달합니다. 값을 전달받을 수 있는 컴포넌트의 수에 제한은 없습니다. Provider 하위에 또 다른 Provider를 배치하는 것도 가능하며, 이 경우 하위 Provider의 값이 우선시됩니다.

Provider 하위에서 context를 구독하는 모든 컴포넌트는 Provider의 `value` prop가 바뀔 때마다 다시 렌더링됩니다. Provider로부터 하위 consumer (`.contextType`과 `useContext`를 포함한) 로의 전파는 `shouldComponentUpdate` 메소드가 적용되지 않으므로, 상위 컴포넌트가 업데이트를 건너 뛰더라도 consumer가 업데이트됩니다.

context 값이 바뀌었는지 여부는 `Object.is`와 동일한 알고리즘을 사용해 이전 값과 새로운 값을 비교해 측정됩니다.

### Class.contextType

```javascript
class MyClass extends React.Component {
    render() {
        let value = this.context;
        /* context 값을 이용한 렌더링 */
    }
}
MyClass.contextType = MyContext;
```

`React.createContext()`로 생성한 context 객체를 원하는 클래스의 `contextType` 프로퍼티로 지정할 수 있습니다. 이 프로퍼티를 활용해 클래스 안에서 `this.context`를 이용해 해당 context의 가장 가까운 Provider를 찾아 그 값을 읽을 수 있게됩니다. 이 값은 render를 포함한 모든 컴포넌트 생명주기 메소드에서 사용할 수 있습니다.

"Public class fields syntax"를 사용하고 있다면 static 클래스 프로퍼티로 `contextType`을 지정할 수 있습니다.

```javascript
class MyClass extends React.Component {
    static contextType = MyContext;
    render() {
        let value = this.context;
        /* context 값을 이용한 렌더링 */
    }
}
```

### Context.Consumer

```javascript
<MyContext.Consumer>
    {value => /* context 값을 이용한 렌더링 */}
</MyContext.Consumer>
```

Context 변화를 구독하는 React 컴포넌트입니다. 이 컴포넌트를 사용하면 함수 컴포넌트안에서 context를 구독할 수 있습니다.

Context.Consumer의 자식은 함수여야합니다. 이 함수는 context의 현재값을 받고 React 노드를 반환합니다. 이 함수가 받는 `value` 매개변수 값은 해당 context의 Provider 중 상위 트리에서 가장 가까운 Provider의 `value` prop과 동일합니다. 상위에 Provider가 없다면 `value` 매개변수 값은 `createContext()`에 보냈던 `defaultValue`와 동일할 것입니다.

### Context.displayName

Context 객체는 `displayName` 문자열 속성을 사용해 context를 어떻게 보여줄지 결정합니다.

예를 들어, 아래 컴포넌트는 개발자 도구에 MyDisplayName으로 표시됩니다.

```javascript
const MyContext = React.createContext(defaultValue);
MyContext.displayName = "MyDisplayName";

<MyContext.Provider>  // "MyDisplayName.Provider" in DevTools
<MyContext.Consumer>  // "MyDisplayName.Consumer" in DevTools
```

<br>

## 예시

### 하위 컴포넌트에서 context 업데이트하기

컴포넌트 트리 하위 컴포넌트에서 context를 업데이트해야 할 때가 종종 있습니다. 그럴 때는 context를 통해 메소드를 보내면 됩니다.

#### theme-context.js

```javascript
export const ThemeContext = React.createContext({
    theme: themes.dark,
    toggleTheme: () => {},
});
```

#### theme-toggler-button.js

```javascript
import { ThemeContext } from "./theme-context";

function ThemeTogglerButton() {
    return (
        <ThemeContext.Consumer>
            {({theme, toggleTheme}) => (
                <button
                    onClick={toggleTheme}
                    style={{backgroundColor: theme.background}}>
                    Toggle Theme
                </button>
            )}
        </ThemeContext.Consumer>>
    )
}

export default ThemeTogglerButton;
```

#### app.js

```javascript
import { ThemeContext, themes } from "./theme-context";
import ThemeTogglerButton from "./theme-toggler-button";

class App extends React.Component {
    constructor(props) {
        super(props);

        this.toggleTheme = () => {
            this.setState((state) => ({
                theme: state.theme === themes.dark ? themes.light : themes.dark,
            }));
        };

        this.state = {
            theme: themes.light,
            toggleTheme: this.toggleTheme,
        };
    }

    render() {
        return (
            <ThemeContext.Provider value={this.state}>
                <Content />
            </ThemeContext.Provider>
        );
    }
}

function Content() {
    return (
        <div>
            <ThemeTogglerButton />
        </div>
    );
}

ReactDOM.render(<App />, document.root);
```

### 여러 context 구독하기

```javascript
// 기본값이 light인 ThemeContext
const ThemeContext = React.createContext("light");

// 로그인한 유저 정보를 담는 UserContext
const UserContext = React.createContext({
    name: "Guest",
});

class App extends React.Component {
    render() {
        const {signedInUser, theme} = this.props;

        // Context 초기값을 제공하는 App 컴포넌트
        return (
            <ThemeContext.Provider value={theme}>
                <UserContext.Provider value={signedInUser}>
                    <Layout />
                </UserContext.Provider>
            </ThemeContext.Provider>>
        )
    }
}

function Layout() {
    return (
        <div>
            <Sidebar />
            <Content />
        </div>
    )
}

// 여러 context의 값을 받는 컴포넌트
function Content() {
    return (
        <ThemeContext.Consumer>
            {theme => (
                <UserContext.Consumer>
                    {user => (
                        <ProfilePage user={user} theme={theme} />
                    )}
                </UserContext.Consumer>
            )}
        </ThemeContext.Consumer>
    )
}
```

각 context마다 Consumer를 개벽 노드로 만들게 설계되어있는데, 이것은 context 변화로 인해 다시 렌더링하는 과정을 빠르게 유지하기 위함입니다.

<br>

## 주의사항

다시 렌더링할지 여부를 정할 떄 reference를 확인하기 때문에, Provider의 부모가 렌더링 될 때마다 불필요하게 하위 컴포넌트가 다시 렌더링 되는 문제가 생길 수도 있습니다. 예를 들어 아래 코드는 `value`가 바뀔 때마다 매번 새로운 객체가 생성되므로 Provider가 렌더링 될 때마다 하위에서 구독하고 있는 컴포넌트 모두가 다시 렌더링 될 것입니다.

```javascript
class App extends React.Component {
    render() {
        return (
            <MyContext.Provider value={{ something: "something" }}>
                <Toolbar />
            </MyContext.Provider>
        );
    }
}
```

이를 피하기 위해서는 `value` 값을 부모의 state로 끌어 올려야 합니다.

```javascript
class App extends React.Component {
    constructor(props) {
        super(props);
        this.state = {
            value: { something: "something" },
        };
    }

    render() {
        return (
            <MyContext.Provider value={this.state.value}>
                <Toolbar />
            </MyContext.Provider>
        );
    }
}
```

<br>

## Reference

https://ko.reactjs.org/docs/context.html
