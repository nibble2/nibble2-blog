---
title: "spotify-youtube project"
date: "2021-04-29"
description: "문제점 및 해결방안"
tags: ['spotify-youtube project']
draft: true
ShowToc: false
ShowBreadCrumbs: false
---

# 문제점
1. 인증한 후 콜백 과정에서 redirect가 잘 안됨
   => res.redirect를 사용했으나 에러 발생
   ~~~
   UnhandledPromiseRejectionWarning: Error [ERR_HTTP_HEADERS_SENT]: Cannot set headers after they are sent to the client
    at ServerResponse.setHeader (_http_outgoing.js:543:11)
    ~~~
    - https://burning-camp.tistory.com/22
    - https://www.codementor.io/@oparaprosper79/understanding-node-error-err_http_headers_sent-117mpk82z8