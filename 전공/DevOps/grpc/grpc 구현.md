
플러그인
```
id("com.google.protobuf") version PluginVersions.GRPC_PLUGIN_VERSION
```


의존성 빌드시 자동으로 protoc host 컴퓨터에 설치 로직
```
protobuf {  
    protoc {  
        artifact = 'com.google.protobuf:protoc:3.25.1'  
    }  
    plugins {  
        grpc {  
            artifact = 'io.grpc:protoc-gen-grpc-java:1.64.0'  
        }  
    }  
    generateProtoTasks {  
        all().each { task ->  
            task.plugins {  
                grpc {}  
            }  
        }  
    }  
}
```


의존성
```
implementation 'io.grpc:grpc-protobuf:1.64.0'  
implementation 'io.grpc:grpc-stub:1.64.0'  
implementation 'io.grpc:grpc-netty-shaded:1.64.0'
```


proto 파일
```
syntax = "proto3";  
  
import "google/protobuf/empty.proto";  
import "google/protobuf/wrappers.proto";  
  
option java_package = "team.aliens.notification";  
option java_multiple_files = true;  
  
  
service NotificationService {  
  rpc checkDeviceTokenByUserId(UserId) returns (google.protobuf.BoolValue);  
  rpc deleteDeviceTokenByUserId(UserId) returns (google.protobuf.Empty);  
  rpc saveDeviceToken(DeviceTokenInfo) returns (DeviceTokenInfo);  
}  
  
message UserId {  
  bytes userId = 1;  
}  
  
message DeviceTokenInfo {  
  bytes id = 1;  
  bytes userId = 2;  
  bytes schoolId = 3;  
  string token = 4;  
}
```

proto 파일만 빌드(java,kotlin 빌드 X)
```
./gradlew :{플러그인 의존성 있는 모듈이름}:generateProto
```

