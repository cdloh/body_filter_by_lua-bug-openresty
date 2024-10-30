# How to replicate

Clone this repo and ensure that you pull down submoudles

```
git clone --recurse-submodules https://github.com/cdloh/body_filter_by_lua-bug-openresty.git
cd body_filter_by_lua-bug-openresty
```

Run openresty in a container at the root of this repo

```
docker run --rm -v $(pwd):/etc/nginx/conf.d:Z --publish 8080:80 openresty/openresty:1.25.3.2-alpine-fat
```

Make a curl request to throttling to cause buffering

```
curl localhost:8080/file.js --limit-rate 423K > /dev/null
```

Note the error about even though we aren't in that context / phase

```
2024/10/30 13:59:36 [error] 7#7: *1 lua entry thread aborted: runtime error: content_by_lua(/etc/nginx/conf.d/default.conf:13):35: API disabled in the context of body_filter_by_lua*
stack traceback:
coroutine 0:
	[C]: in function 'print'
	content_by_lua(/etc/nginx/conf.d/default.conf:13):35: in main chunk, client: 192.168.65.1, server: localhost, request: "GET /file.js HTTP/1.1", host: "localhost:8080"
```



Note that this doesn't happen on 1.19

```
docker run --rm -v $(pwd):/etc/nginx/conf.d:Z --publish 8080:80 openresty/openresty:1.19.9.1-14-alpine-fat
```
