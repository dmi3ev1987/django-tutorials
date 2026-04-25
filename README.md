## Install venv

```bash
python3 -m venv venv
```

```bash
python --version
```

```bash
source venv/bin/activate
```

```bash
python -m pip install --upgrade pip
```

## Install Django

```bash
python -m pip install Django
```

### Verify Django is installed

```bash
python -m django --version
```

```bash
# enter python shell
python

# type commands below by lines  (after `>>>`)  
>>> import django
>>> print(django.get_version())
6.0
```


## Save and install requirements

```bash
pip freeze > requirements.txt
```

```bash
pip install -r requirements.txt
```
