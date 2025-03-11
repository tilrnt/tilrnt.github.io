---
layout: post
title: Compile and install python 3.13 on Ubuntu 20.04
date: 2025-03-11 00:00:00
categories: ubuntu python 
---

While trying to compile and install python from source on Ubuntu 20.04, I kept hitting the following error on after running make:

```
Need to install the following packages:

__dbm __tkinter
```

To fix the issue, I had to install the following packages:
```
sudo apt get update && sudo apt get install libgdbm-compat-dev tk-dev
```

To install python 3.13.2, the full set of commands become:
```
sudo apt get update

sudo apt install build-essential zlib1g-dev libncurses5-dev libgdbm-dev libnss3-dev libssl-dev libreadline-dev libffi-dev libsqlite3-dev libbz2-dev libgdbm-compat-dev tk-dev wget

wget https://www.python.org/ftp/python/3.13.0/Python-3.13.2.tgz

tar -xvf Python-3.13.2.tgz

cd Python-3.13.2

./configure --enable-optimizations

make -j 4

sudo make altinstall

python3.13 --version
```

Creating a virtual env from the new python install:
```
python3.13 -m venv myvenv

source myvenv/bin/activate
```
