## Installation

References
* [HomebrewでインストールしたMacVimがpyenvのpythonでビルドされない問題を解決 - Qiita](http://qiita.com/enushi/items/6b3282650290e6ea556e)
* [え？君せっかく Python のバージョン管理に pyenv 使ってるのに Vim の補完はシステムライブラリ参照してるの？ - Λlisue's blog](http://lambdalisue.hatenablog.com/entry/2014/05/21/065845)
* [MacVimでPythonの開発環境を構築する (2016/夏) - ryunix's blog](http://ryunix.hatenablog.jp/entry/2016/05/30/090000)
* [Python 2.x and Python 3.x · macvim-dev/macvim Wiki](https://github.com/macvim-dev/macvim/wiki/Python-2.x-and-Python-3.x)

```bash
# create pyenv for two versions
CONFIGURE_OPTS="--enable-shared" pyenv install 2.7.12
CONFIGURE_OPTS="--enable-shared" pyenv install 3.5.2
pyenv local --unset
pyenv shell --unset
pyenv global 2.7.12 3.5.2
pyenv versions
   system
 * 2.7.12 (set by /home/XXXX/.pyenv/version)
 * 3.5.2 (set by /home/XXXX/.pyenv/version)

# install macvim kaoriya
brew tap manboubird/splhack/macvim-kaoriya
brew install --HEAD --with-properly-linked-python2-python3 manboubird/splhack/macvim-kaoriya
```

Add below into `.bashrc`

```
export PYENV_PYTHON2_VERSION="2.7.12"
export PYENV_PYTHON3_VERSION="3.5.2"
```


Add below into `.vimrc`.
```
let s:python2home = $PYENV_ROOT . '/versions/' . $PYENV_PYTHON2_VERSION
let s:python2dll  = $PYENV_ROOT . '/versions/' . $PYENV_PYTHON2_VERSION . '/lib/libpython2.7.dylib'
let s:python3home = $PYENV_ROOT . '/versions/' . $PYENV_PYTHON3_VERSION
" let s:python3dll  = '/Users/toshiaki_toyama/.pyenv/versions/3.5.2/lib/libpython3.5m.dylib'
let s:python3dll  = $PYENV_ROOT . '/versions/' . $PYENV_PYTHON3_VERSION .'/lib/libpython3.5m.dylib'

if (executable(s:python2dll))
  let &pythondll = s:python2dll
  let $PYTHONHOME = s:python2home
  execute 'python import sys'
endif

if (executable(s:python3dll))
  let pythonthreedll = s:python3dll
  let $PYTHONHOME = s:python3home
  execute 'python3 import sys'
endif

```
