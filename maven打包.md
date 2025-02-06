**在 Maven 打包时跳过单元测试的几种常用方法如下：**

------

# 1. **命令行参数**

在运行 `mvn package` 时添加参数：

```
mvn package -DskipTests
```

- **作用**：编译测试代码，但跳过执行单元测试。
- **注意**：测试代码会被编译，但不会运行。

或者：

```
mvn package -Dmaven.test.skip=true
```

- **作用**：完全跳过测试代码的编译和执行。
- **区别**：比 `-DskipTests` 更彻底，适用于临时需要快速打包的场景。

------

# 2. **在 `pom.xml` 中配置（永久生效）**

在项目的 `pom.xml` 中添加以下配置：

```
<properties>
    <maven.test.skip>true</maven.test.skip>
</properties>
```



或针对 Surefire 插件配置：

```
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-surefire-plugin</artifactId>
            <configuration>
                <skipTests>true</skipTests>
            </configuration>
        </plugin>
    </plugins>
</build>
```



- **适用场景**：需要长期禁用测试（不推荐，仅限特殊场景）。

------

# 3. **Maven 生命周期跳过测试**

```
mvn package -Dskip
```

- **说明**：跳过整个测试生命周期（较少用）。

------

# 总结

- **临时跳过测试**：优先使用 `mvn package -DskipTests`。
- **彻底跳过编译和执行**：用 `-Dmaven.test.skip=true`。
- **生产环境慎用**：确保跳过测试是安全的，测试是保障质量的关键步骤！