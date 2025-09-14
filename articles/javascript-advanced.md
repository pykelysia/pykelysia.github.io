# JavaScript高级技巧

探索JavaScript的高级特性，掌握这些技巧让你成为更优秀的前端开发者。

## 闭包 (Closures)

闭包是JavaScript最强大的特性之一：

```javascript
function createCounter() {
  let count = 0;
  
  return function() {
    return ++count;
  };
}

const counter = createCounter();
console.log(counter()); // 1
console.log(counter()); // 2
```

### 实际应用场景

1. **模块模式**
```javascript
const myModule = (function() {
  let privateVar = 0;
  
  return {
    increment() {
      privateVar++;
    },
    getCount() {
      return privateVar;
    }
  };
})();
```

2. **防抖函数**
```javascript
function debounce(func, wait) {
  let timeout;
  return function executedFunction(...args) {
    const later = () => {
      clearTimeout(timeout);
      func(...args);
    };
    clearTimeout(timeout);
    timeout = setTimeout(later, wait);
  };
}
```

## 原型链 (Prototype Chain)

理解原型链是掌握JavaScript面向对象的关键：

```javascript
function Person(name) {
  this.name = name;
}

Person.prototype.greet = function() {
  return `Hello, I'm ${this.name}`;
};

const john = new Person('John');
console.log(john.greet()); // "Hello, I'm John"
```

### 继承实现

```javascript
function Student(name, grade) {
  Person.call(this, name);
  this.grade = grade;
}

Student.prototype = Object.create(Person.prototype);
Student.prototype.constructor = Student;

Student.prototype.study = function() {
  return `${this.name} is studying in grade ${this.grade}`;
};
```

## 异步编程

### Promise链式调用

```javascript
function fetchUserData(userId) {
  return fetch(`/api/users/${userId}`)
    .then(response => response.json())
    .then(user => {
      return fetch(`/api/posts?userId=${user.id}`);
    })
    .then(response => response.json())
    .catch(error => {
      console.error('Error:', error);
      throw error;
    });
}
```

### Async/Await

```javascript
async function getUserPosts(userId) {
  try {
    const userResponse = await fetch(`/api/users/${userId}`);
    const user = await userResponse.json();
    
    const postsResponse = await fetch(`/api/posts?userId=${user.id}`);
    const posts = await postsResponse.json();
    
    return { user, posts };
  } catch (error) {
    console.error('Error fetching user posts:', error);
    throw error;
  }
}
```

## 高级数组方法

### 函数式编程

```javascript
const numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];

// 链式调用
const result = numbers
  .filter(n => n % 2 === 0)    // 过滤偶数
  .map(n => n * n)             // 平方
  .reduce((sum, n) => sum + n, 0); // 求和

console.log(result); // 220
```

### 自定义reduce实现

```javascript
Array.prototype.myReduce = function(callback, initialValue) {
  let accumulator = initialValue;
  let startIndex = 0;
  
  if (accumulator === undefined) {
    accumulator = this[0];
    startIndex = 1;
  }
  
  for (let i = startIndex; i < this.length; i++) {
    accumulator = callback(accumulator, this[i], i, this);
  }
  
  return accumulator;
};
```

## 设计模式

### 观察者模式

```javascript
class EventEmitter {
  constructor() {
    this.events = {};
  }
  
  on(event, callback) {
    if (!this.events[event]) {
      this.events[event] = [];
    }
    this.events[event].push(callback);
  }
  
  emit(event, data) {
    if (this.events[event]) {
      this.events[event].forEach(callback => callback(data));
    }
  }
  
  off(event, callback) {
    if (this.events[event]) {
      this.events[event] = this.events[event].filter(cb => cb !== callback);
    }
  }
}
```

### 单例模式

```javascript
class Singleton {
  constructor() {
    if (Singleton.instance) {
      return Singleton.instance;
    }
    
    this.data = {};
    Singleton.instance = this;
  }
  
  setData(key, value) {
    this.data[key] = value;
  }
  
  getData(key) {
    return this.data[key];
  }
}
```

## 性能优化技巧

### 1. 避免内存泄漏

```javascript
// 正确清理事件监听器
function setupComponent() {
  const button = document.getElementById('myButton');
  
  function handleClick() {
    console.log('Button clicked');
  }
  
  button.addEventListener('click', handleClick);
  
  // 清理函数
  return function cleanup() {
    button.removeEventListener('click', handleClick);
  };
}
```

### 2. 使用WeakMap避免内存泄漏

```javascript
const elementData = new WeakMap();

function setElementData(element, data) {
  elementData.set(element, data);
}

function getElementData(element) {
  return elementData.get(element);
}
```

## 总结

掌握这些JavaScript高级技巧：

- ✅ 闭包和作用域链
- ✅ 原型链和继承
- ✅ 异步编程模式
- ✅ 函数式编程
- ✅ 设计模式应用
- ✅ 性能优化策略

通过实践这些概念，你将能够编写更高质量、更高效的JavaScript代码。

---

*更新时间：2024年1月10日*
*作者：李四*