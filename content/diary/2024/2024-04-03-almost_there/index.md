+++
title = '练习编程'
date = 2024-04-03T22:08:09+08:00
draft = false
categories = ['玲珑雕心']
tags = ['日记']
+++

准备往`~/.zshrc`里添加`source /opt/homebrew/opt/zsh-vi-mode/share/zsh-vi-mode/zsh-vi-mode.plugin.zsh`, 错误地敲成了：`echo source /opt/homebrew/opt/zsh-vi-mode/share/zsh-vi-mode/zsh-vi-mode.plugin.zsh > ~/.zshrc`，打开文件一看，咋只有一行内容呢？发出尖锐爆鸣。

还好有Time Machine.

### 第二天

记录昨晚和今天debug的过程。

我在使用Spring Security做登录功能，`login`和`register`都正常工作，但是`reset`接口就是不对。经过半天的debug，原因分析如下：

#### 错误代码
```java
@PutMapping("/reset")
public ResponseEntity<String> resetPassword(@RequestBody String password){
  return ResponseEntity.ok(this.authenticationService.resetPassword(password));
}
```

```java
public String resetPassword(String password) {
  var encryptedPassword = this.passwordEncoder.encode(password);
  var username = SecurityContextHolder.getContext().getAuthentication().getName();
  this.userService.resetPassword(username, encryptedPassword);
  ...
}
```
Postman前端请求格式：![alt text](<_media/CleanShot 2024-04-04 at 11.16.47.png>)，设计的是username从token中校验通过后拿取。所以前端只需要在Body中传递密码。但问题就出在这里。我基础掌握得不牢，以为可以直接用String来接收Json对象。一个很基础的错误，但是失败不可怕，我觉得值得反思的是我低效的debug流程。

如图所示：
![alt text](<_media/CleanShot 2024-04-04 at 10.43.43.png>)

我只需要简单的在这里打断点，就能看到为什么不能进入这段`if`逻辑的原因，从根本上分析是因为rawPassword和encodedPassword不匹配，继续倒推就是rawPassword不对，rawPassword从controller到service没有被改变过，那就只能是传递的值有问题。

```java
try{
  if (passwordEncoder.matches(password, userDetails.getPassword())) {
    return new UsernamePasswordAuthenticationToken(username, password,
            userDetails.getAuthorities());
  }
}
```

但我先入为主地认为传递的值没有问题，这就给调试造成了很大的阻碍。（质疑一切）

有句话说的很对：
> 失败是成功之母，但必须反思才能成长，不然就是无意义的失败。