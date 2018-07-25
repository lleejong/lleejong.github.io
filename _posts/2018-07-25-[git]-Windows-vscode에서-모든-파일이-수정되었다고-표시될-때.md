---
layout: post
title: [Git] Windows vscode에서 모든 파일이 수정되었다고 표시될 때
---
## Windows vscode에서 수정된 사항이 없는데도 git에서 수정되었다고 표시 될 .

vscode에서 git repository에서 방금 받은 프로젝트의 모든 파일 혹은 일부 파일이 수정되었다고 표시되는 경우가 간혹 발생한다.   
![vscode marked files as modiffied]({{"/assets/images/2018-07-25/2018-07-25-[git]-marked-as-modified-in-vscode.PNG"| relative_url}})

vscode의 Gitlens(https://github.com/eamodio/vscode-gitlens)를 통해 봐도, 파일이 변해있다고 표시가 되어있으나 실제 diff창을 확인해봐도 육안으로는 차이점을 못 느낀다.  
![vscode marked files as modiffied]({{"/assets/images/2018-07-25/2018-07-25-[git]-marked-as-modified-in-gitlens.PNG"| relative_url}})

> 진짜로 달라보이는게 없음...

심지어, 터미널에서 확인해보면 커밋할 것도 없고, 변한거 없이 깨끗하단다.

```
$ git status
```

![git status]({{"/assets/images/2018-07-25/2018-07-25-[git]-git-status.PNG"| relative_url}})



## 해결책
이러한 문제가 발생할때, 원인은 여러가지가 있을 수 있다.( 대부분의 경우, *eol* 문제인 것 같다. )
고려해볼 수 있는 사항이 나의 경우와 같이 **filemode**에 의한 문제일 수 있다.

터미널을 열고, 아래와 같이 **core.filemode**의 설정 값을 false로 바꿔주니 해결되었다. ( 기본값은 *true* )

```
$ git config --global core.filemode false
```

[git-config](https://mirrors.edge.kernel.org/pub/software/scm/git/docs/git-config.html) 문서에서 **core.filemode**에 대한 내용을 확인해보니, filesystem에서 프로젝트의 포함된 파일의 권한을 clone 받았을 때에도 유지하기 위한 옵션이었다. 프로젝트 성격에 따라서 *true*로 사용되어야 할 수도 있겠다.

> Tells Git if the executable bit of files in the working tree is to be honored. **Some filesystems lose the executable bit when a file that is marked as executable is checked out, or checks out a non-executable file with executable bit on. git-clone(1) or git-init(1) probe the filesystem to see if it handles the executable bit correctly and this variable is automatically set as necessary.**  A repository, however, may be on a filesystem that handles the filemode correctly, and this variable is set to true when created, but later may be made accessible from another environment that loses the filemode (e.g. exporting ext4 via CIFS mount, visiting a Cygwin created repository with Git for Windows or Eclipse). In such a case it may be necessary to set this variable to false. See git-update-index(1). The default is true (when core.filemode is not specified in the config file).