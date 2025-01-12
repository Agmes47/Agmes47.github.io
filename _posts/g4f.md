+++
title = 'Install g4f in Debian12'
date = 2024-01-31T09:37:19+08:00
draft = false
+++

#### Set up Python environment in Debian 12/11/10

[source](https://tecadmin.net/how-to-install-python-3-11-on-debian/)

Update packages

```bash
sudo apt update && sudo apt upgrade 
```

Install the required packages for the compilation of Python source code

```bash
sudo apt install wget build-essential libncursesw5-dev libssl-dev \
     libsqlite3-dev tk-dev libgdbm-dev libc6-dev libbz2-dev libffi-dev zlib1g-dev  
```

Download python 3.11

```bash
wget https://www.python.org/ftp/python/3.11.3/Python-3.11.7.tgz 
```

Extract archive

```bash
tar xzf Python-3.11.7.tgz 
```

Prepare for the installation

```bash
cd Python-3.11.7 
./configure --enable-optimizations 
```

Build and install

```bash
make -j 4
make altinstall 
```

#### Install g4f

[source](https://github.com/xtekky/gpt4free?tab=readme-ov-file#or)

Create a directory and `cd` in it. Clone the github repository

```bash
git clone https://github.com/xtekky/gpt4free.git
```

Navigate to the project directory

```bash
cd gpt4free
```

(Recommended) Create a Python virtual environment: You can follow the [Python official documentation](https://docs.python.org/3/tutorial/venv.html) for virtual environments.

```bash
python3 -m venv venv_name
```

Activate the virtual environment 

```bash
source venv/bin/activate
```

Install all used Python packages from `requirements.txt`

```bash
pip install -r requirements.txt
```

Create a example.py and paste the following

```python
from g4f.gui import run_gui
run_gui()
```

Run it

```bash
python example.py
```
