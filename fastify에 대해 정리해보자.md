---
title: fastify에 대해 정리해 보자
---
##  Register

> we used the `register` API, which is the core of the Fastify framework. It is the only way to add routes, plugins, etcetera.

register를 통해 라우팅과 plugin을 등록한다

> `register` creates a new Fastify context, which means that if you perform any changes on the Fastify instance, those changes will not be reflected in the context's ancestors. In other words, encapsulation!

register는 새로운 context를 생성한다. 캡슐화 되어 있어서 해당 context의 변경은 전파되지 않는다