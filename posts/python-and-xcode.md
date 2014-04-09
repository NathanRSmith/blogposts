---
title: 'Python & Xcode 5.1'
author: Nathan Smith
tags: [python, xcode, homebrew, clang, virtualenv]
---

The other day I was working on my Mac and needed to install some Python packages.  Like any sane Python developer, I created a new virtualenv and attempted to install several packages.  Among these packages was [Fabric](http://www.fabfile.org/) which depends on [PyCrypto](https://www.dlitz.net/software/pycrypto/) which has compiled extensions.  The installation failed with a somewhat cryptic message:

~~~~~~~~
clang: error: unknown argument: '-mno-fused-madd' [-Wunused-command-line-argument-hard-error-in-future]

clang: note: this will be a hard error (cannot be downgraded to a warning) in the future

error: command 'cc' failed with exit status 1
~~~~~~~~

Hmmm... Googling did not immediately show a solution but eventually I found [this question](http://stackoverflow.com/questions/22313407/clang-error-unknown-argument-mno-fused-madd-python-package-installation-fa) on StackOverflow which explained the issue.

The latested clang update that came with Xcode 5.1 is causing some issues among users of compiled extensions.  This does not only affect Pythonistas, but also Rubyists, etc.  The problem is that clang now produces an error when it encounters unknown flags, specifically ``-mno-fused-madd``.  I have no idea what this flag is supposed to do, but apparently Apple's clang doesn't know what to do with it. The problem is that the system version of Python uses the unsupported flag.  Anyway, there are a few solutions that I tried and few more in the SO answers.


### Solution 1 - ARCHFLAGS ###

In the future this will not be an option, but it will work for now.

~~~~~~~~
env ARCHFLAGS="-Wno-error=unused-command-line-argument-hard-error-in-future" pip install fabric
~~~~~~~~

This downgrades the error into a warning for now.

### Solution 2 - Homebrew ###

This second solution is prefferable to me as it will not suddenly stop working in the future.  Build and install a new version of Python via Homebrew.  Apparently the issue has been fixed in the Python source and just needs to be rebuilt.

~~~~~~~~
brew install python
brew link --overwrite python
~~~~~~~~

Assuming ``/usr/local/bin/`` comes before the default Python location, installing system level packages should work again.  To use the new Python with virtualenv simple use the ``-p`` option to specify the version of Python to use:

~~~~~~~~
mkvirtualenv -p /usr/local/bin/python myenv
~~~~~~~~

Now the virtualenv will be able to happily build extensions and I can get back to doing more interesting things.