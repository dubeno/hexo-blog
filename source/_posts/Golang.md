---
title: 限流 
---

循环
``` bash
for是Go中唯一可用的循环
```

1-10

```
for i:= 0;i < 10; i++ {
  fmt.Println("%d",i)
}
```

break

```
for i := 0 ;i < 10 ;i++{
  if i > 5 =0 {
  		break
	}
	fmt.PrintLn("%d",i)
}
```

continue

```
for i := 0 ;i < 10 ;i++{
  if i % 2 =0 {
  		continue
	}
	fmt.PrintLn("%d",i)
}
```

for for

```
n := 5
for i := 0 ;i < n; i++ {
  for j:= 1; j < i; j++ {
  	fmt.Print("*")
  }
  fmt.Println()
}
```

标签