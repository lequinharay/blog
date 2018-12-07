+++
date = "2016-03-17T17:15:26+09:00"
draft = true
title = "allow guzzle redirect"
tags = ["php", "Guzzle"]
+++

# Guzzleによるpostリクエストがリダイレクトされるときに、postフィールドの値を維持する

Guzzleでpostリクエストを送る際に、何らかの理由でリダイレクトが発生するとpostフィールドが消失してしまう。
