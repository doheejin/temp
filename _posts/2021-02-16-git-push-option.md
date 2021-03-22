---
layout: post
title:  "[git] git push option (깃 푸시 옵션 - simple / matching)"
date:   2021-02-15T14:25:52-05:00
author: Heejin Do
categories: git
tags:	git push simple matching
---

git에서 push를 할 때 타겟 브랜치를 따로 지정하지 않고 push를 하면 아래 사진과 같은 `warning`이 뜬다.

<img src="/assets/images/git_push1.png" title="">
advice처럼 push를 할 때 두 가지 옵션(simple, matching)을 지정하라는 것인데,
이 옵션에 따라 push 결과가 달라지므로 미리 설정해주는 것이 좋다.

이 포스트에서는 push.default의 두 옵션에 대해 간단히 정리해보았다.

### simple 옵션
현재 사용 중인 브랜치만 원격 저장소에 push 하는 옵션이다.
(git 2.0 ver 부터는 simple이 default로 설정됨)

{% highlight bash %}
git config --global push.default simple
# --global : 현재 로그인된 user에 적용됨
# --global 이 없으면 현재 저장소에만 적용됨
{% endhighlight %}

### matching 옵션
로컬과 원격 저장소의 브랜치 이름이 동일한 모든 브랜치를 push하는 옵션이다.
이 경우, 원하지 않는 브랜치가 원격으로 push 될 수 있어서 simple을 기본으로 사용한다.

{% highlight bash %}
git config --global push.default matching
{% endhighlight %}

