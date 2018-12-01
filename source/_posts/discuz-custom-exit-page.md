title: discuz 自定义退出时的跳转地址
date: 2012-10-15 12:40:07
tags:
- discuz
---
## 需求
如果我们想在discuz退出后跳转到其他页面，比如说另外定制的一个页面，该怎么做呢?

## 知识
discuz处理退出时的跳转地址的文件是`source\class_member.php`

## 具体步骤
假如我们有一个`logout.html`的文件，需要在discuz退出后跳转到它

打开`source\class_member.php`，找到`on_logout()`函数

修改为如下代码
```php
function on_logout() {
	global $_G;

	$ucsynlogout = $this->setting['allowsynlogin'] ? uc_user_synlogout() : '';

	if($_G['gp_formhash'] != $_G['formhash']) {
		header("Location: logout.html");//在这里设置跳转地址
	/*showmessage('logout_succeed', '../logout.html', array('formhash' => FORMHASH, 'ucsynlogout' => $ucsynlogout));干掉showmessge函数*/
	}

	clearcookies();
	$_G['groupid'] = $_G['member']['groupid'] = 7;
	$_G['uid'] = $_G['member']['uid'] = 0;
	$_G['username'] = $_G['member']['username'] = $_G['member']['password'] = '';
	$_G['setting']['styleid'] = $this->setting['styleid'];
	header("Location: logout.html");//在这里设置跳转地址
	/*showmessage('logout_succeed', '../logout.html', array('formhash' => FORMHASH, 'ucsynlogout' => $ucsynlogout));干掉showmessge函数*/
}
```
这里showmessage函数的作用是显示对话框，告诉用户已经退出了discuz，我们不需要这个对话框，所以把它注释了，有关showmessage函数的具体使用请自行谷歌
