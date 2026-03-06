# Codigos Reutilizaveis


## Ambiente Virtual
Criando ambiente

```
python -m venv venv
```

Ativando / Desativando
```
venv\Scripts\activate
venv\Scripts\deactivate
```
No Linux
```
source venv/bin/activate
```

## Arquivo .env (dontenv)
Crie o arquivo .env
```
pip install python-dotenv
```

## Arquivo requirements.txt
Com ambiente virtual instalado
Criar/Atualizar
```
pip freeze > requirements.txt

```
Desinstalar
```
pip uninstall -r requirements.txt -y
pip install -r requirements.txt
```
Para instalar pacotes via requiments.txt
```
pip install -r requirements.txt
```