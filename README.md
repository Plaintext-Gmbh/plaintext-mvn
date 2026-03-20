# Plaintext Maven Repository

A Git-based Maven repository for hosting release artifacts of the [Plaintext Root](https://github.com/daniel-marthaler/plaintext-root) framework.

## About

This repository serves as a lightweight Maven repository, allowing projects to consume Plaintext Root artifacts without requiring a dedicated artifact server. GitHub hosts the repository contents, and Maven resolves dependencies directly from it.

## Usage

Add this repository to your `pom.xml`:

```xml
<repositories>
    <repository>
        <id>plaintext-mvn</id>
        <url>https://raw.githubusercontent.com/daniel-marthaler/plaintext-mvn/master</url>
    </repository>
</repositories>
```

Then add Plaintext Root dependencies as needed:

```xml
<dependency>
    <groupId>ch.plaintext</groupId>
    <artifactId>plaintext-root</artifactId>
    <version>LATEST</version>
    <type>pom</type>
</dependency>
```

## Related Projects

- [Plaintext Root](https://github.com/daniel-marthaler/plaintext-root) — The open-source application framework for building multi-tenant web applications with Jakarta Faces, PrimeFaces, and Spring Boot.

## License

This project is licensed under the [Mozilla Public License 2.0](LICENSE).
