```python
package com.example.demu.global.error

import com.example.demu.global.error.exception.DemuException
import com.example.demu.global.error.exception.ErrorCode
import org.springframework.http.HttpStatus
import org.springframework.http.ResponseEntity
import org.springframework.web.bind.annotation.ExceptionHandler
import org.springframework.web.bind.annotation.RestControllerAdvice
import javax.validation.ConstraintViolationException
import org.slf4j.LoggerFactory

@RestControllerAdvice
class GlobalExceptionHandler {

    private val log = LoggerFactory.getLogger(GlobalExceptionHandler::class.java)

    @ExceptionHandler(DemuException::class)
    fun handleBusinessException(e: DemuException): ResponseEntity<ErrorResponse> {
        val errorCode = e.errorCode
        val response = ErrorResponse.of(errorCode, errorCode.message)
        e.printStackTrace()

        return ResponseEntity(response, HttpStatus.valueOf(errorCode.status))
    }

    @ExceptionHandler(ConstraintViolationException::class)
    fun handleConstraintViolationException(e: ConstraintViolationException): ResponseEntity<ErrorResponse> {
        val errorCode = ErrorCode.BAD_REQUEST
        val response = ErrorResponse.of(errorCode, errorCode.message)
        e.printStackTrace()

        return ResponseEntity(response, HttpStatus.valueOf(errorCode.status))
    }

    @ExceptionHandler(Exception::class)
    fun handleException(e: Exception): ResponseEntity<ErrorResponse> {
        val errorCode = ErrorCode.INTERNAL_SERVER_ERROR
        val response = ErrorResponse.of(errorCode, e.message ?: "Unknown error occurred")

        return ResponseEntity(response, HttpStatus.INTERNAL_SERVER_ERROR)
    }
}

```