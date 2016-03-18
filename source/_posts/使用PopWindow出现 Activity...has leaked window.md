---

title: 使用PopWindow出现 Activity...has leaked window 

---

## 原因 ##

> popWindow依赖的Activity已经finish,而PopWindow还存在

## 解决办法 ##

> 在 *onPause* 和 *onDismiss()* 把popWindow dimiss();

