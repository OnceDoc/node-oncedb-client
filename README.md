oncedb-client - a node.js oncedb client
===========================

This is a complete Redis client for node.js.  It supports all Redis commands, including many recently added commands like EVAL from
experimental Redis server branches.


Install with:

    npm install oncedb-client

Pieter Noordhuis has provided a binding to the official `hiredis` C library, which is non-blocking and fast.  To use `hiredis`, do:

    npm install hiredis oncedb-client

If `hiredis` is installed, `node_redis` will use it by default.  Otherwise, a pure JavaScript parser will be used.

If you use `hiredis`, be sure to rebuild it whenever you upgrade your version of node.  There are mysterious failures that can
happen between node and native code modules after a node upgrade.


## Usage

Simple example, included as `examples/simple.js`:

```js
    var client = require("oncedb-client").createClient();

    // if you'd like to select database 3, instead of 0 (default), call
    // client.select(3, function() { /* ... */ });

    client.on("error", function (err) {
        console.log("Error " + err);
    });

    client.set("string key", "string val", oncedb.print);
    client.hset("hash key", "hashtest 1", "some value", oncedb.print);
    client.hset(["hash key", "hashtest 2", "some other value"], oncedb.print);
    client.hkeys("hash key", function (err, replies) {
        console.log(replies.length + " replies:");
        replies.forEach(function (reply, i) {
            console.log("    " + i + ": " + reply);
        });
        client.quit();
    });
```

This will display:

    mjr:~/work/node_redis (master)$ node example.js
    Reply: OK
    Reply: 0
    Reply: 0
    2 replies:
        0: hashtest 1
        1: hashtest 2
    mjr:~/work/node_redis (master)$


## More


    client.search('text*', '=', 'Kris', function(err, objs) {
      console.log(objs)
    })

    > [ 'text1', 'Kris' ] 

    client.search('text*', '~', 'Kris', function(err, objs) {
      console.log(objs)
    })

    > [ 'text1', 'Kris', 'text5', 'This is ok, Kris' ]


    client.hsearch('userInfo:*', {'nVisit': {'$gt': 300}}, function(err, objs) {
      console.log(objs)

    })

    client.hsearch('userInfo:*', {'nVisit': {'>': 300}}, function(err, objs) {
      console.log(objs)
    })

     [ { _key: 'userInfo:1004', nVisit: '400' },
       { _key: 'userInfo:1005', nVisit: '10000' },
       { _key: 'userInfo:1006', nVisit: '10000' } ] }



    client.hsearch('userInfo:*', {
        'name'     : '*'
      , 'gender'   : '*'
      , 'nVisit'   : {'>'  : '100'}
      , 'content'  : {'~|' : 'libs.min.js'}
    }, function(err, objs) {
        console.log(objs)  
    })

    > [ { _key: 'userInfo:1006',
       name: 'Mar',
       gender: 'male',
       nVisit: '10000',
       content: ':0 0 60px 0"> <p style="float:left;line-height: 30px;"> 沪ICP备13020027号-2 </p> <p style="text-align           :right;"> <a class="btn" href="#top">返回顶部</a>  </p> </div> </div></div><script src="/js/libs.min.js"></script><scr           ipt src="/js/prod.min.js?v=219"></script><script>' } ]


    client.hselect(['name'], ['userInfo:100'], function(err, objs) {
      console.log(objs)
    })

    > [ { _key: 'userInfo:100', name: 'shanghai' } ]



    client.hselect(
        ['name', 'email', 'isPublic', 'nVisit']
      , ['userInfo:100', 'userInfo:103', 'userInfo:1005', 'userInfo:1006']
      , function(err, objs) {
        console.log(objs)
    })

    [ { _key: 'userInfo:100',
        name: 'shanghai',
        email: null,
        isPublic: null,
        nVisit: null },
    { _key: 'userInfo:103',
        name: 'newghost',
        email: null,
        isPublic: null,
        nVisit: null },
    { _key: 'userInfo:1005',
        name: 'Mars2',
        email: null,
        isPublic: '0',
        nVisit: '10000' },
    { _key: 'userInfo:1006',
        name: 'Mar',
        email: null,
        isPublic: '1',
        nVisit: '10000' } ]



    client.hmgetall(['userInfo:100', 'userInfo:1003', 'userInfo:100'], function(err, objs) {
      console.log(objs)
    })

    > [ { _key: 'userInfo:100',
        id: '100',
        name: 'shanghai',
        gender: 'female',
        poster: '龙' },
      { _key: 'userInfo:1003',
        name: 'Telyer',
        id: '1003',
        gender: 'male',
        active: '0',
        joinTime: '1484445746020',
        poster: '王五',
        isPublic: '0',
        nVisit: '300' },
      { _key: 'userInfo:100',
        id: '100',
        name: 'shanghai',
        gender: 'female',
        poster: '龙' } ]

## About

It's fork from https://github.com/NodeRedis/node_redis/tree/v0.12.1