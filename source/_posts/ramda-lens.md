---
title: ramda lens 理解和使用
date: 2019-02-28 14:18:34
tags: "functional"
---

Lens 最先诞生于 Haskell。它是函数式 getter 和 setter，用来处理对复杂数据集的操作。

Ramda 中的 lens 接受一个 "getter" 函数和一个 "setter" 函数，然后返回一对象。 可以类比，Object.defineProperty()中的set, get方法，功能上几乎一致。也可以想想 vue 中对computed的值写的set, get方法。

现在我们来解读官网中的lens例子。

```javascript
//R.prop 为get函数， R.assoc为set函数
const xLens = R.lens(R.prop('x'), R.assoc('x'));
//R.view lens的配套对象，用来返回lens定义的数据
R.view(xLens, {x: 1, y: 2});            //=> 1
//R.set lens的配套对象，通过 lens 对数据结构聚焦的部分进行设置。
//并返回一个新对象
R.set(xLens, 4, {x: 1, y: 2});          //=> {x: 4, y: 2}
//R.over lens的配套对象, 对数据结构中被 lens 聚焦的部分进行函数变换。
R.over(xLens, R.negate, {x: 1, y: 2});  //=> {x: -1, y: 2}
```

上面是lens的基本用法。但其实在更普遍的情况下，lensProp, lensPath的使用频率会更高。

```javascript
const person = {
  name: 'Randy',
  socialMedia: {
    github: 'randycoulman',
    twitter: '@randycoulman'
  }
}
 //不使用lensProp和lensPath，对象取值
const nameLens = lens(prop('name'), assoc('name'))
const twitterLens = lens(
 	path(['socialMedia', 'twitter']),
  assocPath(['socialMedia', 'twitter'])

//使用lensProp和lensPath
const nameLens = lensProp('name')
const twitterLens = lensPath(['socialMedia', 'twitter'])

//根据路径设置对象的值，并返回一个新对象
R.assocPath(['a', 'b', 'c'], 42, {a: {b: {c: 0}}}); //=> {a: {b: {c: 42}}}
```


其中对我而言，目前比较有用的是lensPath, 可以避免嵌套取值的校验问题，之前用的解决方案是 facebook的 idx. 显然这个也是一个不错的方案。

## 看起来更有用的lens例子
以下例子，来自参考链接中的第3个。
整个程序要做的事情是，是将 华氏温度 转成 摄氏温度。 

```javascript
const temperature = { fahrenheit: 68 }
//华 -> 摄
const far2cel = far => (far - 32) * (5 / 9)
//摄 -> 华
const cel2far = cel => (cel * 9) / 5 + 32

const fahrenheit = lensProp('fahrenheit')

const lcelsius = lens(far2cel, cel2far)
const celsius = compose(
  fahrenheit,
  lcelsius
)
view(celsius, temperature) // 20
set(celsius, 20, temperature) //{ fahrenheit: 68 }
```

lcelsius,  get时把华氏温度作为返回摄氏度。set时把摄氏度转成华氏温度。
这样就隐藏了数据转换的过程。

## 参考
1. https://ramdajs.com/docs/
2. [Thinking in Ramda: 透镜（Lenses） - 知乎](https://zhuanlan.zhihu.com/p/27780023)
3. https://lambda.academy/functional-lenses/