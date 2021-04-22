Cambios posibles

1. Agregar backticks a las palabras `toDTO`, `fromDTO` del README

> toDTO . Devuelve un DTO, en el ejemplo de tipo DTOConvertable

> fromDTO. Setea las propiedades del entity basado en la info del DTO parametro. En el ejemplo, el DTO parametor sera del tipo DTOConvertable

Y también dentro de:

> La clase BaseEntity posee una implementacion particular para toDTO y fromDTO

2. Corregir acentos en palabras como:

- conversion
- historico
- metodos
- generacion
- etc., otros acentos

3. Agregar una sección al readme de utilización

## Utilización de la última versión

```xml
    <dependency>
        <groupId>io.github.jokoframework</groupId>
        <artifactId>joko-utils</artifactId>
        <version>0.6.4</version>
    </dependency>
```

Como previo paso se necesita instalar el jar en un repositorio compartido de artefactos.

4. Agregar una sección al readme de requerimientos

## Requerimientos

- JDK 8.

5. Agregar al final una sección de documentación

## Documentación Adicional

- [Java Docs](https://jokoframework.github.io/joko-utils/)
