+++
date = "2016-04-20T09:00:00+09:00"
draft = false
title = "vimにNERDTreeを入れる"
tags = ["vim", "nerdtree", "vim-plugin"]
categories = ["development"]
+++

# vimにNERDTreeを入れる

## 記事の通りにやる

[vim-plugin NERDTree で開発効率をアップする！ - Qiita](http://qiita.com/zwirky/items/0209579a635b4f9c95ee) を参考にする

## 記事からの修正点

そのままやると、NERDTreeを起動しようとすると以下のように怒られる

    [neobundle] neobundle#rc() is removed function.
    [neobundle] Please use neobundle#begin()/neobundle#end() instead.
    [neobundle] neobundle directory is empty.
    [neobundle] `NeoBundle` commands must be executed within a neobundle#begin/end block.  Please check your usage.
    Press ENTER or type command to continue

という訳で、以下のように.vimrcを書き換え。

    if has('vim_starting')
        set runtimepath+=~/.vim/bundle/neobundle.vim
    endif
    call neobundle#begin(expand('~/.vim/bundle/'))
        "insert here your Neobundle plugins"
        NeoBundle 'scrooloose/nerdtree'

        filetype plugin indent on
    call neobundle#end()

## できた

![完成](/img/vim-nerdtree/complete.png)

