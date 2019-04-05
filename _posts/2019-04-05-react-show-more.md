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

{% highlight javascript linenos %}
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
{% endhighlight %}

`App.js`에서 버튼을 클릭하면 10개의 아이템을 추가해주는 코드가 있습니다.

이 때, `Item`컴포넌트의 구현에 따라 앱의 성능이 좌우될 것입니다.

## Component with shouldComponentUpdate

{% highlight javascript linenos %}
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
{% endhilight %}

`Component`로 구현할시에 성능을 잡는 가장 쉬운 방법은 `shouldComponentUpdate (이하, SCU)`를 사용하는 것입니다.
SCU가 `return`하는 값에 따라 해당 컴포넌트의 리렌더링 여부가 결정됩니다.
따라서, `props`의 변경이 이루어졌을 경우만 `true`를 return 시키도록 합니다.

위 코드를 실행시키면 버튼을 클릭할 때 마다, 'render'가 **10번**씩 찍히는 것을 확인할 수 있습니다. 

## PureComponent

{% highlight javascript linenos %}
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
{% endhighlight %}

`PureComponent`를 사용하면 `SCU`를 작성하지 않아도 됩니다. PureComponent가 `staet`, `props`가 변경됐을 경우만 rerender되도록 만들어진 컴포넌트이기 때문입니다. 그래서 `SCU`가 있는 `Component`와 마찬가지로 'render' 콘솔이 **10번씩** 찍힙니다.

## 함수형 컴포넌트

{% highlight javascript linenos %}
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
{% endhighlight %}

가장 간단하게 컴포넌트를 만드는 방법은 함수형 컴포넌트일 것이다. 하지만 함수형 컴포넌트는 **state**나 **라이프사이클 API**가 **전혀 사용되지 않을 때**, 또는 해당 컴포넌트는 자체 기능이 없고 **props가 들어가면 뷰가 나오는 경우**에만 사용됩니다.
따라서 위 경우는 버튼 클릭시마다 'render'가 **리스트의 개수만큼** 출력된다.
즉,
- 처음: 10번
- 버튼 클릭: 20번 렌더링 (20개의 아이템)
- 버튼 클릭: 30번 렌더링 (30개의 아이템)
- ...
이 된다.

---

이제 각 컴포넌트의 특징을 알았으니 리스트 더보기 기능 구현시에는 **성능**을 꼭 잡아주세요!
