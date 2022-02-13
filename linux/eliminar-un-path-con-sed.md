# Eliminar un path con sed

Fichero actual, un ejemplo:

```
#sonar.tests=src/test/java,src/test/resources
```

Si queremos quitar el segundo path, lo hacemos con el sed.

```
sed -i 's|#sonar.tests=src/test/java,src/test/resources|sonar.tests=src/test/java|g' NombreDelFichero
```

y en una sola linea, le hemos quitado el #, y ademas la segunda ruta.

Ejemplo final en Jenkins

```
stage('Code Quality Check via SonarQube') {
    steps {
        sh "sed -i 's|#sonar.tests=src/test/java,src/test/resources|sonar.tests=src/test/java|g' sonar-project.properties"
        runSonar("ci-code", "${NAME_PROJECT}", "${SONAR_TOKEN}")
    }
}
```
