---
title:  "Java Refactoring"
date:   2015-02-27 11:00:00
categories: refactoring
---

# 1장 깨끗한 코드

`MemoDto`에 첨부된 파일의 경로명과 파일명이 유효한지 검사하고, ObjectStorage에서 해당 `memo`의 첨부파일을 다운로드 받는 코드다.
```java
if( isValidFileName(memo.getFilePath(), memo.getFileName()) ) {
    ObjectStorage.download(request, response, memo);
}
```
위 코드는 아래와 같은 문제점이 있다.

 - isValidFileName는 여러 곳에서 사용될 가능성이 있다.
 - 파라미터가 복잡하다.


`MemoDto`의 멤버값 유효성 검사는 `MemoDto`에서 하면 간편하다.
```java
if( memo.isValidFileName() ) {
    ObjectStorage.download(request, response, memo);
}
```

이름도 `isValidFileName()` 보단 `hasValidFileName()`가 낫겠다.
```java
if( memo.hasValidFileName() ) {
    ObjectStorage.download(request, response, memo);
}
```

`MemoDto`뿐만 아니라 `NoteDto`도 같은 로직이 필요하다. 그리고 `ObjectStorage` 클래스나 다른 `FileDownload` 클래스에서 유효성 검사를 하고 싶은 경우
```java
if( ObjectStorage.hasValidFile(memo) ) {
    ObjectStorage.download(request, response, memo);
}
if( ObjectStorage.hasValidFile(note) ) {
    ObjectStorage.download(request, response, note);
}
```

기존 `MemoDto`와 `NoteDto`는 첨부파일의 경로명과 파일명 String 변수가 있다.
```java
public class MemoDto {
    private String fileName;
    private String filePath;
}
public class NoteDto {
    private String fileName;
    private String filePath;
}
```

공통으로 있기 때문에 `AttachedFile` 이라는 클래스를 만들어서 상속 받아 구현할 수도 있다. 
```java
public class AttachedFile {
    private String fileName;
    private String filePath;   
}
public class MemoDto extends AttachedFile {
}
public class NoteDto extends AttachedFile {
}
```

하지만 is-a 관계가 뭔가 어색하다. 이런 경우 인터페이스로 만들자.
```java
public interface FileItem {
    String getFileName();
    String getFilePath();
}
public class MemoDto implements FileItem {
}
public class NoteDto implements FileItem {
}
```

이제 `FileItem` 인터페이스를 가지고 있는 클래스는 모두 유효성 검사를 할 수 있다.
```java
    public boolean hasValidFile(FileItem fileItem) {
        String filePath = fileItem.getFilePath();
        String fileName = fileItem.getFileName();
        return (filePath != null && filePath.trim().length() > 0 && fileName != null && fileName.trim().length() > 0)
            ? true : false;
    }
```



