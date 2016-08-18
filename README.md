## Installation

References
* [HomebrewでインストールしたMacVimがpyenvのpythonでビルドされない問題を解決 - Qiita](http://qiita.com/enushi/items/6b3282650290e6ea556e)
* [え？君せっかく Python のバージョン管理に pyenv 使ってるのに Vim の補完はシステムライブラリ参照してるの？ - Λlisue's blog](http://lambdalisue.hatenablog.com/entry/2014/05/21/065845)
* [MacVimでPythonの開発環境を構築する (2016/夏) - ryunix's blog](http://ryunix.hatenablog.jp/entry/2016/05/30/090000)
* [Python 2.x and Python 3.x · macvim-dev/macvim Wiki](https://github.com/macvim-dev/macvim/wiki/Python-2.x-and-Python-3.x)
* [Building · splhack/macvim-kaoriya Wiki](https://github.com/splhack/macvim-kaoriya/wiki/Building)

```bash
# create pyenv for two versions
PYTHON_CONFIGURE_OPTS="--enable-shared" \
    LDSHARED="clang -bundle" \
    LDCXXSHARED="clang++ -bundle" \
    BLDSHARED="clang -bundle -lpython2.7" \
    pyenv install 2.7.12

# confirm `libpython2.7.dylib` is included
otool -L ~/.pyenv/versions/2.7.12/lib/python2.7/lib-dynload/_ctypes.so | grep dylib

PYTHON_CONFIGURE_OPTS="--enable-shared" \
    LDSHARED="clang -bundle" \
    LDCXXSHARED="clang++ -bundle" \
    BLDSHARED="clang -bundle -lpython3.5m" \
    pyenv install 3.5.2

# confirm `libpython3.5m.dylib` is included
otool -L ~/.pyenv/versions/3.5.2/lib/python3.5/lib-dynload/_ctypes.cpython-35m-darwin.so

# setup pyenv
pyenv local --unset
pyenv shell --unset
pyenv global 2.7.12 3.5.2
pyenv versions
   system
 * 2.7.12 (set by /home/XXXX/.pyenv/version)
 * 3.5.2 (set by /home/XXXX/.pyenv/version)

# create virtualenv
pyenv virtualenv 2.7.12 py27
pyenv virtualenv 3.5.2 py35

# install macvim kaoriya
brew tap universal-ctags/universal-ctags
brew install --HEAD universal-ctags

brew tap manboubird/splhack
brew install --HEAD manboubird/splhack/cmigemo-mk
brew install --HEAD --with-properly-linked-python2-python3 manboubird/splhack/macvim-kaoriya
```

Usage
```
$ pyenv versions
  system
* 2.7.12 (set by /Users/xxx/.pyenv/version)
* 3.5.2 (set by /Users/xxx/.pyenv/version)
  py27
  py35

$ pyenv activate py35

(py35) $ vim-with-compiled-with-pyenv test.py

# press space at end of characters and display suggestions if pandas is installed
import pandas.re

$ deactivate
$ pyenv activate py27

(py27) $ vim-with-compiled-with-pyenv test.py

# press space at end of characters and display suggestions if pandas is installed
import pandas.re
```

## For diynamic loading without mac vim re-compilation (Experimental)

Add below into `.bashrc`

```
export PYENV_ROOT="${HOME}/.pyenv"
export PYENV_PYTHON2_VERSION="2.7.12"
export PYENV_PYTHON3_VERSION="3.5.2"
```

Add below into `.vimrc`. This may not be required.
```
let s:python2home = $PYENV_ROOT . '/versions/' . $PYENV_PYTHON2_VERSION
let s:python2dll  = $PYENV_ROOT . '/versions/' . $PYENV_PYTHON2_VERSION . '/lib/libpython2.7.dylib'
let s:python3home = $PYENV_ROOT . '/versions/' . $PYENV_PYTHON3_VERSION
" let s:python3dll  = '/Users/toshiaki_toyama/.pyenv/versions/3.5.2/lib/libpython3.5m.dylib'
let s:python3dll  = $PYENV_ROOT . '/versions/' . $PYENV_PYTHON3_VERSION .'/lib/libpython3.5m.dylib'

if (executable(s:python3dll))
  let pythonthreedll = s:python3dll
  let $PYTHONHOME = s:python3home
  execute 'python3 import sys'
endif

if (executable(s:python2dll))
  let &pythondll = s:python2dll
  let $PYTHONHOME = s:python2home
  execute 'python import sys'
endif
```
