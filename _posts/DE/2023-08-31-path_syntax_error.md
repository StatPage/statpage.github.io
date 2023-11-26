---
layout: single
title:  "SyntaxError: (unicode error) 'unicodeescape' codec can't decode bytes in position 2-3"
toc: true
toc_sticky: true
# categories: []
# tags: [, ]
published: true
---

# 에러가 발생할 때

가끔씩 파이썬 파일 경로에서 아래와 같은 에러가 발생할 때가 있다.

```console
SyntaxError: (unicode error) 'unicodeescape' codec can't decode bytes in position 2-3: truncated \UXXXXXXXX escape
```

원인은 경로에 있는 `\`를 유니코드로 인식하기 때문이다.


# 해결 방안

경로에 있는 \ 대신 /나 \\를 써주면 문제가 발생하지 않는다.