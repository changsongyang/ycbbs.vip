Tomcat 是 Java Web 最流行的容器之一，Docker 安装 `Tomcat` 有两种方法

我们以当前最新的版本 `9.0.8` 安装为例

因为 `Tomcat` 需要 Java 环境，我们使用的是 `jre8`

## 1. docker pull tomcat ##

这种方法非常适合只需要使用 `Tomcat` 的开发者

1、  查找 Docker Hub 上的 tomcat 镜像
    
```
    [root@localhost ~]# docker search tomcat
    NAME     DESCRIPTION                          OFFICIAL   
    tomcat   Apache Tomcat is an open source ...  [OK]  
    ...
```
    
    有很多版本，我们选择官方的 `tomcat`
2、  拉取官方的镜像,标签为 `9.0.8-jre8`
    
```
    [root@localhost ~]# docker pull tomcat:9.0.8-jre8
    9.0.8-jre8: Pulling from library/tomcat
```
3、  稍等片刻，就能在本地镜像列表里看到 `tomcat` 了
    
```
    [root@localhost ~]# docker images tomcat
    REPOSITORY   TAG           IMAGE ID         CREATED        SIZE
    tomcat       9.0.8-jre8    fffeb191ae8c     2 weeks ago    465.6 MB
```
    
    一个 `Tomcat` 容器 400m ....
4、  通过 Dockerfile 构建

可以使用 `Dockerfile` 定制自己的 Tomcat 容器

1、  创建目录 `tomcat` ,用于存放后面的相关东西
    
```
    [root@localhost ~]# mkdir -p ~/tomcat/webapps ~/tomcat/logs ~/tomcat/conf
```
    
    <table> 
     <thead> 
      <tr> 
       <th align="left">目录</th> 
       <th align="left">说明</th> 
      </tr> 
     </thead> 
     <tbody> 
      <tr> 
       <td align="left">webapps</td> 
       <td align="left">将映射为 tomcat 容器配置的应用程序目录</td> 
      </tr> 
      <tr> 
       <td align="left">logs</td> 
       <td align="left">将映射为 tomcat 容器的日志目录</td> 
      </tr> 
      <tr> 
       <td align="left">conf</td> 
       <td align="left">该目录下的配置文件将映射为 tomcat 容器的配置文件</td> 
      </tr> 
     </tbody> 
    </table>
2、  进入 `tomcat`目录，创建 `Dockerfile`
    
```
    [root@localhost ~]# cd tomcat
    [root@localhost tomcat]# vi Dockerfile
```
    
    复制并粘贴以下内容：
    
```
    FROM openjdk:8-jre
    
    ENV CATALINA_HOME /usr/local/tomcat
    ENV PATH $CATALINA_HOME/bin:$PATH
    RUN mkdir -p "$CATALINA_HOME"
    WORKDIR $CATALINA_HOME
    
    # let "Tomcat Native" live somewhere isolated
    ENV TOMCAT_NATIVE_LIBDIR $CATALINA_HOME/native-jni-lib
    ENV LD_LIBRARY_PATH ${LD_LIBRARY_PATH:+$LD_LIBRARY_PATH:}$TOMCAT_NATIVE_LIBDIR
    
    # runtime dependencies for Tomcat Native Libraries
    # Tomcat Native 1.2+ requires a newer version of OpenSSL than debian:jessie has available
    # > checking OpenSSL library version >= 1.0.2...
    # > configure: error: Your version of OpenSSL is not compatible with this version of tcnative
    # see http://tomcat.10.x6.nabble.com/VOTE-Release-Apache-Tomcat-8-0-32-tp5046007p5046024.html (and following discussion)
    # and https://github.com/docker-library/tomcat/pull/31
    ENV OPENSSL_VERSION 1.1.0f-3+deb9u2
    RUN set -ex; \
        currentVersion="$(dpkg-query --show --showformat '${Version}\n' openssl)"; \
        if dpkg --compare-versions "$currentVersion" '<<' "$OPENSSL_VERSION"; then \
            if ! grep -q stretch /etc/apt/sources.list; then \
    # only add stretch if we're not already building from within stretch
                { \
                    echo 'deb http://deb.debian.org/debian stretch main'; \
                    echo 'deb http://security.debian.org stretch/updates main'; \
                    echo 'deb http://deb.debian.org/debian stretch-updates main'; \
                } > /etc/apt/sources.list.d/stretch.list; \
                { \
    # add a negative "Pin-Priority" so that we never ever get packages from stretch unless we explicitly request them
                    echo 'Package: *'; \
                    echo 'Pin: release n=stretch*'; \
                    echo 'Pin-Priority: -10'; \
                    echo; \
    # ... except OpenSSL, which is the reason we're here
                    echo 'Package: openssl libssl*'; \
                    echo "Pin: version $OPENSSL_VERSION"; \
                    echo 'Pin-Priority: 990'; \
                } > /etc/apt/preferences.d/stretch-openssl; \
            fi; \
            apt-get update; \
            apt-get install -y --no-install-recommends openssl="$OPENSSL_VERSION"; \
            rm -rf /var/lib/apt/lists/*; \
        fi
    
    RUN apt-get update && apt-get install -y --no-install-recommends \
            libapr1 \
        && rm -rf /var/lib/apt/lists/*
    
    ENV TOMCAT_MAJOR 9
    ENV TOMCAT_VERSION 9.0.8
    
    ENV TOMCAT_TGZ_URLS \
    # https://issues.apache.org/jira/browse/INFRA-8753?focusedCommentId=14735394#comment-14735394
        https://www.apache.org/dyn/closer.cgi?action=download&filename=tomcat/tomcat-$TOMCAT_MAJOR/v$TOMCAT_VERSION/bin/apache-tomcat-$TOMCAT_VERSION.tar.gz \
    # if the version is outdated, we might have to pull from the dist/archive :/
        https://www-us.apache.org/dist/tomcat/tomcat-$TOMCAT_MAJOR/v$TOMCAT_VERSION/bin/apache-tomcat-$TOMCAT_VERSION.tar.gz \
        https://www.apache.org/dist/tomcat/tomcat-$TOMCAT_MAJOR/v$TOMCAT_VERSION/bin/apache-tomcat-$TOMCAT_VERSION.tar.gz \
        https://archive.apache.org/dist/tomcat/tomcat-$TOMCAT_MAJOR/v$TOMCAT_VERSION/bin/apache-tomcat-$TOMCAT_VERSION.tar.gz
    
    ENV TOMCAT_ASC_URLS \
        https://www.apache.org/dyn/closer.cgi?action=download&filename=tomcat/tomcat-$TOMCAT_MAJOR/v$TOMCAT_VERSION/bin/apache-tomcat-$TOMCAT_VERSION.tar.gz.asc \
    # not all the mirrors actually carry the .asc files :'(
        https://www-us.apache.org/dist/tomcat/tomcat-$TOMCAT_MAJOR/v$TOMCAT_VERSION/bin/apache-tomcat-$TOMCAT_VERSION.tar.gz.asc \
        https://www.apache.org/dist/tomcat/tomcat-$TOMCAT_MAJOR/v$TOMCAT_VERSION/bin/apache-tomcat-$TOMCAT_VERSION.tar.gz.asc \
        https://archive.apache.org/dist/tomcat/tomcat-$TOMCAT_MAJOR/v$TOMCAT_VERSION/bin/apache-tomcat-$TOMCAT_VERSION.tar.gz.asc
    
    RUN set -eux; \
        \
        savedAptMark="$(apt-mark showmanual)"; \
        apt-get update; \
        \
        apt-get install -y --no-install-recommends gnupg dirmngr; \
        \
        apt-get install -y --no-install-recommends wget ca-certificates; \
        \
        success=; \
        for url in $TOMCAT_TGZ_URLS; do \
            if wget -O tomcat.tar.gz "$url"; then \
                success=1; \
                break; \
            fi; \
        done; \
        [ -n "$success" ]; \
        \
        tar -xvf tomcat.tar.gz --strip-components=1; \
        rm bin/*.bat; \
        rm tomcat.tar.gz*; \
        \
        nativeBuildDir="$(mktemp -d)"; \
        tar -xvf bin/tomcat-native.tar.gz -C "$nativeBuildDir" --strip-components=1; \
        apt-get install -y --no-install-recommends \
            dpkg-dev \
            gcc \
            libapr1-dev \
            libssl-dev \
            make \
            "openjdk-${JAVA_VERSION%%[.~bu-]*}-jdk=$JAVA_DEBIAN_VERSION" \
        ; \
        ( \
            export CATALINA_HOME="$PWD"; \
            cd "$nativeBuildDir/native"; \
            gnuArch="$(dpkg-architecture --query DEB_BUILD_GNU_TYPE)"; \
            ./configure \
                --build="$gnuArch" \
                --libdir="$TOMCAT_NATIVE_LIBDIR" \
                --prefix="$CATALINA_HOME" \
                --with-apr="$(which apr-1-config)" \
                --with-java-home="$(docker-java-home)" \
                --with-ssl=yes; \
            make -j "$(nproc)"; \
            make install; \
        ); \
        rm -rf "$nativeBuildDir"; \
        rm bin/tomcat-native.tar.gz; \
        \
    # reset apt-mark's "manual" list so that "purge --auto-remove" will remove all build dependencies
        apt-mark auto '.*' > /dev/null; \
        [ -z "$savedAptMark" ] || apt-mark manual $savedAptMark; \
        apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false; \
        rm -rf /var/lib/apt/lists/*; \
        \
    # sh removes env vars it doesn't support (ones with periods)
    # https://github.com/docker-library/tomcat/issues/77
        find ./bin/ -name '*.sh' -exec sed -ri 's|^#!/bin/sh$|#!/usr/bin/env bash|' '{}' +
    
    # verify Tomcat Native is working properly
    RUN set -e \
        && nativeLines="$(catalina.sh configtest 2>&1)" \
        && nativeLines="$(echo "$nativeLines" | grep 'Apache Tomcat Native')" \
        && nativeLines="$(echo "$nativeLines" | sort -u)" \
        && if ! echo "$nativeLines" | grep 'INFO: Loaded APR based Apache Tomcat Native library' >&2; then \
            echo >&2 "$nativeLines"; \
            exit 1; \
        fi
    
    EXPOSE 8080
    CMD ["catalina.sh", "run"]
```
3、  使用 `docker build` 命令通过 `Dockerfile` 创建一个镜像 `my-tomcat:9.0.8-jre8`
    
```
    [root@localhost tomcat]# docker build -t my-tomcat:9.0.8-jre8 .
    Sending build context to Docker daemon 10.75 kB
    ...
```
4、  稍等片刻，创建完成后就可以在本地的镜像列表里查找到刚刚创建的镜像
    
```
    [root@localhost tomcat]# docker images my-tomcat
    REPOSITORY   TAG           IMAGE ID         CREATED        SIZE
    my-tomcat    9.0.8-jre8    d0ff57656e50     11 days ago    463.8 MB
```

## 运行 tomcat 容器 ##

首先创建 `tomcat/webapps/demo` 目录并在 `demo` 目录下放一个 `index.html` 文件，内容如下

```
<!DOCTYPE html>
<meta charset="utf-8">
<h2>Hello www.ycbbs.vip</h2>
<p>研发军团，教程 </p>
```

然后使用 `docker run` 运行 `my-tomcat` 容器

```
[root@localhost tomcat]# docker run -d --name tomcat -p 8080:8080 -v $PWD/webapps/demo:/usr/local/tomcat/webapps/demo tomcat:9.0.8-jre8
e139c23bf7bc3a4926db947e63fa0c07b771fc9b400645668945c07cb8dfd05a
```

命令说明

1、  `-p 8080:8080`
    
    将容器的 `8080` 端口映射到主机的 `8080` 端口
2、  `-v $PWD/webapps/demo:/usr/local/tomcat/webapps/demo`
    
    将主机中当前目录下的 `webapps/demo` 挂载到容器的 `/usr/local/tomcat/webapps/demo`

## 查看容器启动情况 ##

可以使用 `docker ps -a` 查看 tomcat 容器的运行状况

```
[root@localhost tomcat]# docker ps -a
CONTAINER ID   IMAGE               STATUS
e139c23bf7bc   tomcat:9.0.8-jre8   Up About a minute
```

通过浏览器访问 [http://localhost:8080/demo/][http_localhost_8080_demo]

输出如下

![img\_1.png][img_1.png]


[http_localhost_8080_demo]: http://localhost:8080/demo/
[img_1.png]: https://gitee.com/duchaochen/gongzhonghao/raw/master/个人博客文章/001-images/souyunku-web/2019/08/0803/05/25/img_1.png

希望读者能够给小编留言，也可以点击[此处扫下面二维码关注微信公众号](https://www.ycbbs.vip/?p=28 "此处扫下面二维码关注微信公众号")