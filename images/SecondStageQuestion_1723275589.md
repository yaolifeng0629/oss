## Question Logic

### [Improve a function](https://bigfrontend.dev/problem/improve-a-function)

```js
/**
 * 1. 根据 exclude 数组的 key，value 键值对过滤 item
 * 2. 不能，因为存在错误
 * 3. O(m * n) m: exclude.length n: item.length
 * 4. 使用 hashMap
 */

function excludeItems(items, excludes) {
    const excludeMap = new Map();

    excludes.forEach(({ k, v }) => {
        if (!excludeMap.get(k)) {
            excludeMap.set(k, new Set([v]));
        } else {
            excludeMap.get(k).add(v);
        }
    });

    return items.filter(item => {
        for (let [key, values] of excludeMap) {
            if (values.has(item[key])) {
                return false;
            }
        }
        return true;
    });
}
```

### [CreateELement](https://bigfrontend.dev/problem/virtual-dom-II-createElement)

```js
/**
 * 1. createElement 返回一个标准的 VM DOM 结构
 * 2. 在 render 方法中创建真实的 DOM
 * 3. render 方法
 *  1. 解构 myElement，获取 type, props, children
 *  2. 创建根节点 root
 *  3. 处理 props，因为 props 是一个对象，使用 for...in 来遍历（但注意 for...in 遍历的对象属性会包含自身属性和继承的属性，因此，这里我们使用 hasOwnProperty 方法来判断，只处理对象自己的属性）
 *  4. 处理 children，首先需要将 children 转换为数组，然后递归处理它的子元素。
 *  5. 最后因为子节点有可能是一个文本节点，因此需要添加一个额外的边缘case，判断 myElement 是否是一个字符串，如果是字符串则创建一个文本节点。
 *  6. 最后，返回根节点即可。
 */

/**
 * MyElement is the type your implementation supports
 *
 * type MyNode = MyElement | string
 */

/**
 * @param { string } type - valid HTML tag name
 * @param { object } [props] - properties.
 * @param { ...MyNode} [children] - elements as rest arguments
 * @return { MyElement }
 */
function createElement(type, props, ...children) {
    // your code here
    return {
        type,
        props: {
            ...props,
            children,
        },
    };
}

/**
 * @param { MyElement }
 * @returns { HTMLElement }
 */
function render(myElement) {
    if (typeof myElement === 'string') {
        return document.createTextNode(myElement);
    }
    const {
        type,
        props: { children, ...attrs },
    } = myElement;

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

    children.forEach(itemNode => {
        element.append(render(itemNode));
    });

    return element;
}
```

### [Traverse DOM level by level](https://bigfrontend.dev/problem/Traverse-DOM-level-by-level)

```js
/**
 * 1. 就是一个 BFS, BFS 使用队列数据结构，遵循先出先进(FIFO)原则
 * 2. 这里使用 shift 是为了实现广度优先搜索(breadth-first search)的遍历顺序，从上到下，从左到右。使用 shift 来确保我们总是处理当前层级最早加入到队列的节点，然后再移动到下一层。
 *   -  如果这里我使用 pop，我们会得到 DFS 的结果，这并不是题目所要求我们的。
 * 3. 子元素的处理，因为 node.children 返回一个一个 HTML 的集合，包含所有的子元素，所以我们使用扩展运算符将子元素元素展开并添加到队列中。
 */

/**
 * @param {HTMLElement | null} root
 * @return {HTMLElement[]}
 */
function flatten(root) {
    if (!root) return [];

    let queue = [root];
    let result = [];

    while (queue.length) {
        let node = queue.shift();
        result.push(node);
        queue.push(...node.children);
    }

    return result;
}
```

### [Partial](https://bigfrontend.dev/typescript/implement-Partial-T)

```js
type MyPartial<T> = {[key in keyof T] ?: T[key]};
```

### [Required](https://bigfrontend.dev/typescript/implement-Required-T)

```js
type MyRequired<T> = {
  [key in keyof T] -?: T[key];
}

// js
const foo = {
    a: '',
    b: '',
    c: ''
};

const MyPartial = {};

for(let key in foo){
    if(foo.hasOwnProperty(key)){
        MyPartial[key] = foo[key];
    }
}

console.log(MyPartial);
// {
//     a: '',
//     b: '',
//     c: ''
// };
```

### [what is Composition? create a pipe()](https://bigfrontend.dev/problem/what-is-composition-create-a-pipe)

```js
/***
 * pipe 函数创建一个函数通道，将多个函数串联起来，使得一个函数的输出成为下一个函数的输入。
 */

/**
 * @param {Array<(arg: any) => any>} funcs
 * @return {(arg: any) => any}
 */
function pipe(funcs) {
    return function (...args) {
        // 如果没有函数，直接返回输入
        if (funcs.length === 0) {
            // 如果只有一个参数，返回该参数，否则返回原参数。
            return args.length === 1 ? args[0] : args;
        }
        return funcs.reduce((result, method) => {
            if (Array.isArray(result)) {
                return method(...result);
            } else {
                return method(result);
            }
        }, args);
    };
}
```

### [promisify](https://bigfrontend.dev/problem/promisify)

```js
/**
 * @param {(...args) => void} func
 * @returns {(...args) => Promise<any}
 */
function promisify(func) {
    return function (...args) {
        return new Promise((resolve, reject) => {
            // call
            func.call(this, ...args, (err, data) => {
                if (err) {
                    reject(err);
                } else {
                    resolve(data);
                }
            });

            // apply
            func.apply(this, [
                ...args,
                (err, data) => {
                    if (err) {
                        reject(err);
                    } else {
                        resolve(data);
                    }
                },
            ]);

            // bind
            func.bind(this, ...args, (err, data) => {
                if (err) {
                    reject(err);
                } else {
                    resolve(data);
                }
            })();
        });
    };
}
```

### [decode-message-new](https://bigfrontend.dev/problem/decode-message)

```js
/**
 * 1. 获取行，列的长度，行是 message.length, 列是 message[0].length
 * 2. 初始化 row, col 的起始位置 0
 * 3. 初始化一个方向，因为这代表着下一步是往上还是往下。
 * 4. 这里我们使用一个队列来遍历，那它的判断条件就是找到列的最后字符，那就是 col 要小于 col 的长度
 * 5. 对于行来说，我们需要判断查找的方向，往上还是往下，所以它应该是根据 direction 来查找的。对于列来说，每次都是递增的状态，我们只需要改动行的方向即可。
 * 6. 最后，我们需要判断查找方向，向下：当不能继续往下时，也就是 row < rowLen - 1 时代表着已经到最后的，那我们就需要改变方向。往上：当 row 等于 0 时，也就是说明已经到头了，应该要往下了。
 * 7. 最后返回结果。
 */

/**
 * @param {string[][]} message
 * @return {string}
 */
function decode(message) {
    if (!message.length) return '';

    let rowLen = message.length;
    let colLen = message[0].length;

    let row = 0,
        col = 0;
    let direction = 1;
    let result = '';

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

### [memo](https://bigfrontend.dev/problem/implement-general-memoization-function)

```js
/**
 * 这道题的关键在于，cacheKey，也就是说 cacheKey 相同，就返回之前缓存的结果，否则计算。
 */

/**
 * @param {Function} func
 * @param {(args:[]) => string }  [resolver] - cache key generator
 */
function memo(func, resolver) {
    let resultMap = new Map();
    return function (...args) {
        if (!resolver) {
            resolver = (...args) => args.join('_');
        }
        const key = resolver(...args);
        if (resultMap.has(key)) {
            return resultMap.get(key);
        }
        let result = func.call(this, ...args);
        resultMap.set(key, result);
        return result;
    };
}
```

### [Detect data type](https://bigfrontend.dev/problem/detect-data-type-in-JavaScript)

```js
/**
 * Object.prototype.toString.call() 的优点：
 *  准确性：能够准确的识别所有的原生 JavaScrip 类型，包括基本类型和复杂类型。
 *  一致性：对于所有的类型都会返回格式一致的字符串 [object Type]
 *  不可篡改：即使对象的 toString 方法被改写，这个方法依然能够正常工作。
 *  适用范围广：可以检测内置对象和自定义对象。
 *
 * typeof
 *  优点：简单，快速。
    缺点：无法区分数组和普通对象（都返回 'object'），对 null 返回 'object'，无法识别许多内置对象类型。

   instanceof 运算符
    优点：可以检查对象是否属于特定的类或构造函数。
    缺点：需要分别检查每种类型，无法一次性识别所有类型。对原始类型不起作用。

    constructor 属性
    优点：可以识别大多数类型，包括自定义类型。
    缺点：如果对象的 constructor 属性被修改，会导致错误结果。对 null 和 undefined 无效。
 */

/**
 * @param {any} data
 * @return {string}
 */
function detectType(data) {
    return Object.prototype.toString.call(data).slice(8, -1).toLowerCase();
}
```

### [Find the largest difference](https://bigfrontend.dev/problem/Find-the-largest-difference)

```js
/**
 * @param {number[]} arr
 * @return {number}
 */
function largestDiff(arr) {
    if (arr.length < 2) return 0;

    let min = Infinity;
    let max = -Infinity;

    for (let i = 0; i < arr.length; i++) {
        min = Math.min(min, arr[i]);
        max = Math.max(max, arr[i]);
    }

    return Math.abs(min - max);
}
```

### [implement-once](https://bigfrontend.dev/problem/implement-once)

```js
/**
 * @param {Function} func
 * @return {Function}
 */
function once(func) {
    let flag = false;
    let result = null;
    return function (...arg) {
        if (flag) {
            return result;
        }
        result = func.call(this, ...arg);
        flag = true;
        return result;
    };
}
```

### [get-Dom-tags](https://bigfrontend.dev/problem/get-Dom-tags)

```js
/**
 * @param {HTMLElement} tree
 * @return {string[]}
 */
function getTags(tree) {
    let queue = [tree];
    let result = new Set();
    while (queue.length) {
        let node = queue.shift();
        result.add(node.tagName.toLowerCase());
        queue.push(...node.children);
    }

    return Array.from(result);
}
```

### [lit-html 1 - tagged templates](https://bigfrontend.dev/problem/lit-html-1-tagged-templates)

```js
// 通用解法和最佳实践：安全性增强方法
function escapeHTML(str) {
    return str.replace(
        /[&<>'"]/g,
        tag =>
            ({
                '&': '&amp;',
                '<': '&lt;',
                '>': '&gt;',
                "'": '&#39;',
                '"': '&quot;',
            }[tag] || tag)
    );
}

function html(strings, ...values) {
    return strings.reduce((result, str, i) => result + str + escapeHTML(values[i] || ''), '');
}

// 性能优化：对于大量重复渲染的情况，考虑使用 DocumentFragment
function render(result, container) {
    const fragment = document.createDocumentFragment();
    const temp = document.createElement('div');
    temp.innerHTML = result;
    while (temp.firstChild) {
        fragment.appendChild(temp.firstChild);
    }
    container.appendChild(fragment);
}

// 常用写法：
function html(string, ...values) {
    return string.reduce((prev, next, index) => {
        return prev + next + (values[index] || '');
    }, '');
}

// render the result from html() into the container
function render(result, container) {
    container.innerHTML = result;
}
```

### [create an Event Emitter](https://bigfrontend.dev/problem/create-an-event-emitter)

```js
class EventEmitter {
    constructor() {
        this.subEvents = new Map();
    }
    subscribe(eventName, callback) {
        if (!this.subEvents.has(eventName)) {
            this.subEvents.set(eventName, [callback]);
        } else {
            this.subEvents.get(eventName).push(callback);
        }
        return {
            release: () => {
                this.subEvents.get(eventName).forEach((item, index) => {
                    if (item === callback) {
                        this.subEvents.get(eventName).splice(index, 1);
                    }
                });

                if (this.subEvents.get(eventName).size === 0) {
                    this.subEvents.delete(eventName);
                }
            },
        };
    }

    emit(eventName, ...args) {
        this.subEvents.get(eventName).forEach(callback => {
            callback.call(this, ...args);
        });
    }
}
```

### [implement range()](https://bigfrontend.dev/problem/implement-range)

```js
/**
 * @param {integer} from
 * @param {integer} to
 */
function range(from, to) {
    return Array.from({ length: to - from + 1 }, (_, i) => from + i);
}
```
