# RESTful 接口设计

一句话解释 RESTful 就是：URL 定位资源，用 HTTP 动词（GET,POST,PUT,DELETE）描述操作。

示例如下：

```
   GET /posts    // Returns a list of all posts available!
  POST /posts    // Creates a new post!
   GET /posts/1  // Returns a single post!
   PUT /posts/1  // Updates a post that exists!
DELETE /posts/1  // Deletes a post that exists!
```

批量操作时，可以这样定义：

```
// batch create
POST /posts/batch
     Body: { 'create': [{ name: 'New post!', body: 'Some stuff..' }, { name: 'Another...', body: 'This is nifty!'}] }

// batch update
POST /posts/batch
     Body: { 'update': { 1: { title: 'My new title!' }, 2: { author: 'Walter White' } } }

// batch delete
POST /posts/batch
     Body: { 'delete': [1, 2, 3, 4, 5, 10, 42, 68, 99] }
```

## 参考
