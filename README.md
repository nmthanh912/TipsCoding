## 1. Library npm

-   `moment:` https://www.npmjs.com/package/moment --> Handle date data
-   `multer:` https://www.npmjs.com/package/multer --> Handle upload file

## 2. Function to check data type

> Check type of data by using object prototype

```
const checkType = value => Object.prototype.toString().call(value).slice(8, -1)

checkType('checkType')
// string

checkType(3)
// number
```

---

## 3. Redis issue

> More detail about this, read here:
> https://anonystick.com/blog-developer/redis-cache-penetration-vs-cache-breakdown-2021090327198739

-   **Cache penetration:** query data that doesn't exist in database or cache memory. Then attacker will send amount of
    request per second for server, it leads to pressure database.

`Solution:` check condition at user interface side, if database doesn't contain value then set null.

```
let value = redis.get('key') // check xem cache có data không

if (!value) {
    value = await db.get('key') // check xem database có data không
}

if (!value) {
    redis.set('key', null, 30000) // set = null
}

return value
```

-   **Cache breakdown:** query data that doesn't exist in cache memory but database contains it. Problem happens when
    data expire. When amount of users send request to server it will lead to pressure database.

`Solution:` don't settime for data that you know it will be used a lot or use `mutex` with Redis SETNX tools or Memcache
ADD.

```
const getData = async get(key) => {
    String value = redis.get(key);
    if (value == null) { // nếu cache = null
        // setnx -> insert nếu chưa có key tồn tại, nên đặt 3 phút thôi
        if (redis.setnx(key_mutex, 1, 3 * 60) == 1) {  // Nó có nghĩa là set thành công
            value = db.get(key); // lay db
            redis.set(key, value, expiretime); // set cache
            redis.del(key_mutex); // xóa mutex đi
        } else {  // Lúc này có nghĩa là các luồng khác cùng lúc đã tải db và đặt lại vào bộ nhớ đệm, lúc này bạn hãy thử lấy lại giá trị trong cache
            sleep(50);
            get(key);  // data
        }
    } else {
        return value;
    }
}
```

---

## 4. Function check time

```
const checkTime = /^(?:(?:0?|1)\d|2[0-3]):(?:0?|[1-5])\d$/

console.log(checkTime.test('01:14')) // true
console.log(checkTime.test('23:59')) // true
console.log(checkTime.test('23:60')) // false
```

## 5.
