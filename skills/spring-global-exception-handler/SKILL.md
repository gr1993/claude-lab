---
name: spring-global-exception-handler
description: Spring Boot 프로젝트에 전역 예외 처리기를 생성하는 skill. "전역 예외 처리", "GlobalExceptionHandler", "에러 핸들링", "예외 처리기 만들어줘", "오류 응답 통일" 요청 시 사용. ErrorResponse DTO와 GlobalExceptionHandler를 생성하여 일관된 에러 응답 구조를 제공.
---

# Spring Global Exception Handler

Spring Boot 프로젝트에 전역 예외 처리기를 생성한다.

## 생성 파일

1. **ErrorResponse.java** - 통일된 에러 응답 DTO
2. **GlobalExceptionHandler.java** - 전역 예외 처리기

## 사용법

1. 프로젝트의 기본 패키지 구조 확인
2. `global.exception` 패키지에 두 파일 생성
3. 필요시 커스텀 예외 클래스 추가

## ErrorResponse 구조

```java
{
    "timestamp": "2024-01-01T10:00:00",
    "status": 400,
    "error": "Bad Request",
    "message": "상세 에러 메시지"
}
```

## 기본 처리 예외

| 예외 | HTTP 상태 | 용도 |
|------|-----------|------|
| `IllegalArgumentException` | 400 | 잘못된 파라미터 |
| `MethodArgumentNotValidException` | 400 | Bean Validation 실패 |
| `Exception` | 500 | 기타 서버 오류 |

## 생성 코드

### ErrorResponse.java

```java
package {{basePackage}}.global.exception;

import io.swagger.v3.oas.annotations.media.Schema;
import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;

import java.time.LocalDateTime;

@Getter
@NoArgsConstructor
@AllArgsConstructor
@Builder
@Schema(description = "에러 응답")
public class ErrorResponse {

    @Schema(description = "발생 시각", example = "2024-01-01T10:00:00")
    private LocalDateTime timestamp;

    @Schema(description = "HTTP 상태 코드", example = "400")
    private int status;

    @Schema(description = "에러 유형", example = "Bad Request")
    private String error;

    @Schema(description = "에러 메시지", example = "잘못된 요청입니다.")
    private String message;
}
```

### GlobalExceptionHandler.java

```java
package {{basePackage}}.global.exception;

import lombok.extern.slf4j.Slf4j;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.MethodArgumentNotValidException;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

import java.time.LocalDateTime;
import java.util.stream.Collectors;

@RestControllerAdvice
@Slf4j
public class GlobalExceptionHandler {

    @ExceptionHandler(IllegalArgumentException.class)
    public ResponseEntity<ErrorResponse> handleIllegalArgumentException(IllegalArgumentException e) {
        log.warn("IllegalArgumentException: {}", e.getMessage());

        ErrorResponse response = ErrorResponse.builder()
                .timestamp(LocalDateTime.now())
                .status(HttpStatus.BAD_REQUEST.value())
                .error("Bad Request")
                .message(e.getMessage())
                .build();

        return ResponseEntity.badRequest().body(response);
    }

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ErrorResponse> handleValidationException(MethodArgumentNotValidException e) {
        String message = e.getBindingResult().getFieldErrors().stream()
                .map(error -> error.getField() + ": " + error.getDefaultMessage())
                .collect(Collectors.joining(", "));

        log.warn("Validation failed: {}", message);

        ErrorResponse response = ErrorResponse.builder()
                .timestamp(LocalDateTime.now())
                .status(HttpStatus.BAD_REQUEST.value())
                .error("Bad Request")
                .message(message)
                .build();

        return ResponseEntity.badRequest().body(response);
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorResponse> handleException(Exception e) {
        log.error("Unexpected error occurred", e);

        ErrorResponse response = ErrorResponse.builder()
                .timestamp(LocalDateTime.now())
                .status(HttpStatus.INTERNAL_SERVER_ERROR.value())
                .error("Internal Server Error")
                .message("서버 오류가 발생했습니다.")
                .build();

        return ResponseEntity.internalServerError().body(response);
    }
}
```

## 커스텀 예외 추가 예시

새로운 예외 타입 추가 시:

```java
// 1. 커스텀 예외 클래스 생성
public class ResourceNotFoundException extends RuntimeException {
    public ResourceNotFoundException(String message) {
        super(message);
    }
}

// 2. GlobalExceptionHandler에 핸들러 추가
@ExceptionHandler(ResourceNotFoundException.class)
public ResponseEntity<ErrorResponse> handleResourceNotFoundException(ResourceNotFoundException e) {
    log.warn("ResourceNotFoundException: {}", e.getMessage());

    ErrorResponse response = ErrorResponse.builder()
            .timestamp(LocalDateTime.now())
            .status(HttpStatus.NOT_FOUND.value())
            .error("Not Found")
            .message(e.getMessage())
            .build();

    return ResponseEntity.status(HttpStatus.NOT_FOUND).body(response);
}
```

## 필수 의존성

```groovy
// build.gradle
implementation 'org.springframework.boot:spring-boot-starter-web'
implementation 'org.springframework.boot:spring-boot-starter-validation'
implementation 'org.springdoc:springdoc-openapi-starter-webmvc-ui'
compileOnly 'org.projectlombok:lombok'
annotationProcessor 'org.projectlombok:lombok'
```
