---
layout: post
title:  "将swagger2接口 导出成文档 文档类型可以为html pdf"
date:   2019-09-24 21:03:36 +0530
categories: swagger2 导出文档 html pdf
---
今天正好需要整理接口文档，所以就想偷个懒看看能否通过swagger来导出，网上查看了一些前辈们的资料发现还是蛮方便的，这里也留个足迹方便记忆。
首选说使用swagger2markup 导出adoc文档，我们需要依赖引入，这中间可能也会涉及到一些jar包冲突的问题，网上都能搜到可以自行解决（建议google搜）。

这里主要看 springfox-swagger2 依赖里的 swagger-models swagger-core的版本，我这里将是1.5.9 我讲版本换成了1.5.16

```javascript
   <dependency>
       <groupId>io.swagger</groupId>
       <artifactId>swagger-models</artifactId>
       <version>1.5.16</version>
   </dependency>
   <dependency>
       <groupId>io.swagger</groupId>
       <artifactId>swagger-core</artifactId>
       <version>1.5.16</version>
   </dependency>
```
解决完冲突，继续往下

```javascript
    <dependency>
        <groupId>io.github.swagger2markup</groupId>
        <artifactId>swagger2markup</artifactId>
        <version>1.3.1</version>
    </dependency>
```
然后写测试类
```javascript
    @Test
    public void generateAsciiDocs() throws Exception {
        //    输出Ascii格式
        Swagger2MarkupConfig config = new Swagger2MarkupConfigBuilder()
                .withMarkupLanguage(MarkupLanguage.ASCIIDOC)
                .withOutputLanguage(Language.ZH)
                .withPathsGroupedBy(GroupBy.TAGS)
                .withGeneratedExamples()
                .withoutInlineSchema()
                .build();

        URL url = new URL("http://ip:端口/项目名/v2/api-docs");
        Swagger2MarkupConverter.from(url)
                .withConfig(config)
                .build()
                .toFolder(Paths.get("D:\\"));
    }

```
运行测试类后我们可以得到4个.adoc的文件。MarkupLanguage 可以用来设置生成的文档格式

然后再说生成html和pdf文档，需要添加的是生成ASCIIDOC所需要maven插件
```javascript
    <!--生成ASCIIDOC-->
    <plugin>
        <groupId>io.github.swagger2markup</groupId>
        <artifactId>swagger2markup-maven-plugin</artifactId>
        <version>1.2.0</version>
        <configuration>
            <swaggerInput>http://ip:当前项目端口/v2/api-docs</swaggerInput>
            <outputDir>src/docs/asciidoc/generated</outputDir>
            <config>
                <!-- MarkupLanguage 可以用来设置生成的文档格式 -->
                <swagger2markup.markupLanguage>ASCIIDOC</swagger2markup.markupLanguage>
            </config>
        </configuration>
    </plugin>
```
输出PDF和HTML的maven插件
```javascript

    <plugin>
        <groupId>org.asciidoctor</groupId>
        <artifactId>asciidoctor-maven-plugin</artifactId>
        <version>1.5.3</version>

        <dependencies>
            <dependency>
                <groupId>org.asciidoctor</groupId>
                <artifactId>asciidoctorj-pdf</artifactId>
                <version>1.5.0-alpha.10.1</version>
            </dependency>
            <dependency>
                <groupId>org.jruby</groupId>
                <artifactId>jruby-complete</artifactId>
                <version>1.7.21</version>
            </dependency>
        </dependencies>

        <configuration>
            <sourceDirectory>src/docs/asciidoc/generated</sourceDirectory>
            <sourceHighlighter>coderay</sourceHighlighter>
            <attributes>
                <toc>left</toc>
            </attributes>
        </configuration>

        <executions>
            <execution>
                <id>output-html</id>
                <phase>generate-resources</phase>
                <goals>
                    <goal>process-asciidoc</goal>
                </goals>
                <configuration>
                    <backend>html5</backend>
                    <outputDirectory>src/docs/asciidoc/html</outputDirectory>
                </configuration>
            </execution>

            <execution>
                <id>output-pdf</id>
                <phase>generate-resources</phase>
                <goals>
                    <goal>process-asciidoc</goal>
                </goals>
                <configuration>
                    <backend>pdf</backend>
                    <outputDirectory>src/docs/asciidoc/pdf</outputDirectory>
                </configuration>
            </execution>
        </executions>
    </plugin>
```
配置好后首先需要运行asciidoctor-maven-plugin插件的命令去生成adoc文件,再运行generate-resources命令输出静态文档，因为该插件是根据adoc文件生成的PDF和HTML，所以顺序不能错，最后输出的PDF文件会在src/docs/asciidoc/pdf目录下面。

//生成adoc文件
mvn asciidoctor:process-asciidoc
//生成pdf和html
mvn generate-resources
这里要顺序执行，我在执行这第一个命令的时候发现了一个问题 控制台报 "io/console not supported; tty will not be manipulated"，网上搜了一下没找到解决的办法，可能我搜的不够仔细，要是有朋友知道
什么原因可以告知一下，反正第一个命令是为了生存.adoc的文件，所以我用第一步测试类生成了，然后执行命令2 ，顺利生成了html和pdf。

至此就顺利生成了需要的接口文档了。


