```dockerignore
### Git ###  
.git  
.gitignore  
  
### IDE ###  
.idea  
*.iml  
  
### Gradle cache ###  
.gradle  
build/  
  
### Module build outputs ###  
dms-*/build/  
buildSrc/build/  
  
### Local files ###  
*.log
```


## 추가하게 된 이유
* dockerfile 로만 배포하도록 플로우가 변경되어서 docker build 시 최적화와 버그방지를 위해
