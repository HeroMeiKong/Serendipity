# React 官网学习笔记

★★★★★★★★★★

更新于2019-01-10
这是自己平时书写的笔记，有很多漏洞，也有很多错误，还望大家多多指正，感谢🙏

★★★★★★★★★★

1. 所有 React 组件都必须像纯函数一样保护它们的 props 不被更改

2. 在 React 中另一个不同点是你不能通过返回 false 的方式阻止默认行为。你必须显式的使用 preventDefault

    ```jsx
    function ActionLink() {
      function handleClick(e) {
        e.preventDefault();
        console.log('The link was clicked.');
      }

      return (
        <a href="#" onClick={handleClick}>
          Click me
        </a>
      );
    }
    ```

3. 当你的函数需要传旨的时候，你应该绑定 `this`：

    ```jsx
    class Toggle extends React.Component {
      constructor(props) {
        super(props);
        this.state = {isToggleOn: true};

        // 为了在回调中使用 `this`，这个绑定是必不可少的
        this.handleClick = this.handleClick.bind(this);
      }

      handleClick() {
        this.setState(state => ({
          isToggleOn: !state.isToggleOn
        }));
      }

      render() {
        return (
          <button onClick={this.handleClick}>
            {this.state.isToggleOn ? 'ON' : 'OFF'}
          </button>
        );
      }
    }
    ```

   你必须谨慎对待 JSX 回调函数中的 `this`，在 JavaScript 中，class 的方法默认不会[绑定](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_objects/Function/bind) `this`。如果你忘记绑定 `this.handleClick` 并把它传入了 `onClick`，当你调用这个函数的时候 `this` 的值为 `undefined`。通常情况下，如果你没有在方法后面添加 `()`，例如 `onClick={this.handleClick}`，你应该为这个方法绑定 `this`。

   **为什么绑定很必要的？**

    ```javascript
    obj.method();

    var method = obj.method;
    method();
    //两个不一样

    例子:
    const module = {
      x: 42,
      getX: function() {
        return this.x;
      }
    };

    const unboundGetX = module.getX;
    console.log(unboundGetX());// undefined

    const boundGetX = unboundGetX.bind(module);
    console.log(boundGetX()); // 42let
    // this 的指定不对
    bind方法确保了第二种写法与第一种写法相同
    ```

   不使用 `bind` 方法一：`public class fields 语法`

    ```jsx
    class LoggingButton extends React.Component {
      // 此语法确保 `handleClick` 内的 `this` 已被绑定。
      // 注意: 这是 *实验性* 语法。
      handleClick = () => {
        console.log('this is:', this);
      }

      render() {
        return (
          <button onClick={this.handleClick}>
            Click me
          </button>
        );
      }
    }
    ```

   不使用 `bind` 方法二：箭头函数

    ```jsx
    class LoggingButton extends React.Component {
      handleClick() {
        console.log('this is:', this);
      }

      render() {
        // 此语法确保 `handleClick` 内的 `this` 已被绑定。
        return (
          <button onClick={() => this.handleClick()}>
            Click me
          </button>
        );
      }
    }
    ```

4. 向事件处理程序传递参数

    ```react
    # 箭头函数，e 在指定位置传值
    <button onClick={(e) => this.deleteRow('1', e, '2')}>Delete Row</button>
    # Function.prototype.bind，注意第一个值this|null 这里是不会传递的，e 在末尾传值
    <button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>
    ```

5. 简单条件渲染
   *注意*：使用函数***直接传值***，使用组件则通过***对象(props)传值***

    ```jsx
    function Greeting(props) {
      const isLoggedIn = props.isLoggedIn;
      if (isLoggedIn) {
        return <UserGreeting />;
      }
      return <GuestGreeting />;
    }

    function Greeting1(isLoggedIn) {
      if (isLoggedIn) {
        return <UserGreeting />;
      }
      return <GuestGreeting />;
    }

    ReactDOM.render(
      // this.Greeting1(false),
      <Greeting isLoggedIn={false} />,
      document.getElementById('root')
    );
    ```

6. 当使用 `function() {} + bind()` 结合时，推荐写法：

    ```jsx
    class LoginControl extends React.Component {
      constructor(props) {
        super(props);
          this.handleLoginClick = this.handleLoginClick.bind(this);
      }

      handleLoginClick() {
        this.setState({isLoggedIn: true});
      }

      render() {
        // 不推荐写在这里 this.handleLoginClick = this.handleLoginClick.bind(this);
        // 注意：在 render 方法中使用 Function.prototype.bind 会在每次组件渲染时创建一个新的函数，可能会影响性能
        return (
          <div>
            <LogoutButton onClick={this.handleLogoutClick} />
          </div>
        );
      }
    }
    ```

7. 阻止组件渲染可以这样：

    ```jsx
    function WarningBanner(props) {
      if (!props.warn) {
        return null;
      }

      return (
        <div className="warning">
          Warning!
        </div>
      );
    }
    ```

8. 如果列表项目的顺序可能会变化，我们不建议使用索引来用作 key 值，因为这样做会导致性能变差，还可能引起组件状态的问题。如果你选择不指定显式的 key 值，那么 React 将默认使用索引用作为列表项目的 key 值。

9. 在 `map()` 方法中的元素需要设置 key 属性:

    ```jsx
    const listItems = numbers.map((number) =>
      // 正确！key 应该在数组的上下文中被指定
      <ListItem key={number.toString()} value={number} />
    );
    ```

10. key 会传递信息给 React ，但不会传递给你的组件。组件可以读出 `props.id`，但是不能读出 `props.key`

11. 表单元素：`<input>`、 `<textarea>` 、 `<select>`

12. **受控组件**：表单数据是由 React 组件来管理的。使 React 的 state 成为“唯一数据源”。渲染表单的 React 组件还控制着用户输入过程中表单发生的操作。被 React 以这种方式控制取值的表单输入元素就叫做“受控组件”。对于受控组件，输入的值始终由 React 的 state 驱动。

13. **非受控组件**：表单数据将交由 DOM 节点来处理。

14. 在 React 中，`<input>`、 `<textarea>` 、 `<select>` 的值都由 value 控制，如果 `<select>` 想要选中多个，则传入数组即可

15. 处理多个输入：当需要处理多个 `input` 元素时，我们可以给每个元素添加 `name` 属性，并让处理函数根据 `event.target.name` 的值选择要执行的操作。

    ```jsx
    class Reservation extends React.Component {
      constructor(props) {
        super(props);
        this.state = {
          isGoing: true,
          numberOfGuests: 2
        };
        ...
      }

      handleInputChange(event) {
        ...
        const name = target.name;
        this.setState({
          [name]: value
        });
      }

      render() {
        return (
          <form>
            <input name="isGoing" ... />
            <input name="numberOfGuests" ... />
          </form>
        );
      }
    }
    ```

16. 受控输入空值：在[受控组件](https://zh-hans.reactjs.org/docs/forms.html#controlled-components)上指定 value 的 prop 会阻止用户更改输入。如果你指定了 `value`，但输入仍可编辑，则可能是你意外地将`value` 设置为 `undefined` 或 `null`。

17. 状态提升：将多个组件中需要共享的 state 向上移动到它们的最近共同父组件中，便可实现共享 state。

18. 在 React 中推荐使用组合而非继承来实现代码重用：`props.children`

    ```jsx
    function FancyBorder(props) {
      return (
        <div>
          {props.children}
        </div>
      );
    }

    function WelcomeDialog() {
      return (
        <FancyBorder color="blue">
          <h1 className="Dialog-title">
            Welcome
          </h1>
          <p className="Dialog-message">
            Thank you for visiting our spacecraft!
          </p>
        </FancyBorder>
      );
    }
    ```

19. 如果需要多个“洞“｜“槽”(slot)，不建议使用 `props.children` 而是自行约定，例如：

    ```jsx
    function SplitPane(props) {
      return (
        <div className="SplitPane">
          <div className="SplitPane-left">
            {props.left}
          </div>
          <div className="SplitPane-right">
            {props.right}
          </div>
        </div>
      );
    }

    function App() {
      return (
        <SplitPane
          left={ <Contacts /> }
          right={ <Chat /> }
        />
      );
    }
    ```

    ***注意：组件可以接受任意 props，包括基本数据类型，React 元素以及函数。***

20. WAI-ARIA([网络无障碍倡议 - 无障碍互联网应用（Web Accessibility Initiative - Accessible Rich Internet Applications）](https://www.w3.org/WAI/intro/aria))
    注意：JSX 支持所有 `aria-*` HTML 属性。虽然大多数 React 的 DOM 变量和属性命名都使用驼峰命名（camelCased），但 aria-* 应该像其在 HTML 中一样使用带连字符的命名法（也叫诸如 hyphen-cased，kebab-case，lisp-case)。

    ```jsx
    <input
      type="text"
      aria-label={labelText}
      aria-required="true"
      onChange={onchangeHandler}
      value={inputValue}
      name="name"
    />
    ```

21. React Fragments：为了不破坏HTML语义化

    ```jsx
    import React, { Fragment } from 'react';
    // fragment 普通用法
    function ListItem({ item }) {
      return (
        <Fragment>
          <dt>{item.term}</dt>
          <dd>{item.description}</dd>
        </Fragment>
      );
    }
    // fragment 带 key
    function Glossary(props) {
      return (
        <dl>
          {props.items.map(item => (
            // Fragments should also have a `key` prop when mapping collections
            <Fragment key={item.id}>
              <dt>{item.term}</dt>
              <dd>{item.description}</dd>
            </Fragment>
          ))}
        </dl>
      );
    }
    // fragment 短语法
    function ListItem({ item }) {
      return (
        <>
          <dt>{item.term}</dt>
          <dd>{item.description}</dd>
        </>
      );
    }
    ```

22. 标记：所有的 HTML 表单控制，例如 `<input>` 和 `<textarea>` ，都需要被标注来实现无障碍辅助功能。我们需要提供屏幕朗读器以解释性标注。

    ```jsx
    // 请注意 for 在 JSX 中应该被写作 htmlFor：
    <label htmlFor="namedInput">Name:</label>
    <input id="namedInput" type="text" name="name"/>
    ```

23. setState 写法：

    ```jsx
    this.setState(currentState => ({
      isOpen: !currentState.isOpen
    }));

    this.setState({
      isOpen: !this.state.isOpen
    });
    ```

24. 当需要关闭类似弹窗的时候，可给指定元素添加 `ref` ，通过 `refs` 来判断是否点击的非指定元素：

    ```jsx
    class OuterClickExample extends React.Component {
      constructor(props) {
        super(props);
        this.toggleContainer = React.createRef();
      }

      componentDidMount() {
        window.addEventListener('click', this.onClickOutsideHandler);
      }

      componentWillUnmount() {
        window.removeEventListener('click', this.onClickOutsideHandler);
      }

      onClickOutsideHandler(event) {
        if (!this.toggleContainer.current.contains(event.target)) {
          this.setState({ isOpen: false });
        }
      }

      render() {
        return (
          <div ref={this.toggleContainer}>
            <button>Select an option</button>
          </div>
        );
      }
    }
    ```

25. 无障碍使用浮窗：使用 `onBlur` 和 `onFocus` 替代 `click` ：

    ```jsx
    class BlurExample extends React.Component {
      constructor(props) {
        super(props);

        this.state = { isOpen: false };
        this.timeOutId = null;

        this.onClickHandler = this.onClickHandler.bind(this);
        this.onBlurHandler = this.onBlurHandler.bind(this);
        this.onFocusHandler = this.onFocusHandler.bind(this);
      }

      onClickHandler() {
        this.setState(currentState => ({
          isOpen: !currentState.isOpen
        }));
      }

      // 我们在下一个时间点使用 setTimeout 关闭弹窗。
      // 这是必要的，因为失去焦点事件会在新的焦点事件前被触发，
      // 我们需要通过这个步骤确认这个元素的一个子节点
      // 是否得到了焦点。
      onBlurHandler() {
        this.timeOutId = setTimeout(() => {
          this.setState({
            isOpen: false
          });
        });
      }

      // 如果一个子节点获得了焦点，不要关闭弹窗。
      onFocusHandler() {
        clearTimeout(this.timeOutId);
      }

      render() {
        // React 通过把失去焦点和获得焦点事件传输给父节点
        // 来帮助我们。
        return (
          <div onBlur={this.onBlurHandler}
               onFocus={this.onFocusHandler}>
            <button onClick={this.onClickHandler}
                    aria-haspopup="true"
                    aria-expanded={this.state.isOpen}>
              Select an option
            </button>
            {this.state.isOpen && (
              <ul>
                <li>Option 1</li>
                <li>Option 2</li>
                <li>Option 3</li>
              </ul>
            )}
          </div>
        );
      }
    }
    ```

26. React.lazy & Suspense：

    ```jsx
    import React, { Suspense } from 'react';

    const OtherComponent = React.lazy(() => import('./OtherComponent'));

    function MyComponent() {
      return (
        <div>
          <Suspense fallback={<div>Loading...</div>}>
            <OtherComponent />
          </Suspense>
        </div>
      );
    }
    ```

27. Context：提供了一个无需为每层组件手动添加 props，就能在组件树间进行数据传递的方法。
    **通用的场景**：管理当前的 locale，theme，或者一些缓存数据。

    ***注意*：如果你只是想避免层层传递一些属性，可以通过[组件组合（component composition）](https://zh-hans.reactjs.org/docs/composition-vs-inheritance.html)将*组件*传递下去**
    也可使用 `Render Props` ：

    ```jsx
    <DataProvider render={data => (
      <h1>Hello {data.target}</h1>
    )}/>
    ```

28. `React.createContext`：创建一个 Context 对象。当 React 渲染一个订阅了这个 Context 对象的组件，这个组件会从组件树中离自身最近的那个匹配的 `Provider` 中读取到当前的 context 值。

    ​**只有**当组件所处的树中没有匹配到 Provider 时，其 `defaultValue` 参数才会生效。这有助于在不使用 Provider 包装组件的情况下对组件进行测试。注意：将 `undefined` 传递给 Provider 的 value 时，使用该值组件的 `defaultValue` 不会生效。

    ```jsx
    const MyContext = React.createContext(defaultValue);
    ```

29. `Context.Provider`：每个 Context 对象都会返回一个 Provider React 组件，它允许消费组件订阅 context 的变化。

    ​Provider 接收一个 `value` 属性，传递给消费组件。一个 Provider 可以和多个使用该值组件有对应关系。多个 Provider 也可以嵌套使用，里层的会覆盖外层的数据。

    ​当 Provider 的 `value` 值发生变化时，它内部的所有使用该值组件都会重新渲染。Provider 及其内部 consumer(顾客) 组件都不受制于 `shouldComponentUpdate` 函数，因此当 consumer(顾客) 组件在其祖先组件退出更新的情况下也能更新。

    ```jsx
    <MyContext.Provider value={/* 某个值 */}>
    ```

30. `Class.contextType`：挂载在 class 上的 `contextType` 属性会被重赋值为一个由 [`React.createContext()`](https://zh-hans.reactjs.org/docs/context.html#reactcreatecontext) 创建的 Context 对象。这能让你使用 `this.context` 来消费最近 Context 上的那个值。你可以在任何生命周期中访问到它，包括 render 函数中。
    **注意：**
    1. 你只通过该 API 订阅单一 context。

    2. 如果你正在使用实验性的 [public class fields 语法](https://babeljs.io/docs/plugins/transform-class-properties/)，你可以使用 `static` 这个类属性来初始化你的 `contextType`。

    ```jsx
    class MyClass extends React.Component {
      static contextType = MyContext; // public class fields need
      componentDidMount() {
        let value = this.context;
        /* 在组件挂载完成后，使用 MyContext 组件的值来执行一些有副作用的操作 */
      }
      componentDidUpdate() {
        let value = this.context;
        /* ... */
      }
      componentWillUnmount() {
        let value = this.context;
        /* ... */
      }
      render() {
        let value = this.context;
        /* 基于 MyContext 组件的值进行渲染 */
      }
    }
    MyClass.contextType = MyContext;

    ```

31. `Context.Consumer`：一个 React 组件可以订阅 context 的变更，这让你在[函数式组件](https://zh-hans.reactjs.org/docs/components-and-props.html#function-and-class-components)中可以订阅 context。

    ​这个函数接收当前的 context 值，并返回一个 React 节点。传递给函数的 `value` 值等价于组件树上方离这个 context 最近的 Provider 提供的 `value` 值。如果没有对应的 Provider，`value` 参数等同于传递给 `createContext()` 的 `defaultValue`。

    ```jsx
    <MyContext.Consumer>
      {value => /* 基于 context 值进行渲染*/}
    </MyContext.Consumer>
    ```

32. `Context.displayName`：context 对象接受一个名为 `displayName` 的 property，类型为字符串。React DevTools 使用该字符串来确定 context 要显示的内容。

    ```jsx
    const MyContext = React.createContext(/* some value */);
    MyContext.displayName = 'MyDisplayName';

    <MyContext.Provider> // "MyDisplayName.Provider" 在 DevTools 中
    <MyContext.Consumer> // "MyDisplayName.Consumer" 在 DevTools 中
    ```

33. ES5 中，方法写在 constructor 里面和外面的区别：

    ```javascript
    /* A类，方法写在constructor里面 */
    class A{
      constructor(){
        this.show = function (){
          console.log( 'A show' )
        }
      }
    }

    /* B类，方法写在constructor外面 */
    class B{
      show(){
        console.log( 'B show' )
      }
    }

    const a1 = new A();
    const a2 = new A();
    console.log(a1.show === a2.show); // false
    const b1 = new B();
    const b2 = new B();
    console.log(b1.show === b2.show); // true

    说明：B的方法是在 prototype 上的
    A每实例一个类就创建一个独有的方法，每个都需要单独修改
    B则是公用原型上的方法，改一次
    即 A 的 a1.__proto__ 无 show 方法，是不会影响已存在的对象 a1，a2 的 show 方法的
    而 B 的 a1.__proto__ 有 show 方法，则会同时修改 b1，b2
    ```

34. 在 React 中，获取父类传递的值可通过以下方式：

    ```jsx
    class ThemedButton extends React.Component {
      render() {
        let props = this.props;
        return (
          <button
            {...props}
            style={{backgroundColor: 'yellow'}}
          />
        );
      }
    }

    class A extends React.Component {
      render() {
        return (
          <ThemedButton
            aria-label="hahaha"
            aria-required="true"
            onClick={this.changeTheme}>
            what
          </ThemedButton>
        );
      }
    }

    // 输出
    <button style="background-color: 'yellow'" aria-label="hahaha" aria-required="true">
      what
    </button>

    ```

35. 如何使用 `Context.Consumer` ？

    ```jsx
    const themes = {
      light: {
        foreground: '#000000',
        background: '#eeeeee',
      },
      dark: {
        foreground: '#ffffff',
        background: '#222222',
      },
    };

    const ThemeContext = React.createContext({
      theme: themes.dark,
      toggleTheme: () => {},
    });

    function ThemeTogglerButton() {
      // Theme Toggler 按钮不仅仅只获取 theme 值，它也从 context 中获取到一个 toggleTheme 函数
      return (
        <ThemeContext.Consumer>
          {({theme, toggleTheme}) => (
            <button
              onClick={toggleTheme}
              style={{backgroundColor: theme.background}}>
              Toggle Theme
            </button>
          )}
        </ThemeContext.Consumer>
      );
    }

    class App extends React.Component {
      constructor(props) {
        super(props);

        // 方式一
        this.toggleTheme = () => {
          this.setState(state => ({
            theme:
              state.theme === themes.dark
                ? themes.light
                : themes.dark,
          }));
        };

        // State 也包含了更新函数，因此它会被传递进 context provider。
        this.state = {
          theme: themes.light,
          toggleTheme: this.toggleTheme,
        };
      }

      // 方式二
      // toggleTheme = () => {
      //   this.setState(state => ({
      //     theme:
      //     state.theme === themes.dark
      //     ? themes.light
      //     : themes.dark,
      //   }));
      // };

      render() {
        // 整个 state 都被传递进 provider
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

    ReactDOM.render(
      <App />,
      document.getElementById('root')
    );

    ```

36. 使用多个 Context：为了确保 context 快速进行重渲染，React 需要使每一个 consumers 组件的 context 在组件树中成为一个单独的节点。

    ```jsx
    // Theme context，默认的 theme 是 “light” 值
    const ThemeContext = React.createContext('light');

    // 用户登录 context
    const UserContext = React.createContext({
      name: 'Guest',
    });

    class App extends React.Component {
      render() {
        const {signedInUser, theme} = this.props;

        // 提供初始 context 值的 App 组件
        return (
          <ThemeContext.Provider value={theme}>
            <UserContext.Provider value={signedInUser}>
              <Layout />
            </UserContext.Provider>
          </ThemeContext.Provider>
        );
      }
    }

    function Layout() {
      return (
        <div>
          <Sidebar />
          <Content />
        </div>
      );
    }

    // 一个组件可能会消费多个 context
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
      );
    }
    ```

37. 使用 context 的注意事项：因为 context 会使用参考标识（reference identity）来决定何时进行渲染，这里可能会有一些陷阱，当 provider 的父组件进行重渲染时，可能会在 consumers 组件中触发意外的渲染。举个例子，当每一次 Provider 重渲染时，以下的代码会重渲染所有下面的 consumers 组件，因为 `value` 属性总是被赋值为新的对象：

    ```jsx
    // the wrong way
    class App extends React.Component {
      render() {
        return (
          <MyContext.Provider value={{something: 'something'}}>
            <Toolbar />
          </MyContext.Provider>
        );
      }
    }

    // the right way
    class App extends React.Component {
      constructor(props) {
        super(props);
        this.state = {
          value: {something: 'something'},
        };
      }

      render() {
        return (
          <Provider value={this.state.value}>
            <Toolbar />
          </Provider>
        );
      }
    }
    ```

38. 错误边界：是一种 React 组件，这种组件**可以捕获并打印发生在其子组件树任何位置的 JavaScript 错误，并且，它会渲染出备用 UI**，而不是渲染那些崩溃了的子组件树。错误边界在渲染期间、生命周期方法和整个组件树的构造函数中捕获错误。

    ​只有 class 组件才可以成为错误边界组件。大多数情况下, 你只需要声明一次错误边界组件, 并在整个应用中使用它。

    注意**错误边界仅可以捕获其子组件的错误**，它无法捕获其自身的错误。如果一个错误边界无法渲染错误信息，则错误会冒泡至最近的上层错误边界，这也类似于 JavaScript 中 catch {} 的工作机制。

    **注意：**错误边界**无法**捕获以下场景中产生的错误：

    - 事件处理（[了解更多](https://zh-hans.reactjs.org/docs/error-boundaries.html#how-about-event-handlers)）
    - 异步代码（例如 `setTimeout` 或 `requestAnimationFrame` 回调函数）
    - 服务端渲染
    - 它自身抛出来的错误（并非它的子组件）

    ​如果一个 class 组件中定义了 [`static getDerivedStateFromError()`](https://zh-hans.reactjs.org/docs/react-component.html#static-getderivedstatefromerror) 或 [`componentDidCatch()`](https://zh-hans.reactjs.org/docs/react-component.html#componentdidcatch) 这两个生命周期方法中的任意一个（或两个）时，那么它就变成一个错误边界。当抛出错误后，请使用 `static getDerivedStateFromError()` 渲染备用 UI ，使用 `componentDidCatch()` 打印错误信息。

    ```jsx
    class ErrorBoundary extends React.Component {
      constructor(props) {
        super(props);
        this.state = { hasError: false };
      }

      static getDerivedStateFromError(error) {
        // 更新 state 使下一次渲染能够显示降级后的 UI
        return { hasError: true };
      }

      componentDidCatch(error, errorInfo) {
        // 你同样可以将错误日志上报给服务器
        logErrorToMyService(error, errorInfo);
      }

      render() {
        if (this.state.hasError) {
          // 你可以自定义降级后的 UI 并渲染
          return <h1>Something went wrong.</h1>;
        }

        return this.props.children;
      }
    }

    // 使用方式
    <ErrorBoundary>
      <MyWidget />
    </ErrorBoundary>

    ```

39. 未捕获错误（Uncaught Errors）的新行为：**任何未被错误边界捕获的错误将会导致整个 React 组件树被卸载。**

40. 为什么不用 try / catch 捕获异常？

    ```jsx
    // try / catch 很棒但它仅能用于命令式代码（imperative code）
    try {
      showButton();
    } catch (error) {
      // ...
    }

    // React 组件是声明式的并且具体指出什么需要被渲染
    <Button />
    ```

    错误边界**无法**捕获事件处理器内部的错误。如果你需要在事件处理器内部捕获错误，使用普通的 JavaScript `try` / `catch` 语句：

    ```jsx
    class MyComponent extends React.Component {
      constructor(props) {
        super(props);
        this.state = { error: null };
        this.handleClick = this.handleClick.bind(this);
      }

      handleClick() {
        try {
          // 执行操作，如有错误则会抛出
        } catch (error) {
          this.setState({ error });
        }
      }

      render() {
        if (this.state.error) {
          return <h1>Caught an error.</h1>
        }
        return <button onClick={this.handleClick}>Click Me</button>
      }
    }
    ```

41. 编程范式：命令式编程(Imperative)、声明式编程(Declarative)和函数式编程(Functional)
    - 命令式编程：关注计算机执行的步骤，即一步一步告诉计算机先做什么再做什么。
    - 声明式编程：以数据结构的形式来表达程序执行的逻辑。告诉计算机应该做什么，但不指定具体要怎么做。
    - 函数式编程：即只关注做什么而不是怎么做。但函数式编程不仅仅局限于声明式编程。
  
42. Refs 转发：Ref 转发是一项将 [ref](https://zh-hans.reactjs.org/docs/refs-and-the-dom.html) 自动地通过组件传递到其一子组件的技巧。对于可重用的组件库是很有用的。**Ref 转发是一个可选特性，其允许某些组件接收 `ref`，并将其向下传递（换句话说，“转发”它）给子组件。**

    ```jsx
    const FancyButton = React.forwardRef((props, ref) => (
      <button ref={ref} className="FancyButton">
        {props.children}
      </button>
    ));

    // 你可以直接获取 DOM button 的 ref：
    const ref = React.createRef();
    <FancyButton ref={ref}>Click me!</FancyButton>;
    ```

    **注意：第二个参数 `ref` 只在使用 `React.forwardRef` 定义组件时存在。常规函数和 class 组件不接收 `ref` 参数，且 props 中也不存在 `ref`。**

    Ref 转发不仅限于 DOM 组件，你也可以转发 refs 到 class 组件实例中。

43. 在高阶组件中转发 refs：

    ```jsx
    function logProps(WrappedComponent) {
      class LogProps extends React.Component {
        componentDidUpdate(prevProps) {
          console.log('old props:', prevProps);
          console.log('new props:', this.props);
        }

        render() {
          return <WrappedComponent {...this.props} />;
        }
      }

      return LogProps;
    }

    class FancyButton extends React.Component {
      focus() {
        // ...
      }

      // ...
    }

    // 我们导出 LogProps，而不是 FancyButton。
    // 虽然它也会渲染一个 FancyButton。
    export default logProps(FancyButton);
    ```

    注意：refs 将不会透传下去。这是因为 ref 不是 prop 属性。就像 key 一样，其被 React 进行了特殊处理。**如果你对 HOC 添加 ref，该 ref 将引用最外层的容器组件，而不是被包裹的组件。**

    ```jsx
    import FancyButton from './FancyButton';

    const ref = React.createRef();

    // 我们导入的 FancyButton 组件是高阶组件（HOC）LogProps。
    // 尽管渲染结果将是一样的，
    // 但我们的 ref 将指向 LogProps 而不是内部的 FancyButton 组件！
    // 这意味着我们不能调用例如 ref.current.focus() 这样的方法
    <FancyButton
      label="Click Me"
      handleClick={handleClick}
      ref={ref}
    />;
    ```

    但是，我们可以使用 `React.forwardRef` API 明确地将 refs 转发到内部的 `FancyButton` 组件。`React.forwardRef` 接受一个渲染函数，其接收 `props` 和 `ref` 参数并返回一个 React 节点：

    ```jsx
    function logProps(Component) {
      class LogProps extends React.Component {
        componentDidUpdate(prevProps) {
          console.log('old props:', prevProps);
          console.log('new props:', this.props);
        }

        render() {
          const {forwardedRef, ...rest} = this.props;

          // 将自定义的 prop 属性 “forwardedRef” 定义为 ref
          return <Component ref={forwardedRef} {...rest} />;
        }
      }

      // 注意 React.forwardRef 回调的第二个参数 “ref”。
      // 我们可以将其作为常规 prop 属性传递给 LogProps，例如 “forwardedRef”
      // 然后它就可以被挂载到被 LogProps 包裹的子组件上。
      return React.forwardRef((props, ref) => {
        return <LogProps {...props} forwardedRef={ref} />;
      });
    }
    ```

44. 高阶组件：**是参数为组件，返回值为新组件的函数。**
  HOC 在 React 的第三方库中很常见，例如 Redux 的 [`connect`](https://github.com/reduxjs/react-redux/blob/master/docs/api/connect.md#connect) 和 Relay 的 [`createFragmentContainer`](http://facebook.github.io/relay/docs/en/fragment-container.html)。
  
    高阶组件使用场景：我们需要一个抽象，允许我们在一个地方定义这个逻辑，并在许多组件之间共享它。

    ```jsx
    // 此函数接收一个组件...
    function withSubscription(WrappedComponent, selectData) {
      // ...并返回另一个组件...
      return class extends React.Component {
        constructor(props) {
          super(props);
          this.handleChange = this.handleChange.bind(this);
          this.state = {
            data: selectData(DataSource, props)
          };
        }

        componentDidMount() {
          // ...负责订阅相关的操作...
          DataSource.addChangeListener(this.handleChange);
        }

        componentWillUnmount() {
          DataSource.removeChangeListener(this.handleChange);
        }

        handleChange() {
          this.setState({
            data: selectData(DataSource, this.props)
          });
        }

        render() {
          // ... 并使用新数据渲染被包装的组件!
          // 请注意，我们可能还会传递其他属性
          return <WrappedComponent data={this.state.data} {...this.props} />;
        }
      };
    }
    ```

    注意，HOC 不会修改传入的组件，也不会使用继承来复制其行为。相反，HOC 通过将组件 *包装* 在容器组件中来 *组成* 新组件。HOC 是纯函数，没有副作用。

    - 不要改变原始组件。使用组合。

    - 约定：将不相关的 props 传递给被包裹的组件 --- 保证 HOC 的灵活性以及可复用性。

      ```jsx
      render() {
        // 过滤掉非此 HOC 额外的 props，且不要进行透传
        const { extraProp, ...passThroughProps } = this.props;

        // 将 props 注入到被包装的组件中。
        // 通常为 state 的值或者实例方法。
        const injectedProp = someStateOrInstanceMethod;

        // 将 props 传递给被包装组件
        return (
          <WrappedComponent
            injectedProp={injectedProp}
            {...passThroughProps}
          />
        );
      }
      ```

    - 约定：最大化可组合性
  
      ```jsx
      // React Redux 的 `connect` 函数
      const ConnectedComment = connect(commentSelector, commentActions)(CommentList);

      // connect 是一个函数，它的返回值为另外一个函数。
      const enhance = connect(commentListSelector, commentListActions);
      // 返回值为 HOC，它会返回已经连接 Redux store 的组件
      const ConnectedComment = enhance(CommentList);

      --------------------------------------------------------

      // 而不是这样...
      const EnhancedComponent = withRouter(connect(commentSelector)(WrappedComponent))

      // ... 你可以编写组合工具函数
      // compose(f, g, h) 等同于 (...args) => f(g(h(...args)))
      const enhance = compose(
        // 这些都是单参数的 HOC
        withRouter,
        connect(commentSelector)
      )
      const EnhancedComponent = enhance(WrappedComponent)
      ```
  
    - 约定：包装显示名称以便轻松调试
  
      ```jsx
      function withSubscription(WrappedComponent) {
        class WithSubscription extends React.Component {/* ... */}
        WithSubscription.displayName = `WithSubscription(${getDisplayName(WrappedComponent)})`;
        return WithSubscription;
      }

      function getDisplayName(WrappedComponent) {
        return WrappedComponent.displayName || WrappedComponent.name || 'Component';
      }
      ```

      **不要在 render 方法中使用 HOC**

      **务必复制静态方法**

      ```jsx
      function enhance(WrappedComponent) {
        class Enhance extends React.Component {/*...*/}
        // 必须准确知道应该拷贝哪些方法 :(
        Enhance.staticMethod = WrappedComponent.staticMethod;
        return Enhance;
      }

      // or
      import hoistNonReactStatic from 'hoist-non-react-statics';
      function enhance(WrappedComponent) {
        class Enhance extends React.Component {/*...*/}
        hoistNonReactStatic(Enhance, WrappedComponent);
        return Enhance;
      }

      // or
      // 使用这种方式代替...
      MyComponent.someFunction = someFunction;
      export default MyComponent;

      // ...单独导出该方法...
      export { someFunction };

      // ...并在要使用的组件中，import 它们
      import MyComponent, { someFunction } from './MyComponent.js';
      ```

    **Refs 不会被传递**，可通过 `React.forwardRef` 传递

45. JSX：仅仅只是 `React.createElement(component, props, ...children)` 函数的语法糖

    ```jsx
    <MyButton color="blue" shadowSize={2}>
      Click Me
    </MyButton>

    =========
    React.createElement(
      MyButton,
      {color: 'blue', shadowSize: 2},
      'Click Me'
    )

    --------------------------------------------------------
    <div className="sidebar" />
    =========
    React.createElement(
      'div',
      {className: 'sidebar'}
    )
    ```

    JSX 点语法

    ```jsx
    import React from 'react';

    const MyComponents = {
      DatePicker: function DatePicker(props) {
        return <div>Imagine a {props.color} datepicker here.</div>;
      }
    }

    function BlueDatePicker() {
      return <MyComponents.DatePicker color="blue" />;
    }
    ```

46. 在运行时选择类型(动态组件)

    ```jsx
    import React from 'react';
    import { PhotoStory, VideoStory } from './stories';

    const components = {
      photo: PhotoStory,
      video: VideoStory
    };

    // wrong

    function Story(props) {
      // 错误！JSX 类型不能是一个表达式。
      return <components[props.storyType] story={props.story} />;
    }
    // right

    function Story(props) {
      // 正确！JSX 类型可以是大写字母开头的变量。
      const SpecificStory = components[props.storyType];
      return <SpecificStory story={props.story} />;
    }
    ```

47. 字符串字面量：

    ```jsx
    <MyComponent message="hello world" />
    // 等价于
    <MyComponent message={'hello world'} />

    <MyComponent message="&lt;3" />
    // 等价于
    <MyComponent message={'<3'} />
    ```

48. Props 默认值为 “True”：如果你没给 prop 赋值，它的默认值是 `true`。

    ```jsx
    <MyTextBox autocomplete /> // 不推荐
    // 等价于
    <MyTextBox autocomplete={true} /> // 推荐
    ```

49. 函数作为子元素：

    ```jsx
    // 调用子元素回调 numTimes 次，来重复生成组件
    function Repeat(props) {
      let items = [];
      for (let i = 0; i < props.numTimes; i++) {
        items.push(props.children(i));
      }
      return <div>{items}</div>;
    }

    function ListOfTenThings() {
      return (
        <Repeat numTimes={10}>
          {(index) => <div key={index}>This is item {index} in the list</div>}
        </Repeat>
      );
    }
    ```

50. 布尔类型、Null 以及 Undefined 将会忽略：

    ```jsx
    // 以下的 JSX 表达式渲染结果相同：
    <div />

    <div></div>

    <div>{false}</div>

    <div>{null}</div>

    <div>{undefined}</div>

    <div>{true}</div>
    ```

    值得注意的是有一些 [“falsy” 值](https://developer.mozilla.org/en-US/docs/Glossary/Falsy)，如数字 `0`，仍然会被 React 渲染。

51. Portals：提供了一种将子节点渲染到存在于父组件以外的 DOM 节点的优秀的方案。

    ```jsx
    ReactDOM.createPortal(child, container)
    // 第一个参数（child）是任何可渲染的 React 子元素，例如一个元素，字符串或 fragment。第二个参数（container）是一个 DOM 元素。
    ```

    ​一个 portal 的典型用例是当父组件有 `overflow: hidden` 或 `z-index` 样式时，但你需要子组件能够在视觉上“跳出”其容器。例如，对话框、悬浮卡以及提示框：

    ```jsx
    // index.html
    <html>
      <body>
        <div id="app-root"></div>
        <div id="modal-root"></div>
      </body>
    </html>

    // index.jsx
    // 在 DOM 中有两个容器是兄弟级 （siblings）
    const appRoot = document.getElementById('app-root');
    const modalRoot = document.getElementById('modal-root');

    class Modal extends React.Component {
      constructor(props) {
        super(props);
        this.el = document.createElement('div');
      }

      componentDidMount() {
        // 在 Modal 的所有子元素被挂载后，
        // 这个 portal 元素会被嵌入到 DOM 树中，
        // 这意味着子元素将被挂载到一个分离的 DOM 节点中。
        // 如果要求子组件在挂载时可以立刻接入 DOM 树，
        // 例如衡量一个 DOM 节点，
        // 或者在后代节点中使用 ‘autoFocus’，
        // 则需添加 state 到 Modal 中，
        // 仅当 Modal 被插入 DOM 树中才能渲染子元素。
        modalRoot.appendChild(this.el);
      }

      componentWillUnmount() {
        modalRoot.removeChild(this.el);
      }

      render() {
        return ReactDOM.createPortal(
          this.props.children,
          this.el
        );
      }
    }

    class Parent extends React.Component {
      constructor(props) {
        super(props);
        this.state = {clicks: 0};
        this.handleClick = this.handleClick.bind(this);
      }

      handleClick() {
        // 当子元素里的按钮被点击时，
        // 这个将会被触发更新父元素的 state，
        // 即使这个按钮在 DOM 中不是直接关联的后代
        this.setState(state => ({
          clicks: state.clicks + 1
        }));
      }

      render() {
        return (
          <div onClick={this.handleClick}>
            <p>Number of clicks: {this.state.clicks}</p>
            <p>
              Open up the browser DevTools
              to observe that the button
              is not a child of the div
              with the onClick handler.
            </p>
            <Modal>
              <Child />
            </Modal>
          </div>
        );
      }
    }

    function Child() {
      // 这个按钮的点击事件会冒泡到父元素
      // 因为这里没有定义 'onClick' 属性
      return (
        <div className="modal">
          <button>Click</button>
        </div>
      );
    }

    ReactDOM.render(<Parent />, appRoot);
    ```

52. Profiler API：测量渲染一个 React 应用多久渲染一次以及渲染一次的“代价”。 它的目的是识别出应用中渲染较慢的部分，或是可以使用[类似 memoization 优化](https://react.docschina.org/docs/hooks-faq.html#how-to-memoize-calculations)的部分，并从相关优化中获益。

    注意：Profiling 增加了额外的开支，所以**它在[生产构建](https://react.docschina.org/docs/optimizing-performance.html#use-the-production-build)中会被禁用**。

    用法：`Profiler` 能添加在 React 树中的任何地方来测量树中这部分渲染所带来的开销。 它需要两个 prop ：一个是 `id`(string)，一个是当组件树中的组件“提交”更新的时候被React调用的回调函数 `onRender`(function)。

    ```jsx
    // 分析 Navigation 组件和它的子代
    render(
    <App>
        <Profiler id="Navigation" onRender={callback}>
      <Navigation {...props} />
        </Profiler>
        <Main {...props} />
      </App>
    );

    // 多个 Profiler 组件能测量应用中的不同部分
    render(
      <App>
        <Profiler id="Navigation" onRender={callback}>
          <Navigation {...props} />
        </Profiler>
        <Profiler id="Main" onRender={callback}>
          <Main {...props} />
        </Profiler>
      </App>
    );

    // 嵌套使用 Profiler 组件来测量相同一个子树下的不同组件
    render(
      <App>
        <Profiler id="Panel" onRender={callback}>
          <Panel {...props}>
            <Profiler id="Content" onRender={callback}>
              <Content {...props} />
            </Profiler>
            <Profiler id="PreviewPane" onRender={callback}>
              <PreviewPane {...props} />
            </Profiler>
          </Panel>
        </Profiler>
      </App>
    );
    ```

53. mixins：混入，**数据覆盖，方法依次触发**

    ```jsx
    var SetIntervalMixin = {
      componentWillMount: function() {
        this.intervals = [];
      },
      setInterval: function() {
        this.intervals.push(setInterval.apply(null, arguments));
      },
      componentWillUnmount: function() {
        this.intervals.forEach(clearInterval);
      }
    };

    var createReactClass = require('create-react-class');

    var TickTock = createReactClass({
      mixins: [SetIntervalMixin], // 使用 mixin
      getInitialState: function() {
        return {seconds: 0};
      },
      componentDidMount: function() {
        this.setInterval(this.tick, 1000); // 调用 mixin 上的方法
      },
      tick: function() {
        this.setState({seconds: this.state.seconds + 1});
      },
      render: function() {
        return (
          <p>
            React has been running for {this.state.seconds} seconds.
          </p>
        );
      }
    });

    ReactDOM.render(
      <TickTock />,
      document.getElementById('example')
    );
    ```

    **使用 mixins 时，mixins 会先按照定义时的顺序执行，最后调用组件上对应的方法**

54. 不使用 JSX：

    ```jsx
    // React.createElement(component, props, ...children)
    // 使用 JSX
    class Hello extends React.Component {
      render() {
        return <div>Hello {this.props.toWhat}</div>;
      }
    }

    ReactDOM.render(
      <Hello toWhat="World" />,
      document.getElementById('root')
    );

    // 不使用 JSX
    class Hello extends React.Component {
      render() {
        return React.createElement('div', null, `Hello ${this.props.toWhat}`);
      }
    }

    ReactDOM.render(
      React.createElement(Hello, {toWhat: 'World'}, null),
      document.getElementById('root')
    );
    ```

55. 设计动力：在某一时间节点调用 React 的 `render()` 方法，会创建一棵由 React 元素组成的树。在下一次 state 或 props 更新时，相同的 `render()` 方法会返回一棵不同的树。React 需要基于这两棵树之间的差别来判断如何有效率的更新 UI 以保证当前 UI 与最新的树保持同步。

    ​这个算法问题有一些通用的解决方案，即生成将一棵树转换成另一棵树的最小操作数。 然而，即使在[最前沿的算法中](http://grfia.dlsi.ua.es/ml/algorithms/references/editsurvey_bille.pdf)，该算法的复杂程度为 O(n 3 )，其中 n 是树中元素的数量。
     O(n) 思考：

    1. 两个不同类型的元素会产生出不同的树；
    2. 开发者可以通过 `key` prop 来暗示哪些子元素在不同的渲染下能保持稳定；

    都成立

56. Diffing 算法：当对比两颗树时，React 首先比较两棵树的根节点。不同类型的根节点元素会有不同的形态。

    - ***比对不同类型的元素***

      当根节点为不同类型的元素时，React 会拆卸原有的树并且建立起新的树。

      当拆卸一棵树时，对应的 DOM 节点也会被销毁。组件实例将执行 `componentWillUnmount()` 方法。当建立一棵新的树时，对应的 DOM 节点会被创建以及插入到 DOM 中。组件实例将执行 `componentWillMount()` 方法，紧接着 `componentDidMount()` 方法。所有跟之前的树所关联的 state 也会被销毁。

      ```jsx
      // React 会销毁 Counter 组件并且重新装载一个新的组件
      <div>
        <Counter />
      </div>

      <span>
        <Counter />
      </span>
      ```

    - ***对比同一类型的元素***

        ​当对比两个相同类型的 React 元素时，React 会保留 DOM 节点，仅比对及更新有改变的属性。

        ```jsx
        // 只需要修改 DOM 元素上的 className 属性
        <div className="before" title="stuff" />

        <div className="after" title="stuff" />
        // 仅更新有所更变的属性
        <div style={{color: 'red', fontWeight: 'bold'}} />

        <div style={{color: 'green', fontWeight: 'bold'}} />
        // 只需要修改 DOM 元素上的 color 样式，无需修改 fontWeight。
        <div style={{color: 'red', fontWeight: 'bold'}} />

        <div style={{color: 'green', fontWeight: 'bold'}} />
        ```

    - ***对比同类型的组件元素***

        ​当一个组件更新时，组件实例保持不变，这样 state 在跨越不同的渲染时保持一致。React 将更新该组件实例的 props 来跟最新的元素保持一致，并且调用该实例的 `componentWillReceiveProps()` 和 `componentWillUpdate()` 方法。

        下一步，调用 `render()` 方法，diff 算法将在之前的结果以及新的结果中进行递归。

    - ***对子节点进行递归***

        ​在默认条件下，当递归 DOM 节点的子元素时，React 会同时遍历两个子元素的列表；当产生差异时，生成一个 mutation。
        ​在子元素列表末尾新增元素时，更新开销比较小。(只更新最后一个)

        ​简单的将新增元素插入到表头，更新开销比较大。(更新全部)

    - ***Keys(解决以上问题)***

        ​当子元素拥有 key 时，React 使用 key 来匹配原有树上的子元素以及最新树上的子元素。

        ```jsx
        // 这个 key 不需要全局唯一，但在列表中需要保持唯一
        <li key={item.id}>{item.name}</li>
        // 也可以使用元素在数组中的下标作为 key。这个策略在元素不进行重新排序时比较合适，如果有顺序修改，diff 就会变得慢。
        ```

        注意：当基于下标的组件进行重新排序时，组件 state 可能会遇到一些问题。由于组件实例是基于它们的 key 来决定是否更新以及复用，如果 key 是一个下标，那么修改顺序时会修改当前的 key，导致非受控组件的 state（比如输入框）可能相互篡改导致无法预期的变动。

    注意：

    1. 该算法不会尝试匹配不同组件类型的子树。如果你发现你在两种不同类型的组件中切换，但输出非常相似的内容，建议把它们改成同一类型。在实践中，我们没有遇到这类问题。
    2. Key 应该具有稳定，可预测，以及列表内唯一的特质。不稳定的 key（比如通过 `Math.random()` 生成的）会导致许多组件实例和 DOM 节点被不必要地重新创建，这可能导致性能下降和子组件中的状态丢失。

57. 何时使用 Refs：

    - 管理焦点，文本选择或媒体播放。
    - 触发强制动画。
    - 集成第三方 DOM 库。

    注意：避免使用 refs 来做任何可以通过声明式实现来完成的事情。

58. 创建 Refs：Refs 是使用 `React.createRef()` 创建的，并通过 `ref` 属性附加到 React 元素。在构造组件时，通常将 Refs 分配给实例属性，以便可以在整个组件中引用它们。

    ```jsx
    class MyComponent extends React.Component {
      constructor(props) {
        super(props);
        this.myRef = React.createRef();
      }
      render() {
        return <div ref={this.myRef} />;
      }
    }
    ```

59. 访问 Refs：当 ref 被传递给 `render` 中的元素时，对该节点的引用可以在 ref 的 `current` 属性中被访问。

    ```jsx
    const node = this.myRef.current;
    ```

    ref 的值根据节点的类型而有所不同：

    - 当 `ref` 属性用于 HTML 元素时，构造函数中使用 `React.createRef()` 创建的 `ref` 接收底层 DOM 元素作为其 `current` 属性。
    - 当 `ref` 属性用于自定义 class 组件时，`ref` 对象接收组件的挂载实例作为其 `current` 属性。
    - **你不能在函数组件上使用 `ref` 属性**，因为他们没有实例。

    例子：

    1. 为 DOM 元素添加 ref

       ```jsx
       class CustomTextInput extends React.Component {
         constructor(props) {
           super(props);
           // 创建一个 ref 来存储 textInput 的 DOM 元素
           this.textInput = React.createRef();
           this.focusTextInput = this.focusTextInput.bind(this);
         }

         focusTextInput() {
           // 直接使用原生 API 使 text 输入框获得焦点
           // 注意：我们通过 "current" 来访问 DOM 节点
           this.textInput.current.focus();
         }

         render() {
           // 告诉 React 我们想把 <input> ref 关联到
           // 构造器里创建的 `textInput` 上
           return (
             <div>
               <input
                 type="text"
                 ref={this.textInput} />
               <input
                 type="button"
                 value="Focus the text input"
                 onClick={this.focusTextInput}
               />
             </div>
           );
         }
       }
       ```

       React 会在组件挂载时给 `current` 属性传入 DOM 元素，并在组件卸载时传入 `null` 值。`ref` 会在 `componentDidMount` 或 `componentDidUpdate` 生命周期钩子触发前更新。

    2. 为 class 组件添加 Ref：仅在 `CustomTextInput` 声明为 class 时才有效！

       ```jsx
       class AutoFocusTextInput extends React.Component {
         constructor(props) {
           super(props);
           this.textInput = React.createRef();
         }

         componentDidMount() {
           this.textInput.current.focusTextInput();
         }

         render() {
           return (
             <CustomTextInput ref={this.textInput} />
           );
         }
       }
       ```

    3. Refs 与函数组件：

        默认情况下，**你不能在函数组件上使用 `ref` 属性**，因为它们没有实例。

        如果要在函数组件中使用 `ref`，你可以使用 [`forwardRef`](https://react.docschina.org/docs/forwarding-refs.html)（可与 [`useImperativeHandle`](https://react.docschina.org/docs/hooks-reference.html#useimperativehandle) 结合使用），或者可以将该组件转化为 class 组件。

        你可以**在函数组件内部使用 `ref` 属性**，只要它指向一个 DOM 元素或 class 组件：

        ```jsx
        function CustomTextInput(props) {
          // 这里必须声明 textInput，这样 ref 才可以引用它
          const textInput = useRef(null);

          function handleClick() {
            textInput.current.focus();
          }

          return (
            <div>
              <input
                type="text"
                ref={textInput} />
              <input
                type="button"
                value="Focus the text input"
                onClick={handleClick}
              />
            </div>
          );
        }
        ```

    4. 将 DOM Refs 暴露给父组件：**Ref 转发使组件可以像暴露自己的 ref 一样暴露子组件的 ref**。见前文- **Ref 转发**

60. 回调 Refs：

    ​React 也支持另一种设置 refs 的方式，称为“回调 refs”。它能助你更精细地控制何时 refs 被设置和解除。

    ​不同于传递 `createRef()` 创建的 `ref` 属性，你会传递一个函数。这个函数中接受 React 组件实例或 HTML DOM 元素作为参数，以使它们能在其他地方被存储和访问。

    范例：使用 `ref` 回调函数，在实例的属性中存储对 DOM 节点的引用。

    ```jsx
    class CustomTextInput extends React.Component {
      constructor(props) {
        super(props);

        this.textInput = null;

        this.setTextInputRef = element => {
          this.textInput = element;
        };

        this.focusTextInput = () => {
          // 使用原生 DOM API 使 text 输入框获得焦点
          if (this.textInput) this.textInput.focus();
        };
      }

      componentDidMount() {
        // 组件挂载后，让文本框自动获得焦点
        this.focusTextInput();
      }

      render() {
        // 使用 `ref` 的回调函数将 text 输入框 DOM 节点的引用存储到 React
        // 实例上（比如 this.textInput）
        return (
          <div>
            <input
              type="text"
              ref={this.setTextInputRef}
            />
            <input
              type="button"
              value="Focus the text input"
              onClick={this.focusTextInput}
            />
          </div>
        );
      }
    }
    ```

    React 将在组件挂载时，会调用 `ref` 回调函数并传入 DOM 元素，当卸载时调用它并传入 `null`。在 `componentDidMount` 或 `componentDidUpdate` 触发前，React 会保证 refs 一定是最新的。

    ```jsx
    function CustomTextInput(props) {
      return (
        <div>
          <input ref={props.inputRef} />
        </div>
      );
    }

    class Parent extends React.Component {
      render() {
        return (
          <CustomTextInput
            inputRef={el => this.inputElement = el}
          />
        );
      }
    }

    //Parent 把它的 refs 回调函数当作 inputRef props 传递给了 CustomTextInput，而且 CustomTextInput 把相同的函数作为特殊的 ref 属性传递给了 <input>。结果是，在 Parent 中的 this.inputElement 会被设置为与 CustomTextInput 中的 input 元素相对应的 DOM 节点。
    ```

61. this.refs：过时的API
    **注意：如果你目前还在使用 `this.refs.textInput` 这种方式访问 refs ，我们建议用[回调函数](https://react.docschina.org/docs/refs-and-the-dom.html#callback-refs)或 [`createRef` API](https://react.docschina.org/docs/refs-and-the-dom.html#creating-refs) 的方式代替。**

62. 关于回调 refs 的说明：如果 `ref` 回调函数是以内联函数的方式定义的，在更新过程中它会被执行两次，第一次传入参数 `null`，然后第二次会传入参数 DOM 元素。这是因为在每次渲染时会创建一个新的函数实例，所以 React 清空旧的 ref 并且设置新的。通过将 ref 的回调函数定义成 class 的绑定函数的方式可以避免上述问题，但是大多数情况下它是无关紧要的。

63. Render Props：指一种在 React 组件之间使用一个值为函数的 prop 共享代码的简单技术

    ​具有 render prop 的组件接受一个函数，该函数返回一个 React 元素并调用它而不是实现自己的渲染逻辑。

    ```jsx
    <DataProvider render={data => (
      <h1>Hello {data.target}</h1>
    )}/>
    ```

    使用 render prop 的库有 [React Router](https://reacttraining.com/react-router/web/api/Route/render-func)、[Downshift](https://github.com/paypal/downshift) 以及 [Formik](https://github.com/jaredpalmer/formik)。

64. 使用 Render Props 来解决横切关注点（Cross-Cutting Concerns）：

    ```jsx
    class Cat extends React.Component {
      render() {
        const mouse = this.props.mouse;
        return (
          <img src="/cat.jpg" style={{ position: 'absolute', left: mouse.x, top: mouse.y }} />
        );
      }
    }

    class Mouse extends React.Component {
      constructor(props) {
        super(props);
        this.handleMouseMove = this.handleMouseMove.bind(this);
        this.state = { x: 0, y: 0 };
      }

      handleMouseMove(event) {
        this.setState({
          x: event.clientX,
          y: event.clientY
        });
      }

      render() {
        return (
          <div style={{ height: '100vh' }} onMouseMove={this.handleMouseMove}>

            {/*
              Instead of providing a static representation of what <Mouse> renders,
              use the `render` prop to dynamically determine what to render.
            */}
            {this.props.render(this.state)}
          </div>
        );
      }
    }

    class MouseTracker extends React.Component {
      render() {
        return (
          <div>
            <h1>移动鼠标!</h1>
            <Mouse render={mouse => (
              <Cat mouse={mouse} />
            )}/>
          </div>
        );
      }
    }
    ```

    我们提供了一个 `render` 方法 让 `<Mouse>` 能够动态决定什么需要渲染，而不是克隆 `<Mouse>` 组件然后硬编码来解决特定的用例。
    **render prop 是一个用于告知组件需要渲染什么内容的函数 prop。**

    ```jsx
    // 如果你出于某种原因真的想要 HOC，那么你可以轻松实现
    // 使用具有 render prop 的普通组件创建一个！
    function withMouse(Component) {
      return class extends React.Component {
        render() {
          return (
            <Mouse render={mouse => (
              <Component {...this.props} mouse={mouse} />
            )}/>
          );
        }
      }
    }
    ```

    [*任何*被用于告知组件需要渲染什么内容的函数 prop 在技术上都可以被称为 “render prop”](https://cdb.reacttraining.com/use-a-render-prop-50de598f11ce)

    尽管之前的例子使用了 `render`，我们也可以简单地使用 `children` prop！

    ```jsx
    <Mouse children={mouse => (
      <p>鼠标的位置是 {mouse.x}，{mouse.y}</p>
    )}/>

    // 记住，children prop 并不真正需要添加到 JSX 元素的 “attributes” 列表中。相反，你可以直接放置到元素的内部！
    <Mouse>
      {mouse => (
        <p>鼠标的位置是 {mouse.x}，{mouse.y}</p>
      )}
    </Mouse>

    // 由于这一技术的特殊性，当你在设计一个类似的 API 时，你或许会要直接地在你的 propTypes 里声明 children 的类型应为一个函数。
    Mouse.propTypes = {
      children: PropTypes.func.isRequired
    };
    ```

65. 将 Render Props 与 React.PureComponent 一起使用时要小心：

    ​如果你在 render 方法里创建函数，那么使用 render prop 会抵消使用 [`React.PureComponent`](https://react.docschina.org/docs/react-api.html#reactpurecomponent) 带来的优势。因为浅比较 props 的时候总会得到 false，并且在这种情况下每一个 `render` 对于 render prop 将会生成一个新的值。

    ```jsx
    class Mouse extends React.PureComponent {
      // 与上面相同的代码......
    }

    class MouseTracker extends React.Component {
      render() {
        return (
          <div>
            <h1>Move the mouse around!</h1>

            {/*
              这是不好的！
              每个渲染的 `render` prop的值将会是不同的。
            */}
            <Mouse render={mouse => (
              <Cat mouse={mouse} />
            )}/>
          </div>
        );
      }
    }

    ```

    为了绕过这一问题，有时你可以定义一个 prop 作为实例方法，类似这样：

    ```jsx
    class MouseTracker extends React.Component {
      // 定义为实例方法，`this.renderTheCat`始终
      // 当我们在渲染中使用它时，它指的是相同的函数
      renderTheCat(mouse) {
        return <Cat mouse={mouse} />;
      }

      render() {
        return (
          <div>
            <h1>Move the mouse around!</h1>
            <Mouse render={this.renderTheCat} />
          </div>
        );
      }
    }
    ```

    如果你无法静态定义 prop（例如，因为你需要关闭组件的 props 和/或 state），则 `<Mouse>` 应该扩展 `React.Component`。

66. 严格模式：`StrictMode` 是一个用来突出显示应用程序中潜在问题的工具。与 `Fragment` 一样，`StrictMode` 不会渲染任何可见的 UI。它为其后代元素触发额外的检查和警告。
    **注意：严格模式检查仅在开发模式下运行；*它们不会影响生产构建*。**
    你可以为应用程序的任何部分启用严格模式：

    ```jsx
    import React from 'react';

    function ExampleApplication() {
      return (
        <div>
          <Header />
          <React.StrictMode>
            <div>
              <ComponentOne />
              <ComponentTwo />
            </div>
          </React.StrictMode>
          <Footer />
        </div>
      );
    }
    ```

    在上述的示例中，*不*会对 `Header` 和 `Footer` 组件运行严格模式检查。但是，`ComponentOne` 和 `ComponentTwo` 以及它们的所有后代元素都将进行检查。

    `StrictMode` 目前有助于：

    - [识别不安全的生命周期](https://react.docschina.org/docs/strict-mode.html#identifying-unsafe-lifecycles)

    - [关于使用过时字符串 ref API 的警告](https://react.docschina.org/docs/strict-mode.html#warning-about-legacy-string-ref-api-usage)

      ​this.refs => createRef | 回调 ref

    - [关于使用废弃的 findDOMNode 方法的警告](https://react.docschina.org/docs/strict-mode.html#warning-about-deprecated-finddomnode-usage)

      ​`findDOMNode` 也可用于 class 组件，但它违反了抽象原则，它使得父组件需要单独渲染子组件。它会产生重构危险，你不能更改组件的实现细节，因为父组件可能正在访问它的 DOM 节点。`findDOMNode` 只返回第一个子节点，但是使用 Fragments，组件可以渲染多个 DOM 节点。`findDOMNode` 是一个只读一次的 API。调用该方法只会返回第一次查询的结果。如果子组件渲染了不同的节点，则无法跟踪此更改。因此，`findDOMNode` 仅在组件返回单个且不可变的 DOM 节点时才有效。

      ​你也可以在组件中创建一个 DOM 节点的 wrapper，并将 ref 直接绑定到它。

      ```jsx
      class MyComponent extends React.Component {
        constructor(props) {
          super(props);
          this.wrapper = React.createRef();
        }
        render() {
          return <div ref={this.wrapper}>{this.props.children}</div>;
        }
      }
      ```

      注意：在 CSS 中，如果你不希望节点成为布局的一部分，则可以使用 [`display: contents`](https://developer.mozilla.org/en-US/docs/Web/CSS/display#display_contents) 属性。

      `display: contents`：不将自身渲染到页面(可访问树)上，只保留子元素

    - [检测意外的副作用](https://react.docschina.org/docs/strict-mode.html#detecting-unexpected-side-effects)

      从概念上讲，React 分两个阶段工作：

      - **渲染** 阶段会确定需要进行哪些更改，比如 DOM。在此阶段，React 调用 `render`，然后将结果与上次渲染的结果进行比较。
      - **提交** 阶段发生在当 React 应用变化时。（对于 React DOM 来说，会发生在 React 插入，更新及删除 DOM 节点的时候。）在此阶段，React 还会调用 `componentDidMount` 和 `componentDidUpdate` 之类的生命周期方法。

      渲染阶段的生命周期包括以下 class 组件方法：

      - `constructor`

      - `componentWillMount` (or `UNSAFE_componentWillMount`)

      - `componentWillReceiveProps` (or `UNSAFE_componentWillReceiveProps`)

      - `componentWillUpdate` (or `UNSAFE_componentWillUpdate`)

      - `getDerivedStateFromProps`

      - `shouldComponentUpdate`

      - `render`

      - `setState` 更新函数（第一个参数）

        因为上述方法可能会被多次调用，所以不要在它们内部编写副作用相关的代码，这点非常重要。忽略此规则可能会导致各种问题的产生，包括内存泄漏和或出现无效的应用程序状态。不幸的是，这些问题很难被发现，因为它们通常具有[非确定性](https://en.wikipedia.org/wiki/Deterministic_algorithm)。

    - [检测过时的 context API](https://react.docschina.org/docs/strict-mode.html#detecting-legacy-context-api)

67. PropTypes：

    ```jsx
    import PropTypes from 'prop-types';

    MyComponent.propTypes = {
      // 你可以将属性声明为 JS 原生类型，默认情况下
      // 这些属性都是可选的。
      optionalArray: PropTypes.array,
      optionalBool: PropTypes.bool,
      optionalFunc: PropTypes.func,
      optionalNumber: PropTypes.number,
      optionalObject: PropTypes.object,
      optionalString: PropTypes.string,
      optionalSymbol: PropTypes.symbol,

      // 任何可被渲染的元素（包括数字、字符串、元素或数组）
      // (或 Fragment) 也包含这些类型。
      optionalNode: PropTypes.node,

      // 一个 React 元素。
      optionalElement: PropTypes.element,

      // 一个 React 元素类型（即，MyComponent）。
      optionalElementType: PropTypes.elementType,

      // 你也可以声明 prop 为类的实例，这里使用
      // JS 的 instanceof 操作符。
      optionalMessage: PropTypes.instanceOf(Message),

      // 你可以让你的 prop 只能是特定的值，指定它为
      // 枚举类型。
      optionalEnum: PropTypes.oneOf(['News', 'Photos']),

      // 一个对象可以是几种类型中的任意一个类型
      optionalUnion: PropTypes.oneOfType([
        PropTypes.string,
        PropTypes.number,
        PropTypes.instanceOf(Message)
      ]),

      // 可以指定一个数组由某一类型的元素组成
      optionalArrayOf: PropTypes.arrayOf(PropTypes.number),

      // 可以指定一个对象由某一类型的值组成
      optionalObjectOf: PropTypes.objectOf(PropTypes.number),

      // 可以指定一个对象由特定的类型值组成
      optionalObjectWithShape: PropTypes.shape({
        color: PropTypes.string,
        fontSize: PropTypes.number
      }),

      // An object with warnings on extra properties
      optionalObjectWithStrictShape: PropTypes.exact({
        name: PropTypes.string,
        quantity: PropTypes.number
      }),

      // 你可以在任何 PropTypes 属性后面加上 `isRequired` ，确保
      // 这个 prop 没有被提供时，会打印警告信息。
      requiredFunc: PropTypes.func.isRequired,

      // 任意类型的数据
      requiredAny: PropTypes.any.isRequired,

      // 你可以指定一个自定义验证器。它在验证失败时应返回一个 Error 对象。
      // 请不要使用 `console.warn` 或抛出异常，因为这在 `onOfType` 中不会起作用。
      customProp: function(props, propName, componentName) {
        if (!/matchme/.test(props[propName])) {
          return new Error(
            'Invalid prop `' + propName + '` supplied to' +
            ' `' + componentName + '`. Validation failed.'
          );
        }
      },

      // 你也可以提供一个自定义的 `arrayOf` 或 `objectOf` 验证器。
      // 它应该在验证失败时返回一个 Error 对象。
      // 验证器将验证数组或对象中的每个值。验证器的前两个参数
      // 第一个是数组或对象本身
      // 第二个是他们当前的键。
      customArrayProp: PropTypes.arrayOf(function(propValue, key, componentName, location, propFullName) {
        if (!/matchme/.test(propValue[key])) {
          return new Error(
            'Invalid prop `' + propFullName + '` supplied to' +
            ' `' + componentName + '`. Validation failed.'
          );
        }
      })
    };
    ```

68. 默认 Prop 值：
    您可以通过配置特定的 `defaultProps` 属性来定义 `props` 的默认值：

    ```jsx
    class Greeting extends React.Component {
      render() {
        return (
          <h1>Hello, {this.props.name}</h1>
        );
      }
    }

    // 指定 props 的默认值：
    Greeting.defaultProps = {
      name: 'Stranger'
    };

    // 渲染出 "Hello, Stranger"：
    ReactDOM.render(
      <Greeting />,
      document.getElementById('example')
    );
    ```

    如果你正在使用像 [transform-class-properties](https://babeljs.io/docs/plugins/transform-class-properties/) 的 Babel 转换工具，你也可以在 React 组件类中声明 `defaultProps` 作为静态属性。未确定语法，暂不推荐使用。

    ```jsx
    class Greeting extends React.Component {
      static defaultProps = {
        name: 'stranger'
      }

      render() {
        return (
          <div>Hello, {this.props.name}</div>
        )
      }
    }
    ```

    `defaultProps` 用于确保 `this.props.name` 在父组件没有指定其值时，有一个默认值。`propTypes` 类型检查发生在 `defaultProps` 赋值后，所以类型检查也适用于 `defaultProps`。

69. 非受控组件：

    ```jsx
    class NameForm extends React.Component {
      constructor(props) {
        super(props);
        this.handleSubmit = this.handleSubmit.bind(this);
        this.input = React.createRef();
      }

      handleSubmit(event) {
        alert('A name was submitted: ' + this.input.current.value);
        event.preventDefault();
      }

      render() {
        return (
          <form onSubmit={this.handleSubmit}>
            <label>
              Name:
              <input type="text" ref={this.input} />
            </label>
            <input type="submit" value="Submit" />
          </form>
        );
      }
    }
    ```

    默认值：在 React 渲染生命周期时，表单元素上的 `value` 将会覆盖 DOM 节点中的值，在非受控组件中，你经常希望 React 能赋予组件一个初始值，但是不去控制后续的更新。 在这种情况下, 你可以指定一个 `defaultValue` 属性，而不是 `value`。

    ```jsx
    render() {
      return (
        <form onSubmit={this.handleSubmit}>
          <label>
            Name:
            <input
              defaultValue="Bob"
              type="text"
              ref={this.input} />
          </label>
          <input type="submit" value="Submit" />
        </form>
      );
    }
    ```

    同样，`<input type="checkbox">` 和 `<input type="radio">` 支持 `defaultChecked`，`<select>` 和 `<textarea>` 支持 `defaultValue`。

    文件输入：在 React 中，`<input type="file" />` 始终是一个非受控组件，因为它的值只能由用户设置，而不能通过代码控制。

70. React 顶层 API：

    - `React.Component`

    - `React.PureComponent`：`React.PureComponent` 与 [`React.Component`](https://react.docschina.org/docs/react-api.html#reactcomponent) 很相似。两者的区别在于 [`React.Component`](https://react.docschina.org/docs/react-api.html#reactcomponent) 并未实现 [`shouldComponentUpdate()`](https://react.docschina.org/docs/react-component.html#shouldcomponentupdate)，而 `React.PureComponent` 中以浅层对比 prop 和 state 的方式来实现了该函数。

      ​如果赋予 React 组件相同的 props 和 state，`render()` 函数会渲染相同的内容，那么在某些情况下使用 `React.PureComponent` 可提高性能。

      ​注意：`React.PureComponent` 中的 `shouldComponentUpdate()` 仅作对象的浅层比较。如果对象中包含复杂的数据结构，则有可能因为无法检查深层的差别，产生错误的比对结果。仅在你的 props 和 state 较为简单时，才使用 `React.PureComponent`，或者在深层数据结构发生变化时调用 [`forceUpdate()`](https://react.docschina.org/docs/react-component.html#forceupdate) 来确保组件被正确地更新。你也可以考虑使用 [immutable 对象](https://facebook.github.io/immutable-js/)加速嵌套数据的比较。

      ​此外，`React.PureComponent` 中的 `shouldComponentUpdate()` 将跳过所有子组件树的 prop 更新。因此，请确保所有子组件也都是“纯”的组件。

    - `React.memo`：性能优化

      ```react
      const MyComponent = React.memo(function MyComponent(props) {
        /* 使用 props 渲染 */
      });
      ```

      `React.memo` 为[高阶组件](https://react.docschina.org/docs/higher-order-components.html)。它与 [`React.PureComponent`](https://react.docschina.org/docs/react-api.html#reactpurecomponent) 非常相似，但只适用于函数组件，而不适用 class 组件。

      ​如果你的函数组件在给定相同 props 的情况下渲染相同的结果，那么你可以通过将其包装在 `React.memo` 中调用，以此通过记忆组件渲染结果的方式来提高组件的性能表现。这意味着在这种情况下，React 将跳过渲染组件的操作并直接复用最近一次渲染的结果。

      ​`React.memo` 仅检查 props 变更。如果函数组件被 `React.memo` 包裹，且其实现中拥有 [`useState`](https://react.docschina.org/docs/hooks-state.html) 或 [`useContext`](https://react.docschina.org/docs/hooks-reference.html#usecontext) 的 Hook，当 context 发生变化时，它仍会重新渲染。

      ​默认情况下其只会对复杂对象做浅层对比，如果你想要控制对比过程，那么请将自定义的比较函数通过第二个参数传入来实现。

      ```jsx
      function MyComponent(props) {
        /* 使用 props 渲染 */
      }
      function areEqual(prevProps, nextProps) {
        /*
        如果把 nextProps 传入 render 方法的返回结果与
        将 prevProps 传入 render 方法的返回结果一致则返回 true，
        否则返回 false
        */
      }
      export default React.memo(MyComponent, areEqual);
      ```

      **注意：与 class 组件中 [`shouldComponentUpdate()`](https://react.docschina.org/docs/react-component.html#shouldcomponentupdate) 方法不同的是，如果 props 相等，`areEqual` 会返回 `true`；如果 props 不相等，则返回 `false`。这与 `shouldComponentUpdate` 方法的返回值相反。**

    - `createElement()`

    - `cloneElement()`

      ```jsx
      React.cloneElement(
        element,
        [props],
        [...children]
      )

      // 等同于
      <element.type {...element.props} {...props}>{children}</element.type>
      ```

      以 `element` 元素为样板克隆并返回新的 React 元素。返回元素的 props 是将新的 props 与原始元素的 props 浅层合并后的结果。新的子元素将取代现有的子元素，而来自原始元素的 `key` 和 `ref` 将被保留。

    - `isValidElement()`：验证对象是否为 React 元素，返回值为 `true` 或 `false`。

      ```react
      React.isValidElement(object)
      ```

    - `React.Children`：提供了用于处理 `this.props.children` 不透明数据结构的实用方法。

      - `React.Children.map(children, function[(thisArg)])`：在 `children` 里的每个直接子节点上调用一个函数，并将 `this` 设置为 `thisArg`。如果 `children` 是一个数组，它将被遍历并为数组中的每个子节点调用该函数。如果子节点为 `null` 或是 `undefined`，则此方法将返回 `null` 或是 `undefined`，而不会返回数组。

        ​**注意：如果 `children` 是一个 `Fragment` 对象，它将被视为单一子节点的情况处理，而不会被遍历。**

      - `React.Children.forEach(children, function[(thisArg)])`：与 [`React.Children.map()`](https://react.docschina.org/docs/react-api.html#reactchildrenmap) 类似，但它不会返回一个数组。

      - `React.Children.count(children)`：返回 `children` 中的组件总数量，等同于通过 `map` 或 `forEach` 调用回调函数的次数。

      - `React.Children.only(children)`：验证 `children` 是否只有一个子节点（一个 React 元素），如果有则返回它，否则此方法会抛出错误。

        ​**注意：`React.Children.only()` 不接受 [`React.Children.map()`](https://react.docschina.org/docs/react-api.html#reactchildrenmap) 的返回值，因为它是一个数组而并不是 React 元素。**

      - `React.Children.toArray(children)`：将 `children` 这个复杂的数据结构以数组的方式扁平展开并返回，并为每个子节点分配一个 key。当你想要在渲染函数中操作子节点的集合时，它会非常实用，特别是当你想要在向下传递 `this.props.children` 之前对内容重新排序或获取子集时。

        ​**注意：`React.Children.toArray()` 在拉平展开子节点列表时，更改 key 值以保留嵌套数组的语义。也就是说，`toArray` 会为返回数组中的每个 key 添加前缀，以使得每个元素 key 的范围都限定在此函数入参数组的对象内。**

    - `React.Fragment`

    - `React.createRef`

    - `React.forwardRef`：`React.forwardRef` 会创建一个React组件，这个组件能够将其接受的 [ref](https://react.docschina.org/docs/refs-and-the-dom.html) 属性转发到其组件树下的另一个组件中。这种技术并不常见，但在以下两种场景中特别有用：

      - [转发 refs 到 DOM 组件](https://react.docschina.org/docs/forwarding-refs.html#forwarding-refs-to-dom-components)
      - [在高阶组件中转发 refs](https://react.docschina.org/docs/forwarding-refs.html#forwarding-refs-in-higher-order-components)

      `React.forwardRef` 接受渲染函数作为参数。React 将使用 `props` 和 `ref` 作为参数来调用此函数。此函数应返回 React 节点。

      ```jsx
      const FancyButton = React.forwardRef((props, ref) => (
        <button ref={ref} className="FancyButton">
          {props.children}
        </button>
      ));

      // You can now get a ref directly to the DOM button:
      const ref = React.createRef();
      <FancyButton ref={ref}>Click me!</FancyButton>;
      ```

    - `React.lazy`：允许你定义一个动态加载的组件。这有助于缩减 bundle 的体积，并延迟加载在初次渲染时未用到的组件。

      ```jsx
      // 这个组件是动态加载的
      const SomeComponent = React.lazy(() => import('./SomeComponent'));
      ```

      注意：渲染 `lazy` 组件依赖该组件渲染树上层的 `<React.Suspense>` 组件。这是指定加载指示器（loading indicator）的方式。

    - `React.Suspense`： 可以指定加载指示器（loading indicator），以防其组件树中的某些子组件尚未具备渲染条件。目前，懒加载组件是 `<React.Suspense>` 支持的**唯一**用例：

      ```jsx
      // 该组件是动态加载的
      const OtherComponent = React.lazy(() => import('./OtherComponent'));

      function MyComponent() {
        return (
          // 显示 <Spinner> 组件直至 OtherComponent 加载完成
          <React.Suspense fallback={<Spinner />}>
            <div>
              <OtherComponent />
            </div>
          </React.Suspense>
        );
      }
      ```

71. React.Component 生命周期

    - 挂载

      当组件实例被创建并插入 DOM 中时，其生命周期调用顺序如下：

      - [**`constructor()`**](https://react.docschina.org/docs/react-component.html#constructor)
      - [`static getDerivedStateFromProps()`](https://react.docschina.org/docs/react-component.html#static-getderivedstatefromprops)
      - [**`render()`**](https://react.docschina.org/docs/react-component.html#render)
      - [**`componentDidMount()`**](https://react.docschina.org/docs/react-component.html#componentdidmount)

    - 更新

      当组件的 props 或 state 发生变化时会触发更新。组件更新的生命周期调用顺序如下：

      - [`static getDerivedStateFromProps()`](https://react.docschina.org/docs/react-component.html#static-getderivedstatefromprops)
      - [`shouldComponentUpdate()`](https://react.docschina.org/docs/react-component.html#shouldcomponentupdate)
      - [**`render()`**](https://react.docschina.org/docs/react-component.html#render)
      - [`getSnapshotBeforeUpdate()`](https://react.docschina.org/docs/react-component.html#getsnapshotbeforeupdate)
      - [**`componentDidUpdate()`**](https://react.docschina.org/docs/react-component.html#componentdidupdate)

    - 卸载

      当组件从 DOM 中移除时会调用如下方法：

      - [**`componentWillUnmount()`**](https://react.docschina.org/docs/react-component.html#componentwillunmount)

    - 错误处理

      当渲染过程，生命周期，或子组件的构造函数中抛出错误时，会调用如下方法：

      - [`static getDerivedStateFromError()`](https://react.docschina.org/docs/react-component.html#static-getderivedstatefromerror)
      - [`componentDidCatch()`](https://react.docschina.org/docs/react-component.html#componentdidcatch)

    - 详细：

      - `render()`：是 class 组件中唯一必须实现的方法。

        ​`render()` 函数应该为纯函数，这意味着在不修改组件 state 的情况下，每次调用时都返回相同的结果，并且它不会直接与浏览器交互。

        ​如需与浏览器进行交互，请在 `componentDidMount()` 或其他生命周期方法中执行你的操作。保持 `render()` 为纯函数，可以使组件更容易思考。
        ​**注意：如果 `shouldComponentUpdate()` 返回 false，则不会调用 `render()`。**

      - `constructor(props)`：**如果不初始化 state 或不进行方法绑定，则不需要为 React 组件实现构造函数。**

        在 React 组件挂载之前，会调用它的构造函数。在为 React.Component 子类实现构造函数时，应在其他语句之前前调用 super(props)。否则，this.props 在构造函数中可能会出现未定义的 bug。

        通常，在 React 中，构造函数仅用于以下两种情况：
        - 通过给 this.state 赋值对象来初始化内部 state。
        - 为事件处理函数绑定实例

        在 constructor() 函数中不要调用 setState() 方法。如果你的组件需要使用内部 state，请直接在构造函数中为 this.state 赋值初始 state：

        ```jsx
        constructor(props) {
          super(props);
          // 不要在这里调用 this.setState()
          this.state = { counter: 0 };
          this.handleClick = this.handleClick.bind(this);
        }
        ```

        只能在构造函数中直接为 `this.state` 赋值。如需在其他方法中赋值，你应使用 `this.setState()` 替代。

        要避免在构造函数中引入任何副作用或订阅。如遇到此场景，请将对应的操作放置在 `componentDidMount` 中。

        注意⚠️

        ```jsx
        constructor(props) {
          super(props);
          // 不要这样做
          this.state = { color: props.color };
        }
        ```

        直接使用 `this.props.color`，以上做法会产生 `bug` (更新 `prop` 中的 `color` 时，并不会影响 `state`)。

        ***只有在你刻意忽略 `prop` 更新的情况下使用***。此时，应将 `prop` 重命名为 `initialColor` 或 `defaultColor`。必要时，你可以修改它的 `key`，以强制“重置”其内部 `state`。

      - `componentDidMount()`: 会在组件挂载后（插入 DOM 树中）立即调用。依赖于 DOM 节点的初始化应该放在这里。如需通过网络请求获取数据，此处是实例化请求的好地方。

        这个方法是比较适合添加订阅的地方。如果添加了订阅，请不要忘记在 `componentWillUnmount()` 里取消订阅

        你可以在 `componentDidMount()` 里直接调用 `setState()`。**它将触发额外渲染，但此渲染会发生在浏览器更新屏幕之前。**如此保证了即使在 `render()` 两次调用的情况下，用户也不会看到中间状态。**请谨慎使用该模式，因为它会导致性能问题。**通常，你应该在 `constructor()` 中初始化 `state`。***如果你的渲染依赖于 DOM 节点的大小或位置，比如实现 modals 和 tooltips 等情况下，你可以使用此方式处理***
      - `componentDidUpdate(prevProps, prevState, snapshot)`: 会在更新后会被立即调用。首次渲染不会执行此方法。

        当组件更新后，可以在此处对 DOM 进行操作。如果你对更新前后的 props 进行了比较，也可以选择在此处进行网络请求。（例如，当 props 未发生变化时，则不会执行网络请求）。

        ```javascript
        componentDidUpdate(prevProps) {
          // 典型用法（不要忘记比较 props）:
          if (this.props.userID !== prevProps.userID) {
            this.fetchData(this.props.userID);
          }
        }
        ```

        你也可以在 `componentDidUpdate()` 中直接调用 `setState()`，但请注意它必须被包裹在一个条件语句里，正如上述的例子那样进行处理，否则会导致死循环。它还会导致额外的重新渲染，虽然用户不可见，但会影响组件性能。不要将 `props` “镜像”给 `state`，请考虑直接使用 `props`。

        如果组件实现了 `getSnapshotBeforeUpdate()` 生命周期（不常用），则它的返回值将作为 `componentDidUpdate()` 的第三个参数 “`snapshot`” 参数传递。否则此参数将为 `undefined`。

        注意⚠️：如果 `shouldComponentUpdate()` 返回值为 `false`，则不会调用 `componentDidUpdate()`。
      - `componentWillUnmount()`: 会在组件卸载及销毁之前直接调用。在此方法中执行必要的清理操作，例如，清除 timer，取消网络请求或清除在 `componentDidMount()` 中创建的订阅等。

        `componentWillUnmount()` 中不应调用 `setState()`，因为该组件将永远不会重新渲染。组件实例卸载后，将永远不会再挂载它。
      - `shouldComponentUpdate(nextProps, nextState)`: 根据其返回值，判断 React 组件的输出是否受当前 `state` 或 `props` 更改的影响。默认行为是 `state` 每次发生变化组件都会重新渲染。大部分情况下，你应该遵循默认行为。

        当 `props` 或 `state` 发生变化时，`shouldComponentUpdate()` 会在渲染执行之前被调用。返回值默认为 `true`。首次渲染或使用 `forceUpdate()` 时不会调用该方法。

        此方法仅作为性能优化的方式而存在。不要企图依靠此方法来“阻止”渲染，因为这可能会产生 `bug`。你应该考虑使用内置的 `PureComponent` 组件，而不是手动编写 `shouldComponentUpdate()`。`PureComponent` 会对 `props` 和 `state` 进行浅层比较，并减少了跳过必要更新的可能性。

        如果你一定要手动编写此函数，可以将 `this.props` 与 `nextProps` 以及 `this.state` 与 `nextState` 进行比较，并返回 `false` 以告知 React 可以跳过更新。请注意，返回 `false` 并不会阻止子组件在 `state` 更改时重新渲染。

        我们不建议在 `shouldComponentUpdate()` 中进行深层比较或使用 `JSON.stringify()`。这样非常影响效率，且会损害性能。

        目前，如果 `shouldComponentUpdate()` 返回 `false`，则不会调用 `UNSAFE_componentWillUpdate()`，`render()` 和 `componentDidUpdate()`。***后续版本，React 可能会将 `shouldComponentUpdate` 视为提示而不是严格的指令，并且，当返回 `false` 时，仍可能导致组件重新渲染。***
      - 

72. 其它 API

73. s

74. s

75. s

76. s

77. s

78. s

79. s

80. s

81. s

82. s

83. s
