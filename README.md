# CrafterCMS documentation

## Requirements

#### Install Python & PIP & pipenv

First, you need to install Python 3 and PIP (if not already present on your system):

```
sudo apt-get install python3-pip
```

You might need to update PIP right away

```
pip3 install -U pip
```

Then, install `pipenv`

```
sudo pip install pipenv
```

Finally, install dependencies

```
pipenv install
```

and activate the pipenv shell

```
pipenv shell
```

#### Install Extensions

Install the following required extensions

- [sphinx-tabs](https://pypi.org/project/sphinx-tabs/) extension
  ```
  pip install sphinx-tabs
  ```
- [sphinx-copybutton](https://pypi.org/project/sphinx-copybutton/) extension extension
  ```  
  pip install sphinx-copybutton
  ``` 
- [sphinxext-remoteliteralinclude](https://pypi.org/project/sphinxext-remoteliteralinclude/) extension
  ```
  pip3 install sphinxext-remoteliteralinclude
  ```

- [Furo theme](https://pradyunsg.me/furo/quickstart/)
  ```
  pip install furo
  ```

## Building CrafterCMS documentation

After meeting all requirements, you're ready to build the docs by running this command:

```
make html
```

The docs will be under `build` folder.

To clear the build folder run

```
make clean
```

---
**NOTE**
Remember that when building the docs you need to activate the pipenv shell.

---


