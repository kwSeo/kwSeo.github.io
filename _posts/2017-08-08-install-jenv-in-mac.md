---
layout: post
title: "install jenv in mac"
categories: JAVA
---

- homebrew 필요

```bash
brew install jenv
```

- ~/.bash_profile에 필요한 설정 등록

```bash
...
eval "$(jenv init -)"
...
```

- 문제가 없는지 확인

```bash
jenv doctor
```

- 사용할 JDK 등록

```bash
jenv add {java8_home}
jenv add {java7_home}
jenv add {java6_home}
...
```

- JAVA_HOME 자동 등록을 위한 플러그인 실행

```bash
jenv enable-plugin export
```
