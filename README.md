# swagger-functional-webflux

A friendly kotlin library used to validate spring functional API endpoints against a Swagger 2.0 specification. Great with webflux functional.
It works happily with any JVM language including Java 8. 

## Prequisites

Java 8

## Install

### Maven

```xml
<dependency>
    <groupId>io.github.cdimascio</groupId>
    <artifactId>swagger-spring-functional</artifactId>
    <version>0.6.2</version>
</dependency>
```

### Gradle

```
compile 'io.github.cdimascio:swagger-spring-functional:0.6.2'
```

For sbt, grape, ivy and more, see [here](https://search.maven.org/#artifactdetails%7Cio.github.cdimascio%7Cswagger-spring-functional%7C0.6.2%7Cjar)

## Usage

```kotlin
import io.github.cdimascio.swagger.Validate
```

### Validate

#### Kotlin

```kotlin
Validate.request(req) {
    ok().body(Mono.just(listOf("carmine", "alex", "eliana")))
}
```

### Java 8
```java
ArrayList<String> users = new ArrayList<String>() {{
    add("carmine");
    add("alex");
    add("eliana");
}};
Validate.INSTANCE.request(req, () -> ServerResponse.ok().body(fromObject(users));
```

#### with body

#### Kotlin

```kotlin
Validate.request(req).withBody(User::class.java) { 
	body -> ok().body(Mono.just(body)) // body is deserialized as User
}
```

### Java 8

```java
Validate.INSTANCE.request(null).withBody(User.class, user -> ServerResponse.ok().body(fromObject(user)));
```


## Example


Let's say you function to create `/users`

#### Set up routes:

```kotlin
class Routes(private val userHandler: UserHandler) {
	fun router() = router {
        "/api".nest {
            accept(APPLICATION_JSON).nest {
                POST("/users", userHandler::create)
            }
            accept(TEXT_EVENT_STREAM).nest {
                GET("/users", userHandler::findAll)
            }

        }
    }
}
```

#### validate handler functions
```kotlin
class UserHandler {
	
	fun findAll(req: ServerRequest): Mono<ServerResponse> {
		return Validate.request(req) {
			ok().body(Mono.just(listOf("carmine", "alex", "eliana")))
		}
	}
	 
	fun create(req: ServerRequest): Mono<ServerResponse> {
	   return Validate.request(req).withBody(User::class.java) {
	   		// it is the request body deserialized as User
			ok().body(Mono.just(it))
		}
	}
}
```

## License

[Apache 2.0](https://www.apache.org/licenses/LICENSE-2.0)



