# The Third Phrase Summary

## 第十五周

### 英语

-   How would you optimize it? 你如何优化它？
-   What does this function xxx do? xxx 函数做了什么？
-   x made of xxxx. x 由 xxx 组成
-   What is the time complexity of this function? 这个函数的时间复杂度是多少？
-   nested loop statement. 嵌套语句
-   iterates over all items. 迭代每个元素
-   combination complexity. 组合复杂度
-   space complexity. 空间复杂度

### 技术

#### [Improve a function](https://bigfrontend.dev/problem/improve-a-function)

```js
// i planed use Map store excludeMap optimize find way.

function excludeItems(items, excludes) {
    const excludeMap = new Map();
  // construct item: {k ,v}
    excludes.forEach(({ k, v }) => {
      // excludeMap if has current k, if not exist, we should set, value is set array.
        if (!excludeMap.has(k)) {
            excludeMap.set(k, new Set([v]);
        } else {
          // if present, we should get current k to push value.
            excludeMap.get(k).add(v);
        }
    });

    return items.filter(item => {
      // we use key values pairs array to construct excludeMap item
      // please note, here,

      // Why we use array to construct excludeMap item?
      // because new Map iterator return a [key, value] pairs array.
        for (let [key, values] of excludeMap) {
          // If the attribute value of the current item is in the excluded values array,
          // the item should be excluded, return false
            if (values.includes(item[key])) {
                return false;
            }
        }
        // If the current item does not meet any exclusion conditions,
        // it should be retained, return true.
        return true;
    });
}
```

## 第十六周

### 英语

-   return an empty array immediately. 直接返回空数组
-   Initial Check. 初始化检查
-   Traverse the Queue. 遍历队列
-   When the queue is empty. 当队列为空时

### 技术

#### [CreateELement](https://bigfrontend.dev/problem/virtual-dom-II-createElement)

```js
function createElement(type, props, ...children) {
    return {
        type,
        props: {
            ...props,
            children
        }
    };
}

// input is myElement, output is HTMLElement
function render(myElement) {
    // textNode
    if (typeof myElement === 'string') {
        return document.createTextNode(myElement);
    }

    const {
        type,
        props: { children, ...attrs }
    } = myElement;
    // element
    const element = document.createElement(type);

    // props
    for (let attr in attrs) {
        if (attrs.hasOwnProperty(attr)) {
            element.setAttribute(attr === 'className' ? 'class' : attr, attrs[attr]);
        }
    }

    // children
    if (!Array.isArray(children)) {
        children = [children];
    }
    children.forEach(child => {
        // recursive append children
        element.append(render(child));
    });

    return element;
}
```

#### [Traverse DOM level by level](https://bigfrontend.dev/problem/Traverse-DOM-level-by-level)

```js
function flatten(root) {
    // If the root is null, return an empty array
    // 如果根节点是 null，返回空数组
    if (root === null) return [];

    // Initialize the queue with the root node
    // 用根节点初始化队列
    const queue = [root];
    // Initialize the result array
    // 初始化结果数组
    const result = [];

    // While there are nodes in the queue
    // 当队列中还有节点时
    while (queue.length > 0) {
        // Get the first node from the queue
        // 从队列中取出第一个节点
        const head = queue.shift();
        // Add the node to the result array
        // 将节点加入结果数组
        result.push(head);
        // Add all children of the node to the queue
        // 将节点的所有子节点加入队列
        queue.push(...head.children);
    }

    // Return the result array
    // 返回结果数组
    return result;
}
```

## 第十七周

### 英语

-   according xx we know. 根据 xx 我们得知
-   by the following code. 根据以上代码
-   now that we have a clear topic. 现在我们明确了题目
-   For the call method. 对于 call 方法来说
-   we use bind first, they apply. 我们先用 bind, 后 apply
-   Their xxx is the same, but there is one difference. 他们 xxx 基本相同，但是有一个不同点
-   Relative to xxx we know. 相对于 xxx 我们得知

### 技术

#### [Partial](https://bigfrontend.dev/typescript/implement-Partial-T)

```ts
type MyPartial<T> = { [key in keyof T]?: T[key] };
```

#### [Required](https://bigfrontend.dev/typescript/implement-Required-T)

```ts
type MyRequired<T> = { [key in keyof T]-?: T[key] };
```

#### [Composition](https://bigfrontend.dev/problem/what-is-composition-create-a-pipe)

```js
/**
 * @param {Array<(arg: any) => any>} funcs
 * @return {(arg: any) => any}
 */
// compatibility multiple arguments
function pipe(funcs) {
    return function (...arg) {
        if (funcs.length === 0) {
            return args.length === 1 ? args[0] : args;
        }
        return funcs.reduce((result, method) => {
            if (Array.isArray(result)) {
                return method(...result);
            } else {
                return method(result);
            }
        }, arg);
    };
}

// compatibility single arguments
function pipe(funcs) {
    return function (arg) {
        return funcs.reduce((result, method) => {
            return method(result);
        }, arg);
    };
}
```

#### [promisify](https://bigfrontend.dev/problem/promisify)

```js
/**
 * @param {(...args) => void} func
 * @returns {(...args) => Promise<any}
 */

// call
function promisify(func) {
    return function (...args) {
        return new Promise((resolve, reject) => {
            func.call(this, ...args, (error, data) => {
                if (error) {
                    reject(error);
                } else {
                    resolve(data);
                }
            });
        });
    };
}

// apply
function promisify(func) {
    return function (...args) {
        return new Promise((resolve, reject) => {
            func.apply(this, [
                ...args,
                (err, res) => {
                    if (err) {
                        reject(err);
                    } else {
                        resolve(res);
                    }
                }
            ]);
        });
    };
}

// bind
function promisify(func) {
    return function (...args) {
        return new Promise((resolve, reject) => {
            const bindFunc = func.bind(this, ...args, (err, res) => {
                if (err) {
                    reject(err);
                } else {
                    resolve(res);
                }
            });
            bindFunc();
        });
    };
}
```

## 第十八周

### 英语

-   two-dimensional array. 二维数组
-   Start from the xxx. 从 xxx 开始
-   we need to change the direction to the opposite. 我们需要将方向改变到相反的方向
-   My approach is xxx. 我们思路是 xxx
-

### 技术

#### [decode-message-new](https://bigfrontend.dev/problem/decode-message)

```js
/**
 * @param {string[][]} message
 * @return {string}
 */
function decode(message) {
    if (!message || !message.length) {
        return '';
    }
    const rowLen = message.length;
    const colLen = message[0].length;
    let row = 0,
        col = 0;
    let result = '';
    let direction = 1;
    while (col < colLen) {
        result += message[row][col];
        row += direction;
        col++;
        if (row >= rowLen - 1 || row <= 0) {
            direction = -direction;
        }
    }
    return result;
}
```

#### [memo](https://bigfrontend.dev/problem/implement-general-memoization-function)

```js
/**
 * @param {Function} func
 * @param {(args:[]) => string }  [resolver] - cache key generator
 */
function memo(func, resolver) {
    const cache = new Map();
    return function (...args) {
        if (!resolver) {
            resolver = (...args) => args.join('_');
        }

        const key = resolver(...args);
        if (cache.has(key)) {
            return cache.get(key);
        }

        const result = func.call(this, ...args);
        cache.set(key, result);

        return result;
    };
}
```

## 第十九周

### 英语

-   By following these steps. 通过以上步骤
-   we have completed the function. 我们已经完成了函数
-   The question requires us to xxx. 题目要求我们 xxx
-   find the maximum difference between any two elements in a numbers array. 在一个整数数组中找两个值的最大差
-   define default maximum and minimum values. 定义最大值和最小值
-   xxx for the same reason. xxx 相同的原因
-   traverse the numbers array. 遍历数字数组

### 技术

#### [Detect data type](https://bigfrontend.dev/problem/detect-data-type-in-JavaScript)

```js
/**
 * @param {any} data
 * @return {string}
 */
function detectType(data) {
    return Object.prototype.toString.call(data).slice(8, -1).toLowerCase();
}
```

#### [this](https://bigfrontend.dev/quiz/this)

```js
1;
1;
1;
undefined;
2;
1;
undefined;
undefined;
undefined;
undefined;
undefined;
undefined;
1;
1;
1;
```

#### [Find the largest difference](https://bigfrontend.dev/problem/Find-the-largest-difference)

```js
/**
 * @param {number[]} arr
 * @return {number}
 */
function largestDiff(arr) {
    if (arr.length < 2) return 0;
    let max = 0;
    for (let i = 0; i < arr.length; i++) {
        for (let j = i + 1; j < arr.length; j++) {
            max = Math.max(max, Math.abs(arr[i] - arr[j]));
        }
    }
    return max;
}

function largestDiff(arr) {
    if (arr.length < 2) return 0;
    let min = Infinity;
    let max = -Infinity;

    for (let i = 0; i < arr.length; i++) {
        min = Math.min(arr[i], min);
        max = Math.max(arr[i], max);
    }

    return Math.abs(min - max);
}
```

#### [implement-once](https://bigfrontend.dev/problem/implement-once)

```js
/**
 * @param {Function} func
 * @return {Function}
 */
function once(func) {
    let flag = false;
    let result = '';
    return function (...arg) {
        if (!flag) {
            flag = true;
            result = func.apply(this, arg);
            return result;
        } else {
            return result;
        }
    };
}
```

## 第二十周

### 英语

-   To achieve [goal], we will [method or approach]. 为了实现[目标]，我们将[方法或途径]。
-   The function takes [input] and returns [output]. 该函数接收[输入]并返回[输出]。
-   We start by [initial action], followed by [subsequent action]. 我们首先[初始行动]，然后进行[后续行动]。
-   For each [element or item], we [action to be performed]. 对于每个[元素或项目]，我们执行[要执行的动作]。
-   This is similar to [a known concept or method]. 这类似于[已知的概念或方法]。
-   In order to [desired outcome], we must [necessary action]. 为了[期望的结果]，我们必须[必要的行动]。
-   By using [specific method or tool], we ensure that [desired result or benefit]. 通过使用[特定的方法或工具]，我们确保了[期望的结果或好处]
-   iterate over the queue. 遍历整个队列
-   it defines a [method or available] that calls the [method or interface] to return [output or result]. 定义一个[方法或可用的]，调用[方法或接口]来返回[输出或结果]
-   With each iteration. 每次迭代

### 技术

#### [get-Dom-tags](https://bigfrontend.dev/problem/get-Dom-tags)

```js
/**
 * @param {HTMLElement} tree
 * @return {string[]}
 */
function getTags(tree) {
    const queue = [tree];
    const tags = new Set();
    while (queue.length) {
        const node = queue.shift();
        tags.add(node.tagName.toLowerCase());
        queue.push(...node.children);
    }
    return Array.from(tags);
}
```

#### [lit-html 1 - tagged templates](https://bigfrontend.dev/problem/lit-html-1-tagged-templates)

```js
function html(string, ...values) {
    return string.reduce((acc, cur, index) => {
        return acc + cur + (values[index] || '');
    }, '');
}

// render the result from html() into the container
function render(result, container) {
    container.innerHTML = result;
}
```

#### [create an Event Emitter](https://bigfrontend.dev/problem/create-an-event-emitter)

```js
// please complete the implementation
class EventEmitter {
    constructor() {
        this.subscriptions = new Map();
    }
    subscribe(eventName, callback) {
        if (!this.subscriptions.has(eventName)) {
            this.subscriptions.set(eventName, [callback]);
        } else {
            this.subscriptions.set(eventName, [...this.subscriptions.get(eventName), callback]);
        }
        return {
            release: () => {
                this.subscriptions.get(eventName).forEach((item, index) => {
                    if (item === callback) {
                        this.subscriptions.get(eventName).splice(index, 1);
                    }
                });
                if (this.subscriptions.get(eventName).size === 0) {
                    delete this.subscriptions.eventName;
                }
            }
        };
    }

    emit(eventName, ...args) {
        const subscribeItem = this.subscriptions.get(eventName);
        subscribeItem.forEach(callback => {
            callback.call(this, ...args);
        });
    }
}
```

#### [implement range()](https://bigfrontend.dev/problem/implement-range)

```js
/**
 * @param {integer} from
 * @param {integer} to
 */
function range(from, to) {
    const result = [];
    for (let i = from; i <= to; i++) {
        result.push(i);
    }
    return result;
}

function range(from, to) {
    return Array.from({ length: to - from + 1 }, (_, i) => from + i);
}

// 2. implement iterable/iterator protocol
// for ... of uses interable protocol
// [Symbol.iterator]: () =>  Iterator
// next: () => {done: bolean, value?: any}
function range(from, to) {
    return {
        // iterable protocol
        [Symbol.iterator]() {
            // iterator protocol
            return {
                next() {
                    return {
                        done: from > to,
                        value: from++
                    };
                }
            };
        }
    };
}
```
