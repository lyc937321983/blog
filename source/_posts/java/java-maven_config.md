---
title: Java Maven配置
date: 2026-05-19 09:50:08
tags: Java Maven
comment: 'valine'
categories: 
- Java
---

# Maven配置


Maven是配合着idea一起使用，下载依赖速度慢就去网上找个镜像配置一下，但总会遇到莫名其妙的问题，比如镜像源不生效、Error reading file pom.xml等等。今天详细讲解一下maven配置文件settings.xml的配置方法。

# 小知识

maven的配置文件存在于两个地方，一个是用户目录下的.m2目录：`${user.home}/.m2/`；另一个是maven程序目录下的conf目录下：`C:\Program Files\apache-maven-3.9.6\conf`（假设maven程序被解压到了C:\Program Files\） 如果没有指定使用哪一个settings配置，maven会使用这两个配置文件合并后的配置，相同的配置项会优先使用用户目录`${user.home}/.m2/`中settings文件内的配置。建议这两个目录的settings文件保持一致。

# settings.xml中的顶级标签

```xml
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 https://maven.apache.org/xsd/settings-1.0.0.xsd">
  <localRepository/>
  <interactiveMode/>
  <offline/>
  <pluginGroups/>
  <servers/>
  <mirrors/>
  <proxies/>
  <profiles/>
  <activeProfiles/>
</settings>
```

我们逐一讲解一下

# localRepository

localRepository就是你的本地仓库位置，一般使用idea的话会在idea中配置这个属性，但使用cmd的话就会用到这个配置，示例配置：`<localRepository>D:/maven_repository/</localRepository>`

# interactiveMode

当`interactiveMode`设置为true时，表示Maven处于交互模式。在交互模式下，当Maven需要用户输入时（例如需要用户确认某些操作或提供必要的信息），它会提示用户并等待用户的输入。这对于需要用户交互的操作非常有用，例如在部署过程中需要用户提供密码或确认某些操作。 当`interactiveMode`设置为false时，表示Maven处于非交互模式。在非交互模式下，Maven不会提示用户输入，而是使用默认值或者根据配置自动执行操作。这对于自动化构建或者持续集成环境非常有用，因为在这些场景下通常不希望Maven等待用户输入。 `interactiveMode`默认是true，但也可以在settings中配置一下，示例：`<interactiveMode>true</interactiveMode>`

# offline

当`offline`设置为true时，表示Maven处于离线模式。在离线模式下，Maven不会尝试从远程仓库下载任何依赖项或插件，而是只使用本地仓库中已有的资源。这意味着，如果本地仓库中缺少某些依赖项，Maven将无法自动下载它们，并可能导致构建失败。 当`offline`设置为false时，表示Maven处于在线模式。在在线模式下，Maven会根据需要从远程仓库下载依赖项和插件，并将它们存储在本地仓库中以供后续使用。 使用离线模式的主要目的是在无法连接到远程仓库的情况下进行构建，例如在没有网络连接的环境中，或者为了加快构建速度而避免不必要的网络请求。 `offline`默认是false，也可以在settings中配置一下，示例：`<offline>false</offline>`

# pluginGroups

`pluginGroups`是一个用于配置插件组的参数。它允许你定义一组插件的前缀，以便在项目的POM文件中使用较短的插件名称。 当你在POM文件中使用插件时，通常需要指定插件的完整坐标，包括`groupId`、`artifactId`和`version`。例如：

```xml
<plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-compiler-plugin</artifactId>
  <version>3.8.1</version>
  ...
</plugin>
```

但是，如果你在pluginGroups中配置了插件组，就可以省略插件的groupId，只使用较短的artifactId。Maven会根据pluginGroups中的配置自动推断完整的插件坐标。 下面是一个pluginGroups的配置示例：

```xml
<settings>
  ...
  <pluginGroups>
    <pluginGroup>org.apache.maven.plugins</pluginGroup>
    <pluginGroup>org.codehaus.mojo</pluginGroup>
  </pluginGroups>
  ...
</settings>
```

在上述示例中，我们定义了两个插件组：org.apache.maven.plugins和org.codehaus.mojo。这意味着在POM文件中使用这两个组下的插件时，可以省略groupId。 例如，在配置了上述pluginGroups后，可以在POM文件中简化插件的声明：

```xml
<plugin>
  <artifactId>maven-compiler-plugin</artifactId>
  <version>3.8.1</version>
  ...
</plugin>
```

Maven会自动将maven-compiler-plugin解析为完整的坐标`org.apache.maven.plugins:maven-compiler-plugin:3.8.1`。 使用pluginGroups的好处是可以简化POM文件中的插件声明，减少重复的groupId配置。它提供了一种更简洁的方式来引用常用的插件组。 需要注意的是，如果项目的POM文件中明确指定了插件的groupId，那么pluginGroups中的配置将被忽略，Maven会使用POM文件中指定的完整坐标。

# servers

`servers`是一个用于配置服务器身份验证信息的参数。它允许你定义一组服务器的身份验证详细信息，以便Maven在访问受保护的资源（如部署到远程仓库）时使用这些信息进行身份验证。 当Maven需要访问受保护的资源时，它会查找`servers`配置，并使用匹配的服务器身份验证信息来进行身份验证。 下面是一个`servers`的配置示例，一般使用私服会用到这个配置：

```xml
<settings>
  ...
  <servers>
    <server>
      <id>my-repo</id>
      <username>my-username</username>
      <password>my-password</password>
    </server>
    <server>
      <id>another-repo</id>
      <username>another-username</username>
      <password>another-password</password>
    </server>
  </servers>
  ...
</settings>
```

在上述示例中，我们定义了两个服务器的身份验证信息： 第一个服务器的`id`为`my-repo`，对应的用户名为`my-username`，密码为`my-password`。 第二个服务器的`id`为`another-repo`，对应的用户名为`another-username`，密码为`another-password`。 服务器的`id`是一个唯一的标识符，用于在其他配置或POM文件中引用该服务器。例如，在pom.xml的`distributionManagement`部分中，你可以使用服务器的`id`来指定部署到远程仓库时使用的身份验证信息。

```xml
<project>
  ...
	<distributionManagement>
	  <repository>
	    <id>my-repo</id>
	    <url>https://example.com/my-repo</url>
	  </repository>
	</distributionManagement>
  ...
</project>
```

在上述示例中，`my-repo`对应于`servers`中定义的服务器id。Maven将使用与`my-repo`关联的用户名和密码来访问和部署构件到指定的远程仓库。 通过在settings.xml文件中配置`servers`，你可以集中管理Maven访问受保护资源时所需的身份验证信息。这样可以避免在项目的POM文件中直接存储敏感的用户名和密码，提高了安全性。 需要注意的是，在Maven2.1.0及更新的版本中，`servers`中的密码可以使用Maven的加密机制进行加密，以进一步提高安全性。你可以使用Maven的内置加密功能或使用外部加密工具来加密密码，然后将加密后的密码存储在settings.xml文件中。

## 使用Maven加密密码：

- 打开命令行终端或命令提示符。

- 导航到Maven的安装目录下的bin目录。

- 执行以下命令来生成主密码（master password）：

  ```xml
  mvn --encrypt-master-password
  ```

  系统会提示你输入主密码，输入后按回车键。Maven会生成一个加密后的主密码。

- 将加密后的主密码复制，并将其添加到settings.xml文件中的

  ```
  <settings>
  ```

  元素下，使用

  ```
  <settingsSecurity>
  ```

  元素进行配置：

  ```xml
  <settings>
    <settingsSecurity>
      <master>{加密后的主密码}</master>
    </settingsSecurity>
    ...
  </settings>
  ```

- 执行以下命令生成加密后的服务器密码：

  ```xml
  mvn --encrypt-password
  ```

  系统会提示你输入要加密的密码，输入后按回车键。Maven会生成一个加密后的密码。

- 将加密后的密码复制，并将其添加到settings.xml文件中对应服务器的

  ```
  <password>
  ```

  元素中：

  ```xml
  <servers>
    <server>
      <id>my-server</id>
      <username>my-username</username>
      <password>{加密后的密码}</password>
    </server>
  </servers>
  ```

完成上述步骤后，settings.xml文件中的密码就被加密了。当Maven需要访问受保护的资源时，它会使用主密码来解密服务器密码，然后使用解密后的密码进行身份验证。 以下是一个完整的示例：

```xml
<settings>
  <settingsSecurity>
    <master>{jSMOWnoPFgsHVpMvz5VrIt5kRbzGpI8u+9EF1iFQyJQ=}</master>
  </settingsSecurity>
  
  <servers>
    <server>
      <id>my-server</id>
      <username>my-username</username>
      <password>{COQLCE6DU6GtcS5P=}</password>
    </server>
  </servers>
</settings>
```

主密码和服务器密码都放在了settings文件中，那谁拿到这个文件都可以访问服务器，和不加密没什么区别，建议使用私钥进行加密，maven密码加密部分我会新写一篇文章，感兴趣的可以关注一下。

# mirrors

这个配置项使我们使用最多的了，`mirrors`用于配置仓库镜像。仓库镜像是一种代理服务器，用于替代原始的仓库URL，以提高下载速度、增强可用性或提供额外的安全性。 当Maven需要从仓库下载构件时，它会先检查是否有匹配的镜像配置。如果找到了匹配的镜像，Maven将使用镜像URL替代原始的仓库URL进行下载。 下面是一个示例：

```xml
<settings>
  ...
  <mirrors>
    <mirror>
      <id>my-mirror</id>
      <name>My Mirror</name>
      <url>https://my-mirror.example.com/repo</url>
      <mirrorOf>central</mirrorOf>
    </mirror>
  </mirrors>
  ...
</settings>
```

`<id>`：镜像的唯一标识符。它用于区分不同的镜像配置。 `<name>`：镜像的名称。这个名称是可选的，主要用于提供一个友好的描述。 `<url>`：镜像的URL。这是镜像服务器的实际地址，Maven将使用这个URL替代原始的仓库URL进行下载。 `<mirrorOf>`：指定镜像所代表的原始仓库。这个值可以是仓库的ID或者一个特殊的值，用于匹配多个仓库。常见的值有：

- `central`：替代Maven中央仓库。
- `*`：匹配所有仓库。
- `external:*`：匹配除了本地仓库之外的所有外部仓库。
- `repo1,repo2`：匹配仓库ID为repo1和repo2的仓库。
- `*,!repo1`：匹配除了仓库ID为repo1之外的所有仓库。

当Maven需要下载构件时，它会按照以下顺序查找镜像：

- 如果有匹配的镜像，且`<mirrorOf>`的值与要下载的仓库`ID`完全匹配，则使用该镜像。
- 如果有匹配的镜像，且`<mirrorOf>`的值为`*`，则使用该镜像。
- 如果有匹配的镜像，且`<mirrorOf>`的值为`external:*`，且要下载的仓库不是本地仓库，则使用该镜像。
- 如果有匹配的镜像，且`<mirrorOf>`的值包含要下载的仓库`ID`，则使用该镜像。
- 如果没有找到匹配的镜像，则直接使用原始的仓库URL进行下载。

当Maven需要从仓库下载构件时,它会按照以下顺序查找匹配的镜像配置:

1. 精确匹配:
   - 如果有一个镜像的`<mirrorOf>`值与要下载的仓库ID完全匹配,则使用该镜像。
   - 例如,如果要下载的仓库`ID`为`my-repo`,且有一个镜像的`<mirrorOf>`值为`my-repo`,则使用该镜像。
2. 全匹配:
   - 如果有一个镜像的`<mirrorOf>`值为`*`,则使用该镜像。
   - 这个镜像将匹配所有的仓库,无论仓库`ID`是什么。
3. 外部仓库匹配:
   - 如果有一个镜像的`<mirrorOf>`值为`external:*`,且要下载的仓库不是本地仓库,则使用该镜像。
   - 这个镜像将匹配所有外部仓库,但不包括本地仓库。
4. 仓库列表匹配:
   - 如果有一个镜像的`<mirrorOf>`值包含要下载的仓库`ID`,则使用该镜像。
   - 例如,如果要下载的仓库`ID`为`repo1`,且有一个镜像的`<mirrorOf>`值为`repo1,repo2`,则使用该镜像。
5. 排除仓库匹配:
   - 如果有一个镜像的`<mirrorOf>`值排除了要下载的仓库`ID`,则不使用该镜像。
   - 例如,如果要下载的仓库`ID`为`repo1`,且有一个镜像的`<mirrorOf>`值为`*,!repo1`,则不使用该镜像。
6. 如果没有找到匹配的镜像,Maven将直接使用原始的仓库URL进行下载。

在这里推荐一下[阿里巴巴开源镜像站](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.aliyun.com%2Fmirror%2F)，里面有很多镜像，有需要的同学可以去看看

# proxies

需要通过代理服务器访问网络资源（如下载构件）时，可以使用`<proxies>`配置来指定代理服务器的详细信息。 下面是一个`<proxies>`配置的示例：

```xml
<settings>
  ...
  <proxies>
    <proxy>
      <id>my-proxy</id>
      <active>true</active>
      <protocol>http</protocol>
      <host>proxy.example.com</host>
      <port>8080</port>
      <username>proxyuser</username>
      <password>proxypassword</password>
      <nonProxyHosts>*.example.com|localhost</nonProxyHosts>
    </proxy>
  </proxies>
  ...
</settings>
```

- `<id>`：代理的唯一标识符。它用于区分不同的代理配置。
- `<active>`：表示是否激活该代理配置。值为`true`表示激活，`false`表示不激活。
- `<protocol>`：指定代理服务器使用的协议。常见的值有`http`和`https`。
- `<host>`：代理服务器的主机名或IP地址。
- `<port>`：代理服务器的端口号。
- `<username>`：连接代理服务器时使用的用户名（如果代理服务器需要身份验证）。
- `<password>`：连接代理服务器时使用的密码（如果代理服务器需要身份验证）。
- `<nonProxyHosts>`：指定不需要通过代理服务器访问的主机列表。可以使用`|`分隔多个主机，支持通配符`*`。

当Maven需要访问网络资源时，它会按照以下顺序查找匹配的代理配置：

- 如果有激活的代理配置（`<active>`为`true`），且目标主机不在`<nonProxyHosts>`列表中，则使用该代理。
- 如果有多个激活的代理配置满足条件，则按照它们在`<proxies>`中的顺序，使用第一个匹配的代理。
- 如果没有找到匹配的代理配置，则直接连接目标主机，不使用代理。

# profiles

`Profile`允许你定义一组配置设置，并根据不同的环境或条件激活这些设置。通过使用`Profile`，可以轻松地在不同的环境中切换配置。 下面是一个`<profiles>`配置的示例：

```xml
<settings>
  ...
  <profiles>
    <profile>
      <id>development</id>
      <activation>
        <activeByDefault>true</activeByDefault>
      </activation>
      <properties>
        <db.url>jdbc:mysql://localhost/mydb</db.url>
        <db.username>devuser</db.username>
        <db.password>devpassword</db.password>
      </properties>
    </profile>
    <profile>
      <id>production</id>
      <activation>
      	<jdk>1.5</jdk>
        <os>
          <name>Windows XP</name>
          <family>Windows</family>
          <arch>x86</arch>
          <version>5.1.2600</version>
        </os>
        <property>
          <name>mavenVersion</name>
          <value>2.0.3</value>
        </property>
        <file>
          <exists>${basedir}/file2.properties</exists>
          <missing>${basedir}/file1.properties</missing>
        </file>
      </activation>
      <properties>
        <db.url>jdbc:mysql://prodserver/mydb</db.url>
        <db.username>produser</db.username>
        <db.password>prodpassword</db.password>
      </properties>
    </profile>
  </profiles>
  ...
</settings>
```

让我们详细解释`<profile>`元素的主要子元素：

- `<id>`：`Profile`的唯一标识符。它用于在命令行或其他配置中引用该`Profile`。

- ```
  <activation>
  ```

  ：定义

  ```
  Profile
  ```

  的激活条件。可以通过以下方式激活

  ```
  Profile
  ```

  ：

  - `<activeByDefault>`：如果设置为`true`，则默认激活该`Profile`。
  - `<jdk>`：根据JDK版本激活`Profile`。
  - `<os>`：根据操作系统属性激活`Profile`。
  - `<property>`：根据Maven属性的存在或值激活`Profile`。
  - `<file>`：根据文件的存在或缺失激活`Profile`。

- `<properties>`：定义`Profile`中的属性。这些属性可以在POM文件或其他配置中引用。

- `<repositories>`：定义`Profile`特定的仓库配置。

- `<pluginRepositories>`：定义`Profile`特定的插件仓库配置。

Profile配置可以在多个级别（如项目的POM文件、用户的settings.xml文件、全局的settings.xml文件）中定义。当多个级别的Profile配置存在冲突时，会按照优先级进行合并和覆盖。 优先级：POM文件 > 用户目录的settings > maven程序conf目录的settings

# activeProfiles

`<activeProfiles>`部分用于显式激活指定的Profile。通过在`<activeProfiles>`中列出Profile的`ID`，可以强制Maven在构建过程中使用这些Profile的配置，而无需通过其他方式（如命令行参数或激活条件）来激活它们。 下面是一个`<activeProfiles>`配置的示例：

```xml
<settings>
  ...
  <activeProfiles>
    <activeProfile>development</activeProfile>
    <activeProfile>ci-server</activeProfile>
  </activeProfiles>
  ...
</settings>
```

所有在`<activeProfiles>`中列出的Profile都会被激活，无论它们在settings.xml文件中的定义顺序如何。 如果多个激活的Profile具有相同的属性或元素，则后面的Profile会覆盖前面的Profile的配置。这意味着如果`development`和`ci-server`都定义了相同的属性，则`ci-server`中的属性值会覆盖`development`中的属性值。 如果激活的Profile之间没有冲突的配置，则它们的配置会被合并应用于构建过程。

# 配置了阿里云镜像的settings

复制下来替换掉自己本地的settings文件即可，包含了常用的配置，如需添加配置可以研究下这篇文章，发现错误请评论区指正，谢谢

```xml
<?xml version="1.0" encoding="UTF-8"?>

<!--
Licensed to the Apache Software Foundation (ASF) under one
or more contributor license agreements.  See the NOTICE file
distributed with this work for additional information
regarding copyright ownership.  The ASF licenses this file
to you under the Apache License, Version 2.0 (the
"License"); you may not use this file except in compliance
with the License.  You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing,
software distributed under the License is distributed on an
"AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
KIND, either express or implied.  See the License for the
specific language governing permissions and limitations
under the License.
-->
<settings xmlns="http://maven.apache.org/SETTINGS/1.2.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.2.0 https://maven.apache.org/xsd/settings-1.2.0.xsd">

  <localRepository>D:/maven_repository/</localRepository>

  <interactiveMode>true</interactiveMode>

  <offline>false</offline>

  <pluginGroups>

  </pluginGroups>

  <proxies>

  </proxies>

  <servers>

  </servers>

  <mirrors>
    <mirror>
      <id>aliyunmaven_central</id>
      <mirrorOf>central</mirrorOf>
      <name>阿里云公共仓库</name>
      <url>https://maven.aliyun.com/repository/central</url>
    </mirror>
    <mirror>
      <id>aliyunmaven_public</id>
      <mirrorOf>public</mirrorOf>
      <name>阿里云公共仓库</name>
      <url>https://maven.aliyun.com/repository/public</url>
    </mirror>
    <mirror>
      <id>aliyunmaven_apache-snapshots</id>
      <mirrorOf>apache-snapshots</mirrorOf>
      <name>阿里云公共仓库</name>
      <url>https://maven.aliyun.com/repository/apache-snapshots</url>
    </mirror>
  </mirrors>

  <profiles>
    <profile>
      <id>aliyun_spring</id>
      <activation>
        <activeByDefault>true</activeByDefault>
      </activation>
      <repository>
        <id>spring</id>
        <url>https://maven.aliyun.com/repository/spring</url>
        <releases>
          <enabled>true</enabled>
        </releases>
        <snapshots>
          <enabled>true</enabled>
        </snapshots>
      </repository>
    </profile>
  </profiles>

  <activeProfiles>
    <activeProfile>aliyun_spring</activeProfile>
  </activeProfiles>
</settings>
```