#sublime 学习笔记

[TOC]

###1.必备扩展包

---

####Package Control
安装指导 　https://sublime.wbond.net/installation

The simplest method of installation is through the Sublime Text console. 
The console is accessed via the ``` ctrl+` ```shortcut or the `View > Show Console menu`. 
Once open, paste the appropriate Python code for your version of Sublime Text into the console.

for sublime3:
```python
import urllib.request,os,hashlib; h = '7183a2d3e96f11eeadd761d777e62404' + 'e330c659d4bb41d3bdf022e94cab3cd0'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); by = urllib.request.urlopen( 'http://packagecontrol.io/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); print('Error validating download (got %s instead of %s), please try manual install' % (dh, h)) if dh != h else open(os.path.join( ipp, pf), 'wb' ).write(by)
```
for sublime2:
```python
import urllib2,os,hashlib; h = '7183a2d3e96f11eeadd761d777e62404' + 'e330c659d4bb41d3bdf022e94cab3cd0'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); os.makedirs( ipp ) if not os.path.exists(ipp) else None; urllib2.install_opener( urllib2.build_opener( urllib2.ProxyHandler()) ); by = urllib2.urlopen( 'http://packagecontrol.io/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); open( os.path.join( ipp, pf), 'wb' ).write(by) if dh == h else None; print('Error validating download (got %s instead of %s), please try manual install' % (dh, h) if dh != h else 'Please restart Sublime Text to finish installation')
```
经过上面安装了Package Control后，我们就可以通过快捷键 `Ctrl+Shift+P` 打开Package Control来安装插件了。在打开的输入框中输入 `install` ，会根据你的输入自动提示，选择 Install Package。

---

####常用扩展包

 - ConvertToUTF8 	编码
 - IMESupport 		输入法跟随光标
 - Alignment		对齐
 - Bracket Highlighter 括号匹配

---

###2.快捷键

---

####文本编辑

选中：

 1. 选中文档中，所有文本： `alt+F3`
 2. 向下选中，多个当前文本： `ctrl+D`
 3. 多个光标： `ctrl+alt+up/down`
 4. 区域矩阵选中：`shift+鼠标右键`
 5. 选择整行（按住 `L` 继续选择下行）：`Ctrl+L`
 6. 选择括号内的内容（按住`M`继续选择父括号）：`Ctrl+Shift+M`

注释：

 1. 注释整行（如已选择内容，同“Ctrl+Shift+/”效果）：`Ctrl+/` 
 2. 注释已选择内容：`Ctrl+Shift+/`

编辑：

 1. 改为大写：`Ctrl+K+U`
 2. 改为小写：`Ctrl+K+L`
 3. 与上行互换：`Ctrl+Shift+↑ `自定义为`alt+up`
 4. 与下行互换：`Ctrl+Shift+↓ `自定义为`alt+down`

删除

 1. Ctrl+KK 从光标处删除至行尾
 3. Ctrl+Shift+K 删除整行

标签

 1. 重新打开上次关闭的标签（可恢复多个）：`Shift + Ctrl + T`


代码样式

 1. Ctrl+Shift+[ 折叠代码
 2. Ctrl+Shift+] 展开代码

 4. Ctrl+Shift+D 复制光标所在整行，插入在该行之前
 5. Ctrl+J 合并行（已选择需要合并的多行时）

 9. Ctrl+M 光标移动至括号内开始或结束的位置
 10.  

Ctrl+Z 撤销
Ctrl+Y 恢复撤销
Ctrl+M 光标跳至对应的括号
Alt+. 闭合当前标签
Ctrl+Shift+A 选择光标位置父标签对儿

Ctrl+KT 折叠属性
Ctrl+K0 展开所有
Ctrl+U 软撤销
Ctrl+T 词互换
Tab 缩进 自动完成
Shift+Tab 去除缩进

Ctrl+K Backspace 从光标处删除至行首
Ctrl+Enter 光标后插入行
Ctrl+Shift+Enter 光标前插入行
Ctrl+F2 设置书签
F2 下一个书签
Shift+F2 上一个书签









