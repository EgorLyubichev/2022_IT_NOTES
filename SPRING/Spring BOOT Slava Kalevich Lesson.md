Spring Boot.

Основные преимущества:
1. Спинг бут стартеры. Это коллекции библиотек определенного направления, подобранные друг к другу по совместимости. Т.е. библиотеки, между которыми нет конфликта.
2. Автоконфигурирование. Благодаря подключению спринг бут, приложение имеет пакеты конфигураций, и благодаря наличию ресурсных файлов и конфигураций, приложение настраивается самостоятельно.
3. Spring Initializr. Сайт, который собирает стартовый набор пакетов, библиотек и конфигураций для необходимого приложения.
4. Наличие веб-сервисов сразу же из под капота. Т.е. спринг бут обслуживает как сам спринг, так и все те сервисы, необходимые для инфраструктуры приложения вокруг спринга. 

5. Spring CLI (command line interface). Spring позволяет в запущенном приложении выполнять джаву в ран-тайме.

# - - - Стартеры - - -
 - Самый главный стартер - Spring-boot-starter (dependencies: spring-boot, spring-context, spring-beans).

 - Spring-boot-starter-actuator (dependencies: spring-boot-actuator, micrometer-core). Это такая штука, с помощью которой будет собираться вся статистика, бэнчмарки и т.д. Т.е. актуатор будет собирать статистику по перформансу, анализировать сколько оперативы, сколько потоков использовалось, сколько по времени оно отрабатывало, а также будет создавать автоматически так называемые хэлс-енд-поинты для нашего приложения. 

	**Endpoint** - это в контроллере вот этот маппинг (get запрос, post запрос и т.п. на определенный URL). Spring boot автоматически создает несколько урлов, перейдя по которым можно понять: какя версия приложения работает, работает ли она.
	
	**Тест производительности, бенчмарк (англ. benchmark)** — контрольная задача, необходимая для определения сравнительных характеристик производительности компьютерной системы. Иногда бенчмарками также называются программы, которые тестируют время автономной работы ноутбуков и карманных персональных компьютеров, радиус действия беспроводной сети, пропускную способность каналов передачи данных, амплитудно-частотную характеристику звукового тракта и другие доступные для измерения характеристики, напрямую не связанные с производительностью. Бенчмарки используются для сравнения производительности компьютеров и часто являются критерием для выбора компонента того или иного производителя. Кроме того, успешное прохождение ряда тестов является свидетельством стабильности системы в штатном и в разогнанном режимах.
	
 - Spring-boot-starter-aop (spring-aop, aspectjrt, aspectjvieawer)
 
На уроке качали в pom.xml:  

spring-boot-starter  
spring-boot-starter-aop  
spring-boot-starter-web  
spring-boot-starter-jdbc  
(постгрес зависимость осталась отдельно)  
(inject осталось)  

```xml
<parent>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-parent</artifactId>
  <version>2.7.3</version>
  <relativePath/>
</parent>
```
Родительский стартер. Это есть pom.xml, он наследует значение от super pom.xml, внутри которого прописаны все библиотеки с версиями, которые доступны в спринг буте. Далее в зависимостях spring-boot-starter(ов) мы уже не указываем тег с версиями - он подтягивается от родительского стартера. 

# - - - Конфигурация Spring-boot-starter - - -

Создаем класс в папке `by.lev` - `SpringBootStarter`
Создаем в нем main.
Аннотации:
```java
@SpringBootApplication(scanBasePackages = "by.lev", exclude = {DataSourceAutoConfiguration.class})
@EnableMVC
@EnableAspectJAutoProxy(proxyTargetClass = true)
@Import({ConnectionPoolConfig.class, DatabaseProperties.class})
```
В методе main:
```java
	SpringApplication.run(SpringBootStarter.class, args);
```

Это класс, кот. будет стартовать наше приложение, и мы должны сказать спринг-буту где отправная точка (т.е. указываем этот же класс в методе main, и он будет считаться отправной точкой).

Аннотация `@SpringBootApplication` содержит внутри себя `@SpringBootConfiguration, @EnableAutoConfiguration, @ComponentScan`

(!) В данном случае мы прописали `@Import({ConnectionPoolConfig.class, DatabaseProperties.class})`, а также включили `exclude = {DataSourceAutoConfiguration.class}`. Всё это потому, что на данном этапе проекта у нас: 1) есть класс с конфигурацией подключения к базе данных, и класс с конфигурацией пула соединений `(ConnectionPoolConfig.class, DatabaseProperties.class)`, 2) нет файла с расширением .yml, где должны быть прописаны такие конфигурации. По умолчанию спринг бут обращается к своему классу DataSourceAutoConfiguration.class, который в свою очередь смотрит на файл с расширением `.yml`, где должны лежать настройки для спринг бута.

(!) Для работы логгера у нас в проекте есть файл `log4j.properties`, где прописаны конфигурации логгера.
(!) Также в данной версии проекта прописан аспект, где настроен логгер.

Dependency `hikari` в `pom.xml` оставили с последней версией.
Lombok поставляется со `spring-boot-starter`.

`JavaxInject` нам больше не нужен.

`Log4j` оставили

Следующий шаг - нам надо отказаться полностью от проперти файлов и перейти на конфигурирование через аннотации.
В папке resources создаем файл `application.yml`. Внутри этого файла мы можем описывать конфигурацию. Она будет автоматически вычитываться, сетиться в бины. 
Ключевое слово в этом файле `spring` - оно является стартовым, именно его спринг будет искать, чтобы вычитать настройки.

Содержание файла `application.yml`:
```yaml

spring:
  datasource:
    hikari:
	  jdbc-url: "jdbc:postgresql://localhost:5432/jd2"
	  driver-class-name: org.postgresql.Driver
	  maximum-pool-size: 10
    url: "jdbc:postgresql://localhost:5432/jd2"
    username: postgres
    password: 1234
server:
  port: 8082
```
	 
	 
`username` & `password` можно указать на уровне `datasource` или на уровне `connectionpool` (hikari).
	 
После того, как мы это прописали, мы можем удалить `exclude = {DataSourceAutoConfiguration.class}`
Т.к. мы написали `jdbc-url: jdbc:postgresql://localhost:5432/jd2`, спринг будет понимать, что мы работаем с postgresql, и сам будет подтягивать драйвер.
Также теперь нам не нужен класс `DatabaseProperties` - удаляем. Файл `database.properties` остался.
Настройку (метод `hikariDatasource`) в классе `ConnectionPoolConfig` мы тоже удалаяем.
После этих действий убираем `exclude = {DataSourceAutoConfiguration.class}`.

Теперь мы можем видеть, что прописанные в классе `ConnectionPoolConfig` бины `jdbcTemplate` & `nameParameterJdbcTemplate` работают с новыми настройками (параметр `DataSource dataSource` подтягивается). `HikariDataSource`, который проинициализируется автоматически спрингом у нас подтягивается.

Что-то пошло не так. В результате вернули зависимость Lombok.
Далее мы убрали бины jdbcTemplate & nameParameterJdbcTemplate.
Мы удаляем ручную вычитку из проперти файлов, ручное создание DataSource объекта (jdbcTemplate & nameParameterJdbcTemplate). Мы делаем сейчас так, чтобы эти объекты автоматически были инициализированы DataSource'ом, который сам же создал спринг и потом мы везде могли их переиспользовать. Для автоконфигурации мы убрали `exclude = {DataSourceAutoConfiguration.class}`, и тем самым сказали: "Спринг, автоконфигурируй подключение так как ты считаешь нужным, на основании файла, который мы тебе предоставили (`application.yml`)


# - - - Замена баннера Spring на свой - - -
В папке resources создаем файл banner.txt
Заходим на сайт: https://devops.datenkollektiv.de/banner.txt/index.html
Создаем на сайте баннер, копируем его как есть и вставляем в файл banner.txt. Он подтянется сам.

// commit: Spring Boot (06.09.2022)


# - - - Spring Boot Actuator - - -
После того, как мы добавили зависимость `spring-boot-starter-actuator`, нам стала доступна ссылка на http://localhost:8082/actuator. 