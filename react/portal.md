# Portal

Portal은 부모 컴포넌트의 DOM 계층 구조 바깥에 있는 DOM 노드로 자식을 렌더링하는 방법을 제공합니다.

```javascript
ReactDOM.createPortal(child, container);
```

첫 번째 인자(`child`)는 엘리먼트, 문자열, 혹은 Fragment와 같은 어떤 종류이든 렌더링할 수 있는 React 자식입니다. 두 번쨰 인자(`container`)는 DOM 엘리먼트입니다.

<br>

## 사용법

보통 컴포넌트 렌더링 메소드에서 엘리먼트를 반환할 때 그 엘리먼트는 부모 노드에서 가장 가까운 자식으로 DOM에 마운트됩니다.

```javascript
// React는 새로운 div를 마운트하고 그 안에 자식을 렌더링합니다.
render() {
    return (
        <div>
            {this.props.children}
        </div>
    );
}
```

그런데 가끔 DOM의 다른 위치에 자식을 삽입하는 것이 유용할 수 있습니다.

```javascript
// React는 새로운 div를 생성하지 않고 `domNode` 안에 자식을 렌더링합니다.
// `domNode`는 DOM 노드라면 어떠한 것이든 유효하고, DOM 내부의 어디에 있든지 상관없습니다.
render() {
    return ReactDOM.createPortal(
        this.props.children,
        domNode
    );
}
```

일반적으로 Portal은 부모 컴포넌트에 `overflow: hidden`이나 `z-index`가 있는 경우에 사용하지만, 다이얼로그, 호버카드나 툴팁처럼 시각적으로 자식을 "튀어나오도록" 보여야 하는 경우에도 사용합니다.

<br>

## Portal을 통한 이벤트 버블링

Portal이 DOM 트리의 어디에도 존재할 수 있다 하더라도 모든 다른 면에서 일반적인 React 자식처럼 동작합니다. Context와 같은 기능은 자식이 portal이든지 아니든지 상관없이 같게 동작합니다. 이는 DOM 트리에서의 위치에 상관없이 portal은 여전히 React 트리에 존재하기 때문입니다.

이벤트 버블링도 마찬가지입니다. Portal 내부에서 발생한 이벤트는 React 트리에 포함된 상위 (DOM 트리에서는 상위가 아니더라도) 로 전파될 것입니다.

### 예시

```html
<html>
    <body>
        <div id="app-root"></div>
        <div id="modal-root"></div>
    </body>
</html>
```

`#app-root` 안에 있는 `Parent` 컴포넌트는 형제 노드인 `#modal-root` 안의 컴포넌트에서 전파된 이벤트가 포착되지 않았을 경우 그것을 포착할 수 있습니다.

```javascript
const appRoot = document.getElementById("app-root");
const modalRoot = document.getElementById("modal-root");

class Modal extends React.Component {
    constructor(props) {
        super(props);
        this.el = document.createElement("div");
    }

    // Portal 엘리먼트는 Modal의 자식이 마운트된 후 DOM 트리에 삽입됩니다.
    // 요컨대, 자식은 어디에도 연결되지 않은 DOM 노드로 마운트됩니다.
    // 자식 컴포넌트가 마운트될 때 그것을 즉시 DOM 트리에 연결해야만 한다면,
    // 예를 들어, DOM 노드를 계산한다든지 자식 노드에서 `autoFocus`를 사용한다든지 하는 경우에는,
    // Modal에 state를 추가하고 Modal이 DOM 트리에 삽입되어 있을 때만 자식을 렌더링해주세요.
    componentDidMount() {
        modalRoot.appendChild(this.el);
    }

    componentWillUnmount() {
        modalRoot.removeChild(this.el);
    }

    render() {
        return ReactDOM.createPortal(this.props.children, this.el);
    }
}

class Parent extends React.Component {
    constructor(props) {
        super(props);
        this.state = { clicks: 0 };
        this.handleClick = this.handleClick.bind(this);
    }

    // 버튼이 DOM 상에서 직계 자식이 아니어도
    // 이 함수는 Child에 있는 버튼이 클릭 되었을 때 발생하고 Parent의 state를 갱신합니다.
    handleClick() {
        this.setState((state) => ({
            clicks: state.clicks + 1,
        }));
    }

    render() {
        return (
            <div onClick={this.handleClick}>
                <p>Number of clicks: {this.state.clicks}</p>
                <p>
                    Open up ths browser DevTools to observe that the button is
                    not a child of the div with the onClick handler.
                </p>
                <Modal>
                    <Child />
                </Modal>
            </div>
        );
    }
}

function Child() {
    // `onClick` 속성이 정의되지 않았기 때문에
    // 이 버튼에서의 클릭 이벤트는 부모로 버블링됩니다.
    return (
        <div className='modal'>
            <button>Click</button>
        </div>
    );
}

ReactDOM.render(<Parent />, appRoot);
```

Portal에서 버블링된 이벤트를 부모 컴포넌트에서 포착한다는 것은 본질적으로 portal에 의존하지 않는 조금 더 유연한 추상화 개발이 가능함을 나타냅니다. 예를 들어, `<Modal />` 컴포넌트를 렌더링할 때 부모는 그것이 portal을 사용했는지와 관계없이 `<Modal />`의 이벤트를 포착할 수 있습니다.

<br>

## Reference

https://ko.reactjs.org/docs/portals.html
