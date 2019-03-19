---
title: generator实现fibonacci数列
date: 2018-02-22 17:01:56
tags: [JavaScript]
---

```js
function* fibonacci() {
  let [prev, curr] = [0, 1];
  for(;;) {
    [prev, curr] = [curr, prev+curr];
    yield curr
  }
}

for( let value of fibonacci() ) {
  if (value > 1000) break;
  console.log(value);
}
```