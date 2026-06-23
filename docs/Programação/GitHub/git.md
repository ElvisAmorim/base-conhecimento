## Ver status

```bash
git status
```

## Enviar pro GitHub
```bash
git add .
git commit -m "mensagem"
git push
```

## Baixar alteracoes
```bash
git pull
```

## Dar permissao em projetos

No GitHub > Repositorio > Settings(config) > Secao Access > Collaborators > Add peaple > Informe o email ou usuario

!!! info "Nota"
    A pessoa receberá um convite por e-mail ou uma notificação dentro do GitHub. Ela precisa estar logada na conta dela e aceitar o convite para que o acesso seja efetivado


## Multiplas contas do github
Ao acessar uma pasta local para fazer o pul rode o comando abaixo:
```
git config core.sshCommand "ssh -i ~/.ssh/elvis.amorim -o IdentitiesOnly=yes -F /dev/null"
```
