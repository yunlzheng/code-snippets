# Gradle

> Java自动化构建工具

## Checkstyle

build.gradle

```Groovy
apply plugin: 'checkstyle'

checkstyle {
    configFile = new File("${project.projectDir}/checkstyle_rules.xml")
    checkstyleMain.exclude "**/src/test"
}
```

checkstyle_rules.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE module PUBLIC "-//Puppy Crawl//DTD Check Configuration 1.3//EN" "http://www.puppycrawl.com/dtds/configuration_1_3.dtd">
<module name="Checker">
  <property name="severity" value="error"/>
  <module name="TreeWalker">
    <property name="cacheFile" value="checkstyle-cachefile"/>
    <property name="tabWidth" value="4"/>
    <module name="ConstantName"/>
    <module name="EmptyBlock"/>
    <module name="EmptyForIteratorPad"/>
    <module name="EqualsHashCode"/>
    <module name="IllegalImport"/>
    <module name="IllegalInstantiation"/>
    <module name="IllegalThrows"/>
    <module name="InnerAssignment"/>
    <module name="LeftCurly">
      <property name="tokens" value="CLASS_DEF,INTERFACE_DEF,METHOD_DEF,CTOR_DEF"/>
    </module>
    <module name="LeftCurly">
      <property name="tokens" value="LITERAL_CATCH,LITERAL_DO,LITERAL_ELSE,LITERAL_FINALLY,LITERAL_FOR,LITERAL_IF,LITERAL_SWITCH,LITERAL_SYNCHRONIZED,LITERAL_TRY,LITERAL_WHILE"/>
    </module>
    <module name="OuterTypeNumber"/>
    <module name="LineLength">
      <property name="ignorePattern" value="^ *\* *[^ ]+$"/>
      <property name="max" value="140"/>
    </module>
    <module name="LocalVariableName"/>
    <module name="MemberName"/>
    <module name="MethodLength"/>
    <module name="MethodName"/>
    <module name="MethodParamPad"/>
    <module name="ModifierOrder"/>
    <module name="NeedBraces"/>
    <module name="NoWhitespaceAfter">
      <property name="tokens" value="ARRAY_INIT, BNOT, DEC, DOT, INC, LNOT, UNARY_MINUS, UNARY_PLUS"/>
    </module>
    <module name="NoWhitespaceBefore"/>
    <module name="NoWhitespaceBefore">
      <property name="allowLineBreaks" value="true"/>
      <property name="tokens" value="DOT"/>
    </module>
    <module name="OperatorWrap"/>
    <module name="OperatorWrap">
      <property name="option" value="eol"/>
      <property name="tokens" value="ASSIGN, DIV_ASSIGN, PLUS_ASSIGN, MINUS_ASSIGN, STAR_ASSIGN, MOD_ASSIGN, SR_ASSIGN, BSR_ASSIGN, SL_ASSIGN, BXOR_ASSIGN, BOR_ASSIGN, BAND_ASSIGN"/>
    </module>
    <module name="PackageName"/>
    <module name="ParameterName"/>
    <module name="ParameterNumber">
      <property name="id" value="paramNum"/>
      <property name="max" value="9"/>
    </module>
    <module name="ParenPad"/>
    <module name="TypecastParenPad"/>
    <module name="RedundantImport"/>
    <module name="RedundantModifier"/>
    <module name="RightCurly"/>
    <module name="SimplifyBooleanExpression"/>
    <module name="SimplifyBooleanReturn"/>
    <module name="StaticVariableName"/>
    <module name="TypeName"/>
    <module name="UnusedImports"/>
    <module name="UpperEll"/>
    <module name="VisibilityModifier">
      <property name="packageAllowed" value="true"/>
      <property name="protectedAllowed" value="true"/>
    </module>
    <module name="WhitespaceAfter"/>
    <module name="WhitespaceAround"/>
    <module name="GenericWhitespace"/>
    <module name="FinalClass"/>
    <module name="MissingSwitchDefault"/>
    <module name="EqualsAvoidNull"/>
    <module name="NestedIfDepth">
      <property name="max" value="3"/>
    </module>
    <module name="NestedTryDepth"/>
    <module name="ExplicitInitialization"/>
    <module name="AnnotationUseStyle"/>
    <module name="MissingDeprecated"/>
    <module name="MissingOverride">
      <property name="javaFiveCompatibility" value="true"/>
    </module>
    <module name="PackageAnnotation"/>
  </module>
  <module name="SuppressionFilter">
    <property name="file" value="${basedir}/checkstyle_suppressions.xml"/>
  </module>
  <module name="Translation">
    <property name="severity" value="warning"/>
  </module>
  <module name="FileTabCharacter"/>
  <module name="FileLength"/>
</module>

```

## Swagger Docs

build.gradle

```Groovy
buildscript {
    dependencies {
        classpath 'org.asciidoctor:asciidoctor-gradle-plugin:1.5.3'
        classpath 'org.asciidoctor:asciidoctorj-pdf:1.5.0-alpha.10.1'
        classpath 'io.github.swagger2markup:swagger2markup-spring-restdocs-ext:1.0.0'
        classpath 'io.github.swagger2markup:swagger2markup-gradle-plugin:1.1.0'
    }
}

apply plugin: 'org.asciidoctor.convert'
apply plugin: 'io.github.swagger2markup'

ext {
    asciiDocOutputDir = file("${buildDir}/asciidoc/generated")
    snippetsOutputDir = file("${buildDir}/asciidoc/snippets")
}

convertSwagger2markup {
    swaggerInput "http://localhost:9910/v2/api-docs?group=Api%20Document"
    outputDir asciiDocOutputDir
    config = [
            'swagger2markup.pathsGroupedBy'                          : 'TAGS',
            'swagger2markup.extensions.springRestDocs.snippetBaseUri': snippetsOutputDir.getAbsolutePath()]
}

asciidoctor {
    dependsOn convertSwagger2markup
    sources {
        include 'index.adoc'
    }
    backends = ['html5', 'pdf']
    attributes = [
            doctype    : 'book',
            toc        : 'left',
            toclevels  : '3',
            numbered   : '',
            sectlinks  : '',
            sectanchors: '',
            hardbreaks : '',
            generated  : asciiDocOutputDir
    ]
}
```

```
./gradlew asciidoctor
```

## Linqubase

```
buildscript {
    dependencies {
        classpath "org.liquibase:liquibase-gradle-plugin:1.2.1"
    }
}

liquibase {
    activities {
        changeLog {
            changeLogFile "src/main/resources/db/changelog/main-changelog.xml"
            url "jdbc:mysql://localhost:3306/builderdb?useUnicode=true&characterEncoding=utf-8"
            username "root"
            password "123456"
        }
        main {
            changeLogFile "src/main/resources/db/changelog/main-changelog.xml"
            url "jdbc:mysql://localhost:3307/builderdb?useUnicode=true&characterEncoding=utf-8"
            username "root"
            password "123456"
            referenceUrl "jdbc:mysql://localhost:3306/builderdb?useUnicode=true&characterEncoding=utf-8"
            referenceUsername "root"
            referencePassword "123456"
        }
        runList = project.ext.runList
    }
}
```

```
./gradlew diffChangelog
```

## Jacoco

```
apply plugin: "jacoco"

jacoco {
    toolVersion = "0.7.6.201602180812"
    reportsDir = file("$buildDir/customJacocoReportDir")
}
```