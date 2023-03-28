---
title: React-Native에서 GemNotFoundException 해결하기
date: 2023-03-28 15:03:39
category: TIL
thumbnail: { thumbnailSrc }
draft: false
---

기존에 하던 프로젝트의 React Native가 쓰는 Ruby 엔진과 중간에 참여하게 된 프로젝트에 세팅된 Ruby 엔진 버전이 안맞아서 새로 설치했다. 새로 설치했음에도 Gemfile ~ 관련 오류가 나는 걸 보니 추가로 작업을 해줘야하는 것 같아서 방법을 찾아보았다.

Ruby 버전이 다르면 프로젝트 옮겨 다닐 때마다 이렇게 해줘야하는지 고민스럽긴 한데 일단 해결방법을 적어둔다.

## 오류 내용

```bash
/System/Library/Frameworks/Ruby.framework/Versions/2.6/usr/lib/ruby/2.6.0/rubygems.rb:283:in `find_spec_for_exe': can't find gem cocoapods (>= 0.a) with executable pod (Gem::GemNotFoundException)
        from /System/Library/Frameworks/Ruby.framework/Versions/2.6/usr/lib/ruby/2.6.0/rubygems.rb:302:in `activate_bin_path'
        from /usr/local/bin/pod:23:in `<main>'
```

## 해결 방법

```bash
# 프로젝트 최상위 경로에서
> bundle install
> gem install cocoapods

# ios 경로에서
> pod install
```

## 참고자료

- can't find gem cocoapods (>= 0.a) with executable pod(react-native pod install) ([스택오버플로우](https://stackoverflow.com/questions/73092311/cant-find-gem-cocoapods-0-a-with-executable-pod-react-native-pod-install))