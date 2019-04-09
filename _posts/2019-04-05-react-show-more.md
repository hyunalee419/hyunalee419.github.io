---
layout: post
title: React Show More
categories: [React]
tags: [React, more]
comments: true
---
해당 페이지는 `Component', 'PureComponent`, `Functional Component`에 대해서 **rerender** 관련해서 정리하고 있습니다.
React에서 더보기 기능을 구현할 때 기존에 보여지고 있던 리스트는 다시 렌더링을 하면 안됩니다. 각 컴포넌트로 구현했을 시에 render 수가 어떻게 되는지 예제를 통해 이해해봅시다. 

## 예제

```javascript
import React, { Component } from 'react';
import Item from './components/Item';

let index = 0;

class App extends Component {
  state = {
    items: []
  }

  componentDidMount() {
    this.addList();
  }

  addList = () => {
    let items = [];
    for ( let i = 0; i < 10; i++ ) {
      items.push({
        idx: index * 10 + i,
        name: `hello` + i
      });
    }
    this.setState({ items: [ ...this.state.items, ...items ]});
    index++;
  }

  render() {
    const { items } = this.state;
    return (
      <div>
        <button onClick={this.addList}>add items</button>
        <ul>
          {items && items.map((item, i) => <Item key={i} {...item} />)}
        </ul>
      </div>
    );
  }
}

export default App;
```

`App.js`에서 버튼을 클릭하면 10개의 아이템을 추가해주는 코드가 있습니다.

이 때, `Item`컴포넌트의 구현에 따라 앱의 성능이 좌우될 것입니다.

## Component with shouldComponentUpdate

```javascript
import React from 'react';

export default class Item extends React.Component {
	shouldComponentUpdate(nextProps) {
		return JSON.stringify(this.props) !== JSON.stringify(nextProps)
	}

	render() {
		const { idx, name } = this.props;
		console.log('redner') // TODO: render 확인 후 제거 
		return (
			<li>인덱스: {idx} / 이름: {name}</li>
		);
	}
}
```

`Component`로 구현할시에 성능을 잡는 가장 쉬운 방법은 `shouldComponentUpdate (이하, SCU)`를 사용하는 것입니다.
SCU가 `return`하는 값에 따라 해당 컴포넌트의 리렌더링 여부가 결정됩니다.
따라서, `props`의 변경이 이루어졌을 경우만 `true`를 return 시키도록 합니다.

위 코드를 실행시키면 버튼을 클릭할 때 마다, 'render'가 **10번**씩 찍히는 것을 확인할 수 있습니다. 

## PureComponent

```javascript
import React from 'react';

export default class Item extends React.PureComponent {
	render() {
		const { idx, name } = this.props;
		console.log('redner') // TODO: render 확인 후 제거 
		return (
			<li>인덱스: {idx} / 이름: {name}</li>
		);
	}
}
```

`PureComponent`를 사용하면 `SCU`를 작성하지 않아도 됩니다. PureComponent가 `staet`, `props`가 변경됐을 경우만 rerender되도록 만들어진 컴포넌트이기 때문입니다. 그래서 `SCU`가 있는 `Component`와 마찬가지로 'render' 콘솔이 **10번씩** 찍힙니다.

## 함수형 컴포넌트

```javascript
import React from 'react';

const Item = ({ idx, name }) => {
	console.log('render')
	return (
		<div>
			<p>인덱스: {idx} / 이름: {name}</p>
		</div>
	);
}

export default Item;
```

가장 간단하게 컴포넌트를 만드는 방법은 함수형 컴포넌트일 것이다. 하지만 함수형 컴포넌트는 **state**나 **라이프사이클 API**가 **전혀 사용되지 않을 때**, 또는 해당 컴포넌트는 자체 기능이 없고 **props가 들어가면 뷰가 나오는 경우**에만 사용됩니다.
따라서 위 경우는 버튼 클릭시마다 'render'가 **리스트의 개수만큼** 출력된다.
즉,
- 처음: 10번
- 버튼 클릭: 20번 렌더링 (20개의 아이템)
- 버튼 클릭: 30번 렌더링 (30개의 아이템)
- ...
이 된다.

### [React.memo](https://ko.reactjs.org/docs/react-api.html#reactmemo)
함수형 컴포넌트를 사용할 때도 렌더링 성능을 잡을 수 있다. 바로 `React.memo`를 사용하면 된다.
공식 문서에서 설명하길 React.memo는 아래와 같다.

> React.memo는 고차 컴포넌트(Higher Order Component)입니다. React.PureComponent와 비슷하지만 class가 아니라 함수 컴포넌트라는 점이 다릅니다.

그렇다면 위 예제에서 적용은 어떻게 하면될까?
아주 쉽다. 아래처럼 `export` 전에 `React.memo`만 작성해주면 끝난다.

```javascript
import React from 'react';

const Item = ({ idx, name }) => {
	console.log('render')
	return (
		<div>
			<p>인덱스: {idx} / 이름: {name}</p>
		</div>
	);
}

export default React.memo(Item);
```

> **주의**
>
> React.memo는 오직 **성능 최적화**를 위해 사용되며, 두번째 파라미터로 `shouldComponentUpdate()`와 비슷한 함수를 전달할 수 있는데 이 함수는 `shouldComponentUpdate` 와 정반대의 동작이므로 주의해야 한다. 

---

이제 각 컴포넌트의 특징을 알았으니 리스트 더보기 기능 구현시에는 **성능**을 꼭 잡아주세요!
