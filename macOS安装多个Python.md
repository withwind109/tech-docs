安装 pyenv：🛰

```sh
$ brew update
$ brew install pyenv
$ pyenv -v
```

安装管理多个 Python：
$ pyenv install 2.7.15
$ pyenv install 3.7.0
$ pyenv versions
system
2.7.15

-   3.7.0 (set by /Users/john/.pyenv/version)

切换版本：
$ pyenv global 2.7.15
$ pyenv versions
system

-   2.7.15 (set by /Users/john/.pyenv/version)
    3.7.0
    $ python --version
    Python 2.7.15
