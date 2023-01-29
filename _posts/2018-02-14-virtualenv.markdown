---
layout: post
title:  "Using virtualenv in python"
date:   2018-02-14 00:00:00 +0000
categories: python virtualenv
---

When working with python, sometimes it is important to create isolated environments due to compatibility issues with the libs being used. Some examples that come to mind is the dependency of pyOpenSSL lib by certbot or setting up a deep learning environment.

To install virtualenv:
```
pip install virtualenv
```

To create an isolated environment based on a specific python version:
```
virtualenv -p /usr/bin/python2.7 <path to env>
```

Without the -p option, virtualenv defaults to the current python version.

To activate the virtualenv:
```
source <path to env>/bin/activate
```

You should see the name of the virtualenv in brackets to the left of the terminal. As an extra step, do python -v to check that the version is the one specified above.

To exit the virtualenv and return to the terminal:
```
deactivate
```

Also install virtualenvwrapper as it provides some useful utility commands to list and create virtualenvs:
```
pip install virtualenvwrapper
```

To list all available virtualenvs, for example:
```
lsvirtualenv
```

## Additional Resources
[virtualenv docs](https://virtualenv.pypa.io/en/stable/)

[virtualenvwrapper docs](http://virtualenvwrapper.readthedocs.io/en/latest/index.html)
