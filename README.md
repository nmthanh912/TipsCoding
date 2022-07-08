## 1. Function to check data type

```
const checkType = value => Object.prototype.toString().call(value).slice(8, -1)

checkType('checkType')
// string

checkType(3)
// number
```
