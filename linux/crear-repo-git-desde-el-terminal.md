# Crear repo git desde el terminal

Con esto creamos un repo, en este ejemplo de nombre desde la consola, necesitamos el token que podremos generarlo desde [aqui](https://github.com/settings/tokens).

```shell
curl -s POST https://api.github.com/user/repos -H "Authorization: token ghp_nXqcBG3qohY3QBMLJ7sFOsCh8ysN8s1R5sWZ" -d "{\"name\":\"REPO_TEST\"}"
```
